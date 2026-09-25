# Dependency Injection

## 1. What Is a Dependency?

A dependency is an object or component that another class **needs in order to perform its work**.

For example:

```python
class EmailService:

    def send(self, message):
        print(f"Sending: {message}")


class NotificationService:

    def __init__(self):
        self.email_service = EmailService()

    def notify(self, message):
        self.email_service.send(message)
```

`NotificationService` depends on `EmailService`.

Why?

Because `NotificationService` cannot perform its notification work without it.

So:

```text
NotificationService
        │
        │ depends on
        ▼
   EmailService
```

The important question is:

> **Who creates the dependency?**

In the example above, `NotificationService` creates it itself:

```python
self.email_service = EmailService()
```

That creates **tight coupling**.


## 2. What Is Dependency Injection?

**Dependency Injection (DI)** means:

> Instead of a class creating the objects it depends on, those dependencies are provided to it from outside.

Instead of:

```python
class NotificationService:

    def __init__(self):
        self.email_service = EmailService()
```

we write:

```python
class NotificationService:

    def __init__(self, email_service):
        self.email_service = email_service
```

Now someone outside the class creates the dependency:

```python
email_service = EmailService()

notification_service = NotificationService(email_service)
```

The dependency has been **injected** into `NotificationService`.

The relationship becomes:

```text
Outside code
     │
     │ creates
     ▼
EmailService
     │
     │ injects
     ▼
NotificationService
```

The class receives what it needs instead of constructing it itself.


## 3. Why Is Dependency Injection Needed?

Consider this tightly coupled design:

```python
class NotificationService:

    def __init__(self):
        self.email_service = EmailService()
```

The class is now directly tied to:

```text
EmailService
```

If we later want:

```text
SMSService
PushNotificationService
MockNotificationService
```

we have to modify `NotificationService`.

That means the class knows too much about the specific implementation.


## 4. Tight Coupling vs Loose Coupling

### Tight Coupling

```python
class NotificationService:

    def __init__(self):
        self.service = EmailService()
```

The class decides:

* which implementation to use
* how to create it
* when to create it

So:

```text
NotificationService
        │
        │ tightly coupled to
        ▼
   EmailService
```

---

### Loose Coupling

```python
class NotificationService:

    def __init__(self, service):
        self.service = service
```

Now:

```python
notification = NotificationService(EmailService())
```

or:

```python
notification = NotificationService(SMSService())
```

The `NotificationService` does not need to know how those objects are created.

```text
NotificationService
        │
        │ depends on behavior
        ▼
    dependency
    ▲        ▲
    │        │
 Email      SMS
```

This is the main benefit of DI:

> **The class depends on an abstraction or capability rather than controlling the creation of a specific implementation.**


## 5. Constructor Injection

**Constructor Injection** is the most important DI technique to learn first.

The dependency is passed through the constructor:

```python
class NotificationService:

    def __init__(self, sender):
        self.sender = sender
```

Then:

```python
sender = EmailService()

service = NotificationService(sender)
```

The dependency is available immediately after the object is created.

Conceptually:

```text
Object creation
      │
      ▼
Constructor receives dependencies
      │
      ▼
Object stores dependencies
      │
      ▼
Object is ready to use
```


## 6. Why Constructor Injection Is Usually Preferred

Constructor injection makes dependencies **explicit**.

Consider:

```python
class NotificationService:

    def __init__(self, sender):
        self.sender = sender
```

Someone reading the class immediately knows:

```text
NotificationService requires a sender.
```

Compare that with:

```python
class NotificationService:

    def notify(self, message):
        self.sender.send(message)
```

Where `self.sender` might be configured somewhere else.

Constructor injection makes the required dependencies visible at the point of construction.


## 7. Constructor Injection with an Abstract Class

This is where the previous topic becomes directly useful.

Suppose we define an abstraction:

```python
from abc import ABC, abstractmethod


class NotificationSender(ABC):

    @abstractmethod
    def send(self, message):
        pass
```

Concrete implementations:

```python
class EmailSender(NotificationSender):

    def send(self, message):
        print(f"Email: {message}")


class SMSSender(NotificationSender):

    def send(self, message):
        print(f"SMS: {message}")
```

