# The Networking Behind Pivoting

Poder comprender el concepto de`pivoting`Lo suficientemente bueno como para tener éxito en un compromiso requiere una comprensión fundamental sólida de algunos conceptos clave de redes. Esta sección será un repaso rápido sobre los conceptos esenciales de redes fundamentales para comprender el pivote.

# **Dirección IP y NICS**

Cada computadora que se comunica en una red necesita una dirección IP. Si no tiene uno, no está en una red. La dirección IP se asigna en software y generalmente se obtiene automáticamente de un servidor DHCP. También es común ver computadoras con direcciones IP asignadas estáticamente. La asignación de IP estática es común con:

- Servidor
- Enrutadores
- Cambiar de interfaces virtuales
- Impresoras
- Y cualquier dispositivo que brinde servicios críticos a la red

Si asignado`dynamically`o`statically`, la dirección IP se asigna a un`Network Interface Controller` (`NIC`). Comúnmente, la NIC se conoce como un`Network Interface Card`o`Network Adapter`. Una computadora puede tener múltiples NIC (física y virtual), lo que significa que puede tener múltiples direcciones IP asignadas, lo que le permite comunicarse en varias redes. Identificar oportunidades de pivotación a menudo dependerá de las IP específicas asignadas a los hosts que comprometemos porque pueden indicar que las redes comprometidas pueden alcanzar los hosts. Es por eso que es importante para nosotros verificar siempre las NIC adicionales usando comandos como`ifconfig`(en macOS y Linux) y`ipconfig`(en Windows).

### **Usando ifconfig**

Las redes detrás de pivotar

```
xnoxos@htb[/htb]$ ifconfigeth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 134.122.100.200  netmask 255.255.240.0  broadcast 134.122.111.255
        inet6 fe80::e973:b08d:7bdf:dc67  prefixlen 64  scopeid 0x20<link>
        ether 12:ed:13:35:68:f5  txqueuelen 1000  (Ethernet)
        RX packets 8844  bytes 803773 (784.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5698  bytes 9713896 (9.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.106.0.172  netmask 255.255.240.0  broadcast 10.106.15.255
        inet6 fe80::a5bf:1cd4:9bca:b3ae  prefixlen 64  scopeid 0x20<link>
        ether 4e:c7:60:b0:01:8d  txqueuelen 1000  (Ethernet)
        RX packets 15  bytes 1620 (1.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 18  bytes 1858 (1.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 19787  bytes 10346966 (9.8 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 19787  bytes 10346966 (9.8 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
        inet 10.10.15.54  netmask 255.255.254.0  destination 10.10.15.54
        inet6 fe80::c85a:5717:5e3a:38de  prefixlen 64  scopeid 0x20<link>
        inet6 dead:beef:2::1034  prefixlen 64  scopeid 0x0<global>
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 7  bytes 336 (336.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

En la salida anterior, cada NIC tiene un identificador (`eth0`, `eth1`, `lo`, `tun0`) seguido de abordar la información y las estadísticas de tráfico. La interfaz del túnel (TUN0) indica que una conexión VPN está activa. Cuando nos conectamos a cualquiera de los servidores VPN de HTB a través de Pwnbox o nuestro propio host de ataque, siempre notaremos que se crea una interfaz de túnel y se le asigna una dirección IP. La VPN nos permite acceder a los entornos de red de laboratorio alojados por HTB. Tenga en cuenta que estas redes de laboratorio no son accesibles sin establecer un túnel. La VPN cifra el tráfico y también establece un túnel a través de una red pública (a menudo Internet), a través de`NAT`en un dispositivo de red de orientación pública y en la red interna/privada. Además, observe las direcciones IP asignadas a cada NIC. La IP asignada a eth0 (`134.122.100.200`) es una dirección IP de rutina pública. Lo que significa que los ISP enrutarán el tráfico que se origina en esta IP a través de Internet. Veremos IP públicos en dispositivos que se enfrentan directamente a Internet, comúnmente alojados en DMZS. Las otras NIC tienen direcciones IP privadas, que son enrutables dentro de las redes internas pero no en Internet público. Al momento de escribir este artículo, cualquier persona que quiera comunicarse a través de Internet debe tener al menos una dirección IP pública asignada a una interfaz en el dispositivo de red que se conecta a la infraestructura física que se conecta a Internet. Recuerde que NAT se usa comúnmente para traducir direcciones IP privadas a direcciones IP públicas.

### **Usando ipconfig**

Las redes detrás de pivotar

```powershell
PS C:\Users\htb-student> ipconfig

