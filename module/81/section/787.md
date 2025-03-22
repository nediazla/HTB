# Interrogar el tráfico de red con filtros de captura y visualización

Este laboratorio tiene como objetivo proporcionar cierta exposición a interrogar el tráfico de la red y brindar a todos algunas prácticas valiosas implementando filtros de paquetes. Utilizaremos filtros como `host`, `port`, `protocol`, y más para cambiar nuestra vista mientras cava a través de un archivo .pcap.

Ahora que hemos demostrado ser capaces de capturar el tráfico de red para la corporación, la gerencia nos ha encargado de realizar un análisis rápido del tráfico que nuestro equipo ha capturado al inspeccionar la red. El objetivo es determinar qué servidores responden las solicitudes de DNS y HTTP/S en nuestra red local.

Si desea adoptar un enfoque más exploratorio para este laboratorio, he publicado las tareas generales para cumplir. Para obtener un tutorial más detallado de cómo completar cada paso, mire debajo de cada tarea en la burbuja de solución.

---

# **Tareas**

Utilización `TCPDump-lab-2.zip` En los recursos opcionales, realice el laboratorio lo mejor que pueda. Encontrar todo en el primer disparo no es el objetivo. Nuestra comprensión de los conceptos es nuestra principal preocupación. A medida que realizamos estas acciones repetidamente, será más fácil.

### **Tarea #1**

`Read a capture from a file without filters implemented.`

Para comenzar, examinemos este PCAP sin filtros aplicados.

- **Haga clic para mostrar respuesta**
    
    ```
    
    ```
    

---

### **Tarea #2**

`Identify the type of traffic seen.`

Tome nota de qué tipos de tráfico se puede ver. (Puertos utilizados, protocolos, cualquier otra información que considere relevante). ¿Qué filtros podemos usar para facilitar esta tarea?

¿Qué tipo de tráfico vemos?

Protocolos comunes:

Puertos utilizados:

- **Haga clic para mostrar respuestas**

---

### **Tarea #3**

`Identify conversations.`

Hemos examinado los conceptos básicos de este tráfico, ahora determine si nota algún patrón con el tráfico.

¿Estás notando alguna conexión común entre un servidor y un host? Si es así, ¿quién?

¿Cuáles son los números de puerto de cliente y servidor utilizados en el primer apretón de manos TCP TCP completo?

¿Quiénes son los servidores en estas conversaciones? ¿Cómo sabemos?

¿Quiénes son los anfitriones receptores?

- **Haga clic para mostrar respuesta**

---

### **Tarea #4**

`Interpret the capture in depth.`

Ahora que estamos familiarizados con el archivo PCAP, hagamos un análisis. Utilice cualquier sintaxis necesaria para lograr la respuesta a las preguntas a continuación.

¿Cuál es la marca de tiempo de la primera conversación establecida en el archivo PCAP?

¿Cuál es la dirección IP de apache.org de las respuestas del servidor DNS?

¿Qué protocolo se está utilizando en esa primera conversación? (nombre/#)

- **Haga clic para mostrar respuesta**

---

### **Tarea #5**

`Filter out traffic.`

Es hora de eliminar algunos de estos datos ahora. Recargar el archivo PCAP y filtrar todo el tráfico que no sea DNS. ¿Qué puedes ver?

¿Quién es el servidor DNS para este segmento?

¿Qué nombres de dominio se solicitaron en el archivo PCAP?

¿Qué tipo de registros DNS se pueden ver?

- **Haga clic para mostrar respuesta**
    
    ```
    
    ```
    

Ahora que solo estamos viendo el tráfico de DNS y tenemos una mejor comprensión de cómo aparece el paquete, intente responder las siguientes preguntas sobre la resolución de nombres en la empresa: ¿Quién solicita un registro para apache.org? (nombre de host o IP)

¿Qué información proporciona un registro A?

¿Quién es el servidor DNS que responde en el PCAP? (nombre de host o IP)

---

### **Tarea #6**

`Filter for TCP traffic.`

Ahora que tenemos una idea clara de nuestro servidor DNS, busquemos cualquier servidor web presente. Filtre la vista para que solo veamos el tráfico relacionado con HTTP o HTTPS. ¿Qué páginas web se solicitaron?

¿Cuáles son los métodos de solicitud HTTP más comunes de este PCAP?

¿Cuál es la respuesta HTTP más común de este PCAP?

- **Haga clic para mostrar respuesta**

### **Tarea #7**

`What can you determine about the server in the first conversation.`

Echemos un vistazo más de cerca. ¿Qué se puede determinar sobre el servidor web en la primera conversación? ¿Algo sobresale? Para obtener algo de claridad, asegúrese de que nuestra vista incluya la salida HEX y ASCII para PCAP.

¿Podemos determinar qué aplicación está ejecutando el servidor web?

- **Haga clic para mostrar respuesta**

---

# **Resumen**

A través de este laboratorio, ampliamos nuestros horizontes mientras utilizamos TCPDUMP para analizar el tráfico PCAP. Aprendimos cómo capturar y mostrar filtros de manera efectiva, diseccionar el tráfico para determinar qué protocolos se estaban ejecutando en el entorno e incluso obtuvimos información crítica sobre nuestros segmentos empresariales, DNS y servidores web. Continúe jugando solo y vea qué tan profundo va la madriguera del conejo. ¿Puede capturar el tráfico en su red doméstica y responder las mismas preguntas?

---

# **Consejos para el análisis**

A continuación se muestra una lista de preguntas que podemos hacernos durante el proceso de análisis para mantener el rastro.

---

¿Qué tipo de tráfico ves? (Protocolo, puerto, etc.)

---

¿Hay más de una conversación? (¿cuántos?)

---

¿Cuántos anfitriones únicos?

---

¿Cuál es la marca de tiempo de la primera conversación en el PCAP (tráfico TCP)

---

¿Qué tráfico puedo filtrar para limpiar mi vista?

---

¿Quiénes son los servidores en el PCAP? (Respondiendo en puertos conocidos, 53, 80, etc.)

---

¿Qué registros se solicitaron o se utilizaron los métodos? (Obtener, publicar, DNS A Records, etc.)

---