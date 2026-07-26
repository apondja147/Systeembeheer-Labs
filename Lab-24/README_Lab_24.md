# **LAB 24: TRAFFIC STORM CONTROL & ROGUE BROADCAST MITIGATION (FINAL)**

**Document Type:** Layer 2 Denial-of-Service Defense & Traffic Shaping

**Project Name:** Broadcast Suppression & Availability Assurance

**Author:** P. Apondja

**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is **Traffic Storm Control** geïmplementeerd op **Switch-A (FastEthernet0/3)** om de netwerkzone te beschermen tegen Broadcast DoS-aanvallen of netwerklussen veroorzaakt door apparaten zoals Hacker-laptop B. Door een strikte drempelwaarde van **1.00%** van de totale poortbandbreedte in te stellen, worden mogelijke broadcast storms automatisch gefilterd voordat ze de verwerkingscapaciteit van de PLC's of HMI's kunnen overbelasten.

## **2\. Storm Control Status Matrix**

| Switch / Poort | Target Device | Storm Type | Filter State | Threshold (Upper/Lower) | Security Action |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Switch-A (Fa0/3)** | Hacker-laptop B | **Broadcast** | **Link Up** | **1.00% / 1.00%** | **Drop Excess Traffic** |

## **3\. Applied CLI Configuration (Switch-A)**

```shell
! --- TRAFFIC STORM CONTROL HARDENING FA0/3 ---
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 10
 storm-control broadcast level 1.00
 no shutdown
exit

end
write memory
```

## 

## **4\. Validation & Proof of Concept**

> 1. **Threshold Inspection:**  
   * **Command:** show storm-control broadcast  
   * **Resultaat:** Interface Fa0/3 staat op Link Up met een actieve Upper/Lower drempelwaarde van 1.00%.  
> 2. **Mitigation Guarantee:** Overtollige broadcast-frames boven de 1.00% capaciteit worden door de switch op hardware-niveau gedropt zonder legitiem Unicast-verkeer in VLAN 10 te hinderen.

## **5\. FortiGate / FortiOS Equivalent (Voor Recruiters & Sollicitaties)**

**FortiSwitch CLI:**

```
config switch interface
    edit "port3"
        set storm-control-mode override
        set broadcast-storm-control enable
        set storm-control-rate 100
    next
end
```

* **FortiGate DoS Policy (Layer 3/4):** Op een FortiGate Next-Gen Firewall stel je dit in via **Policy & Objects** $\\rightarrow$ **IPv4 DoS Policy** door een L4 Anomaly (UDP/ICMP Flood) drempelwaarde in te stellen om Denial-of-Service aanvallen op te vangen.

**\[END OF LAB 24 DOCUMENTATION\]**

