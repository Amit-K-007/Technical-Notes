# Object Oriented Programming

<br>

## Index
- [4 Pillars of OOPS](#4-pillars-of-oops)
- [Access modifiers](#access-modifiers)
- [Getters & Setters](#getters--setters)
- [Method overriding vs Method overloading](#method-overriding-vs-method-overloading)
- [Class method vs Static method](#class-method-vs-static-method)
- [Abstract class vs Interface](#abstract-class-vs-interface)
- [Class vs Object](#class-vs-object)
- [Constructor & Destructor](#constructor--destructor)
- [super() method](#super-method)
- [Method Resolution Order](#method-resolution-order)
- [Types of inheritance](#types-of-inheritance)
- [Diamond Problem](#diamond-problem)
- [Inheritance vs Composition](#inheritance-vs-composition)
- [Name mangling](#name-mangling)

<br>

### 4 Pillars of OOPS

- Abstraction
- Encapsulation
- Inheritance
- Polymorphism

Abstraction
- Hiding complex implementation details.
- Providing a cleaner API to interact with the object.
- It allows to define the structure of the class.
- It focuses on 'what object does' instead of 'how it does it'.

Encapsulation
- It bundles data (attributes) & methods that operate on this data into a single class.
- It also restricts access to object's internal state using access modifiers.
- Access modifiers control how data is accessed and modified.

Inheritance
- Inheritance allows child class to access data (attributes) & methods of parent class.
- This promotes code reuse and establish child-parent hierarchy.
- It enables child class to extend the functionality of the parent class.

Polymorphism
- Polymorphism means Many Forms.
- It allows objects of different classes to be treated as object of common superclass.
- It enables a method to behave in different ways, depending on the object that invoke it.

<br>
<br>
<br>

### Access modifiers

- Access modifiers are used to control the access of class attributes & methods.
- There are 3 types of access modifiers: public, private, protected.
- Public data members can be accessed within as well as outside the class.
- Private data members can be accessed only within the current class.
- Protected data members can be accessed within the current class & all the subclasses that inherit this class.

---

- In python, private data members starts with "__" (double underscore).
- Protected data members start with "_" (single underscore).
- Note: Python does not enforce strict access control like Java or C++.

<br>
<br>
<br>

### Getters & Setters

- A getter is a special method used to get value of private & protected attributes.
- A setter is used to set the value of private & protected attributes.
- In python, we use `@property` decorator to implement getters & setters.

```python
class Tree:
    _age = 10

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value: int):
        self._age = value
```

<br>
<br>
<br>

### Method overriding vs Method overloading

- In method overriding, the method in the child class replaces the method in the parent class.
- The method signature (parameters & return type) remains the same.
- When we call the method using child's object, only the new method is accessible.

---

- Method overloading refers to defining multiple methods with the same name but different signatures.
- The method signature is changed in this case (different parameters or return type).
- When we call the method using class object, both new and old methods can be called separately (depending on the arguments passed).

---

- Python only supports method overriding.
- To implement functionality like method overloading, we can use `*args` & `**kwargs`.

<br>
<br>
<br>

### Class method vs Static method

- A class method is bound to the class rather than the instance.
- It can access class level attributes & methods.
- It is defined using @classmethod decorator.

---

- A static method is neither bound to the class nor the instance.
- It does not have acess to any attributes or methods.
- They are generally stateless and are used as utility functions.
- It is defined using @staticmethod decorator.

```python
class MyClass:
    _value = 10

    @classmethod
    def get_value(cls):
        return cls._value


class MathUtilities:
    @staticmethod
    def add(x, y):
        return x + y


MyClass.get_value()  # Outputs: 10
MathUtilities.add(5, 3)  # Outputs: 8
```

<br>
<br>
<br>

### Abstract class vs Interface

- Abstract class can have both concrete as well as abstract methods.
- Can have a constructor.
- Common behaviour (methods) can be implemented in abstract class, leaving specific implementation to child class.

---

- Interface can have only abstract methods.
- Cannot have a constructor.
- No implementation can be done in an interface. All methods must be implemented in the child class.

---

- Both provides a blueprint for the child class to implement.
- Python only supports abstract class using abc module.

```python
from abc import ABC, abstractmethod

class BaseClass(ABC):
    @abstractmethod
    def print_hello(self):
        pass

class ChildClass(BaseClass):
    def print_hello(self):
        print("hello")

child = ChildClass()
child.print_hello()  # Output: hello
```

<br>
<br>
<br>

### Class vs Object

Class
- It is a blueprint or template for creating objects.
- It defines the properties (attributes) & behaviours (methods) of the object.
- It does not hold data itself. It is just a structure.

Object
- An object is a concrete implementation of the class.
- It holds actual data and can use the class methods.

```python
class Dog:  # Class
    def __init__(self, name):
        self.name = name

dog1 = Dog("Buddy")  # Object
``` 

<br>
<br>
<br>

### Constructor & Destructor

Constructor
- It is called automatically when an object is created.
- It is used to initialise the object.

```python
class MyClass:
    def __init__(self, name):
        self.name = name
```

<br>

Destructor
- It is called automatically when an object is about to be destroyed.
- It is used for cleanup operations like closing a file or releasing resources.

```python
class MyClass:
    def __del__(self):
        print("Destructor called, object deleted")
```

<br>
<br>
<br>

### super() method

- It is used to call methods from the parent class.
- Commonly used in inheritance to access parent class's methods / constructor.
- It is also used when we are overriding methods from parent class.
- Promotes code reuse.

```python
class Parent:
    def __init__(self):
        print("Parent init")

class Child(Parent):
    def __init__(self):
        super().__init__()  # Calls Parent's __init__()
        print("Child init")
```

```python
class Parent:
    def show(self):
        print("Parent show")

class Child(Parent):
    def show(self):
        super().show()  # Calls Parent's show()
        print("Child show")

```

<br>
<br>
<br>

### Method Resolution Order

- MRO refers to the order in which python searches for a method in parent classes.
- It is used in **Multiple** inheritance.
- It follows **C3 linearization** algorithm:
    - Go from left to right.
    - All the child classes must appear before parent classes.

```python
class A:
    def show(self):
        print("A")

class B(A):
    def show(self):
        print("B")

class C(A):
    def show(self):
        print("C")

class D(B, C):
    pass

d = D()

print(D.mro())  
# [D, B, C, A, object]
# object is Python’s ultimate base class.
# Every class in Python (whether you write it or not) inherits from object.
```

<br>
<br>
<br>

### Types of Inheritance

Single Inheritance
- One child class inherits from one parent class.

```python
class A:
    pass

class B(A):
    pass
```

<br>

Multiple Inheritance
- One child class inherits from multiple parent classes.

```python
class A:
    pass

class B:
    pass

class C(A, B):
    pass
```

<br>

Multilevel Inheritance
- A class inherits from a **child** class, forming a chain.

```python
class A:
    pass

class B(A):
    pass

class C(B):
    pass
```

<br>

Hierarchical Inheritance
- Multiple child classes inherit from a single parent class.

```python
class A:
    pass

class B(A):
    pass

class C(A):
    pass
```

<br>

Hybrid Inheritance
- A combination of two or more types of inheritance.

```python
class A:
    pass

class B(A):
    pass

class C(A):
    pass

class D(B, C):  # Multiple + Hierarchical
    pass
```

<br>
<br>
<br>

### Diamond Problem

Definition
- It occurs when we combine **Hierarchical** & **Multiple** inheritance.
- It occurs when 2 classes inherit from a common class and the last class inherits from those 2 classes.
- It is called the diamond problem, because the inheritance tree takes shape of a diamond.

Ambiguity
- D inherits from both B and C, and both override `A.show()`.
- Which `.show()` should be called?

```python
class A:
    def show(self):
        print("A")

class B(A):
    def show(self):
        print("B")

class C(A):
    def show(self):
        print("C")

class D(B, C):
    pass

d = D()
d.show()
```

Solution
- Method Resolution Order (MRO).
- As explained above, it states the order in which methods are searched for in parent classes.

```python
print(D.mro())
# [D, B, C, A, object]
```

<br>
<br>
<br>

### Inheritance vs Composition

#### Inheritance

- In inheritance, a child class inherits the attributes and methods of the parent class.
- This is "**is-a**" type of relationship. Example, a `Dog` is a `Animal`.
- Here, the parent class defines the behaviour and the child class can implements or extends it.
- Pros: Promotes code reuse and support polymorphism.
- Cons: Tight coupling; changes made in parent class can affect child class.

<br>

```py
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Bark"
```

<br>
<br>

#### Composition

- In composition, a class contains another class as an attribute.
- This is "**has-a**" type of relationship. Example, a `Car` has a `Engine`.
- Here, the behaviour is delegated to the contained class.
- Pros: Loose coupling, more flexible, easier to change or extend.
- Cons: Slightly more boilerplate code.

<br>

```py
class Engine:
    def start(self):
        return "Engine started"

class Car:
    def __init__(self):
        self.engine = Engine()

    def drive(self):
        return self.engine.start()
```

<br>
<br>

#### When to choose which?

- Use inheritance when:
    - There is a clear is-a relationship.
    - We want polymorphism across related types.
- Use composition when:
    - You want flexibility and reusability.
    - Behavior may change at runtime.

<br>
<br>

#### Pythonic principle

```
Favor composition over inheritance.
```

- In Python, composition is generally preferred unless inheritance is clearly justified.
- This aligns with Python's design philosophy: **Explicit is better than implicit.**
- Explicit is better than implicit means code should clearly express intent and behavior rather than relying on hidden assumptions or magic, making it easier to read, debug, and maintain.

```py
# ❌ Implicit
if len(items):
    ...

# ✅ Explicit
if len(items) > 0:
    ...
```
<br>
<br>
<br>

### Name Mangling

-Name mangling automatically renames private variables/methods with a double underscore (`__x`) to `_ClassName__x`.
- It helps avoid accidental overriding or name clashes in subclasses.
- It is not true privacy—the mangled name can still be accessed if needed.

```py
class A:
    def __init__(self):
        self.__x = 10

class B(A):
    def show(self):
        print(self._A__x)   # name-mangled access

b = B()
b.show()
```
<br>
<br>
<br>
