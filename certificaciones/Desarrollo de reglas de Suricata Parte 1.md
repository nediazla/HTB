# Desarrollo de reglas de Suricata Parte 1

En esencia, una regla en Suricata sirve como una directiva, indicando al motor que vigile activamente ciertos marcadores en el tráfico de la red. Cuando aparezcan tales marcadores específicos, recibiremos una notificación.

Las reglas de Suricata no se centran exclusivamente en la detección de actividades nefastas o tráfico potencialmente dañino. En muchos casos, las reglas se pueden diseñar para proporcionar defensores de red o miembros del equipo azul con ideas críticas o datos contextuales con respecto a la actividad de la red en curso.

La especificidad o generalidad de las reglas está en nuestras manos. La búsqueda de un equilibrio es primordial, por ejemplo, identificar variaciones de una cierta tensión de malware mientras evade falsos positivos.

El desarrollo de estas reglas a menudo aprovecha la información crucial proporcionada por las comunidades infosec y la inteligencia de amenazas. Sin embargo, vale la pena señalar que cada regla que implementamos consume una parte de la CPU y los recursos de memoria del host. Por lo tanto, Suricata proporciona pautas específicas para escribir reglas efectivas.

# **Anatomía de la regla de suricata**

A continuación se puede encontrar una regla de muestra suricata. Vamos a desglosarlo.

Desarrollo de reglas de Suricata Parte 1

```
action protocol from_ip port -> to_ip port (msg:"Known malicious behavior, possible X malware infection"; content:"some thing"; content:"some other thing"; sid:10000001; rev:1;)

```

- **Encabezamiento** (`action protocol from_ip port -> to_ip port`Parte): el`header`La sección de una regla encapsula la acción prevista de la regla, junto con el protocolo donde se espera que se aplique la regla. Además, incluye direcciones IP, información del puerto y una flecha que indica direccionalidad del tráfico.
    - `action`instruye a Suricata sobre qué pasos tomar si el contenido coincide. Esto podría variar desde generar una alerta (`alert`), registrar el tráfico sin una alerta (`log`), ignorando el paquete (`pass`), dejando caer el paquete en modo IPS (`drop`), o enviando paquetes RST TCP (`reject`).
    - `protocol`puede variar, incluido`tcp`, `udp`, `icmp`, `ip`, `http`, `tls`, `smb`, o`dns`.
    - La direccionalidad del tráfico se declara utilizando`rule host variables`(como`$HOME_NET`, `$EXTERNAL_NET`, etc. que vimos dentro`suricata.yaml`) y`rule direction`. La flecha de dirección entre los dos pares de puerto IP informa a Suricata sobre el flujo de tráfico.
        - Ejemplos:
            - Salida:`$HOME_NET any -> $EXTERNAL_NET 9090`
            - Entrante:`$EXTERNAL_NET any -> $HOME_NET 8443`
            - Bidireccional:`$EXTERNAL_NET any <> $HOME_NET any`
    - `Rule ports`Defina los puertos en los que se evaluará el tráfico para esta regla.
        - Ejemplos:
            - `alert tcp $HOME_NET any -> $EXTERNAL_NET 9443`
            - `alert tcp $HOME_NET any -> $EXTERNAL_NET $UNCOMMON_PORTS`
                - $ Poco común se puede definir dentro`suricata.yaml`
            - `alert tcp $HOME_NET any -> $EXTERNAL_NET [8443,8080,7001:7002,!8443]`
