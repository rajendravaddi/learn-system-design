# Encapsulation in Python

> **Encapsulation** is the principle of bundling an **object's data** and **the methods that operate on that data** together inside a class, while controlling how that data can be accessed or modified.

In simple terms:

> **Encapsulation = Keep related data and behavior together, and control access to the internal data.**

---

## 1. Without Encapsulation

Consider a simple `BankAccount` class:

```python
class BankAccount:

    def __init__(self, balance):
        self.balance = balance
```

We can create an account:

```python
account = BankAccount(10000)
```

Since `balance` is directly accessible, outside code can freely modify it:

```python
account.balance = -50000
```

Now the object contains:

```text
balance = -50000
```

The class has no control over whether the new value is valid.

For a bank account, this could be a problem because we may want rules such as:

* Balance cannot be negative.
* Deposit amount must be positive.
* Withdrawal cannot exceed the available balance.

Without encapsulation, outside code can bypass these rules.

---

## 2. Encapsulation

Instead of allowing outside code to freely modify the balance, we let the class control how the balance changes.

Example:

```python
class BankAccount:

    def __init__(self, balance):
        self._balance = balance

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount

    def withdraw(self, amount):
        if 0 < amount <= self._balance:
            self._balance -= amount
        else:
            print("Invalid withdrawal")

    def get_balance(self):
        return self._balance
```

Usage:

```python
account = BankAccount(10000)

account.deposit(5000)
account.withdraw(2000)

print(account.get_balance())
```

Output:

```text
13000
```

Here, the class controls how `_balance` is modified.

```text
Outside code
     │
     │ deposit()
     │ withdraw()
     │ get_balance()
     ↓
┌─────────────────────────┐
│      BankAccount        │
│                         │
│      _balance           │ ← Internal data
│                         │
│      deposit()          │
│      withdraw()         │
│      get_balance()      │
└─────────────────────────┘
```

The object is responsible for maintaining its own valid state.

**Why is it called Encapsulation?**

Think of a **capsule**.

A capsule contains something inside it while providing controlled ways to interact with what is inside.

Similarly, a class can encapsulate:

```text
Data + Behavior
```

inside a single unit.

For example:

```text
BankAccount
├── Data
│   └── _balance
│
└── Behavior
    ├── deposit()
    └── withdraw()
```

So encapsulation is not simply about hiding variables. It is about **organizing related data and behavior together** and **controlling how the internal state is accessed or modified.**

---

## 3. Two Main Ideas of Encapsulation

Encapsulation can be understood through two main ideas:

### 3.1 Bundling

> keeping related data and the methods that operate on that data together inside a class.

For example:

```python
class BankAccount:

    balance

    deposit()
    withdraw()
```

Instead of having the data and related operations scattered throughout the program, they are organized inside the `BankAccount` class.

Conceptually:

```text
BankAccount
│
├── Data
│   └── balance
│
└── Behavior
    ├── deposit()
    └── withdraw()
```

---

### 3.2 Controlled Access

> Controlling how outside code can access or modify the object's internal data.

For example, instead of allowing:

```python
account.balance = -50000
```

we can provide a method:

```python
account.withdraw(500)
```

The method can validate the operation before changing the internal state.

```python
def withdraw(self, amount):

    if 0 < amount <= self._balance:
        self._balance -= amount
    else:
        print("Invalid withdrawal")
```

Therefore:

```text
Encapsulation
│
├── Bundling
│   └── Data + Behavior together
│
└── Controlled Access
    └── Control how internal data is accessed/modified
```

---

## 4. Python's Approach to Encapsulation

Python does not enforce strict access modifiers like some other programming languages.

Instead, Python uses:

```text
public
protected (by convention)
private-like / name-mangled
```

The naming convention is based mainly on underscores.

```text
name       → Public
_name      → Protected/internal by convention
__name     → Private-like / name-mangled
```

### 4.1 Public Attributes

A normal attribute is considered **public**.

> A public attribute can be accessed directly from outside the class.

Example:

```python
class Student:

    def __init__(self, name):
        self.name = name
```

We can access it directly:

```python
student = Student("Ramesh")

print(student.name)
```

Output:

```text
Ramesh
```

Here:

```python
self.name
```

is a public attribute.

---

### 4.2 Protected Attributes

A single leading underscore indicates that an attribute is intended for **internal or protected use**.

Example:

```python
class BankAccount:

    def __init__(self, balance):
        self._balance = balance
```

The `_balance` attribute is intended to be used internally by the class or its subclasses.

