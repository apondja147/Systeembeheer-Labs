<img width="953" height="776" alt="image" src="https://github.com/user-attachments/assets/f689f96d-74d4-4c79-bcf2-a9892c079232" />



# **LAB 28: OSPF MD5 AUTHENTICATION & ROUTING PROTOCOL HARDENING (FINAL)**

**Document Type:** Dynamic Routing Security & OSPF Infrastructure Hardening  
**Project Name:** OSPF Area 0 Cryptographic MD5 Authentication  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## 

## **1\. Executive Summary**

In dit lab is het **OSPFv2 (Area 0\)** dynamische routingprotocol tussen **Defensie-R1** en **Router-2** gehard door de implementatie van **MD5 Message Digest Authentication**. Onbeveiligde OSPF-pakketten in plaintext zijn vervangen door cryptografisch gevalideerde OSPF-pakketten via Key-ID 1 (DefensieOT2026).  
Deze beveiligingsmaatregel voorkomt dat kwaadwillenden op het WAN-netwerk (zoals rogue apparaten op de transit-link) valse OSPF-routingupdates kunnen injecteren (**Route Poisoning / Blackholing**) of verkeer tussen de IT-beheerszone en de OT-productiezone kunnen omleiden via Man-in-the-Middle (MitM) technieken.

## 

## **2\. OSPF Hardening Matrix**

| Device / Interface | OSPF Process | Target Area | Authentication Type | Key ID / Hash | Security State |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Defensie-R1 (Gi0/1)** | Process 1 | Area 0 | **MD5 Message-Digest** | Key 1 / MD5 | **PROTECTED (FULL/DR)** |
| **Router-2 (Gi0/0)** | Process 1 | Area 0 | **MD5 Message-Digest** | Key 1 / MD5 | **PROTECTED (FULL/BDR)**  |

## 

## **3\. Applied CLI Configurations**

### **Defensie-R1 CLI (WAN Interface Gi0/1):**

```shell
! --- 1. OSPF MD5 INTERFACE HARDENING ---
interface GigabitEthernet0/1
 ip ospf message-digest-key 1 md5 DefensieOT2026
 ip ospf authentication message-digest
exit

! --- 2. OSPF AREA 0 AUTHENTICATION ENFORCEMENT ---
router ospf 1
 area 0 authentication message-digest
exit

end
write memory
```

### 

### **Router-2 CLI (WAN Interface Gi0/0):**

```shell
! --- 1. OSPF MD5 INTERFACE HARDENING ---
interface GigabitEthernet0/0
 ip ospf message-digest-key 1 md5 DefensieOT2026
 ip ospf authentication message-digest
exit

! --- 2. OSPF AREA 0 AUTHENTICATION ENFORCEMENT ---
router ospf 1
 area 0 authentication message-digest
exit

end
write memory
```

## 

## **4\. Validation & Proof of Concept**

1. **OSPF Neighbor Adjacency Inspection:**  
   * **Command:** show ip ospf neighbor op Router-2.  
   * **Resultaat:** Neighbor 1.1.1.1 (Defensie-R1) staat geregistreerd in de **FULL/BDR** staat over interface GigabitEthernet0/0. Dit bevestigt een succesvolle MD5-authenticatie-handshake.  
2. **Routing Table Verification:**  
   * **Command:** show ip route ospf op Router-2.  
   * **Resultaat:** Actieve OSPF-route O 172.16.20.0/24 \[110/2\] via 10.0.0.1 aanwezig.  
3. **End-to-End ICMP Reachability Test:**  
   * **Source:** PC-A (172.16.10.11)  
   * **Destination:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Resultaat:** **0% packet loss (4/4 packets received, RTT \<1ms)**.

**\[END OF LAB 28 DOCUMENTATION\]**
