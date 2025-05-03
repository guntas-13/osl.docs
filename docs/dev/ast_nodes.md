---
icon: material/code-json
---

# Abstract Binding Tree (ABT) Nodes

The Abstract Binding Tree (ABT) consists of various nodes that represent different constructs in the language. Below is a list of the primary node types along with their descriptions.

## AST Base Class

```python
@dataclass
class AST:
    pass
```

## Expression Nodes

### Binary Operation

```python
@dataclass
class BinOp(AST):
    op: str
    left: AST
    right: AST
```

Represents a binary operation with an operator (`op`) and two operands (`left` and `right`).

### Number Literal

```python
@dataclass
class Number(AST):
    val: int | float
```

Represents a numeric literal.

### Unary Operation

```python
@dataclass
class UnOp(AST):
    op: str
    right: AST
```

Represents a unary operation with an operator (`op`) and a single operand (`right`).

## Control Flow Nodes

### If Statement

```python
@dataclass
class If(AST):
    condition: AST
    then_body: AST
    else_body: AST
```

Represents an `if` statement with a condition, a `then` block, and an `else` block.

### If Without Else (Unmatched If)

```python
@dataclass
class IfUnM(AST):
    condition: AST
    then_body: AST
```

Represents an `if` statement without an `else` block.

## Variable and Assignment Nodes

### Let Binding

```python
@dataclass
class Let(AST):
    var: AST
    e1: Optional[AST]
```

Represents a `let` binding where `var` is assigned an optional expression `e1`.

### Assignment

```python
@dataclass
class Assign(AST):
    var: AST
    e1: AST
```

Represents an assignment of `e1` to `var`.

### Variable Reference

```python
@dataclass
class Variable(AST):
    varName: str
    id: int = None
```

Represents a variable reference with a name (`varName`) and an optional unique identifier (`id`).

## Function Nodes

### Function Definition

```python
@dataclass
class LetFun(AST):
    name: AST   # considering functions as first-class just like variables else it'll be str
    params: List[AST]
    body: AST
```

Represents a function definition with a name, a list of parameters, and a body.

### Function Call

```python
@dataclass
class CallFun(AST):
    fn: AST     # considering functions as first-class just like variables else it'll be str
    args: List[AST]
```

Represents a function call with a function (`fn`) and a list of arguments.

### Function Object

```python
@dataclass
class FunObj:
    params: List[AST]
    body: AST
    env: Environment
```

Represents a function object, including parameters, a body, and an environment for closures.

```py
def foo(y)
{
    var x := 3;
    def bar(z)
    {
        return x + y + z;
    }
    return bar;
}
log foo(1)(5);
```

```py
Program(decls=[LetFun(name=Variable(varName='foo', id=1),
                      params=[Variable(varName='y', id=2)],
                      body=Statements(stmts=[Let(var=Variable(varName='x',
                                                              id=3),
                                                 e1=Number(val=3)),
                                             LetFun(name=Variable(varName='bar',
                                                                  id=4),
                                                    params=[Variable(varName='z',
                                                                     id=5)],
                                                    body=Statements(stmts=[ReturnStmt(expr=BinOp(op='+',
                                                                                                 left=BinOp(op='+',
                                                                                                            left=Variable(varName='x',
                                                                                                                          id=3),
                                                                                                            right=Variable(varName='y',
                                                                                                                           id=2)),
                                                                                                 right=Variable(varName='z',
                                                                                                                id=5)))])),
                                             ReturnStmt(expr=Variable(varName='bar',
                                                                      id=4))])),
               PrintStmt(expr=CallFun(fn=CallFun(fn=Variable(varName='foo',
                                                             id=1),
                                                 args=[Number(val=1)]),
                                      args=[Number(val=5)]))])
```

## Array nodes

### Array Defining

```python
@dataclass
class Arr(AST):
    arr: List[AST]
    size: int
```

Represents an array definition with a list of elements (`arr`) and a size (`size`).

### Array Declaration

```python
@dataclass
class ArrDecl(AST):
    arr: AST
```

Represents an array declaration.

### Array Access

```python
@dataclass
class ArrAccess(AST):
    arr: AST
    index: AST
```

Represents an array access with an array (`arr`) and an index (`index`).

### Array Assignment

```python
@dataclass
class AssignArr(AST):
    ArrAccessNode: AST
    e1: AST
```

```py
var arr := [1, [2, 3, [4, 5]], 6, 7];
```

```py
Program(decls=[Let(var=Variable(varName='arr', id=1),
                   e1=Arr(arr=[Number(val=1),
                               Arr(arr=[Number(val=2),
                                        Number(val=3),
                                        Arr(arr=[Number(val=4), Number(val=5)],
                                            size=2)],
                                   size=3),
                               Number(val=6),
                               Number(val=7)],
                          size=4))])
```

```py
var x := arr[1][0] + arr[1][2][1];
```

```py
Program(decls=[Let(var=Variable(varName='x', id=2),
                   e1=BinOp(op='+',
                            left=ArrAccess(arr=ArrAccess(arr=Variable(varName='arr',
                            id=1),
                                                        index=Number(val=1)),
                                            index=Number(val=0)),
                            right=ArrAccess(arr=ArrAccess(arr=ArrAccess(arr=Variable(varName='arr', id=1),
                                                        index=Number(val=1)),
                                                          index=Number(val=2)),
                                            index=Number(val=1))))])
```

```py
arr[1][2][x - 7] := 10;
```

```py
Program(decls=[AssignArr(ArrAccessNode=ArrAccess(arr=ArrAccess(arr=ArrAccess(arr=Variable(varName='arr',
                                                                                          id=1),
                                                                             index=Number(val=1)),
                                                               index=Number(val=2)),
                                                 index=BinOp(op='-',
                                                             left=Variable(varName='x',
                                                                           id=2),
                                                             right=Number(val=7))),
                         e1=Number(val=10))])

```

## Statement Nodes

### Sequence of Statements

```python
@dataclass
class Statements(AST):
    stmts: List[AST]
```

Represents a sequence of statements.

### Print Statement

```python
@dataclass
class PrintStmt(AST):
    expr: AST
```

Represents a print statement for evaluating and displaying an expression.

### Return Statement

```python
@dataclass
class ReturnStmt(AST):
    expr: Optional[AST]
```

Represents a return statement, optionally returning an expression.

## Program Node

### Program

```python
@dataclass
class Program(AST):
    decls: List[AST]
```

Represents the root node of the program, containing a list of declarations.
