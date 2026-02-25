# Guide: Creating a Device-Specific Repository (Cedric)

## Overview

This repository (`device_motorola_msm8937-common`) is a **common device tree** that contains shared configuration and files for multiple Motorola MSM8937-based devices:
- ahannah
- **cedric** (Moto G5)
- hannah
- james
- montana
- rhannah

## Common vs Device-Specific Structure

### What Stays in Common Repository
The common repository should contain:
- Shared hardware configuration (audio, camera, GPS, sensors, etc.)
- Common firmware symlinks
- Shared overlays and properties
- Build system makefiles that apply to all devices
- Common sepolicy rules

### What Goes in Device-Specific Repository

A device-specific repository (e.g., `device_motorola_cedric`) should contain:

1. **Device-specific configuration files:**
   - `lineage_cedric.mk` - Main device makefile
   - `AndroidProducts.mk` - Product definition
   - `BoardConfig.mk` - Device-specific board configuration
   - `device.mk` - Device-specific build configuration

2. **Device-specific properties:**
   - Display resolution and density
   - Device codename
   - Model name and manufacturer
   - Device-specific features

3. **Device-specific overlays:**
   - Custom UI elements
   - Device-specific resources
   - Status bar configuration

4. **Device-specific proprietary files:**
   - `proprietary-files.txt` - List of device-specific blobs
   - `extract-files.sh` - Script to extract proprietary files
   - `setup-makefiles.sh` - Script to setup vendor makefiles

5. **Kernel configuration:**
   - Kernel defconfig name
   - Kernel cmdline parameters
   - Partition sizes and layout

## Example Cedric Repository Structure

```
device_motorola_cedric/
├── AndroidProducts.mk
├── BoardConfig.mk
├── device.mk
├── lineage_cedric.mk
├── lineage.dependencies
├── proprietary-files.txt
├── extract-files.sh
├── setup-makefiles.sh
├── overlay/
│   └── frameworks/
│       └── base/
│           ├── core/
│           │   └── res/
│           │       └── res/
│           │           └── values/
│           │               └── config.xml
│           └── packages/
│               └── SystemUI/
│                   └── res/
│                       └── values/
│                           └── dimens.xml
└── README.md
```

## Key Files to Create

### 1. lineage.dependencies
```json
[
  {
    "repository": "android_device_motorola_msm8937-common",
    "target_path": "device/motorola/msm8937-common"
  }
]
```

### 2. AndroidProducts.mk
```makefile
PRODUCT_MAKEFILES := \
    $(LOCAL_DIR)/lineage_cedric.mk

COMMON_LUNCH_CHOICES := \
    lineage_cedric-user \
    lineage_cedric-userdebug \
    lineage_cedric-eng
```

### 3. BoardConfig.mk
```makefile
# Inherit from common msm8937-common
include device/motorola/msm8937-common/BoardConfigCommon.mk

DEVICE_PATH := device/motorola/cedric

# Assertions
TARGET_OTA_ASSERT_DEVICE := cedric

# Kernel
TARGET_KERNEL_CONFIG := cedric_defconfig

# Partitions
BOARD_SYSTEMIMAGE_PARTITION_SIZE := 4294967296
BOARD_USERDATAIMAGE_PARTITION_SIZE := 25614597120

# Properties
TARGET_SYSTEM_PROP += $(DEVICE_PATH)/system.prop

# Inherit from the proprietary version
include vendor/motorola/cedric/BoardConfigVendor.mk
```

### 4. device.mk
```makefile
# Inherit from msm8937-common
$(call inherit-product, device/motorola/msm8937-common/msm8937.mk)

# Overlays
DEVICE_PACKAGE_OVERLAYS += $(LOCAL_PATH)/overlay

# Audio
PRODUCT_COPY_FILES += \
    $(LOCAL_PATH)/audio/mixer_paths.xml:$(TARGET_COPY_OUT_VENDOR)/etc/mixer_paths.xml

# Inherit from vendor
$(call inherit-product, vendor/motorola/cedric/cedric-vendor.mk)
```

### 5. lineage_cedric.mk
```makefile
# Inherit from those products. Most specific first.
$(call inherit-product, $(SRC_TARGET_DIR)/product/core_64_bit.mk)
$(call inherit-product, $(SRC_TARGET_DIR)/product/full_base_telephony.mk)

# Inherit from cedric device
$(call inherit-product, device/motorola/cedric/device.mk)

# Inherit some common Lineage stuff.
$(call inherit-product, vendor/lineage/config/common_full_phone.mk)

# Device identifier
PRODUCT_NAME := lineage_cedric
PRODUCT_DEVICE := cedric
PRODUCT_BRAND := motorola
PRODUCT_MODEL := Moto G5
PRODUCT_MANUFACTURER := Motorola

PRODUCT_GMS_CLIENTID_BASE := android-motorola

PRODUCT_BUILD_PROP_OVERRIDES += \
    PRIVATE_BUILD_DESC="cedric-user 8.1.0 OPPS28.85-13-5 4e197 release-keys"

BUILD_FINGERPRINT := motorola/cedric/cedric:8.1.0/OPPS28.85-13-5/4e197:user/release-keys
```

## Migration Steps

1. **Create the new device repository** (e.g., `device_motorola_cedric`)

2. **Identify device-specific files** from this common repository that should move:
   - Look for files in subdirectories with device-specific configurations
   - Check for any `$(TARGET_DEVICE)` conditionals in makefiles
   - Review proprietary-files.txt for device-specific blobs

3. **Set up the dependency** in `lineage.dependencies` to inherit from this common tree

4. **Copy and adapt** device-specific configurations:
   - Kernel configuration
   - Partition sizes
   - Display properties
   - Audio mixer paths (if device-specific)

5. **Test the build** to ensure everything works correctly

## Device-Specific Configurations for Cedric

Based on the current common repository, here are some configurations that would be Cedric-specific:

### Display
- Resolution: 1920x1080 (Full HD)
- Density: 420 dpi

### Partitions
- System: ~4GB
- Userdata: ~25GB
- Boot: 16MB
- Recovery: 16MB

### Kernel
- Kernel config: `cedric_defconfig`
- Kernel source: Check `lineage.dependencies` for kernel repository

## Additional Resources

- **LineageOS Device Tree Structure:** https://wiki.lineageos.org/devices/
- **Android Build System:** https://source.android.com/setup/build
- **Device Tree Examples:** Look at other Motorola device trees in the LineageOS organization

## Questions?

If you need help with:
- Identifying which files should be device-specific
- Setting up the repository structure
- Configuring the build system
- Extracting proprietary files

Please consult the LineageOS wiki or ask in the LineageOS community channels.

---

**Note:** This repository (`device_motorola_msm8937-common`) should remain as a common base that is inherited by all device-specific repositories. Do not remove common files from here unless they are truly device-specific.
