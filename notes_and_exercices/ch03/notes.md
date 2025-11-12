# Chapter 3: Common Programming Concepts

## Variables and Mutability
- `let` - Declare immutable variable (default)
- `let mut` - Declare mutable variable
- `const NAME: type = value` - Declare constant (always immutable, compile-time only)
- **Immutability** - Variables cannot change once bound unless marked `mut`
- **Shadowing** - Redeclare variable with same name using `let`; allows type changes

## Data Types

### Scalar Types (single values)
- **Integer** - Number without decimal: `i8`, `i16`, `i32`, `i64`, `i128`, `isize` (signed) or `u8`, `u16`, `u32`, `u64`, `u128`, `usize` (unsigned)
- **Floating-point** - Decimal numbers: `f32`, `f64` (default)
- **Boolean** - `bool`: `true` or `false`
- **Character** - `char`: Single Unicode character (4 bytes)

### Compound Types (multiple values)
- **Tuple** - Fixed-length, mixed types: `(value1, value2, value3)`
  - Access by index: `.0`, `.1`, `.2`
  - Destructure: `let (x, y, z) = tuple;`
  - **Unit** - Empty tuple `()` representing empty value/return
- **Array** - Fixed-length, same type: `[1, 2, 3]`
  - Type syntax: `[type; length]`
  - Access by index: `array[0]`
  - Stack allocated

## Functions
- `fn name() {}` - Declare function
- `fn name(param: type) {}` - Function with parameters
- `fn name() -> ReturnType {}` - Function with return type
- **Parameter** - Variable in function signature
- **Statement** - Instruction that performs action, no return value
- **Expression** - Evaluates to a value (no semicolon at end)
- Last expression in function is return value (no semicolon)
- `return value;` - Early return from function

## Control Flow
- `if condition {}` - Conditional branching (condition must be `bool`)
- `else if condition {}` - Multiple conditions
- `else {}` - Fallback branch
- `if` can be used as expression: `let x = if condition { val1 } else { val2 };`
- `loop {}` - Infinite loop until `break`
- `while condition {}` - Loop while condition is true
- `for item in collection {}` - Iterate over collection
- `break` - Exit loop (can return value: `break value;`)
- `continue` - Skip to next iteration
- `'label:` - Name a loop for nested loop control
- `break 'label` - Exit specific outer loop

## Comments
- `//` - Single line comment (idiomatic)
- Can appear above code or at end of line

## Key Concepts
- **Type annotation** - Specify type with `:` when compiler needs help
- **Type inference** - Compiler deduces types automatically
- **Static typing** - All types known at compile time
- **Integer overflow** - Methods: `wrapping_*`, `checked_*`, `saturating_*`, `overflowing_*`
- **Range** - `(1..4)` or `(1..=4)` for iteration
- `.rev()` - Reverse a range

## Rust-Specific Mechanics

### Immutability by Default
- Unlike most languages, Rust variables are immutable unless explicitly marked `mut`
- Encourages safer code by preventing accidental changes
- Compiler enforces this at compile time

### Shadowing vs Mutability
- **Shadowing** - Create new variable with same name using `let` (can change type)
- **Mutability** - Modify existing variable's value with `mut` (same type only)
- Shadowing effectively creates a new variable; mutability modifies existing one

### Expressions Everywhere
- Blocks `{}` are expressions that return values
- `if` is an expression, not just a statement (can assign result to variable)
- Functions return last expression automatically (no `return` needed)
- Adding `;` turns expression into statement (returns unit `()`)

### Type Safety
- No automatic type coercion (e.g., integers won't convert to booleans)
- `if` conditions must be explicitly `bool`
- All `if` branches must return same type
- Array access is bounds-checked at runtime (panics on invalid index)

### Stack vs Heap
- Arrays are stack-allocated (fixed size, fast)
- For dynamic size, use `Vec<T>` (heap-allocated, covered later)

### Integer Literals
- Decimal: `98_222` (underscores for readability)
- Hex: `0xff`
- Octal: `0o77`
- Binary: `0b1111_0000`
- Byte (`u8` only): `b'A'`

### Loop Return Values
- `loop` can return a value: `let result = loop { break value; };`
- Unique to Rust; allows loops to be expressions
