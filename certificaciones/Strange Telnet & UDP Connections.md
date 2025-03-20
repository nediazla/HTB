# Strange Telnet & UDP Connections

Cuando buscamos un tráfico extraño, siempre debemos considerar el tráfico Telnet y UDP. Después de todo, estos pueden pasarse por alto, pero pueden revelar especialmente durante nuestros esfuerzos de análisis de tráfico.

---

# **Telnet**

![](https://academy.hackthebox.com/storage/modules/229/Internet.png)

Telnet es un protocolo de red que permite una sesión de comunicación interactiva bidireccional entre dos dispositivos a través de una red. Este protocolo se desarrolló en la década de 1970 y se definió en RFC 854. En los últimos años, su uso ha disminuido significativamente en lugar de SSH.

En muchos casos más antiguos, como nuestras máquinas como Windows NT, aún pueden utilizar Telnet para proporcionar comando y control remotos a Microsoft Terminal Services.

Sin embargo, siempre debemos observar las comunicaciones de telnet extrañas y extrañas, ya que los atacantes también pueden ser utilizado por los atacantes con fines maliciosos, como la exfiltración de datos y el túnel.

# **Encontrar el puerto de tráfico de Telnet tradicional 23**

**Archivo PCAP relacionado**:

- `telnet_tunneling_23.pcapng`

Supongamos que íbamos a abrir Wireshark, podríamos notar algunas comunicaciones de Telnet originadas en el puerto 23. En este caso, siempre podemos inspeccionar este tráfico aún más.

![](https://academy.hackthebox.com/storage/modules/229/1-telnet.png)

Afortunadamente para nosotros, el tráfico de Telnet tiende a descifrar y fácilmente inspeccionable, pero al igual que ICMP, DNS y otros métodos de túneles, los atacantes pueden encriptar, codificar o ofusar este texto. Entonces siempre debemos tener cuidado.

![](https://academy.hackthebox.com/storage/modules/229/2-telnet.png)

# **TCP Telnet no reconocido en Wireshark**

**Archivo PCAP relacionado**:

- `telnet_tunneling_9999.pcapng`

Telnet es solo un protocolo de comunicación, y como tal puede cambiar fácilmente a otro puerto por un atacante. Estar ojo en estas extrañas comunicaciones portuarias puede permitirnos encontrar acciones potencialmente maliciosas. Tomemos lo siguiente, por ejemplo.

![](https://academy.hackthebox.com/storage/modules/229/3-telnet.png)

Podemos ver una tonelada de comunicaciones de un cliente en el puerto 9999. Podemos sumergirnos un poco más al observar el contenido de estas comunicaciones.

![](https://academy.hackthebox.com/storage/modules/229/4-telnet.png)

Si notamos algo como lo anterior, quisiéramos seguir esta transmisión TCP.

![](https://academy.hackthebox.com/storage/modules/229/5-telnet.png)

Hacerlo puede permitirnos inspeccionar acciones potencialmente maliciosas.

---

# **Protocolo de Telnet a través de IPv6**

**Archivo PCAP relacionado**:

- `telnet_tunneling_ipv6.pcapng`

Después de todo, a menos que nuestra red local esté configurada para utilizar IPv6, observar el tráfico IPv6 puede ser un indicador de malas acciones dentro de nuestro entorno. Podríamos notar el uso de direcciones IPv6 para Telnet como las siguientes.

![](https://academy.hackthebox.com/storage/modules/229/6-telnet.png)

Podemos reducir nuestro filtro en Wireshark para mostrar solo el tráfico de Telnet desde estas direcciones con el siguiente filtro.

- `((ipv6.src_host == fe80::c9c8:ed3:1b10:f10b) or (ipv6.dst_host == fe80::c9c8:ed3:1b10:f10b)) and telnet`

![](https://academy.hackthebox.com/storage/modules/229/7-telnet.png)

Del mismo modo, podemos inspeccionar el contenido de estos paquetes a través de su campo de datos, o siguiendo el flujo TCP.

![](https://academy.hackthebox.com/storage/modules/229/8-telnet.png)

# **Ver comunicaciones de UDP**

**Archivo PCAP relacionado**:

- `udp_tunneling.pcapng`

Por otro lado, los atacantes pueden optar por usar conexiones UDP sobre TCP en sus esfuerzos de exfiltración.

![](https://academy.hackthebox.com/storage/modules/229/udp-tcp.jpg)

Uno de los aspectos distintivos más grandes entre TCP y UDP es que UDP no tiene conexión y proporciona una transmisión rápida. Tomemos el siguiente tráfico, por ejemplo.

![](https://academy.hackthebox.com/storage/modules/229/1-udp.png)

Notaremos que en lugar de una secuencia SYN, SYN/ACK, ACK, las comunicaciones se envían inmediatamente al destinatario. Al igual que TCP, podemos seguir el tráfico UDP en Wireshark e inspeccionar su contenido.

![](https://academy.hackthebox.com/storage/modules/229/2-udp.png)

# **Usos comunes de UDP**

UDP, aunque es menos confiable que TCP, proporciona conexiones más rápidas a través de su estado sin conexión. Como tal, podríamos encontrar un tráfico legítimo que use UDP como lo siguiente:

| **Paso** | **Descripción** |
| --- | --- |
| `1. Real-time Applications` | Aplicaciones como medios de transmisión, juegos en línea, comunicaciones de voz y video en tiempo real |
| `2. DNS (Domain Name System)` | Las consultas y respuestas de DNS usan UDP |
| `3. DHCP (Dynamic Host Configuration Protocol)` | DHCP utiliza UDP para asignar direcciones IP e información de configuración a los dispositivos de red. |
| `4. SNMP (Simple Network Management Protocol)` | SNMP utiliza UDP para el monitoreo y la gestión de la red |
| `5. TFTP (Trivial File Transfer Protocol)` | TFTP utiliza UDP para transferencias de archivos simples, comúnmente utilizados por los sistemas de Windows más antiguos y otros. |