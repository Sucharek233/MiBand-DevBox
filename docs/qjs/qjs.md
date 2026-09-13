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

## Return Value Sanitization

The result of the JavaScript code is converted to a JSON-safe value before it is returned. The serialized value is placed in `res`, so `res` is always a JSON string rather than a native response object.

The same conversion is applied to each argument passed to `console.log`. The resulting serialized arguments are joined with a space and stored as one entry in the `logs` array.

| JavaScript value | Sanitized value |
| --- | --- |
| `function` | `{ "$": "fn" }` |
| `undefined` | `{ "$": "undef" }` |
| `NaN` | `{ "$": "nan" }` |
| `Infinity` | `{ "$": "inf" }` |
| `-Infinity` | `{ "$": "ninf" }` |
| `null` | `{ "$": "null" }` |
| String longer than 1024 characters | First 1024 characters followed by `...` |
| Circular object reference | `{ "$": "ref", "to": "<path>" }` |

Finite numbers, booleans, and strings within the length limit retain their values. Objects are converted to JSON objects and arrays remain arrays. Object keys are converted to strings, including symbol keys. If reading a property throws, that property is represented as `{ "$": "err", "message": "..." }`.

### Example

```js
const value = {};
value.self = value;

console.log(undefined, null, NaN, Infinity);
return {
  callback: () => {},
  missing: undefined,
  negativeInfinity: -Infinity,
  self: value
};
```

```json
{
  "type": "qjs",
  "res": "{\"callback\":{\"$\":\"fn\"},\"missing\":{\"$\":\"undef\"},\"negativeInfinity\":{\"$\":\"ninf\"},\"self\":{\"self\":{\"$\":\"ref\",\"to\":\"root.self\"}}}",
  "logs": [
    "{\"$\":\"undef\"} {\"$\":\"null\"} {\"$\":\"nan\"} {\"$\":\"inf\"}"
  ],
  "state": 0
}
```