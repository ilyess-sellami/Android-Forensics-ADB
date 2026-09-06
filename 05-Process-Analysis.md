# 05 - Process Analysis

Running processes provide a view of what is currently executing on the Android device. Process analysis can help identify active applications, background services, unusual processes, process owners, and potentially suspicious activity.

---

## 1. List Running Processes

List all running processes:

```bash
adb> ps -A
```

Save the result:

```bash
mkdir -p evidence/processes

adb shell ps -A > evidence/processes/processes.txt
```

---

## 2. Search for a Specific Application

Search for a specific package:

```bash
adb> ps -A | grep com.example.app
```

Example:

```text
u0_a245  12345  ...  com.example.app
```

This can help determine whether the application is currently running.

---

## 3. Display Process Details

Display detailed process information:

```bash
adb> ps -A -o USER,PID,PPID,NAME
```

Important fields include:

* `USER` — process owner
* `PID` — process ID
* `PPID` — parent process ID
* `NAME` — process name

The exact output format may vary depending on the Android version.

---

## 4. Check a Process ID

Once a suspicious process is identified, find its PID:

```bash
adb> ps -A | grep com.example.app
```

Example:

```text
u0_a245  12345  ...  com.example.app
```

Here:

```text
PID = 12345
```

Check the process directory:

```bash
adb> ls -l /proc/12345
```

Some `/proc` information may be restricted on non-rooted devices.

---

## 5. Check Parent and Child Processes

Identify the parent process:

```bash
adb> ps -A -o PID,PPID,NAME | grep com.example.app
```

The `PPID` represents the parent process ID.

This can help identify unusual process relationships or unexpected process creation.

---

## 6. Check Process Memory

Check memory information for a process:

```bash
adb> dumpsys meminfo 12345
```

For a package:

```bash
adb> dumpsys meminfo com.example.app
```

This provides information about the application's memory usage.

Save the result:

```bash
adb shell dumpsys meminfo com.example.app \
> evidence/processes/com.example.app_meminfo.txt
```

Unexpected memory consumption can be useful when investigating applications performing intensive background activity.

---

## 7. Check Application Processes

Android applications may use multiple processes.

Search for all processes belonging to a package:

```bash
adb> ps -A | grep com.example.app
```

Example:

```text
u0_a245  12345  ... com.example.app
u0_a245  12401  ... com.example.app:service
```

The `:service` process can indicate a separate application process used for background functionality.

---

## 8. Check CPU Usage

Display process CPU usage:

```bash
adb> top -n 1
```

For a specific package:

```bash
adb> top -n 1 | grep com.example.app
```

Save the result:

```bash
adb shell top -n 1 \
> evidence/processes/top.txt
```

High CPU usage does not automatically indicate malicious activity, but it can be useful when correlated with battery consumption, network activity, or suspicious application behavior.

---

## 9. Check Process Network Activity

List active network connections:

```bash
adb> ss -tunap
```

Depending on the Android version and privileges, process or UID information may be displayed.

You can also inspect network statistics:

```bash
adb> dumpsys netstats
```

When investigating a suspicious application, correlate its UID with network activity where possible.

---

## 10. Investigate Suspicious Processes

When a process looks unusual, collect:

```text
Process name
PID
PPID
UID
Application package
CPU usage
Memory usage
Network activity
Associated services
```

Start by identifying the process:

```bash
adb> ps -A | grep suspicious
```

Then check its memory:

```bash
adb> dumpsys meminfo <PID>
```

Check associated package information:

```bash
adb> dumpsys package <package_name>
```

And investigate related network connections:

```bash
adb> ss -tunap
```

---

## 11. Save Process Evidence

Create the evidence directory:

```bash
mkdir -p evidence/processes
```

Collect running processes:

```bash
adb shell ps -A \
> evidence/processes/processes.txt
```

Collect process and activity information:

```bash
adb shell dumpsys activity processes \
> evidence/processes/activity_processes.txt
```

Collect running services:

```bash
adb shell dumpsys activity services \
> evidence/processes/services.txt
```

Collect CPU information:

```bash
adb shell top -n 1 \
> evidence/processes/top.txt
```

Collect network statistics:

```bash
adb shell dumpsys netstats \
> evidence/processes/netstats.txt
```