# Fundamentos tcpdump

`Tcpdump` es un rastreador de paquetes de línea de comandos que puede capturar e interpretar directamente los marcos de datos desde un archivo o interfaz de red. Fue construido para su uso en cualquier sistema operativo similar a UNIX y tenía un gemelo de Windows llamado `WinDump`. Es una herramienta potente y directa utilizada en la mayoría de los sistemas basados ​​en UNIX. No requiere una GUI y puede usarse a través de cualquier conexión terminal o remota, como SSH. Sin embargo, esta herramienta puede parecer abrumadora al principio debido a las diferentes funciones y filtros que nos ofrece. Sin embargo, una vez que aprendamos las funciones esenciales, nos resultará mucho más fácil usar esta herramienta de manera eficiente. Para capturar el tráfico de red desde "Off the Wire", utiliza las bibliotecas `pcap` y `libpcap`, emparejado con una interfaz en modo promiscuo para escuchar datos. Esto permite que el programa vea y capture paquetes de abastecimiento o destinado a cualquier dispositivo en la red de área local, no solo los paquetes destinados a nosotros.

TCPDUMP está disponible para la mayoría de los sistemas UNIX y derivados UNIX, como AIX, BSD, Linux, Solaris, y es suministrado por muchos fabricantes que ya están en el sistema. Debido al acceso directo al hardware, necesitamos el `root` o el `administrator's` privilegios para ejecutar esta herramienta. Para nosotros eso significa que tendremos que utilizar `sudo` para ejecutar tcpdump como se ve en los ejemplos a continuación. `TCPDump` A menudo viene preinstalado en la mayoría de los sistemas operativos de Linux.

Cabe señalar que Windows tenía un puerto de tcpdump llamado Windump. El apoyo a Windump ha cesado. Como alternativa, ejecutar una distribución de Linux como Parrot o Ubuntu en el subsistema de Windows para Linux puede ser una manera fácil de tener un host virtual de Linux directamente en nuestra computadora, lo que permite el uso de TCPDump y muchas otras herramientas construidas de Linux.

### **Localizar tcpdump**

Para validar si el paquete existe en nuestro host, use el siguiente comando:

Tcpdump Fundamentals

```
xnoxos@htb[/htb]$ which tcpdump
```

A menudo se puede encontrar en `/usr/sbin/tcpdump`. Sin embargo, si el paquete no existe, podemos instalarlo con:

### **Instalar tcpdump**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo apt install tcpdump
```

Podemos ejecutar el paquete tcpdump con el `--version` Cambie para verificar nuestra versión de instalación y paquete actual para validar nuestra instalación.

### **Validación de la versión tcpdump**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump --versiontcpdump version 4.9.3
libpcap version 1.9.1 (with TPACKET_V3)
OpenSSL 1.1.1f  31 Mar 2020

```

---

# **Capturas de tráfico con tcpdump**

Debido a las muchas funciones y filtros diferentes, primero debemos familiarizarnos con las características esenciales de la herramienta. Discutamos algunas opciones básicas de tcpdump, demostramos algunos comandos y mostramos cómo ahorrar tráfico para `PCAP` archivos y leer de estos.

### **Opciones de captura básica**

A continuación se muestra una tabla de interruptores básicos de tcpdump que podemos usar para modificar cómo se ejecutan nuestras capturas. Estos interruptores se pueden encadenar para crear cómo se nos muestra la salida de la herramienta en Stdout y qué se guarda en el archivo de captura. Esta no es una lista exhaustiva, y hay muchos más que podemos usar, pero estas son las más comunes y valiosas.

