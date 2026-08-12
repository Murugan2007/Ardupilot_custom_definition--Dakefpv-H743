# ArduPilot Custom Hardware Definition — DakeFPV H743

Unofficial ArduPilot `hex` target and pre-compiled firmware for the **DakeFPV H743** flight controller (STM32H743 MCU), tweaked since no official ArduPilot build was available from the manufacturer or the main firmware server.

## Background

The DakeFPV H743 had no upstream ArduPilot support at the time of writing. This repo is the result of a community effort (with a fellow builder from r/ArduPilot) to reverse-engineer the pinout and DMA mapping and produce a working `hwdef.dat` target, plus pre-compiled `.apj` firmware files.

## What's Working

| Subsystem | Status |
|---|---|
| UART ports (all) | ✅ Mapped and verified |
| OSD | ✅ Working |
| IMU | ✅ Running |
| Barometer | ✅ Running |
| Flash logging | ✅ Verified |
| DShot outputs | ✅ Clean timer mapping |
| Flight test | ✅ Passed |

## Firmware Notes

The included `.hex` was modified from the base ArduPilot build to add:

- **LSM6x Series** IMU driver support
- **DPS310 / DPS280** barometer driver support
- **Bluejay** ESC firmware for the paired ESC

DShot output wasn't reaching the ESC on the initial build — flashing the ESC with **Bluejay** resolved it. If you hit the same "no signal to ESC" issue on DShot, this is the fix that worked here; it's not a stock requirement for the FC firmware itself, just what got motors spinning on this setup.

> ⚠️ Because this is a hand-built `hwdef` and a modified `.hex`, flash it at your own risk. Always verify sensor orientation, DShot output mapping, and OSD behavior on your own unit before flying.

## Repo Contents

```
.
├── hwdef/
│   └── DakeFPV-H743/
│       └── hwdef.dat        # Custom hardware definition
├── firmware/
│   ├── arducopter_dakefpv_h743.apj
│   └── arducopter_dakefpv_h743_bluejay.hex
└── README.md
```

## Installation

1. Download the appropriate `.apj` (or the modified `.hex`) from the [`firmware/`](./firmware) folder.
2. Connect the DakeFPV H743 via USB in bootloader mode.
3. Flash using Mission Planner, QGroundControl, or `dfu-util`, depending on your workflow.
4. On first boot, verify:
   - IMU is detected and calibrates cleanly
   - Baro readings are stable
   - All UARTs enumerate as expected for your peripherals (GPS, RX, telemetry, etc.)
   - DShot outputs respond correctly on the intended motor channels
   - OSD displays correctly

## Building From Source

If you'd rather build it yourself instead of using the pre-compiled binaries:

```bash
git clone https://github.com/ArduPilot/ardupilot.git
cd ardupilot
git submodule update --init --recursive
cp -r /path/to/this/repo/hwdef/DakeFPV-H743 libraries/AP_HAL_ChibiOS/hwdef/
./waf configure --board DakeFPV-H743
./waf copter
```

## Known Limitations / TODO

- [ ] Not yet submitted upstream to the official ArduPilot hwdef repo
- [ ] Additional flight testing across different frame sizes welcome
- [ ] Feedback on DMA conflicts or edge-case peripheral combos appreciated

## Contributing

Pull requests, pinout corrections, and test reports from other DakeFPV H743 owners are welcome — this was built through community reverse-engineering, and more flight data only makes the target more reliable for everyone.

## Disclaimer

This is an unofficial, community-built target. It is not supported by DakeFPV or the official ArduPilot project. Use on your own aircraft at your own risk.

## License

This project follows ArduPilot's GPLv3 license. See [LICENSE](./LICENSE) for details.
