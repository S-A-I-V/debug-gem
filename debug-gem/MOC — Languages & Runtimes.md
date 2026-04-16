# MOC — Languages & Runtimes

> Domain-specific error patterns for programming languages and their runtimes. Each language section covers common errors, unique traps, debugging tools, and the failure patterns that language is most susceptible to.

---

## [[Python]]

### Common Errors & Messages
- `SyntaxError: invalid syntax` → [[Syntax & Parse Errors]]: Missing colon, bracket, or keyword. Check the line BEFORE the reported line.
- `IndentationError: unexpected indent` → [[Syntax & Parse Errors]]: Mixed tabs/spaces or wrong indentation level.
- `TabError: inconsistent use of tabs and spaces` → [[Syntax & Parse Errors]]: Use spaces only. Configure editor.
- `NameError: name 'X' is not defined` → [[Null & Undefined Reference Errors]]: Typo in variable name, not imported, or out of scope.
- `AttributeError: 'NoneType' object has no attribute 'X'` → [[Null & Undefined Reference Errors]]: Function returned None, variable is None.
- `AttributeError: 'X' object has no attribute 'Y'` → [[Type & Casting Errors]]: Wrong object type, or method doesn't exist on this class.
- `TypeError: unsupported operand type(s) for +: 'int' and 'str'` → [[Type & Casting Errors]]: Mixing types without conversion.
- `TypeError: X() takes Y positional arguments but Z were given` → [[Type & Casting Errors]]: Wrong number of arguments.
- `TypeError: X() got an unexpected keyword argument 'Y'` → [[Type & Casting Errors]]: Typo in kwarg or API changed.
- `TypeError: 'NoneType' object is not iterable` → [[Null & Undefined Reference Errors]]: Iterating over None.
- `TypeError: 'NoneType' object is not subscriptable` → [[Null & Undefined Reference Errors]]: Indexing into None.
- `TypeError: 'NoneType' object is not callable` → [[Null & Undefined Reference Errors]]: Calling None as function.
- `TypeError: Object of type X is not JSON serializable` → [[Serialization & Encoding Errors]]: Custom object needs serializer.
- `TypeError: unhashable type: 'list'` → [[Type & Casting Errors]]: Using mutable type as dict key or set element.
- `TypeError: 'int' object is not iterable` → [[Type & Casting Errors]]: Forgot to wrap in range() or list.
- `ValueError: invalid literal for int() with base 10` → [[Type & Casting Errors]]: String can't be converted to int.
- `ValueError: too many values to unpack` → [[Offset & Boundary Errors]]: Tuple/list unpacking size mismatch.
- `ValueError: not enough values to unpack` → [[Offset & Boundary Errors]]: Same as above, other direction.
- `ValueError: I/O operation on closed file` → [[State & Lifecycle Errors]]: File handle used after close.
- `ValueError: math domain error` → [[Offset & Boundary Errors]]: sqrt of negative, log of zero.
- `IndexError: list index out of range` → [[Offset & Boundary Errors]]: Accessing index >= len(list).
- `IndexError: string index out of range` → [[Offset & Boundary Errors]]: Same for strings.
- `KeyError: 'X'` → [[Offset & Boundary Errors]] or [[Configuration & Environment Errors]]: Dict key doesn't exist. Use `.get(key, default)`.
- `ModuleNotFoundError: No module named 'X'` → [[Dependency & Import Errors]]: Package not installed or venv not activated.
- `ImportError: cannot import name 'X' from 'Y'` → [[Dependency & Import Errors]]: Name doesn't exist in module, or circular import.
- `ImportError: DLL load failed` → [[Dependency & Import Errors]]: Missing system library (Windows).
- `ImportError: libX.so: cannot open shared object file` → [[Dependency & Import Errors]]: Missing system library (Linux).
- `RecursionError: maximum recursion depth exceeded` → [[Resource Exhaustion Errors]]: Infinite recursion or very deep recursion. Increase `sys.setrecursionlimit()` or convert to iterative.
- `MemoryError` → [[Resource Exhaustion Errors]]: Loading too much data into memory. Use generators, chunked reading, or Dask/Polars.
- `OSError: [Errno 24] Too many open files` → [[Resource Exhaustion Errors]]: File descriptor leak. Use context managers (`with` statement).
- `OSError: [Errno 28] No space left on device` → [[Resource Exhaustion Errors]]: Disk full.
- `OSError: [Errno 13] Permission denied` → [[Permission & Authorization Errors]]: File/directory permission issue.
- `FileNotFoundError: [Errno 2] No such file or directory` → [[Configuration & Environment Errors]]: Wrong path, file doesn't exist.
- `ConnectionRefusedError` → [[Connection & Network Errors]]: Target service not running.
- `ConnectionResetError` → [[Connection & Network Errors]]: Server dropped connection.
- `TimeoutError` → [[Connection & Network Errors]]: Operation took too long.
- `UnicodeDecodeError: 'utf-8' codec can't decode byte` → [[Serialization & Encoding Errors]]: File isn't UTF-8. Specify correct encoding.
- `UnicodeEncodeError: 'ascii' codec can't encode character` → [[Serialization & Encoding Errors]]: Trying to write Unicode to ASCII stream.
- `json.decoder.JSONDecodeError` → [[Serialization & Encoding Errors]]: Invalid JSON string.
- `pickle.UnpicklingError` → [[Serialization & Encoding Errors]]: Pickle version mismatch or corrupted data.
- `RuntimeError: Event loop is closed` → [[State & Lifecycle Errors]]: asyncio event loop used after shutdown.
- `RuntimeError: cannot reuse already awaited coroutine` → [[State & Lifecycle Errors]]: Coroutine can only be awaited once.
- `RuntimeError: dictionary changed size during iteration` → [[Concurrency & Race Condition Errors]] or [[State & Lifecycle Errors]]: Modifying dict while iterating.
- `RuntimeError: generator already executing` → [[Concurrency & Race Condition Errors]]: Generator reentrancy.
- `StopIteration` → [[State & Lifecycle Errors]]: Iterator exhausted. Usually caught by `for` loop, but can leak in generators.
- `AssertionError` → [[The Assumption Trap]]: Assertion failed. Check the assumption.
- `NotImplementedError` → [[State & Lifecycle Errors]]: Abstract method not overridden.
- `OverflowError: math range error` → [[Offset & Boundary Errors]]: Number too large for float representation.
- `ZeroDivisionError: division by zero` → [[Offset & Boundary Errors]]: Divisor is zero. Validate input.
- `BrokenPipeError` → [[Connection & Network Errors]]: Writing to pipe/socket whose reader closed.
- `ssl.SSLCertVerificationError` → [[Connection & Network Errors]]: SSL certificate verification failed.
- `requests.exceptions.SSLError` → [[Connection & Network Errors]]: SSL/TLS issue with requests library.
- `requests.exceptions.ConnectionError` → [[Connection & Network Errors]]: Can't connect to server.
- `requests.exceptions.Timeout` → [[Connection & Network Errors]]: Request timed out.
- `requests.exceptions.HTTPError` → Various patterns depending on status code.
- `urllib3.exceptions.MaxRetryError` → [[Connection & Network Errors]]: All retries exhausted.
- `sqlalchemy.exc.OperationalError` → [[Connection & Network Errors]] or [[Configuration & Environment Errors]]: Database connection issue.
- `sqlalchemy.exc.IntegrityError` → [[Offset & Boundary Errors]]: Constraint violation (unique, foreign key, not null).
- `django.core.exceptions.ImproperlyConfigured` → [[Configuration & Environment Errors]]: Django settings wrong.
- `django.db.utils.OperationalError` → [[Connection & Network Errors]]: Database connection issue in Django.
- `celery.exceptions.Retry` → [[Connection & Network Errors]]: Task retry in Celery.
- `pkg_resources.DistributionNotFound` → [[Dependency & Import Errors]]: Package not installed.
- `pip._vendor.resolvelib.resolvers.ResolutionImpossible` → [[Dependency & Import Errors]]: Version conflict.

