---
icon: material/code-json
---

# README: osl. Language Syntax

## 1. Variables

### Syntax

Variables in osl. are declared using the `var` keyword, followed by an optional initialization using `:=`. Variables are dynamically typed, allowing them to hold integers, floats, characters, arrays, or function objects.

```python
var x := 5;           // Integer
var y := 3.5;         // Float
var z := 'a';         // Character
var t := x + 'g';     // Character (computed as 'l')
```

### Features

- **Assignment**: Variables can be reassigned using `:=` after declaration.
- **Scope**: Variables are scoped to the block they are declared in, with support for nested scopes and closures.
- **Dynamic Typing**: A variable's type can change with reassignment, e.g., from an integer to a character.

**Example**:

```python
var x := 5;
var y := x - 1.5;   // y = 3.5
var z := x + 'g';   // z = 'l'
log x;              // Prints 5
log y;              // Prints 3.5
log z;              // Prints 'l'
```

## 2. Arrays

### Syntax

python supports dynamic, Python-like arrays, declared using square brackets `[]` for initialization or `var arr[n]` for declared arrays. Arrays can be multi-dimensional and store heterogeneous elements (integers, floats, characters, or other arrays).

- **Initialization**:
  ```python
  var arr := [1, 2+5, 3, 4, 5];  // Array of integers
  var arr2 := [[1, 2], [3, 4], 3, 4, 5];  // Nested array
  ```
- **Declared Array**:
  ```python
  var arr6[2][3][4];  // 3D array initialized to 0
  var arr7[13];       // 1D array initialized to 0
  ```

### Features

- **Indexing**: Arrays are accessed using `[index]`, with zero-based indexing.
- **Assignment**: Elements can be modified using `arr[index] := value`.
- **Multi-dimensional Arrays**: Nested arrays are supported, with each level accessed using additional brackets.
- **Dynamic Sizing**: Arrays created with `[]` can hold any number of elements, while declared arrays have fixed dimensions.

**Example**:

```python
var arr := [1, 2+5, 3, 4, 5];  // arr = [1, 7, 3, 4, 5]
arr[0] := 10;                  // Modify first element
log arr[4];                    // Prints 5
log arr[1];                    // Prints 7

var arr2 := [[1, 2], [3, 4], 3, 4, 5];
arr2[1][0] := 10;              // Modify nested element
log arr2[1][0];                // Prints 10
```

## 3. Functions as First-Class Citizens

### Syntax

Functions in osl. are defined using the `def` keyword, followed by a name, parameters in parentheses, and a body in curly braces `{}`. Functions are first-class, meaning they can be assigned to variables, passed as arguments, and returned from other functions.

```python
def fact(n) {
    if (n = 0) {
        return 1;
    }
    return n * fact(n-1);
}
```

### Features

- **First-Class Functions**: Functions can be stored in variables, passed to other functions, or returned.
- **Closures**: Functions can capture variables from their enclosing scope, creating closures.
- **Recursion**: Functions can call themselves, as seen in recursive algorithms like factorial.

**Example**:

```python
def fact(n) {
    if (n = 0) {
        return 1;
    }
    return n * fact(n-1);
}
log fact(5);  // Prints 120
```

**Closure Example**:

```python
def counter() {
    var count := 0;
    def inc() {
        count := count + 1;
        return count;
    }
    return inc;
}
var c1 := counter();
var c2 := counter();
log c1();  // Prints 1
log c1();  // Prints 2
log c2();  // Prints 1
```

## 4. Function Calls

### Syntax

Functions are called using the function name or a variable holding a function, followed by arguments in parentheses. osl. supports nested function calls and chained calls for closures.

```python
log fact(5);           // Call fact with argument 5
var f := counter();    // Call counter to get inc function
log f();               // Call inc
log counter()();       // Chained call -> creates a new instance of inc and calls it right away
```

### Features

- **Argument Passing**: Arguments are passed by value, with closures maintaining references to captured variables.
- **Nested Calls**: Functions can be called within other expressions or as part of chained calls.
- **Return Values**: Functions return values using the `return` statement, or `0` if no return is specified.

**Example**:

