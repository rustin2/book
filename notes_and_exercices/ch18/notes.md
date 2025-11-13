# Chapter 18: Object-Oriented Programming Features

## Encapsulation
```rust
pub struct AveragedCollection {
    list: Vec<i32>,      // private
    average: f64,        // private
}

impl AveragedCollection {
    pub fn add(&mut self, value: i32) {
        self.list.push(value);
        self.update_average();
    }

    pub fn average(&self) -> f64 {
        self.average
    }

    fn update_average(&mut self) {  // private method
        let total: i32 = self.list.iter().sum();
        self.average = total as f64 / self.list.len() as f64;
    }
}
```

## Trait Objects (Polymorphism)
```rust
// Define trait
pub trait Draw {
    fn draw(&self);
}

// Use trait object for heterogeneous collections
pub struct Screen {
    pub components: Vec<Box<dyn Draw>>,
}

impl Screen {
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();  // dynamic dispatch
        }
    }
}

// Implement trait for different types
struct Button { /* ... */ }
impl Draw for Button {
    fn draw(&self) { /* ... */ }
}

struct TextField { /* ... */ }
impl Draw for TextField {
    fn draw(&self) { /* ... */ }
}

// Use with different types
let screen = Screen {
    components: vec![
        Box::new(Button { /* ... */ }),
        Box::new(TextField { /* ... */ }),
    ],
};
```

## Trait Object Syntax
```rust
// Two forms:
&dyn Trait       // reference to trait object
Box<dyn Trait>   // boxed trait object

// Require pointer because size unknown at compile time
```

## Object Safety
```rust
// Trait must be object-safe to use as trait object:
// 1. Return type isn't Self
// 2. No generic type parameters

// Object-safe
pub trait Draw {
    fn draw(&self);
}

// NOT object-safe (returns Self)
pub trait Clone {
    fn clone(&self) -> Self;
}
```

## State Pattern
```rust
// Traditional OOP state pattern
pub struct Post {
    state: Option<Box<dyn State>>,
    content: String,
}

trait State {
    fn request_review(self: Box<Self>) -> Box<dyn State>;
    fn approve(self: Box<Self>) -> Box<dyn State>;
}

// Rust idiomatic approach: use type system
pub struct DraftPost {
    content: String,
}

pub struct PendingReviewPost {
    content: String,
}

pub struct Post {
    content: String,
}

impl DraftPost {
    pub fn request_review(self) -> PendingReviewPost {
        PendingReviewPost { content: self.content }
    }
}
```

## Key Concepts
- **Encapsulation** - Hide implementation details, expose controlled interface
- **Polymorphism** - Call different implementations based on type
- **Trait object** - Pointer to type implementing trait; enables dynamic dispatch
- **Dynamic dispatch** - Determine method to call at runtime (via vtable)
- **Static dispatch** - Determine method at compile time (monomorphization)
- **Object safety** - Rules determining if trait can be used as trait object

## OOP in Rust vs Traditional OOP

### Rust Has:
- **Encapsulation** - Via `pub`/private and modules
- **Polymorphism** - Via traits and generics
- **Composition** - Preferred over inheritance

### Rust Doesn't Have:
- **Inheritance** - No class hierarchies
- **Method overriding** - Use trait implementations instead
- **Traditional objects** - Structs + traits instead

## Rust-Specific Mechanics

### Trait Objects vs Generics
```rust
// Static dispatch (monomorphization, faster)
fn draw<T: Draw>(item: &T) {
    item.draw();
}

// Dynamic dispatch (flexible, slight runtime cost)
fn draw(item: &dyn Draw) {
    item.draw();
}
```

### Performance Trade-offs
- **Generics** - Zero runtime cost, larger binary (code duplication)
- **Trait objects** - Small runtime cost (vtable lookup), smaller binary

### When to Use Trait Objects
- Heterogeneous collections (different types in same collection)
- Return different types from same function
- Plugin systems
- When compile-time types unknown

### Object Safety Rules
- Can't use `Self` in return type (size unknown)
- No generic parameters (each type needs different vtable)
- Methods must use `&self`, `&mut self`, `Box<Self>`, etc.

### Type System for State
- Use different types for different states
- Compile-time state guarantees
- Invalid state transitions caught at compile time
- More Rust-idiomatic than traditional state pattern