| **Comando de cambio** | **Resultado** |
| --- | --- |
| D | Mostrará cualquier interfaces disponibles para capturar. |
| i | Selecciona una interfaz para capturar. ex. -I eth0 |
| norte | No resuelva los nombres de host. |
| nn | No resuelva los nombres de host o los puertos bien conocidos. |
| mi | Agarrará el encabezado Ethernet junto con los datos de la capa superior. |
| incógnita | Mostrar contenido de paquetes en Hex y ASCII. |
| Xx | Igual que X, pero también especificará los encabezados Ethernet. (como usar Xe) |
| V, VV, VVV | Aumente la verbosidad de la salida mostrada y guardada. |
| do | Tome un número específico de paquetes, luego deje del programa. |
| s | Define cuánto paquete para agarrar. |
| S | Cambie los números de secuencia relativa en la pantalla de captura a números de secuencia absoluta. (13248765839 en lugar de 101) |
| Q | Imprima menos información del protocolo. |
| r file.pcap | Leer desde un archivo. |
| w file.pcap | Escribir en un archivo |

### **Utilización de la página del hombre**

Para ver la lista completa de interruptores, podemos utilizar las páginas del hombre:

### **TCPDUMP MAN PÁGINA**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ man tcpdump
```

Aquí hay algunos ejemplos de uso básico de interruptor TCPDump junto con descripciones de lo que está sucediendo:

### **Listado de interfaces disponibles**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -D1.eth0 [Up, Running, Connected]
2.any (Pseudo-device that captures on all interfaces) [Up, Running]
3.lo [Up, Running, Loopback]
4.bluetooth0 (Bluetooth adapter number 0) [Wireless, Association status unknown]
5.bluetooth-monitor (Bluetooth Linux Monitor) [Wireless]
6.nflog (Linux netfilter log (NFLOG) interface) [none]
7.nfqueue (Linux netfilter queue (NFQUEUE) interface) [none]
8.dbus-system (D-Bus system bus) [none]
9.dbus-session (D-Bus session bus) [none]

```

El comando anterior llama a TCPDump utilizando privilegios de sudo y enumera las interfaces de red utilizables. Podemos elegir una de estas interfaces de red y decirle a TCPDump qué interfaces debe escuchar.

### **Elegir una interfaz para capturar desde**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
10:58:33.719241 IP 172.16.146.2.55260 > 172.67.1.1.https: Flags [P.], seq 1953742992:1953743073, ack 2034210498, win 501, length 81
10:58:33.747853 IP 172.67.1.1.https > 172.16.146.2.55260: Flags [.], ack 81, win 158, length 0
10:58:33.750393 IP 172.16.146.2.52195 > 172.16.146.1.domain: 7579+ PTR? 1.1.67.172.in-addr.arpa. (41)

```

En este terminal, llamamos a TCPDUMP y seleccionamos la interfaz ETH0 para capturar el tráfico. Una vez que emitamos el comando, TCPDump comenzará a oler el tráfico y verá los primeros paquetes en la interfaz. Emitiendo el `-nn` Los interruptores como se ve a continuación, le dicemos a TCPDUMP que se abstenga de resolver direcciones IP y números de puerto a sus nombres de host y nombres de puertos comunes. En esta representación, el último octeto es el puerto desde/al que va la conexión.

### **Desactivar la resolución de nombre**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 -nntcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:02:35.580449 IP 172.16.146.2.48402 > 52.31.199.148.443: Flags [P.], seq 988167196:988167233, ack 1512376150, win 501, options [nop,nop,TS val 214282239 ecr 77421665], length 37
11:02:35.588695 IP 172.16.146.2.55272 > 172.67.1.1.443: Flags [P.], seq 940648841:940648916, ack 4248406693, win 501, length 75
11:02:35.654368 IP 172.67.1.1.443 > 172.16.146.2.55272: Flags [.], ack 75, win 70, length 0
11:02:35.728889 IP 52.31.199.148.443 > 172.16.146.2.48402: Flags [P.], seq 1:34, ack 37, win 118, options [nop,nop,TS val 77434740 ecr 214282239], length 33
11:02:35.728988 IP 172.16.146.2.48402 > 52.31.199.148.443: Flags [.], ack 34, win 501, options [nop,nop,TS val 214282388 ecr 77434740], length 0
11:02:35.729073 IP 52.31.199.148.443 > 172.16.146.2.48402: Flags [P.], seq 34:65, ack 37, win 118, options [nop,nop,TS val 77434740 ecr 214282239], length 31
11:02:35.729081 IP 172.16.146.2.48402 > 52.31.199.148.443: Flags [.], ack 65, win 501, options [nop,nop,TS val 214282388 ecr 77434740], length 0
11:02:35.729348 IP 52.31.199.148.443 > 172.16.146.2.48402: Flags [F.], seq 65, ack 37, win 118, options [nop,nop,TS val 77434740 ecr 214282239], length 0

```

