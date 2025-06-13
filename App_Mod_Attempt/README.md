# Attempt to Patch "Last War" Mobile Game to Block In-App Purchases

This repository documents an experimental effort to analyze and modify the mobile game **Last War** (`com.fun.lastwar.gp`) to disable Google Play billing and in-app purchases (IAP), in the context of systemic risk mitigation under the EU Digital Services Act (DSA). The goal was to neutralize addictive or manipulative monetization patterns.

---

## Objective

- **Target app**: Last War (strategy/idle mobile game)
- **Platform**: Android (native install, not emulator-compatible)
- **Goal**: Disable or block in-app purchase mechanisms (Google Play billing)
- **Constraints**: Non-rooted phone; manual static patching; no access to source code.

---

## Tools Used

- `apktool` – for decompiling and recompiling APKs.
- `Notepad++` – for code inspection and text search.
- `adb` – for debugging and log capture.
- `bundletool` – for rebuilding `.apks` bundles.
- `keytool` + `apksigner` – for signing APKs.
- `Developer Assistant` / `UI Inspect` tools – for UI identification (limited success).

---

## Methodology (Step-by-Step)

### 1. APK Extraction
- Extracted `.apk` and `.xapk` files using backup from the phone.
- Decompiled `base.apk` using `apktool`.

### 2. Billing Function Discovery
- Identified the core billing methods using Smali:
  - `launchBillingFlowCpp(...)`
  - `launchBillingFlow(...)` (public)
- Patched billing-related methods to return immediately (skipping actual billing logic).

### 3. Rebuild & Signing
- Rebuilt APK via `apktool`.
- Generated test keystore using `keytool`.
- Signed modified APK with `apksigner`.

### 4. Reinstall & Launch Attempt
- Removed original app.
- Installed patched app using:
  ```bash
  adb install-multiple base.apk split_config.arm64_v8a.apk split_install_time_pack.apk
