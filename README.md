# Flutter SSL Pinning Bypass

A collection of tools, techniques, and scripts to help you bypass SSL pinning in Flutter applications. This repository aims to assist security researchers, penetration testers, and developers who need to assess the security of Flutter apps or perform dynamic analysis by circumventing SSL/TLS certificate pinning.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Bypass Techniques](#bypass-techniques)
- [Usage](#usage)
- [Disclaimer](#disclaimer)
- [References](#references)
- [License](#license)

## Overview

Flutter uses the Dart programming language and its own networking stack, which makes traditional SSL pinning bypass techniques less effective. This repository provides up-to-date methods for bypassing SSL pinning in Flutter apps, including code patches, script-based hooks, and useful references.

## Features

- Frida scripts for runtime instrumentation and bypass
- Code samples to patch SSL pinning in Dart/Flutter
- Step-by-step guides for using tools like Objection and Xposed
- Support for iOS and Android Flutter apps
- Reference to latest research and community findings

## Prerequisites

- Basic knowledge of Flutter and mobile app security
- Android or iOS device/emulator with debugging enabled
- Frida ([https://frida.re/](https://frida.re/))
- adb (Android Debug Bridge) or Xcode tools (for iOS)
- Python (for running Frida scripts)
- Rooted/jailbroken device (recommended for advanced bypass)

## Bypass Techniques

1. **Frida Script Injection**
   - Use the provided Frida scripts to hook SSL/TLS verification functions at runtime.
2. **Modifying Dart Source Code**
   - Patch the Flutter app’s source or bytecode to disable SSL pinning.
3. **Objection Framework**
   - Use Objection to automate patching and bypass pinning on Android/iOS.
4. **Network Proxy with Custom CA**
   - Install a custom CA certificate and route traffic through tools like Burp Suite.

## Usage

### 1. Frida Script

This repository includes a Frida script (`flutter-ssl-bypass.js`) for bypassing SSL pinning in Flutter apps.

**Usage:**

```sh
frida -U -f com.example.flutterapp -l flutter-ssl-bypass.js --no-pause
```

- Replace `com.example.flutterapp` with your target app's package name.
- The `flutter-ssl-bypass.js` script is provided in this repository.

### 2. Using Objection

```sh
objection -g com.example.flutterapp explore
android sslpinning disable
```

### 3. Static Patching

- Decompile the APK or IPA, patch the relevant Dart code, and recompile.

## Disclaimer

This repository is intended for educational and ethical research purposes only. Do not use these techniques on applications without explicit permission. The author is not responsible for any misuse or damages caused.

## References

- [Frida SSL Pinning Bypass](https://github.com/OWASP/owasp-mstg/blob/master/Document/0x06a-Testing-Data-Transport-Security.md#testing-custom-certificate-stores-and-certificate-pinning)
- [Flutter SSL Pinning Bypass Techniques](https://medium.com/@username/flutter-ssl-pinning-bypass-2022-edition-xyz)
- [Objection Framework](https://github.com/sensepost/objection)

## License

[MIT](LICENSE)