# Lua Sensors Activity
List sensors and subscribe to sensor data through the Lua sensor provider. This activity bridges Lua sensor buffering to QuickJS by polling a temporary output file.

## Operations Overview
| Operation | Purpose |
| :--- | :--- |
| [`list`](#list) | Lists all available sensors |
| [`listPre`](#listpre) | Lists predefined sensors with known properties |
| [`sub`](#sub) | Subscribes to a Lua sensor and starts streaming data |
| [`unsub`](#unsub) | Stops the active subscription and removes buffered output |

The activity type is `sensorsLua`. The screen is kept on while the activity is active.

---

## list
Returns all sensors reported by the Lua sensor information provider.

### Arguments
None

### Request
```json
{
  "type": "sensorsLua",
  "args": {
    "type": "list"
  }
}
```

### Response
```json
{
  "type": "sensorsLua",
  "state": 0,
  "res": [
    "sensor_accel0",
    "sensor_gyro0",
    "sensor_light0",
    // ...
  ]
}
```

## listPre
Returns the predefined sensors known to the Lua sensor information provider. Predefined entries can include properties used to convert raw sensor value arrays into named values.

### Arguments
None

### Request
```json
{
  "type": "sensorsLua",
  "args": {
    "type": "listPre"
  }
}
```

### Response
```json
{
  "type": "sensorsLua",
  "state": 0,
  "res": {
    "gyro": {
      "available": true,
      "props": ["x", "y", "z"],
      "name": "Gyroscope",
      "path": "sensor_gyro0"
    },
    "light": {
      "available": true,
      "props": ["lx"],
      "name": "Light Sensor",
      "path": "sensor_light0"
    },
    // ...
  }
}
```

## sub
Subscribes to a sensor using the Lua provider and starts forwarding readings to QuickJS.

Only one Lua sensor subscription can be active at a time. Lua collects readings in a buffer and flushes at `sendInterval`. Each flush contains at most `streamEntries` readings. If more readings are buffered, they are evenly downsampled to that limit.

### Arguments
| Field | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `sensor` | string | **Yes** | - | Sensor name |
| `provider` | string | No | `file` | Sensor provider implementation - `file` or `topic` |
| `sendInterval` | number | No | `1000` | Lua flush period and QuickJS output-file polling period, in milliseconds |
| `useKnown` | boolean | No | `true` | Use predefined sensor properties when the provider is `file` |
| `dataPollPeriod` | number | No | `80` | Sensor provider polling period, in milliseconds |
| `streamEntries` | number | No | `10` | Maximum readings written during each Lua flush |

> **Note:** The `topic` provider mostly only works for the accelerometer and gyroscope. Subscribing to other sensors will cause the wearable to halt or crash and reboot.

### Request
```json
{
  "type": "sensorsLua",
  "args": {
    "type": "sub",
    "sensor": "sensor_accel0",
    "provider": "file",
    "sendInterval": 1000,
    "useKnown": true,
    "dataPollPeriod": 80,
    "streamEntries": 10
  }
}
```

### Response
```json
{
	"type": "sensorsLua",
	"state": 0,
	"res": "Subscribed"
}
```

### Stream Response

QuickJS polls the Lua output file at `sendInterval`. When the file contains new data, it sends a `6` response:

```json
{
  "type": "sensorsLua",
  "state": 6,
  "samples": "[{\"x\":1.2,\"y\":0.4,\"z\": 3.0}]"
}
```

The `samples` field is the raw text read from the output file. Lua writes a JSON array of the selected readings, so consumers should parse this string as JSON. The exact reading shape depends on the provider and sensor.

### Reading Conversion
- With the `topic` provider, structured readings are forwarded as received.
- With other providers, known sensor properties map raw value-array positions to named fields.
- If no known properties are selected, raw `values` arrays are retained.

## unsub
Stops the active Lua sensor provider, flushes remaining buffered readings, stops QuickJS polling, and deletes the temporary output file.

### Arguments
None

### Request
```json
{
  "type": "sensorsLua",
  "args": {
    "type": "unsub"
  }
}
```

### Response
```json
{
  "type": "sensorsLua",
  "state": 0,
  "res": "Unsubscribed"
}
```

## Errors

Unknown operations, invalid arguments, provider failures, and subscription state conflicts return state `4`.

```json
{
  "type": "sensorsLua",
  "state": 4,
  "res": "Sensor not found"
}
```

Errors:
- `Already subscribed` when `sub` is called while a sensor is active.
- `Missing sensor name` when the sensor argument is unavailable to Lua.
- `Sensor not found` when `/dev/uorb/<sensor>` does not exist.
- `Not subscribed` when `unsub` is called without an active sensor.