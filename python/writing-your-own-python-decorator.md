Decorators are one of Python’s most powerful features — they let you wrap a function with extra behavior without changing the function’s body. In this post you’ll learn what decorators are, how to build them from scratch, and how to use best practices like **functools.wraps**. We'll build several decorators (simple → advanced), show real-world examples, and list common pitfalls.

---

## What is a decorator (short version)

A **decorator** is a callable that takes a function (or class) and returns a new callable that replaces it. You apply decorators with the **@decorator_name** syntax above a function definition.

Think of it like: you hand your function to the decorator, the decorator returns a wrapped version that does something extra before/after (or instead of) the original function.

---

## Quick example (the idea)

```python
def my_decorator(fn):
    def wrapper(*args, **kwargs):
        print("Before")
        result = fn(*args, **kwargs)
        print("After")
        return result
    return wrapper

@my_decorator
def greet(name):
    print(f"Hello, {name}!")

greet("Akshay")
```

Output:

```
Before
Hello, Akshay!
After
```

**greet** is replaced by **wrapper**, which calls the original **greet** inside.

---

## Step 1 — Write the simplest decorator

Start by writing a decorator that logs when a function runs.

```python
def log_call(fn):
    def wrapper(*args, **kwargs):
        print(f"Calling {fn.__name__}")
        return fn(*args, **kwargs)
    return wrapper

@log_call
def add(a, b):
    return a + b

print(add(2, 3))  # prints "Calling add" then "5"
```

**Key points**

* `*args, **kwargs` keeps the wrapper generic.
* The wrapper calls and returns the original function’s result.

---

## Step 2 — Preserve metadata with **functools.wraps**

Problem: after decorating, **add.__name__** becomes **'wrapper'** and docstrings/signatures are lost. Fix it with **functools.wraps**.

```python
from functools import wraps

def log_call(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        print(f"Calling {fn.__name__}")
        return fn(*args, **kwargs)
    return wrapper
```

Now **add.__name__**, **add.__doc__**, and the signature are preserved — important for debugging, introspection, and tools (e.g., Sphinx, CLI help).

---

## Step 3 — Decorators that accept arguments

Make a decorator configurable (e.g., log level or a message). That requires one more nesting level.

```python
from functools import wraps

def log_message(msg):
    def decorator(fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            print(f"{msg} — calling {fn.__name__}")
            return fn(*args, **kwargs)
        return wrapper
    return decorator

@log_message("DEBUG")
def multiply(a, b):
    return a * b

print(multiply(3, 4))  # prints "DEBUG — calling multiply", then "12"
```

Pattern: **decorator_factory(...) -> decorator(fn) -> wrapper(...)**.

---

## Step 4 — Decorating functions with return values & side effects

Decorators must return the wrapped function result unless deliberately altering it.

Example — caching (simple memoize):

```python
from functools import wraps

def memoize(fn):
    cache = {}
    @wraps(fn)
    def wrapper(*args):
        if args in cache:
            return cache[args]
        cache[args] = fn(*args)
        return cache[args]
    return wrapper

@memoize
def fib(n):
    if n < 2:
        return n
    return fib(n-1) + fib(n-2)
```

Note: This naive memoize works for hashable positional args only.

---

## Step 5 — Decorating methods and preserving **self**

Decorators work for methods too. The wrapper receives **self** as the first arg.

```python
def ensure_positive_result(fn):
    @wraps(fn)
    def wrapper(self, *args, **kwargs):
        result = fn(self, *args, **kwargs)
        if result < 0:
            raise ValueError("Negative result!")
        return result
    return wrapper

class Calculator:
    @ensure_positive_result
    def subtract(self, a, b):
        return a - b
```

If you design for both functions and methods, use `*args, **kwargs` and avoid assuming arg structure.

---

## Step 6 — Class-based decorators (stateful decorators)

If you need decorated state (like counting calls), a class can be clearer.

