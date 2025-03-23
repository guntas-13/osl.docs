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

