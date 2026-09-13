# Apps Activity

Manage application manifests, retrieve package metadata, and perform low-level reads/writes on `apps.json`.

## Operations Overview

| Operation | Purpose |
| :--- | :--- |
| [`list`](#list) | Reads raw `apps.json` file content |
| [`writeList`](#writelist) | Overwrites raw `apps.json` file content |
| [`listApps`](#listapps) | Gets a key-value map of installed packages and names |
| [`info`](#info) | Gets detailed metadata for a specific package |
| [`manifest`](#getmanifest) | Reads the `manifest.json` for a specific package |
| [`writeManifest`](#writemanifest) | Overwrites `manifest.json` for a specific package |
| [`icon`](#icon) | Retrieves icon for a specific package |
| [`run`](#run) | Runs a specific application |

---

## list
Retrieves the raw content of the `apps.json` file.

### Arguments
None

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "list"
  }
}
```

### Response
> **Note:** The `res` property returns a stringified JSON payload.
```json
{
  "type": "apps",
  "res": "{\n  \"InstalledApps\": [ ... ]\n}",
  "state": 0
}
```

## writeList
Writes content directly to the `apps.json` file.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `content` | string | **Yes** | Stringified JSON payload |

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "writeList",
    "content": "{\"InstalledApps\": [ ... ]}"
  }
}
```

### Response
```json
{
  "type": "apps",
  "res": "Written",
  "state": 0
}
```

## listApps
Returns a mapped dictionary of all installed package IDs to their display names.

### Arguments
None

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "listApps"
  }
}
```

### Response
```json
{
  "type": "apps",
  "res": {
    "com.sucharek.devbox": "DevBox",
    // ...
  },
  "state": 0
}
```

## info
Retrieves details and metadata for a specific application.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `pkg` | string | **Yes** | Package name |

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "info",
    "pkg": "com.sucharek.devbox"
  }
}
```

### Response
```json
{
  "type": "apps",
  "res": {
    "package": "com.sucharek.devbox",
    "names": [
      {
        "lang": "defaults",
        "value": "DevBox"
      }
    ],
    "icon": "common/logo.png",
    // ...
  },
  "state": 0
}
```

## manifest
Retrieves the `manifest.json` configuration for a specific application.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `pkg` | string | **Yes** | Package name |

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "manifest",
    "pkg": "com.sucharek.devbox"
  }
}
```

### Response
> **Note:** The `res` property returns a stringified JSON payload.
```json
{
  "type": "apps",
  "res": "{\n  \"package\": \"com.sucharek.devbox\", ...}",
  "state": 0
}
```

## writeManifest
Writes content directly to the `manifest.json` file of a specific application.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `pkg` | string | **Yes** | Package name |
| `content` | string | **Yes** | Stringified JSON payload |

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "writeManifest",
    "pkg": "com.sucharek.devbox",
    "content": "{\n  \"package\": \"com.sucharek.devbox\", ...}"
  }
}
```

### Response
> **Note:** The `res` property returns a stringified JSON payload.
```json
{
  "type": "apps",
  "res": "Written",
  "state": 0
}
```

## icon

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `pkg` | string | **Yes** | Package name |

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "icon",
    "pkg": "com.sucharek.devbox"
  }
}
```

### Response
> **Note:** The `res` property returns the icon in base64 format.
```json
{
  "type": "apps",
  "res": "iVBORw0KGgoAAAANSUhEUgA...",
  "state": 0
}
```

## run
Starts a specific application. This is handled purely in QuickJS.

**Starting another app will close the current one, so nothing will get returned!**

**In newer firmwares this doesn't work!** It returns `router.push: changing app is forbidden!`.

### Arguments
| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `pkg` | string | **Yes** | Package name |

### Request
```json
{
  "type": "apps",
  "args": {
    "type": "run",
    "pkg": "com.sucharek.devbox"
  }
}
```

### Response
None