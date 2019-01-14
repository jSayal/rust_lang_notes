# Understanding Ownership

Ownership is Rust's most important and unique feature that enables Rust to guaranteee memory safety without needing a **garbage collector**. Due to Ownership feature, Rust handles memory layout differently from other languages, therefore it essential to understand rules of ownership and borrowing to write effective Rust programs. In addition, **slicing** collections also plays an impotant role while working with strings or any other kind of collection object. In this chapter, we will briefly discuss fundamentals of **slicing** and in later chapters we will expand on slicing with any collection type in Rust.

## What is Ownership?

All programs have to manage computer memory. Two most common approaches are 

1. Let the programmer manage the memory e.g C/C++ uses this approach.
2. Use garbage collector, that constantly check for unused memory and free it. Java, C#, Python, Javascript etc uses this approach. Garbage collection has its toll on program's performance.

Rust offeres a third way of managing memory using the concept of Ownership. It's simple to understand, but it is unique in some ways and sometimes it's tricky. Luckily, <u>Rust establishes very strict rules for Ownership and as long as you understand the applicable rules in context, you will have no trouble understanding Ownership.</u>

### Basic Rules of Owership

- Each variable in Rust has a variable that's called it's owner.
- There can only be one owner.
- When the owner goes out of scope, the value is dropped by Rust.

### Variable Scope

Scope is the range within the program, for which an item is valid. Rust uses **Lexical Scoping** and  `{ }` as scope delimiter.

```rust
//
// Code Sample 3.1
// ----------------------------------------
// This code sample demonstrate lexical scoping rules.
//

fn main() {
  let mut var1 = 20;
  {
    let mut var2 = 30;			// var1 is in scope
  }
  
  // var2 is out of scope.
}
```

### Fundamentals of Ownership

To demonstate rules of ownership, we will use `String` data type which is more complex compared to primitive data types that we have discussed before. A crucial point here is to understand when Rust will store values on stack and when heap memory is used.

#### Stack & Heap

Both stack and heap and parts of memory that are available to a program during runtime, and both of these are structured differently. Stack uses Last in First Out **(LIFO)** approach, means the last value stored on stack is retrieved first and then second last and so on.

Stack is fast, because during execution of program we do not have to serach for memory addresses as only last value on stack is retrievable and all data on stack is of fixed size e.g `i8` is stored on stack as it will always take 4 bytes, on the other hand String data type memory size depends upon the string stored.

Data with unknown size at compile time or when size of data can change, it is stored on heap. Heap is less organized compared to stack. When a program wants to store data on heap, it will ask operating system(OS) for memory. OS will find appropriate size of memory and mark it as being used and return pointer to it's location. Accessing data on heap is slow compared to stack.

Rust provides us two string types. One is string literal, and second is String type. String literals are immutable fixed and hard coded into binary, and String type is stored on heap and can grow or shrink in size.

```rust
//
// Code Sample 3.2
// ----------------------------------------
// This code sample demonstrate how to declare a string literal and create a String 
// type from a string literal.
//

// String literal
let str_literal = "Hello World";

// String literal can be converted into String type using from function of String type.
let s = String::from(string_literal);
println!("{}.", s);
```

`::` is an operator that allows to namespace functions from types and used for type's associated functions. We will discuss associated functions in Chapter 04 - Structs.

Let's see how ownership works.

```rust
//
// Code Sample 3.3
// ----------------------------------------
// This code sample demonstrate simplest case of data ownership and memory management.
//

{
  let s = String::from("Hello");
  
  // do something with s
}																// This scope is now over and s is no longer
																// valid.
```

When variable goes out of scope, Rust calls a special function `drop` that will return the memory to operating system.

#### Copy & Drop Types

In other languages like C/C++/C#/Java, data types are normally divided into two broad categories i.e primitive and custom. Primitive types includes integers and floating point numbers, characters and sometimes booleans. Custom data types are created by using primitive data types. When passing values to functions or methods, primitive types are passed by value means their copy is created and passed to function or method. Custom data types are passed by reference, means pointer to their memory location is passed instead of deep cloning the custom type values in memory.

