# Evaluación de vulnerabilidad

A`Vulnerability Assessment`Su objetivo es identificar y clasificar los riesgos de debilidades de seguridad relacionadas con los activos dentro de un entorno. Es importante tener en cuenta que`there is little to no manual exploitation during a vulnerability assessment`. Una evaluación de vulnerabilidad también proporciona pasos de remediación para solucionar los problemas.

El propósito de un`Vulnerability Assessment`es comprender, identificar y clasificar el riesgo de los problemas más aparentes presentes en un entorno sin explotarlos para obtener más acceso. Dependiendo del alcance de la evaluación, algunos clientes pueden pedirnos que validemos tantas vulnerabilidades como sea posible mediante la realización de una explotación mínimamente invasiva para confirmar los hallazgos del escáner y descartar falsos positivos. Otros clientes solicitarán un informe de todos los hallazgos identificados por el escáner. Como con cualquier evaluación, es esencial aclarar el alcance y la intención de la evaluación de vulnerabilidad antes de comenzar. La gestión de vulnerabilidad es vital para ayudar a las organizaciones a identificar los puntos débiles en sus activos, comprender el nivel de riesgo y calcular y priorizar los esfuerzos de remediación.

También es importante tener en cuenta que las organizaciones siempre deben probar parches sustanciales antes de empujarlos a su entorno para evitar interrupciones.

---

# **Metodología**

A continuación se muestra una metodología de evaluación de vulnerabilidad de muestra con la que la mayoría de las organizaciones podrían seguir y encontrar el éxito. Las metodologías pueden variar ligeramente de una organización a otra, pero este gráfico cubre los pasos principales, desde la identificación de activos hasta la creación de un plan de remediación.