### Unique Traps
- **Mutable default arguments**: `def f(x=[])` — the list is shared across calls. Use `None` as default, create inside function.
- **GIL (Global Interpreter Lock)**: Threads don't give true parallelism for CPU-bound work. Use `multiprocessing` or `concurrent.futures.ProcessPoolExecutor`.
- **Late binding closures**: `lambda` in loop captures variable by reference, not value. All lambdas see the last value.
- **`is` vs `==`**: `is` checks identity (same object), `==` checks equality (same value). `is` works for small ints (-5 to 256) by coincidence.
- **Circular imports**: Module A imports B, B imports A. Causes `ImportError` or partially initialized module. Fix: restructure, use lazy imports, or import inside function.
- **`import *` pollution**: Imports everything, can shadow names. Use explicit imports.
- **String immutability**: Strings can't be modified in place. Concatenation in loop creates new string each time → O(n²). Use `''.join()`.
- **Shallow vs deep copy**: `list.copy()` and `copy.copy()` are shallow. Nested objects are still shared. Use `copy.deepcopy()` for nested structures.
- **`__init__.py` confusion**: Missing `__init__.py` in Python 3 namespace packages vs regular packages.
- **f-string gotchas**: Backslashes not allowed inside f-string expressions. Use a variable instead.
- **`async`/`await` pitfalls**: Forgetting `await` returns a coroutine object instead of the result. No error, just wrong type.

### Debug Tools
- `pdb` / `breakpoint()` — built-in debugger. `breakpoint()` added in Python 3.7.
- `ipdb` — enhanced pdb with IPython features.
- `traceback` module — programmatic stack trace access.
- `logging` module — structured logging with levels.
- `py-spy` — sampling profiler, no code changes needed.
- `memory_profiler` — line-by-line memory usage.
- `objgraph` — object reference graph visualization.
- `cProfile` / `profile` — deterministic profiling.
- `dis` — bytecode disassembler.
- `mypy` — static type checker.
- `pylint` / `flake8` / `ruff` — linters.
- `black` / `autopep8` — formatters.
- `pytest` — testing framework with excellent debugging support.
- `rich.traceback` — beautiful tracebacks.

---

## [[JavaScript]]

