# Composition vs Inheritance in Python

Composition and inheritance are two different ways of designing relationships between classes.

Both allow us to build larger and more useful objects from existing classes, but they represent **different kinds of relationships** and should be used for different design situations.

The central question is:

> **Should one class be a specialized version of another class, or should one class contain and use another object?**

Understanding this distinction is important because choosing inheritance when composition is more appropriate can make software unnecessarily rigid and difficult to maintain.


## 1. The Two Fundamental Relationships

Before comparing composition and inheritance, understand the two relationships they usually represent.

### 1.1 "Is-A" Relationship

Inheritance generally represents an **is-a relationship**.

For example:

```text
Dog is an Animal
Car is a Vehicle
Manager is an Employee
```

If `Dog` inherits from `Animal`:

```python
class Animal:
    pass


class Dog(Animal):
    pass
```

we are saying:

> A `Dog` is a specialized form of `Animal`.

The child class represents a more specific version of the parent class.


### 1.2 "Has-A" Relationship

Composition generally represents a **has-a relationship**.

For example:

```text
Car has an Engine
Computer has a CPU
Order has a PaymentMethod
House has a Room
```

For example:

```python
class Engine:
    pass


class Car:

    def __init__(self):
        self.engine = Engine()
```

Here:

```text
Car
 │
 └── has an → Engine
```

A `Car` is not an `Engine`.

Instead:

> A `Car` contains or uses an `Engine`.

This distinction is the foundation for deciding between inheritance and composition.

## 2. Inheritance

### 2.1 Definition

**Inheritance is an OOP mechanism in which one class derives from another class and receives its attributes and methods, allowing the child class to specialize or extend the behavior of the parent.**

Example:

```python
class Employee:

    def work(self):
        print("Employee is working")


class Developer(Employee):

    def write_code(self):
        print("Developer is writing code")
```

A `Developer` is an `Employee`, so inheritance represents a meaningful conceptual relationship:

```text
Employee
   │
   └── Developer
```

The `Developer` class can use functionality defined by `Employee`:

```python
developer = Developer()

developer.work()
developer.write_code()
```

Inheritance is therefore useful when the child genuinely represents a specialized form of the parent.

## 3. Composition

### 3.1 Definition

**Composition is an object-oriented design technique in which a class contains references to objects of other classes and uses those objects to provide its functionality.**

Instead of saying:

```text
Class A is a Class B
```

composition says:

```text
Class A has a Class B
```

Example:

```python
class Engine:

    def start(self):
        print("Engine started")


class Car:

    def __init__(self):
        self.engine = Engine()

    def start(self):
        self.engine.start()
        print("Car started")
```

Here:

```text
Car
 │
 └── Engine
```

The `Car` object contains an `Engine` object and delegates engine-related work to it.

Usage:

```python
car = Car()
car.start()
```

Output:

```text
Engine started
Car started
```

The important point is that `Car` does not inherit from `Engine`.

It **uses** an `Engine`.


## 4. Why Composition Is Important

Composition allows a class to build its behavior by combining smaller, independent objects.

For example:

```python
class Logger:

    def log(self, message):
        print(f"LOG: {message}")


class UserService:

    def __init__(self):
        self.logger = Logger()

    def create_user(self):
        self.logger.log("Creating user")
```

`UserService` does not need to inherit from `Logger`.

Instead, it uses a `Logger`.

This separates responsibilities:

```text
UserService
     │
     └── uses → Logger
```

Each class has a more focused responsibility.

This can make systems easier to change because the components are not tightly tied together through an inheritance hierarchy.


## 5. Composition and Delegation

Composition often works together with **delegation**.

### Definition

**Delegation means that an object passes responsibility for a particular operation to another object that specializes in performing that operation.**

For example:

```python
class PaymentProcessor:

    def process(self, amount):
        print(f"Processing payment of ₹{amount}")


class Order:

    def __init__(self):
        self.payment_processor = PaymentProcessor()

    def pay(self, amount):
        self.payment_processor.process(amount)
```

When we call:

```python
order = Order()
order.pay(1000)
```

`Order` does not implement the payment-processing logic itself.

Instead, it delegates that responsibility to:

```python
PaymentProcessor
```

This gives us:

```text
Order
  │
  └── PaymentProcessor
          │
          └── process()
```

Delegation is one of the most common practical uses of composition.


## 6. Problems That Can Arise With Inheritance

Inheritance is useful, but using it simply for code reuse can create design problems.

Consider:

```python
class Bird:

    def fly(self):
        print("Flying")
```

We might initially create:

```python
class Sparrow(Bird):
    pass
```

This makes sense because a sparrow can fly.

But suppose we create:

```python
class Penguin(Bird):
    pass
```

Now `Penguin` inherits:

```python
fly()
```

even though a penguin cannot fly.

The problem is not Python's inheritance mechanism.

The problem is that the inheritance relationship does not accurately represent the domain.

The parent class contains behavior that is not appropriate for every child.

This is a sign that the abstraction may be wrong.


## 7. Composition Can Avoid Unnecessary Inheritance

