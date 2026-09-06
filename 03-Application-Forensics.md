# 03 - Application Forensics

After the initial device triage, the next step is to examine the applications installed on the Android device. Application forensics helps identify installed packages, application metadata, permissions, components, installation times, and potentially suspicious applications.

---

## 1. List Installed Applications

List all installed packages:

```bash
adb> pm list packages
```

List only third-party applications:

```bash
adb> pm list packages -3
```

List system applications:

```bash
adb> pm list packages -s
```

---

## 2. Search for an Application

Search for a specific application:

```bash
adb> pm list packages | grep -i whatsapp
```

Example:

```text
package:com.whatsapp
```

You can also search for potentially interesting package names:

```bash
$ adb shell pm list packages | grep -Ei "vpn|prshelloxy|remote|monitor|spy|admin"
```

> **Note:** A suspicious package name does not automatically mean that the application is malicious. Further analysis is required.

---

## 3. Get Application Information

For a specific package:

```bash
adb> dumpsys package com.example.app
```

This can provide information about:

* Package name
* Version
* UID
* Permissions
* Activities
* Services
* Receivers
* Providers
* Installation information

---

## 4. Find the APK Path

Find where the APK is installed:

```bash
adb> pm path com.example.app
```

Example:

```text
package:/data/app/~~abc123==/com.example.app-xyz/base.apk
```

The APK path can later be used to acquire the application for static analysis.

---

## 5. Application Version

Check the installed version:

```bash
adb> dumpsys package com.example.app | grep -i version
```

Example:

```text
versionCode=123
versionName=1.2.3
```

Record the version because it can be useful when comparing the installed application with known releases.

---

## 6. Installation and Update Times

Check when the application was installed:

```bash
adb> dumpsys package com.example.app | grep -Ei "firstInstallTime|lastUpdateTime"
```

Example:

```text
firstInstallTime=2026-08-20 10:32:15
lastUpdateTime=2026-08-25 14:11:02
```

These timestamps can be useful for timeline analysis.

For example:

```text
Application installed
        ↓
Suspicious activity
        ↓
Network connection
        ↓
Security event
```

Correlating these timestamps with system logs and other artifacts can help determine whether the application is relevant to an incident.

---

## 7. Application UID

Find the application's UID:

```bash
adb> dumpsys package com.example.app | grep -i userId
```

Example:

```text
userId=10245
```

The UID can later be used to correlate the application with processes, network activity, and other system information.

---

## 8. Application Data Directory

Find the application's data directory:

```bash
adb> dumpsys package com.example.app | grep -i dataDir
```

Example:

```text
dataDir=/data/user/0/com.example.app
```

On a standard non-rooted Android device, access to private application data is normally restricted. However, identifying the directory is still useful during forensic analysis.

---

## 9. Application Permissions

Display the permissions requested by an application:

```bash
adb> dumpsys package com.example.app | grep -A 50 "requested permissions"
```

Look for sensitive permissions such as:

```text
android.permission.READ_SMS
android.permission.RECEIVE_SMS
android.permission.READ_CONTACTS
android.permission.RECORD_AUDIO
android.permission.CAMERA
android.permission.ACCESS_FINE_LOCATION
android.permission.READ_CALL_LOG
android.permission.READ_PHONE_STATE
```

You can also list Android permission groups:

```bash
adb shell pm list permissions -g
```

Permissions should always be evaluated according to the application's expected functionality.

For example, a messaging application requesting SMS permissions may be expected, while the same permissions in an unrelated application may require further investigation.

---

## 10. Check Running Application Processes

Check whether the application is currently running:

```bash
adb> shell ps -A | grep com.example.app
```

Example:

```text
u0_a245  12345  ...  com.example.app
```

Process analysis will be covered in more detail in the process forensics section.

---

## 11. Application Services

Check services associated with the application:

```bash
adb> dumpsys package com.example.app | grep -A 20 "Service"
```

Services are important because applications can use background services to perform actions without an active user interface.

During an investigation, pay attention to services related to:

```text
Network communication
Background execution
Accessibility
Monitoring
Device administration
Data synchronization
```

---

## 12. Check Application Network Activity

Find the UID of the application:

```bash
adb> dumpsys package com.example.app | grep -i userId
```

Then investigate network connections associated with the UID:

```bash
adb> dumpsys netstats
```

You can also inspect active connections:

```bash
adb> ip route
```

```bash
adb> ss -tunap
```

Availability of connection details depends on the Android version and device privileges.

Network forensics will be covered separately.

---

## 13. Check Application Battery Usage

Search battery statistics for the application:

```bash
adb> dumpsys batterystats | grep -i com.example.app
```

Unexpected battery consumption can be an indicator worth investigating, especially when combined with background services or network activity.

Battery analysis will be covered in the dedicated battery forensics section.

---

## 14. Acquire the APK

First identify the APK path:

```bash
adb> pm path com.example.app
```

Example:

```text
package:/data/app/~~abc123==/com.example.app-xyz/base.apk
```

Then attempt to pull the APK:

```bash
$ adb pull /data/app/~~abc123==/com.example.app-xyz/base.apk evidence/apps/com.example.app.apk
```

If the APK is accessible, calculate its SHA-256 hash:

```bash
$ sha256sum evidence/apps/com.example.app.apk
```

Save the hash:

```bash
$ sha256sum evidence/apps/com.example.app.apk > evidence/apps/com.example.app.apk.sha256
```

The hash can be used to verify the integrity of the acquired APK.

---

## 15. Application Evidence Collection

Create the application evidence directory:

```bash
$ mkdir -p evidence/apps
```

Collect the package list:

```bash
adb> pm list packages > evidence/apps/packages.txt
```

Collect third-party applications:

```bash
adb> pm list packages -3 > evidence/apps/third_party_packages.txt
```

Collect system applications:

```bash
adb> shell pm list packages -s > evidence/apps/system_packages.txt
```

Collect running processes:

```bash
adb shell ps -A > evidence/apps/processes.txt
```

For a suspicious application:

```bash
adb> shell dumpsys package com.example.app > evidence/apps/com.example.app_package.txt
```

```bash
adb> shell pm path com.example.app > evidence/apps/com.example.app_apk_path.txt
```
