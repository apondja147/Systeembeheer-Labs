<img width="961" height="783" alt="image" src="https://github.com/user-attachments/assets/13db7a9d-8f14-40db-a9e3-3305f4d5d42e" />


# **LAB 13: SWITCH PORT SECURITY & NETWORK HARDENING (FINAL)**

**Document Type:** Network Security & Layer 2 Defense

**Project Name:** Hardening van Switch-A & Port Security Implementatie

**Author:** P. Apondja

**Status:** Fully Validated & Security Approved

## **1\. Executive Summary**

In dit lab is **Switch-A** succesvol gehard tegen onbevoegde fysieke netwerktoegang. Door middel van **Port Security** is poort FastEthernet0/1 strikt gekoppeld aan de geautoriseerde werkplek (**PC-A**). Pogingen om onbevoegde apparaten (zoals de gesimuleerde Hacker-laptops) op het netwerk aan te sluiten, worden door de switch automatisch gedetecteerd, waarna de poort onmiddellijk in een err-disable (Secure-shutdown) status wordt geplaatst. Alle overige ongebruikte switchpoorten zijn administratief uitgeschakeld.

## **2\. Port Security & Hardening Matrix**

## 

| Switch Interface | Verbonden Status | Beveiligingsinstelling | Violation Modus | Actuele Status |
| :---- | :---- | :---- | :---- | :---- |
| **FastEthernet0/1** | **PC-A** | Max: 1, Sticky MAC (0090.0CBD.3833) | shutdown | **Secure-up** (Shutdown bij inbreuk) |
| **FastEthernet0/2 \- 24** | *Ongebruikt* | Geen (Hardened) | N/v/t | **Administratively Down** |
| **GigabitEthernet0/1** | **Router-1** | Uplink Trunk / Access Link | N/v/t | **Up / Connected** |
| **GigabitEthernet0/2** | *Ongebruikt* | Geen (Hardened) | N/v/t | **Administratively Down** |

## **3\. Applied Configurations (Switch-A CLI)**

```shell
! --- 1. PORT SECURITY OP ACCESS POORT PC-A ---
interface FastEthernet0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 no shutdown
exit

! --- 2. HARDENING: ONGEBRUIKTE POORTEN UITSCHAKELEN ---
interface range FastEthernet0/2 - 24
 shutdown
exit

interface GigabitEthernet0/2
 shutdown
exit

end
write memory
```

## **4\. Validation & Proof of Security Concept**

> 1. **Geautoriseerde Toegang (PC-A):**  
   * **Test:** Ping verstuurd vanaf PC-A (172.16.1.10) naar Default Gateway 172.16.1.1.  
   * **Resultaat:** Verbinding geslaagd. MAC-adres 0090.0CBD.3833 succesvol geregistreerd als Sticky MAC.  
> 2. **Inbreuk Simulatie (Hacker-laptop A & B):**  
   * **Test:** Onbevoegde laptops aangesloten met afwijkend MAC-adres (0011.2233.4455).  
   * **Resultaat:** Switch-A genereerde direct een %PORT\_SECURITY-2-PSECURE\_VIOLATION syslog-melding en plaatste de poort in err-disable (status: Secure-shutdown). Netwerktoegang werd volledig geblokkeerd.  
> 3. **Unused Port Testing:**  
   * **Test:** Kabel aangesloten op FastEthernet0/2.  
   * **Resultaat:** Link blijft down vanwege administratieve shutdown.

**\[END OF LAB 13 DOCUMENTATION\]**
