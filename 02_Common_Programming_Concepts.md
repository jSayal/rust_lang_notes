# Common Programming Concepts

In this chapter, we will cover programming concepts that applies to all programming languages in the context of Rust. If you are coming from C/C++ background read carefully as things are bit different in Rust land.

## Identifiers

A name of anything (variable, function, struct, etc) is called an identifier and can be made up of any non-empty ASCII string with few restrictions.

1. The first character is a letter and remaining characters are alphanumeric or _.
2. The first character is an _ and remaining characters are alphanumeric or _. Single character _ is not a valid identifier.
3. Any Rust language keyword cannot be an indentifier, unless it is a raw identifier.

### Raw Identifiers

Since Rust code can use C language libraries, and there may times when an identifier in C code is one of the keyword in Rust. For these special cases, Rust has raw identifiers, that allows us to use keyword as identifier. Only use raw identifier on need basis.

```rust
// Basic usage
let r#fn = "fn is a keyword, but now being used as a raw identifier";
println!("{}", r#fn);

// match is a keyword in Rust, and suppose we are using a C library that has
// a function named match. We call this function as follows.
r#match();
```

## Variables

Major difference between Rust and major other programming languages variables is that **all variables in Rust are immutable until decalred as mutable**.

```rust
let x = 5;		// Here x varibale is mutable and its value cannot be changed.
							// If we try to change it by reassignment, it will result in 
							// compilation error.

x = 6;				// Now allowed

// To make any variable mutable use mut keyword
let mut y = 5;	// Variable y is now mutable and reassignment is allowd.
y = 10;					// No compilation error.
```

At this time, you might be thinking that since all variable by default are immutable, they are constants, but it is not true. There are few differences between immutable variables and contents that we will discuss in next section.

### Difference between Variables and Constants

In Rust, we use `const` keyword to declare constants instead of `let` keyword that is used for variables. You cannot use `mut` keyword with constants, as constant are immutable by default. When declaring constants their data type must be annotated and value of constants can only be set to constant expressions, not the result of a function call or any other value that is computed at run time.

```rust
const MAX_PLAYERS: i32 = 100;	// constant declaration and assignment
```

Using capital letter and underscore as space for constants is a convention in Rust.

### Shadowing

As all variables are immutable by default, Rust provides us with shadowing facility to change immutable variable value. Shadowing allows to declare a variable with same name as we have declared before, but it transforms the variable to a new value and as soon as these transformations are completed, the variable becomes immutable again. In essence, when we use let keyword to shadow an existing variable, we are creating a new variable.

```rust
let x = 5;
println!("{}", x);

// Increment x
let x = x + 1;
println!("Value of x is {}.", x);

// Shadow x to a string type
let x = "Hello World";
println!("Value of x is {}", x);
```

## Data Types

Rust is a statically typed language, that means compiler should know the type of all variables at compile time. Rust compiler can also infer type from variable values and it's usage, but if we are casting values from one type to another and multiple data types are possible, we have to annotate the desired type, otherwise we will have a compilation error.

Every value is Rust must have a data type. Data types can be classified as scalar and compound.

### Scalar Data Types

Scalar types represent single values. Rust has four primary scalar types: integer, floating-point numbers, Booleans and characters.

#### Integers

Integers are numbers without the fractional part. Integers can signed and unsigned.

| Length  | Signed | Unsigned |
| :-----: | :----: | :------: |
|  8 bit  |   i8   |    u8    |
| 16 bit  |  i16   |   u16    |
| 32 bit  |  i32   |   u32    |
| 64 bit  |  i64   |   u64    |
| 128 bit |  i128  |   U128   |
|  arch   | isize  |   Size   |

Each signed variant can store numbers from `-2^(n - 1) to 2^(n - 1) - 1 inclusive`, where *n* is the number of bits that variant uses. So an `i8` can store numbers from `-2^7 to 2^(7 - 1)`, which equals -128 to 127. Unsigned variants can store numbers from `0 to 2^n - 1`, so a `u8` can store numbers from 0 to 2^8 - 1, which equals 0 to 255.

Additionally, the `isize` and `usize` types depend on the kind of computer your program is running on: 64 bits if you’re on a 64-bit architecture and 32 bits if you’re on a 32-bit architecture.

Integers in decimal literals can use _ as visual separator.

```rust
let x = 1_000_000;
```

Integer literals can be written using hex, octal and binary values using following prefixes.

| Number Literals                       | Example   |
| ------------------------------------- | --------- |
| Decimal                               | 1_000_234 |
| Hex                                   | 0xff      |
| Octal                                 | 0o77      |
| Binary                                | 0b111_000 |
| Byte (u8 only - characters data type) | b'A'      |

#### Integer Overflow

Let's say you have a u8 variable x with a value of 256. We know that max value is 255. Rust handles these overflows differently in debug and release mode.

In debug mode, it wall cause panic - means your program will exit with an error.

In release mode, it will do **"two's complement wrapping"**, means 256 will be 0 and 257 will become 1, this behavior is same as C.

#### Floating-Point Type

Rust has two primitive types for floating type number `f32` and `f64` which are 32 bits and 64 bits in size respectively. The default type is `f64` as on modern CPUs speed is same as `f32` but it provides more precision.

Floating-point numbers are represented according to the IEEE-754 standard. The `f32` type is a single-precision float (6 digits), and `f64` has double precision (15 digits).

```rust
fn main() {
  let x = 21.5; 			// f64
  let y: f32 = 11.3;	// f32 (explicit annotation is required)
}
```

#### Booleans

Booleans are one byte in size. `bool` keyword is used declaring variables of type bool and it can have values of `true` or `false`.

```rust
fn main() {
  let x = true;
  
  let y: bool = false;
}
```

#### Character Type

