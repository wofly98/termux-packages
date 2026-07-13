# Termux Packages Custom Build Instructions for com.xpmall

This document outlines the steps and modifications required to build Termux packages and bootstrap archives for the custom application package `com.xpmall`.

## 1. Project Configuration Changes

The following files have been modified to support the `com.xpmall` package name instead of the default `com.termux`.

### `scripts/properties.sh`
- `TERMUX_APP_PACKAGE` has been updated to `"com.xpmall"`.
- `TERMUX_BASE_DIR`, `TERMUX_CACHE_DIR`, and other derived paths now point to `/data/data/com.xpmall/...`.
- `CGCT_DEFAULT_PREFIX` and `CGCT_DIR` have been updated.
- `TERMUX_REPO_PACKAGE` is set to `"com.xpmall"`.

### `build-package.sh`
- Hardcoded checks for `/data/data/com.termux` have been updated to `/data/data/com.xpmall` to support on-device builds and dependency installation options (`-i`, `-I`).

### `scripts/setup-ubuntu.sh`
- The system symlink creation has been updated to point to `/data/data/com.xpmall/files/usr/opt/aosp`.

### `scripts/bootstrap/01-termux-bootstrap-second-stage-fallback.sh`
- Fallback paths for `preinstall.tgz` extraction have been updated to `com.xpmall`.

## 2. Bootstrap Generation (`scripts/generate-bootstraps.sh`)

The `generate-bootstraps.sh` script has been heavily modified to support the custom package name and improve build reliability from China.

### Key Modifications:
1.  **Mirror Update**: The default APT repository has been switched to **USTC** (`mirrors.ustc.edu.cn`) for faster downloads.
2.  **Download Reliability**: `curl` commands now use `--retry 5 --retry-delay 10 -C -` to handle connection instability and support resuming downloads.
3.  **Persistent Temp Directory**: The script now uses a persistent temporary directory (`${TMPDIR}/termux-bootstrap-tmp`) so that downloaded `.deb` files are not lost between runs.
4.  **Path Relocation**:
    - Automatic logic to move extracted files from `data/data/com.termux` to `data/data/com.xpmall`.
    - Updates `.list` and `.md5sums` metadata files to reflect the new paths.
5.  **Global String Replacement**:
    - A final step runs `perl` to replace all occurrences of `com.termux` with `com.xpmall` inside the generated rootfs text files.

### How to Generate Bootstrap Archives

To generate the bootstrap archive for `aarch64` (or other architectures), run:

```bash
# Install dependencies if not already present
sudo apt-get install -y zip jq curl perl

# Run the generation script
./scripts/generate-bootstraps.sh --architectures aarch64
```

The output file `bootstrap-aarch64.zip` will be created in the root of the project directory.

## 3. General Package Building

To build individual packages, use the standard `build-package.sh` script. It will automatically respect the `com.xpmall` configuration set in `properties.sh`.

```bash
./build-package.sh [package_name]
```

## 4. Troubleshooting

- **Download Failures**: If `generate-bootstraps.sh` fails during download, simply re-run the script. It will resume where it left off thanks to the persistent temp directory.
- **Path Issues**: If you find any hardcoded `/data/data/com.termux` paths remaining, use `grep -r "com.termux" .` to locate them.
