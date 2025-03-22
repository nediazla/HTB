# Host Discovery

Cuando necesitamos realizar una prueba de penetración interna para toda la red de una empresa, por ejemplo, entonces deberíamos, en primer lugar, obtener una visión general de los sistemas en línea con los que podemos trabajar. Para descubrir activamente tales sistemas en la red, podemos usar varios`Nmap`Opciones de descubrimiento del anfitrión. Hay muchas opciones`Nmap`proporciona para determinar si nuestro objetivo está vivo o no. El método de descubrimiento del host más efectivo es usar**Solicitudes de eco de ICMP**, que analizaremos.

Siempre se recomienda almacenar cada escaneo. Esto se puede usar más tarde para comparación, documentación e informes. Después de todo, diferentes herramientas pueden producir resultados diferentes. Por lo tanto, puede ser beneficioso distinguir qué herramienta produce qué resulta.

### **Escanear el rango de red**

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f510.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.0/24` | Rango de red de destino. |
| `-sn` | Desactiva el escaneo de puertos. |
| `-oA tnet` | Almacena los resultados en todos los formatos que comienzan con el nombre 'tnet'. |

Este método de escaneo solo funciona si los firewalls de los hosts lo permiten. De lo contrario, podemos usar otras técnicas de escaneo para averiguar si los hosts están activos o no. Echaremos un vistazo más de cerca a estas técnicas en "`Firewall and IDS Evasion`".

---

# **Escanear la lista de IP**

Durante una prueba de penetración interna, no es raro que se nos proporcione una lista de IP con los hosts que necesitamos probar.`Nmap`También nos da la opción de trabajar con listas y leer los hosts de esta lista en lugar de definirlos o escribirlos manualmente.

Tal lista podría verse algo así:

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ cat hosts.lst10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28

```

Si usamos la misma técnica de escaneo en la lista predefinida, el comando se verá así:

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f510.129.2.18
10.129.2.19
10.129.2.20

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `-sn` | Desactiva el escaneo de puertos. |
| `-oA tnet` | Almacena los resultados en todos los formatos que comienzan con el nombre 'tnet'. |
| `-iL` | Realiza escaneos definidos contra objetivos en la lista proporcionada de 'hosts.lst'. |

En este ejemplo, vemos que solo 3 de 7 hosts están activos. Recuerde, esto puede significar que los otros hosts ignoran el valor predeterminado**Solicitudes de eco de ICMP**Debido a sus configuraciones de firewall. Desde`Nmap`no recibe una respuesta, marca esos hosts como inactivos.

---

# **Escanear múltiples IPS**

También puede suceder que solo necesitemos escanear una pequeña parte de una red. Una alternativa al método que utilizamos la última vez es especificar múltiples direcciones IP.

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20| grep for | cut -d" " -f510.129.2.18
10.129.2.19
10.129.2.20

```

Si estas direcciones IP están una al lado de la otra, también podemos definir el rango en el octeto respectivo.

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap -sn -oA tnet 10.129.2.18-20| grep for | cut -d" " -f510.129.2.18
10.129.2.19
10.129.2.20

```

---

# **Escanear una sola IP**

Antes de escanear un solo host para puertos abiertos y sus servicios, primero tenemos que determinar si está vivo o no. Para esto, podemos usar el mismo método que antes.

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-14 23:59 CEST
Nmap scan report for 10.129.2.18
Host is up (0.087s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.18` | Realiza escaneos definidos contra el objetivo. |
| `-sn` | Desactiva el escaneo de puertos. |
| `-oA host` | Almacena los resultados en todos los formatos que comienzan con el nombre 'host'. |

Si deshabilitamos el escaneo de puertos (`-sn`), Nmap automáticamente escaneo con`ICMP Echo Requests` (`-PE`). Una vez que se envía dicha solicitud, generalmente esperamos un`ICMP reply`Si el anfitrión de Pinging está vivo. El hecho más interesante es que nuestros escaneos anteriores no hicieron eso porque antes de que NMAP pudiera enviar una solicitud de eco de ICMP, enviaría un`ARP ping`resultando en un`ARP reply`. Podemos confirmar esto con el "`--packet-trace`"Opción. Para garantizar que se envíen solicitudes de eco ICMP, también definimos la opción (`-PE`) para esto.

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:08 CEST
SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
Nmap scan report for 10.129.2.18
Host is up (0.023s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.18` | Realiza escaneos definidos contra el objetivo. |
| `-sn` | Desactiva el escaneo de puertos. |
| `-oA host` | Almacena los resultados en todos los formatos que comienzan con el nombre 'host'. |
| `-PE` | Realiza el escaneo de ping utilizando 'solicitudes ICMP Echo' contra el objetivo. |
| `--packet-trace` | Muestra todos los paquetes enviados y recibidos |

---

Otra forma de determinar por qué NMAP tiene nuestro objetivo marcado como "vivo" es con el "`--reason`" opción.

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --reason Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:10 CEST
SENT (0.0074s) ARP who-has 10.129.2.18 tell 10.10.14.2
RCVD (0.0309s) ARP reply 10.129.2.18 is-at DE:AD:00:00:BE:EF
Nmap scan report for 10.129.2.18
Host is up, received arp-response (0.028s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.18` | Realiza escaneos definidos contra el objetivo. |
| `-sn` | Desactiva el escaneo de puertos. |
| `-oA host` | Almacena los resultados en todos los formatos que comienzan con el nombre 'host'. |
| `-PE` | Realiza el escaneo de ping utilizando 'solicitudes ICMP Echo' contra el objetivo. |
| `--reason` | Muestra el motivo de un resultado específico. |

---

Vemos aquí que`Nmap`De hecho, detecta si el anfitrión está vivo o no a través del`ARP request`y`ARP reply`solo. Para deshabilitar las solicitudes de ARP y escanear nuestro objetivo con el deseado`ICMP echo requests`, podemos deshabilitar pings arp configurando el "`--disable-arp-ping`"Opción. Luego podemos escanear nuestro objetivo nuevamente y mirar los paquetes enviados y recibidos.

Descubrimiento del anfitrión

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:12 CEST
SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
Nmap scan report for 10.129.2.18
Host is up (0.086s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds

```

Ya hemos mencionado en el "`Learning Process`, "y al comienzo de este módulo, es esencial prestar atención a los detalles.`ICMP echo request`puede ayudarnos a determinar si nuestro objetivo está vivo e identificar su sistema. Se pueden encontrar más estrategias sobre el descubrimiento de huéspedes en:

[https://nmap.org/book/host-discovery-strategies.html](https://nmap.org/book/host-discovery-strategies.html)