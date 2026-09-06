# 04 - Permissions Analysis

Application permissions are an important part of Android forensics. They show what access an application requests from the Android system and can help identify applications that have access to sensitive data, hardware, or system functions.

---

## 1. List Application Permissions

Display the permissions requested by an application:

```bash
adb> dumpsys package com.example.app | grep -A 50 "requested permissions"
```

Replace:

```text
com.example.app
```

with the package being investigated.

---

## 2. List All Android Permissions

List available Android permissions:

```bash
adb> pm list permissions
```

Group permissions by category:

```bash
adb> pm list permissions -g
```

List only dangerous permissions:

```bash
adb> pm list permissions -d
```

---

## 3. Check Granted Permissions

Check which permissions are currently granted to an application:

```bash
adb> dumpsys package com.example.app | grep -A 50 "grantedPermissions"
```

You can also check the application's permission state:

```bash
adb> cmd appops get com.example.app
```

This can provide additional information about how the application is using privileged operations.

---

## 4. Sensitive Permissions

During an investigation, pay particular attention to permissions related to sensitive resources.

### SMS

```text
android.permission.READ_SMS
android.permission.RECEIVE_SMS
android.permission.SEND_SMS
```

### Contacts

```text
android.permission.READ_CONTACTS
android.permission.WRITE_CONTACTS
```

### Location

```text
android.permission.ACCESS_FINE_LOCATION
android.permission.ACCESS_COARSE_LOCATION
android.permission.ACCESS_BACKGROUND_LOCATION
```

### Camera and Microphone

```text
android.permission.CAMERA
android.permission.RECORD_AUDIO
```

### Calls and Phone Information

```text
android.permission.READ_CALL_LOG
android.permission.WRITE_CALL_LOG
android.permission.READ_PHONE_STATE
android.permission.CALL_PHONE
```

### Storage and Media

```text
android.permission.READ_MEDIA_IMAGES
android.permission.READ_MEDIA_VIDEO
android.permission.READ_MEDIA_AUDIO
```

The exact permissions available depend on the Android version.

---

## 5. Check Special Access

Some Android capabilities are not represented only by normal runtime permissions.

Check application operations:

```bash
adb> cmd appops get com.example.app
```

Look for operations related to:

```text
Camera
Microphone
Location
Notifications
Background activity
Storage
Network
```

Special access should be correlated with the application's intended functionality.

---

## 6. Check Accessibility Services

Accessibility services can provide significant control over device interaction and may be abused by malicious applications.

Check enabled accessibility services:

```bash
adb> settings get secure enabled_accessibility_services
```

Check accessibility settings:

```bash
adb> settings list secure | grep -i accessibility
```

If an unknown application has an enabled accessibility service, investigate it further.

---

## 7. Check Device Administrators

Check active device administrators:

```bash
adb> dumpsys device_policy
```

Search for administrators:

```bash
adb> dumpsys device_policy | grep -i "admin"
```

Device administrator privileges can provide applications with additional control over the device.

---

## 8. Compare Requested and Granted Permissions

For a suspicious application, compare the permissions it requests with the permissions actually granted.

Collect the package information:

```bash
adb> dumpsys package com.example.app > evidence/apps/com.example.app_permissions.txt
```

Then review:

```bash
$ grep -A 50 "requested permissions" evidence/apps/com.example.app_permissions.txt
```

And:

```bash
$ grep -A 50 "grantedPermissions" evidence/apps/com.example.app_permissions.txt
```

This distinction is important because an application may request a permission without currently having it granted.

---

## 9. Save Permission Evidence

Create the permissions evidence directory:

```bash
$ mkdir -p evidence/permissions
```

Collect all available permissions:

```bash
adb> pm list permissions -g > evidence/permissions/all_permissions.txt
```

For a suspicious application:

```bash
adb> dumpsys package com.example.app > evidence/permissions/com.example.app.txt
```

Collect AppOps information:

```bash
adb> cmd appops get com.example.app > evidence/permissions/com.example.app_appops.txt
```

Collect accessibility configuration:

```bash
adb> settings list secure | grep -i accessibility > evidence/permissions/accessibility.txt
```

Collect device policy information:

```bash
adb> dumpsys device_policy > evidence/permissions/device_policy.txt
```