# Inheritance

## 1. Inheritance

> Inheritance is a mechanism in which a child class derives from a parent class, gaining its accessible attributes and methods and allowing the child to add or customize behavior.

### Inheritance Syntax

The basic syntax is:

```python
class ChildClass(ParentClass):
    # child class body
```

Example:

```python
class Animal:

    def eat(self):
        print("Eating")


class Dog(Animal):

    def bark(self):
        print("Barking")
```

Here:

```text
Animal → Parent class
Dog    → Child class
```

`Dog` inherits from `Animal`.

Therefore:

```python
dog = Dog()

dog.eat()
dog.bark()
```

Output:

```text
Eating
Barking
```

`eat()` comes from the parent, while `bark()` belongs to `Dog`.


## 2. Parent Class and Child Class

A class that is inherited from is called the **parent class**.

Other common names are:

* **Base class**
* **Superclass**

The class that inherits from it is called the **child class**.

Other common names are:

* **Derived class**
* **Subclass**


The class inside the parentheses:

```python
class Dog(Animal):
```

is the parent class.


## 3. What Does a Child Class Inherit?

A child class can use the accessible attributes and methods defined by its parent.


**Adding New Behavior**

A child class can add its own attributes and methods in addition to the inherited functionality.

This demonstrates an important purpose of inheritance:

> **A child class can reuse common functionality from the parent and add specialized functionality of its own.**

Example:

```python
class Animal:

    species = "Animal"

    def eat(self):
        print("Eating")

    def sleep(self):
        print("Sleeping")


class Dog(Animal):

    def bark(self):
        print("Barking")
```

Now:

```python
dog = Dog()

print(dog.species)

dog.eat()
dog.sleep()
dog.bark()
```

Output:

```text
Animal
Eating
Sleeping
Barking
```

The `Dog` object has:

```text
Inherited from Animal:
    species
    eat()
    sleep()

Defined in Dog:
    bark()
```

## 4. Constructor Inheritance, Child's Own Constructor and `super()`

### Constructor Inheritance

Consider:

```python
class Animal:

    def __init__(self, name):
        self.name = name


class Dog(Animal):
    pass
```

`Dog` does not define its own `__init__()`.

Therefore, `Dog` can use the inherited initializer:

```python
dog = Dog("Tommy")

print(dog.name)
```

Output:

```text
Tommy
```

The `Dog` object uses `Animal.__init__()`.


### Child's Own Constructor

A child class can define its own `__init__()`.

```python
class Animal:

    def __init__(self, name):
        self.name = name


class Dog(Animal):

    def __init__(self, name, breed):
        self.name = name
        self.breed = breed
```

Now:

```python
dog = Dog("Tommy", "Labrador")

print(dog.name)
print(dog.breed)
```

Output:

```text
Tommy
Labrador
```

However, notice that the child has repeated:

```python
self.name = name
```

The parent already knows how to initialize `name`.

We can reuse the parent's initialization using `super()`.


### `super()`

> **`super()` provides access to the next class in the Method Resolution Order (MRO), allowing a child class to reuse behavior defined in its parent or another class in the inheritance chain.**

For simple single inheritance, `super()` commonly calls the parent implementation.

Example:

```python
class Animal:

    def __init__(self, name):
        self.name = name


class Dog(Animal):

    def __init__(self, name, breed):
        super().__init__(name)
        self.breed = breed
```

Now:

```python
dog = Dog("Tommy", "Labrador")

print(dog.name)
print(dog.breed)
```

Output:

```text
Tommy
Labrador
```

Here:

```python
super().__init__(name)
```

calls the `Animal` implementation of `__init__()`.

Conceptually:

```text
Dog.__init__()
     │
     ├── super().__init__(name)
     │          ↓
     │    Animal.__init__()
     │          ↓
     │    self.name = name
     │
     └── self.breed = breed
```

### Why use `super()`?

It allows the child to **reuse parent behavior instead of duplicating it**.


## 5. Method Overloading, Method Overriding and `super()`

### Method Overloading

> **Method overloading means defining multiple methods with the same name but different parameter lists.**

Languages such as Java and C++ support traditional method overloading.

Python does **not** support traditional compile-time method overloading in the same way.

For example, this does **not** create two overloaded methods:

