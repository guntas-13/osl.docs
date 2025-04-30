---
icon: material/run-fast
---

# Codegen Escape Analysis

## Escape Analysis for Closures

Escape analysis in osl's codegen determines which variables are captured by closures to ensure they persist beyond their lexical scope. This is critical for supporting closures, where a function retains access to variables from its enclosing environment.

### How It Works

- **Tracking Used Variables**: The `codegen.py` script maintains a `closure` list of lists, where each inner list tracks variable IDs used in a scope. When generating code for a function (`LetFun`), a new scope is created by appending an empty list to `closure`.
- **Capturing Variables**: During codegen, operations like `Variable`, `Let`, `Assign`, and `CallFun` add variable IDs to the current scope's list if `make_closure` is `True`. This indicates the variable is used within the function body or its nested functions.
- **Closure Creation**: In the `LetFun` case, after generating the function body, the codegen identifies variables that "escape" (are used in the function or its nested functions) by collecting IDs from the `closure` list into `used_vars`. These IDs are then used to generate `MAKE_CLOSURE` instructions, which include:
  - Pushing the IDs of captured variables.
  - Pushing the count of captured variables.
  - Creating a closure object tied to the function.
- **Optimization**: Only variables that are actually used (`used_vars`) are included in the closure, minimizing memory overhead. The `closure` list is popped after processing the function body to restore the outer scope's context.

### Code Snippet

The `LetFun` case in `codegen.py` illustrates this:

```python
case LetFun(Variable(varName, i), params, body):
    full_code.append(JUMP)
    full_code.extend(int(0).to_bytes(4, 'little'))
    entry_point = len(full_code)
    closure.append([])  # New scope for closure
    for param in params:
        full_code.append(BIND)
        full_code.extend(int(param.id).to_bytes(8, 'little'))
        closure[-1].append(param.id)
    do_codegen(body, True, closure)  # Generate body, track used variables
    body_pos = len(full_code)
    full_code[entry_point - 4: entry_point] = int(body_pos - entry_point).to_bytes(4, 'little')
    body_needs = closure.pop()  # Get used variables
    for bn in body_needs:
        used_vars.add(bn)  # Collect escaping variables
    # Generate function
    full_code.append(PUSH_INT)
    full_code.extend(int(entry_point).to_bytes(8, 'little'))
    full_code.append(PUSH_INT)
    full_code.extend(int(body_pos).to_bytes(8, 'little'))
    full_code.append(MAKE_FUNC)
    full_code.append(BIND)
    full_code.extend(int(i).to_bytes(8, 'little'))
    full_code.append(GET)
    full_code.extend(int(i).to_bytes(8, 'little'))
    # Generate closure
    ctr = 0
    if make_closure:
        for cl in closure[::-1]:
            for it in cl:
                if it in used_vars:
                    full_code.append(PUSH_INT)
                    full_code.extend(int(it).to_bytes(8, 'little'))
                    ctr += 1
    full_code.append(PUSH_INT)
    full_code.extend(int(ctr).to_bytes(8, 'little'))
    full_code.append(MAKE_CLOSURE)
```
