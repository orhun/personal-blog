+++
title = "My First Month as an Arch Linux Trusted User"
date = 2021-02-06

[taxonomies]
categories = ["Activities"]
+++

There's a start for everything. A new blog... New responsibilities... Maybe a new role in a Linux distribution that is for advanced users...

<!-- more -->

There's a start for everything.

A new blog... New responsibilities... Maybe a new role in a Linux distribution that is for advanced users...

No, this is not a blog post about how did I join [Arch Linux](https://archlinux.org/) as a [Trusted User](https://wiki.archlinux.org/index.php/Trusted_Users) in January 2021. You can read my [TU application](https://lists.archlinux.org/pipermail/aur-general/2020-December/036036.html) if you came for that. This is more like the aftermath...

So right after I've been onboarded, I immediately tried to adapt to my new role and do everything I can in order to be more helpful to this awesome distribution. Well, everything comes with a cost apparently. My agility caused some (tiny?) problems (for me and others) but I also got the chance to do/learn awesome stuff. Here in this very blog post, I'd like to tell what happened in my first month at Arch Linux... for future packagers and for those who wonder what I've been doing in the meantime. Buckle up!

## The Good Stuff

### Package additions to the [community]

If you know me, you probably know that I like the most _memetastic_ programming language [Rust](https://www.rust-lang.org/). So I moved a fair amount of tools that are written in Rust along with some other nice projects from [AUR](https://aur.archlinux.org/) to the [community](https://wiki.archlinux.org/index.php/Official_repositories#community) repository.

```
addpkg: menyoki 1.2.0-2
addpkg: lychee-link-checker 0.5.0-1
addpkg: viu 1.3.0-3
addpkg: onefetch 2.9.0-2
addpkg: ali 0.5.4-3
addpkg: gping 1.2.0-2
addpkg: zps 1.2.4-1
addpkg: cargo-spellcheck 0.6.0-1
addpkg: shy 0.1.10-3
addpkg: hex 0.4.0-1
addpkg: procs 0.11.3-1
addpkg: cargo-bloat 0.10.0-2
addpkg: cargo-depgraph 1.2.2-2
addpkg: cargo-udeps 0.1.17-2
addpkg: taskwarrior-tui 0.9.8-1
```

### Package adoptions in [community]

I adopted the following packages to help {co-,}maintain them:

* [cargo-edit](https://www.archlinux.org/packages/community/x86_64/cargo-edit/)
* [cargo-outdated](https://www.archlinux.org/packages/community/x86_64/cargo-outdated/)
* [cargo-tarpaulin](https://www.archlinux.org/packages/community/x86_64/cargo-tarpaulin/)
* [dog](https://www.archlinux.org/packages/community/x86_64/dog/)
* [dumb](https://www.archlinux.org/packages/community/x86_64/dumb/)
* [hexyl](https://www.archlinux.org/packages/community/x86_64/hexyl/)
* [intellij-idea-community-edition](https://www.archlinux.org/packages/community/x86_64/intellij-idea-community-edition/)
* [kmon](https://www.archlinux.org/packages/community/x86_64/kmon/)
* [lsd](https://www.archlinux.org/packages/community/x86_64/lsd/)
* [mari0](https://www.archlinux.org/packages/community/x86_64/mari0/)
* [mdbook](https://www.archlinux.org/packages/community/x86_64/mdbook/)
* [mdp](https://www.archlinux.org/packages/community/x86_64/mdp/)
* [pycharm-community-edition](https://www.archlinux.org/packages/community/x86_64/pycharm-community-edition/)
* [rust-racer](https://www.archlinux.org/packages/community/x86_64/rust-racer/)
* [scrot](https://www.archlinux.org/packages/community/x86_64/scrot/)
* [task](https://www.archlinux.org/packages/community/x86_64/task/)
* [tokei](https://www.archlinux.org/packages/community/x86_64/tokei/)
* [xdo](https://www.archlinux.org/packages/community/x86_64/xdo/)

## Mistakes

At first, I couldn't think of asking AUR maintainers and TUs for permission to move their packages to the [community]. (silly me!) At least no one complained and everyone seemed to be happy about it. I'll remember to be more _courteous_ about this from now on :)

But the biggest mistake I ever made happened during I was trying to fix [FS#69291](https://bugs.archlinux.org/task/69291)... I somehow overlooked the latest _stable_ version of `IntelliJ IDEA` and built/released the 2021 version which is unstable and not "intended for production". However, someone realized my mistake and filled a bug report quickly enough to avert the crisis. ([FS#69461](https://bugs.archlinux.org/task/69461)) (oof!)

## Conclusion

Apart from those, I also helped to handle the AUR requests, fixed some bugs, and contributed to [arch-rebuild-order](https://gitlab.archlinux.org/archlinux/arch-rebuild-order/).

I hope I'll be more useful for the distribution in the future. Arch Linux is such a nice project that I'm glad I'm a part of it :)
