+++
title = "Open Source Grindset Explained"
date = 2022-12-25

[taxonomies]
categories = ["Thoughts"]
+++

Let's talk about how to develop an open sourcerer mindset.

<!-- more -->

<center>

<img src="/open-sourcerer.png"/>

</center>

Very much like any other open source developer who has a great passion, I have been dreaming about doing open source full-time to make a living. Ever since I started to see the impact of open source and the spirit behind contributing I always wanted to keep doing it. And for a long time, I actually did it without thinking about it too much. It was just fun to share projects, contribute to others, and simply apply the open source philosophy to every part of my life. Well, even though you might say "just keep doing it if you like it that much", I think it is time for me to take things more seriously to get close to the _dream_ I initially mentioned.

There are a lot of good developers out there who contribute to open source in great magnitudes. Since I'm mainly interested in [Rust](https://www.rust-lang.org/), I would give [@dtolnay](https://github.com/dtolnay), author of [serde](https://crates.io/crates/serde), as an example. There is also [@epage](https://github.com/epage) who maintains [clap](https://crates.io/crates/clap). Another example would be [@burntsushi](https://github.com/BurntSushi/), who made the blazingly fast [ripgrep](https://crates.io/crates/ripgrep). The point where I'm getting is, there are many people with small/big open source projects who are also contributing to many other small/big open source projects that we use every day. After all, open source got to a point where its importance is now very apparent and non-negligible.

