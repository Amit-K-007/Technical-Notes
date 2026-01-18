## Index
- [Python Mutability vs Immutability](#python-mutability-vs-immutability)
- [Python == vs is](#python--vs-is)
- [Python Interning](#python-interning)
- [Python Deep Copy vs Shallow copy](#python-deep-copy-vs-shallow-copy)
- [Python Iterator](#python-iterator)
- [Python Iterable](#python-iterable)
- [Python Iterator vs Iterable](#python-iterator-vs-iterable)
- [Generator vs Iterator](#generator-vs-iterator)
- [Python List](#python-list)
- [Python Tuple](#python-tuple)
- [Python Set](#python-set)
- [Python Dictionary](#python-dictionary)
- [Python String](#python-string)
- [Python Array](#python-array)
- [Python DefaultDict](#python-defaultdict)
- [Python Module vs Package](#python-module-vs-package)

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

### Python List

A Python list is an ordered and mutable collection used to store multiple items. Lists are created using square brackets `[]` and can store different data types.

Examples of list creation:
- `fruits = ["apple", "banana", "cherry"]`
- `mixed = [1, "hello", 3.5, True]`
- `empty = []`

Elements in a list can be accessed using:
- Indexing: `fruits[0]`
- Negative indexing: `fruits[-1]`
- Slicing: `fruits[1:3]`, `fruits[1:]`

List elements can be modified by assigning a new value to an index:
- `fruits[1] = "blueberry"`

Elements can be added using:
- `append()` to add an element at the end
- `insert()` to add an element at a specific index
- `extend()` to add multiple elements

Elements can be removed using:
- `remove()` to remove by value
- `pop()` to remove by index (returns the element)
- `del` to remove by index or delete the entire list

Common list utility operations include:
- `len()` to get the number of elements
- `index()` to find the position of an element
- `count()` to count occurrences of an element
- `in` keyword to check if an element exists

Lists can be rearranged using:
- `sort()` to sort the list in place
- `reverse()` to reverse the list
- `sorted()` to return a new sorted list

<br>

### Python Tuple

Python tuples are ordered, immutable, and efficient collections used for fixed and read-only data.

Examples of tuple creation:
- `fruits = ("apple", "banana", "cherry")`
- `mixed = (1, "hello", 5.5, True)`
- `colors = "red", "green", "blue"`
- `single = ("item",)`  

A trailing comma is required for a single-item tuple to distinguish it from a normal expression, ex. `(5 + 2)`

Elements in a tuple can be accessed using:
- Indexing: `fruits[0]`
- Negative indexing: `fruits[-1]`
- Slicing: `fruits[1:3]`

Tuples **cannot be modified** directly:
- Assignment like `fruits[1] = "blueberry"` raises a `TypeError`

If modification is required, a common workaround is:
- Convert tuple → list → modify → convert back to tuple

Common tuple utility operations include:
- `len()` to get number of elements
- `index()` to find the position of an element
- `count()` to count occurrences
- `in` keyword to check membership

Tuple unpacking allows assigning tuple elements to variables:
- `x, y, z = (10.0, 20.0, 30.0)`

<br>

### Python Set

A **Python set** is an unordered, unindexed, and mutable collection used to store **unique items**. Sets are created using curly braces `{}` or the `set()` constructor. Duplicate elements are automatically removed.

Examples of set creation:

fruits = {"apple", "banana", "cherry"}  
numbers = {1, 2, 3, 2, 4}  
empty_set = set()

Elements in a set:
- Cannot be accessed using indexing or slicing (unordered and unindexed)
- Can only contain immutable data types (int, float, string, tuple)

Elements can be added using:
- `add()` to add a single element
- `update()` to add multiple elements from another iterable

Elements can be removed using:
- `remove()` to remove an element (raises error if not found)
- `discard()` to remove an element without error
- `pop()` to remove and return an arbitrary element
- `clear()` to remove all elements

Common set utility operations include:
- `len()` to get the number of elements
- `in` keyword to check if an element exists (very fast)
- `issubset()` to check subset relationships

Set mathematical operations include:
- Union: `A | B` or `A.union(B)`
- Intersection: `A & B` or `A.intersection(B)`
- Difference: `A - B` or `A.difference(B)`
- Symmetric difference: `A ^ B` or `A.symmetric_difference(B)`

<br>

### Python Dictionary

A **Python dictionary** is a built-in, mutable collection used to store data in **key–value pairs**. Dictionaries are created using curly braces `{}` and allow fast data retrieval using unique keys.

Examples of dictionary creation:

```py
person = {
    "name": "Alice",
    "age": 30,
    "city": "New York"
}

empty_dict = {}

another_dict = dict(brand="Ford", model="Mustang", year=1964)
```

Values in a dictionary are accessed using **keys**, not indices:
- **Bracket syntax:** `person["name"]`
- **get() method** for safe access: `person.get("age")`

Dictionary elements can be **modified or added** using keys:
- **Modify value:** `person["age"] = 31`
- **Add new pair:** `person["occupation"] = "Software Engineer"`

Elements can be **removed** using:
- `del` to delete a key-value pair
- `pop()` to remove and return a value by key
- `popitem()` to remove the last inserted pair
- `clear()` to remove all items

Dictionary **utility operations** include:
- `keys()` to get all keys
- `values()` to get all values
- `items()` to get key–value pairs
- Iteration using `for key, value in dict.items()`

Other common operations:
- `len()` to get number of key-value pairs
- `in` keyword to check if a key exists

<br>

### Python String

A Python string is an ordered and **immutable** sequence of Unicode characters used to handle text. Strings are created using single quotes `' '`, double quotes `" "`, or triple quotes `''' '''` / `""" """` for multi-line text.

**Examples of string creation:**

```py
name = "Alice"
greeting = 'Hello'
multiline = """This is
a multi-line string"""
```

**Accessing characters:**

- **Indexing:** `name[0]` → `"A"`  
- **Negative indexing:** `name[-1]` → `"e"`  
- **Slicing:** `name[1:4]` → `"lic"`  
- **Length:** `len(name)` → `5`  

**Concatenation and repetition:**

- **Concatenation:** `"Hello" + " World"` → `"Hello World"`  
- **Repetition:** `"ha" * 3` → `"hahaha"`  

**Modifying strings (creates new strings):**

- `replace()`: `"hello world".replace("world", "Python")` → `"hello Python"`  
- `upper()` / `lower()`: `"Python".upper()` → `"PYTHON"`  
- `strip()`: Removes leading/trailing whitespace  

**Searching and splitting:**

- `find()` / `index()`: Locate substring  
- `split()`: Break string into list  
- `join()`: Combine iterable into string  

**String formatting:**

- **f-strings:** `f"Welcome, {name}!"` → `"Welcome, Alice!"` (name is a variable)
- **format() method:** `"Welcome, {}!".format("Bob")` → `"Welcome, Bob!"`

<br>

### Python Array

- Ordered collection of items.
- Dynamic sizing. Can add and remove items.
- Can hold items of same datatype.
- Faster than lists as they hold items of same datatype.
- Efficient memory usage and performance.

```py
from array import array

int_array = array("i", [1, 2, 3, 4])
float_array = array("f", [1.0, 2, 3.0, 4.0])

int_array.append(5)
int_array.remove(1)
float_array.pop()

print(int_array)  # array('i', [2, 3, 4, 5])
print(float_array)  # array('f', [1.0, 2.0, 3.0])
```

Prefer `List` when you want a flexible ordered collection of items.<br>
Prefer `Array` when you want to store large data of same type.

<br>

### Python DefaultDict

- `defaultdict` is a subclass of Python’s standard `dict` that provides default values for missing keys.
- Automatically initializes a key with a default value when accessed for the first time.

```py
# Counting occurrences (default int = 0)
from collections import defaultdict

words = ["apple", "banana", "apple"]
count = defaultdict(int)

for word in words:
    count[word] += 1
print(count)  # {'apple': 2, 'banana': 1}
```

```py
# Grouping items (default list = [])
group = defaultdict(list)
group["fruits"].append("apple")
group["fruits"].append("banana")

print(group)  # {'fruits': ['apple', 'banana']}
```

<br>

### Python Module vs Package

Module
- A module is simply a `.py` file.
- It can contain functions, classes, or variables.
- You can import and use a module in other Python files.

Package
- A package is a directory containing multiple Python files (modules).
- This directory must contain an `__init__.py` file. Even if it is empty.
- This `__init__.py` file differentiates a directory from a package.
- A package can contain sub-packages.

```py
mypackage/
    __init__.py
    module1.py   # contains greet()

# __init__.py
from .module1 import greet

# main.py
from mypackage import greet
greet()  # works directly
```

- You can define an `__all__` list in `__init__.py` to specify which modules or functions are imported when using `from package import *`.
```py
# mypackage/__init__.py
__all__ = ["module1", "module2"]
```

<br>