```python
def f(a) {
    return a + 2;
}
log f(2);  // Prints 4

def foo(y) {
    def bar(z) {
        log x;  // Accesses outer variable x
        return x;
    }
    log x;
    return bar;
}
var f := foo(46);
var g := f(12);  // Chained call: foo(46)(12)
log g;           // Prints 5 (value of x)
```

## 5. Control Flow

### Syntax

osl. supports conditional statements (`if`, `if-else`) and loops (`while`) for control flow.

- **If Statement**:
  ```python
  if (condition) {
      // body
  } else {
      // else body
  }
  ```
- **While Loop**:
  ```python
  while (condition) {
      // body
  }
  ```

### Features

- **Conditions**: Conditions are expressions that evaluate to integers (0 for false, non-zero for true).
- **Nested Control Flow**: If statements and loops can be nested to arbitrary depth.
- **Short-Circuit Evaluation**: Logical operators (`&&`, `||`) are supported for combining conditions.

**Example**:

```python
var x := 1;
var y := 1;
while x < 10 {
    if y = 5 {
        log y;  // Prints 5 when y equals 5
    } else {
        log x + 50;  // Prints x + 50 for other iterations
    }
    y := y + 1;
    log x;
    x := x + 1;
}
```

**Example (While Loop with Arrays)**:

```python
var arr3 := [10, 11, 12, 13, 14];
var i := 0;
while (i < 5) {
    log arr3[i];  // Prints elements 10, 11, 12, 13, 14
    i := i + 1;
}
```

## 6. Operators

### Syntax

osl. supports arithmetic, comparison, and logical operators, which can be used in expressions.

- **Arithmetic**: `+`, `-`, `*`, `/`, `%`
- **Comparison**: `<`, `>`, `<=`, `>=`, `=`, `~=`
- **Logical**: `&&`, `||`, `~` (not)

**Example**:

```python
var x := 5;
var y := x - 1.5;  // y = 3.5
log x < y;         // Prints 0 (false)
log 1.5 + 2;       // Prints 3.5
log x % 2;         // Prints 1
log x ~= y;        // Prints 1 (true)
```

### Features

- **Type Coercion**: Operations between different types (e.g., integer and float) may result in a float or integer, depending on the operation.
- **Character Arithmetic**: Characters can be manipulated with arithmetic (e.g., `'a' + 1` yields `'b'`).

## 7. I/O Operations

### Syntax

The `log` statement is used to print values to the standard output. It supports integers, floats, characters, and arrays.

```python
log x;           // Prints the value of x
log arr[0];      // Prints the first element of arr
```

### Features

- **Polymorphic Output**: `log` handles different types, printing integers as numbers, floats with decimals, and characters as symbols.
- **Array Output**: Arrays can be logged, typically printing their elements or memory address.

**Example**:

```python
var s := "Guntas";
var i := 0;
while (i < 6) {
    log s[i];  // Prints G, u, n, t, a, s
    i := i + 1;
}
```

## 8. Example Programs

### Factorial

A recursive factorial function demonstrates function calls and conditionals:

```python
def fact(n) {
    if (n = 0) {
        return 1;
    }
    return n * fact(n-1);
}
log fact(15);  // Prints 1307674368000
```

### Multi-dimensional Array Initialization

This example initializes and prints a 2D array:

```python
var arr7[11][10];
var i := 0;
while (i < 11) {
    var j := 0;
    while (j < 10) {
        arr7[i][j] := 10*i + j;
        j := j + 1;
    }
    i := i + 1;
}
log arr7[5][5];  // Prints 55
```

### Palindrome Product

This program finds the largest palindromic product of two numbers:

```python
def isPal(n) {
    var rev := 0;
    var nn := n;
    while (n > 0) {
        rev := rev * 10 + n % 10;
        n := n / 10;
    }
    return rev = nn;
}
var maxPal := 0;
var i := 999;
while (i >= 1) {
    var j := i;
    while (j >= 1) {
        var prod := i * j;
        if ((prod > maxPal) && (isPal(prod))) {
            maxPal := prod;
        }
        j := j - 1;
    }
    i := i - 1;
}
log maxPal;  // Prints 906609
```
