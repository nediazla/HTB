# Capturar con TCPDUMP (Fundamentals Labs)

El propósito de este laboratorio es exponernos a tcpdump y darnos tiempo para familiarizarnos con la terminal y utilizar herramientas dentro de él. Practicaremos varios conceptos básicos de tcpdump, como leer y escribir en archivos, utilizar interruptores básicos y localizar archivos en el terminal. Al completar estos laboratorios, podemos explorar y practicar el uso de diferentes interruptores y funcionalidades dentro de TCPDUMP. Cuando sea cómodo, tómese un tiempo e intente determinar si podemos distinguir cualquier tráfico visible para nosotros en la red.

Tenga en cuenta que este tipo de trabajo a menudo se usa para examinar hosts y servidores específicos con más detalle y descubrir con quién interactúan todos. Este procedimiento también se puede utilizar para identificar los llamados puertas traseras u otras infracciones potenciales. Esto podría usarse para monitorear e registrar toda la comunicación de un servidor para analizar los paquetes enviados y recibidos. Para el análisis en sí, luego usamos varios filtros y patrones para filtrar paquetes sospechosos. Veremos esto en otra sección.

Como el nuevo administrador de la red para la corporación, se nos ha encargado capturar algo de tráfico de red para ayudar a la línea de base y validar la red de la corporación. Como prueba, comenzamos a utilizar TCPDump para obtener una pequeña captura de nuestro tráfico de dominio de transmisión local para garantizar que nuestro dispositivo de captura funcione para realizar esta tarea. Necesitamos asegurarnos de que las herramientas y dependencias requeridas estén instaladas y probar nuestra capacidad de leer el tráfico y capturarlo en un archivo.

Si desea adoptar un enfoque más exploratorio para este laboratorio, he publicado las tareas generales para cumplir. Para obtener un tutorial más detallado de cómo completar cada paso, mire debajo de cada tarea en la burbuja de solución.

---

# **Tareas**

### **Tarea #1**

`Validate Tcpdump is installed on our machine.`

Antes de que podamos comenzar, asegúrese de que tengamos tcpdump instalados. ¿Qué comando usamos para determinar si TCPDUMP está instalado en Linux?

- **Haga clic para mostrar respuesta**
    
    ```
    xnoxos@htb[/htb]$ which tcpdump
    ```
    

---

### **Tarea #2**

`Start a capture.`

Una vez que sabemos que TCPDump está instalado, estamos listos para comenzar nuestra primera captura. Si no estamos seguros de qué interfaces tenemos que escuchar, podemos utilizar un interruptor incorporado para enumerarlos todos para nosotros.

¿Qué interruptor TCPDump se usa para mostrarnos todas las interfaces posibles que podemos escuchar?

- **Haga clic para mostrar respuesta**
    
    `Step one`: Lista de interfaces para capturar.
    
    ```
    xnoxos@htb[/htb]$ tcpdump -D
    ```
    
    `Step two`: Comience nuestra captura.
    
    ```
    xnoxos@htb[/htb]$ tcpdump -i [interface name or #]
    ```
    

---

### **Tarea #3**

`Utilize Basic Capture Filters.`

Ahora que podemos capturar el tráfico, modificemos cómo se nos presenta esa información. Lo lograremos agregando verbosidad a nuestra salida y mostrando contenidos en ASCII y hexadecimal. Una vez que completemos esta tarea, intente nuevamente usando otros interruptores.

Deshabilite la resolución de nombre y muestre números de secuencia relativa para otro desafío.

- **Haga clic para mostrar respuesta**
    
    ```
    xnoxos@htb[/htb]$ tcpdump -i [interface name or #] -vX
    ```
    

---

### **Tarea #4**

`Save a Capture to a .PCAP file.`

Ahora depende de nosotros cómo deseamos capturar y ver la salida. Recuerde, al utilizar los filtros de captura, modificará lo que obtenemos. Tome nuestra primera captura completa del cable y guárdela en un archivo PCAP. Esta será una muestra para basarse en la red empresarial.

- **Haga clic para mostrar respuesta**
    
    ```
    xnoxos@htb[/htb]$ tcpdump -i [interface name or #] -nvw [/path/of/filename.pcap]
    ```
    

---

### **Tarea #5**

`Read the Capture from a .PCAP file.`

Los miembros de nuestro equipo nos han dado un PCAP que capturaron mientras examinaba otra sección de la empresa, lee el archivo PCAP en TCPDUMP y modifica nuestra vista del PCAP para ayudarnos a determinar qué está sucediendo. Podemos deshabilitar el nombre de host y la resolución de puertos por simplicidad y asegurarnos de ver cualquier secuencia TCP y números de reconocimiento en valores absolutos. En aras del laboratorio, utilice el archivo PCAP que creamos en el paso anterior para esta tarea.

- **Haga clic para mostrar respuesta**
    
    ```
    xnoxos@htb[/htb]$ tcpdump -nnSXr [file/to/read.pcap]
    ```
    
    Los conmutadores utilizados anteriormente no resolverán los nombres de host o los números de puerto, se aplicarán a los números de secuencia absoluta y mostrarán contenido en Hex y ASCII al leer el archivo PCAP.
    

Cuando realice las tareas anteriores, responda las preguntas en la parte inferior de la sección para probar nuestra comprensión.