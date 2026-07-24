# **LAB 14: INTER-VLAN ROUTING & DHCP CONFIGURATION (FINAL)**

**Document Type:** Network Infrastructure & Dynamic Host Configuration

**Project Name:** Implementatie van VLAN-segmentatie & Dynamic IP Allocation

**Author:** P. Apondja

**Status:** Fully Validated & Approved

## **1\. Executive Summary**

In dit lab is de netwerkinfrastructuur succesvol gesegmenteerd in virtuele netwerken (VLAN's). Met behulp van **Router-on-a-Stick (802.1Q encapsulation)** verzorgt Router-1 (Defensie-R1) de routing tussen VLAN 10 (Management) en VLAN 20 (Guest/IoT). Daarnaast is op Router-1 een centrale DHCP-service geconfigureerd die de geautoriseerde werkplekken automatisch voorziet van dynamische IP-adresseringen, waaronder gateways en subnetmaskers.

## **2\. Network & Sub-interface Addressing Plan**

## 

| Apparaat | Interface / Sub-interface | VLAN | IP-Adres / Subnet | DHCP Pool Naam | Status |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Router-1** | GigabitEthernet0/0.10 | VLAN 10 | 172.16.10.1 /24 | LAN\_MGMT | Active / Gateway |
| **Router-1** | GigabitEthernet0/0.20 | VLAN 20 | 172.16.20.1 /24 | LAN\_GUEST | Active / Gateway |
| **Switch-A** | FastEthernet0/1 | Access VLAN 10 | Dynamisch via DHCP | N/v/t | Up / Access |
| **Switch-A** | GigabitEthernet0/1 | Trunk (dot1q) | N/v/t | N/v/t | Up / Trunk |
| **PC-A** | FastEthernet0 | VLAN 10 | 172.16.10.11 /24 | LAN\_MGMT | Lease Success |

## **3\. Applied Configurations (Cisco IOS)**

### **3.1 Switch-A Configuration (VLANs & Trunking)**

```shell
vlan 10
 name MGMT
exit

vlan 20
 name GUEST_IOT
exit

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
exit

interface GigabitEthernet0/1
 switchport mode trunk
exit
```

### **3.2 Router-1 Configuration (Inter-VLAN & DHCP)**

```shell
interface GigabitEthernet0/0
 no ip address
 no shutdown
exit

! --- SUB-INTERFACES ---
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 172.16.10.1 255.255.255.0
exit

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 172.16.20.1 255.255.255.0
exit

! --- DHCP CONFIGURATIE ---
ip dhcp excluded-address 172.16.10.1 172.16.10.10
ip dhcp excluded-address 172.16.20.1 172.16.20.10

ip dhcp pool LAN_MGMT
 network 172.16.10.0 255.255.255.0
 default-router 172.16.10.1
exit

ip dhcp pool LAN_GUEST
 network 172.16.20.0 255.255.255.0
 default-router 172.16.20.1
exit
```

## **4\. Validation & Proof of Concept**

> 1. **DHCP Lease Verification:** PC-A gewisseld naar DHCP-modus. Binnen 2 seconden een geldige IP-lease verkregen: 172.16.10.11/24, Gateway: 172.16.10.1.  
> 2. **Trunk Status:** GigabitEthernet0/1 transporteert getagged verkeer over dot1q naar Router-1.

**\[END OF LAB 14 DOCUMENTATION\]**