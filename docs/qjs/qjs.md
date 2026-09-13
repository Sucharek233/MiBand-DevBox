# QuickJS Shell activity
Executes JavaScript code within the application context. Supports calling `async` functions.

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

## Handling invalid code
If received code is invalid, it'll get caught and the error will get returned.

### Request
```json
{
  "type": "qjs",
  "args": {
    "code": "iDontExist();"
  }
}
```

### Response
```json
{
  "type": "qjs",
  "state": 4,
  "msg": "'iDontExist' is not defined",
  "stack": "    at <anonymous> (<input>:8)\n    at anonymous (<input>:9)\n    at execute\n    at handle\n    at messageReceived\n    at apply (native)\n    at <anonymous> (@aiot/framework)\n    at <anonymous>\n    at <anonymous>\n    at <anonymous> (@aiot/framework:1)\n"
}
```

## Special return types
TODO