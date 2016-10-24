# Random Notes on Async Process.

# Multi-Threading
- Each task that a process performs is a series of operations. The processing of these series of operations is called execution of a thread. A process can distribute tasks to multiple threads. (It creates threads by calling a function exposed by the OS. The OS creates, manages and schedules threads). 
- The operating system usually ends up creating a much larger number of threads than the number of cores on the computer. To be able to handle so many threads, each core has to serve multiple threads. The operating system assigns a thread to be executed on a core for some time, and then uses the same core for another thread. This process is called scheduling, and the duration for which a thread is assigned to a processor/core is called a time slice. When the time slice for a thread is over, it is pre-empted (taken off the processor) and put aside for scheduling. Whenever the thread gets another time slice for itself, it resumes from where it had been pre-empted in the last run.
- Problem while writing a multi-threaded application.
    -   The process might slow down due to lock (to avoid race condition)
    -   The process might hang up. (Let us say there are two variables which are protected using different mutexes. Now suppose there are two threads operating on the variables.)
    -   Other synchronization methods to avoid race conditions are Semaphores and Condition Variables and even more higher level of synchronisation techniques provided by languages and operating systems
- Understanding SMP hardware architecture. https://techtake.info/2016/10/13/symmetric-multi-processing-multi-threading-and-synchronisation-explained/#hw_arch

# Process
- Each process provides the resources needed to execute a program.
- A process has a virtual address space, executable code, a security context, a unique process identifier, environment variables, a priority class, minimum and maximum working set sizes, and **at least one thread of execution**.
- Each process is started with a single thread, often called the primary thread, but can create additional threads from any of its threads.

# Thread
- A **thread is the entity within a process** that can be scheduled for execution.
- All threads of a process share its virtual address space and system resources. In addition, each thread maintains exception handlers, a scheduling priority, thread local storage, a unique thread identifier, and a set of structures the system will use to save the **thread context** until it is scheduled.
- The **thread context** includes the thread's set of machine registers, the kernel stack, a thread environment block, and a user stack in the address space of the thread's process. Threads can also have their own security context, which can be used for impersonating clients.

# Coroutine
- Coroutines are cooperative functions. Instead of being run on kernel threads and scheduled by the operating system, they run in a single thread until they yield or finish, yielding to other functions as determined by the programmer.
- Coroutines are computer program components that generalize subroutines for **nonpreemptive multitasking**, by allowing multiple entry points for suspending and resuming execution at certain locations.
- Coroutines are well-suited for implementing more familiar program components such as **cooperative tasks, exceptions, event loop, iterators, infinite lists and pipes**.

# Event loop
- Event loop is a queue of events and a loop that just constantly pulls events off the queue and runs them. These jobs are called coroutines. They are a small set of instructions, including which events to put back on to the queue, if any.
- A Green Thread is a thread that is scheduled by a virtual machine (VM) instead of natively by the underlying operating system. Green threads emulate multithreaded environments without relying on any native OS capabilities, and they are managed in user space instead of kernel space, enabling them to work in environments that do not have native thread support.
- Includes all the problems of normal thread-based programming other than CPU context switching

# How NGINX works
- Each worker (there’s usually one worker for each CPU core)  waits for events on the listen and connection sockets.
-  When event occurs on the socket, worker handles them. The worker creates a new connection socket and respond immediately.
-  NGINX scales very well to support hundreds of thousands of connections per worker process. **Each new connection creates another file descriptor and consumes a small amount of additional memory in the worker process**. There is very little additional overhead per connection. NGINX processes can remain pinned to CPUs. Context switches are relatively infrequent and occur when there is no work to be done.
-   In the blocking, connection-per-process approach, each connection requires a large amount of additional resources and overhead, and context switches (swapping from one process to another) are very frequent.

## Nginx worker model
As previously mentioned, nginx doesn't spawn a process or thread for every connection. Instead, worker processes accept new requests from a shared "listen" socket and execute a highly efficient `run-loop` inside each worker to process thousands of connections per worker. 

The run-loop is the most complicated part of the nginx worker code. It includes comprehensive inner calls and relies heavily on the idea of asynchronous task handling. **Asynchronous operations are implemented through modularity, event notifications, extensive use of callback functions and fine-tuned timers**. Overall, the key principle is to be as non-blocking as possible. The only situation where nginx can still block is when there's not enough disk storage performance for a worker process.

Because nginx does not fork a process or thread per connection, memory usage is very conservative and extremely efficient in the vast majority of cases. nginx conserves CPU cycles as well because there's no ongoing create-destroy pattern for processes or threads. **What nginx does is check the state of the network and storage, initialize new connections, add them to the run-loop, and process asynchronously until completion, at which point the connection is deallocated and removed from the run-loop**.

Because nginx spawns several workers to handle connections, it scales well across multiple cores. Generally, a separate worker per core allows full utilization of multicore architectures, and prevents thread thrashing and lock-ups. There's no resource starvation and the resource controlling mechanisms are isolated within single-threaded worker processes. This model also allows more scalability across physical storage devices, facilitates more disk utilization and avoids blocking on disk I/O. As a result, server resources are utilized more efficiently with the workload shared across several workers.


## These are contents from
- http://msdn.microsoft.com/en-us/library/windows/desktop/ms681917%28v=vs.85%29.aspx
- https://hackernoon.com/asynchronous-python-45df84b82434#.nh342eh1r
- https://en.wikipedia.org/wiki/Coroutine
- http://stackoverflow.com/questions/1934715/difference-between-a-coroutine-and-a-thread#1934718
- http://www.aosabook.org/en/nginx.html
- https://www.nginx.com/resources/library/infographic-inside-nginx/
- https://dzone.com/articles/inside-nginx-how-we-designed

## Other good resource on this
- http://stackoverflow.com/questions/11883214/efficient-handling-of-long-running-http-connections-in-an-nginx-gunicorn-django