Windows IP Configuration

Unknown adapter NordLynx:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Ethernet adapter Ethernet0 2:

   Connection-specific DNS Suffix  . : .htb
   IPv6 Address. . . . . . . . . . . : dead:beef::1a9
   IPv6 Address. . . . . . . . . . . : dead:beef::f58b:6381:c648:1fb0
   Temporary IPv6 Address. . . . . . : dead:beef::dd0b:7cda:7118:3373
   Link-local IPv6 Address . . . . . : fe80::f58b:6381:c648:1fb0%8
   IPv4 Address. . . . . . . . . . . : 10.129.221.36
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::250:56ff:feb9:df81%8
                                       10.129.0.1

Ethernet adapter Ethernet:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

```

La salida directamente arriba es de emisión`ipconfig`en un sistema de Windows. Podemos ver que este sistema tiene múltiples adaptadores, pero solo uno de ellos tiene direcciones IP asignadas. Hay[IPv6](https://www.cisco.com/c/en/us/solutions/ipv6/overview.html)direcciones y un[IPv4](https://en.wikipedia.org/wiki/IPv4)DIRECCIÓN. Este módulo se centrará principalmente en las redes que ejecutan IPv4, ya que sigue siendo el mecanismo de direccionamiento IP más común en las LAN empresariales. Notemos que algunos adaptadores, como el de la salida anterior, tendrán una dirección IPv4 y una dirección IPv6 asignada en un[Configuración de doble pila](https://www.cisco.com/c/dam/en_us/solutions/industries/docs/gov/IPV6at_a_glance_c45-625859.pdf)permitiendo que se alcancen los recursos sobre IPv4 o IPv6.

Cada dirección IPv4 tendrá una correspondiente`subnet mask`. Si una dirección IP es como un número de teléfono, la máscara de subred es como el código de área. Recuerde que la máscara de subred define la`network` & `host`parte de una dirección IP. Cuando el tráfico de red está destinado a una dirección IP ubicada en una red diferente, la computadora enviará el tráfico a su asignado`default gateway`. La puerta de enlace predeterminada suele ser la dirección IP asignada a una NIC en un aparato que actúa como el enrutador para una LAN determinada. En el contexto de la pivotación, debemos tener en cuenta en qué redes puede llegar un anfitrión, por lo que documentar la mayor cantidad de información de IP puede ser útil.

---

# **Enrutamiento**

Es común pensar en un dispositivo de red que nos conecta a Internet al pensar en un enrutador, pero técnicamente cualquier computadora puede convertirse en un enrutador y participar en el enrutamiento. Algunos de los desafíos que enfrentaremos en este módulo requieren que hagamos un tráfico de ruta de host de pivote a otra red. Una forma en que veremos esto es a través del uso de Autoroute, que permite que nuestro cuadro de ataque tenga`routes`a las redes de orientación que se pueden acceder a través de un host de pivote. Una característica de definición clave de un enrutador es que tiene una tabla de enrutamiento que utiliza para reenviar el tráfico en función de la dirección IP de destino. Veamos esto en pwnbox usando los comandos`netstat -r`o`ip route`.

### **Mesa de enrutamiento en pwnbox**

Las redes detrás de pivotar

```
xnoxos@htb[/htb]$ netstat -rKernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         178.62.64.1     0.0.0.0         UG        0 0          0 eth0
10.10.10.0      10.10.14.1      255.255.254.0   UG        0 0          0 tun0
10.10.14.0      0.0.0.0         255.255.254.0   U         0 0          0 tun0
10.106.0.0      0.0.0.0         255.255.240.0   U         0 0          0 eth1
10.129.0.0      10.10.14.1      255.255.0.0     UG        0 0          0 tun0
178.62.64.0     0.0.0.0         255.255.192.0   U         0 0          0 eth0

