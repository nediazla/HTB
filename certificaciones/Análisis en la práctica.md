# Análisis en la práctica

La sección anterior definió el análisis de tráfico de red, las dependencias para realizar el análisis de tráfico y su importancia. Esta sección desglosará un flujo de trabajo para realizar el análisis de tráfico, y nos familiarizaremos con los componentes clave.

Esta no es una ciencia exacta. Puede ser un proceso muy dinámico y no es un bucle directo. Está muy influenciado por lo que estamos buscando (errores de red versus acciones maliciosas) y dónde tiene visibilidad en su red. Sin embargo, el análisis se puede destilarse a algunos principios básicos.

---

# **Análisis descriptivo**

El análisis descriptivo es un paso esencial en cualquier análisis de datos. Sirve para describir un conjunto de datos basado en características individuales. Ayuda a detectar posibles errores en la recopilación de datos y/o valores atípicos en el conjunto de datos.

1. `What is the issue?`
    - Sospecha de violación? ¿Problema de red?
2. `Define our scope and the goal. (what are we looking for? which time period?)`
    - Objetivo: múltiples hosts que potencialmente descargan un archivo malicioso de bad.example.com
    - Cuándo: dentro de las últimas 48 horas + 2 horas a partir de ahora.
    - Información de apoyo: nombres de archivo/tipos 'superbad.exe' 'new-crypto-miner.exe'
3. `Define our target(s) (net / host(s) / protocol)`
    - Alcance: la red 192.168.100.0/24, los protocolos utilizados fueron HTTP y FTP.

Usando nuestro flujo de trabajo, determinaremos nuestro problema, lo que estamos buscando, cuándo y dónde encontrarlo. El análisis descriptivo cubre estos conceptos críticos para nuestro análisis.

---

# **Análisis de diagnóstico**

El análisis de diagnóstico aclara las causas, los efectos e interacciones de las afecciones. Al hacerlo, proporciona información que se obtienen a través de correlaciones e interpretación. Característica aquí hay una visión hacia atrás, como en la analítica descriptiva estrechamente relacionada, con la sutil diferencia que intenta encontrar razones para eventos y desarrollos.

1. `Capture network traffic`
    - Conéctese en un enlace con acceso a la red 192.168.100.0/24 para capturar el tráfico en vivo para tratar de obtener uno de los ejecutables en transferencia. Vea si un administrador puede extraer datos PCAP y/o NetFlow de nuestro SIEM para los datos históricos.
2. `Identification of required network traffic components (filtering)`
    - Una vez que tengamos tráfico, filtre cualquier paquete que no sea necesario para que esta investigación incluya; Cualquier tráfico que coincida con nuestra línea de base común y mantenga algo relevante para el alcance de la investigación. Por ejemplo, HTTP y FTP desde la subred, cualquier cosa que transfiera o contenga una solicitud GET para los archivos ejecutables sospechosos.
3. `An understanding of captured network traffic`
    - Una vez que hemos filtrado el ruido, es hora de buscar nuestros objetivos, filtros en cosas como `ftp-data`Para encontrar cualquier archivo transferido y reconstruyéndote. Para http, podemos filtrar en `http.request.method == "GET"` Para ver cualquier solicitud GET que coincida con los nombres de archivo que estamos buscando. Esto puede mostrarnos quién ha adquirido los archivos y potencialmente otras transferencias internas a la red en los mismos protocolos.

Al capturar el tráfico en torno a la fuente de nuestro problema, eliminar los datos buenos conocidos y luego tomarse el tiempo para inspeccionar y comprender lo que queda, podemos determinar si es la causa de nuestro problema. Al hacerlo, acabamos de realizar un análisis de diagnóstico. Estamos validando la causa de nuestros problemas y examinando los eventos que los rodean.

---

# **Análisis predictivo**

Al evaluar los datos históricos y actuales, el análisis predictivo crea un modelo predictivo para probabilidades futuras. Según los resultados de los análisis descriptivos y de diagnóstico, este método de análisis de datos permite identificar tendencias, detectar las desviaciones de los valores esperados en una etapa temprana y predecir las ocurrencias futuras con la mayor precisión posible.

1. `Note-taking and mind mapping of the found results`
    - Anotar todo lo que hacemos, ver o encontrar a lo largo de la investigación es crucial. Asegúrese de que estemos tomando muchas notas, incluyendo:
    - Plazos de tiempo capturamos el tráfico durante.
    - Anfitriones sospechosos dentro de la red.
    - Conversaciones que contienen los archivos en cuestión. (Para incluir marcas de tiempo y números de paquetes)
2. `Summary of the analysis (what did we find?)`
    - Finalmente, resume lo que hemos encontrado explicando los detalles relevantes para que los superiores puedan decidir poner en cuarentena a los anfitriones afectados o realizar una respuesta de incidentes más significativa.
    - Nuestro análisis afectará las decisiones tomadas, por lo que es esencial ser lo más claro y conciso posible.