Al utilizar el `-e` Switch, estamos marcando tcpdump para incluir los encabezados Ethernet en la salida de la captura junto con su contenido regular. Podemos ver que esto funcionó examinando la salida. Por lo general, los campos del primer y segundo consisten en la marca de tiempo y luego el comienzo del encabezado IP. Ahora consiste en TimeStamp y la dirección MAC de origen del host.

### **Muestra el encabezado Ethernet**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 -etcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:05:45.982115 00:0c:29:97:52:65 (oui Unknown) > 8a:66:5a:11:8d:64 (oui Unknown), ethertype IPv4 (0x0800), length 103: 172.16.146.2.57142 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 922951468:922951505, ack 1842875143, win 501, options [nop,nop,TS val 1368272062 ecr 65637925], length 37
11:05:45.989652 00:0c:29:97:52:65 (oui Unknown) > 8a:66:5a:11:8d:64 (oui Unknown), ethertype IPv4 (0x0800), length 129: 172.16.146.2.55272 > 172.67.1.1.https: Flags [P.], seq 940656124:940656199, ack 4248413119, win 501, length 75
11:05:46.047731 00:0c:29:97:52:65 (oui Unknown) > 8a:66:5a:11:8d:64 (oui Unknown), ethertype IPv4 (0x0800), length 85: 172.16.146.2.54006 > 172.16.146.1.domain: 31772+ PTR? 207.22.80.99.in-addr.arpa. (43)
11:05:46.049134 8a:66:5a:11:8d:64 (oui Unknown) > 00:0c:29:97:52:65 (oui Unknown), ethertype IPv4 (0x0800), length 147: 172.16.146.1.domain > 172.16.146.2.54006: 31772 1/0/0 PTR ec2-99-80-22-207.eu-west-1.compute.amazonaws.com. (105)

```

Emitiendo el `-X` Cambie, podemos ver el paquete un poco más claro ahora. Obtenemos una salida ASCII a la derecha de interpretar cualquier cosa en el texto claro que corresponde a la salida hexadecimal de la izquierda.

### **Incluir la salida ASCII y HEX**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 -Xtcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:10:34.972248 IP 172.16.146.2.57170 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 2612172989:2612173026, ack 3165195759, win 501, options [nop,nop,TS val 1368561052 ecr 65712142], length 37
    0x0000:  4500 0059 4352 4000 4006 3f1b ac10 9202  E..YCR@.@.?.....
    0x0010:  6350 16cf df52 01bb 9bb2 98bd bca9 0def  cP...R..........
    0x0020:  8018 01f5 b87d 0000 0101 080a 5192 959c  .....}......Q...
    0x0030:  03ea b00e 1703 0300 2000 0000 0000 0000  ................
    0x0040:  0adb 84ac 34b4 910a 0fb4 2f49 9865 eb45  ....4...../I.e.E
    0x0050:  883c eafd 8266 3e23 88                   .<...f>#.11:10:34.984582 IP 172.16.146.2.38732 > 172.16.146.1.domain: 22938+ A? app.hackthebox.eu. (35)
    0x0000:  4500 003f 2e6b 4000 4011 901e ac10 9202  E..?.k@.@.......
    0x0010:  ac10 9201 974c 0035 002b 7c61 599a 0100  .....L.5.+|aY...
    0x0020:  0001 0000 0000 0000 0361 7070 0a68 6163  .........app.hac
    0x0030:  6b74 6865 626f 7802 6575 0000 0100 01    kthebox.eu.....
11:10:35.055497 IP 172.16.146.2.43116 > 172.16.146.1.domain: 6524+ PTR? 207.22.80.99.in-addr.arpa. (43)
    0x0000:  4500 0047 2e72 4000 4011 900f ac10 9202  E..G.r@.@.......
    0x0010:  ac10 9201 a86c 0035 0033 7c69 197c 0100  .....l.5.3|i.|..
    0x0020:  0001 0000 0000 0000 0332 3037 0232 3202  .........207.22.
    0x0030:  3830 0239 3907 696e 2d61 6464 7204 6172  80.99.in-addr.ar
    0x0040:  7061 0000 0c00 01                        pa.....

```

