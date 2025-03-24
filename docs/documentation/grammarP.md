---
icon: material/clock
---

# Promised Unambiguous Grammar

```py
program → declaration* EOF;

declaration → funDecl | varDecl | statement;

funDecl → "fn" TYPE IDENTIFIER "(" parameters? ")" block;
varDecl → "var" TYPE IDENTIFIER (":=" expression)? ";";
statement → ifStmt | printStmt | returnStmt | block | expressionStmt;

ifStmt → "if" expression block ("else" block)?; // block is mandatory to avoid ambiguity
printStmt → "print" "(" expression ")" ";";
returnStmt → "return" (expression)? ";";
block → "{" declaration* "}";
expressionStmt → expression ";";

expression → assignment | ExpB;
assignment → IDENTIFIER ":=" ExpB;

parameters → IDENTIFIER ("," IDENTIFIER)*;

ExpB → UnAmbB | OrB | NotB | AndB | Less | Greater;
OrB → UnAmbB | UnAmbB "|" OrB;
NotB → UnAmbB | "~" NotB;
AndB → UnAmbB | UnAmbB "&" AndB;
Less → Exp ("<" | "<=" | "=") Exp | Exp "<" Less | Exp "<=" Less | Exp "=" Less;
Greater → Exp (">" | ">=" | "=") Exp | Exp ">" Greater | Exp ">=" Greater | Exp "=" Greater;
UnAmbB → BOOL | IDENTIFIER | funCall | "(" ExpB ")";

Exp → Add | Multiply | Divide | Subtract | Power | Modulo | And | Or | Not | Xor
    | Xand | Shift | UnaryNeg | UnAmb;
Add → UnAmb | UnAmb "+" Add;
Multiply → UnAmb | UnAmb "*" Multiply;
Divide → UnAmb | Multiply "/" UnAmb;
Subtract → UnAmb | Add "-" UnAmb;
Power → UnAmb | UnAmb "^" UnAmb;
Modulo → UnAmb | UnAmb "%" UnAmb;
Or → UnAmb | UnAmb "|" Or;
And → UnAmb | UnAmb "&" And;
Xor → UnAmb | UnAmb "!|" Xor;
Xand → UnAmb | UnAmb "!&" Xand;
Shift → UnAmb (">>" | "<<") UnAmb;
Not → UnAmb | "~" Not;
UnaryNeg → ("-" | "+") UnaryNeg | UnAmb;

UnAmb → NUMBER | IDENTIFIER | funCall | "(" Exp ")";
funCall → IDENTIFIER "(" arguments? ")";
arguments → ExpB ("," ExpB)*;

NUMBER → (bINTEGER bFRACTION bEXPONENT)
    | (oINTEGER oFRACTION oEXPONENT)
    | (dINTEGER dFRACTION dEXPONENT)
    | (hINTEGER hFRACTION hEXPONENT);
hEXPONENT → ""
    | "p" HEXES;
bEXPONENT → ""
    | "e" BITS;
oEXPONENT → ""
    | "e" OCTS;
dEXPONENT → ""
    | "e" DIGITS;
bfraction → ""
    | "." BITS;
ofraction → ""
    | "." OCTS;
dfraction → ""
    | "." DIGITS;
hfraction → ""
    | "." HEXES;
bINTEGER → "0b" BITS
    | "0B" BITS;
oINTEGER → "0o" OCTS
    | "0O" OCTS;
dINTEGER → DIGITS;
hINTEGER → "0x" HEXES
    | "0X" HEXES;
BIT → "0" | "1";
BITS → BIT?;
OCT → "0".."7";
OCTS → OCT?;
DIGIT → "0".."9";
DIGITS → DIGIT?;
HEX → "0".."9" | "a".."f" | "A".."F";
HEXES → HEX?;
BOOL → "true" | "false";
IDENTIFIER → LETTER (LETTER | DIGIT | "_")*;
LETTER → "a" .. "z" | "A" .. "Z";
TYPE → "i8" | "i16" | "i32" | "i64" | "i128" | "u8" | "u16" | "u32" | "u64" | "u128" | "bool";
```
