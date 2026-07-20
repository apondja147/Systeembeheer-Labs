# **LAB 10: OT NETWORK SEGMENTATION & ZONE ROUTING**

**Document Type:** Purdue Model Architecture & VLAN Segmentation. **Project Name:** Logische Isolatie van de Fabrieksvloer via Router-on-a-Stick. **Author:** P. Apondja. **Status:** Fully Validated & Approved. **Date:** July 20, 2026\.

## **1\. Executive Summary**

Dit document beschrijft de succesvolle scheiding van de IT- en OT-netwerkverkeersstromen. Om te voorkomen dat onbeheerst broadcast-verkeer vanuit kantooromgevingen de kritieke PLC's bereikt, is het netwerk op Layer 2 logisch opgedeeld in gescheiden Virtual LANs (VLANs). De routering tussen deze afgeschermde zones wordt gefaciliteerd via een Router-on-a-Stick (RoaS) architectuur op de perimeter-router, waarbij 802.1Q trunking wordt gebruikt om verkeerstromen te identificeren en isoleren.

## **2\. Network Architecture & VLAN Design**

Het netwerk is opgedeeld in twee primaire security-zones om het Purdue-model na te bootsen:

* **VLAN 10 (IT\_Management):** Gereserveerd voor regulier kantoorverkeer en netwerkbeheer.  
* **VLAN 20 (OT\_Production):** Strikte productiezone (10.0.20.0/24 subnet). De PLC (10.0.20.1) functioneert uitsluitend binnen dit afgeschermde domein.

## **3\. Device Configurations (Cisco IOS)**

### **3.1 Layer 2 Isolation (Switch-B)**

De toegangsswitch is geconfigureerd om de VLAN-database op te bouwen, de PLC lokaal te isoleren en een 802.1Q trunk-verbinding naar de router op te zetten.

**Cisco IOS Configuration**

```shell
Switch-B> enable
Switch-B# configure terminal
! VLAN Database
Switch-B(config)# vlan 10
Switch-B(config-vlan)# name IT_Management
Switch-B(config-vlan)# exit
Switch-B(config)# vlan 20
Switch-B(config-vlan)# name OT_Production
Switch-B(config-vlan)# exit
! PLC Access Port Allocation
Switch-B(config)# interface FastEthernet0/2
Switch-B(config-if)# switchport access vlan 20
Switch-B(config-if)# exit
! 802.1Q Trunk Configuration
Switch-B(config)# interface FastEthernet0/1
Switch-B(config-if)# switchport mode trunk
Switch-B(config-if)# exit
```

### **3.2 Layer 3 Zone Routing (Router-2)**

De perimeter-router (Router-2) is voorzien van virtuele sub-interfaces om als Default Gateway te fungeren voor de geïsoleerde OT-zone, waarbij IP-conflicten met de bestaande IT-subnets zijn gemitigeerd.

**Cisco IOS Configuration**

```shell
Router-2> enable
Router-2# configure terminal
! Physical Interface Activation
Router-2(config)# interface GigabitEthernet0/1
Router-2(config-if)# no shutdown
Router-2(config-if)# no ip address
Router-2(config-if)# exit
! Virtual Sub-Interface (OT_Production Gateway)
Router-2(config)# interface GigabitEthernet0/1.20
Router-2(config-subif)# encapsulation dot1Q 20
Router-2(config-subif)# ip address 10.0.20.254 255.255.255.0
Router-2(config-subif)# exit
```

**\[END OF LAB 10 DOCUMENTATION\]**

