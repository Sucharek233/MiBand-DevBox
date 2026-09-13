# Sensors Activity
Discover available sensors and stream sensor data.

## Operations Overview

| Operation | Purpose |
| :--- | :--- |
| [`list`](#list) | Gets detailed information about available sensors |
| [`listLite`](#listlite) | Gets a simple list of available sensors |
| [`sub`](#sub) | Subscribes to a sensor and starts streaming samples |
| [`unsub`](#unsub) | Stops the active sensor subscription |

---

## list
Returns detailed information about the sensors available on the device.

### Arguments
None

### Request
```json
{
	"type": "sensors",
	"args": {
		"type": "list"
	}
}
```

### Response
> **Note:** The `res` property contains the sensor list.
```json
{
	"type": "sensors",
	"res": [ ... ],
	"state": 0
}
```

## listLite
Returns a simple list of the sensors available on the device.

### Arguments
None

### Request
```json
{
	"type": "sensors",
	"args": {
		"type": "listLite"
	}
}
```

### Response
> **Note:** The `res` property contains the simple sensor list.
```json
{
	"type": "sensors",
	"res": [ ... ],
	"state": 0
}
```

## sub
Subscribes to a sensor and begins collecting samples.

Only one sensor subscription can be active at a time. The screen is kept on while sensor streaming is active.

### Arguments
| Field | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `sensor` | string | **Yes** | - | Sensor name |
| `streamEntries` | number | No | `10` | Maximum number of samples sent in each stream response |
| `sendInterval` | number | No | `1000` | Interval between stream responses, in milliseconds |

### Request
```json
{
	"type": "sensors",
	"args": {
		"type": "sub",
		"sensor": "Accelerometer",
		"streamEntries": 10,
		"sendInterval": 1000
	}
}
```

### Response
```json
{
	"type": "sensors",
	"state": 0,
	"msg": "Subscribed"
}
```

### Stream Response
```json
{
	"type": "sensors",
	"state": 6,
	"samples": [ ... ]
}
```

> **Note:** Sample values and their structure are provided by the selected sensor.

## unsub
Stops the active sensor subscription.

### Arguments
None

### Request
```json
{
	"type": "sensors",
	"args": {
		"type": "unsub"
	}
}
```

### Response
```json
{
	"type": "sensors",
	"state": 0,
	"msg": "Unsubscribed"
}
```

## Errors

Unknown operations, invalid arguments, provider failures, and subscription state conflicts return an error response.

```json
{
	"type": "sensors",
	"state": 1,
	"msg": "Already subscribed"
}
```

Subscription errors:
- `Already subscribed` when `sub` is called while another subscription is active
- `Not supported` when `sub` tries subscribing to a non existant sensor
- `Not subscribed` when `unsub` is called without an active subscription
- `Sensor object not found` when `unsub` is called, but the subscribed sensor object is missing