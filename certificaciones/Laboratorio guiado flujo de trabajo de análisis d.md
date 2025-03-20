# Laboratorio guiado: flujo de trabajo de análisis de tráfico

Uno de nuestros compañeros administradores notó una conexión extraña del anfitrión de Bob `IP = 172.16.10.90` Al analizar las capturas de referencia, hemos estado reuniendo. Nos pidió que lo revisáramos y veamos qué creemos que está sucediendo.

Intente utilizar los conceptos de las secciones del proceso de análisis para completar un análisis del análisis guiado. ZIP proporcionado en los recursos opcionales y el tráfico en vivo desde la red de la Academia. Una vez hecho esto, se incluye una clave de respuesta guiada con el PCAP en la zip para verificar su trabajo.

---

# **Tareas:**

### **Tarea #1**

`Connect to the live host for capture.`

`Connection Instructions`: Acceso al entorno de laboratorio para completar las siguientes tareas requerirá el uso de [Xfreerdp](https://manpages.ubuntu.com/manpages/trusty/man1/xfreerdp.1.html) Para proporcionar acceso GUI a la máquina virtual para que podamos utilizar Wireshark desde el entorno.

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

Una vez conectado, abra Wireshark y comience a capturar en la interfaz ENS224.

---

### **Análisis**

Siga esta plantilla de flujo de trabajo y examine el tráfico sospechoso. El objetivo es determinar qué está sucediendo con el anfitrión en cuestión.

1. ¿Cuál es el problema?
    1. Un breve resumen del problema.
2. Defina nuestro alcance y el objetivo (¿qué estamos buscando? ¿Qué período de tiempo?)
    1. Alcance: ¿Qué estamos buscando, dónde?
    2. Cuando comenzó el problema:
    3. Información de soporte: archivos, fuentes de datos, cualquier cosa útil.
3. Defina nuestro objetivo (S) (Net / Host (S) / Protocol)
    1. Hosts de destino: red o dirección de los hosts.
4. Capture el tráfico de red / lectura del PCAP previamente capturado.
    1. Realice acciones según sea necesario para analizar el tráfico en busca de signos de intrusión.
5. Identificación de los componentes de tráfico de red requeridos (filtrado)
    1. Una vez que tengamos nuestro tráfico, filtre cualquier tráfico que no sea necesario para que esta investigación incluya; Cualquier tráfico que coincida con nuestra línea de base común y mantenga algo relevante para el alcance de la investigación.
6. Una comprensión del tráfico de red capturado
    1. Una vez que hemos filtrado el ruido, es hora de buscar nuestros objetivos. Comience de amplio y cierre el círculo alrededor de nuestro alcance.
7. Nota Tomar / mapeo mental de los resultados encontrados.
    1. Anotar todo lo que hacemos, ver o encontrar a lo largo de la investigación es crucial. Asegúrese de tomar notas amplias, incluidas:
    - Plazos de tiempo capturamos el tráfico durante.
    - Hosts/puertos sospechosos dentro de la red.
    - Conversaciones que contienen algo sospechoso. (Para incluir marcas de tiempo y números de paquetes, archivos, etc.)
8. Resumen del análisis (¿qué encontramos?)
    1. Finalmente, resume lo que se ha encontrado, explicando los detalles relevantes para que los superiores puedan decidir poner en cuarentena a los anfitriones afectados o realizar una misión de respuesta a incidentes más crítica.
    2. Nuestro análisis afectará las decisiones tomadas, por lo que es esencial ser lo más claro y conciso posible.

`Complete an attempt on your own first to examine and follow the workflow, then look below for a guided walkthrough of the lab.`

- **Haga clic para mostrar el tutorial**
    
    Esta tarea se completó mejor utilizando el archivo PCAP proporcionado para la lección. Después de los pasos del flujo de trabajo, completamos la información y realizamos nuestro análisis.
    
    1. ¿Cuál es el problema?
        1. Tráfico sospechoso proveniente de la red.
    2. Defina nuestro alcance y el objetivo (¿qué estamos buscando? ¿Qué período de tiempo?)
        1. Objetivo: El tráfico se origina en 10.129.43.4
        2. Cuándo: dentro de las últimas 48 horas. Capture el tráfico para determinar si todavía está sucediendo.
        3. Información de apoyo: archivo: NTA-Guided.pcap
    3. Defina nuestro objetivo (S) (Net / Host (S) / Protocol)
        1. Alcance: 10.129.43.4 y cualquier persona con una conexión a él. Protocolo desconocido sobre el puerto 4444.
    4. Captura del tráfico de red
        1. Conéctese en un enlace con acceso a la red 10.129.43.0/24 para capturar el tráfico en vivo que intenta ver si está sucediendo algo.
        2. Nos han dado un PCAP con datos históricos que contienen parte del tráfico sospechoso. Examinaremos esto para analizar el problema.
    5. Identificación de los componentes de tráfico de red requeridos (filtrado)
        1. Primero, filtraremos cualquier cosa que no tenga una conexión a 10.129.43.4, ya que este es nuestro objetivo principal sospechoso por el momento.
    
    ### **Conversaciones**
    
    ![](https://academy.hackthebox.com/storage/modules/81/guided-conversations.png)
    
    Después de revisar el complemento de conversaciones que se muestra arriba, podemos ver que solo hay tres conversaciones capturadas en este archivo PCAP, y todos pertenecen a nuestro host sospechoso. A continuación, veremos el`protocol hierarchy`complemento para ver cuál es nuestro tráfico.
    
    ### **Estadística de protocolo**
    
    ![](https://academy.hackthebox.com/storage/modules/81/guided-proto.png)
    
    Podemos ver aquí que este PCAP es principalmente tráfico TCP, con un poco de tráfico UDP. Dado que hay menos UDP que el tráfico TCP, veamos eso primero.
    
    1. Una vez que hemos filtrado el ruido, es hora de cavar para algo inusual. Vamos a filtrar todo menos el tráfico UDP primero.
    
    ### **UDP**
    
    ![](https://academy.hackthebox.com/storage/modules/81/guided-udp.png)
    
    Al filtrar solo el tráfico UDP, solo vemos nueve paquetes. Cuatro paquetes ARP, cuatro traducciones de dirección de red `NAT`, y un simple protocolo de descubrimiento de sevice`SSDP`paquete. Podemos determinar en base a sus tipos de paquetes e información que contienen que este tráfico es el tráfico de red normal y nada de qué preocuparse.
    
    1. Ahora, pasemos a mirar `TCP` tráfico. Deberíamos tener bastante más aquí para examinar. Vamos a utilizar el filtro de visualización`!udp && !arp`. Este filtro borrará todo lo que ya hemos analizado.
    
    ### **TCP**
    
    ![](https://academy.hackthebox.com/storage/modules/81/guided-tcp.png)
    
    1. Ahora que hemos limpiado un poco nuestra visión, podemos ver que los paquetes restantes son todos TCP, y todos parecen ser la misma conversación entre los anfitriones`10.129.43.4`y `10.129.43.29`. Podemos determinar esto ya que podemos ver el establecimiento de la sesión a través de un apretón de manos de tres vías en el paquete 3, y los mismos puertos se usan a través del resto de los paquetes en la salida a continuación.
    
    ### **Establecimiento de sesión de TCP**
    
    ![](https://academy.hackthebox.com/storage/modules/81/guided-handshake.png)
    
    1. Lo que parece interesante es que no vemos un desmontaje de la sesión TCP en este archivo PCAP. Esto podría significar que la sesión todavía estaba activa y no terminó. Creemos que esto es cierto ya que tampoco vemos ningún paquete de reinicio.
    2. También podemos examinar la conversación siguiendo el `TCP stream` del paquete 3 para determinar qué abarca.
    
    ### **Sigue la transmisión TCP**
    
    ![](https://academy.hackthebox.com/storage/modules/81/guided-stream.png)
    
    Ahora que seguimos la transmisión TCP, deberíamos hacer sonar las alarmas para nosotros. Podemos ver toda esta conversación entre los dos anfitriones en texto plano, y parece que alguien estaba realizando varias acciones diferentes en el anfitrión.
    
    1. Mirando la imagen de arriba, parece que alguien está realizando un reconocimiento básico del host. Están emitiendo comandos como `whoami`, `ipconfig`, `dir`. Parece que están tratando de obtener un laico de la tierra y descubrir qué usuario aterrizó en el anfitrión.`highlighted in orange in the image above.`
    2. Lo que es realmente alarmante es que ahora podemos ver que alguien hizo la cuenta `hacker` y lo asignó al `administrators group` en este anfitrión. O esta es una broma de un pobre administrador. O alguien se ha infiltrado en la infraestructura corporativa.
    3. Nota Tomar / mapeo mental de los resultados encontrados.
        1. Anotar todo lo que hacemos, ver o encontrar a lo largo de la investigación es crucial. Si es necesario, haga una imagen para representar el flujo de acciones.
        2. Usando este ejemplo de flujo de trabajo, ya hemos documentado nuestras acciones y hemos incluido capturas de pantalla de todo lo que incluimos para el análisis. Esto ayudará a influir en la decisión tomada para una respuesta.
    4. Resumen del análisis (¿qué encontramos?)
        1. Según nuestro análisis, determinamos que un actor malicioso se ha infiltrado al menos un host en la red. Host 10.129.43.29 muestra signos de alguien que ejecuta comandos para incluir la creación de usuarios y la asignación de permisos de administrador local a través del `net`comandos. Parece que el actor estaba usando el anfitrión de Bob para realizar dichas acciones. Dado que Bob estaba previamente investigado por el exfil de secretos corporativos y disfrazándolo como tráfico web, creo que es seguro decir que el problema se ha extendido aún más. Las capturas de pantalla incluidas con este documento muestran el flujo de tráfico y comandos utilizados.
        2. Es nuestra opinión que una respuesta completa de incidentes `IR` Se promulga el procedimiento para garantizar que la amenaza se impida que se propague aún más. Podemos dedicar recursos a la limpieza de la presencia maliciosa y la limpieza de los anfitriones afectados.

---

# **Resumen**

Después de analizar las acciones tomadas, el equipo IR determinó que el actor se volvió perezoso y decidió utilizar un shell NetCat e interactuar directamente con el anfitrión de Bob mientras recopilaba más información. Mientras lo hacía, usó RDP del host de Bob a otro escritorio de Windows en el entorno para tratar de establecer otro punto de apoyo. Afortunadamente, el equipo IR pudo capturar un PCAP del tráfico RDP. El anfitrión de Bob fue en cuarentena, y se inició una respuesta de incidentes para determinar qué se tomó y qué otros anfitriones potenciales se vieron comprometidos. Gran trabajo detectando la intrusión.