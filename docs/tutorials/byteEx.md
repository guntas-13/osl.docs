---
icon: material/hexadecimal
---

# Bytecode Examples

- **Stack-Based Execution**: Instructions push and pop values on the stack, with operations like `ADD`, `MUL`, and `CALL` consuming operands from the stack and pushing results.
- **Environment Management**: Variables are bound to IDs using `BIND`, accessed with `GET`, and updated with `SET`. The linked-list environment (`adjList`) supports scoping and closures.
- **Closures**: The `MAKE_CLOSURE` opcode captures variables identified during escape analysis, ensuring they persist for nested functions.
- **Arrays**: `MAKE_ARRAY` and `MAKE_ARRAY_DECL` create dynamic and declared arrays, with `ARRACC` and `STORE` handling indexing and updates.
- **Control Flow**: `JUMP`, `JUMP_IF_ZERO`, and `RETURN` manage program flow, supporting conditionals and loops.

## Factorial Function

```python
def fact(n) {
    if (n = 0) {
        return 1;
    }
    return n * fact(n-1);
}
log fact(5);
```

## Bytecode

```
JUMP 92                  # Skip function body to setup code (jump forward 92 bytes)
BIND 2                   # Bind parameter n to ID 2 in the current scope
GET 2                    # Push value of n (ID 2) onto the stack
PUSH_INT 0               # Push 0 for comparison with n
EQ                       # Pop n and 0, push 1 if n == 0, else 0
JUMP_IF_ZERO 10          # If top is 0 (n != 0), jump forward 10 bytes to skip return 1
PUSH_INT 1               # Push 1 (base case result)
RETURN                   # Pop 1 and return address, return to caller
GET 2                    # Push n (ID 2)
PUSH_INT 95              # Push placeholder return address
GET 2                    # Push n again
PUSH_INT 1               # Push 1
SUB                      # Pop n and 1, push n - 1
GET 1                    # Push fact function object (ID 1)
CALL                     # Call fact(n-1), push return address, execute function
MUL                      # Pop n and fact(n-1), push n * fact(n-1)
RETURN                   # Pop result and return address, return to caller
PUSH_INT 5               # Push entry point (offset 5, start of BIND 2)
PUSH_INT 97              # Push exit point (offset 97, after function body)
MAKE_FUNC                # Pop entry/exit points, create function object, push it
BIND 1                   # Bind function object to fact (ID 1)
GET 1                    # Push fact function object
PUSH_INT 0               # Push 0 (no captured variables)
MAKE_CLOSURE             # Create empty closure for fact
PUSH_INT 172             # Push placeholder return address
PUSH_INT 5               # Push argument 5
GET 1                    # Push fact function object
CALL                     # Call fact(5), push return address
LOG                      # Pop result (120), print it
HALT                     # Terminate execution
```

- **Purpose**: Computes `fact(5)` (120) using a recursive function and prints the result.
- **Key Opcodes**:
  - `JUMP 92`: Skips the function body during initialization to set up the function object.
  - `BIND 2`, `GET 2`: Manage the parameter `n` and its value.
  - `EQ`, `JUMP_IF_ZERO`: Implement the `if (n = 0)` condition.
  - `CALL`, `MUL`: Handle the recursive call and multiplication for `n * fact(n-1)`.
  - `MAKE_FUNC`, `MAKE_CLOSURE`: Create the function object with an empty closure (no captured variables).
- **Comments**: Each instruction is annotated with its effect on the stack or environment, clarifying control flow (e.g., jumps) and function setup.

## Array Manipulation

```python
var arr := [1, [2, 3, [4, 5]], 6, 7];
var x := arr[1][0] + arr[1][2][1];
arr[1][2][x - 7] := 10;
log arr[1][2][0];
var M := 1;
var N := 2;
var a[M][M + N];
```

## Bytecode

