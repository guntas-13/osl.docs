---
icon: material/function
---

# Functions

## Functions as First-Class Citizens

Functions in osl. are first-class, meaning they can be assigned to variables, passed as arguments, and returned from other functions.

```python
def multiplyBy(n)
{
    def inner(x)
    {
        return x * n;
    }
    return inner;
}

var doubleBy := multiplyBy(2);
log doubleBy(5); // Outputs: 10

log multiplyBy(2)(5); // Outputs: 10
```

## Closures

Functions capture their enclosing scope, enabling closures:

```python
def counter()
{
    var count := 0;
    def increment()
    {
        count := count + 1;
        return count;
    }
    return increment;
}

var c1 := counter();
var c2 := counter();
log c1(); // Outputs: 1
log c1(); // Outputs: 2
log c2(); // Outputs: 1
log c2(); // Outputs: 2
```

## Recursion

osl. supports recursive functions:

```python
def factorial(n)
{
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}

log factorial(5); // Outputs: 120
```
