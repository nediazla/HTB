# Host and Port Scanning

Es esencial comprender cómo funciona la herramienta que usamos y cómo funciona y procesa las diferentes funciones. Solo entenderemos los resultados si sabemos lo que significan y cómo se obtienen. Por lo tanto, analizaremos más de cerca y analizaremos algunos de los métodos de escaneo. Después de haber descubierto que nuestro objetivo está vivo, queremos obtener una imagen más precisa del sistema. La información que necesitamos incluye:

- Abrir puertos y sus servicios
- Versiones de servicio
- Información que brindaron los servicios
- Sistema operativo

Hay un total de 6 estados diferentes para un puerto escaneado que podemos obtener:

| **Estado** | **Descripción** |
| --- | --- |
| `open` | Esto indica que se ha establecido la conexión con el puerto escaneado. Estas conexiones pueden ser**Conexiones TCP**, **Datagramas UDP**así como**Asociaciones SCTP**. |
| `closed` | Cuando el puerto se muestra como cerrado, el protocolo TCP indica que el paquete que recibimos contiene un`RST`bandera. Este método de escaneo también se puede usar para determinar si nuestro objetivo está vivo o no. |
| `filtered` | NMAP no puede identificar correctamente si el puerto escaneado está abierto o cerrado porque no se devuelve ninguna respuesta del objetivo para el puerto o obtenemos un código de error del objetivo. |
| `unfiltered` | Este estado de un puerto solo ocurre durante el**Tcp-contra**Escanee y significa que el puerto es accesible, pero no se puede determinar si está abierto o cerrado. |
| `open|filtered` | Si no obtenemos una respuesta para un puerto específico,`Nmap`lo establecerá en ese estado. Esto indica que un firewall o filtro de paquetes puede proteger el puerto. |
| `closed|filtered` | Este estado solo ocurre en el**ID de IP inactiva**escaneos e indica que era imposible determinar si el puerto escaneado está cerrado o filtrado por un firewall. |

---

# **Descubriendo puertos TCP abiertos**

Por defecto,`Nmap`escanea los 1000 puertos TCP con el syn scan (`-sS`). Este syn scan se establece solo en predeterminado cuando lo ejecutamos como raíz debido a los permisos de socket requeridos para crear paquetes TCP sin procesar. De lo contrario, el escaneo TCP (`-sT`) se realiza por defecto. Esto significa que si no definimos puertos y métodos de escaneo, estos parámetros se establecen automáticamente. Podemos definir los puertos uno por uno (`-p 22,25,80,139,445`), por rango (`-p 22-445`), por los puertos superiores (`--top-ports=10`) de la`Nmap`base de datos que se ha firmado como más frecuente, escaneando todos los puertos (`-p-`) pero también definiendo un escaneo de puerto rápido, que contiene los 100 puertos principales (`-F`).

### **Escaneo de los 10 puertos TCP principales**

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 --top-ports=10 Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:36 CEST
Nmap scan report for 10.129.2.28
Host is up (0.021s latency).

PORT     STATE    SERVICE
21/tcp   closed   ftp
22/tcp   open     ssh
23/tcp   closed   telnet
25/tcp   open     smtp
80/tcp   open     http
110/tcp  open     pop3
139/tcp  filtered netbios-ssn
443/tcp  closed   https
445/tcp  filtered microsoft-ds
3389/tcp closed   ms-wbt-server
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 1.44 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `--top-ports=10` | Escanea los puertos superiores especificados que se han definido como más frecuentes. |

---

Vemos que solo escaneamos los 10 puertos TCP principales de nuestro objetivo, y`Nmap`Muestra su estado en consecuencia. Si rastreamos los paquetes`Nmap`envía, veremos el`RST`ascender`TCP port 21`que nuestro objetivo nos envía de vuelta. Para tener una visión clara de Syn Scan, deshabilitamos las solicitudes de eco de ICMP (`-Pn`), Resolución DNS (`-n`), y arp Ping Scan (`--disable-arp-ping`).

