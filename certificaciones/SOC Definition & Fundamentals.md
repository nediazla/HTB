# SOC Definition & Fundamentals

# **¿Qué es un SOC?**

Un Centro de Operaciones de Seguridad (SOC) es una instalación esencial que alberga un equipo de expertos en seguridad de la información responsables de monitorear y evaluar continuamente el estado de seguridad de una organización. El objetivo principal de un equipo SOC es identificar, examinar y abordar incidentes de ciberseguridad empleando una combinación de soluciones tecnológicas y un conjunto integral de procedimientos.

El equipo SOC generalmente está formado por analistas, ingenieros y gerentes de seguridad competentes que supervisan las operaciones de seguridad. Colaboran estrechamente con los equipos de respuesta a incidentes de la organización para garantizar que los problemas de seguridad se detecten y resuelvan rápidamente.

El equipo SOC utiliza varias soluciones tecnológicas, como sistemas de gestión de eventos e información de seguridad (SIEM), sistemas de prevención y detección de intrusiones (IDS/IPS) y herramientas de detección y respuesta de terminales (EDR), para monitorear e identificar amenazas a la seguridad. También hacen uso de inteligencia sobre amenazas y participan en iniciativas de búsqueda de amenazas para detectar de forma proactiva amenazas y vulnerabilidades potenciales.

Además de emplear soluciones tecnológicas, el equipo SOC sigue una serie de procesos bien definidos para abordar los incidentes de seguridad. Estos procesos abarcan la clasificación, contención, eliminación y recuperación de incidentes. El equipo SOC coopera estrechamente con el equipo de respuesta a incidentes para garantizar el manejo adecuado de los incidentes de seguridad, salvaguardando la postura de seguridad de la organización.

En resumen, un SOC es un elemento vital del enfoque de ciberseguridad de una organización. Ofrece capacidades de respuesta y monitoreo continuo, lo que permite a las organizaciones detectar y abordar rápidamente incidentes de seguridad, minimizando las consecuencias de una violación de seguridad y disminuyendo la probabilidad de ataques futuros.

---

# **¿Cómo funciona un SOC?**

La función principal del equipo SOC es gestionar el aspecto operativo continuo de la seguridad de la información empresarial en lugar de concentrarse en el desarrollo de estrategias de seguridad, diseñar arquitectura de seguridad o implementar medidas de protección.

El equipo SOC está formado principalmente por analistas de seguridad que trabajan colectivamente para detectar, evaluar, responder, informar y prevenir incidentes de ciberseguridad.

Además de las responsabilidades principales de un equipo SOC, algunos SOC pueden poseer capacidades avanzadas como análisis forense y análisis de malware. Estas capacidades permiten al equipo SOC realizar investigaciones en profundidad de incidentes de seguridad y examinar la causa raíz del incidente para evitar futuros ataques.

Como se mencionó anteriormente, el equipo SOC también colabora estrechamente con el equipo de respuesta a incidentes para garantizar el manejo adecuado de los incidentes de seguridad y la preservación de la postura de seguridad de la organización.

---

# **Funciones dentro de un SOC**

Un equipo SOC consta de diversos roles responsables de manejar el aspecto operativo continuo de la seguridad de la información empresarial. Estos roles pueden abarcar:

- `SOC Director`: Responsable de la gestión general y la planificación estratégica del SOC, incluido el presupuesto, la dotación de personal y la alineación con los objetivos de seguridad de la organización.
- `SOC Manager`: Supervisa las operaciones diarias, gestiona el equipo, coordina los esfuerzos de respuesta a incidentes y garantiza una colaboración fluida con otros departamentos.
- `Tier 1 Analyst`: Supervisa alertas y eventos de seguridad, clasifica incidentes potenciales y los escala a niveles superiores para una mayor investigación.
- `Tier 2 Analyst`: Realiza análisis en profundidad de incidentes escalados, identifica patrones y tendencias y desarrolla estrategias de mitigación para abordar las amenazas a la seguridad.
- `Tier 3 Analyst`: Proporciona experiencia avanzada en el manejo de incidentes de seguridad complejos, realiza actividades de búsqueda de amenazas y colabora con otros equipos para mejorar la postura de seguridad de la organización.
- `Detection Engineer`: Un ingeniero de detección es responsable de desarrollar, implementar y mantener reglas de detección y firmas para herramientas de monitoreo de seguridad, como soluciones SIEM, IDS/IPS y EDR. Trabajan en estrecha colaboración con analistas de seguridad para identificar brechas en la cobertura de detección y mejorar continuamente la capacidad de la organización para detectar y responder a las amenazas.
- `Incident Responder`: Se hace cargo de los incidentes de seguridad activos, lleva a cabo análisis forenses digitales en profundidad y esfuerzos de contención y remediación, y colabora con otros equipos para restaurar los sistemas afectados y prevenir incidentes futuros.
- `Threat Intelligence Analyst`: Reúne, analiza y difunde datos de inteligencia sobre amenazas para ayudar a los miembros del equipo SOC a comprender mejor el panorama de amenazas y defenderse proactivamente contra los riesgos emergentes.
- `Security Engineer`: Desarrolla, implementa y mantiene herramientas, tecnologías e infraestructura de seguridad, y proporciona experiencia técnica al equipo SOC.
- `Compliance and Governance Specialist`: Garantiza que las prácticas y procesos de seguridad de la organización cumplan con los estándares, regulaciones y mejores prácticas relevantes de la industria, y ayuda con los requisitos de auditoría y generación de informes.
- `Security Awareness and Training Coordinator`: Desarrolla e implementa programas de concientización y capacitación en seguridad para educar a los empleados sobre las mejores prácticas de ciberseguridad y promover una cultura de seguridad dentro de la organización.

