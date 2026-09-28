# 09 - Log Analysis

System logs are one of the most important sources of forensic evidence on Android devices. Logs can reveal application activity, authentication events, crashes, network activity, system changes, privilege escalation attempts, and indicators of compromise.

---

## 1. Capture Live System Logs

Display live logs:

```bash
adb> logcat
```

Display logs with timestamps:

```bash
adb> logcat -v time
```

Example:

```text
09-21 14:35:22.112 I ActivityManager: Start proc
09-21 14:35:22.340 W PackageManager: Unknown package
```

Save logs:

```bash
mkdir -p evidence/logs

adb logcat -d \
> evidence/logs/logcat.txt
```

---

## 2. Display Available Log Buffers

Android stores logs in multiple buffers.

Display available buffers:

```bash
adb> logcat -g
```

Common buffers:

```text
main
system
events
radio
crash
kernel
```

Save information:

```bash
adb logcat -g \
> evidence/logs/log_buffers.txt
```

---

## 3. Dump All Log Buffers

Collect logs from all available buffers:

```bash
adb> logcat -d -b all
```

Save the output:

```bash
adb logcat -d -b all \
> evidence/logs/all_logs.txt
```

This may contain:

```text
Application activity
System events
Radio activity
Crash information
Security events
```

---

## 4. Analyze Application Logs

Filter logs for a specific application.

Example:

```bash
adb> logcat | grep com.example.app
```

Or:

```bash
adb> logcat -d | grep com.example.app
```

Save application-specific logs:

```bash
adb logcat -d | grep com.example.app \
> evidence/logs/app_logs.txt
```

Useful information includes:

```text
Application startup
Errors
Authentication activity
API calls
Background execution
```

---

## 5. Check Crash Logs

Display crash-related logs:

```bash
adb> logcat -b crash -d
```

Save crash information:

```bash
adb logcat -b crash -d \
> evidence/logs/crashes.txt
```

Crash logs may reveal:

```text
Application failures
Unexpected terminations
Security exceptions
Memory issues
```

Example:

```text
FATAL EXCEPTION
java.lang.SecurityException
```

---

## 6. Check System Logs

Display system buffer:

```bash
adb> logcat -b system -d
```

Save the results:

```bash
adb logcat -b system -d \
> evidence/logs/system_logs.txt
```

Investigate:

```text
Service activity
System configuration changes
Permission enforcement
Device state changes
```

---

## 7. Check Event Logs

Display event buffer:

```bash
adb> logcat -b events -d
```

Save the output:

```bash
adb logcat -b events -d \
> evidence/logs/event_logs.txt
```

Event logs may contain:

```text
Application launches
Package installation
Battery events
System events
```

---

## 8. Check Radio Logs

Display radio subsystem logs:

```bash
adb> logcat -b radio -d
```

Save the output:

```bash
adb logcat -b radio -d \
> evidence/logs/radio_logs.txt
```

Useful information:

```text
Cellular connectivity
SMS activity
Network registration
Carrier information
```

---

## 9. Search for Authentication Events

Search for authentication-related activity:

```bash
adb> logcat -d | grep -Ei "login|auth|authentication|token"
```

Save results:

```bash
adb logcat -d | grep -Ei "login|auth|authentication|token" \
> evidence/logs/authentication_events.txt
```

Potential findings:

```text
User sign-ins
OAuth events
Token generation
Authentication failures
```

---

## 10. Search for Security Exceptions

Display security-related exceptions:

```bash
adb> logcat -d | grep -Ei "SecurityException|permission denied"
```

Save the output:

```bash
adb logcat -d | grep -Ei "SecurityException|permission denied" \
> evidence/logs/security_exceptions.txt
```

Examples:

```text
Permission denied
SecurityException
Unauthorized access
```

These entries may indicate:

```text
Privilege escalation attempts
Application misconfiguration
Unauthorized access
```

---

## 11. Search for Package Installation Events

Look for package manager activity:

```bash
adb> logcat -d | grep -i PackageManager
```

Save the output:

```bash
adb logcat -d | grep -i PackageManager \
> evidence/logs/package_events.txt
```

Relevant events include:

```text
Package installation
Package removal
Application updates
Permission changes
```

---

## 12. Search for Network Activity

Search logs for network communications:

```bash
adb> logcat -d | grep -Ei "http|https|socket|tcp|udp"
```

Save the output:

```bash
adb logcat -d | grep -Ei "http|https|socket|tcp|udp" \
> evidence/logs/network_events.txt
```

Potential findings:

```text
Network requests
Connection failures
Remote hosts
Communication errors
```

