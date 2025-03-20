# Threat Hunting Definition

# **Definición de caza de amenazas**

La duración media entre una brecha de seguridad real y su detección, también denominada "tiempo de permanencia", suele ser de varias semanas, si no meses. Esto implica una posible presencia adversaria dentro de una red durante un lapso de aproximadamente tres semanas, una duración que puede tener un impacto significativo.

Este hecho alarmante subraya la creciente ineficacia de las tácticas tradicionales de ciberseguridad orientadas a la defensa. En respuesta, abogamos por un cambio de paradigma hacia una estrategia ofensiva y proactiva: el inicio de la caza de amenazas.

`Threat hunting` es una práctica activa, dirigida por humanos y, a menudo, basada en hipótesis, que examina sistemáticamente los datos de la red para identificar amenazas avanzadas y sigilosas que evaden las soluciones de seguridad existentes. Esta evolución estratégica desde una postura convencionalmente reactiva nos permite descubrir amenazas insidiosas que los sistemas de detección automatizados o entidades externas como las fuerzas del orden podrían no discernir.

El objetivo principal de la búsqueda de amenazas es reducir sustancialmente el tiempo de permanencia mediante el reconocimiento de entidades maliciosas en la etapa más temprana de la cadena de ciberataque. Esta postura proactiva tiene el potencial de evitar que los actores de amenazas se atrincheren profundamente en nuestra infraestructura y neutralizarlos rápidamente.

El proceso de búsqueda de amenazas comienza con la identificación de activos (sistemas o datos) que podrían ser objetivos de alto valor para los actores de amenazas. A continuación, analizamos las TTP (tácticas, técnicas y procedimientos) que probablemente emplearán estos adversarios, según la inteligencia de amenazas actual. Posteriormente, nos esforzamos por detectar, aislar y validar de manera proactiva cualquier artefacto relacionado con los TTP mencionados anteriormente y cualquier actividad anómala que se desvíe de las normas básicas establecidas.

Durante la tarea de caza, empleamos regularmente Threat Intelligence, un componente vital que ayuda a formular hipótesis de caza efectivas, desarrollar contratácticas y ejecutar medidas de protección para evitar que el sistema se vea comprometido.

Las facetas clave de la caza de amenazas incluyen:

- Una ofensiva, `proactive` estrategia que prioriza la anticipación de amenazas sobre la reacción, basada en hipótesis, TTP del atacante e inteligencia.
- Una ofensiva, `reactive` respuesta que busca en la red artefactos relacionados con un`verified` incident, based on evidence and intelligence.
- Una comprensión sólida y práctica del panorama de amenazas, las ciberamenazas, los TTP adversarios y la cadena de ciberataque.
- Empatía cognitiva con el atacante, fomentando la comprensión de la mentalidad adversaria.
- Un conocimiento profundo del entorno de TI de la organización, la topología de la red, los activos digitales y la actividad normal.
- Utilización de datos de alta fidelidad y análisis tácticos, y aprovechamiento de herramientas y plataformas avanzadas de búsqueda de amenazas.

---

# **La relación entre la gestión de incidentes y la búsqueda de amenazas**

Entonces, ¿cómo se cruza la caza de amenazas con las distintas fases del manejo de incidentes?

- En el `Preparation` En la fase de manejo de incidentes, un equipo de caza de amenazas debe establecer reglas de enfrentamiento sólidas y claras. Se deben establecer protocolos operativos que describan cuándo y cómo intervenir, el curso de acción en escenarios específicos, etc. Las organizaciones pueden optar por integrar la búsqueda de amenazas en sus políticas y procedimientos de manejo de incidentes existentes, obviando la necesidad de políticas y procedimientos de búsqueda de amenazas separados.
- durante el `Detection & Analysis` En la fase de manejo de incidentes, la perspicacia de un cazador de amenazas es indispensable. Pueden aumentar las investigaciones, determinar si los indicadores de compromiso (IoC) observados realmente significan un incidente y, además, su mentalidad adversaria puede ayudar a descubrir artefactos o IoC adicionales que podrían haberse pasado por alto inicialmente.
- En el `Containment, Eradication, and Recovery` En la fase de gestión de incidentes, el papel de un cazador puede ser diverso. Algunas organizaciones pueden esperar que los cazadores realicen tareas dentro de las etapas de Contención, Erradicación y Recuperación. Sin embargo, esta no es una práctica universalmente aceptada. Las funciones y responsabilidades específicas del equipo de caza se estipularán en los documentos procesales y las políticas de seguridad.
- En cuanto a la `Post-Incident Activity` En la fase de manejo de incidentes, los cazadores, con su amplia experiencia que abarca varios dominios de TI y seguridad de TI, pueden contribuir significativamente. Pueden ofrecer recomendaciones para fortalecer la postura de seguridad general de la organización.

