# Project: Endpoint Detection and Response (EDR) Implementation and Threat Hunting

## Project Overview
This repository documents my hands-on deployment, configuration, and operational testing of Microsoft Defender for Endpoint. The objective was to establish a baseline EDR environment, simulate real-world attack techniques, and perform end-to-end incident investigation to validate detection capabilities and refine Security Operations (SecOps) response procedures.

## Objectives
* Initialize and configure the Microsoft Defender for Endpoint environment, including role-based access control and device grouping.
* Successfully onboard endpoints and validate telemetry ingestion.
* Execute controlled attack simulations covering persistence, privilege escalation, and command-and-control (C2) communication.
* Investigate generated alerts and incidents using the Defender XDR portal, analyzing the attack story and entity relationships.

## Tools and Technologies
* Microsoft Defender XDR / Microsoft Defender for Endpoint
* PowerShell and Command Prompt (for attack simulation and onboarding)
* Kusto Query Language (KQL) for advanced hunting

## Prerequisites
* Microsoft Defender XDR tenant environment.
* Windows 10/11 virtual machine for onboarding and attack simulation.

## Implementation Phases

### Phase 1: Environment Initialization and Device Onboarding
1. **Initialized Defender for Endpoint**: Navigated to the Defender XDR portal and configured Standard discovery under Device Discovery settings to ensure optimal device visibility across the network.
2. **Generated Onboarding Package**: Created a custom onboarding package via the Defender deployment tool. I generated a unique access key and downloaded the executable package.
3. **Onboarded the Endpoint**: Executed the deployment tool on the target Windows virtual machine, pasted the access key, and verified the successful onboarding status in the portal.
4. **Configured Access Control**: Created a custom "Tier 1 Support" role with read and manage permissions for day-to-day security operations. I assigned this role to the `sg-IT` user group to enforce least privilege.
5. **Established Device Groups**: Created a "Regular" device group targeting Windows 11 devices. I assigned full remediation capabilities and granted access to the `sg-IT` group to ensure automated response policies are applied correctly.

[Screenshot] (images/defender-device-groups-configuration.png)  
*Caption: Device group configuration showing remediation level and user access assignments.*

### Phase 2: Attack Simulation and Detection Validation
1. **Validated Onboarding**: Ran the official Microsoft detection test script via an elevated Command Prompt to confirm the sensor was actively reporting telemetry to the cloud.
2. **Simulated Persistence**: Executed a registry modification command to add a malicious entry to the `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run` key. This simulates an attacker establishing persistence on a host.
3. **Simulated Privilege Escalation**: Created a new local user account and added it to the local Administrators group using `net user` and `net localgroup` commands to mimic lateral movement preparation.
4. **Simulated C2 Communication**: Executed a PowerShell script designed to generate a high volume of randomized DNS TXT queries to a mock external domain. This simulates beaconing behavior to a command-and-control server.

[Screenshot] (images/defender-simulated-attack-powershell.png)
*Caption: PowerShell execution of the simulated C2 DNS beaconing script.*

### Phase 3: Incident Investigation and Response
1. **Alert Triage**: Navigated to the Alerts queue and investigated the "[TestAlert] Suspicious PowerShell commandline" alert. I reviewed the alert story timeline and associated remediation recommendations.
2. **Incident Correlation**: Opened the correlated incident ("Multi-stage incident involving Defense evasion & Discovery on one endpoint"). I analyzed the dynamic Incident Graph to visualize the relationship between the compromised host, the malicious process, and the external IP address.
3. **Entity Analysis**: Investigated the suspicious external IP address within the Evidence and Response tab. I reviewed its observed presence in other organizations to assess threat intelligence context.
4. **Incident Management**: Tagged the incident with "Simulation", assigned ownership to myself, and classified it as "Informational, expected activity - Security testing". This maintains accurate metrics and properly closes the loop on the investigation.

[Screenshot] (images/defender-incident-graph-analysis.png)  
*Caption: Defender XDR incident graph visualizing the attack chain and entity relationships.*

## Key Findings and Outcomes
* Validated that the Defender for Endpoint sensor correctly identifies and categorizes living-off-the-land (LotL) techniques, such as malicious PowerShell execution and registry manipulation.
* Demonstrated proficiency in navigating the Defender XDR incident graph to trace attack progression from initial execution to potential C2 communication.
* Built a repeatable framework for onboarding devices and assigning role-based access, ensuring the SecOps team has the correct permissions to manage incidents effectively.
