<img width="958" height="771" alt="image" src="https://github.com/user-attachments/assets/bb8c7077-64d5-4719-a763-fab6be9ee253" />


**LAB 20: DHCP SNOOPING & DYNAMIC ARP INSPECTION (DAI) (FINAL)**

**Document Type:** Layer 2 Attack Mitigation & Infrastructure Hardening

**Project Name:** DHCP Spoofing Protection & ARP Poisoning Defense

**Author:** P. Apondja

**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit jubileum-lab is **Layer 2 Security** op **Switch-A** gecompleteerd door de implementatie van **DHCP Snooping** en **Dynamic ARP Inspection (DAI)** voor VLAN 10 en VLAN 20\. Deze beveiligingsmechanismen voorkomen dat een kwaadwillende op het netwerk een valse DHCP-server opzet (DHCP Spoofing) of ARP-tabel manipulaties uitvoert (ARP Poisoning / Man-in-the-Middle). Alle ARP-pakketten op untrusted poorten worden vanaf nu gevalideerd tegen de DHCP Snooping binding tabel.

## **2\. Layer 2 Defense Matrix**

## 

| Beveiligingstechniek | Target VLANs | Trusted Interface(s) | Untrusted Ports | Beveiligingsdoel | Status |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **DHCP Snooping** | VLAN 10, 20 | GigabitEthernet0/1 (Uplink Router-1) | Alle overige access poorten | Blokkeren van Rogue DHCP-servers | **ACTIVE** |
| **Dynamic ARP Inspection (DAI)** | VLAN 10, 20 | GigabitEthernet0/1 (Uplink Router-1) | Alle overige access poorten | Mitigatie van ARP Poisoning / MitM | **ACTIVE (Operational)** |

## **3\. Applied Configurations (Switch-A CLI)**

\--- CLI CONFIGURATION BLOCK \---

| Shell Environment: \! \--- 1\. GLOBAL & VLAN DHCP SNOOPING \---ip dhcp snoopingip dhcp snooping vlan 10,20no ip dhcp snooping information option\! \--- 2\. DYNAMIC ARP INSPECTION (DAI) \---ip arp inspection vlan 10,20\! \--- 3\. TRUSTED UPLINK INTERFACE CONFIGURATION \---interface GigabitEthernet0/1 ip dhcp snooping trust ip arp inspection trustexitendwrite memory |
| :---- |

## **4\. Validation & Proof of Concept**

> 1. **DHCP Snooping Verification:**  
   * **Command:** show ip dhcp snooping  
   * **Resultaat:** DHCP Snooping is actief op VLAN 10 en 20; GigabitEthernet0/1 is succesvol ingesteld als trusted.  
> 2. **DAI Operational Verification:**  
   * **Command:** show ip arp inspection vlan 10  
   * **Resultaat:** DAI Status staat op **Configuration: Enabled** en **Operation: Active**. Ongeldige ARP-pakketten op untrusted poorten worden automatisch gedropt.

**\[END OF LAB 20 DOCUMENTATION\]**
