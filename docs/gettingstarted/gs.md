---
icon: material/rocket
---

# Getting Started

Welcome to osl. This guide will help you set up the environment, write your first `"Hello World!"` program, and explore the key features of osl.

## Quick Installation

The osl. compiler (cosl.) is under active development. For now, use the following placeholder instructions:

1. Clone the repository:
   ```bash
   git clone https://github.com/mshandilya/osl.git
   cd osl
   ```
2. Build the compiler (in progress):
   ```bash
   make install
   ```

Stay tuned to [GitHub](https://github.com/mshandilya/osl) for official releases!

## VSCode Extension: OSL Syntax Highlighting

Enhance your coding experience with the `osl-syntax-highlighting` VSCode extension:

1. Open VSCode.
2. Go to Extensions (`Ctrl+Shift+X`).
3. Search for `osl-syntax-highlighting` and install.
4. Open `.osl` files to enjoy syntax highlighting and basic autocompletion.

Download it from the [VSCode Marketplace](https://marketplace.visualstudio.com/items?itemName=GuntasSinghSaran.osl).

## Writing "Hello World!"

Here's a simple "Hello World!" program in OSL:

```py
log "Hello World!";
```

To run this:

1. Save it as `hello.osl`.
2. Use the OSL compiler to compile and execute:
   ```bash
   osl run hello.osl
   ```

Expected output:

```
Hello World!
```

## Overview of Features

OSL is designed to be a modern, efficient scripting language. Here’s a quick look at its features (some implemented, others proposed):

- **Unambiguity**: Grammar designed to eliminate parsing ambiguities (see [Grammar](../documentation/grammar.md)).
- **Garbage Collection**: Automatic memory management via a mark-and-sweep GC in the Stack VM (see [GC](../dev/gc.md)).
- **Escape Analysis**: Planned optimization to reduce heap allocations.
- **Bytecode-Generated Stack VM**: Executes programs via an efficient stack-based virtual machine (see [Bytecode](../dev/bytecode.md)).
- **Strongly Typed**: Enforces type safety with explicit declarations (e.g., `i32`, `bool`).

Let’s dive deeper in the [User Documentation](../documentation/variables.md)!