### Common Errors & Messages
- `TypeError: Cannot read properties of undefined (reading 'X')` → [[Null & Undefined Reference Errors]]: Accessing property on undefined. Most common JS error.
- `TypeError: Cannot read properties of null (reading 'X')` → [[Null & Undefined Reference Errors]]: Accessing property on null.
- `TypeError: X is not a function` → [[Type & Casting Errors]] or [[Null & Undefined Reference Errors]]: Variable isn't a function, or is undefined.
- `TypeError: X is not iterable` → [[Type & Casting Errors]]: Trying to iterate non-iterable (null, undefined, number).
- `TypeError: Cannot destructure property 'X' of undefined` → [[Null & Undefined Reference Errors]]: Destructuring undefined object.
- `TypeError: Assignment to constant variable` → [[State & Lifecycle Errors]]: Reassigning `const`.
- `TypeError: X.map is not a function` → [[Type & Casting Errors]]: Calling array method on non-array.
- `TypeError: Converting circular structure to JSON` → [[Serialization & Encoding Errors]]: Object references itself.
- `TypeError: Failed to fetch` → [[Connection & Network Errors]]: Network request failed (CORS, offline, server down).
- `ReferenceError: X is not defined` → [[Null & Undefined Reference Errors]]: Variable not declared or out of scope.
- `ReferenceError: Cannot access 'X' before initialization` → [[State & Lifecycle Errors]]: Temporal dead zone (let/const before declaration).
- `RangeError: Maximum call stack size exceeded` → [[Resource Exhaustion Errors]]: Infinite recursion.
- `RangeError: Invalid array length` → [[Offset & Boundary Errors]]: Negative or too-large array size.
- `RangeError: Invalid time value` → [[Type & Casting Errors]]: Invalid Date.
- `SyntaxError: Unexpected token` → [[Syntax & Parse Errors]]: Parse error in code or JSON.
- `SyntaxError: Unexpected end of JSON input` → [[Syntax & Parse Errors]]: Incomplete JSON string.
- `SyntaxError: Cannot use import statement outside a module` → [[Dependency & Import Errors]]: ESM import in CJS context.
- `SyntaxError: Unexpected identifier` → [[Syntax & Parse Errors]]: Missing comma, semicolon, or operator.
- `SyntaxError: Unterminated string constant` → [[Syntax & Parse Errors]]: Missing closing quote.
- `URIError: URI malformed` → [[Serialization & Encoding Errors]]: Invalid URI encoding.
- `Error: ENOENT: no such file or directory` → [[Configuration & Environment Errors]]: File not found (Node.js).
- `Error: EACCES: permission denied` → [[Permission & Authorization Errors]]: File permission (Node.js).
- `Error: EADDRINUSE: address already in use` → [[Resource Exhaustion Errors]]: Port already taken.
- `Error: EMFILE: too many open files` → [[Resource Exhaustion Errors]]: File descriptor leak.
- `Error: ENOMEM: not enough memory` → [[Resource Exhaustion Errors]]: Out of memory.
- `Error: connect ECONNREFUSED` → [[Connection & Network Errors]]: Target not listening.
- `Error: connect ETIMEDOUT` → [[Connection & Network Errors]]: Connection timed out.
- `Error: getaddrinfo ENOTFOUND` → [[Connection & Network Errors]]: DNS resolution failed.
- `Error: socket hang up` → [[Connection & Network Errors]]: Server closed connection unexpectedly.
- `Error: read ECONNRESET` → [[Connection & Network Errors]]: Connection reset by peer.
- `Error: self-signed certificate` → [[Connection & Network Errors]]: TLS cert not trusted.
- `Error [ERR_HTTP_HEADERS_SENT]: Cannot set headers after they are sent to the client` → [[State & Lifecycle Errors]]: Response already sent.
- `Error [ERR_REQUIRE_ESM]` → [[Dependency & Import Errors]]: Trying to `require()` an ESM-only package.
- `UnhandledPromiseRejectionWarning` → [[State & Lifecycle Errors]]: Promise rejected but no `.catch()`.
- `FATAL ERROR: CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory` → [[Resource Exhaustion Errors]]: Increase with `--max-old-space-size`.
- `AbortError: The operation was aborted` → [[State & Lifecycle Errors]]: AbortController cancelled the operation.
- `DOMException: Failed to execute 'querySelector' on 'Document'` → [[Syntax & Parse Errors]]: Invalid CSS selector.
- `SecurityError: Blocked a frame with origin` → [[Permission & Authorization Errors]]: Cross-origin iframe restriction.
- `Access to XMLHttpRequest blocked by CORS policy` → [[Permission & Authorization Errors]]: CORS not configured on server.
- `QuotaExceededError` → [[Resource Exhaustion Errors]]: localStorage/IndexedDB full.
- `NotAllowedError: play() failed because the user didn't interact with the document first` → [[Permission & Authorization Errors]]: Autoplay policy.

### Unique Traps
- **`this` binding**: `this` depends on HOW a function is called, not where it's defined. Arrow functions inherit `this` from enclosing scope. Class methods lose `this` when passed as callbacks — use `.bind()` or arrow functions.
- **`==` vs `===`**: `==` does type coercion (`"0" == false` is `true`). Always use `===`.
- **Type coercion madness**: `[] + [] = ""`, `[] + {} = "[object Object]"`, `{} + [] = 0`, `"5" + 3 = "53"`, `"5" - 3 = 2`.
- **`NaN` propagation**: `NaN !== NaN`. Use `Number.isNaN()` not `isNaN()` (global `isNaN` coerces).
- **Floating point**: `0.1 + 0.2 !== 0.3`. Use `Math.abs(a - b) < Number.EPSILON` or integer math (cents not dollars).
- **Event loop blocking**: Long synchronous operation blocks everything. Use `setImmediate`, `process.nextTick`, Web Workers.
- **Callback hell / Promise chains**: Deeply nested callbacks. Use async/await.
- **`var` hoisting**: `var` is function-scoped and hoisted. Use `let`/`const` (block-scoped).
- **Temporal dead zone**: `let`/`const` exist in scope but can't be accessed before declaration line.
- **Prototype pollution**: Modifying `Object.prototype` affects all objects. Security vulnerability.
- **`typeof null === "object"`**: Historical bug in JavaScript. Use `=== null` to check for null.
- **Array holes**: `[1,,3]` has a hole at index 1. `undefined` but different from `[1, undefined, 3]` in iteration.
- **`for...in` on arrays**: Iterates over keys (strings), not values. Includes prototype properties. Use `for...of` or `.forEach()`.
- **`parseInt` gotchas**: `parseInt("08")` was octal in old engines. Always pass radix: `parseInt("08", 10)`.
- **`JSON.stringify` drops**: Drops `undefined`, functions, and symbols. Converts `NaN`/`Infinity` to `null`.
- **Memory leaks in closures**: Closures retain references to outer scope variables. Can prevent garbage collection.
- **`setTimeout(fn, 0)`**: Not actually 0ms. Minimum is ~4ms in browsers. Goes to end of task queue.

### Debug Tools
- Chrome DevTools — debugger, profiler, memory, network, performance.
- `console.log` / `console.table` / `console.trace` / `console.time` — quick debugging.
- Node.js `--inspect` flag — Chrome DevTools for Node.
- `ndb` — improved Node.js debugging experience.
- VS Code debugger — breakpoints, watch, call stack.
- `why-is-node-running` — find what's keeping Node.js process alive.
- `clinic.js` — Node.js performance profiling suite.
- `0x` — flamegraph profiling for Node.js.
- ESLint — linter with auto-fix.
- Prettier — code formatter.
- TypeScript — catches type errors at compile time.
- `source-map-support` — better stack traces with source maps.

---

## [[TypeScript]]

