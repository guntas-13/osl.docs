---
icon: material/code-json
---

# Doc 1

```py
@dataclass
class Assign(AST):
    var: AST
    e1: AST

def parse(s: str) -> AST:
    t = peekable(lex(s))
    i = 0

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


def resolve(program: AST, env: Environment = None) -> AST:

    case Assign(Variable(varName, _), e1):
            re1 = resolve_(e1)
            return Assign(Variable(varName, env.get(varName)), re1)

def e(tree: AST, env: Environment = None) -> int | float | bool:

    case Assign(Variable(varName, i), e1):
            v1 = e_(e1)
            env.update(f"{varName}:{i}", v1)
            return None
```
