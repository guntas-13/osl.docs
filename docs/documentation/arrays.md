---
icon: material/format-list-group
---

# Arrays

Arrays in osl are dynamic, Python-like data structures that can store heterogeneous elements (integers, floats, characters, or other arrays).

## Declaration and Initialization

Arrays can be created in two ways:

```py
// Dynamic initialization with values
var arr := [1, 2+5, 3, 4, 5];  // arr = [1, 7, 3, 4, 5]

// Nested arrays
var nested := [1, [2, 3, [4, 5]], 6, 7];

// Fixed-size declaration (initialized to 0)
var matrix[2][3][4];  // 3D array
var lst[13];          // 1D array
```

## Accessing and Modifying

Arrays use zero-based indexing:

```py
var arr := [1, [2, 3, [4, 5]], 6, 7];
log arr[0];     // Prints 1 (first element)
log arr[3];     // Prints 7 (last element)

// Nested access
var x := arr[1][0] + arr[1][2][1];      // Computes x using multi-dimensional indexing

// Modification
arr[1][2][x - 7] := 10;     // Modifies nested element
log arr[1][2][0];       // Prints 10
```

## Multi-dimensional Arrays

osl supports multi-dimensional arrays through nesting:

```py
// Creating and initializing a 2D array
var arr[11][10];
var i := 0;
while (i < 11) {
    var j := 0;
    while (j < 10) {
        arr[i][j] := 10*i + j;
        j := j + 1;
    }
    i := i + 1;
}
log arr[5][5];  // Prints 55
```

## Dynamic Sizing

Arrays created with `[]` can hold any number of elements, while declared arrays with `[n]` have fixed dimensions.

## Type System

In the typed version of osl, array types are explicitly declared:

```py
var i32[2][3] matrix;  // 2x3 matrix of 32-bit integers
var i32 arr := {1, 2, 3};  // Array of integers using curly braces
```

## Implementation Details

Arrays are implemented as heap-allocated `Value` arrays, supporting dynamic sizing and heterogeneous elements. The VM handles array operations with opcodes like `MAKE_ARRAY`, `MAKE_ARRAY_DECL`, `ARR_ACC`, and `STORE`.
