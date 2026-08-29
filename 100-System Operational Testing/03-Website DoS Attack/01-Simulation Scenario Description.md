# Simulation Scenario 03: Website DoS Attack

## Overview
Simulate a Denial of Service (DoS) attack using the SYN Flood method. The objective is to exhaust the resources of the Web Server by flooding it with forged connection requests from the external Internet environment. Through this, verify the detection and logging capabilities of the Firewall/IDS/IPS as well as the SOC's alert aggregation capabilities.

## Scenario Parameters
- Actor / Source: Internet User - Kali Linux (a device located in the external internet environment)
- Attack Target: Public IP address (WAN IP on pfSense) of the system.

## Monitoring Objectives
- Verify the Firewall's capability to identify anomalous traffic.
- Verify IDS/IPS detection of DoS/Flood attack signatures.
- Verify the capability to collect logs from Firewall, IDS/IPS, and Web Server regarding the sudden spike in status codes.
- Evaluate Wazuh's data correlation capability in generating high-severity alerts.

## Command Execution
```sudo hping3 -S -p 80 --flood --rand-source xxx.xxx.xxx.xxx```
