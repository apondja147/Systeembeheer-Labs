<img width="965" height="606" alt="image" src="https://github.com/user-attachments/assets/28bbf031-891e-4b58-9615-1cfdf918991c" />


\# LAB 03: INFRASTRUCTURE HARDENING & TRANSPORT SECURITY (SSH)  
\*\*Document Type:\*\* Low-Level Design (LLD) / Security Hardening Documentation    
\*\*Project Name:\*\* Implementing Password Encryption and Secure Remote Access (SSH)    
\*\*Author:\*\* P. Apondja    
\*\*Status:\*\* Approved & Validated    
\*\*Date:\*\* June 25, 2026  

\---

\#\# 1\. Executive Summary  
Dit document dekt de defensieve beveiligingsfase (hardening) van de bestaande netwerkinfrastructuur. In deze fase zijn twee kritieke kwetsbaarheden binnen de Cisco IOS-apparatuur verholpen. Alle lokale wachtwoorden zijn met cryptografische hashes beveiligd tegen fysieke diefstal van configuratiebestanden, en onbeveiligde beheersprotocollen (Telnet) zijn volledig uitgefaseerd en vervangen door een end-to-end versleutelde SSH-omgeving.

\---

\#\# 2\. Identified Vulnerabilities & Mitigation Strategy  
1\. \*\*Plain-Text Password Vulnerability:\*\* Lokale wachtwoorden stonden voorheen in leesbare tekst opgeslagen in de \`running-config\`. Dit is opgelost door het globaal activeren van het Cisco proprietary encryptie-algoritme.  
2\. \*\*Clear-Text Remote Access (Telnet Risk):\*\* Het beheer op afstand via virtuele lijnen (VTY) accepteerde standaard onversleuteld verkeer. Dit is gemitigeerd door het afdwingen van het \*Secure Shell (SSH)\* protocol (versie 1.99/2) met een RSA-sleutellengte van 1024 bits.

\---

\#\# 3\. Cryptographic Identity & Keys  
Voor het genereren van de asymmetrische sleutelparen is de router voorzien van een unieke hostname en een lokaal defensiedomein.

\* \*\*Hostname:\*\* \`Defensie-R1\`  
\* \*\*Domain Name:\*\* \`defensie.local\`  
\* \*\*Algoritme:\*\* RSA (Rivest-Shamir-Adleman)  
\* \*\*Sleutelsterkte:\*\* 1024 bits (Modulus)

\---

\#\# 4\. Security Hardening Configuration Logs (Cisco IOS)

\#\#\# 4.1 Activering van Wachtwoordversleuteling & SSH  
\`\`\`text  
\!  
Defensie-R1\> enable  
Defensie-R1\# configure terminal  
\!  
\! \--- GLOBAL SECURITY UPGRADES \---  
Defensie-R1(config)\# service password-encryption  
Defensie-R1(config)\# hostname Defensie-R1  
Defensie-R1(config)\# ip domain-name defensie.local  
\!  
\! \--- CRYPTO KEY GENERATION (1024 BITS) \---  
Defensie-R1(config)\# crypto key generate rsa  
\! Input bij prompt: 1024  
\!  
\! \--- LOCAL LOCAL DATABASE ACCOUNT \---  
Defensie-R1(config)\# username admin secret CyberSafe2026\!  
\!  
\! \--- VIRTUAL TERMINAL LINES HARDENING \---  
Defensie-R1(config)\# line vty 0 4  
Defensie-R1(config-line)\# transport input ssh  
Defensie-R1(config-line)\# login local  
Defensie-R1(config-line)\# exit  
\!  
Defensie-R1(config)\# end  
Defensie-R1\# copy running-config startup-config  
