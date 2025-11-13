# Chapter 20: Advanced Features

## Unsafe Rust
```rust
unsafe {
    // Five unsafe superpowers:
    // 1. Dereference raw pointer
    // 2. Call unsafe function
    // 3. Access/modify mutable static
    // 4. Implement unsafe trait
    // 5. Access fields of union
}

// Raw pointers
let mut num = 5;
let r1 = &num as *const i32;      // immutable raw pointer
let r2 = &mut num as *mut i32;    // mutable raw pointer

unsafe {
    println!("r1: {}", *r1);      // dereference requires unsafe
    *r2 = 10;
}

// Unsafe function
unsafe fn dangerous() { /* ... */ }

unsafe {
    dangerous();  // call requires unsafe block
}

// Safe abstraction over unsafe code
fn split_at_mut(slice: &mut [i32], mid: usize) -> (&mut [i32], &mut [i32]) {
    let len = slice.len();
    let ptr = slice.as_mut_ptr();

    assert!(mid <= len);

    unsafe {
        (
            std::slice::from_raw_parts_mut(ptr, mid),
            std::slice::from_raw_parts_mut(ptr.add(mid), len - mid),
        )
    }
}
```

## Advanced Traits

### Associated Types
```rust
pub trait Iterator {
    type Item;  // associated type
    fn next(&mut self) -> Option<Self::Item>;
}

impl Iterator for Counter {
    type Item = u32;
    fn next(&mut self) -> Option<Self::Item> { /* ... */ }
}
```

### Default Type Parameters
```rust
trait Add<Rhs=Self> {  // Rhs defaults to Self
    type Output;
    fn add(self, rhs: Rhs) -> Self::Output;
}

// Custom impl
impl Add<Meters> for Millimeters {
    type Output = Millimeters;
    fn add(self, other: Meters) -> Millimeters { /* ... */ }
}
```

### Fully Qualified Syntax
```rust
trait Pilot {
    fn fly(&self);
}

trait Wizard {
    fn fly(&self);
}

struct Human;

impl Pilot for Human {
    fn fly(&self) { println!("This is your captain speaking."); }
}

impl Wizard for Human {
    fn fly(&self) { println!("Up!"); }
}

impl Human {
    fn fly(&self) { println!("*waving arms furiously*"); }
}

let person = Human;
person.fly();           // calls Human::fly
Pilot::fly(&person);    // calls Pilot::fly
Wizard::fly(&person);   // calls Wizard::fly

// Fully qualified (for associated functions without self)
<Human as Pilot>::fly(&person);
```

### Supertraits
```rust
trait OutlinePrint: fmt::Display {  // requires Display
    fn outline_print(&self) {
        println!("* {} *", self);  // can use Display::to_string
    }
}
```

### Newtype Pattern
```rust
// Wrapper type for external trait on external type
struct Wrapper(Vec<String>);

impl fmt::Display for Wrapper {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "[{}]", self.0.join(", "))
    }
}
```

## Advanced Types

### Type Aliases
```rust
type Kilometers = i32;  // synonym for i32

// Reduce repetition
type Result<T> = std::result::Result<T, std::io::Error>;

// In trait definitions
type Thunk = Box<dyn Fn() + Send + 'static>;
```

### Never Type
```rust
// ! (never type) - represents computations that never return
fn bar() -> ! {
    panic!("never returns");
}

// Used in match arms that panic or loop forever
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,  // continue has type !
};
```

### Dynamically Sized Types (DST)
```rust
// str is DST (size unknown at compile time)
let s1: &str = "Hello";  // reference includes size info

// Generic bounds with ?Sized
fn generic<T: ?Sized>(t: &T) {
    // T might be DST (like str)
}
```

## Advanced Functions

### Function Pointers
```rust
fn add_one(x: i32) -> i32 {
    x + 1
}

// Function pointer type: fn(i32) -> i32
fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
    f(arg) + f(arg)
}

let answer = do_twice(add_one, 5);  // 12

// fn is type, Fn is trait
// fn implements Fn, FnMut, FnOnce
```

### Returning Closures
```rust
// Can't return closure directly (unknown size)
fn returns_closure() -> Box<dyn Fn(i32) -> i32> {
    Box::new(|x| x + 1)
}

// Or use impl Trait
fn returns_closure() -> impl Fn(i32) -> i32 {
    |x| x + 1
}
```

## Macros

### Declarative Macros
```rust
#[macro_export]
macro_rules! vec {
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
}
```

### Procedural Macros
```rust
// Derive macro
#[derive(HelloMacro)]
struct Pancakes;

// Attribute-like macro
#[route(GET, "/")]
fn index() { }

// Function-like macro
let sql = sql!(SELECT * FROM posts WHERE id=1);
```

## Key Concepts
- **Unsafe** - Opt-out of safety checks; programmer ensures correctness
- **Raw pointers** - `*const T` and `*mut T`; can be null, no automatic cleanup
- **Associated types** - Trait placeholder type specified per implementation
- **Default type parameters** - Generic defaults reducing verbosity
- **Fully qualified syntax** - Disambiguate method calls: `<Type as Trait>::method()`
- **Newtype pattern** - Wrapper type for orphan rule or type safety
- **Type alias** - Alternative name for existing type
- **Never type** - `!` for computations that never return
- **DST** - Types without known size at compile time (need pointers)
- **Function pointers** - `fn` type for functions (vs `Fn` trait for closures)
- **Macros** - Code that writes code; expand at compile time

## Rust-Specific Mechanics

### Unsafe Doesn't Disable All Checks
- Still enforces borrowing rules
- Still checks lifetimes
- Only bypasses: raw pointer dereferencing, unsafe calls, static mutation, union access

### Associated Types vs Generic Parameters
- Associated types: one implementation per type
- Generics: multiple implementations per type with different parameters
- Associated types cleaner when single implementation expected

### Newtype Pattern Benefits
- Zero runtime cost (wrapper optimized away)
- Bypasses orphan rule (can implement external trait)
- Provides type safety (different meaning for same underlying type)

### Never Type Coercion
- `!` coerces to any type
- Enables match arms that panic/loop with typed branches

### Sized Trait
- Automatically implemented for types with known size
- `?Sized` bound allows DST
- Generic parameters implicitly `Sized` unless `?Sized`

### fn vs Fn
- `fn` is concrete function pointer type
- `Fn` is trait implemented by closures and functions
- Use `fn` when don't need closure capture
