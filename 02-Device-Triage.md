# 02 - Device Triage

After confirming the ADB connection, the next step is to collect basic information about the Android device. This gives us the device model, Android version, security patch level, storage, battery state, and network configuration before starting deeper analysis.

## Device Information

Manufacturer:

```bash
adb> getprop ro.product.manufacturer
```

Model:

```bash
adb> getprop ro.product.model
```

Device:

```bash
adb> getprop ro.product.device
```

Product:

```bash
adb> getprop ro.product.name 
```

---

## Android Information

Android version:

```bash
adb> getprop ro.build.version.release
```

SDK version:

```bash
adb> getprop ro.build.version.sdk
```

Security patch:

```bash
adb> getprop ro.build.version.security_patch
```

Build ID:

```bash
adb> getprop ro.build.id
```

Build fingerprint:

```bash
adb> getprop ro.build.fingerprint
```

---

## System Information

Get all system properties:

```bash
adb> getprop
```

Kernel information:

```bash
adb> uname -a
```

Device uptime:

```bash
adb> uptime
```

Current device date and time:

```bash
adb> date
```

---

## Battery Status

Check the current battery state:

```bash
adb> dumpsys battery
```

Important fields include:

* `AC powered`
* `USB powered`
* `status`
* `health`
* `level`
* `temperature`
* `voltage`

For example:

```text
level: 47
temperature: 320
voltage: 3900
```

Battery analysis will be covered in more detail in the battery forensics section.

---

## Storage

Check available storage:

```bash
adb> df -h
```

Check /data specifically:

```bash
adb> df -h /data
```

---

## Network Information

List network interfaces:

```bash
adb> ip addr
```

Check routing:

```bash
adb> ip route
```

Check DNS properties:

```bash
adb> getprop | grep -i dns
```

Check HTTP proxy:

```bash
adb> settings get global http_proxy
```