Intentamos arrojar luz sobre la relación simbiótica entre el manejo de incidentes y la búsqueda de amenazas. Si estos procesos deben integrarse o funcionar de forma independiente es una decisión estratégica, que depende del panorama de amenazas, riesgos, etc. únicos de cada organización.

---

# **La estructura de un equipo de caza de amenazas**

La construcción de un equipo de caza de amenazas es un proceso estratégico y meticulosamente planificado que requiere una amplia gama de habilidades, experiencia y perspectivas. Es crucial que cada miembro del equipo ofrezca un conjunto único de competencias que, cuando se combinan, brinden un enfoque holístico e integral para identificar, mitigar y eliminar amenazas.

La composición ideal del equipo de caza de amenazas suele incluir las siguientes funciones:

- `Threat Hunter`: La función principal dentro del equipo, los cazadores de amenazas, son profesionales de la ciberseguridad con un profundo conocimiento del panorama de amenazas, las tácticas, técnicas y procedimientos (TTP) de los ciberadversarios y metodologías sofisticadas de detección de amenazas. Buscan proactivamente indicadores de compromiso (IoC) y dominan el uso de una variedad de herramientas y plataformas de búsqueda de amenazas.
- `Threat Intelligence Analyst`: Estas personas son responsables de recopilar y analizar datos de una variedad de fuentes, incluida inteligencia de código abierto, inteligencia de la web oscura, informes de la industria y fuentes de amenazas. Su trabajo es comprender el panorama de amenazas actual y predecir tendencias futuras, proporcionando información valiosa a los cazadores de amenazas.
- `Incident Responders`: Cuando los cazadores de amenazas identifican amenazas potenciales, los servicios de respuesta a incidentes intervienen para gestionar la situación. Investigan exhaustivamente el incidente y también son responsables de las acciones de contención, erradicación y recuperación, y aseguran que la organización pueda reanudar rápidamente sus operaciones normales.
- `Forensics Experts`: Estos son los miembros del equipo que profundizan en los detalles técnicos de un incidente. Son competentes en análisis forense digital y respuesta a incidentes (DFIR), capaces de analizar malware, realizar ingeniería inversa de ataques y proporcionar informes detallados de incidentes.
- `Data Analysts/Scientists`: Desempeñan un papel fundamental en el examen de grandes conjuntos de datos, utilizando modelos estadísticos, algoritmos de aprendizaje automático y técnicas de minería de datos para descubrir patrones, correlaciones y tendencias que pueden conducir a conocimientos prácticos para los cazadores de amenazas.
- `Security Engineers/Architects`: Los ingenieros de seguridad son responsables del diseño general de la infraestructura de seguridad de la organización. Se aseguran de que todos los sistemas, aplicaciones y redes estén diseñados teniendo en cuenta la seguridad y, a menudo, trabajan en estrecha colaboración con los cazadores de amenazas para implementar herramientas y técnicas que faciliten la caza de amenazas, así como las defensas de la cadena de destrucción.
- `Network Security Analyst`: Estos profesionales se especializan en el comportamiento de la red y los patrones de tráfico. Entienden el flujo y reflujo normal de la actividad de la red y pueden identificar rápidamente anomalías indicativas de una posible violación de la seguridad.
- `SOC Manager`: El gerente del Centro de operaciones de seguridad (SOC) supervisa las operaciones del equipo de caza de amenazas, asegurando una coordinación fluida entre los miembros del equipo y una comunicación efectiva con el resto de la organización.

