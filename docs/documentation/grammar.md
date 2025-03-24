---
icon: fontawesome/solid/spell-check
---

# Overall Grammar

```py
program → declaration* EOF;

declaration → funDecl | varDecl | statement;

funDecl → "fn" IDENTIFIER "(" parameters? ")" block;
varDecl → "var" IDENTIFIER (":=" expression)? ";";
statement → ifStmt | printStmt | returnStmt | block | expressionStmt;

ifStmt → "if" expression statement ("else" statement)?;
printStmt → "print" "(" expression ")" ";";
returnStmt → "return" (expression)? ";";
block → "{" declaration* "}";

expressionStmt → expression ";";
expression → assignment | expB;
assignment → IDENTIFIER ":=" expB;

parameters → IDENTIFIER ("," IDENTIFIER)*;

expB → logicOr;
logicOr → logicAnd ("||" logicAnd)*;
logicAnd → comparison ("&&" comparison)*;
comparison → add (("<" | ">" | "<=" | ">=" | "=" | "!=") add)*;
add → mul (("+" | "-") mul)*;
mul → exp (("*" | "/" | "%") exp)*;
exp → unary ("^" unary)*;
unary → ("-" | "√") unary | atom;

atom → NUMBER | IDENTIFIER | funCall | "(" expB ")";
funCall → IDENTIFIER "(" arguments? ")";
arguments → expB ("," expB)*;

NUMBER → DIGIT+ ("." DIGIT*)? | "." DIGIT+;
DIGIT → "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9";
IDENTIFIER → LETTER (LETTER | DIGIT | "_")*;
LETTER → "a" .. "z" | "A" .. "Z";
```
