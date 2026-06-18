# Configuration Documentation
## 06 - Enterprise DMZ & Firewall Lab

---

## 1. Core-Switch

### Creación de VLANs
```cisco
vlan 10
 name INSIDE
vlan 20
 name DMZ
vlan 30
 name SERVERS
```

### Trunk hacia FW-NovaTech
```cisco
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
```

### Puertos de acceso
```cisco
! VLAN 10 - Usuarios internos
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 no shutdown

! VLAN 20 - DMZ
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 20
 no shutdown

! VLAN 30 - Servidores internos
interface FastEthernet0/4
 switchport mode access
 switchport access vlan 30
 no shutdown
```

### Verificación
```cisco
show vlan brief
show interfaces trunk
```

---

## 2. FW-NovaTech

### Subinterfaces Router-on-a-Stick
```cisco
! VLAN 10 - Inside
interface GigabitEthernet0/0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
 no shutdown

! VLAN 20 - DMZ
interface GigabitEthernet0/0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 ip nat inside
 no shutdown

! VLAN 30 - Servers
interface GigabitEthernet0/0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 ip nat inside
 no shutdown

! WAN hacia ISP
interface GigabitEthernet0/0/1
 ip address 200.1.1.2 255.255.255.0
 ip nat outside
 no shutdown
```

### Default Route
```cisco
ip route 0.0.0.0 0.0.0.0 200.1.1.1
```

### NAT/PAT
```cisco
access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
access-list 1 permit 192.168.30.0 0.0.0.255

ip nat inside source list 1 interface GigabitEthernet0/0/1 overload
```

### NAT estático - Web Server
```cisco
ip nat inside source static 192.168.20.10 200.1.1.10
```

### ACL perimetral
```cisco
ip access-list extended DMZ-INBOUND
 permit icmp any host 200.1.1.2
 permit tcp any host 192.168.20.10 eq 80
 permit tcp any host 192.168.20.10 eq 443
 deny ip any any

interface GigabitEthernet0/0/1
 ip access-group DMZ-INBOUND in
```

### Verificación
```cisco
show ip nat translations
show ip nat statistics
show access-lists
show ip route
show ip interface brief
```

---

## 3. ISP Router

### Interfaces
```cisco
interface GigabitEthernet0/0/0
 ip address 200.1.1.1 255.255.255.0
 no shutdown

interface GigabitEthernet0/0/1
 ip address 203.0.113.1 255.255.255.0
 no shutdown
```

### Rutas estáticas hacia la empresa
```cisco
ip route 192.168.10.0 255.255.255.0 200.1.1.2
ip route 192.168.20.0 255.255.255.0 200.1.1.2
ip route 192.168.30.0 255.255.255.0 200.1.1.2
ip route 200.1.1.10 255.255.255.255 200.1.1.2
```

---

## 4. Pruebas de conectividad

```cisco
! Inside → Internet
ping 200.1.1.1

! Inside → DMZ
ping 192.168.20.10

! Internet → Web Server (desde browser)
http://200.1.1.10
```

---

## 5. Resultado final

| Función | Estado |
|---|---|
| Segmentación VLANs | ✅ |
| Firewall perimetral | ✅ |
| NAT/PAT usuarios internos | ✅ |
| Publicación Web Server | ✅ |
| ACL bloqueando Internet → Inside | ✅ |
| Troubleshooting documentado | ✅ |
