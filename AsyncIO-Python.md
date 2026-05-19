# Python Asyncio — Complete Theory & Practical Guide

---

# Introduction to Async Programming

Traditional Python programs execute code **line by line** in a sequential manner.

Example:

```python
task1()
task2()
task3()
```

Each task waits for the previous task to finish before starting.

This approach works well for many programs, but it becomes inefficient when the program spends a lot of time **waiting** for external operations such as:

- Network requests
- API calls
- Database queries
- File reading/writing

During these waiting periods, the CPU often sits idle.

---

# What is Async Programming?

Asynchronous programming allows a program to:

- Pause long waiting operations
- Continue running other tasks meanwhile
- Resume paused tasks later

Instead of blocking the entire program while waiting, async programming allows efficient multitasking inside a single thread.

---

# Why Asyncio Exists

Python introduced the `asyncio` library to make asynchronous programming easier and more structured.

`asyncio` provides:

- Coroutines
- Event loops
- Task scheduling
- Concurrent execution
- Async networking support

It is especially useful for **I/O-bound applications**.

---

# I/O-Bound vs CPU-Bound Tasks

Understanding this difference is extremely important.

---

# I/O-Bound Tasks

These tasks spend most of their time waiting for external systems.

Examples:

- HTTP requests
- Downloading files
- Database operations
- Reading files
- Waiting for user input

## Perfect for Asyncio

Because while one task waits, another task can run.

---

# CPU-Bound Tasks

These tasks actively use the processor.

Examples:

- Image processing
- Video rendering
- Machine learning training
- Heavy calculations
- Encryption algorithms

## Asyncio Does NOT Improve These

Because the CPU is already fully busy.

For CPU-bound work, use:

- Multiprocessing
- Parallel computing
- Native extensions

---

# Core Concepts of Python Asyncio

Asyncio is built around four major concepts:

1. Coroutines
2. `await`
3. Event Loop
4. Tasks

---

# 1. Coroutines

Coroutines are special functions that can:

- Pause execution
- Resume later
- Cooperate with the event loop

They are defined using:

```python
async def
```

---

# Normal Function vs Coroutine

## Normal Function

```python
def greet():
    return "Hello"
```

Runs immediately from start to finish.

---

## Coroutine

```python
async def greet():
    return "Hello"
```

Does NOT execute immediately.

Instead, it returns a coroutine object.

---

# Important Theory About Coroutines

A coroutine is basically:

> "A function that can temporarily give control back to the event loop."

This allows multiple coroutines to share execution time efficiently.

Coroutines are lightweight compared to threads.

---

# Example Coroutine

```python
import asyncio

async def greet():
    print("Hello")
```

Calling it:

```python
greet()
```

does NOT run the function.

It creates a coroutine object.

---

# Executing Coroutines

To actually execute a coroutine, you need:

- `await`
- `asyncio.run()`
- Task scheduling

Example:

```python
asyncio.run(greet())
```

---

# 2. Understanding `await`

The `await` keyword pauses a coroutine until another async operation finishes.

Example:

```python
await asyncio.sleep(2)
```

---

# Theory Behind `await`

When Python reaches `await`:

1. The coroutine pauses
2. Control returns to the event loop
3. Other tasks can run
4. The paused coroutine resumes later

This is the foundation of asynchronous multitasking.

---

# Blocking vs Non-Blocking

---

# Blocking Code

```python
import time

time.sleep(5)
```

The entire program freezes for 5 seconds.

Nothing else can execute.

---

# Non-Blocking Code

```python
await asyncio.sleep(5)
```

The coroutine pauses, but the event loop continues running other tasks.

This is why async applications scale efficiently.

---

# Key Rule of `await`

You can only use `await`:

- Inside an async function
- With awaitable objects

Examples of awaitables:

- Coroutines
- Tasks
- Futures

---

# Common Beginner Mistake

```python
asyncio.sleep(2)
```

Without `await`, this does nothing useful.

Correct:

```python
await asyncio.sleep(2)
```

---

# 3. The Event Loop

The event loop is the heart of asyncio.

It continuously:

- Tracks running coroutines
- Schedules execution
- Pauses/resumes tasks
- Handles I/O events

---

# Theory of the Event Loop

Think of the event loop as a manager.

It asks:

- Which coroutine is ready?
- Which coroutine is waiting?
- Which task should run next?

It efficiently switches between tasks whenever coroutines pause.

---

# Event Loop Workflow

Example:

```python
asyncio.run(main())
```

Internally:

1. Create event loop
2. Start coroutine
3. Run until `await`
4. Pause coroutine
5. Run another coroutine
6. Resume paused coroutine later
7. Repeat until complete

---

# Simple Event Loop Example

```python
import asyncio

async def greet():
    print("Start")
    await asyncio.sleep(2)
    print("End")

asyncio.run(greet())
```

---

# Behind the Scenes

Execution flow:

```text
Event loop starts
↓
greet() begins
↓
Hits await asyncio.sleep(2)
↓
Coroutine pauses
↓
Event loop waits or runs other tasks
↓
2 seconds complete
↓
Coroutine resumes
↓
Program exits
```

---

# 4. Tasks

Tasks allow coroutines to run concurrently.

Created using:

```python
asyncio.create_task()
```

---

# Theory Behind Tasks

A task is:

> "A coroutine wrapped and scheduled by the event loop."

Tasks run independently in the background.

This enables concurrency.

---

# Why Tasks Matter

Without tasks:

```python
await task1()
await task2()
```

runs sequentially.

With tasks:

```python
t1 = asyncio.create_task(task1())
t2 = asyncio.create_task(task2())
```

both run concurrently.

---

# Sequential Execution Example

