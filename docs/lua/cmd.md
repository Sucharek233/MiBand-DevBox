# Terminal Activity

Execute a shell command on the device and return its captured output and exit code.

## Request

### Arguments
| Field | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `cmd` | string | **Yes** | - | Shell command to execute |
| `cwd` | string | No | `/` | Working directory for the command |

### Example
```json
{
  "type": "cmd",
  "args": {
    "cmd": "ls -la",
    "cwd": "/tmp"
  }
}
```

The Lua side runs the command using `sh -c` after changing to `cwd`:

```sh
cd "<cwd>" ; sh -c "<cmd>" > "<output file>"
```

## Response

The command output is written to a temporary file on the device. QuickJS reads that file and returns its text in `res`.

```json
{
  "type": "cmd",
  "state": 0,
  "res": "/tmp:\n drwxrwxrwx       0 tmp/\n drwxrwxrwx       0 hi/\n -rwxrwxrwx   12288 temporary.db\n",
  "code": 0
}
```

| Field | Description |
| --- | --- |
| `res` | Captured standard output from the command, returned as text |
| `code` | Exit code returned by the shell process |
| `state` | `0` when the command result was returned |

## Limitations
Since this isn't a regular shell, some stuff may not work. Such as:
- `&&` - isn't present at all, use `;`
- `stderr` capture - `2>` redirection doesn't work (instead, `stderr` gets printed into the system logs)

`os.execute()`, which this activity relies on, might get removed in upcoming system updates, because it allows dangerous modifications to the system.

## Exit Codes

Well, there are just two. `0` for success and `65280` for everything else. Find something else, I dare you :)

```json
{
  "type": "cmd",
  "state": 0,
  "res": "",
  "code": 65280
}
```