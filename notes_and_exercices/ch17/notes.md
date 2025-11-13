# Chapter 17: Async and Await

## Async Functions
```rust
// Async function returns Future
async fn do_something() -> i32 {
    42
}

// Call with .await
async fn example() {
    let result = do_something().await;
    println!("Result: {}", result);
}
```

## Future Trait
```rust
pub trait Future {
    type Output;
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}

pub enum Poll<T> {
    Ready(T),
    Pending,
}
```

## Async Runtime
```rust
// Using tokio
#[tokio::main]
async fn main() {
    let result = async_function().await;
}

// Or manually
fn main() {
    let runtime = tokio::runtime::Runtime::new().unwrap();
    runtime.block_on(async {
        async_function().await;
    });
}
```

## Concurrent Async Tasks
```rust
use tokio::join;

async fn fetch_data() {
    let (result1, result2) = join!(
        async_operation1(),
        async_operation2()
    );
}

// Or use tokio::spawn for separate tasks
let handle1 = tokio::spawn(async { async_operation1().await });
let handle2 = tokio::spawn(async { async_operation2().await });
let result1 = handle1.await.unwrap();
let result2 = handle2.await.unwrap();
```

## Async Blocks
```rust
let future = async {
    println!("In async block");
    42
};

let result = future.await;
```

## Pin and Unpin
```rust
// Most types are Unpin (can be moved freely)
// Futures may need to be pinned (prevent moving)

use std::pin::Pin;

fn poll_future(future: Pin<&mut impl Future<Output = i32>>) {
    // future is pinned, safe to poll
}
```

## Key Concepts
- **Async** - Programming model for code with pause points
- **Future** - Value representing computation that may not be complete
- **Await** - Pause execution until future completes
- **Runtime** - Executes and schedules async tasks (e.g., Tokio, async-std)
- **Poll** - Check if future is ready or still pending
- **Concurrency** - Multiple tasks making progress (may pause and resume)
- **Parallelism** - Multiple tasks running simultaneously on different cores

## Async vs Threads

### Use Async When:
- I/O-bound operations (network, file I/O)
- Many tasks with lots of waiting
- Need to handle thousands of concurrent connections
- Tasks spend time waiting, not computing

### Use Threads When:
- CPU-bound operations (heavy computation)
- Need true parallelism across cores
- Blocking operations that can't be async
- Smaller number of concurrent tasks

## Rust-Specific Mechanics

### Futures are Lazy
- Creating async function doesn't execute it
- Must `.await` or poll for anything to happen
- Enables zero-cost abstraction

### Zero-Cost Async
- Compiles to state machine
- No heap allocation for simple futures
- Minimal runtime overhead compared to manual state machines

### Multiple Runtimes
- Rust doesn't include runtime in std library
- Choose runtime based on needs (Tokio, async-std, smol)
- Async code is runtime-agnostic (mostly)

### .await Syntax
- Can only use `.await` in async functions
- `.await` yields control to runtime
- Runtime schedules other tasks while waiting

### Combining Async and Threads
- Use threads for CPU-bound work
- Use async for I/O-bound work
- `tokio::spawn` creates async task on runtime thread pool
- `std::thread::spawn` creates OS thread

### Pin and Move Semantics
- Some futures can't be moved after polling
- `Pin<T>` prevents moving
- Most types are `Unpin` (safe to move)
- Compiler handles pinning automatically with `.await`

### Send + Sync Bounds
- Async tasks that cross threads need `Send`
- `Arc<Mutex<T>>` works across async boundaries
- Careful with `Rc<T>` and `RefCell<T>` (not thread-safe)
