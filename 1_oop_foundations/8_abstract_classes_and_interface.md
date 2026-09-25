# Abstract Classes & Interfaces

## 1. Abstract Class vs Interface

You already learned **abstraction** and saw how Python can implement it using:

* Abstract Base Classes (`ABC`)
* Abstract methods
* Duck typing
* `Protocol`

This topic focuses on **how these mechanisms are used in design**, rather than redefining abstraction.

### Abstract Class

An **abstract class** is a base class that defines a contract for subclasses and can also provide shared implementation.

It can contain:

* abstract methods
* concrete methods
* shared state
* properties
* constructors

So an abstract class can provide:

```text
Contract
+
Common behavior
+
Common state
```

Example:

```python
from abc import ABC, abstractmethod


class Storage(ABC):

    @abstractmethod
    def save(self, data):
        pass

    def validate(self, data):
        if not data:
            raise ValueError("Data cannot be empty")
```

Here:

```text
save()
    → required from subclasses

validate()
    → shared implementation
```

### Interface

An **interface** primarily describes what an object must be capable of doing.

For example:

```text
Storage
├── save()
├── load()
└── delete()
```

The interface does not need to specify how those operations are implemented.

Python does not have a separate `interface` keyword. Interface-like designs are commonly expressed through:

* ABCs
* `Protocol`
* duck typing

The key distinction is:

```text
Abstract Class
    → Contract + potentially shared implementation/state

Interface
    → Primarily a contract/capability
```


## 2. Abstract Classes in Practice

The important thing to remember is that an abstract class does **not** have to contain only abstract methods.

It can combine required behavior with reusable behavior.

```python
from abc import ABC, abstractmethod


class Storage(ABC):

    def __init__(self, name):
        self.name = name

    @abstractmethod
    def save(self, data):
        pass

    def validate(self, data):
        if not data:
            raise ValueError("Data cannot be empty")
```

A subclass implements the required behavior:

```python
class FileStorage(Storage):

    def save(self, data):
        self.validate(data)
        print(f"{self.name}: saving data to file")
```

Now:

```python
storage = FileStorage("FileStorage")
storage.save("Hello")
```

Output:

```text
FileStorage: saving data to file
```

The subclass receives both:

```text
Required contract
    save()

Shared behavior
    validate()

Shared state
    name
```

### Multiple Abstract Methods

An abstract class can define several required operations:

```python
class Storage(ABC):

    @abstractmethod
    def save(self, data):
        pass

    @abstractmethod
    def load(self):
        pass

    @abstractmethod
    def delete(self):
        pass
```

A concrete subclass must implement the required abstract methods.

### Abstract Properties

An abstract requirement can also represent information rather than an action:

```python
class Storage(ABC):

    @property
    @abstractmethod
    def storage_type(self):
        pass
```

A subclass must provide that property:

```python
class FileStorage(Storage):

    @property
    def storage_type(self):
        return "File"
```

These are useful features to know, but they do not need separate design patterns or large examples.



## 3. Interfaces in Python

You previously learned the mechanics of duck typing and `Protocol`.

The important design difference is **how the contract is established**.

### ABC — Explicit Relationship

With an ABC:

```python
from abc import ABC, abstractmethod


class Storage(ABC):

    @abstractmethod
    def save(self, data):
        pass
```

A concrete implementation explicitly participates in the abstraction:

```python
class FileStorage(Storage):

    def save(self, data):
        print("Saving file")
```

The relationship is explicit.



### Protocol — Structural Relationship

With a Protocol:

```python
from typing import Protocol


class Storage(Protocol):

    def save(self, data):
        ...
```

A class does not need to inherit from `Storage`.

If it has the required structure:

```python
class FileStorage:

    def save(self, data):
        print("Saving file")
```

it can satisfy the Protocol for type-checking purposes.

The important idea is:

```text
ABC
→ "I explicitly implement this abstraction."

Protocol
→ "I have the required structure."
```

### Duck Typing

Duck typing goes one step further toward flexibility.

```python
def save_data(storage):
    storage.save("Hello")
```

The function simply assumes the object provides:

```python
save()
```

No formal interface is required.

So:

```text
Duck typing
    → behavior is enough

Protocol
    → behavior is formally described for typing

ABC
    → explicit abstraction + possible shared implementation
```



## 4. ABC vs Protocol vs Duck Typing

Here is the comparison you should remember:

| Feature                      | Duck Typing | Protocol             | ABC                  |
| ---------------------------- | ----------- | -------------------- | -------------------- |
| Formal contract              | ❌           | ✅                    | ✅                    |
| Explicit inheritance         | ❌           | ❌                    | ✅                    |
| Structural typing            | Informal    | ✅                    | ❌                    |
| Runtime abstract enforcement | ❌           | ❌                    | ✅                    |
| Shared implementation        | ❌           | ❌                    | ✅                    |
| Shared state                 | ❌           | ❌                    | ✅                    |
| Loose coupling               | High        | High                 | Moderate             |
| Main purpose                 | Flexibility | Structural interface | Explicit abstraction |

