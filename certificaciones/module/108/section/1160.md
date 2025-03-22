# Evaluaciones de seguridad

Cada organización debe realizar diferentes tipos de`Security assessments`en su`networks`, `computers`, y`applications`al menos de vez en cuando. El propósito principal de la mayoría de los tipos de evaluaciones de seguridad es encontrar y confirmar que las vulnerabilidades están presentes, por lo que podemos trabajar para`patch`, `mitigate`, o`remove`a ellos. Hay diferentes formas y metodologías para probar cuán seguro es un sistema informático. Algunos tipos de evaluaciones de seguridad son más apropiadas para ciertas redes que otras. Pero todos tienen un propósito para mejorar la ciberseguridad. Todas las organizaciones tienen diferentes requisitos de cumplimiento y tolerancia al riesgo, enfrentan diferentes amenazas y tienen diferentes modelos comerciales que determinan los tipos de sistemas que ejecutan externamente e internamente. Algunas organizaciones tienen una postura de seguridad mucho más madura que sus pares y pueden centrarse en simulaciones avanzadas del equipo rojo realizadas por terceros, mientras que otros aún están trabajando para establecer la seguridad de referencia. De todos modos, todas las organizaciones deben estar al tanto de las vulnerabilidades heredadas y recientes y tener un sistema para detectar y mitigar los riesgos a sus sistemas y datos.

---

# **Evaluación de vulnerabilidad**

`Vulnerability assessments`son apropiados para todas las organizaciones y redes. Una evaluación de vulnerabilidad se basa en un estándar de seguridad particular, y se analiza el cumplimiento de estos estándares (por ejemplo, pasando por una lista de verificación).

Una evaluación de vulnerabilidad puede basarse en diversos estándares de seguridad. Qué estándares se aplican a una red en particular dependerán de muchos factores. Estos factores pueden incluir regulaciones de seguridad de datos específicas y regionales de la industria, el tamaño y la forma de la red de una empresa, que tipos de aplicaciones que utilizan o desarrollan, y su nivel de madurez de seguridad.

Las evaluaciones de vulnerabilidad se pueden realizar de forma independiente o junto con otras evaluaciones de seguridad dependiendo de la situación de una organización.

---

# **Prueba de penetración**

Aquí a`Hack The Box`, amamos las pruebas de penetración, también conocidas como Pentests. Nuestros laboratorios y muchos de nuestros otros cursos de la Academia se centran en Pentesting.

Se llaman pruebas de penetración porque los evaluadores las realizan para determinar si pueden penetrar una red y cómo. Un Pentest es un tipo de ataque cibernético simulado, y los pentesteros realizan acciones que un actor de amenaza puede realizar para ver si son posibles ciertos tipos de exploits. La diferencia clave entre un pentest y un ataque cibernético real es que el primero se realiza con el consentimiento legal total de la entidad que se está acumulando. Ya sea que un Pentester sea un empleado o un contratista de un tercero, deberá firmar un documento legal largo con la compañía objetivo que describe lo que puede hacer y lo que no se les permite hacer.

Al igual que con una evaluación de vulnerabilidad, un Pentest efectivo dará como resultado un informe detallado lleno de información que puede usarse para mejorar la seguridad de una red. Todo tipo de pentests se pueden realizar de acuerdo con las necesidades específicas de una organización.

`Black box`Pentesting se realiza sin conocimiento de la configuración o aplicaciones de una red. Por lo general, a un probador se le dará acceso a la red (o un puerto Ethernet y tendrá que evitar el control de acceso a la red NAC) y nada más (que requiere que realicen su propio descubrimiento para las direcciones IP) si el Pentest es interno, o nada más que el nombre de la compañía si el Pentest es desde un punto de vista externo. Este tipo de pentesting generalmente es realizado por terceros desde la perspectiva de un`external`agresor. A menudo, el cliente le pedirá al Pentester que les muestre direcciones IP/rangos IP internas/externos descubiertas para que puedan confirmar la propiedad y anotar cualquier hosts que deba considerarse fuera de alcance.

`Grey box`Pentesting se realiza con un poco de conocimiento de la red que están probando, desde una perspectiva equivalente a un`employee`Quién no trabaja en el departamento de TI, como un`receptionist`o`customer service agent`. El cliente normalmente le dará a los rangos de red en alcance del probador o direcciones IP individuales en una situación de caja gris.

`White box`Pentesting generalmente se realiza al dar al probador de penetración acceso completo a todos los sistemas, configuraciones, construir documentos, etc. y código fuente si las aplicaciones web están en el alcance. El objetivo aquí es descubrir la mayor cantidad de defectos posible que serían difíciles o imposibles de descubrir ciegamente en un tiempo razonable.

