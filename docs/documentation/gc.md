---
icon: material/trash-can
---

# Garbage Collector Algorithm Overview

osl. implements a simple **mark-and-sweep** garbage collector. The GC reclaims heap-allocated objects (of type `VAL_OBJ`) that are no longer reachable from the VM’s root set.

---

## 1. Object Representation

Each heap-allocated object is represented by a `GCObject` structure that contains:

- **Marked Flag (`marked`):**  
  Indicates whether the object has been reached during the mark phase.

- **Next Pointer (`next`):**  
  Points to the next object in a global linked list (`gc_objects`) used to traverse all allocated objects during the sweep phase.

- **Field Count (`field_count`):**  
  Specifies how many `Value` fields (the object’s contents) the object holds.

- **Fields (`fields[]`):**  
  A flexible array member that stores the actual data (which can be primitive types or references to other objects).

**Figure 1. GCObject Structure**

```
+-----------------------------+
|  GCObject                 |
+-----------------------------+
| marked (1 byte)           | <-- Mark flag for GC
+-----------------------------+
| next (pointer)            | <-- Next object in GC list
+-----------------------------+
| field_count (1 byte)      | <-- Number of Value fields
+-----------------------------+
| fields[0] ... fields[n-1] | <-- Data fields (each is a Value)
+-----------------------------+
```

---

## 2. Global GC Data

- **`gc_objects`:**  
  A global linked list of all allocated `GCObject`s.

- **`total_allocated`:**  
  The total size (in bytes) of all allocated GC objects. This is used to decide when to trigger a collection.

- **`gc_threshold`:**  
  A predefined memory threshold (e.g., 10KB) that, when exceeded, triggers a GC cycle.

---

## 3. Allocation (`gc_alloc`)

When a new object is created via the `NEW_OBJECT` opcode, the `gc_alloc` function:

1. Computes the total size needed (header plus space for all `Value` fields).
2. Allocates memory using `malloc` and initializes:
   - The mark flag (`marked = 0`).
   - The field count.
   - All fields are initialized to a default value (e.g., an integer 0).
3. Inserts the new object at the head of the `gc_objects` linked list.
4. Increases the `total_allocated` counter.
5. Returns a pointer to the new object, which is then wrapped in a `Value` of type `VAL_OBJ`.

---

## 4. Mark Phase

### Purpose:

To identify all objects that are still in use (reachable) by marking them.

### Process:

- **Starting from Roots:**  
  The GC scans all roots—primarily the VM’s operand stack (via `gc_mark_roots`).
- **Marking Objects:**  
  For each `Value` on the stack:
  - If the value is of type `VAL_OBJ`, `gc_mark_value` calls `gc_mark` on the object.
  - `gc_mark` sets the object's `marked` flag to 1 and recursively marks any other objects referenced in its fields.

**Figure 2. Mark Phase Flow**

```
        +-----------------+
        |   VM Stack      |
        +-----------------+
                │
                ▼
     For each Value on the stack
                │
                ▼
      If Value is VAL_OBJ ---> gc_mark_value()
                │
                ▼
         gc_mark(GCObject*)
                │
    +-----------+-----------+
    |                       |
    ▼                       ▼
Mark object as reached   For each field:
 (set marked = 1)         If field is VAL_OBJ → Recursively mark
```

---

## 5. Sweep Phase

### Purpose:

To reclaim memory by freeing objects that were not marked as reachable.

### Process:

- **Traverse the Global List:**  
  The `gc_sweep` function iterates over the `gc_objects` list using a pointer-to-pointer technique.
- **Free Unmarked Objects:**  
  For each object:
  - If the `marked` flag is 0, it is considered unreachable and is freed.
  - The `total_allocated` counter is reduced by the size of the freed object.
- **Reset Marked Flag:**  
  For surviving objects, the mark flag is cleared (set back to 0) in preparation for the next GC cycle.

---

## 6. GC Cycle (`gc_collect`)

The function `gc_collect` ties together the mark and sweep phases:

1. **Mark:**  
   `gc_mark_roots(stack, top)` is called to mark all objects reachable from the VM’s current stack.

2. **Sweep:**  
   `gc_sweep()` is executed to free all unmarked objects and reset marks on survivors.

This cycle is typically triggered in the `NEW_OBJECT` opcode when `total_allocated` exceeds `gc_threshold`.

---

## Summary

The garbage collector in this VM uses a **mark-and-sweep** strategy:

- **Mark Phase:**
  - Traverses the root set (the VM stack).
  - Recursively marks all reachable objects.
- **Sweep Phase:**
  - Iterates over all allocated objects.
  - Frees any object that was not marked.
  - Resets the mark flag for objects that survive.