Rust’s `char` type represents a Unicode Scalar Value, which means it can represent a lot more than just ASCII. Accented letters; Chinese, Japanese, and Korean characters; emoji; and zero-width spaces are all valid `char` values in Rust. Unicode Scalar Values range from `U+0000` to `U+D7FF` and `U+E000`to `U+10FFFF` inclusive. However, a “character” isn’t really a concept in Unicode, so your human intuition for what a “character” is may not match up with what a `char` is in Rust. We will discuss this in more details, when we get to Strings chapter.

```rust
fn main() {
  let x = 'A'; // char type is represented by single quote
  println!("Value of x is {}", x);
  
  // All char tricks that we use in C/C++ does not work in Rust.
  // This program will not compile
  let x = x + 1;
  println!("New value of x is {}", x);
}
```

### Compound Data Types

Compound data types can group multiple values into type. Rust has two primitive compound types tuples and arrays.

##### Tuples

Tuple is general way of grouping together some number of values of different data types. Tuple has a fixed size, once declared it cannot grow or shrink.

To get values from a tuple, we can either use `destructuring` or `.` operator to get values by index. Tuples starts with index 0.

```rust
fn main() {
  let tup: (i32, f64, u8) = (500, 21.45, 12);
  let (x, y, z) = tup;
  println!("Values of x, y and z are {}, {}, {}", x, y, z);
  
  println!("First value of tuple is {}.", tup.0);
}
```

##### Arrays

Array data type also group multiple scalar values like tuples, but the data type of all values must be same. Arrays have fixed length.

```rust
fn main() {
  // Array literals
  let arr1 = [1, 2, 3];
  // arr1[0] = 100; // will result in compile error
  println!("{:?}", arr1);

  // Mutable array
  // To change array elements, make array mutable
  let mut arr2 = [0, 2, 3];
  arr2[0] = 1;
  println!("{:?}", arr2);

  // Annotated array
  let arr3: [i32; 3] = [100, 200, 300];
  println!("{:#?}", arr3);

  // Initialize array with default values
  // Here we are decalring arr4 of width 5 with all values initialized to 0
  let arr4 = [0; 5];
  println!("{:#?}", arr4);
}
```

nRust arrays have a safety feature regarding it's index. In C if access values outside array index, we get whatever value is in memory at that location. Rust compiler will not produce any compile time errors for array out of bound index, but will cause run time panic.

## Functions

Rust uses `fn` keyword to define functions. Most of the functionality of functions are very similar to other programming languages. Rust functions are composed of statements and expressions. One important point to note here is statements do no return any value but expressions do.

```rust
fn main() {
  // Calling function with no return value
  show_greetings("Eeshal");

  // Calling function with return value
  println!("Sum of 2 and 2 is {}", add(2, 2));
}

// Simple function example - no return value
fn show_greetings(name: &str) {
  println!("Hello, {}", name);
}

// Function that return value, must specify the type of return value
fn add(num1: i32, num2: i32) -> i32 {
  // return num1 + num2;

  // We can also just write the expression and
  // the value of expression will be returned 
  // as result of function execution. 
  // NOTE: there is no semicolon
  num1 + num2
}
```

## Control Flow

### Conditionals

if statement in Rust works just like `C ` language, and it also `else if` and `else` clauses as well.

```rust
fn main() {
  let number = 5;

  if number == 5 {
    println!("Number is equals to 5.");
  } else if number > 5 {
    println!("Number is greater than 5");
  } else {
    println!("Number is less than 5.");
  }
}
```

if statement can also be used with let.

```rust
fn main() {
    let condition = false;
  
  	// Conditional assignment
    let x = if condition {
        5
    } else {
        6
    };
    
    println!("Value of x is {}.", x);
}
```

### Loops

There are three looping constructs in Rust, `loop, while & for`. Rust also provides us with a range function that helps with for loop.

```rust
// Using simple loop statement will result in infinite loop.
fn main() {
  loop {
		println!("This is an infinite loop. Ctrl + C to quit.");
  };
}
```

We can use `continue & break` statements to control flow of `loop`.

```rust
fn main() {
  let mut counter = 1;
  loop {
    if counter >= 20 {
      break;
    } else if counter % 2 == 0 {
      println!("{} is an even number", counter);
      counter = counter + 1;
    } else {
      counter = counter + 1;
      continue;
    }
  };
}
```

`loop` can be used for retrying tasks that you know can fail, e.g taking input from user or checking if a thread has completed it's job. When you break the loop you can pass return value to break statement.

```rust
fn main() {
  let mut counter = 0;
  
  let result = loop {
    counter += 1;
    if counter == 10 {
      break counter * 2;
    }
  };
  
  println!("Value of result is: {}", result);
}
```

`while` loop can be used to execute set of instructions, until. a condition holds true.

```rust
fn main() {
  let mut num = 3;
  while num != 0 {
    println!("")
  }
}
```

#### Looping over collections

To loop over collections like arrays we can use while and for loop, for loop is preferred.

```rust
// Using while loop to iterate over array
fn main() {
  let arr = [1, 2, 3, 4, 5];
  let mut index = 0;

  while index < 5 {
    println!("{} is at index {}.", arr[index], index);
    index += 1;
  }
}
```

In above example, if index is incorrect program will panic.

```rust
// Using iter function will prevent out of bound index error.
fn main() {
  let arr = [1, 2, 3, 4, 5];
  for el in arr.iter() {
    println!("The value is {}.", el);
  }
}
```

Rust provides us with a range type that will generate number from first number to second number - 1.

```rust
fn main() {
  for num in 1..4 {
    println!("{}", num);
  }
}
```

We can also reverse values in range using `rev()` function.

```rust
fn main() {
  for num in (1..4).rev() {
    println!("{}", num);
  }
}
```

