# Rust LEARNING Resources

### Main Sources

1. From Rust Docs - [rustup docs](file:///Users/jahan/.rustup/toolchains/nightly-x86_64-apple-darwin/share/doc/rust/html/index.html)

### Other Resrouces for Learning

1. [YouTube Rust Tutorial](https://www.youtube.com/watch?v=vOMJlQ5B-M0&list=PLVvjrrRCBy2JSHf9tGxGKJ-bYAN_uDCUL)

### Code Snippets

To check type of a variable.

```rust
#![feature(core_intrinsics)]

fn print_type_of<T>(_: &T) {
    println!("{}", unsafe { std::intrinsics::type_name::<T>() });
}

fn main() {
    let num1 = Some(5);
    let num2 = Some(10);
  	let num3 = 12;

    print_type_of(&num1);
    print_type_of(&num2);
  	print_type_of(&num3);
}
```

