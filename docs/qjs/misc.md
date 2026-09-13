# Miscellaneous activity
Contains simple misc functions.

## Operations Overview

| Operation | Purpose |
| :--- | :--- |
| [`gc`](#gc) | Runs garbage collection |

---

## gc
Runs the global.runGC() garbage collection function

### Arguments
None

### Request
```json
{
  "type": "misc",
  "args": {
    "type": "gc"
  }
}
```

### Response
```json
{
  "type": "apps",
  "state": 0
  "res": "Ok",
}
```