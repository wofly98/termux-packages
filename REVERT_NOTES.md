# Revert Customization from com.xpmall back to com.termux Notes

This document records the details of reverting the custom package name namespace change from `com.xpmall` back to `com.termux`.

## Background

Previously, the project was modified to support the custom application package name `com.xpmall`. 
However, it has been decided that hardcoding a custom package name directly into the core configuration is not reasonable. Thus, we have reverted all configurations and core scripts to point back to the default `com.termux` namespace.

## Reversion Details

We reverted all hardcoded strings and environment values of `com.xpmall` back to `com.termux`. The files modified include:

### 1. Core Configuration Scripts
- [scripts/properties.sh](file:///mnt/d/newcompany/termux/package/scripts/properties.sh):
  - Changed `TERMUX_APP_PACKAGE` to `"com.termux"`.
  - Reverted `CGCT_DEFAULT_PREFIX` to point to `/data/data/com.termux/files/usr/glibc`.
  - Reverted `CGCT_DIR` to point to `/data/data/com.termux/cgct`.
  - Changed `TERMUX_REPO_PACKAGE` to `"com.termux"`.
- [build-package.sh](file:///mnt/d/newcompany/termux/package/build-package.sh):
  - Restored package verification constraints and error messages for options `-i` and `-I` to check for `com.termux`.
- [scripts/setup-ubuntu.sh](file:///mnt/d/newcompany/termux/package/scripts/setup-ubuntu.sh):
  - Restored the system symlink path to `/data/data/com.termux/files/usr/opt/aosp`.
- [scripts/test-runner.sh](file:///mnt/d/newcompany/termux/package/scripts/test-runner.sh):
  - Restored the shell interpreter path in the shebang header to `/data/data/com.termux`.
- [x11-packages/qt5-qmake/termux-build-qmake.sh](file:///mnt/d/newcompany/termux/package/x11-packages/qt5-qmake/termux-build-qmake.sh):
  - Restored shebang header and `TERMUX_PREFIX` variable paths.

### 2. Bootstrap Tools & Fallbacks
- [scripts/build-bootstraps.sh](file:///mnt/d/newcompany/termux/package/scripts/build-bootstraps.sh):
  - Restored default package name warning text.
- [scripts/build-bootstraps-from-source.sh](file:///mnt/d/newcompany/termux/package/scripts/build-bootstraps-from-source.sh):
  - Restored package validation checks and build start logs.
- [scripts/bootstrap/01-termux-bootstrap-second-stage-fallback.sh](file:///mnt/d/newcompany/termux/package/scripts/bootstrap/01-termux-bootstrap-second-stage-fallback.sh):
  - Restored path verification and script paths to `/data/data/com.termux`.
- [scripts/generate-bootstraps.sh](file:///mnt/d/newcompany/termux/package/scripts/generate-bootstraps.sh):
  - Reverted documentation comments headers referring to `com.xpmall`.
- **scripts/generate-bootstraps-custom.sh**:
  - Identified as an unused and untracked duplicate script; permanently removed from the codebase to keep the project clean.

### 3. Specific Packages & Patches
- **tergent**:
  - Modified [packages/tergent/0001-Automatically-unlock-keystore-using-fingerprint.patch](file:///mnt/d/newcompany/termux/package/packages/tergent/0001-Automatically-unlock-keystore-using-fingerprint.patch) to send the keystore unlock broadcast back to `com.termux.api` instead of `com.xpmall.api`.
- **alpine**:
  - Modified [packages/alpine/build.sh](file:///mnt/d/newcompany/termux/package/packages/alpine/build.sh) line 55 to set the public certificate CN domain to `anything.com.termux`.
- **pacman**:
  - Modified [packages/pacman/build.sh](file:///mnt/d/newcompany/termux/package/packages/pacman/build.sh) line 41 to revert backup S3 mirror host server back to `com.termux-pacman`.
- **nlohmann-json**:
  - Modified [packages/nlohmann-json/build.sh](file:///mnt/d/newcompany/termux/package/packages/nlohmann-json/build.sh) line 12 to use `/data/data/com.termux`.
- **osm2pgsql**:
  - Modified [packages/osm2pgsql/FindLua.cmake.patch](file:///mnt/d/newcompany/termux/package/packages/osm2pgsql/FindLua.cmake.patch) line 1 to use `/data/data/com.termux`.

### 4. Global Sweeps
- Automatically modified remaining instances in CMake cache configs (such as `packages/libhdf5` cache targets) and build recipe comments via global find-and-replace scripts.
