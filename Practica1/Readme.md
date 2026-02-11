# Práctica 1 – Monte Alto y la Conectividad

## Curso

Redes de Computadoras 2
Ingeniería en Ciencias y Sistemas
Facultad de Ingeniería
Universidad de San Carlos de Guatemala

## Grupo

Grupo: 28
Periodo: Primer Semestre 2026

## Integrantes

| Carné     |             Nombre             |
| --------- | :----------------------------: |
| 202302220 |  Enner Esaí Mendizabal Castro  |
| 202202410 | Marcos Daniel Bonifasi de Leon |
| 202300670 | David Estuardo Barrios Ramírez |

## 1. Introducción

La presente documentación corresponde al desarrollo de la Práctica 1: “Monte Alto y la conectividad”, cuyo objetivo es el diseño, implementación y validación de una red LAN segmentada y segura, utilizando Cisco Packet Tracer como herramienta de simulación.

El escenario planteado representa la infraestructura de red de un colegio que ha crecido de forma desordenada, provocando problemas de conectividad, pérdida de paquetes y dificultad en la administración de la red. A partir de esta problemática, se propone una solución basada en buenas prácticas de redes, aplicando segmentación lógica, protocolos de redundancia, enrutamiento dinámico y políticas de seguridad.

## 2. Objetivo General

Diseñar e implementar una red LAN funcional que permita la comunicación eficiente y segura entre los distintos departamentos del colegio Monte Alto, mediante:

- Segmentación de la red usando VLANs.

- Administración centralizada de VLANs con VTP.

- Implementación de Spanning Tree Protocol para evitar bucles y analizar tiempos de convergencia.

- Uso de protocolos de enrutamiento dinámico para la comunicación entre redes.

- Aplicación de medidas de seguridad en los puertos de acceso.

## 3. Objetivos Específicos

- Configurar al menos 6 VLANs distribuidas entre dos edificios.

- Garantizar la correcta propagación de VLANs mediante VTP.

- Implementar PVST y Rapid PVST, analizando y comparando sus tiempos de convergencia.

- Configurar EIGRP, RIP y OSPF en los routers correspondientes.

- Verificar la comunicación:
  - Intra-VLAN

  - Inter-VLAN

  - Entre ambos edificios

- Aplicar Port Security para restringir el acceso no autorizado a la red.

- Documentar detalladamente todas las configuraciones realizadas.

## 4. Alcance del Proyecto

El alcance de la práctica incluye:

- Configuración básica de switches y routers.

- Diseño e implementación de la topología solicitada.

- Creación y administración de VLANs por nivel académico.

- Configuración de enlaces trunk y puertos access.

- Implementación de VTP en modo servidor y cliente.

- Aplicación de Spanning Tree Protocol en sus versiones PVST y Rapid PVST.

- Configuración de protocolos de enrutamiento dinámico.

- Pruebas de conectividad y análisis de convergencia.

- Elaboración de documentación técnica completa en formato Markdown.

Quedan fuera del alcance de esta práctica la implementación en equipos reales y la aplicación de mecanismos avanzados de seguridad a nivel de firewall.

## 5. Metodología de Trabajo

Para el desarrollo del proyecto se siguió la siguiente metodología:

- Análisis de los requerimientos técnicos de la práctica.

- Diseño de la topología lógica y física.

- División del trabajo por áreas técnicas entre los integrantes del grupo.

- Implementación progresiva de las configuraciones en Cisco Packet Tracer.

- Validación mediante pruebas de conectividad y simulación de fallas.

- Documentación detallada de cada configuración realizada.

- Control de versiones y seguimiento del proyecto mediante GitHub.

## 6. Herramientas y Recursos Utilizados

- Cisco Packet Tracer

- GitHub (repositorio del proyecto)

- Markdown para documentación técnica

- Material de apoyo proporcionado en el curso:
  - CCNA Desde Cero

  - Repositorio de clases del curso

## 7. Diseño de la Topología de Red

### 7.1 Descripción General

La topología de red fue diseñada bajo un esquema jerárquico, separando la infraestructura en dos edificios (izquierdo y derecho), cada uno con switches de acceso y un router dedicado, los cuales se interconectan a través de un router central.

Este diseño permite:

- Segmentación lógica mediante VLANs.

- Redundancia y control de bucles con STP.

- Escalabilidad para futuras expansiones.

- Separación clara entre capa de acceso, distribución y capa de red.