```
PUSH_INT 7               # Push 7 (last element of arr)
PUSH_INT 6               # Push 6 (third element of arr)
PUSH_INT 5               # Push 5 (second element of [4, 5])
PUSH_INT 4               # Push 4 (first element of [4, 5])
MAKE_ARRAY 2             # Pop 4, 5, create array [4, 5], push it
PUSH_INT 3               # Push 3 (second element of [2, 3, [4, 5]])
PUSH_INT 2               # Push 2 (first element of [2, 3, [4, 5]])
MAKE_ARRAY 3             # Pop 2, 3, [4, 5], create array [2, 3, [4, 5]], push it
PUSH_INT 1               # Push 1 (first element of arr)
MAKE_ARRAY 4             # Pop 1, [2, 3, [4, 5]], 6, 7, create array [1, [2, 3, [4, 5]], 6, 7], push it
BIND 1                   # Bind array to arr (ID 1)
PUSH_INT 0               # Push index 0
PUSH_INT 1               # Push index 1
GET 1                    # Push arr (ID 1)
ARRACC                   # Pop arr, 1, push arr[1]
ARRACC                   # Pop arr[1], 0, push arr[1][0] (2)
PUSH_INT 1               # Push index 1
PUSH_INT 2               # Push index 2
PUSH_INT 1               # Push index 1
GET 1                    # Push arr
ARRACC                   # Pop arr, 1, push arr[1]
ARRACC                   # Pop arr[1], 2, push arr[1][2]
ARRACC                   # Pop arr[1][2], 1, push arr[1][2][1] (5)
ADD                      # Pop 2, 5, push 2 + 5 = 7
BIND 2                   # Bind 7 to x (ID 2)
PUSH_INT 10              # Push value 10
GET 2                    # Push x (7)
PUSH_INT 7               # Push 7
SUB                      # Pop 7, 7, push 7 - 7 = 0
PUSH_INT 2               # Push index 2
PUSH_INT 1               # Push index 1
GET 1                    # Push arr
ARRACC                   # Pop arr, 1, push arr[1]
ARRACC                   # Pop arr[1], 2, push arr[1][2]
ARRACC                   # Pop arr[1][2], 0, push arr[1][2][0]
STORE                    # Pop 10, arr[1][2], 0, store 10 at arr[1][2][0]
PUSH_INT 0               # Push index 0
PUSH_INT 2               # Push index 2
PUSH_INT 1               # Push index 1
GET 1                    # Push arr
ARRACC                   # Pop arr, 1, push arr[1]
ARRACC                   # Pop arr[1], 2, push arr[1][2]
ARRACC                   # Pop arr[1][2], 0, push arr[1][2][0] (10)
LOG                      # Pop 10, print it
PUSH_INT 1               # Push 1
BIND 3                   # Bind 1 to M (ID 3)
PUSH_INT 2               # Push 2
BIND 4                   # Bind 2 to N (ID 4)
GET 3                    # Push M (1)
GET 3                    # Push M (1)
GET 4                    # Push N (2)
ADD                      # Pop 1, 2, push 1 + 2 = 3
MAKE_ARRAY_DECL 2        # Pop 1, 3, create 2D array [1][3] initialized to 0
BIND 5                   # Bind array to a (ID 5)
HALT                     # Terminate execution
```

- **Purpose**: Creates a nested array, computes `x` using multi-dimensional indexing, updates an element, and declares a 2D array.
- **Key Opcodes**:
  - `MAKE_ARRAY`: Builds the nested array bottom-up, creating `[4, 5]`, then `[2, 3, [4, 5]]`, then `[1, ..., 7]`.
  - `ARRACC`: Accesses multi-dimensional elements (e.g., `arr[1][0]`).
  - `STORE`: Updates `arr[1][2][0]` to 10.
  - `MAKE_ARRAY_DECL`: Creates a zero-initialized 2D array `[1][3]`.
- **Comments**: Detail the construction of nested arrays, index calculations, and array modifications, making the sequence of `ARRACC` calls clear.

