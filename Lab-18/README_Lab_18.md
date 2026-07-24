<img width="961" height="777" alt="image" src="https://github.com/user-attachments/assets/4e8272cc-e413-4eaa-85aa-3ceb1dd9e9a5" />


# **LAB 18: OT NETWORK MONITORING & CENTRAL SYSLOG LOGGING (FINAL)**

**Document Type:** OT/ICS Security Operations & Threat Detection  
**Project Name:** Centralized Event Logging & Security Auditing  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is centrale event logging via het **Syslog-protocol (UDP poort 514\)** geïmplementeerd op zowel **Router-1 (10.0.0.1)** als **Router-2 (10.0.10.254)**. Alle beveiligings- en beheergebeurtenissen (zoals SSH-sessies, configuratiewijzigingen en systeemstatus-updates) worden nu automatisch doorgestuurd naar de centrale **Syslog-Server (10.0.10.250)**. Dit is een essentiële vereiste voor **Security Operations Center (SOC)** audits en OT-incident response.

## **2\. Syslog Topology & Collector Matrix**

| Apparaat | IP-Adres / Bron | Target Syslog Server | Transport Protocol / Poort | Status Log Stream |
| :---- | :---- | :---- | :---- | :---- |
| **Router-1 (Defensie-R1)** | 10.0.0.1 | 10.0.10.250 | UDP / 514 | **ACTIVE (Captured)** |
| **Router-2** | 10.0.10.254 | 10.0.10.250 | UDP / 514 | **ACTIVE (Captured)** |
| **Syslog-Server** | 10.0.10.250 | N/v/t | Local Collector | **Receiving Logs** |

## **3\. Applied Configurations (Router CLI)**

### **3.1 Configuration Router-1 & Router-2**

```shell
! --- SYSLOG HOST BINDING ---
logging host 10.0.10.250

! --- TIMESTAMP ENRICHMENT ---
service timestamps log datetime msec

end
write memory
```

## **4\. Validation & Proof of Concept**

> 1. **Service Verification:** Op de Syslog-Server is te zien dat er actieve berichten binnenkomen van zowel 10.0.0.1 (Router-1) als 10.0.10.254 (Router-2).  
> 2. **Audit Trail:**  
   * Logregel 1 & 2: Statusberichten van Router-1 met betrekking tot beheer- en logging-initialisatie.  
   * Logregel 3: Realtime configuratie-event ontvangen van Router-2 (%SYS-5-CONFIG\_I).

**\[END OF LAB 18 DOCUMENTATION\]**
