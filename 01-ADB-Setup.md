# 01 - ADB Setup

**ADB (Android Debug Bridge)** is the main interface we will use to communicate with the Android device from Kali Linux. Before starting the forensic collection, we need to install ADB and make sure the device is correctly detected.

## Install ADB

Update the Kali package list:

```bash
$ sudo apt update
```

Install the Android Debug Bridge package:

```bash
$ sudo apt install adb
```

Verify that ADB was installed:

```bash
$ adb version
```

You should get an output similar to:

```text
Android Debug Bridge version 1.0.41 
Version 35.x.x
```

---

## Enable USB Debugging

ADB requires USB debugging to be enabled on the Android device.

On the phone:

```text
Settings
→ About phone
→ Software information
→ Build number
→ Tap 7 times
```

This enables Developer options.

Go back to:

```text
Settings
→ Developer options
→ USB debugging
```

Enable USB debugging.

---

## Connect the Android Device

Connect the phone to Kali Linux using a USB data cable.

Start the ADB server:

```bash
$ adb start-server
```

Then check the connected devices:

```bash
$ adb devices
```

A correctly connected device should appear as:

```text
List of devices attached
R5CY80SG38    device
```

---

## Test the ADB Connection

Once the device appears as device, test the shell:

```bash
$ adb shell

adb> whoami
```
Typical output:

```text
shell
```

Exit the shell:

```bash
adb> exit
```

You can also run the command directly:

```bash
$ adb shell whoami
```

---

## Test Device Information

Check the manufacturer:

```bash
$ adb shell getprop ro.product.manufacturer
```

Check the model:

```bash
$ adb shell getprop ro.product.model
```

Check the Android version:

```bash
$ adb shell getprop ro.build.version.release
```