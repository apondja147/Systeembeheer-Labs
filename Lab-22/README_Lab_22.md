# **LAB 22: OT TOPOLOGY EXPANSION (PLC-POMP-A) & MULTI-PLC SEGMENTATION (FINAL)**

**Document Type:** Multi-Zone Industrial Topology & Segmentation  
**Project Name:** Dual-PLC Deployment & Inter-Zone Access Control  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## 

## **1\. Executive Summary**

In dit lab is de industriële netwerktopologie uitgebreid met een tweede **PLC-unit (PLC-Pomp-A)** op poort Fa0/2 van **Switch-A** (VLAN 10, IP 172.16.10.20). Hiermee wordt een geografisch en logisch gescheiden productieomgeving nagebootst. De uitbreiding stelt ons in staat om gedifferentieerde toegangseisen op te stellen tussen beheer/IT-control en OT-control op verschillende locaties in het **Purdue Model**.

## 

## **2\. Expanded Device & VLAN Matrix**

## 

| Device Naam | IP-Adres | Subnet Mask | Switch / Poort | VLAN | Functie in OT-Architectuur |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **PC-A** | 172.16.10.11 | 255.255.255.0 | Switch-A (Fa0/1) | VLAN 10 | IT / Engineer Workstation |
| **PLC-Pomp-A** | 172.16.10.20 | 255.255.255.0 | Switch-A (Fa0/2) | VLAN 10 | Site A Industrial Controller (PLC) |
| **PLC-Pomp-B** | 10.0.20.1 | 255.255.255.0 | Switch-B (Fa0/2) | VLAN 20 | Site B Industrial Controller (PLC) |
| **HMI-OT-A** | 10.0.20.10 | 255.255.255.0 | Switch-B (Fa0/4) | VLAN 20 | OT Zone HMI Terminal |
| **Syslog-Server** | 10.0.10.250 | 255.255.255.0 | Switch-B (Fa0/1) | VLAN 10 | Central Audit & Event Collector |

## **3\. Applied Switch-A Configuration**

```shell
! --- PLC-POMP-A ACCESS PORT CONFIGURATION ---
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

end
write memory
```

## 

## **4\. Validation & Proof of Concept**

> 1. **Intra-VLAN Reachability Test:**  
   * **Source:** PC-A (172.16.10.11)  
   * **Target:** PLC-Pomp-A (172.16.10.20)  
   * **Command:** ping 172.16.10.20  
   * **Resultaat:** **0% loss** (4/4 packets received, RTT \<1ms).

**\[END OF LAB 22 DOCUMENTATION\]**