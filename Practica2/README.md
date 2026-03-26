# Práctica 2: La Facultad de Ingeniería y su Problema de Conectividad en la Biblioteca

**Universidad San Carlos de Guatemala**
**Facultad de Ingeniería**
**Ingeniería en Ciencias y Sistemas**
**Curso: Redes de Computadoras 2**

## Datos del Grupo

| Campo            | Detalle    |
| ---------------- | ---------- |
| Número de Grupo  | 28         |
| Fecha de Entrega | 27/03/2026 |

## Integrantes

| No. | Nombre Completo                  | Carné       |
| --- | -------------------------------- | ----------- |
| 1   | _David Estuardo Barrios Ramírez_ | _202300670_ |
| 2   | _Marcos Daniel Bonifasi de Leon_ | _202202410_ |
| 3   | _Enner Esaí Mendizabal Castro_   | _202302220_ |

---

## Resumen de Cambios v8

> **⚠️ CAMBIOS EN ESTA VERSIÓN:** Correcciones menores de configuración OSPF, VLANs fantasma en MS2, `ip helper-address` innecesarios en servidores y registro DNS NS redundante.

| # | Error | Corrección |
|---|-------|------------|
| 1 | MS0 y MS3 no tenían `passive-interface` en OSPF para sus puertos Gi0/1 y Gi0/2 (conectados a WRT300Ns). OSPF enviaba hellos hacia los routers inalámbricos sin respuesta. | **Agregado `passive-interface GigabitEthernet0/1` y `passive-interface GigabitEthernet0/2`** en secciones 10.8 y 10.9. |
| 2 | MS2 tenía `vlan 31` y `vlan 41` definidas, pero todos sus puertos son routed ports (`no switchport`). Esas VLANs no tienen ningún puerto asignado y generan confusión en `show vlan brief`. | **Eliminadas `vlan 31` y `vlan 41` de la configuración de MS2** (sección 6.2). |
| 3 | R2 y R3 tenían `ip helper-address 192.198.100.130` en las subinterfaces `.31` y `.41`. Los servidores Web y DHCP tienen IPs estáticas y nunca solicitan DHCP. Configuración muerta que puede confundir durante la calificación. | **Eliminados los `ip helper-address`** de las subinterfaces `.31` y `.41` de R2 y R3 (secciones 7.4 y 7.5). |
| 4 | Registro DNS tipo NS (`practica2_Grupo1.com NS www.practica2_Grupo1.com`) es técnicamente incorrecto: un NS record debe apuntar a un servidor DNS, no a un servidor web. En Packet Tracer puede causar comportamiento inesperado. | **Eliminado el registro NS**. Se conservan únicamente los dos registros tipo A (sección 9.1). |
| 5 | El orden de configuración en el documento no advertía que LACP debe aplicarse antes que OSPF. Si se configura OSPF primero, los `network` statements que referencian Po1/Po2/Po6/Po7/Po8 no encontrarán interfaces activas. | **Agregada nota de orden de implementación** en sección 11 (LACP). |

---

## Resumen de Cambios v7

> **⚠️ CAMBIOS CRÍTICOS EN ESTA VERSIÓN:** Corrección del error arquitectónico principal: MS0 y MS3 ya NO usan SVI VLAN 11. En su lugar utilizan puertos enrutados (routed ports) con subredes /30 propias del espacio 10.2.1.x para conectarse a los WRT300Ns. Esto elimina la superposición de subredes en OSPF y el problema de gateway inalcanzable en los routers inalámbricos.

| # | Error | Corrección |
|---|-------|------------|
| 1 | MS0/MS3 tenían SVI Vlan11 en 192.198.11.128/27 → misma subred que R0/R1 en Switch0 (Piso 1). OSPF anunciaba el mismo prefijo desde 4 dispositivos distintos. MS4 no sabía a cuál reenviar. | **Eliminadas SVIs VLAN 11 de MS0 y MS3.** Se reemplazan por routed ports en subredes /30 independientes (10.2.1.28/30, .32/30, .36/30, .40/30). |
| 2 | WRT300N apuntaba gateway a 192.198.11.129 (HSRP VIP de Piso 1), pero el único vecino L2 del WRT300N era MS0. MS0 intentaba ARP por .129 en su propio segmento VLAN 11, sin éxito. El WRT300N nunca podía llegar a su gateway. | **WRT300N ahora apunta su gateway directamente al routed port de MS0/MS3** (p.ej. 10.2.1.29). La conectividad es directa y funcional. |
| 3 | Pool ADMIN tenía Start IP en .138, reservando .132-.137 para SVIs y WRT300Ns que ya no existen. Espacio desperdiciado. | **Pool ADMIN inicia ahora en .132**, aprovechando las IPs liberadas. |
| 4 | Tabla de enrutamiento 10.2.1.x en sección 5.6 no incluía los enlaces MS0/MS3 → WRT300Ns. | **Agregados 4 nuevos enlaces /30** en la tabla de subnetting de enrutamiento. |
| 5 | Tabla OSPF 10.1 y configuraciones OSPF de MS0/MS3 anunciaban 192.198.11.128/27 (red que ya no les pertenece). | **Actualizados** para anunciar únicamente sus nuevas subredes /30. |
| 6 | Router3 no tenía `standby priority` explícita para VLAN 31 ni VLAN 41 en la documentación. | **Agregadas prioridades explícitas** (100) para completar la documentación. |
| 7 | Sección 15 Nota 2 describía la arquitectura antigua con SVIs. | **Actualizada** para reflejar los routed ports. |
| 8 | Sección 8.3 describía Access Port VLAN 11 en MS0/MS3. | **Actualizada** para reflejar routed ports con /30. |

---

## Resumen de Cambios v6

> **⚠️ CAMBIOS EN ESTA VERSIÓN:** Corrección de textos descriptivos con `/28` incorrecto → `/27`, y corrección de la tabla OSPF donde MS4 listaba subredes de Piso 1 que no le corresponden.

| # | Error | Corrección |
|---|-------|------------|
| 1 | Sección 8.2 encabezado: `192.198.11.128/28` | Corregido a `/27` |
| 2 | Tabla OSPF 10.1 — fila MS4 listaba `192.198.11.0/25, 192.198.11.128/28` | Eliminadas (MS4 no tiene interfaces ahí; las anuncia R0/R1) |
| 3 | Tabla OSPF 10.1 — Router1, Router0, MS0, MS3 mostraban `/28` | Corregido a `/27` en todos |
| 4 | Sección 15 Nota 3: `192.198.11.128/28` | Corregido a `/27` |
| 5 | Sección 15 Nota 5: `192.198.11.128/28` | Corregido a `/27` |
| 6 | Nota debajo de tabla OSPF (10.1): `192.198.11.128/28` | Corregido a `/27` |

---

## Resumen de Cambios v5

> **⚠️ CAMBIOS CRÍTICOS EN ESTA VERSIÓN:** Esta versión corrige conflictos de IPs dentro del pool DHCP de ADMIN y errores menores en OSPF y HSRP.

