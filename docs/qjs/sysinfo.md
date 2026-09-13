# Miscellaneous activity
Obtains system information.

> **Note:** Nothing is being passed to this activity

---

### Arguments
None

### Request
```json
{
  "type": "sysinfo",
}
```

### Response
```json
{
  "type": "sysinfo",
  "state": 0,
  "res": {
    "main": {
      "brand": "Vela",
      "manufacturer": "XiaoMi Vela Team",
      "product": "Xiaomi Smart Band 10",
      "osType": "NuttX",
      "osVersionName": "3.6.1",
      // ...
    },
    "storage": {
      "total": 133562368,
      "available": 107216896,
      "used": 26345472
    },
    "serial": "...",
    "deviceId": "..."
  }
}
```