Rust does not follow the above mentioned approach. Rust uses the concept of **Copy Types** and **Drop Types**. Copy types implement <u>copy trait</u> and drop types implement <u>drop trait</u>. If a type or any part of that type (in case of custom types) has implemented a drop trait, Rust won't let us implement copy trait for that type.

> For now, think of **trait** as an **Interface** in Java or C# with some differences. We will the differences when we will dicuss traits in detail.

Following are the `Copy` types in Rust.

- All integer (i8, i16 etc.), floating point (f32 and f64), bool and character types.
- Tuples - only if all values in a tuple are copy types.

Primitive values like i8, i32 etc are value types. Value of variables are binded to variables.

```rust
//
// Code Sample 3.4
// ----------------------------------------
// This code sample demonstrate the functionality of Copy trait.
//

let x = 5;
let mut y = x;  // Here a copy of x is made and binded to y.

// If we change the value of y, x will retain it's orignal value.
y = 20;
println!("Value of x is {}, and value of y is {}.", x, y);
```

Types that implement `drop` trait does work in this way. We will take example of String type.

```rust
//
// Code Sample 3.5
// ----------------------------------------
// This code sample demonstate move operation of drop type in Rust.
//

let s1 = String::from("Hello");
let s2 = s1;
```

In above s1 and s2 refer to same place in memory. String type is made up of three parts,

1. Pointer to memory location.
2. Length
3. Capacity

When let `s2 = s1` is executed, only pointer from s1 is copied to s2. Contents of s1 on heap are not cloned and stored again as it will be very expensive operation.

Another impotant point to note here is that when `let s2 = s1;` is executed, s1 is longer valid and when s1 and s2 goes out of scope we will not have **double free memory error**.

> **Double free memory error** means that unnallocating same location in memory more than once. In code sample 3.5, when variables s1 and s2 goes out of scope, Rust will drop memory for s1 and s2. As they are drop types and refer to same place in memory, unallocating twice will result in error. Rust handles this situation by using Ownership rules (discussed later in this chapter). So when one drop types is assigned to another drop type (or moved - in terms of Rust), the assinged type must become invalid or we will have the double free memory error.

```rust
//
// Code Sample 3.6
// ----------------------------------------
// WARNING: This program will not compile.
// This code sample demonstrate the move operation of drop types and
// * memory safety * feature of Rust language.
//

fn main() {
  let s1 = String::from("Hello");
  let s2 = s1;	// This is called move operation

  println!("s1 is {}, and s2 is {}.", s1, s2);	// s1 is invalid.
}
```

Using clone function for deep copy.

> String type has implemented clone function. For custom types, we have to code the clone function ourselves.

```rust
//
// Code Sample 3.7
// ----------------------------------------
// This code sample demonstrate the use of clone function of String type.
// Clone functions performs deep copy operation - means it will create a new copy 
// of data stored on heap memory and allocate it to a new variable. Copying pointer
// value is shallow copy.

fn main() {
  let s1 = String::from("Hello");
  let s2 = s1.clone();			// Deep copy. Here s1 will remain valid as it is not a move
  													// move operation.

  println!("s1 is {}, and s2 is {}.", s1, s2);
}
```

When clone function is used, Rust will make deep copy of s1, and store it in a separate memory space. Rust always make shallow copy (copying pointer value), unless explicitly asked to clone value of drop types.

#### Ownership and Functions

Semantics of passing values to function are same as assigning values.

```rust
//
// Code Sample 3.8
// ----------------------------------------
// This code sample demonstrate data ownership, as values are passed to funciton.
// For copy types, data ownership is not transferred to functionn argument.
// For drop types, by default data ownership is transferred to function argument unless
// reference to drop is passed. Referencing drop types are discussed in next section.

fn main() {
  let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here
  																	// After this functionn call, s cannot
  																	// be used, as it becomes invalid.

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it’s okay to
  																	// still use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, 
	// nothing special happens.


fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.


fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

After we pass a drop type to a function, we might need it's value in the functionn later. For this purpose we need to take ownership back from function.

```rust
//
// Code Sample 3.9
// ----------------------------------------
// This code sample demonstrate the transfer of data during function call and also 
// show how to get ownership back after function call.
//

fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into takes_and_gives_back
                                        // function argument, after function execution
                                        // a new String type is returned and moved into
  																			// s3.
  
} // Here, s3 goes out of scope and is dropped. s2 goes out of scope but was
  // moved, so nothing happens. s1 goes out of scope and is dropped.


fn gives_ownership() -> String {             // gives_ownership will move 
  																					 // its return value into the 
  																					 // function that calls it

    let some_string = String::from("hello"); // some_string comes into scope

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function
  
  // Note: Here drop is not called for some_string as value is assinged
  // to a variable (s1).
}


// takes_and_gives_back will take a String and returns it to transfer ownership
// of data.
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

    a_string  // a_string is returned and moves out to the calling function
}
```

By returning a tuple, we can return multiple values from a function. This will make it easy to return result of function call and transfer back ownership of data in a single statement. Function callee can unpack the returned tuple to get function result and data ownership.

```rust
//
// Code Sample 3.10
// ----------------------------------------
// This code sample demonstrates returning multiple values from a function using tuple.
//

fn main() {
  let s1 = String::from("Hello"); // Ownership is passed to calculate_length
  																// function.
  
  // Here, we get ownership back in s2, and now we can use s2, but s1 is
  // still invalid.
  let (s2, len) = calculate_length(s1);
  println!("{} length is {}.", s2, len);
}


fn calculate_length(s: String) -> (String, usize) {
  let length = s.len();
  (s, length)
}
```

### References and Borrowing

Returning ownership of data all the times is a bit of hassle and can also result in compilation errors. Fortunately, Rust allows us to create a refernce to drop types using `&` operator. When drop type values are passed to a function using a reference, it is called **Borrowing**. By default, all borrowed values are immutable that means a function cannot change borrowed reference data value.

> Referenced values can be dereferenced using `*` operator.

```rust
//
// Code Sample 3.11
// ----------------------------------------
// In this code sample, we are refactoring code sample 3.10.
// Instead of transferring ownership of s1 and then taking it back, we are using 
// borrowing value in calculate_length function and returning the result.

fn main() {
  let s1 = String::from("Hello");
  let len = calculate_length(&s1);

  println!("Length of {} is {}.", s1, len);
}

// Here calculate_length function is borrowing value for temporary use
// and does not take ownership of variable s.
// Borrowed value cannot be changed, unless we have a mutable reference.
fn calculate_length(s: &String) -> usize {
  s.len()
}
```

First look at the function signature `fn calculate_length(s: &String) -> usize` instead of taking String as an argument, now we are takingn `&String` as an argument. &String is a reference to String type. References allows us to access to a value without taking ownership. We call having references as function parameters ***borrowing***. 

#### Mutable References

Mutable references allows us to change value that is borrowed. While creating mutable references keep in mind that you can only have one mutable reference to a variable in a particular scope, and unlimited immutable references.

```rust
//
// Code Sample 3.12
// ----------------------------------------
// As discussed earlier all references are immutable by default. In this code sample we 
// are creating a mutable reference that allows the function to change borrowed value.

fn main() {
  let mut s1 = String::from("Hello");
  change(&mut s1); // Mutable reference can only be created when variable is mutable.

  println!("New value of s1 is {}.", s1);
}

// Function change takes mutable reference, allowing us to change borrowed value.
fn change(s: &mut String) {
  s.push_str(", World");
}
```

At any point in time, there can only be one mutable reference. In very simple words, **if you have created one mutable reference, you cannot create another <u>mutable or immutable reference again</u>**.

```rust
//
// Code Sample 3.13
// ----------------------------------------
// This code sample demonstrate the first rule of ownership for references.
// If a mutable reference has been created, you cannot create any mutable or
// immutable reference again.
//

fn main() {
  let mut s = String::from("Hello World");

  // Creating mutable references.
  // Since we have two mutable references, s1 andn s2. This program will
  // not compile.
  let s1 = &mut s;
  let s2 = &mut s;
}
```

If no mutable reference has been created, you can create multiple immutable references.

```rust
//
// Code Sample 3.14
// ----------------------------------------
// This code sample demonstrate the second rule of reference ownership.
// If no mutable reference has been created, you can create multiple immutable
// references.

