---
icon: material/hammer-screwdriver
---

## Unambiguity

osl’s grammar avoids ambiguity by requiring blocks in control structures:

```python
if (x > 5)
{
    log "Greater";
}
else
{
    log "Less or equal";
}
```

See [Grammar](./grammar.md) for more details.

## Escape Analysis

Escape analysis is a planned optimization to determine when variables can stay on the stack instead of the heap. Implementation is in progress—stay tuned!

```python
var x := 6;
fn f()
{
    var x := 6;
    fn g()
    {
        return x;
    }
    return g;
}
var h := f();
log h(); // 6
```
