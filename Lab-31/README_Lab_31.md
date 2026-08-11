<img width="952" height="777" alt="image" src="https://github.com/user-attachments/assets/8b3db70f-edef-412b-8701-450e39034a32" />



# **LAB 31: DEFENSE-IN-DEPTH MICRO-SEGMENTATION & SITE-B INTER-ZONE ACL HARDENING (FINAL)**

**Document Type:** Multi-Site Inter-Zone Security & Local Gateway Hardening  
**Project Name:** Egress Extended ACL Enforcement (SECURE\_OT\_SITE\_B) on Router-2  
**Author:** P. Apondja  
**Status:** Fully Validated & OT Security Approved

## **1\. Executive Summary**

In dit lab is **Defense-in-Depth Micro-Segmentation** voltooid op **Router-2 (Site B)** met de implementatie van een lokaal afdwingende Extended Access Control List (**SECURE\_OT\_SITE\_B**) op subinterface **GigabitEthernet0/1.20**. Waar Lab 30 inkomende restricties afdwong op Site A, waarborgt deze laag een redundante, lokale beveiligingsbarrière direct voor het fysieke OT-pompsegment (10.0.20.0/24).  
De configuratie staat expliciet toe dat het engineering workstation **PC-A (172.16.10.11)** en de geautoriseerde DMZ-zone (10.0.28.0/24) communiceren met de OT-controllers. Ongeautoriseerd verkeer van overige hosts op Site B (zoals de **Hacker-laptop**) of omgeleid verkeer vanaf Site A (zoals **Hacker-laptop B**) wordt door de outbound ACL-filter van Router-2 geblokkeerd.

## **2\. Local Defense-in-Depth Matrix**

| Source Entity / Subnet | Target Zone | Protocol / Port | Action | Security Outcome |
| :---- | :---- | :---- | :---- | :---- |
| **PC-A Workstation (172.16.10.11)**  | **OT Zone (10.0.20.0/24)**  | IP (Any) | **PERMIT** | **Authorized Cross-Site Control** |
| **DMZ Zone (10.0.28.0/24)**  | **OT Zone (10.0.20.0/24)**  | IP (Any) | **PERMIT** | **Authorized SCADA Data Ingestion** |
| **Site-B Hacker-laptop**  | **OT Zone (10.0.20.0/24)**  | IP (Any) | **DENY** | **BLOCKED (Request Timed Out)** |
| **VLAN 10 Rogue Hosts (Hacker B)** | **OT Zone (10.0.20.0/24)**  | IP (Any) | **DENY** | **BLOCKED (Host Unreachable)**  |
| **Any Source** | **Any Destination** | IP (Any) | **PERMIT** | Standard Network Routing |

## **3\. Applied CLI Configurations (Router-2)**

Shell  
\! \--- 1\. EXTENDED ACCESS CONTROL LIST CREATION \---  
ip access-list extended SECURE\_OT\_SITE\_B  
 permit ip host 172.16.10.11 10.0.20.0 0.0.0.255  
 permit ip 10.0.28.0 0.0.0.255 10.0.20.0 0.0.0.255  
 deny ip any 10.0.20.0 0.0.0.255  
 permit ip any any  
exit

\! \--- 2\. OUTBOUND ACL ENFORCEMENT ON OT SUBINTERFACE \---  
interface GigabitEthernet0/1.20  
 ip access-group SECURE\_OT\_SITE\_B out  
exit

end  
write memory

## **4\. Validation & Proof of Concept**

1. **Access List Verification:**  
   * **Command:** show ip access-lists SECURE\_OT\_SITE\_B op Router-2.  
   * **Resultaat:** Regels permit ip host 172.16.10.11, permit ip 10.0.28.0/24, deny ip any 10.0.20.0/24 en permit ip any any staan exact op volgorde.  
2. **Cross-Site Authorized Control Test (PC-A):**  
   * **Source:** PC-A (172.16.10.11)  
   * **Target:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Resultaat:** **0% packet loss (4/4 packets received, RTT \<1ms)**.  
3. **Site-B Local Rogue Device Mitigation Test (Hacker-laptop):**  
   * **Source:** Hacker-laptop (Site B)  
   * **Target:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Resultaat:** **100% packet loss (Request timed out)**.  
4. **Site-A Rogue Device Cross-Site Mitigation Test (Hacker-laptop B):**  
   * **Source:** Hacker-laptop B (Site A)  
   * **Target:** PLC-Pomp-B (10.0.20.1)  
   * **Command:** ping 10.0.20.1  
   * **Resultaat:** **100% packet loss (Destination host unreachable)**.

**\[END OF LAB 31 DOCUMENTATION\]**
