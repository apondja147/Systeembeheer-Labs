# **RAPPORTAGE LAB 11: ENTERPRISE-ARCHITECTUUR EN BEVEILIGINGSHARDENING**

**Documentclassificatie:** Netwerkbeveiliging, Verkeersinspectie en Gecentraliseerde Monitoring

**Projectidentificatie:** Implementatie van Layer 2/3 Hardening en Syslog-systemen

**Author:** P. Apondja

**Status:** Fully Validated, Troubleshooted & Approved

## **1\. Executive Summary**

Dit rapport documenteert de succesvolle optimalisatie van de OT-netwerkinfrastructuur conform enterprise-beveiligingsnormen. De infrastructuur is structureel beveiligd tegen ongeoorloofde toegang. Daarnaast zijn kritieke netwerkdefecten, waaronder een fysieke bekabelingsfout op Layer 1, gedetecteerd, geanalyseerd en definitief verholpen.

## **2\. Security Implementations (Cisco IOS)**

### **2.1 Layer 2 Hardening (Switch-B)**

* **Deactivatie van DTP:** De configuratie "switchport nonegotiate" is geïmplementeerd om de risico's op VLAN-hopping aanvalsscenario's te mitigeren.  
* **STP-Optimalisatie:** "Spanning-tree portfast" is geconfigureerd op essentiële access-poorten om de operationele efficiëntie te verhogen door de standaard forwarding-vertraging te elimineren.

### **2.2 Layer 3 Isolation (Router-2)**

* **Outbound Access Control Lists (ACL):** De "ISOLATE\_OT" ACL waarborgt de integriteit van de industriële componenten op VLAN 20 door al het ongeautoriseerde verkeer vanuit het IT-netwerk (VLAN 10\) proactief te blokkeren.

### **2.3 Centralized Logging (Router-on-a-Stick)**

* **Herstel van Inter-VLAN Routing:** De sub-interface GigabitEthernet0/1.10 is geconfigureerd middels 802.1Q-encapsulatie (IP-adres: 10.0.10.254) ter facilitering van beveiligde communicatie tussen de netwerksegmenten.  
* **Correctie van Fysieke Topologie:** De trunk-verbinding is hersteld en verbonden met interface Gig0/1, waardoor de correcte routering van VLAN-verkeer is gewaarborgd.  
* **Syslog-centralisatie:** Real-time monitoring is geactiveerd waarbij systeemlogs via UDP-poort 514 worden geëxporteerd naar de centrale logserver (10.0.10.250).

*Einde officiële rapportage*