# **LAB 29: OSPF RECONNAISSANCE MITIGATION & PASSIVE INTERFACE HARDENING (FINAL)**

**Document Type:** Routing Infrastructure Hardening & Reconnaissance Defense  
**Project Name:** Zero Trust OSPF Passive-Interface Default Strategy  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## 

## **1\. Executive Summary**

In dit lab is het **OSPFv2** routingproces op **Defensie-R1** en **Router-2** gehard tegen **Network Reconnaissance** en **Unsolicited Routing Adjacency Exploits**. Door het toepassen van een **Passive-Interface Default** strategie volgens de Zero Trust architectuur, worden OSPF Hello-multicasts (224.0.0.5) niet langer uitgezonden op interne LAN-, DMZ- en OT-segmenten (VLAN 10, VLAN 20, VLAN 28).  
Hierdoor wordt het lekken van kritieke netwerkinformatie (zoals Router IDs, OSPF Area IDs en subnetstructuren) naar potentiële aanvallers (zoals Hacker-laptop B) op Layer 2/3 volledig geneutraliseerd, terwijl de point-to-point OSPF-route-uitwisseling over de WAN-backbone (GigabitEthernet) intact blijft.

## 

## **2\. OSPF Interface Exposure Matrix**

| Router | Interface / Subinterface | Associated Zone / Function | OSPF Hello State | Security Level |
| :---- | :---- | :---- | :---- | :---- |
| **Defensie-R1** | GigabitEthernet0/1 | WAN Backbone Link | **Active (P2P)** | Protected (MD5 Key 1\) |
| **Defensie-R1** | Gi0/0.10 / Gi0/0.20 | IT Mgmt / OT LAN Subnets | **PASSIVE (Disabled)** | **HARDENED (Zero Leakage)** |
| **Router-2** | GigabitEthernet0/0 | WAN Backbone Link | **Active (P2P)** | Protected (MD5 Key 1\) |
| **Router-2** | Gi0/1.10 / .20 / .28 | IT / OT / DMZ Subinterfaces | **PASSIVE (Disabled)** | **HARDENED (Zero Leakage)** |

## **3\. Applied CLI Configurations**

### **Defensie-R1 CLI:**

**CLI Configuration**

```
! --- OSPF PASSIVE INTERFACE DEFAULT HARDENING ---
router ospf 1
 passive-interface default
 no passive-interface GigabitEthernet0/1
exit
!
end
write memory
```

### 

### **Router-2 CLI:**

**CLI Configuration**

```
! --- OSPF PASSIVE INTERFACE DEFAULT HARDENING ---
router ospf 1
 passive-interface default
 no passive-interface GigabitEthernet0/0
exit
!
end
write memory
```

## 

## **4\. Validation & Proof of Concept**

1. **OSPF Protocol Inspection:**  
   * **Command:** show ip protocols op Router-2.  
   * **Resultaat:** Onder Passive Interface(s) worden alle interne interfaces (Gi0/1.10, Gi0/1.20, Gi0/1.28, etc.) expliciet vermeld.  
2. **WAN Neighbor Verification:**  
   * **Command:** show ip ospf neighbor op Router-2.  
   * **Resultaat:** Enkel WAN-interface GigabitEthernet0/0 onderhoudt een actieve OSPF-buurrelatie (1.1.1.1 \- FULL/DR).  
3. **End-to-End Inter-Site Reachability:**  
   * **Source:** PC-A (172.16.10.11)  
   * **Target:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Resultaat:** **0% packet loss (4/4 packets received, RTT \<1ms)**.

**\[END OF LAB 29 DOCUMENTATION\]**