### 7.2 Topología Principal

### 7.3 Asignación de puertos

#### 7.3.1 Enlaces entre dispositivos (Nivel 0)

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Router0            | Gig0/0        | Router1             | Gig0/0         |
| Router1            | Gig0/1        | Router4             | Gig0/0         |
| Router4            | Gig0/1        | Router3             | Gig0/0         |

#### 7.3.2 Enlaces entre dispositivos - Edificio Izquierdo

**Nivel 1:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Router0            | Gig0/1        | Switch0             | Gig0/1         |

**Nivel 2:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch0            | FaE0/24       | Switch1             | FaE0/24        |
| Switch0            | FaE0/23       | Switch12            | FaE0/24        |
| Switch0            | FaE0/22       | Switch2             | FaE0/24        |

**Nivel 3:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch1            | FaE0/23       | Switch4             | FaE0/24        |
| Switch1            | FaE0/22       | Switch3             | FaE0/24        |
| Switch12           | FaE0/23       | Switch3             | FaE0/23        |
| Switch2            | FaE0/23       | Switch3             | FaE0/22        |
| Switch2            | FaE0/22       | Switch5             | FaE0/24        |

**Nivel 4:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch4            | FaE0/23       | Switch3             | FaE0/21        |
| Switch3            | FaE0/20       | Switch5             | FaE0/23        |
| Switch4            | FaE0/22       | Switch7             | FaE0/24        |
| Switch4            | FaE0/21       | Switch6             | FaE0/24        |
| Switch3            | FaE0/19       | Switch7             | FaE0/23        |
| Switch3            | FaE0/18       | Switch6             | FaE0/23        |
| Switch3            | FaE0/17       | Switch8             | FaE0/24        |
| Switch3            | FaE0/16       | Switch9             | FaE0/24        |
| Switch3            | FaE0/15       | Switch10            | FaE0/24        |
| Switch5            | FaE0/22       | Switch9             | FaE0/23        |
| Switch5            | FaE0/21       | Switch10            | FaE0/23        |

**Nivel 5:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch7            | FaE0/22       | PC1                 | FaE0           |
| Switch6            | FaE0/22       | PC2                 | FaE0           |
| Switch8            | FaE0/23       | Laptop1             | FaE0           |
| Switch9            | FaE0/22       | PC4                 | FaE0           |
| Switch10           | FaE0/22       | PC5                 | FaE0           |

#### 7.3.3 Enlaces entre dispositivos - Edificio Derecho

**Nivel 1:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Router3            | Gig0/1        | Switch(0)1          | Gig0/1         |

**Nivel 2:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch(0)1         | FaE0/24       | Switch(1)1          | FaE0/24        |
| Switch(0)1         | FaE0/23       | Switch11            | FaE0/24        |
| Switch(0)1         | FaE0/22       | Switch(2)1          | FaE0/24        |

**Nivel 3:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch(1)1         | FaE0/23       | Switch(3)1          | FaE0/24        |
| Switch(1)1         | FaE0/22       | Switch(4)1          | FaE0/24        |
| Switch11           | FaE0/23       | Switch(4)1          | FaE0/23        |
| Switch(2)1         | FaE0/23       | Switch(4)1          | FaE0/22        |
| Switch(2)1         | FaE0/22       | Switch(5)1          | FaE0/24        |

**Nivel 4:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch(3)1         | FaE0/23       | Switch(4)1          | FaE0/21        |
| Switch(4)1         | FaE0/20       | Switch(5)1          | FaE0/23        |
| Switch(3)1         | FaE0/22       | Switch(6)1          | FaE0/24        |
| Switch(3)1         | FaE0/21       | Switch(7)1          | FaE0/24        |
| Switch(4)1         | FaE0/19       | Switch(6)1          | FaE0/23        |
| Switch(4)1         | FaE0/18       | Switch(7)1          | FaE0/23        |
| Switch(4)1         | FaE0/17       | Switch(8)1          | FaE0/24        |
| Switch(4)1         | FaE0/16       | Switch(9)1          | FaE0/24        |
| Switch(4)1         | FaE0/15       | Switch(10)1         | FaE0/24        |
| Switch(5)1         | FaE0/22       | Switch(9)1          | FaE0/23        |
| Switch(5)1         | FaE0/21       | Switch(10)1         | FaE0/23        |

**Nivel 5:**

