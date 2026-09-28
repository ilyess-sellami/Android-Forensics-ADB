# 08 - Persistence Analysis

Persistence mechanisms allow applications, services, or malware to survive reboots, automatically start, or continue executing in the background. Persistence analysis helps identify applications and components that maintain long-term execution on an Android device.

---

## 1. Check Installed Applications

Display all installed packages:

```bash
adb> pm list packages
```

Save the output:

```bash
mkdir -p evidence/persistence

adb shell pm list packages \
> evidence/persistence/packages.txt
```

Display package paths:

```bash
adb> pm list packages -f
```

Save the results:

```bash
adb shell pm list packages -f \
> evidence/persistence/packages_with_paths.txt
```

Look for:

```text
Unknown applications
Recently installed applications
Third-party APKs
Applications outside standard locations
```

---

## 2. Check Applications Enabled at Boot

Applications can register broadcast receivers that execute after device startup.

Inspect package information:

```bash
adb> dumpsys package com.example.app
```

Search for boot-related receivers:

```bash
adb> dumpsys package com.example.app | grep -i BOOT_COMPLETED
```

Example:

```text
android.intent.action.BOOT_COMPLETED
```

Save results:

```bash
adb shell dumpsys package com.example.app \
> evidence/persistence/package_info.txt
```

Applications registering for boot events may automatically start after reboot.

---

## 3. Check Registered Broadcast Receivers

Display package details:

```bash
adb> dumpsys package
```

Search for receivers:

```bash
adb> dumpsys package | grep -i receiver
```

Save the output:

```bash
adb shell dumpsys package \
> evidence/persistence/package_dump.txt
```

Common persistence-related broadcasts include:

```text
BOOT_COMPLETED
LOCKED_BOOT_COMPLETED
PACKAGE_ADDED
USER_PRESENT
CONNECTIVITY_CHANGE
```

---

## 4. Check Running Services

Display active services:

```bash
adb> dumpsys activity services
```

Save the results:

```bash
adb shell dumpsys activity services \
> evidence/persistence/services.txt
```

Search for a specific application:

```bash
adb> dumpsys activity services | grep -i com.example.app
```

Look for:

```text
Long-running services
Foreground services
Background services
Unexpected applications
```

---

## 5. Check Foreground Services

Foreground services are less likely to be terminated by Android.

Display foreground service information:

```bash
adb> dumpsys activity services
```

Search for foreground entries:

```bash
adb> dumpsys activity services | grep -i foreground
```

Save the results:

```bash
adb shell dumpsys activity services \
> evidence/persistence/foreground_services.txt
```

Persistent foreground services may indicate:

```text
Location tracking
Remote access tools
Data synchronization
Malicious monitoring
```

---

## 6. Check Scheduled Jobs

Android applications can schedule jobs that execute periodically.

Display jobs:

```bash
adb> dumpsys jobscheduler
```

Save the output:

```bash
adb shell dumpsys jobscheduler \
> evidence/persistence/jobscheduler.txt
```

Search for a specific package:

```bash
adb> dumpsys jobscheduler | grep -i com.example.app
```

Useful information includes:

```text
Job ID
Application UID
Scheduled execution
Periodic tasks
```

---

## 7. Check Alarm Manager Entries

Applications can use alarms to relaunch activities or services.

Display alarm information:

```bash
adb> dumpsys alarm
```

Save the output:

```bash
adb shell dumpsys alarm \
> evidence/persistence/alarm.txt
```

Search for a package:

```bash
adb> dumpsys alarm | grep -i com.example.app
```

Look for:

```text
Recurring alarms
Wake-up alarms
Background execution triggers
```

---

## 8. Check Device Administrators

Malicious applications may abuse device administrator privileges to resist removal.

Display device administrators:

```bash
adb> dumpsys device_policy
```

Save the output:

```bash
adb shell dumpsys device_policy \
> evidence/persistence/device_policy.txt
```

Useful information:

```text
Active administrators
Security policies
Managed profiles
```

Investigate any unknown administrator applications.

---

## 9. Check Accessibility Services

Accessibility services are frequently abused by malware.

Display enabled accessibility services:

```bash
adb> settings get secure enabled_accessibility_services
```

Save the output:

```bash
adb shell settings get secure enabled_accessibility_services \
> evidence/persistence/accessibility_services.txt
```

Example:

```text
com.example.app/.AccessibilityService
```

Unexpected accessibility services may require closer investigation.

