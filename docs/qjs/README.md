# QuickJS Activities

This section documents activities exposed through the QuickJS application context.

## Activity Overview

| Activity | Type | Purpose | Documentation |
| :--- | :--- | :--- | :--- |
| QuickJS Shell | `qjs` | Executes JavaScript in the application context and captures return values and `console.log` output | [`qjs.md`](./qjs.md) |
| Sensors | `sensors` | Lists available sensors and streams sensor samples | [`sensors.md`](./sensors.md) |
| System Information | `sysinfo` | Retrieves device, operating system, storage, serial, and device ID | [`sysinfo.md`](./sysinfo.md) |
| Miscellaneous | `misc` | Provides utility operations such as garbage collection | [`misc.md`](./misc.md) |
| Module Compatibility | `modules` | Checks module availability and lists exposed module functions | [`moduleCompatibility.md`](./moduleCompatibility.md) |

## Quick Reference

### QuickJS Shell

Execute JavaScript code, including `async` functions. Results are serialized into `res`, and captured `console.log` calls are returned in `logs`.

Main input:
- `code` - JavaScript source code to execute

Documented behavior includes errors and return-value sanitization for functions, `undefined`, `null`, non-finite numbers, long strings, arrays, objects, and circular references.

### Sensors

Discover sensors with `list` or `listLite`, then use `sub` and `unsub` to control sample streaming.

Main operations:
- `list`
- `listLite`
- `sub`
- `unsub`

### System Information

The `sysinfo` activity takes no arguments and returns system, storage, serial number, and device ID data.

### Miscellaneous

The `misc` activity currently documents the `gc` operation for running garbage collection.

### Module Compatibility

The `modules` activity provides two operations:
- `compat` - Checks whether one or more modules are available
- `funcs` - Lists the functions exposed by a module