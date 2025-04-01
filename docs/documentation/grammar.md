---
icon: fontawesome/solid/spell-check
---

# Current Grammar

## Program Structure

- `program` → `declaration* EOF`
- `declaration` → `funDecl` | `varDecl` | `statement`
- `block` → `"{" declaration* "}"`

## Function Declarations and Calls

- `funDecl` → `"fn" IDENTIFIER "(" parameters? ")" block`
- `parameters` → `IDENTIFIER ("," IDENTIFIER)*`
- `funCall` → `IDENTIFIER "(" arguments? ")"`
- `arguments` → `expB ("," expB)*`

## Variable Declarations

- `varDecl` → `"var" IDENTIFIER (":=" expression)? ";"`

## Statements

- `statement` → `ifStmt` | `printStmt` | `returnStmt` | `block` | `expressionStmt`
- `ifStmt` → `"if" expression statement ("else" statement)?`
- `printStmt` → `"print" "(" expression ")" ";"`
- `returnStmt` → `"return" (expression)? ";"`
- `expressionStmt` → `expression ";"`

## Expressions

- `expression` → `assignment` | `expB`
- `assignment` → `IDENTIFIER ":=" expB`
- `expB` → `logicOr`
- `logicOr` → `logicAnd ("||" logicAnd)*`
- `logicAnd` → `comparison ("&&" comparison)*`
- `comparison` → `add (("<" | ">" | "<=" | ">=" | "=" | "!=") add)*`
- `add` → `mul (("+" | "-") mul)*`
- `mul` → `exp (("*" | "/" | "%") exp)*`
- `exp` → `unary ("^" unary)*`
- `unary` → `("-" | "√") unary` | `atom`
- `atom` → `NUMBER` | `IDENTIFIER` | `funCall` | `"(" expB ")"`

## Literals and Tokens

- `NUMBER` → `DIGIT+ ("." DIGIT*)?` | `"." DIGIT+`
- `DIGIT` → `"0"` | `"1"` | `"2"` | `"3"` | `"4"` | `"5"` | `"6"` | `"7"` | `"8"` | `"9"`
- `IDENTIFIER` → `LETTER (LETTER | DIGIT | "_")*`
- `LETTER` → `"a".."z"` | `"A".."Z"`

## Notes on Recursive Descent

- **Left Recursion**: Productions like `logicOr`, `logicAnd`, `comparison`, `add`, `mul`, and `exp` are left-recursive and can be handled iteratively in a parser to avoid infinite recursion.
- **Right Recursion**: `unary` has a right-recursive form with operators like `"-"` or `"√"`, fitting naturally into recursive descent.
- **Operator Precedence**: The grammar enforces precedence from highest (`unary`) to lowest (`logicOr`), ensuring correct expression evaluation.
- **Optional Elements**: Constructs like `parameters?`, `expression?`, and `"else" statement?` indicate optional components, parsed with conditional checks.

# OSL Grammar (Recursive Descent Style)

Below is the OSL grammar presented in a recursive descent style using arrow notation (`→`). This format avoids Markdown table complexities and focuses on clarity for parsing and implementation.

## Program Structure

- `Prog` → `Decl* EOF`
- `Decl` → `FunDecl` | `FunDeclB` | `VarDecl` | `Stmt`
- `Block` → `"{" Decl* "}"`

## Function Declarations and Calls

- `FunDecl` → `"fn" Type FunIden "(" Params? ")" Block`
- `FunDeclB` → `"fn" Type FunIdenB "(" Params? ")" Block`
- `FunCall` → `FunIden "(" Params? ")"`
- `FunCallB` → `FunIdenB "(" Params? ")"`
- `Params` → `Param ("," Param)*`
- `Param` → `Type (Iden | IdenB)`

## Statements

- `Stmt` → `IfStmt` | `LogStmt` | `RetStmt` | `Block` | `ExpStmt`
- `IfStmt` → `"if" ValB Block ("elif" ValB Block)* ("else" Block)?`
- `LogStmt` → `"log" (Val | ValB) ";"`
- `RetStmt` → `"return" (Val | ValB) ";"`
- `ExpStmt` → `(Val | ValB) ";"`
- `VarDecl` → `"var" Type (IdenB | Iden | Assn | AssnB) ";"`

## Expressions and Values

### Value Variants

- `Val` → `Assn` | `Exp` | `Null`
- `ValB` → `AssnB` | `ExpB` | `Null`
- `Assn` → `Iden ":=" (Exp | Null)` | `Iden ":=" Assn`
- `AssnB` → `IdenB ":=" (ExpB | Null)` | `IdenB ":=" AssnB`

### Expressions (Typed: `ExpB`)

