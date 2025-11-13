# Chapter 4: Understanding Ownership

## Ownership Rules
1. Each value has an owner
2. Only one owner at a time
3. Value is dropped when owner goes out of scope

## Memory Concepts
- **Stack** - LIFO, fixed size, fast (e.g., integers, booleans)
- **Heap** - Dynamic size, slower, flexible (e.g., `String`)
- **Scope** - Range where variable is valid (until end of block)
- **Drop** - Automatic cleanup when variable goes out of scope

## String Types
- `let s = "hello";` - String literal (`&str`, immutable, stack)
- `String::from("hello")` - Heap-allocated String (growable, mutable)
- `s.len()` - Get string length
- `s.clone()` - Deep copy heap data
- `s.clear()` - Clear string contents

## Move vs Copy
- **Move** - Transfer ownership (heap data); original becomes invalid
- **Copy** - Duplicate value (stack data); both remain valid
- **Copy Trait** - Types that are trivially copied (integers, bools, chars, tuples of Copy types)
- Cannot have both Copy and Drop traits

## References and Borrowing
- `&s` - Immutable reference (borrow without taking ownership)
- `&mut s` - Mutable reference (borrow with modification rights)
- `*r` - Dereference operator

### Borrowing Rules
- **Either** one mutable reference **OR** any number of immutable references
- References must always be valid (no dangling references)
- Reference scope: from creation to last use

## Slices
- `&s[0..5]` - Slice from index 0 to 5 (exclusive)
- `&s[..2]` - From start to index 2
- `&s[3..]` - From index 3 to end
- `&s[..]` - Entire slice
- `&str` - String slice type (immutable reference to string data)
- `&[T]` - Generic slice type for arrays

## Key Concepts

### Ownership
- **Ownership** - Rules governing memory management without garbage collection
- **Move semantics** - Ownership transfers; prevents double-free errors
- **RAII pattern** - Resource acquired, automatically freed at scope end

### Data Race Prevention
- **Data race** - Multiple pointers, one writes, no synchronization
- Rust prevents at compile time with borrowing rules

### Deref Coercion
- Functions accepting `&str` work with both `&String` and `&str`
- Compiler automatically converts `&String` to `&str`

## Rust-Specific Mechanics

### Ownership-Based Memory Safety
- No garbage collector needed
- Memory freed automatically when owner goes out of scope
- Zero runtime cost - enforced at compile time

### Move by Default
- Heap-allocated values are moved, not copied
- Prevents expensive implicit copies
- Original variable invalidated after move

### Compile-Time Guarantees
- No dangling references (data outlives references)
- No use-after-free errors
- No data races
- No null pointer dereferences

### Reference Rules Prevent Data Races
- Can't have mutable and immutable references simultaneously
- Only one mutable reference at a time
- Compile-time concurrency safety

### Slice Safety
- Slices keep data and index in sync
- Borrowing rules prevent invalidation while slice is in use
- Eliminates entire class of bugs at compile time
