# **LAB 16: OT ZONE EXPANSION & SCADA/HMI MANAGEMENT (FINAL)**

**Document Type:** OT/ICS Infrastructure & SCADA Security

**Project Name:** SCADA/HMI Integration & Purdue Model Enforcement

**Author:** P. Apondja

**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is de industriële zone (OT-segment) succesvol uitgebreid met een **HMI-werkstation (HMI-OT-A)** op poort Fa0/4 van Switch-B (VLAN 20). De beveiligingsarchitectuur garandeert dat lokale OT-besturing en telemetrie richting de Syslog-server vlekkeloos verlopen, terwijl directe netwerktoegang vanuit en naar het IT-netwerk (172.16.10.0/24) aan beide zijden volledig wordt geblokkeerd.

## **2\. OT Matrix & Access Verification**

## 

| Bron (Source) | Bestemming (Destination) | Type Verkeer | ACL Regel | Resultaat | Status |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **HMI-OT-A** (10.0.20.10) | **PLC-Pomp-B** (10.0.20.1) | Intra-VLAN OT Control | Local Switch Layer 2 | **SUCCESS** (0% loss) | **VALIDATED** |
| **HMI-OT-A** (10.0.20.10) | **Syslog-Server** (10.0.10.250) | Cross-Zone Logging | permit ip ... | **SUCCESS** (0% loss) | **VALIDATED** |
| **PC-A** (172.16.10.11) | **HMI-OT-A** (10.0.20.10) | IT to OT Access | deny ip ... | **BLOCKED** (Host Unreachable) | **SECURE** |
| **HMI-OT-A** (10.0.20.10) | **PC-A** (172.16.10.11) | OT to IT Access | deny ip ... | **BLOCKED** (Request timed out) | **SECURE** |

## **3\. Applied Switch-B Configuration**

```shell
! --- HMI PORT ASSIGNMENT ON SWITCH-B ---
interface FastEthernet0/4
 switchport mode access
 switchport access vlan 20
 no shutdown
exit

end
write memory
```

## **4\. Validation & Proof of Concept**

> 1. **Local OT Control:** HMI-OT-A stuurt rechtstreeks op Layer 2 commando's/pings naar PLC-Pomp-B.  
> 2. **Bi-Directional Isolation:** Pogingen tot communicatie tussen PC-A (IT) en HMI-OT-A (OT) worden aan beide kanten door Router-2 gedropt.

**\[END OF LAB 16 DOCUMENTATION\]**