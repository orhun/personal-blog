+++
title = "Introducing runst: Handle desktop notifications neatly on Linux!"
date = 2023-03-05

[taxonomies]
categories = ["Projects"]
+++

`runst` is a dead simple notification daemon 🦡 In this post, I'm introducing the project and giving different usage examples that will improve your Linux desktop experience.

<!-- more -->

<a href="https://github.com/orhun/runst"><b>https://github.com/orhun/runst</b></a>

### Notifications

<img alt="https://xkcd.com/2555/" src="/xkcd2555.png"/>

It is undeniable that notifications became an inescapable part of our overly techy lives in the past decade. Most of the time, it is overlooked how a simple alert dialog can fire different synapses in the human brain and let us feel a certain way. It is truly magnificent that we commonly don't see the fact that "notifying" is a form of communication and it is deeply integrated into our lives at this point.

So let's talk about Linux desktop and how we can handle notifications.

_What? I hear you say "What the hell was that intro?". Well, just stay tight. It was definitely not a trick to hook you to read this blog post._

As in every interactive system, GNU/Linux also has different ways of handling notifications. First, let's try to understand what a notification is and what we need to actually work with it.

> Desktop notifications are small, passive popup dialogs that notify the user of particular events in an asynchronous manner.

In other words, notifications are dialogs that are either shown on the screen for a certain amount of time and disappear or persist until user interaction. Cool!

---

### D-Bus

Now, how do we interact with notifications? We clearly need some kind of internal communication protocol between processes to handle messages from different applications. Today, most Linux desktops depend on D-Bus for that purpose.

