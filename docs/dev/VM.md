---
icon: material/stack-overflow
---

# Stack Virtual Machine: Design and Implementation

## Design Choices

### 1. Stack-Based Architecture

The VM adopts a stack-based architecture, where operands are pushed onto a stack, and operations pop their inputs from the stack and push results back. This design simplifies instruction encoding and execution, as most instructions do not need to specify operand locations explicitly.

**Motivation**:

- **Simplicity**: Stack-based VMs are easier to implement than register-based ones, as they avoid complex register allocation and management.
- **Portability**: The stack model is inherently portable across platforms, as it does not rely on specific hardware registers.
- **Compatibility with High-Level Constructs**: Stack-based operations align well with the evaluation of expressions in high-level languages, particularly for arithmetic and function calls.

**Example**:
For the `ADD` operation, the VM pops two values, adds them, and pushes the result:

```c
case ADD: {
    Value b = POP();
    Value a = POP();
    if (a.type == VAL_INT && b.type == VAL_INT) {
        Value result; result.type = VAL_INT;
        result.v = malloc(sizeof(int64_t));
        *(int64_t*)result.v = *(int64_t*)a.v + *(int64_t*)b.v;
        PUSH(result);
    } else if (a.type == VAL_FLOAT || b.type == VAL_FLOAT) {
        // Handle float addition
    } else {
        fprintf(stderr, "ADD supports only INT, FLOAT, CHAR values.\n");
        exit(1);
    }
    pc += 1;
    break;
}
```

### 2. Pointers on the Stack

The VM stores pointers to dynamically allocated memory on the stack for values like integers, floats, characters, arrays, and function objects. For example, an `int64_t` value is allocated on the heap, and a `Value` struct on the stack holds a pointer (`v`) to this memory.

**Motivation**:

- **Uniform Representation**: Storing pointers allows all values to have a consistent size on the stack (the size of a `Value` struct), simplifying stack management.
- **Dynamic Typing**: Pointers enable the `Value` union to support multiple types (`VAL_INT`, `VAL_FLOAT`, `VAL_ARR`, etc.) without needing to resize stack slots.
- **Python-like Arrays**: Arrays, inspired by Python's lists, are implemented as contiguous memory blocks of `Value` structs. Storing pointers on the stack allows arrays to reference heap-allocated memory, supporting dynamic resizing and heterogeneous elements.

**Example**:
Pushing an integer involves allocating memory and storing a pointer:

```c
case PUSH_INT: {
    if (pc + 8 >= codeSize) { fprintf(stderr, "Unexpected end (PUSH_INT)\n"); exit(1); }
    Value v; v.type = VAL_INT;
    int64_t l = 0;
    for (int j = 0; j < 8; j++) {
        l |= ((int64_t)code[pc+1+j]) << (8*j);
    }
    v.v = malloc(sizeof(int64_t));
    memcpy(v.v, &l, sizeof(int64_t));
    PUSH(v);
    pc += 9;
    break;
}
```

### 3. Environment as a Linked List

The VM uses a linked list (`llNode` in an `adjList` structure) to manage variable bindings in the environment. Each variable ID maps to a linked list of `llNode` entries, where each node represents a binding in a specific scope.

**Motivation**:

- **Scope Management**: The linked list allows the VM to maintain a stack of bindings for each variable, with the tail pointing to the most recent binding in the current scope. This supports nested scopes and closures.
- **Efficient Lookup**: Using an array of heads and tails (`vals.heads` and `vals.tails`) indexed by variable IDs provides O(1) access to the most recent binding.
- **Closure Support**: Closures capture variables by marking them as `isClosed`, ensuring their memory persists beyond their scope. The linked list structure facilitates tracking these bindings.

**Example**:
The `BIND` operation creates a new binding for a variable ID:

```c
case BIND: {
    if (pc + 8 >= codeSize) { fprintf(stderr, "Unexpected end (BIND)\n"); exit(1); }
    Value* v = (Value*)malloc(sizeof(Value));
    int64_t id = 0;
    for (int j = 0; j < 8; j++) {
        id |= ((int64_t)code[pc+1+j]) << (8*j);
    }
    Value vv = POP();
    ValueType t = vv.type;
    v->v = vv.v;
    if (vals.tails[id] == NULL) {
        vals.heads[id] = (llNode*)malloc(sizeof(llNode));
        vals.tails[id] = vals.heads[id];
        vals.tails[id]->type = t;
        vals.tails[id]->location = v;
        vals.tails[id]->scopeId = callScope;
        vals.tails[id]->isClosed = 0;
        sNodeStack[sNodeTail++] = (sNode){id, callScope};
    } else {
        // Append new node to the linked list
    }
    pc += 9;
    break;
}
```

