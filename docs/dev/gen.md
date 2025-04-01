---
icon: material/generator-portable
---

# Lexer and Parser Generator

This project implements a simple lexer and parser generator. The lexer is based on a trie data structure to efficiently match token patterns defined in an external file, and the parser uses a pushdown automaton (PDA) approach to build a parse tree from the token stream. The project is organized into separate files for lexing, parsing, and execution.

---

## Table of Contents

- [Lexer and Parser Generator](#lexer-and-parser-generator)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
  - [Project Structure](#project-structure)
  - [Lexer Details](#lexer-details)
    - [Trie Data Structure](#trie-data-structure)
    - [Token Generation](#token-generation)
    - [Lexical Rules File](#lexical-rules-file)
  - [Parser Details](#parser-details)
    - [Grammar Rule Parsing](#grammar-rule-parsing)
    - [PDA Construction](#pda-construction)
    - [Parse Tree Construction](#parse-tree-construction)
    - [Grammar Rules File](#grammar-rules-file)
  - [Usage Instructions](#usage-instructions)
  - [Code Walkthrough](#code-walkthrough)
    - [Lexer (`lexer.cpp`)](#lexer-lexercpp)
    - [Parser (`parser.cpp`)](#parser-parsercpp)
    - [Main Application (`main.cpp`)](#main-application-maincpp)
  - [Future Enhancements](#future-enhancements)
  - [Conclusion](#conclusion)

---

## Overview

This project comprises two major components:

1. **Lexer:**
   - Uses a trie (prefix tree) to store and match token symbols.
   - Reads token definitions from `lex_rules.txt`.
   - Processes an input stream character by character to output tokens.

2. **Parser:**
   - Reads grammar rules from `parse_rules.txt` using a BNF-like syntax.
   - Constructs a pushdown automaton (PDA) based on the grammar.
   - Uses a breadth-first search to find a valid parsing path and builds a parse tree.
   - Visualizes the resulting parse tree.

---

## Project Structure

- **lexer.cpp**  
  Contains the implementation of the lexer's trie, token insertion, and tokenization process.

- **parser.cpp**  
  Implements the parser using PDA techniques, grammar rule processing, and parse tree construction.

- **main.cpp**  
  Provides the main entry point. It runs the lexer and parser on an input file and visualizes the parse tree.

- **lex_rules.txt**  
  Specifies lexical rules. For example:
  ```
  ( LBRACE
  ) RBRACE
  + ADD
  * MUL
  / DIV
  - NEG
  ^ POW
  % MOD
  ~ NOT
  | OR
  & AND
  !| XOR
  !& XAND
  >> RSHIFT
  << LSHIFT
  0-->9 NUM
  a-->z CHAR
  ```

- **parse_rules.txt**  
  Defines grammar rules using a production rule format:
  ```
  <unamb> ::= LBRACE <add> RBRACE
  <add> ::= <atomic> ADD <atomic>
  <atomic> ::= <number> | NEG <number>
  <number> ::= NUM <number> | NUM
  ```

- **input.txt**  
  Contains the input string to be tokenized and parsed. For example:
  ```
  (1+2)
  ```

---

## Lexer Details

### Trie Data Structure

- **Purpose:**  
  The trie is used to store all token symbols from `lex_rules.txt` for fast prefix matching.

- **Implementation:**  
  Each node in the trie is represented by an array of 256 integers (one for each ASCII character), initially set to -1. A separate vector (`term`) stores terminal status and token names.

- **Key Functions:**
  - `initNode()`: Initializes a trie node.
  - `insert(std::string sym, std::string name)`: Inserts a symbol into the trie.
  - `feed(char c)`: Processes one character at a time and returns a token when a complete symbol is matched.

_Example of token insertion:_

```cpp
void lexer::Trie::insert(std::string sym, std::string name){
    int node = 0;
    for(char c: sym){
        if(nodes[node][c] == -1){
            nodes[node][c] = initNode();
        }
        node = nodes[node][c];
    }
    term[node] = {true, name};
}
```

### Token Generation

- When the `feed()` function detects a complete token (a terminal node in the trie), it generates a `Token` object.
- Some tokens may be marked as special (defined in the `sp` map) so that the actual character is included in the token value.

### Lexical Rules File

- **File:** `lex_rules.txt`
- **Format:** Each line maps a symbol (or a range, e.g., `0-->9` for digits) to a token name.
- The lexer constructor (`lexer::genLexer`) reads this file and populates the trie.

---

## Parser Details

### Grammar Rule Parsing

- The parser reads grammar rules from `parse_rules.txt`.
- Each rule is defined in a BNF-like format using `::=` and alternatives separated by `|`.
- Rules are split into tokens and stored in a structured format for later PDA construction.

### PDA Construction

- **Symbol IDs:**  
  Each grammar symbol (terminals and nonterminals) is assigned a unique numeric ID.
- **Transitions:**  
  The PDA builds transitions for each production rule. Transitions indicate how symbols should be replaced or reduced during parsing.
- **Epsilon Transitions:**  
  These are added for non-token symbols to allow the parser to handle optional or recursive rules.

### Parse Tree Construction

- **Breadth-First Search (BFS):**  
  The parser uses a BFS strategy to explore possible parsing paths.
- **Recursive Tree Population:**  
  The `populateTree()` function is used to recursively build the parse tree once a valid parsing path is found.

_Example snippet from `populateTree()`:_

```cpp
void parser::genParser::populateTree(int curNode, int curSym, int &pind, std::vector<int> path, int &tind, std::vector<Token> tokens, parseTree &tree){
    tree.id[curNode] = pda.idSym[curSym];
    if(pda.isToken[curSym]){
        tree.val[curNode] = tokens[tind++].getVal();
        return;
    }
    int ind = path[pind++];
    for(int i = 0;i < pda.pro[curSym][ind].size();i++){
        int nNode = tree.newNode();
        tree.adj[curNode].push_back(nNode);
        populateTree(nNode, pda.pro[curSym][ind][i], pind, path, tind, tokens, tree);
    }
}
```

### Grammar Rules File

- **File:** `parse_rules.txt`
- **Format:** Each line is a production rule. For example:
  ```
  <unamb> ::= LBRACE <add> RBRACE
  <add> ::= <atomic> ADD <atomic>
  <atomic> ::= <number> | NEG <number>
  <number> ::= NUM <number> | NUM
  ```

---

## Usage Instructions

1. **Prepare the Rule Files:**

   - Edit `lex_rules.txt` to define your token symbols.
   - Edit `parse_rules.txt` to define your grammar productions.

2. **Provide an Input File:**

   - Create or edit `input.txt` with the string you wish to tokenize and parse.

3. **Compile the Project:**  
   Use a C++ compiler (e.g., g++) to compile the source files:
   ```bash
   g++ -std=c++17 utils.cpp main.cpp lexer.cpp parser.cpp -o main
   ```

4. **Run the Executable:**  
   Execute the program:
   ```bash
   ./main
   ```
   The program will print the tokens produced by the lexer and then display the parse tree.

---

## Code Walkthrough

### Lexer (`lexer.cpp`)

- **Trie Initialization:**  
  `initNode()` initializes a node for the trie.

- **Token Insertion:**  
  `insert()` adds each token symbol to the trie, handling individual characters and ranges.

- **Character Feeding:**  
  `feed()` processes each input character and matches it against the trie. When a terminal node is reached, a token is produced.

### Parser (`parser.cpp`)

- **Grammar Initialization:**  
  The PDA constructor assigns IDs to grammar symbols and builds transitions based on production rules.

- **Parsing Process:**  
  The `parse()` function uses a queue-based search to find a valid sequence of transitions that consume the token stream.

- **Tree Population:**  
  Once a valid parse is detected, `populateTree()` recursively builds a parse tree reflecting the grammar structure.

### Main Application (`main.cpp`)

- **Lexing Phase:**  
  Reads `input.txt` and uses the lexer to produce tokens.

- **Parsing Phase:**  
  Uses the parser to convert the token stream into a parse tree.

- **Visualization:**  
  The `vizTree()` function prints the parse tree in a human-readable hierarchical format.

---

## Future Enhancements

- **Stream-Based Lexing:**  
  Modify the lexer to accept streams of characters (instead of file names) for increased flexibility.

- **Improved Error Handling:**  
  Enhance error reporting in both the lexer and parser to provide more meaningful diagnostics.

- **Extended Grammar Support:**  
  Expand the parser to handle more complex grammars and additional language features.

---

## Conclusion

This lexer and parser generator provides a modular and extensible framework for processing textual input based on external token and grammar rules. The trie-based lexer efficiently matches token patterns, while the PDA-based parser constructs a parse tree that reflects the input's syntactic structure. This design lays the groundwork for further enhancements and adaptations to support more advanced language processing needs.
