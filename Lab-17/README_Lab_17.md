<img width="947" height="768" alt="image" src="https://github.com/user-attachments/assets/655892f2-6b61-4701-a1c0-701fb3da0b7e" />


# **LAB 17: OT SECURE REMOTE ACCESS (SSH MANAGEMENT)**

**Document Type:** Remote Access & Secure OT Management

**Project Name:** SSH Hardening op Router-1 & Encrypted Admin Access

**Author:** P. Apondja

**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is de beheeromgeving van **Router-1 (Defensie-R1)** gehard volgens moderne OT/ICS-beveiligingsnormen. Onbeveiligde protocollen (zoals Telnet) zijn volledig uitgesloten van de VTY-lijnen. Er is een **RSA 1024-bit crypto-sleutel** gegenereerd en een lokaal administrator-account aangemaakt met een sterk gehasht wachtwoord. Netwerkbeheerders op **PC-A** kunnen het netwerk nu veilig en geëncrypteerd beheren via **SSHv2**.

## **2\. Management & Security Matrix**

## 

| Apparaat | Interface / Protocol | Beheer Account | Authenticatie Type | Status |
| :---- | :---- | :---- | :---- | :---- |
| **Router-1** | VTY 0-4 (SSHv2) | admin | Secret (C1scoOT2026\!) | **SECURE (Encrypted)** |
| **Router-1** | Telnet | N/v/t | Blocked (transport input ssh) | **DISABLED** |
| **PC-A** | SSH Client | admin | Target: 172.16.10.1 | **Active SSH Session** |

## **3\. Applied Configurations (Router-1 CLI)**

```shell
! --- 1. DOMAIN & RSA KEY GENERATION ---
ip domain-name defensie.ot.local
username admin privilege 15 secret C1scoOT2026!
crypto key generate rsa
! Chosen key size: 1024 bits
ip ssh version 2

! --- 2. SECURE VTY LINES HARDENING ---
line vty 0 4
 login local
 transport input ssh
exit

end
write memory
```

## 

## **4\. Validation & Proof of Concept**

> 1. **SSH Connection Test:**  
   * **Command:** ssh \-l admin 172.16.10.1 vanaf PC-A.  
   * **Resultaat:** Succesvolle authenticatie met gehashte credential controle. Directe CLI access verkregen tot Defensie-R1\#.  
> 2. **Telnet Mitigation:**  
   * Door het afdwingen van transport input ssh worden onbeveiligde Telnet-pogingen direct door de VTY-lijnen van Router-1 geweigerd.

**\[END OF LAB 17 DOCUMENTATION\]**