fn main() {
  let s = String::from("Hello World");

  // Creating multiple immutable references. This program will compile successfully.
  let s1 = &s;
  let s2 = &s;

  println!("Value of s1 is {}, and s2 is {}.", s1, s2);
}
```

#### Dangling Pointers

In languages with pointers, it’s easy to erroneously create a *dangling pointer*, a pointer that references a location in memory that may have been given to someone else, by freeing some memory while preserving a pointer to that memory. In Rust, by contrast, the compiler guarantees that references will never be dangling references: if you have a reference to some data, the compiler will ensure that the data will not go out of scope before the reference to the data does.

```rust
//
// Code Sample 3.15
// ----------------------------------------
// WARNING: The following program will not compile.
// This code sample demonstrate the Rust safety feature - guarantee that 
// there can never be dangling pointer.

fn main() {
  let ref_to_nothing = dangle();
}

// In this function we are returning a reference (pointer) to s1.
// s1 is not valid as long as it goes out of scope and return value
// &s1 will reference nothing. In this case, Rust compiler will not
// compile this code and prevent us from creating a dangling pointer.
fn dangle() -> &String {
  let s1 = String::from("Hello");
  &s1
}
```

#### Rules of Ownership

- At any point, we can have one mutable reference **OR** any number of immutable references.
- References must always be valid.

```rust
//
// Code Sample 3.16
// ----------------------------------------
// This code sample demonstrate Rust's ownership rules.

fn main() {
  let mut s = String::from("Hello World");

  let s1 = &s;        // Creating immutable reference.
  let s2 = &mut s;    // Creating a mutable reference. Compilation error!

  println!("Program execution successful. {}, {}", s1, s2);
}
```

## The Slice Type

Slice types enables enables us to refer to contiguous sequence of elements in a collection, rather than the whole collection. Slice type does have ownership of data.

As we have been discussing strings in this chapter. We will continue with string slices. String slice type is written as `&str`.

```rust
//
// Code Sample 3.17
// ----------------------------------------
// The following code sample demonstrate various ways of creating String slice type.

fn main() {
  let s = String::from("Hello World");
  
  // Creating slices
  // End index is excluded
  let hello = &s[0..5];
  
  // If we want to include the end index, use ..=
  let hello2 = &s[0..=4];
  
  // If we omit the start or end index, start index will be 0 and end index
  // will be the end index of string
  
  let hello3 = &s[..5];
  
  // If we omit both start and end index, whole string will be a slice
  let whole_string = &s[..];
  
  println!("{}, {}, {}, {}", hello, hello2, hello3, whole_string);
}
```

Earlier in the chapter, we have seen that Rust provides us with two types of strings, one is string literal and other is String type. String Literals and string slices. 

```rust
//
// Code Sample 3.18
// ----------------------------------------
// This code sample demonstrate the use of slice type.
// when variable s will be out of scope, all the references to it will also not exist.
// It is the same safety feature that we have seen in the dangling pointers section
// above.

fn main() {
    let s = String::from("Hello World");
    let sentence = "This is a wonderful world.";    // String literal
  
  	let s_first_word = first_word(&s[..]);
  	let sentence_first_word = first_word(sentence);

    println!("First word is {}.", s_first_word);

    // The two statements below are same, because string literal is a string 
  	// slice.
    println!("First word is {}", sentence_first_word);
    println!("First word is {}", sentence_first_word);
}

fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();

    for (i, &item ) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[..i];
        }
    }

    &s[..]
}
```

In addition to string slices we can have slices of other types as well.

```rust
//
// Code Sample 3.19
// ----------------------------------------
// In this code sample, we have an integer array. We have created a slice of type
// &[i32] from it. We will use slices in the collections chapter. But for now just
// keep in mind that slice type can be made out of any collection type.

fn main() {
  let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0];

  let arr_slice = &arr[..3];		// slice type is &[i32]
  println!("{}", arr_slice[0]);
}
```

