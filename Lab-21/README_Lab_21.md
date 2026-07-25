<img width="957" height="772" alt="image" src="https://github.com/user-attachments/assets/b7d34362-3c64-4741-ac85-c5ef247c3c5c" />



# **LAB 21: AAA AUTHENTICATION & CENTRAL USER MANAGEMENT (FINAL)**

**Document Type:** Identity, Credential & Access Management (ICAM)  
**Project Name:** AAA Model Implementation & Centralized Admin Authentication  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## 

## **1\. Executive Summary**

In dit lab is het **AAA (Authentication, Authorization, Accounting)** framework succesvol geïmplementeerd op **Router-1 (Defensie-R1)**. Toegang tot zowel de fysieke **Console-poort** als de virtuele **VTY (SSH)-lijnen** wordt nu centraal afgedwongen via een default AAA-authenticatiestructuur op basis van gehashte lokale credentials. Dit legt het fundament voor uitbreiding naar een centrale RADIUS/TACACS+ server in de toekomst.

## 

## **2\. AAA Authentication Matrix**

## 

| Toegangskanaal | Auth Method | Primary Source | Fallback | Active Session Verified |
| :---- | :---- | :---- | :---- | :---- |
| **Line Console 0** | login authentication default | Local DB (admin) | Enable Password | Verified |
| **Line VTY 0-4 (SSH)** | login authentication default | Local DB (admin) | Enable Password | **ACTIVE** (\*390 vty 0\) |

## **3\. Applied Configurations (Router-1 CLI)**

```shell
! --- 1. GLOBAL AAA MODEL INITIALIZATION ---
aaa new-model
aaa authentication login default local
aaa authentication enable default enable

! --- 2. CONSOLE HARDENING ---
line console 0
 login authentication default
exit

! --- 3. VTY (SSH) HARDENING ---
line vty 0 4
 login authentication default
exit

end
write memory
```

## 

## **4\. Validation & Proof of Concept**

> 1. **AAA Login Test:** Succesvol ingelogd vanaf **PC-A** via ssh \-l admin 172.16.10.1.  
> 2. **Session Inspection:**  
   * **Command:** show users op Defensie-R1\>.  
   * **Resultaat:** Sessie vty 0 is actief geregistreerd onder de gebruiker **admin**.

**\[END OF LAB 21 DOCUMENTATION\]**
