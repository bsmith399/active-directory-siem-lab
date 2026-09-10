### Project 1: Multi-OS Home Lab Architecture & SIEM Telemetry Pipeline

### 🎯 Project Objectives

The core objective of this project is to build a functional, isolated enterprise-grade laboratory network to engineering and validate a robust **SIEM and Threat Detection Pipeline**.

This lab was designed to meet the following specific technical goals:
1.  **Isolate** a multi-vendor environment within Oracle VirtualBox (Windows 11, Server 2022, Ubuntu, Kali Linux).
2.  **Engineer** a centralized security telemetry pipeline using Wazuh Manager and lightweight agents with Sysmon integration.
3.  **Simulate** real-world malicious activity (credential attacks, port scanning) using Kali Linux against the target domain.
4.  **Analyze** security logs and domain authentication events to validate detection capabilities and alert accuracy.

### 🧠 **Skills Learned & Applied**

Through this project, I demonstrated and validated technical proficiency in the following domains:

*   **SIEM Design & Implementation:** Deployed and engineered a Wazuh Central Manager pipeline on Ubuntu to collect and correlate log telemetry from multi-vendor hosts.
*   **Windows & Active Directory Administration:** Built and administered a simulated Windows domain environment (Windows Server 2022 and Windows 11 clients) to replicate enterprise monitoring challenges.
*   **Vulnerability Assessment & Attack Simulation:** Utilized Kali Linux utilities (including Nmap and Hydra) to perform controlled network scans and brute-force testing to validate detection visibility.
*   **Telemetry Tuning (Sysmon & Event Logs):** Engineered enhanced endpoint visibility by deploying Sysmon and customizing Windows Event Log auditing policies for high-value authentication events (e.g., Event IDs 4624/4625).

  ### **🛠️ Tools Used**
*   **Virtualization:** Oracle VM VirtualBox
*   **Offensive Security:** Kali Linux, Nmap, Hydra
*   **Defensive Security/SIEM:** Wazuh Manager (Ubuntu), Wazuh Agent, Sysmon
*   **Operating Systems:** Windows 11, Windows Server 2022, Ubuntu, Kali Linux

 **Architecture Diagram:**

```text
+---------------------------------------------------------------------------------------------------+
|                                  ORACLE VM VIRTUALBOX HOST ENVIRONMENT                            |
|                               (Host-Only / NAT Virtual Subnet: 192.168.1.0/24)                    |
+---------------------------------------------------------------------------------------------------+
                                                  |
       +-------------------------+----------------+-------------------------+
       |                         |                                          |
       v                         v                                          v
+------------------+     +-------------------+                      +-------------------+
|       kali       |     |      Ubuntu       |                      |    Windows111     |
|   (Kali Linux)   |     |  (Ubuntu Linux)   |                      |    (Windows 11)   |
|                  |     |                   |                      |                   |
|  * Attacker Box  |     |  * SIEM Engine    |                      |  * Client Host    |
|  * Threat Sim    |     |  * Wazuh Manager  |                      |  * Sysmon Active  |
|  * Port Scans    |     |  * Dashboard      |                      |  * Wazuh Agent    |
+------------------+     +-------------------+                      +-------------------+
        |                          ^                                          ^
        |                          |                                          |
        |  [Attack Telemetry]      |         [Encrypted Log Stream]           |
        +--------------------------|------------------------------------------+
        |  (Hydra / Nmap Probes)   |         (Ports 1514 / 1515)
        |                          |
        v                          |         [Encrypted Log Stream]
+------------------+               |         (Event IDs 4624/4625)
|    Server2022    |               |
| (Win Server 22)  |               |
|                  |               |
|  * Domain Ctrl   |---------------+
|  * Active Dir.   |
|  * Wazuh Agent   |
+------------------+
```

#### 🖥️ Virtual Environment Breakdown
* **`Ubuntu` (SIEM Engine & Workstation):** Runs Wazuh Manager to collect, correlate, and parse log telemetry from all networked endpoints.
* **`Server2022` (Active Directory Domain Controller):** Configured with AD DS, Group Policy (GPO), and advanced audit logging to capture authentication events (`Event ID 4624`/`4625`).
* **`Windows111` (Target Client Endpoint):** Monitored workstation equipped with Sysmon and a Wazuh Agent forwarding endpoint process telemetry.
* **`kali` (Attacker Machine):** Isolated testing host used to generate traffic, execute port scans, and simulate authentication brute-force attacks.

   # Detailed Implementation Steps: Setting Up the Home Lab

Here is the step-by-step process used to design and deploy the isolated, multi-OS enterprise environment in Oracle VM VirtualBox.

### Phase 1: Environment & Virtual Network Configuration

1.  **Virtualization Setup:** Installed Oracle VM VirtualBox as the hypervisor to host the entire lab environment locally.
2.  **VM Provisioning:** Deployed all five required virtual guest machines (Kali, Ubuntu, Win Server 2022, Win 11). Ensured minimum hardware specifications were met to support simultaneous execution (minimum ~8GB RAM total overhead).
3.  **Network Engineering:**
    *   Configured an isolated, internal VirtualBox Network (Host-Only Adapter) to function as the "simulated enterprise internal network" (192.168.1.0/24). This contains all attack traffic within the lab.
    *   Configured an optional NAT Adapter on the `Ubuntu` SIEM host only, allowing specific outbound internet access for package updates and initial Wazuh installation.

### Phase 2: Active Directory & Endpoint Configuration

4.  **Domain Controller Setup:** Installed and configured Windows Server 2022 as a functional Active Directory Domain Controller (AD DS). Promoted the server to a new domain (e.g., `brandonsmith.lab`).
5.  **Audit Policy Tuning:** Enabled advanced security auditing via Group Policy Object (GPO) to ensure crucial authentication events are logged to the Windows Security Log (specifically Event ID 4624/4625 for Logon/Failed Logon, 4672 for Privilege Use).
6.  **Workstation Deployment:** Installed the Windows 11 target machine and joined it to the created Active Directory domain to simulate real-world client interaction and central policy management.
7.  **Sysmon Deployment:** Installed Microsoft Sysmon on both Windows target machines (`Windows111` and `Server2022`) using a known modular configuration file (such as SwiftOnSecurity's config) to track detailed process creation and network connection telemetry.

### Phase 3: Wazuh SIEM Deployment

8.  **SIEM Manager Installation:** Installed and configured the Wazuh Manager engine on the specialized Ubuntu Virtual Machine. Verified the core services (Manager, Indexer, Dashboard) were operational.
9.  **Agent Deployment:** Lightweight Wazuh agents were generated and installed on all target guest systems (`Windows111`, `Server2022`, and local agents on the `Ubuntu` host). Verify two-way connectivity via the Wazuh Dashboard over ports 1514 and 1515.
10. **Telemetry Tuning:** Custom-tuned the `ossec.conf` file on the Windows agents to ingest and parse specific Sysmon operational logs and high-value Windows Event channels that are crucial for threat detection.

