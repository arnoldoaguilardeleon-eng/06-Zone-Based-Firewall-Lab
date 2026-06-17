# 06 - Enterprise DMZ & Firewall Lab

# Configuration Documentation

Configuraciones utilizadas para implementar una arquitectura empresarial con segmentación mediante VLANs, firewall perimetral, NAT/PAT, DMZ y publicación de servicios hacia Internet.

---

# 1. Switch-Core Configuration

## VLAN Creation

Se crearon las VLAN correspondientes a cada zona de red:

```cisco
vlan 10
name INSIDE

vlan 20
name DMZ

vlan 30
name SERVERS
````

---

## Trunk Configuration

El enlace hacia el firewall fue configurado como trunk 802.1Q para transportar múltiples VLANs.

```cisco
interface FastEthernet0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30
```

---

## Access Ports

Ejemplo de asignación de puertos:

### VLAN 10 - Usuarios internos

interface FastEthernet0/2
switchport mode access
switchport access vlan 10


### VLAN 20 - DMZ

interface FastEthernet0/3
switchport mode access
switchport access vlan 20

### VLAN 30 - Servidores internos

interface FastEthernet0/4
switchport mode access
switchport access vlan 30

---

## Verification

Comandos utilizados:

show vlan brief

show interfaces trunk

---

# 2. FW-NovaTech Configuration

## Interface Configuration

El firewall fue configurado utilizando subinterfaces para realizar Router-on-a-Stick.

---

## VLAN 10 - Inside Network

interface GigabitEthernet0/0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
ip nat inside

---

## VLAN 20 - DMZ Network

interface GigabitEthernet0/0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
ip nat inside


---

## VLAN 30 - Server Network

interface GigabitEthernet0/0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
ip nat inside


---

## WAN Interface

Conexión hacia ISP:

interface GigabitEthernet0/0/1
ip address 200.1.1.2 255.255.255.0
ip nat outside

---

# 3. Default Route

Se configuró una ruta por defecto hacia el ISP.

ip route 0.0.0.0 0.0.0.0 200.1.1.1


Verificación:

show ip route

---

# 4. NAT/PAT Configuration

## ACL para tráfico interno

Se permitió la traducción de las redes internas:

access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
access-list 1 permit 192.168.30.0 0.0.0.255

---

## Dynamic NAT Overload

Permite que múltiples equipos internos compartan la IP pública del firewall.

ip nat inside source list 1 interface GigabitEthernet0/0/1 overload


Verificación:

show ip nat translations


---

# 5. Static NAT - Web Server Publication

Se publicó el servidor web de la DMZ hacia Internet.

Servidor interno:


192.168.20.10


IP pública:

```
200.1.1.10
```

Configuración:

ip nat inside source static 192.168.20.10 200.1.1.10


---

# 6. DMZ Security ACL

Se creó una ACL para controlar el tráfico entrante desde Internet.

ip access-list extended DMZ-INBOUND

permit icmp any host 200.1.1.2

permit tcp any host 192.168.20.10 eq 80

permit tcp any host 192.168.20.10 eq 443

deny ip any any


---

## Aplicación de ACL

La ACL fue aplicada en la interfaz WAN:

interface GigabitEthernet0/0/1

ip access-group DMZ-INBOUND in


---

# 7. ISP Router Configuration

## WAN Interface

interface GigabitEthernet0/0/0

ip address 200.1.1.1 255.255.255.0

no shutdown

---

## External Network

Red simulada de Internet:

interface GigabitEthernet0/0/1

ip address 203.0.113.1 255.255.255.0

no shutdown

---

## Routing hacia la empresa

Rutas estáticas hacia las redes internas:

ip route 192.168.10.0 255.255.255.0 200.1.1.2

ip route 192.168.20.0 255.255.255.0 200.1.1.2

ip route 192.168.30.0 255.255.255.0 200.1.1.2

Ruta hacia IP pública del servidor:

ip route 200.1.1.10 255.255.255.255 200.1.1.2


---

# 8. Verification Commands

## Interfaces

show ip interface brief


---

## Routing Table

show ip route


---

## NAT Status

show ip nat translations

show ip nat statistics


---

## ACL Verification

show access-lists


---

## Connectivity Tests

### Inside → Internet

ping 200.1.1.1


---

### Inside → DMZ

ping 192.168.20.10


---

### Internet → Web Server

Browser:


http://200.1.1.10


---

# Final Result

La infraestructura quedó funcionando con:

* Segmentación mediante VLANs.
* Firewall perimetral.
* Red DMZ aislada.
* NAT/PAT para usuarios internos.
* Publicación segura de servidor Web.
* Control de acceso mediante ACLs.
* Troubleshooting documentado.

```

Esta versión ya tiene una estructura más cercana a documentación de un ingeniero de redes: **README explica el proyecto, CONFIGURATION explica cómo está construido**.
```
