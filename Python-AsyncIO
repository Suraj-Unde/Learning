# Python's async system uses a few core concepts:
  Coroutines: Functions defined with async def instead of def. They can pause and resume execution, making them perfect for operations that involve waiting.
  await: This keyword tells Python, "pause this coroutine until this operation completes, but let other code run in the meantime."
  Event loop: The engine that manages all your coroutines, deciding which one to run and when to switch between them.
  Tasks: Coroutines wrapped for concurrent execution. You create them with asyncio.create_task() to run multiple operations at once.

# To avoid confusion, what async programming can (and can’t) do, please keep this in mind:
  Async works best with I/O-bound work like HTTP requests, database queries, and file operations, where your code waits for external systems.
  Async doesn't help with CPU-bound work such as complex calculations or data processing, where your code actively computes rather than waits.
import time

def greet_after_delay():
    print("Starting...")
    time.sleep(2)  # Blocks for 2 seconds
    print("Hello!")

greet_after_delay()

The function works, but time.sleep(2) blocks your entire program. Nothing else can run during those two seconds.
Now here's the async version:

import asyncio
async def greet_after_delay():
    print("Starting...")
    await asyncio.sleep(2)  # Pauses, but doesn't block
    print("Hello!")

asyncio.run(greet_after_delay())

output:

Starting...
Hello!

The output looks identical, but something different is happening under the hood. Three changes made this async:
async def instead of def declares this as a coroutine.
await asyncio.sleep(2) instead of time.sleep(2) pauses without blocking.
asyncio.run() starts the event loop and runs the coroutine.
Notice that asyncio.sleep() is itself an async function, which is why it needs await. This is a key rule: every async function must be called with await. Whether it's a built-in like asyncio.sleep() or one you write yourself, forgetting await means it won't actually execute.
Right now, the async version doesn't seem faster. That's because we only have one task. The real benefit shows up when you run multiple coroutines at once, which we'll cover in the next section.
Another important thing to know: you can't call an async function directly like a regular function. Let’s try it:

result = greet_after_delay()
print(result)
print(type(result))

Outpt:
<coroutine object greet_after_delay at 0x...>
<class 'coroutine'>

Calling greet_after_delay() returns a coroutine object, not the result. The function doesn't actually run. You need asyncio.run() or await to execute it inside another function.

How the event loop works
The event loop is the engine behind async programming. It manages your coroutines and decides what runs when. Here's what happens step-by-step when you run the async greet_after_delay() function:

asyncio.run() creates an event loop.
Event loop starts greet_after_delay().
"Starting..." prints.
Hits await asyncio.sleep(2) → coroutine pauses.
Event loop checks: "Any other tasks to run?" (none right now).
2 seconds pass, sleep completes.
Event loop resumes greet_after_delay().
"Hello!" prints.
Function finishes → event loop exits.

import asyncio
from os import sync
import time

# Normally, this would block the entire program for 2 seconds, preventing any other code from running during that time. 
# With asyncio, we can pause the function without blocking the entire program, allowing other tasks to run concurrently.
def greet_after_delay():
    print("Starting...")
    time.sleep(2)  # Blocks for 2 seconds
    print("Hello!")

greet_after_delay()

# This function simulates a time-consuming task by using asyncio.sleep, which is non-blocking. 
# It allows other tasks to run while waiting for the sleep to complete.
async def greet_after_delay(Greet):
    print("Starting... ", Greet)
    await asyncio.sleep(2)  # Pauses, but doesn't block
    print("Hello! .. ", Greet)

# If we run the greet_after_delay function multiple times sequentially, it will take 2 seconds for each call, resulting in a total of 8 seconds for four calls. 
# However, if we use asyncio.gather to run them concurrently, all four calls will start at the same time, and the total time taken will be just over 2 seconds, demonstrating the efficiency of asynchronous programming.
async def main():
    start = time.perf_counter()
    await greet_after_delay("Alice")
    await greet_after_delay("Bob")
    await greet_after_delay("Charlie")
    await greet_after_delay("David")
    end = time.perf_counter()
    print(f"Time taken: {end - start:.02f} seconds")

# To run the main function, we use asyncio.run, which sets up the event loop and executes the asynchronous code.
# This will run the main function, which in turn runs the greet_after_delay function for each name concurrently, 
# demonstrating how asynchronous programming allows for efficient execution of tasks that would otherwise block the program.
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



# Now lets see how to capture the result of an asynchronous function. 
# We can use the return statement in an async function, and when we await that function, it will give us the returned value.