---

## 13. Search for Application Crashes

Display fatal exceptions:

```bash
adb> logcat -d | grep -i "FATAL EXCEPTION"
```

Save the results:

```bash
adb logcat -d | grep -i "FATAL EXCEPTION" \
> evidence/logs/fatal_exceptions.txt
```

Example:

```text
FATAL EXCEPTION: main
```

Correlate crashes with:

```text
Application activity
User actions
System events
```

---

## 14. Check Boot Events

Search for boot activity:

```bash
adb> logcat -d | grep -Ei "boot|BOOT_COMPLETED"
```

Save the output:

```bash
adb logcat -d | grep -Ei "boot|BOOT_COMPLETED" \
> evidence/logs/boot_events.txt
```

Useful information:

```text
Device startup
Application auto-start
Persistence mechanisms
```

---

## 15. Check ADB Activity

Search for USB debugging and ADB activity:

```bash
adb> logcat -d | grep -i adb
```

Save the output:

```bash
adb logcat -d | grep -i adb \
> evidence/logs/adb_activity.txt
```

Investigate:

```text
ADB connections
Debug sessions
USB activity
Developer actions
```

---

## 16. Check Device Reboots

Search for reboot events:

```bash
adb> logcat -d | grep -Ei "reboot|shutdown"
```

Save the output:

```bash
adb logcat -d | grep -Ei "reboot|shutdown" \
> evidence/logs/reboot_events.txt
```

Useful for timeline analysis:

```text
Unexpected reboots
System crashes
Power events
```

---

## 17. Check SELinux Events

Display SELinux-related activity:

```bash
adb> logcat -d | grep -i avc
```

Alternative:

```bash
adb> dmesg | grep -i avc
```

Save the results:

```bash
adb logcat -d | grep -i avc \
> evidence/logs/selinux_events.txt
```

Example:

```text
avc: denied
```

These entries may indicate:

```text
Blocked access attempts
Policy violations
Malicious behavior
Security misconfigurations
```

---

## 18. Check Kernel Messages

Display kernel logs:

```bash
adb> dmesg
```

Save the output:

```bash
adb shell dmesg \
> evidence/logs/kernel_messages.txt
```

Kernel logs may reveal:

```text
Hardware events
Driver activity
USB insertion
SELinux events
System errors
```

---

## 19. Create a Timeline from Logs

Correlate timestamps from:

```text
Application Logs
Crash Logs
Authentication Events
Network Activity
Installation Events
Boot Events
Kernel Logs
```

Example:

```text
Application Installed
        ↓
BOOT_COMPLETED Receiver Triggered
        ↓
Background Service Started
        ↓
Network Connection Established
        ↓
Authentication Attempt
        ↓
Suspicious Activity Timeline
```

Timeline analysis often provides more value than examining isolated log entries.

---

## 20. Log Investigation

When performing log analysis, correlate:

```text
System Logs
Event Logs
Crash Logs
Application Logs
Authentication Events
Network Activity
SELinux Events
Package Events
Kernel Messages
Reboot Events
```

For example:

```text
Application Installed
        ↓
Background Service Started
        ↓
Network Connections Observed
        ↓
Security Exceptions Logged
        ↓
Potential Malicious Activity
```

Logs alone should not be considered definitive evidence and must be correlated with application, network, filesystem, and persistence artifacts.

---

## 21. Save Log Evidence

Create the evidence directory:

```bash
$ mkdir -p evidence/logs
```

Collect all logs:

```bash
adb> logcat -d -b all \
> evidence/logs/all_logs.txt
```

Collect system logs:

```bash
adb> logcat -d -b system \
> evidence/logs/system_logs.txt
```

Collect crash logs:

```bash
adb> logcat -d -b crash \
> evidence/logs/crashes.txt
```

Collect event logs:

```bash
adb> logcat -d -b events \
> evidence/logs/event_logs.txt
```

Collect radio logs:

```bash
adb> logcat -d -b radio \
> evidence/logs/radio_logs.txt
```

Collect kernel messages:

```bash
adb> dmesg \
> evidence/logs/kernel_messages.txt
```

Collect authentication events:

```bash
adb> logcat -d | grep -Ei "login|auth|token" \
> evidence/logs/authentication_events.txt
```

Collect security exceptions:

```bash
adb> logcat -d | grep -Ei "SecurityException|permission denied" \
> evidence/logs/security_exceptions.txt
```

Collect package events:

```bash
adb> logcat -d | grep -i PackageManager \
> evidence/logs/package_events.txt
```
