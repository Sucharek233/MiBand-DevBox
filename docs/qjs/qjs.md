# QuickJS Shell activity
Executes JavaScript code within the application context.

> **Note:** `type` isn't being passed in this activity

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `code` | string | **Yes** | Code to be executed |

### Request
```json
{
  "type": "qjs",
  "args": {
    "code": "console.log('hello world!'); console.log('hi!'); return [1,2,3];"
  }
}
```

### Response
```json
{
  "type": "qjs",
  "res": "[1,2,3]",
  "logs": [
    "\"hello world!\"",
    "\"hi!\""
  ],
  "state": 0
}
```

## Special return types
TODO