# In this example, the greet_after_delay function returns a greeting message after the delay. 
# When we use asyncio.gather to run multiple instances of greet_after_delay concurrently, we can capture their results in a list called greetings. 
# This allows us to see the output of each asynchronous function call once they have completed.
async def greet_after_delay(name):
    print(f"Starting... {name}")
    await asyncio.sleep(2)  # Simulate a time-consuming task
    greeting = f"Hello, {name}!"
    print(greeting)
    return greeting

# In this example, we run the greet_after_delay function for four different names concurrently using asyncio.gather. 
# The results of each function call are captured in the greetings list, which is printed at the end along with the total time taken to execute all tasks.
async def main():
    start = time.perf_counter()
    # We can capture the results of the asynchronous function calls in a list.
    greetings = await asyncio.gather(
        greet_after_delay("Alice"),
        greet_after_delay("Bob"),
        greet_after_delay("Charlie"),
        greet_after_delay("David")
    )
    end = time.perf_counter()
    print(f"Time taken: {end - start:.02f} seconds")
    print("Greetings:", greetings)

# When we run the main function, it will execute all the greet_after_delay calls concurrently, and we will see the greetings printed after the delay, 
# along with the total time taken and the list of greetings.    
asyncio.run(main())



# Now lets see the python asyncio event loop in action. The event loop is responsible for managing and scheduling the execution of asynchronous tasks. 
# It allows us to run multiple tasks concurrently without blocking the main thread.
# In this example, we create an event loop and run the main function using loop.run_until_complete. 
# The event loop will manage the execution of the asynchronous tasks defined in the main function, allowing them to run concurrently and efficiently.
# loop = asyncio.get_event_loop()
# loop.run_until_complete(main())   


# In modern Python versions (3.7 and above), we typically use asyncio.run to execute the main function, which internally creates and manages the event loop for us. 
# However, if you want to see how to manually create and manage the event loop, you can use the following code:
# loop = asyncio.get_event_loop()
# loop.run_until_complete(main())   

# Now lets explore Python asyncio create_task function. The create_task function is used to schedule the execution of a coroutine (an async function) as a separate task in the event loop. 
# This allows you to run multiple coroutines concurrently without blocking each other.
# In this example, we use asyncio.create_task to schedule the execution of the greet_after_delay function for each name. 
# This allows all the tasks to run concurrently, and we can await their completion using asyncio.gather. 
# The create_task function is useful when you want to start a coroutine without immediately awaiting its result, allowing other tasks to run in the meantime.

async def main():
    start = time.perf_counter()
    taskList = []
    # We can use create_task to schedule the execution of the coroutines.
    task1 = asyncio.create_task(greet_after_delay("Alice"))
    task2 = asyncio.create_task(greet_after_delay("Bob"))
    task3 = asyncio.create_task(greet_after_delay("Charlie"))
    task4 = asyncio.create_task(greet_after_delay("David"))
    
    # different ways to use the tasklist or create_task function
    # taskList.append(asyncio.create_task(greet_after_delay("Alice")))
    # taskList.append(asyncio.create_task(greet_after_delay("Bob")))
    # taskList.append(asyncio.create_task(greet_after_delay("Charlie")))
    # taskList.append(asyncio.create_task(greet_after_delay("David")))

    # Await the completion of all tasks
    #greetings = await asyncio.gather(*taskList)
    greetings = await asyncio.gather(task1, task2, task3, task4)
    
    end = time.perf_counter()
    print(f"Time taken: {end - start:.02f} seconds")
    print("Greetings:", greetings)

asyncio.run(main())



# Now we have fair understanding of the basics of Python's asyncio library, including how to define asynchronous functions, 
# run them concurrently, capture their results, and manage the event loop.
# With this knowledge, you can start building more complex asynchronous applications that can handle multiple tasks efficiently without blocking the main thread.   
# You can explore more advanced features of asyncio, such as handling exceptions in asynchronous tasks, using locks and semaphores for synchronization, 
# and integrating with other libraries that support asynchronous programming.

# Remember that asynchronous programming can greatly improve the performance of your applications, especially when dealing with I/O-bound tasks, 
# such as network requests, file operations, or database interactions. By leveraging asyncio, 
# you can create responsive and efficient applications that can handle multiple tasks concurrently without blocking the user interface or other critical operations.

# Now lets see how to handle exceptions in asynchronous tasks. When an exception occurs in an asynchronous task, it can be caught and handled using try-except blocks within the coroutine.
# In this example, we modify the greet_after_delay function to raise an exception if the name is "Charlie". We then use a try-except block to catch and handle the exception when awaiting the task.
async def greet_after_delay(name):  
    print(f"Starting... {name}")
    await asyncio.sleep(2)  # Simulate a time-consuming task
    if name == "Charlie":
        raise ValueError("An error occurred for Charlie!")
    greeting = f"Hello, {name}!"
    print(greeting)
    return greeting

