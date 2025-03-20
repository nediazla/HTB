# Incepción de paquetes, diseccionar el tráfico de red con Wireshark

El propósito de este laboratorio es proporcionar experiencia con la disección del tráfico en Wireshark. Tendremos la oportunidad de sacar objetos del tráfico de red capturado previamente junto con extraer datos del tráfico en vivo.

Se nos ha proporcionado un archivo de captura de paquetes que contiene datos de una sesión web sin cifrar. Hay una imagen integrada que debe usarse como evidencia de uso inadecuado de la red. El administrador de seguridad cree que el usuario está enviando mensajes ocultos detrás de la imagen. Usando Wireshark, aplique filtros para localizar y extraer la evidencia.

Si desea adoptar un enfoque más exploratorio para este laboratorio, he publicado las tareas generales para lograr. Para obtener un tutorial más detallado de cómo completar cada paso, mire debajo de cada tarea en la burbuja de solución.

---

# **Tareas**

Utilización `Wireshark-lab-2.zip` En los recursos opcionales, realice el laboratorio lo mejor que pueda.

### **Tarea #1**

`Open a pre-captured file (HTTP extraction)`

En Wireshark, seleccione Archivo → Abrir →, luego navegue a Wireshark-Lab-2.pcap. Abra el archivo.

### **Tarea #2**

`Filter the results.`

Ahora que tenemos el archivo PCAP abierto en Wireshark, podemos ver bastante tráfico dentro de este archivo de captura. Tiene alrededor de 1171 paquetes en total, y de ellos, menos de 20 son paquetes HTTP específicamente. Tómese un minuto para examinar el archivo PCAP, familiarizarse con las conversaciones que se realizan al pensar en la tarea de lograr. Nuestro objetivo es extraer imágenes potenciales integradas para evidencia. Según lo que nos ha pedido, aclaremos nuestra opinión filtrando solo el tráfico HTTP.

Aplique un filtro para incluir solo solicitudes HTTP (80/TCP).

- **Haga clic para mostrar respuesta**
    
    `Step one`: Haga clic dentro de la barra de herramientas del filtro de visualización en la parte superior de su pantalla y escriba `HTTP`. Si es correcto, la barra se iluminará verde.
    

Tenga en cuenta cómo esto elimina los datagramas TCP o IP adicionales de la ventana y nos permite centrarnos en la comunicación únicamente con HTTP. Desde aquí, podemos ver varios datagramas HTTP básicos que contienen el método GET y 200 respuestas OK. Estos son interesantes porque ahora podemos ver que un cliente solicitó varios archivos, y el servidor respondió con un OK. Si seleccionamos una de las respuestas OK, podemos seguir esa secuencia y ver la transferencia de datos a través de TCP. Vamos a darle una oportunidad.

### **Tarea #3**

`Follow the stream and extract the item(s) found.`

Entonces, ahora que hemos establecido que hay tráfico HTTP en este archivo de captura, intentemos obtener algunos de los elementos adentro según lo solicitado. Lo primero que debemos hacer es seguir la transmisión para una de las transferencias de archivos. Con nuestro `http` Filtro aún aplicado, busque una de las líneas en las que el servidor web responde con un mensaje "200 OK" que actúa como un reconocimiento/recibo para la solicitud GET de los usuarios. Ahora seleccionemos ese paquete y siga el flujo TCP.

- **Haga clic para mostrar respuesta**
    
    `Step one`: Seleccione un paquete con 200 Aceptar en el campo de información. Haga clic derecho en el paquete.
    
    `Step two`: En el menú presentado, seleccione Seguir → TCP Stream. Esto abrirá una nueva ventana con toda la transmisión TCP. También aplicará el filtro de visualización `tcp.stream eq #`.
    
    `Step three`: Tomemos un segundo para mirar los datos para asegurarse de que el archivo parezca transferirse.
    

