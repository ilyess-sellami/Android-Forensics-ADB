# 07 - Network Forensics

Network activity is one of the most valuable forensic sources on Android devices. It can reveal application communications, active connections, DNS requests, open ports, and indicators of data exfiltration or command-and-control activity.

---

## 1. Check Network Interfaces

Display all network interfaces:

```bash
adb> ip addr show
```

Alternative command:

```bash
adb> ifconfig
```

Save the output:

```bash
mkdir -p evidence/network

adb shell ip addr show \
> evidence/network/interfaces.txt
```

Useful information includes:

```text
Wi-Fi interface
Mobile data interface
IPv4 addresses
IPv6 addresses
MAC addresses
Interface status
```

Example:

```text
wlan0
rmnet_data0
lo
```

---

## 2. Check Current Network Connections

Display active network connections:

```bash
adb> netstat -an
```

On newer Android versions:

```bash
adb> ss -an
```

Save the output:

```bash
adb shell netstat -an \
> evidence/network/netstat.txt
```

or:

```bash
adb shell ss -an \
> evidence/network/ss.txt
```

Look for:

```text
Established connections
Listening ports
Remote IP addresses
Unexpected connections
```

Example:

```text
tcp ESTABLISHED 192.168.1.10:54123 34.120.45.33:443
```

---

## 3. Identify Applications Using Network Connections

Display sockets associated with applications:

```bash
adb> dumpsys connectivity
```

Check detailed network information:

```bash
adb> dumpsys netstats
```

Save the results:

```bash
adb shell dumpsys connectivity \
> evidence/network/connectivity.txt

adb shell dumpsys netstats \
> evidence/network/netstats.txt
```

These outputs may help correlate:

```text
Application
UID
Network activity
Data usage
Connection history
```

---

## 4. Check Open Listening Ports

Display listening services:

```bash
adb> netstat -tuln
```

Alternative:

```bash
adb> ss -lntup
```

Save the results:

```bash
adb shell netstat -tuln \
> evidence/network/listening_ports.txt
```

Example:

```text
tcp 0 0 0.0.0.0:8080 LISTEN
```

Unexpected listening ports may indicate:

```text
Remote administration tools
Debug services
Malware
Backdoors
```

---

## 5. Inspect DNS Configuration

Display DNS servers currently in use:

```bash
adb> getprop | grep dns
```

Example:

```text
net.dns1
net.dns2
```

Save the output:

```bash
adb shell getprop | grep dns \
> evidence/network/dns.txt
```

Investigate:

```text
Unknown DNS servers
Suspicious private DNS settings
Malicious redirection
```

---

## 6. Check Wi-Fi Information

Display Wi-Fi details:

```bash
adb> dumpsys wifi
```

Save the output:

```bash
adb shell dumpsys wifi \
> evidence/network/wifi.txt
```

Useful information includes:

```text
Connected SSID
BSSID
Signal strength
Connection history
Network configuration
```

Example:

```text
SSID: CorpWiFi
BSSID: 90:1B:0E:AA:BB:CC
```

---

## 7. Check Mobile Network Information

Display telephony information:

```bash
adb> dumpsys telephony.registry
```

Save the output:

```bash
adb shell dumpsys telephony.registry \
> evidence/network/telephony.txt
```

Relevant information may include:

```text
Operator
Network type
Roaming status
SIM state
Cell information
```

Example:

```text
mServiceState
mDataConnectionState
mNetworkType
```

---

## 8. Check Data Usage Statistics

Display network statistics:

```bash
adb> dumpsys netstats
```

Search for specific applications:

```bash
adb> dumpsys netstats | grep -i com.example.app
```

Save the output:

```bash
adb shell dumpsys netstats \
> evidence/network/netstats.txt
```

The statistics may reveal:

```text
Bytes sent
Bytes received
Mobile usage
Wi-Fi usage
Application traffic
```

Large outbound transfers may warrant further investigation.

---

## 9. Inspect Routing Table

Display network routes:

