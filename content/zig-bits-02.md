+++
title = "Zig Bits 0x2: Using defer to defeat memory leaks"
date = 2023-03-21

[taxonomies]
categories = ["Zig Bits"]
+++

Let's talk about how to detect memory leaks in Zig and avoid them by using the `defer` statement.

<!-- more -->

<center>

<img src="/ziggy_fly.svg" style="width: 25%"/>

</center>

### Retrospective

In the [first part](https://blog.orhun.dev/zig-bits-01/) of the Zig Bits series, I covered the topic of returning slices from functions and how Zig manages memory. I have learned that Zig arrays are allocated on the stack and they could be corrupted when they are accessed in the case that memory pointed to them are no longer available. It was fun.

Some people also pointed out that one of the examples I gave wasn't idiomatic:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Returns a slice.
fn zigBits() ![]u8 {
    // Create an array literal.
    var message = [_]u8{ 'z', 'i', 'g', 'b', 'i', 't', 's' };

    // Print the array as string.
    std.log.debug("{s}", .{message});

    // Allocate the slice on the heap and return.
    var message_copy = try std.heap.page_allocator.dupe(u8, &message);
    return message_copy;
}

/// Entrypoint of the program.
pub fn main() !void {
    // Get the message.
    const message = try zigBits();

    // Print the message.
    std.log.debug("{s}", .{message});
}
```

Here you can see that I used a hardcoded [`std.heap.page_allocator`](https://ziglang.org/documentation/master/std/#A;std:heap.page_allocator) for duplicating the memory. However, the Zig convention for allocation is that we declare an allocator at another higher-scoped unit of code and then pass the allocator as an argument to the function like the following:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Returns a slice.
fn zigBits(allocator: std.mem.Allocator) ![]u8 {
    // Create an array literal.
    var message = [_]u8{ 'z', 'i', 'g', 'b', 'i', 't', 's' };

    // Print the array as string.
    std.log.debug("{s}", .{message});

    // Allocate the slice and return.
    var message_copy = try allocator.dupe(u8, &message);
    return message_copy;
}

/// Entrypoint of the program.
pub fn main() !void {
    // Get the message.
    const allocator = std.heap.page_allocator;
    const message = try zigBits(allocator);

    // Print the message.
    std.log.debug("{s}", .{message});
}
```

This way, the callers can decide the allocator type due to it being defined as generic [std.mem.Allocator](https://ziglang.org/documentation/master/std/#A;std:mem.Allocator).

Now that this allocator usage is made clear, let's bear this in mind and jump right into our example program for Zig Bits part 2!

#### Concatenating paths

<img src="/ziggy_fix.svg" style="width: 25%"/>

Here is our example program which simply aims to concatenate filesystem paths and print the result:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Concatenates the given paths and returns a path under '/home' directory.
fn concatPath(allocator: std.mem.Allocator, p1: []const u8, p2: []const u8) ![]const u8 {
    // Define the path separator (which is '/' for POSIX).
    const separator = std.fs.path.sep_str;

    // Concatenate the paths and return the result.
    const path = try std.fs.path.join(allocator, &.{ separator, "home", p1, p2 });
    return path;
}

/// Entrypoint of the program.
pub fn main() !void {
    // Choose an allocator based on our needs.
    const allocator = std.heap.page_allocator;

    // Concatenate.
    const path = try concatPath(allocator, "zig", "bits");

    // Print the final path.
    std.log.debug("{s}", .{path});
}
```

As you might have guessed, the main thing we should focus on in this program is the `concatPath` function. It takes a generic allocator type (`allocator`) and concatenates the given `p1` and `p2` path strings. It also prepends "/home" to the final path. Very cool!

**Q**: Alright, we got it. Are you gonna run the damn thing?

Oh yeah, sure:

```sh
$ zig build run

debug: /home/zig/bits
```

As you can see everything worked and we have successfully printed out the final path.

See you in the next post!

---

**Q**: Uhhh, that can't be it, right?

Of course not! Don't you see the memory leak there!?

**Q**: What?

You heard me. Right there!

**Q**: It is pretty much impossible to see where you are pointing at right now.

Okay okay, let's take a step back and start over.

### Explanation

<img src="/ziggy_think.svg" style="width: 25%"/>

What even is a memory leak and why does it happen?

> Imagine you're on vacation in Costa Rica and you come across an iguana sanctuary where they let you feed the iguanas. You start feeding them and soon realize that they have bottomless stomachs! No matter how much you feed them, they keep eating and eating until they become giant, indigestible beasts.
>
> In computer programming, a memory leak is like those insatiable iguanas - a program keeps consuming more and more memory without any limit. Just like the iguanas are never full, the program fails to release the memory it has used, causing a continual drain on the computer's resources. This can slow down or even crash the system if enough memory is consumed.
>
> Memory leaks are often caused by programming mistakes, like failing to release memory when it's no longer needed. They can also occur due to inefficient code or poorly designed algorithms. But just like not giving into the iguanas' endless hunger, programmers need to identify and fix memory leaks to prevent their programs from becoming giant, unmanageable beasts.

**Q**: Okay you had your fun with [ChatGPT](https://openai.com/blog/chatgpt), now, how do we check if we have memory leaks in our program?

Before I show the conventional way of checking memory leaks, let's try something traditional: [Valgrind](https://valgrind.org/)!

#### Checking memory leaks with Valgrind

```sh
$ valgrind --leak-check=full --track-origins=yes --show-leak-kinds=all --num-callers=15 ./zig-out/bin/app

==794283== Memcheck, a memory error detector
==794283== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==794283== Using Valgrind-3.20.0 and LibVEX; rerun with -h for copyright info
==794283== Command: ./zig-out/bin/app
==794283==
debug: /home/zig/bits
==794283==
==794283== HEAP SUMMARY:
==794283==     in use at exit: 0 bytes in 0 blocks
==794283==   total heap usage: 0 allocs, 0 frees, 0 bytes allocated
==794283==
==794283== All heap blocks were freed -- no leaks are possible
==794283==
==794283== For lists of detected and suppressed errors, rerun with: -s
==794283== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

**Q**: A-ha! I knew that you were trolling me. Where is the memory leak?

Don't jump to the conclusion that quickly. [Apparently](https://dev.to/stein/some-notes-on-using-valgrind-with-zig-35c1), we need to switch to the [C allocator](https://ziglang.org/documentation/master/std/#A;std:heap.c_allocator) so that Valgrind can trace memory allocations correctly via preloading:

```diff
- const allocator = std.heap.page_allocator;
+ const allocator = std.heap.c_allocator;
```

Let's check if our program is still working properly:

```sh
$ zig build run

/usr/lib/zig/std/heap.zig:25:13: error: C allocator is only available when linking against libc
            @compileError("C allocator is only available when linking against libc");
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
/usr/lib/zig/std/c/linux.zig:289:12: error: dependency on libc must be explicitly specified in the build command
pub extern "c" fn posix_memalign(memptr: *?*anyopaque, alignment: usize, size: usize) c_int;
           ^~~
/usr/lib/zig/std/c/linux.zig:290:12: error: dependency on libc must be explicitly specified in the build command
pub extern "c" fn malloc_usable_size(?*const anyopaque) usize;
           ^~~
/usr/lib/zig/std/c.zig:236:12: error: dependency on libc must be explicitly specified in the build command
pub extern "c" fn free(?*anyopaque) void;
           ^~~
```

Ah, right. We need to link against [`libc`](https://en.wikipedia.org/wiki/C_standard_library) in `build.zig`:

```zig
const exe = b.addExecutable("app", "src/main.zig");
exe.linkLibC();
```

Let's run Valgrind again:

```sh
$ valgrind --leak-check=full --track-origins=yes --show-leak-kinds=all --num-callers=15 ./zig-out/bin/app

==800663== Memcheck, a memory error detector
==800663== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==800663== Using Valgrind-3.20.0 and LibVEX; rerun with -h for copyright info
==794283== Command: ./zig-out/bin/app
==800663==
debug: /home/zig/bits
==800663==
==800663== HEAP SUMMARY:
==800663==     in use at exit: 14 bytes in 1 blocks
==800663==   total heap usage: 1 allocs, 0 frees, 14 bytes allocated
==800663==
==800663== 14 bytes in 1 blocks are definitely lost in loss record 1 of 1
==800663==    at 0x4846E20: memalign (vg_replace_malloc.c:1531)
==800663==    by 0x4846F81: posix_memalign (vg_replace_malloc.c:1703)
==800663==    by 0x213E1A: heap.CAllocator.alignedAlloc (heap.zig:62)
==800663==    by 0x213ACF: heap.CAllocator.alloc (heap.zig:110)
==800663==    by 0x214602: rawAlloc (Allocator.zig:154)
==800663==    by 0x214602: mem.Allocator.allocAdvancedWithRetAddr__anon_3090 (Allocator.zig:302)
==800663==    by 0x212767: mem.Allocator.alloc__anon_1897 (Allocator.zig:194)
==800663==    by 0x211C6C: fs.path.joinSepMaybeZ__anon_1894 (path.zig:79)
==800663==    by 0x211218: fs.path.join (path.zig:111)
==800663==    by 0x212A43: main.concatPath (main.zig:12)
==800663==    by 0x212B77: main.main (main.zig:22)
==800663==    by 0x212FD7: callMain (start.zig:614)
==800663==    by 0x212FD7: initEventLoopAndCallMain (start.zig:548)
==800663==    by 0x212FD7: callMainWithArgs (start.zig:498)
==800663==    by 0x212FD7: main (start.zig:513)
==800663==
==800663== LEAK SUMMARY:
==800663==    definitely lost: 14 bytes in 1 blocks
==800663==    indirectly lost: 0 bytes in 0 blocks
==800663==      possibly lost: 0 bytes in 0 blocks
==800663==    still reachable: 0 bytes in 0 blocks
==800663==         suppressed: 0 bytes in 0 blocks
==800663==
==800663== For lists of detected and suppressed errors, rerun with: -s
==800663== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

Bingo! You can see from this output that our `concatPath` function actually leaks memory:

```sh
==800663==    by 0x211218: fs.path.join (path.zig:111)
==800663==    by 0x212A43: main.concatPath (main.zig:12)
==800663==    by 0x212B77: main.main (main.zig:22)
==800663==    by 0x212FD7: callMain (start.zig:614)
```

**Q**: Nice. But this Valgrind setup seems to require a bit of work and it requires `libc`. Isn't there a more _native_ and recommended way of checking memory leaks in Zig?

Good question. Of course, there is!

#### Using [`std.testing.allocator`](https://ziglang.org/documentation/master/std/#A;std:testing.allocator) for detecting memory leaks

When code allocates memory using the Zig standard library's testing allocator, `std.testing.allocator`, the default test runner will report any leaks that are found from using the testing allocator.

For utilizing this, we can modify our program to add a simple test case that will check the expected output:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Concatenates the given paths and returns a path under '/home' directory.
fn concatPath(allocator: std.mem.Allocator, p1: []const u8, p2: []const u8) ![]const u8 {
    // Define the path separator (which is '/' for POSIX).
    const separator = std.fs.path.sep_str;

    // Concatenate the paths and return.
    const path = try std.fs.path.join(allocator, &.{ separator, "home", p1, p2 });
    return path;
}

// Test path concatenation.
test "path concat" {
    // Use the testing allocator for memory leak detection.
    const allocator = std.testing.allocator;

    // Run the function.
    const path = try concatPath(allocator, "zig", "bits");

    // Assert the result.
    try std.testing.expectEqualStrings("/home/zig/bits", path);
}
```

Let's run the tests:

```sh
$ zig build test

Test [1/1] test.path concat... [gpa] (err): memory address 0x7f6544978000 leaked:
/usr/lib/zig/std/fs/path.zig:79:36: 0x214449 in joinSepMaybeZ__anon_2120 (test)
    const buf = try allocator.alloc(u8, total_len);
                                   ^
/usr/lib/zig/std/fs/path.zig:111:25: 0x213a6b in join (test)
    return joinSepMaybeZ(allocator, sep, isSep, paths, false);
                        ^
/tmp/tmp.mxqPUXlhnh/src/main.zig:12:38: 0x2151e3 in concatPath (test)
    const path = try std.fs.path.join(allocator, &.{ separator, "home", p1, p2 });
                                     ^
/tmp/tmp.mxqPUXlhnh/src/main.zig:22:32: 0x216523 in test.path concat (test)
    const path = try concatPath(allocator, "zig", "bits");
                               ^
/usr/lib/zig/test_runner.zig:63:28: 0x21e0d3 in main (test)
        } else test_fn.func();
                           ^
/usr/lib/zig/std/start.zig:604:22: 0x216dfc in posixCallMainAndExit (test)
            root.main();
                     ^
/usr/lib/zig/std/start.zig:376:5: 0x216901 in _start (test)
    @call(.{ .modifier = .never_inline }, posixCallMainAndExit, .{});
    ^

All 1 tests passed.
1 errors were logged.
1 tests leaked memory.
```

Great! We just spotted a memory leak from the tests.

```sh
Test [1/1] test.path concat... [gpa] (err): memory address 0x7f6544978000 leaked:
```

That's something very powerful when you think of it. Also, it definitely makes testing the Zig code more important.

**Q**: It's cool and all but I still don't know how to _fix_ the memory leak.

Oh, that... Follow me.

### Solution

<img src="/ziggy_cool.svg" style="width: 20%"/>

The solution is to free the memory after using it. In other words, we need to free the memory allocated by `path` at the end of the program. Luckily, allocators in Zig have [`free()`](https://ziglang.org/documentation/master/std/#A;std:mem.Allocator.free) method for freeing arrays.

**Q**: So we will just call `allocator.free(path);` at the end of the program?

Yes, but not quite. It would not be very convenient for having a lot of free calls at the end of our program. For example, in C, cleaning stuff up can leave a large distance between where the resource was acquired and where it is disposed of. However, in Zig, we have [`defer`](https://ziglang.org/documentation/master/#defer) statement for that purpose. It allows us to put the resource acquisition and disposal immediately next to each other in the code. For example:

```zig
{
    // allocate some memory
    var general_purpose_allocator = std.heap.GeneralPurposeAllocator(.{}){};
    const gpa = general_purpose_allocator.allocator();
    const args = try std.process.argsAlloc(gpa);

    // `defer` will execute an expression at the end of the current scope
    defer std.process.argsFree(gpa, args);

    // more code
    // ...

} // memory is freed!
```

It is a pattern that is often used when we acquire some sort of resource and want to ensure that the resource is dispensed with before we return, or otherwise leave a scope. Common use cases for `defer` are things like files or memory, but it can be anything, and any code too; the `defer` statement can accept a block of code if necessary. The only exception is, we can't use a `return` statement from `defer`.

Let's modify our program to free the allocated memory at the end of the scope by using `defer`:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Concatenates the given paths and returns a path under '/home' directory.
fn concatPath(allocator: std.mem.Allocator, p1: []const u8, p2: []const u8) ![]const u8 {
    // Define the path separator (which is '/' for POSIX).
    const separator = std.fs.path.sep_str;

    // Concatenate the paths and return.
    const path = try std.fs.path.join(allocator, &.{ separator, "home", p1, p2 });
    return path;
}

/// Entrypoint of the program.
pub fn main() !void {
    // Choose an allocator based on our needs.
    const allocator = std.heap.page_allocator;

    // Concatenate.
    const path = try concatPath(allocator, "zig", "bits");
    defer allocator.free(path); // free the memory at the end of the scope

    // Print the final path.
    std.log.debug("{s}", .{path});
}

// Test path concatenation.
test "path concat" {
    // Use the testing allocator for memory leak detection.
    const allocator = std.testing.allocator;

    // Run the function.
    const path = try concatPath(allocator, "zig", "bits");
    defer allocator.free(path); // free the memory at the end of the scope

    // Assert the result.
    try std.testing.expectEqualStrings("/home/zig/bits", path);
}
```

Let's run the tests now:

```sh
$ zig build test

All 1 tests passed.
```

Yay! No leaks 💯

For making it more clear, we can take a look how `defer` works under scopes:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Concatenates the given paths and returns a path under '/home' directory.
fn concatPath(allocator: std.mem.Allocator, p1: []const u8, p2: []const u8) ![]const u8 {
    // Define the path separator (which is '/' for POSIX).
    const separator = std.fs.path.sep_str;

    // Concatenate the paths and return.
    const path = try std.fs.path.join(allocator, &.{ separator, "home", p1, p2 });
    return path;
}

/// Entrypoint of the program.
pub fn main() !void {
    // Choose an allocator based on our needs.
    const allocator = std.heap.page_allocator;
    {

        // Concatenate.
        const path = try concatPath(allocator, "zig", "bits");
        defer allocator.free(path); // free the memory at the end of the scope

        // Print the final path.
        std.log.debug("{s}", .{path});
    }

    {
        // Concatenate.
        const path = try concatPath(allocator, "zig", "bits2");
        defer allocator.free(path); // free the memory at the end of the scope

        // Print the final path.
        std.log.debug("{s}", .{path});
    }
}

// Test path concatenation.
test "path concat" {
    // Use the testing allocator for memory leak detection.
    const allocator = std.testing.allocator;

    // Run the function.
    const path = try concatPath(allocator, "zig", "bits");
    defer allocator.free(path); // free the memory at the end of the scope

    // Assert the result.
    try std.testing.expectEqualStrings("/home/zig/bits", path);
}
```

#### Bonus I

It was also pointed on Discord by `rimuspp#1713` that there is another way of dealing with leaks: using the general purpose allocator!

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Concatenates the given paths and returns a path under '/home' directory.
fn concatPath(allocator: std.mem.Allocator, p1: []const u8, p2: []const u8) ![]const u8 {
    // Define the path separator (which is '/' for POSIX).
    const separator = std.fs.path.sep_str;

    // Concatenate the paths and return.
    const path = try std.fs.path.join(allocator, &.{ separator, "home", p1, p2 });
    return path;
}

/// Entrypoint of the program.
pub fn main() !void {
    // Choose an allocator based on our needs.
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();
    {

        // Concatenate.
        const path = try concatPath(allocator, "zig", "bits");
        defer allocator.free(path); // free the memory at the end of the scope

        // Print the final path.
        std.log.debug("{s}", .{path});
    }

    {
        // Concatenate.
        const path = try concatPath(allocator, "zig", "bits2");

        // Print the final path.
        std.log.debug("{s}", .{path});
    }
}

// Test path concatenation.
test "path concat" {
    // Use the testing allocator for memory leak detection.
    const allocator = std.testing.allocator;

    // Run the function.
    const path = try concatPath(allocator, "zig", "bits");
    defer allocator.free(path); // free the memory at the end of the scope

    // Assert the result.
    try std.testing.expectEqualStrings("/home/zig/bits", path);
}
```

This will leak and report on the main program itself:

```sh
$ zig build run

debug: /home/zig/bits
debug: /home/zig/bits2
error(gpa): memory address 0x7f88d8788000 leaked:
/usr/lib/zig/std/fs/path.zig:79:36: 0x2129f9 in joinSepMaybeZ__anon_3899 (tmp.bwNSfwKPEH)
    const buf = try allocator.alloc(u8, total_len);
                                   ^
/usr/lib/zig/std/fs/path.zig:111:25: 0x21201b in join (tmp.bwNSfwKPEH)
    return joinSepMaybeZ(allocator, sep, isSep, paths, false);
                        ^
/tmp/tmp.bwNSfwKPEH/src/main.zig:12:38: 0x2136e3 in concatPath (tmp.bwNSfwKPEH)
    const path = try std.fs.path.join(allocator, &.{ separator, "home", p1, p2 });
                                     ^
/tmp/tmp.bwNSfwKPEH/src/main.zig:34:36: 0x2138d7 in main (tmp.bwNSfwKPEH)
        const path = try concatPath(allocator, "zig", "bits2");
                                   ^
```

This approach is also what powers `std.testing.allocator`.

#### Bonus II

Another use case for `defer` is file operations such as creating/opening a file:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Reads the given file and returns a byte array with the length of `len`.
pub fn readBytes(
    allocator: std.mem.Allocator,
    path: []const u8,
    len: usize,
) ![]u8 {
    // Open the file.
    const file = try std.fs.cwd().openFile(path, .{});
    defer file.close(); // close the file at the end of the scope

    // Create a buffer for reading.
    var list = try std.ArrayList(u8).initCapacity(allocator, len);
    var buffer = list.allocatedSlice();

    // Read the file and return the read bytes.
    const bytes_read = try file.read(buffer);
    return buffer[0..bytes_read];
}

test "read bytes from the file" {
    // Get the current directory.
    var cwd_buffer: [std.fs.MAX_PATH_BYTES]u8 = undefined;
    var cwd = try std.os.getcwd(&cwd_buffer);

    // Concatenate the current directory with `build.zig`.
    const allocator = std.testing.allocator;
    const path = try std.fs.path.join(allocator, &.{ cwd, "build.zig" });
    defer allocator.free(path); // free the concatenated path

    // Read the contents of the file and compare.
    const bytes = try readBytes(allocator, path, 9);
    try std.testing.expectEqualStrings("const std", bytes);
    defer allocator.free(bytes); // free the read bytes
}
```

In this example, we are reading a number of bytes from an arbitrary file with `readBytes` function. As you can see, `defer` can be used to close the file at the end of a function. Also, we are using it for freeing the memory in the tests as shown before.

Alternatively, it is possible to use [`errdefer`](https://ziglang.org/documentation/master/#errdefer) for cases that we want to execute something if the scope returns with an error:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

// This is especially useful in allowing a function to clean up properly
// on error, and replaces goto error handling tactics as seen in C.
fn deferErrorExample(is_error: bool) !void {
    std.log.debug("start of function", .{});

    // This will always be executed on exit
    defer {
        std.log.debug("end of function", .{});
    }

    errdefer {
        std.log.debug("encountered an error!", .{});
    }

    if (is_error) {
        return error.DeferError;
    }
}

/// Entrypoint of the program.
pub fn main() !void {
    try deferErrorExample(false);
    try deferErrorExample(true);
}
```

When we run it:

```sh
$ zig build run

debug: start of function
debug: end of function

debug: start of function
debug: encountered an error!
debug: end of function
error: DeferError
```

### Conclusion

<img src="/ziggy_pizza.svg" style="width: 25%"/>

In Zig, the most convenient way of detecting memory leaks seems to test the code via `std.testing.allocator`. For that, we need to structure our program to pass `std.mem.Allocator` to the functions that allocate memory. After that, we can use the `free()` method of the allocator to free the memory.

In that case, `defer` statement is handy for ensuring that the cleanup will happen at the end of the scope. It can also be used for different purposes that require execution when leaving the scope.

Hope you enjoyed the read, any feedback is welcome! Let me know what you think and what might be the topic for the next Zig Bits 🦎

[Here](https://blog.orhun.dev/zig-bits-03/) is the next post of the Zig Bits series! ("Mastering project management in Zig")

_görüşmək üzrə!_
