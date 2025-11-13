# Chapter 9: Error Handling

## Unrecoverable Errors (panic!)
```rust
panic!("crash and burn");

// Automatic panic
let v = vec![1, 2, 3];
let invalid = &v[100];  // panics: index out of bounds
```
- `RUST_BACKTRACE=1` - Environment variable to see full call stack

## Recoverable Errors (Result)
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}

// Match on Result
use std::fs::File;
let file = match File::open("hello.txt") {
    Ok(file) => file,
    Err(error) => panic!("Problem: {:?}", error),
};

// Match on error kind
match File::open("hello.txt") {
    Ok(file) => file,
    Err(error) => match error.kind() {
        io::ErrorKind::NotFound => File::create("hello.txt").unwrap(),
        other_error => panic!("Problem: {:?}", other_error),
    },
}
```

## Shortcuts
```rust
// unwrap - panic on Err
let file = File::open("hello.txt").unwrap();

// expect - panic with custom message
let file = File::open("hello.txt").expect("Failed to open hello.txt");
```

## Propagating Errors
```rust
// Manual propagation
fn read_username() -> Result<String, io::Error> {
    let file = match File::open("hello.txt") {
        Ok(file) => file,
        Err(e) => return Err(e),
    };
    // ...
}

// ? operator (shortcut for propagation)
fn read_username() -> Result<String, io::Error> {
    let mut username = String::new();
    File::open("hello.txt")?.read_to_string(&mut username)?;
    Ok(username)
}

// ? with Option
fn last_char(text: &str) -> Option<char> {
    text.lines().next()?.chars().last()
}

// main returning Result
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let file = File::open("hello.txt")?;
    Ok(())
}
```

## When to panic vs Result

### Use panic! when:
- Prototype/example code (unwrap/expect acceptable)
- Tests (want failure to fail the test)
- Impossible situations (unreachable code logically)
- Contract violations (caller broke assumptions)
- Invalid state that could cause security/safety issues

### Use Result when:
- Failure is expected (file not found, parse error)
- Caller can meaningfully handle error
- Library code (let caller decide recovery strategy)

## Custom Validation
```rust
pub struct Guess {
    value: i32,
}

impl Guess {
    pub fn new(value: i32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess must be 1-100, got {}", value);
        }
        Guess { value }
    }

    pub fn value(&self) -> i32 {
        self.value  // getter ensures valid value
    }
}
```

## Key Concepts
- **Recoverable error** - Can handle meaningfully (use `Result`)
- **Unrecoverable error** - Program in invalid state (use `panic!`)
- **panic!** - Stop execution, unwind stack, exit
- **Result\<T, E\>** - Enum encoding success (`Ok`) or failure (`Err`)
- **Error propagation** - Return error to caller instead of handling locally
- **? operator** - Return `Err` early or unwrap `Ok` value
- **Bad state** - Broken invariants/contracts/assumptions

## Rust-Specific Mechanics

### Exhaustive Error Handling
- Compiler forces acknowledgment of all errors
- `Result` must be used (compiler warns if ignored)
- Prevents silent failures

### Type System Prevents Mistakes
- `Result<T, E>` documents that operation can fail
- Can't use `Result<T, E>` where `T` expected
- Forces explicit handling or propagation

### ? Operator Requirements
- Only works in functions returning `Result`, `Option`, or `FromResidual` types
- Return type must match or be convertible
- `main` can return `Result<(), Box<dyn Error>>`

### Error Type Conversion
- `?` automatically calls `From` trait to convert error types
- Enables custom error types while propagating library errors
- Simplifies error handling in complex applications

### panic! Behavior
- **Debug mode**: Unwinds stack (cleans up memory)
- **With `panic = 'abort'`**: Terminates immediately (smaller binary)
- Panics across FFI boundaries are undefined behavior

### Type-Driven Validation
- Use type system to encode valid states
- Example: `Guess` type only exists if value in valid range
- Pushes runtime checks to compile-time where possible
- Getters provide safe access without exposing internals
