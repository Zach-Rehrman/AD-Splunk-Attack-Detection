# Active Directory Attack Detection and Analysis with Splunk SIEM

## Objective

The Active Directory/Splunk Detection Lab project aimed to design and establish a controlled, realistic environment for simulating and detecting cyber attacks and then analyzing the resulting telemetry using a Security Information and Event Management (SIEM) platform. The primary focus was to ingest and analyze logs within a SIEM system, generating test telemetry to mimic real world attack scenarios. This hands on experience was designed to deepen my understanding of Windows security logging, attack patterns, attack detection workflows commonly used by Security Operations Centers (SOC).

### Skills Learned

- Advanced understanding of SIEM deployment, concepts and practical application
- Virtualized network design and resource optimization
- Proficiency in analyzing and interpreting logs
- Ability to generate and recognize attack signatures and patterns
- Development of critical thinking and problem solving skills in cybersecurity

### Tools Used

- **VirtualBox:** Virtualization platform used to host and manage lab virtual machines
- **Windows Server 2022 Active Directory Domain Services:** Centralized identity management and authentication infrastructure
- **Windows 10:** Domain joined endpoint used to simulate user activity and attack targets
- **Kali Linux:** Attack simulation platform for telemetry generation to create realistic logging and attack scenarios
- **Ubuntu Splunk Enterprise Server:** SIEM platform used to ingest, index, and analyze Windows security telemetry
- **Splunk Universal Forwarder:** Log collection agent configured on Windows systems to forward Security, System, Application, and Sysmon logs

## Steps

### 1: Create Network Diagram

The first step in this project was to design and document the overall lab architecture using a network diagram. This diagram was created to clearly illustrate system roles and data flows between the attacker, endpoint, domain controller, and SIEM components prior to implementation. All IP addresses shown in the diagram use non routable documentation ranges (RFC 5737) rather than real infrastructure addresses. The architecture was designed to operate in phases to accommodate host resource constraints while maintaining a realistic Active Directory environment and SIEM ingestion behavior.

*Ref 1: Network Diagram*

<img width="680" height="543" alt="image" src="https://github.com/user-attachments/assets/0e5bc74e-016a-4aeb-a481-1283e35283f4" />

### 2: Virtual Machine Creation

Four virtual machines (VMs) were created to represent each role within the lab environment, allowing for isolation, repeatability, and safe experimentation. All of the VMs were created utilizing VirtualBox which I had already previously installed and enjoy. All systems were provisioned on the same isolated NAT network as defined in the network diagram to ensure reliable inter host communication while preventing direct exposure to the host network. Static IP addresses were assigned to core infrastructure systems to ensure reliable service availability and simplified troubleshooting. The Windows 10 endpoint was intentionally left on DHCP to reflect typical enterprise workstation behavior and to simulate realistic endpoint conditions during attack and detection scenarios. During this step, I first tried using Google's DNS server as my name server (8.8.8.8) but I could only ping IP addresses, not domain names meaning external DNS did not work. I discovered that I needed to use the NAT adapter's virtual router as the name server. Hostnames were changed once each VM was confirmed operational to easily identify each machine. Due to host system memory constraints, virtual machine resources were intentionally sized to support phased operation as previously mentioned.

**Active Directory Domain Controller**
- Operating System: Windows Server 2022
- Role: Active Directory Domain Services (AD DS)
- Network: NAT (Static IP)
- Purpose: Centralized identity management and authentication

**Windows 10 Endpoint**
- Operating System: Windows 10
- Role: Domain joined workstation
- Network: NAT (DHCP)
- Purpose: Target system for attack simulation

**Splunk Server**
- Operating System: Linux (Ubuntu Server)
- Role: SIEM and log indexing platform
- Network: NAT (Static IP)
- Purpose: Centralized log ingestion, storage, and analysis
- Additional Details: Changing the network configuration of the Ubuntu server is more of an intensive process and was the first time I had to do something like this in Ubuntu. I had to change the /etc/netplan/50-cloud-init.yaml configuration, disabling dhcp and adding a valid nameserver along with a default route to the default gateway.

**Attacker System**
- Operating System: Kali Linux
- Role: Attack simulation platform
- Network: NAT (Static IP)
- Purpose: Generation of attack telemetry

### 3: Software Installation & Configuration

With the virtual machines provisioned the next phase focused on installing and configuring the core software components required for identity management, telemetry generation, and centralized log analysis. During this step I created a shared folder on my host machine that could be accessed by VMs that included software like Splunk Enterprise installer. To make the shared folder accessible for my Ubuntu server, I needed to install the virtualbox-guest-additions-iso and vboxsf to add the appropriate user the group. I then needed to mount the shared folder onto the directory that was created in the Ubuntu server.

**Splunk Installation**
- Installed Splunk Enterprise on a Linux based server and configured it as the centralized SIEM platform
- Created a shared folder on the host system to stage installation media and supporting files for use by virtual machines
- Installed VirtualBox Guest Additions on the Ubuntu server to enable shared folder support and configured the vboxsf group and user permissions to allow the Splunk service account access to the shared directory
- Mounted the shared folder to a dedicated directory on the Ubuntu server for installer execution and file management
- After confirming Splunk Enterprise is operational, I configured Splunk to start on boot for the splunk user
- Tested the Splunk server was reachable from the other endpoints within the network by searching the IP of the Splunk server on port 8000

**Endpoint Telemetry Configuration**
- Installed Sysmon on both the domain controller and Windows 10 endpoint to capture detailed process, network, and authentication telemetry
- Applied the Olaf Hartong Sysmon configuration to ensure consistent telemetry across hosts
- Verified Sysmon event generation locally using Windows Event Viewer prior to forwarding

**Log Forwarding**
- Installed the Splunk Universal Forwarder on Windows systems
- Configured inputs to forward Windows Security, System, Application, and Sysmon event logs using inputs.conf
- Changed log on as from NT Service to local system account due to limitations with log collecting
- Restarted universal forwarder service and validated successful log forwarding through test events and index searches
- Created custom indexes to separate endpoint and authentication telemetry
- Validated logs were being forwarded using Splunk's Search and Reporting App

### 4: Configuring Active Directory

This step involved installing and configuring Active Directory to establish a functional domain environment for authentication and telemetry collection. The Windows Server VM was prepared with the Active Directory Domain Services (AD DS) role, promoted to a domain controller, and configured with users, groups, and security policies. The Windows 10 endpoint was then joined to the domain to ensure that authentication events would be generated in a realistic enterprise environment.

**Active Directory Installation & Domain Controller Promotion**
- Installed Active Directory Domain Services (AD DS) on the Windows Server system
- Promoted the server to a domain controller and created a new Active Directory domain

**User & Group Configuration**
- Configured domain users and groups to represent typical enterprise roles
- Applied password policies to simulate realistic authentication constraints

**Joining the Windows 10 Endpoint**
- Needed to change this endpoints DNS server to the DCs IP address
- Configured the Windows 10 VM as a domain joined workstation
- Verified connectivity to the domain controller and successful Kerberos authentication

### 5: Telemetry Generation and Analysis
