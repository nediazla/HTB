# Análisis de tráfico de red

`Network Traffic Analysis (NTA)` puede describirse como el acto de examinar el tráfico de la red para caracterizar los puertos y protocolos comunes utilizados, establecer una línea de base para nuestro entorno, monitorear y responder a las amenazas y garantizar la mayor visión posible de la red de nuestra organización.

Este proceso ayuda a los especialistas en seguridad a determinar las anomalías, incluidas las amenazas de seguridad en la red, las amenazas tempranas y efectivas para las amenazas. El análisis de tráfico de red también puede facilitar el proceso de cumplir con las pautas de seguridad. Los atacantes actualizan sus tácticas con frecuencia para evitar la detección y aprovechar las credenciales legítimas con herramientas que la mayoría de las empresas permiten en sus redes, lo que hace que la detección y, posteriormente, la respuesta sea desafiante para los defensores. En tales casos, el análisis de tráfico de red puede volver a ser útil. Los casos de uso diario de NTA incluyen:

---

```
Collecting
```

Tráfico en tiempo real dentro de la red para analizar las próximas amenazas.

---

```
Setting
```

Una línea de base para las comunicaciones de red diarias.

---

```
Identifying
```

y analizar el tráfico de puertos no estándar, hosts sospechosos y problemas con protocolos de redes como errores HTTP, problemas con TCP u otras configuraciones erróneas de redes.

---

```
Detecting
```

Malware en el cable, como ransomware, hazañas e interacciones no estándar.

---

La NTA también es útil al investigar incidentes pasados ​​y durante la caza de amenazas.

---

Trate de imaginar a un actor de amenaza que se dirige e infiltrara nuestra red. Si desean violar la red, los atacantes deben inevitablemente interactuar y comunicarse con nuestra infraestructura. La comunicación de la red se lleva a cabo sobre muchos puertos y protocolos diferentes, todos utilizados simultáneamente por empleados, equipos y clientes. Para detectar el tráfico malicioso, necesitaríamos utilizar nuestro conocimiento del tráfico de red típico dentro de nuestro enclave. Hacerlo reducirá nuestra búsqueda y nos ayudará a encontrar e interrumpir rápidamente la comunicación adversa.

Por ejemplo, si detectamos muchos `SYN` Los paquetes en los puertos que nunca utilizamos (o raramente) en nuestra red, podemos concluir que es muy probable que alguien intente determinar qué puertos están abiertos en nuestros hosts. Acciones como esta son marcadores típicos de un `portscan`. Realizar dicho análisis y llegar a tales conclusiones requiere habilidades y conocimientos específicos.

---

# **Habilidades y conocimientos requeridos**

Las habilidades que estamos a punto de enumerar y describir requieren un conocimiento teórico y práctico adquirido con el tiempo. No tenemos que saber todo de memoria, pero debemos saber qué buscar cuando ciertos aspectos del contenido parecen desconocidos. Esto se aplica no solo a NTA sino también a la mayoría de los otros temas con los que trataremos en ciberseguridad.

### **Modelo TCP/IP y OSI**

Esta comprensión asegurará que comprendamos cómo interactúan el tráfico de redes y las aplicaciones de host.

### **Conceptos de red básicos**

Comprender qué tipos de tráfico veremos en cada nivel incluye una comprensión de las capas individuales que componen el modelo TCP/IP y OSI y los conceptos de conmutación y enrutamiento. Si tocamos una red en un enlace de columna vertebral, veremos mucho más tráfico de lo habitual, y será muy diferente de lo que encontramos aprovechar un interruptor de oficina.

### **Puertos y protocolos comunes**

Identificar puertos y protocolos estándar rápidamente y tener una comprensión funcional de cómo se comunican asegurará que podamos identificar el tráfico de red potencialmente malicioso o malformado.

### **Conceptos de paquetes IP y los subcapas**

El conocimiento fundamental de cómo se comunican TCP y UDP, como mínimo, asegurar que comprendamos lo que vemos o estamos buscando. TCP, por ejemplo, está orientado a la corriente y nos permite seguir una conversación entre los anfitriones fácilmente. UDP es rápido pero no está preocupado por la integridad, por lo que sería más difícil recrear algo de este tipo de paquete.

