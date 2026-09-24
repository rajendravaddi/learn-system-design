# Abstraction

## 1. Abstraction

**Definition**

> Abstraction is the principle of **hiding unnecessary implementation details** and **exposing only the essential features and interface needed** to interact with an object.

In simple words:

> Abstraction means focusing on **what an object does** rather than **how it does it**.

---

**Real-World Example — ATM**

Consider an ATM.

When you withdraw money, you interact with:

```text
Insert card
     ↓
Enter PIN
     ↓
Choose withdrawal
     ↓
Enter amount
     ↓
Receive cash
```

You don't need to know the internal implementation:

```text
Validate card
     ↓
Validate PIN
     ↓
Contact bank server
     ↓
Check account balance
     ↓
Authorize transaction
     ↓
Update database
     ↓
Control cash dispenser
```

The ATM provides a simple interface while hiding the complex implementation.

This is **abstraction**.

```text
                  ATM
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
    Withdraw     Deposit   Check Balance
        │          │          │
        └──────────┼──────────┘
                   ↓
          Complex internal
           implementation
```

The user only needs to know **what operations are available**, not how those operations are implemented.

---

**Program Example**

Consider a coffee machine:

```python
class CoffeeMachine:

    def make_coffee(self):
        self._boil_water()
        self._grind_beans()
        self._brew()
        print("Coffee is ready")

    def _boil_water(self):
        print("Boiling water")

    def _grind_beans(self):
        print("Grinding beans")

    def _brew(self):
        print("Brewing coffee")
```

The user only needs to call:

```python
machine = CoffeeMachine()

machine.make_coffee()
```

They don't need to manually perform:

```python
machine._boil_water()
machine._grind_beans()
machine._brew()
```

The public operation is:

```python
make_coffee()
```

while the implementation details are handled internally.

```text
User
 │
 ↓
make_coffee()
 │
 ↓
┌───────────────────────┐
│ Internal implementation│
│                       │
│ _boil_water()         │
│ _grind_beans()        │
│ _brew()               │
└───────────────────────┘
```

**Why Do We Need Abstraction?**

Abstraction becomes increasingly useful as software becomes more complex.

Imagine a payment system.

Without abstraction, a developer might have to understand:

```text
Payment API
     ↓
Authentication
     ↓
Encryption
     ↓
Request construction
     ↓
Network communication
     ↓
Response parsing
     ↓
Error handling
     ↓
Database update
```

Instead, we can provide a simple interface:

```python
payment.process(amount)
```

The developer only needs to understand the interface.

The complex implementation remains hidden.

**Benefits of abstraction**

1. Reduces complexity
2. Makes code easier to use
3. Hides unnecessary implementation details
4. Provides a clear interface
5. Makes large systems easier to maintain
6. Allows implementations to change without necessarily changing how users interact with them
7. Encourages loosely coupled code

---

## 2. Abstraction vs Encapsulation

Abstraction and encapsulation are closely related, but they solve different problems.

### Encapsulation

> **Encapsulation focuses on bundling data and behavior together and controlling access to the object's internal state.**

Example:

```python
class BankAccount:

    def __init__(self, balance):
        self._balance = balance

    def withdraw(self, amount):
        if amount <= self._balance:
            self._balance -= amount
```

Here, `_balance` is managed internally by the class.

### Abstraction

> **Abstraction focuses on hiding unnecessary implementation details and exposing a simple interface.**

For example:

```python
account.withdraw(500)
```

The caller doesn't need to know exactly how the withdrawal is processed internally.

### Simple comparison

```text
Encapsulation
    ↓
"How do I control access to the internal state?"

Abstraction
    ↓
"How do I hide unnecessary complexity?"
```

Another useful way to remember:


### Comparison Table