---

# **¿Cuándo debemos cazar?**

En el ámbito de la ciberseguridad, la caza de amenazas no debe verse como una práctica esporádica o reaccionaria, sino más bien como una actividad sostenida y con visión de futuro. Sin embargo, hay casos específicos que requieren una operación inmediata e intensa de búsqueda de amenazas. Aquí hay un desglose más complejo de estos casos:

- `When New Information on an Adversary or Vulnerability Comes to Light`: El panorama de la ciberseguridad está en constante evolución y periódicamente se descubre nueva información sobre posibles amenazas y vulnerabilidades del sistema. Si hay un adversario recién descubierto o una vulnerabilidad asociada con una aplicación que utiliza nuestra red, esto requiere una sesión de búsqueda de amenazas inmediata. Es imperativo descifrar el modus operandi del adversario y examinar la vulnerabilidad para evaluar el posible riesgo para nuestros sistemas. Por ejemplo, si nos topamos con una vulnerabilidad previamente desconocida en una aplicación ampliamente utilizada, inmediatamente iniciaríamos una iniciativa de búsqueda de amenazas para detectar cualquier signo de explotación.
- `When New Indicators are Associated with a Known Adversary`: A menudo, las fuentes de inteligencia de ciberseguridad publican nuevos indicadores de compromiso (IoC) vinculados a adversarios específicos. Si estos indicadores están asociados con un adversario conocido por atacar redes similares a la nuestra o si hemos sido objetivo del mismo adversario en el pasado, debemos lanzar una iniciativa de búsqueda de amenazas. Esto ayuda a detectar cualquier rastro de las actividades del adversario dentro de nuestro sistema, lo que nos permite protegernos de posibles infracciones.
- `When Multiple Network Anomalies are Detected`: Las anomalías de la red a veces pueden ser inofensivas, causadas por fallas del sistema o modificaciones válidas. Sin embargo, varias anomalías que aparecen simultáneamente o en un período corto pueden indicar un problema sistémico o un ataque orquestado. En tales casos, es fundamental llevar a cabo una búsqueda de amenazas para identificar la causa raíz de estas anomalías y abordar cualquier posible amenaza. Por ejemplo, si observamos un comportamiento extraño en el tráfico de la red o actividades inesperadas en el sistema, iniciaríamos una búsqueda de amenazas para investigar estas anomalías.
- `During an Incident Response Activity`: Tras la detección de un incidente de seguridad confirmado, nuestro equipo de Respuesta a Incidentes (IR) se concentrará en la contención, erradicación y recuperación. Sin embargo, mientras el proceso de IR está en marcha, es vital realizar simultáneamente una búsqueda de amenazas en toda la red. Esto nos permite exponer cualquier amenaza conectada que podría no ser fácilmente visible, comprender el alcance total del compromiso y evitar daños mayores. Por ejemplo, durante una infiltración de malware confirmada, mientras el equipo de IR se ocupa del sistema infectado, la búsqueda de amenazas puede ayudar a identificar otros sistemas potencialmente comprometidos.
- `Periodic Proactive Actions`: Más allá de los escenarios mencionados anteriormente, es fundamental tener en cuenta que la búsqueda de amenazas no debe ser simplemente una tarea reactiva. Los ejercicios periódicos y proactivos de búsqueda de amenazas son clave para descubrir amenazas latentes que pueden haber traspasado nuestras defensas de seguridad. Esto garantiza una estrategia de monitoreo continuo, reforzando nuestra postura general de seguridad y minimizando el impacto potencial de un ataque.

En pocas palabras, el momento ideal para la caza de amenazas es siempre el presente. Una postura proactiva en la búsqueda de amenazas nos permite detectar y neutralizar amenazas antes de que puedan causar daños sustanciales.

---

# **La relación entre la evaluación de riesgos y la búsqueda de amenazas**