| # | Error | Corrección |
|---|-------|------------|
| 1 | VLAN 21 solo 59 hosts DHCP con /26 | **Cambiado a /25** → 123 hosts DHCP |
| 2 | VLAN 11 ADMIN en /28 tenía solo 4 IPs libres para hosts | **Cambiado a /27** → 21+ IPs DHCP libres |
| 3 | SVIs MS0/MS3 (.133/.134) y WRT300Ns (.135-.138) estaban dentro del pool DHCP → conflictos | **Corregido en v7** con arquitectura de routed ports |
| 4 | WRT300N WAN IP en subred incorrecta | **Corregido en v7**: IPs en subredes /30 independientes |
| 5 | MS0/MS3 sin SVIs para DHCP relay | **Reemplazado en v7** por routed ports directos |
| 6 | DNS no configurado en WRT300N | **AGREGADO: 192.198.100.2** |
| 7 | MS4 OSPF anunciaba subredes 192.198.11.x sin interfaces directas ahí | **Eliminados** |
| 8 | OSPF verification mostraba IPs de interfaz como Neighbor ID | **Corregido a 33.33.33.33 y 34.34.34.34** |
| 9 | MS2 tenía VLANs 11 y 21 definidas sin uso en el Data Center | **Eliminadas** |
| 10 | Router3 sin priority explícita en HSRP VLAN 41 | **Corregido en v7** con prioridades explícitas |

---

## 1. Introducción

La Facultad de Ingeniería de la Universidad de San Carlos de Guatemala (USAC) atraviesa un proceso de modernización de su infraestructura tecnológica, impulsado por el crecimiento continuo de su población estudiantil y la creciente demanda de servicios digitales dentro del campus. En este contexto, el edificio de la biblioteca representa un área crítica que requiere atención inmediata.

La presente práctica documenta el diseño, configuración e implementación de una nueva topología de red para el edificio de la biblioteca de ingeniería, desarrollada en el simulador **Cisco Packet Tracer**.

> **Nota sobre el número de grupo:** Según el manual de práctica, para grupos de 2 dígitos se debe realizar la suma de ambos hasta obtener un solo dígito. Para el Grupo 28: 2 + 8 = 10 → 1 + 0 = **X = 1**. Por lo tanto, todas las referencias a "GX" en este documento corresponden a **G1**.

## 2. Objetivos

### 2.1 Objetivo General

Diseñar e implementar una infraestructura de red completa para el edificio de la biblioteca de la Facultad de Ingeniería de la USAC, garantizando alta disponibilidad, seguridad y escalabilidad.

### 2.2 Objetivos Específicos

- Configurar la red cableada del primer piso con VLANs diferenciadas para administración y estudiantes.
- Implementar zonas de acceso inalámbrico seguras en el segundo y tercer piso mediante routers inalámbricos con WPA2 Personal.
- Configurar un servidor DHCP centralizado en el Data Center.
- Implementar un servidor Web con servicios DNS y HTTP.
- Configurar el protocolo de enrutamiento dinámico OSPF.
- Establecer enlaces LACP de 4 interfaces entre cada par de edificios.
- Configurar HSRP en el primer piso y en el Data Center para redundancia de gateway.

## 3. Descripción del Problema

La biblioteca de la Facultad de Ingeniería de la USAC presenta deficiencias críticas en su infraestructura de red:

- Falta de cobertura inalámbrica en los pisos superiores.
- Ausencia de redundancia en los dispositivos de enrutamiento.
- Segmentación inadecuada de la red.
- Servicios centralizados inexistentes.
- Ancho de banda insuficiente entre edificios.

## 4. Topología Propuesta

```
                    ┌─────────────────────────────────────────────────────┐
                    │                    DATA CENTER                       │
                    │   ┌──────────┐  ┌──────────┐                       │
                    │   │ ServerWeb│  │ServerDHCP│                       │
                    │   │(VLAN 31) │  │(VLAN 41) │                       │
                    │   └────┬─────┘  └────┬─────┘                       │
                    │          │             │                              │
                    │   ┌─────┴─────────────┴─────┐                       │
                    │   │        Switch3           │                       │
                    │   │    (VLANs 31, 41)        │                       │
                    │   └───────────┬──────────────┘                       │
                    │               │  Trunk                               │
                    │   ┌───────────┴──────────────┐                      │
                    │   │  R2 (Gi0/1.31, Gi0/1.41) │◄─ HSRP Activo       │
                    │   │  R3 (Gi0/1.31, Gi0/1.41) │◄─ HSRP Standby      │
                    │   └───────┬──────────┬────────┘                      │
                    │           │Gi0/0     │Gi0/0                          │
                    │   ┌───────┴──────────┴────────┐                      │
                    │   │       MS2 (L3)             │                      │
                    │   │  Po1 (10.2.1.25/26 ↔ MS4) │                      │
                    └───┴───────────────────────────┴──────────────────────┘
                                        │ LACP Po1/Po8 (4 puertos Fa0/21-24)
                    ┌───────────────────┴────────────────────────────────┐
                    │                 MS4 (L3 Core — Piso 1)             │
                    │  Gi0/1─────────────────────────────Gi0/2           │
                    │ (10.2.1.9/30)                   (10.2.1.13/30)     │
                    │     │                                   │           │
                    │  Router1 (HSRP Activo V11)      Router0 (HSRP Act V21) │
                    │     │ Gi0/1 → Switch0             │ Gi0/1 → Switch0 │
                    │     └──────────┬──────────────────┘                 │
                    │                │                                     │
                    │          Switch0 (VLANs 11, 21)                     │
                    │          PC1(V21) / Laptop1(V11)                    │
                    │                                                      │
                    │  Po6 (10.2.1.17/30)     Po7 (10.2.1.21/30)         │
                    └──────────┬─────────────────────┬────────────────────┘
                               │ LACP (Fa0/17-20)    │ LACP (Fa0/13-16)
                    ┌──────────┴────────┐   ┌─────────┴──────────┐
                    │   MS0 (L3-Piso 2) │   │   MS3 (L3-Piso 3)  │
                    │ Po2: 10.2.1.18/30 │   │ Po3: 10.2.1.22/30  │
                    │ Gi0/1: 10.2.1.29  │   │ Gi0/1: 10.2.1.37   │
                    │ Gi0/2: 10.2.1.33  │   │ Gi0/2: 10.2.1.41   │
                    └────┬─────────┬────┘   └───┬────────────┬───┘
                         │         │             │            │
                   WRT-P2R1   WRT-P2R2     WRT-P3R1    WRT-P3R2
                  WAN:.30/30  WAN:.34/30  WAN:.38/30  WAN:.42/30
                  GW:.29      GW:.33      GW:.37      GW:.41
                  WLAN1-P2    WLAN2-P2    WLAN1-P3    WLAN2-P3
               192.198.21.0/25 192.198.21.128/25 192.198.31.0/25 192.198.31.128/25
```

## 5. Subnetting

### 5.1 Resumen de Redes Asignadas (X = 1)

> **Cálculo de X:** Para el Grupo 28: 2 + 8 = 10 → 1 + 0 = **X = 1**

