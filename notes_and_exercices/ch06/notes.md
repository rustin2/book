# Chapter 6: Enums and Pattern Matching

## Enum Basics
```rust
// Simple enum
enum IpAddrKind {
    V4,
    V6,
}

// Enum with associated data
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}
let home = IpAddr::V4(127, 0, 0, 1);

// Methods on enums
impl Message {
    fn call(&self) { ... }
}
```

## Option Enum
```rust
enum Option<T> {
    Some(T),
    None,
}

let some_number = Some(5);
let absent_number: Option<i32> = None;
```
- **Option\<T\>** - Replaces null; represents presence/absence of value
- In prelude (no `Option::` prefix needed)

## Pattern Matching
```rust
// match (exhaustive)
match coin {
    Coin::Penny => 1,
    Coin::Nickel => 5,
    Coin::Dime => 10,
    Coin::Quarter(state) => {
        println!("State: {:?}", state);
        25
    },
}

// Catch-all patterns
match dice_roll {
    3 => fancy_hat(),
    7 => lose_hat(),
    other => move_player(other),  // bind value
    _ => (),                       // ignore value
}
```

## Concise Control Flow
```rust
// if let (single pattern, loses exhaustiveness)
if let Some(max) = config_max {
    println!("Max is {}", max);
}

// if let with else
if let Coin::Quarter(state) = coin {
    println!("State: {:?}", state);
} else {
    count += 1;
}

// let...else (extract or return early)
let Some(digit) = get_digit() else {
    return Err("expected digit");
};
```

## Key Concepts
- **Enum** - Type representing one of several possible variants (mutually exclusive states)
- **Variant** - One possible value of an enum (can hold different data types)
- **Pattern matching** - Control flow comparing values against patterns
- **Exhaustive matching** - Compiler ensures all variants are handled
- **if let** - Shorthand for single pattern match (less verbose, no exhaustiveness check)
- **let...else** - Extract value or execute else branch on mismatch

## Rust-Specific Mechanics

### No Null
- Rust eliminates null pointer errors
- Use `Option<T>` to explicitly represent absence
- Must handle `None` case before using value

### Type Safety with Option
- `Option<T>` and `T` are different types
- Can't use `Option<T>` where `T` expected
- Forces explicit null checking

### Match Arms Bind Values
- Extract data from enum variants: `Coin::Quarter(state) => { ... }`
- Bindings available in arm's code block

### Variants as Constructor Functions
- Enum variants automatically become functions
- `Some` and `None` are callable: `Some(5)`

### Exhaustive Matching Prevents Bugs
- Compiler error if any variant unhandled
- Catch-all `_` allows ignoring remaining cases
- Named catch-all (`other`) binds the value
