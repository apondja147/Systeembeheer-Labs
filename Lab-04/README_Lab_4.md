<img width="798" height="580" alt="image" src="https://github.com/user-attachments/assets/3a641427-f49a-41bd-b34c-7d85c0c21fa9" />


# **COMBINED LAB REPORT: ADVANCED ROUTING, SWITCHING & INFRASTRUCTURE HARDENING**

**Document Type:** Integrated Low-Level Design (LLD) & Security As-Built Documentation. **Project Name:** Multi-Router Infrastructure with Static Routing & Layer 2/3 Hardening. **Author:** P. Apondja. **Status:** Fully Validated & Approved. **Date:** June 26, 2026\.

---

## **1\. Executive Summary**

Dit gecombineerde document beschrijft de volledige opbouw, configuratie, routering en meerlaagse beveiliging (hardening) van de netwerkinfrastructuur ontwikkeld in Labs 02, 03 en 04\. Het project startte met het fysiek koppelen van twee gescheiden LAN-omgevingen via een point-to-point WAN-link (Lab 2). Vervolgens is de management-laag van de routers gecodeerd en voorzien van transportbeveiliging via SSH (Lab 3). Tot slot is de toegang op Layer 2 (Switch-niveau) dichtgetimmerd met Port Security en interface-deactivering (Lab 4). De totale keten is hiermee beschermd tegen zowel netwerkblindheid als fysieke indringers.

---

## **2\. Network Topology & Architecture**

De infrastructuur is opgebouwd uit drie logisch en fysiek gescheiden IP-netwerken die via twee Cisco Layer 3 routers met elkaar communiceren. Alle netwerksegmenten maken gebruik van een */24* subnetmasker (255.255.255.0) om voldoende host-ruimte te bieden en logische scheiding te handhaven.

### **2.1 Hardware Inventory**

| Device Name | Device Model | Operating System | Function / Role |
| :---- | :---- | :---- | :---- |
| **Defensie-R1** | Cisco ISR 2911 | Cisco IOS 15.1 | Gateway LAN links, WAN Edge & SSH Server |
| **Router-2** | Cisco ISR 2911 | Cisco IOS 15.1 | Gateway LAN rechts & WAN Edge |
| **Switch-A** | Cisco Catalyst 2960 | Cisco IOS | Layer 2 Access Switch (Kantoor LAN / Port Security) |
| **Switch-B** | Cisco Catalyst 2960 | Cisco IOS | Layer 2 Access Switch (Server LAN) |
| **PC-A** | Generic Endpoint | Windows Client | Secure Workstation (Kantooromgeving) |
| **Server-B** | Generic Endpoint | Server OS | Enterprise Data Server (Serverruimte) |

### **2.2 IP Addressing & Subnetting Plan**

| Network Segment | Subnet Range | Device Interface | IP Address | Default Gateway |
| :---- | :---- | :---- | :---- | :---- |
| **LAN Links (Kantoor)** | 192.168.10.0 /24 | PC-A / Defensie-R1 GigE0/0 | 192.168.10.1 / 192.168.10.254 | 192.168.10.254 |
| **WAN Link (Tussenweg)** | 172.16.1.0 /24 | Defensie-R1 GigE0/1 / Router-2 GigE0/1 | 172.16.1.1 / 172.16.1.2 | NVT |
| **LAN Rechts (Server)** | 10.0.0.0 /24 | Server-B / Router-2 GigE0/0 | 10.0.0.1 / 10.0.0.254 | 10.0.0.254 |

---

## **3\. Core Device Configurations (Cisco IOS)**

### **3.1 Defensie-R1 (Router 1\) \- Inclusief Routing & Security Hardening**

