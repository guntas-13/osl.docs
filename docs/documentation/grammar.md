---
icon: fontawesome/solid/spell-check
---

# Current Grammar - Python Version

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
- `expB` → `logicAnd ("||" logicAnd)*`
- `logicAnd` → `comparison ("&&" comparison)*`
- `comparison` → `add (("<" | ">" | "<=" | ">=" | "=" | "!=") add)*`
- `add` → `mul (("+" | "-") mul)*`
- `mul` → `exp (("*" | "/" | "%") exp)*`
- `exp` → `unary ("^" unary)*`
- `unary` → `("-" | "~") unary` | `secondary` | `arrayDecl`

## Calls, Array Decls, and Array Accesses

- `secondary` → `primary` | `primary calls` | `primary ArrAccesses`
- `calls` → `cl` | `cl calls`
- `cl` → `"(" arguments? ")"`
- `ArrAccesses` → `ac` | `ac ArrAccesses`
- `ac` → `"[" expB "]"`

## Primary: Literals and Tokens

- `primary` → `NUMBER` | `IDENTIFIER` | `"(" expB ")"`
- `NUMBER` → `DIGIT+ ("." DIGIT*)?` | `"." DIGIT+`
- `DIGIT` → `"0"` | `"1"` | `"2"` | `"3"` | `"4"` | `"5"` | `"6"` | `"7"` | `"8"` | `"9"`
- `IDENTIFIER` → `LETTER (LETTER | DIGIT | "_")*`
- `LETTER` → `"a".."z"` | `"A".."Z"`

## Notes on Recursive Descent

- **Left Recursion**: Productions like `logicOr`, `logicAnd`, `comparison`, `add`, `mul`, and `exp` are left-recursive and can be handled iteratively in a parser to avoid infinite recursion.
- **Right Recursion**: `unary` has a right-recursive form with operators like `"-"` or `"√"`, fitting naturally into recursive descent.
- **Operator Precedence**: The grammar enforces precedence from highest (`unary`) to lowest (`logicOr`), ensuring correct expression evaluation.
- **Optional Elements**: Constructs like `parameters?`, `expression?`, and `"else" statement?` indicate optional components, parsed with conditional checks.

# Grammar Structure Unambiguous

The grammar is organized into several categories: program structure, declarations, statements, expressions, types, and literals. Below, we explain each category with examples from OSL syntax.

### 1. Program Structure

- **Rules**:

  - `<Prog> ::= <Decls>`: A program is a sequence of declarations.
  - `<Decls> ::= <Decl> <Decls> | <Decl>`: Declarations are processed sequentially.
  - `<Block> ::= LBRACE RBRACE | LBRACE <Decls> RBRACE`: A block is a sequence of declarations within `{}`.

### 2. Declarations

- **Rules**:

  - `<Decl> ::= <ConstDecl> | <VarDecl> | <FunDecl> | <Stmt>`: Declarations include constants, variables, functions, or statements.
  - `<ConstDecl> ::= CONST <DeclType> IDEN ASSIGN <Val> SEMC`: Constant declaration with mandatory initialization.
  - `<VarDecl> ::= VAR <DeclType> IDEN ASSIGN <Val> SEMC | VAR <DeclType> IDEN SEMC`: Variable declaration with optional initialization.
  - `<FunDecl> ::= DEFINE <Type> IDEN LPAREN RPAREN <Block> | DEFINE <Type> IDEN LPAREN <DeclParams> RPAREN <Block>`: Function declaration with optional parameters.
  - `<DeclParams> ::= <DeclParam> | <DeclParam> COMMA <DeclParams>`: Parameter list.
  - `<DeclParam> ::= <Type> IDEN`: Typed parameter.

### 3. Statements

- **Rules**:

  - `<Stmt> ::= <ExpStmt> | <LogStmt> | <RetStmt> | <Cond> | <Loop>`: Statements include expressions, logging, returns, conditionals, or loops.
  - `<ExpStmt> ::= <Val> SEMC`: Expression statement.
  - `<LogStmt> ::= LOG <Val> SEMC`: Print a value.
  - `<RetStmt> ::= RETURN <Val> SEMC`: Return a value.
  - `<Loop> ::= WHILE <Val> <Block>`: While loop with a condition and block.
  - `<Cond> ::= IF <Val> <Block> | IF <Val> <Block> <Else> | IF <Val> <Block> <Elifs> | IF <Val> <Block> <Elifs> <Else>`: Conditional with `if`, `elif`, `else`.
  - `<Elifs> ::= <Elif> | <Elif> <Elifs>`: Multiple `elif` clauses.
  - `<Elif> ::= ELIF <Val> <Block>`: Single `elif` clause.
  - `<Else> ::= ELSE <Block>`: Optional `else` clause.

