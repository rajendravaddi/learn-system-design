# Polymorphism in Python

Polymorphism is one of the four fundamental principles of Object-Oriented Programming (OOP):

* Encapsulation
* Abstraction
* Inheritance
* Polymorphism

The word **polymorphism** comes from:

* **Poly** → many
* **Morph** → forms

Therefore, polymorphism means **"many forms."**

In programming, polymorphism allows the **same interface, method, or operation to work with different objects while producing behavior appropriate to each object**.


## 1. Polymorphism

### 1.1 Definition

**Polymorphism is an OOP concept in which the same interface or operation can have different implementations or behaviors depending on the object involved.**

For example, different objects can provide a method with the same name:

```python
class Dog:

    def speak(self):
        print("Woof")


class Cat:

    def speak(self):
        print("Meow")
```

The method:

```python
speak()
```

has different behavior depending on whether it is called on a `Dog` or `Cat`.

```python
dog = Dog()
cat = Cat()

dog.speak()
cat.speak()
```

Output:

```text
Woof
Meow
```

The important idea is:

```text
Same interface
      ↓
Different implementations
      ↓
Different behavior
```


### 1.2 Need for Polymorphism

Without polymorphism, code often becomes tightly coupled to specific classes.

For example, a function could explicitly check every possible type:

```python
def make_sound(animal):

    if isinstance(animal, Dog):
        print("Woof")

    elif isinstance(animal, Cat):
        print("Meow")
```

As more classes are introduced, this function would need to keep changing.

Polymorphism allows the function to depend on the required behavior instead:

```python
def make_sound(animal):
    animal.speak()
```

Now the function does not need to know the exact class of the object.

This provides several benefits:

* **Reusability** — the same function can work with different object types.
* **Extensibility** — new implementations can often be added without changing existing code.
* **Maintainability** — fewer type-specific conditions are required.
* **Loose coupling** — code depends on behavior rather than concrete implementations.
* **Flexibility** — different objects can be substituted as long as they provide the required interface.


## 2. Compile-Time vs Runtime Polymorphism

Polymorphism is commonly classified into:

1. Compile-time polymorphism
2. Runtime polymorphism


### 2.1 Compile-Time Polymorphism

#### Definition

**Compile-time polymorphism, also called static polymorphism, occurs when the implementation to be used can be determined during compilation.**

Traditional method overloading is a common example.

For example, a language such as Java can define:

```java
class Calculator {

    int add(int a, int b) {
        return a + b;
    }

    int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

The compiler can distinguish the methods based on their parameter lists:

```text
add(int, int)
add(int, int, int)
```

The selected method is therefore determined before runtime.

### 2.2 Method Overloading in Python

Python does not support traditional method overloading based on parameter signatures.

For example:

```python
class Calculator:

    def add(self, a, b):
        return a + b

    def add(self, a, b, c):
        return a + b + c
```

Python stores only the second `add()` definition.

Instead, Python uses mechanisms such as:

* Default parameters
* `*args`
* `**kwargs`
* Conditional logic

For example:

```python
class Calculator:

    def add(self, *numbers):
        return sum(numbers)
```

This allows:

```python
calculator = Calculator()

print(calculator.add(1, 2))
print(calculator.add(1, 2, 3))
print(calculator.add(1, 2, 3, 4))
```

Output:

```text
3
6
10
```

This is **overloading-like behavior**, not traditional compile-time method overloading.


### 2.3 Runtime Polymorphism

#### Definition

**Runtime polymorphism, also called dynamic polymorphism, occurs when the implementation to execute is determined during program execution based on the actual object involved.**

Method overriding is the classic example.

Consider:

```python
class Animal:

    def speak(self):
        print("Animal sound")


class Dog(Animal):

    def speak(self):
        print("Woof")


class Cat(Animal):

    def speak(self):
        print("Meow")
```

Now:

```python
animals = [Dog(), Cat()]

for animal in animals:
    animal.speak()
```

Output:

```text
Woof
Meow
```

The code:

```python
animal.speak()
```

is identical in both cases.

However, the actual object determines which implementation is executed:

```text
Dog object → Dog.speak()

Cat object → Cat.speak()
```

This is runtime polymorphism.


### 2.4 Comparison

| Feature                                    | Compile-Time Polymorphism  | Runtime Polymorphism           |
| ------------------------------------------ | -------------------------- | ------------------------------ |
| Also called                                | Static polymorphism        | Dynamic polymorphism           |
| Decision                                   | During compilation         | During execution               |
| Common example                             | Method overloading         | Method overriding              |
| Traditional Python method overloading      | Not supported              | —                              |
| Python alternative                         | Default arguments, `*args` | Method overriding, duck typing |
| Object type determines behavior at runtime | No                         | Yes                            |


## 3. Polymorphism + Inheritance

Inheritance and polymorphism work particularly well together.

**Inheritance** allows a child class to reuse or extend behavior from a parent class.

**Polymorphism** allows different child classes to provide different implementations of the same method.

Example:

```python
class Animal:

    def speak(self):
        print("Animal makes a sound")


class Dog(Animal):

    def speak(self):
        print("Woof")


class Cat(Animal):

    def speak(self):
        print("Meow")


class Cow(Animal):

    def speak(self):
        print("Moo")