Now the service depends on the abstraction:

```python
class NotificationService:

    def __init__(self, sender: NotificationSender):
        self.sender = sender

    def notify(self, message):
        self.sender.send(message)
```

We can inject different implementations:

```python
email_sender = EmailSender()
sms_sender = SMSSender()

email_service = NotificationService(email_sender)
sms_service = NotificationService(sms_sender)
```

Then:

```python
email_service.notify("Welcome!")
sms_service.notify("Your OTP is 1234")
```

Possible output:

```text
Email: Welcome!
SMS: Your OTP is 1234
```

The important architecture is:

```text
                 NotificationSender
                   <<abstract>>
                         ▲
              ┌──────────┴──────────┐
              │                     │
         EmailSender            SMSSender
              │                     │
              └──────────┬──────────┘
                         │
                    injected into
                         │
                         ▼
               NotificationService
```

This combines:

```text
Abstract Class
      +
Polymorphism
      +
Composition
      +
Dependency Injection
```


## 8. Dependency Injection Does Not Require an ABC

DI can also be used with ordinary concrete classes.

```python
class Logger:

    def log(self, message):
        print(message)


class UserService:

    def __init__(self, logger):
        self.logger = logger

    def create_user(self):
        self.logger.log("User created")
```

Usage:

```python
logger = Logger()
service = UserService(logger)

service.create_user()
```

There is no ABC here.

Therefore:

> **Dependency Injection and abstraction are separate concepts.**

You can use DI with:

* concrete classes
* abstract classes
* Protocols
* duck-typed objects


## 9. Dependency Injection and Composition

You learned in the previous topic that:

> **Composition means one object contains or uses another object.**

For example:

```python
class UserService:

    def __init__(self, logger):
        self.logger = logger
```

This is composition.

Now notice:

```python
logger = Logger()

service = UserService(logger)
```

The `Logger` object is supplied from outside.

That is Dependency Injection.

So:

```text
Composition
    ↓
UserService HAS-A Logger

Dependency Injection
    ↓
Logger is provided to UserService from outside
```

Therefore:

> **Dependency Injection is commonly a way of implementing composition while keeping object creation outside the dependent class.**


## 10. Without DI vs With DI

### Without Dependency Injection

```python
class OrderService:

    def __init__(self):
        self.logger = Logger()
        self.database = MySQLDatabase()
```

`OrderService` controls its dependencies.

```text
OrderService
 ├── creates Logger
 └── creates MySQLDatabase
```

This increases coupling.


### With Dependency Injection

```python
class OrderService:

    def __init__(self, logger, database):
        self.logger = logger
        self.database = database
```

Outside code:

```python
logger = Logger()
database = MySQLDatabase()

service = OrderService(logger, database)
```

Now:

```text
Application / Composition Root
        │
        ├── creates Logger
        ├── creates Database
        │
        └── injects them
                │
                ▼
          OrderService
```

The service focuses on its actual responsibility rather than constructing infrastructure.


## 11. Testing Becomes Easier

One major practical benefit of DI is **testability**.

Without DI:

```python
class UserService:

    def __init__(self):
        self.database = MySQLDatabase()
```

Testing the service may require a real database.

With DI:

```python
class UserService:

    def __init__(self, database):
        self.database = database
```

We can provide a fake implementation:

```python
class FakeDatabase:

    def save(self, user):
        print("Fake save")
```

Then:

```python
database = FakeDatabase()

service = UserService(database)
```

The service can be tested without connecting to a real database.

The important principle is:

```text
Production
    → real dependency

Testing
    → fake/mock dependency
```

The service itself does not need to change.


## 12. Other Forms of Dependency Injection

Constructor injection is the most important form.

There are also other techniques.

### Setter / Property Injection

The dependency is provided after object creation.

```python
class UserService:

    def set_logger(self, logger):
        self.logger = logger
```

Usage:

```python
service = UserService()
service.set_logger(Logger())
```

Potential problem:

```text
Object can exist before its required dependency exists.
```

So this is generally less explicit than constructor injection for required dependencies.


### Method Injection

A dependency is provided only for a particular operation.

```python
class ReportService:

    def generate(self, writer):
        writer.write("Report")
```