Al realizar una evaluación de los datos que hemos encontrado, comparándolo con nuestro tráfico de referencia y datos malos conocidos, como marcadores de infiltración o explotación (como firmas para virus y otras herramientas de piratería), estamos realizando un análisis predictivo. En este proceso, pintamos una imagen clara para que se puedan tomar acciones apropiadas en respuesta.

---

# **Análisis prescriptivo**

El análisis prescriptivo tiene como objetivo reducir qué acciones tomar para eliminar o prevenir un problema futuro o activar una actividad o proceso específico. Usando los resultados de nuestro flujo de trabajo, podemos tomar decisiones sólidas sobre qué acciones se requieren para resolver el problema y evitar que vuelva a suceder. Prescribir una solución es la culminación de este flujo de trabajo. Una vez hecho y el problema se resuelve, es prudente reflexionar sobre todo el proceso y desarrollar lecciones aprendidas. Estas lecciones, cuando se documentan, nos permitirán fortalecer nuestros procesos: documentan lo que se hizo correctamente, qué acciones no pudieron ayudar y lo que podría mejorar.

Este flujo de trabajo es un ejemplo de cómo comenzar el proceso de análisis sobre el tráfico capturado. Arriba lo dividimos en sus partes para explicar dónde encajan dentro del proceso de análisis y con qué tipo de análisis pertenece. Lo incluimos aquí nuevamente en su conjunto para que pueda servir como plantilla.

1. `What is the issue?`
    - Sospecha de violación? ¿Problema de red?
2. `Define our scope and the goal (what are we looking for? which time period?)`
    - Objetivo: múltiples hosts que potencialmente descargan un archivo malicioso de bad.example.com
    - Cuándo: dentro de las últimas 48 horas + 2 horas a partir de ahora.
    - Información de apoyo: nombres de archivo/tipos 'superbad.exe' 'new-crypto-miner.exe'
3. `Define our target(s) (net / host(s) / protocol)`
    - Alcance: 192.168.100.0/24 Los protocolos de red utilizados fueron HTTP y FTP.
4. `Capture network traffic`
    - Conéctese en un enlace con acceso a la red 192.168.100.0/24 para capturar el tráfico en vivo para tratar de obtener uno de los ejecutables en transferencia. Vea si un administrador puede extraer datos PCAP y/o NetFlow de nuestro SIEM para los datos históricos.
