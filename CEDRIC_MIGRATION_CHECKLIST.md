# Cedric Device Repository Migration Checklist

## Purpose
This checklist helps identify what files and configurations should be moved from the common MSM8937 device tree into a device-specific Cedric repository.

## Current Status
This repository (`device_motorola_msm8937-common`) currently supports multiple devices:
- ahannah
- **cedric** (Moto G5)
- hannah
- james
- montana
- rhannah

## Files to Identify for Device-Specific Repository

### 1. Device-Specific Conditionals
The following conditional statements in makefiles indicate device-specific code:

**In Android.mk (line 18):**
```makefile
ifneq ($(filter ahannah cedric hannah james montana rhannah,$(TARGET_DEVICE)),)
```
This includes Cedric, so this file can stay in common.

**In msm8937.mk:**
- Line 187: `ifeq ($(filter ahannah rhannah,$(TARGET_DEVICE)),)` - Excludes ahannah/rhannah
  - **Action:** LineageActions is included for cedric - this can stay in common
  
- Line 246: `ifeq ($(filter ahannah james rhannah,$(TARGET_DEVICE)),)` - Excludes ahannah/james/rhannah
  - **Action:** Compass and gyroscope sensors ARE included for cedric - document this
  
- Line 252: `ifeq ($(filter james rhannah,$(TARGET_DEVICE)),)` - Excludes james/rhannah
  - **Action:** Vulkan support IS included for cedric - document this

### 2. Files That Should Be in Cedric Repository

#### Required Files (Must Create):
- [ ] `AndroidProducts.mk` - Defines the product
- [ ] `BoardConfig.mk` - Board-specific configuration
- [ ] `device.mk` - Device-specific build rules
- [ ] `lineage_cedric.mk` - Product definition
- [ ] `lineage.dependencies` - Dependency on this common tree
- [ ] `README.md` - Device documentation
- [ ] `vendorsetup.sh` - Build environment setup (optional)

#### Device-Specific Configuration Files:
- [ ] `overlay/` - Cedric-specific UI overlays
  - Display resolution: 1920x1080
  - DPI: 420
  - Status bar configuration
  - Navigation bar configuration
  
- [ ] Device-specific audio configuration (if any):
  - `mixer_paths.xml` - Audio routing for Cedric's specific audio hardware
  - Check if Cedric has unique speaker/microphone configuration
  
- [ ] Device-specific camera configuration (if any):
  - Check if Cedric has different camera sensors than other devices
  
- [ ] Kernel configuration:
  - Kernel defconfig name: `cedric_defconfig`
  - Kernel command line parameters
  - Device Tree Blob (DTB) configuration

#### Partition Configuration (BoardConfig.mk):
- [ ] System partition size
- [ ] Userdata partition size
- [ ] Boot partition size
- [ ] Recovery partition size
- [ ] Cache partition size
- [ ] Vendor partition size (if applicable)

#### Proprietary Files:
- [ ] `proprietary-files.txt` - Cedric-specific blobs
  - Review the current `proprietary-files.txt` in common
  - Identify which blobs are Cedric-specific
  - Common blobs should stay in msm8937-common
  
- [ ] `extract-files.sh` - Script to extract blobs from Cedric device
- [ ] `setup-makefiles.sh` - Script to generate vendor makefiles

### 3. Properties to Configure

#### Device Properties (device.mk or system.prop):
```properties
# Device
ro.product.device=cedric
ro.product.model=Moto G5
ro.product.brand=motorola
ro.product.manufacturer=Motorola

# Display
ro.sf.lcd_density=420

# Fingerprint (for OTA updates)
ro.build.fingerprint=motorola/cedric/cedric:8.1.0/OPPS28.85-13-5/4e197:user/release-keys
```

### 4. Overlays to Create

#### frameworks/base/core/res/res/values/config.xml
- Screen brightness values
- Battery capacity (Cedric: 2800 mAh)
- Hardware keys configuration
- Notification LED configuration (if applicable)

#### frameworks/base/packages/SystemUI/res/values/dimens.xml
- Status bar height
- Navigation bar height (if on-screen buttons)

