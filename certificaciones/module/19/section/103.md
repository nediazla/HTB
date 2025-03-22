# Service Enumeration

---

Para nosotros, es esencial determinar la aplicación y su versión con la mayor precisión posible. Podemos usar esta información para escanear vulnerabilidades conocidas y analizar el código fuente para esa versión si la encontramos. Un número de versión exacto nos permite buscar una exploit más precisa que se ajuste al servicio y al sistema operativo de nuestro objetivo.

---

# **Detección de versiones de servicio**

Se recomienda realizar un escaneo de puerto rápido primero, que nos brinda una pequeña descripción de los puertos disponibles. Esto causa significativamente menos tráfico, lo cual es ventajoso para nosotros porque de lo contrario podemos ser descubiertos y bloqueados por los mecanismos de seguridad. Podemos lidiar con estos primero y ejecutar un escaneo de puertos en segundo plano, que muestra todos los puertos abiertos (`-p-`). Podemos usar el escaneo de la versión para escanear los puertos específicos para los servicios y sus versiones (`-sV`).

Un escaneo de puerto completo lleva bastante tiempo. Para ver el estado de escaneo, podemos presionar el`[Space Bar]`durante el escaneo, que causará`Nmap`para mostrarnos el estado de escaneo.

Enumeración de servicio

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sVStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:44 CEST
[Space Bar]
Stats: 0:00:03 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 3.64% done; ETC: 19:45 (0:00:53 remaining)

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p-` | Escanea todos los puertos. |
| `-sV` | Realiza la detección de versiones de servicio en puertos especificados. |

---

Otra opción (`--stats-every=5s`) que podemos usar es definir cómo se deben mostrar los períodos de tiempo el estado. Aquí podemos especificar el número de segundos (`s`) o minutos (`m`), después de lo cual queremos obtener el estado.

Enumeración de servicio

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV --stats-every=5sStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 19:46 CEST
Stats: 0:00:05 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 13.91% done; ETC: 19:49 (0:00:31 remaining)
Stats: 0:00:10 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 39.57% done; ETC: 19:48 (0:00:15 remaining)

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p-` | Escanea todos los puertos. |
| `-sV` | Realiza la detección de versiones de servicio en puertos especificados. |
| `--stats-every=5s` | Muestra el progreso del escaneo cada 5 segundos. |

---

También podemos aumentar el`verbosity level` (`-v` / `-vv`), que nos mostrará los puertos abiertos directamente cuando`Nmap`los detecta.

Enumeración de servicio

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -v Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:03 CEST
NSE: Loaded 45 scripts for scanning.
Initiating ARP Ping Scan at 20:03
Scanning 10.129.2.28 [1 port]
Completed ARP Ping Scan at 20:03, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:03
Completed Parallel DNS resolution of 1 host. at 20:03, 0.02s elapsed
Initiating SYN Stealth Scan at 20:03
Scanning 10.129.2.28 [65535 ports]
Discovered open port 995/tcp on 10.129.2.28
Discovered open port 80/tcp on 10.129.2.28
Discovered open port 993/tcp on 10.129.2.28
Discovered open port 143/tcp on 10.129.2.28
Discovered open port 25/tcp on 10.129.2.28
Discovered open port 110/tcp on 10.129.2.28
Discovered open port 22/tcp on 10.129.2.28
<SNIP>

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p-` | Escanea todos los puertos. |
| `-sV` | Realiza la detección de versiones de servicio en puertos especificados. |
| `-v` | Aumenta la verbosidad del escaneo, que muestra información más detallada. |

---

# **Pancarta**

Una vez que se complete el escaneo, veremos todos los puertos TCP con el servicio correspondiente y sus versiones que están activas en el sistema.

Enumeración de servicio

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sVStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 20:00 CEST
Nmap scan report for 10.129.2.28
Host is up (0.013s latency).
Not shown: 65525 closed ports
PORT      STATE    SERVICE      VERSION
22/tcp    open     ssh          OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
25/tcp    open     smtp         Postfix smtpd
80/tcp    open     http         Apache httpd 2.4.29 ((Ubuntu))
110/tcp   open     pop3         Dovecot pop3d
139/tcp   filtered netbios-ssn
143/tcp   open     imap         Dovecot imapd (Ubuntu)
445/tcp   filtered microsoft-ds
993/tcp   open     ssl/imap     Dovecot imapd (Ubuntu)
995/tcp   open     ssl/pop3     Dovecot pop3d
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 91.73 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p-` | Escanea todos los puertos. |
| `-sV` | Realiza la detección de versiones de servicio en puertos especificados. |

---

