# Chapter 13: Functional Language Features - Iterators and Closures

## Closures
```rust
// Basic closure
let add_one = |x| x + 1;

// With type annotations (optional)
let add_one = |x: i32| -> i32 { x + 1 };

// Multi-line closure
let expensive_closure = |num| {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};

// Capturing environment
let x = 4;
let equal_to_x = |z| z == x;  // captures x
```

## Closure Types (Fn Traits)
```rust
// FnOnce - consumes captured variables (can only call once)
// FnMut - mutates captured variables
// Fn - borrows captured variables immutably

fn apply_fn<F>(f: F) where F: Fn(i32) -> i32 { ... }
fn apply_fn_once<F>(f: F) where F: FnOnce() { ... }
fn apply_fn_mut<F>(mut f: F) where F: FnMut() { ... }
```

## Iterators
```rust
// Create iterators
let v = vec![1, 2, 3];
let iter = v.iter();        // borrows immutably
let iter = v.iter_mut();    // borrows mutably
let iter = v.into_iter();   // takes ownership

// Iterator trait
pub trait Iterator {
    type Item;
    fn next(&mut self) -> Option<Self::Item>;
}
```

## Iterator Adapters (Lazy)
```rust
// Transform elements
let v2: Vec<_> = v.iter().map(|x| x + 1).collect();

// Filter elements
let filtered: Vec<_> = v.iter().filter(|x| **x > 2).collect();

// Chain iterators
let combined = v1.iter().chain(v2.iter());

// Take first n elements
let first_two: Vec<_> = v.iter().take(2).collect();

// Enumerate (index, value)
for (i, v) in v.iter().enumerate() { ... }

// Zip two iterators
let pairs: Vec<_> = v1.iter().zip(v2.iter()).collect();
```

## Consuming Adapters
```rust
// Sum all elements
let total: i32 = v.iter().sum();

// Collect into collection
let v2: Vec<i32> = v.iter().map(|x| x + 1).collect();

// Find element
let found = v.iter().find(|&&x| x > 2);

// Any/All
let has_even = v.iter().any(|&x| x % 2 == 0);
let all_positive = v.iter().all(|&x| x > 0);

// For each (consumes iterator)
v.iter().for_each(|x| println!("{}", x));

// Count elements
let count = v.iter().filter(|&&x| x > 2).count();
```

## Key Concepts
- **Closures** - Anonymous functions that capture environment variables
- **Iterators** - Lazy sequences producing values one at a time
- **Iterator adapter** - Transform iterator into another iterator (lazy, doesn't execute)
- **Consuming adapter** - Exhaust iterator and produce final value
- **Fn traits** - `Fn`, `FnMut`, `FnOnce` describe how closures capture environment
- **Zero-cost abstraction** - High-level constructs compile to efficient low-level code

## Rust-Specific Mechanics

### Closure Type Inference
- Parameter and return types inferred from usage
- First use locks in the types
- Can explicitly annotate if needed

### Capturing Environment
- Closures automatically capture variables from scope
- Three ways: by reference (`Fn`), by mutable reference (`FnMut`), by value (`FnOnce`)
- Rust chooses minimal capture needed
- Use `move` keyword to force taking ownership

### Lazy Iterators
- Iterator adapters don't execute until consumed
- Can chain many adapters without performance penalty
- Final consuming adapter triggers execution

### Zero-Cost Abstractions
- Iterators compile to same assembly as manual loops
- No runtime overhead compared to lower-level code
- Compiler optimizations make high-level code as fast as low-level

### Iterator vs for Loop
```rust
// These are equivalent in performance
for item in &v { /* ... */ }
v.iter().for_each(|item| /* ... */);

// Iterator chains often more expressive
v.iter()
    .filter(|x| **x > 0)
    .map(|x| x * 2)
    .collect()
```

### into_iter vs iter vs iter_mut
- `iter()` - Borrows each element (`&T`)
- `iter_mut()` - Mutably borrows each element (`&mut T`)
- `into_iter()` - Takes ownership, moves each element (`T`)

### collect() Type
- Needs type annotation or context to know target type
- `::<Vec<_>>` syntax (turbofish) for explicit type
- `_` lets compiler infer inner type