```
!
Router> enable
Router# configure terminal
!
hostname Defensie-R1
ip domain-name defensie.local
service password-encryption
!
enable secret cisco123
!
username admin secret CyberSafe2026!
!
crypto key generate rsa
1024
!
interface GigabitEthernet0/0
 description LAN_Kantoor_Links
 ip address 192.168.10.254 255.255.255.0
 no shutdown
 exit
!
interface GigabitEthernet0/1
 description WAN_Link_To_Router2
 ip address 172.16.1.1 255.255.255.0
 no shutdown
 exit
!
! --- STATIC ROUTE TO SERVER LAN ---
ip route 10.0.0.0 255.255.255.0 172.16.1.2
!
line console 0
 password 7 0822155D412C1B18
 login
 exit
!
line vty 0 4
 transport input ssh
 login local
 exit
!
end
copy running-config startup-config
```

### **3.2 Router-2 \- Operational & Basic Security Config**

```
!
Router> enable
Router# configure terminal
!
interface GigabitEthernet0/0
 description LAN_Server_Rechts
 ip address 10.0.0.254 255.255.255.0
 no shutdown
 exit
!
interface GigabitEthernet0/1
 description WAN_Link_To_Router1
 ip address 172.16.1.2 255.255.255.0
 no shutdown
 exit
!
! --- STATIC ROUTE TO KANTOOR LAN ---
ip route 192.168.10.0 255.255.255.0 172.16.1.1
!
enable secret cisco123
!
line console 0
 password defensie2026
 login
 exit
!
end
copy running-config startup-config
```

### **3.3 Switch-A \- Layer 2 Port Security & Port Deactivation**

```
!
Switch> enable
Switch# configure terminal
!
! --- PORT SECURITY ON ACTIVE USER PORT ---
interface FastEthernet0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 exit
!
! --- SHUTDOWN UNUSED UNSECURE INTERFACES ---
interface range fastEthernet 0/3 - 24
 shutdown
 exit
!
interface range gigabitEthernet 0/1 - 2
 shutdown
 exit
!
end
copy running-config startup-config
```

## **4\. Operational Verification & Cyber Assessment**

### **4.1 Layer 3 Connectivity (End-to-End Ping)**

Vanaf workstation PC-A is een ICMP-connectiviteitstest uitgevoerd naar Server-B (10.0.0.1) om de werking van de statische routes te valideren. **Analyse:** De initiële packet drop (50% verlies) werd veroorzaakt door het Address Resolution Protocol (ARP) dat de MAC-tabellen over de WAN-schakeling moest synchroniseren. Directe opeenvolgende pings resulteren in een stabiele 0% loss verbinding met een TTL van 126, wat de correcte werking van de statische routing-tabellen bewijst.

```
Pinging 10.0.0.1 with 32 bytes of data:
Request timed out.
Request timed out.
Reply from 10.0.0.1: bytes=32 time<1ms TTL=126
Reply from 10.0.0.1: bytes=32 time<1ms TTL=126

Ping statistics for 10.0.0.1:
    Packets: Sent = 4, Received = 2, Lost = 2 (50% loss),
```

### **4.2 Security Hardening Status (Audit)**

1. **Wachtwoordbeveiliging:** Lokale clear-text wachtwoorden zijn met succes gecodeerd via service password-encryption (Type 7). De Privileged EXEC-modus is zwaar beveiligd met SHA-hashing (Type 5 via enable secret).  
2. **Transportbeveiliging:** Onbeveiligde netwerklijnen (Telnet) zijn gedeactiveerd op de VTY-lijnen van Defensie-R1. SSH is succesvol geïnitieerd (bevestigd via log: %SSH-5-ENABLED: SSH 1.99 has been enabled), waardoor beheer op afstand end-to-end is versleuteld.  
3. **Layer 2 Fysieke Beveiliging:** Switch-A weigert actieve netwerktoegang aan onbekende apparaten op poort Fa0/1 via de sticky MAC-binding. Alle 22 ongebruikte FastEthernet-poorten en beide Gigabit-poorten zijn administratief uitgeschakeld (Administratively Down), waardoor het fysieke aanvalsoppervlak van het gebouw met 91.6% is gereduceerd.