> [D-Bus](https://www.freedesktop.org/wiki/Software/dbus/) is a message bus system, a simple way for applications to talk to one another. In addition to interprocess communication, D-Bus helps coordinate process lifecycle; it makes it simple and reliable to code a "single instance" application or daemon, and to launch applications and daemons on demand when their services are needed.

D-Bus has [Desktop Notifications Specification](https://specifications.freedesktop.org/notification-spec/notification-spec-latest.html) which defines the standard implementation details for notification-related applications. On the other hand, [libnotify](https://gitlab.gnome.org/GNOME/libnotify) is widely used for sending desktop notifications to a "notification daemon". Every system needs a running notification daemon for handling notifications.

If you are running D-Bus, you can create a desktop notification using the [`notify-send(1)`](https://man.archlinux.org/man/notify-send.1.en) command of `libnotify`:

```sh
$ notify-send "runst" "A dead simple notification daemon 🦡" --expire-time 3000
```

And you can query the desktop notifications real-time with running [`dbus-monitor(1)`](https://man.archlinux.org/man/dbus-monitor.1):

```sh
$ dbus-monitor "interface='org.freedesktop.Notifications'"
```

This will result in:

```sh
method call time=1677934706.650704 sender=:1.2392 -> destination=:1.1673 serial=8 path=/org/freedesktop/Notifications; interface=org.freedesktop.Notifications; member=Notify
   string "notify-send"
   uint32 0
   string ""
   string "runst"
   string "A dead simple notification daemon 🦡"
   array [
   ]
   array [
      dict entry(
         string "urgency"
         variant             byte 1
      )
      dict entry(
         string "sender-pid"
         variant             int64 442049
      )
   ]
   int32 3000
```

With these in mind, let's take a look at different notification daemon implementations.

---

### Notification Daemons

[Desktop environments](https://en.wikipedia.org/wiki/Desktop_environment) have a built-in feature for handling the notifications:

> Cinnamon, Deepin, Enlightenment, GNOME, GNOME Flashback and KDE Plasma use their own implementations to display notifications, and they cannot be replaced. Their notification servers are started automatically on login to receive notifications from applications via D-Bus.

In that case, if we want to use a custom notification daemon, we need something _standalone_. This means they might require a manual setup to work with Xorg/Wayland. Here are a few projects:

- [wired](https://github.com/Toqozz/wired-notify) - Lightweight notification daemon with highly customizable layout blocks.
- [mako](https://github.com/emersion/mako) - Lightweight notification daemon for Wayland.
- [dunst](https://github.com/dunst-project/dunst) - Lightweight and customizable notification daemon.

(Editor's note: wow, they are all lightweight!)

Among those projects, by far the most popular notification daemon is `dunst`. Needless to say, I WAS a user of `dunst`. Also, the reason why I wrote `runst` is basically... `dunst`.

So, what is `runst`?

---

### `runst` - **a dead simple notification daemon** 🦡

<center>

<img src="https://github.com/orhun/runst/raw/main/assets/runst-logo.png" style="width: 25%"/>

![runst demo I](https://github.com/orhun/runst/raw/main/assets/runst-demo.gif)

</center>

`runst` is yet another notification daemon implementation in Rust. It aims to be as simple as possible while providing customizable features. The reason why I wrote `runst` is simply that:

> I have been a user of [dunst](https://github.com/dunst-project/dunst) for a long time. However, they made some [uncool breaking changes](https://github.com/dunst-project/dunst/issues/940) in [v1.7.0](https://github.com/dunst-project/dunst/releases/tag/v1.7.0) and it completely broke my configuration. That day, I refused to update `dunst` (I was too lazy to re-configure) and decided to write my own notification server using Rust.

<center>

Hence the name. (`dunst + rust = runst`)

</center>

`runst` is initially designed to show a simple notification window. On top of that, it combines customization-oriented and semi-innovative features. The way that I use `runst` is pretty simple, it's just an overlay on top of [i3status](https://github.com/i3/i3status) bar:

<center>

![runst demo II](https://github.com/orhun/runst/raw/main/assets/runst-demo2.gif)

</center>

However, there are many cool things that you can do with `runst`.

Let's go through its features with usage examples.

(`runst` is configured with a single configuration file. You can check out the defaults [here](https://github.com/orhun/runst/blob/main/config/runst.toml).)

---

#### Custom window

You can customize the notification popup text, color and dimensions. For example:

```toml
[global]
    geometry = "312x184+1078+354" # `slop -k`
    font = "Monospace 10"
    template = """
    [{{app_name}}] <b>{{summary}}</b>\
    {% if body %} {{body}}{% endif %} \
    {% if now(timestamp=true) - timestamp > 60 %} \
        ({{ (now(timestamp=true) - timestamp) | humantime }} ago)\
    {% endif %}\
    {% if unread_count > 1 %} ({{unread_count}}){% endif %}
    """

[urgency_normal]
    background = "#000000"
    foreground = "#aaaaaa"
    text = "normal"
```

In the configuration above, we configured the notification window dimensions, colors (for the normal urgency), font, and the message template.

Now let's take a closer look at the `template`:

```jinja
[{{app_name}}] <b>{{summary}}</b>\
{% if body %} {{body}}{% endif %} \
{% if now(timestamp=true) - timestamp > 60 %} \
    ({{ (now(timestamp=true) - timestamp) | humantime }} ago)\
{% endif %}\
{% if unread_count > 1 %} ({{unread_count}}){% endif %}
```

`runst` uses the following context for the template variables:

```json
{
  "app_name": "runst",
  "summary": "example",
  "body": "this is a notification 🦡",
  "urgency": "normal",
  "unread_count": 1,
  "timestamp": 1672426610
}
```

So we will end up with notifications like this:

- <span style="background-color:#191919;">[runst] <strong>example</strong> this is a notification 🦡</span>

  - (default)

- <span style="background-color:#191919;">[runst] <strong>example</strong> this is a notification 🦡 (3m 2s ago)</span>

  - (while viewing an old notification)

- <span style="background-color:#191919;">[runst] <strong>example</strong> this is a notification 🦡 (2)</span>

  - (when there are 2 more unread notifications)

- <span style="background-color:#191919;">[runst] <strong>example</strong></span>

  - (if there is no `body`)

With this [Tera](https://keats.github.io/tera/)-powered `template`, you can customize the notification text as you like.

---

#### Custom commands

You can run custom OS commands based on urgency levels and the notification contents.

Imagine you want to perform a task when you receive a critical notification from a specific application:

```toml
[urgency_critical]
custom_commands = [
  { filter = '{ "app_name":"important-app","body":"^delete.*" }', command = 'delete-everything.sh' },
]
```

Or maybe you simply want to play a custom notification sound:

```toml
[urgency_normal]
custom_commands = [
  { command = 'aplay notification.wav' },
]
```

Or maybe you want a [Gotify](https://github.com/gotify) notification on your different devices when someone says hi in any chatting application matched by the regex:

```toml
[urgency_normal]
custom_commands = [
  { filter = '{ "app_name":"telegram|discord|.*chat$","body":"^hello.*" }', command = 'gotify push -t "{{app_name}}" "someone said hi!"' },
  { filter = '{ "app_name":"matrix","body":"^hey.*" }', command = 'gotify push -t "{{app_name}}" "{{body}}"' }
]
```

See more examples [here](https://github.com/orhun/runst#custom_commands).

---

#### Auto clear

Normally, if you want the notifications to disappear after e.g. 3 seconds, you can do this:

```toml
[urgency_low]
timeout = 3
```

If the timeout is not specified by the sender, the low urgency notifications will be closed after 3 seconds. You can set `timeout` to 0 for _unexpiring_ notifications.

On the other hand, if you want the notifications to disappear as you finish reading them, you can use the following option:

```toml
[urgency_low]
auto_clear = true
```

If this option is set to `true`, the _estimated read time_ of the notification contents is calculated and it is used as the timeout for closing the notification. So if it takes 5 seconds to read the notification, it will disappear after 5 seconds.

---

### Endnote

There are other features that are not mentioned in this post so definitely check out the project's home page: [https://github.com/orhun/runst](https://github.com/orhun/runst)

I'm using `runst` daily and adding new features as new ideas rush through my mind so feedback is very welcome! It is still a bit barebone :>

See you in the next project! 🦡
