---
title: "Instalación de IPCop en VirtualBox: Tu primer firewall virtual"
description: "Guía paso a paso para instalar y configurar IPCop como firewall en una máquina virtual, con explicación de conceptos de red fundamentales."
pubDate: "2025-01-01"
tags: ["redes", "seguridad", "virtualbox", "firewall", "ipcop"]
---

## ¿Qué es IPCop y por qué usarlo?

IPCop es una distribución Linux especializada que convierte una máquina (física o virtual) en un **firewall/router** con interfaz web de administración. Es una excelente opción para laboratorios de redes porque permite entender cómo funciona la segmentación de redes sin necesidad de hardware costoso.

En este tutorial instalaremos IPCop dentro de VirtualBox, configuraremos sus interfaces de red y accederemos a su panel de administración web.

---

## Requisitos previos

- VirtualBox instalado
- ISO de IPCop descargada desde su [página oficial](https://www.ipcop.org/)
- Una segunda VM (en este caso Kali Linux) para probar la conectividad

---

## Paso 1: Crear la máquina virtual

Al crear una nueva VM en VirtualBox, configuramos los siguientes parámetros:

| Parámetro | Valor |
|---|---|
| Nombre | `Enrutador2` |
| ISO | IPCop (imagen descargada) |
| Distribución | Debian 64-bit |
| RAM | 1346 MB |
| Disco | 2.11 GB |

<img src="/imagenes/tutorial/tutorial-ipcop/creacion-vm.jpg" alt="Creación de la VM en VirtualBox" style="max-width:100%; height:auto; border-radius:6px;" />

<img src="/imagenes/tutorial/tutorial-ipcop/configuracionram.png" alt="Configuración de RAM" style="max-width:100%; height:auto; border-radius:6px;" />
<img src="/imagenes/tutorial/tutorial-ipcop/configuraciontamanodisco.png" alt="Configuración del disco" style="max-width:100%; height:auto; border-radius:6px;" />

> **¿Por qué esos recursos?** Aunque IPCop puede correr con menos, se asignaron estos valores con margen para implementar funciones adicionales en el futuro, como reglas de firewall o proxies.

---

## Paso 2: Configurar los adaptadores de red

Una vez creada la VM, vamos a **Configuración → Red → Avanzado (Expert)**.

IPCop necesita **dos interfaces de red** para funcionar correctamente, cada una con un rol distinto:

### Adaptador 1 — Interfaz RED (zona no confiable / internet)

Esta interfaz representa la conexión hacia el exterior. La configuramos como **Red interna** con el nombre `Internet`.

| Opción | Valor |
|---|---|
| Conectar a | Red interna |
| Nombre | Intnet |
| Modo promiscuo | Permitir todo |
| Cable conectado | Verificar Habilitado |

<img src="/imagenes/tutorial/tutorial-ipcop/configuracion-adaptador-1.png" alt="Configuración Adaptador 1" style="max-width:100%; height:auto; border-radius:6px;" />

### Adaptador 2 — Interfaz GREEN (zona confiable / LAN)

Esta interfaz representa la red interna. La configuramos en **Modo puente** para que IPCop pueda comunicarse con las VMs cliente.

| Opción | Valor |
|---|---|
| Conectar a | Modo puente |
| Cable conectado | ✅ Habilitado |

<img src="/imagenes/tutorial/tutorial-ipcop/adaptador-2.png" alt="Configuración Adaptador 2" style="max-width:100%; height:auto; border-radius:6px;" />
---

## Paso 3: Instalación del sistema operativo

Al iniciar la VM por primera vez veremos el menú de arranque de IPCop. Presionamos **Enter** para comenzar la instalación.

<img src="/imagenes/tutorial/tutorial-ipcop/paso1.png" alt="Menú de arranque" style="max-width:100%; height:auto; border-radius:6px;" />

El instalador nos guiará a través de los siguientes pasos:

**1. Selección de idioma**

<img src="/imagenes/tutorial/tutorial-ipcop/seleccion-de-idioma.png" alt="Selección de idioma" style="max-width:100%; height:auto; border-radius:6px;" />

**2. Distribución del teclado**

<img src="/imagenes/tutorial/tutorial-ipcop/seleccion-del-teclado.png" alt="Selección de teclado" style="max-width:100%; height:auto; border-radius:6px;" />

**3. Zona horaria**

<img src="/imagenes/tutorial/tutorial-ipcop/seleccion-zona-horaria.png" alt="Selección de zona horaria" style="max-width:100%; height:auto; border-radius:6px;" />

**4. Configuración de hora y fecha**

<img src="/imagenes/tutorial/tutorial-ipcop/configuracion-hora.png" alt="Configuración de hora" style="max-width:100%; height:auto; border-radius:6px;" />


**5. Selección del disco de instalación**

<img src="/imagenes/tutorial/tutorial-ipcop/seleccion-disco-instalacion.png" alt="Selección del disco" style="max-width:100%; height:auto; border-radius:6px;" />

Después de seleccionar el disco, el instalador mostrará una **advertencia de borrado de datos**. Como se trata de un disco virtual sin datos reales, simplemente aceptamos.


**6. Método de instalación**

El sistema preguntará si la instalación proviene de un disco duro o una memoria flash. Seleccionamos **disco duro**.

<img src="/imagenes/tutorial/tutorial-ipcop/flashvsdisco.png" alt="Flash vs disco" style="max-width:100%; height:auto; border-radius:6px;" />

**7. Opciones de restauración**

Si es una instalación nueva (sin backup previo), omitimos este paso.

<img src="/imagenes/tutorial/tutorial-ipcop/opciones-de-restauracion.png" alt="Opciones de restauración" style="max-width:100%; height:auto; border-radius:6px;" />

Una vez completado el particionado e instalación base, el sistema nos informará que la instalación finalizó correctamente.

<img src="/imagenes/tutorial/tutorial-ipcop/instalaccioncompleta.png" alt="Instalación completa" style="max-width:100%; height:auto; border-radius:6px;" />

---

## Paso 4: Configuración del sistema

### Nombre del host y dominio

<img src="/imagenes/tutorial/tutorial-ipcop/nombrehost.png" alt="Nombre del host" style="max-width:100%; height:auto; border-radius:6px;" />

Asignamos `Enrutador2` como nombre del host, coincidiendo con el nombre de la VM. Para el dominio, dejamos el valor por defecto a menos que cuentes con uno propio registrado.

<img src="/imagenes/tutorial/tutorial-ipcop/dominio.png" alt="Dominio" style="max-width:100%; height:auto; border-radius:6px;" />

### Configuración de la interfaz RED

<img src="/imagenes/tutorial/tutorial-ipcop/configuracioninterfazred.png" alt="Configuración Interfaz RED" style="max-width:100%; height:auto; border-radius:6px;" />

La interfaz RED es la "cara" de IPCop hacia internet. Recibe tráfico directamente desde el ISP o el router principal. Por eso seleccionamos **DHCP**: la dirección IP será asignada automáticamente por el router/módem.

> Solo deberías seleccionar IP estática si tienes una dirección contratada con tu ISP. Para navegar con el teclado: usa las flechas y marca la opción con la **barra espaciadora**.

### Asignación de tarjetas de red (NICs)

<img src="/imagenes/tutorial/tutorial-ipcop/seleccion-directivas.png" alt="Selección de directivas" style="max-width:100%; height:auto; border-radius:6px;" />

En esta pantalla el sistema lista las tarjetas de red detectadas y debemos asignarles su rol:

| NIC | Zona | Función |
|---|---|---|
| `eth0` | GREEN | Red local / LAN (confiable) |
| `eth1` | RED | Internet / exterior (no confiable) |

<img src="/imagenes/tutorial/tutorial-ipcop/interfazgrenn.png" alt="Interfaz GREEN" style="max-width:100%; height:auto; border-radius:6px;" />

<img src="/imagenes/tutorial/tutorial-ipcop/configuracioninterfazred.png" alt="Configuración interfaz RED" style="max-width:100%; height:auto; border-radius:6px;" />

<img src="/imagenes/tutorial/tutorial-ipcop/interfaz-final.png" alt="Interfaz final" style="max-width:100%; height:auto; border-radius:6px;" />

### IP de la interfaz GREEN — Segmentación de red


Este es uno de los pasos más importantes. La dirección IP de la interfaz GREEN **no puede pertenecer al mismo rango** que la red del router principal.

<img src="/imagenes/tutorial/tutorial-ipcop/ipgreen.png" alt="IP de la interfaz GREEN" style="max-width:100%; height:auto; border-radius:6px;" />


**¿Por qué?** Porque la interfaz RED ya usará ese rango (asignado por DHCP desde el router). Si la GREEN tuviera el mismo rango, habría un conflicto de direccionamiento y IPCop no sabría por qué interfaz enrutar el tráfico.

**Ejemplo práctico:**

```
Red del router principal:  192.168.110.0/24  →  usada por la interfaz RED
IP de la interfaz GREEN:   192.168.1.1/24    →  red interna nueva
```

Esto nos da la siguiente topología:

```
[Internet / Router real]
    192.168.110.0/24
           │
       (RED - DHCP)
         IPCop
       (GREEN - 192.168.1.1)
           │
   [Clientes internos]
    192.168.1.0/24
```

IPCop actúa como **gateway** entre las dos redes, separando y controlando el tráfico.

### Host para la interfaz RED

<img src="/imagenes/tutorial/tutorial-ipcop/hostred.png" alt="Host para interfaz RED" style="max-width:100%; height:auto; border-radius:6px;" />

Ingresamos el mismo nombre `Enrutador2`. Este nombre se usará como identificador cuando la interfaz RED solicite una IP al servidor DHCP del router.

### DNS y Gateway

<img src="/imagenes/tutorial/tutorial-ipcop/dnsygateway.png" alt="DNS y Gateway" style="max-width:100%; height:auto; border-radius:6px;" />

Como habilitamos DHCP en la interfaz RED, estos valores serán asignados automáticamente. **Podemos omitir este paso.**

### Servidor DHCP para la zona GREEN

<img src="/imagenes/tutorial/tutorial-ipcop/serviciodhcp.png" alt="Servicio DHCP" style="max-width:100%; height:auto; border-radius:6px;" />

Aquí habilitamos el **servidor DHCP interno de IPCop** para la red GREEN.

**¿Por qué necesitamos esto?** Existen dos redes separadas:

- **RED (exterior):** El router ya tiene su propio servidor DHCP y asigna IPs en el rango `192.168.110.x`.
- **GREEN (interior):** Es una red nueva (`192.168.1.x`) que el router no conoce ni puede alcanzar.

Como el router no puede llegar al segmento GREEN, necesitamos que **IPCop sea el servidor DHCP para esa red**. Esto sigue el principio general: cada segmento de red necesita su propio servidor DHCP, y normalmente lo provee el gateway de ese segmento.

### Contraseñas

El instalador solicitará tres contraseñas distintas:

1. **Root** — acceso por consola/SSH al sistema operativo.

<img src="/imagenes/tutorial/tutorial-ipcop/contrasena-root.png" alt="Contraseña Root" style="max-width:100%; height:auto; border-radius:6px;" />

2. **Admin** — acceso a la interfaz web de administración de IPCop.

<img src="/imagenes/tutorial/tutorial-ipcop/admin-ipcop.png" alt="Contraseña Admin IPCop" style="max-width:100%; height:auto; border-radius:6px;" />

3. **Backup** — clave para cifrar/exportar copias de seguridad de la configuración.

<img src="/imagenes/tutorial/tutorial-ipcop/contrasena-backup.png" alt="Contraseña Backup" style="max-width:100%; height:auto; border-radius:6px;" />

---

## Paso 5: Primer arranque

Tras el mensaje de felicitaciones, el sistema se reinicia y arranca en el menú propio de IPCop.

<img src="/imagenes/tutorial/tutorial-ipcop/felicitaciones.png" alt="Mensaje de felicitaciones" style="max-width:100%; height:auto; border-radius:6px;" />

<img src="/imagenes/tutorial/tutorial-ipcop/menu-de-arranque-ipcop.png" alt="Menú de arranque IPCop" style="max-width:100%; height:auto; border-radius:6px;" />

Iniciamos sesión con las credenciales:

```
usuario: root
contraseña: (la configurada durante la instalación)
```

<img src="/imagenes/tutorial/tutorial-ipcop/loginlogrado.png" alt="Login logrado" style="max-width:100%; height:auto; border-radius:6px;" />
---

## Paso 6: Verificar el puerto de la GUI

Antes de intentar acceder a la interfaz web, debemos confirmar en qué puerto está escuchando el servidor HTTP de IPCop:

```bash
nano /etc/httpd/conf/portgui.conf
```

La primera línea nos indicará el puerto. En este caso: `Listen 8443`.

<img src="/imagenes/tutorial/tutorial-ipcop/comprabacion-puerto.png" alt="Verificación del puerto" style="max-width:100%; height:auto; border-radius:6px;" />

---

## Paso 7: Acceder a la GUI desde la red GREEN

La interfaz web de IPCop **solo es accesible desde la red GREEN** (la zona confiable). Usaremos una VM de Kali Linux con su adaptador configurado en **Red interna** con el mismo nombre que usamos para la interfaz GREEN de IPCop.

<img src="/imagenes/tutorial/tutorial-ipcop/adaptadorhost.png" alt="Adaptador host VM cliente" style="max-width:100%; height:auto; border-radius:6px;" />

> **¿Por qué es importante que coincida el nombre?** En VirtualBox, la **Red interna** funciona como un switch virtual. Las VMs solo pueden comunicarse entre sí si comparten exactamente el mismo nombre de red interna. Si los nombres difieren, VirtualBox las coloca en redes independientes y no habrá comunicación.

Abrimos el navegador en Kali Linux y navegamos a:

```
https://192.168.1.1:8443
```

Aparecerá una advertencia de seguridad por el certificado autofirmado.

<img src="/imagenes/tutorial/tutorial-ipcop/advertencia.png" alt="Advertencia de seguridad" style="max-width:100%; height:auto; border-radius:6px;" />

Hacemos clic en **Avanzado → Acepto los riesgos y continuar**. Aparecerá el formulario de login.

<img src="/imagenes/tutorial/tutorial-ipcop/loginnavegador.png" alt="Login en el navegador" style="max-width:100%; height:auto; border-radius:6px;" />

Ingresamos las credenciales del usuario **admin** configuradas durante la instalación.

---

## Resultado final

<img src="/imagenes/tutorial/tutorial-ipcop/gui.png" alt="GUI de IPCop" style="max-width:100%; height:auto; border-radius:6px;" />

¡IPCop está instalado y funcionando! Desde este panel podrás gestionar reglas de firewall, ver logs de tráfico, configurar el proxy y mucho más.

En próximas entradas exploraremos la creación de reglas de firewall y cómo segmentar el tráfico entre zonas.

---

*¿Dudas o sugerencias? No dudes en dejar un comentario.*
