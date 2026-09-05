# 02 - Device Triage

After confirming the ADB connection, the next step is to collect basic information about the Android device. This gives us the device model, Android version, security patch level, storage, battery state, and network configuration before starting deeper analysis.

---

## 1. Device Information

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

## 3. Android Information

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

## 4. System Information

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

## 5. Battery Status

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

## 6. Storage

Check available storage:

```bash
adb> df -h
```

Check /data specifically:

```bash
adb> df -h /data
```

---

## 7. Network Information

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

---

## 8. Evidence Collection

Create the evidence directory:

```bash
mkdir -p evidence/device
```

Collect device information:

```bash
$ adb shell getprop > evidence/device/getprop.txt
```

Collect kernel information:

```bash
$ adb shell uname -a > evidence/device/uname.txt
```

Collect device uptime:

```bash
$ adb shell uptime > evidence/device/uptime.txt
```

Collect device date and time:

```bash
$ adb shell date > evidence/device/date.txt
```

Collect battery information:

```bash
$ adb shell dumpsys battery > evidence/device/battery.txt
```

Collect storage information:

```bash
$ adb shell df -h > evidence/device/storage.txt
```

Collect network interfaces:

```bash
$ adb shell ip addr > evidence/device/ip_addr.txt
```

Collect routing information:

```bash
$ adb shell ip route > evidence/device/ip_route.txt
```

Collect DNS configuration:

```bash
$ adb shell getprop | grep -i dns > evidence/device/dns.txt
```

Collect the HTTP proxy configuration:

```bash
$ adb shell settings get global http_proxy > evidence/device/http_proxy.txt
```