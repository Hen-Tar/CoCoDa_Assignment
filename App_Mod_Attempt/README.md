# Attempt to Patch "Last War" (com.fun.lastwar.gp) to Block In-App Purchases

This repository documents an experimental reverse-engineering effort targeting the mobile game **Last War**. The aim was to detect and block monetization mechanisms (like in-app purchases) that might exploit users through dark patterns — aligning with ethical technology and EU Digital Services Act (DSA) compliance goals.

---

## Objective

- **App name**: Last War – Strategy Idle Game  
- **Package**: `com.fun.lastwar.gp`  
- **Goal**: Block or disable all Google Play billing interactions  
- **Use case**: Support systemic risk mitigation per DSA Article 34

---

## 🔧 Tools & Technologies Used

| Tool                          | Purpose                                      |
|-------------------------------|----------------------------------------------|
| `apktool`                     | Decompile and recompile APKs                 |
| `adb`                         | Install, inspect, and debug apps             |
| `Notepad++`                   | Bulk search across Smali code                |
| `apksigner`                   | Sign rebuilt APKs                            |
| `bundletool`                  | Repackage `.apks` or install bundles         |
| `keytool`                     | Generate keystore for signing                |
| `logcat`                      | Monitor runtime behavior                     |
| Developer Assistant / UI Inspector | Explore UI structure (limited success) |

---

## Process Breakdown

### 1. APK Acquisition & Decompilation

- Extracted the game APK from a real Android device using backup and transfer.
- Decompiled the base APK using `apktool`.

---

### 2. Billing Function Discovery (Smali)

Identified and patched billing methods like:

```smali
.method public final launchBillingFlow(...)
    .locals 1
    return v0
.end method
```

This forces an early return and avoids calling Google Billing APIs.

---

### 3. Rebuild and Sign

- Recompiled with `apktool`
- Created a test keystore using `keytool`
- Signed the APK with `apksigner`

---

### 4. Multi-APK Handling

Manually combined the following split APKs:

- `base.apk`
- `split_config.arm64_v8a.apk`
- `split_install_time_pack.apk`

Installed using:

```bash
adb install-multiple base.apk split_config.arm64_v8a.apk split_install_time_pack.apk
```

---

## Why the Mod Didn't Work

### 1. Update Check Failure

```plaintext
I Unity : update_required: current version 1.0.1, required version 1.0.11
```

The app blocks launch if the version is not the latest.

---

### 2. Manifest Metadata Checks

```xml
<meta-data android:name="com.android.stamp.source" android:value="https://play.google.com/store"/>
```

The app expects to be installed from the Google Play Store and may verify this source and signature.

---

### 3. Split APK Signature Dependency

The game uses split APKs that likely require matching **Play-distributed signatures** across all components to pass integrity validation.

---

## What Worked

- Successfully patched Google Play Billing method at the Smali level
- Signed and installed the patched APK set using `adb`
- Used `logcat` to observe runtime behavior and pinpoint version check failure

---

## Lessons Learned

Modern apps — especially games built with Unity — use layered protections:

- Runtime version checks  
- Installer source and signature validation  
- Play-specific split APK structures  

 **Simple Smali patching is not sufficient** for hardened, network-verified apps.

---

## Future Directions

### Hook Version Checks

- Patch version validation methods directly in Smali  
- Use tools like **Frida** to spoof return values or block update prompts

### Bypass Integrity Enforcement

- Use a **rooted device** or **custom ROM** with Play Integrity API spoofing

### Runtime Hooking

- Use **LSPosed/Xposed** to intercept billing-related methods at runtime

### Safer Demonstration Cases

- Apply these interventions to **open-source** or **less hardened** apps
- Demonstrate alignment with **DSA systemic risk mitigation goals**

---

## What You Should Do Next

- Push this folder and `README.md` to GitHub  
- Include:
  - This documentation  
  - Patched Smali snippets only (**never include full APKs**)  
- Prepare a fallback demo using a safer, controlled app where billing behavior is easier to intercept  
- Include an ethical note: this is for **academic analysis only**, not redistribution or tampering

---

## Disclaimer

> This project is for **educational and research purposes only**.  
> No modified APKs were shared or redistributed.  
> Modifying commercial apps without permission may violate terms of service or legal protections.  
> All work was performed on personal test devices as part of research into **systemic risk mitigation under the DSA**.

---