| Dispositivo Origen | Puerto Origen | Dispositivo Destino | Puerto Destino |
| ------------------ | ------------- | ------------------- | -------------- |
| Switch(6)1         | FaE0/22       | PC6                 | FaE0           |
| Switch(7)1         | FaE0/22       | PC7                 | FaE0           |
| Switch(8)1         | FaE0/23       | Laptop2             | FaE0           |
| Switch(9)1         | FaE0/22       | PC3                 | FaE0           |
| Switch(10)1        | FaE0/22       | PC8                 | FaE0           |

![Topología Principal](img/topologia_principal.png)

## 8. Configuración Básica de Switches

Se realizó la configuración base en todos los switches de la topología cumpliendo con los estándares de nomenclatura y seguridad definidos para el **Grupo 28**.

### 8.1 Estandarización de Nombres (Hostnames)

Se aplicó el formato `SW#A_G#B` donde `#A` representa el identificador secuencial del dispositivo y `#B` el número de grupo (28).

Comando utilizado para configurar el hostname en cada switch:

```bash
enable
configure terminal
hostname SW1_G28
exit
copy running-config startup-config
```

| Dispositivo Original | Nuevo Hostname |
| -------------------- | -------------- |
| Router0 (Izq)        | R0_G28         |
| Switch0 (Izq)        | SW0_G28        |
| Switch1              | SW1_G28        |
| Switch12             | SW12_G28       |
| Switch2              | SW2_G28        |
| Switch4              | SW4_G28        |
| Switch3              | SW3_G28        |
| Switch5              | SW5_G28        |
| Switch7              | SW7_G28        |
| Switch6              | SW6_G28        |
| Switch8              | SW8_G28        |
| Switch9              | SW9_G28        |
| Switch10             | SW10_G28       |
| Router1 (Centro)     | R1_G28         |
| Router4 (Centro)     | R4_G28         |
| Router3 (Der)        | R3_G28         |
| Switch(0)1 (Der)     | SW(0)1_G28     |
| Switch(1)1           | SW(1)1_G28     |
| Switch11             | SW11_G28       |
| Switch(2)1           | SW(2)1_G28     |
| Switch(3)1           | SW(3)1_G28     |
| Switch(4)1           | SW(4)1_G28     |
| Switch(5)1           | SW(5)1_G28     |
| Switch(6)1           | SW(6)1_G28     |
| Switch(7)1           | SW(7)1_G28     |
| Switch(8)1           | SW(8)1_G28     |
| Switch(9)1           | SW(9)1_G28     |
| Switch(10)1          | SW(10)1_G28    |

## 9. Configuración del Protocolo VTP y Troncales

Antes de la creación de las VLANs, se preparó la infraestructura de capa 2 configurando enlaces troncales y el protocolo de administración VTP para garantizar la coherencia de la base de datos de VLANs en toda la topología.

### 9.1 Configuración de Enlaces Troncales (Trunks)

Se configuraron en modo `trunk` todas las interfaces que interconectan dispositivos de red (Switch-Switch y Switch-Router) para permitir el transporte de tramas etiquetadas bajo el estándar 802.1Q.

**Edificio Izquierdo:**

- Switch0 (SW0_G28)
  - Conecta a: Router0 (G0/1), Switch1 (F0/24), Switch12 (F0/23), Switch2 (F0/22)

    ```bash
    enable
    configure terminal
    interface range fa0/22 - 24, gig0/1
    switchport mode trunk
    exit
    copy running-config startup-config
    ```

- Switch1 (SW1_G28)
  - Conecta a: Switch0 (F0/24), Switch4 (F0/23), Switch3 (F0/22)

    ```bash
    enable
    configure terminal
    interface range fa0/22 - 24
    switchport mode trunk
    exit
    copy running-config startup-config
    ```

- Switch12 (SW12_G28)
  - Conecta a: Switch0 (F0/24), Switch3 (F0/23)

    ```bash
    enable
    configure terminal
    interface range fa0/23 - 24
    switchport mode trunk
    exit
    copy running-config startup-config
    ```

