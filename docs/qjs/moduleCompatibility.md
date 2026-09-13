# Module Compatibility activity
Checkes whether a module can be used and retrieves its available functions.

## Operations Overview

| Operation | Purpose |
| :--- | :--- |
| [`compat`](#compat) | Checks if a module is compatible |
| [`funcs`](#funcs) | Retrieves module functions |

---

## compat
Checks if a module/modules are compatible

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `modules` | array | **Yes** | Array of to be checked modules |

### Request
```json
{
  "type": "modules",
  "args": {
    "type": "compat",
    "modules": [
        "system.app",
        "system.fetch"
    ]
  }
}
```

### Response
```json
{
  "type": "modules",
  "state": 0,
  "res": {
    "system.app": true,
    "system.fetch": false
  }
}
```

## funcs
Retrieves module functions

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `module` | string | **Yes** | Module name |

### Request
```json
{
  "type": "modules",
  "args": {
    "type": "funcs",
    "module": "system.router"
  }
}
```

### Response
```json
{
  "type": "modules",
  "state": 0,
  "res": "{\"push\":{\"$\":\"fn\"},\"replace\":{\"$\":\"fn\"},\"back\":{\"$\":\"fn\"},\"clear\":{\"$\":\"fn\"},\"getLength\":{\"$\":\"fn\"},\"getState\":{\"$\":\"fn\"},\"getPages\":{\"$\":\"fn\"}}"
}
```

> **Note:** Since a module can have not only functions, functions are seperated with `{"$":"fn"}`