### 5. Investigation Needed

To properly split the device tree, investigate:

1. **Audio Configuration:**
   - [ ] Does Cedric have a unique `mixer_paths.xml`?
   - [ ] Check audio HAL configuration in `/audio/` directory
   - Current: Audio files are in common - determine if Cedric-specific

2. **Camera Configuration:**
   - [ ] Identify Cedric's camera sensors
   - [ ] Check if camera blob list differs from other devices

3. **Proprietary Blobs:**
   - [ ] Compare proprietary-files.txt with actual Cedric device
   - [ ] Identify device-specific firmware files

4. **Kernel:**
   - [ ] Confirm kernel defconfig name for Cedric
   - [ ] Check device tree blob (DTB) requirements

5. **SELinux Policies:**
   - [ ] Review sepolicy/ directory
   - [ ] Determine if Cedric needs specific SELinux rules

## Device Specifications (Cedric - Moto G5)

### Hardware
- **SoC:** Qualcomm MSM8937 (Snapdragon 430)
- **CPU:** Octa-core 1.4 GHz Cortex-A53
- **GPU:** Adreno 505
- **RAM:** 2GB / 3GB variants
- **Storage:** 16GB / 32GB variants
- **Display:** 5.0" 1920x1080 (Full HD), 441 ppi
- **Camera:** 13 MP rear, 5 MP front
- **Battery:** 2800 mAh
- **Sensors:** Accelerometer, Gyroscope, Proximity, Compass, Fingerprint

### Features to Configure
- [x] Gyroscope sensor support (included in common)
- [x] Compass sensor support (included in common)  
- [x] Vulkan graphics support (included in common)
- [x] LineageActions (included in common)
- [ ] Fingerprint sensor configuration
- [ ] Display DPI (420)
- [ ] Battery capacity (2800 mAh)

## Steps to Create Cedric Repository

1. **Create new repository:** `device_motorola_cedric`

2. **Copy template files** from similar devices or use the examples in `DEVICE_SPECIFIC_GUIDE.md`

3. **Set up inheritance:**
   ```makefile
   # In device.mk
   $(call inherit-product, device/motorola/msm8937-common/msm8937.mk)
   ```

4. **Configure device properties** specific to Cedric

5. **Create device-specific overlays** for UI customization

6. **Extract and list proprietary files** specific to Cedric

7. **Test build** using:
   ```bash
   source build/envsetup.sh
   lunch lineage_cedric-userdebug
   make -j$(nproc --all)
   ```

## Common Tree Maintenance

**What stays in msm8937-common:**
- ✅ Shared audio configuration
- ✅ Shared camera HAL
- ✅ Common firmware symlinks
- ✅ Shared GPS configuration
- ✅ Common sepolicy rules
- ✅ Shared build system configuration
- ✅ Common vendor blobs used by all devices

**What moves to device_motorola_cedric:**
- ❌ Cedric-specific product definition
- ❌ Cedric-specific board configuration
- ❌ Cedric-specific overlays
- ❌ Cedric-specific partition sizes
- ❌ Cedric-specific kernel configuration
- ❌ Cedric-specific proprietary files

## Resources

- **Cedric XDA Thread:** https://forum.xda-developers.com/moto-g5
- **LineageOS Wiki:** https://wiki.lineageos.org/
- **Similar Device Trees:** Look at other Motorola devices in LineageOS
- **Motorola MSM8937 Common:** This repository serves as the base

## Notes

- The common repository should remain minimal and only contain truly shared components
- Device-specific configurations should always go in the device-specific repository
- When in doubt, check how other similar devices are structured in LineageOS

## Summary

To create a proper Cedric device repository:
1. Start with the templates provided in `DEVICE_SPECIFIC_GUIDE.md`
2. Inherit from this common tree via `lineage.dependencies`
3. Add Cedric-specific configurations
4. Extract Cedric-specific proprietary files
5. Test and iterate

The common repository (`device_motorola_msm8937-common`) should not be moved or deleted - it serves as a shared base for all MSM8937 Motorola devices.
