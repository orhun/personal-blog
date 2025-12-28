+++
title = "Looking back on 2025"
date = 2025-12-31

[taxonomies]
categories = ["Thoughts"]
+++

2025 was a crazy simulation. A lot of glitches, plot twists and fun stuff™.

<!-- more -->

It was equally un-fun and chaotic as well.
In this post I want to document the lore and share my plans for the next year.

<img src="/poorly-drawn-rat.png" width="15%"/>

<sup>

"Only way to move forward is to look back."  
— Text on a random guy's T-shirt at the gym

</sup>

- [Intro](#intro)
- [Projects](#projects)
- [Travels](#travels)
- [Communities](#communities)
- [Health](#health)
- [Extras](#extras-looking-for-a-job)
- [Closing](#closing)

# Intro

Throughout the year I felt like [Sisyphus](https://en.wikipedia.org/wiki/Sisyphus), always working on stuff and expecting things to change or get better.
Then I realized that's the point, you just gotta keep rolling that damn boulder up the hill.

It was fun though, I have:

- [traveled](#travels) to many places,
- [worked](#projects) on computers,
- built [communities](#communities),
- and [benched](#health) 100kg.

<img src="/todo-grind.jpg" width="35%"/>

# Projects

- [Ratatui](#ratatui)
- [git-cliff](#git-cliff)
- [Ratzilla](#ratzilla)
- [Tuitar](#tuitar)
- [ALPM](#alpm)
- [cargo-tui](#cargo-tui)

## [Ratatui](https://ratatui.rs/)

<img src="/ratatui-logo.png" width="10%"/>

> <g>A Rust library to cook up terminal user interfaces (TUIs). 🐭</g>

I have worked on Ratatui for the whole year...  
And as we are wrapping up the year, we have released the biggest version of Ratatui yet: [0.30.0](https://ratatui.rs/highlights/v030/)

This excites me for many reasons but most prominently because Ratatui is now `no_std`, meaning that it can be used without the Rust standard library.
This opens up a lot of doors for experiments, e.g. running Ratatui on Cortex-M microcontrollers and a bunch of other environments.

Speaking of [Ratatuifying](https://www.urbandictionary.com/define.php?term=ratatuify), we ran a challenge called [**Rat in the Wild**](https://github.com/ratatui/ratatui/discussions/1886) this year. And we have gifted aprons to several people who have managed to run Ratatui on crazy platforms, including [an actual car dashboard](https://github.com/thatdevsherry/suzui-rs).

For me the TUI of the year was [gitlogue](https://github.com/unhappychoice/gitlogue) ⭐

<img src="/gitlogue.gif" width="70%"/>

_Watch your repo code itself._

---

## [git-cliff](https://git-cliff.org/)

<img src="/git-cliff.png" width="8%"/>

> <g>A configurable changelog generator for Git repositories. ⛰️</g>

git-cliff is my solo project that I have been working on and off for several years now.  
I really enjoy tackling Git issues and automating changelog generation with new features.  
This year, I have released 4 new versions of the project, the latest one being [2.11.0](https://git-cliff.org/blog/2.11.0).

Also, I have done a special T-shirt giveaway to celebrate reaching [10k stars on GitHub](https://github.com/orhun/git-cliff).

<img src="/git-cliff-giveaway.jpg" width="80%"/>

The winner of the giveaway already sent me a pic from the gym wearing the T-shirt! 💪

---

## [Ratzilla](https://github.com/orhun/ratzilla)

<img src="/ratzilla.gif" width="15%"/>

> <g>Build terminal-themed web applications with Rust and WebAssembly. Powered by Ratatui. 🦖</g>

This was my first big [grind](#grindhouse) of the year. I submitted a talk to [FOSDEM](#fosdem), got accepted and speedran to build this crazy idea.

Now you can render TUIs directly in the browser using DOM, Canvas or WebGL backends!

The following demo embeds a Ratatui application compiled to WebAssembly, initialized in the browser and injected into a static Zola layout:

{{ ratzilla() }}

You can press arrow keys to interact with it.

---

## [Tuitar](https://github.com/orhun/tuitar)

<img src="/tuitar-logo-dark.png" width="15%"/>

> <g>A portable guitar training tool & DIY kit 🎸</g>

After my [Superbooth](#superbooth) trip this year, I got inspired to build a guitar practice tool.

This was my second big [grind](#grindhouse) and it was also a talk-driven project. I simply submitted a talk to [Rust Forge](#rustforge), got accepted and forced myself to build it.

Recently it got featured on [Hackster.io](https://www.hackster.io/news/fret-not-this-esp32-gadget-makes-guitar-practice-a-breeze-dcc26bcef0ba) which made me very happy!

> Orhun Parmaksız is not only learning the guitar, but he is also a hardware hacker.
> He created what he calls Tuitar, which is a portable, ESP32-powered guitar training tool.
> It helps improve one’s playing skills, and can also be used to aid in perfectly tuning a guitar.
> Tuitar is available as a DIY kit, so anyone in need of a little help can build their own.

<img src="/tuitar-pcb.jpg" width="60%"/>

Follow [@tuitardev on X](https://x.com/tuitardev) to not miss any updates!

<iframe width="560" height="315" src="https://www.youtube.com/embed/tZm7cLaHAR0?si=ilAY4rk8yqG-OIfX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

I also got a bass guitar recently to experiment with bass support for Tuitar.

---

## [ALPM](https://gitlab.archlinux.org/archlinux/alpm/alpm)

<img src="/arch-linux.png" width="13%"/>

> <g>Rust libraries and tools for Arch Linux Package Management. 🐧</g>

For the past year I have done contracting work for [Arch Linux](https:/archlinux.org/) in the context of the ALPM project (backed by [Sovereign Tech Agency](https://www.sovereign.tech/tech/arch-linux-package-management)). In the scope of this work, we have implemented modern Rust libraries for interacting with packages, repositories and databases.

During this time, I have specifically learned how to write custom parsers (with [winnow](https://docs.rs/winnow)) and deserializers for old file formats.

One day pacman will be fully oxidized! 🦀

---

## [cargo-tui](https://github.com/orhun/cargo-tree-tui)

<img src="/rat-cargo.png" width="20%"/>

> <g>Contributing to Rust's package manager (Cargo) to add a TUI for specific commands 📦</g>

This was my last big [grind](#grindhouse) of the year and I'm still working on it. Next year I'm planning to give talks about my journey, so stay tuned! You can also follow my progress on [YouTube](https://www.youtube.com/playlist?list=PLxqHy2Zr5TiWOHYcTgxYZMzHV36PxRVf9) or [GitHub](https://github.com/orhun/cargo-tree-tui).

# Travels

In 2025 I attended **12** conferences and did presentations in **10** countries:

- Feb 2025 – FOSDEM – **Brussels, Belgium**
- Mar 2025 – Rust Meet – **Gliwice, Poland**
- Mar 2025 – CSCON’25 – **Ankara, Türkiye**
- Mar 2025 – Codeaholics March Meetup – **Hong Kong**
- Mar 2025 – RustAsia – **Hong Kong**
- May 2025 – Rust Konf Türkiye – **Istanbul, Türkiye**
- May 2025 – Superbooth – **Berlin, Germany**
- May 2025 – Rust Week – **Utrecht, Netherlands**
- Jun 2025 – Sicily HolyC Meetup – **Sicily, Italy**
- Jun 2025 – Cloud Native Meetup – **Mauritius**
- Aug 2025 – RustForge – **Wellington, New Zealand**
- Sep 2025 – Rust China Conf – **Hangzhou, China**
- Oct 2025 – Tokyo Rust Meetup – **Tokyo, Japan**
- Nov 2025 – Arch Summit – **Hamburg, Germany**
- Nov 2025 – Barcelona Trip – **Barcelona, Spain**
- Dec 2025 – Backend Community Meetup – **Ankara, Türkiye**

Here are some of my highlights:

- [FOSDEM 🇧🇪](#fosdem)
- [Rust Meet 🇵🇱](#rust-meet)
- [Superbooth 🇩🇪](#superbooth)
- [Mauritius Trip 🇲🇺](#mauritius-trip)
- [RustForge 🇳🇿](#rustforge)
- [Rust China Conf 🇨🇳](#rust-china-conf)
- [Tokyo Rust Meetup 🇯🇵](#tokyo-rust-meetup)

<a id="fosdem"></a>

### [FOSDEM](https://fosdem.org) 🇧🇪

I have presented [Ratzilla](#ratzilla) at FOSDEM 2025.

<iframe width="560" height="315" src="https://www.youtube.com/embed/iepbyYrF_YQ?si=VTx9wcLlryRR4gtL" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

And yes, that's an apron I am wearing.

<a id="rust-meet"></a>

### [Rust Meet](https://rustmeet.eu/en/) 🇵🇱

I gave 2 talks at Rust Meet in Poland.

1. [My journey in open source](https://www.youtube.com/watch?v=HDU8dpt-tNE)
2. [Mind blowing terminal things](https://www.youtube.com/watch?v=38EWluqEIs8)

Also, this was my first time in Poland and it was fun!

<sup>

We stayed in a haunted hotel with Júlia and some paranormal things happened.
[It was livestreamed.](https://www.youtube.com/watch?v=dzwfpfHTsPw)

</sup>

<a id="superbooth"></a>

### [Superbooth](https://www.superbooth.com/en/) 🇩🇪

I wrote a detailed blog post about it: [Am I a musician yet?](https://blog.orhun.dev/am-i-a-musician-yet/)

This trip/event really changed my perspective on music and hardware hacking.

<a id="mauritius-trip"></a>

### Mauritius Trip 🇲🇺

Mauritius was insane.  
I have visited my friend [Jain](https://github.com/k3ii) there and he showed me around the beautiful island.
Maybe I should have written a blog post about it, but I will just let this photo do the talking for now:

<img src="/mauritius.jpg" width="50%"/>

I also attended a Cloud Native meetup there and gave a talk about Rust in Cloud Native.  
You can read the blog post about it: [Cloud Native Mauritius, June Meetup — Rust special 🦀](https://sysadmin-journal.com/cloud-native-mauritius-june-meetup-rust-speccial/)

Made some great friends there and hopefully I will visit again in 2026!

<a id="rustforge"></a>

### RustForge 🇳🇿

I presented [Tuitar](#tuitar) at RustForge in New Zealand.

<iframe width="560" height="315" src="https://www.youtube.com/embed/es48dmNWMVQ?si=inoj7sPPmLEPlJXV&amp;start=29905" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

It was my longest distance travel ever and I got to meet amazing people there. The whole conference was super well organized thanks to the legend [Tim McNamara](https://github.com/timclicks).

<img src="/rust-rocks.jpg" width="90%"/>

_Rust rocks, literally._

<a id="rust-china-conf"></a>

### Rust China Conf 🇨🇳

Man, I loved China. The food, the people, the culture... everything was amazing.

<iframe width="560" height="315" src="https://www.youtube.com/embed/pJJATZoht1c?si=Nrp9ovHRwOHxOatm" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

If I were to learn another language, it would be Mandarin for sure.  
Definitely would love to visit again!

<a id="tokyo-rust-meetup"></a>

### Tokyo Rust Meetup 🇯🇵

I gave an embedded Ratatui workshop at Tokyo Rust Meetup and it was a blast!

<iframe width="560" height="315" src="https://www.youtube.com/embed/F04kQMKwrwQ?si=oCffNfNCVMqmoL9v" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

I was surprised what people have built at the end of the workshop.  
Definitely watch the livestream recording above, it was hella fun.

# Communities

## [Grindhouse](https://grindhouse.dev)

<img src="/grindhouse_white.png" width="10%"/>

Life is all about grinding.
So I started a community for it.

> <g>A community for developers, artists, and visionaries who embrace the Grind™ as a way of life.</g>

As of now, we have 300 members on Discord (at least 10 being active), many "grind" projects and a welcoming environment for everyone!

I have also met many members IRL and we occasionally hang out on livestreams:

<iframe width="560" height="315" src="https://www.youtube.com/embed/e6qLNXTnwKQ?si=lH4fz0glonE-jpzE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The Grindhouse was one of the best things I have done this year.  
May the Grind™ never end.

---

## [Mercimek](https://mercimek.space/)

<img src="/mercimek-logo.png" width="15%"/>

One of the things that amazed me while traveling the world was the power of hackerspaces. I have visited several of them and realized we really need one in my city.

Also, after I saw the success of [Grindhouse](#grindhouse), I thought why not do the same thing but IRL?

So, I founded [Mercimek](https://mercimek.space/):

> <g>A hackerspace in Ankara, Türkiye. </g>

We already have a physical location and will start planning meetups in 2026!

To make it more accessible, we have created a [meetup group](https://www.meetup.com/mercimekspace/) and [a waitlist](https://forms.gle/z8V9UPBxftFMtskQ7) for memberships.
Stay tuned!

# Health

"Get jacked and you will figure it out."  
— Me

<img src="/jack3d.jpg" width="70%"/>

---

<a id="extras-looking-for-a-job"></a>

# Extras: Looking for a J.O.B.

[ALPM](#alpm) was fun, but the contracting work is coming to an end.

So I'm looking for new opportunities starting in 2026! Hit me up on [LinkedIn](https://www.linkedin.com/in/orhunp/) or [through email](mailto:orhunparmaksiz@gmail.com). Here is my [resume](https://orhun.dev/resume/) if that's your thing.

I'm open to Rust (preferred), embedded (would be nice), open source (of course), community building (y'know), developer relations (yessir) and other related roles.

Soon I will be living on sponsorships and donations... So [consider supporting my work](https://github.com/orhun/)!

Lastly, big shout out to [JetBrains](https://jetbrains.com/), [Terminal Trove](https://terminaltrove.com/) and my 33 individual sponsors for keeping me up and running 💖

# Closing

Here are some cool stats!

- Pushed 4192 commits (currently on a [2514-day commit streak](https://github.com/orhun/))
- 1,893,227 impressions on [LinkedIn](https://www.linkedin.com/in/orhunp/) (reached 164,694 members)
- K/D ratio: 0/0 (no rats were harmed)

All in all, I give 2025 a **solid 🐀/10.**

<img src="/change-the-world.png" width="50%"/>

---

<sup>

What's reality?  
I don't know. When my bird was looking at my computer monitor I thought,
"That bird has no idea what he’s looking at." And yet what does the bird do? Does he panic?
No, he can't really panic, he just does the best he can. Is he able to live in a world where he's so ignorant?
Well, he doesn't really have a choice. The bird is okay even though he doesn't understand the world.
You're that bird looking at the monitor, and you're thinking to yourself, "I can figure this out."
Maybe you have some bird ideas.

Maybe that's the best you can do.

— Terry Davis

</sup>
