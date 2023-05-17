+++
title = "Taking Rust to the Cloud: Blazingly Fast File Sharing"
date = 2023-05-17

[taxonomies]
categories = ["Guides", "Projects"]
+++

"[**rustypaste**](https://github.com/orhun/rustypaste)" is a self-hosted and minimal file upload/pastebin service written in Rust. In this post, I will be talking about its features and telling the story behind how I deployed it to [shuttle.rs](https://www.shuttle.rs) to make it publicly available for free use.

<!-- more -->

<center>

<img alt="https://blog.xkcd.com/2019/08/26/how-to-send-a-file/" src="/xkcd-sendfile.png" width="50%"/>

<i>If the Sun is warm, the winds are favorable, and it’s the right time of year, you could use butterflies to send someone the entire internet.</i><a href="https://blog.xkcd.com/2019/08/26/how-to-send-a-file/">\*</a>

</center>

If you have chatted with me on the internet before and if I wanted to share an image/link with you, it is highly likely that I might have sent you a message like this:

<center>

```sh
https://p.orhun.dev/enzkJQ.png
```

</center>

The first time people get this, they either find it interesting and ask about or they just roll with it and click the link. Most of the time, it is the latter.

If you have ever wondered why I prefer sending links instead of directly sending the image/file and what's behind that, you are lucky. Today is the day that I will finally reveal this mystery.

Buckle up!

---

### The Story of File Sharing

To properly convey the idea behind file sharing, I need to tell you a story.

In the early days of the internet (not that I lived through them), communication was simpler and naturally less secure. One of the things people have used was the almighty [Internet Relay Chat](https://en.wikipedia.org/wiki/Internet_Relay_Chat) (IRC), the famous chat system that is being used even today by some projects. Back in the day, people were creating channels there and enjoying the non-secure, less-censored, and kind-of-anonymous communication. Today, those times are considered the golden era of the internet by some people including me. Everything was so peaceful and you could smell the dusty air which is coming from an old PC fan that is hardly running Windows XP with an Intel Pentium 4 processor. \*sigh\* Happy times.

Well, no.

You couldn't share images on IRC. At least it wasn't that easy since IRC clients were heavily text-based and designed to be run on a terminal (the author is talking about [BitchX](https://en.wikipedia.org/wiki/BitchX) here). Surely, it wasn't as easy and as convenient as today's chatting applications and we can't expect it to be. But I think it is still Cheff's [KISS](https://en.wikipedia.org/wiki/KISS_principle), what even is file-sharing support anyways? It requires storing the files somewhere and serving them to the recipient. There are so many things to consider such as upload limits, nude detection (if you care about it), privacy, persistence of the files, additional server costs, disk spaces, etc. Long story short, file sharing is _BLOAT_! Describing to my friends how to craft a chainmail armor in Minecraft with purely plain-text messages instead of sending a screenshot is much BETTER. Or wait, we can use ASCII art, oh yeah, that's also a thing. Right?

\*ahem\* I guess I went a bit off-track there. But you see where I'm getting, on IRC, there was no _easy_ file sharing support so people had to come up with their own solutions, in other words, their own upload servers. In my time in IRC, I came across a lot of users who use links like the below, just to share files/images:

- https://paste.xinu.at/jHKy
- https://0x0.st/H8hSa.png

In a sense, in today's world, doing this seems redundant and actually more difficult than just clicking 3 buttons on an interface. While I agree with that, I approach this situation from a different angle.

Technology is all about _having control_. I believe it is especially true today since every corner of the human brain is easily conquerable by technology. We are deeply integrated with this world and in the situation where we don't control it, it controls us. Cliché but true.

In the case of file sharing, having your own (self-hosted) service for uploading your own files is simply having control over what you have provided to this virtual world. The thing that you want to share is there if you want it, and it is there under the circumstances that you have chosen. Maybe the files will be served from an external drive from your homeserver. Maybe they will be expiring after 2 hours. Maybe you want to see who has clicked the link. It is all about owning your data.

When you consider these points, you see that the insufficient conditions from the IRC times revealed a perfect solution for file sharing which compliments the idea that centralization is bad. It is kind of ironic that IRC's limitations gave birth to self-hosted file sharing.

Although I joined IRC pretty late (3 years ago), I got the hang of it pretty fast. At first, I used a couple of public services for sharing files but then I really warmed up to the self-hosting idea and decided to host my own upload server. That is why I wrote the "[Spin Up Your Own No-Bullshit File Hosting Service with 0x0](https://blog.orhun.dev/no-bullshit-file-hosting/)" post 2 years ago. I used [0x0](https://github.com/mia-0/0x0/) for a while but then I had to migrate to another server and I was too lazy to set it up again. Plus it was written in Python x_x.

That day I decided to write my own pastebin service. And of course, I was going to write it in Rust.

---

### Blazingly Fast File Sharing: [**rustypaste**](https://github.com/orhun/rustypaste) 🦀

<img src="/rustypaste-shuttleapp.png" style="width: 80%"/>

⭐ **GitHub**: [https://github.com/orhun/rustypaste](https://github.com/orhun/rustypaste)  
🚀 **Public instance**: [https://rustypaste.shuttleapp.rs](https://rustypaste.shuttleapp.rs)

`rustypaste` is a minimal and simple upload service written in Rust and implemented using the [Actix Web](https://actix.rs) framework. I chose Actix back then since it was the most suitable framework from the safety, performance, and simplicity standpoint. As a regular user of `rustypaste` for almost 2 years, I can say that it is in a stable state and have a bunch of features that support extensive configuration.

The easiest way to interact with a `rustypaste` server is using `curl` but you can also use the command line tool called [`rpaste`](https://github.com/orhun/rustypaste-cli) which is written in Rust.

⭐ **CLI tool**: [https://github.com/orhun/rustypaste-cli](https://github.com/orhun/rustypaste-cli)

![rpaste](/rpaste.gif)

```sh
Usage:
    rpaste [options] <file(s)>

Options:
    -h, --help          prints help information
    -v, --version       prints version information
    -V, --server-version
                        retrieves the server version
    -o, --oneshot       generates one shot links
    -p, --pretty        prettifies the output
    -c, --config CONFIG sets the configuration file
    -s, --server SERVER sets the address of the rustypaste server
    -a, --auth TOKEN    sets the authentication token
    -u, --url URL       sets the URL to shorten
    -r, --remote URL    sets the remote URL for uploading
    -e, --expire TIME   sets the expiration time for the link
```

Here is a list of features along with examples:

| **Upload a file**                                             |
| ------------------------------------------------------------- |
| `curl -F "file=@ferris.txt" https://rustypaste.shuttleapp.rs` |
| `rpaste ferris.txt`                                           |

| **Shorten an URL**                                                                 |
| ---------------------------------------------------------------------------------- |
| `curl -F "url=https://example.com/some/long/url" https://rustypaste.shuttleapp.rs` |
| `rpaste -u https://example.com/some/long/url`                                      |

| **Upload a file from URL**                                                       |
| -------------------------------------------------------------------------------- |
| `curl -F "remote=https://example.com/file.png" https://rustypaste.shuttleapp.rs` |
| `rpaste -r https://example.com/file.png`                                         |

| **Expire the file after 10 minutes (also works for URLs)**                      |
| ------------------------------------------------------------------------------- |
| `curl -F "file=@ferris.txt" -H "expire:10min" https://rustypaste.shuttleapp.rs` |
| `rpaste -e 10min ferris.txt`                                                    |

| **Delete the file after viewed once**                            |
| ---------------------------------------------------------------- |
| `curl -F "oneshot=@ferris.txt" https://rustypaste.shuttleapp.rs` |
| `rpaste -o ferris.txt`                                           |

| **Authenticate with the server**                                                               |
| ---------------------------------------------------------------------------------------------- |
| `curl -F "file=@ferris.txt" -H "Authorization: <auth_token>" https://rustypaste.shuttleapp.rs` |
| `rpaste -a "<auth_token>" ferris.txt` (you can also use a configuration file)                  |

To configure `rustypaste`, you only need a single configuration file. The default one can be viewed from the [GitHub repository (`config.toml`)](https://github.com/orhun/rustypaste/blob/master/config.toml).

Here are some things that you can tweak and change:

- **Random file names**
  - Pet name (`capital-mosquito.txt`)
  - Alphanumeric string (`yB84D2Dv.txt`)
- **MIME types**
  - Supports overriding and blacklisting
  - Supports forcing to download via `?download=true`
- **Support disabling duplicate uploads**
- **Auto-expiration + auto-deletion of files**

If you want to host your own `rustypaste` server, there are a couple of ways of doing it:

- Run the single binary (preferably installed from your distribution's package manager).
  - `rustypaste` is available in the [Arch Linux repositories](https://archlinux.org/packages/?sort=&q=rustypaste).
- Use the lightweight Docker image.
  - [https://hub.docker.com/r/orhunp/rustypaste](https://hub.docker.com/r/orhunp/rustypaste)
  - [docker-compose.yml](https://github.com/orhun/rustypaste/blob/master/docker-compose.yml) (example)
  - [Nginx configuration](https://github.com/orhun/rustypaste#nginx) (example)

Or you can deploy it to the cloud! ☁️

---

### Deploying Rust Services via [**Shuttle**](https://www.shuttle.rs) 🚀

> [Shuttle](https://www.shuttle.rs) is a Rust-native cloud development platform that lets you deploy your app while also taking care of all of your infrastructure.

Shuttle derives the infrastructure definitions from the Rust code itself via _function signatures_ and _annotations_. It is an awesome service that makes cloud deployment super easy and supports a couple of Rust frameworks including Actix. Also, you can control everything (deployment, monitoring, resource management, etc.) with a single cargo subcommand by running [`cargo shuttle`](https://github.com/shuttle-hq/shuttle/tree/main/cargo-shuttle) from the command line. When you consider all of this, there is no wonder that they are backed by [YC](https://www.ycombinator.com) :o

Currently, they support a _hobby plan_ which is basically free and you can reach out to them if the following features are not suitable/enough for you:

- Unlimited deployment
- 150k requests per month
- 500MB database storage

That is pretty much enough for `rustypaste` so I decided to go with Shuttle. 🚀

However, there was one question that came up to my mind about the storage space. So I asked a question on their [Discord](https://discord.gg/shuttle):

**orhun**: How does Shuttle deployment store files? Internally, are they uploaded inside an isolated container? If so, is there any disk/memory/CPU limits?

**jonaro00**: Your project runs in a docker container, but writing to the file system is not recommended, since the container is wiped when you do `cargo shuttle project restart` (which you do when upgrading shuttle version, or when things break). For persistent storage, you want to use some form of database. You can check the supported ones on the docs, but there are also more of them in the works.

**orhun**: I'm planning to auto-expire the files after one hour or so would you recommend using the filesystem in this case?

**jonaro00**: Yeah, for that use case it works fine then I guess.

Still, I'm not sure if there are any disk space limits for a Shuttle deployment and we couldn't figure it out on Discord so let me know if you know anything about this.

Now that we have enough information about Shuttle, let's deploy a Rust project!

Start by installing `cargo-shuttle` and logging in:

```sh
$ cargo install cargo-shuttle

$ cargo shuttle login
```

After this step, you can simply create a Rust project ready for deployment. For creating an Actix template, we can run:

```sh
$ cargo shuttle init -t actix-web
```

As of now, `cargo-shuttle` supports the following templates: `actix-web`, `axum`, `poem`, `poise`, `rocket`, `salvo`, `serenity`, `tide`, `thruster`, `tower`, `warp`

After the creation, the project looks roughly like this:

```rs
#!/usr/bin/env rust-script

//! [dependencies]
//! shuttle-runtime = "0.16.0"
//! actix-web = "4.3.1"
//! shuttle-actix-web = "0.16.0"
//! tokio = "1.28.1"

use actix_web::{get, web::ServiceConfig};
use shuttle_actix_web::ShuttleActixWeb;

// Define a route for the root path ("/") that returns a string "Hello World!"
#[get("/")]
async fn hello_world() -> &'static str {
    "Hello World!"
}

// The main entry point for the Shuttle runtime.
#[shuttle_runtime::main]
async fn actix_web(
) -> ShuttleActixWeb<impl FnOnce(&mut ServiceConfig) + Send + Clone + 'static> {
    // Define a closure to configure the ServiceConfig of the HttpServer.
    let config = move |cfg: &mut ServiceConfig| {
        // Register the hello_world function as a service at the root path.
        cfg.service(hello_world);
    };

    // Wrap the closure in a ShuttleActixWeb object and return it.
    Ok(config.into())
}
```

To test it locally:

```sh
$ cargo shuttle run
```

For deployment, it is just easy as running the following command:

```sh
$ cargo shuttle deploy
```

Then you can view the deployment at `<app>.shuttleapp.rs` and view the logs via `cargo shuttle logs`.

In the case of `rustypaste`, I already have an Actix application so I need to tweak things a little bit for deployment. Mainly, I needed to change the main entry point of the application. I decided to have a feature flag called "shuttle" for wrapping the additional dependencies of Shuttle and enabling another entry point. I didn't touch the rest of the code and it feels absolutely awesome when there is less effort.

```toml
[features]
default = []
shuttle = [
  "dep:shuttle-actix-web",
  "dep:shuttle-runtime",
  "dep:shuttle-static-folder",
  "dep:tokio",
]

[dependencies]
# other dependencies
# ...
shuttle-actix-web = { version = "0.16.0", optional = true }
shuttle-runtime = { version = "0.16.0", optional = true }
shuttle-static-folder = { version = "0.16.0", optional = true }
tokio = { version = "1.28.1", optional = true }
```

Shuttle can be activated via `--features shuttle` now.

Then I hit another road block.

You see, `rustypaste` needs a `config.toml` file to be present to run. And when we deploy it as a single binary, there won't be a configuration file next to it. To solve this problem, I used a static folder for my Shuttle deployment and edited the entry point of the application as follows:

```rs
#[cfg(feature = "shuttle")]
#[shuttle_runtime::main]
async fn actix_web(
    #[shuttle_static_folder::StaticFolder(folder = "shuttle")] static_folder: PathBuf,
) -> ShuttleActixWeb<impl FnOnce(&mut ServiceConfig) + Send + Clone + 'static>
```

So if I put the configuration file in "shuttle/config.toml", it will be the part of the deployment. Nice.

All seems good now. Let's deploy it!

**Q**: Wait, how do you enable the custom "shuttle" feature via `cargo shuttle`? In other words, can you pass `--features shuttle` to it?

**A**: At the time of writing this blog post, it is not possible. So I created this issue: [https://github.com/shuttle-hq/shuttle/issues/913](https://github.com/shuttle-hq/shuttle/issues/913)

**Q**: Oh cool, is there a workaround?

**A**: Yeah, I decided to enable "shuttle" feature as default in Cargo.toml temporarily when I want to deploy.

**Q**: Nice. Does it work like that?

**A**: You bet!

```sh
# make "shuttle" feature default
$ sed -i 's|default = \[\]|default = \["shuttle"\]|g' Cargo.toml

# deploy without running tests
$ cargo shuttle deploy --no-test

2023-05-16T13:56:37.027724537Z  INFO Entering queued state
2023-05-16T13:56:37.036643575Z DEBUG hyper::client::connect::http: connecting to 10.99.82.119:8001
2023-05-16T13:56:37.039322882Z DEBUG hyper::client::connect::http: connected to 10.99.82.119:8001
2023-05-16T13:56:37.041805834Z DEBUG hyper::client::pool: pooling idle connection for ("http", gateway:8001)

2023-05-16T13:56:37.046562562Z  INFO Entering building state
2023-05-16T13:57:08.734972530Z  INFO     Finished release [optimized] target(s) in 31.63s

2023-05-16T13:57:08.924988890Z  INFO Entering built state

2023-05-16T13:57:08.925183822Z  INFO Entering loading state
2023-05-16T13:57:08.933992618Z DEBUG shuttle_deployer::runtime_manager: Starting alpha runtime at: /opt/shuttle/shuttle-executables/abe6d63d-8a0f-4d59-9545-8979261889e4
2023-05-16T13:57:10.937504021Z  INFO shuttle_proto::runtime: connecting runtime client
2023-05-16T13:57:10.937602528Z DEBUG hyper::client::connect::http: connecting to 127.0.0.1:18369
2023-05-16T13:57:10.940519024Z DEBUG hyper::client::connect::http: connected to 127.0.0.1:18369
2023-05-16T13:57:10.942760924Z DEBUG {service.ready=true} tower::buffer::worker: processing request
2023-05-16T13:57:10.949727188Z DEBUG {service.ready=true} tower::buffer::worker: processing request
2023-05-16T13:57:10.954448150Z  INFO shuttle_deployer::deployment::run: loading project from: /opt/shuttle/shuttle-executables/abe6d63d-8a0f-4d59-9545-8979261889e4
2023-05-16T13:57:10.956904512Z DEBUG shuttle_deployer::deployment::run: loading service
2023-05-16T13:57:10.961554512Z DEBUG {service.ready=true} tower::buffer::worker: processing request
2023-05-16T13:57:10.978349558Z  INFO {success="true"} shuttle_deployer::deployment::run: loading response
These static folders can be accessed by rustypaste
╭─────────╮
│ Folders │
╞═════════╡
│ shuttle │
╰─────────╯

Service Name:  rustypaste
Deployment ID: abe6d63d-8a0f-4d59-9545-8979261889e4
Status:        running
Last Updated:  2023-05-16T10:57:10Z
URI:           https://rustypaste.shuttleapp.rs
```

Yay, the service is live at [https://rustypaste.shuttleapp.rs](https://rustypaste.shuttleapp.rs)! 🚀

As a bonus, I wanted to deploy this service when I push a new tag to the repository so I created the following GitHub Actions workflow:

```yml
name: Deploy on Shuttle

on:
  push:
    tags:
      - "v*.*.*"

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-22.04
    steps:
      - name: Checkout the repository
        uses: actions/checkout@v3

      - name: Install Rust
        uses: actions-rs/toolchain@v1
        with:
          toolchain: stable
          profile: minimal
          override: true

      - name: Install cargo-binstall
        uses: taiki-e/install-action@cargo-binstall

      - name: Install cargo-shuttle
        run: cargo binstall -y cargo-shuttle

      - name: Prepare for deployment
        shell: bash
        run: sed -i 's|default = \[\]|default = \["shuttle"\]|g' Cargo.toml

      - name: Login
        run: cargo shuttle login --api-key ${{ secrets.SHUTTLE_TOKEN }}

      - name: Deploy
        run: cargo shuttle deploy --allow-dirty --no-test
```

One thing to note, I thought it would be nicer to install `cargo-shuttle` via [`cargo-binstall`](https://github.com/cargo-bins/cargo-binstall) since it is faster to download pre-built binaries.

And finally here is how I put together everything:

[https://github.com/orhun/rustypaste/commit/29ddef8](https://github.com/orhun/rustypaste/commit/29ddef8df0bb38ec4bd77281836105b7c7797d71)

---

### Conclusion

There are a lot of ways to share a file on the internet and I'm on the "you should host your own files" side. At first, it seems like a hassle to host your own server and configure everything but I believe it is really worth it at the end of the day. I mean heck, with `rustypaste` you can even share a very secret document as a oneshot URL so they disappear from the surface of the web after being viewed once.

And also, who can deny that using your own domain to share an image/file is super cool?

Long story short, I believe there are many benefits to self-hosting an upload server and owning the things you share with the rest of the world.

On a side note, feel free to use the public instance of `rustypaste` and let me know if you liked it, there isn't a stability guarantee for Shuttle deployments but it is worth a try!

Happy uploading! ☁️ 🦀 🚀

_arrivederci!_
