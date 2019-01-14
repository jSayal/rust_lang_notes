# Enums & Pattern Matching

Enums also called enumerations is the second way you can custom types. Enum allows you to define a type by enumerating it's possible values. Enums are a feature in many languages, but their capabilities differ in each language. Rust’s enums are most similar to *algebraic data types* in functional languages, such as F#, OCaml, and Haskell.

Basic difference between struct and enum is that enum type can only contain one out of all possible variants.

## Defining & Using Enums

To create an enum type we use `enum` keyword.

```rust
//
// Code Sample 5.1
//

enum IpAddr {
  V4,
  V6,
}

// IpAddr is now a custom data type that we can use in our code.

let four = IpAddr::V4;
let six = IpAddr::V6;
```

At this point, we have defined the IpAddr type. It can hold information about the kind of IP address, but not the ip address itself. One solution is to use `struct`.

```rust
//
// Code Sample 5.2
//

#[derive(Debug)]
enum IpAddrKind {
 	V4,
  V6,
}

#[derive(Debug)]
struct IpAddr {
  kind: IpAddrKind,
  address: String
}

fn main() {
  let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1")
  };

  println!("Home struct is {:?}.", home);
}
```

Rust also allows us to store data directly in enum type.

```rust
//
// Code Sample 5.3
//

#[derive(Debug)]
enum IpAddr {
  V4(u8, u8, u8, u8),
  V6(String),
}

fn main() {
  let home = IpAddr::V4(127, 0, 0, 1);
  let loopback = IpAddr::V6(String::from("::1"));
  
  println!("Home is {:?}.", home);
  println!("Loopback is {:?}.", loopback);
}
```

Like structs, enums can also have methods in `impl block`.

```rust
#![allow(dead_code)]
#[derive(Debug)]
enum Day {
  Mon, Tue, Wed, Thu, Fri, Sat, Sun
}

impl Day {
  fn is_weekday(&self) -> bool {
    match self {
      &Day::Sat | &Day::Sun => false,
      _ => true
    }
  }
}

fn main() {
  let d = Day::Sat;
  println!("d is a weekday; {}.", d.is_weekday());
}
```

## Pattern Matching 

Before we go into details of options, we should learn about match keyword, which provides a switch like control flow. Later in the code, we will use `match` to determine current value of enum.

### match keyword

Just like switch case in C, rust has match keyword to do the same job, but `match` offers lot of features.

```rust
//
// Code Sample 5.4
//

fn main() {
  let number = 1;

  match number {
    1 => {
      println!("One is default value");
      println!("This is how you can write multiline code.");
    },
    2|3|4|5 => println!("Value is between 2 and 5"),
    // Inclusive range, exclusive range is experimental feature as of 
    // now.
    6...8 => println!("Value is either 6 or 7"),
    9...12 => println!("Value is b/w 9 an 12"),
    _ => println!("No match occured")
  }
}

```

Basic use of match in context of enums is to go through all the possible choices of enum and match the currnent choice.

```rust
//
// Code Sample 5.5
//

#[allow(dead_code)]
#[derive(Debug)]
enum Days {
  Monday,
  Tuesday,
  Wednesday,
  Thursday,
  Friday,
  Saturday,
  Sunday
}

fn main() {
  let day_of_birth = Days::Tuesday;

  match day_of_birth {
    Days::Friday => println!("Birth day is Friday"),
    Days::Saturday => println!("Birth day is Saturday"),
    Days::Sunday => println!("Birth day is Sunday"),
    Days::Monday => println!("Birth day is Monday"),
    Days::Tuesday => println!("Birth day is Tuesday"),
    Days::Wednesday => println!("Birth day is Wednesday"),
    Days::Thursday => println!("Birth day is Thursday")
  }
}
```

## Option<T> Enum

Rust doest have any `null type`, but it does not mean that Rust cannot handle situations where value of a variable can be null. Intead of using null as a type, Rust uses `Option<T> enum`. Option<T> enum and its' variants `Some` and `None` are included in prelude.

```rust
//
// Code Sample 5.6
//

enum Option<T> {
  Some(T),
  None,
}
```

> <T> is basically generic type that we will discuss in later chapters. For now, <T> respresents any possible data type. It can be i8, i32, f64, String, &str etc. 

Basic use of Option variants.

```rust
//
// Code Sample 5.7
//

fn main() {
  let some_number = Some(5);

  match some_number {
    Some(n) => println!("some_number is not null and value is {}.", n),
    None => println!("some_number is null")
  }
}
```

Some of the methods in Rust's standard library returns Option<T> as return value.

```rust
//
// Code Sample 5.8
//

fn main() {
  let name = String::from("Jahan");
	
  // name.chars().nth() method return an Option enum.
  println!("Character at index 2 is {}.", match name.chars().nth(8) {
    Some(c) => c.to_string(),
    None => "No character at specified index".to_string()
  });
}
```

We can return Option<T> from our own functions.

```rust
//
// Code Sample 5.9
//

fn main() {
  println!("Occupation is {}", match get_occupation("Hina") {
    Some(o) => o,
    None => "No occupation found."
  });
}

fn get_occupation(name: &str) -> Option<&str> {
  match name {
    "Jahan" => Some("Software Engineer"),
    "Aslam" => Some("Doctor"),
    _ => None
  }
}
```

## Result<T, E>`

Result<T, E> is an enum that is used for returning or propagating errors. Variants of this enum are Ok(T) and Err(E). There are many methods in standard library that returns this enum. One example is parsing string to number. If an action is successful Ok(T) is the return value, if there is an error, the result is Err<E>.

```rust
fn main() {
  let guess_result = guess_number(30);
  
  match guess_result {
    Ok(num) => println!("Guess {} is correct.", num),
    Err(e) => println!("InCorrect Guess. {}.", e)
  }
}

fn guess_number(num: i32) -> Result<i32, String> {
    match num {
        1...10 => Ok(num),
        _ => Err(String::from("Number should be less than or equal to 10."))
    }
}
```

## IF LET Statement

Sometimes we are only interested in one particular result, and using match statement can be a bit verbose. `If let` statement can be used in these situations.

```rust
fn main() {
  let v = Some(0b10);

  if let Some(0b10) = v {
    println!("Value is 2");
  } else {
    println!("Value is not 2");
  }
}
```

Using If let with enums

```rust
#![allow(dead_code)]
enum Days {
  Mon, Tue, Wed, Thu, Fri, Sat, Sun
}

fn main() {
  let d = Days::Mon;

  if let Days::Mon = d {
    println!("It is Monday");
  } else {
    println!("It's not Monday");
  }
}
```

