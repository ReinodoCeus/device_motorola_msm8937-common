# Creating a Device-Specific Repository for Cedric

## Quick Start

If you're looking to create a device-specific repository for the Motorola Moto G5 (codename: cedric), you're in the right place!

This document provides a quick overview and links to detailed guides.

## Understanding the Structure

This repository (`device_motorola_msm8937-common`) is a **common device tree** that contains shared configuration for multiple Motorola MSM8937-based devices, including Cedric (Moto G5).

### The Android Device Tree Pattern

In Android build systems like LineageOS, devices are typically organized as:

```
device/motorola/
├── msm8937-common/          ← This repository (shared code)
├── cedric/                  ← Device-specific repository (what you need to create)
├── hannah/                  ← Another device
└── montana/                 ← Another device
```

## What You Need to Do

### Option 1: You Want to Build Cedric
If you want to build LineageOS for Cedric, you need:
1. This common repository (`device_motorola_msm8937-common`)
2. A device-specific repository (`device_motorola_cedric`)
3. Vendor blobs (`vendor_motorola_cedric`)

The device-specific repository inherits from this common one and adds Cedric-specific configuration.

### Option 2: You Want to Create a Cedric Repository
If you're creating a new device repository for Cedric from scratch:

**Read these guides in order:**
1. 📖 **[DEVICE_SPECIFIC_GUIDE.md](./DEVICE_SPECIFIC_GUIDE.md)** - Complete guide on structure and examples
2. ✅ **[CEDRIC_MIGRATION_CHECKLIST.md](./CEDRIC_MIGRATION_CHECKLIST.md)** - Detailed checklist of what to configure

## Key Points

### ❌ Do NOT Move or Delete This Repository
This common repository should **stay as is**. It's a shared base for multiple devices.

### ✅ Create a New Repository Instead  
Create a new repository (e.g., `device_motorola_cedric`) that:
- Inherits from this common tree
- Adds Cedric-specific configurations
- Contains device-specific files only

### What Goes Where?

**In the Common Repository (HERE):**
- Shared hardware configurations
- Common firmware and drivers
- Build system code used by all MSM8937 devices

**In the Device-Specific Repository (NEW):**
- Product definition (`lineage_cedric.mk`)
- Board configuration (`BoardConfig.mk`)
- Partition sizes
- Kernel configuration
- Device-specific overlays
- Device-specific properties

## Quick Example

### In your new `device_motorola_cedric` repository:

**lineage.dependencies:**
```json
[
  {
    "repository": "android_device_motorola_msm8937-common",
    "target_path": "device/motorola/msm8937-common"
  }
]
```

**device.mk:**
```makefile
# Inherit from msm8937-common
$(call inherit-product, device/motorola/msm8937-common/msm8937.mk)

# Cedric-specific configurations
PRODUCT_DEVICE := cedric
PRODUCT_NAME := lineage_cedric
PRODUCT_BRAND := motorola
PRODUCT_MODEL := Moto G5
PRODUCT_MANUFACTURER := Motorola
```

## Device Information: Moto G5 (Cedric)

- **Display:** 5.0" Full HD (1920x1080), 420 dpi
- **SoC:** Qualcomm Snapdragon 430 (MSM8937)
- **RAM:** 2GB / 3GB
- **Storage:** 16GB / 32GB
- **Battery:** 2800 mAh
- **Camera:** 13 MP rear, 5 MP front
- **Sensors:** Accelerometer, Gyroscope, Proximity, Compass, Fingerprint

## Need More Help?

1. **For complete structure and file templates:**
   → Read [DEVICE_SPECIFIC_GUIDE.md](./DEVICE_SPECIFIC_GUIDE.md)

2. **For a detailed migration checklist:**
   → Read [CEDRIC_MIGRATION_CHECKLIST.md](./CEDRIC_MIGRATION_CHECKLIST.md)

3. **For examples:**
   - Look at other Motorola devices in LineageOS
   - Check the [LineageOS Wiki](https://wiki.lineageos.org/)

## Summary

To answer "how do I move this into the Cedric repo?":

1. **Don't move this repository** - it stays as a common base
2. **Create a new `device_motorola_cedric` repository**
3. **Inherit from this common tree** using `lineage.dependencies`
4. **Add Cedric-specific configurations** as shown in the guides
5. **Keep shared code here**, device-specific code there

---

**Good luck with your build! 🚀**

For questions, refer to the LineageOS community or XDA forums for the Moto G5.
