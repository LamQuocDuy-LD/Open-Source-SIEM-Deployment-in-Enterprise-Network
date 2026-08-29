# Simulation Scenario 01: Anomalous Login

## Overview
Simulate a brute-force password guessing attack to escalate privileges from within the internal network. The objective is to verify the SOC system's capability to detect, analyze, and trigger alerts upon detecting multiple consecutive authentication failures on Windows workstations.

## Scenario Parameters
- **Actor / Source:** Internal User - Windows
- **Attack Target:** Testing multiple failed login attempts with incorrect credentials for the Administrator account.

## Monitoring Objectives
- Verify the capability of the Wazuh Agent to collect authentication event logs.
- Check Wazuh default rules regarding Brute Force attack detection.
- Evaluate the alert response time on the Dashboard.

## Command Execution
```for /L %i in (1,1,100) do net use \\127.0.0.1\c$ "WrongPassword%i" /user:Administrator>```