La evaluación de riesgos, como faceta esencial de la ciberseguridad, permite una comprensión integral de las posibles vulnerabilidades y vectores de amenazas dentro de una organización. En el contexto de la caza de amenazas, la evaluación de riesgos sirve como un facilitador clave, permitiéndonos priorizar nuestras actividades de caza y centrar nuestros esfuerzos en las áreas de mayor impacto potencial.

Para empezar, la evaluación de riesgos implica un proceso sistemático de identificación y evaluación de riesgos en función de las posibles fuentes de amenazas, las vulnerabilidades existentes y el impacto potencial en caso de que se exploten estas vulnerabilidades. Implica una serie de pasos que incluyen la identificación de activos, la identificación de amenazas, la identificación de vulnerabilidades, la determinación de riesgos y, finalmente, la formulación de una estrategia de mitigación de riesgos.

En el proceso de búsqueda de amenazas, la información obtenida de una evaluación de riesgos exhaustiva puede guiar nuestras actividades de varias maneras:

- `Prioritizing Hunting Efforts`: Al reconocer los activos más críticos (a menudo denominados "joyas de la corona") y sus riesgos asociados, podemos priorizar nuestros esfuerzos de búsqueda de amenazas en estas áreas. Los activos podrían incluir repositorios de datos confidenciales, aplicaciones de misión crítica o infraestructura de red clave.
- `Understanding Threat Landscape`: El paso de identificación de amenazas de la evaluación de riesgos nos permite comprender mejor el panorama de amenazas, incluidas las tácticas, técnicas y procedimientos (TTP) utilizados por los posibles actores de amenazas. Esta comprensión nos ayuda a desarrollar nuestras hipótesis de caza, que son esenciales para la caza proactiva de amenazas.
- `Highlighting Vulnerabilities`: La evaluación de riesgos ayuda a resaltar las vulnerabilidades en nuestros sistemas, aplicaciones y procesos. Conocer estas debilidades nos permite buscar indicadores de explotación en estas áreas. Por ejemplo, si sabemos que una aplicación en particular tiene una vulnerabilidad que permite una escalada de privilegios, podemos buscar anomalías en los niveles de privilegios de los usuarios.
- `Informing the Use of Threat Intelligence`: La inteligencia de amenazas se utiliza a menudo en la búsqueda de amenazas para identificar patrones de comportamiento malicioso. La evaluación de riesgos ayuda a informar la aplicación de inteligencia sobre amenazas al identificar los actores de amenazas más probables y sus métodos de ataque preferidos.
- `Refining Incident Response Plans`: La evaluación de riesgos también desempeña un papel fundamental en el perfeccionamiento de los planes de respuesta a incidentes (IR). Comprender los riesgos probables nos ayuda a anticipar y planificar posibles infracciones, garantizando una respuesta rápida y eficaz.
- `Enhancing Cybersecurity Controls`: Por último, las estrategias de mitigación de riesgos derivadas de la evaluación de riesgos pueden contribuir directamente a mejorar los controles y defensas de ciberseguridad existentes, fortaleciendo aún más la postura de seguridad de la organización.

Los aspectos técnicos del empleo de la evaluación de riesgos para la búsqueda de amenazas incluyen el uso de herramientas y técnicas avanzadas. Estos van desde escáneres de vulnerabilidades automatizados y herramientas de pruebas de penetración hasta sofisticadas plataformas de inteligencia sobre amenazas. Por ejemplo, los sistemas SIEM (gestión de eventos e información de seguridad) se pueden utilizar para agregar y correlacionar eventos de diversas fuentes, proporcionando una visión holística del estado de seguridad de la organización y ayudando en la búsqueda de amenazas.

En esencia, la evaluación de riesgos y la búsqueda de amenazas están profundamente entrelazadas y cada una se complementa para crear una postura de ciberseguridad más sólida y resiliente. Al realizar periódicamente evaluaciones integrales de riesgos, podemos centrar mejor nuestras actividades de búsqueda de amenazas, reduciendo así el tiempo de permanencia, mitigando daños potenciales y mejorando nuestra defensa general de ciberseguridad.