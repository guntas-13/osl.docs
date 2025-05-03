---
icon: fontawesome/solid/spell-check
---

# Current Grammar - Python Version

## Program Structure

- `program` → `declaration* EOF`
- `declaration` → `funDecl` | `varDecl` | `statement`
- `block` → `"{" declaration* "}"`

## Function Declarations and Calls

- `funDecl` → `"def" IDENTIFIER "(" parameters? ")" block`
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
- `comparison` → `add (("<" | ">" | "<=" | ">=" | "=" | "~=") add)*`
- `add` → `mul (("+" | "-") mul)*`
- `mul` → `unary (("*" | "/" | "%") unary)*`
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

The osl grammar is structured to define a program as a sequence of declarations, which include variable declarations, function declarations, and statements. It supports:

- **Typed System**: Variables and functions are explicitly typed (e.g., `i32`, `bool`, array types, function types).
- **Dynamic Arrays**: Arrays can be initialized with values or declared with fixed sizes.
- **First-Class Functions and Closures**: Functions can be assigned to variables and capture their environment.
- **Pointers and Dereferencing**: Support for pointer types and operations.
- **Control Flow**: Conditional statements (`if`, `elif`, `else`), loops (`while`), and return statements.
- **Expressions**: A rich expression system with unary, binary, and array access operations.

The grammar avoids ambiguities by:

- Separating typed and untyped constructs (e.g., `Val` vs. `Loc`).
- Using mandatory blocks for control flow to eliminate dangling-else issues.
- Defining clear precedence for operators and expressions.
- Supporting recursive descent parsing with iterative handling of left-recursive productions (e.g., `Add`, `Mul`).

## Grammar Structure

The grammar is organized into several categories: program structure, declarations, statements, expressions, types, and literals. Below, we explain each category with examples from OSL syntax.

### 1. Program Structure

- **Rules**:

  - `<Prog> ::= <Decls>`: A program is a sequence of declarations.
  - `<Decls> ::= <Decl> <Decls> | <Decl>`: Declarations are processed sequentially.
  - `<Block> ::= LBRACE RBRACE | LBRACE <Decls> RBRACE`: A block is a sequence of declarations within `{}`.

- **Description**: A program consists of declarations (variable, function, or statement). Blocks encapsulate declarations, used in functions, conditionals, and loops, ensuring scoped environments.

- **Example**:
  ```py
  var i32 x := 5;
  def i32 fact(i32 n) {
    if (n = 0) { return 1; }
    return n * fact(n - 1);
  }
  log fact(5);
  ```
  This program declares a variable, a function, and a statement within a global block.

### 2. Declarations

- **Rules**:

  - `<Decl> ::= <ConstDecl> | <VarDecl> | <FunDecl> | <Stmt>`: Declarations include constants, variables, functions, or statements.
  - `<ConstDecl> ::= CONST <DeclType> IDEN ASSIGN <Val> SEMC`: Constant declaration with mandatory initialization.
  - `<VarDecl> ::= VAR <DeclType> IDEN ASSIGN <Val> SEMC | VAR <DeclType> IDEN SEMC`: Variable declaration with optional initialization.
  - `<FunDecl> ::= DEFINE <Type> IDEN LPAREN RPAREN <Block> | DEFINE <Type> IDEN LPAREN <DeclParams> RPAREN <Block>`: Function declaration with optional parameters.
  - `<DeclParams> ::= <DeclParam> | <DeclParam> COMMA <DeclParams>`: Parameter list.
  - `<DeclParam> ::= <Type> IDEN`: Typed parameter.

- **Description**: Declarations define the program’s structure. Constants and variables are typed, functions support multiple parameters, and all declarations end with a semicolon (`;`).

- **Example**:
  ```py
  const i32 MAX := 100;
  var i32 count := 0;
  def i32 add(i32 a, i32 b) {
      return a + b;
  }
  ```
  Declares a constant, a variable, and a function with two parameters.

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

- **Description**: Statements control program execution. `log` outputs values, `return` exits functions, `while` loops iterate, and conditionals (`if`, `elif`, `else`) branch based on conditions. Blocks are mandatory to avoid ambiguity (e.g., dangling-else problem).

