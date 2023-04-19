+++
title = "Zig Bits 0x3: Mastering project management in Zig"
date = 2023-04-19

[taxonomies]
categories = ["Guides"]
+++

In this post, I'm sharing tips & tricks about managing/maintaining an open-source Zig project and mentioning the commonly used practices. I'm also giving a brief introduction to my first-ever Zig project "[**linuxwave**](https://github.com/orhun/linuxwave)" which led to the writing of this series.

<!-- more -->

<center>

<img src="/ziggy_fly.svg" style="width: 25%"/>

</center>

Recently, I shared my first Zig project called "[linuxwave](https://github.com/orhun/linuxwave)" which is a simple command-line tool for generating music from a (random) stream of data. I will talk about it more at the end of this post.

While writing Zig in the past months, I sometimes had a hard time figuring out what to do or which way to go to efficiently do a certain thing. During those times, I either went for a hunt in the wild interwebs or asked a question in the [Discord server](https://discord.gg/invite/zig) of Zig. Unfortunately, there is still a lack of documentation so I turned this _getting help process_ into writing blog posts and sharing my findings. Here are the first 2 parts of this series which came out exactly in the way that I described - me trying to figure out things:

- [Zig Bits 0x1: Returning slices from functions](https://blog.orhun.dev/zig-bits-01/)
- [Zig Bits 0x2: Using defer to defeat memory leaks](https://blog.orhun.dev/zig-bits-02/)

In the meantime, I was working on my project and writing Zig at every chance that I get. I must say, I absolutely enjoyed this process since it's been a while since I learned something low-level like Zig. Actually, it's been a while since I learned a new programming language as well. Development was really fun.

However, like every good thing, this had an end. After the core functionality is there and the Zig code is thoroughly tested, I started to do chores that are more related to _project management_ rather than actual development. Although it is sometimes boring, I still believe that doing _grunt_ tasks is also important since it makes you able to efficiently maintain the project and paves the way for new contributors.

So I will be sharing my experience of project management in Zig to document what could be done to achieve a better stance for your Zig project in the open-source community. This could also be perceived as me trying to apply the same open-source maintenance techniques that I use for my Rust projects to a Zig project.

I will cover the following topics:

- Adding libraries
- Running tests
- Code coverage
- Documentation generation
- CI/CD

So let's jump right into it.

---

## Adding libraries ⚡

For newcomers, this is one of the prominent questions. Currently, there does not seem to be an easy and standard way like `cargo add` for adding libraries to your Zig project. However, there are some package managers available for this purpose:

- [gyro](https://github.com/mattnite/gyro): A Zig package manager with an index, build runner, and build dependencies.
- [zigmod](https://github.com/nektro/zigmod): A package manager for the Zig programming language.
  - [aquila](https://github.com/nektro/aquila): A package index for Zig projects.

For my project, I followed a more straightforward approach: [Git Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)!

1- Create `libs` directory at the root of your project.  
2- Add the library as a git submodule:

Either run `git submodule add <remote_url> libs/<lib>` or add `.gitmodules` file. For example, for [`zig-clap`](https://github.com/Hejsil/zig-clap):

```ini
[submodule "libs/zig-clap"]
  path = libs/zig-clap
  url = https://github.com/Hejsil/zig-clap
```

3- Then you need to add the package to your project in `build.zig` as follows:

```zig
const exe = b.addExecutable("exe_name", "src/main.zig");
// ...
// ...
exe.addPackagePath("clap", "libs/zig-clap/clap.zig")
```

4- Now you can import the library from your source files like this:

```zig
const clap = @import("clap");
```

[Hejsil](https://github.com/Hejsil) summarizes this very well [in this issue](https://github.com/Hejsil/zig-clap/issues/61):

> Right now, there is no standard way to install Zig libraries. There are a few common ways people do it:
>
> - git submodule or copying and pasting the library into their own project.
>
>   - After this, you can add `exe.addPackagePath("clap", "path/to/clap.zig");` to your `build.zig` file to use the library
>
> - Use an unofficial package manager such as [`zigmod`](https://github.com/nektro/zigmod) or [`gyro`](https://github.com/mattnite/gyro)
>   - Look at the docs for these package managers for how to install packages using them. They should both be able to install `zig-clap` as far as I know.

---

## Running tests ⚡

While writing tests for my project, I realized I need to add tests for each file and specify which files to test in `build.zig`. For example:

```zig
const exe_tests = b.addTest("src/main.zig");
exe_tests.setTarget(target);
exe_tests.setBuildMode(mode);

const test_step = b.step("test", "Run unit tests");
test_step.dependOn(&exe_tests.step);
```

When I run this code with `zig build test`, it will only run the tests in `main.zig`. What I wanted is to run the tests in _every_ file in my project so I did the most obvious thing that comes to mind:

```zig
const test_step = b.step("test", "Run tests");

// loop through the modules and add them for testing
for ([_][]const u8{ "main", "wav", "file", "gen" }) |module| {
    const test_module = b.fmt("src/{s}.zig", .{module});
    var exe_tests = b.addTest(test_module);
    test_step.dependOn(&exe_tests.step);
}
```

```sh
$ zig build test

1/1 test.run... OK
All 1 tests passed.
1/2 test.encode WAV... OK
2/2 test.stream out WAV... OK
All 2 tests passed.
1/1 test.read bytes from the file... OK
All 1 tests passed.
1/1 test.generate music... OK
All 1 tests passed.
```

This works just fine but there is also another way (and a better way) of doing this which is mentioned in this [Reddit thread](https://www.reddit.com/r/Zig/comments/y65qa6/how_to_test_every_file_in_a_simple_zig_project/).

> You need to have a common file that "references" the testing code for them to be run by a single "addTest" statement in your build file. For example, in `src/myLibrary.zig`:

```zig
pub const A = @import("A.zig");
pub const B = @import("B.zig");
pub const SomeDataType = @import("C.zig").SomeDataType;

test {
  @import("std").testing.refAllDecls(@This());
}
```

> Then in `build.zig`, you can simply add it as `b.addTest("src/myLibrary.zig")`.

---

## Code coverage ⚡

One of the cool things you can do is to track how much of your tests cover your code. This also helps with testing the functionality better and potentially eliminating bugs. Sometimes you even need to refactor your code to write tests for a certain function/module which makes the code better at the end of the day.

In Rust projects, I usually follow this path for testing/coverage:

- Write tests
- Run them using [cargo-nextest](https://github.com/nextest-rs/nextest)
- Generate code coverage report with a tool
  - [cargo-tarpaulin](https://github.com/xd009642/tarpaulin)
  - [cargo-llvm-cov](https://github.com/taiki-e/cargo-llvm-cov)
- Upload it to [Codecov.io](https://about.codecov.io)

For Zig, we will do the following:

- Write tests
- Run them using `zig build test`
- Generate code coverage report with [kcov](https://github.com/SimonKagstrom/kcov)
- Upload it to [Codecov.io](https://about.codecov.io)

After the tests are passed, the first step is to generate a code coverage report. While researching this topic, I came across [this article](https://zig.news/squeek502/code-coverage-for-zig-1dk1) that tells you how you can do that with [`kcov`](https://github.com/SimonKagstrom/kcov).

We simply need to add a new flag in `build.zig` for generating the coverage report:

```zig
const coverage = b.option(bool, "test-coverage", "Generate test coverage") orelse false;

const exe_tests = b.addTest("src/main.zig");
exe_tests.setTarget(target);
exe_tests.setBuildMode(mode);

if (coverage) {
    exe_tests.setExecCmd(&[_]?[]const u8{
        "kcov",
        "kcov-output",
        null,
    });
}
```

Now when you run `zig build test -Dtest-coverage`, the report will be generated in `kcov-output`.

Neat!

The next step is to upload this report to Codecov. I put together a simple GitHub Actions workflow for that purpose:

```yml
# contents of .github/workflows/ci.yml

name: Continuous Integration

on:
  push:
    branches:
      - main

jobs:
  test:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the repository
        uses: actions/checkout@v3
        with:
          # include libraries
          submodules: recursive

      - name: Install Zig
        uses: goto-bus-stop/setup-zig@v2
        with:
          version: 0.10.1

      - name: Install kcov
        run: |
          sudo apt-get update
          sudo apt-get install -y \
            --no-install-recommends \
            --allow-unauthenticated \
            kcov

      - name: Test
        run: zig build test -Dtest-coverage

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          name: code-coverage-report
          directory: kcov-output
          fail_ci_if_error: true
          verbose: true
```

And here we go: [https://app.codecov.io/gh/orhun/linuxwave](https://app.codecov.io/gh/orhun/linuxwave)

![codecov](/zig-codecov.png)

---

## Documentation generation ⚡

In [Chapter 3](https://ziglearn.org/chapter-3/#generating-documentation) of [Ziglearn](https://ziglearn.org/), the documentation generation is explained in detail:

> The Zig compiler comes with automatic documentation generation. This can be invoked by adding `-femit-docs` to your `zig build-{exe, lib, obj}` or `zig run` command. This documentation is saved into `./docs`, as a small static website.
>
> This generation is experimental, and often fails with complex examples. This is used by the [standard library documentation](https://ziglang.org/documentation/master/std/).

So we simply need to activate the `emit_docs` flag for the auto-generation of the documentation. As a bonus, I recommend adding a flag in `build.zig` as follows:

```zig
const documentation = b.option(bool, "docs", "Generate documentation") orelse false;

const exe = b.addExecutable(exe_name, "src/main.zig");
exe.setTarget(target);
exe.setBuildMode(mode);

if (documentation) {
    exe.emit_docs = .emit;
}
```

After this, you can generate the documentation via `zig build -Ddocs=true`.

The generated static site in `docs/` looks like this:

![docs](/zig-docs.png)

It is cool but I would like to go one step further and deploy this site to [GitHub Pages](https://pages.github.com).

I don't find it feasible to maintain the `docs/` folder in the repository so I added it to the `.gitignore`. What I want instead is to auto-generate the documentation and deploy it when I push commits to the `main` branch.

To do that, you first need to enable GitHub Actions feature for the GitHub Pages:

![GitHub Pages I](/github-pages-1.png)

> Repository -> Settings -> Pages -> Build and deployment -> Source -> Select GitHub Actions instead of the legacy Deploy from a branch option.

After that, we can simply add the following workflow file for deploying the documentation to the GitHub Pages:

```yml
# contents of .github/workflows/pages.yml

name: GitHub Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: [main]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  deploy:
    name: Deploy website
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Checkout the repository
        uses: actions/checkout@v3
        with:
          submodules: recursive

      - name: Install Zig
        uses: goto-bus-stop/setup-zig@v2
        with:
          version: 0.10.1

      - name: Generate documentation
        run: zig build -Ddocs=true

      - name: Setup Pages
        uses: actions/configure-pages@v3
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          # upload documentation
          path: docs

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

You can check out the deployment under Settings > Pages:

![GitHub Pages II](/github-pages-2.png)

As an example, see the documentation of my project here: [https://orhun.dev/linuxwave/docs/](https://orhun.dev/linuxwave/docs/)

---

## CI/CD ⚡

Lastly, let's set up a CI/CD workflow for our project. There will be 2 workflow files:

- `ci.yml`: for making sure that the project builds fine.
  - triggered via pushing a commit
- `cd.yml`: for distributing pre-built binaries for different platforms.
  - triggered via pushing a tag

### Continuous Integration

Here is a GitHub Actions workflow file that automates the process of building, testing, and checking the formatting of a project every time there's a push or a pull request to the main branch:

```yml
name: Continuous Integration

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  build:
    name: "Build with args: '${{ matrix.OPTIMIZE }}'"
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        OPTIMIZE: ["", "-Drelease-safe", "-Drelease-fast", "-Drelease-small"]
    steps:
      - name: Checkout the repository
        uses: actions/checkout@v3
        with:
          submodules: recursive

      - name: Install Zig
        uses: goto-bus-stop/setup-zig@v2
        with:
          version: 0.10.1

      - name: Build
        run: zig build ${{ matrix.OPTIMIZE }}

      - name: Test
        run: zig build test ${{ matrix.OPTIMIZE }}

      - name: Check formatting
        run: zig fmt --check .
```

The binary is tested for 3 different optimization profiles:

- [ReleaseFast](https://ziglang.org/documentation/master/#toc-ReleaseFast)
  - Fast runtime performance
  - Safety checks disabled
  - Slow compilation speed
  - Large binary size
- [ReleaseSafe](https://ziglang.org/documentation/master/#toc-ReleaseSafe)
  - Medium runtime performance
  - Safety checks enabled
  - Slow compilation speed
  - Large binary size
- [ReleaseSmall](https://ziglang.org/documentation/master/#toc-ReleaseSmall)
  - Medium runtime performance
  - Safety checks disabled
  - Slow compilation speed
  - Small binary size

### Continuous Deployment

Here is a GitHub Actions workflow file that automates the process of building a binary for a specific target and publishing it on GitHub every time there is a new version tag pushed to the repository:

```yml
name: Continuous Deployment

on:
  push:
    tags:
      - "v*.*.*"

jobs:
  publish-github:
    name: Publish on GitHub
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        TARGET:
          [
            x86_64-linux,
            x86_64-macos,
            x86_64-windows,
            aarch64-linux,
            aarch64-macos,
            aarch64-windows,
            arm-linux,
            riscv64-linux,
            i386-linux,
          ]

    steps:
      - name: Checkout the repository
        uses: actions/checkout@v3
        with:
          submodules: recursive

      - name: Set the release version
        run: echo "RELEASE_VERSION=${GITHUB_REF:11}" >> $GITHUB_ENV

      - name: Install Zig
        uses: goto-bus-stop/setup-zig@v2
        with:
          version: 0.10.1

      - name: Build
        run: zig build -Drelease-safe -Dtarget=${{ matrix.TARGET }}

      - name: Upload the binary
        uses: svenstaro/upload-release-action@v2
        with:
          file: zig-out/bin/binary-${{ env.RELEASE_VERSION }}-${{ matrix.TARGET }}*
          file_glob: true
          overwrite: true
          tag: ${{ github.ref }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
```

As you can see here, it is really easy to cross-compile Zig projects by only providing the `-Dtarget` option.

`TARGET` variable in this workflow consists of 2 parts:

- CPU architecture (e.g. `x86_64`)
- Operating system (e.g. `linux`)

You can get more information about cross compilation [here](https://ziglearn.org/chapter-3/#cross-compilation).

---

## [**linuxwave**](https://github.com/orhun/linuxwave) 🐧🎵

It is time for the main event.

![demo](https://raw.githubusercontent.com/orhun/linuxwave/main/assets/demo.gif)

<center>

[**Click here to watch the demo!**](https://youtube.com)

</center>

**linuxwave** is a command-line tool written in Zig for generating music from the entropy of the Linux kernel (`/dev/urandom`). It can also encode WAV files as a music composition from a given input file.

Here are some examples:

- Use A minor blues scale:

```sh
linuxwave -s 0,3,5,6,7,10 -n 220 -o blues.wav
```

- Read from an arbitrary file and turn it into a 10-second music composition:

```sh
linuxwave -i build.zig -n 261.63 -d 10 -o music.wav
```

- Generate a **calming music**:

```sh
linuxwave -r 2000 -f S32_LE -o calm.wav
```

- Generate a **chiptune music**:

```sh
linuxwave -r 44100 -f U8 -c 2 -o chiptune.wav
```

More examples/presets are available in the [repository](https://github.com/orhun/linuxwave).

✨ It is up to your imagination to generate fancy stuff!

⚡ GitHub: [**https://github.com/orhun/linuxwave**](https://github.com/orhun/linuxwave)

## Conclusion

<img src="/ziggy_pizza.svg" style="width: 25%"/>

Before anyone comments about the title of this post, I must admit that we didn't actually _master_ the project management in Zig completely (yet). But that was the most fitting title that ChatGPT suggested based on my bullet points so I decided to roll with it.

I believe I covered a couple of important techniques and best practices for efficiently managing open-source Zig projects. I might share more stuff about this topic in the future and feel free to let me know if you have additional tips or any questions!

I hope you enjoy the stuff you generate with **linuxwave**! Feel free to share them [here](https://github.com/orhun/linuxwave/discussions/1).

_sayonara!_
