# Generics / Type Hints

## 1. Why Type Hints?

Python is dynamically typed, so you can write:

```python
name = "Rajendra"
age = 25
```

without explicitly declaring the types.

Python also allows you to describe the expected types:

```python
name: str = "Rajendra"
age: int = 25
```

These are called **type hints** or **type annotations**.

### Why use type hints?

Type hints improve:

* readability
* IDE/editor support
* autocomplete
* static type checking
* understanding of function contracts
* maintainability of larger codebases

For example:

```python
def calculate_total(price: float, quantity: int) -> float:
    return price * quantity
```

From the function signature, we can immediately understand:

```text
price     → float
quantity  → int
return    → float
```

without reading the implementation.

### Type hints are not runtime enforcement

This is important.

```python
def greet(name: str) -> str:
    return f"Hello {name}"
```

The annotation:

```python
name: str
```

does not automatically prevent this:

```python
greet(123)
```

Python generally does not enforce ordinary type hints at runtime.

Type hints primarily provide information to:

* developers
* IDEs
* static type checkers

such as tools like mypy or pyright.

Therefore:

```text
Type hint
    ≠
Runtime validation
```

If runtime validation is required, it must be implemented separately.



## 2. Core Type Hint Syntax

### Variable Annotations

You can annotate variables:

```python
name: str = "Rajendra"
age: int = 25
price: float = 99.99
active: bool = True
```

Python can still infer many types automatically, so annotations are most useful when they improve clarity or are needed for a larger API.



### Function Parameters

```python
def greet(name: str):
    print(f"Hello {name}")
```

The parameter is expected to be a `str`.



### Return Types

Use `->` to specify the expected return type:

```python
def add(a: int, b: int) -> int:
    return a + b
```

Another example:

```python
def get_name() -> str:
    return "Rajendra"
```



### Functions That Return Nothing

Use `None`:

```python
def log_message(message: str) -> None:
    print(message)
```

This communicates that the function is not expected to return a useful value.



### Type Aliases

A type alias gives a complex type a meaningful name.

```python
UserID = int
```

Now:

```python
def get_user(user_id: UserID):
    ...
```

For more complex types:

```python
UserData = dict[str, str]
```

Then:

```python
def process_user(user: UserData):
    ...
```

Type aliases are especially useful when the same complex type appears in multiple places.



### Modern Collection Syntax

Modern Python allows built-in collection types to be parameterized directly:

```python
names: list[str]
scores: dict[str, int]
unique_ids: set[int]
```

This is generally preferred in modern Python instead of older forms such as:

```python
from typing import List, Dict, Set
```

So:

```python
list[str]
```

is the modern style.



## 3. Typing Collections and Common Types

Type hints become particularly useful when describing the contents of collections.

### Lists

```python
names: list[str]
```

means:

```text
list
└── contains strings
```

Example:

```python
names: list[str] = [
    "Rajendra",
    "Amit",
    "Priya"
]
```



### Dictionaries

Use two type parameters:

```python
users: dict[int, str]
```

This means:

```text
key   → int
value → str
```

Example:

```python
users: dict[int, str] = {
    1: "Rajendra",
    2: "Amit"
}
```



### Sets

```python
ids: set[int]
```

means a set containing integers.



### Tuples

Tuples can describe the type of each position:

```python
user: tuple[int, str]
```

meaning:

```text
position 1 → int
position 2 → str
```

Example:

```python
user: tuple[int, str] = (101, "Rajendra")
```

For a tuple containing an arbitrary number of values of the same type:

```python
numbers: tuple[int, ...]
```



### Nested Types

Types can be nested:

```python
users: dict[int, list[str]]
```

This means:

```text
dict
├── key   → int
└── value → list[str]
```

Example:

```python
users = {
    1: ["Python", "Linux"],
    2: ["Java", "SQL"]
}
```



### Optional Values

A value may sometimes be absent:

```python
name: str | None
```

This means:

```text
name can be:
    str
    or
    None
```

Example:

```python
def find_user(user_id: int) -> str | None:
    ...
```

The function may return a string or `None`.

Older Python code commonly uses:

