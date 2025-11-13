# Chapter 10: Generic Types, Traits, and Lifetimes

## Generics
```rust
// Generic function
fn largest<T: PartialOrd>(list: &[T]) -> &T { ... }

// Generic struct
struct Point<T> {
    x: T,
    y: T,
}

// Generic enum
enum Option<T> {
    Some(T),
    None,
}

// Generic methods
impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

// Methods for specific types
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 { ... }
}
```

## Traits
```rust
// Define trait
pub trait Summary {
    fn summarize(&self) -> String;
}

// Implement trait
impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {}", self.headline, self.author)
    }
}

// Default implementation
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}

// Trait bounds
fn notify<T: Summary>(item: &T) { ... }
fn notify(item: &impl Summary) { ... }  // syntactic sugar

// Multiple trait bounds
fn notify<T: Summary + Display>(item: &T) { ... }

// where clause
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{ ... }

// Return types implementing trait
fn returns_summarizable() -> impl Summary { ... }
```

## Lifetimes
```rust
// Lifetime annotation
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() { x } else { y }
}

// Struct with lifetime
struct ImportantExcerpt<'a> {
    part: &'a str,
}

// Static lifetime (entire program duration)
let s: &'static str = "I have a static lifetime.";

// Lifetime elision rules (automatic inference)
fn first_word(s: &str) -> &str { ... }  // compiler infers lifetimes
```

## Key Concepts
- **Generics** - Abstract over types; write code that works with multiple concrete types
- **Monomorphization** - Compiler generates specific code for each concrete type (zero runtime cost)
- **Trait** - Define shared behavior across types; similar to interfaces
- **Trait bound** - Restrict generic types to those implementing specific traits
- **Default implementation** - Trait provides default method; types can override
- **Lifetime** - Scope for which a reference is valid
- **Lifetime annotation** - Syntax describing how reference lifetimes relate
- **Static lifetime** - Reference valid for entire program duration

## Rust-Specific Mechanics

### Zero-Cost Generics
- Generics compiled to specific types (monomorphization)
- No runtime penalty compared to writing code for each type manually
- Type safety without performance cost

### Trait Bounds Enforce Behavior
- Can't use generic type without bounds
- Compiler ensures type has required methods/operators
- Prevents compilation if generic used incorrectly

### Lifetime Annotations Don't Change Lifetimes
- Annotations describe relationships between lifetimes
- Don't extend or shorten actual lifetimes
- Help compiler verify references are valid

### Lifetime Elision Rules
1. Each reference parameter gets its own lifetime
2. If one input lifetime, assigned to all output lifetimes
3. If multiple input lifetimes with `&self`/`&mut self`, lifetime of `self` assigned to output

### 'static Lifetime
- Lives entire program duration
- All string literals have `'static` lifetime
- Use sparingly; often indicates design issue