### **Nmap - traza los paquetes**

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p 21 --packet-trace -Pn -n --disable-arp-pingStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:39 CEST
SENT (0.0429s) TCP 10.10.14.2:63090 > 10.129.2.28:21 S ttl=56 id=57322 iplen=44  seq=1699105818 win=1024 <mss 1460>
RCVD (0.0573s) TCP 10.129.2.28:21 > 10.10.14.2:63090 RA ttl=64 id=0 iplen=40  seq=0 win=0
Nmap scan report for 10.11.1.28
Host is up (0.014s latency).

PORT   STATE  SERVICE
21/tcp closed ftp
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p 21` | Escanea solo el puerto especificado. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |

---

Podemos ver en la línea enviada que nosotros (`10.10.14.2`) envió un paquete TCP con el`SYN`bandera (`S`) a nuestro objetivo (`10.129.2.28`). En la siguiente línea RCVD, podemos ver que el objetivo responde con un paquete TCP que contiene el`RST`y`ACK`banderas (`RA`). `RST`y`ACK`Las banderas se utilizan para reconocer la recepción del paquete TCP (`ACK`) y para finalizar la sesión TCP (`RST`).

### **Pedido**

| **Mensaje** | **Descripción** |
| --- | --- |
| `SENT (0.0429s)` | Indica la operación enviada de NMAP, que envía un paquete al objetivo. |
| `TCP` | Muestra el protocolo que se está utilizando para interactuar con el puerto objetivo. |
| `10.10.14.2:63090 >` | Representa nuestra dirección IPv4 y el puerto de origen, que NMAP utilizará para enviar los paquetes. |
| `10.129.2.28:21` | Muestra la dirección IPv4 de destino y el puerto de destino. |
| `S` | Syn Flag del paquete TCP enviado. |
| `ttl=56 id=57322 iplen=44 seq=1699105818 win=1024 mss 1460` | Parámetros adicionales del encabezado TCP. |

### **Respuesta**

| **Mensaje** | **Descripción** |
| --- | --- |
| `RCVD (0.0573s)` | Indica un paquete recibido del objetivo. |
| `TCP` | Muestra el protocolo que se está utilizando. |
| `10.129.2.28:21 >` | Representa los objetivos La dirección IPv4 y el puerto de origen, que se utilizará para responder. |
| `10.10.14.2:63090` | Muestra nuestra dirección IPv4 y el puerto al que se responderá. |
| `RA` | Las banderas RST y ACK del paquete TCP enviado. |
| `ttl=64 id=0 iplen=40 seq=0 win=0` | Parámetros adicionales del encabezado TCP. |

### **ESCANILLA CONECTA**

El nmap[TCP Connect Scan](https://nmap.org/book/scan-methods-connect-scan.html) (`-sT`) usa el apretón de manos de tres vías TCP para determinar si un puerto específico en un host de destino está abierto o cerrado. El escaneo envía un`SYN`Empaque al puerto de destino y espera una respuesta. Se considera abierto si el puerto de destino responde con un`SYN-ACK`paquete y cerrado si responde con un`RST`paquete.

El`Connect`El escaneo (también conocido como un escaneo de conexión TCP completo) es muy preciso porque completa el apretón de manos TCP de tres vías, lo que nos permite determinar el estado exacto de un puerto (abierto, cerrado o filtrado). Sin embargo, no es el más sigiloso. De hecho, el ESCHE CONNECT es una de las técnicas menos sigilosas, ya que establece completamente una conexión, que crea registros en la mayoría de los sistemas y es fácilmente detectado por las soluciones modernas de IDS/IPS. Dicho esto, la exploración de conexión puede ser útil en ciertas situaciones, particularmente cuando la precisión es una prioridad, y el objetivo es mapear la red sin causar una interrupción significativa a los servicios. Dado que el escaneo establece completamente una conexión TCP, interactúa limpiamente con los servicios, lo que hace que sea menos probable que cause errores de servicio o inestabilidad en comparación con escaneos más intrusivos. Si bien no es el método más sigiloso, a veces se considera una exploración más "educada" porque se comporta como una conexión de cliente normal, con un impacto mínimo en los servicios objetivo.

También es útil cuando el host Target tiene un firewall personal que deja caer paquetes entrantes pero permite paquetes salientes. En este caso, un escaneo de conexión puede omitir el firewall y determinar con precisión el estado de los puertos de destino. Sin embargo, es importante tener en cuenta que el escaneo de conexión es más lento que otros tipos de escaneos porque requiere que el escáner espere una respuesta del objetivo después de cada paquete que envía, lo que podría llevar algún tiempo si el objetivo está ocupado o no respondía.

Los escaneos como Syn Scan (también conocido como un escaneo medio abierto) generalmente se consideran más sigilosos porque no completan el apretón de manos completo, dejando la conexión incompleta después de enviar el paquete SYN inicial. Esto minimiza la posibilidad de activar los registros de conexión mientras aún recopila información del estado del puerto. Sin embargo, los sistemas avanzados de IDS/IPS se han adaptado para detectar incluso estas técnicas más sutiles.

### **Conecte el escaneo en el puerto TCP 443**

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p 443 --packet-trace --disable-arp-ping -Pn -n --reason -sT Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:26 CET
CONN (0.0385s) TCP localhost > 10.129.2.28:443 => Operation now in progress
CONN (0.0396s) TCP localhost > 10.129.2.28:443 => Connected
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.013s latency).

PORT    STATE SERVICE REASON
443/tcp open  https   syn-ack

Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds

```