| Red          | Dirección Base   | Uso                                |
| ------------ | ---------------- | ---------------------------------- |
| Piso 1       | 192.198.11.0/24 | Red cableada (ADMIN + ESTUDIANTES) |
| Piso 2       | 192.198.21.0/24 | Red inalámbrica (WLAN1 + WLAN2)    |
| Piso 3       | 192.198.31.0/24 | Red inalámbrica (WLAN1 + WLAN2)    |
| Data Center  | 192.198.100.0/24 | Servidores (WEB + DHCP)            |
| Enrutamiento | 10.2.1.0/24     | Enlaces entre routers y switches   |

### 5.2 Piso 1 — VLSM (192.198.11.0/24)

> Se aplica VLSM para aprovechar el espacio: primero la subred más grande (ESTUDIANTES/60 hosts), luego ADMIN (10 hosts).

| VLAN | Nombre      | Hosts Requeridos | Subred Asignada    | Máscara         | Rango Usable                     | Broadcast       |
| ---- | ----------- | ---------------- | ------------------ | --------------- | -------------------------------- | --------------- |
| 21   | ESTUDIANTES | 60               | 192.198.11.0/25   | 255.255.255.128 | 192.198.11.1 – 192.198.11.126   | 192.198.11.127  |
| 11   | ADMIN       | 10               | 192.198.11.128/27 | 255.255.255.224 | 192.198.11.129 – 192.198.11.158 | 192.198.11.159  |

> **Distribución de IPs Piso 1:**
> - VLAN 21: .1(HSRP), .2(R0), .3(R1) reservadas | **.4 - .126 → DHCP (123 hosts)**
> - VLAN 11: .129(HSRP), .130(R1), .131(R0) reservadas | **.132 - .158 → DHCP (27 hosts)**
>
> Con la arquitectura v7, las IPs .132-.137 ya no se reservan para infraestructura (MS0/MS3 usan el espacio 10.2.1.x). El pool DHCP ADMIN puede arrancar directamente en .132.

### 5.3 Piso 2 — FLSM (192.198.21.0/24)

| WLAN  | Hosts | Subred Asignada    | Máscara         | Rango DHCP              |
| ----- | ----- | ------------------ | --------------- | ------------------------ |
| WLAN1 | 80    | 192.198.21.0/25   | 255.255.255.128 | .2 - .126 (125 hosts)   |
| WLAN2 | 80    | 192.198.21.128/25 | 255.255.255.128 | .130 - .254 (125 hosts) |

### 5.4 Piso 3 — FLSM (192.198.31.0/24)

| WLAN  | Hosts | Subred Asignada    | Máscara         | Rango DHCP              |
| ----- | ----- | ------------------ | --------------- | ------------------------ |
| WLAN1 | 80    | 192.198.31.0/25   | 255.255.255.128 | .2 - .126 (125 hosts)   |
| WLAN2 | 80    | 192.198.31.128/25 | 255.255.255.128 | .130 - .254 (125 hosts) |

### 5.5 Data Center — FLSM (192.198.100.0/24)

> La red 192.198.100.0/24 se divide en 2 partes iguales (/25) para WEB_SERVERS y DHCP_SERVERS.

| VLAN | Nombre       | Subred Asignada    | Máscara         | IPs Reservadas                             |
| ---- | ------------ | ------------------ | --------------- | ------------------------------------------ |
| 31   | WEB_SERVERS  | 192.198.100.0/25  | 255.255.255.128 | .1(HSRP), .2(ServerWeb), .3(R2), .4(R3)  |
| 41   | DHCP_SERVERS | 192.198.100.128/25 | 255.255.255.128 | .129(HSRP), .130(ServerDHCP), .131(R2), .132(R3) |

### 5.6 Red de Enrutamiento — 10.2.1.0/24 (VLSM con /30)

| Enlace                                   | Subred         | IP A              | IP B              |
| ---------------------------------------- | -------------- | ----------------- | ----------------- |
| R2 ↔ MS2                                | 10.2.1.0/30   | .1 (R2)          | .2 (MS2)         |
| R3 ↔ MS2                                | 10.2.1.4/30   | .5 (R3)          | .6 (MS2)         |
| MS4 ↔ R1                                | 10.2.1.8/30   | .9 (MS4)         | .10 (R1)         |
| MS4 ↔ R0                                | 10.2.1.12/30  | .13 (MS4)        | .14 (R0)         |
| MS4 ↔ MS0 (Piso 2)                      | 10.2.1.16/30  | .17 (MS4)        | .18 (MS0)        |
| MS4 ↔ MS3 (Piso 3)                      | 10.2.1.20/30  | .21 (MS4)        | .22 (MS3)        |
| MS4 ↔ MS2 (Data Center)                 | 10.2.1.24/30  | .25 (MS4)        | .26 (MS2)        |
| **MS0 ↔ WRT300N Piso2-R1 (WLAN1)**     | **10.2.1.28/30** | **.29 (MS0)**  | **.30 (WRT-P2R1)** |
| **MS0 ↔ WRT300N Piso2-R2 (WLAN2)**     | **10.2.1.32/30** | **.33 (MS0)**  | **.34 (WRT-P2R2)** |
| **MS3 ↔ WRT300N Piso3-R1 (WLAN1)**     | **10.2.1.36/30** | **.37 (MS3)**  | **.38 (WRT-P3R1)** |
| **MS3 ↔ WRT300N Piso3-R2 (WLAN2)**     | **10.2.1.40/30** | **.41 (MS3)**  | **.42 (WRT-P3R2)** |

> **Por qué subredes separadas por WRT300N:** Cada WRT300N necesita un gateway propio y único. Usar un gateway en 192.198.11.128/27 (la subred de Piso 1) causaba que los WRT300Ns apuntaran a la HSRP VIP (.129) pero esa IP sólo se anuncia en el segmento L2 de Switch0 (Piso 1). MS0/MS3 están en un segmento L2 completamente diferente. El ARP para .129 nunca se respondía desde MS0, dejando a los WRT300Ns sin conectividad con su gateway.

## 6. Configuración de VLANs

| VLAN ID | Nombre       | Segmento                    |
| ------- | ------------ | --------------------------- |
| 11      | ADMIN        | Administración — Piso 1     |
| 21      | ESTUDIANTES  | Estudiantes — Piso 1        |
| 31      | WEB_SERVERS  | Servidor Web — Data Center  |
| 41      | DHCP_SERVERS | Servidor DHCP — Data Center |

### 6.1 Switch3 (Data Center)

```bash
enable
configure terminal

vlan 31
name WEB_SERVERS
exit

vlan 41
name DHCP_SERVERS
exit

# ServerWeb
interface FastEthernet0/24
switchport mode access
switchport access vlan 31
no shutdown
exit

# ServerDHCP
interface FastEthernet0/23
switchport mode access
switchport access vlan 41
no shutdown
exit

# Trunk hacia R2
interface GigabitEthernet0/1
switchport mode trunk
switchport trunk allowed vlan 31,41
no shutdown
exit

# Trunk hacia R3
interface GigabitEthernet0/2
switchport mode trunk
switchport trunk allowed vlan 31,41
no shutdown
exit

end
write memory
```