```python
from functools import wraps

class Counter:
    def __init__(self, fn):
        wraps(fn)(self)
        self.fn = fn
        self.count = 0

    def __call__(self, *args, **kwargs):
        self.count += 1
        return self.fn(*args, **kwargs)

@Counter
def greet(name):
    print(f"Hello, {name}!")

greet("A"); greet("B")
print(greet.count)  # 2
```

Note: **wraps(fn)(self)** applies metadata to the instance so **__name__** and **__doc__** behave nicely.

---

## Step 7 — Stacking decorators

You can stack multiple decorators. Order matters: the decorator closest to the function is applied first.

```python
@decorator_a
@decorator_b
def f():
    pass
```

This is equivalent to **f = decorator_a(decorator_b(f))**.

---

## Step 8 — Real world examples

### 1. Timing a function

```python
import time
from functools import wraps

def timeit(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        val = fn(*args, **kwargs)
        end = time.perf_counter()
        print(f"{fn.__name__} took {end - start:.6f}s")
        return val
    return wrapper
```

### 2. Authorization guard (web handlers)

```python
from functools import wraps

def require_role(role):
    def decorator(fn):
        @wraps(fn)
        def wrapper(user, *args, **kwargs):
            if role not in user.roles:
                raise PermissionError("Forbidden")
            return fn(user, *args, **kwargs)
        return wrapper
    return decorator
```

These fit well into frameworks (Flask/Django) for pre-route checks.

---

## Step 9 — Testing decorators

* Test both the decorated function behavior and that the original function is called with expected arguments.
* Use small, deterministic functions in tests.
* For stateful decorators, test state changes (e.g., call count).
* Use **inspect.signature** to ensure signatures are preserved if needed.

Example (pytest):

```python
def test_log_call(monkeypatch):
    called = {}
    def fake_print(msg):
        called['msg'] = msg
    monkeypatch.setattr('builtins.print', fake_print)
    @log_call
    def f(x): return x+1
    assert f(1) == 2
    assert 'Calling f' in called['msg']
```

---

## Common pitfalls & how to avoid them

* **Losing function metadata** — fix with **functools.wraps**.
* **Wrong decorator signature** — if you want decorator arguments, add an extra function level.
* **Non-hashable args in caching** — handle mutable args (use **repr**, or explicit key), or restrict to hashable.
* **Decorating built-ins** — avoid or be careful (some built-ins are in C and can’t be wrapped the same).
* **Mutable default state shared across instances** — avoid class attributes for per-function state unless intentional.
* **Decorating methods vs. functions** — remember **self**/**cls**.

---

## Advanced topics (brief)

* ****functools.partial**** — use inside decorators to bind arguments to wrapped functions if needed.
* ****inspect.signature**** — to introspect and forward parameters more precisely (rarely needed).
* ****wrapt** library** — for very robust decorator needs (keeps signature, works with descriptors); useful for complex libraries.
* **Asynchronous functions** — if decorating **async def** functions, the wrapper must be async:

  ```python
  def decorator(fn):
      @wraps(fn)
      async def wrapper(*args, **kwargs):
          # await something
          return await fn(*args, **kwargs)
      return wrapper
  ```

  Detect with **asyncio.iscoroutinefunction(fn)** if you want one decorator that supports both sync and async.

---

## Cheat-sheet: decorator shapes

1. Simple decorator:

```python
def dec(fn):
    def wrapper(*a, **k): return fn(*a, **k)
    return wrapper
```

2. Decorator with args:

```python
def dec_arg(x):
    def decorator(fn):
        def wrapper(*a, **k): return fn(*a, **k)
        return wrapper
    return decorator
```

3. Class decorator:

```python
class Dec:
    def __init__(self, fn): self.fn = fn
    def __call__(self, *a, **k): return self.fn(*a, **k)
```

Always add **@wraps(fn)** in inner wrappers.

---

## Wrap up — Best practices

* Use **functools.wraps** to preserve metadata.
* Keep wrappers simple and focused (single responsibility).
* When you need configuration, add one extra layer of nesting.
* For async functions, write async wrappers or detect coroutine functions.
* Consider existing libraries (**functools**, **wrapt**) before writing complex decorator logic.