### Common Errors & Messages
- `Type 'X' is not assignable to type 'Y'` → [[Type & Casting Errors]]: Type mismatch. Check the types carefully.
- `Argument of type 'X' is not assignable to parameter of type 'Y'` → [[Type & Casting Errors]]: Wrong argument type.
- `Property 'X' does not exist on type 'Y'` → [[Type & Casting Errors]]: Typo, or type is too narrow.
- `Object is possibly 'undefined'` → [[Null & Undefined Reference Errors]]: Strict null checks catching potential null.
- `Object is possibly 'null'` → [[Null & Undefined Reference Errors]]: Same for null.
- `Type 'X' is not assignable to type 'never'` → [[Type & Casting Errors]]: Usually exhaustive check failure or impossible type narrowing.
- `Cannot find module 'X' or its corresponding type declarations` → [[Dependency & Import Errors]]: Missing `@types/X` package.
- `Cannot find name 'X'` → [[Null & Undefined Reference Errors]]: Variable not declared or not imported.
- `Type 'X | undefined' is not assignable to type 'X'` → [[Null & Undefined Reference Errors]]: Need to handle undefined case.
- `Conversion of type 'X' to type 'Y' may be a mistake` → [[Type & Casting Errors]]: Unsafe type assertion.
- `'X' is declared but its value is never read` → Warning, not error. Dead code.
- `Type assertion using 'as' is not allowed` → [[Type & Casting Errors]]: Strict mode preventing unsafe cast.
- `TS2307: Cannot find module 'X'` → [[Dependency & Import Errors]]: Module resolution failure.
- `TS2322: Type 'X' is not assignable to type 'Y'` → [[Type & Casting Errors]]: The most common TS error.
- `TS2339: Property 'X' does not exist on type 'Y'` → [[Type & Casting Errors]]: Missing property.
- `TS2345: Argument of type 'X' is not assignable to parameter of type 'Y'` → [[Type & Casting Errors]].
- `TS2531: Object is possibly 'null'` → [[Null & Undefined Reference Errors]].
- `TS2532: Object is possibly 'undefined'` → [[Null & Undefined Reference Errors]].
- `TS2554: Expected X arguments, but got Y` → [[Type & Casting Errors]]: Wrong number of arguments.
- `TS2769: No overload matches this call` → [[Type & Casting Errors]]: None of the function overloads match.
- `TS7006: Parameter 'X' implicitly has an 'any' type` → [[Type & Casting Errors]]: Need explicit type annotation.
- `TS18046: 'X' is of type 'unknown'` → [[Type & Casting Errors]]: Need to narrow the type.

### Unique Traps
- **Types disappear at runtime**: TypeScript compiles to JavaScript. All type information is erased. Runtime is still JS.
- **`any` escape hatch**: Using `any` defeats the purpose of TypeScript. Use `unknown` instead and narrow.
- **`as` assertions are lies**: `x as string` doesn't convert — it tells the compiler to trust you. If you're wrong, runtime crash.
- **Strict mode differences**: `strict: true` enables many checks. Code that compiles without strict may fail with it.
- **Declaration files**: `.d.ts` files can be out of sync with actual library. `@types` packages may lag behind.
- **Enum pitfalls**: Numeric enums allow reverse mapping and accept any number. Use string enums or `as const`.
- **Structural typing**: TypeScript uses structural typing, not nominal. Two unrelated types with same shape are compatible.
- **`readonly` is shallow**: `readonly` on an object doesn't make nested objects readonly. Use `Readonly<T>` recursively.
- **Generic inference limits**: Sometimes TS can't infer generic types. Need explicit type arguments.
- **Module augmentation**: Extending third-party types requires declaration merging, which can be confusing.

### Debug Tools
- `tsc --noEmit` — type check without compiling.
- VS Code — built-in TypeScript language service with inline errors.
- `ts-node` — run TypeScript directly.
- `tsx` — faster alternative to ts-node.
- TypeScript Playground — online type checking and experimentation.
- `type-fest` — collection of useful TypeScript types.
- `ts-expect-error` — assert that a line should have a type error (for testing).

---

## [[Java]]

### Common Errors & Messages
- `java.lang.NullPointerException` → [[Null & Undefined Reference Errors]]: The classic. Calling method on null reference.
- `java.lang.NullPointerException: Cannot invoke "X" because "Y" is null` → [[Null & Undefined Reference Errors]]: Java 17+ helpful NPE messages.
- `java.lang.ClassCastException: X cannot be cast to Y` → [[Type & Casting Errors]]: Invalid downcast.
- `java.lang.NumberFormatException: For input string: "X"` → [[Type & Casting Errors]]: String can't be parsed as number.
- `java.lang.ArrayIndexOutOfBoundsException` → [[Offset & Boundary Errors]]: Array index >= length or < 0.
- `java.lang.StringIndexOutOfBoundsException` → [[Offset & Boundary Errors]]: String index out of range.
- `java.lang.StackOverflowError` → [[Resource Exhaustion Errors]]: Infinite recursion.
- `java.lang.OutOfMemoryError: Java heap space` → [[Resource Exhaustion Errors]]: Heap full. Increase `-Xmx` or fix leak.
- `java.lang.OutOfMemoryError: GC overhead limit exceeded` → [[Resource Exhaustion Errors]]: GC spending >98% time collecting <2% memory.
- `java.lang.OutOfMemoryError: Metaspace` → [[Resource Exhaustion Errors]]: Too many classes loaded. Common in hot-reload scenarios.
- `java.lang.OutOfMemoryError: unable to create new native thread` → [[Resource Exhaustion Errors]]: Thread limit reached.
- `java.lang.ClassNotFoundException: X` → [[Dependency & Import Errors]]: Class not on classpath.
- `java.lang.NoClassDefFoundError: X` → [[Dependency & Import Errors]]: Class was available at compile time but not at runtime.
- `java.lang.NoSuchMethodError: X` → [[Dependency & Import Errors]]: Method existed at compile time but not at runtime. Version mismatch.
- `java.lang.UnsatisfiedLinkError` → [[Dependency & Import Errors]]: Native library (.so/.dll) not found.
- `java.lang.IncompatibleClassChangeError` → [[Dependency & Import Errors]]: Binary incompatibility between compiled classes.
- `java.lang.IllegalStateException` → [[State & Lifecycle Errors]]: Object in wrong state for operation.
- `java.lang.IllegalArgumentException` → [[Type & Casting Errors]]: Invalid argument passed to method.
- `java.lang.UnsupportedOperationException` → [[State & Lifecycle Errors]]: Operation not supported (e.g., modifying unmodifiable collection).
- `java.util.ConcurrentModificationException` → [[Concurrency & Race Condition Errors]]: Collection modified during iteration.
- `java.lang.InterruptedException` → [[Concurrency & Race Condition Errors]]: Thread interrupted while waiting.
- `java.util.concurrent.TimeoutException` → [[Connection & Network Errors]]: Future/lock timed out.
- `java.util.concurrent.RejectedExecutionException` → [[Resource Exhaustion Errors]]: Thread pool full, can't accept new tasks.
- `java.io.FileNotFoundException` → [[Configuration & Environment Errors]]: File doesn't exist.
- `java.io.IOException` → Various: I/O operation failed.
- `java.net.ConnectException: Connection refused` → [[Connection & Network Errors]]: Target not listening.
- `java.net.SocketTimeoutException` → [[Connection & Network Errors]]: Socket operation timed out.
- `java.net.UnknownHostException` → [[Connection & Network Errors]]: DNS resolution failed.
- `javax.net.ssl.SSLHandshakeException` → [[Connection & Network Errors]]: TLS handshake failed.
- `java.sql.SQLException: Connection is closed` → [[State & Lifecycle Errors]]: Using closed DB connection.
- `java.sql.SQLSyntaxErrorException` → [[Syntax & Parse Errors]]: Invalid SQL.
- `java.sql.SQLIntegrityConstraintViolationException` → [[Offset & Boundary Errors]]: Constraint violation.
- `org.springframework.beans.factory.NoSuchBeanDefinitionException` → [[Dependency & Import Errors]]: Spring bean not found.
- `org.springframework.beans.factory.BeanCreationException` → [[Configuration & Environment Errors]]: Spring bean initialization failed.
- `org.hibernate.LazyInitializationException` → [[State & Lifecycle Errors]]: Accessing lazy-loaded entity outside session.
- `org.hibernate.StaleObjectStateException` → [[Concurrency & Race Condition Errors]]: Optimistic locking conflict.