Instead of putting optional behavior into a common parent, we can model capabilities separately.

For example:

```python
class Flying:

    def fly(self):
        print("Flying")


class Sparrow:

    def __init__(self):
        self.flying = Flying()
```

Now only objects that actually need flying behavior contain the corresponding component.

The design becomes:

```text
Sparrow
   │
   └── has → Flying capability
```

rather than forcing every type in a hierarchy to inherit behavior that it may not need.

The general principle is:

> **Do not create an inheritance relationship merely because two classes share some code.**

The relationship should make conceptual sense as well.


## 8. Inheritance Creates Strong Coupling

When one class inherits from another, the child becomes dependent on the parent's design.

For example:

```python
class Report:

    def generate(self):
        print("Generating report")


class PDFReport(Report):
    pass
```

`PDFReport` depends directly on the structure and behavior of `Report`.

If the parent class changes significantly, the child classes may also be affected.

This is known as **tight coupling**.

Composition can reduce this coupling because a class can depend on a separate component without becoming a specialized version of that component.


## 9. Composition Provides Replaceability

One of the strongest benefits of composition is that a component can often be replaced with another implementation.

For example:

```python
class FileLogger:

    def log(self, message):
        print(f"Writing to file: {message}")


class ConsoleLogger:

    def log(self, message):
        print(f"Console: {message}")


class Application:

    def __init__(self, logger):
        self.logger = logger

    def run(self):
        self.logger.log("Application started")
```

Now we can choose the logger:

```python
app = Application(ConsoleLogger())
app.run()
```

or:

```python
app = Application(FileLogger())
app.run()
```

`Application` does not need to inherit from either logger.

It simply uses an object that provides the required behavior.

This idea becomes especially important when we study **Dependency Injection** later.


## 10. When Should You Choose Inheritance?

Inheritance is appropriate when all of the following are reasonably true:

### 1. There is a genuine "is-a" relationship

For example:

```text
Manager is an Employee
```

rather than:

```text
Manager has an Employee
```

### 2. The child genuinely represents a specialization

The child should be a more specific version of the parent.

### 3. The parent behavior is meaningful for the child

The child should not inherit large amounts of behavior that it does not logically support.

### 4. The hierarchy represents a stable domain relationship

If the relationship is likely to change frequently, composition may provide more flexibility.

### 5. Polymorphic substitution makes sense

A child should be usable where the parent is expected without violating the meaning of the parent abstraction.


## 11. When Should You Choose Composition?

Composition is generally appropriate when:

### 1. The relationship is "has-a"

For example:

```text
Car has an Engine
```

### 2. You want to combine independent responsibilities

For example:

```text
Application
 ├── Logger
 ├── Database
 └── Cache
```

### 3. You want components to be replaceable

For example:

```text
Application
    │
    └── Logger
          │
          ├── ConsoleLogger
          └── FileLogger
```

### 4. You want to avoid a deep inheritance hierarchy

Large inheritance trees can become difficult to understand and maintain.

### 5. Behavior may change independently

If one component needs to change without affecting the rest of the object's conceptual identity, composition is often a better fit.


## 12. A Useful Design Rule

A common design principle is:

> **Prefer composition over inheritance when inheritance does not represent a strong, meaningful "is-a" relationship.**

This does **not** mean:

> "Always use composition."

Inheritance is still valuable when the relationship is genuinely hierarchical.

The goal is to choose the relationship that best represents the design.

Think of it as:

```text
Does A really represent a specialized type of B?
             │
       ┌─────┴─────┐
      Yes          No
       │            │
       ↓            ↓
 Inheritance    Composition
```


## 13. Complete Example

Consider an online store.

An `Order` needs several independent services:

* Payment processing
* Notification
* Logging

These are not specialized types of an order.

Therefore, inheritance would be inappropriate:

```text
Order
 ├── is-a PaymentProcessor   ❌
 ├── is-a Notifier           ❌
 └── is-a Logger             ❌
```

Instead, composition is appropriate:

```text
                Order
             /    |    \
            /     |     \
     Payment   Notifier  Logger
```

Implementation:

```python
class PaymentProcessor:

    def process(self, amount):
        print(f"Payment processed: ₹{amount}")


class Notifier:

    def send(self, message):
        print(f"Notification: {message}")


class Logger:

    def log(self, message):
        print(f"LOG: {message}")


class Order:

    def __init__(self, payment, notifier, logger):
        self.payment = payment
        self.notifier = notifier
        self.logger = logger

    def place_order(self, amount):
        self.logger.log("Placing order")

        self.payment.process(amount)

        self.notifier.send("Order placed successfully")

        self.logger.log("Order completed")
```

Now the dependencies can be provided from outside:

```python
payment = PaymentProcessor()
notifier = Notifier()
logger = Logger()

order = Order(payment, notifier, logger)

order.place_order(1500)
```

Output:

```text
LOG: Placing order
Payment processed: ₹1500
Notification: Order placed successfully
LOG: Order completed
```

The important design point is that `Order` **uses** these components rather than inheriting from them.

This gives each class a focused responsibility and allows the components to be replaced independently.
