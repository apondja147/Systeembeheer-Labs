# **LAB 06: INDUSTRIAL NAT & ASSET OBFUSCATION**

**Document Type:** Industrial Network Address Translation (NAT) & Asset Hiding Design. **Project Name:** Perimeter Defense & PLC IP Masking via Static NAT. **Author:** P. Apondja. **Status:** Fully Validated & Approved. **Date:** June 26, 2026\.

## **1\. Executive Summary**

Dit document beschrijft de succesvolle implementatie van Industrial NAT (Network Address Translation) en Asset Obfuscation op de perimeter-router van de fabrieksvloer (Router-2). Het primaire doel van deze configuratie is het volledig maskeren en verbergen van het daadwerkelijke IP-adresseringsplan van de operationele technologie (OT-LAN). Door kritieke industriële componenten te voorzien van een extern virtueel IP-adres, wordt gerichte netwerk-scanning en directe IP-gebaseerde exploitatie door onbevoegde IT-hosts effectief gemitigeerd.

## **2\. OT Defense-in-Depth & Obfuscation Strategy**

In een volwassen OT-beveiligingsarchitectuur mag de interne netwerkstructuur van kritieke productielijnen nooit blootgesteld worden aan de IT-beheerlaag. Indien een aanvaller erin slaagt om de Access Control Lists (ACL) te omzeilen, voorkomt IP-maskering dat de aanvaller de exacte locatie en topologie van de PLC's in kaart brengt.

Conform deze defensieve strategie is de vitale vloeistofpomp-PLC losgemaakt van directe Layer 3 routing:

* **Internal Real IP (Private OT):** Het daadwerkelijke IP-adres (10.0.0.1) is nu uitsluitend lokaal bekend binnen het afgeschermde switch-segment achter de grensrouter.  
* **External Virtual IP (Masked WAN):** Voor de buitenwereld en de IT-beheerlaag is de PLC uitsluitend bereikbaar en zichtbaar op het transit-adres 172.16.1.100.

## **3\. Network Architecture & Filter Plan**

De netwerkarchitectuur is ontworpen om een strikte grens te trekken tussen de IT- en OT-omgevingen via netwerkadressering.

### **3.1 NAT Interface Domain Designation**

### 

| Device Name | Router Interface | NAT Domain Role | Security Context |
| :---- | :---- | :---- | :---- |
| **Router-2** | GigabitEthernet0/0 | ip nat inside | Trusted / Protected Production Zone\[cite: 2\] |
| **Router-2** | GigabitEthernet0/1 | ip nat outside | Untrusted / Exposed Corporate Zone\[cite: 2\] |

### **3.2 Static NAT Translation Table**

### 

| Inside Local IP (Real Asset) | Outside Local IP (Masked IP) | Protocol / Ports | Authorization Status |
| :---- | :---- | :---- | :---- |
| **10.0.0.1** | **172.16.1.100** | All Protocols (Filtered by ACL 101\) | Restricted to PC-A\[cite: 2\] |

## **4\. Device Configurations (Cisco IOS)**

### **4.1 Router-2 Core NAT & Firewall Realignment**

```shell
!
Router-2> enable
Router-2# configure terminal
!
! --- DESIGNATE NAT DOMAINS ---
interface GigabitEthernet0/0
 ip nat inside
 exit
!
interface GigabitEthernet0/1
 ip nat outside
 exit
!
! --- CONFIGURE STATIC NAT TRANSLATION ---
ip nat inside source static 10.0.0.1 172.16.1.100
!
! --- REALIGN FIREWALL (ACL 101) TO THE VIRTUAL NAT IP ---
no access-list 101
access-list 101 permit tcp host 192.168.10.1 host 172.16.1.100 eq 502
access-list 101 permit icmp host 192.168.10.1 host 172.16.1.100
access-list 101 deny ip 192.168.10.0 0.0.0.255 host 172.16.1.100
access-list 101 permit ip any any
!
interface GigabitEthernet0/1
 ip access-group 101 in
 exit
!
end
copy running-config startup-config
```

## **5\. Operational Verification & Cyber Assessment**

### **5.1 Masked ICMP Flow Validation**

Vanaf het geautoriseerde Engineering Workstation (PC-A) is een ICMP-connectiviteitstest uitgevoerd naar het nieuw geconfigureerde virtuele NAT IP-adres van de PLC. **Analyse:** Na het herstellen van de realtime netwerktijd in de simulator vliegen de pakketjes direct en zonder vertraging door de router heen. De router vertaalt het virtuele adres vlekkeloos door naar de back-end PLC, wat resulteert in een stabiele verbinding met 0% pakketverlies.

```
Pinging 172.16.1.100 with 32 bytes of data:

Reply from 172.16.1.100: bytes=32 time<16ms TTL=126
Reply from 172.16.1.100: bytes=32 time<16ms TTL=126
Reply from 172.16.1.100: bytes=32 time<16ms TTL=126
Reply from 172.16.1.100: bytes=32 time<16ms TTL=126

Ping statistics for 172.16.1.100:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

### **5.2 Security Hardening Assessment**

1\. **Successful Obfuscation:** Pogingen om vanaf PC-A direct naar het oude, echte IP-adres (10.0.0.1) te pingen falen onherroepelijk. Dit bewijst dat het interne OT-netwerk succesvol is verborgen achter het Layer 3 NAT-scherm van Router-2.

2\. **Stateful Inspection Boundary:** Inkomend verkeer gericht aan 172.16.1.100 wordt eerst gecontroleerd door ACL 101\. Onbevoegde IT-hosts die poort-scans uitvoeren op het transit-netwerk stuiten op de actieve 'Deny' regels, waardoor malafide Modbus-injecties op poort 502 onmogelijk worden gemaakt zonder de identiteit van PC-A te spoofen.

**\[END OF LAB 06 DOCUMENTATION\]**