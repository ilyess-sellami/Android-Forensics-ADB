# 06 - Battery Forensics

Battery information can provide useful forensic indicators about application activity and device usage. High battery consumption, frequent wakeups, and long-running background processes can help identify applications that may require further investigation.

---

## 1. Check Battery Status

Display the current battery state:

```bash
adb> dumpsys battery
```

Important fields include:

```text
AC powered
USB powered
status
health
level
voltage
temperature
```

Example:

```text
level: 47
voltage: 3900
temperature: 320
```

Save the battery status:

```bash
mkdir -p evidence/battery

adb shell dumpsys battery \
> evidence/battery/battery_status.txt
```

---

## 2. Check Battery Statistics

Android maintains battery usage statistics that can provide information about applications and system components.

Run:

```bash
adb> dumpsys batterystats
```

Save the output:

```bash
adb shell dumpsys batterystats \
> evidence/battery/batterystats.txt
```

The output can contain information about:

* Applications
* CPU usage
* Wake locks
* Network activity
* Screen usage
* Charging
* Battery consumption

---

## 3. Check Application Battery Usage

Search for a specific application:

```bash
adb> dumpsys batterystats | grep -i com.example.app
```

Replace:

```text
com.example.app
```

with the package being investigated.

For example:

```bash
adb> dumpsys batterystats | grep -i whatsapp
```

Unexpected battery activity can be useful when investigating applications running continuously in the background.

---

## 4. Check Wake Locks

Wake locks allow applications or system components to keep the device awake.

Check wake lock information:

```bash
adb> dumpsys batterystats | grep -i "wake"
```

You can also inspect power information:

```bash
adb> dumpsys power
```

Save the results:

```bash
adb shell dumpsys power \
> evidence/battery/power.txt
```

Long-running or unexpected wake locks may indicate intensive background activity.

---

## 5. Check CPU Activity

Battery consumption can be correlated with CPU usage.

Display current CPU usage:

```bash
adb> top -n 1
```

For a specific application:

```bash
adb> top -n 1 | grep com.example.app
```

Save the result:

```bash
adb shell top -n 1 \
> evidence/battery/top.txt
```

An application consuming significant CPU while running in the background may require further investigation.

---

## 6. Check Charging State

Display the current charging state:

```bash
adb> dumpsys battery | grep -Ei "AC powered|USB powered|Wireless powered|status"
```

Example:

```text
AC powered: false
USB powered: true
Wireless powered: false
status: 2
```

The exact output depends on the Android version and device.

---

## 7. Check Battery Temperature

Check the current battery temperature:

```bash
adb> dumpsys battery | grep -i temperature
```

Example:

```text
temperature: 320
```

Android generally reports the temperature in tenths of a degree Celsius.

For example:

```text
320 = 32.0°C
```

Unusual temperature combined with high CPU or network activity can be useful during an investigation.

---

## 8. Check Screen and Interactive State

Check power state information:

```bash
adb> dumpsys power
```

Search for interactive state:

```bash
adb> dumpsys power | grep -i "interactive"
```

Screen activity can help distinguish normal foreground usage from unexpected background activity.

---

## 9. Battery Investigation

When investigating unusual battery consumption, correlate:

```text
Battery level
CPU usage
Wake locks
Running processes
Application activity
Network connections
Charging state
Screen activity
```

For example:

```text
High Battery Usage
        ↓
High CPU Usage
        ↓
Unknown Background Process
        ↓
Network Connections
        ↓
Suspicious Application
```

Battery activity alone is not sufficient to classify an application as malicious.

---

## 10. Save Battery Evidence

Create the evidence directory:

```bash
$ mkdir -p evidence/battery
```

Collect current battery status:

```bash
adb> dumpsys battery > evidence/battery/battery_status.txt
```

Collect battery statistics:

```bash
adb> dumpsys batterystats > evidence/battery/batterystats.txt
```

Collect power information:

```bash
adb> dumpsys power > evidence/battery/power.txt
```

Collect CPU information:

```bash
adb> top -n 1 > evidence/battery/top.txt
```