A menudo, los pentesteros se especializan en un área en particular. Los probadores de penetración deben tener conocimiento de muchas tecnologías diferentes, pero aún así tendrán una especialidad.

`Application`Pentesters evalúa aplicaciones web, aplicaciones de cliente grueso, API y aplicaciones móviles. A menudo estarán bien versados en la revisión del código fuente y pueden evaluar una aplicación web determinada desde un cuadro negro o punto de vista de caja blanca (generalmente una revisión de código seguro).

`Network`o`infrastructure`Pentesters evalúa todos los aspectos de una red de computadoras, incluida su`networking devices`tales como enrutadores y firewalls, estaciones de trabajo, servidores y aplicaciones. Estos tipos de probadores de penetración generalmente deben tener una sólida comprensión de las redes, Windows, Linux, Active Directory y al menos un lenguaje de secuencias de comandos. Escáneres de vulnerabilidad de la red, como`Nessus`, se puede usar junto con otras herramientas durante la reducción de red, pero el escaneo de vulnerabilidad de la red es solo una parte de un Pentest adecuado. Es importante tener en cuenta que hay diferentes tipos de pentests (evasivos, no evasivos, evasivos híbridos). Un escáner como Nessus solo se usaría durante un pentest no evasivo cuyo objetivo es encontrar tantos defectos en la red como sea posible. Además, el escaneo de vulnerabilidad solo sería una pequeña parte de este tipo de prueba de penetración. Los escáneres de vulnerabilidad son útiles pero limitados y no pueden reemplazar el toque humano y otras herramientas y técnicas.

`Physical`Los pentestres intentan aprovechar las debilidades y desgloses de seguridad física en los procesos para obtener acceso a una instalación como un centro de datos o un edificio de oficinas.

- ¿Puedes abrir una puerta de manera involuntaria?
- ¿Puedes colocar a alguien en el centro de datos?
- ¿Puedes arrastrarte por una ventilación?

`Social engineering`Los pentesteros prueban a los seres humanos.

- ¿Pueden los empleados ser engañados por Phishing, Vishing (Phishing por teléfono) u otras estafas?
- ¿Puede un Pentester de ingeniería social caminar hasta una recepcionista y decir: "Sí, trabajo aquí?"

Pentesting es más apropiado para organizaciones con un nivel de madurez de seguridad media o alta. La madurez de seguridad mide qué tan bien desarrollado está el programa de ciberseguridad de una empresa, y el vencimiento de la seguridad tarda años en construirse. Implica la contratación de profesionales de seguridad cibernética expertos, que tiene políticas de seguridad bien diseñadas y la aplicación (como la configuración, el parche y la gestión de vulnerabilidad), los estándares de endurecimiento de línea de base para todos los tipos de dispositivos en la red, un fuerte cumplimiento regulatorio, un fuerte cumplimiento regulatorio, bien ejecutado.`incident response plans`, un experimentado`CSIRT` (`computer security incident response team`), un proceso de control de cambios establecido, un`CISO` (`chief information security officer`), a`CTO` (`chief technical officer`), pruebas de seguridad frecuentes realizadas a lo largo de los años y una fuerte cultura de seguridad. La cultura de seguridad tiene que ver con la actitud y los hábitos que los empleados tienen hacia la ciberseguridad. Parte de esto se puede enseñar a través de programas de capacitación de conciencia de seguridad y parte al desarrollar la seguridad en la cultura de la compañía. Todos, desde los secretarios hasta los sysadmins y el personal de nivel C, deben ser conscientes de la seguridad, comprender cómo evitar prácticas riesgosas y educarse en reconocer actividades sospechosas que deben informarse al personal de seguridad.

Las organizaciones con un nivel de vencimiento de seguridad más bajo pueden querer centrarse en las evaluaciones de vulnerabilidades porque un Pentest podría encontrar demasiadas vulnerabilidades para ser útiles y podría abrumar al personal encargado de remediación. Antes de considerar las pruebas de penetración, debe haber un historial de evaluaciones de vulnerabilidad y acciones tomadas en respuesta a evaluaciones de vulnerabilidad.

---

# **Evaluaciones de vulnerabilidad versus pruebas de penetración**

`Vulnerability Assessments`y las pruebas de penetración son dos evaluaciones completamente diferentes. Las evaluaciones de vulnerabilidad buscan vulnerabilidades en las redes sin simular ataques cibernéticos. Todas las empresas deben realizar evaluaciones de vulnerabilidad de vez en cuando. Se podría utilizar una amplia variedad de estándares de seguridad para una evaluación de vulnerabilidad, como el cumplimiento de GDPR o los estándares de seguridad de aplicaciones web de OWASP. Una evaluación de vulnerabilidad pasa por una lista de verificación.

