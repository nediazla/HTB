# Hunting For Stuxbot

# **Informe de inteligencia sobre amenazas: Stuxbot**

El presente informe de Threat Intelligence subraya la amenaza inmediata que representa el colectivo organizado de cibercrimen conocido como "Stuxbot". El grupo inició sus campañas de phishing a principios de este año y opera con un amplio alcance, aprovechando las oportunidades a medida que surgen, sin ninguna estrategia de orientación específica; su lema parece ser cualquiera, en cualquier momento. La motivación principal detrás de sus acciones parece ser el espionaje, ya que no ha habido indicios de que hayan extraído planos confidenciales, información comercial patentada o que hayan buscado ganancias financieras a través de métodos como ransomware o chantaje.

- Plataformas en la mira: `Microsoft Windows`
- Entidades amenazadas: `Windows Users`
- Impacto potencial: `Complete takeover of the victim's computer / Domain escalation`
- Nivel de riesgo: `Critical`

El grupo aprovecha principalmente el phishing oportunista para el acceso inicial, explotando datos de las redes sociales, infracciones pasadas (por ejemplo, bases de datos de direcciones de correo electrónico) y sitios web corporativos. Hay escasa evidencia que sugiera phishing dirigido contra individuos específicos.

El documento recopila todas las Técnicas y Procedimientos Tácticos (TTP) e Indicadores de Compromiso (IOC) conocidos vinculados al grupo, que actualmente se encuentran en continuo perfeccionamiento. Este boceto preliminar es confidencial y está destinado exclusivamente a nuestros socios, a quienes se recomienda encarecidamente que realicen análisis de sus infraestructuras para detectar posibles infracciones exitosas en la etapa más temprana posible.

En resumen, la secuencia de ataque para el dispositivo inicialmente comprometido se puede presentar de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/214/lifecycle.png)

**`Initial Breach`**

El correo electrónico de phishing es relativamente rudimentario y el malware se hace pasar por un archivo de factura. A continuación se muestra un ejemplo de un correo electrónico de phishing real que incluye un vínculo que conduce a un archivo de OneNote:

