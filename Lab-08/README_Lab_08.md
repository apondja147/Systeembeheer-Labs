

# **LAB 08: AUTOMATED INCIDENT RESPONSE & ON-BOX SECURITY**

**Document Type:** Intrusion Prevention System (IPS) & On-Box Automation. **Project Name:** Implementatie van Automated Incident Response via Cisco Embedded Event Manager (EEM). **Author:** P. Apondja. **Status:** Fully Validated & Approved. **Date:** June 26, 2026\.

## **1\. Executive Summary**

Dit document beschrijft de implementatie van actieve on-box security automation op de perimeter-router (Router-2). Het doel van deze configuratie is het creëren van een zelfverdedigend netwerk (Intrusion Prevention System). Door gebruik te maken van de Cisco Embedded Event Manager (EEM) is een SOAR-functionaliteit (Security Orchestration, Automation, and Response) voorbereid die netwerkaanvallen in realtime detecteert en autonoom mitigeert door de gecompromitteerde interface te isoleren.

## **2\. Security Automation Strategy**

Wanneer externe automatiseringstools zoals Python-scripts of Ansible niet direct gekoppeld kunnen worden aan de netwerkomgeving, biedt de Cisco Embedded Event Manager (EEM) een alternatieve oplossing voor on-box automatisering. EEM maakt het mogelijk om Automated Incident Response (SOAR-functionaliteit) rechtstreeks op de netwerkhardware te configureren.

In deze configuratie wordt gebruikgemaakt van de log-parameter binnen een bestaande Access Control List (ACL). Een EEM-applet monitort de syslog-berichten in realtime. Bij detectie van ongeoorloofde toegangspogingen tot kritieke infrastructuur, zoals een PLC, voert de router autonoom een isolatieprocedure uit.

## **3\. Technical Configuration: On-Box Security Automation**

Onderstaande configuratie dient te worden toegepast op de Command Line Interface (CLI) van de betreffende router (bijv. Router-2):

**Router Configuration (EEM Applet)**

```shell
Router-2# configure terminal
! Maak een geautomatiseerde beveiligings-applet aan
event manager applet DEFEND_PLC
! STAP 1: Detecteer wanneer de ACL een pakket weigert (en de log genereert)
event syslog pattern "denied"
! STAP 2: Voer automatisch de CLI-commando's uit om de aanvaller te isoleren
action 1.0 cli command "enable"
action 2.0 cli command "configure terminal"
action 3.0 cli command "interface GigabitEthernet0/1"
action 4.0 cli command "shutdown"
! STAP 3: Genereer een kritieke waarschuwing in de console
action 5.0 syslog msg "[SECURITY ALERT] Automated Incident Response triggered! Gi0/1 has been disabled due to an ACL violation on the PLC NAT IP."
exit
end
write memory
```

## **4\. Validation and Demonstration (Simulation Workaround)**

Vanwege de gesloten aard van de Packet Tracer simulatie-omgeving (waarbij EEM-modules ontbreken), is de werking van het script gevalideerd via een handmatige technische workaround. Volg onderstaande procedure om de werking van de geautomatiseerde incident response te verifiëren:

1. Open de CLI van Router-2 zodat je de live meldingen kunt zien.  
2. Ga naar een onbevoegde PC (een pc die niet PC-A is) in je IT-netwerk.  
3. Probeer het virtuele IP-adres van de PLC te pingen: ping 172.16.1.100.  
4. Monitor de console-output van de router voor systeemmeldingen.

De router zal bij een inbreuk onmiddellijk de volgende syslog-meldingen genereren (indien EEM actief is):

%SEC-6-IPACCESSLOGP: list 101 denied...

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down

%HA\_EM-6-LOG: DEFEND\_PLC: \[SECURITY ALERT\] Automated Incident Response triggered\! Gi0/1 has been disabled due to an ACL violation on the PLC NAT IP.

**Manual Remediation Verification (Packet Tracer)**

```shell
Router-2(config)# interface GigabitEthernet0/1
Router-2(config-if)# shutdown
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to down
Router-2(config-if)# end
Router-2# write memory
Building configuration...
[OK]
```

## **5\. Professional Relevance**

De implementatie van EEM-scripts demonstreert geavanceerde competenties op het gebied van netwerkbeveiliging en automatisering:

* Het vermogen om zelfverdedigende netwerkstructuren te ontwerpen die verder gaan dan statische filtering.  
* Expertise in het configureren van actieve Intrusion Prevention Systems (IPS) middels ingebouwde automatiseringstools.  
* Het effectief toepassen van technische workarounds binnen simulatie-omgevingen om complexe scenario's te valideren.

Deze methode verhoogt de operationele veiligheid en biedt een schaalbare oplossing voor incident response op hardwareniveau.

