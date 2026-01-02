# Active Directory Attack Detection and Analysis with Splunk SIEM

## Objective

The Active Directory/Splunk Detection Lab project aimed to design and establish a controlled, realistic environment for simulating and detecting cyber attacks and then analyzing the resulting telemetry using a Security Information and Event Management (SIEM) platform. The primary focus was to ingest and analyze logs within a SIEM system, generating test telemetry to mimic real world attack scenarios. This hands on experience was designed to deepen my understanding of Windows security logging, attack patterns, attack detection workflows commonly used by Security Operations Centers (SOC).

### Skills Learned

- Advanced understanding of SIEM deployment, concepts and practical application.
- Virtualized network design and resource optimization.
- Proficiency in analyzing and interpreting logs.
- Ability to generate and recognize attack signatures and patterns.
- Development of critical thinking and problem solving skills in cybersecurity.

### Tools Used

- **VirtualBox:** Virtualization platform used to host and manage lab virtual machines.
- **Windows Server 2022 Active Directory Domain Services:** Centralized identity management and authentication infrastructure.
- **Windows 10:** Domain joined endpoint used to simulate user activity and attack targets.
- **Kali Linux:** Attack simulation platform for telemetry generation to create realistic logging and attack scenarios.
- **Ubuntu Splunk Enterprise Server:** SIEM platform used to ingest, index, and analyze Windows security telemetry
- **Splunk Universal Forwarder:** Log collection agent configured on Windows systems to forward Security, System, Application, and Sysmon logs.

## Steps

### 1: Create Network Diagram

The first step in this project was to design and document the overall lab architecture using a network diagram. This diagram was created to clearly illustrate system roles and data flows between the attacker, endpoint, domain controller, and SIEM components prior to implementation. All IP addresses shown in the diagram use non routable documentation ranges (RFC 5737) rather than real infrastructure addresses. The architecture was designed to operate in phases to accommodate host resource constraints while maintaining a realistic Active Directory environment and SIEM ingestion behavior.

*Ref 1: Network Diagram*

<img width="680" height="543" alt="image" src="https://github.com/user-attachments/assets/0e5bc74e-016a-4aeb-a481-1283e35283f4" />

### 2: Virtual Machine Creation

Four virtual machines were created to represent each role within the lab environment, allowing for isolation, repeatability, and safe experimentation. All systems were provisioned on the same isolated NAT network as defined in the network diagram to ensure reliable inter host communication while preventing direct exposure to the host network. Due to host system memory constraints, virtual machine resources were intentionally sized to support phased operation as previously mentioned.

**Active Directory Domain Controller**
- Operating System: Windows Server 2022
- Role: Active Directory Domain Services (AD DS), DNS
- Network: NAT (Static IP)
- Purpose: Centralized identity management and authentication

**Windows 10 Endpoint**
- Operating System: Windows 10
- Role: Domain-joined workstation
- Network: NAT (DHCP)
- Purpose: Target system for authentication-based attack simulation

**Splunk Server**
- Operating System: Linux (Ubuntu Server)
- Role: SIEM and log indexing platform
- Network: NAT (Static IP)
- Purpose: Centralized log ingestion, storage, and analysis

**Attacker System**

Operating System: Kali Linux

Role: Attack simulation platform

Network: NAT (Static IP)

Purpose: Generation of authentication abuse and brute-force telemetry

### 3: Software Installation & Configuration

### 4: Configuring Active Directory

### 5: Telemetry Generation and Analysis
