+++
title = "Rewriting sysctl(8) in Rust: systeroid"
date = 2022-04-17

[taxonomies]
categories = ["Projects"]
+++

[sysctl](https://en.wikipedia.org/wiki/Sysctl) is a simple and great tool for modifying the kernel parameters. It does its job very well by providing an easy-to-use interface for `/proc/sys`. It is maintained as a part of [procps](https://gitlab.com/procps-ng/procps) toolkit for years and it can easily be considered a legacy tool today. So why not push it to its limits and turn it into a more user-friendly and even more useful tool with the power of Rust?

<!-- more -->

### sysctl

sysctl depends on [procfs](https://en.wikipedia.org/wiki/Procfs) and works by accessing/modifying the kernel parameters that reside in `/proc/sys` directory. The basic functionalities of sysctl are the following:

- List parameter values: `sysctl -A`
- Set a parameter value: `sysctl -w parameter=1`

A more concrete example would be:

```sh
sysctl -w vm.swappiness=60
```

Considering this simple example, how different use cases could you have, and what role does **systeroid** play?

### systeroid

[**https://github.com/orhun/systeroid**](https://github.com/orhun/systeroid)

**systeroid** is "sysctl on steroids". Similar to sysctl, it is implemented using procfs and the primary goal is to manage the kernel parameters. It has a bunch of features to ease the process of reading and modifying the values and even retrieving information about them from the official Linux kernel documentation. It also has a text-based user interface to visualize the state of the kernel parameters and interactively perform these management operations.

![demo](https://github.com/orhun/systeroid/raw/main/assets/systeroid-demo.gif)

#### Comparison

If you compare the `--help` output of systeroid with sysctl, you will see that they are compatible with each other, also meaning that systeroid encapsulates the functionality. There are some additional arguments/flags which is the actual thing that gives systeroid its power:

- `-T, --tree`: display the variables in a tree-like format
- `-J, --json`: display the variables in JSON format
- `-E, --explain`: provide a detailed explanation for variable
- `-D, --docs <path>`: set the path of the kernel documentation
- `-P, --no-pager`: do not pipe output into a pager
- `-v, --verbose`: enable verbose logging
- `--tui`: show terminal user interface

systeroid can also be used as a drop-in replacement for sysctl.

#### Output formats

`systeroid -A` is the simplest way of listing the kernel parameters. In addition, there is also `-T, --tree` flag which will output a tree:

```sh
$ systeroid --names --pattern 'kernel.*_max$' --tree

kernel
├── ngroups_max
├── pid_max
└── sched_util_clamp_max
```

You can also `-J, --json` to get the output as JSON:

```sh
$ systeroid --pattern '^net.*' --json

[
  {
    "name": "net.unix.max_dgram_qlen",
    "section": "net",
    "value": "512"
  }
]
```

#### Explain

Let's assume that you have faced a particular problem and someone on Stack Overflow recommended using the following command:

```sh
sysctl -w kernel.oops_all_cpu_backtrace=0
```

In this case, you might ask what is "kernel.oops_all_cpu_backtrace" and what does setting it to 0 do?

This is where systeroid comes in to help:

```sh
systeroid --explain kernel.oops_all_cpu_backtrace
```

The command above will fetch the corresponding information from the Linux kernel documentation and yield the results to stdout via `less`. Similarly, you can run systeroid with `-E, --explain` argument to retrieve information about any other parameter (as long as they are present in the kernel documentation).

So how does this work?

As you might have guessed, you need to have the kernel documentation "installed" or cloned somewhere. Distro packages solve this by having the kernel documentation as a separate package and in that case, all you have to do is install that package. (e.g. [linux-docs](https://archlinux.org/packages/core/x86_64/linux-docs/) in Arch Linux) In case this is not applicable, you need to clone the kernel repository and specify a path via `-D, --docs` argument. There is a [convenience script](https://github.com/orhun/systeroid/blob/main/scripts/clone-linux-docs.sh) in the repository.

```sh
# specify the kernel documentation path explicitly
# (not needed if you have the kernel documentation installed as a package)
systeroid -E user.max_user_namespaces -D /usr/share/doc/linux
```

After the kernel documentation is supplied to systeroid, it parses the corresponding sysctl files (using [parseit](https://github.com/orhun/parseit) crate) and caches them for the next run. This way, documentation about each parameter can be shown to the user.

For more information, see the [README.md#showing-information-about-parameters](https://github.com/orhun/systeroid#showing-information-about-parameters).

#### TUI

systeroid has a terminal interface that can be launched via `systeroid-tui` (binary) or `systeroid --tui` (alias).

![systeroid-tui](https://raw.githubusercontent.com/orhun/systeroid/main/assets/systeroid-tui-set-value.gif)

Currently, the following actions are supported:

- [Browse the available parameters](https://github.com/orhun/systeroid#scrolling)
- [View the documentation](https://github.com/orhun/systeroid#viewing-the-parameter-documentation)
- [Search for parameters](https://github.com/orhun/systeroid#searching)
- [Update the value of a parameter](https://github.com/orhun/systeroid#setting-values-1)
- [Copy to clipboard](https://github.com/orhun/systeroid#copying-to-clipboard)
- [And more!](https://github.com/orhun/systeroid#running-commands)

The colors are also [customizable](https://github.com/orhun/systeroid#changing-the-colors) and I'm thinking of adding a [configuration file](https://github.com/orhun/systeroid/issues/12) to customize the TUI in the future.

### Endnote

My primary goal for the future of the project is simply adding more ways to conveniently manage the kernel parameters. I also plan to work on the [existing issues](https://github.com/orhun/systeroid/issues). I hope systeroid will help you out in ways that will improve your workflow and the security of your kernel :)

If you liked **systeroid** and/or my other projects on my GitHub, please consider supporting me on [GitHub Sponsors](https://github.com/sponsors/orhun) or [Patreon](https://www.patreon.com/orhunp)! 🐻

_Görüşmek üzere!_
