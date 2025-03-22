# Filtrado de paquetes tcpdump

TCPDUMP proporciona una forma robusta y eficiente de analizar los datos incluidos en nuestras capturas a través de filtros de paquetes. Esta sección examinará esos filtros y vislumbrará cómo modifica la salida de nuestra captura.

---

# **Filtrado y opciones de sintaxis avanzadas**

La utilización de opciones de filtrado más avanzadas como las que se enumeran a continuación nos permitirá recortar qué tráfico se imprime a la salida o enviado a Archivo. Al reducir la cantidad de información que capturamos y escribimos en el disco, podemos ayudar a reducir el espacio necesario para escribir el archivo y ayudar a los datos del proceso de buffer más rápido. Los filtros pueden ser útiles cuando se combinan con opciones de sintaxis TCPDump estándar. Podemos capturar tan ampliamente como deseamos, o ser súper específicos solo para capturar paquetes de un host en particular, o incluso con un bit en particular en el encabezado TCP establecido en ON. Se recomienda explorar los filtros más avanzados y encontrar diferentes combinaciones.

Estos filtros y operadores avanzados de ninguna manera son una lista exhaustiva. Fueron elegidos porque son los más utilizados y nos pondrán en funcionamiento rápidamente. Cuando se implementan, estos filtros inspeccionarán cualquier paquete capturado y buscarán los valores dados en el encabezado del protocolo para que coincida.

### **Filtros TCPDump útiles**

| **Filtrar** | **Resultado** |
| --- | --- |
| anfitrión | `host` filtrará el tráfico visible para mostrar cualquier cosa que involucre al host designado. Bidireccional |
| SRC / Dest | `src` y`dest`son modificadores. Podemos usarlos para designar un host o puerto de origen o destino. |
| net | `net` will show us any traffic sourcing from or destined to the network designated. It uses / notation. |
| proto | se filtrará para un tipo de protocolo específico. (Ether, TCP, UDP e ICMP como ejemplos) |
| puerto | `port` es bidireccional. Mostrará cualquier tráfico con el puerto especificado como fuente o destino. |
| retraso | `portrange` nos permite especificar una gama de puertos. (0-1024) |
| menos / mayor "<>" | `less` y `greater` Se puede usar para buscar una opción de paquete o protocolo de un tamaño específico. |
| y / && | `and` `&&` se puede usar para concatenar dos filtros diferentes juntos. Por ejemplo, SRC Host and Port. |
| o | `or` Permite una coincidencia en cualquiera de las dos condiciones. No tiene que cumplir con ambos. Puede ser complicado. |
| no | `not` es un modificador que dice cualquier cosa menos x. Por ejemplo, no UDP. |

Con estos filtros, podemos filtrar el tráfico de red en la mayoría de las propiedades para facilitar el análisis. Veamos algunos ejemplos de estos filtros y cómo se ven cuando los usamos. Al usar el `host` Filtro, cualquier IP que ingresemos se verificará en el campo IP de origen o destino. Esto se puede ver en la salida a continuación.

### **Filtro de host**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: host [IP]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 host 172.16.146.2tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:50:53.072536 IP 172.16.146.2.48738 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 3400465007:3400465044, ack 254421756, win 501, options [nop,nop,TS val 220968655 ecr 80852594], length 37
14:50:53.108740 IP 172.16.146.2.55606 > 172.67.1.1.https: Flags [P.], seq 4227143181:4227143273, ack 1980233980, win 21975, length 92
14:50:53.173084 IP 172.67.1.1.https > 172.16.146.2.55606: Flags [.], ack 92, win 69, length 0
14:50:53.175017 IP 172.16.146.2.35744 > 172.16.146.1.domain: 55991+ PTR? 148.199.31.52.in-addr.arpa. (44)
14:50:53.175714 IP 172.16.146.1.domain > 172.16.146.2.35744: 55991 1/0/0 PTR ec2-52-31-199-148.eu-west-1.compute.amazonaws.com. (107)

