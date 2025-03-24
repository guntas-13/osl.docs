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

# Scope & Closure Support

## The `Environment` class

The `Environment` class is used to manage variable scopes in the interpreter. It provides methods to add, get, and update variables within the current scope.

```py
class Environment:
    envs: List

    def __init__(self):
        self.envs = [{}]

    def enter_scope(self):
        self.envs.append({})

    def exit_scope(self):
        assert self.envs
        self.envs.pop()

    def add(self, var, val):
        assert var not in self.envs[-1], f"Variable {var} already defined"
        self.envs[-1][var] = val

    def get(self, var):
        for env in reversed(self.envs):
            if var in env:
                return env[var]
        raise ValueError(f"Variable {var} not defined")

    def update(self, var, val):
        for env in reversed(self.envs):
            if var in env:
                env[var] = val
                return
        raise ValueError(f"Variable {var} not defined")

    def copy(self):
        new_env = Environment()
        new_env.envs = [dict(scope) for scope in self.envs]
        return new_env
```

## Closure Support

```py
def e(tree: AST, env: Environment = None) -> int | float | bool:

    match tree:
        case LetFun(Variable(varName, i), params, body):
            # Closure -> Copy of Environment taken along with the declaration!
            funObj = FunObj(params, body, None)
            env.add(f"{varName}:{i}", funObj)
            funObj.env = env.copy()
            return None

        case CallFun(Variable(varName, i), args):
            fun = env.get(f"{varName}:{i}")
            rargs = [e_(arg) for arg in args]

            # use the environment that was copied when the function was defined
            call_env = fun.env.copy()
            call_env.enter_scope()
            for param, arg in zip(fun.params, rargs):
                call_env.add(f"{param.varName}:{param.id}", arg)

            rbody = e(fun.body, call_env)
            return rbody
```

# Implementation of the Top-Level Grammar

## Program as a Sequence of Declarations

```py
program → declaration* EOF;
declaration → funDecl | varDecl | statement;
```

```py
def parse(s: str) -> AST:

    def parse_program():
        decls = []
        while peek():
            decls.append(parse_declaration())
        return Program(decls)

    def parse_declaration():
        match peek():
            case KeyWordToken("fn"):
                return parse_func()
            case KeyWordToken("var"):
                return parse_let()
            case _:
                return parse_statement()
```

## Assignment

```py
expression → assignment | expB;
```

```py
def parse(s: str) -> AST:

    def parse_expression():
        # expression -> expB | assignment
        # first parse the lhs, if it's a variable and next token is ':=' then it's an assignment
        # otherwise it's an expB so return it as is.
        ast = parse_bool()
        if not isinstance(ast, Variable) and peek() == OperatorToken(":="):
            raise ParseErr(f"Expected variable on the left side of assignment := operator at index {i}")
        if isinstance(ast, Variable) and peek() == OperatorToken(":="):
            consume(OperatorToken, ":=")
            e1 = parse_bool()
            return Assign(ast, e1)
        return ast
```

## Unmatched If

```py
ifStmt → "if" expression statement ("else" statement)?;
```

```py
def parse(s: str) -> AST:

    def parse_if():
        consume(KeyWordToken, "if")
        condition = parse_expression()
        then_body = parse_statement()
        if peek() == KeyWordToken("else"):
            consume(KeyWordToken, "else")
            else_body = parse_statement()
            return If(condition, then_body, else_body)
        return IfUnM(condition, then_body)
```
