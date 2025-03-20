# Detection & Analysis Stage (Part 1)

En este punto, hemos creado procesos y procedimientos, y tenemos pautas sobre cómo actuar ante incidentes de seguridad.

El `detection & analysis` La fase involucra todos los aspectos de la detección de un incidente, como la utilización de sensores, registros y personal capacitado. También incluye el intercambio de información y conocimientos, así como el uso de inteligencia sobre amenazas basada en el contexto. La segmentación de la arquitectura y tener una comprensión clara y visibilidad dentro de la red también son factores importantes.

Las amenazas se introducen en la organización a través de una cantidad infinita de vectores de ataque y su detección puede provenir de fuentes como:

- Un empleado que nota un comportamiento anormal.
- Una alerta de alguna de nuestras herramientas (EDR, IDS, Firewall, SIEM, etc.)
- Actividades de caza de amenazas
- Una notificación de terceros que nos informa que descubrieron signos de que nuestra organización estaba comprometida.

---

Es muy recomendable crear niveles de detección categorizando lógicamente nuestra red de la siguiente manera.

- Detección en el perímetro de la red (mediante cortafuegos, sistemas de detección/prevención de intrusiones en la red con acceso a Internet, zona desmilitarizada, etc.)
- Detección a nivel de red interna (mediante firewalls locales, sistemas de detección/prevención de intrusiones en el host, etc.)
- Detección a nivel de endpoint (usando sistemas antivirus, sistemas de respuesta y detección de endpoints, etc.)
- Detección a nivel de aplicación (mediante registros de aplicaciones, registros de servicios, etc.)

---

# **Investigación inicial**

Cuando se detecta un incidente de seguridad, se debe realizar una investigación inicial y establecer el contexto antes de reunir al equipo y solicitar una respuesta al incidente en toda la organización. Piense en cómo se presenta la información en caso de que una cuenta administrativa se conecte a una dirección IP en HH:MM:SS. Sin saber qué sistema está en esa dirección IP y a qué zona horaria se refiere la hora, fácilmente podemos llegar a una conclusión errónea sobre de qué se trata este evento. En resumen, deberíamos intentar recopilar la mayor cantidad de información posible en esta etapa sobre lo siguiente:

- Fecha/Hora en que se informó el incidente. Además, ¿quién detectó el incidente y/o quién lo reportó?
- ¿Cómo se detectó el incidente?
- ¿Cuál fue el incidente? ¿Suplantación de identidad? ¿Indisponibilidad del sistema? etc.
- Reúna una lista de sistemas afectados (si corresponde)
- Documente quién ha accedido a los sistemas afectados y qué acciones se han tomado. Tome nota de si se trata de un incidente en curso o si la actividad sospechosa se ha detenido
- Ubicación física, sistemas operativos, direcciones IP y nombres de host, propietario del sistema, propósito del sistema, estado actual del sistema
- (Si hay malware involucrado) Lista de direcciones IP, hora y fecha de detección, tipo de malware, sistemas afectados, exportación de archivos maliciosos con información forense (como hashes, copias de los archivos, etc.)

Con esa información a mano, podemos tomar decisiones basadas en el conocimiento que hemos recopilado. ¿Qué quiere decir esto? Probablemente tomaríamos medidas diferentes si supiéramos que la computadora portátil del director ejecutivo está comprometida y no la de un pasante.

Con la información recopilada inicialmente, podemos comenzar a construir un cronograma del incidente. Esta línea de tiempo nos mantendrá organizados durante todo el evento y brindará una imagen general de lo sucedido. Los eventos en la línea de tiempo están ordenados según el momento en que ocurrieron. Tenga en cuenta que durante el proceso de investigación posterior, no necesariamente descubriremos pruebas en este orden temporal. Sin embargo, cuando clasificamos la evidencia según cuándo ocurrió, obtendremos el contexto de los eventos separados que tuvieron lugar. La línea de tiempo también puede arrojar algo de luz sobre si la evidencia recién descubierta es parte del incidente actual. Por ejemplo, imaginemos que hace dos semanas se descubre que lo que pensábamos que era la carga útil inicial de un ataque estaba presente en otro dispositivo. Nos encontraremos con situaciones en las que los datos que estamos viendo son extremadamente relevantes y situaciones en las que los datos no están relacionados y estamos buscando en el lugar equivocado. En general, el cronograma debe contener la información descrita en las siguientes columnas:

| **`Date`** | **`Time of the event`** | **`hostname`** | **`event description`** | **`data source`** |
| --- | --- | --- | --- | --- |

Tomemos un evento y completemos la tabla de ejemplo anterior. Se verá de la siguiente manera:

| **`Date`** | **`Time of the event`** | **`hostname`** | **`event description`** | **`data source`** |
| --- | --- | --- | --- | --- |
| 09/09/2021 | 13:31 CET | SQLServer01 | Se detectó la herramienta hacker 'Mimikatz' | Software antivirus |

Como puede inferir, la línea de tiempo se centra principalmente en el comportamiento del atacante, por lo que las actividades que se registran representan cuándo ocurrió el ataque, cuándo se estableció una conexión de red para acceder a un sistema, cuándo se descargaron los archivos, etc. Es importante asegurarse de capturar desde donde se detectó/descubrió la actividad y los sistemas asociados a ella.

---

# **Preguntas sobre gravedad y extensión del incidente**

Al manejar un incidente de seguridad, también debemos intentar responder las siguientes preguntas para tener una idea de la gravedad y el alcance del incidente:

- ¿Cuál es el impacto de la explotación?
- ¿Cuáles son los requisitos de explotación?
- ¿Algún sistema crítico para el negocio puede verse afectado por el incidente?
- ¿Hay alguna medida de remediación sugerida?
- ¿Cuántos sistemas se han visto afectados?
- ¿Se está utilizando el exploit en la naturaleza?
- ¿Tiene el exploit alguna capacidad similar a la de un gusano?

Los dos últimos posiblemente puedan indicar el nivel de sofisticación de un adversario.

Como puede imaginar, los incidentes de alto impacto se manejarán con prontitud y los incidentes con una gran cantidad de sistemas afectados deberán escalarse.

---

# **Confidencialidad y comunicación del incidente**

Los incidentes son temas muy confidenciales y, como tales, toda la información recopilada debe conservarse según sea necesario, a menos que las leyes aplicables o una decisión administrativa nos indiquen lo contrario. Hay múltiples razones para esto. El adversario puede ser, por ejemplo, un empleado de la empresa, o si se ha producido un incumplimiento, la comunicación a partes internas y externas debe ser manejada por la persona designada de acuerdo con el departamento jurídico.

Cuando se inicia una investigación, fijaremos algunas expectativas y objetivos. Estos suelen incluir el tipo de incidente que ocurrió, las fuentes de evidencia que tenemos disponibles y una estimación aproximada de cuánto tiempo necesita el equipo para la investigación. Además, en función del incidente, estableceremos expectativas sobre si podremos descubrir al adversario o no. Por supuesto, mucho de lo anterior puede cambiar a medida que evolucione la investigación y se descubran nuevas pistas. Es importante mantener informados a todos los involucrados y a la gerencia sobre cualquier avance y expectativa.