```

Este filtro a menudo se usa cuando queremos examinar solo un host o servidor específico. Con esto, podemos identificar con quién se comunica este host o servidor y de qué manera. Según nuestras configuraciones de red, entenderemos si esta conexión es legítima. Si la comunicación parece extraña, podemos usar otros filtros y opciones para ver el contenido con más detalle. Además de los hosts individuales, también podemos definir el host de origen y el host de destino. También podemos definir redes completas y sus puertos.

### **Filtro de origen/destino**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: src/dst [host|net|port] [IP|Network Range|Port]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 src host 172.16.146.2
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:53:36.199628 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 1428378231:1428378268, ack 3778572066, win 501, options [nop,nop,TS val 221131782 ecr 80889856], length 37
14:53:36.203166 IP 172.16.146.2.55606 > 172.67.1.1.https: Flags [P.], seq 4227144035:4227144103, ack 1980235221, win 21975, length 68
14:53:36.267059 IP 172.16.146.2.36424 > 172.16.146.1.domain: 40873+ PTR? 148.199.31.52.in-addr.arpa. (44)
14:53:36.267880 IP 172.16.146.2.51151 > 172.16.146.1.domain: 10032+ PTR? 2.146.16.172.in-addr.arpa. (43)
14:53:36.276425 IP 172.16.146.2.46588 > 172.16.146.1.domain: 28357+ PTR? 1.1.67.172.in-addr.arpa. (41)
14:53:36.337722 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 34, win 501, options [nop,nop,TS val 221131920 ecr 80899875], length 0
14:53:36.338841 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 65, win 501, options [nop,nop,TS val 221131921 ecr 80899875], length 0
14:53:36.339273 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 37:68, ack 66, win 501, options [nop,nop,TS val 221131922 ecr 80899875], length 31
14:53:36.339334 IP 172.16.146.2.48766 > ec2-52-31-199-148.eu-west-1.compute.amazonaws.com.https: Flags [F.], seq 68, ack 66, win 501, options [nop,nop,TS val 221131922 ecr 80899875], length 0
14:53:36.370791 IP 172.16.146.2.32972 > 172.16.146.1.domain: 3856+ PTR? 1.146.16.172.in-addr.arpa. (43)

```

El origen y el destino nos permiten trabajar con las instrucciones de comunicación. Por ejemplo, en la última salida, hemos especificado que nuestro `source` el anfitrión es `172.16.146.2`, y solo los paquetes enviados desde este host serán interceptados. Esto se puede hacer para puertos y rangos de red también. Un ejemplo de esta utilización `src port #` se vería algo así:

### **Utilizar la fuente con puerto como filtro**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 tcp src port 8006:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:08.993643 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1:1381, ack 480, win 6432, length 1380: HTTP: HTTP/1.1 200 OK
06:17:09.123830 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1381:2761, ack 480, win 6432, length 1380: HTTP
06:17:09.754737 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 2761:4141, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [P.], seq 4141:5521, ack 480, win 6432, length 1380: HTTP
06:17:09.945011 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 5521:6901, ack 480, win 6432, length 1380: HTTP

```

¿Observe ahora que solo vemos un lado de la conversación? Esto se debe a que estamos filtrando en el puerto de origen de 80 (http). Utilizado de esta manera,`net`agarrará cualquier cosa que coincida con el `/` Notación para una red. En el ejemplo, estamos buscando algo destinado para el `172.16.146.0/24` red.

### **Uso de destino en combinación con el filtro neto**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 dest net 172.16.146.0/24tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
16:33:14.376003 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], ack 1486880537, win 316, options [nop,nop,TS val 2311579424 ecr 263866084], length 0
16:33:14.442123 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 0:385, ack 1, win 316, options [nop,nop,TS val 2311579493 ecr 263866084], length 385
16:33:14.442188 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 385:1803, ack 1, win 316, options [nop,nop,TS val 2311579493 ecr 263866084], length 1418
16:33:14.442223 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 1803:4639, ack 1, win 316, options [nop,nop,TS val 2311579494 ecr 263866084], length 2836
16:33:14.443161 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [P.], seq 4639:5817, ack 1, win 316, options [nop,nop,TS val 2311579495 ecr 263866084], length 1178
16:33:14.443199 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 5817:8653, ack 1, win 316, options [nop,nop,TS val 2311579495 ecr 263866084], length 2836
16:33:14.444407 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 8653:10071, ack 1, win 316, options [nop,nop,TS val 2311579497 ecr 263866084], length 1418
16:33:14.445479 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 10071:11489, ack 1, win 316, options [nop,nop,TS val 2311579497 ecr 263866084], length 1418
16:33:14.445531 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 11489:12907, ack 1, win 316, options [nop,nop,TS val 2311579498 ecr 263866084], length 1418
16:33:14.446955 IP 64.233.177.103.443 > 172.16.146.2.36050: Flags [.], seq 12907:14325, ack 1, win 316, options [nop,nop,TS val 2311579498 ecr 263866084], length 1418

```

Este filtro puede utilizar el nombre o el número de protocolo común del protocolo para cualquier protocolo IP, IPv6 o Ethernet. Los ejemplos comunes serían `tcp[6], udp[17], or icmp[1]`. En las salidas a continuación, utilizaremos tanto el nombre común (superior) como el número de protocolo (abajo). Podemos ver que produjo la misma salida. En su mayor parte, estos son intercambiables, pero utilizan`proto`se volverá más útil cuando comience a diseccionar una parte específica de la IP u otros encabezados de protocolo. Será más evidente más adelante en esta sección cuando hablemos de buscar banderas TCP. Podemos echar un vistazo a esto[recurso](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) Para una lista útil que cubra los números de protocolo.