---

# **Puertos filtrados**

Cuando se muestra un puerto como filtrado, puede tener varias razones. En la mayoría de los casos, los firewalls tienen ciertas reglas establecidas para manejar conexiones específicas. Los paquetes pueden ser`dropped`, o`rejected`. Cuando se deja caer un paquete,`Nmap`no recibe respuesta de nuestro objetivo y, por defecto, la tasa de reintento (`--max-retries`) está configurado en`10`. Esto significa`Nmap`Reenviará la solicitud al puerto de destino para determinar si el paquete anterior fue accidentalmente mal manejado o no.

Veamos un ejemplo donde el firewall`drops`Los paquetes TCP que enviamos para el escaneo de puertos. Por lo tanto, escaneamos el puerto TCP**139**, que ya se mostró como filtrado. Para poder rastrear cómo se manejan nuestros paquetes enviados, desactivamos las solicitudes de eco ICMP (`-Pn`), Resolución DNS (`-n`), y arp Ping Scan (`--disable-arp-ping`) de nuevo.

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p 139 --packet-trace -n --disable-arp-ping -PnStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:45 CEST
SENT (0.0381s) TCP 10.10.14.2:60277 > 10.129.2.28:139 S ttl=47 id=14523 iplen=44  seq=4175236769 win=1024 <mss 1460>
SENT (1.0411s) TCP 10.10.14.2:60278 > 10.129.2.28:139 S ttl=45 id=7372 iplen=44  seq=4175171232 win=1024 <mss 1460>
Nmap scan report for 10.129.2.28
Host is up.

PORT    STATE    SERVICE
139/tcp filtered netbios-ssn
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p 139` | Escanea solo el puerto especificado. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |
| `-Pn` | Desactiva las solicitudes de eco ICMP. |

---

Vemos en el último escaneo que`Nmap`Envió dos paquetes TCP con la bandera SYN. Por la duración (`2.06s`) del escaneo, podemos reconocer que tomó mucho más tiempo que los anteriores (`~0.05s`). El caso es diferente si el firewall rechaza los paquetes. Para esto, miramos el puerto TCP`445`, que se maneja en consecuencia por tal regla del firewall.

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p 445 --packet-trace -n --disable-arp-ping -PnStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 15:55 CEST
SENT (0.0388s) TCP 10.129.2.28:52472 > 10.129.2.28:445 S ttl=49 id=21763 iplen=44  seq=1418633433 win=1024 <mss 1460>
RCVD (0.0487s) ICMP [10.129.2.28 > 10.129.2.28 Port 445 unreachable (type=3/code=3) ] IP [ttl=64 id=20998 iplen=72 ]
Nmap scan report for 10.129.2.28
Host is up (0.0099s latency).

PORT    STATE    SERVICE
445/tcp filtered microsoft-ds
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p 445` | Escanea solo el puerto especificado. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |
| `-Pn` | Desactiva las solicitudes de eco ICMP. |

