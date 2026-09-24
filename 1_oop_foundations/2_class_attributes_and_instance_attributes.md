# Class Attributes & Instance Attributes

In Python, attributes can mainly be divided into two types:

1. **Class Attributes**
2. **Instance Attributes**

The key difference is **where the data belongs**.

> **Class attribute → Belongs to the class and is shared by objects.**

> **Instance attribute → Belongs to a particular object, so each object can have its own value.**

---

## 1. Class Attributes

> **A class attribute is an attribute defined directly inside a class and shared by all instances of that class.**

### Example

```python
class Student:

    school = "ABC School"

    def __init__(self, name):
        self.name = name
```

Here:

```python
school = "ABC School"
```

is a **class attribute**.

It belongs to the `Student` class rather than to one particular student.

We can access it using the class:

```python
print(Student.school)
```

Output:

```text
ABC School
```

Objects can also access it:

```python
st1 = Student("Ramesh")
st2 = Student("Rahul")

print(st1.school)
print(st2.school)
```

Output:

```text
ABC School
ABC School
```

Both objects can access the same class attribute.

Conceptually:

```text
                 Student Class
                      │
             school = "ABC School"
                      │
             ┌────────┴────────┐
             ↓                 ↓
            st1               st2
          Ramesh             Rahul
```

The `school` value is associated with the class and can be shared by the instances.

---

## 2. Instance Attributes

> **An instance attribute is an attribute that belongs to a specific object. Each object can have its own value for an instance attribute.**

Instance attributes are commonly created using `self`.

Example:

```python
class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age
```

Here:

```python
self.name
self.age
```

are **instance attributes**.

When we create:

```python
st1 = Student("Ramesh", 22)
st2 = Student("Rahul", 21)
```

each object gets its own values.

Conceptually:

```text
st1
├── name → "Ramesh"
└── age  → 22

st2
├── name → "Rahul"
└── age  → 21
```

Changing one object's attribute does not change the other object's attribute:

```python
st1.age = 25

print(st1.age)
print(st2.age)
```

Output:

```text
25
21
```

`st1` and `st2` have independent `age` values.

---

## 3. Why Do We Need Class Attributes?

Use a **class attribute** when a value is logically common to all objects.

For example:

```python
class Student:

    school = "ABC School"
```

All students belong to the same school.

Other examples could be:

```python
class Employee:

    company = "TechCorp"
```

```python
class Car:

    wheels = 4
```

```python
class Circle:

    pi = 3.14159
```

These values can be common across instances.

---

## 4. Why Do We Need Instance Attributes?

Use **instance attributes** when each object needs its own value.

For example:

```python
class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age
```

Different students have different:

* names
* ages
* roll numbers
* email addresses
* grades

Therefore, these should be instance attributes.

---

## 5. The Role of `self`

This is where the concept of `self` becomes important.

When we write:

```python
class Student:

    def __init__(self, name, age):
        self.name = name
        self.age = age
```

`self` refers to the **current object**.

For example:

```python
st1 = Student("Ramesh", 22)
```

During initialization:

```text
self → st1
```

Therefore:

```python
self.name = name
```

effectively stores:

```python
st1.name = "Ramesh"
```

Similarly:

```python
st2 = Student("Rahul", 21)
```

During initialization:

```text
self → st2
```

So:

```python
self.name = name
```

effectively stores:

```python
st2.name = "Rahul"
```

This is why every object can have its own instance attributes.

---

## 6. What Happens When an Object Accesses an Attribute?

Suppose:

```python
class Student:

    school = "ABC School"

    def __init__(self, name):
        self.name = name
```

And:

```python
st1 = Student("Ramesh")
```

When Python evaluates:

```python
st1.name
```

Python finds `name` on the object itself.

But when Python evaluates:

```python
st1.school
```

if `school` is not present on `st1`, Python looks at the class and finds:

```python
Student.school
```

So:

```text
st1.name
   ↓
Object's own attribute
   ↓
"Ramesh"


st1.school
   ↓
Not found on object
   ↓
Look in Student class
   ↓
"ABC School"
```

This is an important part of Python's **attribute lookup mechanism**.

---

## 7. Important: An Object Can Override a Class Attribute

Consider:

```python
class Student:

    school = "ABC School"
```

Create an object:

```python
st1 = Student()
```

Initially:

```python
print(st1.school)
```

Output:

```text
ABC School
```

Now:

```python
st1.school = "XYZ School"
```

This does **not** change the class attribute.

Instead, Python creates an instance attribute called `school` for `st1`.

Now:

```python
print(st1.school)
print(Student.school)
```

Output:

```text
XYZ School
ABC School
```

Conceptually:

```text
Student Class
└── school → "ABC School"

st1
└── school → "XYZ School"
```

`st1.school` takes priority because the object has its own `school` attribute.