### **Filtro de protocolo - Nombre común**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: [tcp/udp/icmp]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 udp06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3009 > 145.253.2.203.domain: 35+ A? pagead2.googlesyndication.com. (47)
06:17:10.225414 IP 145.253.2.203.domain > dialin-145-254-160-237.pools.arcor-ip.net.3009: 35 4/0/0 CNAME pagead2.google.com., CNAME pagead.google.akadns.net., A 216.239.59.104, A 216.239.59.99 (146)

```

### **Filtro de protocolo - Número**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: proto [protocol number]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 proto 1706:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3009 > 145.253.2.203.domain: 35+ A? pagead2.googlesyndication.com. (47)
06:17:10.225414 IP 145.253.2.203.domain > dialin-145-254-160-237.pools.arcor-ip.net.3009: 35 4/0/0 CNAME pagead2.google.com., CNAME pagead.google.akadns.net., A 216.239.59.104, A 216.239.59.99 (146)

```

Usando el `port` Filtrar, debemos tener en cuenta lo que estamos buscando y cómo funciona ese protocolo. Algunos protocolos estándar como HTTP o HTTPS solo usan puertos 80 y 443 con el protocolo de transporte de TCP. Con eso en mente, los puertos de imágenes como una forma simple de establecer conexiones y protocolos como TCP y UDP para determinar si utilizan un método establecido. Los puertos por sí mismos se pueden usar para cualquier cosa, por lo que filtrar en el puerto 80 mostrará todo el tráfico sobre ese número de puerto. Sin embargo, si buscamos capturar todo el tráfico HTTP, utilizando `tcp port 80` Se asegurará de que solo veamos el tráfico HTTP.

Con protocolos que usan TCP y UDP para diferentes funciones, como DNS, podemos filtrar mirando uno u otro `TCP/UDP port 53` o filtrar para `port 53`. Al hacer esto, veremos cualquier tráfico que utilice ese puerto, independientemente del protocolo de transporte.

### **Filtro de puerto**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: port [port number]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 tcp port 44306:17:07.311224 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [S], seq 951057939, win 8760, options [mss 1460,nop,nop,sackOK], length 0
06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1, win 9660, length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [P.], seq 1:480, ack 1, win 9660, length 479: HTTP: GET /download.html HTTP/1.1
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:08.993643 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1:1381, ack 480, win 6432, length 1380: HTTP: HTTP/1.1 200 OK
06:17:09.123830 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1381, win 9660, length 0
06:17:09.123830 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 1381:2761, ack 480, win 6432, length 1380: HTTP
06:17:09.324118 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 2761, win 9660, length 0
06:17:09.754737 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 2761:4141, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [P.], seq 4141:5521, ack 480, win 6432, length 1380: HTTP
06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 5521, win 9660, length 0
06:17:09.945011 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 5521:6901, ack 480, win 6432, length 1380: HTTP
06:17:10.125270 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 6901, win 9660, length 0
06:17:10.205385 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], seq 6901:8281, ack 480, win 6432, length 1380: HTTP
06:17:10.295515 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [P.], seq 918691368:918692089, ack 778785668, win 8760, length 721: HTTP: GET /pagead/ads?client=ca-pub-2309191948673629&random=1084443430285&lmt=1082467020&format=468x60_as&output=html&url=http%3A%2F%2Fwww.ethereal.com%2Fdownload.html&color_bg=FFFFFF&color_text=333333&color_link=000000&color_url=666633&color_border=666633 HTTP/1.1

