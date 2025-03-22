# Intrusion Detection With Splunk (Real-world Scenario)

# **Introducción**

El `Windows Event Logs & Finding Evil` El módulo nos familiarizó con la exploración de registros en una sola máquina para identificar actividad maliciosa. Ahora estamos intensificando nuestro juego. Llevaremos a cabo investigaciones similares, pero a una escala mucho mayor, en numerosas máquinas para descubrir actividades irregulares dentro de toda la red en lugar de solo un dispositivo. Nuestras herramientas seguirán incluyendo registros de eventos de Windows, pero el alcance de nuestro trabajo se ampliará significativamente, exigiendo un escrutinio cuidadoso de un conjunto más grande de información e identificando y descartando falsos positivos siempre que sea posible.

En este módulo, nos centraremos en máquinas maliciosas específicas. Dominaremos el arte de elaborar consultas precisas y activar alertas para mejorar de forma proactiva la seguridad de nuestro entorno.

La estrategia que seguiremos para identificar eventos reflejará nuestras lecciones iniciales. Sin embargo, nos enfrentamos a un conjunto de datos mucho más grande en lugar de simplemente una colección de registros de eventos. Y desde nuestro punto de vista en el panel de Splunk, intentaremos eliminar los falsos positivos.

---

# **Ingesta de fuentes de datos**

Al comenzar a crear búsquedas, alertas o consultas, el gran volumen de información y datos puede resultar abrumador. Parte del arte de ser un profesional de la ciberseguridad implica identificar los datos más significativos, determinar cómo examinarlos de manera rápida y eficiente y garantizar la solidez de nuestro análisis.