- **Mensaje de regla y contenido** (`(msg:"Known malicious behavior, possible X malware infection"; content:"some thing"; content:"some other thing";`Parte): el`rule message & content`La sección contiene el mensaje que deseamos mostrar a los analistas o a nosotros mismos cuando se detecta una actividad sobre la que queremos ser notificados.`content`son los segmentos del tráfico que consideramos esenciales para tales detecciones.
    - `Rule message (msg)`es un texto arbitrario que se muestra cuando se activa la regla. Idealmente, los mensajes de regla que creamos deben contener detalles sobre la arquitectura de malware, la familia y la acción.
        - `flow`Identifica el creador y el respondedor. Recuerde siempre, al elaborar las reglas, tener el monitor del motor "establecido" sesiones de TCP.
            - Ejemplos:
                - `alert tcp any any -> 192.168.1.0/24 22 (msg:"SSH connection attempt"; flow:to_server; sid:1001;)`
                - `alert udp 10.0.0.0/24 any -> any 53 (msg:"DNS query"; flow:from_client; sid:1002;)`
                - `alert tcp $EXTERNAL_NET any -> $HOME_NET 80 (msg:"Potential HTTP-based attack"; flow:established,to_server; sid:1003;)`
        - `dsize`coincide con el tamaño de carga útil del paquete. Se basa en la longitud del segmento TCP, no en la longitud total del paquete.
            - Ejemplo:`alert http any any -> any any (msg:"Large HTTP response"; dsize:>10000; content:"HTTP/1.1 200 OK"; sid:2003;)`
    - `Rule content`comprende valores únicos que ayudan a identificar el tráfico o actividades de red específicas. Suricata coincide con estos valores de contenido únicos en paquetes para la detección.
        - Ejemplo:`content:"User-Agent|3a 20|Go-http-client/1.1|0d 0a|Accept-Encoding|3a 20|gzip";`
            - `|3a 20|`: Esto representa la representación hexadecimal de los personajes ":", seguido de un personaje espacial. Se usa para que coincida con la secuencia de bytes exacta en la carga útil del paquete.
            - `|0d 0a|`: Esto representa la representación hexadecimal de los caracteres "\ r \ n", que significa el final de una línea en los encabezados HTTP.
        - Utilizando`Rule Buffers`, no tenemos que buscar en todo el paquete para cada coincidencia de contenido. Esto ahorra tiempo y recursos. Se pueden encontrar más detalles aquí:[https://suricata.readthedocs.io/en/latest/rules/http-keywords.html](https://suricata.readthedocs.io/en/latest/rules/http-keywords.html)
            - Ejemplo:`alert http any any -> any any (http.accept; content:"image/gif"; sid:1;)`
                - `http.accept`: Buffer adhesivo para que coincida en el encabezado HTTP Aceptar. Solo contiene el valor del encabezado. El`\r\n`Después del encabezado no son parte del búfer.
        - `Rule options`actúe como modificadores adicionales para ayudar a la detección, ayudando a Suricata a ubicar la ubicación exacta del contenido.
            - `nocase`Asegura que las reglas no se pasen por alto a través de cambios de casos.
                - Ejemplo:`alert tcp any any -> any any (msg:"Detect HTTP traffic with user agent Mozilla"; content:"User-Agent: Mozilla"; nocase; sid:8001;)`
            - `offset`informa a Suricata sobre la posición de inicio dentro del paquete para que coincida.
                - Ejemplo:`alert tcp any any -> any any (msg:"Detect specific protocol command"; content:"|01 02 03|"; offset:0; depth:5; sid:3003;)`
                    - Esta regla desencadena una alerta cuando Suricata detecta un comando de protocolo específico representado por la secuencia de bytes`|01 02 03|`En la carga útil TCP. El`offset:0`La palabra clave establece la coincidencia de contenido para comenzar desde el comienzo de la carga útil, y`depth:5`Especifica una longitud de cinco bytes a considerar para la coincidencia.
            - `distance`le dice a Suricata que busque el contenido especificado`n`Bytes en relación con el partido anterior.
                - Ejemplo:`alert tcp any any -> any any (msg:"Detect suspicious URL path"; content:"/admin"; offset:4; depth:10; distance:20; within:50; sid:3001;)`
                    - Esta regla desencadena una alerta cuando Suricata detecta la cadena`/admin`en la carga útil TCP, a partir del quinto byte (`offset:4`) y considerando una longitud de diez bytes (`depth:10`). El`distance:20`La palabra clave especifica que las coincidencias posteriores de`/admin`no debe ocurrir dentro de los siguientes 20 bytes después de un partido anterior. El`within:50`La palabra clave asegura que la coincidencia de contenido ocurra dentro de los siguientes 50 bytes después de una coincidencia anterior.
- **Metadatos de regla** (`sid:10000001; rev:1;`parte):
    - `reference`nos proporciona un liderazgo, un rastro que nos lleva de vuelta a la fuente original de información que inspiró la creación de la regla.
    - `sid`(ID de firma). La calidad única de este identificador numérico hace que el escritor de reglas gestione y distinga entre reglas.
    - `revision`Ofrece información sobre la versión de la regla. Sirve como un indicador de la evolución de la regla a lo largo del tiempo, destacando modificaciones y mejoras realizadas.

Habiendo discutido el quid de las reglas de Suricata, ahora es el momento de arrojar luz sobre una herramienta poderosa en el desarrollo de reglas:`PCRE`o`Pearl Compatible Regular Expression`. Utilizar PCRE puede ser un cambio de juego al elaborar reglas. Para emplear pcre, usamos el`pcre`Declaración, que luego es seguida por una expresión regular. Tenga en cuenta que el PCRE debe estar encerrado en las cortes de liderazgo y trasero hacia adelante, con cualquier bandera colocada después del último corte.

Además, tenga en cuenta que los anclajes se colocan después y antes de los recortes de cubierta, y ciertos personajes exigen que escape con una barra invertida. Un consejo de las trincheras: evitar autorizando una regla que se basa únicamente en PCRE.

- Ejemplo:`alert http any any -> $HOME_NET any (msg: "ATTACK [PTsecurity] Apache Continuum <= v1.4.2 CMD Injection"; content: "POST"; http_method; content: "/continuum/saveInstallation.action"; offset: 0; depth: 34; http_uri; content: "installation.varValue="; nocase; http_client_body; pcre: !"/^\$?[\sa-z\\_0-9.-]*(\&|$)/iRP"; flow: to_server, established;sid: 10000048; rev: 1;)`
    - En primer lugar, la regla se desencadena en el tráfico HTTP (`alert http`) desde cualquier fuente y destino a cualquier puerto de la red doméstica (`any any -> $HOME_NET any`).
    - El campo MSG ofrece una descripción legible por humanos de para qué es la alerta, a saber`ATTACK [PTsecurity] Apache Continuum <= v1.4.2 CMD Injection`.
    - A continuación, la regla verifica el`POST`cadena en el método HTTP usando el`content`y`http_method`Palabras clave. La regla coincidirá si el método HTTP utilizado es una solicitud posterior.
    - El`content`La palabra clave se usa luego con`http_uri`para que coincida con el uri`/continuum/saveInstallation.action`, comenzando en`offset 0`Y yendo a un`depth`de`34`bytes. Esto especifica el punto final objetivo, que en este caso es el`saveInstallation`Acción de la aplicación Continuum Apache.
    - Después de esto, otra palabra clave de contenido busca`installation.varValue=`En el cuerpo del cliente HTTP, el caso de manera insensible (`nocase`). Esta cadena puede ser parte de la carga útil de inyección de comando que el atacante está tratando de entregar.
    - A continuación, vemos un`pcre`Palabra clave, que se utiliza para implementar expresiones regulares compatibles con Perl.
        - `^`marca el comienzo de la línea.
        - `\$?`Comprueba un letrero de dólar opcional al comienzo.
        - `[\sa-z\\_0-9.-]*`coincide con cero o más () de los caracteres del set. El conjunto incluye:
            - `\s`un espacio
            - `a-z`Cualquier carta minúscula
            - `\\`una barra de chaqueta
            - `_`un bajo
            - `0-9`cualquier dígito
            - `.`un período
            - un guión
            - `(\&|$)`Comprueba un ampers y el final de la línea.
            - `/iRP`Al final indica que esta es una coincidencia invertida (lo que significa que la regla se desencadena cuando la coincidencia no ocurre), insensible a la caja (`i`), y en relación con la posición del amortiguador (`RP`).
    - Finally, the `flow` keyword is used to specify that the rule triggers on `established`, inbound traffic towards the server.

Para aquellos que buscan ampliar su comprensión de las reglas de Suricata y profundizar en el desarrollo de reglas, el siguiente recurso sirve como una guía completa:[https://docs.suricata.io/en/latest/rules/index.html](https://docs.suricata.io/en/latest/rules/index.html).

# **IDS/IPS Rule Development Approaches**

Cuando se trata de crear reglas para los sistemas de detección de intrusos (IDS) y los sistemas de prevención de intrusiones (IPS), hay un arte y una ciencia detrás de él. Requiere una comprensión integral de los protocolos de red, los comportamientos de malware, las vulnerabilidades del sistema y el panorama de amenazas en general.

Una estrategia clave que empleamos mientras elaboramos estas reglas implica la detección de elementos específicos dentro del tráfico de red que son exclusivos del malware. Esto a menudo se conoce como detección basada en la firma, y ​​es el enfoque clásico en el que la mayoría de los ID/IP dependen. Las firmas pueden variar desde patrones simples en cargas útiles de paquetes, como la detección de un comando específico o una cadena distintiva asociada con un malware particular, hasta patrones complejos que coinciden con una serie de paquetes o características de paquetes. La detección basada en la firma es altamente efectiva cuando se trata de amenazas conocidas, ya que puede identificar estas amenazas con alta precisión, sin embargo, lucha por detectar nuevas amenazas para las cuales todavía no existe ninguna firma.

Otro enfoque se centra en identificar comportamientos específicos que son característicos para el malware. Esto generalmente se conoce como detección basada en anomalías o basada en el comportamiento. Por ejemplo, un cierto tamaño de respuesta HTTP que aparece constantemente dentro de un umbral, o un intervalo de baliza específico podría ser indicativo de un patrón de comunicación de malware. Otros comportamientos pueden incluir volúmenes inusualmente altos de transferencias de datos y puertos poco comunes que se utilizan. La ventaja de este enfoque es su capacidad para identificar potencialmente ataques de día cero o amenazas novedosas que no serían detectadas por sistemas basados ​​en la firma. Sin embargo, también tiende a tener tasas falsas más altas debido a la naturaleza dinámica de los comportamientos de la red.

Un tercer enfoque que utilizamos en la elaboración de reglas de IDS/IPS es el análisis de protocolo con estado. Esta técnica implica comprender y rastrear el estado de los protocolos de red y comparar los comportamientos observados con las transiciones estatales esperadas de estos protocolos. Al realizar un seguimiento del estado de cada conexión, podemos identificar desviaciones del comportamiento esperado que podría sugerir una actividad maliciosa.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de los comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Espere aproximadamente 5-6 minutos antes de iniciar una conexión utilizando el protocolo de escritorio remoto (RDP). ¡Es posible que deba probar 2-3 veces antes de establecer una conexión RDP exitosa!

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution /relax-order-checks +glyph-cache
```

Ahora, exploraremos varios ejemplos de desarrollo de reglas de Suricata para obtener una comprensión sólida de los diferentes enfoques que podemos adoptar y la estructura de una regla.

# **Suricata Desarrollo de reglas Ejemplo 1: Detección del imperio PowerShell**

Desarrollo de reglas de Suricata Parte 1

```
alert http$HOME_NET any -> $EXTERNAL_NET any (msg:"ET MALWARE Possible PowerShell Empire Activity Outbound"; flow:established,to_server; content:"GET"; http_method; content:"/"; http_uri; depth:1; pcre:"/^(?:login\/process|admin\/get|news)\.php$/RU"; content:"session="; http_cookie; pcre:"/^(?:[A-Z0-9+/]{4})*(?:[A-Z0-9+/]{2}==|[A-Z0-9+/]{3}=|[A-Z0-9+/]{4})$/CRi"; content:"Mozilla|2f|5.0|20 28|Windows|20|NT|20|6.1"; http_user_agent; http_start; content:".php|20|HTTP|2f|1.1|0d 0a|Cookie|3a 20|session="; fast_pattern; http_header_names; content:!"Referer"; content:!"Cache"; content:!"Accept"; sid:2027512; rev:1;)
```

La regla de Suricata anterior está diseñada para detectar una posible actividad de salida de[Imperio PowerShell](https://github.com/EmpireProject/Empire), un marco común posterior a la explotación utilizado por los atacantes. Desglosemos las partes importantes de esta regla para comprender sus trabajos.

- `alert`: Esta es la acción de la regla, lo que indica que Suricata debería generar una alerta cada vez que se cumplan las condiciones especificadas en las opciones de regla.
- `http`: Este es el protocolo de regla. Especifica que la regla se aplica al tráfico HTTP.
- `$HOME_NET any -> $EXTERNAL_NET any`: Estas son las especificaciones de la dirección IP de origen y destino. La regla se activará cuando el tráfico HTTP se origine desde cualquier puerto (cualquiera) en un host dentro del`$HOME_NET`(red interna) y está destinado a cualquier puerto (cualquiera) en un host en el`$EXTERNAL_NET`(red externa).
- `msg:"ET MALWARE Possible PowerShell Empire Activity Outbound"`: Este es el mensaje que se incluirá en la alerta para describir lo que está buscando la regla.
- `flow:established,to_server`: Esto especifica la dirección del tráfico. La regla está buscando conexiones establecidas donde los datos fluyen al servidor.
- `content:"GET"; http_method;`: Esto coincide con el método HTTP Get en la solicitud HTTP.
- `content:"/"; http_uri; depth:1;`: Esto coincide con el directorio raíz ("/") en el URI.
- `pcre:"/^(?:login\/process|admin\/get|news)\.php$/RU";`: Esta expresión regular compatible con Perl (PCRE) está buscando URI que terminen con login/process.php, admin/get.php o news.php.
    - `PowerShell Empire`es un marco de comando y control de código abierto (C2). Su agente se puede explorar a través del siguiente repositorio.[https://github.com/empireproject/empire/blob/master/data/agent/agent.ps1#l78](https://github.com/EmpireProject/Empire/blob/master/data/agent/agent.ps1#L78)
    - Examinar el`psempire.pcap`archivo que se encuentra en el`/home/htb-student/pcaps`Directorio del objetivo de esta sección usando Wireshark para identificar las solicitudes relacionadas.
- `content:"session="; http_cookie;`: Esto está buscando la cadena "session =" en la cookie HTTP.
- `pcre:"/^(?:[A-Z0-9+/]{4})*(?:[A-Z0-9+/]{2}==|[A-Z0-9+/]{3}=|[A-Z0-9+/]{4})$/CRi";`: Este PCRE está verificando los datos codificados en Base64 en la cookie.
    - Una gran cantidad de artículos que examinan`PowerShell Empire`Existen, aquí hay uno que señala que las cookies utilizadas por el imperio de PowerShell se adhieren al estándar de codificación Base64.[https://www.keysight.com/blogs/tech/nwvs/2021/06/16/empire-c2-networking-into-the-dark-side](https://www.keysight.com/blogs/tech/nwvs/2021/06/16/empire-c2-networking-into-the-dark-side)
- `content:"Mozilla|2f|5.0|20 28|Windows|20|NT|20|6.1"; http_user_agent; http_start;`: Esto coincide con una cadena específica de agente de usuario que incluye "Mozilla/5.0 (Windows NT 6.1".
    - [https://github.com/empireproject/empire/blob/master/data/agent/agent.ps1#l78](https://github.com/EmpireProject/Empire/blob/master/data/agent/agent.ps1#L78)
- `content:".php|20|HTTP|2f|1.1|0d 0a|Cookie|3a 20|session="; fast_pattern; http_header_names;`: Esto coincide con un patrón en los encabezados HTTP que comienza con ".php http/1.1 \ r \ ncookie: session =".
- `content:!"Referer"; content:!"Cache"; content:!"Accept";`: Estas son coincidencias de contenido negativo. La regla solo se activará si los encabezados HTTP no contienen "referente", "caché" y "aceptar".

Esta regla de Suricata desencadena una alerta cuando detecta una solicitud HTTP establecida de nuestra red a una red externa, con un patrón específico en los campos de URI, cookies y agentes de usuario, y excluyendo ciertos encabezados.

La regla anterior ya está incorporada en el`local.rules`archivo encontrado en el`/home/htb-student`directorio del objetivo de esta sección. Para probarlo, primero, debe desenchufar la regla. Luego, ejecute Suricata en el`psempire.pcap`archivo, que se encuentra en el`/home/htb-student/pcaps`directorio.

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ sudo suricata -r /home/htb-student/pcaps/psempire.pcap -l . -k none 15/7/2023 -- 03:57:42 - <Notice> - This is Suricata version 4.0.0-beta1 RELEASE
15/7/2023 -- 03:57:42 - <Notice> - all 5 packet processing threads, 4 management threads initialized, engine started.
15/7/2023 -- 03:57:42 - <Notice> - Signal Received.  Stopping engine.
15/7/2023 -- 03:57:42 - <Notice> - Pcap-file module read 511 packets, 101523 bytes

```

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ cat fast.log11/21/2017-05:04:53.950737  [**] [1:2027512:1] ET MALWARE Possible PowerShell Empire Activity Outbound [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.56.14:50447 -> 51.15.197.127:80
11/21/2017-05:04:01.308390  [**] [1:2027512:1] ET MALWARE Possible PowerShell Empire Activity Outbound [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.56.14:50436 -> 51.15.197.127:80
11/21/2017-05:05:20.249515  [**] [1:2027512:1] ET MALWARE Possible PowerShell Empire Activity Outbound [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.56.14:50452 -> 51.15.197.127:80
11/21/2017-05:05:56.849190  [**] [1:2027512:1] ET MALWARE Possible PowerShell Empire Activity Outbound [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.56.14:50459 -> 51.15.197.127:80
11/21/2017-05:06:02.062235  [**] [1:2027512:1] ET MALWARE Possible PowerShell Empire Activity Outbound [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.56.14:50460 -> 51.15.197.127:80
11/21/2017-05:06:17.750895  [**] [1:2027512:1] ET MALWARE Possible PowerShell Empire Activity Outbound [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.56.14:50463 -> 51.15.197.127:80
11/21/2017-05:04:11.988856  [**] [1:2027512:1] ET MALWARE Possible PowerShell Empire Activity Outbound [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.56.14:50439 -> 51.15.197.127:80
---SNIP---

```

El`local.rules`El archivo contiene otra regla para detectar`PowerShell Empire`, ubicado directamente debajo de la regla que acabamos de examinar. Invertir algo de tiempo en la escrutinización de los dos`psempire.pcap`archivo usando`Wireshark`Y esta regla recién encontrada para comprender cómo funciona.

# **Desarrollo de reglas de Suricata Ejemplo 2: Detección del pacto**

Desarrollo de reglas de Suricata Parte 1

```
alert tcp any any ->$HOME_NET any (msg:"detected by body"; content:"<title>Hello World!</title>"; detection_filter: track by_src, count 4 , seconds 10; priority:1; sid:3000011;)
```

**Fuente de reglas**: [IDS basados ​​en la firma para detección de tráfico C2 cifrado - Eduardo Macedo](https://repositorio-aberto.up.pt/bitstream/10216/142718/2/572020.pdf)

La regla (ineficiente) suricata anterior está diseñada para detectar ciertas variaciones de[Pacto](https://github.com/cobbr/Covenant), otro marco común posterior a la explotación utilizado por los atacantes. Desglosemos las partes importantes de esta regla para comprender sus trabajos.

- `alert`: Esta es la acción de la regla. Cuando se cumplen las condiciones en las opciones de regla, Suricata generará una alerta.
- `tcp`: Este es el protocolo de regla. La regla se aplica al tráfico TCP.
- `any any -> $HOME_NET any`: Estos son la dirección IP de origen y destino y las especificaciones de puerto. La regla está observando el tráfico TCP que se origina en cualquier IP y cualquier puerto (`any any`) y está destinado a cualquier puerto (`any`) en un anfitrión en el`$HOME_NET`(red interna).
- `content:"<title>Hello World!</title>";`: Esto instruye a Suricata a buscar la cadena`<title>Hello World!</title>`En la carga útil TCP.
    - `Covenant`es un marco de comando y control de código abierto (C2). Sus bases se pueden explorar a través del siguiente repositorio.[https://github.com/cobbr/covenant/blob/master/covenant/data/profiles/defaulthttpprofile.yaml#l35](https://github.com/cobbr/Covenant/blob/master/Covenant/Data/Profiles/DefaultHttpProfile.yaml#L35)
    - Examinar el`covenant.pcap`archivo que se encuentra en el`/home/htb-student/pcaps`Directorio del objetivo de esta sección usando Wireshark para identificar las solicitudes relacionadas.
- `detection_filter: track by_src, count 4, seconds 10;`: Este es un filtro posterior a la detección. Especifica que la regla debe rastrear la dirección IP de origen (`by_src`) y solo activará una alerta si esta misma detección ocurre al menos 4 veces (`count 4`) dentro de una ventana de 10 segundos (`seconds 10`).
    - Examinar el`covenant.pcap`archivo que se encuentra en el`/home/htb-student/pcaps`Directorio del objetivo de esta sección usando Wireshark para identificar las solicitudes relacionadas.

Esta regla de Suricata está diseñada para generar una alerta de alta prioridad si detecta al menos cuatro instancias de tráfico TCP en diez segundos que contienen la cadena`<title>Hello World!</title>`En la carga útil, originada de la misma fuente de IP y se dirigió hacia cualquier host en nuestra red interna.

La regla anterior ya está incorporada en el`local.rules`archivo encontrado en el`/home/htb-student`directorio del objetivo de esta sección. Para probarlo, primero, debe desenchufar la regla. Luego, ejecute Suricata en el`covenant.pcap`archivo, que se encuentra en el`/home/htb-student/pcaps`directorio.

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ sudo suricata -r /home/htb-student/pcaps/covenant.pcap -l . -k none15/7/2023 -- 04:47:15 - <Notice> - This is Suricata version 4.0.0-beta1 RELEASE
15/7/2023 -- 04:47:15 - <Notice> - all 5 packet processing threads, 4 management threads initialized, engine started.
15/7/2023 -- 04:47:16 - <Notice> - Signal Received.  Stopping engine.
15/7/2023 -- 04:47:16 - <Notice> - Pcap-file module read 27384 packets, 3125549 bytes

```

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ cat fast.log01/21/2021-06:38:51.250048  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50366
01/21/2021-06:40:55.021993  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50375
01/21/2021-06:36:21.280144  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50358
01/21/2021-06:41:53.395248  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50378
01/21/2021-06:42:21.582624  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50379
01/21/2021-06:41:25.215525  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50377
01/21/2021-07:17:01.778365  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50462
01/21/2021-07:12:55.294094  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50454
01/21/2021-07:14:27.846352  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50457
01/21/2021-07:17:29.981168  [**] [1:3000011:0] detected by body [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:50463
---SNIP---

```

El`local.rules`El archivo contiene tres (3) otras reglas para detectar`Covenant`, ubicado directamente debajo de la regla que acabamos de examinar. Invertir algo de tiempo en examinar[https://github.com/cobbr/covenant/blob/master/covenant/data/profiles/defaulthttpprofile.yaml](https://github.com/cobbr/Covenant/blob/master/Covenant/Data/Profiles/DefaultHttpProfile.yaml), el`covenant.pcap`archivo usando`Wireshark`, y estas reglas recién encontradas para comprender cómo funcionan. Estas reglas pueden producir resultados falsos positivos y, por lo tanto, para un rendimiento óptimo, es aconsejable integrarlas con otras reglas de detección.

# **Desarrollo de reglas de Suricata Ejemplo 3: Detección del pacto (usando análisis)**

Desarrollo de reglas de Suricata Parte 1

```
alert tcp$HOME_NET any -> any any (msg:"detected by size and counter"; dsize:312; detection_filter: track by_src, count 3 , seconds 10; priority:1; sid:3000001;)
```

El`local.rules`El archivo también contiene la regla anterior para detectar`Covenant`. Desglosemos las partes importantes de esta regla para comprender sus trabajos.

- `dsize:312;`: Esto instruye a Suricata que busque el tráfico TCP con un tamaño de carga útil de datos de exactamente 312 bytes.
- `detection_filter: track by_src, count 3 , seconds 10;`: Este es un filtro posterior a la detección. Dice que la regla debe realizar un seguimiento de la dirección IP de origen (`by_src`), y solo activará una alerta si detecta la misma regla golpe al menos 3 veces (`count 3`) dentro de una ventana de 10 segundos (`seconds 10`).

Esta regla de Suricata está diseñada para generar una alerta de alta prioridad si detecta al menos tres instancias de tráfico TCP en diez segundos que contienen una carga útil de datos de exactamente 312 bytes, todos originados de la misma IP de origen dentro de nuestra red y se dirigen a cualquier parte.

La regla anterior ya está incorporada en el`local.rules`archivo encontrado en el`/home/htb-student`directorio del objetivo de esta sección. Para probarlo, primero, debe desenchufar la regla. Luego, ejecute Suricata en el`covenant.pcap`archivo, que se encuentra en el`/home/htb-student/pcaps`directorio.

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ sudo suricata -r /home/htb-student/pcaps/covenant.pcap -l . -k none15/7/2023 -- 05:29:19 - <Notice> - This is Suricata version 4.0.0-beta1 RELEASE
15/7/2023 -- 05:29:19 - <Notice> - all 5 packet processing threads, 4 management threads initialized, engine started.
15/7/2023 -- 05:29:20 - <Notice> - Signal Received.  Stopping engine.
15/7/2023 -- 05:29:20 - <Notice> - Pcap-file module read 27384 packets, 3125549 bytes

```

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ cat fast.log01/21/2021-06:45:21.609212  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50386
01/21/2021-06:48:49.965761  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50395
01/21/2021-06:42:49.682887  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50380
01/21/2021-06:49:20.143398  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50396
01/21/2021-06:50:49.706170  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50400
01/21/2021-06:51:21.905950  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50401
01/21/2021-06:50:18.527587  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50399
01/21/2021-06:52:52.484676  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50406
01/21/2021-06:51:51.090923  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50404
01/21/2021-06:55:56.650678  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50413
01/21/2021-06:53:22.680676  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50407
01/21/2021-06:54:25.067327  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50409
01/21/2021-06:54:55.275951  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50410
01/21/2021-06:57:25.201284  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50416
01/21/2021-06:57:53.387489  [**] [1:3000001:0] detected by size and counter [**] [Classification: (null)] [Priority: 1] {TCP} 157.230.93.100:80 -> 10.0.0.61:
50417
---SNIP---

```

Invertir algo de tiempo en la escrutinización de los dos`covenant.pcap`archivo usando`Wireshark`Y esta regla recién encontrada para comprender cómo funciona.

# **Desarrollo de reglas de Suricata Ejemplo 4: Detección de Sliver**

Desarrollo de reglas de Suricata Parte 1

```
alert tcp any any -> any any (msg:"Sliver C2 Implant Detected"; content:"POST"; pcre:"/\/(php|api|upload|actions|rest|v1|oauth2callback|authenticate|oauth2|oauth|auth|database|db|namespaces)(.*?)((login|signin|api|samples|rpc|index|admin|register|sign-up)\.php)\?[a-z_]{1,2}=[a-z0-9]{1,10}/i"; sid:1000007; rev:1;)

```

**Fuente de reglas**: [https://www.bilibili.com/read/cv19510951/](https://www.bilibili.com/read/cv19510951/)

La regla de Suricata anterior está diseñada para detectar ciertas variaciones de[Astilla](https://github.com/BishopFox/sliver), otro marco común posterior a la explotación utilizado por los atacantes. Desglosemos las partes importantes de esta regla para comprender sus trabajos.

- `content:"POST";`: Esta opción instruye a Suricata que busque el tráfico TCP que contiene la cadena "Post".
- `pcre:"/\/(php|api|upload|actions|rest|v1|oauth2callback|authenticate|oauth2|oauth|auth|database|db|namespaces)(.*?)((login|signin|api|samples|rpc|index|admin|register|sign-up)\.php)\?[a-z_]{1,2}=[a-z0-9]{1,10}/i";`: Esta expresión regular se utiliza para identificar patrones de URI específicos en el tráfico. Hará que coincida con URI que contengan nombres particulares de directorio seguidos de nombres de archivos que terminan con una extensión de PHP.
    - `Sliver`es un marco de comando y control de código abierto (C2). Sus bases se pueden explorar a través del siguiente repositorio.[https://github.com/bishopfox/sliver/blob/master/server/configs/http-c2.go#294](https://github.com/BishopFox/sliver/blob/master/server/configs/http-c2.go#L294)
    - Examinar el`sliver.pcap`archivo que se encuentra en el`/home/htb-student/pcaps`Directorio del objetivo de esta sección usando Wireshark para identificar las solicitudes relacionadas.

La regla anterior ya está incorporada en el`local.rules`archivo encontrado en el`/home/htb-student`directorio del objetivo de esta sección. Para probarlo, primero, debe desenchufar la regla. Luego, ejecute Suricata en el`sliver.pcap`archivo, que se encuentra en el`/home/htb-student/pcaps`directorio.

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ sudo suricata -r /home/htb-student/pcaps/sliver.pcap -l . -k none16/7/2023 -- 02:27:50 - <Notice> - This is Suricata version 4.0.0-beta1 RELEASE
16/7/2023 -- 02:27:50 - <Notice> - all 5 packet processing threads, 4 management threads initialized, engine started.
16/7/2023 -- 02:27:50 - <Notice> - Signal Received.  Stopping engine.
16/7/2023 -- 02:27:50 - <Notice> - Pcap-file module read 36 packets, 18851 bytes

```

Desarrollo de reglas de Suricata Parte 1

```
xnoxos@htb[/htb]$ cat fast.log01/23/2023-15:14:46.988537  [**] [1:1000002:1] Sliver C2 Implant Detected - POST [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.4.90:50681 -> 192.168.4.85:80
01/23/2023-15:14:47.321224  [**] [1:1000002:1] Sliver C2 Implant Detected - POST [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.4.90:50684 -> 192.168.4.85:80
01/23/2023-15:14:48.074797  [**] [1:1000002:1] Sliver C2 Implant Detected - POST [**] [Classification: (null)] [Priority: 3] {TCP} 192.168.4.90:50687 -> 192.168.4.85:80

```

El`local.rules`El archivo contiene otra regla para detectar`Sliver`, ubicado directamente debajo de la regla que acabamos de examinar.

Desarrollo de reglas de Suricata Parte 1

```
alert tcp any any -> any any (msg:"Sliver C2 Implant Detected - Cookie"; content:"Set-Cookie"; pcre:"/(PHPSESSID|SID|SSID|APISID|csrf-state|AWSALBCORS)\=[a-z0-9]{32}\;/"; sid:1000003; rev:1;)

```

Desglosemos las partes importantes de esta regla para comprender sus trabajos.

- `content:"Set-Cookie";`: Esta opción instruye a Suricata que busque el tráfico TCP que contiene la cadena`Set-Cookie`.
- `pcre:"/(PHPSESSID|SID|SSID|APISID|csrf-state|AWSALBCORS)\=[a-z0-9]{32}\;/";`: Esta es una expresión regular utilizada para identificar patrones específicos de establecimiento de cookies en el tráfico. Coincide con el`Set-Cookie`Encabezado cuando establece nombres de cookies específicos (PhpSessid, SID, SSID, APISID, CSRF-estado, AWSalbcors) con un valor que es una cadena alfanumérica de 32 caracteres.

Invertir algo de tiempo en la escrutinización del`sliver.pcap`archivo usando`Wireshark`para identificar las solicitudes relacionadas.