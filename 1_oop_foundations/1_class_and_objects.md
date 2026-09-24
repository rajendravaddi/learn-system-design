# Classes & Objects

**Classes and objects are the fundamental building blocks of Object-Oriented Programming (OOP) in Python.**

A simple way to understand them:

* **Class → Blueprint / Template**
* **Object → Actual instance created from the class**

For example:

```text
Class  → Student
Objects → student1, student2, student3
```

A class defines **what an object has** and **what an object can do**.

---

## Class

> **A class is a blueprint or template that defines the data and behavior that objects created from it can have.**

A class mainly contains:

* **Attributes → Data / Properties**
* **Methods → Behavior / Actions**

### Example

```python
class Student:

    name = "Ramesh"
    age = 22

    def introduce(self):
        print("Hello, I am a student")
```

Here:

```text
Student
├── name       → Attribute
├── age        → Attribute
└── introduce  → Method
```

`name` and `age` represent the **data/state** of a student.

`introduce()` represents a **behavior/action** that a student object can perform.

### Important

Defining a class does **not** mean that we have created a particular student.

We have only created the **blueprint** for student objects.

---

# Object

> **An object is a specific instance of a class, created using that class.**

We create an object by calling the class:

```python
st = Student()
```

Here:

```text
Student → Class
st      → Object
```

The object can access the attributes and methods defined by its class:

```python
print(st.name)
print(st.age)

st.introduce()
```

Output:

```text
Ramesh
22
Hello, I am a student
```

### One class can create many objects

For example:

```python
st1 = Student()
st2 = Student()
st3 = Student()
```

Here:

```text
             Student
               Class
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
      st1       st2       st3
    Object    Object    Object
```

All three objects are instances of the same `Student` class.

---

# `__init__()` Method

The previous example gives every object the same `name` and `age`.

Usually, we want different objects to have different values.

For example:

```text
student1 → Ramesh, 22
student2 → Rahul, 21
student3 → Priya, 23
```

We can initialize each object with its own values using the `__init__()` method.

> **`__init__()` is a special method that Python automatically calls to initialize an object's attributes when the object is created.**

### Example

```python
class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age
```

Now we can create objects with different values:

```python
st1 = Student("Ramesh", 22)
st2 = Student("Rahul", 21)
```

Each object now has its own data:

```text
st1
├── name → "Ramesh"
└── age  → 22

st2
├── name → "Rahul"
└── age  → 21
```

We can access them using:

```python
print(st1.name)
print(st1.age)

print(st2.name)
print(st2.age)
```

Output:

```text
Ramesh
22
Rahul
21
```

### Why is `__init__()` useful?

It allows us to **initialize each object with different values when the object is created**.

```python
st1 = Student("Ramesh", 22)
st2 = Student("Rahul", 21)
```

---

# `self`

> **`self` is a reference to the current instance (object) of a class. It is used inside instance methods to access that object's attributes and other instance methods.**

Consider:

```python
class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def introduce(self):
        print(self.name)
```

When we create:

```python
st1 = Student("Ramesh", 22)
```

`self` refers to the object `st1` while its instance methods are executing.

Therefore:

```python
self.name
```

refers to:

```python
st1.name
```

And:

```python
self.age
```

refers to:

```python
st1.age
```

Similarly, when we create:

```python
st2 = Student("Rahul", 21)
```

`self` refers to `st2` when its instance methods are executing.

So:

```python
st1.introduce()
```

means that `self` refers to `st1`.

And:

```python
st2.introduce()
```

means that `self` refers to `st2`.

### Easy way to remember

> **`self` means "this particular object".**

For example:

```python
self.name
```

can be understood as:

> "The `name` belonging to this particular object."