### 4. Garbage Collection

The VM implements a mark-and-sweep garbage collector to manage heap-allocated objects (`GCObject` for heap objects and dynamically allocated values like arrays).

**Motivation**:

- **Memory Safety**: Automatic memory management prevents memory leaks and dangling pointers, crucial for a language with dynamic data structures.
- **Support for Objects and Arrays**: Heap objects (used for structs and arrays) require garbage collection to reclaim unused memory, especially since arrays can grow dynamically.
- **Closure Longevity**: Closures may reference variables that outlive their original scope, requiring the GC to preserve these objects until no longer reachable.

**Implementation**:

- **Mark Phase**: The GC marks reachable objects starting from stack roots (`gc_mark_roots`) and recursively marks referenced objects (`gc_mark`).
- **Sweep Phase**: Unmarked objects are freed, and their memory is reclaimed (`gc_sweep`).
- **Trigger**: GC is triggered when total allocated memory exceeds a threshold (`gc_threshold`).

**Example**:
The `gc_alloc` function allocates a new object and tracks it for GC:

```c
GCObject* gc_alloc(uint8_t field_count) {
    size_t size = sizeof(GCObject) + sizeof(Value) * field_count;
    GCObject* obj = (GCObject*) malloc(size);
    if (!obj) {
        fprintf(stderr, "Out of memory\n");
        exit(1);
    }
    obj->marked = 0;
    obj->field_count = field_count;
    obj->next = gc_objects;
    gc_objects = obj;
    total_allocated += size;
    return obj;
}
```

### 5. Python-like Arrays

Arrays are implemented as heap-allocated `Value` arrays, supporting dynamic sizing and heterogeneous elements, similar to Python lists. The `MAKE_ARRAY` and `MAKE_ARRAY_DECL` opcodes create arrays, and `ARR_ACC` and `STORE` handle access and modification.

**Motivation**:

- **Flexibility**: Python-like arrays allow storing elements of different types, supporting dynamic programming patterns.
- **Multi-dimensional Arrays**: `MAKE_ARRAY_DECL` supports multi-dimensional arrays by nesting `Value` arrays, enabling complex data structures.
- **Ease of Use**: Array operations are intuitive, with bounds checking and dynamic allocation handled by the VM.

**Example**:
Creating an array with `MAKE_ARRAY`:

```c
case MAKE_ARRAY: {
    if (pc + 2 >= codeSize) { fprintf(stderr, "Unexpected end in MAKE_ARRAY\n"); exit(1);}
    uint16_t nElems = code[pc+1] | (code[pc+2]<<8);
    Value v;
    v.type = VAL_ARR;
    v.v = malloc(nElems * sizeof(Value));
    for (int i = 0; i < nElems; i++) {
        ((Value*)(v.v))[i] = POP();
    }
    PUSH(v);
    pc += 3;
    break;
}
```

### 6. Closure Support

Closures are implemented using `MAKE_CLOSURE` and `CALL` opcodes, which manage captured variables and function environments.

**Motivation**:

- **Functional Programming**: Closures enable higher-order functions and persistent variable bindings, essential for functional programming paradigms.
- **Lexical Scoping**: The VM supports lexical scoping by capturing variables in a `closureObject`, ensuring they remain accessible after their scope exits.

**Implementation**:

- **Closure Object**: A `closureObject` stores an array of `closedObject` structs, each containing an ID, type, and pointer to a captured variable.
- **Scope Management**: The `CALL` opcode increments the `callScope` and sets up closure bindings, while `RETURN` cleans up non-closed bindings.

**Example**:
Creating a closure:

```c
case MAKE_CLOSURE: {
    Value numIds = POP();
    closureObject* co = (closureObject*)malloc(sizeof(closureObject));
    if (numIds.type == VAL_INT) {
        co->size = *(int64_t*)numIds.v;
        co->objs = (closedObject*)malloc(co->size * sizeof(closedObject));
        for (int j = 0; j < co->size; j++) {
            Value id = POP();
            closedObject cid;
            cid.id = *(int32_t*)(id.v);
            cid.location = vals.tails[cid.id]->location;
            cid.type = vals.tails[cid.id]->type;
            vals.tails[cid.id]->isClosed = 1;
            co->objs[j] = cid;
        }
    }
    Value f = POP();
    if (f.type == VAL_FUN) {
        ((functionObj*)(f.v))->co = co;
    }
    pc += 1;
    break;
}
```

## Implementation Details

### 1. Value Representation

The `Value` struct is central to the VM, encapsulating type information and data:

```c
typedef struct Value {
    ValueType type;
    union {
        void*    v;      // Pointer to heap-allocated data
        char     c;
        int16_t  s;
        int32_t  i;
        int64_t  l;
        float    f;
        double   d;
        GCObject* obj;
    };
} Value;
```

- **Type Safety**: The `type` field (`VAL_INT`, `VAL_FLOAT`, etc.) ensures operations are performed on compatible types.
- **Heap Allocation**: Most values use the `v` field to point to heap memory, supporting dynamic allocation and garbage collection.

### 2. Opcode Execution

The `execute` function is a large switch statement that processes each opcode. It maintains a program counter (`pc`), a stack (`stack`), and a stack pointer (`top`).

**Key Features**:

- **Error Handling**: Checks for stack underflow, invalid types, and out-of-bounds memory access.
- **Dynamic Dispatch**: The switch statement dispatches to the appropriate case based on the opcode.
- **Memory Management**: Allocates memory for values and objects, with GC triggered as needed.

### 3. Scope and Binding Management

The `adjList` structure manages variable bindings:

```c
typedef struct {
    llNode* heads[1<<15];
    llNode* tails[1<<15];
} adjList;
```

- **Initialization**: The `initialize` function sets all heads and tails to `NULL`.
- **Scope Tracking**: The `callScope` variable tracks the current scope, and `sNodeStack` records bindings for cleanup during `RETURN`.

### 4. Bytecode Generation

The `codegen.py` script generates bytecode from an AST, mapping high-level constructs to VM opcodes. It handles:

- **Expressions**: Arithmetic, comparisons, and logical operations.
- **Control Flow**: If-statements, while loops, and function calls.
- **Closures**: Tracks used variables to generate `MAKE_CLOSURE` instructions.

**Example**:
Generating code for a function declaration:

```python
case LetFun(Variable(varName, i), params, body):
    full_code.append(JUMP)
    full_code.extend(int(0).to_bytes(4, 'little'))
    entry_point = len(full_code)
    closure.append([])
    for param in params:
        full_code.append(BIND)
        full_code.extend(int(param.id).to_bytes(8, 'little'))
        closure[-1].append(param.id)
    do_codegen(body, True, closure)
    body_pos = len(full_code)
    full_code[entry_point - 4: entry_point] = int(body_pos - entry_point).to_bytes(4, 'little')
    full_code.append(PUSH_INT)
    full_code.extend(int(entry_point).to_bytes(8, 'little'))
    full_code.append(PUSH_INT)
    full_code.extend(int(body_pos).to_bytes(8, 'little'))
    full_code.append(MAKE_FUNC)
    full_code.append(BIND)
    full_code.extend(int(i).to_bytes(8, 'little'))
    full_code.append(GET)
    full_code.extend(int(i).to_bytes(8, 'little'))
```

### 5. Memory Management

The VM uses `malloc` for all dynamic allocations, with `free` called during GC or scope cleanup. Key considerations:

- **Leak Prevention**: The GC ensures unreferenced objects are freed.
- **Closure Persistence**: Closed variables are marked (`isClosed = 1`) to prevent premature deallocation.
- **Stack Overflow**: A fixed stack size (`STACK_SIZE = 1<<16`) with overflow checks.

## Trade-offs and Limitations

- **Performance**: Heap allocation for every value (e.g., integers) increases memory usage and overhead compared to direct stack storage. However, this enables dynamic typing and Python-like arrays.
- **Complexity**: The linked list environment adds complexity but is necessary for closure support and lexical scoping.
- **GC Overhead**: Mark-and-sweep GC introduces pauses, but the threshold-based triggering minimizes disruptions.
