# Chapter 11: Writing Automated Tests

## Test Basics
```rust
#[test]
fn it_works() {
    assert_eq!(2 + 2, 4);
}

#[test]
fn it_fails() {
    panic!("Make this test fail");
}
```

## Assertion Macros
```rust
// Check boolean
assert!(value);
assert!(value, "custom message {}", arg);

// Check equality
assert_eq!(left, right);
assert_ne!(left, right);

// Custom messages
assert_eq!(result, expected, "Result was {}", result);
```

## Expected Failures
```rust
#[test]
#[should_panic]
fn greater_than_100() {
    Guess::new(200);  // should panic
}

// With expected message substring
#[test]
#[should_panic(expected = "Guess must be less than or equal to 100")]
fn greater_than_100() {
    Guess::new(200);
}

// Using Result
#[test]
fn it_works() -> Result<(), String> {
    if 2 + 2 == 4 {
        Ok(())
    } else {
        Err(String::from("two plus two does not equal four"))
    }
}
```

## Running Tests
```bash
cargo test                      # run all tests
cargo test test_name            # run specific test
cargo test partial_name         # run tests matching pattern
cargo test -- --test-threads=1  # run serially
cargo test -- --show-output     # show println! output
cargo test -- --ignored         # run ignored tests only
cargo test -- --include-ignored # run all including ignored
```

## Test Organization
```rust
// Unit tests (same file as code)
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn internal() {
        assert_eq!(2 + 2, 4);
    }
}

// Integration tests (tests/ directory)
// tests/integration_test.rs
use my_crate;

#[test]
fn it_adds_two() {
    assert_eq!(my_crate::add_two(2), 4);
}

// Common test utilities (tests/common/mod.rs)
// Won't be run as test file
```

## Test Attributes
- `#[test]` - Mark function as test
- `#[ignore]` - Skip test unless explicitly requested
- `#[should_panic]` - Test passes if function panics
- `#[cfg(test)]` - Only compile when testing

## Key Concepts
- **Unit tests** - Test individual functions/modules in isolation
- **Integration tests** - Test how modules work together from external perspective
- **Test-driven development (TDD)** - Write test first, then code to pass it
- **Documentation tests** - Code in doc comments is tested

## Rust-Specific Mechanics

### Tests Run in Parallel
- By default, tests run simultaneously
- Each test gets own thread
- Use `--test-threads=1` for serial execution
- Don't share state between tests

### Test Module Convention
- `#[cfg(test)]` only compiles during `cargo test`
- `use super::*;` brings outer module items into scope
- Can test private functions (tests in same file)

### Integration Tests Structure
- `tests/` directory next to `src/`
- Each file is separate crate
- Only test public API
- `tests/common/mod.rs` for shared utilities (not run as test)
- Binary crates can't have integration tests (need library crate)

### Documentation Tests
- Code examples in `///` comments are compiled and run
- Automatically tests documentation stays accurate
- Use `# ` prefix for hidden lines in docs
