# Análisis con Wireshark

`Wireshark` es un analizador de tráfico de red gratuito y de código abierto muy parecido a tcpdump pero con una interfaz gráfica. wireshark es multiplataforma y es capaz de capturar datos en vivo de muchos tipos de interfaz diferentes (para incluir wifi, usb y bluetooth) y guardar el tráfico a varios formatos diferentes. Wireshark permite al usuario sumergirse mucho más en la inspección de los paquetes de red que otras herramientas. Lo que hace que Wireshark sea realmente poderoso es la capacidad de análisis que proporciona, dando una visión detallada del tráfico.

Dependiendo del anfitrión que estamos usando, es posible que no siempre tengamos una GUI para utilizar Wireshark tradicional. Por suerte para nosotros, varias variantes nos permiten usarlo desde la línea de comando.

### **Características y capacidades:**

- Inspección de paquetes profundos para cientos de protocolos diferentes
- Interfaces gráficas y tty
- Capaz de ejecutar en la mayoría de los sistemas operativos
- Ethernet, IEEE 802.11, PPP/HDLC, ATM, Bluetooth, USB, Token Ring, Frame Relay, FDDI, entre otros
- Capacidades de descifrado para iPsec, Isakmp, Kerberos, SNMPV3, SSL/TLS, WEP y WPA/WPA2
- Muchos muchos más ...

---

# **Requisitos para su uso**

Wireshark requiere lo siguiente para su uso:

### **Windows:**

- El tiempo de ejecución universal C. Esto se incluye con Windows 10 y Windows Server 2019 y se instala automáticamente en versiones anteriores si Microsoft Windows Update está habilitado. De lo contrario, se debe instalar KB2999226 o KB3118401.
- Cualquier moderno procesador AMD64/x86-64 o 32 bits X86 de 32 bits.
- 500 MB disponible Ram. Los archivos de captura más grandes requieren más RAM.
- 500 MB de espacio de disco disponible. Los archivos de captura requieren espacio de disco adicional.
- Cualquier pantalla moderna. Se recomienda una resolución 1280 × 1024 o más alta. Wireshark utilizará resoluciones HIDPI o Retina si están disponibles. Los usuarios avanzados encontrarán útiles múltiples monitores.
- Una tarjeta de red compatible para capturar:
    - Ethernet. Cualquier tarjeta compatible con Windows debería funcionar.
    - 802.11. Vea la página Wireshark Wiki. La captura de información RAW 802.11 puede ser difícil sin equipos especiales.
- Para instalar, descargue el ejecutable de wireshark.org, valida el hash e instalar.

### **Linux:**

- Wireshark se ejecuta en la mayoría de las plataformas de UNIX y de Unix, incluidas Linux y la mayoría de las variantes BSD. Los requisitos del sistema deben ser comparables a las especificaciones enumeradas anteriormente para Windows.
- Los paquetes binarios están disponibles para la mayoría de las distribuciones de UNIX y Linux.
- Para validar si el paquete existe en un host, use el siguiente comando:

### **Localización de Wireshark**

Análisis con Wireshark

```
xnoxos@htb[/htb]$ which wireshark
```

If the package does not exist, (It can often be found in `/usr/sbin/wireshark`) you can install it with:

### **Instalación de Wireshark en Linux**

Análisis con Wireshark

```
xnoxos@htb[/htb]$ sudo apt install wireshark
```

---

# **Tshark vs. Wireshark (Terminal vs. GUI)**

Ambas opciones tienen sus méritos. Tshark es una herramienta terminal especialmente diseñada basada en Wireshark. Tshark comparte muchas de las mismas características que se incluyen en Wireshark e incluso comparte sintaxis y opciones. Tshark es perfecto para usar en máquinas con poco o ningún entorno de escritorio y puede pasar fácilmente la información de captura que recibe a otra herramienta a través de la línea de comando. Wireshark es la opción GUI rica en características para la captura y el análisis de tráfico. Si desea tener la experiencia y trabajar con una máquina con un entorno de escritorio, la GUI de Wireshark es el camino a seguir.

### **Interruptores básicos de Tshark**

