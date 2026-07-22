# **LAB 12: DYNAMIC ROUTING & INFRASTRUCTURE RESILIENCE (FINAL)**

**Document Type:** Network Engineering, Dynamic Routing & Fault Tolerance

**Project Name:** Implementatie van OSPFv2 & Network Convergence

**Author:** P. Apondja

**Status:** Fully Validated, Troubleshooted & Approved

## **1\. Executive Summary**

Dit document beschrijft de succesvolle implementatie van het **OSPFv2 (Open Shortest Path First)** dynamic routing protocol tussen Router-1 (Defensie-R1) en Router-2. De infrastructuur is nu in staat om IP-subnetten automatisch uit te wisselen over Area 0\. Netwerk- en bekabelingsfouten (waaronder interface misconfiguraties en conflicterende Access Control Lists) zijn tijdens de uitrol succesvol gediagnosticeerd en opgelost. End-to-end connectiviteit tussen het LAN van Router-1 en het IT-management netwerk op Router-2 is geverifieerd met een verliespercentage van 0%.

## **2\. Network Architecture & Addressing Plan**

## 

| Apparaat | Interface | IP-Adres / Subnet | OSPF Area | Functie / Netwerk |
| :---- | :---- | :---- | :---- | :---- |
| **Router-1** | GigabitEthernet0/0 | 172.16.1.1 /24 | Area 0 | LAN Gateway (PC-A Segment) |
| **Router-1** | GigabitEthernet0/1 | 10.0.0.1 /24 | Area 0 | P2P Transit Link naar Router-2 |
| **Router-2** | GigabitEthernet0/0 | 10.0.0.2 /24 | Area 0 | P2P Transit Link naar Router-1 |
| **Router-2** | GigabitEthernet0/1.10 | 10.0.10.254 /24 | Area 0 | IT Management Gateway |
| **Router-2** | GigabitEthernet0/1.20 | 10.0.20.254 /24 | Area 0 | OT Production Gateway |
| **PC-A** | FastEthernet0 | 172.16.1.10 /24 | N/v/t | End Device (GW: 172.16.1.1) |
| **Syslog-Server** | FastEthernet0 | 10.0.10.250 /24 | N/v/t | Central Logging Host |

## **3\. Applied Configurations (Cisco IOS)**

### **3.1 Point-to-Point Link Configuration**

#### **Router-1 (Defensie-R1):**

```shell
interface GigabitEthernet0/1
 ip address 10.0.0.1 255.255.255.0
 no shutdown
```

#### **Router-2:**

```shell
interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.0
 no shutdown
```

### **3.2 OSPFv2 Routing Process Configuration**

#### **Router-1 (Defensie-R1):**

```shell
router ospf 1
 router-id 1.1.1.1
 network 10.0.0.0 0.0.0.255 area 0
 network 172.16.1.0 0.0.0.255 area 0
```

#### **Router-2:**

```shell
router ospf 1
 router-id 2.2.2.2
 network 10.0.0.0 0.0.0.255 area 0
 network 10.0.10.0 0.0.0.255 area 0
 network 10.0.20.0 0.0.0.255 area 0
```

### **3.3 Security Cleanup & Interface Remediation**

* **Access List Removals:** De verouderde Extended IP access list 101 is ontkoppeld van GigabitEthernet0/0 op Router-2 en verwijderd uit de running-configuratie om ongeoorloofde ICMP-drops te voorkomen.  
* **Interface Correctie:** Het P2P-netwerk op Router-1 is hergeconfigureerd van Gig0/0 naar Gig0/1 om fysieke aansluiting op de juiste interface te waarborgen.

## **4\. Validation & Proof of Concept**

> 1. **OSPF Neighbor Adjacency Check:**  
   * **Command:** show ip ospf neighbor  
   * **Resultaat:** Status FULL/DR bereikt met buur 2.2.2.2 via GigabitEthernet0/1.  
> 2. **Routing Table Convergence:**  
   * **Command:** show ip route op Router-2  
   * **Resultaat:** OSPF-route O 172.16.1.0/24 \[110/2\] via 10.0.0.1 succesvol opgenomen.  
> 3. **End-to-End Connectivity Verification:**  
   * **Command:** ping 10.0.10.250 vanaf PC-A (172.16.1.10)  
   * **Resultaat:** 4/4 ICMP Echo Replies ontvangen met een vertraging van \<1ms (0% verlies).

**\[END OF LAB 12 DOCUMENTATION\]**