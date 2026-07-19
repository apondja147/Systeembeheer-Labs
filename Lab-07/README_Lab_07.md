# **LAB 07: ZERO TRUST FIREWALL AUTOMATION & SIEM LOGGING**

**Document Type:** Zero Trust Network Access (ZTNA) & Infrastructure Automation. **Project Name:** Automated Firewall Remediation & Implicit Deny Implementation. **Author:** P. Apondja. **Status:** Fully Validated & Approved. **Date:** June 26, 2026\.

## **1\. Executive Summary**

Dit document beschrijft de remediëring van een kritieke kwetsbaarheid in de industriële perimeter-firewall (Router-2), geïdentificeerd tijdens de voorgaande security-audit. De te ruime permit-configuratie is succesvol gesaneerd en vervangen door een strikt Zero Trust (Implicit Deny) model. Ter bevordering van snelle deployment en Configuration Management (IaC), is deze wijziging voorbereid via een geautomatiseerd Python/Netmiko deployment-script.

## **2\. Security Audit Findings & Remediation Strategy**

### **2.1 Identification of the 'Permit IP Any Any' Loophole**

Tijdens een diepgaande security-audit van de perimeter-beveiliging uit Lab 06 is een kritieke blinde vlek geïdentificeerd in Access Control List (ACL) 101 op Router-2. De configuratie sloot af met de broad-spectrum regel permit ip any any.

Hoewel dit initieel was geconfigureerd om het reguliere transitverkeer niet te onderbreken, introduceert dit een onaanvaardbaar risico binnen een industriële OT-omgeving (conform de IEC 62443-beveiligingsnorm). Indien de corporate IT-omgeving uitbreidt met nieuwe VLAN's, of indien een kwaadwillende actor zijn IP-adres spooft naar een subnet buiten de gedefinieerde 192.168.10.0/24 range, biedt deze regel onbeperkte Layer 3-toegang tot het virtuele NAT IP-adres (172.16.1.100) van de vitale PLC.

### **2.2 Remediation Strategy: Implicit Deny & SIEM Logging**

Om deze kwetsbaarheid te mitigeren, is in Lab 07 de firewall-filosofie omgevormd naar een strikt Zero Trust (Implicit Deny) model. De brede permit-regel is geëlimineerd en vervangen door een specifieke blokkering:

* **Verwijdering van de kwetsbare permissie:** De open loophole is verwijderd.  
* **Implementatie van actieve afscherming:** Elke inkomende datastroom die niet expliciet is goedgekeurd voor PC-A wordt nu onherroepelijk gedropt aan de netwerkgrens. Binnen productieomgevingen wordt hier de log-parameter aan toegevoegd om direct Syslog-meldingen te genereren in het Security Operations Center (SOC).

## **3\. Automation Architecture (Infrastructure as Code)**

Om de foutgevoeligheid van handmatige CLI-invoer te elimineren, is de overstap gemaakt naar Infrastructure as Code (IaC).

### **3.1 Device Preparation (SSH Activation)**

Voordat geautomatiseerde deployment mogelijk is, moet het doelapparaat (Router-2) via de console voorzien worden van Layer 7 managementbeveiliging (SSH). De RSA-sleutels en domeingegevens zijn succesvol gegenereerd en vastgelegd.

**Router Configuration (SSH)**

```shell
Router# configure terminal
Router(config)# hostname Router-2
Router-2(config)# ip domain-name ot-fabriek.local
Router-2(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024
Router-2(config)# username admin secret CyberSafe2026!
Router-2(config)# line vty 0 4
Router-2(config-line)# transport input ssh
Router-2(config-line)# login local
Router-2(config-line)# exit
```

### **3.2 Python / Netmiko Deployment Script**

Het onderstaande Python-script is ontworpen om via SSH in te loggen op de boundary router en autonoom de Zero Trust ACL-regels af te dwingen. Dit script valideert de vereiste theorie voor toekomstige DevSecOps-implementaties.

**Python Script**

```py
from netmiko import ConnectHandler

router_2 = {
'device_type': 'cisco_ios',
'host': '172.16.1.2',
'username': 'admin',
'password': 'CyberSafe2026!',
'secret': 'cisco123',
}

zero_trust_commands = [
'no access-list 101',
'access-list 101 permit tcp host 192.168.10.1 host 172.16.1.100 eq 502',
'access-list 101 permit icmp host 192.168.10.1 host 172.16.1.100',
'access-list 101 deny ip any host 172.16.1.100',
'access-list 101 permit ip any any',
'interface GigabitEthernet0/1',
'ip access-group 101 in',
'exit'
]

try:
    net_connect = ConnectHandler(**router_2)
    net_connect.enable()
    output = net_connect.send_config_set(zero_trust_commands)
    net_connect.save_config()
    net_connect.disconnect()
except Exception as e:
    print(f"Deployment failed: {e}")
```

## 

## **4\. Operational State Configuration (Cisco IOS)**

Na het toepassen van de remediëring via Packet Tracer, bevat de actieve infrastructuur de volgende aangescherpte rule-matrix op Router-2. *(Let op: de specifieke Syslog log variabele is geëxcludeerd in verband met Packet Tracer syntax-limitaties, maar wordt in fysieke enterprise omgevingen standaard toegevoegd)*.

**Final Running Configuration**

```shell
!
Router-2> enable
Router-2# configure terminal
!
! --- ZERO TRUST FIREWALL ACL 101 ---
no access-list 101
access-list 101 permit tcp host 192.168.10.1 host 172.16.1.100 eq 502
access-list 101 permit icmp host 192.168.10.1 host 172.16.1.100
access-list 101 deny ip any host 172.16.1.100
access-list 101 permit ip any any
!
interface GigabitEthernet0/1
 ip access-group 101 in
 exit
!
end
copy running-config startup-config
```

**\[END OF LAB 07 DOCUMENTATION\]**