```python
from typing import Optional

def find_user(user_id: int) -> Optional[str]:
    ...
```

Modern Python generally prefers:

```python
str | None
```



### Union Types

A value may have more than one possible type:

```python
value: int | str
```

meaning:

```text
value can be int
OR
value can be str
```

Older code may use:

```python
from typing import Union

value: Union[int, str]
```

Modern Python generally prefers:

```python
int | str
```



### `Any`

`Any` means essentially:

> "Do not enforce a particular static type here."

```python
from typing import Any

value: Any
```

This provides flexibility, but excessive use of `Any` weakens the benefits of type checking.

Prefer a specific type whenever you can reasonably determine it.



## 4. Generics and `TypeVar`

Now we reach the main idea of **generics**.

Suppose we want a function that returns whatever value it receives.

Without thinking about generics, we might write:

```python
def identity(value):
    return value
```

We could add:

```python
def identity(value: Any) -> Any:
    return value
```

But `Any` loses useful type information.

A better approach is a **TypeVar**.

```python
from typing import TypeVar

T = TypeVar("T")
```

Then:

```python
def identity(value: T) -> T:
    return value
```

This means:

> Whatever type goes into the function, the same type comes out.

Conceptually:

```text
T
↓
input type

T
↓
output type
```

For example:

```python
name = identity("Rajendra")
number = identity(100)
```

A type checker can understand:

```text
identity(str) → str
identity(int) → int
```

### Why not just use `Any`?

Compare:

```python
def identity(value: Any) -> Any:
    return value
```

with:

```python
T = TypeVar("T")

def identity(value: T) -> T:
    return value
```

`Any` says:

```text
"I don't care about the type."
```

`T` says:

```text
"Keep track of the type and preserve its relationship."
```

That relationship is the important part of generics.



### Generic Functions

Consider:

```python
T = TypeVar("T")

def first(items: list[T]) -> T:
    return items[0]
```

Now:

```python
first([1, 2, 3])
```

has a type relationship:

```text
list[int]
    ↓
first()
    ↓
int
```

And:

```python
first(["A", "B", "C"])
```

gives:

```text
list[str]
    ↓
first()
    ↓
str
```

The same function works with multiple types while preserving useful type information.



## 5. Generic Classes

Generics can also be used with classes.

Suppose we want a container that can store a value of some type.

```python
from typing import Generic, TypeVar

T = TypeVar("T")


class Box(Generic[T]):

    def __init__(self, value: T):
        self.value = value

    def get(self) -> T:
        return self.value
```

Now we can create typed versions conceptually:

```python
number_box = Box[int](100)
name_box = Box[str]("Rajendra")
```

The type relationship is:

```text
Box[int]
    ↓
value → int
get() → int
```

and:

```text
Box[str]
    ↓
value → str
get() → str
```

The class is reusable for different types.



### Why Generic Classes Are Useful

Without generics, we might use:

```python
class Box:

    def __init__(self, value):
        self.value = value

    def get(self):
        return self.value
```

This is perfectly valid Python.

Generics become useful when we want static type checkers to understand the relationship between:

```text
stored value
     ↓
returned value
```

For example:

```python
Box[int]
```

communicates that the box contains integers.



### Generic Classes with Multiple Types

A class can use multiple type variables.

```python
K = TypeVar("K")
V = TypeVar("V")


class Pair(Generic[K, V]):

    def __init__(self, key: K, value: V):
        self.key = key
        self.value = value
```

Now:

```python
pair = Pair[int, str](101, "Rajendra")
```

represents:

```text
K → int
V → str
```

This is the same idea used by:

```python
dict[K, V]
```



## 6. Protocols + Generics

This connects directly to the **Abstract Classes & Interfaces** topic.

You already learned that a `Protocol` can describe a structural interface.

Protocols can also be generic.

For example:

```python
from typing import Protocol, TypeVar

T = TypeVar("T")


class Repository(Protocol[T]):

    def get(self, id: int) -> T:
        ...
```

This describes a repository that returns some type `T`.

A user repository could conceptually work with:

```text
Repository[User]
```

while another repository could work with:

```text
Repository[Product]
```

The same interface pattern can therefore be reused for different data types.