async def main():
    start = time.perf_counter()
    task1 = asyncio.create_task(greet_after_delay("Alice"))
    task2 = asyncio.create_task(greet_after_delay("Bob"))
    task3 = asyncio.create_task(greet_after_delay("Charlie"))
    task4 = asyncio.create_task(greet_after_delay("David"))
    
    try:
        greetings = await asyncio.gather(task1, task2, task3, task4)
    except ValueError as e:
        print(f"Caught an exception: {e}")
    
    end = time.perf_counter()
    print(f"Time taken: {end - start:.02f} seconds")
    print("Greetings:", greetings)
    
asyncio.run(main())

# In this example, when the greet_after_delay function raises a ValueError for "Charlie", the exception is caught in the main function's try-except block, 
# and an error message is printed.
# The other tasks for "Alice", "Bob", and "David" will still complete successfully, and their greetings will be printed along with the time taken.
# This demonstrates how to handle exceptions in asynchronous tasks without affecting the execution of other concurrent tasks.

# In summary, Python's asyncio library provides powerful tools for writing asynchronous code that can run concurrently without blocking the main thread.
# By using async functions, await statements, and the event loop, you can create efficient applications that can handle multiple tasks simultaneously, 
# improving performance and responsiveness.


# Now we have a good understanding of how to use Python's asyncio library to write asynchronous code. 
# We have seen how to define async functions, run them concurrently, capture their results, manage the event loop, and handle exceptions in asynchronous tasks.
# With this knowledge, you can start building more complex asynchronous applications that can efficiently handle multiple tasks without blocking the main thread.  

# lets write the complete Theory for Python's asyncio library in a single code snippet for better understanding.
# Python's asyncio library is a powerful tool for writing asynchronous code in Python. It allows you to run multiple tasks concurrently without blocking the main thread, 
# making it ideal for I/O-bound tasks such as network requests, file operations, and database interactions.
# To define an asynchronous function, you use the async keyword before the function definition. 
# Inside an async function, you can use the await keyword to pause the function until a certain operation is complete, allowing other tasks to run in the meantime.
# You can run multiple async functions concurrently using asyncio.gather, which allows you to execute several coroutines at the same time and wait for all of them to complete. 
# The results of the coroutines can be captured in a list, which is returned by asyncio.gather.
# The event loop is responsible for managing and scheduling the execution of asynchronous tasks. 
# You can create and manage the event loop manually using asyncio.get_event_loop and loop.run_until_complete, or you can use asyncio.run to execute your main async function, 
# which will handle the event loop for you.
# The create_task function allows you to schedule the execution of a coroutine as a separate task in the event loop, 
# enabling you to run multiple coroutines concurrently without blocking each other. 
# You can await the completion of these tasks using asyncio.gather or by awaiting each task individually.
# When an exception occurs in an asynchronous task, it can be caught and handled using try-except blocks within the coroutine. 
# This allows you to manage errors gracefully without affecting the execution of other concurrent tasks.
# Overall, Python's asyncio library provides a powerful and efficient way to write asynchronous code that can handle multiple tasks concurrently, 
# improving the performance and responsiveness of your applications. 
# By leveraging asyncio, you can create responsive applications that can efficiently manage I/O-bound tasks without blocking the user interface or other critical operations.

# In summary, Python's asyncio library is a powerful tool for writing asynchronous code that can run concurrently without blocking the main thread.
# By using async functions, await statements, and the event loop, you can create efficient applications that can handle multiple tasks simultaneously, improving performance and responsiveness.

# Now we have a good understanding of how to use Python's asyncio library to write asynchronous code. 
# We have seen how to define async functions, run them concurrently, capture their results, manage the event loop, and handle exceptions in asynchronous tasks.
# With this knowledge, you can start building more complex asynchronous applications that can efficiently handle multiple tasks without blocking the main thread.   


# Lets see everything we have learned about Python's asyncio library in a single code snippet for better understanding.
async def greet_after_delay(name):  
    print(f"Starting... {name}")
    await asyncio.sleep(2)  # Simulate a time-consuming task
    if name == "Charlie":
        raise ValueError("An error occurred for Charlie!")
    greeting = f"Hello, {name}!"
    print(greeting)
    return greeting

async def main():
    start = time.perf_counter()
    task1 = asyncio.create_task(greet_after_delay("Alice"))
    task2 = asyncio.create_task(greet_after_delay("Bob"))
    task3 = asyncio.create_task(greet_after_delay("Charlie"))
    task4 = asyncio.create_task(greet_after_delay("David"))
    
    try:
        greetings = await asyncio.gather(task1, task2, task3, task4)
    except ValueError as e:
        print(f"Caught an exception: {e}")
    
    end = time.perf_counter()
    print(f"Time taken: {end - start:.02f} seconds")
    print("Greetings:", greetings)

