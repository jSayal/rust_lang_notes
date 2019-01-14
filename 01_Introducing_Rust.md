# Introducing Rust Programming

```Rust``` is a systems programming language developed by Mozilla Research. It allows low level hardware access like ```C/C++``` but with the conveniences and safety guarantees of a high-level language. Rust does not have a ```garbage collecter (GC)``` by default and it's binaries can be used as a drop in replacement for ```C``` binaries. That also means that we can use Rust to write Node.js add-ons. Instead of using `GC` Rust compiler observes code at compile time and helps prevent various types of error that can go undetected in `C/C++`.

Following sources are used to write this tutorial

1. https://doc.rust-lang.org/stable/book/ch00-00-introduction.html
2. https://learning-rust.github.io/docs/a1.why_rust.html

### Conventions

Rust uses snake case for variables and function names.

## Installation

For Mac and Linux users, use the following script. Also using `rustup` is the recommended method for installing Rust.

```bash
curl https://sh.rustup.rs -sSf | sh
```

For windows users, download **rustup-init.exe** from [www.rustup.rs](https://rustup.rs/) and run the installer.

Installation script/installer will add Rust to your system path on next reboot, but in Mac or Linux environment, you can add ```Rust``` to ```$PATH``` by executing the following command

```bash
$ source $HOME/.cargo/env
```

To check if installation is successful, type the following commands in terminal

```bash
$ rustc --version
$ cargo --veriosn

# If you receive some version numbers, you can safely assume that installtion
# is successful.
```

`rustup` is tool that installs Rust and manage update and switch between stable and nightly builds. Documentation for `rustup` is at https://github.com/rust-lang/rustup.rs/blob/master/README.md

Also, the following link for Rust official website is worth reading https://www.rust-lang.org/tools/install

## Cargo - Rust Package Manager

Cargo is a package and depedency mangement tool for ```Rust``` like `npm` is for `Node.js`.

```bash
# Basic rust usage

# To create new project
$ cargo new <project_name>

# To compile code in debug mode
$ cargo build

# To compile code in releae mode
$ cargo build --release

# To run project, with automatic compilition
$ cargo run

# To check if code will compile
$ cargo check
```

When a project is scafolded using cargo, you will following directory structure

```
For example, if we used cargo to generate hello_cargo project, the directory structure will be like this

hello_cargo
  |
  |__ src
  |__ target
  |     |
  |     |__ debug
  |     |__ rls
  |__ .gitignore
  |__ Cargo.lock
  |__ Cargo.toml

```

`Cargo.toml`file contains sections for [package] - listing package name, author, version & edition, and [dependencies] section to list all external dependencies. Rust compiler will download dependencies when compiling the projject.

## First Program in Rust

To honor our ancestors' rituals when learning a new programming language, we will first write a program in `Rust` that will print the magic `Hello World` string to a termial window.

```rust
// Example 1.1
// --------------------------------------- //
fn main() {
	println!("Hello World");
}
```

In above program notice `println!`. This is a macro, not a function call as it ends with **!**. We will learn about macros in later chapters.

## println! Macro

Macros in Rust are used in very similar sense as use macros in C/C++, but macros is Rust are more complex and advanced compared to C/C++. Basic definition of macro is Rust code writing Rust code. At compile time, Rust compiler will replace macro with actual instead of macro. In the following code example, we will look at various usages of println! macro. **Macro always ends with ! sign**.

```rust
fn main() {
  // Simple srting interpolation
  println!("{}, {}", "Hello", "World");
  
  // String interpolation with positional arguments
  println!("{1}, {0}", "Hello", "World");
  
  // String interpolation with named arguments
  println!("{greeting}, {name}", greeting="Hello", name="Jahan");
  
  // Single line debug output
  println!("{:?}", [1,2,3]);
  
  // Multiple line debug output
  println!("{:#?}", [1,2,3]);
}
```



## Prelude (skip if not interested)

If we compare Example 1.1 to equivalent `C` program. you can see that we do not have to import any library or module compared to `#include <stdio.h>` in `C`. Rust automatically imports most commonly used standard libraries into program's default scope and it is called `prelude`.

The current version of prelude lives in ```std::prelude::v1``` and it consists of the following

```
(1) std::marker::{Copy, Send, Sized, Sync}
		The marker traits indicate fundamental properties of types.

(2) std::ops::{Drop, Fn, FnMut, FnOnce}
		Various operations for both destructors and overloading ().

(3) std::mem::drop
		A convenience function for explicitly dropping a value.

(4) std::boxed::Box
		A way to allocate values on the heap.

(5) std::borrow::ToOwned
		The conversion trait that defines to_owned, the generic method for 					creating an owned type from a borrowed type.

(6) std::clone::Clone
    The ubiquitous trait that defines clone, the method for producing a copy 
    of a value.

(7) std::cmp::{PartialEq, PartialOrd, Eq, Ord }
		The comparison traits, which implement the comparison operators and are 
		often seen in trait bounds.

(8) std::convert::{AsRef, AsMut, Into, From}
		Generic conversions, used by savvy API authors to create overloaded 
		methods.

(9)std::default::Default
		Types that have default values.

(10) std::iter::{Iterator, Extend, IntoIterator, DoubleEndedIterator, ExactSizeIterator}. Iterators of various kinds.

(11) std::option::Option::{self, Some, None}. 
		A type which expresses the presence or absence of a value. This type is 
		so commonly used, its variants are also exported.

(12) std::result::Result::{self, Ok, Err}.
	A type for functions that may succeed or fail. Like Option, its variants 		are exported as well.

(13) std::slice::SliceConcatExt
		A trait that exists for technical reasons, but shouldn't have to exist. 		It provides a few useful methods on slices.

(14)std::string::{String, ToString},
		Heap allocated strings.

(15) std::vec::Vec
		A growable, heap-allocated vector.
```



## Comments 

In contrast to many other languages, Rust does not support multiline comments, `//` is used for single line comments, for multiline comments we can use multile single line comments. For documentation comments use `\\\`, and it supports markdown syntax as well.

## User Input & Output

To get user input, we need ```std::io``` library. Rust has a ```use``` keyword for importing libraries.

```rust
// Example 1.2
// --------------------------------------- //
use std::io;

fn main() {
    println!("Add two integers.");
    let mut x = String::new();
    let mut y = String::new();
    
    println!("Input first integer.");
    io::stdin().read_line(&mut x)
        .expect("Failed to read line.");
  
    println!("Input second integer.");
    io::stdin().read_line(&mut y)
        .expect("Failed to read line.");
        
    // Convert user inputs to integers, and if user input cannot be
  	// converted to integer because of wrong input program will crash.
    let x: u32 = x.trim().parse()
        .expect("Input an integer.");
    let y: u32 = y.trim().parse()
        .expect("Input an integer.");
    
    println!("Sum is {}.", x + y);
}
```

```io.stdin().read_line()``` method returns ```Result``` type. We will discuss ```Result``` type in detail, but for now, it's enough to know that it an `Enumeration` type and has `Ok` and `Err` values (enum's variant). `Ok` variant indicates that operation is successful and `Err` value indicates failure. Purpose of `Result` type is to encode error-handling information. Also in code example 1.2 we have used `expect` function of type `Result` and if value of `Result` is `Err` `expect` fucnction will crash the program.