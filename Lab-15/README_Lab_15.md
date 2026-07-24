<img width="956" height="771" alt="image" src="https://github.com/user-attachments/assets/54995eb8-30d0-46f7-af46-5fa60287144b" />


# **LAB 15: OT/IT CONVERGENCE & INDUSTRIAL ACCESS CONTROL**

**Document Type:** OT/ICS Security & Industrial Network Integration

**Project Name:** Dynamic OSPF Realignment & OT Zone Access Control

**Auteur:** Peter Apondja

**Status:** Gevalideerd & OT Security Goedgekeurd

## **1\. Executive Summary**

Dit rapport documenteert de netwerkconvergentie tussen het IT-segment (VLAN 10: 172.16.10.0/24) en de industriële OT-zone (10.0.20.0/24). Via OSPFv2 is de routing geconfigureerd naar Router-2. Vervolgens is een **Extended Access Control List (PROTECT\_OT)** op Router-2 geïmplementeerd in overeenstemming met de richtlijnen van het **Purdue Model for Industrial Control Systems**:

* **Toegestaan:** IT Management (PC-A) kan netwerkstatistieken verzenden naar de centrale **Syslog-Server** (10.0.10.250).  
* **Geweigerd:** IT Management (PC-A) wordt geblokkeerd voor toegang tot de veldapparatuur (**PLC-Pomp-B** op 10.0.20.1).

## **2\. Access Control List & Routing Architecture**

| Bron (Source) | Doel (Destination) | Protocol / Poort | Actie | Interface | Status |
| :---- | :---- | :---- | :---- | :---- | :---- |
| 172.16.10.0/24 (IT) | 10.0.10.250 (Syslog) | IP / All | **PERMIT** | Gi0/1.20 (Out) | **SUCCESS** |
| 172.16.10.0/24 (IT) | 10.0.20.0/24 (OT) | IP / All | **DENY** | Gi0/1.20 (Out) | **BLOCKED** |
| Any | Any | IP / All | **PERMIT** | Gi0/1.20 (Out) | Active |

## **3\. Final Applied Configurations (Router-2 CLI)**

```shell
! --- 1. OSPF ALIGNMENT ROUTER-1 ---
router ospf 1
 no network 172.16.1.0 0.0.0.255 area 0
 network 172.16.10.0 0.0.0.255 area 0
 network 172.16.20.0 0.0.0.255 area 0
exit

! --- 2. EXTENDED ACL ON ROUTER-2 ---
ip access-list extended PROTECT_OT
 permit ip 172.16.10.0 0.0.0.255 host 10.0.10.250
 deny ip 172.16.10.0 0.0.0.255 10.0.20.0 0.0.0.255
 permit ip any any
exit

! --- 3. ATTACH TO OT SUB-INTERFACE ---
interface GigabitEthernet0/1.20
 ip access-group PROTECT_OT out
exit

end
write memory
```

## **4\. Validation & Proof of OT Security Concept**

> 1. **Syslog Monitoring Test:**  
   * **Actie:** Ping naar 10.0.10.250 vanaf PC-A.  
   * **Resultaat:** Verbinding succesvol. PC-A heeft toegang tot de centrale logs.  
> 2. **OT PLC Block Test:**  
   * **Actie:** Ping naar 10.0.20.1 vanaf PC-A.  
   * **Resultaat:** Verbinding geweigerd (Destination host unreachable). Interceptie door Router-2 succesvol.