![](https://academy.hackthebox.com/storage/modules/108/graphics/VulnerabilityAssessment_Diagram_06a.png)

*Adaptado del gráfico original encontrado[aquí](https://purplesec.us/wp-content/uploads/2019/07/8-steps-to-performing-a-network-vulnerability-assessment-infographic.png).*

---

# **Comprensión de los términos clave**

Antes de continuar, identifiquemos algunos términos clave que cualquier profesional de TI o Infosec debe entender y poder explicar claramente.

### **Vulnerabilidad**

A`Vulnerability`es una debilidad o error en el entorno de una organización, incluidas aplicaciones, redes e infraestructura, que abre la posibilidad de amenazas de actores externos. Las vulnerabilidades se pueden registrar a través de Miter's[Base de datos de exposición a vulnerabilidad común](https://cve.mitre.org/)y recibir un[Sistema de puntuación de vulnerabilidad común (CVSS)](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator)puntaje para determinar la gravedad. Este sistema de puntuación se usa con frecuencia como estándar para empresas y gobiernos que buscan calcular puntajes de gravedad precisos y consistentes para las vulnerabilidades de sus sistemas. Calificar vulnerabilidades de esta manera ayuda a priorizar los recursos y a determinar cómo responder a una amenaza dada. Las puntuaciones se calculan utilizando métricas como el tipo de vector de ataque (red, adyacente, local, físico), la complejidad del ataque, los privilegios requeridos, si el ataque requiere o no la interacción del usuario y el impacto de la explotación exitosa en la confidencialidad, la integridad y la disponibilidad de datos de una organización. Los puntajes pueden variar de 0 a 10, dependiendo de estas métricas.

![](https://academy.hackthebox.com/storage/modules/108/graphics/threat_vulnerability_risk.png)

Por ejemplo, la inyección de SQL se considera una vulnerabilidad ya que un atacante podría aprovechar consultas para extraer datos de la base de datos de una organización. Este ataque tendría una calificación de puntaje CVSS más alta si pudiera realizarse sin autenticación a través de Internet que un atacante necesitara acceso autenticado a la red interna y una autenticación separada a la aplicación de destino. Este tipo de cosas deben considerarse para todas las vulnerabilidades que encontramos.

### **Amenaza**

A`Threat`es un proceso que amplifica el potencial de un evento adverso, como un actor de amenaza que explota una vulnerabilidad. Algunas vulnerabilidades plantean más preocupaciones de amenazas sobre otras debido a la probabilidad de que la vulnerabilidad sea explotada. Por ejemplo, cuanto mayor sea la recompensa del resultado y la facilidad de explotación, más probable es que los actores de amenaza exploten el problema.

### **Explotar**

Un`Exploit`es cualquier código o recurso que se pueda utilizar para aprovechar la debilidad de un activo. Muchos exploits están disponibles a través de plataformas de código abierto como[Exploit-db](https://exploit-db.com/)o[la base de datos de vulnerabilidad y explotación de Rapid7](https://www.rapid7.com/db/). A menudo veremos un código de exploit alojado en sitios como GitHub y Gitlab también.

### **Riesgo**

`Risk`¿Es la posibilidad de que los actores de amenazas dañen o destruyan los activos o datos?

![](https://academy.hackthebox.com/storage/modules/108/graphics/whatisrisk.png)

Para diferenciar los tres, podemos pensar en ello de la siguiente manera:

- `Risk`: algo malo que podría pasar
- `Threat`: algo malo que esta pasando
- `Vulnerabilities`: Debilidades que podrían conducir a una amenaza

Las vulnerabilidades, las amenazas y las hazañas juegan un papel en la medición del nivel de riesgo en las debilidades al determinar la probabilidad y el impacto. Por ejemplo, las vulnerabilidades que tienen un código de hazaña confiable y es probable que se usen para obtener acceso a la red de una organización aumentarían significativamente el riesgo de un problema debido al impacto. Si un atacante tuviera acceso a la red interna, podría ver, editar o eliminar documentos confidenciales cruciales para las operaciones comerciales. Podemos usar una matriz de riesgo cualitativo para medir el riesgo en función de la probabilidad y el impacto con la tabla que se muestra a continuación.

![](https://academy.hackthebox.com/storage/modules/108/graphics/VulnerabilityAssessment_Diagram_07.png)

En este ejemplo, podemos ver que una vulnerabilidad con una baja probabilidad de ocurrir y bajo impacto sería el nivel de riesgo más bajo, mientras que una vulnerabilidad con una alta probabilidad de ser explotado y el mayor impacto en una organización representaría el mayor riesgo y querría ser priorizado para la remediación.

---

# **Gestión de activos**

Cuando una organización de cualquier tipo, en cualquier industria y de cualquier tamaño necesita planificar su estrategia de ciberseguridad, debe comenzar creando un inventario de su`data assets`. Si desea proteger algo, ¡primero debe saber lo que está protegiendo! Una vez que los activos han sido inventorados, puede comenzar el proceso de`asset management`. Este es un concepto clave en seguridad defensiva.

### **Inventario de activos**

`Asset inventory`es un componente crítico de la gestión de vulnerabilidad. Una organización debe comprender qué activos están en su red para proporcionar la protección adecuada y establecer las defensas apropiadas. El inventario de activos debe incluir tecnología de la información, tecnología operativa, activos físicos, software, móviles y de desarrollo. Las organizaciones pueden utilizar herramientas de gestión de activos para realizar un seguimiento de los activos. Los activos deben tener clasificaciones de datos para garantizar controles de seguridad y acceso adecuados.

### **Inventario de aplicación y sistema**

Una organización debe crear un inventario completo y completo de los activos de datos para la gestión adecuada de activos para la seguridad defensiva. Los activos de datos incluyen:

- Todos los datos almacenados en las instalaciones. HDDS y SSD en puntos finales (PC y dispositivos móviles), HDDS y SSD en servidores, unidades externas en la red local, medios ópticos (DVD, discos Blu-ray, CD), medios flash (barras USB, tarjetas SD). La tecnología heredada puede incluir discos de disquete, unidades postales (una reliquia de la década de 1990) y unidades de cinta.
- Todo el almacenamiento de datos que posee su proveedor de la nube.[Servicios web de Amazon](https://aws.amazon.com/) (`AWS`), [Plataforma en la nube de Google](https://cloud.google.com/) (`GCP`), y[Microsoft Azure](https://azure.microsoft.com/en-us/)son algunos de los proveedores de nubes más populares, pero hay muchos más. A veces, las redes corporativas son "múltiples nubes", lo que significa que tienen más de un proveedor de la nube. El proveedor de la nube de una empresa proporcionará herramientas que pueden usarse para inventariar todos los datos almacenados por ese proveedor de nube en particular.
- Todos los datos almacenados en varios`Software-as-a-Service (SaaS)`aplicaciones. Estos datos también están "en la nube", pero es posible que no todos estén dentro del alcance de una cuenta de proveedor de la nube corporativa. Estos son a menudo servicios de consumo o la versión "comercial" de esos servicios. Piense en servicios en línea como`Google Drive`, `Dropbox`, `Microsoft Teams`, `Apple iCloud`, `Adobe Creative Suite`, `Microsoft Office 365`, `Google Docs`, y la lista continúa.
- Todas las aplicaciones que una empresa necesita utilizar para realizar su operación y negocio habituales. Incluidas las aplicaciones que se implementan localmente y las aplicaciones que se implementan a través de la nube o que de otro modo son software como servicio.
- Todos los dispositivos de redes informáticos locales de una empresa. Estos incluyen pero no se limitan a`routers`, `firewalls`, `hubs`, `switches`, dedicado`intrusion detection`y`prevention systems` (`IDS/IPS`), `data loss prevention` (`DLP`) sistemas, y así sucesivamente.

Todos estos activos son muy importantes. Un actor de amenaza o cualquier otro tipo de riesgo para cualquiera de estos activos puede causar daños significativos a la seguridad de la información de una empresa y la capacidad de operar día a día. Una organización debe tomarse su tiempo para evaluar todo y tener cuidado de no perderse un solo activo de datos, o no podrán protegerlo.

Las organizaciones frecuentemente agregan o eliminan computadoras, almacenamiento de datos, capacidad del servidor en la nube u otros activos de datos. Cada vez que se agregan o eliminan los activos de datos, esto debe anotarse a fondo en el`data asset inventory`.

---

# **Adelante**

A continuación, discutiremos algunos estándares clave a los que las organizaciones pueden estar sujetas o elegir seguir para estandarizar su enfoque de gestión de riesgos y vulnerabilidad.