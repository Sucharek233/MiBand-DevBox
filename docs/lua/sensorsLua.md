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
  "samples": "[{\"z\":9.0575017929077,\"y\":-3.7447674274445,\"x\":-0.96191471815109},{\"z\":9.1125364303589,\"y\":-3.2949168682098,\"x\":-1.1988042593002}]"
}
```

The `samples` field is the raw text read from the output file. Lua writes a JSON array of the selected readings, so consumers should parse this string as JSON. The exact reading shape depends on the provider and sensor.

### Reading Conversion
- With the `topic` provider, structured readings are forwarded as received.
- With other providers, known sensor properties map raw value-array positions to named fields.
- If no known properties are selected, raw `values` arrays are retained.

### A bit about raw values
Usually, when reading raw values, more samples are included in one reading. This can create large readings, so using `useKnown: true` is preferred.

Non-truncated example:
```json
{
  "type": "sensorsLua",
  "state": 6,
  "samples": "[[-1.8687945604324,0.2584248483181,9.5408515930176,0,-1.9154935883975e-13,2.1019476964872e-44,-1.7419748306274,0.28474590182304,9.6126365661621,0,-1.9209145992599e-13,2.1019476964872e-44,-1.8544375896454,0.37088748812675,9.4858169555664,0,-1.9263356101223e-13,2.1019476964872e-44,-2.0697915554047,0.61256259679794,9.7346706390381,0,-1.9317566209848e-13,2.1019476964872e-44,-1.9980070590973,0.41635113954544,9.3613901138306,0],[-2.0315065383911,0.24406790733337,9.4977807998657,0,-1.9425986427096e-13,2.1019476964872e-44,-2.0267207622528,0.30867412686348,9.4212102890015,0,-1.9480196535721e-13,2.1019476964872e-44,-2.1320049762726,0.34217363595963,9.5552082061768,0,-1.9534406644345e-13,2.1019476964872e-44,-1.9956141710281,0.2656033039093,9.4977807998657,0,-1.9588616752969e-13,2.1019476964872e-44,-2.0123636722565,0.3278166949749,9.469066619873,0],[-1.993221282959,0.28953155875206,9.5073518753052,0,-1.9697036970218e-13,2.1019476964872e-44,-1.983649969101,0.50967121124268,9.5217094421387,0,-1.9751247078842e-13,2.1019476964872e-44,-1.983649969101,0.49531427025795,9.5169239044189,0,-1.9805457187466e-13,2.1019476964872e-44,-2.1224336624146,0.39242288470268,9.4403533935547,0],[-2.1200408935547,0.38045883178711,9.4834241867065,0,-1.9913877404715e-13,2.1019476964872e-44,-2.1990041732788,0.59581285715103,9.492995262146,0,-1.9968087513339e-13,2.1019476964872e-44,-2.0626132488251,0.46899324655533,9.5791368484497,0,-2.0022297621963e-13,2.1019476964872e-44,-1.9812570810318,0.46660044789314,9.6652784347534,0,-2.0076507730588e-13,2.1019476964872e-44,-2.0793628692627,0.36370903253555,9.4810314178467,0],[-2.0985054969788,0.33738797903061,9.5647802352905,0,-2.0184927947836e-13,2.1019476964872e-44,-2.1415762901306,0.77048891782761,9.4977807998657,0,-2.023913805646e-13,2.1019476964872e-44,-1.7898312807083,-0.83509504795074,9.9021673202515,0,-2.0293348165085e-13,2.1019476964872e-44,-1.993221282959,1.0839486122131,9.4403533935547,0,-2.0347558273709e-13,2.1019476964872e-44,-2.1559331417084,0.586241543293,9.2178211212158,0]]"
}
```

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