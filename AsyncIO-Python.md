````md
# Python Asyncio Guide

## Core Concepts of Python Async Programming

Python's async system uses a few core concepts:

### 1. Coroutines
Functions defined with `async def` instead of `def`.

They can pause and resume execution, making them perfect for operations that involve waiting.

### 2. `await`
This keyword tells Python:

> "Pause this coroutine until this operation completes, but let other code run in the meantime."

### 3. Event Loop
The engine that manages all your coroutines, deciding:

- Which coroutine to run
- When to pause
- When to resume execution

### 4. Tasks
Coroutines wrapped for concurrent execution.

You create them with:

```python
asyncio.create_task()
```

This allows multiple operations to run concurrently.

---

# What Async Programming Can and Cannot Do

## Async Works Best For I/O-Bound Work

Examples:

- HTTP requests
- Database queries
- File operations
- API calls

These tasks spend most of their time waiting for external systems.

---

## Async Does NOT Help CPU-Bound Work

Examples:

- Complex calculations
- Heavy data processing
- Machine learning computations

These tasks actively use the CPU instead of waiting.

---

# Synchronous Example (Blocking)

```python
import time

def greet_after_delay():
    print("Starting...")
    time.sleep(2)  # Blocks for 2 seconds
    print("Hello!")

greet_after_delay()
```

## Problem

`time.sleep(2)` blocks the entire program.

Nothing else can run during those two seconds.

---

# Async Version (Non-Blocking)

```python
import asyncio

async def greet_after_delay():
    print("Starting...")
    await asyncio.sleep(2)  # Pauses, but doesn't block
    print("Hello!")

asyncio.run(greet_after_delay())
```

## Output

```text
Starting...
Hello!
```

---

# What Changed?

Three important changes made this asynchronous:

1. `async def` instead of `def`
2. `await asyncio.sleep(2)` instead of `time.sleep(2)`
3. `asyncio.run()` starts the event loop

---

# Important Rule About `await`

`asyncio.sleep()` is itself an async function.

That means it **must** be called using `await`.

## Key Rule

Every async function must be awaited.

Examples:

```python
await asyncio.sleep(2)
await my_async_function()
```

If you forget `await`, the coroutine will not actually execute.

---

# Why Async Doesn't Seem Faster Yet

Right now the async version doesn't appear faster.

That's because only **one task** is running.

The real benefit appears when multiple coroutines run concurrently.

---

# Calling Async Functions Directly

You cannot call an async function like a normal function.

Example:

```python
result = greet_after_delay()

print(result)
print(type(result))
```

## Output

```text
<coroutine object greet_after_delay at 0x...>
<class 'coroutine'>
```

Calling an async function returns a **coroutine object**, not the final result.

You must use:

- `await`
- `asyncio.run()`

to execute it.

---

# How the Event Loop Works

The event loop is the engine behind async programming.

## Step-by-Step Flow

When running:

```python
asyncio.run(greet_after_delay())
```

this happens:

1. `asyncio.run()` creates an event loop
2. Event loop starts `greet_after_delay()`
3. `"Starting..."` prints
4. `await asyncio.sleep(2)` pauses coroutine
5. Event loop checks for other tasks
6. Sleep completes
7. Coroutine resumes
8. `"Hello!"` prints
9. Event loop exits

---

# Blocking vs Non-Blocking Example

```python
import asyncio
import time

# Blocking version
def greet_after_delay():
    print("Starting...")
    time.sleep(2)
    print("Hello!")

greet_after_delay()

# Async version
async def greet_after_delay(greet):
    print("Starting...", greet)
    await asyncio.sleep(2)
    print("Hello!", greet)
```

---

# Sequential Async Execution

```python
async def main():
    start = time.perf_counter()

    await greet_after_delay("Alice")
    await greet_after_delay("Bob")
    await greet_after_delay("Charlie")
    await greet_after_delay("David")

    end = time.perf_counter()

    print(f"Time taken: {end - start:.02f} seconds")
```

## Problem

This still runs sequentially.

Total time ≈ 8 seconds.

---

# Concurrent Execution with `asyncio.gather`

```python
async def main():
    start = time.perf_counter()

    await asyncio.gather(
        greet_after_delay("Alice"),
        greet_after_delay("Bob"),
        greet_after_delay("Charlie"),
        greet_after_delay("David")
    )

    end = time.perf_counter()

    print(f"Time taken: {end - start:.02f} seconds")

asyncio.run(main())
```

## Result

All four tasks run concurrently.

Total time ≈ 2 seconds instead of 8 seconds.

---

# Returning Values from Async Functions

```python
async def greet_after_delay(name):
    print(f"Starting... {name}")

    await asyncio.sleep(2)

    greeting = f"Hello, {name}!"

    print(greeting)

    return greeting
```

---

# Capturing Results with `asyncio.gather`

