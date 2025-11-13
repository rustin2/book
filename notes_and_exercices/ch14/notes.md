# Chapter 14: More About Cargo and Crates.io

## Release Profiles
```toml
# Cargo.toml
[profile.dev]
opt-level = 0  # no optimization (faster compile)

[profile.release]
opt-level = 3  # max optimization (slower compile, faster runtime)
```
- `cargo build` uses `dev` profile
- `cargo build --release` uses `release` profile

## Documentation Comments
```rust
/// Adds one to the number given.
///
/// # Examples
///
/// ```
/// let arg = 5;
/// let answer = my_crate::add_one(arg);
/// assert_eq!(6, answer);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}

//! # My Crate
//! `my_crate` is a collection of utilities to make
//! performing certain calculations more convenient.
```

## Documentation Commands
```bash
cargo doc              # generate docs
cargo doc --open       # generate and open in browser
cargo test             # runs doc tests
```

## Publishing to Crates.io
```bash
cargo login <api-token>        # authenticate
cargo publish                  # publish crate
cargo yank --vers 1.0.1        # deprecate version (prevents new usage)
cargo yank --vers 1.0.1 --undo # un-deprecate
```

## Cargo.toml Metadata
```toml
[package]
name = "my_crate"
version = "0.1.0"
edition = "2021"
authors = ["Your Name <you@example.com>"]
license = "MIT OR Apache-2.0"
description = "A short description"
documentation = "https://docs.rs/my_crate"
repository = "https://github.com/yourusername/my_crate"
keywords = ["keyword1", "keyword2"]
categories = ["category1"]
```

## Workspaces
```toml
# Cargo.toml in root
[workspace]
members = [
    "adder",
    "add_one",
]
```
```bash
# Run binary from workspace
cargo run -p adder

# Test specific crate
cargo test -p add_one

# Build all workspace members
cargo build
```

## Re-exporting
```rust
// lib.rs
pub use self::kinds::PrimaryColor;
pub use self::utils::mix;

// Users can now use:
use my_crate::PrimaryColor;
// Instead of:
use my_crate::kinds::PrimaryColor;
```

## Installing Binaries
```bash
cargo install ripgrep          # install from crates.io
cargo install --path .         # install from local path
```

## Custom Cargo Commands
- Executables named `cargo-something` become `cargo something` subcommands
- Discoverable via `cargo --list`

## Key Concepts
- **Release profiles** - Compilation configurations (dev vs release)
- **Documentation comments** - `///` and `//!` generate HTML docs
- **Doc tests** - Code in doc comments is compiled and run
- **Crates.io** - Official Rust package registry
- **Workspaces** - Multiple related packages sharing dependencies
- **Re-exporting** - Making items public at different hierarchy levels
- **Semantic versioning** - `major.minor.patch` version numbering

## Rust-Specific Mechanics

### opt-level Settings
- `0` - No optimization (fast compile, slow runtime)
- `1` - Basic optimization
- `2` - Some optimization
- `3` - Full optimization (slow compile, fast runtime)

### Documentation Best Practices
- `///` - Document item that follows
- `//!` - Document containing item (crate/module)
- Common sections: Examples, Panics, Errors, Safety
- Examples are tested by `cargo test`

### Publishing Rules
- Versions are permanent (can't delete or overwrite)
- Use `cargo yank` to prevent new projects from using a version
- Existing projects can still download yanked versions
- Name must be unique on crates.io

### Workspace Benefits
- Single `Cargo.lock` for entire workspace
- Shared `target/` directory (saves disk space)
- All crates use same dependency versions
- `cargo test` runs tests for all workspace members

### Re-exporting for API Design
- Flatten deep module hierarchies for users
- Present convenient public API separate from internal structure
- Use `pub use` to make items available at crate root
