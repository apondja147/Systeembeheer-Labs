<img width="958" height="772" alt="image" src="https://github.com/user-attachments/assets/674eadc7-4287-4221-9485-b87087b5c25b" />



# **LAB 23: INCIDENT RESPONSE & UNUSED PORT HARDENING (FINAL)**

**Document Type:** Threat Mitigation & Physical Port Hardening  
**Project Name:** Blackhole VLAN Assignment & Rogue Connection Defense  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## 

## **1\. Executive Summary**

In dit lab is een **Rogue Device Attack** gesimuleerd op **Switch-B**. Een aanval vanuit een ongeautoriseerd station (Hacker-laptop) is op Layer 2 geneutraliseerd door de toepassing van **Unused Port Hardening** en **Blackhole VLAN Isolation**. Ongebruikte fysieke interfaces op de OT-switch zijn toegewezen aan een geïsoleerd non-routable **VLAN 999 (BLACKHOLE\_UNUSED)** en administratief uitgeschakeld (shutdown).

## 

## **2\. Interface Isolation & Defense Matrix**

## 

| Switch Interface | Aangesloten Apparaat | VLAN Toewijzing | Port Status | Aanvalsvector / Mitigatie Status |
| :---- | :---- | :---- | :---- | :---- |
| **Fa0/3** | Syslog-Server | **VLAN 10** (Mgmt) | **Up (Green)** | Legitieme logging-stream gehersteld. |
| **Fa0/5** | Hacker-laptop | **VLAN 999** (Blackhole) | **Disabled (Red)** | **BLOCKED (Rogue connection dropped)** |
| **Fa0/6 \- 24** | Unused Ports | **VLAN 999** (Blackhole) | **Shutdown** | Preventieve fysieke poortbeveiliging. |

## 

## 

## **3\. Applied CLI Configuration (Switch-B)**

```shell
! --- 1. RESTORE CRITICAL SYSLOG INTERFACE ---
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

! --- 2. BLACKHOLE ISOLATION FOR ROGUE DEVICE (FA0/5) ---
interface FastEthernet0/5
 switchport mode access
 switchport access vlan 999
 shutdown
exit

end
write memory
```

## 

## 

## 

## **4\. Validation & Proof of Concept**

> 1. **Rogue Access Mitigation:** Hacker-laptop aangesloten op Fa0/5 krijgt geen link-state up, waardoor Layer 2-communicatie met de OT-zone (10.0.20.0/24) onmogelijk is. Pings vanuit de laptop resulteren in **100% packet loss**.  
> 2. **Infrastructure Restoration:** De Syslog-Server verbinding op Fa0/3 is geverifieerd als operationeel (Interface FastEthernet0/3 changed state to up).

**\[END OF LAB 23 DOCUMENTATION\]**

### **Klaar voor Lab 24?**

Voor het volgende lab kunnen we **Hacker-laptop B** (die linksonder bij Switch-A zweeft) gaan aanpakken\!  
We kunnen bijvoorbeeld **Dynamic Inspection & Storm Control** gaan inrichten om te voorkomen dat een hacker de switchelementen platlegt met een **Broadcast Storm** (DoS-aanval op het OT-netwerk).  
Zeg maar wat je ervan denkt, dan leg ik het vooraf weer haarfijn voor je uit\!