### The mental model

```text
Duck Typing
"If it behaves correctly, use it."

Protocol
"If it has the required structure, it satisfies the interface."

ABC
"If it belongs to this abstraction, it must satisfy this contract."
```

There is no universal winner.

The correct choice depends on the design requirement.



## 5. Combining Abstract Classes + Polymorphism + Composition

This is the most important connection to the topics you have already learned.

Suppose we have different storage implementations.

```python
from abc import ABC, abstractmethod


class Storage(ABC):

    @abstractmethod
    def save(self, data):
        pass


class FileStorage(Storage):

    def save(self, data):
        print("Saving to file")


class MemoryStorage(Storage):

    def save(self, data):
        print("Saving to memory")
```

We can now create a service:

```python
class DataService:

    def __init__(self, storage: Storage):
        self.storage = storage

    def save_data(self, data):
        self.storage.save(data)
```

Usage:

```python
service = DataService(FileStorage())
service.save_data("Hello")
```

Output:

```text
Saving to file
```

Or:

```python
service = DataService(MemoryStorage())
service.save_data("Hello")
```

Output:

```text
Saving to memory
```

Three OOP concepts are working together.

### 5.1. Abstract Class

`Storage` defines the required contract:

```text
save()
```

### 5.2. Polymorphism

Both:

```text
FileStorage
MemoryStorage
```

provide their own implementation of:

```text
save()
```

### 5.3. Composition

`DataService` **has a** storage object:

```text
DataService
    │
    │ has-a
    ▼
 Storage
```

It does not inherit from `Storage`.

This is exactly the kind of design where the previous **Composition vs Inheritance** topic becomes useful.



## 6. Choosing the Right Approach

Use this practical decision guide.

### Use a Concrete Class

When:

* there is one normal implementation
* subclasses are not required
* there is no meaningful abstraction

```text
Concrete class
    ↓
Normal behavior
```



### Use an ABC

When:

* several related implementations exist
* you need an explicit contract
* subclasses should share behavior or state
* runtime enforcement is useful

```text
ABC
    ↓
Contract + shared implementation/state
```



### Use a Protocol

When:

* you care primarily about capabilities
* unrelated classes may satisfy the same interface
* you want structural typing
* shared implementation is unnecessary

```text
Protocol
    ↓
Capability-based contract
```



### Use Duck Typing

When:

* the required behavior is simple
* a formal contract would add unnecessary complexity
* maximum flexibility is useful

```text
Duck typing
    ↓
Just use the required behavior
```



### Quick Decision Tree

```text
Do I need an abstraction?
│
├── No
│   └── Concrete class / normal composition
│
└── Yes
    │
    ├── Need shared state or implementation?
    │   └── ABC
    │
    └── Mainly need a structural capability?
        │
        ├── Need formal type checking?
        │   └── Protocol
        │
        └── Very simple requirement?
            └── Duck typing
```



## 7. Common Mistakes & Final Summary

### Common Mistakes

#### 1. Creating an ABC just for code reuse

If you only want to reuse a helper method, inheritance may not be necessary.

Consider:

* a normal helper function
* composition
* a concrete class



#### 2. Making huge interfaces

Avoid interfaces containing every possible operation.

Prefer focused contracts.

Instead of:

```text
Storage
├── save
├── load
├── delete
├── search
├── archive
├── export
├── import
└── ...
```

consider smaller, focused capabilities when appropriate.



#### 3. Using inheritance when the relationship is actually "has-a"

If:

```text
Service uses Storage
```

do not make:

```text
Service IS-A Storage
```

Instead:

```text
Service HAS-A Storage
```

Use composition.



#### 4. Using ABC when structural compatibility is enough

If unrelated classes can satisfy the same capability, a `Protocol` may be more appropriate than forcing them into one inheritance hierarchy.



## Final Mental Model

You can now think of these concepts as:

```text
ABSTRACTION
    ↓
What should the object expose?

ABSTRACT CLASS
    ↓
Explicit contract
+
Optional shared behavior/state

INTERFACE
    ↓
What capability must an object provide?

PROTOCOL
    ↓
Structural interface

DUCK TYPING
    ↓
If it behaves correctly, use it

POLYMORPHISM
    ↓
Different implementations
through the same contract

COMPOSITION
    ↓
Use another object instead of inheriting from it
```

And the combined architecture:

```text
              Abstract Contract
                     │
          ┌──────────┴──────────┐
          │                     │
    FileStorage          MemoryStorage
          │                     │
          └──────────┬──────────┘
                     │
                Polymorphism
                     │
                     ▼
                DataService
                     │
                   HAS-A
                     │
                     ▼
                  Storage
```