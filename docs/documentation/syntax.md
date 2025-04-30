---
icon: material/code-json
---

# Syntax of OSL

## Variables: Initialization and Modification

### Variable Declaration

Variables can be declared using the `var` keyword:

```python
var x := 10;
var y := x + 5;
```

### Variable Modification

Existing variables can be reassigned using `:=`:

```python
x := 20;
y := x * 2;
```

## If-Else Statements

Conditional execution in OSL is handled using `if`, `else if`, and `else`:

```python
var x := 10;

if (x > 10)
    log x;
else if (x = 10)
    log x + 1;
else
    log x - 1;
```

### Unmatched If and Dangling Else Problem

osl. follows the `"else"` is matched with the closest unmatched `"if"` rule:

```python
var x := 7;
if (x > 5)
if (x > 10)
log x + 1;
else
log x - 1;
```

To avoid ambiguity, always use braces:

```python
var x := 11;

if (x > 5)
{
    if (x > 10)
    {
        log x + 4;
    }
    else
    {
        log x - 4;
    }
}
```

### Blocks

```python
var x := 5;
{
    var x := 10;
}
x;
```

```bash
5
```

```python
var x := 5;
{
    var x := 10;
    x;
}
```

```bash
10
```

## Functions and Function Calls

### Function Declaration

Functions are defined using the `def` keyword:

```python
def add(a, b)
{
    return a + b;
}
```

### Function Calls

Functions are called using parentheses:

```python
var sum := add(5, 10);
```

### First-Class Functions

osl. treats functions as first-class citizens, meaning they can:

- Be assigned to variables
- Be passed as arguments to other functions
- Be returned from functions

Example:

```python
def multiplyBy(n)
{
    def inner(x)
    {
        return x * n;
    }
    return inner;
}

var doubleTo := multiplyBy(2);
var result := doubleTo(5);
```

### Closures in Functions

Functions in osl can capture variables from their defining scope, enabling closures:

```python
def f1()
{
    var x := 10;
    def f2()
    {
        return x;
    }
    return f2;
}
var msg := f1();
msg();
```

```python
def fib(n)
{
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}
fib(5);
```

Closures allow functions to maintain state between calls by capturing the enclosing environment.
