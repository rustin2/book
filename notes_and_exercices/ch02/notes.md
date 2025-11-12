# Chapter 2: Programming a Guessing Game

## Commands
- `cargo doc --open` - Build and open documentation for dependencies
- `cargo update` - Update crate versions

## Variable Declaration
- `let` - Declare an immutable variable
- `let mut` - Declare a mutable variable
- **Immutable by default** - Variables cannot change unless marked with `mut`

## Importing and Using Libraries
- `use std::io;` - Import I/O library from standard library
- `use rand::Rng;` - Import random number trait
- `use std::cmp::Ordering;` - Import comparison enum
- **Prelude** - Items from standard library automatically brought into scope
- **Crate** - Collection of Rust source code files (binary or library)

## Core Concepts

### String and Input
- **String** - Growable, UTF-8 encoded text type
- `String::new()` - Create a new empty String
- `io::stdin().read_line(&mut guess)` - Read user input into a string

### References
- **Reference** - Created with `&`, allows code to access data without copying
- **Mutable reference** - `&mut`, allows modifying borrowed data

### Functions and Methods
- **Associated function** - Function on a type, called with `::` (e.g., `String::new()`)
- **Method** - Function on an object, called with `.` (e.g., `.read_line()`)

### Error Handling
- **Result** - Enum with variants `Ok` and `Err` for error handling
- `.expect()` - Handle Result; crashes program with message if Err

### Type System
- **Type annotation** - Specify variable type with `:` (e.g., `let guess: u32`)
- `.parse()` - Convert string to another type
- **Shadowing** - Reuse variable name to convert types

### Pattern Matching
- `match` - Expression with multiple patterns (arms)
- **Pattern** - Condition to match against
- **Ordering enum** - Has variants `Less`, `Greater`, `Equal`
- `_` - Catch-all pattern matching any value

### Control Flow
- `loop` - Create an infinite loop
- `break` - Exit a loop
- `continue` - Skip to next iteration

## Syntax
- `::` - Associated function call
- `.method()` - Method call
- `{}` - Placeholder in `println!` for formatting
- **Semantic Versioning** - `0.8.5` means `^0.8.5` (>=0.8.5, <0.9.0)
