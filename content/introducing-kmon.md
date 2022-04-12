+++
title = "Kmon! Let's manage the Linux kernel modules!"
date = 2020-04-02

[taxonomies]
categories = ["Projects"]
+++

Introducing kmon, a Linux kernel manager and activity monitor 🐧💻

<!-- more -->

Unfortunately, it's common to use a technology without knowing the inner workings of it. However, having a good grasp on the specific details of it might help with various situations like fixing a bug or adding new features to it. Especially if you're a developer/engineer.

I've been using **GNU/Linux** for years and I really love the uniqueness and simplicity that some distributions provide. It also provides a close to perfect development environment in my opinion, if you customize it.
But one of the things, probably the most important thing, that I neglected to learn its details was the **Linux kernel**. I was using various tools for adding a module to the Linux kernel to make a program work (_like VirtualBox_) or I was disabling modules to change the purpose of a hardware. But I've never into the Linux kernel really. It was just being updated regularly and sitting there, waiting to be explored.

My previous experiences taught me that the best way of learning something is **creating a project that solves a problem** depending on your needs. Also adding some flavors (like using a tech stack that you've not used before) will increase the chance of drowning inside the "_ocean of the new stuff_". Which is a good thing because it will force you to swim back to the surface. In the context of the project, it will force you to make the project as perfect as possible and finish it. Because as everyone knows, it's always easier to abandon a project.

So I've decided to learn the basics of the Linux kernel including kernel types and its history. It was surely a good idea in order to understand the "core" element of the systems we're using today. And then I created my first Linux kernel module that only prints a message when it's loaded and unloaded. It looked like legacy tools like **dmesg**, **kmod** and **modprobe** are preferred most of the time for Linux kernel management on command line. So I've loaded my example kernel module with modprobe and retrieved the log messages with dmesg. Even though I've checked for other alternative tools, I didn't come across a tool that you can do these operations together at the same time.

Therefore I thought that I should write that tool in a memory-efficient and fast programming language. **Rust!**

[tui-rs](https://github.com/fdehau/tui-rs) is a Rust library for creating **text-based user interfaces** with different backend options. I've chosen [termion](https://github.com/redox-os/termion) for the tool that I'm going to write. But still, one thing was missing. I should find a name for it to keep myself motivated while writing it.

It should be called... Hm... Kernel top? _ktop..._ Nah, it's taken. How about kernel monitor? ***kmon...*** A-ha! Come on!

Actually, I've never written something in Rust before kmon. So it was an adventurous journey about the Linux kernel and Rust. I have to say that Rust has a nice development environment and efficient tools like **rustfmt** and **clippy**. And writing a Rust application about the Linux kernel on an Arch Linux system... It's not something that you can explain with words.

Oh what happened at the end of this journey? Well, let me show:

![come on!](https://user-images.githubusercontent.com/24392180/78243975-68a5f800-74ed-11ea-89b4-d7cfcaeeb6ae.gif)
```
cargo install kmon
```

You can:
- see the information of a module
- search a module
- load/unload a module
- blacklist a module
- and do other cool things

in the text-based interface of kmon.

Here's some examples:

### Navigating & Scrolling

`Arrow keys` are used for navigating between blocks and scrolling.

![Navigating & Scrolling](https://user-images.githubusercontent.com/24392180/76685750-26447600-6627-11ea-99fd-157449c9529f.gif)

### Searching a module

Switch to the search area with arrow keys or using one of the `/, s, enter` and provide a search query for the module name.

![Searching a module](https://user-images.githubusercontent.com/24392180/76686001-23e31b80-6629-11ea-9e9a-ff92c6a05cdd.gif)

### Loading a module

For adding a module to the Linux kernel, switch to load mode with one of the `+, i, insert` keys and provide the name of the module to load. Then confirm/cancel the execution of the load command with `y/n`.

![Loading a module](https://user-images.githubusercontent.com/24392180/76686027-64429980-6629-11ea-852f-1316ff08ec80.gif)

The command that used for loading a module:

```
modprobe <module_name>
```

### Unloading a module

Use one of the `-, u, backspace` keys to remove the selected module from the Linux kernel.

![Unloading a module](https://user-images.githubusercontent.com/24392180/76686045-8b996680-6629-11ea-9d8c-c0f5b367e269.gif)

The command that used for removing a module:

```
modprobe -r <module_name>
```

### Blacklisting a module

[Blacklisting](https://wiki.archlinux.org/index.php/Kernel_module#Blacklisting) is a mechanism to prevent the kernel module from loading. To blacklist the selected module, use one of the `x, b, delete` keys and confirm the execution.

![Blacklisting a module](https://user-images.githubusercontent.com/24392180/78243543-a5252400-74ec-11ea-932e-4a17c94d74f6.gif)

The command that used for blacklisting a module:

```
if ! grep -q <module_name> /etc/modprobe.d/blacklist.conf; then
  echo 'blacklist <module_name>' >> /etc/modprobe.d/blacklist.conf
  echo 'install <module_name> /bin/false' >> /etc/modprobe.d/blacklist.conf
fi
```

### ...

**kmon** aims to be a standard tool for **Linux kernel management** while supporting most of the Linux distributions. On that note, here's the distributions that kmon is tested:


#### Fedora 31

![kmon on fedora](https://user-images.githubusercontent.com/24392180/76520554-27817180-6474-11ea-9966-e564f38c8a6a.png)

#### Debian 10

![kmon on debian](https://user-images.githubusercontent.com/24392180/76514129-79bc9580-6468-11ea-9013-e32fbbdc1108.png)

#### Manjaro 19

![kmon on manjaro](https://user-images.githubusercontent.com/24392180/76940351-1f5d8200-690b-11ea-8fe9-1d751fe102c5.png)

#### Ubuntu 18.04

![kmon on ubuntu](https://user-images.githubusercontent.com/24392180/76690341-18571b00-6650-11ea-85c9-3f511c054194.png)

#### openSUSE

![kmon on opensuse](https://user-images.githubusercontent.com/24392180/77414512-38b27280-6dd2-11ea-888c-9bf6f7245387.png)

#### Void Linux

![kmon on voidlinux](https://user-images.githubusercontent.com/24392180/77417004-c9d71880-6dd5-11ea-82b2-f6c7df9a05c3.png)

### Closing Note

More information can be found at the [project page](https://github.com/orhun/kmon) on GitHub.

* Follow [@kmonitor_](https://twitter.com/kmonitor_) on Twitter
* Follow the [author](https://orhun.dev):
    * [@orhun](https://github.com/orhun) on GitHub
    * [@orhunp_](https://twitter.com/orhunp_) on Twitter

Stay safe.