- ¿Conocemos este estándar?
- ¿Tenemos esta configuración?

Durante una evaluación de vulnerabilidad, el evaluador generalmente ejecutará un escaneo de vulnerabilidad y luego realizará validación de vulnerabilidades críticas, altas y de medio riesgo. Esto significa que mostrarán evidencia de que la vulnerabilidad existe y no es un falso positivo, a menudo utilizando otras herramientas, pero no buscarán realizar una escalada de privilegios, movimiento lateral, post-explotación, etc., si validan, por ejemplo, una vulnerabilidad de ejecución de código remoto.

`Penetration tests`, dependiendo de su tipo, evalúe la seguridad de los diferentes activos y el impacto de los problemas presentes en el medio ambiente. Las pruebas de penetración pueden incluir tácticas manuales y automatizadas para evaluar la postura de seguridad de una organización. También a menudo dan una mejor idea de cuán seguros son los activos de una empresa desde una perspectiva de prueba. A`pentest`es un ataque cibernético simulado para ver si se puede penetrar la red y cómo. Independientemente del tamaño, la industria o el diseño de red de una empresa, los pentests solo deben realizarse después de que algunas evaluaciones de vulnerabilidad se hayan realizado con éxito y con soluciones. Una empresa puede realizar evaluaciones de vulnerabilidad y pentests en el mismo año. Pueden complementarse entre sí. Pero son tipos muy diferentes de pruebas de seguridad utilizadas en diferentes situaciones, y una no es "mejor" que la otra.