### 6.2 MS2 (Data Center)

```bash
enable
configure terminal

ip routing

# Routed ports hacia routers
interface GigabitEthernet0/1
no switchport
ip address 10.2.1.2 255.255.255.252
no shutdown
exit

interface GigabitEthernet0/2
no switchport
ip address 10.2.1.6 255.255.255.252
no shutdown
exit

end
write memory
```

### 6.3 Switch0 (Piso 1)

```bash
enable
configure terminal

vlan 11
name ADMIN
exit

vlan 21
name ESTUDIANTES
exit

# Laptop1 - VLAN 11 ADMIN
interface FastEthernet0/23
switchport mode access
switchport access vlan 11
spanning-tree portfast
no shutdown
exit

# PC1 - VLAN 21 ESTUDIANTES
interface FastEthernet0/24
switchport mode access
switchport access vlan 21
spanning-tree portfast
no shutdown
exit

# Trunk hacia Router1
interface GigabitEthernet0/1
switchport mode trunk
switchport trunk allowed vlan 11,21
no shutdown
exit

# Trunk hacia Router0
interface GigabitEthernet0/2
switchport mode trunk
switchport trunk allowed vlan 11,21
no shutdown
exit

end
write memory
```

### 6.4 MS4 (Piso 1 - Core)

```bash
enable
configure terminal

ip routing

vlan 11
name ADMIN
exit

vlan 21
name ESTUDIANTES
exit

vlan 31
name WEB_SERVERS
exit

vlan 41
name DHCP_SERVERS
exit

# Routed ports hacia routers de Piso 1
interface GigabitEthernet0/1
no switchport
ip address 10.2.1.9 255.255.255.252
no shutdown
exit

interface GigabitEthernet0/2
no switchport
ip address 10.2.1.13 255.255.255.252
no shutdown
exit

end
write memory
```

### 6.5 MS0 (Piso 2) — ARQUITECTURA v7 CORREGIDA

> **⚠️ CAMBIO ARQUITECTÓNICO v7:** MS0 ya NO tiene SVI VLAN 11 ni puertos access en VLAN 11. En su lugar, usa **routed ports** (no switchport) directamente conectados al puerto WAN de cada WRT300N. Esto elimina el conflicto de subred 192.198.11.128/27 en OSPF y permite que cada WRT300N tenga un gateway real y alcanzable.

```bash
enable
configure terminal

ip routing

# Routed port hacia WRT300N Piso 2 - WLAN1
interface GigabitEthernet0/1
no switchport
ip address 10.2.1.29 255.255.255.252
no shutdown
exit

# Routed port hacia WRT300N Piso 2 - WLAN2
interface GigabitEthernet0/2
no switchport
ip address 10.2.1.33 255.255.255.252
no shutdown
exit

# LACP hacia MS4 (Piso 1) - LAYER 3
interface range FastEthernet0/21 - 24
no switchport
channel-group 2 mode passive
no shutdown
exit

interface Port-channel2
ip address 10.2.1.18 255.255.255.252
no shutdown
exit

end
write memory
```

### 6.6 MS3 (Piso 3) — ARQUITECTURA v7 CORREGIDA

> **⚠️ CAMBIO ARQUITECTÓNICO v7:** Igual que MS0. Ya NO tiene SVI VLAN 11. Usa routed ports /30 independientes para cada WRT300N.

```bash
enable
configure terminal

ip routing

# Routed port hacia WRT300N Piso 3 - WLAN1
interface GigabitEthernet0/1
no switchport
ip address 10.2.1.37 255.255.255.252
no shutdown
exit

# Routed port hacia WRT300N Piso 3 - WLAN2
interface GigabitEthernet0/2
no switchport
ip address 10.2.1.41 255.255.255.252
no shutdown
exit

# LACP hacia MS4 (Piso 1) - LAYER 3
interface range FastEthernet0/21 - 24
no switchport
channel-group 3 mode passive
no shutdown
exit

interface Port-channel3
ip address 10.2.1.22 255.255.255.252
no shutdown
exit

end
write memory
```

## 7. Configuración DHCP

### 7.1 ServerWeb (IP Estática)

| Parámetro       | Valor             |
| --------------- | ----------------- |
| IP Address      | `192.198.100.2`   |
| Subnet Mask     | `255.255.255.128` |
| Default Gateway | `192.198.100.1`   |
| DNS Server      | `127.0.0.1`       |

### 7.2 ServerDHCP (IP Estática)

| Parámetro       | Valor             |
| --------------- | ----------------- |
| IP Address      | `192.198.100.130` |
| Subnet Mask     | `255.255.255.128` |
| Default Gateway | `192.198.100.129` |
| DNS Server      | `192.198.100.2`   |

### 7.3 Pools DHCP en ServerDHCP

> **IMPORTANTE:** Las Start IP NO pueden coincidir con IPs de gateways HSRP ni de routers.

**Pool 1 — VLAN 11 (ADMIN)**

| Parámetro        | Valor              |
| ---------------- | ------------------ |
| Pool Name        | `POOL_ADMIN`       |
| Default Gateway  | `192.198.11.129`   |
| DNS Server       | `192.198.100.2`    |
| Start IP Address | `192.198.11.132`   |
| Subnet Mask      | `255.255.255.224`  |
| Max Users        | `27`               |

> **✅ v7:** Pool ADMIN libre de conflictos. IPs .129(HSRP), .130(R1), .131(R0) reservadas. Pool inicia en .132 y llega hasta .158 (27 hosts disponibles, más que suficiente para los 10 de administración requeridos).

**Pool 2 — VLAN 21 (ESTUDIANTES)**

| Parámetro        | Valor               |
| ---------------- | ------------------- |
| Pool Name        | `POOL_ESTUDIANTES`  |
| Default Gateway  | `192.198.11.1`      |
| DNS Server       | `192.198.100.2`     |
| Start IP Address | `192.198.11.4`      |
| Subnet Mask      | `255.255.255.128`   |
| Max Users        | `123`               |

> **✅ Soporta 60 hosts:** /25 tiene 126 usables. Reservando .1(HSRP), .2(R0), .3(R1), quedan **123 IPs DHCP**.

**Pool 3 — VLAN 31 (WEB_SERVERS)**

| Parámetro        | Valor             |
| ---------------- | ----------------- |
| Pool Name        | `POOL_WEB_SERVERS` |
| Default Gateway  | `192.198.100.1`   |
| DNS Server       | `192.198.100.2`   |
| Start IP Address | `192.198.100.5`   |
| Subnet Mask      | `255.255.255.128` |
| Max Users        | `121`             |

> .1(HSRP), .2(ServerWeb), .3(R2), .4(R3) reservadas. Pool inicia en .5.

**Pool 4 — VLAN 41 (DHCP_SERVERS)**

