# Custom Exceptions & Exception Hierarchies

## 1. Why Custom Exceptions?

Python provides many built-in exceptions:

```text
ValueError
TypeError
KeyError
IndexError
FileNotFoundError
RuntimeError
```

These are useful for general programming errors.

However, real applications often have **domain-specific errors**.

For example, an e-commerce application might have:

```text
PaymentFailed
InsufficientBalance
OrderNotFound
InvalidOrderState
OutOfStock
```

Using only a generic exception:

```python
raise ValueError("Payment failed")
```

does not clearly identify the type of application error.

A custom exception gives that error its own identity:

```python
raise PaymentFailed("Payment could not be completed")
```

Now callers can specifically handle:

```python
except PaymentFailed:
    ...
```

### When should you create a custom exception?

Create one when the error:

* has meaningful application/domain semantics
* needs to be handled differently from other errors
* belongs to a meaningful category
* benefits from carrying structured information

You do **not** need a custom exception for every possible error.

For example, a simple invalid argument can often use:

```python
raise ValueError("Age cannot be negative")
```

## 2. Creating Custom Exceptions

A custom exception is normally created by inheriting from `Exception`.

```python
class PaymentFailed(Exception):
    pass
```

You can now raise it:

```python
raise PaymentFailed("Payment could not be completed")
```

And catch it:

```python
try:
    raise PaymentFailed("Payment could not be completed")

except PaymentFailed as error:
    print(error)
```

Output:

```text
Payment could not be completed
```

### Naming Convention

Exception names should clearly describe the problem.

Prefer:

```python
class PaymentTimeout(Exception):
    pass
```

over vague names such as:

```python
class PaymentProblem(Exception):
    pass
```

Use names that allow someone reading the code to understand the error without inspecting its message.

### Why inherit from `Exception`?

Application exceptions should normally inherit from:

```python
Exception
```

rather than directly from:

```python
BaseException
```

`BaseException` also contains special exceptions such as:

```text
KeyboardInterrupt
SystemExit
```

which generally should not be treated as ordinary application errors.

The normal pattern is:

```python
class MyApplicationError(Exception):
    pass
```


## 3. Exception Hierarchies

Custom exceptions become more useful when related errors are organized into a hierarchy.

Instead of:

```python
class InsufficientBalance(Exception):
    pass


class InvalidCard(Exception):
    pass


class PaymentTimeout(Exception):
    pass
```

we can create a common parent:

```python
class PaymentError(Exception):
    pass


class InsufficientBalance(PaymentError):
    pass


class InvalidCard(PaymentError):
    pass


class PaymentTimeout(PaymentError):
    pass
```

The hierarchy becomes:

```text
Exception
    │
    └── PaymentError
          │
          ├── InsufficientBalance
          ├── InvalidCard
          └── PaymentTimeout
```

### Why is this useful?

We can handle a specific error:

```python
try:
    process_payment()

except InsufficientBalance:
    print("Not enough balance")
```

Or handle the entire category:

```python
try:
    process_payment()

except PaymentError:
    print("A payment error occurred")
```

Because:

```text
InsufficientBalance
        ↓
PaymentError
        ↓
Exception
```

catching `PaymentError` also catches `InsufficientBalance`.

### Meaningful inheritance

An exception hierarchy should represent a meaningful relationship.

For example:

```text
PaymentError
    ├── InvalidCard
    ├── PaymentTimeout
    └── InsufficientBalance
```

makes sense because all three are types of payment errors.


## 4. Designing Meaningful Application Exceptions

Custom exceptions are particularly useful at **application or domain boundaries**.

For example:

```text
Authentication
    ├── InvalidCredentials
    └── AccountLocked

Orders
    ├── OrderNotFound
    ├── InvalidOrderState
    └── OutOfStock

Payments
    ├── PaymentFailed
    ├── PaymentTimeout
    └── InsufficientBalance
```

The goal is not to create hundreds of exception classes.

The goal is to create exception types that communicate meaningful application-level problems.

### Built-in vs Custom Exceptions

Use a built-in exception when its meaning is sufficient:

```python
raise ValueError("Age cannot be negative")
```

Use a custom exception when the error needs its own identity:

```python
raise OrderNotFound(order_id)
```

A useful question is:

> **Does the caller need to distinguish this error from other errors?**

If yes, a custom exception may be appropriate.

### Keep the hierarchy focused

Avoid one enormous hierarchy:

```text
ApplicationError
    ├── EverythingElse
    ├── AnotherThing
    ├── SomeOtherThing
    └── ...
```

Instead, create meaningful categories:

```text
ApplicationError
    ├── AuthenticationError
    ├── PaymentError
    └── OrderError
```

Then add child exceptions only when they provide useful distinctions.

### Exceptions as part of an application's vocabulary

A good exception hierarchy gives an application a vocabulary for its failures.

Instead of every layer knowing about low-level implementation details, higher-level code can work with meaningful application errors such as:

```text
PaymentError
OrderError
RepositoryError
AuthenticationError
```

## 5. Custom Exceptions with Attributes

A custom exception can contain structured information in addition to its message.

For example:

```python
class InsufficientBalance(PaymentError):

    def __init__(self, required, available):
        self.required = required
        self.available = available

        super().__init__(
            f"Required: {required}, "
            f"Available: {available}"
        )
```

Now:

```python
raise InsufficientBalance(1000, 600)
```

The exception contains:

```text
required = 1000
available = 600
```

We can access those attributes:

```python
try:
    raise InsufficientBalance(1000, 600)

except InsufficientBalance as error:
    print(error.required)
    print(error.available)
```