```bash
adb> ip route
```

Save the output:

```bash
adb shell ip route \
> evidence/network/routes.txt
```

Example:

```text
default via 192.168.1.1 dev wlan0
```

Review routes for:

```text
VPN configurations
Proxy routing
Unexpected gateways
Network manipulation
```

---

## 10. Check Proxy Configuration

Display proxy settings:

```bash
adb> settings get global http_proxy
```

Save the output:

```bash
adb shell settings get global http_proxy \
> evidence/network/proxy.txt
```

Example:

```text
192.168.1.50:8080
```

Unexpected proxy servers may indicate:

```text
Traffic interception
Monitoring software
Malware activity
```

---

## 11. Check VPN Connections

Display VPN information:

```bash
adb> dumpsys connectivity | grep -i vpn
```

Alternative:

```bash
adb> dumpsys vpn
```

Save the results:

```bash
adb shell dumpsys connectivity \
> evidence/network/vpn.txt
```

Relevant information:

```text
VPN state
VPN application
Tunnel interface
Remote gateway
```

Example:

```text
VPN connected
tun0
```

---

## 12. Monitor Live Network Activity

Display real-time connection activity:

```bash
adb> cat /proc/net/tcp
```

For UDP:

```bash
adb> cat /proc/net/udp
```

Save the data:

```bash
adb shell cat /proc/net/tcp \
> evidence/network/proc_net_tcp.txt

adb shell cat /proc/net/udp \
> evidence/network/proc_net_udp.txt
```

Useful for identifying:

```text
Active sessions
Suspicious destinations
Unknown services
```

---

## 13. Correlate Network Activity With Processes

List running processes:

```bash
adb> ps -A
```

Save the output:

```bash
adb shell ps -A \
> evidence/network/processes.txt
```

Correlate:

```text
PID
Application
Open connections
Data transfer
```

For suspicious findings:

```text
Unknown Process
        ↓
Active Connection
        ↓
External IP Address
        ↓
Large Data Transfer
        ↓
Potential Exfiltration
```

---

## 14. Check Firewall Rules

Display packet filtering rules:

```bash
adb> iptables -L -n
```

On newer Android versions:

```bash
adb> nft list ruleset
```

Save the output:

```bash
adb shell iptables -L -n \
> evidence/network/firewall.txt
```

Review for:

```text
Blocked connections
Forwarding rules
Traffic redirection
Custom filtering
```

---

## 15. Network Investigation

When investigating suspicious network activity, correlate:

```text
Active Connections
DNS Configuration
Open Ports
Network Interfaces
VPN Tunnels
Wi-Fi Information
Running Processes
Data Usage Statistics
Firewall Rules
```

Example:

```text
Unknown Application
        ↓
Persistent Network Connection
        ↓
External IP Address
        ↓
High Data Transfer
        ↓
Suspicious DNS Resolution
        ↓
Potential Data Exfiltration
```

Network indicators should always be correlated with application, process, and system evidence before drawing conclusions.

---

## 16. Save Network Evidence

Create the evidence directory:

```bash
$ mkdir -p evidence/network
```

Collect interface information:

```bash
adb> ip addr show > evidence/network/interfaces.txt
```

Collect active connections:

```bash
adb> netstat -an > evidence/network/netstat.txt
```

Collect connectivity information:

```bash
adb> dumpsys connectivity > evidence/network/connectivity.txt
```

Collect network statistics:

```bash
adb> dumpsys netstats > evidence/network/netstats.txt
```

Collect Wi-Fi information:

```bash
adb> dumpsys wifi > evidence/network/wifi.txt
```

Collect routing information:

```bash
adb> ip route > evidence/network/routes.txt
```

Collect DNS configuration:

```bash
adb> getprop | grep dns > evidence/network/dns.txt
```

Collect process information:

```bash
adb> ps -A > evidence/network/processes.txt
```

Collect firewall rules:

```bash
adb> iptables -L -n > evidence/network/firewall.txt
```