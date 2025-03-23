---
icon: material/code-json
---

# Syntax of OSL

## Operators and Precedence
OSL supports the following operators, listed in decreasing order of precedence:

| Precedence | Operators           | Description                       |
|------------|---------------------|-----------------------------------|
| 1 (Highest) | `()`, `√`, `-`                | Parentheses (explicit grouping), Square root, unary minus  |
| 2          | `^`             | Exponentiation         |
| 3          | `*`, `/`             | Multiplication, Division         |
| 4          | `+`, `-`             | Addition, Subtraction            |
| 5          | `<`, `<=`, `>`, `>=`, `=`, `!=` | Relational Operators             |
| 6          | `&&`, `||`                 | Logical AND and Logical OR                      |
| 7 (Lowest) | `:=`                 | Assignment Operator              |

## Variables: Initialization and Modification
### Variable Declaration
Variables can be declared using the `var` keyword:
```py
var x := 10;
var y := x + 5;
```

### Variable Modification
Existing variables can be reassigned using `:=`:
```py
x := 20;
y := x * 2;
```


## If-Else Statements

Conditional execution in OSL is handled using `if`, `else if`, and `else`:

```osl
let x = 10;

if (x > 10) {
    print("Greater than 10");
} else if (x == 10) {
    print("Equal to 10");
} else {
    print("Less than 10");
}
```

### Unmatched If and Dangling Else Problem

OSL follows the "else is matched with the closest unmatched if" rule:

```osl
if (x > 5)
    if (x > 10)
        print("Greater than 10");
    else
        print("Between 5 and 10");
```

To avoid ambiguity, always use braces:

```osl
if (x > 5) {
    if (x > 10) {
        print("Greater than 10");
    } else {
        print("Between 5 and 10");
    }
}
```

## Functions and Function Calls
### Function Declaration
Functions are defined using the `letFunc` keyword:
```py
letFunc add(a, b) {
    return a + b;
}
```

### Function Calls
Functions are called using parentheses:
```py
var sum := add(5, 10);
```

### First-Class Functions
OSL treats functions as first-class citizens, meaning they can:

- Be assigned to variables
  
- Be passed as arguments to other functions
  
- Be returned from functions

Example:
```py
letFunc multiplyBy(n) {
    return letFunc(x) { return x * n; };
}

var double := multiplyBy(2);
var result := double(5);
```

### Closures in Functions
Functions in OSL can capture variables from their defining scope, enabling closures:
```py
letFunc f1()
{
    var x := 10;
    letFunc f2()
    {
        return x;
    }
    return f2;
}
var msg := f1();
msg();
```
Closures allow functions to maintain state between calls by capturing the enclosing environment.