```

The inheritance structure is:

```text
             Animal
                │
       ┌────────┼────────┐
       │        │        │
      Dog      Cat      Cow
```

All three classes inherit from `Animal`, but each overrides `speak()`.

We can therefore treat them uniformly:

```python
animals = [Dog(), Cat(), Cow()]

for animal in animals:
    animal.speak()
```

Output:

```text
Woof
Meow
Moo
```

The loop does not need to know the concrete class of each object.

This makes the code easier to extend.

For example, adding:

```python
class Horse(Animal):

    def speak(self):
        print("Neigh")
```

does not require changing the logic that calls `speak()`.

## 4. Duck Typing

Duck typing deserves special attention because it is one of the defining characteristics of Python's approach to polymorphism.

### 4.1 Definition

**Duck typing means that an object is considered suitable based on the operations it supports rather than its specific type or inheritance hierarchy.**

The principle can be summarized as:

> "If an object behaves like the required object, it can be used in that context."

For example:

```python
class File:

    def read(self):
        print("Reading from file")


class NetworkResponse:

    def read(self):
        print("Reading from network")


def process(source):
    source.read()
```

The classes are unrelated.

However, both provide:

```python
read()
```

Therefore:

```python
process(File())
process(NetworkResponse())
```

works because `process()` only requires the `read()` behavior.


### 4.2 Duck Typing vs Inheritance

Traditional inheritance-based polymorphism:

```text
             Animal
                │
        ┌───────┴───────┐
       Dog             Cat
```

Duck typing does not require this relationship:

```text
Dog                    Robot
 │                       │
 └─── both provide ──────┘
          speak()
```

The classes can be completely unrelated.

This makes duck typing especially flexible when designing Python APIs and reusable functions.


## 5. Runtime Polymorphism Without Inheritance

Runtime polymorphism does not necessarily require inheritance in Python.

Duck typing allows unrelated classes to participate in the same operation as long as they provide the required behavior.

Consider:

```python
class PDF:

    def save(self):
        print("Saving PDF")


class Image:

    def save(self):
        print("Saving image")


class Video:

    def save(self):
        print("Saving video")


def save_file(file):
    file.save()
```

The classes do not inherit from one another.

Nevertheless:

```python
save_file(PDF())
save_file(Image())
save_file(Video())
```

works because each object provides:

```python
save()
```

The function depends on the **behavior** rather than the **class hierarchy**.

This is runtime polymorphism through duck typing.


## 6. Operator Polymorphism

Operators can have different meanings depending on the objects involved.

### 6.1 Same Operator, Different Behavior

The `+` operator is a simple example.

```python
10 + 20
```

means numerical addition:

```text
30
```

while:

```python
"Hello " + "World"
```

means string concatenation:

```text
Hello World
```

Therefore:

```text
Same operator
      │
      ↓
Different operand types
      │
      ↓
Different behavior
```

### 6.2 Operator Overloading

Python allows custom classes to define how operators behave using special methods.

For example:

```python
class Point:

    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Point(
            self.x + other.x,
            self.y + other.y
        )
```

Now:

```python
p1 = Point(2, 3)
p2 = Point(4, 5)

p3 = p1 + p2

print(p3.x, p3.y)
```

Output:

```text
6 8
```

The expression:

```python
p1 + p2
```

uses:

```python
p1.__add__(p2)
```

internally.

Common operator methods include:

| Operator | Special Method   |
| -------- | ---------------- |
| `+`      | `__add__()`      |
| `-`      | `__sub__()`      |
| `*`      | `__mul__()`      |
| `/`      | `__truediv__()`  |
| `//`     | `__floordiv__()` |
| `%`      | `__mod__()`      |
| `**`     | `__pow__()`      |
| `==`     | `__eq__()`       |
| `!=`     | `__ne__()`       |
| `<`      | `__lt__()`       |
| `<=`     | `__le__()`       |
| `>`      | `__gt__()`       |
| `>=`     | `__ge__()`       |


## 7. Complete Example

The following example brings together the most important concepts: inheritance, method overriding, and runtime polymorphism.

```python
class Animal:

    def speak(self):
        print("Animal makes a sound")


class Dog(Animal):

    def speak(self):
        print("Dog says Woof")


class Cat(Animal):

    def speak(self):
        print("Cat says Meow")


class Cow(Animal):

    def speak(self):
        print("Cow says Moo")


def make_sound(animal):
    animal.speak()


animals = [
    Dog(),
    Cat(),
    Cow()
]

for animal in animals:
    make_sound(animal)
```

Output:

```text
Dog says Woof
Cat says Meow
Cow says Moo
```

### How the Example Demonstrates Polymorphism

The function:

```python
def make_sound(animal):
    animal.speak()
```

does not depend on a specific concrete class.

The same call:

```python
animal.speak()
```

produces different behavior depending on the object:

```text
Dog → Dog.speak() → Woof

Cat → Cat.speak() → Meow

Cow → Cow.speak() → Moo
```

The important principle is:

```text
One interface
      ↓
Multiple implementations
      ↓
Different behavior
```

This allows new classes to be introduced without changing the existing calling code.

For example:

```python
class Horse(Animal):

    def speak(self):
        print("Horse says Neigh")
```

The existing `make_sound()` function already knows how to work with it:

```python
make_sound(Horse())
```

Output:

```text
Horse says Neigh
```