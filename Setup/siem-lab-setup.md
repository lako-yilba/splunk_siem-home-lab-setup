# 🔐 SIEM Home Lab Setup using Splunk

## 📌 Overview

This project demonstrates the setup of a **Security Information and Event Management (SIEM) Home Lab** using **Splunk Enterprise** to collect, centralize, and analyze security logs from multiple systems.

The goal of this lab was to simulate a **basic SOC (Security Operations Center) monitoring environment** where logs from different network devices and operating systems are aggregated for security monitoring and analysis.

### Lab includes:

- 🖥️ **Splunk Enterprise** running on **Ubuntu Server**
- 🔥 **pfSense Firewall** acting as the network gateway
- 🚨 **Suricata IDS** for intrusion detection
- 🪟 **Windows Host System** generating Windows Event Logs
- 🐉 **Kali Linux** acting as a Linux log source and attack simulation machine

All logs are forwarded to **Splunk for indexing, monitoring, and analysis**.

---

## 🧪 Lab Environment

The entire lab was deployed using **VirtualBox**.

### 💻 Virtual Machines

| Machine | Purpose |
|---|---|
| Ubuntu Server | Splunk SIEM Server |
| Kali Linux | Linux log source / attacker simulation |
| pfSense | Firewall and network logging |
| Suricata | Intrusion Detection System |

> ⚠️ **Note:** Windows logs were collected from the **host operating system (Windows)** rather than a virtual machine. All systems were connected inside an **isolated VirtualBox Host-Only network**.

---

## 🖥️ Splunk Server Installation

Splunk Enterprise was installed on an **Ubuntu Server** virtual machine.

### Start Splunk

```bash
sudo /opt/splunk/bin/splunk start
```

The Splunk Web Interface was accessed via:

```
http://192.168.56.10:8000
```

Splunk was configured to receive logs from multiple systems using the **Splunk Universal Forwarder** and **Syslog**.

---

## 🪟 Windows Log Forwarding (Host Machine)

The **Splunk Universal Forwarder** was installed on the Windows host system.

Windows Event Logs were configured in `inputs.conf`:

```ini
[WinEventLog://Application]
disabled = 0

[WinEventLog://Security]
disabled = 0

[WinEventLog://System]
disabled = 0
```

Logs were forwarded to the Splunk server at:

```
192.168.56.10:9997
```
## Windows Forwarder Running
![Windows Forwarder](../screenshots/windows_forwarder.png)

---

## 🐉 Kali Linux Log Forwarding

The Splunk Universal Forwarder was installed on Kali Linux to monitor system logs.

Logs were monitored using:

```bash
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log
```

The logs were then forwarded to the Splunk SIEM server.

## Kali Forwarder Connection
![Kali Forwarder](../screenshots/kali_forwarder.png)

---

## 🔥 pfSense Firewall Logging

The pfSense firewall was configured to forward logs to Splunk using **Syslog**.

**Navigation:** `Status → System Logs → Settings`

Remote syslog server configuration:

```
192.168.56.10:514
```

## pfSense Syslog Configuration
![pfSense Syslog](../screenshots/pfsense_syslog_config.png)

---

## 🚨 Suricata IDS Setup

Suricata was installed using the **pfSense package manager**.

### Configuration Steps

1. Install Suricata package
2. Enable IDS on the LAN interface
3. Enable alert logging

Suricata generates intrusion detection alerts, which are forwarded to Splunk through syslog.

## Suricata IDS
![Suricata Alerts](../screenshots/suricata_alerts.png)
---

## 📊 Log Collection in Splunk

Splunk successfully ingested logs from multiple sources.

| Source | Log Type |
|---|---|
| Windows Host | Windows Event Logs |
| Kali Linux | System Logs |
| pfSense | Firewall Logs |
| Suricata | IDS Alerts |


### Verification Query

The following SPL query was used to confirm log ingestion:

```spl
| stats count by host
```

This query verifies that logs from multiple hosts are being indexed in Splunk.

## Logs Ingested in Splunk
![Logs in Splunk](../screenshots/logs_in_splunk.png)
---

## ⚠️ Challenges & Troubleshooting

### 🌐 1. Network Configuration Issues

**Problem:** Virtual machines were placed on different networks, preventing communication with the Splunk server.

**Solution:** All machines were placed on the same **VirtualBox Host-Only Network** (`192.168.56.x`), allowing communication between:
- Splunk Server
- Kali Linux
- pfSense
- Windows Host

---

### 🔧 2. Incorrect Forwarder Server IP

**Problem:** Some forwarders were initially configured with an incorrect Splunk server IP.

**Solution:** The forward server was corrected to:

```
192.168.56.10:9997
```

After restarting the forwarder service, logs were successfully forwarded.

---

### 🐉 3. Kali Logs Not Appearing in Splunk

**Problem:** Kali logs were not appearing because log monitoring inputs were not configured.

**Solution:** Log monitoring was added manually:

```bash
sudo splunk add monitor /var/log
```

After restarting the forwarder, logs began appearing in Splunk.

---

### 🔥 4. pfSense Logging Port Misconfiguration

**Problem:** pfSense was initially configured to send syslog to port `8000`, which is used by the Splunk Web Interface.

**Solution:** The syslog port was corrected to:

```
192.168.56.10:514
```

---

### ⚙️ 5. Splunk Receiving Port Not Enabled

**Problem:** Forwarders were unable to send logs because the Splunk receiving port was not enabled.

**Solution:** The receiving port was configured in Splunk:

```
Settings → Forwarding and Receiving → Configure Receiving → 9997
```

After enabling the port, forwarders successfully connected.

---

## ✅ Final Outcome

The SIEM lab successfully collects and indexes logs from multiple systems.

**Log sources include:**
- Windows Event Logs
- Linux System Logs
- pfSense Firewall Logs
- Suricata IDS Alerts

**These logs can now be used for:**
- 🛡️ Security Monitoring
- 🔎 Threat Detection
- ⚔️ Attack Simulation Analysis
- 🧑‍💻 SOC Training Exercises

---

## 🎯 Conclusion

This project demonstrates the deployment of a functional SIEM home lab capable of ingesting logs from multiple network sources.

The environment simulates a basic SOC monitoring infrastructure, enabling:
- Centralized log collection
- Security event monitoring
- Threat investigation using Splunk

This lab provides practical experience in **SIEM deployment**, **log forwarding**, **IDS monitoring**, and **security analysis** — essential skills for SOC Analysts and Security Engineers.
