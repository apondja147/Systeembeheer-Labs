# **LAB 09: LAYER 2 OT SWITCH HARDENING**

**Document Type:** Physical Security & Layer 2 Access Control. **Project Name:** Implementatie van Port Security & Sticky MAC op de Fabrieksvloer. **Author:** P. Apondja. **Status:** Fully Validated & Approved. **Date:** July 19, 2026\.

## **1\. Executive Summary**

Dit document beschrijft de implementatie van fysieke beveiligingsmaatregelen op de operationele technologielaag (OT-LAN). Om te voorkomen dat onbevoegde apparaten fysiek toegang krijgen tot het netwerk, is de toegangsswitch (Switch-B) geconfigureerd met Port Security. Door gebruik te maken van Sticky MAC-adressering accepteert de switch uitsluitend netwerkverkeer van de legitieme industriële asset (PLC-Pomp-B). Elke inbreuk leidt tot onmiddellijke hardwarematige isolatie van de gecompromitteerde netwerkpoort.

## **2\. OT Defense-in-Depth Strategy**

Binnen een industriële omgeving vormt fysieke toegang tot een netwerkswitch een aanzienlijk risico. Indien een firewall aan de rand van het netwerk (perimeter) succesvol inkomend verkeer filtert, kan een interne actor deze beveiliging alsnog omzeilen door lokaal een apparaat in te pluggen. Door Layer 2 Switch Hardening toe te passen, conform de strenge IEC 62443 standaarden voor industriële controlesystemen, wordt de netwerktoegang op poortniveau strikt beperkt tot geautoriseerde hardware.

## **3\. Device Configurations (Cisco IOS)**

De volgende configuratie is toegepast op Switch-B om de toegang tot de PLC (verbonden via interface FastEthernet0/2) fysiek af te dwingen op basis van het hardware-adres (MAC).

**Switch Configuration (Port Security)**

```shell
Switch-B> enable
Switch-B# configure terminal
Switch-B(config)# interface FastEthernet0/2
Switch-B(config-if)# switchport mode access
Switch-B(config-if)# switchport port-security
Switch-B(config-if)# switchport port-security maximum 1
Switch-B(config-if)# switchport port-security mac-address sticky
Switch-B(config-if)# switchport port-security violation shutdown
Switch-B(config-if)# exit
Switch-B(config)# end
Switch-B# write memory
```

## 

## **4\. Operational Verification & Security Assessment**

Om de effectiviteit van de configuratie te valideren, is een fysiek inbraakscenario op de fabrieksvloer gesimuleerd in een gecontroleerde testomgeving.

* **Legitieme baselining:** De initiële communicatie tussen het geautoriseerde netwerk en de PLC functioneerde normaal, waardoor de switch het legitieme MAC-adres van de PLC permanent in de actieve configuratie heeft vastgelegd (Sticky MAC).  
* **Fysieke inbreuk simulatie:** De fysieke netwerkkabel van de PLC is losgekoppeld en op dezelfde switchpoort (FastEthernet0/2) vervangen door een onbevoegd testapparaat (Hacker-laptop).  
* **Geautomatiseerde isolatie:** Bij de eerste netwerktransmissie (ICMP Echo Request) van het onbevoegde apparaat detecteerde Switch-B een conflict met het opgeslagen MAC-adres. De hardware greep onmiddellijk in door de poort in de *err-disable* (shutdown) status te plaatsen. Het dataverkeer resulteerde in 100% packet loss, waarmee de indringer direct en effectief werd geïsoleerd van de kritieke infrastructuur.

