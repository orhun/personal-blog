+++
title = "How I set up this blog"
date = 2024-12-23

[taxonomies]
categories = ["Projects"]
+++

Like my blog? Here is how I set it up.

<!-- more -->

As my [FOSDEM 2025 talk] ("Bringing terminal aesthetics to the Web") is approaching, I thought it would be a good idea to take a step back and think about minimal web design. And I believe my blog is a good example for taking apart to the pieces and understand what makes a minimal website minimal.

Also, [some people] requested me to share which tool/language/kit I'm using for writing these posts.

So, ladies and gentlemen, here it is.

P.S. The source of this blog is available [here][source].

---

### **Engine** ⚙️

My blog is proudly powered by [Zola], a static site generator written in Rust. I chose Zola because it's fast, simple, and has many features.

All you need is a single binary installed in your system, and you can start building your blog by running:

```sh
zola init frogblog
```

This command will give you a simple directory structure with a [configuration file], where you can tweak many settings including title, description, RSS feed etc.

In a nutshell, you simply add your posts in the `content` directory in markdown format, and run `zola build` to generate the static HTML files. Easy peasy.

See the [overview] for more information.

---

### **Theme** 🎨

<q> The colors, fonts, styles... _ugh_... the hardest part when it comes to making something appealing. </q>

Luckily, Zola has [many themes] that you can pick from. All you need to do is download the theme and put it in the `themes` directory, either by adding it as a [submodule] or old-the school way (i.e. cloning the repository). Also, don't forget to set the `theme` field in the configuration file!

I'm no designer, but I know what I want. Some find it unappealing, but I really like the terminal-like simplicity and markdown rendering of the [after-dark theme].

So the next step for me was to clone that theme and start making tweaks in the CSS (and yes it was a bit painful but whatever).

---

### **Comments** 💬

<q>Ah, right. The comments section is very important, otherwise where would people leave their hate comments?</q>

It is important for feedback and discussions too!

[utteranc.es] is a great widget for adding comments to your Zola blog. The catch is, it is built on top of GitHub issues. So each comment on these posts are stored in the respective GitHub issue. You can check them out [here][issues].

➡️ Try it out, leave a comment below and go check the issue on GitHub. I'd appreciate genuine feedback too!

To set this up, follow the instructions on the [website][utteranc.es]. For me, it's a matter of adding this snippet to the footer of `page.html`:

```html
<script
  src="https://utteranc.es/client.js"
  repo="orhun/personal-blog"
  issue-term="url"
  label="utterances"
  theme="github-dark"
  crossorigin="anonymous"
  async
></script>
```

---

### **Like Button** 💖

I don't like Medium for its paywall and tracking, but I like their "claps" feature. So people can show their appreciation without leaving a comment.

Luckily, I found about the [applause-button] project which is exactly what I wanted. A zero-config button for applause/claps/kudos to blog posts.

Adding it was also simple:

1\. Add the script to `index.html`:

```html
<link
  rel="stylesheet"
  href="https://unpkg.com/applause-button/dist/applause-button.css"
/>
<script src="https://unpkg.com/applause-button/dist/applause-button.js"></script>
```

2\. Add the button to `page.html`:

```html
<applause-button
  style="width: 50px; height: 50px"
  url="{{ config.base_url | safe }}{{ page.path }}"
  color="white"
  multiclap="true"
/>
```