```

Además de los puertos individuales, también podemos definir rangos específicos de estos puertos, que luego se escuchan por TCPDUM. Escuchar en una variedad de puertos puede ser especialmente útil cuando vemos tráfico de red desde puertos que no coinciden con los servicios que se ejecutan en nuestros servidores. Por ejemplo, si tenemos un servidor web con puertos TCP 80 y 443 que se ejecutan en un segmento particular de nuestra red y de repente tenemos tráfico de red saliente desde el puerto TCP 10000 u otros, es muy sospechoso.

El `portrange` El filtro, como se ve a continuación, nos permite ver todo desde el rango de puerto. En el ejemplo, vemos algunos tráfico DNS junto con algunas solicitudes web HTTP.

### **Filtro de rango de puertos**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: portrange [portrange 0-65535]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 portrange 0-1024tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
13:10:35.092477 IP 172.16.146.1.domain > 172.16.146.2.32824: 47775 1/0/0 CNAME autopush.prod.mozaws.net. (81)
13:10:35.093217 IP 172.16.146.2.48078 > 172.16.146.1.domain: 30234+ A? ocsp.pki.goog. (31)
13:10:35.093334 IP 172.16.146.2.48078 > 172.16.146.1.domain: 32024+ AAAA? ocsp.pki.goog. (31)
13:10:35.136255 IP 172.16.146.1.domain > 172.16.146.2.48078: 32024 2/0/0 CNAME pki-goog.l.google.com., AAAA 2607:f8b0:4002:c09::5e (94)
13:10:35.137348 IP 172.16.146.1.domain > 172.16.146.2.48078: 30234 2/0/0 CNAME pki-goog.l.google.com., A 172.217.164.67 (82)
13:10:35.137989 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [S], seq 1146136517, win 64240, options [mss 1460,sackOK,TS val 1337520268 ecr 0,nop,wscale 7], length 0
13:10:35.174443 IP atl26s18-in-f3.1e100.net.http > 172.16.146.2.55074: Flags [S.], seq 345110814, ack 1146136518, win 65535, options [mss 1430,sackOK,TS val 1000152427 ecr 1337520268,nop,wscale 8], length 0
13:10:35.174481 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [.], ack 1, win 502, options [nop,nop,TS val 1337520304 ecr 1000152427], length 0
13:10:35.174716 IP 172.16.146.2.55074 > atl26s18-in-f3.1e100.net.http: Flags [P.], seq 1:379, ack 1, win 502, options [nop,nop,TS val 1337520305 ecr 1000152427], length 378: HTTP: POST /gts1o1core HTTP/1.1
13:10:35.208007 IP atl26s18-in-f3.1e100.net.http > 172.16.146.2.55074: Flags [.], ack 379, win 261, options [nop,nop,TS val 1000152462 ecr 1337520305], length 0

```

A continuación, estamos buscando cualquier paquete de menos de 64 bytes. De la siguiente salida, podemos ver que para esta captura, esos paquetes consistieron principalmente en `SYN`, `FIN`, o `KeepAlive` paquetes. Menos y mayor que puede ser un conjunto de modificadores útil. Por ejemplo, digamos que estamos buscando capturar el tráfico que incluya una transferencia de archivos o un conjunto de archivos. Sabemos que estos archivos serán más grandes que el tráfico regular. Para demostrar, podemos utilizar`greater 500`(alternativamente `'>500'`), que solo nos mostrará paquetes con un tamaño mayor de 500 bytes. Esto eliminará todos los paquetes adicionales de la vista que sabemos que ya no nos preocupa.

### **Filtro menos/mayor**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: less/greater [size in bytes]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 less 6406:17:07.311224 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [S], seq 951057939, win 8760, options [mss 1460,nop,nop,sackOK], length 0
06:17:08.222534 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [S.], seq 290218379, ack 951057940, win 5840, options [mss 1380,nop,nop,sackOK], length 0
06:17:08.222534 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1, win 9660, length 0
06:17:08.783340 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 480, win 6432, length 0
06:17:09.123830 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 1381, win 9660, length 0
06:17:09.324118 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 2761, win 9660, length 0
06:17:09.864896 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 5521, win 9660, length 0
06:17:10.125270 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 6901, win 9660, length 0
06:17:10.325558 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 8281, win 9660, length 0
06:17:10.806249 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 11041, win 9660, length 0
06:17:10.956465 IP 216.239.59.99.http > dialin-145-254-160-237.pools.arcor-ip.net.3371: Flags [.], ack 918692089, win 31460, length 0
06:17:11.126710 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 12421, win 9660, length 0
06:17:11.266912 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [.], ack 1590, win 8760, length 0
06:17:11.527286 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 13801, win 9660, length 0
06:17:11.667488 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 16561, win 9660, length 0
06:17:11.807689 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 17941, win 9660, length 0
06:17:12.088092 IP dialin-145-254-160-237.pools.arcor-ip.net.3371 > 216.239.59.99.http: Flags [.], ack 1590, win 8760, length 0
06:17:12.328438 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 18365, win 9236, length 0
06:17:25.216971 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [F.], seq 18365, ack 480, win 6432, length 0
06:17:25.216971 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [.], ack 18366, win 9236, length 0
06:17:37.374452 IP dialin-145-254-160-237.pools.arcor-ip.net.3372 > 65.208.228.223.http: Flags [F.], seq 480, ack 18366, win 9236, length 0
06:17:37.704928 IP 65.208.228.223.http > dialin-145-254-160-237.pools.arcor-ip.net.3372: Flags [.], ack 481, win 6432, length 0

