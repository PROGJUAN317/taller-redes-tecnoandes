# Taller de Redes: Diseño de red para TecnoAndes S.A.S.

**Estudiante:** Juan Sebastian Navarrete Rada
**Código:** 2459562
**Institución:** Universidad del Valle
**Actividad:** Ejercicio en parejas (viernes 6 de noviembre de 2026)

---

## 1. ¿Por qué existe este repositorio?

Este repositorio guarda y documenta la solución del taller de redes, que pide proponer una empresa con mínimo 12 áreas y desarrollar:

1. Un documento con la información de la empresa y sus áreas.
2. Una red con mínimo 4 switches.
3. Subnetting de 9 subredes clase C.
4. VLAN clase B.
5. 4 servidores HTTP y DNS.

Se usa GitHub porque permite:

- Tener **todos los entregables en un solo lugar** (simulación, documento y explicación).
- Dejar **evidencia de autoría y fecha** de cada versión (commits).
- **Compartir el trabajo con la pareja** y con el profesor mediante un solo enlace.
- **Descargar todo** como ZIP si la plataforma no acepta enlaces.

---

## 2. Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `tecnoandes.pkt` | Simulación de la red en Cisco Packet Tracer |
| `Documento_TecnoAndes.pdf` | Documento con la información de la empresa y sus áreas |
| `README.md` | Este archivo |
| `topologia.png` | Captura de la topología (opcional, recomendada) |

---

## 3. Cómo abrir el proyecto (paso a paso)

**Paso 1. Descarga el repositorio**
- En la página del repo, clic en **Code → Download ZIP** y descomprime la carpeta.

**Paso 2. Instala Cisco Packet Tracer**
- Descárgalo gratis desde Cisco Networking Academy (requiere cuenta).
- Usa una versión igual o superior a la usada para crear el archivo.

**Paso 3. Abre la simulación**
- En Packet Tracer ve a **File → Open** y selecciona `tecnoandes.pkt`.

**Paso 4. Revisa la topología**
- Verás el router, el switch core (Switch0), tres switches de acceso, los PC de cada área y los 4 servidores.
- Haz clic sobre un dispositivo para ver su configuración (pestañas *Config* y *CLI*).

**Paso 5. Prueba la conectividad**
- En un PC abre **Desktop → Command Prompt**.
- Ejecuta `ping 192.168.10.1` (gateway de Gerencia) o `ping 192.168.10.130` (servidor web).
- Prueba el DNS con `nslookup www.tecnoandes.com`; debe responder `192.168.10.130`.
- Abre **Desktop → Web Browser** y entra a `http://www.tecnoandes.com`.

**Paso 6. Lee el documento**
- Abre `Documento_TecnoAndes.pdf` para ver la información de la empresa y la justificación del diseño.

---

## 4. El escenario: la historia de TecnoAndes S.A.S.

TecnoAndes S.A.S. es una empresa mediana que fabrica y vende sus productos desde un edificio de tres niveles junto a una planta. Cada área quedó ubicada según su forma de trabajar, y de esa ubicación nació el diseño de la red.

En el **tercer piso** está la dirección. La **Gerencia** y la oficina **Jurídica** comparten pasillo, porque casi todos los contratos y decisiones pasan por las dos. Al lado, tras una puerta con control de acceso, trabaja **Recursos Humanos**, que maneja datos personales y nóminas. Por eso vive cerca, pero con su propia red aislada. Al final de ese pasillo está el cuarto de telecomunicaciones, donde se encuentran el router, el switch principal y los **cuatro servidores** (dos web y dos de nombres). Todos los empleados, sin importar su piso, necesitan llegar a esos servidores a diario.

En el **segundo piso** está el bloque de apoyo. **Contabilidad** ocupa una oficina cerrada, por la sensibilidad de sus datos financieros, y a fin de mes recibe de Recursos Humanos las novedades de nómina, que es uno de los pocos puentes entre dos áreas de pisos distintos. Junto a ella está **TI**, que desde ahí atiende a todo el edificio y necesita poder ver cualquier red para administrarla. Al fondo, junto al muelle de carga interno, tiene su oficina **Logística**, que controla las entradas y salidas de bodega.

En el **primer piso** está la cara de la empresa frente al público. **Ventas** comparte sala con **Compras**, aunque hacen trabajos distintos: Compras negocia con los proveedores y Ventas promete entregas a los clientes, y ambas dependen de lo que Logística tiene en bodega, por eso conversan con ella a diario aunque esté un piso más arriba. Al fondo del piso está **Servicio al Cliente**, que atiende llamadas y reclamos de personas externas. Es el área con más contacto con el exterior, así que se mantiene separada y solo habla con Ventas y Marketing, para resolver pedidos y quejas, y con los servidores.

