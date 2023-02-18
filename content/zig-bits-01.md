+++
title = "Zig Bits 0x1: Returning slices from functions"
date = 2023-02-17

[taxonomies]
categories = ["Guides"]
+++

I decided to start a new blog series called "Zig Bits" where I share interesting bits of information about the [Zig programming language](https://ziglang.org/). This series will be especially for beginners because I'm also a beginner.

<!-- more -->

<center>

<img src="/ziggy_fly.svg" style="width: 25%"/>

</center>

Since it's the first part of the series, let me start by giving some information about Zig and my background.

`Zig` is an imperative, general-purpose, statically typed, compiled system programming language and toolchain for maintaining robust, optimal, and reusable software.

- _Robust_: Behavior is correct even for edge cases such as out of memory.
- _Optimal_: Write programs the best way they can behave and perform.
- _Reusable_: The same code works in many environments which have different constraints.
- _Maintainable_: Precisely communicate intent to the compiler and other programmers. The language imposes a low overhead to reading code and is resilient to changing requirements and environments.

It supports compile-time generics, reflection and evaluation, cross-compilation, and manual memory management. A major goal of Zig is to improve upon the [C language](<https://en.wikipedia.org/wiki/C_(programming_language)>), while also taking inspiration from [Rust](https://www.rust-lang.org/), among others.

There are a lot of resources to learn Zig, primarily:

- [Zig Documentation](https://ziglang.org/documentation/master/)
- [Zig `std` Reference](https://ziglang.org/documentation/master/std/#A;std)
- [Ziglearn.org](https://ziglearn.org/)
- [Zig Crash Course](https://ikrima.dev/dev-notes/zig/zig-crash-course/)
- [Zig Strings in 5 minutes](https://www.huy.rocks/everyday/01-04-2022-zig-strings-in-5-minutes)

I usually develop in `Rust` and I decided to learn Zig because the low-levelness of the language piqued my interest. I always liked writing C and learning something more robust is something that I would definitely consider. I'm already writing my first Zig project in my spare time (from other open source projects) and I really enjoy it!

Alright, it's time for the main event.

### Returning a slice in Zig

<img src="/ziggy_fix.svg" style="width: 25%"/>

Here's the code snippet for demonstrating what we're trying to do:

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Returns a slice.
fn zigBits() []u8 {
    // Create an array literal.
    var message = [_]u8{ 'z', 'i', 'g', 'b', 'i', 't', 's' };

    // Print the array as string.
    std.log.debug("{s}", .{message});

    // We need to use address-of operator (&) to coerce to slice type '[]u8'.
    return &message;
}

/// Entrypoint of the program.
pub fn main() void {
    // Get the message.
    const message = zigBits();

    // Print the message.
    std.log.debug("{s}", .{message});
}
```

**Q**: Cool! So we're just returning a slice from a function and printing its value?

Yes! We're expecting to see `zigbits` two times. One from inside the function and one from the `main`.

Let's run it:

```sh
$ zig build run

debug: zigbits
debug: �,�$
```

Uhm... What? That's not what we expected. Maybe run it again?

```sh
$ zig build run

debug: zigbits
debug:
       �;�
```

WTH?! Maybe print the `u8` array instead?

```diff
- std.log.debug("{s}", .{message});
+ std.log.debug("{d}", .{message});
```

```sh
$ zig build run

debug: { 122, 105, 103, 98, 105, 116, 115 }
debug: { 80, 129, 179, 51, 255, 127, 0 }
```

That's not the same array! What is going on here?

### Explanation

<img src="/ziggy_think.svg" style="width: 25%"/>

Let's look at this line:

```zig
return &message;
```

Here, we're actually returning a slice of a **stack-allocated** array.

```zig
try std.testing.expect(@TypeOf(&message) == *[7]u8);
```

Since the array is allocated on the _stack_, it could be corrupted when it's released i.e. when we return from the function. This is explained in the [Lifetime and Ownership](https://ziglang.org/documentation/master/#Lifetime-and-Ownership) part of the documentation:

> It is the Zig programmer's responsibility to ensure that a pointer is not accessed when the memory pointed to is no longer available. Note that a slice is a form of pointer, in that it references other memory.

This is the reason why we get random gibberish when we try to print the array contents after returning from the function.

Also, there is an issue in the [`ziglang/zig`](https://github.com/ziglang/zig) repository about this which states that this situation should lead to a compile error: [https://github.com/ziglang/zig/issues/5725](https://github.com/ziglang/zig/issues/5725)

### Solution

<img src="/ziggy_cool.svg" style="width: 20%"/>

We can work around this in a couple of ways:

- Passing the slice in as a parameter to the function
- Making the array global
- Allocating the slice (returning an allocated _copy_ of the slice)

Let's see how it works with each solution.

#### Passing the slice as a parameter

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Takes a slice as a parameter and fills it with a message.
fn zigBits(slice: []u8) void {
    // Create an array literal.
    var message = [_]u8{ 'z', 'i', 'g', 'b', 'i', 't', 's' };

    // Print the array as string.
    std.log.debug("{s}", .{message});

    // Update the slice.
    std.mem.copy(u8, slice, &message);
}

/// Entrypoint of the program.
pub fn main() void {
    // Define the message buffer.
    var message: [7]u8 = undefined;

    // Get the message.
    zigBits(&message);

    // Print the message.
    std.log.debug("{s}", .{message});
}
```

As you can see, we have updated the return value of the function to `void` and make it accept a slice parameter. Instead of `return`, we update the slice with [`std.mem.cpy`](https://ziglang.org/documentation/master/std/#A;std:mem.copy) method in the function.

(This approach is similar to passing a mutable reference (`&mut`) to a function in Rust.)

Let's run it:

```sh
$ zig build run

debug: zigbits
debug: zigbits
```

Yay!

#### Using a global array

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

// Create a global array literal.
var message = [_]u8{ 'z', 'i', 'g', 'b', 'i', 't', 's' };

/// Returns a slice.
fn zigBits() []u8 {
    // Print the array as string.
    std.log.debug("{s}", .{message});

    // We need to use address-of operator (&) to coerce to slice type '[]u8'.
    return &message;
}

/// Entrypoint of the program.
pub fn main() void {
    // Get the message.
    const msg = zigBits();

    // Print the message.
    std.log.debug("{s}", .{msg});
}
```

We have declared the array literal as top-level so it is lazily analyzed and accessible from the inner scopes.

```sh
$ zig build run

debug: zigbits
debug: zigbits
```

### Allocating the slice

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

We're making a copy of the slice on the heap on this line:

```zig
var message_copy = try std.heap.page_allocator.dupe(u8, &message);
```

This makes the slice available outside the function since it's now allocated on the heap:

```sh
$ zig build run

debug: zigbits
debug: zigbits
```

#### Bonus

Let's improve our program to return a slice with a designated length instead of stack allocated array.

```zig
// Zig version: 0.10.1

// Import the standard library.
const std = @import("std");

/// Returns a slice with the length of `len`.
fn zigBits(len: usize) ![]u8 {
    // Create an array literal.
    var message = [_]u8{ 'z', 'i', 'g', 'b', 'i', 't', 's' };

    // Print the array as string.
    std.log.debug("{s}", .{message});

    // A slice is a pointer and a length. The difference between an array and
    // a slice is that the array's length is part of the type and known at
    // compile-time, whereas the slice's length is known at runtime.
    try std.testing.expect(@TypeOf(message[0..len]) == []u8);

    // We're using `len` parameter to slice with a runtime-known value.
    // If `len` was declared as `comptime len`, then this value would be '*[N]u8'
    return message[0..len];
}

/// Entrypoint of the program.
pub fn main() !void {
    // Get the message.
    const message = try zigBits(7);

    // Print the message.
    std.log.debug("{s}", .{message});
}
```

This will <s>also work since we're not returning the address of the array:</s> not work too! (Thanks for the correction everyone!)

It's the same dangling pointer problem as in the first example. Both `&message` and `message[0..]` in Zig will return the same slice.

[@zenith391](https://www.reddit.com/user/zenith391/) explained really well why this example might have worked during my tests but it still an undefined behavior:

In the first example, the problem was that it was allocated on the stack and then freed. Now _suppose_ calling `std.log.debug` takes 8 bytes of stack space. But then calling `std.log.debug` causes more stack to be allocated, so it happily reuses and overwrites the bytes used for "zigbits".

The reason why it doesn't do that when the return type is `![]u8` is because an error union type like `![]u8` takes more stack bytes than `[]u8` which means our "zigbits" bytes are allocated farther in the stack.
The result is that when `std.log.debug` consumes its (hypothetical) 8 bytes of stack, it doesn't take enough to overwrite "zigbits".

You can check this behavior with [https://godbolt.org/z/cPWjajYxb](https://godbolt.org/z/cPWjajYxb) which shows that "zigbits" is placed _40_ bytes into the stack when using `![]u8` but only _16_ bytes into the stack when using `[]u8`.

Of course if you allocate more stack bytes (by making variables or calling more functions) it will eventually overwrite "zigbits". This also means this bonus snippet suffers from the same problem.

TLDR: Because `![]u8` is bigger, you have to use more of the stack in order to overwrite "zigbits"

### Conclusion

<img src="/ziggy_pizza.svg" style="width: 25%"/>

I'm fairly new to Zig and I really enjoy learning these concepts. Let me know via the comments below if I missed something or if there is an easier way.

Any feedback is welcome!

_¡cuidarse!_
