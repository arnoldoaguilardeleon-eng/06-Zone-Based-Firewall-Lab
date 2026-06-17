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
            INTERNET
                |
                |
          ISP Router
                |
                |
          FW-NovaTech
                |
    -------------------------
    |           |           |
 VLAN 10     VLAN 20     VLAN 30
 Inside       DMZ       Servers

 
---

# Reglas de seguridad implementadas

| Regla | Origen | Destino | Resultado |
|---|---|---|---|
| 1 | VLAN 10 | Internet | ✅ Permitido |
| 2 | VLAN 10 | DMZ Web Server | ✅ Permitido |
| 3 | Internet | Web Server DMZ | ✅ Permitido (HTTP/HTTPS) |
| 4 | Internet | VLAN 10 | ❌ Denegado |
| 5 | Internet | VLAN 30 | ❌ Denegado |
| 6 | Servicios no publicados | DMZ | ❌ Denegado |

---

# Servicios publicados

Servidor Web:

---

# Reglas de seguridad implementadas

| Regla | Origen | Destino | Resultado |
|---|---|---|---|
| 1 | VLAN 10 | Internet | ✅ Permitido |
| 2 | VLAN 10 | DMZ Web Server | ✅ Permitido |
| 3 | Internet | Web Server DMZ | ✅ Permitido (HTTP/HTTPS) |
| 4 | Internet | VLAN 10 | ❌ Denegado |
| 5 | Internet | VLAN 30 | ❌ Denegado |
| 6 | Servicios no publicados | DMZ | ❌ Denegado |

---

# Servicios publicados

Servidor Web:
IP Privada:
192.168.20.10

IP Pública:
200.1.1.10


Servicios expuestos:

| Servicio | Puerto | Estado |
|---|---|---|
| HTTP | 80 | ✅ Publicado |
| HTTPS | 443 | ✅ Publicado |


---

# Configuraciones principales

Las configuraciones completas se encuentran en:
CONFIGURATION.md


Incluyendo:

- VLAN configuration
- Trunk configuration
- Router-on-a-Stick
- NAT/PAT
- Static NAT
- ACL configuration
- Routing configuration

---

# Evidencias

## VLAN y conectividad interna

![VLAN Testing](vlan-test.png)

## Salida a Internet mediante NAT/PAT

![Internet Access](internet-test.png)

## Traducciones NAT

![NAT Translation](nat-table.png)

## Acceso interno hacia DMZ

![Internal Web Access](internal-web.png)

## Acceso externo hacia servidor Web

![External Web Access](external-web.png)

## ACL aplicada en interfaz WAN

![ACL Security](acl-security.png)

---

# Troubleshooting realizado durante el laboratorio


Diagnóstico:

- Gateway interno funcionando.
- Interfaces activas.
- NAT creando traducciones correctamente.
- La conectividad física estaba operativa.

Se identificó que la ACL aplicada en la interfaz WAN estaba bloqueando tráfico ICMP.

---

## Problema 2 — ACL bloqueaba pruebas de conectividad

Configuración inicial:
permit tcp any host 200.1.1.10 eq 80
permit tcp any host 200.1.1.10 eq 443
deny ip any any


La regla final bloqueaba cualquier tráfico que no fuera HTTP/HTTPS.

Solución:

Agregar permiso ICMP antes del deny:

permit icmp any host 200.1.1.2


Resultado:

- Ping de administración funcionando.
- Publicación Web funcionando.
- Seguridad perimetral mantenida.

---

# Conceptos aprendidos

Durante este laboratorio se reforzaron conceptos utilizados en ambientes empresariales:

- Diferencia entre red interna y DMZ.
- Publicación segura de servicios.
- NAT como mecanismo de traducción de direcciones.
- PAT para usuarios internos.
- ACL como mecanismo de filtrado.
- Importancia del orden de evaluación de ACLs.
- Troubleshooting siguiendo capas del modelo OSI.

---

# Archivo Packet Tracer

[Descargar proyecto (.pkt)](Enterprise-DMZ-Firewall-Lab.pkt)


