# Using Structs To Create Custom Data Types

Rust allows us to create custom data types using structs and enums (we will discuss enums in chapter 06). Struct or Structure lets you package together and name multiple values into a meaningful group.

Structs also allow us to write object oriented programs in Rust. OOP nature of Rust will be discussed in a later chapter.

## Defining and Initiating Structs

Structs are somewhat similar to tuples, as you can group different data types into one entity, but unlike tuples, in structs we can name the data values and order of data does not matter. To create a structure, `struct` keyword is used. Data values for a struct are called fields.

```rust
//
// Code Sample 4.1
// ----------------------------------------
// This code samples demonstrate how to create custom types in Rust.

struct User {
  username: String,
  email: String,
  sign_in_count: u64,
  active: bool
}
```

The above code will create a new data type called User, and now we can instantiate objects from this data type as we can can create instances of class in an Object Oriented Languages.

```rust
//
// Code Sample 4.2
// ----------------------------------------
// Following code sample 4.1, here we are creating an instance of User type.

let user1 = User {
  email: 'jahannzeb.sayal@gmail.com',
  username: 'jSayal',
  active: false,
  sign_in_count: 112
};
```

When we create instances of structs, fields do not have to be in same order as defined in struct definition. Like all other variables, data type instances created from structs are also immutable. To create mutable struct custom data type we have to use the `mut` keyword. Individual fields of struct are selectively made mutable, the whole sruct instance will be mutable.

```rust
//
// Code Sample 4.3
// ----------------------------------------
// In this code, sample we are creating a mutable instance of User type.

let mut user1 = User {
  email: 'jahannzeb.sayal@gmail.com',
  username: 'jSayal',
  active: false,
  sign_in_count: 112
};

// Now we can change values for user1 using the . operator.

user1.email = String::from("jahanzeb@yahoo.com");
```

All the essential features of structs are explained in the following example. Read the comments carefully. Following code sample shows 

1. How to create custom types using structs?
2. What are tuple structs?
3. When returning struct from a function with same argument names as struct field names, we can use shorthand notation.
4. Creating new instances from existing instance using update syntax.

```rust
//
// Code Sample 4.4
// ----------------------------------------
//

struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool
}

struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let mut user1 = User {
        email: String::from("jahanzeb.sayal@gmail.com"),
        username: String::from("jSayal"),
        sign_in_count: 22,
        active: false
    };

    // Changing struct field value
    println!("Previous email is {}.", user1.email);
    user1.email = String::from("jahanzeb@yahoo.com");
    println!("New email is {}.", user1.email);

    // Using factory function build_user to create User
    let user2 = build_user(
        String::from("jSayal"),
        String::from("jahanzeb@live.com")
    );
    println!("User2 email is {}.", user2.email);

    // We can create new instances from existing instances, using update syntax
    // For user3 explicitly defined fields username and email will be used and
    // the rest of the fields will be taken from instance user2.
    let user3 = User {
        username: String::from("Eeshal"),
        email: String::from("eeshal@gmail.com"),
        ..user2
    };

    // Tuple structs
    // --------------------------------------------------------------------
    // They are just like tuples with a name, so that we can create its 
  	// instances. Tuple instances fields do not have any names.
    // Here Color and Point are two different types and one cannnot replace 
  	// other, even if there definition is same.
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
  
  	// Values can obtained from tuple struct as we get values from tuples.
  	println!("First value of black is {}.", black.0);
}

//
// Factory function to create User struct
//
fn build_user(username: String, email: String) -> User {
  	// --------------------------------------------------------------------
    // Using normal syntax to crate struct instance
    // --------------------------------------------------------------------
    // User {
    //     username: username,
    //     email: email,
    //     sign_in_count: 1,
    //     active: false
    // }
	
  	// --------------------------------------------------------------------
    // Using shorthand syntax to crate struct instance
    // --------------------------------------------------------------------
    // Above statement can also use shorthand notation, when the function
    // argument name is same as sruct field name.
    User {
        username,
        email,
        sign_in_count: 1,
        active: false
    }
}

```

## Comprehensive Example of struct

```rust
//
// Code Sample 4.5
// ----------------------------------------
// The following code sample demonstrate the following features of struct.
// 1. Implementing methods and associated functions for a struct.
// 2. Associated functions
// 3. Methods using mutable self to alter self.
// 4. Methods taking multiple arguments, including the same struct type.

#[derive(Debug)]
struct Rectangle {
    width: i32,
    height: i32
}

// Implementation block is used to define methods for struct and associated function.
// We can have multiple impl blocks for a struct.
impl Rectangle {
    // The following two functions below in this implementation block are called
    // methods, as they take &self as parameter.
    // In the following methods we are not taking ownership of self.
    fn calculate_area(&self) -> i32 {
        self.width * self.height
    }

    fn can_hold(&self, r: Rectangle) -> bool {
        self.width > r.width && self.height > r.height
    }

    // In the following method, we are using &mut self as the first argument,
    // it means that this method will change values for self. In this case
    // double_me() method is doubling the size of self and returns a new Rectangle.
    fn double_me(&mut self) -> Rectangle {
        Rectangle{ width: self.width * 2, height: self.height * 2}
    }

    // Associated Function
    // -------------------------------------------
    // Associated functions are defined in the struct impl block, but do not take &self 
  	// as parameter. Normally they are used as constructors that will return a new 
  	// instance of the struct.
    fn square(size: i32) -> Rectangle {
        Rectangle{ width: size, height: size}
    }
}

fn main() {
    let r1 = Rectangle{ width: 10, height: 10 };
    let r2 = Rectangle{ width: 5, height: 6};
    let mut r3 = Rectangle{ width: 3, height: 7};
    let sq = Rectangle::square(20);

    let sq2 = Rectangle::square(5);

    println!("Area of rectangle is {}.", r1.calculate_area());
    println!("Rect1 can hold Rect2 is {}.", r1.can_hold(r2));
    println!("Debug info for r1 is {:?}", r1);
    println!("Square info {:?}.", sq);
    println!("Area of square is {}.", sq.calculate_area());

    // ----
    println!("R3 before double_me {:?}, area is {}.", r3, r3.calculate_area());
    let r3 = r3.double_me();
    println!("R3 {:?}, area is {}.", r3, r3.calculate_area());
}
```