| **Comando de cambio** | **Resultado** |
| --- | --- |
| D | Mostrará cualquier interfaces disponibles para capturar y luego salir. |
| L | Enumerará los medios de capa de enlace de los que puede capturar y luego salir. (Ethernet como ejemplo) |
| i | Elija una interfaz para capturar. (-i eth0) |
| F | Filtro de paquetes en la sintaxis libpcap. Utilizado durante la captura. |
| do | Tome un número específico de paquetes, luego deje del programa. Define una condición de parada. |
| a | Define una condición de autostop. Puede ser después de una duración, tamaño de archivo específico, o después de un cierto número de paquetes. |
| R (PCAP-File) | Leer desde un archivo. |
| W (PCAP-File) | Escriba en un archivo utilizando el formato PCAPNG. |
| PAG | Imprimirá el resumen del paquete mientras se escribe en un archivo (-W) |
| incógnita | Agregará la salida hexadecimal y ASCII a la captura. |
| H | Ver el menú de ayuda |

`To see the full list of switches you can utilize:`

### **Ayuda de Tshark**

Análisis con Wireshark

```
xnoxos@htb[/htb]$ tshark -h
```

### **Uso básico de Tshark**

Tshark puede usar filtros para protocolos, elementos comunes como hosts y puertos, e incluso la capacidad de profundizar en los paquetes y diseccionar campos individuales del paquete.

### **Localización de Tshark**

Análisis con Wireshark

```
xnoxos@htb[/htb]$ which tsharkxnoxos@htb[/htb]$ tshark -Dxnoxos@htb[/htb]$ tshark -i 1 -w /tmp/test.pcapCapturing on 'Wi-Fi: en0'
484

```

Con la cadena básica en la línea de comando anterior, utilizamos Tshark para capturar en EN0, especificado con el `-i` bandera y el `-w` Opción para guardar la captura en un archivo de salida especificado. Utilizar tshark es muy similar a tcpdump en los filtros y los conmutadores que podemos usar. Ambas herramientas utilizan la sintaxis BPF. Para leer la captura, Tshark se puede pasar el `-r` cambiar al igual que en tcpdump, o podemos pasar el `-P` Cambie para que Tshark imprima los resúmenes de paquetes mientras escribe en un archivo. A continuación se muestra un ejemplo de lectura del archivo PCAP que capturamos anteriormente.

### **Seleccionar una interfaz y escribir en un archivo**

Análisis con Wireshark

```
xnoxos@htb[/htb]$ sudo tshark -i eth0 -w /tmp/test.pcap
```

### **Aplicación de filtros**

Análisis con Wireshark

```
xnoxos@htb[/htb]$ sudo tshark -i eth0 -f "host 172.16.146.2"Capturing on 'eth0'
    1 0.000000000 172.16.146.2 → 172.16.146.1 DNS 70 Standard query 0x0804 A github.com
    2 0.258861645 172.16.146.1 → 172.16.146.2 DNS 86 Standard query response 0x0804 A github.com A 140.82.113.4
    3 0.259866711 172.16.146.2 → 140.82.113.4 TCP 74 48256 → 443 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM=1 TSval=1321417850 TSecr=0 WS=128
    4 0.299681376 140.82.113.4 → 172.16.146.2 TCP 74 443 → 48256 [SYN, ACK] Seq=0 Ack=1 Win=65535 Len=0 MSS=1436 SACK_PERM=1 TSval=3885991869 TSecr=1321417850 WS=1024
    5 0.299771728 172.16.146.2 → 140.82.113.4 TCP 66 48256 → 443 [ACK] Seq=1 Ack=1 Win=64256 Len=0 TSval=1321417889 TSecr=3885991869
    6 0.306888828 172.16.146.2 → 140.82.113.4 TLSv1 579 Client Hello
    7 0.347570701 140.82.113.4 → 172.16.146.2 TLSv1.3 2785 Server Hello, Change Cipher Spec, Application Data, Application Data, Application Data, Application Data
    8 0.347653593 172.16.146.2 → 140.82.113.4 TCP 66 48256 → 443 [ACK] Seq=514 Ack=2720 Win=63488 Len=0 TSval=1321417937 TSecr=3885991916
    9 0.358887130 172.16.146.2 → 140.82.113.4 TLSv1.3 130 Change Cipher Spec, Application Data
   10 0.359781588 172.16.146.2 → 140.82.113.4 TLSv1.3 236 Application Data
   11 0.360037927 172.16.146.2 → 140.82.113.4 TLSv1.3 758 Application Data
   12 0.360482668 172.16.146.2 → 140.82.113.4 TLSv1.3 258 Application Data
   13 0.397331368 140.82.113.4 → 172.16.146.2 TLSv1.3 145 Application Data

```