- Switch2 (SW2_G28)
  - Conecta a: Switch0 (F0/24), Switch3 (F0/23), Switch5 (F0/22)

  ```bash
  enable
  configure terminal
  interface range fa0/22 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch4 (SW4_G28)
  - Conecta a: Switch1 (F0/24), Switch3 (F0/23), Switch7 (F0/22), Switch6 (F0/21)

  ```bash
  enable
  configure terminal
  interface range fa0/21 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch3 (SW3_G28)
  - Conecta a: Switch1 (F0/24), Switch12 (F0/23), Switch2 (F0/22), Switch4 (F0/21), Switch5 (F0/20), Switch7 (F0/19), Switch6 (F0/18), Switch8 (F0/17), Switch9 (F0/16), Switch10 (F0/15)

  ```bash
  enable
  configure terminal
  interface range fa0/15 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch5 (SW5_G28)
  - Conecta a: Switch2 (F0/24), Switch3 (F0/23), Switch9 (F0/22), Switch10 (F0/21)

  ```bash
  enable
  configure terminal
  interface range fa0/21 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch7 (SW7_G28)
  - Conecta a: Switch4 (F0/24), Switch3 (F0/23)

  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch6 (SW6_G28)
  - Conecta a: Switch4 (F0/24), Switch3 (F0/23)

  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch8 (SW8_G28)
  - Conecta a: Switch3 (F0/24)

  ```bash
  enable
  configure terminal
  interface fa0/24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch9 (SW9_G28)
  - Conecta a: Switch3 (F0/24), Switch5 (F0/23)

  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch10 (SW10_G28)
  - Conecta a: Switch3 (F0/24), Switch5 (F0/23)

  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

**Edificio Derecho:**

- Switch(0)1 (SW(0)1_G28)
  - Conecta a: Router3 (G0/1), Switch(1)1 (F0/24), Switch11 (F0/23), Switch(2)1 (F0/22)

  ```bash
  enable
  configure terminal
  interface range fa0/22 - 24, gig0/1
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(1)1 (SW(1)1_G28)
  - Conecta a: Switch(0)1 (F0/24), Switch(3)1 (F0/23), Switch(4)1 (F0/22)

  ```bash
  enable
  configure terminal
  interface range fa0/22 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch11 (SW11_G28)
  - Conecta a: Switch(0)1 (F0/24), Switch(4)1 (F0/23)
  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```