### Why is this useful?

It allows us to combine:

```text
Protocol
    +
Generics
```

to express:

> "Any object that provides this interface and works with this particular type."

This is especially useful in larger applications and typed backend code.

You do not need to memorize complex generic Protocol syntax at this stage. The important idea is the relationship between **structural interfaces and reusable types**.



## 7. Practical Type Design

Type hints are useful, but more typing does not automatically mean better code.

The goal is:

> **Use enough type information to make the code clearer and safer without making it unnecessarily complicated.**

### Prefer Specific Types Over `Any`

Avoid:

```python
def process(data: Any) -> Any:
    ...
```

when you know the actual type.

Prefer:

```python
def process(data: dict[str, str]) -> list[str]:
    ...
```

when that accurately describes the function.



### Use Type Aliases for Complex Types

Instead of repeatedly writing:

```python
dict[int, list[tuple[str, float]]]
```

you can define:

```python
ProductData = dict[
    int,
    list[tuple[str, float]]
]
```

Then:

```python
def process_products(data: ProductData):
    ...
```

This makes the API easier to read.



### Type Hints for APIs

Type hints are especially useful at boundaries such as:

```text
function parameters
return values
class interfaces
repositories
service methods
public APIs
```

For example:

```python
class UserService:

    def get_user(self, user_id: int) -> User | None:
        ...
```

The contract is immediately visible:

```text
input:
    int

output:
    User or None
```



### Don't Over-Type Simple Code

Avoid unnecessary complexity such as creating multiple generic types when a simple type annotation is perfectly clear.

Bad:

```text
Extremely complex type
        +
many nested TypeVars
        +
unnecessary Protocols
```

when:

```python
list[str]
```

would communicate the requirement clearly.

Good type design makes code easier to understand, not harder.



## 8. Common Mistakes + Practical Example

### Common Mistake 1 — Thinking Type Hints Enforce Types

This:

```python
def add(a: int, b: int) -> int:
    return a + b
```

does not automatically make Python reject:

```python
add("10", "20")
```

Type hints primarily provide information for tooling and static analysis.



### Common Mistake 2 — Using `Any` Everywhere

This:

```python
def process(data: Any) -> Any:
    ...
```

throws away much of the information that type hints are supposed to provide.

Use `Any` when the type genuinely cannot or should not be specified.



### Common Mistake 3 — Confusing Generics with Multiple Implementations

Generics are not primarily about:

```text
multiple classes
```

They are about:

```text
reusable code
+
preserving relationships between types
```

For example:

```python
T = TypeVar("T")

def identity(value: T) -> T:
    return value
```

The important relationship is:

```text
input T
  ↓
output T
```



### Common Mistake 4 — Making Types More Complicated Than the Code

If:

```python
def get_names() -> list[str]:
    ...
```

is sufficient, don't replace it with a complicated collection of aliases and generic abstractions.

Use generics when they solve a real typing problem.



### Practical Example

Let's combine:

* type hints
* a type alias
* `TypeVar`
* a generic class
* a generic function

```python
from typing import Generic, TypeVar


T = TypeVar("T")

UserID = int


class Repository(Generic[T]):

    def __init__(self):
        self.items: list[T] = []

    def add(self, item: T) -> None:
        self.items.append(item)

    def get_all(self) -> list[T]:
        return self.items


def first(items: list[T]) -> T:
    return items[0]
```

Now we can create a repository for a particular type:

```python
user_repository = Repository[str]()

user_repository.add("Rajendra")
user_repository.add("Amit")
```

The repository is conceptually:

```text
Repository[str]
    ↓
items → list[str]
add() → accepts str
get_all() → returns list[str]
```

We can also use:

```python
names = user_repository.get_all()

first_name = first(names)
```

The types remain connected:

```text
Repository[str]
      ↓
list[str]
      ↓
first()
      ↓
str
```

Now create another repository:

```python
score_repository = Repository[int]()

score_repository.add(90)
score_repository.add(85)
```

Its type becomes:

```text
Repository[int]
    ↓
items → list[int]
add() → accepts int
get_all() → returns list[int]
```

The same generic class works with different types without duplicating the implementation.