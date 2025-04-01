---
icon: material/hexadecimal
---

# Bytecode Examples

## Example 1: Simple Function Call

```python
fn foo(n){
    if(n % 2 = 0)
    {
        return n - 1;
    }
    return n + 1;
}
print(foo(10));
```

```python
JUMP 44
LOAD 4
PUSH_INT 2
MOD
PUSH_INT 0
EQ
JUMP_IF_ZERO 12
LOAD 4
PUSH_INT 1
SUB
RETURN
LOAD 4
PUSH_INT 1
ADD
RETURN
PUSH_INT 4
PUSH_INT 10
PUSH_INT 1
PUSH_INT 3
CALL
LOG
HALT
```

## Example 2: Branching

```python
var x := 5;
if (x > 3)
{
    var y := x + 10;
    print(y);
}
else
{
    var z := x + 5;
    print(z);
}
```

```python
PUSH_INT 5
STORE 3
LOAD 3
PUSH_INT 3
GT
JUMP_IF_ZERO 25
LOAD 3
PUSH_INT 10
ADD
STORE 4
LOAD 4
LOG
JUMP 22
LOAD 3
PUSH_INT 5
ADD
STORE 5
LOAD 5
LOG
HALT
```

## Example 3: Manual Bytecode Function Call

```python
fn add(a, b)
{
    return a + b;
}
add(8, 5)
```

```python
PUSH_INT 5
STORE 3
LOAD 3
PUSH_INT 3
GT
JUMP_IF_ZERO 25
LOAD 3
PUSH_INT 10
ADD
STORE 4
LOAD 4
LOG
JUMP 22
LOAD 3
PUSH_INT 5
ADD
STORE 5
LOAD 5
LOG
HALT
```

```python
code = Code(
    bytecode=bytearray([
        Opcode.JUMP, 0x0C, 0x00,
        Opcode.LOAD, 0x02, 0x00,0x00,0x00,
        Opcode.LOAD, 0x03, 0x00,0x00,0x00,
        Opcode.ADD,
        Opcode.RETURN,
        Opcode.PUSH_INT, 0x02,0x00,0x00,0x00, # argument 1's id = 2
        Opcode.PUSH_INT, 0x08,0x00,0x00,0x00, # argument 1's value = 8
        Opcode.PUSH_INT, 0x03,0x00,0x00,0x00, # argument 2's id = 3
        Opcode.PUSH_INT, 0x05,0x00,0x00,0x00, # argument 2's value = 5
        Opcode.PUSH_INT, 0x02,0x00,0x00,0x00, # Number of arguments = 2
        Opcode.PUSH_INT, 0x03,0x00,0x00,0x00, # Function's entry = 3
        Opcode.CALL,
        Opcode.HALT
        ]),
    env=Environment()
)
```

## Example 4: Recursion

```python
fn fact(n)
{
    if (n = 1)
        return n;
    return n * fact(n - 1);
}
fact(5);
```

```python
JUMP 54
LOAD 2
PUSH_INT 1
EQ
JUMP_IF_ZERO 6
LOAD 2
RETURN
LOAD 2
PUSH_INT 2
LOAD 2
PUSH_INT 1
SUB
PUSH_INT 1
PUSH_INT 3
CALL
MUL
RETURN
PUSH_INT 2
PUSH_INT 5
PUSH_INT 1
PUSH_INT 3
CALL
HALT
```

```python
code = Code(
    bytecode=bytearray([
        Opcode.JUMP, 0x36, 0x00, # This is a jump of 16*3 = 48 bytes
        Opcode.LOAD, 0x02, 0x00, 0x00, 0x00,
        Opcode.PUSH_INT, 0x01, 0x00, 0x00, 0x00,
        Opcode.EQ,
        Opcode.JUMP_IF_ZERO, 0x06, 0x00,
        Opcode.LOAD, 0x02, 0x00,0x00,0x00,
        Opcode.RETURN,
        Opcode.LOAD, 0x02, 0x00,0x00,0x00,
        Opcode.PUSH_INT, 0x02, 0x00, 0x00, 0x00, # id = 2
        Opcode.LOAD, 0x02, 0x00,0x00,0x00,
        Opcode.PUSH_INT, 0x01, 0x00, 0x00, 0x00,
        Opcode.SUB,
        Opcode.PUSH_INT, 0x01, 0x00, 0x00, 0x00, # Number of args
        Opcode.PUSH_INT, 0x03, 0x00, 0x00, 0x00, # Function's entry
        Opcode.CALL,
        Opcode.MUL,
        Opcode.RETURN,
        Opcode.PUSH_INT, 0x02, 0x00, 0x00, 0x00, # id = 2
        Opcode.PUSH_INT, 0x05, 0x00, 0x00, 0x00, # val = 5
        Opcode.PUSH_INT, 0x01, 0x00, 0x00, 0x00, # Number of args
        Opcode.PUSH_INT, 0x03, 0x00, 0x00, 0x00, # Function's entry
        Opcode.CALL,
        Opcode.HALT
        ]),
    env=Environment()
)
```