Ahora que validamos la transferencia ocurrida, Wireshark puede hacer que sea extremadamente fácil extraer archivos del tráfico HTTP. Podemos verificar para ver si se extrajo un archivo de imagen buscando el `JFIF` formato en los paquetes. El formato de intercambio de archivo JPEG `JFIF` nos alertará sobre la presencia de cualquier archivo de imagen JPEG. Estamos buscando este formato porque es el tipo de archivo más común para las imágenes junto con el formato PNG. Con eso en mente, probablemente veremos una imagen en este formato para nuestra investigación.

Verifique la presencia de archivos JFIF en el tráfico HTTP.

- **Haga clic para mostrar respuesta**
    
    Borrar el filtro de visualización que se usaba y aplica anteriormente `http && image-jfif` Como filtro de pantalla. Aplique el filtro "HTTP && Image-JFif" para incluir solo paquetes HTTP (80/TCP) junto con un filtro para incluir solo los archivos JPEG deben reducir nuestros resultados a solo unos pocos paquetes. `3` más o menos.
    

Ahora que estamos seguros de que los archivos de imagen se transfirieron entre el host sospechoso y el servidor los sacamos de la captura. Para hacer esto, necesitamos exportar los objetos fuera del tráfico HTTP.

- **Haga clic para mostrar respuesta**
    
    Para exportar las imágenes: seleccione “Archivo → Exportar objetos → Http → `file.JPG`.
    
    Esto le dirá a Wireshark que extraiga los objetos solicitados del tráfico HTTP. Podemos guardar una copia del archivo localmente.
    

En este punto, ahora deberíamos tener los archivos de imagen que nuestro administrador de seguridad nos solicitó que capturáramos si existieran. Ahora pueden examinar el archivo para determinar si algún dato estaba oculto dentro de él.

---

# **Captura y análisis en vivo**

En el escenario anterior, practicamos el filtrado en un archivo pre-capturado. Ahora es el momento de hacer algunas capturas de paquetes en vivo. Nos conectaremos al Laboratorio de la Academia y olfateamos el tráfico en vivo de un host en la red para completar esta porción.

Después de analizar el tráfico PCAP, el Gerente de Seguridad ha regresado y confirmó que el usuario estaba de contrabando datos fuera de la red a través de las imágenes. Está solicitando que ahora capturemos el tráfico para determinar si algo más está sucediendo desde el host del usuario `172.16.10.2`. Tendremos que comenzar una captura, clasificar y filtrar los datos, y extraer cualquier cosa significativa a la investigación.

---

# **Conectividad al laboratorio**

