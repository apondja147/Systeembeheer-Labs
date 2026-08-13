<img width="960" height="778" alt="image" src="https://github.com/user-attachments/assets/55b22bd4-5448-4c6d-915a-411ac734436f" />




# **LAB 32: EXTERNAL BGP (eBGP) INTER-SITE ROUTING & AUTONOMOUS SYSTEM PEERING (FINAL)**

**Document Type:** Layer 3 WAN Routing & Exterior Gateway Protocol Hardening  
**Project Name:** eBGP Autonomous System Peering (AS 65100 \<-\> AS 65200\)  
**Author:** P. Apondja  
**Status:** Fully Validated & Network Architecture Approved

## **1\. Executive Summary**

In dit lab is de WAN-interconnectie tussen **Site A (Defensie-R1)** en **Site B (Router-2)** geüniformeerd en opgeschaald naar een enterprise-grade **External BGP (eBGP)** routingarchitectuur. De twee defensielocaties zijn ingericht als onafhankelijke Autonomous Systems (**AS 65100** voor Site A en **AS 65200** voor Site B).  
Via de point-to-point WAN-link (10.0.0.0/30) is een actieve eBGP-buurrelatie tot stand gebracht. **Defensie-R1** adverteert de lokale IT-beheerszone (172.16.10.0/24), terwijl **Router-2** de industriële OT-zone (10.0.20.0/24) en de DMZ (10.0.28.0/24) dynamisch in BGP injecteert. Dit vormt het routingfundament voor de aankomende IPsec Site-to-Site VPN versleuteling.

## **2\. BGP Topology & Autonomous System Matrix**

| Router Name | Local AS | Interface IP | Peer IP | Remote AS | Advertised Networks | BGP State |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **Defensie-R1** | **65100** | 10.0.0.1 | 10.0.0.2 | **65200** | 172.16.10.0/24 | **ESTABLISHED** |
| **Router-2** | **65200** | 10.0.0.2 | 10.0.0.1 | **65100** | 10.0.20.0/24, 10.0.28.0/24 | **ESTABLISHED** |

## **3\. Applied CLI Configurations**

### **3.1 Defensie-R1 (Site A \- AS 65100\)**

```shell
! --- BGP ROUTING PROCESS CONFIGURATION ---
router bgp 65100
 neighbor 10.0.0.2 remote-as 65200
 network 172.16.10.0 mask 255.255.255.0
exit

end
write memory
```

### **3.2 Router-2 (Site B \- AS 65200\)**

```shell
! --- BGP ROUTING PROCESS CONFIGURATION ---
router bgp 65200
 neighbor 10.0.0.1 remote-as 65100
 network 10.0.20.0 mask 255.255.255.0
 network 10.0.28.0 mask 255.255.255.0
exit

end
write memory
```

## **4\. Validation & Proof of Concept**

1. **BGP Neighbor State Verification:**  
   * **Command:** show ip bgp summary op Defensie-R1.  
   * **Resultaat:** Peer 10.0.0.2 (AS 65200\) bevindt zich in de actieve **ESTABLISHED**\-status met actieve berichtuitwisseling (MsgRcvd 7, MsgSent 5).  
2. **BGP Routing Table Ingestion:**  
   * **Command:** show ip route bgp op Defensie-R1.  
   * **Resultaat:** De BGP-route naar het remote DMZ-netwerk (10.0.28.0/24) is succesvol geïnstalleerd in de globale IP-routingtabel via next-hop 10.0.0.2 (Administrative Distance **20**).

**\[END OF LAB 32 DOCUMENTATION\]**
