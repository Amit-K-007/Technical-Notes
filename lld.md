## Index
- [SOLID Principles](#solid-principles)
- [Design Patterns](#design-patterns)
- [Creational Patterns](#creational-patterns)
- [C - Factory Method](#c---factory-method)
- [C - Abstract Factory](#c---abstract-factory)
- [C - Singleton](#c---singleton)
- [C - Builder](#c---builder)
- [C - Prototype](#c---prototype)
- [S - Adapter](#s---adapter)

<br>


### SOLID Principles

#### Single Responsibility Principle (SRP)

- A class should have only one job or responsibility.
- It should have only one reason to change.
- This keeps the code modular, easy to maintain & test.
- If a class takes care of more than one task, then you should separate those tasks into dedicated classes with descriptive names.

```py
# Violates SRP (File I/O and ZIP handling)

class FileManager:
    def __init__(self, filename):
        self.path = Path(filename)

    def read(self, encoding="utf-8"):
        return self.path.read_text(encoding)

    def compress(self):
        with ZipFile(self.path.with_suffix(".zip"), mode="w") as archive:
            archive.write(self.path)
```

```py 
# Follows SRP (Separate classes having separate responsibilities)

class FileManager:
    def __init__(self, filename):
        self.path = Path(filename)

    def read(self, encoding="utf-8"):
        return self.path.read_text(encoding)

class ZipFileManager:
    def __init__(self, filename):
        self.path = Path(filename)

    def compress(self):
        with ZipFile(self.path.with_suffix(".zip"), mode="w") as archive:
            archive.write(self.path)
```

#### Open/Closed Principle (OCP)

- Modules, classes & functions should be open for extension but closed for modification.
- New behaviour should be added by extending existing code, not changing it.
- This keeps the code stable and flexible.

```py
# Violates OCP (Adding more shapes requires class to modify)

class Shape:
    def __init__(self, shape_type, **kwargs):
        self.shape_type = shape_type
        if self.shape_type == "rectangle":
            self.width = kwargs["width"]
            self.height = kwargs["height"]
        elif self.shape_type == "circle":
            self.radius = kwargs["radius"]
        else:
            raise TypeError("Unsupported shape type")

    def calculate_area(self):
        if self.shape_type == "rectangle":
            return self.width * self.height
        elif self.shape_type == "circle":
            return pi * self.radius**2
        else:
            raise TypeError("Unsupported shape type")
```

```py
# Follows OCP (Can be extended by adding class for other shapes)

class Shape(ABC):
    def __init__(self, shape_type):
        self.shape_type = shape_type

    @abstractmethod
    def calculate_area(self):
        pass

class Circle(Shape):
    def __init__(self, radius):
        super().__init__("circle")
        self.radius = radius

    def calculate_area(self):
        return pi * self.radius**2

class Rectangle(Shape):
    def __init__(self, width, height):
        super().__init__("rectangle")
        self.width = width
        self.height = height

    def calculate_area(self):
        return self.width * self.height
```

#### Liskov Substitution Principle (LSP)

- Objects of parent class should be replaceable with objects of child class.
- Child classes must follow the behaviour (methods) expected from the parent class.
- This prevents unexpected bugs due to broken inheritance

```py
# Violates LSP

class Bird:
    def fly(self):
        print("Flying")

class Ostrich(Bird):
    def fly(self):
        raise Exception("Ostrich cannot fly")

# Problem: Ostrich is a Bird, but it cannot fly. If a function expects a Bird and calls fly(), it will break.

def make_bird_fly(bird: Bird):
    bird.fly()
```

```py
# Follows LSP

class Bird:
    pass

class FlyingBird(Bird):
    def fly(self):
        print("Flying")

class Sparrow(FlyingBird):
    pass

class Ostrich(Bird):
    pass

# Only flying birds are passed. Subclasses properly respect base class expectations.

def make_bird_fly(bird: FlyingBird):
    bird.fly()
```

#### Interface Segragation Principle (ISP)

- A child class should not be forced to implement the methods it does not need.
- Prefer many small, specific interfaces over a large general-purpose one.
- This reduces the impact of changes and improves code clarity.

```py
# Violates ISP (It forces OldPrinter to expose an interface that the class doesn’t implement or need.)

class Printer(ABC):
    @abstractmethod
    def print(self, document):
        pass

    @abstractmethod
    def fax(self, document):
        pass

class OldPrinter(Printer):
    def print(self, document):
        print(f"Printing {document} in black and white...")

    def fax(self, document):
        raise NotImplementedError("Fax functionality not supported")

class ModernPrinter(Printer):
    def print(self, document):
        print(f"Printing {document} in color...")

    def fax(self, document):
        print(f"Faxing {document}...")
```

```py
# Follows ISP (separates the interfaces)

class Printer(ABC):
    @abstractmethod
    def print(self, document):
        pass

class Fax(ABC):
    @abstractmethod
    def fax(self, document):
        pass

class OldPrinter(Printer):
    def print(self, document):
        print(f"Printing {document} in black and white...")

class NewPrinter(Printer, Fax):
    def print(self, document):
        print(f"Printing {document} in color...")

    def fax(self, document):
        print(f"Faxing {document}...")
```

#### Dependency Inversion Principle (DIP)

- Higher level classes (business logic) should not depend on lower level classes (UI).
- Lower level classes should depend on higher level classes through abstract classes.
- This promotes decoupling & code reusability.

```py
# Violates DIP (Tight coupling with MySQLDatabase)

class MySQLDatabase:
    def save(self, data):
        print("Saving to MySQL")

class UserService:
    def __init__(self):
        self.db = MySQLDatabase()
```

```py
# Follows DIP (UserService depends on Abstraction)

class Database(ABC):
    @abstractmethod
    def save(self, data):
        pass

class MySQLDatabase(Database):
    def save(self, data):
        print("Saving to MySQL")

class PostgreSQLDatabase(Database):
    def save(self, data):
        print("Saving to PostgreSQL")

class UserService:
    def __init__(self, db: Database):
        self.db = db
```


<br>


### Design Patterns
- Reference: https://refactoring.guru/design-patterns/catalog
- Video Reference: https://www.youtube.com/playlist?list=PLrhzvIcii6GNjpARdnO4ueTUAVR9eMBpc


<br>


### Creational Patterns
- It deals with object creation mechanisms.
- Main Creational Patterns:
1. **Singleton** → Ensures a class has only one instance and provides global access to it.
2. **Factory Method** → Lets subclasses decide which object to instantiate.
3. **Abstract Factory** → Creates families of related objects without specifying their concrete classes.
4. **Builder** → Constructs complex objects step by step.
5. **Prototype** → Creates objects by cloning existing instances.


<br>


### C - Factory Method

**Overview**
- It is a creational pattern that defines an interface for creating objects but lets subclasses decide which concrete class to instantiate.
- It replaces direct object creation with a factory method call, removing conditional logic from client code.
- The factory method returns objects that share a common interface (Product), ensuring polymorphism.
- The Creator class contains core business logic and calls the factory method as part of its workflow.
- Used when object creation logic may vary, is not known in advance, or when designing extensible/framework-level systems where subclasses customize instantiation.
- https://refactoring.guru/images/patterns/diagrams/factory-method/structure-2x.png

```py
# Product
class Animal(ABC):
    @abstractmethod
    def speak(self):
        pass

# Concrete Products
class Dog(Animal):
    def speak(self):
        return "Woof"

class Cat(Animal):
    def speak(self):
        return "Meow"

# Creator
class AnimalCreator(ABC):
    def generate_animal_sound(self):
        animal = self.create_animal()
        return animal.speak()

    @abstractmethod
    def create_animal(self) -> Animal:
        pass

# Concrete Creators
class RandomAnimalCreator(AnimalCreator):
    def create_animal(self):
        return random.choice([Dog(), Cat()])

class BalancedAnimalCreator(AnimalCreator):
    _toggle = True

    def create_animal(self):
        if BalancedAnimalCreator._toggle:
            BalancedAnimalCreator._toggle = False
            return Dog()
        else:
            BalancedAnimalCreator._toggle = True
            return Cat()
```

**Explanation**
- `AnimalCreator` defines the workflow (`generate_animal_sound()`).
- Subclasses decide how animals are instantiated
- Business logic of selection varies between subclasses.
- No conditional logic in client code.
- New animal creation strategies can be added without modifying existing classes.


<br>


### C - Abstract Factory

**Overview**
- It defines an interface for creating families of related objects.
- Factory pattern focuses on creating individual objects (e.g. button), while abstract factory focuses on creating a set of related objects (e.g. button & input & popup).
- It is like a factory for factories.
- https://refactoring.guru/images/patterns/diagrams/abstract-factory/structure-1.5x.png

```py
from abc import ABC, abstractmethod

class Button(ABC):
    """Abstract Product"""

    @abstractmethod
    def click(self):
        raise NotImplementedError()

class MacButton(Button):
    """Concrete Product"""

    def click(self):
        print("Mac button clicked")

class WindowsButton(Button):
    """Concrete Product"""

    def click(self):
        print("Windows button clicked")

class Input(ABC):
    """Abstract Product"""

    @abstractmethod
    def validate(self):
        raise NotImplementedError()

class MacInput(Input):
    """Concrete Product"""

    def validate(self):
        print("Mac input validated")

class WindowsInput(Input):
    """Concrete Product"""

    def validate(self):
        print("Windows input validated")

class AbstractGUIFactory(ABC):
    """Abstract Factory"""

    @abstractmethod
    def create_button(self) -> Button:
        raise NotImplementedError()

    @abstractmethod
    def create_input(self) -> Input:
        raise NotImplementedError()

class MacGUIFactory(AbstractGUIFactory):
    """Concrete Factory"""

    def create_button(self) -> Button:
        return MacButton()

    def create_input(self) -> Input:
        return MacInput()

class WindowsGUIFactory(AbstractGUIFactory):
    """Concrete Factory"""

    def create_button(self) -> Button:
        return WindowsButton()

    def create_input(self) -> Input:
        return WindowsInput()

def process_form(factory: AbstractGUIFactory):
    input = factory.create_input()
    input.validate()
    button = factory.create_button()
    button.click()

factory = WindowsGUIFactory()
# factory = MacGUIFactory()  # easily replacable

process_form(factory)
# Output:
# Mac input validated
# Mac button clicked
```

**Explanation**
- `Button`, `Input`: These are abstract products that a factory will create.
- `WindowsButton`, `MacButton`, etc.: These are the actual implementations of the products for each platform.
- `AbstractGUIFactory`: This is an abstract factory or factory for factories. It has methods for creating the abstract products.
- `WindowsGUIFactory`, `MacGUIFactory`: These are concrete factories.
- `process_form`: This function is used to render a form using given factory.

<br>

**Advantages**
- Separation of concerns: Each factory handles its own plotform.
- Extensibility: New platform like Linux can easily be added with modifying existing code.
- Interchangeability: One factory can easily be switch with another factory.


<br>


### C - Singleton

**Overview**
- It ensures a class has only one instance and provides a global access point to it.
- It restricts object creation by controlling instantiation internally (usually via a static method and private constructor).
- The single instance is typically stored as a class-level variable.
- Used when exactly one shared resource is required across the system.
- Common use cases include configuration managers, logging systems, database connection managers, and caching layers.
- Instance creation should be made thread-safe, while using with multithreading.
- https://refactoring.guru/images/patterns/diagrams/singleton/structure-en-2x.png?id=cae4853e43f06db09f249668c35d61a1

```py
class MyClass:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

a = MyClass()
b = MyClass()

print(a is b)  # True
```

**Explanation**
- `_instance` class variable holds the class instance.
- Whenever a new object of class is to be created, `__new__()` method is called. Inside this method, we check if class instance is already created.
- If the instance is not created, we create a new class instance, assign it to `_instance` variable and return it.
- If the instance is already created, we simply return that instance.
- `super()` is used to call the `__new__()` method of the parent class (which is default `object` class in above example)


<br>


### C - Builder

**Overview**
- It is used to construct complex objects step by step.
- It separates object construction from its representation (final configuration of the object).
- The same construction process can create different representations.
- It avoids constructors with too many parameters (Telescoping Constructor problem).
- Primarily used when an object has many optional fields or complex setup logic.
- https://refactoring.guru/images/patterns/diagrams/builder/structure-2x.png

**Stucture**
- **Product** → The complex object being built
- **Builder Interface** → Defines construction steps
- **Concrete Builder** → Implements the building steps
- **Director** (Optional) → Controls the building sequence

```py
# Product
class Computer:
    def __init__(self):
        self.cpu = None
        self.ram = None
        self.storage = None
        self.gpu = None

    def __str__(self):
        return f"CPU: {self.cpu}, RAM: {self.ram}, Storage: {self.storage}, GPU: {self.gpu}"

# Builder
class ComputerBuilder:
    def __init__(self):
        self.reset()

    def reset(self):
        self.computer = Computer()

    def add_cpu(self, cpu):
        self.computer.cpu = cpu

    def add_ram(self, ram):
        self.computer.ram = ram

    def add_storage(self, storage):
        self.computer.storage = storage

    def add_gpu(self, gpu):
        self.computer.gpu = gpu

    def get_result(self):
        result = self.computer
        self.reset()          # prepare builder for next product
        return result

# Director
class Director:
    def __init__(self, builder: ComputerBuilder):
        self.builder = builder

    def build_gaming_pc(self):
        self.builder.add_cpu("Intel i9")
        self.builder.add_ram("32GB")
        self.builder.add_storage("1TB SSD")
        self.builder.add_gpu("NVIDIA RTX 4090")

    def build_office_pc(self):
        self.builder.add_cpu("Intel i5")
        self.builder.add_ram("16GB")
        self.builder.add_storage("512GB SSD")

# Client Code
builder = ComputerBuilder()
director = Director(builder)

director.build_gaming_pc()
gaming_pc = builder.get_result()
print(gaming_pc)

director.build_office_pc()
office_pc = builder.get_result()
print(office_pc)

# CPU: Intel i9, RAM: 32GB, Storage: 1TB SSD, GPU: NVIDIA RTX 4090
# CPU: Intel i5, RAM: 16GB, Storage: 512GB SSD, GPU: None
```

- `Computer`: The complex object being built.
- `ComputerBuilder`: Provides methods to construct parts of the product and stores it internally.
- `Director`: Defines the order of construction steps but does not return the product.


<br>


### C - Prototype

**Overview**
- It creates new objects by cloning an existing object (prototype) instead of creating them from scratch.
- It avoids costly object creation when object initialization is expensive or complex.
- The prototype object exposes a `clone()` method that returns a copy of itself, by using a copy constructor (constructor that accepts another object).
```
# Main constructor
ConcretePrototype()

# Copy constructor
ConcretePrototype(Prototype source) {
    # Copy fields from source object
}

# Clone method
clone() {
    return new ConcretePrototype(this)
}
```
- But, as python don't support constructor overloading, we can use `deepcopy` module.
- https://refactoring.guru/images/patterns/diagrams/prototype/structure-2x.png

**Structure**
- Prototype Interface → Declares the `clone()` method
- Concrete Prototype → Implements cloning logic
- Client → Creates objects by cloning prototypes

**When to use**
- When object creation is costly or time-consuming.
- When classes are unknown at runtime, coming through common interface
- When creating an object involves a complex configuration.

```py
import copy

class Car:
    def __init__(self, brand, model, features):
        self.brand = brand
        self.model = model
        self.features = features  # list of features

    def __str__(self):
        return f"{self.brand} {self.model} with features {self.features}"

    def clone(self):
        # Return a deep copy of the object
        return copy.deepcopy(self)

# Create a prototype car
prototype_car = Car(
    brand="Tesla",
    model="Model S",
    features=["Autopilot", "Electric", "Panoramic Roof"],
)

# Clone the prototype to create a new car
car1 = prototype_car.clone()
car1.model = "Model X"
car1.features.append("ABS")

print(prototype_car)  # Original remains unchanged
print(car1)  # Modified clone

# Output:
# Tesla Model S with features ['Autopilot', 'Electric', 'Panoramic Roof']
# Tesla Model X with features ['Autopilot', 'Electric', 'Panoramic Roof', 'ABS']
```

**Explanation**
- `Car` is our product.
- `prototype_car` is the prototype object. New objects will be cloned from this object.
- `car1` is the new object created via cloning `prototype_car`.
- After creating `car1`, we can modify it's properties without affecting the prototype.


<br>


### S - Adapter

**Overview**
- It allows objects with incompatible interfaces to work together.
- It converts/wraps the interface of a class into another interface that the client expects.
- Just like a universal socket adapter acts as a bridge between US electricity plug and an Indian cord.
- https://refactoring.guru/images/patterns/diagrams/adapter/structure-object-adapter-2x.png

**When to use**
- You want to reuse an existing class but its interface doesn’t match the one you need.
- You need to integrate a third-party class with your system. (e.g. custom frappe client).

**Components**
- **Target Interface:** The interface your client code expects.
- **Adaptee:** The existing class with an incompatible interface.
- **Adapter:** A class that implements the target interface and translates the calls to the adaptee.

```py
# Target Interface
class PaymentProcessor:
    def pay(self, amount):
        pass

# Adaptee (Existing class with incompatible interface)
class PayPalGateway:
    def make_payment(self, amount):
        print(f"Payment of {amount} done using PayPal")

# Adapter
class PayPalAdapter(PaymentProcessor):
    def __init__(self, paypal_gateway):
        self.paypal_gateway = paypal_gateway

    def pay(self, amount):
        self.paypal_gateway.make_payment(amount)

# Client Code
gateway = PayPalGateway()
payment = PayPalAdapter(gateway)

payment.pay(100)
# Payment of 100 done using PayPal
```

**Explanation**
- **PaymentProcessor** → Target interface expected by the system.
- **PayPalGateway** → Existing class with incompatible method `make_payment()`.
- **PayPalAdapter** → Converts `pay()` calls into `make_payment()`.
- The client only interacts with the target interface.

**Types of Adapter**
1. **Object Adapter (Composition)**
- It is the above introduced type of adapter, used most commonly.
- The adapter contains an instance of the adaptee and forwards calls to it.

2. **Class Adapter (Multiple Inheritance)**
- The adapter inherits from both Target and Adaptee and adapts the method directly.
- This works in languages that support multiple inheritance (like Python or C++).

```py
# Target Interface
class PaymentProcessor:
    def pay(self, amount):
        pass

# Adaptee
class PayPalGateway:
    def make_payment(self, amount):
        print(f"Payment of {amount} done using PayPal")

# Adapter using multiple inheritance
class PayPalAdapter(PaymentProcessor, PayPalGateway):

    def pay(self, amount):
        self.make_payment(amount)

# Client
payment = PayPalAdapter()
payment.pay(100)
```


<br>