| Parámetro        | Valor               |
| ---------------- | ------------------- |
| Pool Name        | `POOL_DHCP_SERVERS` |
| Default Gateway  | `192.198.100.129`  |
| DNS Server       | `192.198.100.2`    |
| Start IP Address | `192.198.100.133`  |
| Subnet Mask      | `255.255.255.128`  |
| Max Users        | `121`              |

> .129(HSRP), .130(ServerDHCP), .131(R2), .132(R3) reservadas. Pool inicia en .133.

### 7.4 Router2 (Data Center - HSRP Activo)

```bash
enable
configure terminal

# Hacia MS2
interface GigabitEthernet0/0
ip address 10.2.1.1 255.255.255.252
no shutdown
exit

# Hacia Switch3
interface GigabitEthernet0/1
no shutdown
exit

# VLAN 31 - WEB_SERVERS (HSRP ACTIVO)
interface GigabitEthernet0/1.31
encapsulation dot1Q 31
ip address 192.198.100.3 255.255.255.128
standby 31 ip 192.198.100.1
standby 31 priority 110
standby 31 preempt
no shutdown
exit

# VLAN 41 - DHCP_SERVERS (HSRP ACTIVO)
interface GigabitEthernet0/1.41
encapsulation dot1Q 41
ip address 192.198.100.131 255.255.255.128
standby 41 ip 192.198.100.129
standby 41 priority 110
standby 41 preempt
no shutdown
exit

end
write memory
```

### 7.5 Router3 (Data Center - HSRP Standby)

```bash
enable
configure terminal

# Hacia MS2
interface GigabitEthernet0/0
ip address 10.2.1.5 255.255.255.252
no shutdown
exit

# Hacia Switch3
interface GigabitEthernet0/1
no shutdown
exit

# VLAN 31 - WEB_SERVERS (HSRP STANDBY)
interface GigabitEthernet0/1.31
encapsulation dot1Q 31
ip address 192.198.100.4 255.255.255.128
standby 31 ip 192.198.100.1
standby 31 priority 100
no shutdown
exit

# VLAN 41 - DHCP_SERVERS (HSRP STANDBY)
interface GigabitEthernet0/1.41
encapsulation dot1Q 41
ip address 192.198.100.132 255.255.255.128
standby 41 ip 192.198.100.129
standby 41 priority 100
no shutdown
exit

end
write memory
```

### 7.6 Router1 (Piso 1 - HSRP Activo VLAN 11, Standby VLAN 21)

```bash
enable
configure terminal
no ip domain-lookup

# Hacia MS4
interface GigabitEthernet0/0
ip address 10.2.1.10 255.255.255.252
no shutdown
exit

# Hacia Switch0
interface GigabitEthernet0/1
no shutdown
exit

# VLAN 11 - ADMIN (HSRP ACTIVO)
interface GigabitEthernet0/1.11
encapsulation dot1Q 11
ip address 192.198.11.130 255.255.255.224
ip helper-address 192.198.100.130
standby 11 ip 192.198.11.129
standby 11 priority 110
standby 11 preempt
no shutdown
exit

# VLAN 21 - ESTUDIANTES (HSRP STANDBY)
interface GigabitEthernet0/1.21
encapsulation dot1Q 21
ip address 192.198.11.3 255.255.255.128
ip helper-address 192.198.100.130
standby 21 ip 192.198.11.1
standby 21 priority 90
no shutdown
exit

end
write memory
```

### 7.7 Router0 (Piso 1 - HSRP Activo VLAN 21, Standby VLAN 11)

```bash
enable
configure terminal
no ip domain-lookup

# Hacia MS4
interface GigabitEthernet0/0
ip address 10.2.1.14 255.255.255.252
no shutdown
exit

# Hacia Switch0
interface GigabitEthernet0/1
no shutdown
exit

# VLAN 11 - ADMIN (HSRP STANDBY)
interface GigabitEthernet0/1.11
encapsulation dot1Q 11
ip address 192.198.11.131 255.255.255.224
ip helper-address 192.198.100.130
standby 11 ip 192.198.11.129
standby 11 priority 90
no shutdown
exit

# VLAN 21 - ESTUDIANTES (HSRP ACTIVO)
interface GigabitEthernet0/1.21
encapsulation dot1Q 21
ip address 192.198.11.2 255.255.255.128
ip helper-address 192.198.100.130
standby 21 ip 192.198.11.1
standby 21 priority 110
standby 21 preempt
no shutdown
exit

end
write memory
```

## 8. Configuración Inalámbrica

### 8.1 Parámetros WiFi

| Parámetro             | Piso 2              | Piso 3              |
| --------------------- | ------------------- | ------------------- |
| SSID (Router 1)       | PISO2_G1_R1        | PISO3_G1_R1        |
| SSID (Router 2)       | PISO2_G1_R2        | PISO3_G1_R3        |
| Broadcast SSID        | Deshabilitado (No) | Habilitado (Sí)    |
| Seguridad             | WPA2 Personal       | WPA2 Personal       |
| Contraseña de Red     | G1_PISO2           | G1_PISO3           |
| Contraseña del Router | Grupo1_P2          | Grupo1_P3           |

### 8.2 Configuración WRT300N (GUI de Packet Tracer) — CORREGIDA v7

> **⚠️ CAMBIO v7:** Los puertos WAN ahora usan subredes /30 del espacio 10.2.1.x. El gateway apunta directamente al routed port del MS0/MS3 correspondiente. Ya NO se usa 192.198.11.128/27 en los WRT300Ns.

#### Wireless Router0 (Piso 2 - WLAN1)

| Sección | Parámetro | Valor |
|---------|-----------|-------|
| **Internet (WAN)** | Connection Type | Static IP |
| **Internet** | IP Address | `10.2.1.30` |
| **Internet** | Subnet Mask | `255.255.255.252` |
| **Internet** | Default Gateway | `10.2.1.29` |
| **Internet** | DNS Server | `192.198.100.2` |
| **LAN Settings** | IP Address | `192.198.21.1` |
| **LAN Settings** | Subnet Mask | `255.255.255.128` |
| **DHCP** | Enable | On |
| **DHCP** | Start IP | `192.198.21.2` |
| **DHCP** | Max Users | `80` |
| **DHCP** | DNS Server | `192.198.100.2` |
| **Wireless** | SSID | `PISO2_G1_R1` |
| **Wireless** | Broadcast | Disabled |
| **Wireless Security** | Mode | WPA2 Personal |
| **Wireless Security** | Passphrase | `G1_PISO2` |

#### Wireless Router1 (Piso 2 - WLAN2)

| Sección | Parámetro | Valor |
|---------|-----------|-------|
| **Internet (WAN)** | Connection Type | Static IP |
| **Internet** | IP Address | `10.2.1.34` |
| **Internet** | Subnet Mask | `255.255.255.252` |
| **Internet** | Default Gateway | `10.2.1.33` |
| **Internet** | DNS Server | `192.198.100.2` |
| **LAN Settings** | IP Address | `192.198.21.129` |
| **LAN Settings** | Subnet Mask | `255.255.255.128` |
| **DHCP** | Enable | On |
| **DHCP** | Start IP | `192.198.21.130` |
| **DHCP** | Max Users | `80` |
| **DHCP** | DNS Server | `192.198.100.2` |
| **Wireless** | SSID | `PISO2_G1_R2` |
| **Wireless** | Broadcast | Disabled |
| **Wireless Security** | Mode | WPA2 Personal |
| **Wireless Security** | Passphrase | `G1_PISO2` |