Preste atención al nivel de detalle en la salida anterior. Notaremos que tenemos información sobre las opciones de encabezado IP como Time to Live, Offset y Otry Bandera y más detalles en los protocolos de la capa superior. A continuación, estamos combinando los interruptores para crear la salida de nuestro gusto.

### **Combinaciones de interruptor tcpdump**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 -nnvXXtcpdump: listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:13:59.149599 IP (tos 0x0, ttl 64, id 24075, offset 0, flags [DF], proto TCP (6), length 89)
    172.16.146.2.42454 > 54.77.251.34.443: Flags [P.], cksum 0x6fce (incorrect -> 0xb042), seq 671020720:671020757, ack 3699222968, win 501, options [nop,nop,TS val 1154433101 ecr 1116647414], length 37
    0x0000:  8a66 5a11 8d64 000c 2997 5265 0800 4500  .fZ..d..).Re..E.
    0x0010:  0059 5e0b 4000 4006 6d11 ac10 9202 364d  .Y^.@.@.m.....6M
    0x0020:  fb22 a5d6 01bb 27fe f6b0 dc7d a9b8 8018  ."....'....}....
    0x0030:  01f5 6fce 0000 0101 080a 44cf 404d 428e  ..o.......D.@MB.
    0x0040:  aff6 1703 0300 2000 0000 0000 0000 09bb  ................
    0x0050:  38d9 d89a 2d70 73d5 a01e 9df7 2c48 5b8a  8...-ps.....,H[.
    0x0060:  d64d 8e42 2ccc 43                        .M.B,.C
11:13:59.157113 IP (tos 0x0, ttl 64, id 31823, offset 0, flags [DF], proto UDP (17), length 63)
    172.16.146.2.55351 > 172.16.146.1.53: 26460+ A? app.hackthebox.eu. (35)
    0x0000:  8a66 5a11 8d64 000c 2997 5265 0800 4500  .fZ..d..).Re..E.
    0x0010:  003f 7c4f 4000 4011 423a ac10 9202 ac10  .?|O@.@.B:......
    0x0020:  9201 d837 0035 002b 7c61 675c 0100 0001  ...7.5.+|ag\....
    0x0030:  0000 0000 0000 0361 7070 0a68 6163 6b74  .......app.hackt
    0x0040:  6865 626f 7802 6575 0000 0100 01         hebox.eu.....
