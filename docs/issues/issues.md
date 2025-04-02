---
icon: material/alert
---

# Pending Issues

## Python's Bytecode for Closures

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
