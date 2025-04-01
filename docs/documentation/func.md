---
icon: material/function
---

# Functions

## Functions as First-Class Citizens

Functions in OSL are first-class, meaning they can be assigned to variables, passed as arguments, and returned from other functions.

```python
fn multiplyBy(n)
{
    fn inner(x)
    {
        return x * n;
    }
    return inner;
}

var doubleBy := multiplyBy(2);
log doubleBy(5); // Outputs: 10
```

## Closures

Functions capture their enclosing scope, enabling closures:

```python
fn counter()
{
    var count := 0;
    fn increment()
    {
        count := count + 1;
        return count;
    }
    return increment;
}

var c := counter();
log c(); // Outputs: 1
log c(); // Outputs: 2
```

## Recursion

osl. supports recursive functions:

```python
fn factorial(n)
{
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}

log factorial(5); // Outputs: 120
```
