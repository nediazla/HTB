# The Concept of Attacks

Para comprender efectivamente los ataques en los diferentes servicios, debemos ver cómo se pueden atacar estos servicios. Un concepto es un plan describido que se aplica a proyectos futuros. Como ejemplo, podemos pensar en el concepto de construir una casa. Muchas casas tienen un sótano, cuatro paredes y un techo. La mayoría de las casas se construyen de esta manera, y es un concepto que se aplica en todo el mundo. Los detalles más finos, como el material utilizado o el tipo de diseño, son flexibles y se pueden adaptar a deseos y circunstancias individuales. Este ejemplo muestra que un concepto necesita una categorización general (piso, paredes, techo).

En nuestro caso, necesitamos crear un concepto para los ataques a todos los servicios posibles y dividirlo en categorías que resuman todos los servicios pero dejan los métodos de ataque individual.

Para explicar un poco más claramente de qué estamos hablando aquí, podemos tratar de agrupar los servicios SSH, FTP, SMB y HTTP nosotros mismos y descubrir qué tienen en común estos servicios. Luego necesitamos crear una estructura que nos permita identificar los puntos de ataque de estos diferentes servicios utilizando un solo patrón.

Analizar los puntos en común y la creación de plantillas de patrones que se ajusten a todos los casos concebibles no es un producto terminado, sino un proceso que hace que estas plantillas de patrones sean cada vez más grandes. Por lo tanto, hemos creado una plantilla de patrón para este tema para que usted enseñe y explique mejor y de manera más eficiente el concepto detrás de los ataques.

### **El concepto de ataques**

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

El concepto se basa en cuatro categorías que ocurren para cada vulnerabilidad. Primero, tenemos un`Source`que realiza la solicitud específica a un`Process`donde se desencadena la vulnerabilidad. Cada proceso tiene un conjunto específico de`Privileges`con el que se ejecuta. Cada proceso tiene una tarea con un objetivo específico o`Destination`para calcular los nuevos datos o reenviarlos. Sin embargo, las especificaciones individuales y únicas en estas categorías pueden diferir del servicio al servicio.

Cada tarea y información sigue un patrón específico, un ciclo, que hemos hecho deliberadamente lineal. Esto es porque el`Destination`no siempre sirve como`Source`y, por lo tanto, no se trata como una fuente de una nueva tarea.

Para que cualquier tarea exista en absoluto, necesita una idea, información (`Source`), un proceso planificado para ello (`Processes`), y un objetivo específico (`Destination`) para ser logrado. Por lo tanto, la categoría de`Privileges`es necesario controlar el procesamiento de la información adecuadamente.

---

# **Fuente**

Podemos generalizar`Source`Como fuente de información utilizada para la tarea específica de un proceso. Hay muchas formas diferentes de pasar información a un proceso. El gráfico muestra algunos de los ejemplos más comunes de cómo se pasa la información a los procesos.

| **Fuente de información** | **Descripción** |
| --- | --- |
| `Code` | Esto significa que los resultados del código de programa ya ejecutados se utilizan como fuente de información. Estos pueden provenir de diferentes funciones de un programa. |
| `Libraries` | Una biblioteca es una colección de recursos del programa, que incluyen datos de configuración, documentación, datos de ayuda, plantillas de mensajes, código previo y subrutinas, clases, valores o especificaciones de tipo. |
| `Config` | Las configuraciones suelen ser valores estáticos o prescritos que determinan cómo el proceso procesa la información. |
| `APIs` | La interfaz de programación de aplicaciones (API) se utiliza principalmente como la interfaz de los programas para recuperar o proporcionar información. |
| `User Input` | Si un programa tiene una función que permite al usuario ingresar valores específicos utilizados para procesar la información en consecuencia, esta es la entrada manual de información por parte de una persona. |

La fuente es, por lo tanto, la fuente que se explota para las vulnerabilidades. No importa qué protocolo se use porque las inyecciones de encabezado HTTP se pueden manipular manualmente, al igual que los desbordamientos del tampón. Por lo tanto, la fuente de esto se puede clasificar como`Code`. Así que echemos un vistazo más de cerca a la plantilla de patrón basada en una de las últimas vulnerabilidades críticas de las que la mayoría de nosotros hemos oído hablar.

### **Log4j**