### **Encapsulación de transporte de protocolo**

Cada capa encapsulará lo anterior. Ser capaz de leer o diseccionar cuando estos cambios de encapsulación nos ayudará a avanzar a través de los datos más rápido. Es fácil ver sugerencias basadas en encapsulación.

---

# **Ambiente y equipo**

La siguiente lista contiene muchas herramientas y tipos de equipos diferentes que se pueden utilizar para realizar análisis de tráfico de red. Cada uno proporcionará una forma diferente de capturar o diseccionar el tráfico. Algunos ofrecen formas de copiar y capturar, mientras que otros leen e ingieren. Este módulo explorará solo algunos de estos ([Wireshark](https://www.wireshark.org/) y [tcpdump](https://www.tcpdump.org/) principalmente). Tenga en cuenta que estas herramientas no están estrictamente orientadas a los administradores. Muchos de estos también se pueden usar por razones maliciosas.

### **Herramientas comunes de análisis de tráfico**

| **Herramienta** | **Descripción** |
| --- | --- |
| `tcpdump` | [tcpdump](https://www.tcpdump.org/) es una utilidad de línea de comandos que, con la ayuda de libpcap, captura e interpreta el tráfico de red desde una interfaz de red o un archivo de captura. |
| `Tshark` | [Tshark](https://www.wireshark.org/docs/man-pages/tshark.html) es un analizador de paquetes de red muy parecido a TCPDUMP. Capturará paquetes de una red en vivo o leerá y decodificará desde un archivo. Es la variante de línea de comandos de Wireshark. |
| `Wireshark` | [Wireshark](https://www.wireshark.org/) es un analizador de tráfico de red gráfico. Captura y decodifica los marcos del cable y permite una mirada profunda en el entorno. Puede ejecutar muchos disectores diferentes contra el tráfico para caracterizar los protocolos y aplicaciones y proporcionar información sobre lo que está sucediendo. |
| `NGrep` | [Ngrep](https://github.com/jpr5/ngrep) es una herramienta de coincidencia de patrones construida para servir una función similar a GREP para las distribuciones de Linux. La gran diferencia es que funciona con paquetes de tráfico de red. NGREP comprende cómo leer el tráfico o el tráfico en vivo desde un archivo PCAP y utilizar expresiones de regex y sintaxis BPF. Esta herramienta brilla mejor cuando se usa para depurar el tráfico de protocolos como HTTP y FTP. |
| `tcpick` | [tcpick](http://tcpick.sourceforge.net/index.php?p=home.inc) es un sniffer de paquetes de línea de comandos que se especializa en el seguimiento y el reensalamiento de las transmisiones TCP. La funcionalidad para leer una transmisión y volver a armarlo a un archivo con TCPick es excelente. |
| `Network Taps` | Grifos ([Gigamón](https://www.gigamon.com/), [Niagra-Taps](https://www.niagaranetworks.com/products/network-tap)) son dispositivos capaces de tomar copias del tráfico de red y enviarlos a otro lugar para su análisis. Estos pueden estar en línea o fuera de la banda. Pueden capturar y analizar activamente el tráfico directa o pasivamente poniendo el paquete original en el cable como si nada hubiera cambiado. |
| `Networking Span Ports` | [Puertos de span](https://en.wikipedia.org/wiki/Port_mirroring) son una forma de copiar marcos de la capa dos o tres dispositivos de red durante el procesamiento de salida o entrada y enviarlos a un punto de recolección. A menudo, se refleja un puerto para enviar esas copias a un servidor de registro. |
| `Elastic Stack` | El [Pila elástica](https://www.elastic.co/elastic-stack) es una culminación de las herramientas que pueden tomar datos de muchas fuentes, ingerir los datos y visualizarlos, para permitir la búsqueda y el análisis de ellas. |
| `SIEMS` | `SIEMS`(como [Flojo](https://www.splunk.com/en_us)) son un punto central en el que los datos se analizan y visualizan. Alerta, análisis forense y controles diarios contra el tráfico son todos los casos de uso para un SIEM. |
| y otros. |  |

---

# **Sintaxis de BPF**

Muchas de las herramientas mencionadas anteriormente tienen su sintaxis y comandos para utilizar, pero una que se comparte entre ellas es [Filtro de paquetes de Berkeley (BPF)](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter) sintaxis. Esta sintaxis es el método principal que utilizaremos. En esencia, BPF es una tecnología que permite una interfaz sin procesar para leer y escribir desde la capa de enlace de datos. Con todo esto en mente, nos preocupamos por BPF debido a las habilidades de filtrado y decodificación que nos proporciona. Utilizaremos la sintaxis BPF a través del módulo, por lo que una comprensión básica de cómo se configura un filtro BPF puede ser útil. Para obtener más información sobre la sintaxis de BPF, consulte esto [referencia](https://www.ibm.com/docs/en/qsip/7.4?topic=queries-berkeley-packet-filters).

---

# **Realización de análisis de tráfico de red**

Realizar el análisis puede ser tan simple como ver pasar el tráfico en vivo en nuestra consola o tan complejo como capturar datos con un toque, enviarlo de nuevo a un SIEM para ingestión y analizar los datos de PCAP para firmas y alertas relacionadas con tácticas y técnicas comunes.

Como mínimo, para escuchar pasivamente, debemos conectarnos al segmento de red en el que deseamos escuchar. Esto es especialmente cierto en un entorno conmutado donde las VLAN y los puertos de conmutadores no reenviarán el tráfico fuera de su dominio de transmisión. Con eso en mente, si deseamos capturar el tráfico de una VLAN específica, nuestro dispositivo de captura debe estar conectado a esa misma red. Los dispositivos como los grifos de red, las configuraciones de conmutación o el enrutador, como los puertos de span, y el reflejo de puertos pueden permitirnos obtener una copia de todo el tráfico que atraviesa un enlace específico, independientemente del segmento de red o destino al que pertenece.

### **Flujo de trabajo de NTA**

El análisis de tráfico no es una ciencia exacta. NTA puede ser un proceso muy dinámico y no es un bucle directo. Está muy influenciado por lo que estamos buscando (errores de red versus acciones maliciosas) y dónde tenemos visibilidad en nuestra red. Realizar análisis de tráfico puede destilar a algunos inquilinos básicos.

### **Flujo de trabajo de NTA**

![](https://academy.hackthebox.com/storage/modules/81/workflow.png)

### **1. Ingestar tráfico**

Una vez que hemos decidido nuestra ubicación, comience a capturar el tráfico. Utilice filtros de captura si ya tenemos una idea de lo que estamos buscando.

### **2. Reduzca el ruido filtrando**

Capturar el tráfico de un enlace, especialmente uno en un entorno de producción, puede ser extremadamente ruidoso. Una vez que completamos la captura inicial, un intento de filtrar el tráfico innecesario desde nuestra vista puede facilitar el análisis. (Tráfico de transmisión y multidifusión, por ejemplo).

### **3. Analizar y explorar**

Ahora es el momento de comenzar a tallar datos pertinentes al problema que estamos persiguiendo. Mire los hosts específicos, los protocolos, incluso las cosas tan específicas como las banderas establecidas en el encabezado TCP. Las siguientes preguntas nos ayudarán:

1. ¿Está el tráfico encriptado o el texto plano? ¿Debería ser?
2. ¿Podemos ver usuarios que intentan acceder a recursos a los que no deberían tener acceso?
3. ¿Están hablando diferentes anfitriones entre sí que generalmente no?

### **4. Detectar y alertar**

1. ¿Estamos viendo algún error? ¿Un dispositivo no responde que debería ser?
2. Use nuestro análisis para decidir si lo que vemos es benigno o potencialmente malicioso.
3. Otras herramientas como IDS e IP pueden ser útiles en este momento. Pueden ejecutar heurísticas y firmas contra el tráfico para determinar si algo dentro es potencialmente malicioso.

### **5. Arreglar y monitorear**

Fix and Monitor no es parte del bucle, pero debe incluirse en cualquier flujo de trabajo que realicemos. Si hacemos un cambio o solucionamos un problema, debemos continuar monitoreando la fuente durante un tiempo para determinar si el problema se ha resuelto.