```python
async def main():
    start = time.perf_counter()

    greetings = await asyncio.gather(
        greet_after_delay("Alice"),
        greet_after_delay("Bob"),
        greet_after_delay("Charlie"),
        greet_after_delay("David")
    )

    end = time.perf_counter()

    print(f"Time taken: {end - start:.02f} seconds")
    print("Greetings:", greetings)

asyncio.run(main())
```

---

# Manual Event Loop Management

Older style:

```python
loop = asyncio.get_event_loop()
loop.run_until_complete(main())
```

Modern Python (3.7+) prefers:

```python
asyncio.run(main())
```

---

# Using `asyncio.create_task`

`create_task()` schedules coroutines independently.

```python
async def main():
    start = time.perf_counter()

    task1 = asyncio.create_task(greet_after_delay("Alice"))
    task2 = asyncio.create_task(greet_after_delay("Bob"))
    task3 = asyncio.create_task(greet_after_delay("Charlie"))
    task4 = asyncio.create_task(greet_after_delay("David"))

    greetings = await asyncio.gather(
        task1,
        task2,
        task3,
        task4
    )

    end = time.perf_counter()

    print(f"Time taken: {end - start:.02f} seconds")
    print("Greetings:", greetings)

asyncio.run(main())
```

---

# Exception Handling in Async Tasks

```python
async def greet_after_delay(name):
    print(f"Starting... {name}")

    await asyncio.sleep(2)

    if name == "Charlie":
        raise ValueError("An error occurred for Charlie!")

    greeting = f"Hello, {name}!"

    print(greeting)

    return greeting
```

---

# Catching Exceptions

```python
async def main():
    start = time.perf_counter()

    task1 = asyncio.create_task(greet_after_delay("Alice"))
    task2 = asyncio.create_task(greet_after_delay("Bob"))
    task3 = asyncio.create_task(greet_after_delay("Charlie"))
    task4 = asyncio.create_task(greet_after_delay("David"))

    try:
        greetings = await asyncio.gather(
            task1,
            task2,
            task3,
            task4
        )

    except ValueError as e:
        print(f"Caught an exception: {e}")

    end = time.perf_counter()

    print(f"Time taken: {end - start:.02f} seconds")

asyncio.run(main())
```

---

# Better Exception Handling

Instead of failing the whole task:

```python
async def greet_after_delay(name):
    print(f"Starting... {name}")

    await asyncio.sleep(2)

    if name == "Charlie":
        print("An error occurred for Charlie!")
        return f"Hello, {name}! (default greeting)"

    greeting = f"Hello, {name}!"

    print(greeting)

    return greeting
```

---

# Async HTTP Requests with `aiohttp`

```python
import aiohttp
import asyncio

async def fetch_data(session, url):
    async with session.get(url) as response:
        return await response.json()

async def main():
    urls = [
        "https://jsonplaceholder.typicode.com/posts/1",
        "https://jsonplaceholder.typicode.com/posts/2",
        "https://jsonplaceholder.typicode.com/posts/3",
    ]

    async with aiohttp.ClientSession() as session:
        tasks = [
            fetch_data(session, url)
            for url in urls
        ]

        results = await asyncio.gather(*tasks)

        for result in results:
            print(result)

asyncio.run(main())
```

---

# Simpler `aiohttp` Example

```python
async def main():
    urls = [
        "https://jsonplaceholder.typicode.com/posts/1",
        "https://jsonplaceholder.typicode.com/posts/2",
        "https://jsonplaceholder.typicode.com/posts/3",
    ]

    async with aiohttp.ClientSession() as session:
        tasks = [session.get(url) for url in urls]

        responses = await asyncio.gather(*tasks)

        results = [
            await response.json()
            for response in responses
        ]

        for result in results:
            print(result)

asyncio.run(main())
```

---

# Async Database Operations with `aiomysql`

```python
import aiomysql
import asyncio

async def fetch_users(pool):
    async with pool.acquire() as conn:
        async with conn.cursor() as cur:
            await cur.execute(
                "SELECT id, name FROM users"
            )

            return await cur.fetchall()

async def main():
    pool = await aiomysql.create_pool(
        host='localhost',
        port=3306,
        user='root',
        password='password',
        db='test_db'
    )

    users = await fetch_users(pool)

    for user in users:
        print(user)

    pool.close()

    await pool.wait_closed()

asyncio.run(main())
```

---

# Summary

Python's `asyncio` library provides powerful tools for asynchronous programming.

## Main Features

- `async def` for coroutines
- `await` for non-blocking pauses
- Event loop management
- Concurrent execution with `gather`
- Task scheduling with `create_task`
- Async HTTP requests
- Async database operations
- Exception handling in async workflows

---

# Best Use Cases for Asyncio

Asyncio is ideal for:

- APIs
- Web scraping
- Chat applications
- Streaming services
- Real-time systems
- Database-heavy applications
- High-concurrency servers

---

# Final Takeaway

Async programming improves performance by allowing multiple I/O-bound tasks to run concurrently without blocking the main thread.

By mastering:

- Coroutines
- `await`
- Event loops
- Tasks

you can build highly responsive and scalable Python applications.
````