![](https://academy.hackthebox.com/storage/modules/108/graphics/VulnerabilityAssessment_Diagram_02.png)

*Adaptado del gráfico original encontrado[aquí](https://predatech.co.uk/wp-content/uploads/2021/01/Vulnerability-Assessment-vs-Penetration-Testing-min-2.png).*

Una organización puede beneficiarse más de un`vulnerability assessment`sobre una prueba de penetración si desean recibir una visión de los problemas comúnmente conocidos mensualmente o trimestralmente de un proveedor de terceros. Sin embargo, una organización se beneficiaría más de un`penetration test`Si están buscando un enfoque que utilice técnicas manuales y automatizadas para identificar problemas fuera de lo que un escáner de vulnerabilidad identificaría durante una evaluación de vulnerabilidad. Una prueba de penetración también podría ilustrar una cadena de ataque de la vida real que un atacante podría utilizar para acceder al entorno de una organización. Las personas que realizan pruebas de penetración tienen experiencia especializada en pruebas de redes, pruebas inalámbricas, ingeniería social, aplicaciones web y otras áreas.

Para las organizaciones que reciben evaluaciones de pruebas de penetración de forma anual o semestral, todavía es crucial que esas organizaciones evalúen regularmente su entorno con escaneos de vulnerabilidad internos para identificar nuevas vulnerabilidades a medida que se liberan al público de los proveedores.

---

# **Otros tipos de evaluaciones de seguridad**

Las evaluaciones de vulnerabilidad y las pruebas de penetración no son los únicos tipos de evaluaciones de seguridad que una organización puede realizar para proteger sus activos. También pueden ser necesarios otros tipos de evaluaciones, dependiendo del tipo de organización.

### **Auditorías de seguridad**

Las evaluaciones de vulnerabilidad se realizan porque una organización elige realizarlas, y puede controlar cómo y cuándo se evalúan. Las auditorías de seguridad son diferentes.`Security audits`son típicamente requisitos de fuera de la organización, y generalmente son obligatorios por`government agencies`o`industry associations`Asegurar que una organización cumpla con regulaciones de seguridad específicas.

Por ejemplo, todos los minoristas, restaurantes y proveedores de servicios en línea y fuera de línea que aceptan tarjetas de crédito importantes (Visa, MasterCard, AMEX, etc.) deben cumplir con el[PCI-DSS "Estándar de seguridad de datos de la industria de tarjetas de pago"](https://www.pcicomplianceguide.org/faq/#1). PCI DSS es una regulación impuesta por el[Consejo de Estándares de Seguridad de la Industria de Tarjeta de pago](https://www.pcisecuritystandards.org/), una organización dirigida por compañías de tarjetas de crédito y entidades de la industria de servicios financieros. Una empresa que acepta pagos con tarjeta de crédito y débito puede ser auditada para el cumplimiento de PCI DSS, y el incumplimiento podría dar como resultado multas y no se les permite aceptar ya esos métodos de pago.

Independientemente de qué regulaciones se pueda auditar una organización, es su responsabilidad realizar evaluaciones de vulnerabilidad para asegurar que cumplan antes de estar sujetos a una auditoría de seguridad sorpresa.

### **Bounties de errores**

`Bug bounty programs`son implementados por todo tipo de organizaciones. Invitan a los miembros del público en general, con algunas restricciones (generalmente sin escaneo automatizado), para encontrar vulnerabilidades de seguridad en sus aplicaciones. Se puede pagar a los cazarrecompensas de errores desde unos pocos cientos de dólares hasta cientos de miles de dólares por sus hallazgos, que es un pequeño precio a pagar por una empresa para evitar una vulnerabilidad de ejecución de código remoto crítico de caer en las manos equivocadas.

Las empresas más grandes con grandes bases de clientes y el alto vencimiento de la seguridad son apropiadas para los programas de recompensas de errores. Necesitan tener un equipo dedicado a triando y analizar informes de errores y estar en una situación en la que puedan soportar a los extraños que buscan vulnerabilidades en sus productos.

Empresas como Microsoft y Apple son ideales para tener programas de recompensa de errores debido a sus millones de clientes y una robusta madurez de seguridad.

### **Evaluación del equipo rojo**

Las empresas con presupuestos más grandes y más recursos pueden contratar sus propios dedicados`red teams`O utilice los servicios de empresas de consultoría de terceros para realizar evaluaciones de equipo rojo. Un equipo rojo consta de profesionales de seguridad ofensivos que tienen una experiencia considerable con las pruebas de penetración. Un equipo rojo juega un papel vital en la postura de seguridad de una organización.

Un equipo rojo es un tipo de caja negra evasiva Pentesting, que simula todo tipo de ataques cibernéticos desde la perspectiva de un actor de amenaza externa. Estas evaluaciones generalmente tienen un objetivo final (es decir, alcanzar un servidor crítico o una base de datos, etc.). Los evaluadores solo informan las vulnerabilidades que llevaron a la finalización del objetivo, no tantas vulnerabilidades como sea posible como con una prueba de penetración.

Si una empresa tiene su propio equipo rojo interno, su trabajo es realizar pruebas de penetración más específicas con un conocimiento interno de su red. Un equipo rojo debe participar constantemente en el equipo rojo`campaigns`. Las campañas podrían basarse en las nuevas hazañas cibernéticas descubiertas a través de las acciones de`advanced persistent threat groups` (`APTs`), Por ejemplo. Otras campañas podrían dirigirse a tipos específicos de vulnerabilidades para explorarlas con gran detalle una vez que una organización se ha dado cuenta de ellas.

Idealmente, si una empresa puede pagarlo y ha estado construyendo su madurez de seguridad, debe realizar evaluaciones de vulnerabilidad regulares por sí solas, contratar a terceros para realizar pruebas de penetración o evaluaciones de equipo rojo y, si corresponde, construir un equipo rojo interno para realizar una caja gris y blanca que pentera con parámetros y ámbitos más específicos.

### **Evaluación del equipo morado**

A`blue team`Consiste en especialistas en seguridad defensivo. Estas son a menudo personas que trabajan en un SOC (centro de operaciones de seguridad) o un CSIRT (equipo de respuesta a incidentes de seguridad informática). A menudo, también tienen experiencia con forense digital. Entonces, si los equipos azules son defensivos y los equipos rojos son ofensivos, el rojo mezclado con azul es morado.

`What's a purple team?`

`Purple teams`se forman cuando`offensive`y`defensive`Los especialistas en seguridad trabajan junto con un objetivo común, para mejorar la seguridad de su red. Los equipos rojos encuentran problemas de seguridad, y los equipos azules aprenden sobre esos problemas de sus equipos rojos y trabajan para solucionarlos. Una evaluación del equipo púrpura es como una evaluación del equipo rojo, pero el equipo azul también está involucrado en cada paso. El equipo azul incluso puede desempeñar un papel en el diseño de campañas. "Necesitamos mejorar nuestro cumplimiento de PCI DSS. Así que veamos al equipo rojo Pentest nuestros sistemas de punto de venta y proporcione información y retroalimentación activa durante su trabajo".

---

# **Avanzar**

Ahora que hemos pasado por los tipos de evaluación clave que una organización puede sufrir, pasemos por las evaluaciones de vulnerabilidad más profundamente para comprender mejor los términos clave y una metodología de muestra.