However, Python does **not** actually prevent outside code from accessing it:

```python
account = BankAccount(10000)

print(account._balance)
```

This is allowed.

Therefore:

> A single underscore is mainly a convention that tells programmers: "This is intended for internal use; don't access it directly unless necessary."

---

### 4.3 Private Attributes

A double leading underscore triggers **name mangling**.

Example:

```python
class BankAccount:

    def __init__(self, balance):
        self.__balance = balance
```

Now:

```python
account = BankAccount(10000)

print(account.__balance)
```

will result in an `AttributeError`.

Python internally changes the attribute name approximately to:

```text
__balance
     ↓
_BankAccount__balance
```

This mechanism is called **name mangling**.

You can technically access it using the mangled name:

```python
print(account._BankAccount__balance)
```

But this is generally not intended for normal use.

---

### 4.4 Python Has No True Private Attributes

This is an important Python concept.

> Python does not provide truly private attributes that are completely inaccessible from outside the class.

Instead, Python relies on:

* Naming conventions
* Name mangling
* Programmer discipline
* Well-defined public interfaces

For example:

```python
class BankAccount:

    def __init__(self, balance):
        self.__balance = balance
```

The `__balance` attribute is not absolutely inaccessible.

Python simply changes its name internally:

```text
__balance
    ↓
_BankAccount__balance
```

So:

```text
Public
name
↓
Directly accessible


Protected by convention
_name
↓
Accessible, but intended for internal use


Private-like
__name
↓
Name-mangled to discourage direct access
```

**Important takeaway**

Do not think:

```text
__variable = completely private
```

Instead, think:

> Python uses conventions and name mangling to discourage direct access rather than enforcing strict private access.

---

## 5. Getters and Setters

Another common technique for encapsulation is using **getters and setters**.

### Getter

> A getter is a method used to retrieve the value of an internal attribute.

Example:

```python
def get_balance(self):
    return self.__balance
```

### Setter

> A setter is a method used to modify the value of an internal attribute, usually with validation or other rules.

Example:

```python
def set_balance(self, balance):

    if balance >= 0:
        self.__balance = balance
    else:
        print("Balance cannot be negative")
```

### Complete Example

```python
class BankAccount:

    def __init__(self, balance):
        self.__balance = balance

    def get_balance(self):
        return self.__balance

    def set_balance(self, balance):
        if balance >= 0:
            self.__balance = balance
        else:
            print("Balance cannot be negative")
```

Usage:

```python
account = BankAccount(10000)

print(account.get_balance())

account.set_balance(15000)

print(account.get_balance())

account.set_balance(-5000)
```

Output:

```text
10000
15000
Balance cannot be negative
```

The important part is that the setter can **validate the new value before modifying the internal state**.

Without controlled access:

```text
Outside code
     │
     ↓
balance = -5000
     │
     ↓
Invalid state
```

With controlled access:

```text
Outside code
     │
     ↓
set_balance(-5000)
     │
     ↓
Validation
     │
     ↓
Rejected
```

This is one of the practical benefits of encapsulation.

---

## 6. Pythonic Encapsulation with `@property`

Python provides a cleaner and more Pythonic way to implement controlled attribute access using the `@property` decorator.

Instead of writing:

```python
account.get_balance()
account.set_balance(15000)
```

we can make the interface look like normal attribute access:

```python
account.balance
account.balance = 15000
```

while still controlling what happens internally.

## Example

```python
class BankAccount:

    def __init__(self, balance):
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):

        if value >= 0:
            self._balance = value
        else:
            raise ValueError("Balance cannot be negative")
```

Now:

```python
account = BankAccount(10000)

print(account.balance)

account.balance = 15000

print(account.balance)
```

Output:

```text
10000
15000
```

Although this looks like normal attribute access:

```python
account.balance
```

Python is actually calling the property getter:

```python
@property
def balance(self):
    return self._balance
```

And when we write:

```python
account.balance = 15000
```

Python calls the setter:

```python
@balance.setter
def balance(self, value):
    ...
```

So we get **controlled access with a clean interface**.

---

## `@property` Flow

```text
account.balance
      │
      ↓
@property
def balance(self)
      │
      ↓
return self._balance
```

For assignment:

```text
account.balance = 15000
      │
      ↓
@balance.setter
      │
      ↓
Validate value
      │
      ↓
self._balance = 15000
```

If an invalid value is provided:

```python
account.balance = -5000
```

the setter raises:

```text
ValueError: Balance cannot be negative
```

The internal state is therefore protected by the class's rules.