### Unique Traps
- **`equals()` vs `==`**: `==` compares references for objects. Use `.equals()` for value comparison. But `==` works for primitives and interned strings.
- **Autoboxing NPE**: `Integer x = null; int y = x;` → NPE from unboxing null.
- **Checked exceptions**: Force handling but lead to empty catch blocks. Don't swallow exceptions.
- **Classpath hell**: Multiple versions of same library on classpath. Use Maven/Gradle dependency management.
- **Generics type erasure**: `List<String>` becomes `List` at runtime. Can't do `instanceof List<String>`.
- **`String.equals()` order**: `"constant".equals(variable)` avoids NPE if variable is null.
- **Mutable Date**: `java.util.Date` is mutable. Use `java.time` API (Java 8+).
- **`finalize()` is deprecated**: Don't rely on it for cleanup. Use try-with-resources.
- **Thread safety of HashMap**: `HashMap` is not thread-safe. Use `ConcurrentHashMap` for concurrent access.
- **`synchronized` on wrong object**: Synchronizing on different instances doesn't protect shared state.

### Debug Tools
- IntelliJ IDEA debugger — breakpoints, watches, evaluate expression, memory view.
- JVisualVM — monitoring, profiling, heap dump analysis.
- JConsole — JMX monitoring.
- `jstack` — thread dump (find deadlocks).
- `jmap` — heap dump.
- `jstat` — GC statistics.
- Eclipse MAT — heap dump analyzer.
- Arthas — online Java diagnostic tool.
- async-profiler — low-overhead sampling profiler.
- SpotBugs / PMD / Checkstyle — static analysis.
- JUnit / TestNG — testing frameworks.

---

## [[Go]]

### Common Errors & Messages
- `panic: runtime error: invalid memory address or nil pointer dereference` → [[Null & Undefined Reference Errors]]: Dereferencing nil pointer.
- `panic: runtime error: index out of range [X] with length Y` → [[Offset & Boundary Errors]]: Slice/array index out of bounds.
- `panic: runtime error: slice bounds out of range` → [[Offset & Boundary Errors]]: Slice operation exceeds capacity.
- `fatal error: all goroutines are asleep - deadlock!` → [[Concurrency & Race Condition Errors]]: All goroutines blocked.
- `fatal error: concurrent map read and map write` → [[Concurrency & Race Condition Errors]]: Map accessed concurrently without sync.
- `fatal error: concurrent map writes` → [[Concurrency & Race Condition Errors]]: Multiple goroutines writing to map.
- `WARNING: DATA RACE` → [[Concurrency & Race Condition Errors]]: Race detector found data race.
- `runtime: out of memory` → [[Resource Exhaustion Errors]]: Memory exhausted.
- `cannot find module providing package X` → [[Dependency & Import Errors]]: Module not in go.mod.
- `go: X@Y: verifying module: checksum mismatch` → [[Dependency & Import Errors]]: go.sum integrity check failed.
- `cannot convert X (type Y) to type Z` → [[Type & Casting Errors]]: Invalid type conversion.
- `interface conversion: interface {} is X, not Y` → [[Type & Casting Errors]]: Type assertion failed.
- `missing method X` → [[Type & Casting Errors]]: Type doesn't implement interface.
- `syntax error: unexpected X, expecting Y` → [[Syntax & Parse Errors]]: Go syntax error.
- `imported and not used: "X"` → [[Syntax & Parse Errors]]: Go doesn't allow unused imports.
- `declared and not used` → [[Syntax & Parse Errors]]: Go doesn't allow unused variables.
- `no required module provides package X` → [[Dependency & Import Errors]]: Run `go mod tidy`.
- `dial tcp X:Y: connect: connection refused` → [[Connection & Network Errors]]: Target not listening.
- `dial tcp: lookup X: no such host` → [[Connection & Network Errors]]: DNS failure.
- `context deadline exceeded` → [[Connection & Network Errors]]: Context timeout.
- `context canceled` → [[State & Lifecycle Errors]]: Context was cancelled.
- `http: server gave HTTP response to HTTPS client` → [[Configuration & Environment Errors]]: Protocol mismatch.
- `tls: failed to verify certificate` → [[Connection & Network Errors]]: TLS verification failed.
- `invalid character 'X' looking for beginning of value` → [[Serialization & Encoding Errors]]: JSON parse error.
- `json: cannot unmarshal X into Go value of type Y` → [[Serialization & Encoding Errors]]: JSON type mismatch.