- Switch(2)1 (SW(2)1_G28)
  - Conecta a: Switch(0)1 (F0/24), Switch(4)1 (F0/23), Switch(5)1 (F0/22)

  ```bash
  enable
  configure terminal
  interface range fa0/22 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(3)1 (SW(3)1_G28)
  - Conecta a: Switch(1)1 (F0/24), Switch(4)1 (F0/23), Switch(6)1 (F0/22), Switch(7)1 (F0/21)

  ```bash
  enable
  configure terminal
  interface range fa0/21 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(4)1 (SW(4)1_G28)
  - Conecta a: Switch(1)1 (F0/24), Switch11 (F0/23), Switch(2)1 (F0/22), Switch(3)1 (F0/21), Switch(5)1 (F0/20), Switch(6)1 (F0/19), Switch(7)1 (F0/18), Switch(8)1 (F0/17), Switch(9)1 (F0/16), Switch(10)1 (F0/15)

  ```bash
  enable
  configure terminal
  interface range fa0/15 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(5)1 (SW(5)1_G28)
  - Conecta a: Switch(2)1 (F0/24), Switch(4)1 (F0/23), Switch(9)1 (F0/22), Switch(10)1 (F0/21)

  ```bash
  enable
  configure terminal
  interface range fa0/21 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(6)1 (SW(6)1_G28)
  - Conecta a: Switch(3)1 (F0/24), Switch(4)1 (F0/23)

  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(7)1 (SW(7)1_G28)
  - Conecta a: Switch(3)1 (F0/24), Switch(4)1 (F0/23)

  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(8)1 (SW(8)1_G28)
  - Conecta a: Switch(4)1 (F0/24)

  ```bash
  enable
  configure terminal
  interface fa0/24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(9)1 (SW(9)1_G28)
  - Conecta a: Switch(4)1 (F0/24), Switch(5)1 (F0/23)

  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

- Switch(10)1 (SW(10)1_G28)
  - Conecta a: Switch(4)1 (F0/24), Switch(5)1 (F0/23)
  ```bash
  enable
  configure terminal
  interface range fa0/23 - 24
  switchport mode trunk
  exit
  copy running-config startup-config
  ```

**Verificación de Trunks:**

Para verificar la correcta configuración de los enlaces troncales, se utilizó el comando `show interfaces trunk` en cada switch, confirmando que las interfaces correspondientes están operando en modo trunk y transportando las VLANs configuradas.

- Edificio Izquierdo (Ejemplo en SW0_G28):

  ![Verificación Trunks Edificio Izquierdo](img/trunk/SW0_trunk.png)

- Edificio Izquierdo (Ejemplo en SW3_G28):

  ![Verificación Trunks Edificio Izquierdo](img/trunk/SW3_trunl.png)

- Edificio Derecho (Ejemplo en SW(0)1_G28):

  ![Verificación Trunks Edificio Derecho](<img/trunk/SW(0)1_trunk.png>)

- Edificio Derecho (Ejemplo en SW(4)1_G28):

  ![Verificación Trunks Edificio Derecho](<img/trunk/SW(4)1_trunk.png>)

### 9.2 Configuración del Protocolo VTP (VLAN Trunking Protocol)

Se implementó el protocolo VTP versión 2 para centralizar la administración de la base de datos de VLANs. Esto permite crear las VLANs únicamente en los switches de distribución y que se propaguen automáticamente a la capa de acceso, reduciendo errores humanos y garantizando la consistencia de la red.

**Parámetros de Configuración:**

| Parámetro   | Valor configurado | Descripción                                                          |
| ----------- | ----------------- | -------------------------------------------------------------------- |
| Dominio VTP | `redes2_G28`      | Identificador único del dominio administrativo.                      |
| Contraseña  | `redes2grupo28`   | Clave MD5 para evitar que switches no autorizados alteren las VLANs. |
| Versión     | `2`               | Versión por defecto compatible con la infraestructura actual.        |

**Roles Asignados**
Para cumplir con el diseño jerárquico y proteger la red, se asignaron los siguientes roles:

1. **Modo Server:** Únicamente en los switches principales de cada edificio. Tienen permiso para crear, eliminar y modificar VLANs.

- Edificio Izquierdo: Switch0 (SW0_G28)

- Edificio Derecho: Switch(0)1 (SW(0)1_G28)

2. **Modo Client:** En todos los switches de acceso y distribución secundaria. Estos dispositivos solo escuchan y replican la configuración de VLANs recibida del servidor, sin posibilidad de alterar la base de datos localmente.

- Dispositivos: Resto de switches (SW1_G28, SW2_G28, SW11_G28, etc.).

**Comandos Implementados:**
**En Switches Servidores (Switch0 y Switch(0)1):**

```bash
configure terminal
vtp domain G28
vtp password redes2grupo28
vtp mode server
copy running-config startup-config
```

**En Switches Clientes (Todos los demás switches):**

```bash
configure terminal
vtp domain G28
vtp password redes2grupo28
vtp mode client
copy running-config startup-config
```

**Verificación de VTP:**

Para verificar la correcta configuración y propagación de VLANs mediante VTP, se utilizó el comando `show vtp status` en cada switch, confirmando que el dominio, contraseña, versión y modo están correctamente configurados.

- Edificio Izquierdo (Ejemplo en SW0_G28 - Servidor):

![Verificación VTP Edificio Izquierdo](img/vtp/SW0_Vtp.png)

- Edificio Izquierdo (Ejemplo en SW4_G28 - Cliente):

![Verificación VTP Edificio Izquierdo](img/vtp/SW4_vtp.png)

- Edificio Derecho (Ejemplo en SW(0)1_G28 - Servidor):

![Verificación VTP Edificio Derecho](<img/vtp/SW(0)1_vtp.png>)

- Edificio Derecho (Ejemplo en SW(3)1_G28 - Cliente):

![Verificación VTP Edificio Derecho](<img/vtp/SW(3)1_vtp.png>)

### 9.3 Creación y Asignación de VLANs

Se procedió a la creación de las VLANs en los switches servidores VTP. Gracias a la configuración previa, estas se replicaron automáticamente a los switches de acceso, donde finalmente se asignaron los puertos correspondientes a los dispositivos finales.

#### VLANs Creadas - Edificio Izquierdo

**(Configuradas en SW0_G28)**

- **VLAN 11:** PRIMARIA
- **VLAN 21:** BASICOS
- **VLAN 31:** BACHILLERATO

#### VLANs Creadas - Edificio Derecho

**(Configuradas en SW(0)1_G28)**

- **VLAN 61:** PRIMARIA
- **VLAN 71:** BASICOS
- **VLAN 81:** BACHILLERATO

**En Edificio Izquierdo (Switch0 / SW0_G28):**

```bash
enable
configure terminal
vlan 11
name PRIMARIA
exit
vlan 21
name BASICOS
exit
vlan 31
name BACHILLERATO
exit
copy running-config startup-config
```

**En Edificio Derecho (Switch(0)1 / SW(0)1_G28):**

```bash
enable
configure terminal
vlan 61
name PRIMARIA
exit
vlan 71
name BASICOS
exit
vlan 81
name BACHILLERATO
exit
copy running-config startup-config
```

#### Evidencia de Propagación

Se verificó en los switches de borde que las VLANs fueron recibidas correctamente mediante VTP, gracias al comando `show vlan brief`, confirmando que todas las VLANs creadas en los servidores están presentes en los clientes.

- Edificio Izquierdo (Ejemplo en SW4_G28):

  ![Verificación VLANs Edificio Izquierdo](img/vlan/S4_vlan.png)

- Edificio Derecho (Ejemplo en SW(3)1_G28):
  ![Verificación VLANs Edificio Derecho](<img/vlan//S(3)1_vlan.png>)

#### Asignación de Puertos de Acceso

Se configuraron las interfaces de red que conectan a los dispositivos finales en modo `access` y se asignaron a las VLANs correspondientes según la segmentación lógica (Primaria, Básicos, Bachillerato) y la ubicación física (Puerto Fa0/22 o Fa0/23).

#### Tabla de Asignación - Edificio Izquierdo

| Dispositivo  | Interfaz | Equipo Final | Perfil/VLAN Asignada   |
| :----------- | :------- | :----------- | :--------------------- |
| **SW7_G28**  | Fa0/22   | PC1          | Básicos (VLAN 21)      |
| **SW6_G28**  | Fa0/22   | PC2          | Bachillerato (VLAN 31) |
| **SW8_G28**  | Fa0/23   | Laptop1      | Bachillerato (VLAN 31) |
| **SW9_G28**  | Fa0/22   | PC4          | Primaria (VLAN 11)     |
| **SW10_G28** | Fa0/22   | PC5          | Primaria (VLAN 11)     |

**1. Switch7 (SW7_G28) - PC1 (Básicos - VLAN 21)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 21
exit
copy running-config startup-config
```

