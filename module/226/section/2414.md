# Fundamentos de Zeek

Zeek, según lo definido por sus desarrolladores, es un analizador de tráfico de red de código abierto. Por lo general, se emplea para analizar todo el tráfico en una red, cavando profundamente para cualquier signo de actividad sospechosa o maliciosa. Pero, Zeek no se limita a eso. También puede ser una herramienta útil para solucionar problemas de red y realizar una variedad de medidas dentro de una red. Una vez que implementamos Zeek, nuestros equipos de seguridad cibernética defensiva (o equipos azules) pueden aprovechar inmediatamente una gran cantidad de archivos de registro, que ofrecen una vista elevada de todo tipo de actividad de red. Específicamente, estos registros contienen registros detallados de cada conexión realizada a través de la red, junto con las transcripciones de actividades de la capa de aplicación, como consultas y respuestas DNS, sesiones HTTP y más. Pero las capacidades de Zeek se extienden más allá de la mera registro. Viene incluido con un conjunto de funciones para analizar y detectar actividades de red.

Lo que distingue a Zeek es su lenguaje de secuencias de comandos altamente capaz, que permite a los usuarios crear scripts de Zeek que son funcionalmente similares a las reglas de Suricata. Esta poderosa característica permite que Zeek sea totalmente personalizable y extensible, lo que permite a los miembros del equipo azul desarrollar su propia lógica y estrategias para el análisis de redes y la detección de intrusos.

Teniendo en cuenta la funcionalidad de Zeek, combinada con su capacidad para ejecutarse en hardware estándar y el poderoso lenguaje de secuencias de comandos, queda claro que no estamos viendo otras ID basadas en la firma. En cambio, Zeek es una plataforma que puede facilitar la detección de mal uso semántico, la detección de anomalías y el análisis de comportamiento.

# **Modos de operación de Zeek**

Zeek opera en los siguientes modos:

- Análisis de tráfico completamente pasivo
- interfaz libpcap para captura de paquetes
- Análisis en tiempo real y fuera de línea (por ejemplo, basado en PCAP)
- Soporte de clúster para implementaciones a gran escala

# **Arquitectura de Zeek**

La arquitectura de Zeek comprende dos componentes principales: el`event engine`(o núcleo) y el`script interpreter`.

El`event engine`Toma una corriente de paquete entrante y la transforma en una serie de alto nivel`events`. En el contexto de Zeek, estos`events`Describa la actividad de la red en términos neutrales de política, lo que significa que nos informan de lo que está sucediendo, pero no ofrecen una interpretación o evaluación de ello. Por ejemplo, una solicitud HTTP se transformará en un`http_request`evento. Si bien este evento proporciona todos los detalles de la solicitud, no ofrece ningún juicio o análisis, como si un puerto corresponde a un puerto que se sabe que es utilizado por malware.

Dicha interpretación o análisis es proporcionada por Zeek's`script interpreter`, que ejecuta un conjunto de controladores de eventos escritos en el lenguaje de secuencias de comandos de Zeek (Scripts de Zeek). Estos scripts expresan la política de seguridad del sitio, definiendo acciones que se tomarán en la detección de ciertos eventos.

Los eventos generados por el núcleo de Zeek se colocan en cola de manera ordenada, esperando su turno para ser procesado por orden de llegada. La mayoría de los eventos de Zeek se definen en`.bif`archivos ubicados en el`/scripts/base/bif/plugins/`directorio. Para obtener una lista más completa de eventos, consulte el siguiente recurso:[https://docs.zeek.org/en/stable/scripts/base/bif/](https://docs.zeek.org/en/stable/scripts/base/bif)

# **Logios de Zeek**

En cuanto al registro de Zeek, cuando usamos Zeek para el análisis fuera de línea de un archivo PCAP, los registros se almacenarán en el directorio actual.

Entre la diversa variedad de registros que produce Zeek, algunos familiares incluyen:

- `conn.log`: Este registro proporciona detalles sobre las conexiones IP, TCP, UDP e ICMP.
- `dns.log`: Aquí, encontrará los detalles de las consultas y respuestas DNS.
- `http.log`: Este registro captura los detalles de las solicitudes y respuestas HTTP.
- `ftp.log`: Los detalles de las solicitudes y respuestas FTP se registran aquí.
- `smtp.log`: Este registro cubre las transacciones SMTP, como los detalles del remitente y el destinatario.

Consideremos el`http.log`Como ejemplo. Está repleto de datos valiosos como:

- `host`: El dominio HTTP/IP.
- `uri`: El URI HTTP.
- `referrer`: El referente de la solicitud HTTP.
- `user_agent`: El agente de usuario del cliente.
- `status_code`: El código de estado HTTP.

Para una lista más exhaustiva de registros comunes de Zeek y sus respectivos campos, consulte el siguiente recurso:[https://docs.zeek.org/en/master/logs/index.html](https://docs.zeek.org/en/master/logs/index.html)

Es notable mencionar que Zeek, en su configuración estándar, aplica la compresión GZIP a los archivos de registro cada hora. Los registros más antiguos se transfieren a un directorio nombrado en el`YYYY-MM-DD`formato. Al tratar con estos registros comprimidos, herramientas alternativas como`gzcat`para imprimir registros o`zgrep`Para buscar en los registros puede ser útil.

Puede encontrar ejemplos sobre cómo trabajar con registros de Zeek con confirmación de GZIP en este enlace:[https://blog.rapid7.com/2016/06/02/working-with-bro-logs-queries-by-example/](https://blog.rapid7.com/2016/06/02/working-with-bro-logs-queries-by-example/)

Como se indicó anteriormente, al interactuar con los archivos de registro de Zeek, podemos utilizar comandos de UNIX nativos como CAT o GREP. Sin embargo, Zeek también proporciona una herramienta especializada conocida como`zeek-cut`para manejar archivos de registro. Esta utilidad acepta archivos de registro de Zeek a través de la entrada estándar utilizando tuberías o redirecciones de transmisión y luego ofrece las columnas especificadas a la salida estándar.

Si está interesado en explorar ejemplos de Zeek, casos de uso y aprender los conceptos básicos de escribir scripts de Zeek, eche un vistazo al siguiente enlace:[https://docs.zeek.org/en/stable/examples/index.html](https://docs.zeek.org/en/stable/examples/index.html)

Para obtener una guía de inicio rápido a Zeek, consulte el siguiente enlace:[https://docs.zeek.org/en/stable/quickstart/index.html](https://docs.zeek.org/en/stable/quickstart/index.html)

# **Características clave de Zeek**

Las características clave que refuerzan la efectividad de Zeek incluyen:

- Registro integral de actividades de red
- Análisis de protocolos de la capa de aplicación (independientemente del puerto, que cubren protocolos como HTTP, DNS, FTP, SMTP, SSH, SSL, etc.)
- Capacidad para inspeccionar el contenido de archivo intercambiado sobre los protocolos de la capa de aplicación
- Soporte de IPv6
- Detección y análisis de túneles
- Capacidad para realizar controles de cordura durante el análisis de protocolo
- Coincidencia de patrones tipo IDS
- Poderoso lenguaje de secuencias de comandos de dominio que permite expresar tareas de análisis arbitrarias y administrar el estado de la red a lo largo del tiempo
- Interfaño que genera registros ASCII bien estructurados de forma predeterminada y ofrece backends alternativos para Elasticsearch y DataSeries
- Integración en tiempo real de la entrada externa en los análisis
- Biblioteca C externa para compartir eventos de Zeek con programas externas
- Capacidad para desencadenar procesos externos arbitrarios desde el lenguaje de secuencias de comandos