```python
class Calculator:

    def add(self, a):
        return a

    def add(self, a, b):
        return a + b
```

The second `add()` replaces the first one.

```python
calc = Calculator()

calc.add(1)
```

will result in an error because the final definition expects two arguments.

#### How Python handles similar requirements

Python commonly uses:

* Default arguments
* `*args`
* `**kwargs`
* Conditional logic

Example using default arguments:

```python
class Calculator:

    def add(self, a, b=0):
        return a + b
```

Now:

```python
calc = Calculator()

print(calc.add(5))
print(calc.add(5, 10))
```

Output:

```text
5
15
```

So when discussing Python polymorphism later, remember:

> **Python does not have traditional compile-time method overloading like Java or C++.**

---

### Method Overriding

> **Method overriding occurs when a child class provides its own implementation of a method that already exists in the parent class.**

Example:

```python
class Animal:

    def make_sound(self):
        print("Some animal sound")


class Dog(Animal):

    def make_sound(self):
        print("Woof")
```

Now:

```python
animal = Animal()
dog = Dog()

animal.make_sound()
dog.make_sound()
```

Output:

```text
Some animal sound
Woof
```

`Dog` has overridden `Animal.make_sound()`.

Conceptually:

```text
Animal
└── make_sound()
      → Some animal sound

Dog
└── make_sound()
      → Woof
```


### `super()` with Overridden Methods

Sometimes a child doesn't want to completely replace the parent's behavior.

It wants to **extend** it.

Example:

```python
class Animal:

    def make_sound(self):
        print("Animal makes a sound")


class Dog(Animal):

    def make_sound(self):
        super().make_sound()
        print("Dog says Woof")
```

Now:

```python
dog = Dog()

dog.make_sound()
```

Output:

```text
Animal makes a sound
Dog says Woof
```

The child method:

1. Calls the inherited behavior.
2. Adds its own behavior.

```text
Dog.make_sound()
      │
      ├── super().make_sound()
      │       ↓
      │   Parent behavior
      │
      └── Child behavior
```

## 6. Types of Inheritance

Python supports several inheritance structures.

### 6.1 Single Inheritance

One child inherits from one parent.

```text
Animal
   ↓
  Dog
```

```python
class Animal:
    pass


class Dog(Animal):
    pass
```


### 6.2 Multilevel Inheritance

Inheritance occurs across multiple levels.

```text
Animal
   ↓
Mammal
   ↓
Dog
```

```python
class Animal:
    pass


class Mammal(Animal):
    pass


class Dog(Mammal):
    pass
```

Here:

```text
Dog → Mammal → Animal
```

`Dog` indirectly inherits from `Animal`.


### 6.3 Hierarchical Inheritance

Multiple child classes inherit from the same parent.

```text
        Animal
       /      \
     Dog      Cat
```

```python
class Animal:
    pass


class Dog(Animal):
    pass


class Cat(Animal):
    pass
```


### 6.4 Multiple Inheritance

One class inherits from multiple parent classes.

```text
      A       B
       \     /
         C
```

```python
class A:
    pass


class B:
    pass


class C(A, B):
    pass
```

Python supports multiple inheritance.

However, multiple inheritance introduces additional complexity because Python must determine which parent's method or attribute should be used when multiple parents provide the same member.

This is where **MRO** becomes important.


### 6.5 Hybrid Inheritance

> **Hybrid inheritance is a combination of two or more inheritance patterns.**

For example:

```text
          A
        /   \
       B     C
        \   /
          D
```

This combines hierarchical and multiple inheritance.

Complex inheritance structures require a good understanding of MRO.


## 7. MRO and Why MRO Matters

### What Is MRO?

> **Method Resolution Order (MRO) is the order in which Python searches classes when looking for a method or attribute.**

You can inspect the MRO using:

```python
ClassName.mro()
```

or:

```python
ClassName.__mro__
```

Example:

```python
class Animal:
    pass


class Mammal(Animal):
    pass


class Dog(Mammal):
    pass
```

Then:

```python
print(Dog.mro())
```

Conceptually:

```text
Dog
 ↓
Mammal
 ↓
Animal
 ↓
object
```

When Python looks for a method on `Dog`, it follows this order.


### Why Does MRO Matter?