#### Wireless Router2 (Piso 3 - WLAN1)

| Sección | Parámetro | Valor |
|---------|-----------|-------|
| **Internet (WAN)** | Connection Type | Static IP |
| **Internet** | IP Address | `10.2.1.38` |
| **Internet** | Subnet Mask | `255.255.255.252` |
| **Internet** | Default Gateway | `10.2.1.37` |
| **Internet** | DNS Server | `192.198.100.2` |
| **LAN Settings** | IP Address | `192.198.31.1` |
| **LAN Settings** | Subnet Mask | `255.255.255.128` |
| **DHCP** | Enable | On |
| **DHCP** | Start IP | `192.198.31.2` |
| **DHCP** | Max Users | `80` |
| **DHCP** | DNS Server | `192.198.100.2` |
| **Wireless** | SSID | `PISO3_G1_R1` |
| **Wireless** | Broadcast | Enabled |
| **Wireless Security** | Mode | WPA2 Personal |
| **Wireless Security** | Passphrase | `G1_PISO3` |

#### Wireless Router3 (Piso 3 - WLAN2)

| Sección | Parámetro | Valor |
|---------|-----------|-------|
| **Internet (WAN)** | Connection Type | Static IP |
| **Internet** | IP Address | `10.2.1.42` |
| **Internet** | Subnet Mask | `255.255.255.252` |
| **Internet** | Default Gateway | `10.2.1.41` |
| **Internet** | DNS Server | `192.198.100.2` |
| **LAN Settings** | IP Address | `192.198.31.129` |
| **LAN Settings** | Subnet Mask | `255.255.255.128` |
| **DHCP** | Enable | On |
| **DHCP** | Start IP | `192.198.31.130` |
| **DHCP** | Max Users | `80` |
| **DHCP** | DNS Server | `192.198.100.2` |
| **Wireless** | SSID | `PISO3_G1_R3` |
| **Wireless** | Broadcast | Enabled |
| **Wireless Security** | Mode | WPA2 Personal |
| **Wireless Security** | Passphrase | `G1_PISO3` |

### 8.3 Conexión Física MS0/MS3 → WRT300N — CORREGIDA v7

> **CRÍTICO v7:** Los puertos de MS0/MS3 hacia los WRT300Ns son ahora **routed ports** (no switchport), cada uno en su propia subred /30. Conectar el cable desde el routed port del MS a Puerto **Internet** (WAN) del WRT300N. NO usar Access Port ni VLAN 11.

**En MS0 (Piso 2):**
```bash
# Puerto hacia WRT300N WLAN1 - ROUTED PORT
interface GigabitEthernet0/1
no switchport
ip address 10.2.1.29 255.255.255.252
no shutdown
exit

# Puerto hacia WRT300N WLAN2 - ROUTED PORT
interface GigabitEthernet0/2
no switchport
ip address 10.2.1.33 255.255.255.252
no shutdown
exit
```

**En MS3 (Piso 3):**
```bash
# Puerto hacia WRT300N WLAN1 - ROUTED PORT
interface GigabitEthernet0/1
no switchport
ip address 10.2.1.37 255.255.255.252
no shutdown
exit

# Puerto hacia WRT300N WLAN2 - ROUTED PORT
interface GigabitEthernet0/2
no switchport
ip address 10.2.1.41 255.255.255.252
no shutdown
exit
```

**En cada WRT300N:**
- Conectar cable desde el puerto Gi del MS al puerto **Internet** del WRT300N.
- Configurar WAN como Static IP con los valores de la sección 8.2.
- El gateway del WRT300N es directamente el IP del routed port del MS.

## 9. Configuración DNS y Servidor Web

### 9.1 DNS en ServerWeb

1. ServerWeb → Services → DNS → On
2. Agregar registros:

| Name | Type | Address |
|------|------|---------|
| www.practica2_Grupo1.com | A | 192.198.100.2 |
| practica2_Grupo1.com | A | 192.198.100.2 |

