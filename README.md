Copyright (C) 2019 The LineageOS Project

Device configuration for Motorola MSM8937 Devices
==================================================

This is a **common device tree** for Motorola devices based on the MSM8937 chipset.

## Supported Devices

This common tree supports the following devices:
- **ahannah** - Moto E4 Plus (XT1770/XT1771/XT1772/XT1773)
- **cedric** - Moto G5 (XT1670/XT1671/XT1672/XT1675/XT1676)
- **hannah** - Moto G5S (XT1790/XT1791/XT1792/XT1793/XT1794/XT1795/XT1796/XT1797)
- **james** - Moto G6 Play (XT1922)
- **montana** - Moto G5S Plus (XT1802/XT1803/XT1804/XT1805/XT1806)
- **rhannah** - Moto G5S (XT1799) (India variant)

## Structure

This repository contains shared code and configurations common to all the devices listed above. Each device has its own device-specific repository that inherits from this common tree.

### Relationship with Device-Specific Repositories

```
device/motorola/msm8937-common/  ← This repository (shared code)
    ↑ inherited by
device/motorola/cedric/          ← Device-specific code for Moto G5
device/motorola/hannah/          ← Device-specific code for Moto G5S
device/motorola/montana/         ← Device-specific code for Moto G5S Plus
...etc
```

## Creating a Device-Specific Repository

**Looking to create a device repository for Cedric (Moto G5)?**

📖 See **[CREATING_CEDRIC_REPO.md](./CREATING_CEDRIC_REPO.md)** for a quick start guide.

For more detailed information:
- **[DEVICE_SPECIFIC_GUIDE.md](./DEVICE_SPECIFIC_GUIDE.md)** - Complete guide with examples and file templates
- **[CEDRIC_MIGRATION_CHECKLIST.md](./CEDRIC_MIGRATION_CHECKLIST.md)** - Detailed checklist for creating a Cedric repository

## What's Included in This Common Tree

- Audio configuration and HAL
- Bluetooth configuration
- Camera HAL and configuration
- Display and graphics configuration
- GPS/GNSS configuration
- Kernel build configuration
- Media codecs and profiles
- Sensors HAL
- SELinux policies
- Vendor interface configurations
- WiFi configuration
- Common firmware symlinks
- Common overlays

## Dependencies

See `lineage.dependencies` for required dependencies:
- Kernel: `android_kernel_motorola_msm8953`
- BSON library: `android_external_bson`
- QC system components: `android_system_qcom`

## Building

This common tree is not built directly. Instead, it's inherited by device-specific trees.

To build for a specific device:
1. Set up your build environment
2. Sync the device-specific repository (which will pull this common tree as a dependency)
3. Run the build commands for that specific device

Example for Cedric:
```bash
source build/envsetup.sh
lunch lineage_cedric-userdebug
make -j$(nproc --all)
```

## Contributing

When contributing to this repository, ensure that your changes:
- Apply to **all** supported devices, or
- Are properly conditioned with device-specific checks (e.g., `ifeq ($(filter cedric montana,$(TARGET_DEVICE)),)`)

Device-specific changes should go in the device-specific repository, not here.

## License

This project is licensed under Apache 2.0. See individual files for copyright information.