- **Example**:
  ```py
  var i32 i := 0;
  while (i < 5) {
      log i;
      i := i + 1;
  }
  if (i = 5) {
      log "Done";
  } elif (i > 5) {
      log "Overflow";
  } else {
      log "Error";
  }
  ```
  A loop and conditional with logging statements.

### 4. Expressions

- **Rules**:

  - `<Val> ::= <Exp> | <Assn>`: A value is an expression or assignment.
  - `<Assn> ::= <Loc> ASSIGN <Val>`: Assignment to a location (variable, array element, or dereferenced pointer).
  - `<Loc> ::= <ArrAcc> | IDEN | <PtrDeref>`: Location can be an array access, identifier, or dereferenced pointer.
  - `<Exp> ::= <UOp> | <BinOp> | <UnAmb>`: Expressions include unary operations, binary operations, or unambiguous terms.
  - `<BinOp> ::= <Shift> | <Xand> | <Xor> | <And> | <Or> | <Mod> | <Pow> | <Sub> | <Div> | <Add> | <Mul> | <Eq> | <Neq> | <Less> | <Great> | <ArrAcc>`: Binary operations with clear precedence.
  - `<UOp> ::= <UnSign> | <UnNot> | <PtrDeref> | <FunCall> | <Ptr>`: Unary operations (sign, logical not, dereference, function call, pointer).
  - `<UnAmb> ::= <Atom> | <Arr> | <Ptr> | IDEN | <ArrAcc> | LPAREN <Exp> RPAREN`: Unambiguous terms (literals, arrays, pointers, identifiers, array accesses, parenthesized expressions).

- **Description**: Expressions are the core of OSL’s computation, supporting arithmetic (`+`, `-`, `*`, `/`, `%`, `^`), logical (`&`, `|`, `!|`, `!&`), comparison (`=`, `~=`, `<`, `>`, `<=`, `>=`), and bitwise operations (`<<`, `>>`). Array accesses and function calls are integrated into expressions, allowing complex computations.

- **Example**:
  ```py
  var i32 x := 5;
  var i32 y := x * 2 + 3; // 13
  var bool z := (x < 10) & (y > 10); // true
  x := x ^ 2; // x = 25
  log y[0]; // Array access
  ```
  Demonstrates arithmetic, logical, and array access expressions.

### 5. Arrays and Array Access

- **Rules**:

  - `<Arr> ::= LBRACE <Args> RBRACE`: Array literal with comma-separated values.
  - `<ArrAcc> ::= <Arr> <VMats> | IDEN <VMats> | LPAREN <Exp> RPAREN <VMats>`: Array access with multiple indices.
  - `<VMats> ::= <VMat> <VMats> | <VMat>`: Multiple index brackets.
  - `<VMat> ::= LBOX <Val> RBOX`: Single index bracket.
  - `<ArrType> ::= <AtomType> <Mats> | <FunType> <Mats> | <PtrType> <Mats>`: Array type with dimensions.
  - `<ArrDeclType> ::= <AtomType> <VMats> | <FunType> <VMats> | <PtrType> <VMats>`: Declared array type with fixed sizes.

- **Description**: Arrays can be dynamically initialized (e.g., `[1, 2, 3]`) or declared with fixed sizes (e.g., `i32[2][3]`). Multi-dimensional arrays are supported, with access via multiple `[index]` brackets. Array types are explicitly declared.

- **Example**:
  ```py
  var i32 arr := {1, 2, 3};
  var i32[2][2] matrix;
  matrix[0][1] := 5;
  log arr[0]; // Prints 1
  ```
  Shows array initialization, declaration, and access.

### 6. Functions and Function Calls

