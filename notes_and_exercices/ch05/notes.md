# Chapter 5: Using Structs to Structure Related Data

## Struct Basics
```rust
struct User {
    username: String,
    email: String,
    active: bool,
    sign_in_count: u64,
}

// Create instance
let user = User {
    username: String::from("user"),
    email: String::from("user@example.com"),
    active: true,
    sign_in_count: 1,
};
```

## Struct Syntax
- **Field init shorthand** - `User { username, email, .. }` (omit field name if variable matches)
- **Struct update syntax** - `User { email, ..user1 }` (copy remaining fields from another instance)
- **Tuple struct** - `struct Color(i32, i32, i32);` (unnamed fields)
- **Unit-like struct** - `struct AlwaysEqual;` (no fields, for traits)

## Methods and Associated Functions
```rust
impl Rectangle {
    // Method (takes &self)
    fn area(&self) -> u32 {
        self.width * self.height
    }

    // Associated function (no self, like static)
    fn square(size: u32) -> Self {
        Self { width: size, height: size }
    }
}

// Call method with dot notation
rect.area()
// Call associated function with ::
Rectangle::square(10)
```

## Debug Output
- `#[derive(Debug)]` - Auto-implement Debug trait
- `{:?}` - Debug format in println!
- `{:#?}` - Pretty debug format
- `dbg!(&value)` - Print to stderr with file:line info

## Key Concepts
- **Struct** - Custom data type grouping related values with named fields
- **Instance** - Concrete value created from struct definition
- **Methods** - Functions in `impl` block with `&self`, `&mut self`, or `self`
- **Associated functions** - Functions in `impl` block without `self` (called with `::`)
- **Mutability** - Entire struct must be `mut`; no per-field mutability

## Rust-Specific Mechanics

### Ownership in Structs
- Structs own their data by default
- Use owned types (`String`) instead of references (`&str`) to ensure struct owns all data
- References in structs require lifetimes (Chapter 10)

### Automatic Referencing/Dereferencing
- Method calls automatically add `&`, `&mut`, or `*` to match signature
- No need for `->` operator (unlike C/C++)
- `object.method()` works whether `object` is value, reference, or mutable reference

### Multiple impl Blocks
- Can split methods across multiple `impl` blocks
- Useful for organizing code and trait implementations