11:13:59.158029 IP (tos 0x0, ttl 64, id 20784, offset 0, flags [none], proto UDP (17), length 111)
    172.16.146.1.53 > 172.16.146.2.55351: 26460 3/0/0 app.hackthebox.eu. A 104.20.55.68, app.hackthebox.eu. A 172.67.1.1, app.hackthebox.eu. A 104.20.66.68 (83)
    0x0000:  000c 2997 5265 8a66 5a11 8d64 0800 4500  ..).Re.fZ..d..E.
    0x0010:  006f 5130 0000 4011 ad29 ac10 9201 ac10  .oQ0..@..)......
    0x0020:  9202 0035 d837 005b 9d2e 675c 8180 0001  ...5.7.[..g\....
    0x0030:  0003 0000 0000 0361 7070 0a68 6163 6b74  .......app.hackt
    0x0040:  6865 626f 7802 6575 0000 0100 01c0 0c00  hebox.eu........
    0x0050:  0100 0100 0000 ab00 0468 1437 44c0 0c00  .........h.7D...
    0x0060:  0100 0100 0000 ab00 04ac 4301 01c0 0c00  ..........C.....
    0x0070:  0100 0100 0000 ab00 0468 1442 44         .........h.BD
11:13:59.158335 IP (tos 0x0, ttl 64, id 20242, offset 0, flags [DF], proto TCP (6), length 60)
    172.16.146.2.55416 > 172.67.1.1.443: Flags [S], cksum 0xeb85 (incorrect -> 0x72f7), seq 3766489491, win 64240, options [mss 1460,sackOK,TS val 508232750 ecr 0,nop,wscale 7], length 0
    0x0000:  8a66 5a11 8d64 000c 2997 5265 0800 4500  .fZ..d..).Re..E.
    0x0010:  003c 4f12 4000 4006 0053 ac10 9202 ac43  .<O.@.@..S.....C
    0x0020:  0101 d878 01bb e080 1193 0000 0000 a002  ...x............
    0x0030:  faf0 eb85 0000 0204 05b4 0402 080a 1e4b  ...............K
    0x0040:  042e 0000 0000 0103 0307                 ..........

```

Al utilizar los interruptores, encadenándolos como en el ejemplo `above` es la mejor práctica.

---

# **Salida tcpdump**

Al mirar la salida de TCPDUMP, puede ser un poco abrumador. Ejecutar a través de estos interruptores básicos ya nos ha mostrado varias vistas diferentes. Vamos a tomar un minuto para diseccionar esa salida y explicar lo que estamos viendo. La imagen y la tabla de abajo definirán cada campo. Tenga en cuenta que cuanto más vegetal estamos con nuestros filtros, más detalles de cada encabezado se muestran.

### **Desglose de concha tcpdump**

![](https://academy.hackthebox.com/storage/modules/81/breakdown.png)

| **Filtrar** | **Resultado** |
| --- | --- |
| Marca de tiempo | `Yellow` El campo de la marca de tiempo es lo primero y es configurable para mostrar la hora y la fecha en un formato que podemos ingerir fácilmente. |
| Protocolo | `Orange` Esta sección nos dirá cuál es el encabezado de la capa superior. En nuestro ejemplo, muestra IP. |
| Fuente y destino IP.port | `Orange` Esto nos mostrará la fuente y el destino del paquete junto con el número de puerto utilizado para conectarse. Formato == `IP.port == 172.16.146.2.21` |
| Banderas | `Green` Esta porción muestra cualquier bandera utilizada. |
| Números de secuencia y reconocimiento | `Red` Esta sección muestra la secuencia y los números de reconocimiento utilizados para rastrear el segmento TCP. Nuestro ejemplo es utilizar números bajos para suponer que se muestran la secuencia relativa y los números ACK. |
| Opciones de protocolo | `Blue` Aquí, veremos cualquier valor TCP negociado establecido entre el cliente y el servidor, como el tamaño de la ventana, los reconocimientos selectivos, los factores de escala de ventanas y más. |
| Notas / Siguiente encabezado | `White` Notas MISC El disector encontrado estará presente aquí. Como el tráfico que estamos viendo está encapsulado, podemos ver más información de encabezado para diferentes protocolos. En nuestro ejemplo, podemos ver que el disector TCPDump reconoce el tráfico FTP dentro de la encapsulación para mostrarlo para nosotros. |

Hay muchas otras opciones e información que se pueden mostrar. Esta información varía según la cantidad de verbosidad que está habilitada. Para obtener una comprensión más detallada de la IP y otros encabezados del protocolo, consulte el `Networking Primer` en la sección dos o el `Networking fundamentals` camino.

Hay una gran ventaja en saber cómo funciona una red y cómo usar los filtros que proporciona TCPDUMP. Con ellos, podemos ver el tráfico de red, analizarlo para cualquier problema e identificar interacciones sospechosas de red rápidamente. Teóricamente, podemos usar`tcpdump` Para crear un sistema IDS/IPS haciendo que un script bash analice los paquetes interceptados de acuerdo con un patrón específico. Luego podemos establecer condiciones para, por ejemplo, prohibir una dirección IP particular que haya enviado demasiadas solicitudes de eco ICMP para un cierto período.

---

# **Entrada/salida del archivo con TCPDUMP**

Usando `-w` Escribirá nuestra captura a un archivo. Tenga en cuenta que a medida que capturamos el tráfico fuera del cable, podemos usar rápidamente el espacio de disco abierto y encontrarnos con problemas de almacenamiento si no tenemos cuidado. Cuanto más grande sea nuestro segmento de red, más rápido utilizaremos el almacenamiento. La utilización de los conmutadores demostrados anteriormente puede ayudar a ajustar la cantidad de datos almacenados en nuestros PCAP.

### **Guarde nuestra salida de PCAP en un archivo**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 -w ~/output.pcaptcpdump: listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
10 packets captured
131 packets received by filter
0 packets dropped by kernel

```

