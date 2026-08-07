# **LAB 30: PURDUE MODEL MICRO-SEGMENTATION & EXTENDED ACCESS CONTROL LISTS (FINAL)**

**Document Type:** Layer 3/4 Inter-Zone Access Control & Purdue Micro-Segmentation  
**Project Name:** Extended ACL Isolation (PROTECT\_OT\_ZONE) on Subinterface Gi0/0.10  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is **Purdue Model Micro-Segmentation** geïmplementeerd op **Defensie-R1** door middel van een **Extended Access Control List (PROTECT\_OT\_ZONE)** op subinterface **GigabitEthernet0/0.10**. Hiermee wordt strikte toegangscontrole afgedwongen tussen de IT-beheerszone (VLAN 10\) en de industriële OT-productiezone (VLAN 20).  
Uitsluitend het geautoriseerde engineering workstation **PC-A (172.16.10.11)** behoudt volledige IP-toegang tot de OT-pompen (10.0.20.0/24). Pogingen van niet-geautoriseerde apparaten op VLAN 10 (zoals **Hacker-laptop B**) om de OT-zone te bereiken, worden direct door de router-gateway geneutraliseerd met een ICMP *Destination Host Unreachable* melding.

## **2\. Micro-Segmentation Policy Matrix**

| Source Subnet / Host | Destination Subnet | Protocol / Port | Action | Impact / Security Status |
| :---- | :---- | :---- | :---- | :---- |
| **Host PC-A (172.16.10.11)**  | **OT Zone (10.0.20.0/24)**  | IP (Any) | **PERMIT** | **Authorized Admin Access** |
| **VLAN 10 Subnet (172.16.10.0/24)**  | **OT Zone (10.0.20.0/24)**  | IP (Any) | **DENY** | **BLOCKED (Rogue Device Neutralized)** |
| **Any Source** | **Any Destination** | IP (Any) | **PERMIT** | Standard Network Routing |

## **3\. Applied CLI Configurations (Defensie-R1)**

```shell
! --- 1. EXTENDED ACCESS CONTROL LIST CREATION ---
ip access-list extended PROTECT_OT_ZONE
 permit ip host 172.16.10.11 10.0.20.0 0.0.0.255
 deny ip 172.16.10.0 0.0.0.255 10.0.20.0 0.0.0.255
 permit ip any any
exit

! --- 2. ACL INBOUND ENFORCEMENT ON VLAN 10 SUBINTERFACE ---
interface GigabitEthernet0/0.10
 ip access-group PROTECT_OT_ZONE in
exit

end
write memory
```

## **4\. Validation & Proof of Concept**

1. **Access List Inspection:**  
   * **Command:** show ip access-lists PROTECT\_OT\_ZONE op Defensie-R1.  
   * **Resultaat:** De regels permit ip host 172.16.10.11 10.0.20.0 0.0.0.255 en deny ip 172.16.10.0 0.0.0.255 10.0.20.0 0.0.0.255 staan actief in het geheugen.  
2. **Legitimate Access Verification (PC-A):**  
   * **Source:** PC-A (172.16.10.11)  
   * **Target:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Resultaat:** **0% packet loss (4/4 packets received, RTT \<1ms)**.  
3. **Rogue Attack Mitigation Verification (Hacker-laptop B):**  
   * **Source:** Hacker-laptop B (172.16.10.x)  
   * **Target:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Resultaat:** **100% packet loss (Reply from 172.16.10.1: Destination host unreachable)**.

**\[END OF LAB 30 DOCUMENTATION\]**