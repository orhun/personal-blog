+++
title = "Debugging Rust in 2026"
date = 2026-09-20

[taxonomies]
categories = ["Thoughts"]
+++

Let's explore the current state of debugging in Rust.

<!-- more -->

Rust is a great language for many reasons: safety, performance and tooling. However, debugging is maybe one of the weakest points still. Interestingly enough, the [Rust debugging survey 2026 results](https://blog.rust-lang.org/2026/09/07/rust-debugging-survey-2026-results/) show that the majority (53.2%) of Rust users don't use a debugger **at all**. Many Rust developers (81.3%) say that prints are simply easier/faster. The setup is also another problem: many users find it difficult to set up a debugger and it's not a beginner-friendly feature. Especially when you include async (28.1%) and macros (23.0%), stepping features break most of the time.

However, the situation is still better than it was in 2021, when the survey was first conducted. The number of users who use a debugger has increased from 36% to 46.8%!

This made me think: **how is the debugging experience in Rust today?**  
So let's dive into the techniques and tools that are available and how they are used in practice.

## The Scenario

We will be using the [Graham scan algorithm](https://en.wikipedia.org/wiki/Graham_scan) as an example. The algorithm computes the convex hull of a set of points in a plane: the smallest convex shape that encloses every point. Basically, you can think of it as stretching a rubber band around a collection of nails.

🐀: Here, I built a TUI with [Ratatui](https://ratatui.rs) to visualize the algorithm and the bug:

![](/graham-scan-tui.gif)

The [source code is available on GitHub](https://github.com/orhun/debugging-rust).

You can see that it fails to enclose `P2`: the computed fence is red while the expected fence is green. Now, let's go through the available debugging techniques to fix it!

## 1. println!, dbg! or tracing

There is already a unit test that fails, so we can use it as a debugging loop. Simply add `println!` or `dbg!` statements to the code and run the test until we find the bug.

<video controls muted width="100%">
  <source src="/print-debug.mp4" type="video/mp4">
</video>

> Here, we print the intermediate ordering, rerun the failing test and see that points with the same polar angle are ordered farthest-first.
> Reversing the distance comparison fixes the ordering and the next test run passes.

This is the most common way of debugging in Rust and it works well for small programs.
However, you see the problem: it is an iterative and tedious process. You need to recompile and run the program every time which takes time.
It is also not ideal for big projects. It still works though!

🐀: What else?

## 2. rust-gdb or rust-lldb in the terminal

We can inspect the same failing test without changing the source code via [rust-lldb](https://doc.rust-lang.org/nightly/rustc/lldb.html) or [rust-gdb](https://doc.rust-lang.org/nightly/rustc/gdb.html):

```bash
cargo test --lib --no-run
rust-lldb target/debug/deps/graham-<hash>
```

From there, we set breakpoints, run only the failing test and inspect the current frame:

```text
(lldb) breakpoint set --file lib.rs --line 204
(lldb) run tests::keeps_farthest_collinear_point --exact --nocapture
(lldb) frame variable pivot a b
```

![](/debugging-rust-lldb.gif)

> At the breakpoint, `a` is 36 units squared from the pivot while `b` is only 4. The comparator uses `b.cmp(a)`, so points with the same polar angle are sorted in reverse distance order.
>
> That is the bug: the farther point is processed first and later removed from the hull.

So let's apply the fix:

```diff
-Ordering::Equal => distance_squared(pivot, b).cmp(&distance_squared(pivot, a)),
+Ordering::Equal => distance_squared(pivot, a).cmp(&distance_squared(pivot, b)
```

We can also use the TUI to visualize it:

![](/graham-scan-tui-fixed.gif)

🐀: Yay, the rubber band is now wrapped around all the points.

Unlike print debugging, this lets us inspect more state without editing and recompiling the program after every question. The trade-off is manual workflow, settings useful breakpoints and knowing which debugger commands to run.

🐀: Okay, but is there a more friendly way to debug this?

## 3. RustRover's debugger

[RustRover](https://www.jetbrains.com/rust/) provides a graphical interface for GDB and LLDB, which means we can use the same workflow as before but with a more user-friendly interface.

<video controls muted width="100%">
  <source src="/debugging-rust-rover.mp4" type="video/mp4">
</video>

There is also some interesting Rust-specific machinery behind the interface. In my [Rust in Production episode with Matthias Endler](https://corrode.dev/podcast/s06e09-jetbrains/), I explained how RustRover turns the source into its own PSI, then a typed high-level representation and finally MIR. When we evaluate a Rust expression or call a function during a debug session, RustRover lowers it to MIR and sends it to the debugger to execute against the paused process. So the debugger actually understands the Rust expression we are asking it to evaluate!

---

The main reason is that the setup is difficult, and prints are simply easier/faster. The top two complaints among users are poor value representation and inability to print variables; stepping breaks most on async and macros. These are sports for us to focus attention.

Rust debugging survey 2026 results: [blog.rust-lang.org]: https://blog.rust-lang.org/2026/09/07/rust-debugging-survey-2026-results/

LLDB wins by ≥6 points on Windows, WSL and macOS (Linux is the only place the GDB CLI wins by 0.4%)
Only 46% use a debugger at all; 81%+ of non-users of debugger say prints are simply easier/faster. The setup is difficult for the users, this is not a beginner-friendly feature.
The top two complaints among users are poor value representation (74%) and inability to print variables (55%); stepping breaks most on async (28%) and macros (23%). These are sports for us to focus attention. 
44% of respondents use debuggers is if they are debugging programs that use Rust alongside other programming languages.

1. println!, dbg!, or tracing

Unit tests as the debugging loop

2. rust-gdb or rust-lldb in a terminal

3. RustRover's debugger (GDB/LLDB)

4. AI assisted debugging

Asking the agent to debug the problems

---

Hi! I've been experimenting with the debugger skill for RR, here's the demo of how it looks (with the IJ Air plugin!).
(This is my old implementation of the Graham algorithm with a bug injected. I asked AI to debug it and fix it, the middle part of the video is sped up)

https://jetbrains.slack.com/archives/GAY612Q1L/p1785958605543789

https://docs.google.com/document/d/1BkNCufCFRN-oBrvHfazG7Hx5qRaSp7Nf_eOtMhaoU50/edit?tab=t.0
