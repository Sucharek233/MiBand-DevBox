# Lua Activities
This section documents activities implemented on or backed by the Lua runtime.

## Activity Overview
| Activity | Type | Purpose | Documentation |
| :--- | :--- | :--- | :--- |
| Apps | `apps` | Manages application lists, manifests, metadata, icons, and app launching | [`apps.md`](./apps.md) |
| Terminal | `cmd` | Executes shell commands and returns captured output and exit codes | [`cmd.md`](./cmd.md) |
| I/O | `io` | Lists and modifies files and streams file contents in chunks | [`io.md`](./io.md) |
| Lua Shell | `luashell` | Executes Lua code and returns sanitized results and captured `print` output | [`luashell.md`](./luashell.md) |
| Ping | `ping` | Checks QuickJS/Lua communication and returns timing information | [`ping.md`](./ping.md) |
| Lua Sensors | `sensorsLua` | Lists Lua sensors and streams sensor readings through a file-backed bridge | [`sensorsLua.md`](./sensorsLua.md) |

## Quick Reference

### Apps
Manage installed application metadata and manifests, retrieve icons, and run applications.

Main operations:
- `list`, `writeList`
- `listApps`, `info`
- `manifest`, `writeManifest`
- `icon`, `run`

### Command
Run a shell command with an optional working directory. The response includes captured output in `res` and the process exit code in `code`.

Main arguments:
- `cmd` - Shell command to execute
- `cwd` - Working directory, defaulting to `/`

### I/O
Perform directory and file operations or transfer files through a two-stage Lua/QuickJS chunk stream.

Main operations:
- `list`, `cp`, `mv`, `rm`
- `getStream`, `chunk`, `stop`

### Lua Shell
Execute Lua code, capture `print` output, and return JSON-safe values. Special values, functions, long strings, and circular tables are represented by sanitizer markers.

### Ping
The `lua` operation measures the QuickJS-to-Lua mailbox round trip. The `qjs` operation returns an immediate QuickJS acknowledgement.

### Lua Sensors
Discover sensors and subscribe to sensor readings through Lua. Lua buffers and downsamples readings, writes them to a temporary output file, and QuickJS polls that file to emit stream messages.

Main operations:
- `list`, `listPre`
- `sub`, `unsub`