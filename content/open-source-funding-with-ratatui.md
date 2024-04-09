+++
title = "Ratatui Received Funding: What's Next?"
date = 2024-04-08

[taxonomies]
categories = ["Ratatui", "Thoughts"]
+++

Let's delve into the realm of open source funding along with Ratatui's journey.

<!-- more -->

<details>
<summary><b>Table of Contents</b></summary>

<!-- vim-markdown-toc GFM -->

- [**Ratatui**](#ratatui)
- [**What happened?**](#what-happened)
- [**Our Strategy**](#our-strategy)
  - [**Managing Funds**](#managing-funds)
- [**The Future**](#the-future)
  - [**Expenses**](#expenses)
  - [**Sustainability**](#sustainability)
  - [**Crucial tasks**](#crucial-tasks)
  - [**Supporting our dependencies**](#supporting-our-dependencies)
  - [**Non-profit model**](#non-profit-model)
  - [**Beyond money**](#beyond-money)
- [**Closing**](#closing)
  - [**Acknowledgements**](#acknowledgements)

<!-- vim-markdown-toc -->

</details>

<br>

## **Ratatui**

<img src="https://github.com/ratatui-org/ratatui/blob/images/examples/demo2.gif?raw=true" width="70%"/>

In case you haven't been following, I have been actively taking part in an open source project called <g>[Ratatui](https://ratatui.rs/)</g> since last year. It is the community maintained fork of `tui-rs` project which is a [Rust](https://www.rust-lang.org) library for building terminal user interfaces. Ratatui is the [successor](https://github.com/fdehau/tui-rs/commit/335f5a4563342f9a4ee19e2462059e1159dcbf25) of tui-rs and I'm very proud to say that we have built an amazing ever-growing community around the Rust/TUI ecosystem. These [awesome TUI tools and libraries](https://github.com/ratatui-org/awesome-ratatui) and the expanding adoption from companies (such as [Netflix's bpftop](https://github.com/Netflix/bpftop)) are some of the biggest motivations for us to elevate Ratatui to make it the future of the terminal.

Check out our GitHub here: <https://github.com/ratatui-org/ratatui>

---

## **What happened?**

In November last year, we got an issue in GitHub with the title of <g>"Donating Funds to Ratatui"</g>.  
The contents were the following:

> Hi Ratatui, I'm one of the core contributors to the [Radworks](https://radworks.org) project.
>
> [Radicle](https://radicle.xyz) has been using Ratatui. It's in fact _one of our most critical dependencies_!
>
> All Radicle’s core-contributors really appreciate the work you all do and we want to make sure that you are all well-funded to keep doing it.
>
> For that reason, we have decided to donate some funds to your project through [Drips](https://www.drips.network/)  
> (an initiative that is coming out of our organization for funding FOSS).

The problem and the proposed solution in the issue were even more interesting:

> **Problem**: None, you've done amazing work. This is a thank you.
>
> **Solution**: Claim funds. Engage on a call to understand what we are trying to do here.
>
> If you don't need 100% of the funds, share with your own critical dependencies.

<q>I especially like the last part!</q>

Neither I, nor anyone in the team had any idea about this so we quickly started researching what this meant for us. Luke from Radworks was also kind enough to hop on a call with us to explain the process and how the Drips Network works.

Here are some relevant links if you are interested in reading more about this:

- [Drips Network / Radicle Dependencies](https://www.drips.network/app/drip-lists/34625983682950977210847096367816372822461201185275535522726531049130)
  - You can see <g>Ratatui</g> in there along with other projects!
- [Podcast talk](https://www.youtube.com/watch?v=Z8NR2IQhBso)

At the end of the day, it seemed like we had a funding of $20k+.

<img src="/radicle-dependencies.png" width="75%"/>

This was so unexpected and surprising considering that no one was/is working on Ratatui with financial expectations. But no one was opposed to the idea of covering our future expenses!

---

## **Our Strategy**

When it comes to funding open source, I see a couple of viable options:

- [GitHub Sponsors](https://github.com/sponsors)
- [Open Collective](https://opencollective.com)
- One-time Donation Platforms (e.g. [Buy Me A Coffee](https://www.buymeacoffee.com/))
- Grants (such as [NLNet Foundation](https://nlnet.nl))

Our situation did not fully apply to any of these, so we have to come up with something.

I saw that [Sebastian Thiel (@byron)](https://github.com/Byron) (maintainer of [Gitoxide](https://github.com/Byron/gitoxide)) already claimed funds on Drips so I reached out to him about his experience. He gave us very detailed information about his crypto setup, but unlike him, his solution didn't fully fit our needs as a team of 5+ people, because it would be challenging for a single person to manage the funds.

And... taxes? Nobody wants to be responsible for random money sitting in their bank accounts.

<q>As some Turkish saying goes: "If you have the money then you have the problem."</q>

In hindsight, this made us re-consider the possibility of applying for GitHub Sponsors. After some discussion, we also decided to set up Open Collective alongside of GitHub Sponsors and use Open Collective to manage our funds. We [registered](https://docs.oscollective.org/campagins-programs-and-partnerships/github-sponsors) [our collective](https://opencollective.com/ratatui) to GitHub Sponsors which enabled us to manage everything at one place. Pretty neat feature!

We looked into the possibility of doing the same with Drips Network and eventually decided that _if Open Collective is able to create a crypto wallet for us_, that will presumably be the easiest way to send funds from Drips Network to Open Collective. They would handle taxes and other legal matters for us, and it would be just perfect.

For the curious, [Open Source Collective](https://www.oscollective.org) (our fiscal host) takes a 10% cut for operating costs.

### **Managing Funds**

Shout out to [Benjamin Nickolls (@benjam)](https://github.com/BenJam) from Open Collective, he greatly helped us to figure out the crypto-related details of things and set up a multi-signature [wallet](https://github.com/ratatui-org/ratatui/pull/994). After this, we managed to get everything _dripping_ 💧

All of these were in the works for the past couple of months. We now have the following setup and everything works like a charm!

![Ratatui Funding Diagram](/ratatui-funding-diagram.png)

You can see the related [transactions](https://opencollective.com/ratatui/transactions) on Open Collective:

![Ratatui Transactions](/ratatui-transactions.png)

---

## **The Future**

Now that we are an open source project and the money is involved, we need to draw some lines about how we manage these funds. Additionally, we need to come up with a thinking model that will be taken as a baseline when it comes to utilizing this financial source.

### **Expenses**

In the simplest terms, the primary and the most traditional reason that companies make money is to stay afloat and expand their _business_.

In our case, these are already semi-covered thanks to open source. We don't usually worry about the project dying, because if that happens tomorrow someone else might fork the project and start Ratatui v2 (which is exactly what we did).

However, there might be still financial costs of maintaining an open source project. These include things such as a custom domain, additional services/servers, promotional elements and so on.

Thus we will be using these funds to cover these maintenance/promotional costs.

### **Sustainability**

As companies receive investment, they just don't go "hey we got the money let's go home y'all".

That is the same in our case as well. We still have a lot to do.

<q>So can't we just pay people to do work?</q>

That's the beauty of open source, people are _already_ doing work. Not because they want to get rich off of the project, but because they want to learn new things and have some fun on the side.

Paying people for their open source work will also push the incentive that everyone might get paid through this work. Which means when there is no money left, there might not be a project left to work on.

However, don't get this wrong, this isn't a sneaky approach to make people work for free. We will still support people / other projects that we depend on. Finding the balance is the key.

It is good to remember that this policy works for us and is not a one-size-fits-all solution. ⚠️

### **Crucial tasks**

We are happy to support people who are working on core/crucial/technical tasks that won't be worked on otherwise. Some examples might be,

- a port of Ratatui on a specific platform (such as supporting embedded LCD displays),
- a WASM/Web framework built on top of Ratatui,
- a full-fledged game engine that runs on the terminal.

This does not give the promise that we will throw money at you if you do one of these.

But we absolutely don't say the otherwise 🐭

This is similar to [Neovim's approach](https://neovim.io/sponsors) where funded contributors are expected to provide full-time, high-quality contributions on GitHub regularly.

### **Supporting our dependencies**

Ratatui is dependent on other Rust libraries such as [crossterm](https://github.com/crossterm-rs/crossterm).

We don't want them to go unmaintained, so we are considering to support these other open source projects as well.

### **Non-profit model**

To understand the way that we are aiming to manage our funds, we can take a look at the differences between "Company vs. Non-profit":

| **Category**        | **Company**                                                | **Non-profit**                                                         |
| ------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------- |
| Focus               | Profit generation and maximizing shareholder value         | Primarily focused on serving a mission or cause                        |
| Motive              | For-profit                                                 | Not-for-profit                                                         |
| Profit Distribution | Can distribute profits to shareholders                     | Cannot distribute profits to individuals or shareholders               |
| Structure           | Generally structured with a hierarchical management system | Often structured with a board of directors or trustees                 |
| Tax Obligations     | Can face tax obligations on profits                        | May be eligible for tax-exempt status and can receive donations/grants |

We are closer to being a non-profit in that sense.

That is why we don't split the funds among maintainers / project members.

### **Beyond money**

<q>Why do people work on this? You invest your time and make no money. What is the point?</q>

Money is not everything.

The contributors likely stand to gain more from [second-order effects](https://fs.blog/second-order-thinking) such as:

- The experience you gain from working on the project
  - as a Rust programmer, team member, large project maintainer, etc.
- Greater hireability
  - the project is a great portfolio piece, and is also well known in developer circles so it also makes networking easy.
- Community engagement
  - active participation fosters a sense of belonging and camaraderie.

TL;DR: <g>open source is a beautiful thing.</g> 🌸

---

## **Closing**

We will be managing the donations/sponsorships we receive through [our Open Collective](https://opencollective.com/ratatui) transparently in line with the principles outlined in this blog post.

We will be also reaching out to the creator of tui-rs ([Florian Dehau (@fdehau)](https://github.com/fdehau)) about offering a one-off donation for his past work on this awesome library. I will update this post with any developments.

To follow the updates:

- [@ratatui_rs](https://twitter.com/ratatui_rs) on Twitter
- [@ratatui_rs](https://fosstodon.org/@ratatui_rs) on Mastodon

- [@orhun](https://github.com/orhun) on GitHub
- [@orhunp\_](https://twitter.com/orhunp_) on Twitter
- [@orhun](https://fosstodon.org/@orhun) on Mastodon

### **Acknowledgements**

💖 Huge thanks to:

- [Radworks](https://radworks.org) for their funding donation.
- Ratatui maintainers
  - [Dheepak Krishnamurthy (@kdheepak)](https://github.com/kdheepak)
  - [Josh McKinney (@joshka)](https://github.com/joshka)
  - [Leon Sautour (@mindoodoo)](https://github.com/mindoodoo)
  - [Arijit Basu (@sayanarijit)](https://github.com/sayanarijit)
  - [@Valentin271](https://github.com/valentin271)
  - [@EdJoPaTo](https://github.com/EdJoPaTo)
- Ratatui moderators (kaliwowski, owletti, [@tranzystorekk](https://github.com/tranzystorekk))
- [Benjamin Nickolls (@benjam)](https://github.com/BenJam) from Open Collective
- Lucas Fada from Radicle
- [Sebastian Thiel (@byron)](https://github.com/Byron) from Gitoxide
- [Erlend Sogge Heggen (@erlend-sh)](https://github.com/erlend-sh) for his valuable open source and funding advisories.

🥂