5. `Identification of required network traffic components (filtering)`
    - Una vez que tengamos tráfico, filtre cualquier tráfico que no sea necesario para que esta investigación incluya; Cualquier tráfico que coincida con nuestra línea de base común y mantenga algo relevante para el alcance. `HTTP y FTP desde la subred, cualquier cosa que transfiera o contenga una solicitud GET para los archivos ejecutables sospechosos.
6. `An understanding of captured network traffic`
    - Una vez que hemos filtrado el ruido, es hora de buscar nuestros objetivos: filtro en cosas como `ftp-data` Para encontrar cualquier archivo transferido y reconstruyéndote. Para http, podemos filtrar en `http.request.method == "GET"` Para ver cualquier solicitud GET que coincida con los nombres de archivo que estamos buscando. Esto puede mostrarnos quién ha adquirido los archivos y otras transferencias potenciales internas a la red en los mismos protocolos.
7. `Note-taking and mind mapping of the found results.`
    - Anotar todo lo que hacemos, ver o encontrar a lo largo de la investigación es crucial. Asegúrese de que estemos tomando muchas notas, incluyendo:
    - Plazos de tiempo capturamos el tráfico durante.
    - Anfitriones sospechosos dentro de la red.
    - Conversaciones que contienen los archivos en cuestión. (Para incluir marcas de tiempo y números de paquetes)
8. `Summary of the analysis (what did we find?)`
    - Finalmente, resume lo que se ha encontrado, explicando los detalles relevantes para que los superiores puedan tomar una decisión informada de poner en cuarentena a los anfitriones afectados o realizar una respuesta de incidentes más significativa.
    - Nuestro análisis afectará las decisiones tomadas, por lo que es esencial ser lo más claro y conciso posible.

A menudo, este proceso no es un tipo de cosas una vez y hecha. Por lo general, es cíclico, y tendremos que volver a ejecutar los pasos en función de nuestro análisis de la captura original para construir una imagen más grande. Este podría haber sido un ataque mucho más grande que lo que hay en los ejemplos. Supongamos que se considera necesaria una respuesta de incidente a gran escala. En ese caso, es posible que tengamos que reanalizar el PCAP previamente capturado para analizar cualquier conversación que involucre a los hosts afectados dentro de varios minutos de la transferencia ejecutable para asegurarse de que no se extendiera sobre otra ruta, como ejemplo.

---

# **Componentes clave de un análisis efectivo**

### **1. Conozca su entorno**

Existen varios componentes clave para realizar el análisis de tráfico de manera efectiva. Primero, conozca el medio ambiente. Si no estamos seguros de si un anfitrión pertenece a la red, ¿cómo podemos determinar si es pícaro o no? Mantener inventarios de activos y mapas de red es vital. Estos ayudarán en el proceso de análisis.

### **2. La colocación es clave**

A continuación, la colocación de nuestro anfitrión para capturar el tráfico es algo crítico. Más cercano a la fuente del problema es la colocación ideal de nuestra herramienta de captura. Si el tráfico en cuestión viene de Internet, escuchar los enlaces entrantes es una excelente manera de ver la imagen completa. Está tan cerca de la fuente que nosotros, los administradores, podemos obtener. Si el problema parece estar aislado a un host en nuestra red interna, intente colocar las herramientas de captura en el mismo segmento que el host del problema y vea qué tráfico está sucediendo dentro del segmento.

### **3. Persistencia**

La persistencia es el próximo componente crítico para nosotros. El problema no siempre será fácil de detectar. Puede que ni siquiera sea un evento frecuente en la red. Por ejemplo, el comando de un atacante y el servidor de control que se acerca a las computadoras de la víctima solo puede ocurrir en un intervalo de tiempo de una vez cada varias horas, o incluso una vez al día o menos. Esto significa que si no lo atrapamos la primera vez, podría pasar un tiempo antes de que aparezca en nuestros registros. No pierdas el impulso para encontrar el problema. Podría significar la diferencia entre detener al atacante y una violación a gran escala como un ataque de ransomware.

---

# **Enfoque de análisis**

Hemos pasado algún tiempo discutiendo el proceso de análisis y cómo comenzar un flujo de trabajo básico al realizar nuestras tareas. Tomemos un segundo para discutir algunas victorias fáciles al mirar el tráfico y encontrar problemas.

Comenzar con `standard protocols first` y avanzar en el `austere and specific` solo a la organización. La mayoría de los ataques vendrán de Internet, por lo que tiene que acceder a la red interna de alguna manera. Esto significa que habrá tráfico generado y registros escritos al respecto. HTTP/S, FTP, correo electrónico y tráfico básico de TCP y UDP serán las cosas más comunes vistas del mundo. Comience en estos y elimine todo lo que no sea necesario para la investigación. Después de estos, verifique los protocolos estándar que permitan comunicaciones entre redes, como SSH, RDP o Telnet. Cuando busque este tipo de anomalías, tenga en cuenta la política de seguridad de la red. ¿El plan de seguridad de nuestra organización y las implementaciones permiten sesiones RDP que se inician fuera de la empresa? ¿Qué pasa con el uso de Telnet?

Buscar `patterns`. ¿Un host o un conjunto específico de hosts se registra con algo en Internet al mismo tiempo diariamente? Esta es una configuración típica de perfil de comando y control que se puede ver fácilmente buscando patrones en nuestros datos de tráfico.

Comprobar cualquier cosa `host to host` dentro de nuestra red. En una configuración estándar, los hosts del usuario rara vez se hablarán. Así que sospeche de cualquier tráfico que aparezca así. Por lo general, los hosts hablarán con infraestructura para arrendamientos de dirección IP, solicitudes de DNS, servicios empresariales y encontrar su ruta. También veremos a los hosts que hablan con servidores web locales, acciones de archivos y otra infraestructura crítica para que el entorno funcione como controladores de dominio y aplicaciones de autenticación.

Buscar `unique` eventos. Cosas como un anfitrión que generalmente visita un sitio específico diez veces al día cambiando su patrón y solo hacerlo una vez es curioso. Ver una cadena de agente de usuario diferente que no coincide con nuestras aplicaciones u hosts que hablen con un servidor en Internet también es algo que se preocupa. También es notable un puerto aleatorio que solo esté atado una o dos veces en un host. Esto podría ser una apertura para cosas como las devoluciones de llamada C2, alguien que abre un puerto para hacer algo no estándar, o una aplicación que muestra un comportamiento anormal. En entornos grandes, se esperan patrones, por lo que cualquier cosa que sobresalga merece una mirada.

`Don't be afraid to ask for help.`Esto puede parecer exagerado y obvio, pero después de un poco de tiempo mirando las capturas de paquetes, las cosas pueden combinarse, y es posible que no veamos la imagen completa. Tener un segundo conjunto de ojos en los datos puede ser una gran ayuda para detectar cosas que pueden pasar por alto.

---

En resumen, el proceso de análisis es una tarea muy dinámica, y nuestros días nunca serán los mismos. Siga aprendiendo, comprenda lo que sucede a nuestro alrededor y a medida que sus habilidades crecen, también lo hará la capacidad de detectar amenazas. Este proceso no se basa únicamente en el uso de herramientas como TCPDump y Wireshark. Hay muchas herramientas útiles como Snort, Security Cebole, Firewalls y SIEM que pueden ayudar a enriquecer nuestra comprensión del entorno y proporcionar una mejor protección. No tengas miedo de utilizarlos en las investigaciones.

[Anterior](https://academy.hackthebox.com/module/81/section/953) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/81/section/774)

Hoja de trucosRecursos