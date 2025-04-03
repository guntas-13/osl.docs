---
icon: material/alert
---

# Pending Issues

## Python's Bytecode for Closures [1 April 2025]

Take a look at this code snippet:

```python
x = 5

def foo():
    x = 10
    def bar():
        x = 15
        def foobar():
            x = 20
            return x
        print(x)
        return foobar
    print(x)
    return bar

f = foo() # bar
g = f()   # foobar
print(g())
print(x)
```

There are nested closures. The output as usual would be:

```python
10
15
20
5
```

Likewise our osl code:

```python
var x := 5;

fn foo()
{
    x := 10;
    fn bar()
    {
        x := 15;
        fn foobar()
        {
            x := 20;
            return x;
        }
        log x;
        return foobar;
    }
    log x;
    return bar;
}

var f := foo();
var g := f();
log g();
x;
```

With the interpreter also producing the same output:

```python
10
15
20
5
```

Now let's see the bytecode for the `foo` function:

```python
import dis

f = foo() # bar
g = f()   # foobar
print(g())
print(x)

dis.dis(foo)
print("\n=============================\n")
dis.dis(f)
print("\n=============================\n")
dis.dis(g)
```

We get the output as:

```bash
  6           0 LOAD_CONST               1 (10)
              2 STORE_FAST               0 (x)

  7           4 LOAD_CONST               2 (<code object bar at 0x100a0e2f0, file "/Users/guntas13/Desktop/JetBrains Projects/CS327-Compilers/osl/a.py", line 7>)
              6 LOAD_CONST               3 ('foo.<locals>.bar')
              8 MAKE_FUNCTION            0
             10 STORE_FAST               1 (bar)

 14          12 LOAD_GLOBAL              0 (print)
             14 LOAD_FAST                0 (x)
             16 CALL_FUNCTION            1
             18 POP_TOP

 15          20 LOAD_FAST                1 (bar)
             22 RETURN_VALUE

Disassembly of <code object bar at 0x100a0e2f0, file "/Users/guntas13/Desktop/JetBrains Projects/CS327-Compilers/osl/a.py", line 7>:
  8           0 LOAD_CONST               1 (15)
              2 STORE_FAST               0 (x)

  9           4 LOAD_CONST               2 (<code object foobar at 0x100a0e870, file "/Users/guntas13/Desktop/JetBrains Projects/CS327-Compilers/osl/a.py", line 9>)
              6 LOAD_CONST               3 ('foo.<locals>.bar.<locals>.foobar')
              8 MAKE_FUNCTION            0
             10 STORE_FAST               1 (foobar)

 12          12 LOAD_GLOBAL              0 (print)
             14 LOAD_FAST                0 (x)
             16 CALL_FUNCTION            1
             18 POP_TOP

 13          20 LOAD_FAST                1 (foobar)
             22 RETURN_VALUE

Disassembly of <code object foobar at 0x100a0e870, file "/Users/guntas13/Desktop/JetBrains Projects/CS327-Compilers/osl/a.py", line 9>:
 10           0 LOAD_CONST               1 (20)
              2 STORE_FAST               0 (x)

 11           4 LOAD_FAST                0 (x)
              6 RETURN_VALUE

=============================

  8           0 LOAD_CONST               1 (15)
              2 STORE_FAST               0 (x)

  9           4 LOAD_CONST               2 (<code object foobar at 0x100a0e870, file "/Users/guntas13/Desktop/JetBrains Projects/CS327-Compilers/osl/a.py", line 9>)
              6 LOAD_CONST               3 ('foo.<locals>.bar.<locals>.foobar')
              8 MAKE_FUNCTION            0
             10 STORE_FAST               1 (foobar)

 12          12 LOAD_GLOBAL              0 (print)
             14 LOAD_FAST                0 (x)
             16 CALL_FUNCTION            1
             18 POP_TOP

 13          20 LOAD_FAST                1 (foobar)
             22 RETURN_VALUE

Disassembly of <code object foobar at 0x100a0e870, file "/Users/guntas13/Desktop/JetBrains Projects/CS327-Compilers/osl/a.py", line 9>:
 10           0 LOAD_CONST               1 (20)
              2 STORE_FAST               0 (x)

 11           4 LOAD_FAST                0 (x)
              6 RETURN_VALUE

=============================

 10           0 LOAD_CONST               1 (20)
              2 STORE_FAST               0 (x)

 11           4 LOAD_FAST                0 (x)
              6 RETURN_VALUE
```

This sparks motivation to modify the `codegen()` function as:

```python
def do_codegen(tree: AST, env: Environment = None):
    if env is None:
        env = Environment()

    def e_(tree: AST):
        return do_codegen(tree, env)

    code = bytearray()
    match tree:

        # MORE

        case LetFun(Variable(varName, i), params, body):

            # MAYBE SPIT OUT THE FOLLOWING HERE:
            # ARGS IDs
            # MAKE_FUNCTION/NEW_FUNCTION with FUNCTION ID
            # In the VM, we'll make the FunObj with an Environment

            funObj = FunObj(params, body, env.copy())
            global fun_code
            funObj.entry = len(fun_code)+3
            env.add(f"{varName}:{i}", funObj)

            # NOW HERE JUMP PAST
            # BUT WE NEED TO ATTACH THE ABOVE BODY AND BELOW BODY
            # BREAKING AT THE START OF DEFINITION OF A FUNCTION INSIDE THE CODE!
            fbody = do_codegen(body, env)
            fun_code.append(JUMP)
            fun_code.extend(int(len(fbody)).to_bytes(2, 'little'))
            fun_code.extend(fbody)
            return code

        # MORE
```

