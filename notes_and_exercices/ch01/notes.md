# Chapter 1: Getting Started

## Installation Commands
- `rustc --version` - Check Rust installation
- `rustup update` - Update Rust to latest version
- `rustup doc` - Open local documentation

## Compilation and Execution
- `rustc main.rs` - Compile a Rust file directly
- `./main` - Execute compiled binary (Linux/macOS)

## Cargo Commands (Build System)
- `cargo new <project_name>` - Create new Cargo project
- `cargo build` - Build project (debug mode)
- `cargo run` - Compile and run in one command
- `cargo check` - Check code compiles without producing executable
- `cargo build --release` - Compile with optimizations for production

## Basic Rust Syntax
- `fn main()` - Main function (entry point of every Rust program)
- `println!()` - Macro for printing to console
- Use semicolons (`;`) to end statements
- File extension: `.rs`
- Naming convention: use underscores for multi-word file names (e.g., `hello_world.rs`)

## Project Structure
- `src/` - Directory for source files
- `Cargo.toml` - Configuration file for dependencies and metadata
- `target/debug/` - Debug builds location
- `target/release/` - Release builds location
