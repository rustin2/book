# Chapter 15: Smart Pointers

## Box\<T\> (Heap Allocation)
```rust
// Store value on heap
let b = Box::new(5);

// Recursive types (fixed size)
enum List {
    Cons(i32, Box<List>),
    Nil,
}

// Trait objects
let obj: Box<dyn Trait> = Box::new(concrete_type);
```

## Deref Trait
```rust
use std::ops::Deref;

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

// Deref coercion
fn hello(name: &str) { ... }
let m = MyBox::new(String::from("Rust"));
hello(&m);  // automatically converts &MyBox<String> to &str
```

## Drop Trait
```rust
impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer!");
    }
}

// Force early drop
std::mem::drop(value);  // can't call value.drop() directly
```

## Rc\<T\> (Reference Counting)
```rust
use std::rc::Rc;

let a = Rc::new(Cons(5, Rc::new(Nil)));
let b = Cons(3, Rc::clone(&a));  // increment reference count
let c = Cons(4, Rc::clone(&a));

// Check reference count
println!("count = {}", Rc::strong_count(&a));
```

## RefCell\<T\> (Interior Mutability)
```rust
use std::cell::RefCell;

let value = RefCell::new(5);

// Borrow immutably
let borrowed = value.borrow();

// Borrow mutably (panics if already borrowed)
let mut borrowed_mut = value.borrow_mut();

// Common pattern: Rc<RefCell<T>> for multiple owners with mutation
let shared_list = Rc::new(RefCell::new(vec![1, 2, 3]));
shared_list.borrow_mut().push(4);
```

## Weak\<T\> (Weak References)
```rust
use std::rc::{Rc, Weak};

// Create weak reference
let weak: Weak<i32> = Rc::downgrade(&rc_value);

// Upgrade to Rc (returns Option)
if let Some(rc) = weak.upgrade() {
    // Use rc
}
```

## Key Concepts
- **Smart pointer** - Data structure acting like pointer with metadata and capabilities
- **Box\<T\>** - Heap allocation; enables recursive types and trait objects
- **Deref trait** - Allows smart pointers to act like references via `*` operator
- **Drop trait** - Runs cleanup code when value goes out of scope
- **Rc\<T\>** - Reference counting for shared ownership (single-threaded)
- **RefCell\<T\>** - Interior mutability with runtime borrow checking
- **Weak\<T\>** - Non-owning reference that doesn't prevent deallocation

## Borrowing Rules Comparison

### Compile-time (References)
- One `&mut T` OR many `&T`
- Enforced by compiler

### Runtime (RefCell\<T\>)
- One `borrow_mut()` OR many `borrow()`
- Panics if violated at runtime

## Rust-Specific Mechanics

### When to Use Box\<T\>
1. Type size unknown at compile time (recursive types)
2. Transfer ownership of large data without copying
3. Trait objects (`Box<dyn Trait>`)

### Deref Coercion
- Automatically converts `&T` to `&U` when `T: Deref<Target=U>`
- Works for function/method arguments
- Chains: `&Box<String>` → `&String` → `&str`

### Drop Order
- Values dropped in reverse order of creation
- Automatic via RAII pattern
- Use `std::mem::drop()` for early cleanup

### Rc\<T\> Limitations
- Only for single-threaded scenarios
- Immutable data by default
- Can create reference cycles (memory leaks)
- Use `Weak<T>` to break cycles

### Interior Mutability Pattern
- Mutate data through immutable reference
- Useful when need mutability but API requires immutability
- Trade compile-time safety for runtime flexibility
- `RefCell<T>` panics on borrow rule violations

### Preventing Reference Cycles
- Use `Weak<T>` for non-owning relationships
- Parent → Child: `Rc<T>`
- Child → Parent: `Weak<T>`
- `Weak` doesn't keep value alive

### Common Combinations
- `Rc<RefCell<T>>` - Multiple owners, mutable data
- `Box<dyn Trait>` - Trait objects
- `Rc<Weak<T>>` - Prevent reference cycles