However, this button no longer works :(  
See [this issue][applause-button-issue]:

> Apologies all, I've had to retire the hosted service. For the first ~4 years the donations I was receiving from OpenCollective were just about covering the running costs. However, in the past 6 months usage increased significantly, with millions of requests each day, and the costs simply went out of control. If you want to keep using applause button I'm afraid you're going to have to host it yourself.

<q>Suffering from success, huh?</q>

For now this button is disfunctional, but I might self-host it in the future or switch to something else. Let me know if you know any alternatives!

---

### **Search** 🔍

This is my latest addition to the blog and I'm surprisingly satisfied with it. See the search box on the top right corner.

Unfortunately the [instructions][zola-search-instructions] on the Zola website were not very clear to me, so I followed [this blog post][zola-searchbar-blog] for setting this up.

In a nutshell, what you need to do is:

1. Enable building the search index (`build_search_index=true`) and set JSON as the index format (`[search].index_format="elasticlunr_json"`).
2. Adopt the [search script] from the Zola repository along with other HTML and CSS rigmarole.

See [this commit][search-impl] for my implementation. (it's not the best, you have been warned)

---

### **Analytics** 📈

Needless to say, I don't like Google Analytics. I don't want to be tracked by them, and most importantly I don't want _you_ to be tracked by them.

So I set up an instance of [Umami], an open source analytics service. I'm running it on a Digital Ocean droplet that I'm funding via [GitHub Sponsors].

My analytics are also public, you can check them out [here][public-analytics].

For the tech savvy out there, I'm using this `docker-compose.yml` to run the [Umami] service:

```yml
---
version: "3"
services:
  umami:
    image: ghcr.io/umami-software/umami:postgresql-v2.10.2
    container_name: umami
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgresql://umami:umami@db:5432/umami
      DATABASE_TYPE: postgresql
      APP_SECRET: [redacted]
    depends_on:
      db:
        condition: service_healthy
    restart: always
  db:
    image: postgres:12-alpine
    container_name: umami_db
    environment:
      POSTGRES_DB: umami
      POSTGRES_USER: umami
      POSTGRES_PASSWORD: umami
    volumes:
      - ./umami-db-data:/var/lib/postgresql/data
    restart: always
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB}"]
      interval: 5s
      timeout: 5s
      retries: 5
```

⚠️ If you host it like this, make sure to follow the releases and do the required database migrations before updating the image version. Otherwise, you might [break stuff][umami-bug-1] and lose data. Speaking from [experience][umami-bug-2].

---

### **Automation** 🤖

I'm using [GitHub Actions] for automating the deployment and checking the content.

1\. [`deploy.yml`]: This workflow runs for pushes to the `main` branch and pull requests. It uses the following actions:

⭐ [`shalzz/zola-deploy-action`]: Builds and deploys the blog.

⭐ [`rossjrw/pr-preview-action`]: Creates a preview of the blog from pull requests.

The second action that I'm using is pretty useful for checking if everything is good before publishing your blog posts. Just an aside, it can be used with other static site generators too, not just Zola.

2\. [`ci.yml`]: This workflow runs for pushes to the `main` branch, pull requests and every week on Sunday (`0 0 * * 0`). It checks the content with the following actions:

⭐ [`lycheeverse/lychee-action`]: Checks the markdown files for broken links. Uses [`lychee`] under the hood.

⭐ [`codespell-project/actions-codespell`]: Checks the markdown files for spelling mistakes. Uses [`codespell`] under the hood.

Recently I have been thinking of switching to [`typos`] for spell checking.

Let me know if you have any suggestions for other tools that I can use for linting!

---

### **Static Content** 🧊

<q>Git is not a storage server!</q>

If you want to host large images, I recommend that you use a separate service for that, instead of bloating your repository.

For that, I'm hosting my images on a simple Nginx server on the same droplet where Umami is running. I needed this setup specifically for my [Mongolia post] since it contains a lot of images.

---

## **Conclusion** ✨

My blog is not that impressive (yet), it's just a static site. What I want to achieve is to make it fully browseable on the terminal. Also vice-versa, I want to make a terminal UI for my blog that is fully browseable on the web. The latter has been already achieved by [this blog][AvidRustacean] which is built with [Ratatui], the project that I'm working on. We will see where this _rat hole_ will lead me.

Anyhoo, I hope this post was helpful for you. If you have any questions or feedback, feel free to leave a comment below!

I'm out, see you in the next post! 🐸

[FOSDEM 2025 talk]: https://fosdem.org/2025/schedule/event/fosdem-2025-5496-bringing-terminal-aesthetics-to-the-web-with-rust-and-vice-versa-/
[source]: https://github.com/orhun/personal-blog/
[some people]: https://github.com/orhun/personal-blog/issues/31#issuecomment-2097420000
[Zola]: https://www.getzola.org/
[configuration file]: https://www.getzola.org/documentation/getting-started/configuration/
[overview]: https://www.getzola.org/documentation/getting-started/overview/
[many themes]: https://www.getzola.org/themes/
[submodule]: https://git-scm.com/book/en/v2/Git-Tools-Submodules
[after-dark theme]: https://www.getzola.org/themes/after-dark/
[utteranc.es]: https://utteranc.es/
[issues]: https://github.com/orhun/personal-blog/issues
[applause-button]: https://applause-button.com/
[applause-button-issue]: https://github.com/ColinEberhardt/applause-button/issues/101
[zola-search-instructions]: https://www.getzola.org/documentation/content/search/
[zola-searchbar-blog]: https://adrianstobbe.com/posts/zola-searchbar/
[search script]: https://github.com/getzola/zola/blob/master/docs/static/search.js
[search-impl]: https://github.com/orhun/personal-blog/commit/e4a155a6c737474318faed22b087dd301ede74fb
[Umami]: https://umami.is/
[GitHub Sponsors]: https://github.com/sponsors/orhun
[public-analytics]: https://umami.orhun.dev/share/erNH2HJd/Blog
[umami-bug-1]: https://github.com/umami-software/umami/issues/2310
[umami-bug-2]: https://github.com/umami-software/umami/issues/2645
[GitHub Actions]: https://github.com/features/actions
[`deploy.yml`]: https://github.com/orhun/personal-blog/blob/main/.github/workflows/deploy.yml
[`shalzz/zola-deploy-action`]: https://github.com/shalzz/zola-deploy-action
[`rossjrw/pr-preview-action`]: https://github.com/rossjrw/pr-preview-action
[`ci.yml`]: https://github.com/orhun/personal-blog/blob/main/.github/workflows/ci.yml
[`lycheeverse/lychee-action`]: https://github.com/lycheeverse/lychee-action
[`lychee`]: https://lychee.cli.rs/
[`codespell-project/actions-codespell`]: https://github.com/codespell-project/actions-codespell
[`codespell`]: https://github.com/codespell-project/codespell
[`typos`]: https://github.com/crate-ci/typos
[Mongolia post]: https://blog.orhun.dev/mongolia-trip/
[AvidRustacean]: https://github.com/TylerBloom/avid-rustacean
[Ratatui]: https://ratatui.rs/
