# MiBand DevBox

<p align="center">
	<img src="images/icon-1024.png" alt="MiBand DevBox icon" width="180">
</p>

<p align="center">
	A toolbox for exploring, debugging and messing with applications on Xiaomi wearable devices.
</p>

<p align="center">
	<a href="#what-it-does">Features</a> ·
	<a href="#documentation">Documentation</a> ·
	<a href="#contributing">Contributing</a>
</p>

## What It Is
MiBand DevBox is an open-source toolbox for Xiaomi wearable devices. It connects an Android companion app with a QuickJS Quick App and a Lua watchface, making it possible to inspect the device, run code, work with files, access sensors, and experiment with the watch environment from one place.

The project is currently tested with:

- Xiaomi Smart Band 10
- VelaOS Emulator

Testers and contributors are welcome.

## What It Does
- Executes JavaScript in the Quick App context
- Executes Lua code on the watch
- Runs shell commands and returns their output
- Lists, copies, moves, removes, and streams files
- Discovers sensors and streams sensor readings
- Reads device and system information
- Inspects installed apps, manifests, metadata, and icons
- Checks module compatibility and available functions
- Measures communication between the Android, QuickJS, and Lua layers
- Provides small development utilities such as garbage collection

## Project Components
MiBand DevBox consists of three open-source applications working together:

| Component | Role | Repository |
| :--- | :--- | :--- |
| Android app | Companion app ensuring communication between itself and the QuickApp | [Android app](https://github.com/Sucharek233/MiBand-DevBox-Android) |
| QuickJS Quick App | JavaScript part | [QuickJS Quick App](https://github.com/Sucharek233/MiBand-DevBox-QuickApp) |
| Lua watchface | Lua part - here's where the magic happens | [Lua watchface](https://github.com/Sucharek233/MiBand-DevBox-Watchface) |

## Documentation
The protocol and activities are documented by runtime:

- [QuickJS activities](docs/qjs/README.md)
- [Lua activities](docs/lua/README.md)
- [General documentation](docs/README.md)

The activity documentation includes request formats, response payloads, options, streaming behavior, error states, and implementation specific details.

## How it all works together
```text
Android app
		│
		│ device communication
		▼
QuickJS Quick App
		│
		│ mailbox activities
		▼
Lua watchface
		│
		▼
Sensors, files, apps, system, and shell
```

The Android app communicates with the QuickApp. QuickJS handles its own requests, and forwards others to Lua.

## Screenshots
<details>
<summary>Wearable app screenshots</summary>

### QuickJS Quick App
<img src="images/qjs.png" alt="QuickJS Quick App" width="220">

### Lua watchface
<img src="images/lua.png" alt="Lua watchface" width="220">

</details>

<details>
<summary>Android Dashboard screenshots</summary>

### Dashboard
<img src="images/android/dashboard/qjs.png" alt="QuickJS Dashboard" width="220">
<img src="images/android/dashboard/lua.png" alt="Lua Dashboard" width="220">

### Pings
<img src="images/android/mgmt/ping/qjs.png" alt="QuickJS ping results" width="220">
<img src="images/android/mgmt/ping/lua.png" alt="Lua ping results" width="220">

### Management
<img src="images/android/mgmt/logs.png" alt="System logs" width="220">
<img src="images/android/mgmt/websocketapi.png" alt="WebSocket API" width="220">


</details>

<details>
<summary>Android Lua activity screenshots</summary>

### Terminal and Lua shell
<img src="images/android/lua/terminal.png" alt="Lua terminal" width="220">
<img src="images/android/lua/lua-shell.png" alt="Lua shell" width="220">

### Device information
<img src="images/android/lua/device-info/system.png" alt="Lua system information" width="220">
<img src="images/android/lua/device-info/partitions.png" alt="Lua partitions" width="220">
<img src="images/android/lua/device-info/props.png" alt="Lua system properties" width="220">

### File manager
<img src="images/android/lua/file-manager/list.png" alt="Lua file manager" width="220">
<img src="images/android/lua/file-manager/streaming.png" alt="Lua file manager" width="220">

### Sensors
<img src="images/android/lua/sensors/list.png" alt="Lua sensor list" width="220">
<img src="images/android/lua/sensors/all.png" alt="Lua sensor list" width="220">
<img src="images/android/lua/sensors/compass.png" alt="Lua compass sensor" width="220">

### Apps
<img src="images/android/lua/apps/list.png" alt="Lua apps list" width="220">
<img src="images/android/lua/apps/details.png" alt="Lua app details" width="220">
<img src="images/android/lua/apps/manifest.png" alt="Lua app manifest" width="220">

</details>

<details>
<summary>Android QuickJS activity screenshots</summary>

### JavaScript shell
<img src="images/android/qjs/qjs-shell.png" alt="QuickJS shell" width="220">

### Module Compatibility
<img src="images/android/qjs/modules.png" alt="QuickJS modules" width="220">

### Device information
<img src="images/android/qjs/device-info.png" alt="QuickJS device information" width="220">

### Sensors
<img src="images/android/qjs/sensors/list.png" alt="QuickJS sensor list" width="220">
<img src="images/android/qjs/sensors/accel.png" alt="QuickJS accelerometer" width="220">

</details>

## Compatibility
| Device or environment | Status |
| :--- | :--- |
| Xiaomi Smart Band 10 | Tested |
| VelaOS emulator | Tested |

Compatibility may vary across firmware versions. Please include the device model, firmware version, and emulator details when reporting a compatibility result.

## Contributing
Contributions, testing, device reports, documentation improvements, and compatibility patches are welcome.

Useful contribution areas include:

- Testing on additional Xiaomi wearable devices
- Testing across firmware versions
- Improving the Android companion experience
- Adding QuickJS or Lua activities
- Expanding sensor and module support
- Improving documentation and examples
- Sharing screenshots and reproducible device behavior

When reporting an issue, include the device or emulator, firmware version, component involved, steps to reproduce, and any relevant response or error payload.

## Use of AI in this project
GenAI was used quite a bit while making this (only code, not resources - all icons and images were made by me). It recommended features, improved structure, drafted READMEs etc.

Since I don't know anything about Android developement, the Android app was 100% vibecoded. I initially steered it to make stuff my way, but overtime, I just let it do its thing. Just so you know, if you make an issue in the Android app repo, it'll be most likely fixed by genai.

I didn't spend a single penny on this though :) I'm a freeloader of genai :)

## License
This project is licensed under the GPL v3.0 license.