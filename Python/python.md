## Index
- [Python Mutability vs Immutability](#python-mutability-vs-immutability)
- [Python == vs is](#python--vs-is)
- [Python Interning](#python-interning)
- [Python Deep Copy vs Shallow copy](#python-deep-copy-vs-shallow-copy)
- [Python Iterator](#python-iterator)
- [Python Iterable](#python-iterable)
- [Python Iterator vs Iterable](#python-iterator-vs-iterable)
- [Python Generator](#python-generator)
- [Generator vs Iterator](#generator-vs-iterator)
- [Python List](#python-list)
- [Python Tuple](#python-tuple)
- [Python Set](#python-set)
- [Python Dictionary](#python-dictionary)
- [Python String](#python-string)
- [Python Array](#python-array)
- [Python DefaultDict](#python-defaultdict)
- [Python Module vs Package](#python-module-vs-package)
- [Python *args vs **kwargs](#python-args-vs-kwargs)
- [Python Unpacking operators](#python-unpacking-operators)
- [Python Decorators](#python-decorators)
- [Types of Decorators](#types-of-decorators)
- [Python Exception Handling](#python-exception-handling)
- [Python Context Manager](#python-context-manager)
- [Python Module vs Package](#python-module-vs-package)
- [Python Namespace and Scope](#python-namespace-and-scope)
- [Python Scope Resolution](#python-scope-resolution)
- [Python Closure](#python-closure)
- [Python nonlocal vs global keywords](#python-nonlocal-vs-global-keywords)
- [Python GIL](#python-gil)

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

### Python Generator

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

### Python *args vs **kwargs

- Both `*args` & `**kwargs` are used to pass arbitrary number of arguments to a function.
- `*args` collect extra positional arguments as a tuple.
- `**kwargs` collect extra keyword arguments as a dictionary.
- Order always: positional → *args → keyword → **kwargs

```py
# extra positional arguments

def abc(*args):
    print(type(args))
    print(args)

abc(1, 2, "ooo")

# Output:
# <class 'tuple'>
# (1, 2, 'ooo')
```

```py
# extra keyword arguments

def abc(**kwargs):
    print(type(kwargs))
    print(kwargs)

abc(a=1, b="ooo")

# Output:
# <class 'dict'>
# {'a': 1, 'b': 'ooo'}

```

```py
def abc(arg1, *args, kwarg1, **kwargs):
    print(arg1)
    print(args)
    print(kwarg1)
    print(kwargs)

abc(1, 2, 3, kwarg1="val", a="ooo")

# Output:
# 1
# (2, 3)
# val
# {'a': 'ooo'}
```

<br>

### Python Unpacking operators

- In python `*` and `**` are often refered to as unpacking operators.
- `*` is used to unpack an iterable like a list or a tuple.
- `**` is used to unpack a dictionary like object.
- `*` and `**` are used for unpacking arguments in a function call.

```python
def greet(name, age):
    print(f"{name} is {age} years old.")

args = ("Alice", 30)
kwargs = {"name": "Bob", "age": 25}

greet(*args)  # Output: Alice is 30 years old.
greet(**kwargs)  # Output: Bob is 25 years old. (argument names should match with unpacked keys)
```

<br>

### Python Decorators

- Decorators are just functions that return functions.
- They are used to add additional functionality to existing functions.
- For example, `@authenticated` decorator can be used to check if the user is authenticated before passing the request to the view.
- To implement this, create `authenticated()` method with authentication logic and add `@authenticated` above your API view function.
- A decorator can also take arguments. The syntax looks like this: `@authenticated(admin_only=True)`.

<br>

```py
# Normal decorator

def my_decorator(func):
    def inner():
        print("before")
        func()
        print("after")
    return inner

@my_decorator
def my_func():
    print("Hello")

my_func()

# Output:
# before
# Hello
# after
```

```py
# Overridden function accepts parameters

def my_decorator(func):
    def inner(*args, **kwargs):
        print("before")
        func(*args, **kwargs)
        print("after")
    return inner

@my_decorator
def my_func(name: str):
    print("Hello", name)

my_func("john")

# Output:
# before
# Hello john
# after
```

```py
# Overridden function return values

def my_decorator(func):
    def inner(*args, **kwargs):
        print("before")
        res = func(*args, **kwargs)
        print("after")
        return res
    return inner

@my_decorator
def my_func(name: str):
    print("Hello", name)
    return 1

print(my_func("john"))

# Output:
# before
# Hello john
# after
# 1
```

```py
# Decorator accepts parameters

def my_decorator(num):
    def wrapper(func):
        def inner(*args, **kwargs):
            print("before", num)
            res = func(*args, **kwargs)
            print("after", num)
            return res
        return inner
    return wrapper

@my_decorator(10)
def my_func(name: str):
    print("Hello", name)
    return 1

print(my_func("john"))

# Output:
# before 10
# Hello john
# after 10
# 1
```

<br>

### Types of Decorators

- Decorators can be classified into 2 categories depending on:
    - Type of object they decorate:
        - Function decorator
        - Method decorator
        - Class decorator
    - Behaviour:
        - Decorator factory (decorator that accepts arguments)
        - Decorator stacking
        - Decorator that replaces the target entirely

<br>

#### Function decorator

- A function decorator is a function that takes another function as input and returns a new function.
- It extends or modifies the behavior of the original function.

```py
def my_decorator(func):
    def inner(*args, **kwargs):
        print("before")
        print(args, kwargs)
        func(*args, **kwargs)
        print("after")
    return inner

@my_decorator
def say_hello(name):
    print(f"Hello {name}!")

say_hello("apple")

# Output
# before
# ('apple',) {}
# Hello apple!
# after
```

<br>

#### Method decorator

- Method decorators are used inside classes. They can decorate instance methods, class methods, or static methods.
- Note that the custom decorator should be added below `@classmethod` & `@staticmethod` decorators.

```py
# instance method decorator

def instance_method_decorator(func):
    def wrapper(self, *args, **kwargs):
        print("Instance method decorator")
        return func(self, *args, **kwargs)
    return wrapper

class MyClass:
    @instance_method_decorator
    def greet(self, name):
        print(f"Hello {name}! from instance method")

obj = MyClass()
obj.greet("apple")

# Output
# Instance method decorator
# Hello apple! from instance method
```

```py
# class method decorator

def class_method_decorator(func):
    def wrapper(cls, *args, **kwargs):
        print("Class method decorator")
        return func(cls, *args, **kwargs)
    return wrapper

class MyClass:
    @classmethod
    @class_method_decorator
    def display(cls):
        print("Inside class method")

MyClass.display()

# Output
# Class method decorator
# Inside class method
```

```py
# static method decorator

def static_method_decorator(func):
    def wrapper(*args, **kwargs):
        print("Static method decorator")
        return func(*args, **kwargs)
    return wrapper

class MyClass:
    @staticmethod
    @static_method_decorator
    def multiply(a, b):
        return a * b

print(MyClass.multiply(2, 3))

# Output
# Static method decorator
# 6
```

<br>

#### Class decorator

- A class decorator decorates an entire class instead of a function.
- It receives the class as an argument and can modify or replace it.

```py
def class_decorator(cls):
    cls.category = "Decorated Class"
    return cls

@class_decorator
class MyClass:
    pass

print(MyClass.category)  # Decorated Class
```

<br>

#### Decorator factory

- A decorator that accepts arguments is called a Decorator factory.
- It is just a function that returns a decorator.

```py
def repeat(n):
    def decorator(func):
        def inner(*args, **kwargs):
            for _ in range(n):
                func(*args, **kwargs)
        return inner
    return decorator

@repeat(3)
def say_hi():
    print("Hi")

say_hi()

# Hi
# Hi
# Hi
```

<br>

#### Decorator stacking

- Multiple decorators can be stacked one above the other.
- While going in, they are executed from top to bottom and while coming out they are executed from bottom to top.

```py
def decorator_one(func):
    def wrapper():
        print("1 - before")
        func()
        print("1 - after")
    return wrapper

def decorator_two(func):
    def wrapper():
        print("2 - before")
        func()
        print("2 - after")
    return wrapper

@decorator_one
@decorator_two
def greet():
    print("Hello!")

greet()

# 1 - before
# 2 - before
# Hello!
# 2 - after
# 1 - after
```

<br>

#### Decorator that replaces the target entirely

- Some decorators do not call the original function at all, they completely replace the target function.
- This is useful for feature toggles, mocking, or access blocking.

```py
def replace_function(func):
    def new_function():
        print("Original function is replaced")
    return new_function

@replace_function
def original():
    print("This will never run")

original()  # Original function is replaced
```

<br>

### Python Exception Handling

- Exceptions are handled using try - except block.
- We can except specific exceptions like `ValueError`, `ZeroDivisionError`, etc.
- We can also except generic exceptions using `except Exception as e`.
- The code inside else is executed, only if no exception occured.
- The code inside finally is always executed, whether an exception occured or not.

```python
def exception_handling(num):
    try:
        print("try")
        10 / num
        print("end")
    except ZeroDivisionError:
        print("error")
    else:
        print("else")
    finally:
        print("finally")

exception_handling(1)
# try
# end
# else
# finally

exception_handling(0)
# try
# error
# finally
```

- The code inside `finally` block will always be executed. Even if we have `return` statement in `try` and `except` blocks.
- If there is a `return` statement in `finally`, this value will be returned else the value from `try` and `except` blocks will be returned.

```python
def exception_handling(num):
    try:
        print("try")
        10 / num
        print("end")
        return 1
    except ZeroDivisionError:
        print("error")
        return 2
    else:
        print("else")
    finally:
        print("finally")

res = exception_handling(1)
print(res)
# try
# end
# finally
# 1

res = exception_handling(0)
print(res)
# try
# error
# finally
# 2
```

#### Custom Exception

- User-defined error created by subclassing `Exception`.
- Represent application-specific or domain-specific errors clearly and explicitly.

```py
class InvalidAgeError(Exception):
    pass

def register(age):
    if age < 18:
        raise InvalidAgeError("Age must be 18+")
    return "Registered!"
```

#### Exception Chaining

- Used to raise a high-level exception while preserving the original low-level cause
- `raise HighLevelError("Meaningful message") from original_exception`

```py
# Without Chaining
try:
    int("abc")
except ValueError:
    raise RuntimeError("Invalid input")

# RuntimeError: Invalid input
# (Original ValueError is gone)
```

```py
# With Chaning
try:
    int("abc")
except ValueError as e:
    raise RuntimeError("Invalid input") from e

# ValueError: invalid literal for int() with base 10: 'abc'
# The above exception was the direct cause of the following exception:
# RuntimeError: Invalid input
```

<br>

### Python Context Manager

- Context Managers are python class that implement `__enter__` and `__exit__` methods.
- These methods are used to setup & cleanup the resource.
- They are primarily used with the `with` block in python.
- If an error occurs, returning False from `__exit__` function will propogate the error.
- If an error occurs, returning True from `__exit__` function will suppress the error.

```py
class MyCM:
    def __init__(self, file: str) -> None:
        logger.info("init")
        self._file = file

    def __enter__(self):
        logger.info("opening file: %s", self._file)
        return self

    def __exit__(self, exc_type, exc_val, exc_tb) -> bool:
        logger.info("closing file: %s", self._file)
        # Suppress value error
        if exc_type is ValueError:
            return True
        # Propogate other errors
        return False

    def read(self):
        logger.info("reading file: %s", self._file)

with MyCM("Action") as manager:
    time.sleep(1)
    manager.read()
    time.sleep(1)

# Output:
# 00:00:00 - init
# 00:00:00 - opening file: Action
# 00:00:01 - reading file: Action
# 00:00:02 - closing file: Action
```

<br>

- We can also create function-based context managers using `contextlib` module.
- Use `@contextmanager` decorator to create a context manager function.
- The `yield` keyword divides the enter and exit sections of the context manager.

<br>

```python
"""Using contextlib"""

from contextlib import contextmanager

@contextmanager
def my_context_manager():
    print("enter")
    try:
        yield  # This is where the block inside `with` runs
    except Exception as e:
        print("error")
        raise e
    finally:
        print("exit")


with my_context_manager() as manager:
    print("inside")
    raise ValueError("kkk")

# Output:

# enter
# inside
# error
# exit
# Traceback (most recent call last):
#   File "/home/a/Desktop/projects/other/python_notes/python_code.py", line 18, in <module>
#     raise ValueError("kkk")
# ValueError: kkk
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
- `__all__` defines which names are exported when using `from module/package import *`, acting as the module’s public API list.

```py
Folder structure
mypackage/
    __init__.py
    utils.py
    models.py

# __init__,py
from .utils import helpful_function
from .models import User

__all__ = ["helpful_function", "User"]
```

- `import package` → executes `__init__.py` and adds one name (the package) to your namespace.
- `from package import *` → executes `__init__.py` and adds multiple names (from `__all__` or public names) as separate entries in your namespace.

<br>

### Python Namespace and Scope

- A namespace is like a dictionary that holds mapping between a variable identifier and its value.
- There are 4 categories of namespace:
    - **Built-in namespace**: contains built-in variables & functions (`print()`, `None`, etc.)
    - **Global namespace**: contains module level variables & functions.
    - **Local namespace**: contains variables defined inside a function.
    - **Enclosing namespace**: refers to namespace of outer function (in case of nested functions).

<br>

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        x = "local"
    inner()
```

<br>

- Scope defines visibility and lifespan of a variable.
- It also states where we can access a variable in the code.
- Again, there are 4 categories of scope:
    - **Built-in scope**: Python’s built-in names.
    - **Global scope**: Module level scope.
    - **Enclosing scope**: Outer function scope.
    - **Local scope**: Function level scope. 

- We can see the identifiers defined in global and local namespace using: `globals()` and `locals()` functions.

<br>

```python
my_var_1 = 100
print("Globals:", globals())

def abc():
    my_var_2 = 200
    print("Globals:", globals())
    print("Locals:", locals())

abc()

# Output:

# Globals: {..., 'my_var_1': 100}

# Globals: {..., 'my_var_1': 100, 'abc': <function abc at 0x7efa454231a0>}
# Locals: {'my_var_2': 200}
```

<br>

### Python Scope Resolution

- It refers to the process of finding a particular variable in the program.
- Scope resolution is based on the LEGB rule.
- Python first tries to find the variable in the **L**ocal namespace then **E**nclosing namespace then **G**lobal namespace and at last **B**uilt-in namespace.
- If the variable in not found after searching in all 4 namespaces, it raises `NameError`.

<br>

```python
x = "global"

def outer():
    x = "enclosing"

    def inner():
        x = "local"
        print(x)  # Prints "local"
    
    inner()
    print(x)  # Prints "enclosing"

outer()
print(x)  # Prints "global"
```

<br>

### Python Closure

- A closure is a function that captures and remembers variables from its enclosing scope even after that scope has finished executing.
- Normally, local variables die when a function returns.
- But with closures: The inner function keeps a reference to the outer function’s variables.
- Inspecting a closure: `print(double.__closure__)`

```py
def make_logger(prefix):
    def log(msg):
        print(f"[{prefix}] {msg}")
    return log

info = make_logger("INFO")

info("Started")     # [INFO] Started
```

<br>

### Python nonlocal vs global keywords

- `global x` tells Python: “When I assign to `x`, use the module-level (global) x, not a local one.”

```py
x = 0

def inc():
    global x
    x += 1

inc()
inc()
print(x)   # 2
```

- `nonlocal x` tells Python: “When I assign to `x`, use the variable from the nearest enclosing function scope (not global, not local).”

```py
def counter():
    count = 0
    def inc():
        nonlocal count
        count += 1
        return count
    return inc

c = counter()
print(c())  # 1
print(c())  # 2
```

<br>

### Python GIL

- GIL stands from Global Interpreter Lock.
- Each python process has a GIL lock associated with it.
- GIL allows only 1 thread execution at a time by the python interpreter.
- It prevents multithreading and the issues associated with it (like race conditions, data corruption, etc.).
- Due to limitation of having only single thread, true multithreading is not possible. Thus the performance of the applications can be reduced.
- But if we are dealing with I/O bound operations, we can leverage threading module & context switching.

<br>

