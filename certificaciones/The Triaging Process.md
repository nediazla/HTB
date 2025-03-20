# The Triaging Process

# **¿Qué es la clasificación de alertas?**

`Alert triaging`, realizado por un analista del Centro de operaciones de seguridad (SOC), es el proceso de evaluación y priorización de alertas de seguridad generadas por varios sistemas de monitoreo y detección para determinar su nivel de amenaza y el impacto potencial en los sistemas y datos de una organización. Implica revisar y categorizar sistemáticamente las alertas para asignar recursos de manera efectiva y responder a incidentes de seguridad.

`Escalation` es un aspecto importante de la clasificación de alertas en un entorno SOC. El proceso de escalada generalmente implica notificar a los supervisores, equipos de respuesta a incidentes o personas designadas dentro de la organización que tienen la autoridad para tomar decisiones y coordinar el esfuerzo de respuesta. El analista de SOC proporciona información detallada sobre la alerta, incluida su gravedad, impacto potencial y cualquier hallazgo relevante de la investigación inicial. Esto permite a quienes toman las decisiones evaluar la situación y determinar el curso de acción apropiado, como involucrar a equipos especializados, iniciar procedimientos más amplios de respuesta a incidentes o contratar recursos externos si es necesario.

La escalada garantiza que las alertas críticas reciban atención inmediata y facilita la coordinación efectiva entre las diferentes partes interesadas, lo que permite una respuesta oportuna y eficiente a posibles incidentes de seguridad. Ayuda a aprovechar la experiencia y las capacidades de toma de decisiones de las personas responsables de gestionar y mitigar amenazas o incidentes de nivel superior dentro de la organización.

---

# **¿Cuál es el proceso de clasificación ideal?**

1. `Initial Alert Review`:
- Revise minuciosamente la alerta inicial, incluidos los metadatos, la marca de tiempo, la IP de origen, la IP de destino, los sistemas afectados y la regla/firma de activación.
- Analice los registros asociados (tráfico de red, sistema, aplicación) para comprender el contexto de la alerta.
1. `Alert Classification`:
- Clasifique la alerta según la gravedad, el impacto y la urgencia utilizando el sistema de clasificación predefinido de la organización.
1. `Alert Correlation`:
- Haga una referencia cruzada de la alerta con alertas, eventos o incidentes relacionados para identificar patrones, similitudes o posibles indicadores de compromiso (IOC).
- Consulte el SIEM o el sistema de gestión de registros para recopilar datos de registro relevantes.
- Aproveche las fuentes de inteligencia sobre amenazas para comprobar patrones de ataque conocidos o firmas de malware.
1. `Enrichment of Alert Data`:
- Recopile información adicional para enriquecer los datos de alerta y ganar contexto:
    - Recopile capturas de paquetes de red, volcados de memoria o muestras de archivos asociados con la alerta.
    - Utilice fuentes externas de inteligencia sobre amenazas, herramientas de código abierto o entornos sandbox para analizar archivos, URL o direcciones IP sospechosos.
    - Realizar reconocimiento de los sistemas afectados en busca de anomalías (conexiones de red, procesos, modificaciones de archivos).
1. `Risk Assessment`:
- Evalúe el riesgo potencial y el impacto en activos, datos o infraestructura críticos:
    - Considere el valor de los sistemas afectados, la sensibilidad de los datos, los requisitos de cumplimiento y las implicaciones regulatorias.
    - Determine la probabilidad de un ataque exitoso o un posible movimiento lateral.
1. `Contextual Analysis`:
- El analista considera el contexto que rodea la alerta, incluidos los activos afectados, su criticidad y la sensibilidad de los datos que manejan.
- Evalúan los controles de seguridad implementados, como firewalls, sistemas de detección/prevención de intrusiones y soluciones de protección de terminales, para determinar si la alerta indica una posible falla de control o una técnica de evasión.
- El analista evalúa los requisitos de cumplimiento relevantes, las regulaciones de la industria y las obligaciones contractuales para comprender las implicaciones de la alerta en la postura de cumplimiento legal y regulatorio de la organización.
1. `Incident Response Planning`:
- Inicie un plan de respuesta a incidentes si la alerta es importante:
    - Documente los detalles de las alertas, los sistemas afectados, los comportamientos observados, los posibles IOC y los datos de enriquecimiento.
    - Asigne miembros del equipo de respuesta a incidentes con roles y responsabilidades definidas.
    - Coordine con otros equipos (operaciones de red, administradores de sistemas, proveedores) según sea necesario.
1. `Consultation with IT Operations`:
- Evalúe la necesidad de contexto adicional o información faltante consultando con operaciones de TI o departamentos relevantes:
    - Participe en debates o reuniones para recopilar información sobre los sistemas afectados, cambios recientes o actividades de mantenimiento en curso.
    - Colabore para comprender cualquier problema conocido, configuración incorrecta o cambio de red que pueda generar alertas falsas positivas.
    - Obtenga una comprensión holística del entorno y de cualquier actividad no maliciosa que pueda haber desencadenado la alerta.
    - Documente los conocimientos y la información obtenidos durante la consulta.
1. `Response Execution`:
- Con base en la revisión de alertas, la evaluación de riesgos y la consulta, determine las acciones de respuesta apropiadas.
- Si el contexto adicional resuelve la alerta o la identifica como un evento no malicioso, tome las medidas necesarias sin escalarla.
- Si la alerta aún indica posibles problemas de seguridad o requiere más investigación, continúe con las acciones de respuesta al incidente.
1. `Escalation`:
- Identifique los desencadenantes de la escalada según las políticas de la organización y la gravedad de las alertas:
    - Los desencadenantes pueden incluir el compromiso de sistemas/activos críticos, ataques continuos, técnicas desconocidas/sofisticadas, impacto generalizado o amenazas internas.
- Evalúe la alerta frente a los factores desencadenantes de una escalada, considerando las posibles consecuencias si no se intensifica.
- Siga el proceso de escalamiento interno, notificando a los equipos/gerencias de nivel superior responsables de la respuesta a incidentes.
- Proporcione un resumen completo de alertas, gravedad, impacto potencial, datos de enriquecimiento y evaluación de riesgos.
- Documente todas las comunicaciones relacionadas con la escalada.
- En algunos casos, escalar a entidades externas (aplicaciones policiales, proveedores de respuesta a incidentes, CERT) según los requisitos legales/regulatorios.
1. `Continuous Monitoring`:
- Supervisar continuamente la situación y el progreso de la respuesta a incidentes.
- Mantenga una comunicación abierta con los equipos escalados, proporcionando actualizaciones sobre desarrollos, hallazgos o cambios en la gravedad/impacto.
- Colabore estrechamente con los equipos escalados para una respuesta coordinada.
1. `De-escalation`:
- Evalúe la necesidad de reducir las tensiones a medida que avanza la respuesta al incidente y la situación está bajo control.
- Reduzca la escalada cuando se mitigue el riesgo, se contenga el incidente y no sea necesario una mayor escalada.
- Notificar a las partes relevantes, proporcionando un resumen de las acciones tomadas, los resultados y las lecciones aprendidas.

Revisar y actualizar periódicamente el proceso, alineándolo con las políticas, procedimientos y lineamientos de la organización. Adaptar el proceso para abordar las amenazas emergentes y las necesidades cambiantes.