| Encapsulation                                          | Abstraction                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------------- |
| Bundles data and behavior                              | Hides unnecessary implementation details                            |
| Controls access to internal state                      | Exposes essential functionality                                     |
| Focuses on data/state management                       | Focuses on complexity hiding                                        |
| Often uses properties, methods, and naming conventions | Often uses abstract classes, interfaces, duck typing, and protocols |
| Answers **"How do I control access?"**                 | Answers **"What should the user need to know?"**                    |

They often work together.

## 3. Implementing Abstraction in Python

Python supports abstraction in several ways.

The important approaches are:

1. Abstract Base Classes (ABC)
2. Abstract methods
3. Interfaces through duck typing
4. Interfaces through `typing.Protocol`

### 3.1 Abstract Base Classes (ABC)

Python provides the `abc` module for defining **Abstract Base Classes**.

```python
from abc import ABC, abstractmethod
```

Example:

```python
from abc import ABC, abstractmethod


class Animal(ABC):

    @abstractmethod
    def make_sound(self):
        pass
```

Here:

```python
Animal
```

is an **Abstract Base Class**.

It defines a common interface for its subclasses.

The class says:

> "Every concrete animal must provide a `make_sound()` method."

### 3.2 Abstract Methods

> **An abstract method is a method declared in an abstract class that defines a required interface but leaves its implementation to subclasses.**

Here:

```python
make_sound()
```

is an abstract method.

It doesn't provide a concrete implementation.

It establishes a requirement:

> Any concrete subclass of `Animal` must implement `make_sound()`.

For example:

```python
class Dog(Animal):

    def make_sound(self):
        print("Woof")
```

And:

```python
class Cat(Animal):

    def make_sound(self):
        print("Meow")
```

The parent defines **what must exist**.

The child classes define **how it works**.

```text
                 Animal
             Abstract Class
                   │
          ┌────────┴────────┐
          ↓                 ↓
        Dog                Cat
          │                 │
       make_sound()      make_sound()
          │                 │
        "Woof"           "Meow"
```

---

### 3.3 Interfaces Through Duck Typing

Python does not have a separate `interface` keyword like Java.

Instead, Python often relies on **duck typing**.

> **Duck typing means that an object's suitability is determined by the methods and behavior it provides rather than by its explicit class or inheritance relationship.**

The common idea is:

> **"If it behaves like the required object, we can use it."**

Example:

```python
class Dog:

    def make_sound(self):
        print("Woof")


class Cat:

    def make_sound(self):
        print("Meow")


def make_animal_sound(animal):
    animal.make_sound()
```

We can pass either object:

```python
dog = Dog()
cat = Cat()

make_animal_sound(dog)
make_animal_sound(cat)
```

Output:

```text
Woof
Meow
```

Notice that `Dog` and `Cat` don't need to inherit from a common abstract class.

The function only cares that the object provides:

```python
make_sound()
```

This is duck typing.

```text
             Required behavior
               make_sound()
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
        Dog                   Cat
    make_sound()          make_sound()
          │                   │
        "Woof"              "Meow"
```

The abstraction is based on **behavior**, not inheritance.

### 3.4 Interfaces Through `typing.Protocol`

Python also provides `Protocol` through the `typing` module.

`Protocol` allows us to explicitly describe the structure/behavior an object should provide while still supporting structural typing.

Example:

```python
from typing import Protocol


class Animal(Protocol):

    def make_sound(self) -> None:
        ...
```

Now any class that provides a compatible `make_sound()` method can satisfy the protocol.

```python
class Dog:

    def make_sound(self) -> None:
        print("Woof")


class Cat:

    def make_sound(self) -> None:
        print("Meow")
```

A function can depend on the protocol:

```python
def make_animal_sound(animal: Animal):
    animal.make_sound()
```

Then:

```python
dog = Dog()
cat = Cat()

make_animal_sound(dog)
make_animal_sound(cat)
```

The important idea is:

```text
Protocol
   │
   │ requires
   ↓
make_sound()
   │
   ├── Dog → make_sound()
   │
   └── Cat → make_sound()
```

`Dog` and `Cat` don't need to explicitly inherit from `Animal`.

