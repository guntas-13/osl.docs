---
icon: fontawesome/solid/spell-check
---

# Opcode Table for the Stack VM

Below is a table describing the opcodes, their operands, and their functions in the VM:

| Opcode (Hex) | Mnemonic          | Operands                    | Description |
|--------------|-------------------|-----------------------------|-------------|
| **Data Push Instructions** |  |  |  |
| 0x01         | PUSH_CHAR         | 1 byte                      | Push a literal 1‑byte character onto the stack. |
| 0x02         | PUSH_SHORT        | 2 bytes                     | Push a literal 2‑byte short (little‑endian) onto the stack. |
| 0x03         | PUSH_INT          | 4 bytes                     | Push a literal 4‑byte integer (little‑endian) onto the stack. |
| 0x04         | PUSH_LONG         | 8 bytes                     | Push a literal 8‑byte long (little‑endian) onto the stack. |
| 0x05         | PUSH_FLOAT        | 4 bytes                     | Push a literal 4‑byte IEEE 754 float (little‑endian) onto the stack. |
| 0x06         | PUSH_DOUBLE       | 8 bytes                     | Push a literal 8‑byte IEEE 754 double (little‑endian) onto the stack. |
| **Stack Manipulation** |  |  |  |
| 0x10         | POP               | None                        | Pop the top value off the stack. |
| 0x11         | DUP               | None                        | Duplicate the top value on the stack. |
| 0x12         | SWAP              | None                        | Swap the top two values on the stack. |
| 0x13         | OVER              | None                        | Copy the second value and push it onto the stack. |
| **Arithmetic Operations** |  |  |  |
| 0x20         | ADD               | None                        | Pop two values, add them, and push the result. |
| 0x21         | SUB               | None                        | Pop two values, subtract the top from the next, and push the result. |
| 0x22         | MUL               | None                        | Pop two values, multiply them, and push the result. |
| 0x23         | DIV               | None                        | Pop two values, divide the second by the top, and push the result. |
| 0x24         | MOD               | None                        | Pop two values, compute the modulus, and push the result. |
| 0x25         | NEG               | None                        | Negate the top value and push the result. |
| **Bitwise/Logical Operations** |  |  |  |
| 0x30         | BITWISE_NOT       | None                        | Pop a value, perform bitwise NOT, and push the result. |
| 0x31         | BITWISE_AND       | None                        | Pop two values, perform bitwise AND, and push the result. |
| 0x32         | BITWISE_OR        | None                        | Pop two values, perform bitwise OR, and push the result. |
| 0x33         | BITWISE_XOR       | None                        | Pop two values, perform bitwise XOR, and push the result. |
| **Comparison Operations** |  |  |  |
| 0x40         | EQ                | None                        | Pop two values; push 1 if equal, else 0. |
| 0x41         | NEQ               | None                        | Pop two values; push 1 if not equal, else 0. |
| 0x42         | LT                | None                        | Pop two values; push 1 if the second is less than the top, else 0. |
| 0x43         | GT                | None                        | Pop two values; push 1 if the second is greater than the top, else 0. |
| 0x44         | LE                | None                        | Pop two values; push 1 if the second is less than or equal to the top, else 0. |
| 0x45         | GE                | None                        | Pop two values; push 1 if the second is greater than or equal to the top, else 0. |
| **Control Flow** |  |  |  |
| 0x50         | JUMP              | 2 bytes (signed offset)     | Unconditionally jump by the relative offset (added to PC). |
| 0x51         | JUMP_IF_ZERO      | 2 bytes (signed offset)     | Pop an INT; if zero, jump by the relative offset. |
| 0x52         | JUMP_IF_NONZERO   | 2 bytes (signed offset)     | Pop an INT; if nonzero, jump by the relative offset. |
| 0x53         | CALL              | 2 bytes (address)           | Push the return address and jump to the subroutine at the specified address. |
| 0x54         | RETURN            | None                        | Pop the return address from the stack and jump back to it. |
| 0x55         | HALT              | None                        | Terminate program execution. |
| **Type Conversion Operations** |  |  |  |
| 0x60         | I2F               | None                        | Convert the top INT value to a FLOAT. |
| 0x61         | F2I               | None                        | Convert the top FLOAT value to an INT. |
| 0x62         | I2D               | None                        | Convert the top INT value to a DOUBLE. |
| 0x63         | D2I               | None                        | Convert the top DOUBLE value to an INT. |
| 0x64         | F2D               | None                        | Convert the top FLOAT value to a DOUBLE. |
| 0x65         | D2F               | None                        | Convert the top DOUBLE value to a FLOAT. |
| **Heap Object Operations** |  |  |  |
| 0x70         | NEW_OBJECT        | 1 byte (field count)        | Allocate a new object with the specified number of fields; push the object pointer onto the stack. |
| 0x71         | GET_FIELD         | 1 byte (field index)        | Pop an object and push the value from the specified field of the object. |
| 0x72         | SET_FIELD         | 1 byte (field index)        | Pop a value and then an object; set the object's specified field to the popped value. |
