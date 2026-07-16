<img width="945" height="902" alt="image" src="https://github.com/user-attachments/assets/ce958ee5-532d-47da-8a29-bb27290828ae" />


# **LAB 02: STATIC ROUTING FOUNDATIONS & NETWORK HARDENING**

**DOCUMENT CLASSIFICATION:** INTERNAL USE ONLY  
**PROJECT ID:** LAB-02-NET-SEC  
**DATE:** June 24, 2026  
**AUTHOR:** P. Apondja

## **1\. EXECUTIVE SUMMARY**

This report documents the implementation and security hardening of a multi-router network infrastructure for Lab 02\. The project scope involved establishing connectivity between two geographically disparate LAN segments via a dedicated WAN uplink, utilizing Cisco Layer 3 routing platforms. Furthermore, infrastructure integrity was ensured through the implementation of administrative hardening on all network edge devices.

## **2\. PROJECT OBJECTIVES**

* **Scalable Infrastructure Deployment:** Expansion of the network topology to support multi-router integration, simulating a defense-grade enterprise environment.  
* **Static Routing Configuration:** Implementation of static routing protocols to facilitate bidirectional traffic between segmented, isolated network domains.  
* **Security Hardening (Device Integrity):** Mitigation of unauthorized access through privileged EXEC-mode protection and console port authentication.

## **3\. NETWORK TOPOLOGY & ARCHITECTURE**

The infrastructure consists of three logically segmented IP networks, interconnected via a point-to-point WAN link between two edge routers.

### **3.1 Hardware Inventory**

| Device Name | Hardware Model | OS Version | Primary Function |
| :---- | :---- | :---- | :---- |
| **Router-1** | Cisco ISR 2911 | Cisco IOS | Gateway/WAN Edge |
| **Router-2** | Cisco ISR 2911 | Cisco IOS | Gateway/WAN Edge |
| **Switch-A** | Cisco Catalyst 2960 | Cisco IOS | Access Layer (Office LAN) |
| **Switch-B** | Cisco Catalyst 2960 | Cisco IOS | Access Layer (Data Center LAN) |
| **PC-A** | Generic Endpoint | Windows | Client Workstation |
| **Server-B** | Generic Endpoint | Server OS | Enterprise Data Server |

## **4\. IP ADDRESSING & SUBNETTING PLAN**

All subnets are configured with a /24 (255.255.255.0) mask to ensure address space optimization.

| Segment | Subnet | Interface | IP Address | Gateway |
| :---- | :---- | :---- | :---- | :---- |
| **Office LAN** | 192.168.10.0/24 | PC-A / R1 GigE0/0 | 192.168.10.1 / .254 | 192.168.10.254 |
| **WAN Link** | 172.16.1.0/24 | R1 GigE0/1 / R2 GigE0/1 | 172.16.1.1 / .1.2 | N/A |
| **Server LAN** | 10.0.0.0/24 | Server-B / R2 GigE0/0 | 10.0.0.1 / .0.254 | 10.0.0.254 |

## **5\. DEVICE CONFIGURATION LOGS**

### **5.1 Router-1**

```
!
interface GigabitEthernet0/0
 description LAN_Office
 ip address 192.168.10.254 255.255.255.0
 no shutdown
!
interface GigabitEthernet0/1
 description WAN_Uplink
 ip address 172.16.1.1 255.255.255.0
 no shutdown
!
ip route 10.0.0.0 255.255.255.0 172.16.1.2
!
enable secret cisco123
line console 0
 password defensie2026
 login
!
```

### **5.2 Router-2**

```
!
interface GigabitEthernet0/0
 description LAN_Server
 ip address 10.0.0.254 255.255.255.0
 no shutdown
!
interface GigabitEthernet0/1
 description WAN_Uplink
 ip address 172.16.1.2 255.255.255.0
 no shutdown
!
ip route 192.168.10.0 255.255.255.0 172.16.1.1
!
enable secret cisco123
line console 0
 password defensie2026
 login
!
```

## **6\. CONNECTIVITY VALIDATION**

End-to-end verification via ICMP (Ping) from PC-A (192.168.10.1) to Server-B (10.0.0.1).

### **6.1 Performance Analysis**

* **Initial Packet Loss:** Observed 50% loss during the initial sequence. This is attributed to standard Address Resolution Protocol (ARP) resolution times across the WAN segment.  
* **Successful Verification:** Subsequent packets established stable connectivity with a TTL of 126, confirming two Layer 3 hops and correct routing table convergence.

