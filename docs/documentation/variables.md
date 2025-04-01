---
icon: material/variable
---

# Variables and Constants

## Declaration

Use `var` to declare variables with an optional initial value:

```python
var x := 10;
var y; // Uninitialized, defaults to NULL
```

## Modification

Reassign variables with `:=`:

```python
x := 20;
```

## Constants

Use `const` to declare constants (yet to implement):

```python
const x := 10;
```

# Typing and Casting

osl is strongly typed with explicit type declarations:

```python
var i32 x := 10;
var bool flag := true;
const i32 y := 20;
```

## Types

osl supports a robust set of primitive types, with plans for future expansion. The current type system includes:

### Boolean

- `bool`: Represents a boolean value (`true` or `false`).

### Signed Integers

- `i8`: 8-bit signed integer (-128 to 127)
- `i16`: 16-bit signed integer (-32,768 to 32,767)
- `i32`: 32-bit signed integer (-2,147,483,648 to 2,147,483,647)
- `i64`: 64-bit signed integer (-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807)
- `i128`: 128-bit signed integer (-2^127 to 2^127 - 1)

### Unsigned Integers

- `u8`: 8-bit unsigned integer (0 to 255)
- `u16`: 16-bit unsigned integer (0 to 65,535)
- `u32`: 32-bit unsigned integer (0 to 4,294,967,295)
- `u64`: 64-bit unsigned integer (0 to 18,446,744,073,709,551,615)
- `u128`: 128-bit unsigned integer (0 to 2^128 - 1)

### Floating-Point Numbers

- `f8`: 8-bit floating-point (details TBD, likely a custom minifloat format)
- `f16`: 16-bit floating-point (IEEE 754 half-precision)
- `f32`: 32-bit floating-point (IEEE 754 single-precision)
- `f64`: 64-bit floating-point (IEEE 754 double-precision)
- `f128`: 128-bit floating-point (IEEE 754 quadruple-precision)

### Future Types

- `c16`: UTF-16 character (planned for future implementation to support Unicode text processing).

### Declaration Examples

```python
var i32 x := 42;
var u16 y := 1000;
var f64 z := 3.14159;
```

## Casting and Conversions

OSL provides both implicit and explicit mechanisms for type conversions, balancing convenience with control. The system is designed to handle operations across different bit sizes while ensuring data integrity, with runtime warnings planned for potential data loss.

### Implicit Conversions in Operations

When performing operations (e.g., addition, multiplication) between integers of different bit sizes, OSL implicitly converts the operands to the larger bit size to prevent overflow or truncation during computation. Here’s how it works:

1. **Upcasting to Larger Bit Size**:

   - The operand with the smaller bit size is widened to match the larger bit size of the other operand.
   - Signedness is preserved where possible:
     - `i8 + i32` → Both operands treated as `i32` (upcast `i8` to `i32`).
     - `u16 * u64` → Both treated as `u64` (upcast `u16` to `u64`).
     - `i16 + u32` → Both treated as `i32` (upcast `i16` to `i32`, `u32` downcast to signed if needed; see warnings below).
   - For mixed integer/float operations, integers are promoted to the float type:
     - `i32 + f64` → Both treated as `f64` (upcast `i32` to `f64`).

2. **Result Assignment and Downcasting**:
   - After the operation, the result is cast down to the bit size of the target variable.
   - Example:
     ```python
     var i8 a := 100;    // i8: -128 to 127
     var i32 b := 50;    // i32: wider range
     var i8 c := a + b;  // a upcast to i32, result (150) downcast to i8
     ```
     - Computation: `100 (i8)` → `100 (i32)` + `50 (i32)` = `150 (i32)`.
     - Assignment: `150 (i32)` → `150 (i8)` (overflow occurs, see warnings).

### Runtime Warnings for Data Loss

Implicit conversions can lead to data loss, especially during downcasting. OSL plans to implement runtime warnings to alert developers of such cases:

- **Overflow/Underflow**: When a larger integer is downcast to a smaller size and exceeds the target’s range.
  - Example: `150 (i32)` → `i8` results in `-106` due to overflow (150 - 256 = -106). A warning like `"Warning: Overflow in i32 to i8 conversion"` will be issued.
- **Precision Loss**: When converting floats to integers or smaller floats.
  - Example: `3.14159 (f64)` → `f32` may lose precision; warning: `"Warning: Precision loss in f64 to f32 conversion"`.
- **Signedness Change**: Mixing signed and unsigned types may truncate or misinterpret values.
  - Example: `u32 300 + i8 50` → `i8` might warn: `"Warning: Potential data loss in unsigned to signed conversion"`.

These warnings are not yet implemented but are planned for a future release to enhance debugging and safety.

### Explicit Conversions (C-Style)

For precise control, OSL supports explicit casting similar to C. Use the target type as a function-like operator:

- Syntax: `<type>(expression)`
- Examples:
  ```python
  var i32 x := 500;
  var i8 y := i8(x);    // Explicitly cast i32 to i8, may overflow
  var f32 z := f32(3);  // Cast i32 literal to f32
  var u16 w := u16(-10); // Cast signed to unsigned, may warn later
  ```

Explicit casts override implicit rules and allow developers to:

- Force a specific type regardless of operation context.
- Accept potential data loss without relying on runtime checks (though warnings may still apply in the future).

### Example Workflow

```python
var i16 a := 1000;
var i32 b := 2000;
var i8 c := i8(a + b); // Implicit upcast to i32, then explicit downcast to i8

log(c); // Expected: -24 (3000 overflows i8 range: 3000 - 256*11 = -24)
// Future warning: "Warning: Overflow in i32 to i8 conversion"
```

1. `a (i16)` → `1000 (i32)` (upcast to match `b`).
2. `1000 (i32) + 2000 (i32)` = `3000 (i32)`.
3. `3000 (i32)` → `i8(c)` → `-24` (overflow: 3000 mod 256 = 24, but signed i8 adjusts to -24).
4. Future runtime warning will flag the overflow.

## Future Considerations

- **UTF-16 Characters (`c16`)**: Planned to support Unicode via 16-bit characters. Casting between `c16` and integers (e.g., `i16(c16)`) will be added.
- **Warning Implementation**: Runtime checks will be integrated into the VM to detect and report data loss, enhancing type safety.
- **Custom Float Formats**: `f8` may use a non-standard minifloat format, with casting rules to be defined.

osl’s type and casting system aims to balance performance with developer control, making it suitable for both low-level and high-level programming tasks.

### Explanation

1. **Types**:

   - Listed signed (`i8` to `i128`), unsigned (`u8` to `u128`), and floating-point (`f8` to `f128`) types with their ranges.
   - Added `c16` as a future type for UTF-16 chars.

2. **Implicit Conversions**:

   - Explained upcasting to the larger bit size during operations (e.g., `i8 + i32` → `i32`).
   - Detailed downcasting to the target variable’s size (e.g., `i32` result → `i8`).

3. **Runtime Warnings**:

   - Described planned warnings for overflow, precision loss, and signedness issues, with examples.

4. **Explicit Conversions**:

   - Introduced C-style casting (e.g., `i8(x)`) for explicit control, with examples.

5. **Example Workflow**:
   - Walked through a full example to illustrate upcasting, operation, downcasting, and potential warnings.

Casting is supported via VM opcodes (e.g., `I2F`, `F2I`), the high-level syntax is TBD. There is also a `null` value which may be assigned to any type, default for uninitialized variables.Undefined operations on `null` will be handled by the VM, by throwing runtime errors. There's also a `PUSH_NONE` opcode for pushing a `null` value onto the stack in the bytecode.