---

Es importante tener en cuenta que las funciones y responsabilidades específicas dentro de cada nivel pueden variar según el tamaño de la organización, la industria y los requisitos de seguridad específicos.

En general, la estructura escalonada se puede describir de la siguiente manera:

- `Tier 1 Analysts`: También conocidos como "primeros respondedores", estos analistas monitorean eventos y alertas de seguridad, realizan una clasificación inicial y escalan incidentes potenciales a niveles superiores para una mayor investigación. Su principal objetivo es identificar y priorizar rápidamente los incidentes de seguridad.
- `Tier 2 Analysts`: Estos analistas tienen más experiencia y realizan análisis más profundos de incidentes escalados. Identifican patrones y tendencias, desarrollan estrategias de mitigación y, en ocasiones, ayudan en los esfuerzos de respuesta a incidentes. También pueden ser responsables de ajustar las herramientas de monitoreo de seguridad para reducir los falsos positivos y mejorar las capacidades de detección.
- `Tier 3 Analysts`: A menudo considerados los analistas más experimentados y conocedores del equipo, los analistas de nivel 3 manejan los incidentes de seguridad más complejos y de alto perfil. También pueden participar en la búsqueda proactiva de amenazas, desarrollar estrategias avanzadas de detección y prevención y colaborar con otros equipos para mejorar la postura general de seguridad de la organización.

---

# **Etapas del SOC**

Los Centros de Operaciones de Seguridad (SOC) han evolucionado significativamente desde sus inicios, ya que los Centros de Operaciones de Red se centraban principalmente en la seguridad de la red. En la primera generación, conocida como SOC 1.0, las organizaciones invirtieron en determinadas capas de seguridad, como plataformas de inteligencia de seguridad o sistemas de gestión de identidades. Sin embargo, la falta de una integración adecuada generó alertas no correlacionadas y una acumulación de tareas en múltiples plataformas. Esta etapa se caracterizó por un énfasis en la seguridad de la red y del perímetro, incluso cuando las amenazas comenzaron a explotar otros vectores. Sorprendentemente, algunas organizaciones siguen confiando en este enfoque obsoleto, aparentemente esperando que se produzca una infracción importante.

La aparición de amenazas sofisticadas, incluidos ataques multivectoriales, persistentes y asincrónicos con indicadores ocultos de compromiso, ha estimulado la transición al SOC 2.0. El malware, incluidas las variantes móviles, y las botnets sirven como métodos principales de entrega de estos ataques. La longevidad, el comportamiento cambiante y el crecimiento de las botnets a lo largo del tiempo se han convertido en puntos focales de la inteligencia sobre amenazas. SOC 2.0 se basa en inteligencia, integrando telemetría de seguridad, inteligencia sobre amenazas, análisis de flujo de red y otras técnicas de detección de anomalías. Además, en esta etapa se emplea el análisis de capa 7 para identificar ataques bajos y lentos y otras amenazas ocultas. Un enfoque con visión de futuro para la investigación de amenazas y la colaboración entre SOC, ya sea dentro de sectores o a nivel nacional, es crucial para el éxito del SOC 2.0. Se hace hincapié en una conciencia situacional completa, la preparación previa al evento a través de la gestión de vulnerabilidades, la gestión de la configuración y la gestión dinámica de riesgos, así como el análisis y el aprendizaje posteriores al evento a través de la respuesta a incidentes y análisis forenses en profundidad. En esta etapa también es vital perfeccionar las reglas de inteligencia de seguridad y desplegar contramedidas.

El SOC cognitivo, o SOC de próxima generación, busca abordar las deficiencias restantes del SOC 2.0. Si bien SOC 2.0 tiene todos los subsistemas esenciales, a menudo carece de experiencia operativa y de colaboración efectiva entre los equipos empresariales y de seguridad para crear reglas que detecten amenazas específicas a los procesos y sistemas empresariales. Además, muchas organizaciones todavía carecen de procedimientos estandarizados de respuesta y recuperación ante incidentes.

Los SOC cognitivos tienen como objetivo resolver estos problemas incorporando sistemas de aprendizaje que compensen las brechas de experiencia en la toma de decisiones de seguridad. Si bien la tasa de éxito de este enfoque puede no ser perfecta en todos los casos, se espera que mejore con el tiempo.

[Referencia: https://www.linkedin.com/pulse/evolution-security-operations-center-20-beyond-krishnan-jagannathan/](https://www.linkedin.com/pulse/evolution-security-operations-center-20-beyond-krishnan-jagannathan/)