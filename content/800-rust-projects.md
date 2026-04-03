+++
title = "800 Rust terminal projects in 3 years"
date = 2026-04-03

[taxonomies]
categories = ["Projects"]
+++

I have discovered and shared ~800 open source Rust CLI projects over the past 3 years.

<!-- more -->

<a href="https://static.orhun.dev/800-projects.png">
<img class="glowing-border" src="/800-posts.png" width="90%"/>
</a>

<i>Above is a collage of screenshots from hundreds of Rust terminal and TUI projects<br>shared on Mastodon.</i>

[🔗 Click here to jump to the "Top 99 Rust terminal projects"!](#top-99-rust-terminal-tools)

If you follow me on [X](https://x.com/orhundev)/[Mastodon](https://fosstodon.org/@orhun)/[Bluesky](https://bsky.app/profile/orhun.dev)/[LinkedIn](https://www.linkedin.com/in/orhunp/), you might be familiar with my regular posts that usually go like this:

> I have discovered a 🐀 today!
>
> 🐀 - a rat!
>
> 💯 Does rat things
>
> ⭐ GitHub: ...
>
> #rat

Today, I have realized it has been a while since I started doing this and wondered what I could do with everything I collected over the years.
So here we are!

But first, I feel like you might have some questions.

<q>Uhh, yeah. I have one: "Why?"</q>

I have detailed my motivation and strategy towards open source [in this blog post](https://blog.orhun.dev/open-source-grindset/) and [conference talk](https://www.youtube.com/watch?v=HDU8dpt-tNE), but in a nutshell:

**1**\. I like Rust and I want more people to use it after seeing what they can build with it,  
**2**\. I maintain [Ratatui](https://ratatui.rs) and sharing projects built with it helps grow the ecosystem,  
**3**\. It helps surface hidden gems that would otherwise go unnoticed,  
**4**\. It's hella fun.

Recently, one of the Rust ecosystem leaders [said](https://bsky.app/profile/nikomatsakis.com/post/3mhssugo65c2f):

"<i>I love these posts :) highlight of my day to see all the awesome stuff people do</i>"

I also received many similar messages over the years, so I guess that's a good enough reason to keep doing it!

<q>How do you discover projects?</q>

In the same way that everyone does: through GitHub, social media and word of mouth.  
A couple of my special sources include:

**1**\. Discord servers like [Ratatui](https://discord.gg/pMCEU9hNEj), [Grindhouse](https://discord.com/invite/vdUh3KTBPX) and [Terminal Collective](https://discord.gg/6EUERBrAMs)  
(follow channels like `#showcase` and `#show-and-tell` for the good stuff!)

**2**\. [Terminal Trove newsletter](https://terminaltrove.com/) for weekly curated terminal projects  
(they sponsored me a while back which I'm super grateful for, definitely give them a sub!)

**3**\. GitHub search, e.g. [searching for Ratatui projects](https://github.com/search?q=ratatui&type=repositories&s=updated&o=desc)  
(this method is underrated)

Then I maintain a list of things to post in a plain text file and try to post something every day. It's probably fine if I miss a day or two, but I try to keep the cheese rolling 🧀

## Looking at the data 👀

So, I have ran the numbers:

> Posts analyzed: 786  
> Posting range: 2022-12-15 to 2026-03-31  
> Total favourites: 10655  
> Total reblogs: 4529  
> Total replies: 658  
> Average favourites per post: 13.56  
> Average reblogs per post: 5.76  
> Average replies per post: 0.84  
> Active days: 653  
> Covered date range: 2022-12-15 to 2026-03-31 (1203 days)  
> Average posts per active day: 1.20

Please note that those are aggregated from <g>Mastodon only</g>, since the other platforms weren't that friendly for automation:

| **Platform**                                          | **Public API?**  | **Reality**             |
| ----------------------------------------------------- | ---------------- | ----------------------- |
| [X (Twitter)](https://x.com/orhundev)                 | ❌ (paid/locked) | Expensive / restricted  |
| [Mastodon (Fosstodon)](https://fosstodon.org/@orhun/) | ✅               | Fully open, best option |
| [Bluesky](https://bsky.app/profile/orhun.dev)         | ✅               | Modern + dev-friendly   |
| [LinkedIn](https://www.linkedin.com/in/orhunp/)       | ❌ (restricted)  | Only for approved apps  |

I put together a Rust script to fetch my Mastodon project posts, save them as structured JSON, and analyze their posting and engagement stats.
Luckily [megalodon-rs](https://github.com/h3poteto/megalodon-rs) exists, which made everything super easy. The full script is available [on GitHub](https://github.com/orhun/mastodon-post-analyzer/blob/main/src/main.rs) ⭐

With using the data over the years, I present you the Top 99 Rust terminal projects as of April 2026! 🏆

Disclaimers:

- The ranking is based on the number of favourites x reblogs x replies each post received [on Mastodon](https://fosstodon.org/@orhun), which may not be a perfect indicator of a project's quality or popularity. But it's a fun way to see which projects resonated with y'all the most!
- On other platforms, the ranking might be different due to different audiences and engagement patterns.
- The Ratatui projects (`github.com/ratatui`) and my personal projects (`github.com/orhun`) are excluded from the ranking to keep it fair.

I'm sure there is something in there for everyone!

# **Top 99 Rust Terminal Tools**

| **Rank** | **Project**                | **Description**                                                            | **Repo**                                                                                          |                                              **Score** |
| -------- | -------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | -----------------------------------------------------: |
| 🥇       | 🌸 **cyme**                | A modern and cross-platform lsusb                                          | [`tuna-f1sh/cyme`](https://github.com/tuna-f1sh/cyme)                                             | [369](https://fosstodon.org/@orhun/113124441267143423) |
| 🥈       | 🥒 **gurk**                | Signal Messenger client for the terminal                                   | [`boxdot/gurk-rs`](https://github.com/boxdot/gurk-rs)                                             | [214](https://fosstodon.org/@orhun/114007249344819263) |
| 🥉       | ⚔️ **ratthew**             | A 3D dungeon crawler in the terminal                                       | [`cxreiff/ratthew`](https://github.com/cxreiff/ratthew)                                           | [197](https://fosstodon.org/@orhun/114421390014777917) |
| 4        | 📡 **atac**                | A simple API client in your terminal                                       | [`Julien-cpsn/ATAC`](https://github.com/Julien-cpsn/ATAC)                                         | [163](https://fosstodon.org/@orhun/112281288353834485) |
| 5        | 💾 **monolith**            | CLI tool for saving complete web pages                                     | [`Y2Z/monolith`](https://github.com/Y2Z/monolith)                                                 | [149](https://fosstodon.org/@orhun/112852693518362858) |
| 6        | 🖌️ **brush**               | Bash/POSIX-compatible shell implemented in Rust                            | [`reubeno/brush`](https://github.com/reubeno/brush)                                               | [119](https://fosstodon.org/@orhun/114371024652516269) |
| 7        | 📨 **iamb**                | A Matrix client for Vim addicts                                            | [`ulyssa/iamb`](https://github.com/ulyssa/iamb)                                                   | [114](https://fosstodon.org/@orhun/112008978775866361) |
| 8        | 📄 **doxx**                | Terminal-based document viewer for Microsoft Word files                    | [`bgreenwell/doxx`](https://github.com/bgreenwell/doxx)                                           | [109](https://fosstodon.org/@orhun/115066127573137095) |
| 9        | 🔎 **flamelens**           | An interactive flamegraph viewer for the terminal                          | [`YS-L/flamelens`](https://github.com/YS-L/flamelens)                                             |  [90](https://fosstodon.org/@orhun/114018543956542396) |
| 10       | 💽 **caligula**            | A user-friendly, lightweight TUI for disk imaging                          | [`ifd3f/caligula`](https://github.com/ifd3f/caligula)                                             |  [89](https://fosstodon.org/@orhun/112389284218391496) |
| 11       | 🛠️ **heretek**             | A gdb TUI dashboard                                                        | [`wcampbell0x2a/heretek`](https://github.com/wcampbell0x2a/heretek)                               |  [88](https://fosstodon.org/@orhun/114302362201072129) |
| 12       | 🎮 **Plastic**             | A NES emulator that runs in your terminal                                  | [`Amjad50/plastic`](https://github.com/Amjad50/plastic)                                           |  [87](https://fosstodon.org/@orhun/113350571052740107) |
| 13       | 🧩 **setrixtui**           | A TUI puzzle game where falling blocks become sand                         | [`Mjoyufull/Setrixtui`](https://github.com/Mjoyufull/Setrixtui)                                   |  [81](https://fosstodon.org/@orhun/116059079702467991) |
| 14       | 🎬 **gitlogue**            | A cinematic Git commit replay tool for the terminal                        | [`unhappychoice/gitlogue`](https://github.com/unhappychoice/gitlogue)                             |  [79](https://fosstodon.org/@orhun/115576266511493186) |
| 15       | 🌀 **cute**                | TUI HTTP client with API/auth key management and request history/storage   | [`PThorpe92/CuTE`](https://github.com/PThorpe92/CuTE)                                             |  [73](https://fosstodon.org/@orhun/112263799395387466) |
| 16       | 🚀 **serie**               | A rich git commit graph in your terminal, like magic                       | [`lusingander/serie`](https://github.com/lusingander/serie)                                       |  [71](https://fosstodon.org/@orhun/112869522457979235) |
| 17       | ⚙️ **systemctl-tui**       | A fast and simple TUI for interacting with systemd services and their logs | [`rgwood/systemctl-tui`](https://github.com/rgwood/systemctl-tui)                                 |  [69](https://fosstodon.org/@orhun/112371588174657662) |
| 18       | 📡 **netscanner**          | Network scanning tool                                                      | [`Chleba/netscanner`](https://github.com/Chleba/netscanner)                                       |  [67](https://fosstodon.org/@orhun/112072604157874674) |
| 19       | 🧊 **soft_ratatui**        | Pure software renderer                                                     | [`gold-silver-copper/soft_ratatui`](https://github.com/gold-silver-copper/soft_ratatui)           |  [64](https://fosstodon.org/@orhun/115293704572241232) |
| 20       | 🎼 **scope-tui**           | A simple oscilloscope/vectorscope/spectroscope for your terminal           | [`alemidev/scope-tui`](https://github.com/alemidev/scope-tui)                                     |  [62](https://fosstodon.org/@orhun/112083487783239141) |
| 21       | ⚡ **eilmeldung**          | A fast & powerful TUI RSS reader                                           | [`christo-auer/eilmeldung`](https://github.com/christo-auer/eilmeldung)                           |  [61](https://fosstodon.org/@orhun/115762900710823858) |
| 22       | 🐗 **hexhog**              | A configurable hex viewer & editor for your terminal                       | [`DVDTSB/hexhog`](https://github.com/DVDTSB/hexhog)                                               |  [61](https://fosstodon.org/@orhun/115383314341988431) |
| 23       | 🎧 **Auditorium**          | Listen to your music library in the terminal                               | [`nate-craft/auditorium`](https://github.com/nate-craft/auditorium)                               |  [59](https://fosstodon.org/@orhun/115170316997264518) |
| 24       | 🔮 **regect**              | Regex 101 like CLI tool                                                    | [`kloki/regect`](https://github.com/kloki/regect)                                                 |  [59](https://fosstodon.org/@orhun/113011400779721925) |
| 25       | 🌳 **treemd**              | An interactive Markdown navigator with a collapsible heading tree          | [`Epistates/treemd`](https://github.com/Epistates/treemd)                                         |  [58](https://fosstodon.org/@orhun/115649170883381821) |
| 26       | 🎛️ **wiremix**             | A simple TUI audio mixer for PipeWire                                      | [`tsowell/wiremix`](https://github.com/tsowell/wiremix)                                           |  [57](https://fosstodon.org/@orhun/114584479458531031) |
| 27       | 📰 **bulletty**            | A RSS/ATOM feed reader for your terminal                                   | [`CrociDB/bulletty`](https://github.com/CrociDB/bulletty)                                         |  [55](https://fosstodon.org/@orhun/115212417041919103) |
| 28       | 🪄 **xan**                 | The CSV magician                                                           | [`medialab/xan`](https://github.com/medialab/xan)                                                 |  [54](https://fosstodon.org/@orhun/113996390709582528) |
| 29       | 💠 **bevy_tui_texture**    | A Bevy plugin for rendering TUIs using Ratatui and wgpu                    | [`tt-toe/bevy_tui_texture`](https://github.com/tt-toe/bevy_tui_texture)                           |  [52](https://fosstodon.org/@orhun/115921278727633282) |
| 30       | 🐠 **lifecycler**          | Terminal aquarium                                                          | [`cxreiff/lifecycler`](https://github.com/cxreiff/lifecycler)                                     |  [52](https://fosstodon.org/@orhun/112880954506496288) |
| 31       | 🌀 **ratatui-wgpu**        | A wgpu based rendering backend for                                         | [`Jesterhearts/ratatui-wgpu`](https://github.com/Jesterhearts/ratatui-wgpu)                       |  [52](https://fosstodon.org/@orhun/113044663726097022) |
| 32       | 🌀 **bevy_ratatui_render** | A Bevy plugin for rendering a Bevy app to the terminal using Ratatui       | [`cxreiff/bevy_ratatui_render`](https://github.com/cxreiff/bevy_ratatui_render)                   |  [51](https://fosstodon.org/@orhun/112558215589709734) |
| 33       | 🔍 **bpftop**              | Provides a dynamic real-time view of running eBPF programs                 | [`Netflix/bpftop`](https://github.com/Netflix/bpftop)                                             |  [51](https://fosstodon.org/@orhun/112427954507271367) |
| 34       | ✨ **Gitu**                | A TUI Git client inspired by Magit                                         | [`altsem/gitu`](https://github.com/altsem/gitu)                                                   |  [51](https://fosstodon.org/@orhun/112042982860750206) |
| 35       | 📄 **tdf**                 | A TUI-based PDF viewer                                                     | [`itsjunetime/tdf`](https://github.com/itsjunetime/tdf)                                           |  [50](https://fosstodon.org/@orhun/112970461439554375) |
| 36       | ⌨️ **typr**                | Typing practice plugin for Neovim with dashboard                           | [`nvzone/typr`](https://github.com/nvzone/typr)                                                   |  [49](https://fosstodon.org/@orhun/113956194651427353) |
| 37       | 🪨 **basalt**              | Manage Obsidian notes directly from the terminal                           | [`erikjuhani/basalt`](https://github.com/erikjuhani/basalt)                                       |  [46](https://fosstodon.org/@orhun/114375171253674971) |
| 38       | 🏴‍☠️ **Rebels in the Sky**   | P2P terminal game about space pirates playing basketball across the galaxy | [`ricott1/rebels-in-the-sky`](https://github.com/ricott1/rebels-in-the-sky)                       |  [46](https://fosstodon.org/@orhun/111912512874171902) |
| 39       | 🌀 **systemd-manager-tui** | Manage systemd services in the terminal                                    | [`matheus-git/systemd-manager-tui`](https://github.com/matheus-git/systemd-manager-tui)           |  [46](https://fosstodon.org/@orhun/114744065918017875) |
| 40       | 🌀 **termscp**             | A feature rich TUI for file transfer and explorer                          | [`veeso/termscp`](https://github.com/veeso/termscp)                                               |  [46](https://fosstodon.org/@orhun/112287698007247046) |
| 41       | 👾 **cellular-automaton**  | execute aesthetically pleasing animations                                  | [`Eandrju/cellular-automaton.nvim`](https://github.com/Eandrju/cellular-automaton.nvim)           |  [44](https://fosstodon.org/@orhun/111058101605090700) |
| 42       | 🧪 **jiq**                 | An interactive JSON query tool with live results                           | [`bellicose100xp/jiq`](https://github.com/bellicose100xp/jiq)                                     |  [44](https://fosstodon.org/@orhun/116077000868424553) |
| 43       | ➡️ **ratatui-ffi**         | Native C ABI bindings for Ratatui                                          | [`holo-q/ratatui-ffi`](https://github.com/holo-q/ratatui-ffi)                                     |  [44](https://fosstodon.org/@orhun/115230443440553346) |
| 44       | 🌀 **rumdl**               | A fast Markdown linter & formatter                                         | [`rvben/rumdl`](https://github.com/rvben/rumdl)                                                   |  [44](https://fosstodon.org/@orhun/116013126337598119) |
| 45       | 🚗 **suzui-rs**            | Suzuki Serial Data Line (SDL) viewer in Rust                               | [`thatdevsherry/suzui-rs`](https://github.com/thatdevsherry/suzui-rs)                             |  [44](https://fosstodon.org/@orhun/114851577006613106) |
| 46       | 🤠 **yeehaw**              | A batteries-included text-based application framework                      | [`bogzbonny/yeehaw`](https://github.com/bogzbonny/yeehaw)                                         |  [44](https://fosstodon.org/@orhun/114109548685307712) |
| 47       | 📊 **tui-piechart**        | A customizable pie chart widget for                                        | [`sorinirimies/tui-piechart`](https://github.com/sorinirimies/tui-piechart)                       |  [43](https://fosstodon.org/@orhun/115530189473335955) |
| 48       | ⚙️ **DataTUI**             | A terminal UI for viewing data                                             | [`forensicmatt/datatui`](https://github.com/forensicmatt/datatui)                                 |  [41](https://fosstodon.org/@orhun/115232309179984882) |
| 49       | 📨 **mqttui**              | Subscribe to a MQTT Topic or publish something quickly from the terminal   | [`EdJoPaTo/mqttui`](https://github.com/EdJoPaTo/mqttui)                                           |  [41](https://fosstodon.org/@orhun/112123857700405569) |
| 50       | 🐱 **manga-tui**           | Terminal-based manga reader and downloader                                 | [`josueBarretogit/manga-tui`](https://github.com/josueBarretogit/manga-tui)                       |  [40](https://fosstodon.org/@orhun/112926893805919270) |
| 51       | 🐭 **webatui**             | Make TUI-themed WASM web apps                                              | [`TylerBloom/webatui`](https://github.com/TylerBloom/webatui)                                     |  [40](https://fosstodon.org/@orhun/113441770382671965) |
| 52       | 🛠️ **csvlens**             | A command line CSV file viewer                                             | [`YS-L/csvlens`](https://github.com/YS-L/csvlens)                                                 |  [39](https://fosstodon.org/@orhun/112129041509672599) |
| 53       | 🔋 **jolt**                | A battery & energy monitor TUI                                             | [`jordond/jolt`](https://github.com/jordond/jolt)                                                 |  [39](https://fosstodon.org/@orhun/116006123697266808) |
| 54       | ⚙️ **journalview**         | View, filter, and navigate system logs from journalctl                     | [`codervijo/journalview`](https://github.com/codervijo/journalview)                               |  [39](https://fosstodon.org/@orhun/113961869796165176) |
| 55       | 🏒 **ssHattrick**          | Multiplayer game that you can play over SSH                                | [`ricott1/sshattrick`](https://github.com/ricott1/sshattrick)                                     |  [39](https://fosstodon.org/@orhun/111991879758031807) |
| 56       | 🧱 **tetrs**               | A modern Tetromino game with a TUI                                         | [`strophox/tetrs`](https://github.com/strophox/tetrs)                                             |  [39](https://fosstodon.org/@orhun/115833078593947584) |
| 57       | 🌲 **lstr**                | A minimalist directory tree viewer with an optional TUI mode               | [`bgreenwell/lstr`](https://github.com/bgreenwell/lstr)                                           |  [38](https://fosstodon.org/@orhun/116066047015593988) |
| 58       | 🧬 **NanoCore**            | An 8-bit CPU emulator + assembler + TUI debugger                           | [`AfaanBilal/NanoCore`](https://github.com/AfaanBilal/NanoCore)                                   |  [38](https://fosstodon.org/@orhun/115683165333227548) |
| 59       | ♜ **chess-tui**            | Play chess in your terminal                                                | [`thomas-mauran/chess-tui`](https://github.com/thomas-mauran/chess-tui)                           |  [37](https://fosstodon.org/@orhun/112292077917101703) |
| 60       | 🌀 **hl**                  | The tool for analyzing logs                                                | [`pamburus/hl`](https://github.com/pamburus/hl)                                                   |  [37](https://fosstodon.org/@orhun/113520221270347242) |
| 61       | 🦀 **modalkit**            | A Rust library for building modal editing applications                     | [`ulyssa/modalkit`](https://github.com/ulyssa/modalkit)                                           |  [37](https://fosstodon.org/@orhun/112271141387724028) |
| 62       | 🍺 **zerobrew**            | A modern drop-in replacement for Homebrew on macOS                         | [`lucasgelfond/zerobrew`](https://github.com/lucasgelfond/zerobrew)                               |  [37](https://fosstodon.org/@orhun/116047932164305515) |
| 63       | 🦆 **ducker**              | A terminal app for managing Docker containers                              | [`robertpsoane/ducker`](https://github.com/robertpsoane/ducker)                                   |  [36](https://fosstodon.org/@orhun/112688261934022526) |
| 64       | 🎮 **sharad_ratatui**      | A text-based Shadowrun role-playing game                                   | [`ProHaller/sharad_ratatui`](https://github.com/ProHaller/sharad_ratatui)                         |  [36](https://fosstodon.org/@orhun/114065121057270272) |
| 65       | 🔍 **tabiew**              | View and query CSV and TSV files                                           | [`shshemi/tabiew`](https://github.com/shshemi/tabiew)                                             |  [36](https://fosstodon.org/@orhun/112502716625721793) |
| 66       | 🦾 **tenere**              | TUI for LLMs written in Rust                                               | [`pythops/tenere`](https://github.com/pythops/tenere)                                             |  [36](https://fosstodon.org/@orhun/112117127579562805) |
| 67       | 🐘 **tsql**                | A modern PostgreSQL manager TUI                                            | [`fcoury/tsql`](https://github.com/fcoury/tsql)                                                   |  [36](https://fosstodon.org/@orhun/115728558086836742) |
| 68       | 🌀 **channels-console**    | A TUI dashboard for inspecting std/tokio/futures/crossbeam channels        | [`pawurb/channels-console`](https://github.com/pawurb/channels-console)                           |  [35](https://fosstodon.org/@orhun/115532274508528420) |
| 69       | 🖼️ **md-tui**              | Markdown renderer in the terminal                                          | [`henriklovhaug/md-tui`](https://github.com/henriklovhaug/md-tui)                                 |  [35](https://fosstodon.org/@orhun/112450995959357823) |
| 70       | 🦀 **cargo-wizard**        | Applies profile and config templates to your Cargo project                 | [`Kobzol/cargo-wizard`](https://github.com/Kobzol/cargo-wizard)                                   |  [34](https://fosstodon.org/@orhun/112326515478512618) |
| 71       | 🎧 **concertus**           | A plug-and-play TUI music player for local libraries                       | [`Jaxx497/concertus`](https://github.com/Jaxx497/concertus)                                       |  [34](https://fosstodon.org/@orhun/115654628219874598) |
| 72       | 🎹 **CrabSID**             | A TUI music player for Commodore 64 SID tunes                              | [`mlund/crabsid`](https://github.com/mlund/crabsid)                                               |  [34](https://fosstodon.org/@orhun/116022942130800421) |
| 73       | 💽 **dua**                 | View disk space usage and delete unwanted data                             | [`Byron/dua-cli`](https://github.com/Byron/dua-cli)                                               |  [34](https://fosstodon.org/@orhun/111709098835761455) |
| 74       | 🦀 **md-tui**              | Markdown renderer                                                          | [`henriklovhaug/Preview.nvim`](https://github.com/henriklovhaug/Preview.nvim)                     |  [34](https://fosstodon.org/@orhun/112513228192831000) |
| 75       | 💯 **pls**                 | A prettier and powerful ls(1) for the pros                                 | [`pls-rs/pls`](https://github.com/pls-rs/pls)                                                     |  [34](https://fosstodon.org/@orhun/113216805246862474) |
| 76       | 🧪 **SeqTUI**              | A terminal-based sequence data viewer & toolkit                            | [`ranwez-search/SeqTUI`](https://github.com/ranwez-search/SeqTUI)                                 |  [34](https://fosstodon.org/@orhun/115826090895157665) |
| 77       | 🧭 **tui-scrollbar**       | Smooth & fractional scrollbar widget for                                   | [`joshka/tui-widgets`](https://github.com/joshka/tui-widgets)                                     |  [34](https://fosstodon.org/@orhun/115933555840484995) |
| 78       | 🧹 **wiper**               | Disk cleanup tool with visual breakdown of directory sizes                 | [`ikebastuz/wiper`](https://github.com/ikebastuz/wiper)                                           |  [34](https://fosstodon.org/@orhun/112458051058466989) |
| 79       | ✈️ **adsb_deku**           | Rust ADS-B decoder + TUI radar application                                 | [`rsadsb/adsb_deku`](https://github.com/rsadsb/adsb_deku)                                         |  [33](https://fosstodon.org/@orhun/113072977456671678) |
| 80       | 🐦 **perch**               | A TUI client for Mastodon & Bluesky                                        | [`ricardodantas/perch`](https://github.com/ricardodantas/perch)                                   |  [33](https://fosstodon.org/@orhun/116132096080693793) |
| 81       | 🌀 **rgx**                 | TUI regex tester with real-time matching                                   | [`brevity1swos/rgx`](https://github.com/brevity1swos/rgx)                                         |  [33](https://fosstodon.org/@orhun/116223901683903903) |
| 82       | 🧲 **superseedr**          | A full-featured BitTorrent client for the terminal                         | [`Jagalite/superseedr`](https://github.com/Jagalite/superseedr)                                   |  [33](https://fosstodon.org/@orhun/115412577714581422) |
| 83       | 📖 **bookokrat**           | A terminal-based EPUB reader                                               | [`bugzmanov/bookokrat`](https://github.com/bugzmanov/bookokrat)                                   |  [32](https://fosstodon.org/@orhun/115526727604124239) |
| 84       | 🔧 **jirust**              | Jira terminal UI                                                           | [`Code-Militia/jirust`](https://github.com/Code-Militia/jirust)                                   |  [32](https://fosstodon.org/@orhun/112445116954961528) |
| 85       | 🦀 **ouch**                | Painless compression and decompression in the terminal - written in Rust   | [`ouch-org/ouch`](https://github.com/ouch-org/ouch)                                               |  [32](https://fosstodon.org/@orhun/111533591722497726) |
| 86       | 📺 **television**          | A general purpose fuzzy finder TUI                                         | [`alexpasmantier/television`](https://github.com/alexpasmantier/television)                       |  [32](https://fosstodon.org/@orhun/113509495988625551) |
| 87       | 🌀 **tui-shader**          | A library for using GPU shaders in TUI applications                        | [`pemattern/tui-shader`](https://github.com/pemattern/tui-shader)                                 |  [32](https://fosstodon.org/@orhun/114058423007375995) |
| 88       | 🃏 **BalatroTUI**          | Balatro game in your terminal                                              | [`Passeriform/BalatroTUI`](https://github.com/Passeriform/BalatroTUI)                             |  [31](https://fosstodon.org/@orhun/114827940563251355) |
| 89       | 🌪️ **blendr**              | The hacker's BLE (bluetooth low energy) browser terminal app               | [`dmtrKovalenko/blendr`](https://github.com/dmtrKovalenko/blendr)                                 |  [31](https://fosstodon.org/@orhun/112416960856732015) |
| 90       | 🧬 **keifu**               | A TUI for visualizing Git commit graphs                                    | [`trasta298/keifu`](https://github.com/trasta298/keifu)                                           |  [31](https://fosstodon.org/@orhun/115951106671033679) |
| 91       | 🃏 **private_poker**       | A poker library, server, client, and TUI                                   | [`theOGognf/private_poker`](https://github.com/theOGognf/private_poker)                           |  [31](https://fosstodon.org/@orhun/113192763281555921) |
| 92       | 🐭 **ratframe**            | egui widget + Ratatui backend                                              | [`gold-silver-copper/ratatui_egui_wasm`](https://github.com/gold-silver-copper/ratatui_egui_wasm) |  [31](https://fosstodon.org/@orhun/112320924157137862) |
| 93       | 🌐 **RustNet**             | A cross-platform network monitor                                           | [`domcyrus/rustnet`](https://github.com/domcyrus/rustnet)                                         |  [31](https://fosstodon.org/@orhun/115200563156229696) |
| 94       | 🏗️ **texaform**            | A factory game automated by your code                                      | [`JoshuaPostel/texaform`](https://github.com/JoshuaPostel/texaform)                               |  [31](https://fosstodon.org/@orhun/114936069734753992) |
| 95       | 🐢 **tortuise**            | Render 3D scenes using pure terminal symbols                               | [`buildoak/tortuise`](https://github.com/buildoak/tortuise)                                       |  [31](https://fosstodon.org/@orhun/116255422270714671) |
| 96       | 🛰️ **tracker**             | Track satellites and predict orbits in real-time in your terminal          | [`ShenMian/tracker`](https://github.com/ShenMian/tracker)                                         |  [31](https://fosstodon.org/@orhun/113622335730027874) |
| 97       | 📓 **tui-journal**         | Your journal app if you live in a terminal                                 | [`AmmarAbouZor/tui-journal`](https://github.com/AmmarAbouZor/tui-journal)                         |  [31](https://fosstodon.org/@orhun/112342194663598142) |
| 98       | 📚 **ekphos**              | A markdown research TUI inspired by Obsidian                               | [`hanebox/ekphos`](https://github.com/hanebox/ekphos)                                             |  [30](https://fosstodon.org/@orhun/115900423288491075) |
| 99       | 🌌 **fractouille**         | Fractal explorer for the terminal                                          | [`PottierLoic/Fractouille`](https://github.com/PottierLoic/Fractouille)                           |  [30](https://fosstodon.org/@orhun/114635880247903446) |

The full list (600+) is also available [here](https://github.com/orhun/mastodon-post-analyzer/blob/main/FULL_LIST.md).

## What's next?

<g>I will keep posting.</g>

If you want to help in any way:

- If you have a Rust/Ratatui/terminal project or if you know one that deserves more attention, send it to me and I will post about it!
- If you have ideas for what I could do with this data or have any feedback for the posts, let me know!
- Smash the sponsor button below if you want to support the work that I do.

Cheers! 🐁

[X](https://x.com/orhundev) | [Mastodon](https://fosstodon.org/@orhun) | [Bluesky](https://bsky.app/profile/orhun.dev) | [LinkedIn](https://www.linkedin.com/in/orhunp/)