### 4. Expressions

- **Rules**:

  - `<Val> ::= <Exp> | <Assn>`: A value is an expression or assignment.
  - `<Assn> ::= <Loc> ASSIGN <Val>`: Assignment to a location (variable, array element, or dereferenced pointer).
  - `<Loc> ::= <ArrAcc> | IDEN | <PtrDeref>`: Location can be an array access, identifier, or dereferenced pointer.
  - `<Exp> ::= <UOp> | <BinOp> | <UnAmb>`: Expressions include unary operations, binary operations, or unambiguous terms.
  - `<BinOp> ::= <Shift> | <Xand> | <Xor> | <And> | <Or> | <Mod> | <Pow> | <Sub> | <Div> | <Add> | <Mul> | <Eq> | <Neq> | <Less> | <Great> | <ArrAcc>`: Binary operations with clear precedence.
  - `<UOp> ::= <UnSign> | <UnNot> | <PtrDeref> | <FunCall> | <Ptr>`: Unary operations (sign, logical not, dereference, function call, pointer).
  - `<UnAmb> ::= <Atom> | <Arr> | <Ptr> | IDEN | <ArrAcc> | LPAREN <Exp> RPAREN`: Unambiguous terms (literals, arrays, pointers, identifiers, array accesses, parenthesized expressions).

### 5. Arrays and Array Access

- **Rules**:

  - `<Arr> ::= LBRACE <Args> RBRACE`: Array literal with comma-separated values.
  - `<ArrAcc> ::= <Arr> <VMats> | IDEN <VMats> | LPAREN <Exp> RPAREN <VMats>`: Array access with multiple indices.
  - `<VMats> ::= <VMat> <VMats> | <VMat>`: Multiple index brackets.
  - `<VMat> ::= LBOX <Val> RBOX`: Single index bracket.
  - `<ArrType> ::= <AtomType> <Mats> | <FunType> <Mats> | <PtrType> <Mats>`: Array type with dimensions.
  - `<ArrDeclType> ::= <AtomType> <VMats> | <FunType> <VMats> | <PtrType> <VMats>`: Declared array type with fixed sizes.

### 6. Functions and Function Calls

- **Rules**:

  - `<FunDecl> ::= DEFINE <Type> IDEN LPAREN RPAREN <Block> | DEFINE <Type> IDEN LPAREN <DeclParams> RPAREN <Block>`: Function declaration.
  - `<FunCall> ::= <UnAmb> <Calls>`: Function call with chained calls.
  - `<Calls> ::= <Call> <Calls> | <Call>`: Multiple call arguments.
  - `<Call> ::= LPAREN <Args> RPAREN | LPAREN RPAREN`: Single call with optional arguments.
  - `<Args> ::= <Val> | <Val> COMMA <Args>`: Comma-separated arguments.
  - `<FunType> ::= TFN LPAREN <SigParams> RPAREN RETURNS <Type> | TFN LPAREN RPAREN RETURNS <Type>`: Function type signature.

### 7. Pointers and Dereferencing

- **Rules**:

  - `<Ptr> ::= HASH <Loc>`: Pointer to a location.
  - `<PtrDeref> ::= AT <UnAmb>`: Dereference a pointer.
  - `<PtrType> ::= HASH <Type>`: Pointer type.

### 8. Types and Literals

- **Rules**:
  - `<Type> ::= <AtomType> | <CompType>`: Types are atomic or composite.
  - `<AtomType> ::= NULL | TBOOL | TC8 | TU8 | ... | TF128`: Basic types (null, bool, integers, floats, char).
  - `<CompType> ::= <FunType> | <ArrType> | <PtrType>`: Composite types (functions, arrays, pointers).
  - `<Atom> ::= NULL | <Bool> | <Char> | <Number>`: Literals for null, booleans, characters, and numbers.
  - `<Number> ::= BIN | OCT | DEC | HEX`: Numeric literals in binary, octal, decimal, or hexadecimal.

<!--
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
``` -->