Un gran ejemplo es la vulnerabilidad crítica de log4j ([CVE-2021-44228](https://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2021-44228)) que se publicó a fines de 2021. LOG4J es un marco o`Library`Se utiliza para registrar mensajes de aplicación en Java y otros lenguajes de programación. Esta biblioteca contiene clases y funciones que pueden integrar otros lenguajes de programación. Para este propósito, la información está documentada, similar a un libro de registro. Además, el alcance de la documentación se puede configurar ampliamente. Como resultado, se ha convertido en un estándar en muchos productos de software de código abierto y comerciales. En este ejemplo, un atacante puede manipular el encabezado de agente de usuario HTTP e insertar una búsqueda JNDI como un comando destinado al log4j`library`. En consecuencia, no se procesa el encabezado real de agente de usuario, como Mozilla 5.0, sino la búsqueda JNDI.

---

# **Procesos**

El`Process`se trata de procesar la información reenviada desde la fuente. Estos se procesan de acuerdo con la tarea prevista determinada por el código del programa. Para cada tarea, el desarrollador especifica cómo se procesa la información. Esto puede ocurrir usando clases con diferentes funciones, cálculos y bucles. La variedad de posibilidades para esto es tan diversa como el número de desarrolladores en el mundo. En consecuencia, la mayoría de las vulnerabilidades se encuentran en el código del programa ejecutado por el proceso.

| **Componentes de procesos** | **Descripción** |
| --- | --- |
| `PID` | El ID de proceso (PID) identifica el proceso que se inicia o ya se está ejecutando. Los procesos de ejecución ya han asignado privilegios, y los nuevos se inician en consecuencia. |
| `Input` | Esto se refiere a la entrada de información que podría ser asignada por un usuario o como resultado de una función programada. |
| `Data processing` | Las funciones codificadas de un programa dictan cómo se procesa la información recibida. |
| `Variables` | Las variables se utilizan como marcadores de posición para información que diferentes funciones pueden procesar más durante la tarea. |
| `Logging` | Durante el registro, ciertos eventos se documentan y, en la mayoría de los casos, se almacenan en un registro o un archivo. Esto significa que cierta información permanece en el sistema. |

### **Log4j**

El proceso de log4j es registrar el agente de usuario como una cadena utilizando una función y almacenarla en la ubicación designada. La vulnerabilidad en este proceso es la mala interpretación de la cadena, que conduce a la ejecución de una solicitud en lugar de registrar los eventos. Sin embargo, antes de ir más allá en esta función, necesitamos hablar sobre privilegios.

---

# **Privilegios**

`Privileges`están presentes en cualquier sistema que controle los procesos. Estos sirven como un tipo de permiso que determina qué tareas y acciones se pueden realizar en el sistema. En términos simples, se puede comparar con un boleto de autobús. Si usamos un boleto destinado a una región en particular, podremos usar el autobús y, de lo contrario, no lo haremos. Estos privilegios (o en sentido figurado, nuestros boletos) también pueden usarse para diferentes medios de transporte, como aviones, trenes, barcos y otros. En los sistemas informáticos, estos privilegios sirven como control y segmentación de acciones para las cuales se necesitan diferentes permisos, controlados por el sistema. Por lo tanto, los derechos se verifican en función de esta categorización cuando un proceso necesita cumplir con su tarea. Si el proceso satisface estos privilegios y condiciones, el sistema aprueba la acción solicitada. Podemos dividir estos privilegios en las siguientes áreas:

| **Privilegios** | **Descripción** |
| --- | --- |
| `System` | Estos privilegios son los privilegios más altos que se pueden obtener, que permiten cualquier modificación del sistema. En Windows, se llama a este tipo de privilegio`SYSTEM`, y en Linux, se llama`root`. |
| `User` | Los privilegios del usuario son permisos que se han asignado a un usuario específico. Por razones de seguridad, los usuarios separados a menudo se configuran para servicios particulares durante la instalación de distribuciones de Linux. |
| `Groups` | Los grupos son una categorización de al menos un usuario que tiene ciertos permisos para realizar acciones específicas. |
| `Policies` | Las políticas determinan la ejecución de comandos específicos de la aplicación, que también pueden aplicarse a usuarios individuales o agrupados y sus acciones. |
| `Rules` | Las reglas son los permisos para realizar acciones manejadas desde las propias aplicaciones. |

### **Log4j**

Lo que hizo que la vulnerabilidad log4j fuera tan peligrosa fue la`Privileges`que trajo la implementación. Los registros a menudo se consideran sensibles porque pueden contener datos sobre el servicio, el sistema en sí o incluso los clientes. Por lo tanto, los registros generalmente se almacenan en ubicaciones a las que ningún usuario regular debe poder acceder. En consecuencia, la mayoría de las aplicaciones con la implementación LOG4J se ejecutaron con los privilegios de un administrador. El proceso en sí explotó la biblioteca manipulando el agente de usuario para que el proceso malinterpretara la fuente y condujera a la ejecución del código suministrado por el usuario.

---

# **Destino**

Cada tarea tiene al menos un propósito y objetivo que debe cumplirse. Lógicamente, si faltaban o no se almacenaban o no se almacenaban cambios de datos, la tarea sería generalmente innecesaria. El resultado de dicha tarea se almacena en algún lugar o se envía a otro punto de procesamiento. Por lo tanto, hablamos aquí del`Destination`donde se realizarán los cambios. Dichos puntos de procesamiento pueden apuntar a un proceso local o remoto. Por lo tanto, a nivel local, los archivos o registros locales pueden ser modificados por el proceso o reenviarse a otros servicios locales para su uso posterior. Sin embargo, esto no excluye la posibilidad de que el mismo proceso también pueda reutilizar los datos resultantes. Si el proceso se completa con el almacenamiento de datos o su reenvío, el ciclo que conduce a la finalización de la tarea está cerrado.

| **Destino** | **Descripción** |
| --- | --- |
| `Local` | El área local es el entorno del sistema en el que ocurrió el proceso. Por lo tanto, los resultados y los resultados de una tarea se procesan aún más mediante un proceso que incluye cambios en los conjuntos de datos o el almacenamiento de los datos. |
| `Network` | El área de red es principalmente una cuestión de reenviar los resultados de un proceso a una interfaz remota. Esta puede ser una dirección IP y sus servicios o incluso redes completas. Los resultados de tales procesos también pueden influir en la ruta bajo ciertas circunstancias. |

### **Log4j**

La mala interpretación del agente de usuario conduce a una búsqueda JNDI que se ejecuta como un comando del sistema con privilegios de administrador y consulta un servidor remoto controlado por el atacante, que en nuestro caso es el`Destination`En nuestro concepto de ataques. Esta consulta solicita una clase de Java creada por el atacante y se manipula para sus propios fines. El código Java consultado dentro de la clase Java manipulada se ejecuta en el mismo proceso, lo que lleva a una ejecución de código remoto (`RCE`) vulnerabilidad.

Govcert.ch ha creado una excelente representación gráfica de la vulnerabilidad log4j que vale la pena examinar en detalle.

![](https://academy.hackthebox.com/storage/modules/116/log4jattack.png)

Fuente: https://www.govcert.ch/blog/zero-day-exploit-targeting-popular-java-library-log4j/

Este gráfico desglosa el ataque log4j jndi basado en el`Concept of Attacks`.

### **Iniciación del ataque**

| **Paso** | **Log4j** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `1.` | El atacante manipula al agente de usuarios con un comando de búsqueda JNDI. | `Source` |
| `2.` | El proceso malinterpreta el agente de usuario asignado, lo que lleva a la ejecución del comando. | `Process` |
| `3.` | El comando de búsqueda JNDI se ejecuta con privilegios de administrador debido a los permisos de registro. | `Privileges` |
| `4.` | Este comando de búsqueda JNDI apunta al servidor creado y preparado por el atacante, que contiene una clase de Java maliciosa que contiene comandos diseñados por el atacante. | `Destination` |

Esto es cuando el ciclo comienza nuevamente, pero esta vez para obtener acceso remoto al sistema de destino.

### **Activar la ejecución del código remoto**

| **Paso** | **Log4j** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `5.` | Después de que se recupere la clase Java maliciosa del servidor del atacante, se utiliza como fuente para nuevas acciones en el siguiente proceso. | `Source` |
| `6.` | A continuación, se lee el código malicioso de la clase Java, que en muchos casos ha llevado al acceso remoto al sistema. | `Process` |
| `7.` | El código malicioso se ejecuta con privilegios de administrador debido a los permisos de registro. | `Privileges` |
| `8.` | El código lleva a la red al atacante con las funciones que permiten al atacante controlar el sistema de forma remota. | `Destination` |

Finalmente, vemos un patrón que podemos usar repetidamente para nuestros ataques. Esta plantilla de patrón se puede utilizar para analizar y comprender las hazañas y depurar nuestras propias hazañas durante el desarrollo y las pruebas. Además, esta plantilla de patrón también se puede aplicar al análisis del código fuente, lo que nos permite verificar ciertas funciones y comandos en nuestro código paso a paso. Finalmente, también podemos pensar categórica