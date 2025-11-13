# Chapter 16: Fearless Concurrency

## Creating Threads
```rust
use std::thread;
use std::time::Duration;

// Spawn new thread
let handle = thread::spawn(|| {
    for i in 1..10 {
        println!("hi number {} from spawned thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
});

// Wait for thread to finish
handle.join().unwrap();

// Move ownership into closure
let v = vec![1, 2, 3];
let handle = thread::spawn(move || {
    println!("vector: {:?}", v);
});
```

## Message Passing (Channels)
```rust
use std::sync::mpsc;  // multiple producer, single consumer

// Create channel
let (tx, rx) = mpsc::channel();

// Send message
thread::spawn(move || {
    let val = String::from("hi");
    tx.send(val).unwrap();
    // val no longer usable (moved)
});

// Receive message
let received = rx.recv().unwrap();  // blocks until message
// Or use try_recv() - non-blocking, returns Result

// Multiple senders
let tx1 = tx.clone();
thread::spawn(move || { tx1.send("hi from 1").unwrap(); });
thread::spawn(move || { tx.send("hi from 2").unwrap(); });

// Iterate over received messages
for received in rx {
    println!("Got: {}", received);
}
```

## Shared State (Mutex)
```rust
use std::sync::Mutex;

// Create mutex
let m = Mutex::new(5);

{
    // Acquire lock (blocks until available)
    let mut num = m.lock().unwrap();
    *num = 6;
}  // lock automatically released

// Share across threads with Arc
use std::sync::Arc;

let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```

## Send and Sync Traits
```rust
// Send: type can transfer ownership between threads
// Most types are Send (except Rc<T>)

// Sync: type safe to reference from multiple threads
// &T is Send if T is Sync

// Manual implementation (unsafe)
unsafe impl Send for MyType {}
unsafe impl Sync for MyType {}
```

## Key Concepts
- **Thread** - Independent execution path running concurrently
- **Concurrency** - Multiple tasks making progress (not necessarily simultaneously)
- **Parallelism** - Multiple tasks running simultaneously (requires multiple cores)
- **Message passing** - Threads communicate by sending data through channels
- **Shared state** - Multiple threads access same memory location
- **Mutex** - Mutual exclusion lock; one thread accesses data at a time
- **Send trait** - Type can transfer ownership between threads
- **Sync trait** - Type safe to reference from multiple threads

## Rust-Specific Mechanics

### Thread Safety at Compile Time
- Ownership prevents data races
- Can't use variable after moving to thread
- `move` closure captures variables by value

### Channel Ownership
- Sending transfers ownership
- Can't use value after sending
- Prevents using freed memory
- Multiple producers via `clone()`

### Mutex\<T\> Type Safety
- Must call `lock()` to access data
- Returns `MutexGuard<T>` (smart pointer)
- Lock automatically released when guard goes out of scope
- Can't access data without acquiring lock

### Arc\<T\> vs Rc\<T\>
- `Rc<T>` - Single-threaded reference counting
- `Arc<T>` - Atomic reference counting (thread-safe)
- `Arc` has performance cost (atomic operations)
- Use `Arc` only when crossing thread boundaries

### Send and Sync
- Compiler automatically implements for safe types
- `Rc<T>` is not `Send` (would corrupt count)
- `RefCell<T>` is not `Sync` (runtime checks not thread-safe)
- Use `Arc<Mutex<T>>` for shared mutable state across threads

### Deadlock Prevention
- Rust doesn't prevent deadlocks
- Acquire locks in consistent order
- Keep lock scope small
- Consider using `try_lock()` for timeout

### Main Thread Behavior
- If main thread ends, spawned threads killed
- Use `join()` to wait for completion
- Handle thread panics with `join().unwrap()`