```

Arriba fue un excelente ejemplo de uso `less`. Podemos utilizar el modificador `greater 500` Solo mostrarme paquetes con 500 o más bytes. Regresó con una respuesta única en el ASCII. ¿Podemos decir qué pasó aquí?

### **Utilizando más grande**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 greater 50021:12:43.548353 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 401695766:401696254, ack 2579866052, win 17376, options [nop,nop,TS val 2467382 ecr 10234152], length 488
E...;...@.................d.......C........
.%.6..)(Warning: no Kerberos tickets issued.
OpenBSD 2.6-beta (OOF)#4: Tue Oct 12 20:42:32 CDT 1999Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) utility to report bugs in the system.
Before reporting a bug, please try to reproduce it with the latest
version of the code.  With bug reports, please try to ensure that
enough information to reproduce the problem is enclosed, and if a
known fix for it exists, include that as well.

```

`AND`Como modificador, nos mostrará cualquier cosa que cumpla con ambos requisitos establecidos. Por ejemplo, `host 10.12.1.122 and tcp port 80` Buscará cualquier cosa desde el host de origen y contendrá el tráfico TCP o UDP del puerto 80. Ambos criterios deben cumplirse para el filtro para capturar el paquete. Podemos ver esto en acción a continuación. Aquí utilizamos `host 192.168.0.1 and port 23` como filtro. Por lo tanto, solo veremos tráfico que es de este host en particular que es solo el tráfico del puerto 23.

### **Y filtrar**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: and [requirement]xnoxos@htb[/htb]$ sudo tcpdump -i eth0 host 192.168.0.1 and port 2321:12:38.387203 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [S], seq 2579865836, win 32120, options [mss 1460,sackOK,TS val 10233636 ecr 0,nop,wscale 0], length 0
21:12:38.389728 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [S.], seq 401695549, ack 2579865837, win 17376, options [mss 1448,nop,wscale 0,nop,nop,TS val 2467372 ecr 10233636], length 0
21:12:38.389775 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 1, win 32120, options [nop,nop,TS val 10233636 ecr 2467372], length 0
21:12:38.391363 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [P.], seq 1:28, ack 1, win 32120, options [nop,nop,TS val 10233636 ecr 2467372], length 27 [telnet DO SUPPRESS GO AHEAD, WILL TERMINAL TYPE, WILL NAWS, WILL TSPEED, WILL LFLOW, WILL LINEMODE, WILL NEW-ENVIRON, DO STATUS, WILL XDISPLOC]
21:12:38.537538 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 1:4, ack 28, win 17349, options [nop,nop,TS val 2467372 ecr 10233636], length 3 [telnet DO AUTHENTICATION]

```

Los otros modificadores, `OR` y `NOT` Proporcionamos una forma de especificar múltiples condiciones o negar algo. Juguemos con eso un poco ahora. ¿Qué notamos sobre esta salida?

### **Captura básica sin filtro**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
14:39:51.224071 IP 172.16.146.2 > dns.google: ICMP echo request, id 19623, seq 72, length 64
14:39:51.251635 IP dns.google > 172.16.146.2: ICMP echo reply, id 19623, seq 72, length 64
14:39:51.329340 IP 172.16.146.2.39003 > 172.16.146.1.domain: 8231+ PTR? 8.8.8.8.in-addr.arpa. (38)
14:39:51.330334 IP 172.16.146.1.domain > 172.16.146.2.39003: 8231 1/0/0 PTR dns.google. (62)
14:39:51.330613 IP 172.16.146.2.50633 > 172.16.146.1.domain: 65266+ PTR? 2.146.16.172.in-addr.arpa. (43)
14:39:51.331461 IP 172.16.146.1.domain > 172.16.146.2.50633: 65266 NXDomain* 0/0/0 (43)
14:39:51.399970 IP 172.16.146.2.54742 > 72.21.91.29.http: Flags [.], ack 358742210, win 501, options [nop,nop,TS val 1924174236 ecr 1068405332], length 0
14:39:51.420559 IP 72.21.91.29.http > 172.16.146.2.54742: Flags [.], ack 1, win 131, options [nop,nop,TS val 1068415563 ecr 1924143536], length 0
14:39:51.432107 IP 172.16.146.2.41928 > 172.16.146.1.domain: 7232+ PTR? 1.146.16.172.in-addr.arpa. (43)
14:39:51.432795 IP 172.16.146.1.domain > 172.16.146.2.41928: 7232 NXDomain* 0/0/0 (43)
14:39:51.433048 IP 172.16.146.2.47075 > 172.16.146.1.domain: 18932+ PTR? 29.91.21.72.in-addr.arpa. (42)
14:39:51.434374 IP 172.16.146.1.domain > 172.16.146.2.47075: 18932 NXDomain 0/1/0 (113)
14:39:52.225941 IP 172.16.146.2 > dns.google: ICMP echo request, id 19623, seq 73, length 64
14:39:52.252523 IP dns.google > 172.16.146.2: ICMP echo reply, id 19623, seq 73, length 64
14:39:52.683881 IP 172.16.146.2.47004 > 151.139.128.14.http: Flags [.], ack 2006616877, win 501, options [nop,nop,TS val 1585722998 ecr 2103316650], length 0
14:39:52.712283 IP 151.139.128.14.http > 172.16.146.2.47004: Flags [.], ack 1, win 507, options [nop,nop,TS val 2103326900 ecr 1585692473], length 0

```