**2. Switch6 (SW6_G28) - PC2 (Bachillerato - VLAN 31)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 31
exit
copy running-config startup-config
```

**3. Switch8 (SW8_G28) - Laptop1 (Bachillerato - VLAN 31)**

```bash
enable
configure terminal
interface fa0/23
switchport mode access
switchport access vlan 31
exit
copy running-config startup-config
```

**4. Switch9 (SW9_G28) - PC4 (Primaria - VLAN 11)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 11
exit
copy running-config startup-config
```

**5. Switch10 (SW10_G28) - PC5 (Primaria - VLAN 11)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 11
exit
copy running-config startup-config
```

#### Tabla de Asignación - Edificio Derecho

| Dispositivo     | Interfaz | Equipo Final | Perfil/VLAN Asignada   |
| :-------------- | :------- | :----------- | :--------------------- |
| **SW(6)1_G28**  | Fa0/22   | PC6          | Bachillerato (VLAN 81) |
| **SW(7)1_G28**  | Fa0/22   | PC7          | Básicos (VLAN 71)      |
| **SW(8)1_G28**  | Fa0/23   | Laptop2      | Básicos (VLAN 71)      |
| **SW(9)1_G28**  | Fa0/22   | PC3          | Primaria (VLAN 61)     |
| **SW(10)1_G28** | Fa0/22   | PC8          | Primaria (VLAN 61)     |

**1. Switch(6)1 (SW(6)1_G28) - PC6 (Bachillerato - VLAN 81)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 81
exit
copy running-config startup-config
```

**2. Switch(7)1 (SW(7)1_G28) - PC7 (Básicos - VLAN 71)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 71
exit
copy running-config startup-config
```

**3. Switch(8)1 (SW(8)1_G28) - Laptop2 (Básicos - VLAN 71)**

```bash
enable
configure terminal
interface fa0/23
switchport mode access
switchport access vlan 71
exit
copy running-config startup-config
```

**4. Switch(9)1 (SW(9)1_G28) - PC3 (Primaria - VLAN 61)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 61
exit
copy running-config startup-config
```

