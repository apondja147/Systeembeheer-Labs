<img width="957" height="773" alt="image" src="https://github.com/user-attachments/assets/f80b5cf5-69ae-42ce-ad7c-39bec2ed9717" />



# **LAB 27: INTER-VLAN ROUTING & OSPF DYNAMIC ROUTING**

**Document Type:** Network Infrastructure & Routing Configuration

**Project Name:** Subinterface VLAN 28 & OSPF Area 0 Integration

**Author:** P. Apondja

**Status:** Fully Validated & OT Security Approved

### **1\. Executive Summary**

In dit lab is de subinterface GigabitEthernet0/1.28 succesvol geconfigureerd op **Router-2** om Inter-VLAN routing voor VLAN 28 af te handelen. Met de toewijzing van het IP-adres 10.0.28.254/24 fungeert de router als default gateway voor dit netwerksegment. Daarnaast is het netwerk 10.0.28.0/24 toegevoegd aan het **OSPF Process 1** in **Area 0**, waardoor dynamische routing voor dit VLAN actief is. Alle wijzigingen zijn opgeslagen in de startup-configuratie.

### **2\. Interface & Routing Matrix**

### 

| Interface | IP-Adres / Subnet | Encapsulation | OSPF Area | Status | Active Session / Verification |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **GigabitEthernet0/0** | 10.0.0.2/24 | N/A | Area 0 | UP / UP | Transit Link |
| **GigabitEthernet0/1.10** | 10.0.10.254/24 | 802.1Q (VLAN 10\) | \- | UP / UP | Gateway VLAN 10 |
| **GigabitEthernet0/1.20** | 10.0.20.254/24 | 802.1Q (VLAN 20\) | \- | UP / UP | Gateway VLAN 20 |
| **GigabitEthernet0/1.28** | 10.0.28.254/24 | 802.1Q (VLAN 28\) | Area 0 | UP / UP | Verified (Manual IP / OSPF) |

### **3\. Applied Configurations (Router-2 CLI)**

### 

```shell
! 1. VTY HARDENING & LOGGING SYNCHRONOUS
line vty 0 4
  exec-timeout 5 0
  logging synchronous
exit
!
! 2. SUBINTERFACE & INTER-VLAN ROUTING (VLAN 28)
interface GigabitEthernet0/1.28
  encapsulation dot1Q 28
  ip address 10.0.28.254 255.255.255.0
exit
!
! 3. OSPF DYNAMIC ROUTING CONFIGURATION
router ospf 1
  network 10.0.28.0 0.0.0.255 area 0
exit
!
! 4. SAVE CONFIGURATION
end
copy running-config startup-config
```

### 

### **4\. Validation & Proof of Concept**

> 1. **Interface Status Inspection**: Command show ip interface brief bevestigt dat subinterface GigabitEthernet0/1.28 de status up/up heeft gekregen met IP-adres 10.0.28.254 (Method: manual).  
> 2. **OSPF Network Integration**: Netwerk 10.0.28.0/24 is geregistreerd onder router ospf 1 in Area 0\.  
> 3. **Persistence Verification**: Command copy running-config startup-config succesvol uitgevoerd (\[OK\]).

\[END OF LAB 22 DOCUMENTATION\]

