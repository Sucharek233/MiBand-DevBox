# Service API & Activities Architecture

This document describes the messaging architecture for the wearable application ecosystem (Android App <-> Mi Band Runtime).

## Overview

Communication between the Android host application and the band relies on an **IPC messaging protocol**. Requests are routed through the QuickApp on the band, which either handles the task directly or hands it over to Lua.

```
┌───────────────┐       Interconnect       ┌────────────────────────┐
│  Android App  │ <──────────────────────> │ QuickJS Runtime (Band) │
└───────────────┘                          └───────────┬────────────┘
                                                       │
                                                    Invokes
                                                       │
                                                       ▼
                                           ┌────────────────────────┐
                                           │   Lua Engine (Face)    │
                                           └────────────────────────┘
```

## API Connection & Access
For easy API access, the Android companion app hosts a WebSocket server.

### Enabling the WebSocket Server
1. Open the Android application.
2. Connect to your wearable device.
3. Open the **WebSocket API** management workspace.
4. Tap **Start** to initialize the server.

Once running, you can connect with any WebSocket client.

## Engine Roles & Capabilities
| Target Engine | Primary Role | Features & Responsibilities | Reference |
| :--- | :--- | :--- | :--- |
| **QuickJS** | Core System & IPC | • Android-to-band message routing<br>• Core app logic | [`/qjs`](./qjs/README.md) |
| **Lua** | Feature Extension | • Extra functions<br>• Shell and unrestricted filesystem access | [`/lua`](./lua/README.md) |

## Message Format
All messages transmitted between the Android companion app and the band use a single structure.

### Request
```json
{
  "type": "<activity>",
  "args": {
    "type": "<action>",
    "...": "action_specific_payload"
  }
}

```

* **`type` (outer):** The target activity domain (e.g., `apps`, `system`, `watchface`).
* **`args.type` (inner):** The specific action to execute.

### Response
```json
{
  "type": "<activity>",
  "state": 0,
  "res": "<payload_or_object>"
}
```

* **`state`:** Execution status code
* **`res`:** Return payload (can be a JSON object, raw string, or boolean flag depending on the action).

### Error Response
```json
{
  "type": "apps",
  "state": 4,
  "msg": "router.push: changing app is forbidden!",
  "stack": "    at push (@aiot/framework:1)\n    at runApp\n    at run\n    at handle\n    at messageReceived\n"
}
```

* **`msg`:** Error message
* **`stack`:** Error stacktrace

> **Note:** In some places, error is returned as **res** instead of **msg** and **stack**

### Response Codes
| Value | State |
| ---: | --- |
| `0` | `DONE` |
| `1` | `IDLE` |
| `2` | `PENDING` |
| `3` | `RUNNING` |
| `4` | `ERROR` |
| `5` | `TIMEOUT` |
| `6` | `STREAM` |