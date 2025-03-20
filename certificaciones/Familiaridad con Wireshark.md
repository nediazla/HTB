# Familiaridad con Wireshark

Este laboratorio tiene como objetivo darle a Wireshark una familiaridad básica y utilizar su interfaz gráfica para realizar capturas de tráfico. Pasaremos tiempo usando filtros de captura y visualización y acostumbrándonos a las diferentes salidas que se muestran por la herramienta.

Un usuario ha llevado su computadora portátil al servicio de ayuda quejándose de tiempos de carga inusualmente largos cuando intentan navegar por la web. Dijeron que es casi como si la red estuviera empantanada y que la computadora está haciendo muchas conexiones diferentes. Nos encargaron validar la PC que funciona correctamente. Para hacerlo, lo conectamos a la red e iniciamos una captura de paquetes mientras navegamos por la web. Analice lo que se puede ver en la salida para determinar si algo está mal. Solo nos importa la computadora portátil en cuestión en este momento, así que filtre cualquier tráfico que no esté destinado o obtenga de ella.

Si desea adoptar un enfoque más exploratorio para este laboratorio, he publicado las tareas generales para lograr. Para obtener un tutorial más detallado de cómo completar cada paso, mire debajo de cada tarea en la burbuja de solución.

---

# **Tareas**

### **Tarea #1**

`Validate Wireshark is installed, then open Wireshark and familiarize yourself with the GUI windows and toolbars.`

Tómese un minuto y explore la gui de Wireshark. Asegúrese de saber qué opciones residen en qué pestañas en los menús de comando. Preste especial atención a la pestaña Captura y lo que reside dentro de ella.

---

### **Tarea #2**

`Select an interface to run a capture on and create a capture filter to show only traffic to and from your host IP.`

Elija su interfaz activa (ETH0 o su tarjeta WiFi) para capturar.

- **Haga clic para mostrar respuesta**

---

### **Tarea #3**

`Create a capture filter.`

A continuación, queremos crear un filtro de captura para mostrarnos solo un abastecimiento de tráfico o destinado a nuestra dirección IP y aplicarlo.

- **Haga clic para mostrar respuesta**

---

### **Tarea #4**

`Navigate to a webpage to generate some traffic.`

Abra un navegador web y navegue a Pepsi.com. Repita este paso para http://apache.org. Mientras la página se está cargando, vuelva a la ventana Wireshark. Deberíamos ver que el tráfico fluya a través de nuestra ventana de captura. Una vez que la página se haya cargado, detenga la captura haciendo clic en el cuadrado rojo etiquetado para detener en la barra de acción.

---

### **Tarea #5**

`Use the capture results to answer the following questions.`

¿Se están estableciendo múltiples sesiones entre la máquina y el servidor web? ¿Cómo puedes decirlo?

¿Qué protocolos de nivel de aplicación se muestran en los resultados?

¿Podemos discernir cualquier cosa en el texto claro? ¿Qué fue?

- **Haga clic para mostrar respuesta**

No te detengas aquí. Tómese un tiempo para familiarizarnos con la salida que proporciona Wireshark y cómo podemos transformar nuestra vista. También podemos utilizar los archivos PCAP de muestra que se encuentran en https://wiki.wireshark.org/samplecaptures para analizar diferentes protocolos e incluso cosas como el tráfico y los virus de ICS Scada. Si está conectado a la red de la Academia para realizar este laboratorio, tómese un tiempo para capturar el tráfico en la interfaz proporcionada. Se puede encontrar algo de tráfico de red interesante si uno parece lo suficientemente duro.

---

# **Resumen**

Al final de este laboratorio, debemos estar familiarizados con Wireshark y cómo se ve y opera la GUI. Nos hemos familiarizado con la barra de comando y la barra de acción y lo que reside dentro de cada pestaña respectiva. Uno debe entender cómo seleccionar una interfaz para capturar y comenzar con éxito una captura con un filtro aplicado. Experimente con algunos filtros de su propio diseño para ver qué diferentes combinaciones se pueden usar y cómo da forma a los resultados.