El acceso al entorno de laboratorio para completar esta parte del laboratorio será un poco diferente. Estamos usando [Xfreerdp](https://manpages.ubuntu.com/manpages/trusty/man1/xfreerdp.1.html) Para proporcionarnos acceso de escritorio a la máquina virtual de laboratorio para utilizar Wireshark desde el entorno.

Nos conectaremos al Laboratorio de la Academia, como lo normal, utilizando su propia VM con una tecla VPN HTB Academy o el Pwnbox integrado en la sección Módulo. Puede iniciar el cliente FreerDP en el PWNBox escribiendo lo siguiente en su carcasa una vez que el objetivo genera:

Código: intento

```bash
xfreerdp /v:<target IP> /u:htb-student /p:HTB_@cademy_stdnt!

```

Puedes encontrar el `target IP`, `Username`, y `Password` necesario a continuación:

- Haga clic a continuación en la sección Preguntas para generar el host de destino y obtener una dirección IP.
    - `IP` ==
    - `Username`== Htb-Student
    - `Password` == htb_@cademy_stdnt!

### **Iniciar una captura de Wireshark**

Estaremos olfatear el tráfico del host en el que registramos desde nuestro propio VM o Pwnbox. Utilización de la interfaz `ENS224` En Wireshark, deje que la captura funcione durante unos minutos antes de detenerla. Nuestro objetivo es determinar si algo está sucediendo con el host del usuario y otra máquina en las redes corporativas o externas.

### **Autoenize**

Antes de seguir estas tareas a continuación, tómese el tiempo para atravesar nuestro tráfico PCAP sin guía. Use las habilidades que hemos probado previamente, como seguir corrientes, análisis de conversaciones y otras habilidades para determinar qué está sucediendo. Tenga en cuenta estas preguntas al realizar el análisis:

- ¿Cuántas conversaciones se pueden ver?
- ¿Podemos determinar quiénes son los clientes y los servidores?
- ¿Qué protocolos se están utilizando?
- ¿Está sucediendo algo notable? (Los puertos se utilizan mal, clara de tráfico de texto o credenciales, etc.)

En este laboratorio, nos preocupa los anfitriones 172.16.10.2 y 172.16.10.20 mientras realizamos los siguientes pasos. En nuestro análisis, deberíamos haber notado algo de tráfico web entre estos hosts y algún tráfico FTP. Cavemos un poco más.

### **Análisis FTP**

Al examinar el tráfico, capturamos, ¿se notó algún tráfico relacionado con FTP? ¿Quién era el servidor para ese tráfico?

¿Podíamos determinar si un usuario autenticado estaba realizando estas acciones, o eran anónimos?

### **Filtrar los resultados**

Ahora que hemos visto un tráfico interesante, intentemos tomar el archivo del cable.

Examine los comandos FTP para determinar lo que necesita inspeccionar, y luego extrae los archivos de los datos FTP y vuelva a montarlo

- **Haga clic para mostrar respuesta**
    1. Identificar cualquier tráfico FTP utilizando el `ftp` Filtro de visualización.
    2. Mire los controles de comando enviados entre el servidor y los hosts para determinar si se transfirió algo y quién lo hizo con el `ftp.request.command` filtrar.
    3. Elija un archivo, luego filtre para `ftp-data`. Seleccione un paquete que corresponda con nuestro archivo de interés y siga la secuencia TCP que se correlaciona con él.
    4. Una vez hecho esto, cambie "Muestre y guarde los datos como" a "Raw" y guarde el contenido como el nombre del archivo original.
    5. Valide la extracción verificando el tipo de archivo.

### **Análisis HTTP**

Deberíamos haber visto un poco de tráfico HTTP también. ¿Fue este el caso para ti?

Si es así, ¿podríamos determinar quién es el servidor web?

¿Qué aplicación está ejecutando el servidor web?

¿Cuáles fueron las solicitudes de método más comunes que vio?

### **Siga la corriente y extraiga los elementos encontrados**

Ahora intente seguir la corriente HTTP y determine si hay algo que extraer.

- **Haga clic para mostrar respuesta**
    
    Aplique el filtro "http && image-jfif" para incluir solo paquetes HTTP (80/TCP) junto con un filtro para incluir solo formatos de intercambio de archivos JPEG (archivos JPEG).
    
    Busque la línea en la que el servidor web responde con un mensaje "200 OK" que actúa como un reconocimiento/recibo para la solicitud GET de los usuarios.
    
    Seleccione "Archivo> Exportar objetos> HTTP>`file.JPG`
    

---

# **Resumen**

Al final de este laboratorio, deberíamos poder abrir archivos .PCAP previamente capturados, aplicar filtros de visualización, seguir transmisiones y extraer elementos del archivo de captura. Experimente con formas de capturar un nuevo tráfico y aplicar filtros para encontrar tráfico específico. Para verificar nuestra comprensión, responda las preguntas a continuación con el tráfico que captura por su cuenta.

Verifique su comprensión:

- ¿Qué filtros o expresiones usaste? ¿Fueron efectivos?
- ¿Cómo afectaron estos filtros el tráfico que podría ver dentro de la captura?
- ¿Cómo puede ser beneficioso utilizar estas características para usted y su misión?
- ¿Qué filtro usaría si solo quisiera ver el tráfico TCP desde el cliente?