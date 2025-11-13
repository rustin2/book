# Chapter 8: Common Collections

## Vector (Vec\<T\>)
```rust
let mut v: Vec<i32> = Vec::new();
let v = vec![1, 2, 3];        // macro

v.push(4);                     // add element
v.pop();                       // remove last element

// Access elements
let third = &v[2];            // panics if out of bounds
match v.get(2) {              // returns Option<&T>
    Some(val) => println!("{}", val),
    None => println!("out of bounds"),
}

// Iterate
for elem in &v { println!("{}", elem); }
for elem in &mut v { *elem += 50; }
```

### Multiple Types with Enum
```rust
enum SpreadsheetCell {
    Int(i32),
    Float(f64),
    Text(String),
}
let row = vec![
    SpreadsheetCell::Int(3),
    SpreadsheetCell::Text(String::from("blue")),
];
```

## String
```rust
let s = String::new();
let s = "hello".to_string();
let s = String::from("hello");

// Append
s.push_str("bar");      // append &str
s.push('l');            // append char

// Concatenate
let s3 = s1 + &s2;      // s1 moved, s2 borrowed
let s = format!("{}-{}-{}", s1, s2, s3);  // doesn't move

// Iterate
for c in "नमस्ते".chars() { ... }   // Unicode scalar values
for b in "नमस्ते".bytes() { ... }   // raw bytes

// Slicing (use with caution, must be valid UTF-8 boundary)
let slice = &s[0..4];   // panic if not on char boundary
```

## HashMap
```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

// Access
let score = scores.get("Blue");    // returns Option<&V>
for (key, value) in &scores { ... }

// Update
scores.insert(key, new_value);     // overwrites

// Insert if absent
scores.entry("Yellow").or_insert(50);

// Update based on old value
let count = scores.entry(word).or_insert(0);
*count += 1;
```

## Key Concepts
- **Vector** - Growable, heap-allocated list of same type
- **String** - Growable, heap-allocated UTF-8 text (wrapper over `Vec<u8>`)
- **String slice (&str)** - Immutable reference to UTF-8 data
- **HashMap** - Key-value store with O(1) lookups

## Rust-Specific Mechanics

### Vector Type Homogeneity
- Vectors store single type only
- Use enums to store multiple types in one vector
- Compiler knows size at compile time for stack allocation

### Indexing vs get()
- `v[i]` - Panics on out-of-bounds (use when index guaranteed valid)
- `v.get(i)` - Returns `Option<&T>` (safe for uncertain indices)

### Borrow Checker & Vectors
- Can't hold immutable reference while modifying vector
- Modification might reallocate, invalidating existing references
- Forces safe memory usage

### UTF-8 String Complexity
- **No indexing** - `s[0]` not allowed (ambiguous: byte? char? grapheme cluster?)
- `.chars()` - Iterate Unicode scalar values (characters)
- `.bytes()` - Iterate raw bytes
- **Slicing** - `&s[0..4]` works but must be valid UTF-8 boundary or panics

### String Concatenation
- `+` operator moves left operand, borrows right (`&str`)
- `format!` macro doesn't take ownership (more flexible)

### HashMap Ownership
- **Copy types** (`i32`, etc.) - Copied into map
- **Owned types** (`String`) - Moved into map; map becomes owner
- **References** - Require lifetimes; values must outlive map

### Entry API
- `entry().or_insert()` - Returns mutable reference to value
- Prevents multiple lookups for update operations
- Idiomatic way to insert default or modify existing value