### 9.2 HTTP en ServerWeb

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <title>Practica 2 - Grupo 28</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        background-color: #f0f4f8;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
      }
      .container {
        background-color: white;
        border-radius: 10px;
        padding: 40px;
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        max-width: 600px;
        width: 100%;
      }
      h1 {
        color: #1a3c6e;
        text-align: center;
        border-bottom: 2px solid #1a3c6e;
        padding-bottom: 10px;
      }
      h2 {
        color: #2e5fa3;
        margin-top: 20px;
      }
      table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 10px;
      }
      th {
        background-color: #1a3c6e;
        color: white;
        padding: 10px;
        text-align: left;
      }
      td {
        padding: 8px 10px;
        border-bottom: 1px solid #ddd;
      }
      tr:hover {
        background-color: #f5f5f5;
      }
      .grupo {
        text-align: center;
        font-size: 18px;
        font-weight: bold;
        color: #1a3c6e;
        margin-top: 20px;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h1>Redes de Computadoras 2</h1>
      <h2>Practica 2: Conectividad Biblioteca FIUSAC</h2>
      <h2>Integrantes del Grupo</h2>
      <table>
        <tr>
          <th>No.</th>
          <th>Nombre</th>
          <th>Carne</th>
        </tr>
        <tr>
          <td>1</td>
          <td>David Estuardo Barrios Ramirez</td>
          <td>202300670</td>
        </tr>
        <tr>
          <td>2</td>
          <td>Marcos Daniel Bonifasi de Leon</td>
          <td>202202410</td>
        </tr>
        <tr>
          <td>3</td>
          <td>Enner Esai Mendizabal Castro</td>
          <td>202302220</td>
        </tr>
      </table>
      <p class="grupo">Grupo No. 28</p>
      <p class="grupo">Universidad San Carlos de Guatemala</p>
      <p class="grupo">Facultad de Ingenieria - FIUSAC</p>
    </div>
  </body>
</html>
```

## 10. Configuración OSPF

> **Grupo 28 es par → OSPF** (la suma 2+8=10, 1+0=1; el número de grupo 28 es par).

### 10.1 Tabla de Router IDs — ACTUALIZADA v7

| Dispositivo | Router ID    | Redes OSPF anunciadas                                           |
| ----------- | ------------ | --------------------------------------------------------------- |
| Router2     | 2.2.2.2      | 10.2.1.0/30, 192.198.100.0/25, 192.198.100.128/25             |
| Router3     | 3.3.3.3      | 10.2.1.4/30, 192.198.100.0/25, 192.198.100.128/25             |
| MS2         | 22.22.22.22  | 10.2.1.0/30, 10.2.1.4/30, 10.2.1.24/30                        |
| MS4         | 44.44.44.44  | 10.2.1.8/30, 10.2.1.12/30, 10.2.1.16/30, 10.2.1.20/30, 10.2.1.24/30 |
| Router1     | 1.1.1.1      | 10.2.1.8/30, 192.198.11.0/25, 192.198.11.128/27               |
| Router0     | 5.5.5.5      | 10.2.1.12/30, 192.198.11.0/25, 192.198.11.128/27              |
| MS0         | 33.33.33.33  | 10.2.1.16/30, **10.2.1.28/30, 10.2.1.32/30**                  |
| MS3         | 34.34.34.34  | 10.2.1.20/30, **10.2.1.36/30, 10.2.1.40/30**                  |

> **⚠️ Nota v7:** MS0 y MS3 ya **NO** anuncian 192.198.11.128/27. Ese prefijo únicamente es anunciado por R1 y R0, que son los únicos routers físicamente conectados al segmento L2 de Piso 1. Anunciar 192.198.11.128/27 desde MS0/MS3 (en segmentos L2 distintos) causaba routing ambiguo en MS4 y hacía que el gateway de los WRT300Ns fuera inalcanzable. Con la arquitectura v7, cada segmento /30 es único y no hay superposición.

### 10.2 Router2

```bash
router ospf 1
router-id 2.2.2.2
network 10.2.1.0 0.0.0.3 area 0
network 192.198.100.0 0.0.0.127 area 0
network 192.198.100.128 0.0.0.127 area 0
passive-interface GigabitEthernet0/1.31
passive-interface GigabitEthernet0/1.41
exit
```

### 10.3 Router3

```bash
router ospf 1
router-id 3.3.3.3
network 10.2.1.4 0.0.0.3 area 0
network 192.198.100.0 0.0.0.127 area 0
network 192.198.100.128 0.0.0.127 area 0
passive-interface GigabitEthernet0/1.31
passive-interface GigabitEthernet0/1.41
exit
```

### 10.4 MS2

```bash
router ospf 1
router-id 22.22.22.22
network 10.2.1.0 0.0.0.3 area 0
network 10.2.1.4 0.0.0.3 area 0
network 10.2.1.24 0.0.0.3 area 0
exit
```

### 10.5 MS4

```bash
router ospf 1
router-id 44.44.44.44
network 10.2.1.8 0.0.0.3 area 0
network 10.2.1.12 0.0.0.3 area 0
network 10.2.1.16 0.0.0.3 area 0
network 10.2.1.20 0.0.0.3 area 0
network 10.2.1.24 0.0.0.3 area 0
exit
```

### 10.6 Router1

```bash
router ospf 1
router-id 1.1.1.1
network 10.2.1.8 0.0.0.3 area 0
network 192.198.11.0 0.0.0.127 area 0
network 192.198.11.128 0.0.0.31 area 0
passive-interface GigabitEthernet0/1.11
passive-interface GigabitEthernet0/1.21
exit
```

### 10.7 Router0

```bash
router ospf 1
router-id 5.5.5.5
network 10.2.1.12 0.0.0.3 area 0
network 192.198.11.0 0.0.0.127 area 0
network 192.198.11.128 0.0.0.31 area 0
passive-interface GigabitEthernet0/1.11
passive-interface GigabitEthernet0/1.21
exit
```

### 10.8 MS0 — ACTUALIZADO v7

```bash
router ospf 1
router-id 33.33.33.33
network 10.2.1.16 0.0.0.3 area 0
network 10.2.1.28 0.0.0.3 area 0
network 10.2.1.32 0.0.0.3 area 0
passive-interface GigabitEthernet0/1
passive-interface GigabitEthernet0/2
exit
```

### 10.9 MS3 — ACTUALIZADO v7

```bash
router ospf 1
router-id 34.34.34.34
network 10.2.1.20 0.0.0.3 area 0
network 10.2.1.36 0.0.0.3 area 0
network 10.2.1.40 0.0.0.3 area 0
passive-interface GigabitEthernet0/1
passive-interface GigabitEthernet0/2
exit
```

### 10.10 Verificación OSPF

```bash
show ip ospf neighbor
show ip route ospf
```

**Resultado esperado en MS4:**
```
Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1          1   FULL/DR         00:00:38    10.2.1.10       Gi0/1
5.5.5.5          1   FULL/DR         00:00:36    10.2.1.14       Gi0/2
33.33.33.33      1   FULL/BDR        00:00:35    10.2.1.18       Po6
34.34.34.34      1   FULL/BDR        00:00:37    10.2.1.22       Po7
22.22.22.22      1   FULL/BDR        00:00:33    10.2.1.26       Po8
```

## 11. Configuración LACP

> **⚠️ ORDEN DE IMPLEMENTACIÓN CRÍTICO (v8):** Los Port-channels deben existir y estar activos **antes** de aplicar los comandos OSPF. El orden correcto al configurar desde cero es:
> ```
> 1. VLANs e interfaces básicas
> 2. LACP (crear EtherChannels y asignar IPs a Port-channels)
> 3. OSPF (los network statements encontrarán las interfaces Po activas)
> 4. HSRP
> ```
> Si se aplica OSPF antes que LACP, los `network` statements que referencian Po6, Po7, Po8 (MS4), Po2 (MS0), Po3 (MS3) y Po1 (MS2) no encontrarán interfaces activas.

### 11.1 Resumen EtherChannels

| Port-Channel | Enlace      | MS4 Interfaces | Remoto | IP MS4     | IP Remoto  | Modo MS4 | Modo Remoto |
|--------------|-------------|----------------|--------|------------|------------|----------|-------------|
| Po6          | P1 ↔ P2    | Fa0/17-20      | MS0 Po2 | 10.2.1.17 | 10.2.1.18 | active   | passive     |
| Po7          | P1 ↔ P3    | Fa0/13-16      | MS3 Po3 | 10.2.1.21 | 10.2.1.22 | active   | passive     |
| Po8          | P1 ↔ DC    | Fa0/21-24      | MS2 Po1 | 10.2.1.25 | 10.2.1.26 | active   | passive     |

> **Nota:** active + passive es la combinación correcta para LACP. Dos passive no forman canal.

### 11.2 MS4

```bash
# Po6 - Hacia Piso 2
interface range FastEthernet0/17 - 20
no switchport
channel-group 6 mode active
no shutdown
exit

interface Port-channel6
ip address 10.2.1.17 255.255.255.252
no shutdown
exit

# Po7 - Hacia Piso 3
interface range FastEthernet0/13 - 16
no switchport
channel-group 7 mode active
no shutdown
exit

interface Port-channel7
ip address 10.2.1.21 255.255.255.252
no shutdown
exit

# Po8 - Hacia Data Center
interface range FastEthernet0/21 - 24
no switchport
channel-group 8 mode active
no shutdown
exit

interface Port-channel8
ip address 10.2.1.25 255.255.255.252
no shutdown
exit
```

### 11.3 MS2

```bash
interface range FastEthernet0/21 - 24
no switchport
channel-group 1 mode passive
no shutdown
exit

interface Port-channel1
ip address 10.2.1.26 255.255.255.252
no shutdown
exit
```

### 11.4 MS0

```bash
interface range FastEthernet0/21 - 24
no switchport
channel-group 2 mode passive
no shutdown
exit

interface Port-channel2
ip address 10.2.1.18 255.255.255.252
no shutdown
exit
```

### 11.5 MS3

```bash
interface range FastEthernet0/21 - 24
no switchport
channel-group 3 mode passive
no shutdown
exit

interface Port-channel3
ip address 10.2.1.22 255.255.255.252
no shutdown
exit
```

### 11.6 Verificación LACP

```bash
show etherchannel summary
show etherchannel 6 detail
```

## 12. Configuración HSRP

### 12.1 HSRP Piso 1

| Router  | VLAN 11 (ADMIN)       | VLAN 21 (ESTUDIANTES) |
| ------- | --------------------- | --------------------- |
| Router1 | **Activo** (prio 110) | Standby (prio 90)     |
| Router0 | Standby (prio 90)     | **Activo** (prio 110) |

| IP Reservada     | Función               |
| ---------------- | --------------------- |
| 192.198.11.1     | HSRP Virtual IP VLAN 21 |
| 192.198.11.2     | Router0 VLAN 21       |
| 192.198.11.3     | Router1 VLAN 21       |
| 192.198.11.129   | HSRP Virtual IP VLAN 11 |
| 192.198.11.130   | Router1 VLAN 11       |
| 192.198.11.131   | Router0 VLAN 11       |

### 12.2 HSRP Data Center

| Router  | VLAN 31 (WEB)        | VLAN 41 (DHCP)       |
| ------- | -------------------- | -------------------- |
| Router2 | **Activo** (prio 110) | **Activo** (prio 110) |
| Router3 | Standby (prio 100)   | Standby (prio 100)   |

| IP Reservada        | Función                    |
| ------------------- | -------------------------- |
| 192.198.100.1       | HSRP Virtual IP VLAN 31    |
| 192.198.100.2       | ServerWeb (estático)       |
| 192.198.100.3       | Router2 VLAN 31            |
| 192.198.100.4       | Router3 VLAN 31            |
| 192.198.100.129     | HSRP Virtual IP VLAN 41    |
| 192.198.100.130     | ServerDHCP (estático)      |
| 192.198.100.131     | Router2 VLAN 41            |
| 192.198.100.132     | Router3 VLAN 41            |

### 12.3 Verificación HSRP

```bash
show standby brief
show standby
```

**Router1:**
```
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Gi0/1.11    11   110 P Active   local           192.198.11.131  192.198.11.129
Gi0/1.21    21   90    Standby  192.198.11.2    local           192.198.11.1
```

**Router0:**
```
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Gi0/1.11    11   90    Standby  192.198.11.130  local           192.198.11.129
Gi0/1.21    21   110 P Active   local           192.198.11.3    192.198.11.1
```

**Router2:**
```
Interface   Grp  Pri P State    Active          Standby         Virtual IP
Gi0/1.31    31   110 P Active   local           192.198.100.4   192.198.100.1
Gi0/1.41    41   110 P Active   local           192.198.100.132 192.198.100.129
```

**Router3:**
```
Interface   Grp  Pri   State    Active          Standby         Virtual IP
Gi0/1.31    31   100   Standby  192.198.100.3   local           192.198.100.1
Gi0/1.41    41   100   Standby  192.198.100.131 local           192.198.100.129
```

## 13. Comandos de Verificación

```bash
# VLANs
show vlan brief
show interfaces trunk

# LACP
show etherchannel summary
show etherchannel 6 detail

# OSPF
show ip ospf neighbor
show ip route ospf

# HSRP
show standby brief
show standby

# Routing general
show ip route
ping 192.198.100.2

# Verificar conectividad WiFi → Data Center
# Desde un cliente WiFi del Piso 2 hacer ping a 192.198.100.2
```

## 14. Pruebas de Conectividad

- [ ] PC1 (VLAN 21) → Laptop1 (VLAN 11) ✅
- [ ] PC1 → ServerWeb (192.198.100.2) ✅
- [ ] Laptop1 → ServerDHCP (192.198.100.130) ✅
- [ ] Dispositivo WiFi Piso 2 → ServerWeb ✅
- [ ] Dispositivo WiFi Piso 3 → ServerWeb ✅
- [ ] DNS resuelve www.practica2_Grupo1.com desde Piso 1 ✅
- [ ] DNS resuelve www.practica2_Grupo1.com desde Piso 2 (WiFi) ✅
- [ ] DNS resuelve www.practica2_Grupo1.com desde Piso 3 (WiFi) ✅
- [ ] Página web se muestra en los 3 pisos ✅
- [ ] HSRP failover (apagar Router1 → Router0 toma el control) ✅
- [ ] HSRP failover Data Center (apagar Router2 → Router3 asume) ✅
- [ ] LACP failover (desconectar un enlace del bundle → tráfico continúa) ✅

## 15. Notas Importantes

1. **WRT300N en Packet Tracer**: El puerto WAN/Internet NO soporta 802.1Q. Debe conectarse a un routed port o access port, nunca a un trunk.

2. **Routed Ports MS0/MS3 → WRT300N (v7)**: MS0 y MS3 usan routed ports directos en subredes /30 (10.2.1.28/30, .32/30, .36/30, .40/30) para conectar los WRT300Ns. Esto elimina el conflicto de prefix OSPF y garantiza que el WRT300N pueda alcanzar su gateway. Ya NO se usan SVIs VLAN 11 en MS0/MS3.

3. **Subnetting**: VLAN 21 usa 192.198.11.0/25 (123 hosts DHCP). VLAN 11 usa 192.198.11.128/27 (27 hosts DHCP disponibles). Esta subred ADMIN solo existe en Piso 1 (Switch0 + R0 + R1). Los pisos 2 y 3 usan 10.2.1.x para tránsito.

4. **LACP**: MS4 usa `active`, MS0/MS3/MS2 usan `passive`. Combinación correcta para LACP.

5. **OSPF**: MS0/MS3 únicamente anuncian sus enlaces /30 propios. La red 192.198.11.128/27 es exclusiva de R0 y R1 (únicos con interfaces físicas en ese segmento L2). Anunciar esa red desde MS0/MS3 causaba routing ambiguo y gateway inalcanzable en los WRT300Ns.

6. **DNS en WRT300N**: Todos los WRT300N tienen configurado DNS Server `192.198.100.2` para que los clientes WiFi puedan resolver www.practica2_Grupo1.com.

7. **Flujo NAT WiFi**: Los clientes WiFi obtienen IPs en 192.198.21.x o 192.198.31.x. El WRT300N hace NAT hacia su WAN (10.2.1.x/30). MS0/MS3 enrutan hacia MS4, que enruta hacia el Data Center. El retorno funciona por OSPF.

---

**Manual Técnico - Práctica 2 — v8**
**Grupo 28 - Redes de Computadoras 2**
**Universidad San Carlos de Guatemala**
**Facultad de Ingeniería**
