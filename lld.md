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
- [S - Decorator](#s---decorator)
- [S - Composite](#s---composite)
- [S - Proxy](#s---proxy)
- [B - Observer](#b---observer)
- [B - Strategy](#b---strategy)


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


### S - Decorator

**Overview**
- It allows to add new behaviour to objects, without modifying their existing code.
- It wraps around exisiting code to add more functionality.
- It is used in place of creating all the permutations of subclasses.
- https://refactoring.guru/images/patterns/diagrams/decorator/structure-2x.png

**When to use**
- You want to add responsibilities to objects dynamically.
- You want to follow the **Open/Closed Principle**: classes should be open for extension, but closed for modification.
- Subclassing would lead to an explosion of classes to cover all combinations of behaviors.

**Key components**
- Component: The original object interface.
- ConcreteComponent: The actual object being wrapped.
- Decorator: Has a reference to a `Component` and implements the same interface.
- ConcreteDecorator: Extends the behavior of the Component.

```py
from abc import ABC, abstractmethod

# Component
class Handler(ABC):
    @abstractmethod
    def handle(self, request):
        pass

# Concrete Component
class BasicHandler(Handler):
    def handle(self, request):
        return f"Handling request: {request}"

# Base Decorator
class HandlerDecorator(Handler):
    def __init__(self, handler):
        self._handler = handler

    def handle(self, request):
        return self._handler.handle(request)

# Concrete Decorator 1: Logging
class LoggingDecorator(HandlerDecorator):
    def handle(self, request):
        print(f"[LOG] Received request: {request}")
        response = self._handler.handle(request)
        print(f"[LOG] Response: {response}")
        return response

# Concrete Decorator 2: Authentication
class AuthDecorator(HandlerDecorator):
    def handle(self, request):
        user = request.get("user")
        if not user or not user.get("is_authenticated"):
            return "401 Unauthorized"
        return self._handler.handle(request)

# --- Client Code ---

# A simulated request object
request = {
    "user": {"name": "Alice", "is_authenticated": True},
    "payload": "GET /home"
}

# Compose decorators
handler = BasicHandler()
handler = AuthDecorator(handler)
handler = LoggingDecorator(handler)

# Execute request
print(handler.handle(request))


# Output:
# [LOG] Received request: {'user': {'name': 'Alice', 'is_authenticated': True}, 'payload': 'GET /home'}
# [LOG] Response: Handling request: {'user': {'name': 'Alice', 'is_authenticated': True}, 'payload': 'GET /home'}
# Handling request: {'user': {'name': 'Alice', 'is_authenticated': True}, 'payload': 'GET /home'}

# Unauthenticated output:
# "401 Unauthorized"
```


<br>


### S - Composite

**Overview**
- It treats individual objects and compositions of objects (i.e groups of objects) uniformly.
- It is useful when dealing with tree structures like **file systems**, **GUI elements**, etc.
- Here, both the leaf node (file, button) and composite node (folder, div) should be treated similarly.
- https://refactoring.guru/images/patterns/diagrams/composite/structure-en-1.5x.png?id=8e06210d59cadf817621d810accfa5f6

**Components**
- Component: Interface for all objects in the composition.
- Leaf: Leaf object in the composition.
- Composite: Composite object. Has a list of children.

```py
from abc import ABC, abstractmethod

# Component
class FileSystemComponent(ABC):
    @abstractmethod
    def show(self, indent=0):
        pass

# Leaf
class File(FileSystemComponent):
    def __init__(self, name):
        self.name = name

    def show(self, indent=0):
        print(' ' * indent + f"File: {self.name}")

# Composite
class Directory(FileSystemComponent):
    def __init__(self, name):
        self.name = name
        self.children = []

    def add(self, component: FileSystemComponent):
        self.children.append(component)

    def remove(self, component: FileSystemComponent):
        self.children.remove(component)

    def show(self, indent=0):
        print(' ' * indent + f"Directory: {self.name}")
        for child in self.children:
            child.show(indent + 2)

# Usage
if __name__ == "__main__":
    file1 = File("file1.txt")
    file2 = File("file2.txt")
    file3 = File("file3.txt")

    dir1 = Directory("Documents")
    dir1.add(file1)
    dir1.add(file2)

    dir2 = Directory("Downloads")
    dir2.add(file3)
    dir2.add(dir1)  # Nested directory

    dir2.show()
```


<br>


### S - Proxy

**Overview**
- It lets you provide a substitute or placeholder for another object
- A proxy controls access to the original object, allowing you to perform something either before or after the request gets through to the original object.
- The proxy implements the same interface as the real object, and the client interacts with the proxy instead of the real object.
- Useful when direct access to an object is expensive, sensitive, or needs control.
- https://refactoring.guru/images/patterns/diagrams/proxy/structure-1.5x.png?id=0db7bf3c1193b2a1961894349f1e07ad

**Types of Proxy**
- Lazy Initialization (Virtual Proxy) → Use when object creation is expensive; delay instantiation until actually needed.
- Access Control (Protection Proxy) → Use when you need to restrict access based on permissions or credentials.
- Remote Proxy → Use when interacting with objects located on remote servers; hides network complexity from client.
- Logging Proxy → Use when you want to track or log requests made to an object without modifying it.
- Caching Proxy → Use when repeated requests return same result; cache responses to improve performance.

**Components**
- Subject: Common interface for the Real Object and Proxy.
- Real Subject: The actual object that performs the real operations.
- Proxy: Controls access to the Real Subject.

```py
from abc import ABC, abstractmethod
import time

# Subject
class Image(ABC):
    @abstractmethod
    def display(self):
        pass

# Real Subject
class RealImage(Image):
    def __init__(self, filename):
        self.filename = filename
        self.load_from_disk()

    def load_from_disk(self):
        print(f"Loading image from disk: {self.filename}")
        time.sleep(1)  # Simulate delay

    def display(self):
        print(f"Displaying image: {self.filename}")

# Proxy
class ProxyImage(Image):
    def __init__(self, filename):
        self.filename = filename
        self.real_image = None

    def display(self):
        if self.real_image is None:
            print("Creating RealImage object...")
            self.real_image = RealImage(self.filename)
        else:
            print("RealImage already created.")
        self.real_image.display()

# Usage
if __name__ == "__main__":
    print("Creating proxy...")
    image = ProxyImage("photo.jpg")
    
    print("\nFirst display call (loads image):")
    image.display()
    
    print("\nSecond display call (uses cached image):")
    image.display()
```

**Explanation**
- `Image` is our subject interface.
- `RealImage` is the real subject.
- `ProxyImage` is the proxy that controls the access to `RealImage`.


<br>


### B - Observer

**Overview**
- It allows **Subject** to notify a list of **Observers** when an event occures in subject (Change of State).
- Subject & Observers are coupled loosly.
- Promotes **Open/Close** principle, easy to add new observers without modifying the Subject.
- https://refactoring.guru/images/patterns/diagrams/observer/structure-2x.png?id=228af9bded4d6ee6daf43a0e23cca9ff.

**Components**
- Subject: interface for subject.
- Observer: interface for observer.
- ConcreteSubject (Publisher): maintains a list of observers and notifies them of any changes.
- ConcreteObserver (Subscriber): object that should be notified.

```py
from abc import ABC, abstractmethod

# Observer Interface
class Observer(ABC):
    @abstractmethod
    def update(self, message: str):
        pass

# Concrete Observer
class EmailSubscriber(Observer):
    def __init__(self, name):
        self.name = name

    def update(self, message: str):
        print(f"{self.name} received email notification: {message}")

# Subject Interface
class Subject(ABC):
    @abstractmethod
    def attach(self, observer: Observer):
        pass

    @abstractmethod
    def detach(self, observer: Observer):
        pass

    @abstractmethod
    def notify(self, message: str):
        pass

# Concrete Subject
class YouTubeChannel(Subject):
    def __init__(self):
        self.subscribers = []

    def attach(self, observer: Observer):
        self.subscribers.append(observer)

    def detach(self, observer: Observer):
        self.subscribers.remove(observer)

    def notify(self, message: str):
        for subscriber in self.subscribers:
            subscriber.update(message)

    # Business logic that changes state
    def upload_video(self, video_title: str):
        print(f"Channel: Uploaded a new video: {video_title}")
        self.notify(f"New video uploaded: {video_title}")

# === Usage ===
if __name__ == "__main__":
    # Create subject
    channel = YouTubeChannel()

    # Create observers
    alice = EmailSubscriber("Alice")
    bob = EmailSubscriber("Bob")

    # Attach observers to subject
    channel.attach(alice)
    channel.attach(bob)

    # Change state
    channel.upload_video("Observer Pattern in Python")
```

**Explanation**
- `Observer` is the observer interface and `EmailSubscriber` is the concrete implementation.
- `Subject` is the subject interface and `YouTubeChannel` is the concrete implementation.
- `alice` and `bob` are notified when a new video is uploaded.

**When to use**
- Use when a change in one object’s state requires updates in multiple dependent objects, and the set of dependent objects is not fixed or may change dynamically.
- Use when objects need to observe another object temporarily or conditionally, allowing them to subscribe and unsubscribe at runtime.


<br>


### B - Strategy

**Overview**
- It defines a family of algorithms and makes them interchangeable at runtime.
- It encapsulates each algorithm into a separate class (strategy).
- The client selects the required strategy instead of using conditional logic.
- Promotes composition over inheritance.
- Allows changing behavior dynamically at runtime.
- https://refactoring.guru/images/patterns/diagrams/strategy/structure-2x.png?id=5bd791857c3bab419bcf4fa86877439d

**Components**
- Strategy Interface → Common interface for all algorithms
- Concrete Strategies → Different implementations of the algorithm
- Context → Uses a strategy object
- Client → Chooses which strategy to use

```py
# Strategy
class PaymentStrategy(ABC):
    @abstractmethod
    def pay(self, amount):
        pass

# Concrete Strategies
class CreditCardPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paid {amount} using Credit Card")

class UpiPayment(PaymentStrategy):
    def pay(self, amount):
        print(f"Paid {amount} using UPI")

# Context
class PaymentService:
    def __init__(self, strategy: PaymentStrategy):
        self.strategy = strategy

    def process_payment(self, amount):
        self.strategy.pay(amount)

# Client code
payment = PaymentService(CreditCardPayment())
payment.process_payment(100)

payment.strategy = UpiPayment()  # change strategy at runtime
payment.process_payment(200)
```


<br>