---

## 10. Check Notification Listeners

Applications can register notification listeners to monitor system notifications.

Display notification listeners:

```bash
adb> settings get secure enabled_notification_listeners
```

Save the results:

```bash
adb shell settings get secure enabled_notification_listeners \
> evidence/persistence/notification_listeners.txt
```

Review:

```text
Unknown applications
Security tools
Monitoring applications
```

---

## 11. Check Running Processes

Display active processes:

```bash
adb> ps -A
```

Save the output:

```bash
adb shell ps -A \
> evidence/persistence/processes.txt
```

Search for suspicious applications:

```bash
adb> ps -A | grep -i example
```

Correlate:

```text
Process Name
PID
UID
Package Name
Execution Duration
```

---

## 12. Check Application Permissions

Display package permissions:

```bash
adb> dumpsys package com.example.app
```

Search for permissions:

```bash
adb> dumpsys package com.example.app | grep permission
```

Save the output:

```bash
adb shell dumpsys package com.example.app \
> evidence/persistence/app_permissions.txt
```

Pay attention to:

```text
RECEIVE_BOOT_COMPLETED
SYSTEM_ALERT_WINDOW
BIND_ACCESSIBILITY_SERVICE
REQUEST_IGNORE_BATTERY_OPTIMIZATIONS
```

---

## 13. Check Battery Optimization Exceptions

Applications excluded from battery optimizations are more likely to remain active.

Display whitelist information:

```bash
adb> dumpsys deviceidle whitelist
```

Save the output:

```bash
adb shell dumpsys deviceidle whitelist \
> evidence/persistence/deviceidle_whitelist.txt
```

Example:

```text
com.example.app
```

Applications on the whitelist may persist longer in the background.

---

## 14. Check Work Profiles and Managed Applications

Display user information:

```bash
adb> dumpsys user
```

Save the output:

```bash
adb shell dumpsys user \
> evidence/persistence/users.txt
```

Look for:

```text
Managed profiles
Enterprise applications
Work containers
```

Some applications may only be installed within specific profiles.

---

## 15. Check Application Installation Time

Display package information:

```bash
adb> dumpsys package com.example.app
```

Search for install timestamps:

```bash
adb> dumpsys package com.example.app | grep -Ei "firstInstallTime|lastUpdateTime"
```

Example:

```text
firstInstallTime
lastUpdateTime
```

Save the output:

```bash
adb shell dumpsys package com.example.app \
> evidence/persistence/package_timestamps.txt
```

This can help correlate persistence indicators with incident timelines.

---

## 16. Persistence Investigation

When investigating persistence activity, correlate:

```text
Installed Applications
Boot Receivers
Running Services
Foreground Services
Scheduled Jobs
Alarm Manager Entries
Accessibility Services
Notification Listeners
Device Administrators
Battery Optimization Exceptions
Running Processes
Application Permissions
```

Example:

```text
Unknown Application
        ↓
BOOT_COMPLETED Receiver
        ↓
Foreground Service
        ↓
Scheduled Job
        ↓
Accessibility Service
        ↓
Persistence Mechanism Identified
```

A single persistence indicator is not sufficient to classify an application as malicious. Findings should be validated through process, network, filesystem, and application analysis.

---

## 17. Save Persistence Evidence

Create the evidence directory:

```bash
$ mkdir -p evidence/persistence
```

Collect package information:

```bash
adb> pm list packages -f \
> evidence/persistence/packages.txt
```

Collect running services:

```bash
adb> dumpsys activity services \
> evidence/persistence/services.txt
```

Collect scheduled jobs:

```bash
adb> dumpsys jobscheduler \
> evidence/persistence/jobscheduler.txt
```

Collect alarm information:

```bash
adb> dumpsys alarm \
> evidence/persistence/alarm.txt
```

Collect device administrator information:

```bash
adb> dumpsys device_policy \
> evidence/persistence/device_policy.txt
```

Collect accessibility services:

```bash
adb> settings get secure enabled_accessibility_services \
> evidence/persistence/accessibility_services.txt
```

Collect battery optimization whitelist:

```bash
adb> dumpsys deviceidle whitelist \
> evidence/persistence/deviceidle_whitelist.txt
```

Collect running processes:

```bash
adb> ps -A \
> evidence/persistence/processes.txt
```

Collect user and profile information:

```bash
adb> dumpsys user \
> evidence/persistence/users.txt
```