Como respuesta, recibimos un`ICMP`responder con`type 3`y`error code 3`, que indica que el puerto deseado no es posible. Sin embargo, si sabemos que el anfitrión está vivo, podemos asumir fuertemente que el firewall en este puerto está rechazando los paquetes, y tendremos que echar un vistazo más de cerca a este puerto más adelante.

---

# **Descubriendo puertos UDP abiertos**

Algunos administradores del sistema a veces olvidan filtrar los puertos UDP además de los TCP. Desde`UDP`es un`stateless protocol`y no requiere un apretón de manos de tres vías como TCP. No recibimos ningún reconocimiento. En consecuencia, el tiempo de espera es mucho más largo, haciendo el conjunto`UDP scan` (`-sU`) Mucho más lento que el`TCP scan` (`-sS`).

Veamos un ejemplo de qué escaneo UDP (`-sU`) puede verse y qué resultados nos da.

### **Escaneo de puertos UDP**

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -F -sUStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:01 CEST
Nmap scan report for 10.129.2.28
Host is up (0.059s latency).
Not shown: 95 closed ports
PORT     STATE         SERVICE
68/udp   open|filtered dhcpc
137/udp  open          netbios-ns
138/udp  open|filtered netbios-dgm
631/udp  open|filtered ipp
5353/udp open          zeroconf
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 98.07 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-F` | Escanea los 100 puertos principales. |
| `-sU` | Realiza un escaneo UDP. |

---

Otra desventaja de esto es que a menudo no recibimos una respuesta porque`Nmap`Envía datagramas vacíos a los puertos UDP escaneados, y no recibimos ninguna respuesta. Por lo tanto, no podemos determinar si el paquete UDP ha llegado en absoluto o no. Si el puerto UDP es`open`, solo obtenemos una respuesta si la aplicación está configurada para hacerlo.

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 137 --reason Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:15 CEST
SENT (0.0367s) UDP 10.10.14.2:55478 > 10.129.2.28:137 ttl=57 id=9122 iplen=78
RCVD (0.0398s) UDP 10.129.2.28:137 > 10.10.14.2:55478 ttl=64 id=13222 iplen=257
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.0031s latency).

PORT    STATE SERVICE    REASON
137/udp open  netbios-ns udp-response ttl 64
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 0.04 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-sU` | Realiza un escaneo UDP. |
| `-Pn` | Desactiva las solicitudes de eco ICMP. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |
| `-p 137` | Escanea solo el puerto especificado. |
| `--reason` | Muestra la razón por la cual un puerto está en un estado particular. |

---

Si obtenemos una respuesta ICMP con`error code 3`(puerto inalcanzable), sabemos que el puerto es de hecho`closed`.

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 100 --reason Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:25 CEST
SENT (0.0445s) UDP 10.10.14.2:63825 > 10.129.2.28:100 ttl=57 id=29925 iplen=28
RCVD (0.1498s) ICMP [10.129.2.28 > 10.10.14.2 Port unreachable (type=3/code=3) ] IP [ttl=64 id=11903 iplen=56 ]
Nmap scan report for 10.129.2.28
Host is up, received user-set (0.11s latency).