Ante todo,`Nmap`Mira los pancartas de los puertos escaneados y los imprime. Si no puede identificar versiones a través de las pancartas,`Nmap`Intenta identificarlos a través de un sistema de correspondencia basado en la firma, pero esto aumenta significativamente la duración del escaneo. Una desventaja para`Nmap`Los resultados presentados es que el escaneo automático puede perder información porque a veces`Nmap`no sabe cómo manejarlo. Veamos un ejemplo de esto.

Enumeración de servicio

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -Pn -n --disable-arp-ping --packet-traceStarting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 20:10 CEST
<SNIP>
NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..
Service scan match (Probe NULL matched with NULL line 3104): 10.129.2.28:25 is smtp.  Version: |Postfix smtpd|||
NSOCK INFO [0.4200s] nsock_iod_delete(): nsock_iod_delete (IOD#1)Nmap scan report for 10.129.2.28
Host is up (0.076s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
Service Info: Host:  inlane

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.28` | Escanea el objetivo especificado. |
| `-p-` | Escanea todos los puertos. |
| `-sV` | Realiza la detección de versiones de servicio en puertos especificados. |
| `-Pn` | Desactiva las solicitudes de eco ICMP. |
| `-n` | Desactiva la resolución DNS. |
| `--disable-arp-ping` | Deshabilita arp ping. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos. |

---

Si observamos los resultados de`Nmap`, podemos ver el estado del puerto, el nombre del servicio y el nombre de host. Sin embargo, veamos esta línea aquí:

- `NSOCK INFO [0.4200s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 18 [10.129.2.28:25] (35 bytes): 220 inlane ESMTP Postfix (Ubuntu)..`

Luego vemos que el servidor SMTP en nuestro objetivo nos dio más información que`Nmap`nos mostró. Porque aquí, vemos que es la distribución de Linux`Ubuntu`. Ocurre porque, después de un exitoso apretón de manos de tres vías, el servidor a menudo envía un banner para la identificación. Esto sirve para informar al cliente con qué servicio está trabajando. A nivel de red, esto sucede con un`PSH`bandera en el encabezado TCP. Sin embargo, puede ocurrir que algunos servicios no proporcionan inmediatamente dicha información. También es posible eliminar o manipular las pancartas de los servicios respectivos. Si nosotros`manually`conectarse al servidor SMTP usando`nc`, agarra el banner e intercepta el tráfico de la red utilizando`tcpdump`, podemos ver que`Nmap`no nos mostró.

### **Tcpdump**

Enumeración de servicio

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 host 10.10.14.2 and 10.129.2.28tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes

```

### **Carolina del Norte**

Enumeración de servicio

```
xnoxos@htb[/htb]$  nc -nv 10.129.2.28 25Connection to 10.129.2.28 port 25 [tcp/*] succeeded!
220 inlane ESMTP Postfix (Ubuntu)

```

### **Tcpdump - tráfico interceptado**

Enumeración de servicio

```
18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], seq 1798872233, win 65535, options [mss 1460,nop,wscale 6,nop,nop,TS val 331260178 ecr 0,sackOK,eol], length 0
18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], seq 1130574379, ack 1798872234, win 65160, options [mss 1460,sackOK,TS val 1800383922 ecr 331260178,nop,wscale 7], length 0
18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 1, win 2058, options [nop,nop,TS val 331260304 ecr 1800383922], length 0
18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], seq 1:36, ack 1, win 510, options [nop,nop,TS val 1800383985 ecr 331260304], length 35: SMTP: 220 inlane ESMTP Postfix (Ubuntu)
18:28:07.319426 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], ack 36, win 2058, options [nop,nop,TS val 331260368 ecr 1800383985], length 0

```

Las primeras tres líneas nos muestran el apretón de manos de tres vías.

|  |  |  |
| --- | --- | --- |
| 1. | `[SYN]` | `18:28:07.128564 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [S], <SNIP>` |
| 2. | `[SYN-ACK]` | `18:28:07.255151 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [S.], <SNIP>` |
| 3. | `[ACK]` | `18:28:07.255281 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], <SNIP>` |

Después de eso, el servidor SMTP de destino nos envía un paquete TCP con el`PSH`y`ACK`banderas, donde`PSH`establece que el servidor de destino nos está enviando datos y con`ACK`Simultáneamente nos informa que se han enviado todos los datos requeridos.

|  |  |  |
| --- | --- | --- |
| 4. | `[PSH-ACK]` | `18:28:07.319306 IP 10.129.2.28.smtp > 10.10.14.2.59618: Flags [P.], <SNIP>` |

El último paquete TCP que enviamos confirma la recepción de los datos con un`ACK`.

|  |  |  |
| --- | --- | --- |
| 5. | `[ACK]` | `18:28:07.319426 IP 10.10.14.2.59618 > 10.129.2.28.smtp: Flags [.], <SNIP>` |