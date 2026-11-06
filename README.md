# Taller de Redes: Diseño de red para TecnoAndes S.A.S.

**Estudiante:** Juan Sebastian Navarrete Rada
**Código:** 2459562
**Compañero/a de trabajo:** [Nombre y código de la pareja]
**Institución:** Universidad del Valle
**Actividad:** Ejercicio en parejas (apertura y cierre: viernes 6 de noviembre de 2026)

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
- Poder **descargar todo** como ZIP si la plataforma no acepta enlaces.

---

## 2. Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `TecnoAndes.pkt` | Simulación de la red en Cisco Packet Tracer |
| `Documento_TecnoAndes.pdf` | Documento con la información de la empresa y sus áreas |
| `README.md` | Este archivo |
| `topologia.png` | Captura de la topología (opcional, recomendada) |

---

## 3. Cómo abrir el proyecto (paso a paso)

**Paso 1. Descarga el repositorio**
- En la página del repo, clic en **Code → Download ZIP**.
- Descomprime la carpeta en tu computador.

**Paso 2. Instala Cisco Packet Tracer**
- Descárgalo gratis desde Cisco Networking Academy (se requiere cuenta).
- Usa una versión igual o superior a la usada para crear el archivo.

**Paso 3. Abre la simulación**
- Abre Packet Tracer.
- Ve a **File → Open** y selecciona `TecnoAndes.pkt`.
- También puedes hacer doble clic sobre el archivo.

**Paso 4. Revisa la topología**
- Verás el router, el switch core, los 3 switches de acceso, los equipos de cada área y los 4 servidores.
- Haz clic sobre cualquier dispositivo para ver su configuración (pestañas *Config* y *CLI*).

**Paso 5. Prueba la conectividad**
- Abre la **línea de comandos (Desktop → Command Prompt)** de cualquier PC.
- Prueba con `ping` hacia el gateway y hacia los servidores.
- Prueba el DNS con `nslookup` y el HTTP abriendo el navegador (*Web Browser*) del PC.

**Paso 6. Lee el documento**
- Abre `Documento_TecnoAndes.pdf` para ver la información de la empresa y la justificación del diseño.

---

## 4. El escenario: la historia de TecnoAndes S.A.S.

TecnoAndes S.A.S. es una empresa mediana que ocupa un edificio de tres niveles. Cada área quedó ubicada según su forma de trabajar, y de esa ubicación nació el diseño de la red.

En el **tercer piso** está la parte directiva y administrativa. La **Gerencia** y la oficina **Jurídica** comparten un mismo pasillo, porque casi todos los contratos y decisiones pasan por las dos. Frente a ellas, separadas por una puerta con control de acceso, están **Recursos Humanos** y **Contabilidad**, que manejan información delicada como nóminas, datos personales y estados financieros. Por eso viven cerca, pero cada una tiene su propia red aislada. Los lunes y a fin de mes, Recursos Humanos le envía a Contabilidad los datos de la nómina, así que ese es uno de los pocos puentes de comunicación entre ellas. Contabilidad, a su vez, mantiene contacto con el área comercial para facturar las ventas.

En el **segundo piso** está la cara de la empresa frente al cliente. **Ventas** y **Marketing** comparten una sala abierta: planean campañas, revisan clientes potenciales y se pasan información todo el día, por eso trabajan en la misma red. Al fondo del piso está **Servicio al Cliente**, que recibe llamadas y reclamos de personas externas. Como es el área con más contacto con el exterior, se mantiene separada y solo habla con Comercial (para resolver pedidos y quejas) y con los servidores.

En el **primer piso**, junto a la bodega y la planta, trabajan **Compras** y **Logística**, que viven pendientes del inventario y de los proveedores. A pocos metros están **Producción** y **Calidad**, que comparten procesos y reportes de lo que se fabrica. Cuando Producción necesita materia prima, le escribe a Abastecimiento, y cuando Compras negocia con un proveedor nuevo, Calidad revisa que cumpla los estándares. Además, Comercial y Abastecimiento conversan constantemente: lo que Ventas promete depende de lo que Logística tiene en bodega.

Al fondo del primer piso, en un cuarto cerrado y con aire acondicionado, está **TI**, junto al rack principal donde se encuentra el switch core, el router y los **cuatro servidores** (dos HTTP y dos DNS). Todos los empleados, sin importar su piso, necesitan llegar a esos servidores para abrir las páginas internas y resolver nombres. TI, por su parte, necesita ver todas las redes para administrarlas, y la Gerencia puede consultar cualquier área cuando lo requiere.

Así, la distancia física y las necesidades de comunicación definieron todo: las áreas que se tocan en el día a día comparten red (VLAN), las que manejan información sensible o tienen riesgo externo se aíslan, y cada piso se conecta con un switch propio que sube hasta el core.

---

## 5. Resumen técnico del diseño

### 5.1 Switches (24 puertos FastEthernet cada uno)

| Switch | Ubicación | VLAN que atiende |
|---|---|---|
| SW-Core | Cuarto de TI (piso 1) | 80 (TI) y 90 (Servidores) |
| SW1 | Piso 3 | 10, 20, 30 |
| SW2 | Piso 2 | 40, 70 |
| SW3 | Piso 1 | 50, 60 |

Los enlaces entre switches y hacia el router son **trunk 802.1Q**. El enrutamiento entre VLAN se hace con **router-on-a-stick**.

### 5.2 VLAN y subnetting clase C (9 subredes /28)

Red base: `192.168.10.0/24` → máscara `255.255.255.240` (/28), 14 hosts útiles por subred.

| VLAN | Grupo | Áreas | Equipos | Subred | Gateway |
|---|---|---|---|---|---|
| 10 | Directivo | Gerencia + Jurídica | 6 | 192.168.10.0/28 | .1 |
| 20 | Talento | RR.HH. | 5 | 192.168.10.16/28 | .17 |
| 30 | Finanzas | Contabilidad | 8 | 192.168.10.32/28 | .33 |
| 40 | Comercial | Ventas + Marketing | 10 | 192.168.10.48/28 | .49 |
| 50 | Abastecimiento | Compras + Logística | 9 | 192.168.10.64/28 | .65 |
| 60 | Operación | Producción + Calidad | 12 | 192.168.10.80/28 | .81 |
| 70 | Atención | Servicio al Cliente | 10 | 192.168.10.96/28 | .97 |
| 80 | TI | TI | 6 | 192.168.10.112/28 | .113 |
| 90 | Servidores | 4 servidores HTTP y DNS | 4 | 192.168.10.128/28 | .129 |

> Las cantidades de equipos son una estimación para dimensionar la red. Cada subred /28 admite hasta 13 equipos más el gateway.

### 5.3 Red clase B

Se usa `172.16.0.0/16` para la **gestión de dispositivos** (switches y router) y los **enlaces de infraestructura**, conforme al requisito de VLAN clase B del enunciado.

### 5.4 Servidores

| Servidor | Servicio | Ubicación |
|---|---|---|
| SRV1 | HTTP | VLAN 90 |
| SRV2 | HTTP | VLAN 90 |
| SRV3 | DNS | VLAN 90 |
| SRV4 | DNS | VLAN 90 |

---

## 6. Autoría

Trabajo desarrollado por:

- **Juan Sebastian Navarrete Rada**, código **2459562**
- [Nombre y código de la pareja]

Universidad del Valle, noviembre de 2026.
