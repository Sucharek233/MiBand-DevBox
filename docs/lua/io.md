# I/O Activity
List and modify files, or stream a file from Lua to QuickJS in chunks.

## Operations Overview

| Operation | Purpose |
| :--- | :--- |
| [`list`](#list) | Lists the contents of a directory |
| [`cp`](#cp) | Copies a file or directory |
| [`mv`](#mv) | Moves/renames a file or directory |
| [`rm`](#rm) | Removes a file or directory |
| [`getStream`](#getstream) | Opens a file for chunked streaming |
| [`chunk`](#chunk) | Retrieves the next stream chunk |
| [`stop`](#stop) | Stops the active stream |

---

## list
Lists the contents of a directory.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `path` | string | **Yes** | Directory path |

### Request
```json
{
  "type": "io",
  "args": {
    "type": "list",
    "path": "/tmp"
  }
}
```

### Response
> **Note:** The `res` property contains the directory entries returned by the Lua enumerator.
```json
{
  "type": "io",
  "res": {
    "folders": [
      "tmp",
      "hi"
    ],
    "files": {
      "temporary.db": {"size": 12288}
    }
  },
  "state": 0
}
```

## cp
Copies a source path to a destination path. Supports single files or whole directories.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `src` | string | **Yes** | Source path |
| `dst` | string | **Yes** | Destination path |

### Request
```json
{
  "type": "io",
  "args": {
    "type": "cp",
    "src": "/path/to/source",
    "dst": "/path/to/destination"
  }
}
```

### Response
```json
{
  "type": "io",
  "state": 0,
  "res": "<operation result>"
}
```

> **Note:** The `res` value depends on the underlying file operation.

## mv
Moves a source path to a destination path.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `src` | string | **Yes** | Source path |
| `dst` | string | **Yes** | Destination path |

### Request
```json
{
  "type": "io",
  "args": {
    "type": "mv",
    "src": "/path/to/source",
    "dst": "/path/to/destination"
  }
}
```

### Response
```json
{
  "type": "io",
  "state": 0,
  "res": "<operation result>"
}
```

## rm
Removes a file or directory. Works on single files or whole directories.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `path` | string | **Yes** | Path to remove |

### Request
```json
{
  "type": "io",
  "args": {
    "type": "rm",
    "path": "/path/to/remove"
  }
}
```

### Response
```json
{
  "type": "io",
  "state": 0,
  "res": "<operation result>"
}
```

## getStream
Opens a file in Lua and prepares it for chunked transfer to QuickJS. Only one stream can be active at a time.

The Lua side reads up to `lSize` bytes from the source file and writes each Lua chunk to a temporary file. QuickJS then reads that temporary file in pieces no larger than `jSize`. `jSize` is capped at 32 KiB by QuickJS.

### Arguments
| Field | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `path` | string | **Yes** | - | Source file path |
| `jSize` | number | No | `30720` | Maximum number of bytes read by QuickJS per `chunk` response; capped at `32768` |
| `lSize` | number | No | `524288` | Maximum number of source bytes read by Lua for each temporary chunk |
| `b64` | boolean | No | `true` | Base64-encode each Lua chunk |

### Request
```json
{
  "type": "io",
  "args": {
    "type": "getStream",
    "path": "/data/apps.json",
    "jSize": 30720,
    "lSize": 524288,
    "b64": true
  }
}
```

### Response
```json
{
  "type": "io",
  "state": 0,
  "res": {
    "path": "internal://files/chunk",
    "fileSize": 3663
  }
}
```

The returned `fileSize` is the original source file size. The returned `path` is the temporary path used by QuickJS for chunk reading.

## chunk
Retrieves the next part of the active stream. Call `chunk` repeatedly after `getStream` until a response with `state: 0` is returned.

When the current temporary Lua chunk has been fully read, QuickJS requests the next Lua chunk internally. A successful data response has `state: 6` and contains the chunk data in `res`. The first response for each Lua chunk also contains `meta`.

### Arguments
None

### Request
```json
{
  "type": "io",
  "args": {
    "type": "chunk"
  }
}
```

### Data Response
```json
{
  "type": "io",
  "state": 6,
  "res": "ewogICAg...",
  "meta": {
    "currPos": 3663,
    "md5sum": "27d8827baeb342f1de46dabb8f0fc462"
  }
}
```

`res` is returned as a string made from the temporary file bytes. With the default `b64: true`, it contains base64 text. `currPos` is the number of original source bytes processed by Lua, and `md5sum` is the checksum of the temporary chunk file.

### End-of-Stream Response
```json
{
  "type": "io",
  "state": 0,
  "res": null
}
```

At end of stream, the temporary chunk file is removed and the stream is cleared.

## stop
Stops the active stream and removes its temporary chunk data.

### Arguments
None

### Request
```json
{
  "type": "io",
  "args": {
    "type": "stop"
  }
}
```

### Response
```json
{
  "type": "io",
  "state": 0,
  "res": "Stopped"
}
```

## Errors
Invalid arguments, file operation failures, and stream state conflicts return an error response.

```json
{
  "type": "io",
  "state": 4,
  "msg": "Not streaming"
}
```

Stream errors:
- `Already streaming` when `getStream` is called while another Lua stream is active.
- `Not streaming` when `chunk` is called without an active QuickJS stream.
- `Streamer uninitialized` when Lua receives `chunk` or `stop` without an active streamer.
- `File not open` when the Lua streamer cannot read its source file.