MRO becomes particularly important with multiple inheritance.

Consider:

```python
class A:

    def show(self):
        print("A")


class B(A):

    def show(self):
        print("B")


class C(A):

    def show(self):
        print("C")


class D(B, C):
    pass
```

Now:

```python
d = D()

d.show()
```

Which method should Python use?

* `B.show()`?
* `C.show()`?
* `A.show()`?

Python uses MRO.

```python
print(D.mro())
```

Conceptually:

```text
D
↓
B
↓
C
↓
A
↓
object
```

Therefore Python finds `B.show()` first.

Output:

```text
B
```

You don't need to memorize the C3 linearization algorithm at this stage.

Remember:

> **MRO determines the order in which Python searches the inheritance hierarchy for methods and attributes.**

### `super()` and MRO

This also explains why `super()` should not simply be thought of as "call my parent."

More accurately:

> **`super()` follows the MRO and accesses the next class in that order.**

This becomes especially important with multiple inheritance.


## 8. `object` — The Root of Python Classes

Python has a built-in root class called `object`.

A simple class:

```python
class Student:
    pass
```

is effectively derived from `object`.

Conceptually:

```python
class Student(object):
    pass
```

Therefore:

```python
print(Student.mro())
```

will contain:

```text
Student
object
```

For inheritance:

```python
class Animal:
    pass


class Dog(Animal):
    pass
```

the MRO is approximately:

```text
Dog
Animal
object
```

So:

> **`object` is the root class from which Python's class hierarchy ultimately derives.**


### `isinstance()`

`isinstance()` checks whether an object is an instance of a particular class or one of its subclasses.

Example:

```python
class Animal:
    pass


class Dog(Animal):
    pass


dog = Dog()
```

Now:

```python
print(isinstance(dog, Dog))
print(isinstance(dog, Animal))
```

Output:

```text
True
True
```

Why is the second one `True`?

Because:

```text
Dog is an Animal
```

Therefore, a `Dog` object is also considered an instance of `Animal`.


### `issubclass()`

`issubclass()` checks whether one class is derived from another class.

```python
class Animal:
    pass


class Dog(Animal):
    pass
```

Then:

```python
print(issubclass(Dog, Animal))
```

Output:

```text
True
```

But:

```python
print(issubclass(Animal, Dog))
```

Output:

```text
False
```

The relationship is:

```text
Dog → Animal
```

not:

```text
Animal → Dog
```

---

## 9. The "IS-A" Relationship

One of the most important rules for deciding whether inheritance makes sense is the **"is-a" relationship**.

Ask:

> **"Is the child genuinely a type of the parent?"**

Examples:

```text
Dog is an Animal       ✓
Cat is an Animal       ✓
Car is a Vehicle       ✓
Manager is an Employee ✓
```

These can make sense as inheritance relationships.

But:

```text
Car is an Engine       ✗
House is a Door        ✗
Student is a Course    ✗
```

These are not "is-a" relationships.

They are generally **"has-a" relationships**:

```text
Car HAS-A Engine
House HAS-A Door
Student HAS-A Course
```

This distinction becomes especially important when deciding between **inheritance and composition**.


## 10. Inheritance, Code Reuse and Specialization

### Inheritance and Code Reuse

One benefit of inheritance is reusing common functionality.

Without inheritance:

```python
class Dog:

    def eat(self):
        print("Eating")

    def bark(self):
        print("Barking")


class Cat:

    def eat(self):
        print("Eating")

    def meow(self):
        print("Meowing")
```

The `eat()` method is duplicated.

With inheritance:

```python
class Animal:

    def eat(self):
        print("Eating")


class Dog(Animal):

    def bark(self):
        print("Barking")


class Cat(Animal):

    def meow(self):
        print("Meowing")
```

Now both `Dog` and `Cat` inherit:

```text
Animal
└── eat()
```

while adding their own behavior.

**Important**

> **Code reuse is a benefit of inheritance, but code reuse alone is not a sufficient reason to create an inheritance relationship.**

The relationship should make conceptual sense.


### Inheritance and Specialization

Inheritance is particularly useful when a child is a **specialized version** of the parent.

For example:

```text
             Employee
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
   Developer   Designer   Manager
```

Example:

```python
class Employee:

    def work(self):
        print("Employee is working")


class Developer(Employee):

    def write_code(self):
        print("Writing code")


class Designer(Employee):

    def design(self):
        print("Designing")


class Manager(Employee):

    def manage_team(self):
        print("Managing team")
```

All three are employees, but each provides specialized behavior.

This is a natural use of inheritance.


### Inheritance Should Preserve the Parent's Meaning

A child class should not contradict the assumptions made by its parent.

Consider:

```python
class Bird:

    def fly(self):
        print("Flying")
```

Now:

```python
class Penguin(Bird):

    def fly(self):
        raise NotImplementedError
```

This creates a design problem.

A `Penguin` is a bird, but the `Bird` class assumes:

```text
Every Bird can fly
```

That assumption is not valid for every bird.

The problem is not necessarily that `Penguin` is a bad child; the problem is that the parent abstraction is too broad.

A better design might separate the flying capability:

```text
Bird
├── Penguin
└── Eagle
      ↓
   Flying capability
```

This relates to the **Liskov Substitution Principle (LSP)**.

A simplified version is:

> **A child object should be usable wherever its parent object is expected without violating the expected behavior of the parent.**


## 11. When Inheritance Is Useful and When to Be Careful

### When Inheritance Is Useful

#### 1. There is a genuine "is-a" relationship

```text
Dog → Animal
```

The child is genuinely a type of the parent.


#### 2. The child is a specialization of the parent

```text
Employee
   ↓
Developer
```

The child represents a more specific form of the parent.


#### 3. There is meaningful shared behavior

Common functionality naturally belongs in the parent.

```text
Animal
├── eat()
└── sleep()
```

The child classes genuinely need that behavior.


#### 4. The child can safely behave like the parent

The child should preserve the expectations and behavior defined by the parent.

This is the basic idea behind Liskov Substitution.


#### 5. The hierarchy represents a stable relationship

The relationship should make sense as a domain concept rather than existing only to save a few lines of code.


### When to Be Careful with Inheritance

#### 1. Don't use inheritance only for code reuse

If two classes share a method, that doesn't automatically mean they should have a parent class.

For example:

```text
Report → save()
Image  → save()
```

The fact that both have `save()` doesn't mean:

```text
SomethingWithSave
├── Report
└── Image
```

is necessarily a meaningful hierarchy.


#### 2. Avoid inheritance for "has-a" relationships

If:

```text
Car HAS-A Engine
```

don't model it as:

```text
Car IS-A Engine
```

Composition is usually more appropriate.


#### 3. Be careful with deep inheritance hierarchies

Avoid unnecessarily deep structures such as:

```text
A
↓
B
↓
C
↓
D
↓
E
↓
F
```

Deep hierarchies can make code:

* harder to understand
* harder to debug
* harder to test
* harder to modify
* more tightly coupled


#### 4. Be careful when subclasses violate parent behavior

If a child frequently needs to disable, undo, or reject functionality inherited from the parent, the inheritance hierarchy may be poorly designed.

For example:

```python
class Penguin(Bird):

    def fly(self):
        raise NotImplementedError
```

This should make you question whether `fly()` belongs in the `Bird` abstraction.


#### 5. Be careful when behavior needs to change dynamically

Inheritance creates a relatively fixed class relationship.

If behavior needs to be swapped or configured dynamically, composition can often provide more flexibility.


#### 6. Multiple inheritance requires extra care

Multiple inheritance can be useful, but it introduces additional concepts:

```text
Multiple inheritance
       ↓
      MRO
       ↓
   super()
       ↓
Potential complexity
```

Use it when the design genuinely benefits from it rather than simply combining unrelated classes.


### Practical Decision Rule

When considering inheritance, ask these questions:

```text
Is B genuinely an A?
        │
   ┌────┴────┐
  YES        NO
   │          │
   ↓          ↓
Continue    Consider
checking    composition
   │
   ↓
Can B safely behave
like A?
   │
 ┌─┴─┐
YES  NO
 │    │
 ↓    ↓
Use  Avoid
it   inheritance
```

The most important rule is:

> **Use inheritance for genuine, substitutable "is-a" relationships. Do not use inheritance merely to reuse code. Prefer composition when the relationship is "has-a" or when composition provides better flexibility and lower coupling.**