### Unique Traps
- **Error swallowing**: `if err != nil` is explicit, but developers often ignore errors. `_ = riskyFunction()` hides failures.
- **Nil interface vs nil pointer**: An interface holding a nil pointer is NOT nil. `var p *MyStruct = nil; var i interface{} = p; i != nil` is TRUE.
- **Goroutine leaks**: Goroutines that block forever (on channel, mutex, I/O) are never garbage collected. Use context cancellation.
- **Channel deadlocks**: Unbuffered channel blocks until both sender and receiver are ready. Buffered channel blocks when full.
- **Map iteration order**: Map iteration order is randomized. Don't depend on it.
- **Slice gotchas**: Slicing creates a view, not a copy. Appending to a slice may or may not create a new backing array.
- **`defer` evaluation**: Arguments to deferred function are evaluated immediately, not when defer executes.
- **`range` variable capture**: Loop variable is reused. Capturing in goroutine captures the variable, not the value. Fixed in Go 1.22+.
- **`init()` function ordering**: `init()` functions run in dependency order, but multiple `init()` in same package run in source file order.
- **Struct field tags**: JSON tags are case-sensitive. `json:"name"` not `json: "name"` (no space after colon).

### Debug Tools
- `go vet` — static analysis for common mistakes.
- `go test -race` — race condition detector.
- `go test -bench` — benchmarking.
- `pprof` — CPU and memory profiling.
- `delve` (`dlv`) — Go debugger.
- `golangci-lint` — meta-linter running many linters.
- `go tool trace` — execution tracer.
- `go build -gcflags="-m"` — escape analysis.
- `staticcheck` — advanced static analysis.

---

## [[Rust]]

### Common Errors & Messages
- `error[E0382]: borrow of moved value: 'X'` → Ownership moved. Clone or restructure.
- `error[E0502]: cannot borrow 'X' as mutable because it is also borrowed as immutable` → Borrow checker. Can't have mutable + immutable refs simultaneously.
- `error[E0505]: cannot move out of 'X' because it is borrowed` → Value still borrowed, can't move.
- `error[E0308]: mismatched types: expected X, found Y` → [[Type & Casting Errors]].
- `error[E0432]: unresolved import 'X'` → [[Dependency & Import Errors]]: Module/crate not found.
- `error[E0433]: failed to resolve: use of undeclared crate or module` → [[Dependency & Import Errors]].
- `thread 'main' panicked at 'called Option::unwrap() on a None value'` → [[Null & Undefined Reference Errors]]: Use `match`, `if let`, or `?` instead.
- `thread 'main' panicked at 'called Result::unwrap() on an Err value'` → [[Null & Undefined Reference Errors]]: Handle the error properly.
- `thread 'main' panicked at 'index out of bounds'` → [[Offset & Boundary Errors]]: Use `.get()` for safe access.
- `thread 'main' has overflowed its stack` → [[Resource Exhaustion Errors]]: Deep recursion.
- `thread 'main' panicked at 'already borrowed: BorrowMutError'` → [[Concurrency & Race Condition Errors]]: RefCell runtime borrow check failed.
- `error: linking with 'cc' failed` → [[Dependency & Import Errors]]: Missing C compiler or system library.
- `error: failed to select a version for 'X'` → [[Dependency & Import Errors]]: Cargo version resolution conflict.

### Unique Traps
- **Borrow checker fights**: The compiler prevents data races at compile time. Learn to work WITH it, not against it.
- **Lifetime annotations**: Required when compiler can't infer lifetimes. Usually in structs holding references.
- **`unwrap()` abuse**: Using `unwrap()` everywhere defeats Rust's safety. Use `?`, `match`, `unwrap_or`, `expect`.
- **`Clone` performance**: Cloning to satisfy borrow checker works but may be expensive. Consider `Rc`/`Arc` for shared ownership.
- **Async Rust complexity**: `async`/`await` with lifetimes, `Pin`, `Send`/`Sync` bounds. Steeper learning curve than sync Rust.
- **Trait object limitations**: `dyn Trait` has restrictions (object safety). Not all traits can be used as trait objects.
- **Macro debugging**: Macro errors can be cryptic. Use `cargo expand` to see expanded code.

### Debug Tools
- `cargo check` — fast type checking without full compilation.
- `cargo clippy` — advanced linter with suggestions.
- `cargo expand` — expand macros to see generated code.
- `cargo test` — built-in test runner.
- `cargo bench` — benchmarking with Criterion.
- `rust-gdb` / `rust-lldb` — debuggers with Rust pretty-printing.
- `RUST_BACKTRACE=1` — enable backtraces on panic.
- `RUST_LOG` — env_logger log level control.
- `miri` — interpreter that detects undefined behavior.
- `cargo-flamegraph` — performance profiling.

---

## [[C++]]

### Common Errors & Messages
- `Segmentation fault (core dumped)` → [[Offset & Boundary Errors]] or [[Null & Undefined Reference Errors]]: Dereferencing null/invalid pointer, buffer overflow, use-after-free.
- `*** buffer overflow detected ***` → [[Offset & Boundary Errors]]: Writing past buffer boundary.
- `double free or corruption` → [[State & Lifecycle Errors]]: Freeing memory that was already freed.
- `munmap_chunk(): invalid pointer` → [[State & Lifecycle Errors]]: Corrupted heap metadata.
- `undefined reference to 'X'` → [[Dependency & Import Errors]]: Linker can't find symbol. Missing library or object file.
- `fatal error: 'X.h' file not found` → [[Dependency & Import Errors]]: Missing header file.
- `error: no matching function for call to 'X'` → [[Type & Casting Errors]]: Wrong argument types.
- `error: expected ';' before 'X'` → [[Syntax & Parse Errors]]: Missing semicolon.
- `error: use of undeclared identifier 'X'` → [[Null & Undefined Reference Errors]]: Variable not declared.
- `error: no viable conversion from 'X' to 'Y'` → [[Type & Casting Errors]]: Type conversion not possible.
- `terminate called after throwing an instance of 'std::bad_alloc'` → [[Resource Exhaustion Errors]]: Memory allocation failed.
- `terminate called after throwing an instance of 'std::out_of_range'` → [[Offset & Boundary Errors]]: `.at()` index out of range.
- `terminate called after throwing an instance of 'std::logic_error'` → [[State & Lifecycle Errors]]: Logic error detected.
- `std::bad_cast` → [[Type & Casting Errors]]: `dynamic_cast` failed.
- `pure virtual method called` → [[State & Lifecycle Errors]]: Calling virtual method during construction/destruction.
- Template errors → Often produce extremely long error messages. Read from the bottom up.

