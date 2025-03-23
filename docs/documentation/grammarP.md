---
icon: material/clock
---

# Promised Unambiguous Grammar

```py
program → declaration* EOF

declaration → funDecl | varDecl | statement

funDecl → "letFunc" IDENTIFIER "(" parameters? ")" block
varDecl → "var" IDENTIFIER (":=" expression)? ";"
statement → ifStmt | printStmt | returnStmt | block | expressionStmt

ifStmt → "if" expression block ("else" block)? // block is mandatory to avoid ambiguity
printStmt → "print" "(" expression ")" ";"
returnStmt → "return" (expression)? ";"
block → "{" declaration* "}"
expressionStmt → expression ";"

expression → assignment | ExpB
assignment → IDENTIFIER ":=" ExpB

parameters → IDENTIFIER ("," IDENTIFIER)*

ExpB → UnAmbB | OrB | NotB | AndB | Less | Greater
UnAmbB → UnAmb | "(" ExpB ")"
OrB → UnAmbB | UnAmbB "|" OrB
NotB → UnAmbB | "~" NotB
AndB → UnAmbB | UnAmbB "&" AndB
Less → Exp ("<" | "<=" | "=") Exp | Exp "<" Less | Exp "<=" Less | Exp "=" Less
Greater → Exp (">" | ">=" | "=") Exp | Exp ">" Greater | Exp ">=" Greater | Exp "=" Greater

Exp → Add | Multiply | Divide | Subtract | Power | Modulo | And | Or | Not | Xor
    | Xand | Shift | UnaryNeg | UnAmb
Add → UnAmb | UnAmb "+" Add
Multiply → UnAmb | UnAmb "*" Multiply
Divide → UnAmb | Multiply "/" UnAmb
Subtract → UnAmb | Add "-" UnAmb
Power → UnAmb | UnAmb "^" UnAmb
Modulo → UnAmb | UnAmb "%" UnAmb
Or → UnAmb | UnAmb "|" Or
And → UnAmb | UnAmb "&" And
Xor → UnAmb | UnAmb "!|" Xor
Xand → UnAmb | UnAmb "!&" Xand
Shift → UnAmb (">>" | "<<") UnAmb
Not → UnAmb | "~" Not
UnaryNeg → ("-" | "+") UnaryNeg | UnAmb

UnAmb → BOOL | NUMBER | IDENTIFIER | funCall | "(" Exp ")"
funCall → IDENTIFIER "(" arguments? ")"
arguments → ExpB ("," ExpB)*

NUMBER → DIGIT+ ("." DIGIT*)? | "." DIGIT+
BOOL → "true" | "false"
DIGIT → "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"
IDENTIFIER → LETTER (LETTER | DIGIT | "_")*
LETTER → "a" .. "z" | "A" .. "Z"
```