![](https://academy.hackthebox.com/storage/modules/214/email.png)

Nuestra investigación forense sobre estos ataques reveló que el enlace dirige a un archivo OneNote, que siempre ha estado alojado en un servicio de alojamiento de archivos (por ejemplo, Mega.io o plataformas similares).

Este archivo de OneNote se hace pasar por una factura y presenta un botón "OCULTO" que activa un archivo por lotes incrustado. Este archivo por lotes, a su vez, recupera scripts de PowerShell, que representan la etapa 0 de la carga maliciosa.

**`RAT Characteristics`**

El RAT desplegado en estos ataques es modular, lo que implica que puede ampliarse con una gama infinita de capacidades. Si bien solo se puede acceder a unas pocas funciones una vez que se prepara el RAT, hemos notado el uso de herramientas que capturan volcados de pantalla, ejecutan [Mimikatz](https://attack.mitre.org/software/S0002/), proporciona un interactivo`CMD shell`en máquinas comprometidas, etc.

**`Persistence`**

Todos los mecanismos de persistencia utilizados hasta la fecha han implicado un archivo EXE depositado en el disco.

**`Lateral Movement`**

Hasta ahora, hemos identificado dos métodos distintos para el movimiento lateral:

- Aprovechando el PsExec original firmado por Microsoft
- Usando WinRM

**`Indicators of Compromise (IOCs)`**

A continuación se proporciona un inventario completo de todas las IOC identificadas hasta el momento.

**Archivo de OneNote**:

- https://transfer.sh/get/kNxU7/invoice.one
- https://mega.io/dl9o1Dz/invoice.one

**Entidad provisional (script de PowerShell)**:

- https://pastebin.com/raw/AvHtdKb2
- https://pastebin.com/raw/gj58DKz

**Nodos de comando y control (C&C)**:

- 91.90.213.14:443
- 103.248.70.64:443
- 141.98.6.59:443

**Hashes criptográficos de archivos involucrados (SHA256)**:

- 226A723FFB4A91D9950A8B266167C5B354AB0DB1DC225578494917FE53867EF2
- C346077DAD0342592DB753FE2AB36D2F9F1C76E55CF8556FE5CDA92897E99C7E
- 018D37CBD3878258C29DB3BC3F2988B6AE688843801B9ABC28E6151141AB66D4

---

# **Buscando Stuxbot con el Elastic Stack**

Navegue hasta el final de esta sección y haga clic en `Click here to spawn the target system!`

Ahora navega hasta `http://[Target IP]:5601`, haga clic en el botón de navegación lateral y haga clic en "Descubrir". Luego, haga clic en el ícono del calendario, especifique "últimos 15 años" y haga clic en "Aplicar".

Por favor especifique también un `Europe/Copenhagen` zona horaria, a través del siguiente enlace`http://[Target IP]:5601/app/management/kibana/settings`.

![](https://academy.hackthebox.com/storage/modules/214/hunt22.png)

---

`The Available Data`

La estrategia de ciberseguridad implementada se basa en la utilización de Elastic stack como solución SIEM. A través de la funcionalidad "Descubrir" podemos ver registros de múltiples fuentes. Estas fuentes incluyen:

- `Windows audit logs`(categorizado bajo las ventanas de patrón de índice*)
- `System Monitor (Sysmon) logs`(también incluido en el patrón de índice windows*, más sobre Sysmon [aquí](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon))
- `PowerShell logs`(también indexado en Windows*, más información sobre los registros de PowerShell) [aquí](https://www.splunk.com/en_us/blog/security/hunting-for-malicious-powershell-using-script-block-logging.html))
- `Zeek logs`, [una herramienta de monitoreo de seguridad de red](https://www.elastic.co/guide/en/beats/filebeat/current/exported-fields-zeek.html) (clasificado bajo el patrón de índice zeek*)

Nuestra inteligencia sobre amenazas disponible proviene de marzo de 2023, por lo que es imperativo que nuestra configuración de Kibana analice los registros que se remontan al menos a este período de tiempo. Nuestro índice "windows" contiene alrededor de 118.975 registros, mientras que el índice "zeek" alberga aproximadamente 332.261 registros.

`The Environment`

Nuestra organización es relativamente pequeña, con alrededor de 200 empleados dedicados principalmente a actividades de marketing online, por lo que nuestra necesidad de recursos de TI es mínima. Las aplicaciones de Office son el software principal en uso, siendo Gmail nuestro proveedor de correo electrónico estándar, al que se accede a través de un navegador web. Microsoft Edge es el navegador predeterminado en las computadoras portátiles de nuestra empresa. El soporte técnico remoto se proporciona a través de TeamViewer y todos los dispositivos de nuestra empresa se administran a través de objetos de política de grupo (GPO) de Active Directory. Estamos considerando una transición a Microsoft Intune para la administración de terminales como parte de una próxima actualización de Windows 10 a Windows 11.

`The Task`

Nuestra tarea se centra en un informe de inteligencia de amenazas sobre un software malicioso conocido como "Stuxbot". Se espera que utilicemos los Indicadores de compromiso (IOC) proporcionados para investigar si hay signos de compromiso en nuestra organización.

`The Hunt`

La secuencia de actividades de búsqueda se basa en la hipótesis de que un correo electrónico de phishing exitoso entregue un archivo OneNote malicioso. Si nuestra hipótesis hubiera sido la ejecución exitosa de un binario con un hash que coincidiera con uno del informe de inteligencia de amenazas, habríamos emprendido una secuencia diferente de actividades.

El informe indica que todos los compromisos iniciales se produjeron a través de archivos "invoice.one". A pesar de esto, debemos continuar realizando búsquedas en otras IOC, ya que los actores de amenazas pueden haber introducido diferentes técnicas de entrega entre el momento en que se creó el informe y el presente. Volviendo a los archivos "invoice.one", se puede iniciar una búsqueda exhaustiva basada en[ID de evento de Sysmon 15](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90015) (FileCreateStreamHash), que representa un evento de descarga de archivos del navegador. Suponemos que se descargó un archivo OneNote potencialmente malicioso de Gmail, el proveedor de correo electrónico de nuestra organización.

Nuestra consulta de búsqueda debería ser la siguiente.

**Campos relacionados**: [winlog.event_id](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html) o [código.evento](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html) y [Nombre del archivo](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
event.code:15 AND file.name:*invoice.one

```

![](https://academy.hackthebox.com/storage/modules/214/hunt1.png)

Si bien este desarrollo podría implicar implicaciones graves, aún no se ha confirmado si este archivo es el mismo que se menciona en el informe. Además, no se han investigado señales de ejecución. Si ampliamos el registro de eventos para mostrar su contenido completo, revelará que MSEdge era la aplicación (como lo indica`process.name`o `process.executable`) utilizado para descargar el archivo, que estaba almacenado en la carpeta Descargas de un empleado llamado Bob.

La marca de tiempo a tener en cuenta es:`March 26, 2023 @ 22:05:47`

Podemos corroborar esta información examinando [ID de evento de Sysmon 11](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90011) (Creación de archivo) y el nombre del archivo "invoice.one". Este método es especialmente eficaz cuando los navegadores no participan en el proceso de descarga de archivos. La consulta es similar a la anterior, pero el asterisco está al final ya que el nombre del archivo incluye solo el nombre del archivo con un Identificador de zona adicional, lo que probablemente indica que el archivo se originó en Internet.

**Campos relacionados**: [winlog.event_id](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html) o [código.evento](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html) y [Nombre del archivo](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
event.code:11 AND file.name:invoice.one*

```

![](https://academy.hackthebox.com/storage/modules/214/hunt2.png)

Es relativamente fácil deducir que la máquina que reportó el archivo "invoice.one" tiene el nombre de host WS001 (verifique el `host.hostname` o`host.name`campos del evento Sysmon Event ID 11 que estábamos viendo) y una dirección IP de 192.168.28.130, que se puede confirmar verificando cualquier evento de conexión de red (Sysmon Event ID 3) de esta máquina (ejecute la siguiente consulta`event.code:3 AND host.hostname:WS001`y comprobar el `source.ip` campo).

Si inspeccionamos las conexiones de red aprovechando [ID de evento de Sysmon 3](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90003) (Conexión de red) aproximadamente en el momento en que se descargó este archivo, encontraremos que Sysmon no tiene entradas. Esta es una configuración común para evitar capturar conexiones de red creadas por los navegadores, lo que podría generar un volumen abrumador de registros, particularmente aquellos relacionados con nuestro proveedor de correo electrónico.

Aquí es donde los registros de Zeek resultan invaluables. Deberíamos filtrar y examinar las consultas DNS que Zeek ha capturado desde WS001 durante el intervalo desde`22:05:00`a `22:05:48`, cuando se descargó el archivo.

Nuestro `Zeek query` buscará una IP de origen que coincida con 192.168.28.130 y, dado que estamos consultando sobre consultas de DNS, solo seleccionaremos registros que tengan algo en el`dns.question.name`campo. Tenga en cuenta que esto devolverá una gran cantidad de ruido común, como google.com, etc., por lo que es necesario filtrarlo. Aquí está la consulta y algunos filtros.

**Campos relacionados**: [fuente.ip](https://www.elastic.co/guide/en/ecs/current/ecs-source.html) y [dns.nombre.pregunta](https://www.elastic.co/guide/en/ecs/current/ecs-dns.html)

A la caza de Stuxbot

```
source.ip:192.168.28.130 AND dns.question.name:*

```

![](https://academy.hackthebox.com/storage/modules/214/hunt3.png)

Podemos identificar fácilmente las principales fuentes de ruido observando los valores más comunes que Kibana ha detectado (haga clic en un campo de la siguiente manera) y luego aplique un filtro a los ruidosos conocidos.

![](https://academy.hackthebox.com/storage/modules/214/hunt23.png)

Como parte de nuestro proceso de búsqueda, dado que estamos interesados ​​en nombres DNS, nos gustaría mostrar solo los `dns.question.name` campo en la tabla de resultados. Tenga en cuenta el tiempo especificado. `March 26th 2023 @ 22:05:00` a`March 26th 2023 @ 22:05:48`.

![](https://academy.hackthebox.com/storage/modules/214/hunt24.png)

![](https://academy.hackthebox.com/storage/modules/214/hunt4.png)

Al desplazarnos hacia abajo en la tabla de entradas, observamos las siguientes actividades.

![](https://academy.hackthebox.com/storage/modules/214/hunt5.png)

De estos datos, deducimos que el usuario accedió a Google Mail, seguido de interacción con "file.io", un conocido proveedor de alojamiento. Posteriormente, Microsoft Defender SmartScreen inició un análisis de archivos, que normalmente se activa cuando se descarga un archivo a través de Microsoft Edge. Al expandir la entrada del registro para file.io se revelan las direcciones IP devueltas (`dns.answers.data`o `dns.resolved_ip` o `zeek.dns.answers` campos) de la siguiente manera.

`34.197.10.85`, `3.213.216.16`

Ahora, si ejecutamos una búsqueda de conexiones a estas direcciones IP durante el mismo período de tiempo que la consulta DNS, obtenemos los siguientes hallazgos.

![](https://academy.hackthebox.com/storage/modules/214/hunt6.png)

Esta información corrobora que un usuario, Bob, descargó exitosamente el archivo "invoice.one" del proveedor de hosting "file.io".

En este momento, tenemos dos opciones: podemos cruzar los datos con el informe de Threat Intel para identificar información superpuesta dentro de nuestro entorno, o podemos llevar a cabo una investigación similar a la Respuesta a Incidentes (IR) para rastrear la secuencia de eventos posteriores. la descarga del archivo OneNote. Elegimos proceder con el último enfoque, rastreando las actividades posteriores.

Hipotéticamente, si se accediera a "invoice.one", se abriría con la aplicación OneNote. Entonces, la siguiente consulta marcará el evento, si ocurrió.**Nota**: El período de tiempo que especificamos anteriormente debe eliminarse y establecerlo nuevamente en, digamos, 15 años. El `dns.question.name` La columna también debe eliminarse.

![](https://academy.hackthebox.com/storage/modules/214/hunt25.png)

**Campos relacionados**: [winlog.event_id](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html) o [código.evento](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html) y [proceso.command_line](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
event.code:1 AND process.command_line:*invoice.one*

```

![](https://academy.hackthebox.com/storage/modules/214/hunt7.png)

De hecho, encontramos que se accedió al archivo OneNote poco después de su descarga, con un retraso de aproximadamente 6 segundos. Ahora, con OneNote.exe en funcionamiento y el archivo abierto, podemos especular que contiene un enlace malicioso o un archivo adjunto malévolo. En cualquier caso, OneNote.exe iniciará un navegador o un archivo malicioso. Por lo tanto, debemos examinar cualquier proceso nuevo en el que OneNote.exe sea el proceso principal. La consulta correspondiente es la siguiente.[ID de evento de Sysmon 1](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90001) Se utiliza (creación de procesos).

**Campos relacionados**: [winlog.event_id](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html) o [código.evento](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html) y [proceso.padre.nombre](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
event.code:1 AND process.parent.name:"ONENOTE.EXE"

```

![](https://academy.hackthebox.com/storage/modules/214/hunt8.png)

Los resultados de esta consulta presentan tres resultados. Sin embargo, uno de ellos (el de abajo) queda fuera del plazo pertinente y puede ser descartado. Evaluando los otros dos resultados:

- La entrada del medio documenta (cuando está expandida) un nuevo proceso, OneNoteM.exe, que es un componente de OneNote y ayuda a iniciar archivos.
- La entrada superior revela "cmd.exe" en funcionamiento, ejecutando un archivo llamado "invoice.bat". Aquí está la vista al expandir el registro.

![](https://academy.hackthebox.com/storage/modules/214/hunt9.png)

Ahora podemos establecer una conexión entre "OneNote.exe", el sospechoso "invoice.one" y la ejecución de "cmd.exe" que inicia "invoice.bat" desde una ubicación temporal (muy probablemente debido a su archivo adjunto dentro del archivo OneNote). La pregunta ahora es: ¿este script por lotes ha instigado algo más? Busquemos si un proceso principal con un argumento de línea de comando que apunta al archivo por lotes ha generado procesos secundarios con la siguiente consulta.

**Campos relacionados**: [winlog.event_id](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html) o [código.evento](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html) y [proceso.parent.command_line](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
event.code:1 AND process.parent.command_line:*invoice.bat*

```

![](https://academy.hackthebox.com/storage/modules/214/hunt10.png)

Esta consulta devuelve un único resultado: el inicio de PowerShell y los argumentos que se le pasan parecen notoriamente sospechosos (tenga en cuenta que hemos agregado `process.name`, `process.args`, y `process.pid` como columnas)! ¡Un comando para descargar y ejecutar contenido de Pastebin, un proveedor de alojamiento de texto abierto! Podemos intentar acceder y ver si el contenido que el script intentó descargar todavía está disponible (¡de forma predeterminada, no caducará!).

![](https://academy.hackthebox.com/storage/modules/214/hunt28.png)

¡De hecho lo es! Esto se menciona en el informe Threat Intelligence, que indica que se descargó un script de PowerShell de Pastebin.

Para descubrir qué hizo PowerShell, podemos filtrar según el ID y el nombre del proceso para obtener una descripción general de las actividades. Tenga en cuenta que hemos agregado el `event.code` campo como una columna.

**Campos relacionados**: [proceso.pid](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html) y [nombre.proceso](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
process.pid:"9944" and process.name:"powershell.exe"

```

![](https://academy.hackthebox.com/storage/modules/214/hunt12.png)

Inmediatamente, podemos observar resultados intrigantes que indican la creación de archivos, intentos de conexión de red y algunas resoluciones de DNS aprovechando [ID de evento de Sysmon 22](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90022) (DNSEvento). Añadiendo algunos campos informativos adicionales (`file.path`, `dns.question.name`, y`destination.ip`) como columnas para esa vista, podemos expandirla.

![](https://academy.hackthebox.com/storage/modules/214/hunt13.png)

Ahora bien, esto nos presenta abundantes datos sobre las actividades. Es probable que Ngrok se haya empleado como C2 (para enmascarar el tráfico malicioso a un dominio conocido). Si examinamos las conexiones por encima de la resolución DNS de Ngrok, apunta a la dirección IP de destino 443, lo que implica que el tráfico estaba cifrado.

Es probable que el EXE eliminado esté destinado a la persistencia. Su nombre distintivo debería facilitar la determinación de si alguna vez fue ejecutado. Es importante tener en cuenta las marcas de tiempo: hay un lapso de tiempo entre diferentes actividades, lo que sugiere que es menos probable que haya sido escrito, pero tal vez tuvo lugar una interacción humana real (a menos que se produjera un sueño aleatorio entre las acciones ejecutadas). Las acciones finales a las que apunta este proceso son una consulta DNS para DC1 y conexiones a él.

Repasemos los datos de Zeek para obtener información sobre la dirección IP de destino. `18.158.249.75` que acabamos de descubrir. Tenga en cuenta que el`source.ip`, `destination.ip`, y `destination.port` Los campos se agregaron como columnas.

![](https://academy.hackthebox.com/storage/modules/214/hunt14.png)

Curiosamente, la actividad parece haberse extendido hasta el día siguiente. El motivo del cese de la actividad no está claro... ¿Hubo algún cambio en C2 IP? ¿O simplemente se detuvo el ataque? Al inspeccionar las consultas de DNS para "ngrok.io", encontramos que la IP devuelta (`dns.answers.data`) de hecho ha cambiado. Tenga en cuenta que el `dns.answers.data` El campo se agregó como una columna.

![](https://academy.hackthebox.com/storage/modules/214/hunt15.png)

La IP recién descubierta también indica que las conexiones continuaron de manera constante durante los días siguientes.

![](https://academy.hackthebox.com/storage/modules/214/hunt16.png)

Por tanto, es evidente que existe una actividad sostenida en la red y podemos deducir que se ha accedido al C2 de forma continua. Ahora, en cuanto al archivo ejecutable "default.exe" subido anteriormente, ¿se ejecutó alguna vez? Al buscar en los registros de Sysmon un proceso con ese nombre, podemos determinarlo. Tenga en cuenta que el`process.name`, `process.args`, `event.code`, `file.path`, `destination.ip`, y `dns.question.name` Los campos se agregaron como columnas.

**Campo relacionado**: [nombre.proceso](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
process.name:"default.exe"

```

![](https://academy.hackthebox.com/storage/modules/214/hunt17.png)

De hecho, se ejecutó: podemos discernir instantáneamente que el ejecutable inició consultas DNS para Ngrok y estableció conexiones con las direcciones IP C2. También cargó dos archivos "svchost.exe" y "SharpHound.exe". SharpHound es una herramienta reconocida para diagramar Active Directory e identificar rutas de ataque para escalar. En cuanto a svchost.exe, no estamos seguros: ¿será otro agente malicioso? El nombre implica que intenta imitar el archivo svchost legítimo, que forma parte del sistema operativo Windows.

Si nos desplazamos hacia arriba, hay más actividad de este ejecutable, incluida la carga de "payload.exe", un archivo VBS y cargas repetidas de "svchost.exe".

En este momento, nos queda una pregunta: ¿SharpHound ejecutó? ¿El atacante adquirió información sobre Active Directory? Podemos investigar esto con la siguiente consulta (ya que era un archivo ejecutable en disco).

**Campo relacionado**: [nombre.proceso](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
process.name:"SharpHound.exe"

```

![](https://academy.hackthebox.com/storage/modules/214/hunt18.png)

De hecho, la herramienta parece haber sido ejecutada dos veces, con aproximadamente 2 minutos de diferencia entre sí.

Es vital tener en cuenta que Sysmon ha marcado "default.exe" con un hash de archivo (`process.hash.sha256` campo) que se alinea con uno encontrado en el informe Threat Intel. Esto nos lleva a preguntarnos si este ejecutable ha sido detectado en otros dispositivos del entorno. Realicemos una búsqueda amplia. Tenga en cuenta que el`host.hostname`El campo se agregó como una columna.

**Campo relacionado**: [proceso.hash.sha256](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
process.hash.sha256:018d37cbd3878258c29db3bc3f2988b6ae688843801b9abc28e6151141ab66d4

```

![](https://academy.hackthebox.com/storage/modules/214/hunt29.png)

Se han encontrado archivos con este valor hash en WS001 y PKI, lo que indica que el atacante también ha violado el servidor PKI como mínimo. También parece que se ha colocado un archivo de puerta trasera bajo el perfil del usuario "svc-sql1", lo que sugiere que la cuenta de este usuario probablemente esté comprometida.

Al ampliar la primera instancia de ejecución de "default.exe" en PKI, notamos que el proceso principal era "PSEXESVC", un componente de PSExec de SysInternals, una herramienta que se utiliza a menudo para ejecutar comandos de forma remota y que se utiliza con frecuencia para el movimiento lateral en infracciones de Active Directory. .

![](https://academy.hackthebox.com/storage/modules/214/hunt20.png)

Más abajo en el mismo registro, notamos "svc-sql1" en el `user.name` campo, confirmando así el compromiso de este usuario.

¿Cómo se vio comprometida la contraseña de "svc-sql1"? La única explicación plausible de los datos disponibles hasta el momento es potencialmente el script de PowerShell subido anteriormente, aparentemente diseñado para la fuerza bruta de contraseñas. Sabemos que esto se cargó en WS001, por lo que podemos verificar si hay intentos de contraseña exitosos o fallidos desde esa máquina, excluyendo aquellos de Bob, el usuario de esa máquina (y la máquina misma).

**Campos relacionados**: [winlog.event_id](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html) o [código.evento](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html), [winlog.event_data.LogonType](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-winlog.html), y [fuente.ip](https://www.elastic.co/guide/en/beats/winlogbeat/current/exported-fields-ecs.html)

A la caza de Stuxbot

```
(event.code:4624 OR event.code:4625) AND winlog.event_data.LogonType:3 AND source.ip:192.168.28.130

```

![](https://academy.hackthebox.com/storage/modules/214/hunt21.png)

Los resultados son bastante intrigantes: dos intentos fallidos para la cuenta de administrador, aproximadamente en el momento en que se detectó la actividad sospechosa inicial. Posteriormente, hubo numerosos intentos exitosos de inicio de sesión para "svc-sql1". Parece que intentaron descifrar la contraseña del administrador pero fracasaron. Sin embargo, dos días después, el día 28, observamos intentos exitosos con svc-sql1.

En esta etapa, hemos acumulado una cantidad significativa de información para presentar e iniciar una respuesta integral a incidentes, de acuerdo con las políticas de la empresa.