Tenemos una mezcla de diferentes fuentes y destinos junto con múltiples tipos de protocolo. Si tuviéramos que usar el `OR` (alternativamente `||`) Modificador, podríamos pedir tráfico desde un host específico o simplemente el tráfico ICMP como ejemplo. Vamos a repetirlo y agregar un`OR`.

### **O filtrar**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: or/|| [requirement]xnoxos@htb[/htb]$ sudo tcpdump -r sus.pcap icmp or host 172.16.146.1reading from file sus.pcap, link-type EN10MB (Ethernet), snapshot length 262144
14:54:03.659163 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 21, length 64
14:54:03.691278 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 21, length 64
14:54:03.879882 ARP, Request who-has 172.16.146.1 tell 172.16.146.2, length 28
14:54:03.880266 ARP, Reply 172.16.146.1 is-at 8a:66:5a:11:8d:64 (oui Unknown), length 46
14:54:04.661179 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 22, length 64
14:54:04.687120 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 22, length 64
14:54:05.663097 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 23, length 64
14:54:05.686092 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 23, length 64
14:54:06.664174 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 24, length 64
14:54:06.697469 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 24, length 64
14:54:07.666273 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 25, length 64
14:54:07.701475 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 25, length 64
14:54:08.668364 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 26, length 64
14:54:08.694948 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 26, length 64
14:54:09.670523 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 27, length 64
14:54:09.694974 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 27, length 64
14:54:10.672858 IP 172.16.146.2 > dns.google: ICMP echo request, id 51661, seq 28, length 64
14:54:10.697834 IP dns.google > 172.16.146.2: ICMP echo reply, id 51661, seq 28, length 64

```

Nuestro tráfico se ve un poco diferente ahora. Esto se debe a que muchos de los paquetes coincidían con la variable ICMP, mientras que algunos coincidían con la variable del host. Entonces, en esta salida, podemos ver algo de tráfico ARP y tráfico ICMP. El filtro funcionó desde 172.16.146.2 coincidió con la otra variable y apareció como un host en el campo de origen o de destino. Ahora, ¿qué sucede si utilizamos el `NOT` (alternativamente `!`) modificador.

### **No filtrar**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ ### Syntax: not/! [requirement]xnoxos@htb[/htb]$ sudo tcpdump -r sus.pcap not icmp14:54:03.879882 ARP, Request who-has 172.16.146.1 tell 172.16.146.2, length 28
14:54:03.880266 ARP, Reply 172.16.146.1 is-at 8a:66:5a:11:8d:64 (oui Unknown), length 46
14:54:16.541657 IP 172.16.146.2.55592 > ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 3569937476:3569937513, ack 2948818703, win 501, options [nop,nop,TS val 713252991 ecr 12282469], length 37
14:54:16.568659 IP 172.16.146.2.53329 > 172.16.146.1.domain: 24866+ A? app.hackthebox.eu. (35)
14:54:16.616032 IP 172.16.146.1.domain > 172.16.146.2.53329: 24866 3/0/0 A 172.67.1.1, A 104.20.66.68, A 104.20.55.68 (83)
14:54:16.616396 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [S], seq 2697802378, win 64240, options [mss 1460,sackOK,TS val 533261003 ecr 0,nop,wscale 7], length 0
14:54:16.637895 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [S.], seq 752000032, ack 2697802379, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
14:54:16.637937 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [.], ack 1, win 502, length 0
14:54:16.644551 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [P.], seq 1:514, ack 1, win 502, length 513
14:54:16.667236 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [.], ack 514, win 66, length 0
14:54:16.668307 IP 172.67.1.1.https > 172.16.146.2.56204: Flags [P.], seq 1:2766, ack 514, win 66, length 2765
14:54:16.668319 IP 172.16.146.2.56204 > 172.67.1.1.https: Flags [.], ack 2766, win 496, length 0
14:54:16.670536 IP ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.55592: Flags [P.], seq 1:34, ack 37, win 114, options [nop,nop,TS val 12294021 ecr 713252991], length 33
14:54:16.670559 IP 172.16.146.2.55592 > ec2-52-211-164-46.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 34, win 501, options [nop,nop,TS val 713253120 ecr 12294021], length 0

```

Se ve muy diferente ahora. Solo vemos algo de tráfico ARP, y luego vemos algo de tráfico HTTPS al que no llegamos antes. Esto se debe a que negamos que se muestre cualquier tráfico ICMP utilizando`not icmp`.

---

# **Filtros previos a la captura vs. Procesamiento posterior a la captura**