```

Notaremos que Pwnbox, Linux Distross, Windows y muchos otros sistemas operativos tienen una tabla de enrutamiento para ayudar al sistema a tomar decisiones de enrutamiento. Cuando se crea un paquete y tiene un destino antes de que salga de la computadora, la tabla de enrutamiento se usa para decidir dónde enviarlo. Por ejemplo, si estamos tratando de conectarnos a un objetivo con la IP`10.129.10.25`, podríamos decir desde la tabla de enrutamiento dónde se enviaría el paquete para llegar allí. Sería enviado a un`Gateway`fuera de la NIC correspondiente (`Iface`). Pwnbox no está utilizando ningún protocolo de enrutamiento (EIGRP, OSPF, BGP, etc.) para aprender cada una de esas rutas. Aprendió sobre esas rutas a través de sus propias interfaces directamente conectadas (ETH0, ETH1, TUN0). Los electrodomésticos independientes designados como enrutadores generalmente aprenderán rutas utilizando una combinación de creación de ruta estática, protocolos de enrutamiento dinámico e interfaces directamente conectadas. Cualquier tráfico destinado a redes no presentes en la tabla de enrutamiento se enviará al`default route`, que también se puede denominar la puerta de enlace o puerta de enlace predeterminada del último recurso. Al buscar oportunidades para pivotar, puede ser útil observar la tabla de enrutamiento de los hosts para identificar a qué redes podemos alcanzar o qué rutas podemos necesitar agregar.

---

# **Protocolos, servicios y puertos**

`Protocols`son las reglas que rigen las comunicaciones de la red. Muchos protocolos y servicios tienen correspondientes`ports`que actúan como identificadores. Los puertos lógicos no son cosas físicas en las que podemos tocar o conectar nada. Están en software asignado a aplicaciones. Cuando vemos una dirección IP, sabemos que identifica una computadora que puede ser accesible en una red. Cuando vemos un puerto abierto vinculado a esa dirección IP, sabemos que identifica una aplicación a la que podemos conectarnos. Conectarse a puertos específicos que un dispositivo es`listening`ON a menudo puede permitirnos usar puertos y protocolos`permitted`en el firewall para obtener un punto de apoyo en la red.

Tomemos, por ejemplo, un servidor web que usa HTTP (`often listening on port 80`). Los administradores no deben bloquear el tráfico en el puerto 80. Esto evitaría que cualquier persona visite el sitio web que esté alojando. Esta es a menudo una forma de entrar en el entorno de red,`through the same port that legitimate traffic is passing`. No debemos pasar por alto el hecho de que un`source port`También se genera para realizar un seguimiento de las conexiones establecidas en el lado del cliente de una conexión. Necesitamos mantenernos conscientes de los puertos que estamos utilizando para asegurarnos de que cuando ejecutemos nuestras cargas útiles, se conecten nuevamente a los oyentes previstos que configuramos. Nos volveremos creativos con el uso de puertos a lo largo de este módulo.

Para una revisión adicional de los conceptos fundamentales de redes, consulte el módulo[Introducción a las redes](https://academy.hackthebox.com/course/preview/introduction-to-networking).

---

Un consejo de LTNB0B: en este módulo, practicaremos muchas herramientas y técnicas diferentes para pivotar a través de hosts y reenviar servicios locales o remotos a nuestro host de ataque para acceder a objetivos conectados a diferentes redes. Este módulo aumenta gradualmente en la dificultad, proporcionando redes de múltiples host para practicar lo que se aprende. Le recomiendo que practique muchos métodos diferentes de manera creativa a medida que comienza a comprender los conceptos. Tal vez incluso intente dibujar las topologías de red utilizando herramientas de diagramación de red a medida que enfrenta desafíos. Cuando busco oportunidades para pivotar, me gusta usar herramientas como[Dibujo.io](https://draw.io/)Para construir una visual del entorno de red en el que estoy, también sirve como una gran herramienta de documentación. Este módulo es muy divertido y pondrá a prueba sus habilidades de red. ¡Diviértete y nunca dejes de aprender!