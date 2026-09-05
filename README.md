# <div id="00" align="center"> Open-Source SIEM Deployment in Enterprise Network </div>

<div align="center">
   
[![SIEM - Wazuh](https://img.shields.io/badge/SIEM-Wazuh%20v4.14.2-blue?style=flat-square&logo=wazuh)](https://wazuh.com/)
[![Firewall - pfSense](https://img.shields.io/badge/Firewall-pfSense%20v2.8.1-orange?style=flat-square&logo=pfsense)](https://www.pfsense.org/)
[![IDS/IPS - Suricata](https://img.shields.io/badge/IDS%2FIPS-Suricata-red?style=flat-square)](https://suricata.io/)
[![OS - AlmaLinux](https://img.shields.io/badge/OS-AlmaLinux%20v9.7-orange?style=flat-square&logo=almalinux)](https://almalinux.org/)
[![Agent - Windows](https://img.shields.io/badge/Agent-Windows%2010-blue?style=flat-square&logo=windows)](https://microsoft.com/)
[![Agent - Ubuntu](https://img.shields.io/badge/Agent-Ubuntu%20LTS-red?style=flat-square&logo=ubuntu)](https://ubuntu.com/)

This project focuses on deploying an open-source Security Information and Event Management (SIEM) solution using **Wazuh**, integrated with the **pfSense** firewall system and **Suricata** IDS/IPS solution to protect a simulated enterprise network infrastructure. The system supports multi-source log collection, configuration management of monitoring agents, and the establishment of core features: **Login Authentication Monitoring**, **File Integrity Monitoring (FIM)**, and **Vulnerability Detection**.

</div>

---

<div align="center">

[Network Architecture Diagram & System Segmentation](#1)

[Project Directory Structure](#2)

[Technical Deployment Details & Device Configuration](#3)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[SOC Simulation Scenarios & Operational Validation](#4)

[Technology Stack & System Design Philosophy](#5)
   
[Disclaimer](#6)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[License](#7)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[Contributors](#8)

</div>

---

## <div id="1" align="center"> Network Architecture Diagram & System Segmentation </div>

| No. | Diagram Name | Function | Scope |
| :--- | :--- | :--- | :--- |
| 1 | **Detailed Logical Network Diagram** | Illustrates the complete realistic enterprise network architecture, integrating multi-WAN links, clear segmentation via Firewall, Core Switch, and independent IDS/IPS. | Entire enterprise system (Production-ready design) |
| 2 | **Simplified Logical Network Diagram** | Consolidates the structure to optimize virtualization resources on a single workstation for simulating core security functions and conducting security attack/monitoring experiments. | Lab simulation environment (VMware Workstation) |

<div align="right">
   
[*back to top*](#00)

</div>

---

## <div id="2" align="center"> Project Directory Structure </div>

```text
Open-Source-SIEM-Deployment-in-Enterprise-Network
   ├── 01-VM-Firewall/                            # pfSense and Suricata IDS/IPS Configuration
   ├── 02-VM-[Server] Web/                        # aaPanel, Apache, and Web Server Configuration
   ├── 03-VM-[PC] Ubuntu (Internal User)/         # Internal Ubuntu Client Configuration
   ├── 04-VM-[PC] Windows (Internal User)/        # Internal Windows Client Configuration
   ├── 05-VM-[Server] SOC/                        # Wazuh Manager SIEM/SOC Server Configuration
   ├── 06-VM-[PC] Kali Linux (Internet User)/     # External Attack Workstation Configuration
   ├── 100-System Operational Testing/            # Experimental scenarios and results reports
   ├── (Diagram)
   └── README.md
```

<div align="right">
   
[*back to top*](#00)

</div>

---

## <div id="3" align="center"> Technical Deployment Details & Device Configuration </div>

*   **Boundary Firewall & Intrusion Prevention System (IPS)**: Configuration of a 3-interface network (WAN, LAN, DMZ) on pfSense v2.8.1, routing rules, NAT Port Forwarding mechanisms, and the Suricata IDS/IPS system operating in Inline IPS mode with the ETOpen ruleset and specific action override rules. For details, see [01-VM](./01-VM-Firewall/).
*   **DMZ Web Service Server**: Setup of AlmaLinux v9.7 operating system, aaPanel v7.0.29 Pro management software, Apache v2.4.62 Web service publishing the domain name, along with directory permission policies and sensitive configuration file protection. For details, see [02-VM](./02-VM-%5BServer%5D%20Web/).
*   **SOC Server Security Monitoring System**: Detailed global configurations of Wazuh Manager, setup of raw log storage in JSON format, and the operational procedures of the wazuh-manager service. For details, see [05-VM](./05-VM-%5BServer%5D%20SOC/).
*   **Endpoint Security Monitoring Agents (Wazuh Agents)**:
    *   **Web Server & Ubuntu Client Agent**: Configured real-time File Integrity Monitoring (FIM) on critical system directories, Apache Web log monitoring, and periodic Syscollector vulnerability scanning. See [02-VM](./02-VM-%5BServer%5D%20Web/), [03-VM](./03-VM-%5BPC%5D%20Ubuntu%20(Internal%20User)/).
    *   **Windows Client Agent**: Configured system Registry monitoring, FIM on Startup and System32 directories, and filtered out noisy Event IDs from the Windows Security Event Channel (Security EventChannel). See [04-VM](./04-VM-%5BPC%5D%20Windows%20(Internal%20User)/).
*   **Centralized Log Forwarding Mechanism (Syslog-ng)**: Defined Destinations and Sources to capture pfSense firewall filter logs and Suricata EVE JSON logs across all three network segments for centralized forwarding to the SIEM SOC. For details, see [05-VM](./05-VM-%5BServer%5D%20SOC/).

<div align="right">
   
[*back to top*](#00)

</div>

---

## <div id="4" align="center"> SOC Simulation Scenarios & Operational Validation </div>

The project executes three simulated attack scenarios to validate the defense capabilities of the boundary Firewall/IPS and the alert correlation capabilities of the SIEM system. Scenario details, execution commands, and raw event logs are fully documented in [System Operational Testing](./100-System%20Operational%20Testing/).

### Scenario 01: Anomalous Login (Anomalous Authentication on Windows)
*   **Description**: Simulates a brute-force password attack targeting the local Administrator account on an internal Windows workstation to test local security policies and Wazuh Agent detection capabilities.
*   **Experiment**: The attacker executes a loop attempting network connection 100 consecutive times with increasingly incorrect passwords via Command Prompt.
*   **Results**: The Windows system automatically triggers the account lockout policy from the very first failure, returning an error. The Wazuh Dashboard records a corresponding spike, successively triggering Rule ID 60122 (Logon Failure), Rule ID 60204 (Multiple Windows Logon Failures, Level 10), and Rule ID 60115 (User account locked out, Level 9).

### Scenario 02: Sensitive File Modification
*   **Description**: Simulates attacker behavior after gaining control of a Linux workstation, attempting to modify configuration files, assign broad privileges, and delete temporary files to cover their tracks.
*   **Experiment**: Executes a sequence of commands to create, assign permissions, overwrite content, and delete a test configuration file on the Ubuntu machine.
*   **Results**: The File Integrity Monitoring (FIM) module on the Wazuh Dashboard captures all actions in real-time with system integrity violation alerts: Rule ID 554 (File added), Rule ID 550 (Integrity checksum changed, Level 7) which identifies changes in file hash and attributes, and Rule ID 553 (File deleted).

### Scenario 03: Website DoS Attack (Denial of Service from Internet)
*   **Description**: Simulates a TCP SYN Flood DDoS attack from an external Kali Linux machine to port 80 of the DMZ Web Server, aiming to exhaust connection processing resources.
*   **Experiment**: Uses a tool to flood millions of TCP SYN packets at maximum speed with a random source IP spoofing mechanism.
*   **Results**:
    *   **At the attacker end**: Achieved 100% packet loss (over 4.1 million packets sent but 0 response packets received) because the Suricata Inline IPS mechanism on pfSense worked effectively, completely dropping malicious packets using the Netmap driver.
    *   **At the security device**: Suricata recorded a Severity 2 alert with the detection signature (GPL SCAN loopback traffic).
    *   **At the SIEM system**: Wazuh recorded an explosion of alerts from the pfSense Agent within a few minutes, successfully triggering Rule ID 86601 (Suricata Alert) and warnings detecting traces of critical spoofed vulnerability exploitation.

<div align="right">
   
[*back to top*](#00)

</div>

---

## <div id="5" align="center"> Technology Stack & System Design Philosophy </div>

### <div align="center"> Technology Stack Used in Virtualization </div>

| No. | System Component | Tool / Deployed Software | Version / Configuration Details |
| :--- | :--- | :--- | :--- |
| 1 | **Infrastructure Virtualization** | VMware Workstation Pro | Version 17 |
| 2 | **Server Operating System** | AlmaLinux | Version 9.7 (For Web Server and SOC Server) |
| 3 | **Client Operating System** | Windows 10 & Ubuntu LTS | Windows 10 Pro & Ubuntu 24.04.3 LTS |
| 4 | **Firewall & Routing** | pfSense | Version 2.8.1 |
| 5 | **IDS/IPS System** | Suricata | Integrated on pfSense, using ETOpen ruleset |
| 6 | **Administration & Web Services** | aaPanel & Apache | aaPanel v7.0.29 Pro & Apache 2.4.62 |
| 7 | **SIEM/XDR System** | Wazuh | Wazuh Manager & Wazuh Agent v4.14.2 |
| 8 | **Centralized Log Management** | Syslog-ng | pfSense firewall log & Suricata EVE JSON format |

### <div align="center"> System Security Design Philosophy </div>

| No. | Design Philosophy | Practical Application |
| :--- | :--- | :--- |
| 1 | **Defense-in-Depth** | Integrates multiple independent security layers: The boundary layer uses a Firewall for service port filtering combined with an Inline IPS for proactive malware blocking; the server/workstation layer uses Wazuh Agents to collect local logs, monitor system integrity, and analyze end-user behavior. |
| 2 | **Network Segmentation & Isolation** | Completely separates the system into 3 distinct security zones (WAN - DMZ - LOCAL). Firewall rules strictly regulate access between zones, absolutely prohibiting DMZ from initiating reverse connections to LOCAL to stop lateral movement (Lateral Movement) risks. |
| 3 | **Least Privilege** | Establishes specific website access permissions and blocks sensitive website system files. Applies restriction and immediate account lockout policies on Windows Local Security Policy upon detecting abnormal password brute-forcing. |
| 4 | **Continuous Monitoring & Active Response** | Forwards all real-time system logs to the SOC Server via Syslog-ng configuration and Wazuh Agents. Configures real-time FIM auto-scanning and automatic system vulnerability detection/alerting, allowing the SOC operations team to formulate timely incident response plans. |

<div align="right">
   
[*back to top*](#00)

</div>

---

## <div id="6" align="center"> Disclaimer </div>

This project is built and deployed **solely for educational, academic research, and lawful information security experimentation purposes within a simulated Lab environment**. Any use of the scenarios, commands, or configuration information in this project to damage or unauthorizedly attack live production networks without written permission from the owner is strictly illegal. The authors and contributors of this project **bear absolutely no legal liability** for any misuse, damage, or consequences arising from the application of this project's contents.

<div align="right">
   
[*back to top*](#00)

</div>

---

## <div id="7" align="center"> License </div>

The project is fully open to the community. You are free to use, copy, and modify this content under the sole condition of citing the source and attaching a link back to the original repository. [Link](https://github.com/LamQuocDuy-LD/Open-Source-SIEM-Deployment-in-Enterprise-Network)

<div align="right">
   
[*back to top*](#00)

</div>

---

## <div id="8" align="center"> Contributors </div>

*   This entire repository (including system design and virtualization) was independently developed and fully implemented by Lam Quoc Duy.
*   **Note**: This is part of an overall project consisting of 3 members, with Lam Quoc Duy serving as the coordinating Team Leader. Other documents and items are handled by the remaining members and fall outside the scope of this repository.

<div align="right">
   
[*back to top*](#00)

</div>

---
