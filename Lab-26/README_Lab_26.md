# **LAB 26: SITE-TO-SITE IPSEC VPN CONFIGURATION (FINAL)**

**Document Type:** Network Security & Site-to-Site Encrypted Tunneling

**Project Name:** IPsec VPN Tunnel Implementation between Router-1 & Router-2

**Author:** P. Apondja

**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is een beveilige **Site-to-Site IPsec VPN-tunnel** succesvol geïmplementeerd tussen **Router-1 (Defensie-R1)** en **Router-2**. Alle communicatie tussen het IT-netwerk (172.16.10.0/24) achter Router-1 en het Pomp-netwerk (10.0.20.0/24) achter Router-2 wordt nu automatisch versleuteld via Phase 1 (ISAKMP) en Phase 2 (IPsec Transform Set) IPsec protocollen. Hiermee is vertrouwelijkheid en data-integriteit over het tussenliggende WAN-netwerk gegarandeerd.

## **2\. IPsec VPN Security Matrix**

## 

| Fase / Onderdeel | Parameter / Algoritme | Instelling / Waarde | Status |
| :---- | :---- | :---- | :---- |
| **Phase 1 (ISAKMP Policy)** | Encryption / Hash | AES-256 / SHA-1 | Verified |
| **Phase 1 Auth** | Pre-Shared Key (PSK) | GeheimWachtwoord123 | Verified |
| **Phase 1 Diffie-Hellman** | DH Group | Group 5 | Verified |
| **Phase 2 (Transform Set)** | Data Encryption | esp-aes 256 esp-sha-hmac | Verified |
| **Interesting Traffic (ACL 101\)** | Router-1 Source $\\rightarrow$ Dest | 172.16.10.0/24 $\\rightarrow$ 10.0.20.0/24 | Active |
| **Interesting Traffic (ACL 101\)** | Router-2 Source $\\rightarrow$ Dest | 10.0.20.0/24 $\\rightarrow$ 172.16.10.0/24 | Active |
| **Crypto Map Application** | Interface Binding | GigabitEthernet0/0 (beide routers) | ACTIVE |

## **3\. Applied Configurations**

### **Router-1 (Defensie-R1 CLI)**

```shell
! 1. FASE 1 ISAKMP CONFIGURATIE
crypto isakmp policy 10
 encr aes 256
 hash sha
 authentication pre-share
 group 5
 exit
crypto isakmp key GeheimWachtwoord123 address 10.0.0.2

! 2. FASE 2 TRANSFORM SET & ACL
crypto ipsec transform-set VPN-SET esp-aes 256 esp-sha-hmac
access-list 101 permit ip 172.16.10.0 0.0.0.255 10.0.20.0 0.0.0.255

! 3. CRYPTO MAP BINDING
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 10.0.0.2
 set transform-set VPN-SET
 match address 101
 exit

interface GigabitEthernet0/0
 crypto map VPN-MAP
 exit

end
write memory
```

### **Router-2 CLI**

```shell
! 1. FASE 1 ISAKMP CONFIGURATIE
crypto isakmp policy 10
 encr aes 256
 hash sha
 authentication pre-share
 group 5
 exit
crypto isakmp key GeheimWachtwoord123 address 10.0.0.1

! 2. FASE 2 TRANSFORM SET & GESPIEGELDE ACL
crypto ipsec transform-set VPN-SET esp-aes 256 esp-sha-hmac
access-list 101 permit ip 10.0.20.0 0.0.0.255 172.16.10.0 0.0.0.255

! 3. CRYPTO MAP BINDING
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 10.0.0.1
 set transform-set VPN-SET
 match address 101
 exit

interface GigabitEthernet0/0
 crypto map VPN-MAP
 exit

end
write memory
```

## **4\. Validation & Proof of Concept**

> 1. **End-to-End Ping Test:** Succesvolle ICMP-ping uitgevoerd vanaf host **HMI-OT-A** (10.0.20.x) naar host 172.16.10.11 met 0% pakketverlies nadat de IPsec SA-onderhandeling was voltooid.  
> 2. **ISAKMP Session Verification:**  
   * Command: show crypto isakmp sa op Router-2.  
   * Resultaat: Connection State is geregistreerd als **QM\_IDLE** met status **ACTIVE** richting peer 10.0.0.1.  
> 3. **IPsec Data Encryption Inspection:**  
   * Command: show crypto ipsec sa op Router-2.  
   * Resultaat: \#pkts encaps/encrypt en \#pkts decaps/decrypt tellers zijn succesvol opgelopen, wat bevestigt dat het dataverkeer daadwerkelijk versleuteld over de tunnel stroomt.

**\[END OF LAB 26 DOCUMENTATION\]**