They simply need to satisfy the required structure.

### ABC vs Duck Typing vs Protocol

| Approach            | Main idea                                                          |
| ------------------- | ------------------------------------------------------------------ |
| **ABC**             | Explicitly define a common abstract base class                     |
| **Abstract method** | Require subclasses to implement specific behavior                  |
| **Duck typing**     | Use an object if it provides the required behavior                 |
| **Protocol**        | Formally describe the expected structure/behavior using type hints |

For traditional OOP learning, **ABC and abstract methods** are the most important starting point. For modern Python development, understanding **duck typing and `Protocol`** is also valuable.

---

## 4. Why Can't We Create an Object of an Abstract Class?

Consider:

```python
from abc import ABC, abstractmethod


class Animal(ABC):

    @abstractmethod
    def make_sound(self):
        pass
```

Now try:

```python
animal = Animal()
```

Python raises an error similar to:

```text
TypeError: Can't instantiate abstract class Animal
with abstract method make_sound
```

### Why?

Because `Animal` has an abstract method:

```python
make_sound()
```

but no concrete implementation.

The abstract class defines a **requirement**, not a complete implementation.

It essentially says:

> "Every concrete subclass must tell me how `make_sound()` works."

For example:

```python
class Dog(Animal):

    def make_sound(self):
        print("Woof")
```

Now `Dog` provides the required implementation.

Therefore:

```python
dog = Dog()
```

works.

```python
dog.make_sound()
```

Output:

```text
Woof
```

### Conceptually

```text
Animal
   │
   ├── make_sound()
   │      ↓
   │   Not implemented
   │
   └── Cannot instantiate
           
           
Dog
   │
   ├── make_sound()
   │      ↓
   │   "Woof"
   │
   └── Can instantiate
```

The purpose is to ensure that concrete objects satisfy the interface defined by the abstract class.


## 5. Complete Example

Let's combine the concepts into one complete example.

Suppose we are designing a payment system.

Different payment methods may process payments differently, but every payment method should provide a `pay()` operation.

We can represent that requirement using an abstract class.

```python
from abc import ABC, abstractmethod


class Payment(ABC):

    @abstractmethod
    def pay(self, amount):
        pass


class CreditCardPayment(Payment):

    def pay(self, amount):
        print(f"Paid ₹{amount} using Credit Card")


class UPIPayment(Payment):

    def pay(self, amount):
        print(f"Paid ₹{amount} using UPI")


class PayPalPayment(Payment):

    def pay(self, amount):
        print(f"Paid ₹{amount} using PayPal")
```

Now create objects:

```python
credit_card = CreditCardPayment()
upi = UPIPayment()
paypal = PayPalPayment()
```

Use the common interface:

```python
credit_card.pay(1000)
upi.pay(500)
paypal.pay(1500)
```

Output:

```text
Paid ₹1000 using Credit Card
Paid ₹500 using UPI
Paid ₹1500 using PayPal
```

### What's happening?

The abstract class:

```python
class Payment(ABC):

    @abstractmethod
    def pay(self, amount):
        pass
```

defines **what every payment method must provide**:

```python
pay(amount)
```

But it doesn't define exactly **how** payment should be processed.

Each subclass provides its own implementation:

```text
                    Payment
                 Abstract Class
                       │
                 pay(amount)
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
    Credit Card       UPI         PayPal
          │            │            │
       pay()          pay()        pay()
          │            │            │
     Card API       UPI API     PayPal API
```

The application can work with the common abstraction:

```python
payment.pay(amount)
```

without needing to know the implementation details of every payment provider.

### This demonstrates several OOP concepts together:

```text
Abstraction
    ↓
Payment defines the required interface

Encapsulation
    ↓
Each payment class manages its own implementation

Polymorphism
    ↓
Different payment objects respond differently to pay()

Inheritance
    ↓
CreditCardPayment, UPIPayment and PayPalPayment
inherit from Payment
```