PORT    STATE  SERVICE REASON
100/udp closed unknown port-unreach ttl 64
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in  0.15 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-sU` | Realiza un escaneo UDP. |
| `-Pn` | Desactiva las solicitudes de eco ICMP. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |
| `-p 100` | Escanea solo el puerto especificado. |
| `--reason` | Muestra la razón por la cual un puerto está en un estado particular. |

---

Para todas las demás respuestas de ICMP, los puertos escaneados están marcados como (`open|filtered`).

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -sU -Pn -n --disable-arp-ping --packet-trace -p 138 --reason Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 16:32 CEST
SENT (0.0380s) UDP 10.10.14.2:52341 > 10.129.2.28:138 ttl=50 id=65159 iplen=28
SENT (1.0392s) UDP 10.10.14.2:52342 > 10.129.2.28:138 ttl=40 id=24444 iplen=28
Nmap scan report for 10.129.2.28
Host is up, received user-set.

PORT    STATE         SERVICE     REASON
138/udp open|filtered netbios-dgm no-response
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)

Nmap done: 1 IP address (1 host up) scanned in 2.06 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-sU` | Realiza un escaneo UDP. |
| `-Pn` | Desactiva las solicitudes de eco ICMP. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |
| `-p 138` | Escanea solo el puerto especificado. |
| `--reason` | Muestra la razón por la cual un puerto está en un estado particular. |

Otro método útil para escanear puertos es el`-sV`Opción que se utiliza para obtener información disponible adicional de los puertos abiertos. Este método puede identificar versiones, nombres de servicios y detalles sobre nuestro objetivo.

### **Escaneo**

Escaneo de host y puerto

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -Pn -n --disable-arp-ping --packet-trace -p 445 --reason  -sVStarting Nmap 7.80 ( https://nmap.org ) at 2022-11-04 11:10 GMT
SENT (0.3426s) TCP 10.10.14.2:44641 > 10.129.2.28:445 S ttl=55 id=43401 iplen=44  seq=3589068008 win=1024 <mss 1460>
RCVD (0.3556s) TCP 10.129.2.28:445 > 10.10.14.2:44641 SA ttl=63 id=0 iplen=44  seq=2881527699 win=29200 <mss 1337>
NSOCK INFO [0.4980s] nsock_iod_new2(): nsock_iod_new (IOD#1)NSOCK INFO [0.4980s] nsock_connect_tcp(): TCP connection requested to 10.129.2.28:445 (IOD#1) EID 8NSOCK INFO [0.5130s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.2.28:445]
Service scan sending probe NULL to 10.129.2.28:445 (tcp)
NSOCK INFO [0.5130s] nsock_read(): Read request from IOD#1 [10.129.2.28:445] (timeout: 6000ms) EID 18NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: READ TIMEOUT for EID 18 [10.129.2.28:445]
Service scan sending probe SMBProgNeg to 10.129.2.28:445 (tcp)
NSOCK INFO [6.5190s] nsock_write(): Write request for 168 bytes to IOD#1 EID 27 [10.129.2.28:445]NSOCK INFO [6.5190s] nsock_read(): Read request from IOD#1 [10.129.2.28:445] (timeout: 5000ms) EID 34NSOCK INFO [6.5190s] nsock_trace_handler_callback(): Callback: WRITE SUCCESS for EID 27 [10.129.2.28:445]
NSOCK INFO [6.5320s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 34 [10.129.2.28:445] (135 bytes)
Service scan match (Probe SMBProgNeg matched with SMBProgNeg line 13836): 10.129.2.28:445 is netbios-ssn.  Version: |Samba smbd|3.X - 4.X|workgroup: WORKGROUP|
NSOCK INFO [6.5320s] nsock_iod_delete(): nsock_iod_delete (IOD#1)Nmap scan report for 10.129.2.28
Host is up, received user-set (0.013s latency).

PORT    STATE SERVICE     REASON         VERSION
445/tcp open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: Ubuntu

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.55 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-Pn` | Desactiva las solicitudes de eco ICMP. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |
| `-p 445` | Escanea solo el puerto especificado. |
| `--reason` | Muestra la razón por la cual un puerto está en un estado particular. |
| `-sV` | Realiza un escaneo de servicio. |

Más información sobre las técnicas de escaneo de puertos que podemos encontrar en:[https://nmap.org/book/man-port-scanning-techniques.html](https://nmap.org/book/man-port-scanning-techniques.html)