Output:

```text
1000
600
```

### Why store attributes?

Structured information can be useful when the caller needs to make a decision.

For example:

```python
except InsufficientBalance as error:

    if error.available == 0:
        print("Account has no balance")
    else:
        print(
            f"You need {error.required - error.available} more"
        )
```

The exception is therefore carrying both:

```text
Human-readable message
+
Machine-readable information
```

Don't add attributes just because you can. Store information that the handling code actually needs.


## 6. Catching Specific vs Broad Exceptions

Prefer catching the **most specific exception you can meaningfully handle**.

For example:

```python
try:
    process_payment()

except InsufficientBalance:
    print("Please add more funds")

except PaymentTimeout:
    print("Payment timed out")
```

Each exception receives an appropriate response.

### Catching the parent category

Sometimes several related errors should receive the same treatment:

```python
try:
    process_payment()

except PaymentError:
    print("Payment could not be completed")
```

This is useful when the caller does not need to distinguish between the individual payment errors.

### Avoid unnecessary broad catching

Be careful with:

```python
try:
    process_payment()

except Exception:
    print("Something went wrong")
```

This catches almost every ordinary application exception, including errors you may not have intended to handle.

It can hide programming bugs such as:

```text
TypeError
AttributeError
IndexError
```

and make debugging harder.

The principle is:

> **Catch an exception when you know how to handle it.**

### Don't use exceptions for ordinary control flow

Exceptions should generally represent exceptional situations.

If a normal operation can be expressed more clearly using ordinary control flow, prefer that.

For example, don't intentionally raise and catch an exception just to represent a normal `if/else` decision.


## 7. Exception Chaining (`raise ... from ...`)

Sometimes a low-level exception occurs, but the application wants to expose a more meaningful higher-level exception.

Suppose a database operation fails:

```python
try:
    data = database.fetch_user(user_id)

except DatabaseError as error:
    raise UserRepositoryError(
        "Could not retrieve user"
    ) from error
```

The important part is:

```python
from error
```

This is **exception chaining**.

It tells Python:

```text
UserRepositoryError
        ↓
caused by
        ↓
DatabaseError
```

The original exception is preserved as the cause.

### Why is this useful?

Different layers of an application often operate at different levels of abstraction.

For example:

```text
Database layer
    ↓
DatabaseConnectionError

Repository layer
    ↓
UserRepositoryError

Service layer
    ↓
Application-level handling
```

The repository can translate a low-level implementation error into an error meaningful to the application:

```python
raise UserRepositoryError(
    "Unable to retrieve user"
) from error
```

The higher-level code can then handle:

```python
except UserRepositoryError:
    ...
```

without needing to know the details of the database library.

At the same time, the original cause remains available for debugging.


## 8. Common Mistakes + Practical Example

### Common Mistake 1 — Creating custom exceptions for everything

Don't create a custom class for every validation:

```text
InvalidAge
InvalidName
InvalidEmail
InvalidUsername
...
```

unless those errors have meaningful independent handling.

Often a built-in exception is enough:

```python
raise ValueError("Invalid age")
```


### Common Mistake 2 — Catching `Exception` everywhere

Avoid:

```python
try:
    ...
except Exception:
    pass
```

This can silently hide bugs.



### Common Mistake 3 — Losing the original exception

Avoid:

```python
except DatabaseError:
    raise RepositoryError("Database failed")
```

when the original cause is useful.

Prefer:

```python
except DatabaseError as error:
    raise RepositoryError(
        "Database operation failed"
    ) from error
```



### Common Mistake 4 — Creating meaningless hierarchies

Don't create inheritance merely for the sake of inheritance.

The hierarchy should communicate:

```text
"This error belongs to this category."
```



### Practical Example

Let's combine the important concepts.

```python
class OrderError(Exception):
    """Base exception for order-related errors."""


class OrderNotFound(OrderError):
    pass


class InvalidOrderState(OrderError):
    pass


class OutOfStock(OrderError):

    def __init__(self, product, requested, available):
        self.product = product
        self.requested = requested
        self.available = available

        super().__init__(
            f"{product}: requested {requested}, "
            f"but only {available} available"
        )


class OrderService:

    def get_order(self, order_id):

        if order_id != 1:
            raise OrderNotFound(
                f"Order {order_id} was not found"
            )

        return {"id": order_id}

    def check_stock(self, product, requested, available):

        if requested > available:
            raise OutOfStock(
                product,
                requested,
                available
            )
```

### Specific handling

```python
service = OrderService()

try:
    service.check_stock("Laptop", 5, 2)

except OutOfStock as error:
    print(error)
```

Output:

```text
Laptop: requested 5, but only 2 available
```

### Category-level handling

```python
try:
    service.get_order(10)

except OrderError as error:
    print(f"Order operation failed: {error}")
```

Output:

```text
Order operation failed: Order 10 was not found
```

The design is:

```text
Exception
    │
    └── OrderError
          │
          ├── OrderNotFound
          ├── InvalidOrderState
          └── OutOfStock
```

This gives us both:

```text
Specific handling
    ↓
OutOfStock

General order handling
    ↓
OrderError
```

### Final mental model

```text
Built-in Exceptions
        ↓
General Python errors

Custom Exceptions
        ↓
Application/domain-specific errors

Exception Hierarchy
        ↓
Organize related errors

Exception Attributes
        ↓
Carry structured information

Specific Catching
        ↓
Handle known problems precisely

Exception Chaining
        ↓
Preserve the original cause
```