Para continuar, necesitamos acceso a datos que podamos analizar y utilizar para la búsqueda de amenazas. Hay algunas fuentes a las que podemos recurrir. Una fuente que proporciona Splunk, junto con las instrucciones de instalación, es [BOTES](https://github.com/splunk/botsv3). Alternativamente,[https://www.logs.to](https://www.logs.to/) es una utilidad útil que nos proporciona registros ficticios en formato JSON. Si subes datos de `logs.to`, asegúrese de que su tipo de fuente extraiga correctamente el JSON ajustando el `Indexed Extractions`configuración en JSON al crear un nuevo tipo de fuente antes de cargar los datos JSON.

Sin embargo, nuestro enfoque en este módulo estará en un conjunto de datos que hemos creado personalmente. Este conjunto de datos le ayudará en su progreso. Trabajará con más de 500.000 eventos. Al configurar el selector de tiempo en `All time` y al enviar la consulta a continuación, podemos recuperar todos los eventos accesibles.

```
index="main" earliest=0

```

![](https://academy.hackthebox.com/storage/modules/218/1.png)

Ahora tenemos un conjunto de datos gigantesco para examinar y analizar varios tipos de fuentes con múltiples infecciones. Dentro de estos datos nos encontraremos con diferentes tipos de ataques e infecciones. Nuestro objetivo aquí no es identificar cada uno de ellos, sino comprender cómo podemos comenzar a detectar cualquier tipo de ataque dentro de este vasto conjunto de datos. Al final de esta lección, habremos identificado varios ataques y lo alentamos a profundizar en los datos y descubrir más por su cuenta.

---

# **Búsqueda efectiva**

Si es nuevo en Splunk, puede notar que ciertas consultas toman un tiempo considerable para procesar y devolver datos, particularmente cuando se trata de conjuntos de datos más grandes y realistas. La búsqueda eficaz de amenazas en cualquier SIEM depende de elaborar las consultas correctas y apuntar a datos relevantes.

Hemos mencionado la importancia de los datos relevantes o precisos varias veces. ¿Qué significa esto realmente? Los datos de estos eventos contienen una combinación de señales valiosas que pueden ayudarnos a rastrear ataques y ruidos extraños que debemos filtrar. Puede ser desalentador pensar que amenazas potenciales puedan estar escondidas en el ruido de fondo y, si bien esto es una posibilidad, nuestro trabajo como equipo azul es rastrear metódicamente tácticas, técnicas y procedimientos (TTP) y elaborar alertas y buscar consultas para cubrir tantos vectores de amenazas potenciales como podamos. Esto no es una carrera de velocidad; es más bien un maratón, un proceso que a menudo abarca toda la vida de una organización. Comenzamos apuntando a lo que sabemos que es malicioso a partir de datos familiares.

Profundicemos en nuestros datos. Nuestro primer objetivo es ver qué podemos identificar dentro de los datos de Sysmon. Comenzaremos enumerando todos nuestros tipos de fuentes para abordar esto como un entorno desconocido desde cero. Ejecute la siguiente consulta para observar los posibles tipos de fuente (la captura de pantalla puede contener un tipo de fuente WinEventLog que no tendrá).

```
index="main" | stats count by sourcetype

```

![](https://academy.hackthebox.com/storage/modules/218/2.png)

Esto enumerará todos los tipos de fuente disponibles en su entorno Splunk. Ahora consultemos nuestro tipo de fuente Sysmon y echemos un vistazo a los datos entrantes.

```
index="main" sourcetype="WinEventLog:Sysmon"

```

![](https://academy.hackthebox.com/storage/modules/218/3.png)

Podemos profundizar en los eventos pulsando en la flecha de la izquierda.

![](https://academy.hackthebox.com/storage/modules/218/4.png)

Aquí podemos verificar que efectivamente se trata de datos de Sysmon e identificar aún más los campos extraídos a los que podemos apuntar para la búsqueda. Los campos extraídos nos ayudan a realizar búsquedas más eficientes. Aquí está el razonamiento.

Hay varias formas en que podemos realizar búsquedas para lograr nuestro objetivo, pero algunos métodos serán más eficientes que otros. Podemos consultar todos los campos, lo que esencialmente realiza búsquedas de expresiones regulares para nuestros datos suponiendo que no sabemos en qué campo existe. A modo de demostración, ejecutemos algunas consultas generalizadas para ilustrar las diferencias de rendimiento. Busquemos todos los casos posibles de`uniwaldo.local`.

```
index="main" uniwaldo.local

```

![](https://academy.hackthebox.com/storage/modules/218/5.png)

Esto debería arrojar resultados con bastante rapidez. Mostrará cualquier instancia de esta cadena específica que se encuentre en `any` y `all` sourcetypes la forma en que lo ejecutamos. Puede no distinguir entre mayúsculas y minúsculas y aún así arrojar los mismos resultados. Ahora intentemos encontrar todas las instancias de esta cadena concatenadas dentro de cualquier otra cadena como "myuniwaldo.localtest" usando un comodín antes y después.

```
index="main" *uniwaldo.local*

```

![](https://academy.hackthebox.com/storage/modules/218/6.png)

Observarás que esta consulta devuelve resultados. `much` más lentamente que antes, ¡aunque el número de resultados es exactamente el mismo! Ahora apuntemos a esta cadena dentro del `ComputerName` solo campo, ya que es posible que solo nos importe esta cadena si aparece en `ComputerName`. porque no `ComputerName` `only` contiene esta cadena, debemos anteponer un comodín para devolver resultados relevantes.

```
index="main" ComputerName="*uniwaldo.local"

```

![](https://academy.hackthebox.com/storage/modules/218/7.png)

Verás que esta consulta devuelve resultados. `much` más rápidamente que nuestra búsqueda anterior. El punto que se destaca aquí es que las búsquedas dirigidas en su SIEM se ejecutarán y arrojarán resultados mucho más rápidamente. También reducen el consumo de recursos y permiten a sus colegas utilizar SIEM con menos interrupciones e impacto. A medida que diseñamos nuestras consultas para buscar anomalías, es crucial que sigamos elaborando consultas eficientes al frente de nuestro pensamiento, especialmente si pretendemos convertir esta consulta en una alerta más adelante. Tener muchas alertas de ejecución lenta no beneficiará a nuestros sistemas ni a nosotros. Si puede dirigir la búsqueda a usuarios, redes, máquinas, etc. específicos, siempre será una ventaja hacerlo, ya que también reduce una gran cantidad de datos irrelevantes, lo que le permite centrarse en lo que realmente importa. Pero nuevamente, ¿cómo sabemos lo que`need`¿en qué centrarse?

---

# **Adoptar la mentalidad de analistas, cazadores de amenazas e ingenieros de detección**

Para avanzar en nuestro viaje, centrémonos en detectar anomalías en nuestros datos. Recuerde la base que establecimos en el`Windows Event Logs & Finding Evil`módulo, donde exploramos el potencial de los códigos de eventos para rastrear actividades peculiares? Utilizamos recursos públicos como la guía Microsoft Sysinternals para [sismon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon). Apliquemos el mismo enfoque e identifiquemos todos los Sysmon EventCodes que prevalecen en nuestros datos con esta consulta.

```
index="main" sourcetype="WinEventLog:Sysmon" | stats count by EventCode

```

![](https://academy.hackthebox.com/storage/modules/218/8.png)

Nuestro escaneo descubre 20 EventCodes distintos. Antes de continuar, recordemos algunas de las descripciones de eventos de Sysmon y su uso potencial para detectar actividad maliciosa.

- [ID de evento 1 de Sysmon: creación de procesos](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90001): Útil para búsquedas dirigidas a jerarquías anormales de procesos padre-hijo, como se ilustra en la primera lección con Process Hacker. Es un evento que podemos usar más tarde.
- [Sysmon ID de evento 2: un proceso cambió la hora de creación de un archivo](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90002): Útil para detectar ataques de "paso de tiempo", donde los atacantes alteran los tiempos de creación de archivos. Tenga en cuenta que no todas estas acciones indican intenciones maliciosas.
- [ID de evento 3 de Sysmon: conexión de red](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90003): Una fuente de ruido abundante ya que las máquinas están constantemente estableciendo conexiones de red. Es posible que descubramos anomalías, pero consideremos primero otras áreas más tranquilas.
- [Sysmon ID de evento 4: el estado del servicio Sysmon cambió](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90004): Podría ser una búsqueda útil si los atacantes intentan detener Sysmon, aunque la mayoría de estos eventos probablemente sean benignos e informativos, considerando los frecuentes inicios y paradas legítimas de Sysmon.
- [ID de evento de Sysmon 5: proceso finalizado](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90005): Esto podría ayudarnos a detectar cuándo los atacantes eliminan procesos clave o utilizan procesos de sacrificio. Por ejemplo, Cobalt Strike a menudo genera procesos temporales como werfault, cuya terminación se registraría aquí, así como la creación en ID 1.
- [ID de evento de Sysmon 6: controlador cargado](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90006): Una señal potencial para ataques BYOD (traiga su propio controlador), aunque esto es menos común. Antes de profundizar en esto, eliminemos primero las amenazas más llamativas.
- [ID de evento de Sysmon 7: imagen cargada](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90007): Nos permite rastrear cargas de DLL, lo cual es útil para detectar secuestros de DLL.
- [ID de evento de Sysmon 8: CreateRemoteThread](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90008): Potencialmente ayuda a identificar hilos inyectados. Si bien los subprocesos remotos se pueden crear legítimamente, si un atacante hace un mal uso de esta API, podemos rastrear su proceso no autorizado y en qué inyectaron.
- [ID de evento de Sysmon 10: acceso a procesos](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90010): Útil para detectar inyección remota de código y volcado de memoria, ya que registra cuándo se realizan controles de procesos.
- [ID de evento de Sysmon 11 - FileCreate](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90011): Dado que muchos archivos se crean con frecuencia debido a actualizaciones, descargas, etc., puede resultar complicado apuntar nuestra búsqueda directamente aquí. Sin embargo, estos eventos pueden resultar beneficiosos para correlacionar o identificar los orígenes de un archivo más adelante.
- [ID de evento de Sysmon 12: RegistryEvent (creación y eliminación de objetos)](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90012) & [ID de evento de Sysmon 13: RegistryEvent (conjunto de valores)](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90013): Si bien aquí tienen lugar numerosos eventos, muchos eventos de registro pueden ser maliciosos y, con una buena idea de qué buscar, buscar aquí puede resultar fructífero.
- [ID de evento de Sysmon 15: FileCreateStreamHash](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90015): Se relaciona con secuencias de archivos y la "Marca de la Web" relacionada con descargas externas, pero dejaremos esto de lado por ahora.
- [ID de evento de Sysmon 16: el estado de configuración de Sysmon cambió](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90016): Registra modificaciones en la configuración de Sysmon, útil para detectar manipulaciones.
- [ID de evento Sysmon 17: tubería creada](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90017) & [ID de evento de Sysmon 18: tubería conectada](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90018): Registre creaciones y conexiones de tuberías. Pueden ayudar a observar los intentos de comunicación entre procesos del malware, el uso de[PsExec](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec)y movimiento lateral SMB.
- [ID de evento de Sysmon 22: DNSEvent](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90022): Realiza un seguimiento de las consultas de DNS, lo que puede resultar beneficioso para supervisar las resoluciones de balizas y las balizas DNS.
- [ID de evento de Sysmon 23: eliminación de archivos](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90023): Supervisa la eliminación de archivos, lo que puede proporcionar información sobre si un actor de amenazas limpió su malware, eliminó archivos cruciales o posiblemente intentó un ataque de ransomware.
- [Sysmon ID de evento 25: ProcessTampering (cambio de imagen de proceso)](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon): Alertas sobre comportamientos como el herpadering de procesos, actuando como un mini filtro de alerta AV.

Basado en estos `EventCodes`, podemos realizar consultas preliminares. Como se indicó anteriormente, los árboles padre-hijo inusuales siempre resultan sospechosos. Inspeccionemos todos los árboles padre-hijo con esta consulta.

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | stats count by ParentImage, Image

```

![](https://academy.hackthebox.com/storage/modules/218/9.png)

Nos encontramos con 5.427 eventos, una gran cantidad para examinar manualmente. Tenemos opciones, eliminamos lo que parece benigno o apuntamos a procesos infantiles que se sabe que son problemáticos, como`cmd.exe`o `powershell.exe`. Apuntemos a estos dos.

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 (Image="*cmd.exe" OR Image="*powershell.exe") | stats count by ParentImage, Image

```

![](https://academy.hackthebox.com/storage/modules/218/10.png)

El `notepad.exe` a `powershell.exe` La cadena se destaca inmediatamente. Implica que se ejecutó notepad.exe, lo que luego generó un PowerShell secundario para ejecutar un comando. ¿Los próximos pasos? Cuestionar el`why`y valide si esto es típico.

Podemos profundizar más centrándonos únicamente en estos eventos.

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 (Image="*cmd.exe" OR Image="*powershell.exe") ParentImage="C:\\Windows\\System32\\notepad.exe"

```

![](https://academy.hackthebox.com/storage/modules/218/11.png)

![](https://academy.hackthebox.com/storage/modules/218/12.png)

Vemos el `ParentCommandLine` (justo `notepad.exe` sin argumentos) desencadenando una `CommandLine` de `powershell.exe` aparentemente descargando un archivo de un servidor con la IP de `10.0.0.229`!

Nuestro camino ahora se bifurca. Podríamos rastrear lo que inició el `notepad.exe`, o podríamos investigar otras máquinas que interactúan con esta IP y evaluar su legitimidad. Descubramos más sobre esta IP ejecutando algunas consultas para explorar todos los tipos de fuentes que podrían arrojar algo de luz.

```
index="main" 10.0.0.229 | stats count by sourcetype

```

![](https://academy.hackthebox.com/storage/modules/218/13.png)

Entre las pocas opciones en este pequeño entorno de cinco máquinas, la mayoría simplemente nos informará que se produjo una conexión, pero no mucho más.

```
index="main" 10.0.0.229 sourcetype="linux:syslog"

```

![](https://academy.hackthebox.com/storage/modules/218/14.png)

Aquí vemos que con base en los datos y la `host` parámetro, podemos concluir que esta IP pertenece al host llamado `waldo-virtual-machine` en su `ens160` interfaz. La IP parece estar haciendo algunas cosas genéricas.

![](https://academy.hackthebox.com/storage/modules/218/15.png)

Este hallazgo indica que nuestra máquina ha iniciado algún tipo de comunicación con un sistema Linux, en particular descargando archivos ejecutables a través de `PowerShell`. ¡Esto genera algunas preocupaciones, insinuando también el posible compromiso del sistema Linux! Estamos intrigados por profundizar más. Entonces, iniciemos otra investigación utilizando datos de Sysmon para descubrir más conexiones que puedan haberse establecido.

```
index="main" 10.0.0.229 sourcetype="WinEventLog:sysmon" | stats count by CommandLine

```

![](https://academy.hackthebox.com/storage/modules/218/16.png)

¡En este momento deberían sonar las alarmas! Podemos detectar varios archivos binarios con nombres notoriamente maliciosos, que ofrecen fuertes señales de su intención hostil. Le animamos a que ejercite sus habilidades de investigación y trate de rastrear estos ataques de forma independiente, ¡tanto por práctica como por diversión!

Según nuestra evaluación, resulta cada vez más claro que no sólo el desove de `notepad.exe` a `powershell.exe` de naturaleza maliciosa, pero el sistema Linux también parece estar infectado. Parece ser fundamental para transmitir utilidades adicionales. Ahora podemos ajustar nuestra consulta de búsqueda para ampliar los hosts que ejecutan estos comandos.

```
index="main" 10.0.0.229 sourcetype="WinEventLog:sysmon" | stats count by CommandLine, host

```

![](https://academy.hackthebox.com/storage/modules/218/17.png)

Nuestro análisis indica que dos hosts fueron víctimas de este giro de Linux. En particular, parece que el script DCSync PowerShell se ejecutó en el segundo host, lo que indica una probable `DCSync` ataque. En lugar de hacer una suposición, buscaremos validación diseñando una consulta más específica, centrándonos en el ataque DCSync en este caso. Aquí está la consulta.

```
index="main" EventCode=4662 Access_Mask=0x100 Account_Name!=*$

```

![](https://academy.hackthebox.com/storage/modules/218/18.png)

Ahora, analicemos el fundamento de esta consulta. Código de evento `4662` se activa cuando se accede a un objeto de Active Directory (AD). Por lo general, está deshabilitado de forma predeterminada y el controlador de dominio debe habilitarlo deliberadamente para que comience a aparecer. `Access Mask 0x100` solicita específicamente `Control Access` normalmente necesario para los permisos de alto nivel de DCSync. El `Account_Name` comprueba dónde los usuarios acceden directamente a los objetos AD en lugar de a las cuentas, ya que DCSync solo debe realizarse legítimamente por`machine accounts`o `SYSTEM`, no usuarios. Quizás se pregunte cómo podemos determinar que se trata de intentos de DCSync, ya que podrían estar accediendo a cualquier cosa. Para solucionar esto, evaluamos en función del campo de propiedades.

![](https://academy.hackthebox.com/storage/modules/218/19.png)

Notamos dos GUID intrigantes. Una búsqueda rápida en Google puede generar información valiosa. Vamos a buscarlos.

![](https://academy.hackthebox.com/storage/modules/218/20.png)

![](https://academy.hackthebox.com/storage/modules/218/21.png)

![](https://academy.hackthebox.com/storage/modules/218/22.png)

Al investigar encontramos que el primero está vinculado a `DS-Replication-Get-Changes-All`, que, según su descripción, "...permite la replicación de datos de dominio secreto".

Esto nos da una confirmación sólida de que el usuario de Waldo realizó y ejecutó con éxito un intento de DCSync en el `UNIWALDO` dominio. Es razonable suponer que el usuario de Waldo posee `Domain Admin` derechos o tiene un cierto nivel de derechos de acceso que le permiten esta acción. Además, es muy probable que el atacante también haya extraído todas las cuentas dentro del AD. Esto significa un`full compromise`en nuestra red, y deberíamos considerar rotar nuestra `krbtgt` por si acaso un `golden ticket` fue creado.

Sin embargo, es evidente que apenas hemos arañado la superficie de las actividades del atacante. Inicialmente, el atacante debe haberse infiltrado en el sistema y haber realizado varias maniobras para obtener derechos de administrador del dominio, orquestar el movimiento lateral y deshacerse de las credenciales del dominio. Con este conocimiento, adoptaremos una estrategia de búsqueda adicional para intentar deducir cómo el atacante logró obtener inicialmente los derechos de administrador de dominio.

Somos conscientes y hemos observado previamente detecciones de dumping de lsass como técnica predominante de recolección de credenciales. Para detectar esto en nuestro entorno, nos esforzamos por identificar los procesos que abren identificadores a lsass y luego evaluamos si consideramos que este comportamiento es inusual o regular. Afortunadamente, el código de evento 10 de Sysmon puede proporcionarnos datos sobre el acceso a procesos o procesos que abren identificadores a otros procesos. Implementaremos la siguiente consulta para concentrarnos en posibles vertidos de lsass.

```
index="main" EventCode=10 lsass | stats count by SourceImage

```

![](https://academy.hackthebox.com/storage/modules/218/23.png)

Preferimos ordenar por recuento para que los datos sean más comprensibles. Si bien no siempre es seguro hacer suposiciones, generalmente se acepta que una actividad que ocurre con frecuencia es "normal" en un entorno. También es más difícil detectar actividad maliciosa en un mar de 99 eventos en comparación con detectarla en solo 1 o 5 eventos posibles. Con esta lógica, comenzaremos examinando cualquier acceso de proceso extraño y notorio a lsass.exe mediante cualquier imagen fuente. Los más notorios son`notepad`(dado su absurdo) y `rundll32` (dada su limitada frecuencia). Podemos explorarlos más a fondo como lo hacemos habitualmente.

```
index="main" EventCode=10 lsass SourceImage="C:\\Windows\\System32\\notepad.exe"

```

![](https://academy.hackthebox.com/storage/modules/218/24.png)

Estamos investigando los casos en los que el bloc de notas abre el asa. Los datos disponibles son limitados, pero está claro que Sysmon parece pensar que está relacionado con el volcado de credenciales. Podemos utilizar la pila de llamadas para obtener información adicional sobre qué desencadenó qué y desde dónde para determinar cómo se llevó a cabo este ataque.

![](https://academy.hackthebox.com/storage/modules/218/25.png)

Para el ojo inexperto, puede que no sea inmediatamente evidente que la pila de llamadas se refiere a un `UNKNOWN` segmentar en `ntdll`. En la mayoría de los casos, cualquier forma de shellcode se ubicará en lo que se denomina `unbacked` Región de la memoria. Esto implica que CUALQUIER llamada API desde este código shell no se origina en ningún archivo identificable en el disco, sino en archivos arbitrarios o `UNKNOWN`, regiones en la memoria que no se asignan al disco en absoluto. Si bien pueden ocurrir falsos positivos, los escenarios se limitan a procesos como`JIT`procesos, y en su mayoría se pueden filtrar.

---

# **Crear alertas significativas**

Armados con esta nueva fuente de información, ahora podemos intentar crear alertas de malware malicioso basadas en llamadas API de `UNKNOWN` regiones de la memoria. Es fundamental recordar que generar alertas es diferente a cazar. Nuestras alertas deben ser resilientes y efectivas, o corremos el riesgo de inundar a nuestro equipo de defensa con un exceso de datos, proporcionando sin querer una cortina de humo para que los atacantes se filtren a través de nuestros falsos positivos. Además, debemos asegurarnos de que no sean fácilmente eludidos, donde bastan unos pocos ajustes y unos segundos.

En este caso, intentaremos crear una alerta que pueda detectar actores de amenazas basándose en que realizan llamadas desde `UNKNOWN` regiones de la memoria. Queremos centrarnos en los hilos/regiones maliciosos y dejar intactos los elementos estándar para evitar la fatiga de alertas. El enfoque que adoptaremos en este laboratorio será más simplificado y fácil que muchos entornos reales debido a la menor cantidad de datos con los que debemos lidiar. Sin embargo, se aplicarán los mismos conceptos al realizar la transición a una red empresarial: solo necesitaremos administrarla frente a un volumen mucho mayor de datos de manera más efectiva y creativa.

Comenzaremos enumerando todas las pilas de llamadas que contienen `UNKNOWN` durante este período de laboratorio según el código de evento para ver cuál puede generar los datos más significativos.

```
index="main" CallTrace="*UNKNOWN*" | stats count by EventCode

```

![](https://academy.hackthebox.com/storage/modules/218/26.png)

Parece que sólo el código de evento 10 muestra algo relacionado con nuestro `CallTrace`, por lo que nuestra alerta estará vinculada al acceso al proceso. Esto significa que estaremos alertando sobre cualquier intento de abrir identificadores de otros procesos que no se asignan al disco, suponiendo que sea un código shell. Sin embargo, vemos 1575 recuentos... así que comenzaremos agrupando según `SourceImage`. El pedido se puede aplicar haciendo clic en las flechas al lado de`count`.

```
index="main" CallTrace="*UNKNOWN*" | stats count by SourceImage

```

![](https://academy.hackthebox.com/storage/modules/218/27.png)

Aquí están los falsos positivos que mencionamos, y todos son `JITs` ¡también! `.Net` es un `JIT`, y `Squirrel` los servicios públicos están vinculados a `electron`, que es un navegador Chrome y también contiene un JIT. Incluso con nuestro conjunto de datos más pequeño, hay mucho que analizar y no estamos seguros de qué es malicioso y qué no. La forma más eficaz de gestionar esto es vincular algunas consultas.

Primero, no nos preocupa cuándo un proceso accede a sí mismo (necesariamente), así que filtremos eso por ahora.

```
index="main" CallTrace="*UNKNOWN*" | where SourceImage!=TargetImage | stats count by SourceImage

```

![](https://academy.hackthebox.com/storage/modules/218/28.png)

A continuación sabemos que `C Sharp` Será difícil eliminarlo y queremos una alerta de alta fidelidad. Entonces excluiremos cualquier cosa. `C Sharp` relacionado debido a su `JIT`. Podemos lograr esto excluyendo las carpetas Microsoft.Net y todo lo que tenga `ni.dll` en su rastreo de llamadas o`clr.dll`.

```
index="main" CallTrace="*UNKNOWN*" SourceImage!="*Microsoft.NET*" CallTrace!=*ni.dll* CallTrace!=*clr.dll* | where SourceImage!=TargetImage | stats count by SourceImage

```

![](https://academy.hackthebox.com/storage/modules/218/29.png)

En la siguiente fase, nos centraremos en erradicar todo lo relacionado con `WOW64` dentro de su pila de llamadas. ¿Por qué, te preguntarás? Bueno, es bastante frecuente que`WOW64`comprende regiones de la memoria que no están respaldadas por ningún archivo específico, un fenómeno que creemos que está relacionado con la `Heaven's Gate` mecanismo, aunque todavía tenemos que profundizar en este asunto.

```
index="main" CallTrace="*UNKNOWN*" SourceImage!="*Microsoft.NET*" CallTrace!=*ni.dll* CallTrace!=*clr.dll* CallTrace!=*wow64* | where SourceImage!=TargetImage | stats count by SourceImage

```

![](https://academy.hackthebox.com/storage/modules/218/30.png)

En el futuro, también excluiremos `Explorer.exe`, considerando su carácter polivalente. Es similar a un comodín, capaz de realizar una variedad de tareas. Identificar cualquier actividad maliciosa directamente dentro de Explorer es una tarea casi hercúlea. La amplia gama de actividades legítimas que realiza y la multitud de herramientas que a menudo lo descartan debido a sus complejidades hacen que este proceso sea más desafiante. Es difícil verificar el`UNKNOWN`, especialmente en este contexto.

```
index="main" CallTrace="*UNKNOWN*" SourceImage!="*Microsoft.NET*" CallTrace!=*ni.dll* CallTrace!=*clr.dll* CallTrace!=*wow64* SourceImage!="C:\\Windows\\Explorer.EXE" | where SourceImage!=TargetImage | stats count by SourceImage

```

![](https://academy.hackthebox.com/storage/modules/218/browser.png)

Con los pasos descritos anteriormente, ahora hemos establecido un sistema de alerta razonablemente sólido para nuestro entorno. Este sistema de alerta es experto en identificar amenazas conocidas. Sin embargo, es fundamental que revisemos los datos restantes para verificar su legitimidad. Además, debemos inspeccionar el sistema para detectar cualquier actividad invisible. Para obtener una comprensión más completa, podríamos reintroducir algunos campos que eliminamos anteriormente, como`TargetImage`y `CallTrace`, o examine cada imagen fuente individualmente para eliminar cualquier falso positivo restante.

```
index="main" CallTrace="*UNKNOWN*" SourceImage!="*Microsoft.NET*" CallTrace!=*ni.dll* CallTrace!=*clr.dll* CallTrace!=*wow64* SourceImage!="C:\\Windows\\Explorer.EXE" | where SourceImage!=TargetImage | stats count by SourceImage, TargetImage, CallTrace

```

![](https://academy.hackthebox.com/storage/modules/218/31.png)

Tenga en cuenta que crear este sistema de alerta fue relativamente sencillo en nuestro entorno actual debido a los datos limitados y los falsos positivos con los que tuvimos que lidiar. Sin embargo, en un escenario del mundo real, es posible que se enfrente a una gran cantidad de datos que requieran mecanismos más matizados para identificar actividades potencialmente maliciosas. Además, vale la pena reflexionar sobre la fuerza de esta alerta. ¿Con qué facilidad podría evitarse? Desafortunadamente, existen algunas formas de superar la alerta que elaboramos.

Imagine las formas de fortalecer esta alerta. Por ejemplo, un hacker podría simplemente eludir nuestra alerta cargando cualquier DLL aleatoria con `NI` añadido a su nombre. ¿Cómo podríamos mejorar aún más nuestra alerta? ¿De qué otras formas se podría eludir esta alerta?

---

Para concluir, nos hemos equipado con habilidades para examinar grandes cantidades de datos, identificar amenazas potenciales, explorar nuestra Gestión de eventos e información de seguridad (SIEM) en busca de fuentes de datos valiosas, rastrear ataques desde sus fuentes potenciales y crear alertas potentes para mantener una estrecha vigilancia sobre las amenazas emergentes. Si bien las técnicas que analizamos fueron relativamente simplificadas debido a nuestro conjunto de datos más pequeño de alrededor de 500.000 eventos, los escenarios del mundo real pueden implicar conjuntos de datos mucho más grandes o más pequeños, lo que requiere técnicas más rigurosas para identificar actividades maliciosas.

A medida que avanza en su recorrido por la ciberseguridad, recuerde la importancia de mantener estrategias de búsqueda efectivas, ser innovador al analizar conjuntos de datos masivos, aprovechar herramientas de inteligencia de código abierto como Google para identificar amenazas y crear alertas sólidas que no se puedan eludir fácilmente mediante la incorporación de cadenas arbitrarias. en tus guiones.