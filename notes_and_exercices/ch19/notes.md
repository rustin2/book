# Chapter 19: Patterns and Matching

## Pattern Locations
```rust
// match expression
match value {
    Some(x) => x,
    None => 0,
}

// if let
if let Some(x) = option_value {
    println!("{}", x);
}

// while let
while let Some(top) = stack.pop() {
    println!("{}", top);
}

// for loop
for (index, value) in v.iter().enumerate() {
    println!("{} at {}", value, index);
}

// let statement
let (x, y, z) = (1, 2, 3);

// function parameters
fn print_coordinates(&(x, y): &(i32, i32)) {
    println!("({}, {})", x, y);
}
```

## Refutable vs Irrefutable
```rust
// Irrefutable (always match) - used in let, for, function params
let x = 5;
let (a, b) = (1, 2);

// Refutable (might not match) - used in if let, while let
if let Some(x) = option_value { /* ... */ }

// Compiler error: refutable pattern in let
let Some(x) = option_value;  // ERROR
```

## Pattern Syntax

### Literals
```rust
match x {
    1 => println!("one"),
    2 => println!("two"),
    _ => println!("other"),
}
```

### Named Variables
```rust
let x = Some(5);
match x {
    Some(50) => println!("Got 50"),
    Some(y) => println!("Matched, y = {:?}", y),  // y shadows outer scope
    _ => println!("Default"),
}
```

### Multiple Patterns (OR)
```rust
match x {
    1 | 2 => println!("one or two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```

### Range Patterns
```rust
match x {
    1..=5 => println!("one through five"),
    _ => println!("something else"),
}

// Character ranges
match ch {
    'a'..='z' => println!("lowercase"),
    'A'..='Z' => println!("uppercase"),
    _ => println!("other"),
}
```

### Destructuring

#### Structs
```rust
struct Point { x: i32, y: i32 }
let p = Point { x: 0, y: 7 };

// Full match
let Point { x, y } = p;

// Shorthand (field name = variable name)
let Point { x: a, y: b } = p;

// Partial match
match p {
    Point { x, y: 0 } => println!("On x axis at {}", x),
    Point { x: 0, y } => println!("On y axis at {}", y),
    Point { x, y } => println!("On neither axis: ({}, {})", x, y),
}
```

#### Enums
```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
}

match msg {
    Message::Quit => println!("Quit"),
    Message::Move { x, y } => println!("Move to {}, {}", x, y),
    Message::Write(text) => println!("Text: {}", text),
}
```

#### Nested Destructuring
```rust
match color {
    Color::Rgb(r, g, b) => println!("RGB({}, {}, {})", r, g, b),
    Color::Hsv { h, s, v } => println!("HSV({}, {}, {})", h, s, v),
}
```

### Ignoring Values

#### Underscore
```rust
// Ignore entire value
let _ = some_value;

// Ignore parts
let (x, _, z) = (1, 2, 3);

// Ignore in match
match x {
    Some(_) => println!("got a Some"),
    None => println!("got None"),
}
```

#### Remaining Parts
```rust
struct Point3d { x: i32, y: i32, z: i32 }
let origin = Point3d { x: 0, y: 0, z: 0 };

// Ignore remaining fields
match origin {
    Point3d { x, .. } => println!("x is {}", x),
}

// Ignore remaining elements
match numbers {
    (first, .., last) => println!("{} to {}", first, last),
}
```

### Match Guards
```rust
match num {
    Some(x) if x < 5 => println!("less than five: {}", x),
    Some(x) => println!("{}", x),
    None => (),
}

// With OR patterns
match x {
    4 | 5 | 6 if y => println!("yes"),  // guard applies to all
    _ => println!("no"),
}
```

### @ Bindings
```rust
match msg {
    Message::Hello { id: id_variable @ 3..=7 } => {
        println!("Found id in range: {}", id_variable)
    },
    Message::Hello { id: 10..=12 } => {
        println!("Found id in another range")
    },
    Message::Hello { id } => {
        println!("Some other id: {}", id)
    },
}
```

## Key Concepts
- **Pattern** - Special syntax describing data structure shape
- **Refutable** - Pattern that might not match (e.g., `Some(x)`)
- **Irrefutable** - Pattern that always matches (e.g., `x`)
- **Destructuring** - Extract values from complex data types
- **Match guard** - Additional `if` condition in match arm
- **@ binding** - Bind value while also testing pattern

## Rust-Specific Mechanics

### Exhaustiveness Checking
- `match` must cover all cases
- Use `_` for catch-all
- Compiler error if case missing

### Pattern Matching Power
- More than equality checks
- Extracts and binds values simultaneously
- Compiler-verified correctness

### Irrefutable Pattern Requirements
- `let`, `for`, function params need irrefutable patterns
- Ensures code always executes
- Use `if let` or `while let` for refutable patterns

### Shadowing in Patterns
- Pattern variables shadow outer scope
- Only within match arm scope
- Different from regular shadowing

### Performance
- Pattern matching is zero-cost
- Compiles to efficient branching code
- No runtime overhead vs manual checks