- **Rules**:

  - `<FunDecl> ::= DEFINE <Type> IDEN LPAREN RPAREN <Block> | DEFINE <Type> IDEN LPAREN <DeclParams> RPAREN <Block>`: Function declaration.
  - `<FunCall> ::= <UnAmb> <Calls>`: Function call with chained calls.
  - `<Calls> ::= <Call> <Calls> | <Call>`: Multiple call arguments.
  - `<Call> ::= LPAREN <Args> RPAREN | LPAREN RPAREN`: Single call with optional arguments.
  - `<Args> ::= <Val> | <Val> COMMA <Args>`: Comma-separated arguments.
  - `<FunType> ::= TFN LPAREN <SigParams> RPAREN RETURNS <Type> | TFN LPAREN RPAREN RETURNS <Type>`: Function type signature.

- **Description**: Functions are first-class, supporting closures via captured variables. Function calls can be chained (e.g., `f(10)(5)` for nested functions). Types specify return and parameter types.

- **Example**:

  ```py
  def i32 counter() {
    var i32 count := 0;
    def i32 inc() {
        count := count + 1;
        return count;
    }
    return inc;
  }
  var fn()->i32 c1 := counter();
  log c1(); // Prints 1
  ```

  ```py
  def nav bar() {
    log 7;
  }

  def nav foo(fn()->nav f) {
    f();
    log 5;
  }

  foo(bar);
  ```

  A closure example with a counter function.

### 7. Pointers and Dereferencing

- **Rules**:

  - `<Ptr> ::= HASH <Loc>`: Pointer to a location.
  - `<PtrDeref> ::= AT <UnAmb>`: Dereference a pointer.
  - `<PtrType> ::= HASH <Type>`: Pointer type.

- **Description**: Pointers reference memory locations, and dereferencing accesses the pointed-to value. Pointer types are explicitly declared, supporting low-level memory operations.

- **Example**:
  ```py
  var i32 x := 5;
  var #i32 p := #x; // Pointer to x
  @p := 10; // Dereference and set x to 10
  log x; // Prints 10
  ```
  Demonstrates pointer creation and dereferencing.

### 8. Types and Literals

- **Rules**:

  - `<Type> ::= <AtomType> | <CompType>`: Types are atomic or composite.
  - `<AtomType> ::= NULL | TBOOL | TC8 | TU8 | ... | TF128`: Basic types (null, bool, integers, floats, char).
  - `<CompType> ::= <FunType> | <ArrType> | <PtrType>`: Composite types (functions, arrays, pointers).
  - `<Atom> ::= NULL | <Bool> | <Char> | <Number>`: Literals for null, booleans, characters, and numbers.
  - `<Number> ::= BIN | OCT | DEC | HEX`: Numeric literals in binary, octal, decimal, or hexadecimal.

- **Description**: OSL’s type system includes primitive types (`i32`, `bool`, `f64`), composite types (arrays, functions, pointers), and literals for various numeric formats.

- **Example**:
  ```py
  var bool flag := true;
  var i32 num := 0xFF; // Hexadecimal 255
  var f64 pi := 3.14;
  ```
  Shows type declarations and literals.

## Design Choices and Features

1. **Unambiguity**:

   - Mandatory blocks in `if`, `elif`, `else`, and `while` eliminate dangling-else ambiguities.
   - Separate rules for locations (`Loc`) and values (`Val`) clarify assignment targets.
   - Explicit typing reduces parsing conflicts (e.g., `i32` vs. `fn` types).

2. **Recursive Descent**:

   - Left-recursive rules (e.g., `Add`, `Mul`) are iterable in a parser.
   - Right-recursive rules (e.g., `UnNot`, `UnSign`) fit recursive descent naturally.
   - Parenthesized expressions (`LPAREN <Exp> RPAREN`) resolve operator precedence.

3. **Rich Type System**:

   - Supports multiple integer (`i8` to `i128`), unsigned integer (`u8` to `u128`), and floating-point (`f16` to `f128`) types.
   - Composite types enable complex data structures and function signatures.

4. **Closure Support**:

   - Functions can capture variables, enabled by the VM’s linked-list environment and escape analysis in codegen.

5. **Array Flexibility**:
   - Dynamic arrays (`{1, 2, 3}`) and declared arrays (`i32[2][3]`) support diverse use cases.
   - Multi-dimensional access (`arr[0][1]`) is integrated into expressions.