### Unique Traps
- **Undefined behavior (UB)**: The compiler can do ANYTHING. Signed integer overflow, null dereference, buffer overflow, use-after-free — all UB. The bug might not crash; it might silently corrupt data or be "optimized away."
- **Manual memory management**: `new`/`delete`, `malloc`/`free`. Use smart pointers (`unique_ptr`, `shared_ptr`) and RAII.
- **Dangling pointers**: Pointer to freed memory. Use-after-free. AddressSanitizer catches these.
- **Rule of Three/Five/Zero**: If you define destructor, copy constructor, or copy assignment, you probably need all three (or five with move semantics). Or zero (use defaults).
- **Header-only vs compiled libraries**: Include order matters. Forward declarations vs full includes.
- **Template error messages**: Can be pages long. Read the first error and the last line of the template instantiation chain.
- **`#include` order**: Can cause subtle issues. Include your own headers first, then library headers, then system headers.
- **Static initialization order fiasco**: Global/static objects in different translation units have undefined initialization order.

### Debug Tools
- GDB — GNU debugger. `bt` for backtrace, `p` for print, `watch` for watchpoints.
- LLDB — LLVM debugger. Similar to GDB.
- Valgrind — memory error detector (memcheck), profiler (callgrind), cache profiler (cachegrind).
- AddressSanitizer (ASan) — compile with `-fsanitize=address`. Catches buffer overflow, use-after-free, memory leaks.
- ThreadSanitizer (TSan) — compile with `-fsanitize=thread`. Catches data races.
- UndefinedBehaviorSanitizer (UBSan) — compile with `-fsanitize=undefined`. Catches UB.
- `strace` — trace system calls.
- `ltrace` — trace library calls.
- `objdump` / `nm` — inspect object files and symbols.
- `clang-tidy` — static analysis and modernization.
- `cppcheck` — static analysis.
- CMake — build system (debugging build configuration issues).

---

## [[Ruby]]

### Common Errors & Messages
- `NoMethodError: undefined method 'X' for nil:NilClass` → [[Null & Undefined Reference Errors]]: Calling method on nil.
- `NoMethodError: undefined method 'X' for Y:Z` → [[Type & Casting Errors]]: Method doesn't exist on this type.
- `NameError: undefined local variable or method 'X'` → [[Null & Undefined Reference Errors]]: Variable not defined.
- `ArgumentError: wrong number of arguments (given X, expected Y)` → [[Type & Casting Errors]]: Wrong argument count.
- `TypeError: no implicit conversion of X into Y` → [[Type & Casting Errors]]: Type mismatch.
- `LoadError: cannot load such file -- X` → [[Dependency & Import Errors]]: Gem not installed or require path wrong.
- `Bundler::GemNotFound` → [[Dependency & Import Errors]]: Gem not in Gemfile.lock.
- `Gem::ConflictError` → [[Dependency & Import Errors]]: Version conflict.
- `SyntaxError: unexpected X, expecting Y` → [[Syntax & Parse Errors]]: Ruby syntax error.
- `Errno::ENOENT: No such file or directory` → [[Configuration & Environment Errors]]: File not found.
- `Errno::EACCES: Permission denied` → [[Permission & Authorization Errors]]: File permission issue.
- `ActiveRecord::RecordNotFound` → [[Null & Undefined Reference Errors]]: Database record not found.
- `ActiveRecord::RecordInvalid` → [[Type & Casting Errors]]: Validation failed.
- `ActiveRecord::StatementInvalid` → [[Syntax & Parse Errors]]: Invalid SQL.
- `PG::ConnectionBad` → [[Connection & Network Errors]]: PostgreSQL connection failed.

### Unique Traps
- **Method missing magic**: `method_missing` can make debugging very hard. Objects respond to methods that don't visibly exist.
- **Monkey patching**: Reopening classes to add/modify methods. Can cause subtle bugs when libraries conflict.
- **Symbol vs String**: Symbols are immutable and interned. Strings are mutable. Rails `HashWithIndifferentAccess` bridges them.
- **Implicit return**: Last expression is returned. Can accidentally return wrong value.
- **Block/Proc/Lambda differences**: Blocks aren't objects, Procs are. Lambdas check arity, Procs don't. `return` in Proc returns from enclosing method.

### Debug Tools
- `byebug` / `debug` (Ruby 3.1+) — debugger.
- `pry` — REPL with debugging capabilities.
- `binding.irb` — built-in REPL breakpoint (Ruby 2.5+).
- `ruby-prof` — profiler.
- `stackprof` — sampling profiler.
- `rubocop` — linter and formatter.
- `rails console` — interactive Rails environment.
- `better_errors` — better error pages in Rails development.

---

## [[PHP]]

### Common Errors & Messages
- `Fatal error: Uncaught Error: Call to a member function X() on null` → [[Null & Undefined Reference Errors]].
- `Fatal error: Uncaught TypeError: X(): Argument #Y must be of type Z` → [[Type & Casting Errors]].
- `Warning: Undefined variable $X` → [[Null & Undefined Reference Errors]]: Variable not initialized.
- `Warning: Undefined array key "X"` → [[Offset & Boundary Errors]]: Array key doesn't exist.
- `Warning: Trying to access array offset on value of type null` → [[Null & Undefined Reference Errors]].
- `Parse error: syntax error, unexpected X` → [[Syntax & Parse Errors]].
- `Fatal error: Allowed memory size of X bytes exhausted` → [[Resource Exhaustion Errors]]: Increase `memory_limit` or fix leak.
- `Fatal error: Maximum execution time of X seconds exceeded` → [[Resource Exhaustion Errors]]: Increase `max_execution_time` or optimize.
- `Fatal error: Uncaught Error: Class "X" not found` → [[Dependency & Import Errors]]: Autoloader not configured or class missing.
- `Composer: Your requirements could not be resolved to an installable set of packages` → [[Dependency & Import Errors]]: Version conflict.
- `SQLSTATE[HY000] [2002] Connection refused` → [[Connection & Network Errors]]: Database connection failed.
- `SQLSTATE[42S02]: Base table or view not found` → [[Configuration & Environment Errors]]: Table doesn't exist.