En la **planta de producción**, al otro lado del patio, trabajan **Producción** y **Calidad**, que comparten procesos y reportes de lo que se fabrica. Cuando Producción necesita materia prima, le escribe a Compras y a Logística, y cuando se negocia con un proveedor nuevo, Calidad revisa que cumpla los estándares. **Marketing** ocupa una oficina de vidrio dentro de la planta, porque fotografía y revisa los productos terminados para sus campañas. Aunque está lejos de Ventas, ambas comparten clientes y campañas, así que se comunican todo el día pese a estar en edificios distintos.

Así, la distancia física y las necesidades de comunicación definieron todo: las áreas que se tocan en el día a día comparten red (VLAN), las que manejan información sensible o tienen riesgo externo se aíslan, y las que trabajan juntas desde lugares distintos, como Ventas con Marketing y Compras con Logística, comparten VLAN aunque estén en switches diferentes.

---

## 5. Resumen técnico del diseño

### 5.1 Switches (24 puertos FastEthernet cada uno)

| Switch | Papel | VLAN que atiende |
|---|---|---|
| Switch0 | Core (conecta router y servidores) | 10 (Gerencia, Jurídica), 20 (RR.HH.), 90 (Servidores) |
| Switch1 | Acceso | 30 (Contabilidad), 50 (Logística), 80 (TI) |
| Switch2 | Acceso | 40 (Ventas), 50 (Compras), 70 (Servicio al Cliente) |
| Switch3 | Acceso | 40 (Marketing), 60 (Producción, Calidad) |

Topología en estrella: Switch1, Switch2 y Switch3 se conectan al Switch0 por su puerto Fa0/24 (Fa0/21, Fa0/22 y Fa0/23 de Switch0). Todos esos enlaces son **trunk 802.1Q**. El Switch0 se conecta al router por Fa0/24 hacia Gi0/0/0, también en trunk, y el router enruta entre VLAN con **router-on-a-stick** (una subinterfaz por VLAN).

### 5.2 VLAN y subnetting clase C (9 subredes /28)

Red base: `192.168.10.0/24`, máscara `255.255.255.240` (/28), 14 hosts útiles por subred.

| VLAN | Grupo | Áreas | Equipos | Subred | Gateway | Rango de equipos |
|---|---|---|---|---|---|---|
| 10 | Directivo | Gerencia + Jurídica | 6 | 192.168.10.0/28 | .1 | .2 – .7 |
| 20 | Talento | RR.HH. | 5 | 192.168.10.16/28 | .17 | .18 – .22 |
| 30 | Finanzas | Contabilidad | 8 | 192.168.10.32/28 | .33 | .34 – .41 |
| 40 | Comercial | Ventas + Marketing | 10 | 192.168.10.48/28 | .49 | .50 – .59 |
| 50 | Abastecimiento | Compras + Logística | 9 | 192.168.10.64/28 | .65 | .66 – .74 |
| 60 | Operación | Producción + Calidad | 12 | 192.168.10.80/28 | .81 | .82 – .93 |
| 70 | Atención | Servicio al Cliente | 10 | 192.168.10.96/28 | .97 | .98 – .107 |
| 80 | TI | TI | 6 | 192.168.10.112/28 | .113 | .114 – .119 |
| 90 | Servidores | 4 servidores HTTP y DNS | 4 | 192.168.10.128/28 | .129 | .130 – .133 |

> Las cantidades de equipos son una estimación para dimensionar la red. Cada subred /28 admite hasta 13 equipos más el gateway.

### 5.3 Servidores (VLAN 90)

| Servidor | Servicio | IP |
|---|---|---|
| Server0 | HTTP | 192.168.10.130 |
| Server1 | HTTP | 192.168.10.131 |
| Server2 | DNS principal | 192.168.10.132 |
| Server3 | DNS secundario | 192.168.10.133 |

Registros DNS configurados: `www.tecnoandes.com` → 192.168.10.130 e `intranet.tecnoandes.com` → 192.168.10.131. Todos los PC usan como DNS la IP 192.168.10.132.

### 5.4 Red clase B (gestión)

Se usa `172.16.0.0/24` para la administración de los switches:

| Dispositivo | IP de gestión |
|---|---|
| Switch0 | 172.16.0.10 |
| Switch1 | 172.16.0.11 |
| Switch2 | 172.16.0.12 |
| Switch3 | 172.16.0.13 |

### 5.5 Comunicación entre áreas (diseño)

| Origen | Puede acceder a |
|---|---|
| Todas las VLAN | Servidores (HTTP y DNS) |
| Directivo (10) y TI (80) | Todas las VLAN |
| Comercial (40) ↔ Abastecimiento (50) | Pedidos e inventario |
| Finanzas (30) ↔ Talento (20) | Nómina |
| Finanzas (30) ↔ Comercial (40) | Facturación |
| Operación (60) ↔ Abastecimiento (50) | Materias primas |
| Atención (70) | Solo Comercial y servidores |

En la simulación el router enruta todas las VLAN entre sí. Las restricciones de esta tabla corresponden a la política de diseño y se aplicarían con listas de control de acceso (ACL).

---

## 6. Autoría

Trabajo desarrollado por:

- **Juan Sebastian Navarrete Rada**, código **2459562**

Universidad del Valle, noviembre de 2026.
