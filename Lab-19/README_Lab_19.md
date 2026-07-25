<img width="950" height="768" alt="image" src="https://github.com/user-attachments/assets/7f532f1f-dddf-4ddf-9286-ad1ada7b8a39" />


# **LAB 19: SWITCH PORT SECURITY & ROGUE DEVICE MITIGATION (FINAL)**

**Document Type:** Layer 2 Infrastructure Security & Port Hardening

**Project Name:** MAC Address Binding & Automatic Unauthorized Port Shutdown

**Author:** P. Apondja

**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is Layer 2 **Port Security** geconfigureerd op **Switch-A (FastEthernet0/1)**. Met behulp van de **Sticky MAC-addressing** methode is het unieke hardware-adres van de legitieme beheermachine (PC-A) gebonden aan de fysieke switchpoort. Pogingen van ongeautoriseerde apparaten (zoals een rogue laptop) om fysiek op deze poort in te pluggen, resulteren in een directe **Port Violation Shutdown (err-disabled)**, waarmee fysieke inbreuk op het OT/IT-netwerk wordt voorkomen.

## **2\. Port Security Status Matrix**

## 

| Switch / Poort | Security Status | Max MAC | Bound MAC Address | Violation Mode | Security Violation Count |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Switch-A (Fa0/1)** | **Secure-up** | 1 | 0090.0CBD.3833 (PC-A) | **Shutdown** | 0 (Normal Operation) |

## **3\. Applied Configurations (Switch-A CLI)**

```shell
! --- PORT SECURITY CONFIGURATION ON FA0/1 ---
interface FastEthernet0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
exit

end
write memory
```

## **4\. Validation & Proof of Concept**

> 1. **MAC Binding Verification:**  
   * **Command:** show port-security interface FastEthernet0/1 op Switch-A.  
   * **Resultaat:** Port status is Secure-up, en het MAC-adres 0090.0CBD.3833 is succesvol geregistreerd als Sticky MAC.  
> 2. **Ping Test:** PC-A heeft een succesvolle ping verstuurd naar gateway 172.16.10.1 (0% loss), waarmee de actieve en beveiligde verbinding is bevestigd.

**\[END OF LAB 19 DOCUMENTATION\]**
