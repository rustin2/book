# Chapter 7: Managing Growing Projects with Packages, Crates, and Modules

## Project Organization
- **Package** - Cargo project with `Cargo.toml`; contains one or more crates
- **Crate** - Tree of modules; compiles to library or binary
- **Binary crate** - Has `main` function; becomes executable
- **Library crate** - No `main`; provides reusable functionality
- **Crate root** - Entry point: `src/main.rs` (binary) or `src/lib.rs` (library)

## Module Syntax
```rust
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() { ... }
    }
}

// Call with paths
crate::front_of_house::hosting::add_to_waitlist();  // absolute
front_of_house::hosting::add_to_waitlist();         // relative
```

## Paths
- **Absolute path** - Starts with `crate` (current crate) or crate name (external)
- **Relative path** - Starts with `self`, `super`, or module name
- `super` - Access parent module (like `..` in filesystem)

## Visibility
- **Private by default** - Items not visible outside parent module
- `pub` - Make item public to parent
- `pub mod` - Make module name visible, but contents still private
- `pub struct` - Struct public, fields private (unless marked `pub`)
- `pub enum` - Enum and all variants public

## use Keyword
```rust
use crate::front_of_house::hosting;
use std::collections::HashMap;

// Rename with as
use std::io::Result as IoResult;

// Re-export
pub use crate::front_of_house::hosting;

// Nested paths
use std::io::{self, Write};  // brings io and io::Write
use std::collections::{HashMap, HashSet};

// Glob
use std::collections::*;
```

## File Organization
```rust
// src/lib.rs
mod front_of_house;  // looks for src/front_of_house.rs or src/front_of_house/mod.rs

// Nested modules
// src/front_of_house.rs contains: mod hosting;
// src/front_of_house/hosting.rs contains module code
```

## Key Concepts
- **Module** - Namespace for code organization and privacy control
- **Path** - Way to refer to items using `::` separators
- **use** - Create shortcut to path in current scope
- **Re-export** - `pub use` makes imported item available externally

## Rust-Specific Mechanics

### Privacy Boundaries
- Child modules see parent items
- Parents don't see child private items
- Forces clear API boundaries

### Granular Publicity
- `pub mod` makes module visible, contents still private
- Must mark individual items `pub`
- Struct fields private by default (even in `pub struct`)
- Enum variants all public (if enum is `pub`)

### Idiomatic use Patterns
- **Functions**: Import parent module, call `module::function()` (shows source)
- **Structs/Enums**: Import full path, use directly (less verbose)

### Module File Resolution
1. `mod garden;` in crate root looks for:
   - `src/garden.rs` (preferred)
   - `src/garden/mod.rs` (old style)
2. Creates directory structure matching module tree
3. Enables organizing large projects into multiple files

### use Scope
- Imports only apply to declaring scope
- Nested scopes need their own `use` statements
- Re-export with `pub use` to expose in public API