asyncio.run(main()) 

# Above code is failing with an exception because the greet_after_delay function raises a ValueError when the name is "Charlie". 
# The exception is caught in the main function's try-except block, and an error message is printed.
# The other tasks for "Alice", "Bob", and "David" will still complete successfully but their greetings will not be printed because, 
# the exception occurs before the greetings are returned.
# To handle this situation, you can modify the greet_after_delay function to return a default greeting instead of raising an exception, 
# or you can handle the exception within the greet_after_delay function itself to ensure that all greetings are returned regardless of errors.

# Now lets see the more advanced way to handle exceptions in asynchronous tasks. Instead of allowing the exception to propagate to the main function, 
# we can catch and handle the exception within the greet_after_delay function itself. This way, we can ensure that all greetings are returned regardless of errors, and we can provide a default greeting for any names that cause exceptions.

async def greet_after_delay(name):  
    print(f"Starting... {name}")
    await asyncio.sleep(2)  # Simulate a time-consuming task
    if name == "Charlie":
        print("An error occurred for Charlie! Returning default greeting.")
        return f"Hello, {name}! (default greeting)"
    greeting = f"Hello, {name}!"
    print(greeting)
    return greeting 

async def main():
    start = time.perf_counter()
    task1 = asyncio.create_task(greet_after_delay("Alice"))
    task2 = asyncio.create_task(greet_after_delay("Bob"))
    task3 = asyncio.create_task(greet_after_delay("Charlie"))
    task4 = asyncio.create_task(greet_after_delay("David"))
    
    greetings = await asyncio.gather(task1, task2, task3, task4)
    
    end = time.perf_counter()
    print(f"Time taken: {end - start:.02f} seconds")
    print("Greetings:", greetings)      
    
asyncio.run(main())


# Python/s requests library is a popular HTTP client library for making synchronous HTTP requests. However, it does not support asynchronous programming out of the box.
# So lets see the more advanced use of python's asyncio library by integrating it with an HTTP client library like aiohttp to make asynchronous HTTP requests.
# In this example, we will use aiohttp to make asynchronous HTTP requests to a sample API and capture the results.
import aiohttp
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
        tasks = [fetch_data(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
        for result in results:
            print(result)

asyncio.run(main())

# In this example, we define an asynchronous function fetch_data that takes an aiohttp session and a URL as parameters. It makes an asynchronous GET request to the specified URL and returns the JSON response. 
# In the main function, we create a list of URLs to fetch data from. We then create an aiohttp ClientSession and use a list comprehension to create a list of tasks for fetching data from each URL.
# We use asyncio.gather to run all the tasks concurrently and capture their results in the results list. Finally, we print the results, which contain the JSON responses from each of the URLs. 
# This demonstrates how to integrate Python's asyncio library with an HTTP client library like aiohttp to make asynchronous HTTP requests and handle the responses efficiently.
# now see more simple way to make asynchronous HTTP requests using aiohttp without defining a separate fetch_data function.
async def main():
    urls = [
        "https://jsonplaceholder.typicode.com/posts/1",
        "https://jsonplaceholder.typicode.com/posts/2",
        "https://jsonplaceholder.typicode.com/posts/3",
    ]
    async with aiohttp.ClientSession() as session:
        tasks = [session.get(url) for url in urls]
        responses = await asyncio.gather(*tasks)
        results = [await response.json() for response in responses]
        for result in results:
            print(result)

asyncio.run(main())


# Now lets see how to use asyncio with a database library like aiomysql to perform asynchronous database operations.
import aiomysql

async def fetch_users(pool):
    async with pool.acquire() as conn:
        async with conn.cursor() as cur:
            await cur.execute("SELECT id, name FROM users")
            return await cur.fetchall()
        
async def main():
    pool = await aiomysql.create_pool(host='localhost', port=3306, user='root', password='password', db='test_db')
    users = await fetch_users(pool)
    for user in users:
        print(user)
    pool.close()
    await pool.wait_closed()            
    
    
asyncio.run(main())    


# In this example, we define an asynchronous function fetch_users that takes an aiomysql connection pool as a parameter. It acquires a connection from the pool, creates a cursor, executes a SQL query to fetch user data, and returns the results.
# In the main function, we create a connection pool to the MySQL database using aiomysql.create_pool. We then call the fetch_users function to retrieve the user data and print it. Finally, we close the connection pool and wait for it to be fully closed.
# This demonstrates how to use Python's asyncio library with a database library like aiomysql to perform asynchronous database operations, allowing you to efficiently handle database interactions without blocking the main thread.        
