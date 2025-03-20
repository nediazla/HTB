# Uso avanzado de Wireshark

En esta sección, cubriremos un uso avanzado con Wireshark. Los desarrolladores del proyecto han incluido muchas capacidades diferentes que van desde el seguimiento de las conversaciones de TCP hasta las credenciales inalámbricas. La inclusión de muchos complementos diferentes hace que Wireshark sea una de las mejores herramientas de análisis de tráfico.

---

# **Complementos**

Los radiales de análisis y estadísticas proporcionan una gran cantidad de complementos para correr contra la captura. En esta sección, trabajaremos en un par de ellos. Cubriríamos todos los cuales ofrece Wireshark, pero lamentablemente, simplemente no se puede alcanzar en un módulo introductorio. Insto a todos a experimentar y jugar a medida que avanzamos en este viaje.

### **Las estadísticas y las pestañas de análisis**

Las estadísticas y las pestañas de análisis pueden proporcionarnos una gran visión de los datos que estamos examinando. Desde estos puntos, podemos utilizar muchos de los complementos horneados que Wireshark tiene para ofrecer.

Los complementos aquí pueden darnos informes detallados sobre el tráfico de red que se está utilizando. Puede mostrarnos todo, desde los principales conversadores en nuestro entorno hasta conversaciones específicas e incluso desglose por IP y protocolo.

### **Pestaña de estadística**

![](https://academy.hackthebox.com/storage/modules/81/wireshark-statistics.png)

### **Analizar**

Desde la pestaña Analizar, podemos utilizar complementos que nos permitan hacer cosas como seguir las transmisiones de TCP, filtrar los tipos de conversación, preparar nuevos filtros de paquetes y examinar la información experta que Wireshark genera sobre el tráfico. A continuación hay algunos ejemplos de cómo usar estos complementos.

### **Análisis de la pestaña**

![](https://academy.hackthebox.com/storage/modules/81/analyze.png)

### **Siguiendo las transmisiones TCP**

Wireshark puede unir los paquetes TCP para recrear toda la transmisión en un formato legible. Esta habilidad también nos permite extraer datos (`images, files, etc.`) fuera de la captura. Esto funciona para casi cualquier protocolo que utilice TCP como mecanismo de transporte.

Para utilizar esta función:

- Haga clic derecho en un paquete de la transmisión que deseamos recrear.
- Seleccione Seguir → TCP
- Esto abrirá una nueva ventana con la transmisión unida de nuevo. Desde aquí, podemos ver toda la conversación.

### **Sigue una transmisión a través de GUI**

![](https://academy.hackthebox.com/storage/modules/81/follow-tcp.gif)

Alternativamente, podemos utilizar el filtro `tcp.stream eq #` Para encontrar y rastrear conversaciones capturadas en el archivo PCAP.

# **Filtrar para una transmisión TCP específica**

![](https://academy.hackthebox.com/storage/modules/81/tcp-stream.gif)

Observe que los primeros tres paquetes en la imagen de arriba tienen un apretón de manos TCP completo. Siguiendo esos paquetes, podemos ver la transferencia de datos. Hemos despejado cualquier cosa que no esté relacionado fuera de la vista utilizando el filtro, y ahora podemos ver la conversación en orden.

### **Extraer datos y archivos de una captura**

Wireshark puede recuperar muchos tipos diferentes de datos de las transmisiones. Requiere que hayas capturado toda la conversación. De lo contrario, esta capacidad no volverá a poner un datagrama incompleto. Si queremos una comprensión más profunda de cómo funciona esta capacidad, consulte el módulo 101 de redes o investigar fragmentación TCP/IP.

Para extraer archivos de una transmisión:

- Detente tu captura.
- Seleccione el archivo radial → exportar →, luego seleccione el formato de protocolo para extraer.
- (DICOM, HTTP, SMB, etc.)

### **Extraer archivos de la GUI**

![](https://academy.hackthebox.com/storage/modules/81/extract-http.gif)

Otra forma emocionante de sacar datos del archivo PCAP proviene de FTP. El protocolo de transferencia de archivos mueve los datos entre un servidor y el host para sacarlo de los bytes sin procesar y reconstruir el archivo. (Imagen, documentos de texto, etc.) ftp utiliza TCP como su protocolo de transporte y utiliza puertos `20 & 21` para funcionar. El puerto TCP 20 se utiliza para transferir datos entre el servidor y el host, mientras que el puerto 21 se utiliza como puerto de control FTP. Cualquier comando como inicio de sesión, enumerar archivos y emitir descarga o carga ocurre a través de este puerto. Para hacerlo, necesitamos mirar las diferentes`FTP`Mostrar filtros en Wireshark. Se puede encontrar una lista completa de estos [aquí](https://www.wireshark.org/docs/dfref/f/ftp.html). Por ahora, veremos tres:

- `ftp`Mostrará cualquier cosa sobre el protocolo FTP.
    - Podemos utilizar esto para tener una idea de qué hosts/servidores están transfiriendo datos a través de FTP.

### **Disector FTP**

![](https://academy.hackthebox.com/storage/modules/81/ftp-disector.png)

- `ftp.request.command`Mostrará cualquier comando enviado a través del canal FTP-Control (puerto 21)
    - Podemos buscar información como nombres de usuario y contraseñas con este filtro. También puede mostrarnos nombres de archivo para cualquier cosa solicitada.

### **Filtro FTP-Request-Command**

![](https://academy.hackthebox.com/storage/modules/81/ftp-request-command.png)

- `ftp-data`Mostrará cualquier datos transferidos a través del canal de datos (puerto 20)
    - Si filtramos una conversación y utilizamos `ftp-data`, podemos capturar cualquier cosa enviada durante la conversación. Podemos reconstruir cualquier cosa transferida colocando los datos sin procesar en un nuevo archivo y nombrándolo adecuadamente.

### **Filtro de datos FTP**

![](https://academy.hackthebox.com/storage/modules/81/ftp-data.png)

Dado que FTP utiliza TCP como su mecanismo de transporte, podemos utilizar el `follow tcp stream` función que utilizamos anteriormente en la sección para agrupar cualquier conversación que deseamos explorar. Los pasos básicos para diseccionar los datos FTP de un PCAP son los siguientes:

1. Identificar cualquier tráfico FTP utilizando el `ftp` Filtro de visualización.
2. Mire los controles de comando enviados entre el servidor y los hosts para determinar si se transfirió algo y quién lo hizo con el `ftp.request.command` filtrar.
3. Elija un archivo, luego filtre para `ftp-data`. Seleccione un paquete que corresponda con nuestro archivo de interés y siga la secuencia TCP que se correlaciona con él.
4. Una vez hecho, cambie "`Show and save data as`" a "`Raw`"Y guarde el contenido como el nombre del archivo original.
5. Valide la extracción verificando el tipo de archivo.