- `ExpB` → `UnAmbB` | `OrB` | `NotB` | `AndB` | `Less` | `Greater` | `Eq` | `NotEq`
- `UnAmbB` → `Bool` | `"(" ExpB ")"` | `IdenB` | `FunCallB`
- `OrB` → `UnAmbB` | `UnAmbB "||" OrB`
- `AndB` → `UnAmbB` | `UnAmbB "&&" AndB`
- `NotB` → `UnAmbB` | `"~" NotB`
- `Eq` → `UnAmbB` | `Val "=" Val` | `ValB "=" ValB`
- `NotEq` → `UnAmbB` | `Val "~=" Val` | `ValB "~=" ValB`
- `Less` → `Val "<" Val` | `Val "<=" Val` | `Val "=" Val` | `Val "<" Less` | `Val "<=" Less` | `Val "=" Less`
- `Greater` → `Val ">" Val` | `Val ">=" Val` | `Val "=" Val` | `Val ">" Greater` | `Val ">=" Greater` | `Val "=" Greater`

### Expressions (Untyped: `Exp`)

- `Exp` → `Add` | `Multiply` | `Divide` | `Subtract` | `Power` | `Modulo` | `And` | `Or` | `Not` | `Xor` | `Xand` | `Shift` | `UnaryNeg` | `UnAmb`
- `UnAmb` → `Number` | `"(" Exp ")"` | `Iden` | `FunCall`
- `Add` → `UnAmb` | `UnAmb "+" Add`
- `Multiply` → `UnAmb` | `UnAmb "*" Multiply`
- `Divide` → `UnAmb` | `Multiply "/" UnAmb`
- `Subtract` → `UnAmb` | `Add "-" UnAmb`
- `Power` → `UnAmb` | `UnAmb "^" UnAmb`
- `Modulo` → `UnAmb` | `UnAmb "%" UnAmb`
- `Or` → `UnAmb` | `UnAmb "|" Or`
- `And` → `UnAmb` | `UnAmb "&" And`
- `Xor` → `UnAmb` | `UnAmb "!|" Xor`
- `Xand` → `UnAmb` | `UnAmb "!&" Xand`
- `Shift` → `UnAmb ">>" UnAmb` | `UnAmb "<<" UnAmb`
- `Not` → `UnAmb` | `"~" Not`
- `UnaryNeg` → `UnAmb` | `"-" UnaryNeg` | `"+" UnaryNeg`

## Types and Literals

- `Type` → `"bool"` | `"i8"` | `"i16"` | `"i32"` | `"i64"` | `"i128"` | `"u8"` | `"u16"` | `"u32"` | `"u64"` | `"u128"` | `"f8"` | `"f16"` | `"f32"` | `"f64"` | `"f128"` | `"c8"`
- `Number` → `(bINTEGER bFRACTION bEXPONENT)` | `(oINTEGER oFRACTION oEXPONENT)` | `(dINTEGER dFRACTION dEXPONENT)` | `(hINTEGER hFRACTION hEXPONENT)`

### Numeric Literal Components

- `bINTEGER` → `"0b" BITS` | `"0B" BITS`
- `oINTEGER` → `"0o" OCTS` | `"0O" OCTS`
- `dINTEGER` → `DIGITS`
- `hINTEGER` → `"0x" HEXES` | `"0X" HEXES`
- `bFRACTION` → `""` | `"." BITS`
- `oFRACTION` → `""` | `"." OCTS`
- `dFRACTION` → `""` | `"." DIGITS`
- `hFRACTION` → `""` | `"." HEXES`
- `bEXPONENT` → `""` | `"e" BITS`
- `oEXPONENT` → `""` | `"e" OCTS`
- `dEXPONENT` → `""` | `"e" DIGITS`
- `hEXPONENT` → `""` | `"p" HEXES`

### Token Definitions

- `BIT` → `"0"` | `"1"`
- `BITS` → `BIT?`
- `OCT` → `"0".."7"`
- `OCTS` → `OCT?`
- `DIGIT` → `"0".."9"`
- `DIGITS` → `DIGIT?`
- `HEX` → `"0".."9"` | `"a".."f"` | `"A".."F"`
- `HEXES` → `HEX?`
- `Bool` → `"true"` | `"false"`
- `Iden` → `LETTER (LETTER | DIGIT | "_")*`
- `IdenB` → `LETTER (LETTER | DIGIT | "_")*`
- `LETTER` → `"a".."z"` | `"A".."Z"`

## Notes on Recursive Descent

- **Left Recursion**: Productions like `Add`, `Multiply`, `OrB`, etc., are left-recursive and can be parsed iteratively to avoid infinite recursion.
- **Right Recursion**: `Not`, `UnaryNeg`, etc., are right-recursive, fitting naturally into recursive descent parsing.
- **Ambiguity**: The grammar separates typed (`B` suffix) and untyped constructs to minimize ambiguity, though overlaps (e.g., `Val` vs. `ValB`) may need precedence rules.
- **Null**: Represents an uninitialized or void value, applicable in assignments and returns.

# Promised Unambiguous Grammar

```py
program → declaration* EOF;

declaration → funDecl | funcDeclB | varDecl | statement;

funDeclB → "fn" TYPE FUNIDENB "(" parameters? ")" block;
funDecl → "fn" TYPE FUNIDEN "(" parameters? ")" block;
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