**5. Switch(10)1 (SW(10)1_G28) - PC8 (Primaria - VLAN 61)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport access vlan 61
exit
copy running-config startup-config
```

#### Verificación de Asignación

Para verificar que los puertos de acceso están correctamente asignados a las VLANs, se utilizó el comando `show vlan brief` en cada switch, confirmando que las interfaces correspondientes están en modo access y asignadas a la VLAN correcta.

- Edificio Izquierdo (Ejemplo en SW9_G28):

  ![Verificación Asignación Edificio Izquierdo](img/vlan/S9_vlan.png)

- Edificio Derecho (Ejemplo en SW(7)1_G28):
  ![Verificación Asignación Edificio Derecho](<img/vlan/S(7)1_vlan.png>)

#### Direccionamiento IP - Edificio Izquierdo

A continuación se presenta la tabla de direccionamiento IP asignada a los dispositivos finales del Edificio Izquierdo:

| Dispositivo | VLAN (ID)          | Dirección IP   | Máscara de Subred | Default Gateway |
| :---------- | :----------------- | :------------- | :---------------- | :-------------- |
| PC4         | Primaria (11)      | 192.178.11.10  | 255.255.255.0     | 192.178.11.1    |
| PC5         | Primaria (11)      | 192.178.11.11  | 255.255.255.0     | 192.178.11.1    |
| PC1         | Básicos (21)       | 192.178.21.10  | 255.255.255.0     | 192.178.21.1    |
| PC2         | Bachillerato (31)  | 192.178.31.10  | 255.255.255.0     | 192.178.31.1    |
| Laptop1     | Bachillerato (31)  | 192.178.31.11  | 255.255.255.0     | 192.178.31.1    |

#### Direccionamiento IP - Edificio Derecho

A continuación se presenta la tabla de direccionamiento IP asignada a los dispositivos finales del Edificio Derecho:

| Dispositivo | VLAN (ID)          | Dirección IP   | Máscara de Subred | Default Gateway |
| :---------- | :----------------- | :------------- | :---------------- | :-------------- |
| PC3         | Primaria (61)      | 192.178.61.10  | 255.255.255.0     | 192.178.61.1    |
| PC8         | Primaria (61)      | 192.178.61.11  | 255.255.255.0     | 192.178.61.1    |
| PC7         | Básicos (71)       | 192.178.71.10  | 255.255.255.0     | 192.178.71.1    |
| Laptop2     | Básicos (71)       | 192.178.71.11  | 255.255.255.0     | 192.178.71.1    |
| PC6         | Bachillerato (81)  | 192.178.81.10  | 255.255.255.0     | 192.178.81.1    |

### 9.4 Seguridad de Puertos (Port Security)

Siguiendo las políticas de seguridad establecidas, se configuró `port-security` en las interfaces de acceso pertenecientes a la VLAN de **Básicos**. Esta medida previene que usuarios no autorizados conecten dispositivos ajenos a la red o utilicen hubs para extender la red.

#### Políticas Aplicadas

1.  **Activación:** Se habilitó la seguridad en el puerto.
2.  **Límite:** Máximo **1 dirección MAC** permitida por puerto.
3.  **Aprendizaje:** Se configuró `mac-address sticky` para que el switch aprenda la MAC del dispositivo legítimo conectado actualmente y la guarde en la configuración.
4.  **Acción de Violación:** Modo `shutdown`. Si se detecta una MAC diferente o más de una, el puerto se apagará automáticamente.

#### Puertos Protegidos

| Edificio  | Dispositivo | Interfaz | VLAN         | Equipo Autorizado |
| :-------- | :---------- | :------- | :----------- | :---------------- |
| Izquierdo | SW7_G28     | Fa0/22   | 21 (Básicos) | PC1               |
| Derecho   | SW(7)1_G28  | Fa0/22   | 71 (Básicos) | PC7               |
| Derecho   | SW(8)1_G28  | Fa0/23   | 71 (Básicos) | Laptop2           |

#### Comandos Utilizados

**Configuración en Edificio Izquierdo**

**1.En Switch7 (SW7_G28)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
exit
do write memory
```

**Configuración en Edificio Derecho**

**2. En Switch(7)1 (SW(7)1_G28)**

```bash
enable
configure terminal
interface fa0/22
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
exit
do write memory
```

**3. En Switch(8)1 (SW(8)1_G28)**

```bash
enable
configure terminal
interface fa0/23
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security mac-address sticky
exit
do write memory
```