Esta captura anterior generará la salida a un archivo llamado `output.pcap`. Al ejecutar TCPDUM de esta manera, la salida no desplazará a nuestro terminal como de costumbre. Toda la salida de TCPDUM se está redirigiendo al archivo que especificamos para la captura.

### **Leyendo la salida de un archivo**

Fundamentos tcpdump

```
xnoxos@htb[/htb]$ sudo tcpdump -r ~/output.pcapreading from file /home/trey/output.pcap, link-type EN10MB (Ethernet), snapshot length 262144
11:15:40.321509 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 2751910362:2751910399, ack 946558143, win 501, options [nop,nop,TS val 1368866401 ecr 65790024], length 37
11:15:40.337302 IP 172.16.146.2.55416 > 172.67.1.1.https: Flags [P.], seq 3766493458:3766493533, ack 4098207917, win 501, length 75
11:15:40.398103 IP 172.67.1.1.https > 172.16.146.2.55416: Flags [.], ack 75, win 73, length 0
11:15:40.457416 IP ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.57236: Flags [.], ack 37, win 118, options [nop,nop,TS val 65799068 ecr 1368866401], length 0
11:15:40.458582 IP ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.57236: Flags [P.], seq 34:65, ack 37, win 118, options [nop,nop,TS val 65799068 ecr 1368866401], length 31
11:15:40.458599 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 1, win 501, options [nop,nop,TS val 1368866538 ecr 65799068,nop,nop,sack 1 {34:65}], length 0
11:15:40.458643 IP ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https > 172.16.146.2.57236: Flags [P.], seq 1:34, ack 37, win 118, options [nop,nop,TS val 65799068 ecr 1368866401], length 33
11:15:40.458655 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [.], ack 65, win 501, options [nop,nop,TS val 1368866538 ecr 65799068], length 0
11:15:40.458915 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [P.], seq 37:68, ack 65, win 501, options [nop,nop,TS val 1368866539 ecr 65799068], length 31
11:15:40.458964 IP 172.16.146.2.57236 > ec2-99-80-22-207.eu-west-1.compute.amazonaws.com.https: Flags [F.], seq 68, ack 65, win 501, options [nop,nop,TS val 1368866539 ecr 65799068], length 0

```

Esto leerá la captura almacenada en `output.pcap`. Observe que ha vuelto a una vista básica. Para obtener información más detallada del archivo de captura, vuelva a aplicar nuestros conmutadores.