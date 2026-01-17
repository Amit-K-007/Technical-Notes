## Index
- [Python Mutability vs Immutability](#python-mutability-vs-immutability)
- [Python == vs is](#python--vs-is)
- [Python Interning](#python-interning)
- [Python Deep Copy vs Shallow copy](#python-deep-copy-vs-shallow-copy)
- [Python Iterator](#python-iterator)
- [Python Iterable](#python-iterable)
- [Python Iterator vs Iterable](#python-iterator-vs-iterable)
- [Generator vs Iterator](#generator-vs-iterator)

<br>

### Python Mutability vs Immutability

**Immutable object**
- Once created → its value can’t be changed

```py
x = "hello"
x = x.upper()
```

x.upper() creates a new string, it does not modify the original
- Immutable types: int, float, str, tuple, bool

**Mutable object**
- Can be modified “in place”

```py
lst = [1, 2, 3]
lst.append(4)
```

Here, lst is modified directly
- Mutable types: list, dict, set

<br>

### Python == vs is

- `is` check if the 2 object are same. i.e. they point to same memory location.
- `==` check if the 2 objects have same value. i.e. the content of memory location

```py
a = [1, 2]
b = [1, 2]
print(a == b)  # True
print(a is b)  # False

a = None
b = None
print(a == b)  # True
print(a is b)  # True (Each None variable points to same None object)

a = 1
b = 1
print(a is b)  # True
# Integer Interning
```

<br>

### Python Interning

- Interning is an optimization technique where identical immutable objects (like integers and strings) are reused to save memory and speed up comparisons.
- Python automatically interns small integers in the range -5 to 256.
- Larger integers may not be interned.

```py
a = 100
b = 100
a is b  # True

a = 1000
b = 1000
a is b  # False (usually)
```

<br>

- String literals that are short or valid identifiers ('hello', 'foo123') are interned.
- String literals with space in them may not be interned.

```py
a = "hello"
b = "hello"
a is b  # True

a = "hello world"
b = "hello world"
a is b  # Might be False

```

<br>

- Interning only applies to immutable types (e.g., int, str, bool, None, float (some cases)).
- It’s an optimization, not a guarantee.

<br>

### Python Deep Copy vs Shallow copy

- `deepcopy()` recursively copies the outer iterable and the nested iterables as well.
- Shallow copy or simply `copy()` copies only the outer iterable, keeping same reference for the inner items.

<br>

```py
from copy import deepcopy, copy

a = [[1, 2], 3, 4]

a_scopy = copy(a)
a[0].append(5)
print(a_scopy)  # Output: [[1, 2, 5], 3, 4]

a = [[1, 2], 3, 4]

a_dcopy = deepcopy(a)
a[0].append(5)
print(a_dcopy)  # Output: [[1, 2], 3, 4]
```

<br>

### Python Iterator

- An iterator is an object that allows us to iterate over a collection of elements one at a time.
- An iterator must implement these two methods:
    - `__iter__()`: Returns the iterator object itself.
    - `__next__()`: Returns the next element. Raises `StopIteration` indicating end of iteration.

```py
class MyIterator:
    def __init__(self, n: int) -> None:
        self._n = n
        self._i = 0
    
    def __iter__(self):
        # Return iterator object itself
        return self

    def __next__(self):
        if self._i < self._n:
            value = self._i
            self._i += 1
            return value

        raise StopIteration

def main():
    iterator = MyIterator(5)

    for i in iterator:
        print(i)

main()

# Output:
# 0
# 1
# 2
# 3
# 4
```

- Above example creates an iterator that iterates from `[0, n)`.
- **Note**: A `for` loop internally uses `iter()` and `next()` method.

#### Usecases:
- Reading large files line by line. Prevents loading the entire file into memory.
  ```py
  # The standard, memory-efficient way to read a huge file
  with open("huge_data.log", "r") as file:
      for line in file:
          # process(line)  # Only one line exists in memory at a time
          pass
  ```
- Fetching database records in chunk instead of all at once.
- Lazily doing expensive computation when iterating over an iterable one by one.

<br>

### Python Iterable

- An **iterable** is an object that can be looped over using a `for` loop.
- An iterable must implement the `__iter__()` method, which returns an **iterator**.
- An iterable itself does **not** track iteration state.

```py
class MyIterable:
    def __init__(self, n: int) -> None:
        self._n = n

    def __iter__(self):
        return MyIterator(self._n)   # MyIterator class above

def main():
    iterable = MyIterable(5)

    for i in iterable:
        print(i)

main()

# Above example creates a custom iterable that produces a new iterator each time iteration starts.
# Output:
# 0
# 1
# 2
# 3
# 4
```
```py
iterable = [1, 2, 3]

iterator = iter(iterable)   # gets an iterator
print(next(iterator))       # 1
print(next(iterator))       # 2
print(next(iterator))       # 3
```

<br>

### Python Iterator vs Iterable

- Iterable → “Something you can loop over”
- Iterator → “Something that remembers where it is while looping”

```py
# Iterable (safe to reuse)
for n in nums:
    pass
for n in nums:   # works again
    pass
```
```py
# Iterator (state is consumed)
it = iter(nums)
for n in it:
    pass
for n in it:   # nothing happens
    pass
```

<br>

### Python Generators

- Generators in python are lazily evaluated objects.
- The next loop is ran only when it is required.
- It uses `yield` keyword (to return a single value) instead of returning all values at once.
- Like normal iterables, generators **do not** evaluate all the results once and store them in memory.

```py
def count_to_five():
    i = 1
    while i <= 5:
        yield i
        i += 1

for i in count_to_five():
    print(i, end=" ")  # Output: 1 2 3 4 5 
```

#### How generators work?

- When `yield` is executed, the function pauses and returns a value.
- The next call to `next()` resumes execution from where it left off.

#### Generator expression

- A generator expression is a compact inline way to create a generator.
- It doesn't create the whole list in memory, it lazily produces items when needed.
- It is similar to list comprehension, but uses **parentheses** instead of sqaure brackets.

```py
(expression for item in iterable if condition)
```

#### Generator exhaustion

- A generator can be consumed only once.
- Once all values are yielded, it is **exhausted**.
- Iterating over the same generator again will yield nothing.
- If we want to reuse the values, store the values using list comprehension.
- We can also create a new generator by calling the function again `gen_2 = count_to_five()`.

#### StopIteration exception

- When a generator is exhausted, it raises a `StopIteration` exception internally.
- `for` loop uses this exception to stop the iteration.
- Any value returned from the generator will get attached to this exception.

#### Chaining generators

- Chaining generators refer to using one generator inside another generator.
- This is done using `yield from` keyword.
- Used to access the return value of first generator.

```py
def worker():
    yield 1
    yield 2
    return "done"

def manager():
    result = yield from worker()
    print(result)  # done
```

#### Key points

- Generators are memory efficient because they generate values on demand instead of storing them.
- Generators are not inherently thread-safe.

<br>

### Generator vs Iterator

| Feature                | Iterator                                                | Generator                                                         |
| ---------------------- | ------------------------------------------------------- | ----------------------------------------------------------------- |
| **Definition**         | An object that implements `__iter__()` and `__next__()` | A special kind of iterator built using a function with `yield`    |
| **Creation**           | Requires writing a full class                           | Created using a simple function or generator expression           |
| **Memory Usage**       | Depends on implementation; may store large data         | Always lazy, produces one value at a time (very memory-efficient) |
| **Ease of Use**        | More code; verbose                                      | Very easy; minimal code                                           |
| **State Handling**     | You manage state manually                               | Python handles state automatically                                |
| **Infinite Sequences** | Possible but more manual work                           | Very natural and simple                                           |
| **Readability**        | Can be harder to maintain                               | Much more readable for sequential logic                           |

#### In short:

- Use a **Generator** for simple, linear and lazy iteration.
- Use an **Iterator** for complex, object-oriented iteration.

<br>

