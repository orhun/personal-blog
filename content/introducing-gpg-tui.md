+++
title = "Introducing gpg-tui, a Terminal User Interface for GnuPG"
date = 2021-05-29

[taxonomies]
categories = ["Projects"]
+++

gpg-tui is a TUI for managing the GnuPG keys 🔐 In this post, I'm giving a brief introduction to the project as well as describing the thought process and main development challenges behind it.

<!-- more -->

[![Logo](https://github.com/orhun/gpg-tui/raw/master/assets/logo.jpg)](https://github.com/orhun/gpg-tui)

<center>

<a href="https://github.com/orhun/gpg-tui"><b>https://github.com/orhun/gpg-tui</b></a>

</center>

## Background

[GnuPG](https://gnupg.org/) is a well-known implementation of the [OpenPGP](https://www.ietf.org/rfc/rfc4880.txt) standard which is been used for years in various communities and projects. It's also known as "GPG", which is the name of the command line tool that makes it easier to integrate with other applications. It is battle-tested over the years and has a wealth of frontend applications and libraries.

My past with GnuPG is roughly the same with an ordinary developer who is into programming, open source, and related concepts. So I cannot say that I was using it for more than encryption/decryption/verification of files/emails and different types of authentication. It is a tool basically for securing the communication for me.

But some things about gpg, the CLI tool, were bugging me. I remember times that I have to run multiple commands to get fully informed about a key. Or I have to process its output to get the information that I needed. (using `grep`, `sed`, etc.) Not to mention that some of the output of gpg is quite confusing if you're unfamiliar with the GnuPG terms. So for years, I was thinking that it'd be nice to have a more user-friendly and interactive tool to handle the tasks that gpg is trying to accomplish. These tasks include key management operations such as listing (showing subkeys, user ids, signatures), signing, deleting keys, etc.

## Plan

So I decided to write this tool in [Rust](https://www.rust-lang.org/), using [tui-rs](https://github.com/fdehau/tui-rs) and [bindings](https://github.com/gpg-rs/gpgme) for the [GPGME library](https://www.gnupg.org/software/gpgme/index.html).

GPGME uses GnuPG's OpenPGP backend as default to provide a high-level crypto API for various operations including _key management_, which was the thing I needed. And I have written a TUI program using tui-rs [before](https://github.com/orhun/kmon), so why not use it again?

## Implementation

I'd like to point out the 2 main issues that I've dealt with during the development process.

### Listing Format

I started with the very basic: _listing keys_. Thankfully GPGME had an example for it, so it was supposed to be _easy_ to implement.

Output from GPGME [example](https://github.com/gpg-rs/gpgme/blob/master/examples/keylist.rs):

```
keyid   : E19F76D037BD65B6
fpr     : B14085A20355B74DE0CE0FA1E19F76D037BD65B6
caps    : esc
flags   :
userid 0: Example Key <example@key>
valid  0: Validity::Ultimate(5)
userid 1: Other User ID <example@key>
valid  1: Validity::Ultimate(5)
```

Ta-da! It's done.

Well, no. It was good and well until I reached the point where I realized I should come up with my own listing format. If I use this format or the format that `gpg -k` uses, I'm afraid I can't extend it for my needs. So I played around with different formats a little bit and found an optimal one that can expand/shrink to show more/fewer details about an individual key. It consists of two columns which are [key information](https://github.com/orhun/gpg-tui#key-information) and [user information](https://github.com/orhun/gpg-tui#user-information):

```
[sc--] rsa3072/B14085A20355B74DE0CE0FA1E19F76D037BD65B6  │  [u] Example Key <example@key>
|      └─(2021-05-14)                                    │   │  └─[13] selfsig (2021-05-16)
[--e-] rsa3072/E56CAC142AE5A979BEECB00FB4F68595CAD4E7E5  │   └─[u] Other User ID <example@key>
       └─(2021-05-14)                                              ├─[13] selfsig (2021-05-16)
                                                                   └─[10] 84C39331F6F85326 Other Signer Key <example@signer> (2021-05-16)
```

Thanks to tree-like output, everything was clearer and it was possible to show more detail without worrying about making the output look complicated.

Here's the `gpg -k` output of the same key:

```
pub   rsa3072 2021-05-14 [SC]
      B14085A20355B74DE0CE0FA1E19F76D037BD65B6
uid           [ultimate] Example Key <example@key>
uid           [ultimate] Other User ID <example@key>
sub   rsa3072 2021-05-14 [E]
```

I explain this custom format in detail in the project's [documentation](https://github.com/orhun/gpg-tui#approach).

After figuring out the format that I think the most suitable for the key listing, I made it possible to [toggle the detail level](https://github.com/orhun/gpg-tui#detailed-view) that list entries shows by assigning a key to it. So it will be not only possible to interactively select/scroll these keys, the user was also supposed to be able to see the other user ids and signatures if they want. That was a plus one.

![Listing Keys](https://user-images.githubusercontent.com/24392180/119889592-6f889900-bf3f-11eb-85fc-c9c3e445fb81.gif)

### GPG Fallback

My next step was figuring out which operations that I wanted to support. I knew that [gpg manpage](https://linux.die.net/man/1/gpg) had a section called "How to manage your keys", so I decided to go along with it. The plan is to support all of these features, with the extensions/flexibility that having a TUI provides.

After implementing a couple of those features using GPGME, I realized it was not possible to do it all due to various reasons.

> well, whatever.

I thought I can get help from gpg for these cases. It's already a powerful tool, so why not use it instead of designing custom tabs/widgets/layouts for each feature? I can pause the TUI during the execution of a gpg command and resume it afterward. Seems like a good idea.

So I did. And I'm pretty happy with the result. Until I decide to extend the TUI for these operations, I think it's better to get help from 3-4 external gpg commands.

![GPG Fallback](https://user-images.githubusercontent.com/24392180/119894905-df9a1d80-bf45-11eb-8600-3b3c4e340794.gif)

## "GPG on steroids"

And so my journey continued and I implemented the commonly used gpg operations for my interface. I also added some extra features to use the real power of having a TUI. And since I'm designing a TUI for GPG, I named the project **gpg-tui**. Just simple.

Here's the end result:

![Demo](https://github.com/orhun/gpg-tui/raw/master/demo/gpg-tui-showcase.gif)

As of the initial release, **gpg-tui** can do the following:

- [List keys](https://github.com/orhun/gpg-tui#list)
- [Export keys](https://github.com/orhun/gpg-tui#export)
- [Sign keys](https://github.com/orhun/gpg-tui#sign)
- [Edit keys](https://github.com/orhun/gpg-tui#edit)
- [Import/receive keys](https://github.com/orhun/gpg-tui#importreceive)
- [Send keys](https://github.com/orhun/gpg-tui#send)
- [Generate keys](https://github.com/orhun/gpg-tui#generate)
- [Delete keys](https://github.com/orhun/gpg-tui#delete)
- [Refresh keys](https://github.com/orhun/gpg-tui#refresh)

With lots of additional TUI features like [copy/paste mode](https://github.com/orhun/gpg-tui#copy--paste), [options menu](https://github.com/orhun/gpg-tui#options-menu), and [command prompt](https://github.com/orhun/gpg-tui#running-commands)!

See [README.md](https://github.com/orhun/gpg-tui/blob/master/README.md) for more information.

## Endnote

My goal for the next releases is to implement new features that will make key management even easier. I wish a project that is born solely from my needs will also be useful for other people.

- [Homepage of gpg-tui](https://github.com/orhun/gpg-tui) (GitHub)
- [My GitHub profile](https://github.com/orhun) (**@orhun**, check out my other projects!)
- [Follow me on Twitter](https://twitter.com/orhunp_) (**@orhunp_**)
- [Follow gpg-tui on Twitter](https://twitter.com/gpg_tui) (**@gpg_tui**, for latest updates) 

Also if you liked **gpg-tui** and/or my other projects, consider [becoming a patron](https://www.patreon.com/join/orhunp) at [Patreon](https://www.patreon.com/orhunp).

(I recently got my first ever patron so I'd like to thank Ethem for their nice surprise and support :D)

Have a good day!
