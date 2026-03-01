## Index
- [SOLID Principles](#solid-principles)
- [Design Patterns](#design-patterns)
- [C - Factory](#c---factory)

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


<br>


### C - Factory

- It defines an interface for creating objects but lets subclasses decide which concrete class to instantiate.
- It replaces direct object creation with a factory method call.
- The method returns objects that share a common interface (Product).
- The Creator class contains core business logic and calls the factory method within that workflow.
- Primarily used in framework or extensible system design where the base class defines the workflow and subclasses customize object creation.
- https://refactoring.guru/images/patterns/diagrams/factory-method/structure-2x.png

```py
from abc import ABC, abstractmethod

# Product
class Notification(ABC):
    @abstractmethod
    def send(self):
        pass

# Concrete Products
class EmailNotification(Notification):
    def send(self):
        print("Sending Email")

class SMSNotification(Notification):
    def send(self):
        print("Sending SMS")

# Creator
class NotificationService(ABC):

    def notify(self):
        notification = self.create_notification()
        notification.send()

    @abstractmethod
    def create_notification(self) -> Notification:
        pass

# Concrete Creators
class EmailService(NotificationService):
    def create_notification(self):
        return EmailNotification()

class SMSService(NotificationService):
    def create_notification(self):
        return SMSNotification()

# Client Code
email_service = EmailService()
email_service.notify()
```

- `NotificationService` defines the workflow (`notify()`).
- Subclasses decide which notification to create.
- No conditional logic is used.
- New notification types can be added without modifying existing classes.


<br>