- `f`nos permite aplicar filtros a la captura. En el ejemplo, utilizamos `host`, pero puede usar casi cualquier filtro que Wireshark reconozca. Hemos tocado un poco en Tshark ahora. Echemos un vistazo a una herramienta ingeniosa llamada Termshark.

---

# **Termshark**

Termshark es una aplicación de interfaz de usuario basada en texto (TUI) que proporciona al usuario una interfaz similar a Wireshark justo en la ventana de su terminal.

### **Termshark**

![](https://academy.hackthebox.com/storage/modules/81/termshark.png)

Termshark se puede encontrar en [Termshark](https://github.com/gcla/termshark). Se puede construir a partir de la fuente clonando el repositorio, o retirando una de las versiones estables actuales de https://github.com/gcla/termshark/releases, extrae el archivo y presione el lugar en funcionamiento.

Para obtener ayuda para navegar por este TUI, vea la imagen a continuación.

### **Ayuda de Termshark**

![](https://academy.hackthebox.com/storage/modules/81/termshark-help.png)

Para comenzar Termshark, emita las mismas cuerdas, muy al igual que Tshark o TCPDump. Podemos especificar una interfaz para capturar, filtros y otras configuraciones desde el terminal. La ventana TermShark no se abrirá hasta que detenga el tráfico en su filtro de captura. Así que dale un segundo si no pasa nada.

---

# **Tutorial de Wireshark GUI**

Ahora que hemos pasado tiempo aprendiendo el arte de la captura de paquetes desde la línea de comandos, pasemos algún tiempo en Wireshark. Tomaremos unos minutos para examinar lo que estamos viendo en la salida a continuación. Disecemos esta vista de la gui de Wireshark.

### **Gui de Wireshark**

![](https://academy.hackthebox.com/storage/modules/81/wireshark-interface.png)

Tres paneles principales: `See Figure above`

1. Lista de paquetes:`Orange`
    - En esta ventana, vemos una línea de resumen de cada paquete que incluye los campos que se enumeran a continuación de forma predeterminada. Podemos agregar o eliminar columnas para cambiar la información que se presenta.
        - Número de pedido El paquete llegó a Wireshark
        - Tiempo- Formato de tiempo unix
        - Fuente-fuente IP
        - Destino- Destino IP
        - Protocolo: el protocolo utilizado (TCP, UDP, DNS, etc.)
        - Información: información sobre el paquete. Este campo puede variar según el tipo de protocolo utilizado dentro. Mostrará, por ejemplo, qué tipo de consulta es para un paquete DNS.
2. Detalles del paquete: `Blue`
    - La ventana de detalles del paquete nos permite profundizar en el paquete para inspeccionar los protocolos con mayor detalle. Lo romperá en trozos que esperaríamos seguir la referencia típica del modelo OSI.`The packet is dissected into different encapsulation layers for inspection.`
    - Tenga en cuenta que Wireshark mostrará esta encapsulación en orden inverso con encapsulación de capa más baja en la parte superior de la ventana y niveles más altos en la parte inferior.
3. Bytes de paquetes: `Green`
    - La ventana Bytes de paquetes nos permite mirar el contenido del paquete en la salida ASCII o hexadecimal. A medida que seleccionamos un campo en las ventanas de arriba, se resaltará en la ventana Bytes de paquetes y nos mostrará dónde cae ese bit o byte dentro del paquete general.
    - Esta es una excelente manera de validar que lo que vemos en el panel de detalles es preciso y la interpretación de Wireshark coincide con la salida de paquetes.
    - Cada línea en la salida contiene el desplazamiento de datos, dieciséis bytes hexadecimales y dieciséis bytes ASCII. Los bytes no imprimibles se reemplazan con un período en el formato ASCII.

### **Otras características notables**

Al mirar la interfaz Wireshark, notaremos algunas áreas de opción diferentes y botones radiales. Estas áreas son puntos de control en los que podemos modificar la interfaz y nuestra vista de los paquetes en la captura actual.`See Figure below`

### **Menú de Wireshark**

![](https://academy.hackthebox.com/storage/modules/81/wireshark-menu.png)

# **Realizando nuestra primera captura en Wireshark**

Comenzar una captura con Wireshark es un simple esfuerzo. El GIF a continuación mostrará los pasos.

### **Pasos para comenzar una captura**

![](https://academy.hackthebox.com/storage/modules/81/first-capture-ws.gif)

Tenga en cuenta que cada vez que cambiemos las opciones de captura, Wireshark reiniciará el rastro. Al igual que TCPDump, Wireshark tiene opciones de filtro de captura y pantalla que se pueden usar.

---

# **Lo básico**

### **La barra de herramientas**

![](https://academy.hackthebox.com/storage/modules/81/wireshark-toolbar.jpg)

La barra de herramientas de Wireshark es un punto central para administrar las muchas características que incluye Wireshark. Desde aquí, podemos comenzar y detener capturas, cambiar interfaces, abrir y guardar archivos .pcap y aplicar diferentes filtros o complementos de análisis.

### **Cómo guardar una captura**

Digamos que necesitamos capturar lo que tenemos en nuestra ventana actualmente para solucionar problemas más adelante. Guardar una captura es súper simple:

- Seleccione Archivo ⇢ Guardar o
- En la barra de herramientas, seleccione la opción Archivo y elija dónde guardar el archivo y en qué formato.

Tenga en cuenta que Wireshark puede ahorrar capturas en múltiples formatos. Elija el necesario para el escenario, pero usaremos el `.pcap` formato por ahora.

---

# **Procesamiento y filtrado previo a la captura y posterior a la captura**

Mientras capturamos el tráfico con Wireshark, tenemos varias opciones con respecto a cómo y cuándo filtramos el tráfico. Esto se logra utilizando filtros de captura y visualización. El primero se inició antes de que comience la captura y el segundo durante o después de la captura se complete. Si bien Wireshark tiene un montón de funcionalidad de horno útil, vale la pena mencionar que tiene un poco de problemas para manejar grandes capturas. Cuantos más paquetes se capturen, más tiempo tomará Wireshark para ejecutar el filtro de pantalla o análisis contra él. Puede tomar de solo unos segundos a unos minutos si se completa. Si estamos trabajando con un archivo PCAP grande, puede ser mejor dividirlo primero en trozos más pequeños.

### **Filtros de captura**

`Capture Filters-`se ingresan antes de que se inicie la captura. Estos usan sintaxis BPF como `host 214.15.2.30` Muy de la misma manera que tcpdump. Tenemos menos opciones de filtro de esta manera, y un filtro de captura dejará que todos los demás tráfico no cumplan explícitamente el conjunto de criterios. Esta es una excelente manera de recortar los datos que escribe en el disco al solucionar problemas de una conexión, como capturar las conversaciones entre dos hosts.

Aquí hay una tabla de filtros de captura comunes y útiles con una descripción de cada uno:

| **Filtros de captura** | **Resultado** |
| --- | --- |
| host x.x.x.x | Capturar solo el tráfico relacionado con un cierto anfitrión |
| net x.x.x.x/24 | Capture el tráfico hacia o desde una red específica (usando la notación de corte para especificar la máscara) |
| SRC/DST NET X.X.X.X/24 | El uso de SRC o DST NET solo capturará el abastecimiento de tráfico de la red especificada o destinada a la red de destino |
| puerto # | filtrará todo el tráfico, excepto el puerto que especifica |
| no puerto # | capturará todo excepto el puerto especificado |
| puerto # y # | Y concatenará sus puertos especificados |
| retrange x-x | Portrange obtendrá el tráfico de todos los puertos dentro del rango solo |
| ip / éter / tcp | Estos filtros solo agarrarán el tráfico de los encabezados de protocolo especificados. |
| transmisión / multidifusión / unidifusión | Agarra un tipo específico de tráfico. uno a uno, uno a muchos, o uno a todos. |

### **Aplicar un filtro de captura**

Antes de aplicar un filtro de captura, echemos un vistazo a los filtros incorporados. Para hacerlo: haga clic en el radial de captura en la parte superior de la ventana Wireshark → luego seleccione Filtros de captura del menú desplegable.

### **Lista de filtros**

![](https://academy.hackthebox.com/storage/modules/81/capture-filter-list.png)

Desde aquí, podemos modificar los filtros existentes o agregar los nuestros propios.

Para aplicar el filtro a una captura,: haga clic en el radial de captura en la parte superior de la ventana de Wireshark → luego seleccione opciones de desplegable → En la nueva ventana Seleccione el filtro desplegable para captura para el filtro seleccionado interfaces o escriba el filtro que deseamos usar.`below the red arrow in the picture below`

### **Aplicar un filtro de captura**

![](https://academy.hackthebox.com/storage/modules/81/how-to-add-cap.png)

### **Mostrar filtros**

`Display Filters-`se usan mientras la captura se ejecuta y después de que la captura se haya detenido. Los filtros de visualización son propietarios de Wireshark, que ofrece muchas opciones diferentes para casi cualquier protocolo.

Aquí hay una tabla de filtros de pantalla comunes y útiles con una descripción de cada uno:

| **Mostrar filtros** | **Resultado** |
| --- | --- |
| ip.addr == x.x.x.x | Capture solo el tráfico relacionado con un cierto anfitrión. Esta es una declaración OR. |
| ip.addr == x.x.x.x/24 | Capture el tráfico relacionado con una red específica. Esta es una declaración OR. |
| ip.src/dst == x.x.x.x | Capturar tráfico hacia o desde un host específico |
| DNS / TCP / FTP / ARP / IP | Filtrar tráfico por un protocolo específico. Hay muchas más opciones. |
| tcp.port == x | Filtrar por un puerto TCP específico. |
| tcp.port / udp.port! = x | capturará todo excepto el puerto especificado |
| y / o / no | Y concatenará, o encontrará cualquiera de las dos opciones, no excluirá su opción de entrada. |

Tenga en cuenta, mientras se procesa el tráfico de filtros de pantalla para mostrar solo lo que se solicita, pero el resto del archivo de captura no se sobrescribirá. La aplicación de filtros de visualización y opciones de análisis hará que Wireshark reprocese los datos de PCAP para aplicar.

### **Aplicando un filtro de visualización**

Aplicar un filtro de visualización es aún más fácil que un filtro de captura. Desde la ventana principal de captura de Wireshark, todo lo que necesitamos hacer es: Seleccione el marcador en la barra de herramientas →, luego seleccione una opción del desplegable. Alternativamente, coloque el cursor en el texto radial → y escriba el filtro que deseamos usar. Si el campo se vuelve verde, el filtro es correcto.`Just like in the image below.`

### **Aplicación de filtros de visualización**

![](https://academy.hackthebox.com/storage/modules/81/display-filter.png)

Cuando use filtros de captura y pantalla, tenga en cuenta que lo que especificamos se toma en un sentido literal. Por ejemplo, el filtrado para el tráfico del puerto 80 no es lo mismo que el filtrado para HTTP. Piense en puertos y protocolos más como pautas en lugar de reglas rígidas. Los puertos se pueden unir y utilizar para diferentes propósitos distintos de lo que se pretendían originalmente. Por ejemplo, el filtrado para HTTP buscará marcadores clave que use el protocolo, como las solicitudes Get/Post, y mostrará los resultados de ellos. El filtrado para el puerto 80 mostrará cualquier cosa enviada o recibida sobre ese puerto, independientemente del protocolo de transporte.

En la siguiente sección, trabajaremos con algunas de las características más avanzadas de Wireshark.