## Counter with Closure

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
log c1();
log c1();
log c1();
log c2();
log c2();
```

#### Commented Bytecode

```
JUMP 127                 # Skip counter body to setup code (jump forward 127 bytes)
PUSH_INT 0               # Push 0 (initial count)
BIND 2                   # Bind 0 to count (ID 2)
JUMP 38                  # Skip inc body to setup code (jump forward 38 bytes)
GET 2                    # Push count (ID 2)
PUSH_INT 1               # Push 1
ADD                      # Pop count, 1, push count + 1
SET 2                    # Pop count + 1, update count (ID 2)
GET 2                    # Push updated count
RETURN                   # Pop count and return address, return to caller
PUSH_INT 28              # Push entry point for inc (offset 28)
PUSH_INT 66              # Push exit point for inc (offset 66)
MAKE_FUNC                # Pop entry/exit points, create inc function, push it
BIND 3                   # Bind inc function to ID 3
GET 3                    # Push inc function
PUSH_INT 2               # Push ID 2 (count)
PUSH_INT 1               # Push 1 (number of captured variables)
MAKE_CLOSURE             # Pop ID 2, 1, create closure for inc capturing count
GET 3                    # Push inc function
RETURN                   # Pop inc and return address, return to counter caller
PUSH_INT 5               # Push entry point for counter (offset 5)
PUSH_INT 132             # Push exit point for counter (offset 132)
MAKE_FUNC                # Pop entry/exit points, create counter function, push it
BIND 1                   # Bind counter function to ID 1
GET 1                    # Push counter function
PUSH_INT 0               # Push 0 (no captured variables)
MAKE_CLOSURE             # Create empty closure for counter
PUSH_INT 198             # Push placeholder return address
GET 1                    # Push counter function
CALL                     # Call counter, push return address, get inc
BIND 4                   # Bind inc to c1 (ID 4)
PUSH_INT 226             # Push placeholder return address
GET 1                    # Push counter function
CALL                     # Call counter again, get another inc
BIND 5                   # Bind inc to c2 (ID 5)
PUSH_INT 254             # Push placeholder return address
GET 4                    # Push c1 (inc)
CALL                     # Call c1, push return address, increment count (1)
LOG                      # Pop 1, print it
PUSH_INT 274             # Push placeholder return address
GET 4                    # Push c1
CALL                     # Call c1, increment count (2)
LOG                      # Pop 2, print it
PUSH_INT 294             # Push placeholder return address
GET 4                    # Push c1
CALL                     # Call c1, increment count (3)
LOG                      # Pop 3, print it
PUSH_INT 314             # Push placeholder return address
GET 5                    # Push c2
CALL                     # Call c2, increment count (1)
LOG                      # Pop 1, print it
PUSH_INT 334             # Push placeholder return address
GET 5                    # Push c2
CALL                     # Call c2, increment count (2)
LOG                      # Pop 2, print it
HALT                     # Terminate execution
```

- **Purpose**: Creates two counters (`c1`, `c2`) with separate `count` variables, demonstrating closure state persistence.
- **Key Opcodes**:
  - `MAKE_CLOSURE`: Captures `count` (ID 2) for `inc`, ensuring each `inc` has its own counter.
  - `SET`, `GET`: Update and retrieve `count` within the closure.
  - `CALL`: Invokes `counter` to create `inc` and `inc` to increment `count`.
- **Comments**: Highlight the closure setup, variable capture, and repeated calls, clarifying how `c1` and `c2` maintain separate states.

## Nested Closures

```python
var x := 5;
def f(a) {
    var c := 0;
    def g(b) {
        var y := 10;
        def hh(z) {
            hh(1);
            return z + 1;
        }
        def h(d) {
            var tmp := 8;
            b := tmp;
            var z := b * 2;
            b := b + g(d);
            return a + b + d + hh(x);
        }
        return h;
    }
    return g;
}
var t := f(10)(5)(3);
log t;
```

## Bytecode

```
PUSH_INT 5               # Push 5
BIND 1                   # Bind 5 to x (ID 1)
JUMP 529                 # Skip f body to setup code (jump forward 529 bytes)
BIND 3                   # Bind parameter a to ID 3
PUSH_INT 0               # Push 0
BIND 4                   # Bind 0 to c (ID 4)
JUMP 422                 # Skip g body to setup code (jump forward 422 bytes)
BIND 6                   # Bind parameter b to ID 6
PUSH_INT 10              # Push 10
BIND 7                   # Bind 10 to y (ID 7)
JUMP 57                  # Skip hh body to setup code (jump forward 57 bytes)
BIND 9                   # Bind parameter z to ID 9
PUSH_INT 124             # Push placeholder return address
PUSH_INT 1               # Push argument 1
GET 8                    # Push hh function (ID 8)
CALL                     # Call hh(1), recursive call (no-op due to return)
GET 9                    # Push z
PUSH_INT 1               # Push 1
ADD                      # Pop z, 1, push z + 1
RETURN                   # Pop z + 1 and return address, return to caller
PUSH_INT 87              # Push entry point for hh (offset 87)
PUSH_INT 144             # Push exit point for hh (offset 144)
MAKE_FUNC                # Pop entry/exit points, create hh function, push it
BIND 8                   # Bind hh to ID 8
GET 8                    # Push hh function
PUSH_INT 8               # Push ID 8 (hh, self-referential)
PUSH_INT 1               # Push 1 (number of captured variables)
MAKE_CLOSURE             # Pop ID 8, 1, create closure for hh capturing itself
JUMP 179                 # Skip h body to setup code (jump forward 179 bytes)
BIND 11                  # Bind parameter d to ID 11
PUSH_INT 8               # Push 8
BIND 12                  # Bind 8 to tmp (ID 12)
GET 12                   # Push tmp
SET 6                    # Pop tmp, update b (ID 6) to 8
GET 6                    # Push b
PUSH_INT 2               # Push 2
MUL                      # Pop b, 2, push b * 2
BIND 13                  # Bind b * 2 to z (ID 13)
GET 6                    # Push b
PUSH_INT 315             # Push placeholder return address
GET 11                   # Push d
GET 5                    # Push g function (ID 5)
CALL                     # Call g(d), push return address
ADD                      # Pop b, g(d), push b + g(d)
SET 6                    # Pop b + g(d), update b
GET 3                    # Push a
GET 6                    # Push b
ADD                      # Pop a, b, push a + b
GET 11                   # Push d
ADD                      # Pop a + b, d, push a + b + d
PUSH_INT 382             # Push placeholder return address
GET 1                    # Push x (ID 1, value 5)
GET 8                    # Push hh function
CALL                     # Call hh(x), push return address, compute hh(5) = 6
ADD                      # Pop a + b + d, hh(x), push a + b + d + hh(x)
RETURN                   # Pop result and return address, return to caller
PUSH_INT 205             # Push entry point for h (offset 205)
PUSH_INT 384             # Push exit point for h (offset 384)
MAKE_FUNC                # Pop entry/exit points, create h function, push it
BIND 10                  # Bind h to ID 10
GET 10                   # Push h function
PUSH_INT 6               # Push ID 6 (b)
PUSH_INT 8               # Push ID 8 (hh)
PUSH_INT 3               # Push ID 3 (a)
PUSH_INT 5               # Push ID 5 (g)
PUSH_INT 4               # Push 4 (number of captured variables)
MAKE_CLOSURE             # Pop IDs and count, create closure for h
GET 10                   # Push h function
RETURN                   # Pop h and return address, return to g caller
PUSH_INT 55              # Push entry point for g (offset 55)
PUSH_INT 477             # Push exit point for g (offset 477)
MAKE_FUNC                # Pop entry/exit points, create g function, push it
BIND 5                   # Bind g to ID 5
GET 5                    # Push g function
PUSH_INT 3               # Push ID 3 (a)
PUSH_INT 5               # Push ID 5 (g)
PUSH_INT 2               # Push 2 (number of captured variables)
MAKE_CLOSURE             # Pop IDs and count, create closure for g
GET 5                    # Push g function
RETURN                   # Pop g and return address, return to f caller
PUSH_INT 23              # Push entry point for f (offset 23)
PUSH_INT 552             # Push exit point for f (offset 552)
MAKE_FUNC                # Pop entry/exit points, create f function, push it
BIND 2                   # Bind f to ID 2
GET 2                    # Push f function
PUSH_INT 0               # Push 0 (no captured variables)
MAKE_CLOSURE             # Create empty closure for f
PUSH_INT 665             # Push placeholder return address
PUSH_INT 3               # Push argument 3
PUSH_INT 664             # Push placeholder return address
PUSH_INT 5               # Push argument 5
PUSH_INT 663             # Push placeholder return address
PUSH_INT 10              # Push argument 10
GET 2                    # Push f function
CALL                     # Call f(10), push return address, get g
CALL                     # Call g(5), push return address, get h
CALL                     # Call h(3), push return address
BIND 14                  # Bind result to t (ID 14)
GET 14                   # Push t
LOG                      # Pop t, print it
HALT                     # Terminate execution
```

- **Purpose**: Demonstrates nested closures with `f` returning `g`, `g` returning `h`, and `h` using `hh`, computing a result from `f(10)(5)(3)`.
- **Key Opcodes**:
  - `MAKE_CLOSURE`: Creates closures for `g` (capturing `a`, `g`), `h` (capturing `b`, `hh`, `a`, `g`), and `hh` (capturing itself).
  - `CALL`: Handles chained calls (`f(10)(5)(3)`).
  - `SET`, `GET`: Manage updates to captured variables (e.g., `b` in `h`).
- **Comments**: Clarify the nested function definitions, closure captures, and the complex computation in `h`, including the recursive call to `g`.