## FIXED [2 April 2025]!!

```python
full_code = bytearray()

def do_codegen(tree: AST, code: bytearray = None): # returns bytearray

    def e_(tree: AST):
        return do_codegen(tree)

    if code is None:
        code = bytearray()

    match tree:

        case LetFun(Variable(varName, i), params, body):
            code.append(PUSH_INT)
            code.extend(int(i).to_bytes(4, 'little'))
            code.append(MAKEF)

            new_code = bytearray()
            # add arguments to stack
            for param in params:
                new_code.append(PUSH_INT)
                new_code.extend(int(param.id).to_bytes(4, 'little'))
            # add number of arguments
            new_code.append(PUSH_INT)
            new_code.extend(int(len(params)).to_bytes(4, 'little'))
            # add function id
            new_code.append(PUSH_INT)
            new_code.extend(int(i).to_bytes(4, 'little'))
            new_code.append(NEWF)

            fbody = do_codegen(body)

            new_code.append(JUMP)
            new_code.extend(len(fbody).to_bytes(2, 'little'))
            global full_code
            new_code.extend(fbody)
            full_code.extend(new_code)
            return code

        case CallFun(Variable(varName, i), args):
            for arg in args:
                code.extend(e_(arg))

            code.append(PUSH_INT)
            code.extend(int(len(args)).to_bytes(4, 'little'))
            code.append(PUSH_INT)
            code.extend(int(i).to_bytes(4, 'little'))
            code.append(CALL)
            return code

def codegen(t):
    global full_code
    code = do_codegen(t)
    full_code.extend(code)
    full_code.append(HALT)
    return full_code
```

| Opcode (Hex)              | Mnemonic | Operands | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Function Instructions** |          |          |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| 0x91                      | NEWF     | None     | Introduced in the header of bytecode for each function declaration allowing the VM to create a Function Object with Function's ID and its Arguments' ID: Pops Function's ID, Pops the number of arguments, Pops the IDs of the arguments and make a Function Object. This instruction will be followed by a JUMP past its body, hence the entry point of the Function's Body can be infered here in the VM too: PC + 4 [JUMP B1 B2] |
| 0x92                      | MAKEF    | None     | Introduced in the code segement at the time of function declaration: Pops the Function ID, fetches the Function Object (marked from the previous NEWF), adds it into the current environment and assigns the Function Object's environment as this current environment (with its ID added - to support recursion)                                                                                                                   |

```python
var x := 5;

fn foo()
{
    x := 10;
    fn bar()
    {
        x := 15;
        fn foobar()
        {
            x := 20;
            return x;
        }
        log x;
        return foobar;
    }
    log x;
    return bar;
}

var f := foo();
var g := f();
log g();
log x;
```

Hence, for the above code, we get the bytecode as:

```bash
# HEADER STARTS

# fn foobar() -> id 4, 0 args
PUSH_INT 0
PUSH_INT 4
NEWF
JUMP 16         # JUMP past the body
PUSH_INT 20
STORE 1
LOAD 1
RETURN

# fn bar() -> id 3, 1 args
PUSH_INT 0
PUSH_INT 3
NEWF
JUMP 28         # JUMP past the body
PUSH_INT 15
STORE 1
PUSH_INT 4
MAKEF           # fn foobar()
LOAD 1
LOG             # log x
LOAD 4
RETURN          # return foobar

# fn foo() -> id 2, 0 args
PUSH_INT 0
PUSH_INT 2
NEWF
JUMP 28
PUSH_INT 10
STORE 1
PUSH_INT 3
MAKEF           # fn bar()
LOAD 1
LOG             # log x
LOAD 3
RETURN          # return bar

# CODE BODY STARTS HERE
PUSH_INT 5
STORE 1         # var x := 5

PUSH_INT 2
MAKEF           # fn foo() {}

PUSH_INT 0
PUSH_INT 2
CALL
STORE 5
PUSH_INT 0
PUSH_INT 5
CALL
STORE 6
PUSH_INT 0
PUSH_INT 6
CALL
LOG
LOAD 1
LOG
HALT
```

This is executed in the VM as:

```python
class StackVM:

    def execute(self):
        while self.pc < len(self.code.bytecode):
            op = self.code.bytecode[self.pc]

            # CODE

            elif op == Opcode.NEWF:
                fun_id = self.pop().val
                num_args = self.pop().val
                args_ids = []
                for _ in range(num_args):
                    args_ids.append(self.pop().val)

                newFunObj = FunObj(self.pc+4, args_ids, None)
                self.current_env().add(fun_id, newFunObj)
                self.pc += 1

            elif op == Opcode.MAKEF:
                fun_id = self.pop().val
                funObject = self.current_env().get(fun_id)

                self.current_env().add(fun_id, funObject)
                funObject.env = self.current_env().copy()
                self.pc += 1

            # CODE
```