### Unique Traps
- **Loose comparison (`==`)**: `"0" == false` is true, `"" == false` is true, `"0" == ""` is false. Use `===`.
- **`empty()` gotchas**: `empty("0")` returns true. `empty(0)` returns true.
- **Global state**: Superglobals (`$_GET`, `$_POST`, `$_SESSION`) are global mutable state.
- **`include` vs `require`**: `include` warns on failure, `require` fatals. Use `require_once` for dependencies.
- **Type juggling**: PHP silently converts types. `"1" + "2" = 3` (integer). Can cause security vulnerabilities.
- **`null` coalescing**: `??` operator is useful but doesn't catch `false` or `""`. Use `?:` for falsy check.

### Debug Tools
- Xdebug — debugger and profiler.
- `var_dump()` / `print_r()` — quick debugging.
- Laravel Telescope — request/query/job monitoring.
- Symfony Profiler — web debug toolbar.
- PHPStan / Psalm — static analysis.
- PHP CS Fixer — code style fixer.

---

## [[Swift]]

### Common Errors & Messages
- `Fatal error: Unexpectedly found nil while unwrapping an Optional value` → [[Null & Undefined Reference Errors]]: Force unwrap (`!`) on nil.
- `Fatal error: Index out of range` → [[Offset & Boundary Errors]]: Array index out of bounds.
- `Thread 1: EXC_BAD_ACCESS` → [[Null & Undefined Reference Errors]] or [[State & Lifecycle Errors]]: Invalid memory access.
- `Cannot convert value of type 'X' to expected argument type 'Y'` → [[Type & Casting Errors]].
- `Value of type 'X' has no member 'Y'` → [[Type & Casting Errors]]: Property/method doesn't exist.
- `Missing return in a function expected to return 'X'` → [[Syntax & Parse Errors]].
- `Closure cannot implicitly capture a mutating self parameter` → Struct mutation in closure.
- `Escaping closure captures mutating 'self' parameter` → Same issue with escaping closures.
- `Thread 1: Fatal error: Unexpectedly found nil while implicitly unwrapping an Optional value` → [[Null & Undefined Reference Errors]]: Implicitly unwrapped optional is nil.

### Unique Traps
- **Force unwrapping (`!`)**: The #1 cause of Swift crashes. Use `if let`, `guard let`, `??` instead.
- **Retain cycles in closures**: Closures capture `self` strongly by default. Use `[weak self]` or `[unowned self]`.
- **Value types vs reference types**: Structs are value types (copied), classes are reference types (shared). Unexpected behavior when mixing.
- **Protocol-oriented programming**: Powerful but complex. Protocol extensions, associated types, type erasure.
- **Concurrency (Swift 5.5+)**: `async`/`await`, actors, `Sendable`. New concurrency model with its own learning curve.

### Debug Tools
- Xcode debugger — breakpoints, LLDB, view hierarchy debugger, memory graph.
- Instruments — profiling (Time Profiler, Allocations, Leaks, Network).
- `print()` / `dump()` — quick debugging.
- SwiftLint — linter.
- `ASAN` / `TSAN` — sanitizers available in Xcode.

---

## [[C#]]

### Common Errors & Messages
- `System.NullReferenceException: Object reference not set to an instance of an object` → [[Null & Undefined Reference Errors]]: The C# NPE.
- `System.IndexOutOfRangeException` → [[Offset & Boundary Errors]]: Array index out of bounds.
- `System.InvalidCastException` → [[Type & Casting Errors]]: Invalid type cast.
- `System.FormatException` → [[Type & Casting Errors]]: String format invalid for parsing.
- `System.StackOverflowException` → [[Resource Exhaustion Errors]]: Infinite recursion.
- `System.OutOfMemoryException` → [[Resource Exhaustion Errors]]: Memory exhausted.
- `System.ObjectDisposedException` → [[State & Lifecycle Errors]]: Using disposed object.
- `System.InvalidOperationException: Collection was modified` → [[Concurrency & Race Condition Errors]]: Modifying collection during enumeration.
- `System.InvalidOperationException: Sequence contains no elements` → [[Offset & Boundary Errors]]: `.First()` on empty sequence. Use `.FirstOrDefault()`.
- `System.IO.FileNotFoundException` → [[Configuration & Environment Errors]]: File not found.
- `System.UnauthorizedAccessException` → [[Permission & Authorization Errors]]: Access denied.
- `System.TimeoutException` → [[Connection & Network Errors]]: Operation timed out.
- `System.Net.WebException` → [[Connection & Network Errors]]: Web request failed.
- `System.Data.SqlClient.SqlException` → Various database errors.
- `CS0103: The name 'X' does not exist in the current context` → [[Null & Undefined Reference Errors]]: Variable not declared.
- `CS0029: Cannot implicitly convert type 'X' to 'Y'` → [[Type & Casting Errors]].
- `CS8600: Converting null literal or possible null value to non-nullable type` → [[Null & Undefined Reference Errors]]: Nullable reference types warning.

### Unique Traps
- **`async void`**: Only use for event handlers. `async void` methods can't be awaited and exceptions crash the process.
- **`IDisposable` leaks**: Not disposing resources (connections, streams, HttpClient). Use `using` statement.
- **`ConfigureAwait(false)`**: In library code, use to avoid deadlocks. In UI code, don't (need UI thread context).
- **LINQ deferred execution**: LINQ queries execute lazily. Multiple enumeration executes the query multiple times.
- **Value type boxing**: Casting struct to interface boxes it (heap allocation). Performance trap in hot paths.
- **`HttpClient` misuse**: Creating new `HttpClient` per request causes socket exhaustion. Use `IHttpClientFactory` or singleton.

### Debug Tools
- Visual Studio debugger — breakpoints, watches, immediate window, diagnostic tools.
- JetBrains Rider — alternative IDE with excellent debugging.
- dotnet-counters / dotnet-dump / dotnet-trace — CLI diagnostic tools.
- BenchmarkDotNet — benchmarking.
- Roslyn analyzers — compile-time code analysis.
- `dotnet format` — code formatter.