Al utilizar filtros, podemos aplicarlos directamente a la captura o aplicarlos al leer un archivo de captura. Al aplicarlos a la captura, soltará cualquier tráfico que no coincida con el filtro. Esto reducirá la cantidad de datos en las capturas y potencialmente despejar el tráfico que podamos necesitar más tarde, por lo que úselos solo cuando busque algo específico, como la solución de problemas de un problema de conectividad de red. Al aplicar el filtro para capturar, hemos leído desde un archivo y el filtro analizará el archivo y eliminará cualquier cosa de nuestra salida terminal que no coincida con el filtro especificado. El uso de un filtro de esta manera puede ayudarnos a investigar mientras ahorra potenciales datos valiosos en las capturas. No cambiará permanentemente el archivo de captura, y para cambiar o borrar el filtro de nuestra salida requerirá que volvamos a ser nuestro comando con un cambio en la sintaxis.

---

# **Interpretar consejos y trucos**

Usando el `-S` Switch mostrará números de secuencia absoluta, que pueden ser extremadamente largos. Por lo general, TCPDump muestra números de secuencia relativos, que son más fáciles de rastrear y leer. Sin embargo, si buscamos estos valores en otra herramienta o registro, solo encontraremos el paquete basado en números de secuencia absoluta. Por ejemplo, 13245768092588 a 100.

El `-v`, `-X`, y `-e` Los conmutadores pueden ayudarlo a aumentar la cantidad de datos capturados, mientras que el `-c`, `-n`, `-s`, `-S`, y `-q` Los conmutadores pueden ayudar a reducir y modificar la cantidad de datos escritos y vistos.

Muchas opciones útiles que se pueden usar pero que no siempre son directamente valiosas para todos son las `-A` y `-l` interruptores. A mostrará solo el texto ASCII después de la línea de paquete, en lugar de ASCII y HEX.`L`le dirá a tcpdump que salga en un modo diferente. `L` Se alineará a buffer en lugar de agrupar y empujar en trozos. Nos permite enviar la salida directamente a otra herramienta como `grep` Usando una tubería `|`.

### **Consejos y trucos**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$sudo tcpdump -Ar telnet.pcap21:12:43.528695 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 157:217, ack 216, win 17376, options [nop,nop,TS val 2467382 ecr 10234022], length 60
E..p;...@..p..............c.......C........
.%.6..(.Last login: Sat Nov 27 20:11:43 on ttyp2 from bam.zing.org

21:12:43.546441 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 217, win 32120, options [nop,nop,TS val 10234152 ecr 2467382], length 0
E..4FP@.@.s...................d...}x.......
..)(.%.6
21:12:43.548353 IP 192.168.0.1.telnet > 192.168.0.2.1550: Flags [P.], seq 217:705, ack 216, win 17376, options [nop,nop,TS val 2467382 ecr 10234152], length 488
E...;...@.................d.......C........
.%.6..)(Warning: no Kerberos tickets issued.
OpenBSD 2.6-beta (OOF)#4: Tue Oct 12 20:42:32 CDT 1999Welcome to OpenBSD: The proactively secure Unix-like operating system.

Please use the sendbug(1) utility to report bugs in the system.
Before reporting a bug, please try to reproduce it with the latest
version of the code.  With bug reports, please try to ensure that
enough information to reproduce the problem is enclosed, and if a
known fix for it exists, include that as well.

21:12:43.566442 IP 192.168.0.2.1550 > 192.168.0.1.telnet: Flags [.], ack 705, win 32120, options [nop,nop,TS val 10234154 ecr 2467382], length 0
E..4FQ@.@.s...................e...}x.0.....
..)*.%.6

```

Observe cómo tiene los valores ASCII que se muestran debajo de cada línea de salida debido a nuestro uso de `-A`. Esto puede ser útil cuando se busca rápidamente algo legible en humanos en la salida.

### **Capturar una captura a Grep**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -Ar http.cap -l | grep 'mailto:*'reading from file http.cap, link-type EN10MB (Ethernet), snapshot length 65535
  <a href="mailto:ethereal-web[AT]ethereal.com">ethereal-web[AT]ethereal.com</a>
  <a href="mailto:free-support[AT]thewrittenword.com">free-support[AT]thewrittenword.com</a>
  <a href="mailto:ethereal-users[AT]ethereal.com">ethereal-users[AT]ethereal.com</a>
  <a href="mailto:ethereal-web[AT]ethereal.com">ethereal-web[AT]ethereal.com</a>

```

