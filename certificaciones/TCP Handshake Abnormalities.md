# TCP Handshake Abnormalities

Innatamente, cuando los atacantes están ganando información sobre nuestros servicios TCP, podríamos notar algunos comportamientos impares durante nuestros esfuerzos de análisis de tráfico. En primer lugar, consideremos cómo funcionan las conexiones TCP normales con su apretón de manos de 3 vías.

![](https://academy.hackthebox.com/storage/modules/229/tcp_handshake_1.jpg)

Para iniciar una conexión TCP para cualquier propósito, el cliente primero envía la máquina, intenta conectarse a una solicitud de SYN TCP para comenzar la conexión TCP.

Si este puerto está abierto y, de hecho, se puede conectar, la máquina responde con un TCP SYN/ACK para reconocer que la conexión es válida y se puede usar. Sin embargo, debemos considerar todas las banderas TCP.

| **Banderas** | **Descripción** |
| --- | --- |
| `URG (Urgent)` | Este indicador es para denotar la urgencia con los datos actuales en la transmisión. |
| `ACK (Acknowledgement)` | Este indicador reconoce la recepción de datos. |
| `PSH (Push)` | Este indicador instruye a la pila TCP que entregue inmediatamente los datos recibidos a la capa de aplicación y el búfer de derivación. |
| `RST (Reset)` | Esta bandera se utiliza para la terminación de la conexión TCP (pronto nos sumergiremos en secuestro y los primeros ataques). |
| `SYN (Synchronize)` | Esta bandera se utiliza para establecer una conexión inicial con TCP. |
| `FIN (Finish)` | Esta bandera se usa para denotar el acabado de una conexión TCP. Se usa cuando no se deben enviar más datos. |
| `ECN (Explicit Congestion Notification)` | Esta bandera se usa para denotar la congestión dentro de nuestra red, es para que los anfitriones sepan para evitar re-transmisiones innecesarias. |

Como tal, cuando realizamos nuestros esfuerzos de análisis de tráfico, podemos buscar las siguientes condiciones extrañas:

1. `Too many flags of a kind or kinds`Esto podría mostrarnos que el escaneo está ocurriendo dentro de nuestra red.
2. `The usage of different and unusual flags`A veces esto podría indicar un ataque TCP RST, secuestro o simplemente alguna forma de evasión de control para escanear.
3. `Solo host to multiple ports, or solo host to multiple hosts`Es bastante fácil, podemos encontrar escaneo como lo hemos hecho antes al notar a dónde van estas conexiones de un host. En muchos casos, incluso es posible que necesitemos considerar escaneos señuelo y ataques de fuentes aleatorias.

# **Banderas de syn excesivas**

**Archivo PCAP relacionado**:

- `nmap_syn_scan.pcapng`

De inmediato, uno de los patrones de tráfico que podemos notar es demasiados banderas SYN. Este es un excelente ejemplo de escaneo NMAP. En pocas palabras, el adversario enviará paquetes SYN TCP a los puertos de destino. En el caso de que nuestro puerto esté abierto, nuestra máquina responderá con un paquete Syn-Aack para continuar el apretón de manos, que luego será cumplido por un RST del escáner de los atacantes. Sin embargo, podemos perdernos en los RST aquí, ya que nuestra máquina responderá con RST para puertos cerrados.

![](https://academy.hackthebox.com/storage/modules/229/1-TCPhandshake.png)

Sin embargo, vale la pena señalar que hay dos tipos de escaneo primario que podríamos detectar que usan el indicador SYN. Estos son:

1. `SYN Scans`En estos escaneos, el comportamiento será como vemos, sin embargo, el atacante terminará preventivamente el apretón de manos con la primera bandera.
2. `SYN Stealth Scans`En este caso, el atacante intentará evadir la detección solo completando parcialmente el apretón de manos TCP.

# **Sin banderas**

**Archivo PCAP relacionado**:

- `nmap_null_scan.pcapng`

En el lado opuesto de las cosas, el atacante podría no enviar banderas. Esto es lo que comúnmente se hace referencia como una exploración nula. En un escaneo nulo, un atacante envía paquetes TCP sin banderas. Las conexiones TCP se comportan como lo siguiente cuando se recibe un paquete nulo.

1. `If the port is open`El sistema no responderá en absoluto ya que no hay banderas.
2. `If the port is closed`El sistema responderá con un paquete RST.

Como tal, un escaneo nulo puede parecer lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/2-TCPhandshake.png)

# **Demasiados ACK**

**Archivo PCAP relacionado**:

- `nmap_ack_scan.pcapng`

Por otro lado, podríamos notar una cantidad excesiva de reconocimientos entre dos anfitriones. En este caso, el atacante podría estar empleando el uso de una exploración ACK. En el caso de una conexión TCP de escaneo ACK se comportará como lo siguiente.

1. `If the port is open`La máquina afectada no responderá o responderá con un paquete RST.
2. `If the port is closed`La máquina afectada responderá con un paquete RST.

Por lo tanto, podríamos ver el siguiente tráfico que indicaría un escaneo ACK.

![](https://academy.hackthebox.com/storage/modules/229/3-TCPhandshake.png)

# **Aletas excesivas**

**Archivo PCAP relacionado**:

- `nmap_fin_scan.pcapng`

Usando otra parte del apretón de manos, un atacante podría utilizar un escaneo de aletas. En este caso, todos los paquetes TCP se marcarán con la bandera de la aleta. Podríamos notar el siguiente comportamiento de nuestra máquina afectada.

1. `If the port is open`Nuestra máquina afectada simplemente no responderá.
2. `If the port is closed`Nuestra máquina afectada responderá con un paquete RST.

![](https://academy.hackthebox.com/storage/modules/229/4-TCPhandshake.png)

# **Demasiadas banderas**

**Archivo PCAP relacionado**:

- `nmap_xmas_scan.pcapng`

Digamos que el atacante solo quería arrojar espagueti a la pared. En ese caso, podrían utilizar un escaneo de árbol de Navidad, que es cuando ponen todas las banderas TCP en sus transmisiones. Del mismo modo, nuestro anfitrión afectado puede responder como lo siguiente cuando se establecen todas las banderas.

1. `If the port is open`La máquina afectada no responderá, o al menos lo hará con un paquete RST.
2. `If the port is closed`La máquina afectada responderá con un paquete RST.

Los escaneos de árboles de Navidad son bastante fáciles de detectar y parecen los siguientes.

![](https://academy.hackthebox.com/storage/modules/229/5-TCPhandshake.png)