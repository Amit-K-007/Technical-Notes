## Index
- [Python Mutability vs Immutability](#python-mutability-vs-immutability)
- [Python == vs is](#python--vs-is)
- [Python Interning](#python-interning)
- [Python Deep Copy vs Shallow copy](#python-deep-copy-vs-shallow-copy)

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