```python
import asyncio
import time

async def work(name):
    print(f"Starting {name}")
    await asyncio.sleep(2)
    print(f"Finished {name}")

async def main():
    start = time.perf_counter()

    await work("A")
    await work("B")
    await work("C")

    end = time.perf_counter()

    print(end - start)

asyncio.run(main())
```

---

# Theory

Each task waits for the previous one.

Total time:

```text
2 + 2 + 2 = 6 seconds
```

---

# Concurrent Execution Example

```python
async def main():
    start = time.perf_counter()

    await asyncio.gather(
        work("A"),
        work("B"),
        work("C")
    )

    end = time.perf_counter()

    print(end - start)
```

---

# Theory Behind `asyncio.gather`

`gather()` schedules multiple coroutines concurrently.

All tasks begin nearly simultaneously.

Total runtime becomes approximately:

```text
2 seconds
```

instead of 6 seconds.

---

# Understanding Concurrency

Concurrency does NOT mean parallel execution on multiple CPUs.

Instead:

- One task pauses
- Another task runs
- Tasks share execution time

This is called:

> Cooperative multitasking

---

# Asyncio vs Threading

---

# Asyncio

- Single-threaded
- Event-loop based
- Lightweight
- Excellent for I/O tasks

---

# Threading

- Multiple threads
- OS-managed
- Heavier memory usage
- Better for blocking libraries

---

# Asyncio Advantages

- Lower memory overhead
- Massive scalability
- Thousands of simultaneous connections
- Cleaner concurrency model

---

# Returning Values from Async Functions

Async functions can return values normally.

Example:

```python
async def greet(name):
    await asyncio.sleep(1)
    return f"Hello {name}"
```

---

# Collecting Results

```python
results = await asyncio.gather(
    greet("Alice"),
    greet("Bob")
)
```

Output:

```python
['Hello Alice', 'Hello Bob']
```

---

# Exception Handling in Asyncio

Async tasks can raise exceptions like normal functions.

---

# Example

```python
async def risky():
    raise ValueError("Something went wrong")
```

---

# Catching Exceptions

```python
try:
    await risky()
except ValueError as e:
    print(e)
```

---

# Exception Behavior in `gather`

By default:

- One failing task can stop the whole gather operation

Safer version:

```python
await asyncio.gather(
    task1(),
    task2(),
    return_exceptions=True
)
```

This prevents one failure from cancelling everything.

---

# Async HTTP Requests with `aiohttp`

`requests` library is synchronous.

For async networking, use:

```python
aiohttp
```

---

# Why `aiohttp` Matters

It allows:

- Multiple simultaneous HTTP requests
- Non-blocking API communication
- Faster web scraping
- Better scalability

---

# Example

```python
import aiohttp
import asyncio

async def fetch(session, url):
    async with session.get(url) as response:
        return await response.text()

async def main():
    urls = [
        "https://example.com",
        "https://google.com"
    ]

    async with aiohttp.ClientSession() as session:
        tasks = [
            fetch(session, url)
            for url in urls
        ]

        results = await asyncio.gather(*tasks)

        print(results)

asyncio.run(main())
```

---

# Theory of Async Networking

Instead of:

```text
Request 1 → wait → Request 2 → wait
```

asyncio performs:

```text
Request 1
Request 2
Request 3
↓
Wait together
↓
Collect results
```

This dramatically improves performance.

---

# Async Database Operations

Traditional database queries block execution.

Async database libraries solve this.

Examples:

- `aiomysql`
- `asyncpg`
- `motor` (MongoDB)

---

# Why Async Databases Matter

Useful for:

- Web APIs
- Real-time systems
- Chat applications
- High-concurrency servers

---

# Example with `aiomysql`

```python
import aiomysql

async def fetch_users(pool):
    async with pool.acquire() as conn:
        async with conn.cursor() as cur:
            await cur.execute(
                "SELECT * FROM users"
            )

            return await cur.fetchall()
```

---

# Theory Behind Async DB Queries

While waiting for the database response:

- Other coroutines continue running
- Server responsiveness improves
- Throughput increases

---

# Best Practices for Asyncio

---

# 1. Use Async Only When Needed

Asyncio is best for:

- Network-heavy apps
- APIs
- High concurrency

Avoid unnecessary complexity.

---

# 2. Avoid Blocking Calls

Never use blocking functions inside async code:

```python
time.sleep()
requests.get()
```

Use async alternatives instead.

---

# 3. Use `asyncio.gather` Carefully

Large numbers of tasks can consume memory.

Use batching if necessary.

---

# 4. Handle Exceptions Properly

One failing coroutine can affect others.

Always use:

```python
try/except
```

or:

```python
return_exceptions=True
```

---

# 5. Understand Cooperative Multitasking

Coroutines must voluntarily pause using `await`.

Without `await`, other tasks cannot run.

---

# Real-World Applications of Asyncio

Asyncio powers many modern systems:

- FastAPI
- WebSocket servers
- Discord bots
- Telegram bots
- Streaming platforms
- Real-time dashboards
- Chat systems
- Multiplayer game servers

---

# Final Summary

Python's asyncio library enables efficient asynchronous programming using:

- Coroutines
- `await`
- Event loops
- Tasks

It is ideal for:

- I/O-bound operations
- High-concurrency systems
- Network applications

Asyncio improves:

- Responsiveness
- Scalability
- Resource efficiency

while avoiding unnecessary thread overhead.

---

# Final Takeaway

The most important concept to remember is:

> Asyncio does not make code magically faster.

Instead:

- It prevents waiting time from being wasted.
- It allows multiple waiting operations to progress together.

That is the true power of asynchronous programming.


# References 
Check following links:
https://www.datacamp.com/tutorial/python-async-programming
https://www.datacamp.com/tutorial/asyncio-introduction