Here the `writer` is needed only by `generate()`.

This can be useful when a dependency is temporary or specific to one operation.


## 13. Constructor vs Setter vs Method Injection

| Technique                 | Dependency Provided  | Good For                                     |
| ------------------------- | -------------------- | -------------------------------------------- |
| Constructor injection     | Object creation      | Required dependencies                        |
| Setter/property injection | After creation       | Optional/changeable dependencies             |
| Method injection          | Specific method call | Temporary or operation-specific dependencies |

For your OOP foundation:

> **Master constructor injection first.**

The other two are useful to recognize but don't need the same depth.


## 14. Common Mistakes

### Mistake 1 — Creating the dependency inside the class

```python
class Service:

    def __init__(self):
        self.repository = MySQLRepository()
```

This defeats the main purpose of DI.

Prefer:

```python
class Service:

    def __init__(self, repository):
        self.repository = repository
```


### Mistake 2 — Injecting everything

DI does not mean every tiny object must be passed through a constructor.

Don't turn:

```python
class Calculator:
    ...
```

into a class with ten injected dependencies if it doesn't need them.

Use DI where it improves:

* coupling
* replaceability
* testing
* configuration


### Mistake 3 — Depending on concrete implementations unnecessarily

Instead of:

```python
class Service:

    def __init__(self, mysql_repository: MySQLRepository):
        ...
```

consider an abstraction when multiple implementations genuinely exist:

```python
class Service:

    def __init__(self, repository: Repository):
        ...
```

This connects DI with the previous topic on abstract classes and interfaces.


### Mistake 4 — Confusing DI with Dependency Inversion

These concepts are related but not identical.

```text
Dependency Injection
    ↓
A technique for supplying dependencies

Dependency Inversion Principle
    ↓
A design principle about high-level modules
depending on abstractions rather than low-level details
```

DI is one practical technique that can help implement the Dependency Inversion Principle.


## 15. Manual Dependency Injection

You do **not** need a framework to use DI.

This is already Dependency Injection:

```python
logger = Logger()
database = Database()

service = UserService(logger, database)
```

Python itself is enough.

For many Python applications, manually creating and passing dependencies is perfectly reasonable.

Frameworks and DI containers become useful when an application becomes large enough that managing object creation and dependency graphs becomes complicated.

For now:

> **Understand manual DI before learning DI frameworks.**


## 16. Composition Root

There is one useful architectural concept to know.

The part of the application responsible for creating and connecting objects is often called the **composition root**.

For example:

```python
def main():

    logger = Logger()
    database = Database()

    user_service = UserService(
        logger,
        database
    )

    user_service.create_user()
```

The application setup happens here.

The service itself does not create:

```text
Logger
Database
```

It simply receives them.

So:

```text
Composition Root
      │
      ├── creates dependencies
      │
      └── connects objects
              │
              ▼
         Application
```

You don't need to memorize the term deeply yet. Just understand the architectural idea.


## 17. Complete Example

Let's put the important pieces together.

```python
from abc import ABC, abstractmethod


class Repository(ABC):

    @abstractmethod
    def save(self, data):
        pass


class DatabaseRepository(Repository):

    def save(self, data):
        print(f"Saving '{data}' to database")


class InMemoryRepository(Repository):

    def save(self, data):
        print(f"Saving '{data}' to memory")


class UserService:

    def __init__(self, repository: Repository):
        self.repository = repository

    def create_user(self, name):
        self.repository.save(name)


# Composition root

database_repository = DatabaseRepository()

user_service = UserService(database_repository)

user_service.create_user("Rajendra")
```

Output:

```text
Saving 'Rajendra' to database
```

We can change the implementation without changing `UserService`:

```python
memory_repository = InMemoryRepository()

user_service = UserService(memory_repository)

user_service.create_user("Rajendra")
```

Output:

```text
Saving 'Rajendra' to memory
```

The important part is:

```text
UserService
     │
     │ depends on
     ▼
 Repository
     ▲
     │
 ┌───┴─────────────────┐
 │                     │
DatabaseRepository  InMemoryRepository
```

And the dependency is supplied externally:

```text
Outside code
     │
     ├── creates repository
     │
     └── injects repository
              │
              ▼
         UserService
```