Among all of those people, [@fasterthanlime](https://github.com/fasterthanlime) got my attention in the last few months. He is already crushing it with [very detailed articles](https://fasterthanli.me/articles) (I mean, 60 minutes read time?!) and one of his articles about [going full-time open sourcerer](https://fasterthanli.me/articles/becoming-fasterthanlime-full-time) opened my third eye, so to speak. Ever since then, I always kept this move in mind as an example of how my _little dream_ is actually achievable.

So why am I talking about all of these? Well, I might have finally put together a _strategy_ for being a more successful open source developer and it is time to share it. I hope something will come out at the end of these crazy ramblings.
(fingers crossed)

### Open Source Grindset

Before I talk about how I do/plan to do open source, I should clarify what "**grindset**" means. It is the new hip word that all the cool kids use and it simply means "grind mindset". Here are a few definitions:

From [Urban Dictionary](https://www.urbandictionary.com/define.php?term=grindset):

> The state of mind one puts oneself in, in order to outperform fears, insecurities, doubts, and anxieties, to achieve the seemingly impossible and step into their greatness.

And [Wiktionary](https://en.wiktionary.org/wiki/grindset) says:

> A hypothetical collection of thoughts and actions one may undertake to improve one's financial and social life and to stay on the 'grind'.

So, where does that leave us?

---

<span style="color:white">

<b><font color="#845faa">Open Source Grindset</font></b> is the state of mind that maximizes one's effort to contribute to open source and increase self-improvement to deepen the technical knowledge in every possible endeavor. It is the act of contributing to open source regardless of the circumstances and ignoring the demotivating outside factors until eventually making a living from it.

</span>

---

Let's break it down.

#### How to develop the Open Source Grindset?

There are not a lot of people that you can argue about "open source is being good." So of course, if you think that way, you would want to keep contributing since it keeps your motivation going and you feel that you can use your creativity without constraints. So in this case, we should talk about motivation first.

It is surely not easy to always keep your motivation high but there are surely some tricks. They would be out of the scope of this blog post so I would like to link [@ThePrimeagen's video](https://youtube.com/watch?v=fBayRA8o3yQ) which summarizes it very well.

To approach this motivation issue from a different perspective, I would like to talk about how you can benefit from contributing to open source and how might having Open Source Grindset affect your gain in that regard. For this, I will first give a hypothetical example. Afterward, I will be trying to back up this story with a real-world example. Hopefully, these examples will guide you to a realization.

##### Example I

Let's say that there is a frontend developer looking for a job and they have a mild interest in open source. Let's also assume that they have a couple of projects lying around on GitHub that they don't care about too much and the projects are collecting dust. One day, our innocent developer receives an email regarding one of their repositories having a dependency update. They immediately think "Damn you [dependabot](https://github.com/dependabot)!" but there is certainly a curiosity about this email. So they click the link and this page pops up:

![Dependabot update](/dependabot-ssri.png)

"Well, interesting." they thought. "What even is `ssri` and why/where am I using it?"

(Our protagonist turned out to be a junior developer and was not yet aware that JavaScript projects might have dependency trees that go up to the moon so let's ignore the second question for now.)

They finally click the link in the repository and it leads to [another](https://github.com/npm/ssri) repository:

![https://github.com/npm/ssri](/ssri-npm-github.png)

Looking around cluelessly, they saw this piece of code:

```js
const ssri = require("ssri");

const integrity =
  "sha512-9KhgCRIx/AmzC8xqYJTZRrnO8OW2Pxyl2DIMZSBOr0oDvtEFyht3xpp71j/r/pAe1DM+JI/A+line3jUBgzQ7A==?foo";

// Parsing and serializing
const parsed = ssri.parse(integrity);
ssri.stringify(parsed); // === integrity (works on non-Integrity objects)
parsed.toString(); // === integrity
```

"So `ssri` should be some kind of a validation technique, hmmm. But what is that `integrity` string? What is going on here?"

Finally, they decide to ask the daddy (Google) about this and it redirects them to [this document](https://w3c.github.io/webappsec-subresource-integrity):

> This specification defines a mechanism by which user agents may verify that a fetched resource has been delivered without unexpected manipulation.
>
> This document specifies such a validation scheme, extending two HTML elements with an `integrity` attribute that contains a cryptographic hash of the representation of the resource the author expects to load. For instance, an author may wish to load some framework from a shared server rather than hosting it on their own origin. Specifying that the expected SHA-384 hash of `https://example.com/example-framework.js` is `Li9vy3DqF8tnTXuiaAJuML3ky+er10rcgNR/VqsVpcw+ThHmYcwiB1pbOxEbzJr7` means that the user agent can verify that the data it loads from that URL matches that expected hash before executing the JavaScript it contains. This integrity verification significantly reduces the risk that an attacker can substitute malicious content.

"Hmm, okay. Let's see an example."

> An author wishes to use a content delivery network to improve performance for globally-distributed users. It is important, however, to ensure that the CDN’s servers deliver only the code the author expects them to deliver. To mitigate the risk that a CDN compromise (or unexpectedly malicious behavior) would change that site in unfortunate ways, the following integrity metadata is added to the link element included on the page:

```html
<link
  rel="stylesheet"
  href="https://site53.example.net/style.css"
  integrity="sha384-+/M6kredJcxdsqkczBUjMLvqyHb1K/JThDXWsBVxMEeZHEaMKEOEct339VItX1zB"
  crossorigin="anonymous"
/>
```

"Woah. So that `integrity` attribute along with the hash is called '[Standard Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity)'. Interesting."

With this sudden enlightenment, our junior developer remembers that they have a project where they use an external JavaScript resource without any subresource integrity. "Wouldn't it be nice to add this `integrity` attribute?" they say.

But how? Luckily, they found a website that generates an SRI hash from a resource URL:

> https://www.srihash.org

![https://www.srihash.org](/sri-hash-org.png)

"Great! But wait, can I do this from the terminal?"

```sh
$ shasum -b -a 384 jquery-3.6.3.slim.min.js  | awk '{ print $1 }' | xxd -r -p | base64
bX7tuwViPEjy88JZEAaUe+FHOLZ9a9syCt8Z8UJ1sDbfwEIKdcFyHhW3yVPdFhd1
```

"Nice. That was even easier."

---

Fast forward to the future, 2 weeks later, our junior developer is having a job interview with a big tech company and the interviewer asks the following question:

> Resources that we use such as JavaScript and CSS files are capable of performing dangerous actions such as password stealing. And usually, compromised third-party servers are used for this type of attack.
> However, these servers can provide invaluable services that we can’t simply go without, such as CDNs that reduce the total bandwidth usage of a site and serve files to the end-user much faster due to location-based caching.
> So it’s established that we need to sometimes rely on a host that we have no control over, but we also need to ensure that the content we receive from it is safe. What can we do?

Our fellow developer starts to get psyched as the interviewer finishes the question. They immediately remember the research they did 2 weeks ago, thanks to that Dependabot PR. Says with an excited voice:

"Oh, we can use `SRI` for that. It is a security policy that prevents the loading of resources that don’t match an expected hash. By doing this, if an attacker were to gain access to a file and modify its contents to contain malicious code, it wouldn’t match the hash we were expecting and not execute at all."

And there, they would not be able to answer this question if they just yolo-merged the Dependabot PR and did not take time to actually look through it.

The moral of the story is, `rabbit holes` are crucial to gain the **Open Source Grindset**. Even though we don't have a character who is excessively doing open source in this hypothetical example, they still hugely benefited from it. Imagine what you can achieve by being more interested/motivated toward open source.

---

##### Example II

Just recently I was working on closing the issues (implementing new features & fixing bugs) on my project [git-cliff](https://github.com/orhun/git-cliff). One of the issues was the following:

![https://github.com/orhun/git-cliff/issues/113](/git-cliff-issue-113.png)

Simply put, OP wanted to easily download the Debian package of `git-cliff` from GitHub releases. For this, I needed to somehow package `git-cliff` and provide it via releases.

Well, I decided to give [cargo-deb](https://github.com/kornelski/cargo-deb) a try. It was supposed to generate a Debian package from the information in `Cargo.toml`. Unfortunately, when I tried the `cargo-deb` command locally, I got the following error:

```sh
$ cargo-deb --strip --manifest-path git-cliff/Cargo.toml -v
cargo-deb: unable to read extended description from file: ../README.md
  because: No such file or directory (os error 2)
```

This was due to `git-cliff` using a readme file in the parent directory:

```sh
[package]
readme = "../README.md"
```

Well, that's a bummer. My next step was blindly diving into the code of `cargo-deb` and actually fixing the issue:

```diff
From f4ec6f6f6ee50bc86042e0abbc52d80c5ac34c21 Mon Sep 17 00:00:00 2001
From: =?UTF-8?q?Orhun=20Parmaks=C4=B1z?= <orhunparmaksiz@gmail.com>
Date: Thu, 22 Dec 2022 21:35:48 +0300
Subject: [PATCH] Use absolute path for package readme

---
 src/manifest.rs | 8 ++++++--
 1 file changed, 6 insertions(+), 2 deletions(-)

diff --git a/src/manifest.rs b/src/manifest.rs
index bcc9da5d..5a7aba6d 100644
--- a/src/manifest.rs
+++ b/src/manifest.rs
@@ -498,7 +498,11 @@ impl Config {
         manifest_check_config(package, package_manifest_dir, &deb, listener);
         let extended_description = manifest_extended_description(
             deb.extended_description.take(),
-            deb.extended_description_file.as_ref().map(Path::new).or(package.readme().as_path()),
+            deb.extended_description_file.as_ref().map(Path::new).or(package
+                .readme()
+                .as_path()
+                .and_then(|v| package_manifest_dir.join(v).canonicalize().ok())
+                .as_deref()),
         )?;
         let mut config = Config {
             default_timestamp,
@@ -987,7 +991,7 @@ This will be hard error in a future release of cargo-deb.", source_path.display(
             })
             .collect();
         if let OptionalFile::Path(readme) = package.readme() {
-            let path = PathBuf::from(readme);
+            let path = self.pacakge_manifest_dir.join(PathBuf::from(readme)).canonicalize()?;
             let target_path = Path::new("usr/share/doc")
                 .join(&package.name)
                 .join(path.file_name().ok_or("bad README path")?);
```

After fixing the issue, I integrated `cargo-deb` into my release workflow (via this [commit](https://github.com/orhun/git-cliff/commit/efd827f59f8394dd894ebd35a5d630ff558a3ebe)) and I was happy. It was time to share my fix with the upstream:

> [https://github.com/kornelski/cargo-deb/pull/62](https://github.com/kornelski/cargo-deb/pull/62)

And it got rejected. Apparently, `cargo` was also suffering from this issue and thankfully the author of `cargo-deb` redirected me to [this](https://github.com/rust-lang/cargo/issues/5911) issue:

![https://github.com/rust-lang/cargo/issues/5911](/cargo-issue-5911.png)

It was submitted in 2018 and is still open. Do you see where I'm getting at with this?

Even though I put some time into fixing this issue and my changes were rejected, it paved the way for another (and also a bigger) contribution opportunity. In the following days, I'm planning to focus on this `cargo` issue and see what I can do about it.

If I never dove into the code of `cargo-deb`, I would never know such a thing would exist for `cargo` as well. That is why it is important to take your time while you are contributing to open source, I believe.

---

In summary, the rules of developing an **Open Source Grindset** are:

- Take your time.
- Follow the rabbit holes.
- Read and learn as much as you can.
- Every contribution is a contribution regardless of its size.

### Conclusion

In this post, I tried to lay out the mindset that I developed over the years toward open source development. I think of this as a concretization of years of thinking/coding and an early-stage plan for success. We'll see!

If I get nice feedback about this post, I might write a sequel about dealing with burnout. So let me know what you think!

How can you support me? Well, I have [GitHub Sponsors](https://github.com/sponsors/orhun) and [Patreon](https://www.patreon.com/join/orhunp) 🐻

_bye!_
