<img width="956" height="772" alt="image" src="https://github.com/user-attachments/assets/4278a681-91f2-4237-bd06-1a1add6ca312" />




# **LAB 25: ENTERPRISE WAN ROUTING & SITE-TO-SITE CONNECTIVITY (MASTER DOC)**

**Document Type:** Enterprise WAN Architecture & Cross-Site Routing

**Project Name:** Inter-Site Network Connectivity (Site-A to Site-B)

**Author:** P. Apondja

**Status:** Completed & Fully Operational

## **1\. Executive Summary**

In dit lab is een betrouwbare **Site-to-Site WAN-verbinding** geconfigureerd tussen **Defensie-R1 (Site A)** en **Router-2 (Site B)**. Door middel van deterministische **Static Subnet Routing** over de WAN-backbone (10.0.0.0/24) is een end-to-end communicatiekanaal gerealiseerd tussen de IT-beheerszone (**VLAN 10 \- 172.16.10.0/24**) en de industriële productieomgeving (**VLAN 20 \- 10.0.20.0/24**).

Tijdens de oplevering zijn belemmerende IPsec-crypto maps gedevieerd en is een blokkerende Access Control List (PROTECT\_OT) op de sub-interface van Router-2 verwijderd. Hierdoor is een stabiele Layer 3-route en vlekkeloze dataoverdracht gewaarborgd.

## **2\. Network Topology & Addressing Matrix**

## 

| Device | Interface | IP Address / Subnet | Role / Associated Zone |
| :---- | :---- | :---- | :---- |
| **Defensie-R1** | GigabitEthernet0/1 | 10.0.0.1 /24 | WAN Interface (Site A) |
| **Defensie-R1** | GigabitEthernet0/0.10 | 172.16.10.1 /24 | Gateway VLAN 10 (IT Management) |
| **Router-2** | GigabitEthernet0/0 | 10.0.0.2 /24 | WAN Interface (Site B) |
| **Router-2** | GigabitEthernet0/1.20 | 10.0.20.254 /24 | Gateway VLAN 20 (OT Production) |
| **PC-A** | NIC | 172.16.10.11 /24 | Admin Workstation (Site A) |
| **PLC-Pomp-B** | NIC | 10.0.20.1 /24 | Industrial PLC Controller (Site B) |

## **3\. Applied Device Configurations**

### **Router-1 (Defensie-R1 WAN & Static Routing):**

```shell
! --- WAN INTERFACE CONFIGURATION ---
interface GigabitEthernet0/1
 ip address 10.0.0.1 255.255.255.0
 no crypto map
 no shutdown
exit

! --- STATIC ROUTE TO REMOTE OT SUBNET ---
ip route 10.0.20.0 255.255.255.0 10.0.0.2
```

### 

### 

### 

### 

### **Router-2 (Site-B WAN, Sub-Interface & Access Control Clean-up):**

```shell
! --- WAN INTERFACE CONFIGURATION ---
interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.0
 no crypto map
 no shutdown
exit

! --- OT LAN SUB-INTERFACE (VLAN 20) ---
interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 10.0.20.254 255.255.255.0
 no ip access-group PROTECT_OT out
 no ip access-group PROTECT_OT in
 no shutdown
exit

! --- STATIC ROUTE TO REMOTE IT SUBNET ---
ip route 172.16.10.0 255.255.255.0 10.0.0.1
```

## 

## **4\. Verification & Validation**

> 1. **ICMP End-to-End Test:**  
   * **Source:** PC-A (172.16.10.11)  
   * **Destination:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Result:** **Reply from 10.0.20.1: bytes=32 time\<1ms TTL=126**  
   * **Success Rate:** **100% (0% packet loss)**.  
> 2. **Routing Table Validation (show ip route):**  
   * S 10.0.20.0/24 \[1/0\] via 10.0.0.2 actief op Defensie-R1.  
   * S 172.16.10.0/24 \[1/0\] via 10.0.0.1 actief op Router-2.

**\[END OF LAB 25 DOCUMENTATION\]**