#### Verificación de Port Security

Para verificar la correcta configuración de `port-security`, se utilizó el comando `show port-security interface [interfaz]` en cada switch, confirmando que la seguridad está habilitada, el límite de MACs es correcto, la acción de violación es `shutdown` y que la MAC del dispositivo autorizado ha sido aprendida y guardada.

- Edificio Izquierdo (Ejemplo en SW7_G28):

  ![Verificación Port Security Edificio Izquierdo](img/port/SW7_port.png)

- Edificio Derecho (Ejemplo en SW(7)1_G28):

  ![Verificación Port Security Edificio Derecho](<img/port/SW(7)1_port.png>)

- Edificio Derecho (Ejemplo en SW(8)1_G28):

  ![Verificación Port Security Edificio Derecho](<img/port/SW(8)1_port.png>)


## 10. Configuración de STP (Spanning Tree Protocol)

### 10.1 Edificio Izquierdo: PVST (Per-VLAN Spanning Tree)

El Switch0 (SW0_G28) es el jefe (Root Bridge) de todas las VLANs de este lado (11, 21, 31), ya que es el que está conectado al Router.

**Comandos para el Switch Principal (SW0_G28):**

```Bash
enable
configure terminal
spanning-tree mode pvst
spanning-tree vlan 11,21,31 root primary
exit
write
```

**Comandos para el Switch Secundario (SW1_G28):**

Este seria un backup por si falla el switch principal

```Bash
enable
configure terminal
spanning-tree mode pvst
spanning-tree vlan 11,21,31 root secondary
exit
write
```

**Comando para todos los demás switches del lado izquierdo**

```bash
enable
configure terminal
spanning-tree mode pvst
exit
write
```

### 10.2 Edificio Derecho: Rapid-PVST

El Switch0 (SW(0)1_G28) es el jefe (Root Bridge) de todas las VLANs de este lado (61, 71, 81), ya que es el que está conectado al Router.

**Comandos para el Switch Principal (SW(0)1_G28):**

```Bash
enable
configure terminal
spanning-tree mode rapid-pvst
spanning-tree vlan 61,71,81 root primary
exit
write
```

**Comandos para el Switch Secundario (SW(1)1_G28):**

Este seria un backup por si falla el switch principal

```Bash
enable
configure terminal
spanning-tree mode rapid-pvst
spanning-tree vlan 61,71,81 root secondary
exit
write
```

**Comando para todos los demás switches del lado derecho**

```bash
enable
configure terminal
spanning-tree mode rapid-pvst
exit
write
```



### 10.3 Estudio de Convergencia

Se realizaron pruebas de estrés simulando la caída de un enlace troncal principal para medir el tiempo que tarda la red en recalcular la topología y restaurar el tráfico de usuarios.

#### 10.3.1 Lado Izquierdo
**Metodología de prueba:**

- Se identificó un puerto en estado Blocking (Naranja) en la capa de acceso.

![Estado en blocking](img/stp/izquierda.png)

- Se inició un ping continuo entre dos hosts de la misma VLAN. (En este caso de la PC4 a la PC5)

![Ping Continuo](img/stp/izquierda1.png)

- Se desconectó físicamente el enlace Forwarding (Verde) principal.

![Desconexión física](img/stp/izquierda2.png)


- Se cronometró el tiempo hasta que el puerto de respaldo pasó a estado Forwarding y el ping respondió nuevamente. El tiempo que tardó en recuperar la conexión fue de alrededor de 42 segundos. 

![Cronometro](img/stp/izquierda3.png)

**Tabla de Resultados**

#### 10.3.2 Lado Derecho

**Metodología de prueba:**

- Se identificó un puerto en estado Blocking (Naranja) en la capa de acceso.

![Estado en blocking](img/stp/derecha.png)

- Se inició un ping continuo entre dos hosts de la misma VLAN. (En esta caso de la PC3 a la PC8)

![Ping Continuo](img/stp/derecha1.png)

- Se desconectó físicamente el enlace Forwarding (Verde) principal.

![Desconexión física](img/stp/derecha2.png)

- Se cronometró el tiempo hasta que el puerto de respaldo pasó a estado Forwarding y el ping respondió nuevamente. El tiempo fue tan corto que se consideró innecesario el colocar un cronómetro y cálculos del tiempo, pues fue casi inmediato.



**Tabla de Resultados**