Usando `-l` De esta manera, nos permitió examinar la captura rápidamente y GREP para las palabras clave o el formato que sospechamos que podría estar allí. En este caso, usamos el `-l` para pasar la salida a `grep` y buscando cualquier instancia de la frase `mailto:*`. Esto nos muestra cada línea con nuestra búsqueda, y podemos ver los resultados anteriores. El uso de modificadores y la salida de redireccionamiento puede ser una forma rápida de raspar sitios web para direcciones de correo electrónico, nombres de estándares y mucho más.

Podemos cavar tan profundamente como deseamos en los paquetes que capturamos. Sin embargo, requiere un poco de conocimiento de cómo se estructuran los protocolos. Por ejemplo, si quisiéramos ver solo paquetes con el conjunto de indicadores SYN TCP, podríamos usar el siguiente comando:

### **Buscando banderas de protocolo TCP**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ tcpdump -i eth0 'tcp[13] &2 != 0'
```

Esto cuenta con el 13 ° byte en la estructura y mirando el segundo bit. Si se establece en 1 o encendido, se establece el indicador SYN.

### **Caza por una bandera de syn**

Filtrado de paquetes tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 'tcp[13] &2 != 0'tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
15:18:14.630993 IP 172.16.146.2.56244 > 172.67.1.1.https: Flags [S], seq 122498858, win 64240, options [mss 1460,sackOK,TS val 534699017 ecr 0,nop,wscale 7], length 0
15:18:14.654698 IP 172.67.1.1.https > 172.16.146.2.56244: Flags [S.], seq 3728841459, ack 122498859, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:15.017464 IP 172.16.146.2.60202 > a23-54-168-81.deploy.static.akamaitechnologies.com.https: Flags [S], seq 777468939, win 64240, options [mss 1460,sackOK,TS val 1348555130 ecr 0,nop,wscale 7], length 0
15:18:15.021329 IP 172.16.146.2.49652 > 104.16.88.20.https: Flags [S], seq 1954080833, win 64240, options [mss 1460,sackOK,TS val 274098564 ecr 0,nop,wscale 7], length 0
15:18:15.022640 IP 172.16.146.2.45214 > 104.18.22.52.https: Flags [S], seq 1072203471, win 64240, options [mss 1460,sackOK,TS val 1445124063 ecr 0,nop,wscale 7], length 0
15:18:15.042399 IP 104.18.22.52.https > 172.16.146.2.45214: Flags [S.], seq 215464563, ack 1072203472, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:15.043646 IP a23-54-168-81.deploy.static.akamaitechnologies.com.https > 172.16.146.2.60202: Flags [S.], seq 1390108870, ack 777468940, win 28960, options [mss 1460,sackOK,TS val 3405787409 ecr 1348555130,nop,wscale 7], length 0
15:18:15.044764 IP 104.16.88.20.https > 172.16.146.2.49652: Flags [S.], seq 2086758283, ack 1954080834, win 65535, options [mss 1400,nop,nop,sackOK,nop,wscale 10], length 0
15:18:16.131983 IP 172.16.146.2.45684 > ec2-34-255-145-175.eu-west-1.compute.amazonaws.com.https: Flags [S], seq 4017793011, win 64240, options [mss 1460,sackOK,TS val 933634389 ecr 0,nop,wscale 7], length 0
15:18:16.261855 IP ec2-34-255-145-175.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.45684: Flags [S.], seq 106675091, ack 4017793012, win 26847, options [mss 1460,sackOK,TS val 12653884 ecr 933634389,nop,wscale 8], length 0

```

Nuestros resultados incluyen solo paquetes con el TCP `SYN` Bandera establecida desde lo que vemos arriba.

TCPDUMP puede ser una herramienta poderosa si entendemos nuestras redes y cómo los anfitriones interactúan entre sí. Tómese el tiempo para comprender las estructuras típicas del encabezado del protocolo para detectar la anomalía cuando llegue el momento. Aquí hay algunos enlaces para promover nuestros estudios sobre protocolos estándar y sus estructuras. Excepto por el enlace de Wikipedia, cada enlace debe llevarnos directamente al RFC que establece el estándar en su lugar para cada uno.

### **Enlaces de protocolo RFC**

| **Enlace** | **Descripción** |
| --- | --- |
| [Protocolo IP](https://tools.ietf.org/html/rfc791) | `RFC 791` Describe IP y su funcionalidad. |
| [Protocolo ICMP](https://tools.ietf.org/html/rfc792) | `RFC 792` describe ICMP y su funcionalidad. |
| [Protocolo TCP](https://tools.ietf.org/html/rfc793) | `RFC 793` describe el protocolo TCP y cómo funciona. |
| [Protocolo UDP](https://tools.ietf.org/html/rfc768) | `RFC 768` Describe UDP y cómo funciona. |
| [Enlaces rápidos de RFC](https://en.wikipedia.org/wiki/List_of_RFCs#Topical_list) | Este artículo de Wikipedia contiene una gran lista de protocolos vinculados a la RFC que explica su implementación. |