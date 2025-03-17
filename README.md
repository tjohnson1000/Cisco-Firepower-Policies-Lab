# Cisco Firepower Threat Defense (FTD) & Firepower Management Center (FMC) Policies

## Overview
This project provides a comprehensive **Cisco Firepower Threat Defense (FTD) and Firepower Management Center (FMC) policy configuration** using **EVE-NG**. The guide includes **basic setup, NAT policies, access control policies (ACP), security intelligence, IPS configuration, and malware/file policies** to secure network environments effectively.

## Lab Setup
The topology consists of:
- **Cisco Firepower Threat Defense (FTD) 6.7.0-65**
- **Cisco Firepower Management Center (FMC) 6.7.0-65**
- **Switches**: `i86bi_linux_l2-adventerprisek9-ms.SSA.high_iron_20190423.bin`
- **ISP Router**: `IOL i86bi_LinuxL3-AdvEnterpriseK9-M2_157_3_May_2018.bin`
- **Kali-PC**: `Docker eve-kali-large`
- **Admin PC, DMZ Server, Internet-PC**: `Docker eve-gui-server`
- **HQ-PC**: `Windows 10 x86`
- <img src="https://i.imgur.com/XFOWKTy.png" height="80%" alt="FMC/FTD Policies Lab" />

## Objectives
- Configure **FMC and FTD basic settings**
- **Set up network objects** for secure access control
- Implement **NAT policies** for different network zones (Corporate, DMZ, Internet)
- Define **Access Control Policies (ACP)** to allow or deny specific traffic
- **Configure IPS, security intelligence, and malware/file policies** to enhance security
- **Deploy and verify** configuration changes

## Configuration Steps

### **1. Configure FMC and FTD Basic Settings**
#### **FMC Configuration**
```shell
Log into FMC with admin/Admin123
Accept EULA: YES
Set IP Address: 10.101.1.101/24
Set Gateway: 10.101.1.254
Set DNS: 8.8.8.8, 8.8.4.4
```

#### **FTD Configuration**
```shell
Log into FMC with admin/Admin123
Set IP Address: 10.101.1.102/24
Set Gateway: 10.101.1.254
Set Hostname: ftd
Configure firewall mode: routed
```

### **2. Configure Network Objects**
```shell
Navigate to FMC → Objects → Objects Management
Add Network Object: corp-network → 192.168.202.0/24
Add Network Object: dmz-srv-pri → 172.16.201.100
Add Network Object: dmz-srv-pub → 20.1.1.10
```

### **3. Configure NAT Policy**
#### **Dynamic Corporate Internet NAT Rule**
```shell
NAT Type: Dynamic
Source Interface: corporate
Destination Interface: internet
Translated Source: corp-nat-pub-pool (20.1.1.2-20.1.1.5)
```

#### **Dynamic DMZ Internet NAT Rule**
```shell
NAT Type: Dynamic
Source Interface: dmz-zone
Destination Interface: internet
Translated Source: dmz-srv-pub (20.1.1.10)
```

### **4. Configure Access Control Policies (ACP)**
#### **Inbound ACP (Internet to DMZ Server)**
```shell
Policy Name: outside-to-dmz-srv
Source Zones: internet
Destination Zones: dmz-zone
Allowed Services: SSH, FTP, HTTP
```

#### **Outbound ACP (DMZ to Internet)**
```shell
Policy Name: dmz-srv-to-outside
Source Zones: dmz-zone
Destination Zones: internet, corporate
Allowed Services: Any
```

#### **Outbound ACP (Corporate to Internet)**
```shell
Policy Name: corporate-internet
Source Zones: corporate
Destination Zones: internet, dmz-zone
Allowed Services: SSH, FTP, HTTP, HTTPS, DNS, ICMP
```

### **5. Security Intelligence Policy**
#### **Blocking Malicious IPs**
```shell
Navigate to FMC → Policies → Access Control
Enable Security Intelligence → Add blacklist objects
Save and Deploy Policy
```

### **6. IPS Configuration (Intrusion Prevention System)**
#### **Create Custom IPS Policy**
```shell
Navigate to FMC → Policies → Intrusion
Create Policy: EVE-IPS-POLICY
Enable: Drop when Inline
Add Rule: DDoS to DMZ Server (Destination IP: 172.16.201.100, Port 80)
```

### **7. Configure Malware & File Policy**
#### **Blocking Malicious Files**
```shell
Navigate to FMC → Policies → Malware & File
Create Policy: EVE-FILE-POLICY
Block File Types: Executables, PDFs, Office Documents
Apply Policy to Corporate ACP
```

## **Verification**
### **1. Verify Connectivity**
```shell
Ping corporate gateway: ping 192.168.202.254
Ping DMZ gateway: ping 172.16.201.254
Ping Internet gateway: ping 8.8.8.8
```

### **2. Verify NAT Policy**
```shell
show nat
```

### **3. Verify Access Control Logs**
```shell
Navigate to FMC → Analysis → Events
Check blocked and allowed connections
```

### **4. Verify Malware & File Policy**
```shell
Navigate to FMC → Analysis → File Events
Check detected and blocked malicious files
```

## Repository Structure
```
├── configs/
│   ├── FMC_config.txt   # Configuration for Firepower Management Center
│   ├── FTD_config.txt   # Configuration for Firepower Threat Defense
├── README.md          # Documentation
├── screenshots/       # Screenshots of verification outputs
└── topology.png       # Network diagram
```

## **Conclusion**
This project demonstrates the configuration and deployment of **Cisco Firepower Threat Defense (FTD) and Firepower Management Center (FMC) policies**. It covers NAT, access control, IPS, security intelligence, and malware protection to enhance network security.

---
**Author:** Travis Johnson  
**Company:** 10Digit Solutions LLC  
**GitHub Repository:** [Add link when available]
