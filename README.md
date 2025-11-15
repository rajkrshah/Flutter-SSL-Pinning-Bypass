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

Tools Required:
- Burp Suite (or similar proxy tool)
- Frida (dynamic instrumentation toolkit)
- ProxyDroid (for rooted devices; optional)
- Ghidra (disassembler for analyzing native libraries)
- Rooted Android device or emulator

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

**Step 1: Setting Up Proxy Interception**
Configure Burp Suite Proxy:
Set up proxy listener—default is localhost:8080.
Enable "Invisible Proxying" in Burp Suite ("Request Handling" tab), allowing capture of non-proxy style HTTP(S) requests, which are typical in Flutter apps.​

Redirect Android Network Traffic:
Modify Android device Wi-Fi settings to use the Burp proxy’s IP and port.
Confirm interception by browsing HTTPS sites; browser should prompt for CA trust issues.​
Install Burp’s CA certificate on the device to resolve TLS errors.​

**Step 2: Limitations with Flutter Apps**
Flutter Does Not Use System Proxy Settings:
Unlike standard Android apps, Flutter bypasses device proxy configuration using custom SSL implementations, and typically does not use the system-trusted certificates.​

Solution: Global Traffic Redirection:
Use ProxyDroid (requires root) or IP tables to route all traffic to the proxy at the system level.​
Ensure ProxyDroid is globally enabled and set with Burp’s proxy IP and port.​

**Step 3: Overcoming SSL Pinning Failure**
After applying ProxyDroid, Burp may still fail to capture traffic when Flutter performs certificate pinning, causing TLS handshake errors.​

Reason: Flutter apps use native libraries for SSL, specifically Google's BoringSSL (open source), and manage their own trust store.

**Step 4: Disassembling Flutter Native Libraries**
Extract APK and Inspect Libraries:
Use APKTool to decompile the APK.

Identify native libraries: libflutter.so (Flutter engine) and libapp.so (app Dart snapshot).
SSL handshake and certificate verification reside in libflutter.so, implemented using BoringSSL.​

Locate Certificate Verification Function:
Analyze BoringSSL’s source, notably the ssl_x509.cc file; main function: ssl_session_verify_cert_chain.
Disassemble libflutter.so with Ghidra; search for function offset using known error macros and parameter references from BoringSSL’s code.​

**Step 5: Hooking and Patching with Frida**
Create a Frida Script:
Identify location (offset) of the certificate verification function in libflutter.so.
Use Frida to hook into this function at runtime and manipulate its return value to always report successful certificate validation (return true). This bypasses SSL pinning.​

**Script Workflow:**

Identify and hook native linker functions to detect when libflutter.so loads.
Calculate actual function offset at runtime using the library’s base address plus function offset.
Attach Frida interceptor hook to the relevant function.
Replace the return value in the function’s leaving point to indicate success.​

**Step 6: Intercepting HTTPS Traffic**
Launch the Flutter app after applying the Frida script.
Burp Suite will now successfully capture and decode HTTPS requests and responses—even those using custom certificate pinning or trust stores.​
Ensure "Invisible Proxy" is enabled in Burp to correctly intercept non-proxy style requests typical of Flutter's network stack.​

**Note**
Flutter apps use BoringSSL in their engine, bypassing Android's native trust and proxy settings, making classic SSL interception impossible without deeper instrumentation.

Successful interception requires:
System-wide traffic redirection (rooted device)
Bypassing SSL pinning at the native code level (Frida + native disassembly)
Proper proxy configuration for non-proxy requests.​
This approach dynamically hooks and changes certificate validation routines, achieving universal SSL interception for analysis or testing.

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

## Disclaimer

This repository is intended for educational and ethical research purposes only. Do not use these techniques on applications without explicit permission. The author is not responsible for any misuse or damages caused.

## References

- [Frida SSL Pinning Bypass](https://github.com/OWASP/owasp-mstg/blob/master/Document/0x06a-Testing-Data-Transport-Security.md#testing-custom-certificate-stores-and-certificate-pinning)
- [Flutter SSL Pinning Bypass Techniques](https://www.youtube.com/watch?v=lgdCM7yPZzI)
- [Objection Framework](https://github.com/sensepost/objection)

## License

[MIT](LICENSE)
