# 06 - Enterprise DMZ & Firewall Lab

Simulación de una arquitectura empresarial con seguridad perimetral, segmentación de red, DMZ pública y publicación segura de servicios mediante NAT.

Implementada en Cisco Packet Tracer.

---

# Escenario

Empresa con tres zonas principales de red:

- **Inside Network (VLAN 10)** → Red interna de usuarios con acceso a Internet mediante NAT/PAT.
- **DMZ (VLAN 20)** → Zona aislada para servicios públicos accesibles desde Internet.
- **Server Network (VLAN 30)** → Red interna destinada a servidores corporativos.

El objetivo del laboratorio es implementar una arquitectura donde un servidor web pueda ser publicado hacia Internet sin exponer directamente la red interna.

---

# Topología

![Topologia](topologia.png)

---

# Direccionamiento

| Zona | Red | Gateway | VLAN |
|---|---|---|---|
| Inside Users | 192.168.10.0/24 | 192.168.10.1 | VLAN 10 |
| DMZ Web Server | 192.168.20.0/24 | 192.168.20.1 | VLAN 20 |
| Internal Servers | 192.168.30.0/24 | 192.168.30.1 | VLAN 30 |
| Firewall WAN | 200.1.1.0/24 | 200.1.1.2 | — |
| ISP Network | 203.0.113.0/24 | 203.0.113.1 | — |

---

# Tecnologías implementadas

- VLANs y Trunks 802.1Q
- Router-on-a-Stick
- Segmentación de red empresarial
- Firewall perimetral Cisco
- ACLs extendidas
- NAT dinámico con PAT
- NAT estático mediante Port Forwarding
- Publicación de servidor Web hacia Internet
- DMZ empresarial
- Default Route hacia ISP
- Troubleshooting de conectividad y seguridad

---

# Arquitectura de seguridad
