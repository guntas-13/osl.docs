---
icon: material/code-greater-than
---

# Opcode Table for the Stack VM

Below is a table describing the opcodes, their operands, and their functions in the Stack VM, based on the implemented codegen and VM code. Only opcodes with codegen support are included.

| Opcode (Hex)                   | Mnemonic        | Operands                       | Description                                                                                                     |
| ------------------------------ | --------------- | ------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| **Data Push Instructions**     |                 |                                |                                                                                                                 |
| 0x01                           | PUSH_CHAR       | 1 byte                         | Push a literal 1-byte character onto the stack.                                                                 |
| 0x03                           | PUSH_INT        | 8 bytes                        | Push a literal 8-byte integer (little-endian) onto the stack.                                                   |
| 0x05                           | PUSH_FLOAT      | 4 bytes                        | Push a literal 4-byte IEEE 754 float (little-endian) onto the stack.                                            |
| 0x07                           | PUSH_NONE       | None                           | Push a value of 0 (VAL_INT type) onto the stack.                                                                |
| **Stack Manipulation**         |                 |                                |                                                                                                                 |
| 0x10                           | POP             | None                           | Pop the top value off the stack.                                                                                |
| 0x11                           | DUP             | None                           | Duplicate the top value on the stack.                                                                           |
| 0x12                           | SWAP            | None                           | Swap the top two values on the stack.                                                                           |
| 0x13                           | OVER            | None                           | Copy the second value and push it onto the stack.                                                               |
| **Identifier Manipulation**    |                 |                                |                                                                                                                 |
| 0x15                           | GET             | 8 bytes (ID)                   | Push the value corresponding to the variable ID onto the stack.                                                 |
| 0x16                           | SET             | 8 bytes (ID)                   | Pop a value from the stack and assign it to the variable ID.                                                    |
| 0x98                           | BIND            | 8 bytes (ID)                   | Bind a popped value to a variable ID in the current scope, creating a new scope entry if needed.                |
| **Arithmetic Operations**      |                 |                                |                                                                                                                 |
| 0x20                           | ADD             | None                           | Pop two values, add them, and push the result. Supports INT, FLOAT, and CHAR types.                             |
| 0x21                           | SUB             | None                           | Pop two values, subtract the top from the next, and push the result. Supports INT, FLOAT, and CHAR types.       |
| 0x22                           | MUL             | None                           | Pop two values, multiply them, and push the result. Supports INT, FLOAT, and CHAR types.                        |
| 0x23                           | DIV             | None                           | Pop two values, divide the second by the top, and push the result. Supports INT and FLOAT types.                |
| 0x24                           | MOD             | None                           | Pop two values, compute the modulus, and push the result. Supports INT types.                                   |
| 0x25                           | NEG             | None                           | Negate the top value and push the result. Supports INT and FLOAT types.                                         |
| **Logical Operations**         |                 |                                |                                                                                                                 |
| 0x34                           | AND             | None                           | Pop two INT values, perform logical AND, push 1 if true, else 0.                                                |
| 0x35                           | OR              | None                           | Pop two INT values, perform logical OR, push 1 if true, else 0.                                                 |
| 0x36                           | NOT             | None                           | Pop an INT value, push 1 if it is 0, else push 0.                                                               |
| **Comparison Operations**      |                 |                                |                                                                                                                 |
| 0x40                           | EQ              | None                           | Pop two values; push 1 if equal, else 0. Supports INT, FLOAT, and CHAR types.                                   |
| 0x41                           | NEQ             | None                           | Pop two values; push 1 if not equal, else 0. Supports INT, FLOAT, and CHAR types.                               |
| 0x42                           | LT              | None                           | Pop two values; push 1 if the second is less than the top, else 0. Supports INT and FLOAT types.                |
| 0x43                           | GT              | None                           | Pop two values; push 1 if the second is greater than the top, else 0. Supports INT and FLOAT types.             |
| 0x44                           | LE              | None                           | Pop two values; push 1 if the second is less than or equal to the top, else 0. Supports INT and FLOAT types.    |
| 0x45                           | GE              | None                           | Pop two values; push 1 if the second is greater than or equal to the top, else 0. Supports INT and FLOAT types. |
| **Control Flow**               |                 |                                |                                                                                                                 |
| 0x50                           | JUMP            | 4 bytes (signed offset)        | Unconditionally jump by the relative offset (added to PC).                                                      |
| 0x51                           | JUMP_IF_ZERO    | 4 bytes (signed offset)        | Pop an INT; if zero, jump by the relative offset.                                                               |
| 0x53                           | CALL            | None                           | Pop a function object, increment call scope, set up closure objects, and jump to the function's entry point.    |
| 0x54                           | RETURN          | None                           | Pop a return value and address, clean up scope, and jump to the return address.                                 |
| 0x55                           | HALT            | None                           | Terminate program execution.                                                                                    |
| **Type Conversion Operations** |                 |                                |                                                                                                                 |
| 0x60                           | I2F             | None                           | Convert the top INT value to a FLOAT.                                                                           |
| 0x61                           | F2I             | None                           | Convert the top FLOAT value to an INT.                                                                          |
| **Heap Object Operations**     |                 |                                |                                                                                                                 |
| 0x70                           | NEW_OBJECT      | 1 byte (field count)           | Allocate a new object with the specified number of fields; push the object pointer onto the stack.              |
| 0x71                           | GET_FIELD       | 1 byte (field index)           | Pop an object and push the value from the specified field of the object.                                        |
| 0x72                           | SET_FIELD       | 1 byte (field index)           | Pop a value and then an object; set the object's specified field to the popped value.                           |
| **I/O Operations**             |                 |                                |                                                                                                                 |
| 0x90                           | LOG             | None                           | Pop a value and print it to STDOUT. Supports INT, FLOAT, and CHAR types.                                        |
| **Function Instructions**      |                 |                                |                                                                                                                 |
| 0x91                           | MAKE_FUNC       | None                           | Pop entry and exit points, create a function object, and push it onto the stack.                                |
| 0x92                           | MAKE_CLOSURE    | None                           | Pop number of closed objects, their IDs, and a function object; create a closure and attach it to the function. |
| **Array Instructions**         |                 |                                |                                                                                                                 |
| 0x93                           | MAKE_ARRAY      | 2 bytes (size)                 | Pop the specified number of elements, create an array, and push it onto the stack.                              |
| 0x94                           | ARRACC          | None                           | Pop an array and index, push the value at the specified index.                                                  |
| 0x95                           | STORE           | None                           | Pop a location and value, store the value at the location. Supports INT, FLOAT, CHAR, BOOL, and ARR types.      |
| 0x96                           | LOAD            | None                           | Pop a location, push the value stored at that location.                                                         |
| 0x97                           | MAKE_ARRAY_DECL | 2 bytes (number of dimensions) | Pop dimension sizes, create a multi-dimensional array initialized to 0, and push it onto the stack.             |
