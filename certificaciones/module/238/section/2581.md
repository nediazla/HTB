# Elementos de un informe incidente adecuado

# **Resumen ejecutivo**

Consideremos el`Executive Summary`Como la puerta de entrada a nuestro informe, diseñada para atender a una audiencia amplia, incluidas las partes interesadas no técnicas. Esta sección debe proporcionar al lector una visión general sucinta, hallazgos clave, acciones inmediatas ejecutadas y el impacto en las partes interesadas. Dado que muchas partes interesadas solo pueden examinar el`Executive Summary`, es imperativo clavar esta sección. Aquí hay un desglose más granular de lo que debería estar encapsulado en el`Executive Summary`:

| **Sección** | **Descripción** |
| --- | --- |
| ID de incidente | Identificador único para el incidente. |
| Descripción de incidentes | Proporcione un resumen conciso de los eventos del incidente (incluida la detección inicial) y indique explícitamente su tipo. ¿Fue un ataque de ransomware, una violación de datos a gran escala o ambos? Esto también debe abarcar el tiempo y la fecha estimados del incidente, así como su duración, los sistemas/datos afectados y el estado (en curso, resuelto o escalado) |
| Hallazgos clave | Enumere cualquier hallazgo sobresaliente que surgiera del incidente. ¿Cuál fue la causa raíz? ¿Se explotó un CVE específico? ¿Qué datos, si los hay, se vieron comprometidos, exfiltrados o en peligro? |
| Acciones inmediatas tomadas | Describe las medidas de respuesta inmediata tomadas. ¿Se aislaron los sistemas afectados de inmediato? ¿Se identificó la causa raíz? ¿Invactamos en algún servicio de terceros y, de ser así, ¿quiénes fueron? |
| Impacto de las partes interesadas | Evaluar el impacto potencial en varias partes interesadas. Por ejemplo, ¿algún cliente experimentó el tiempo de inactividad y cuáles son las ramificaciones financieras? ¿Se comprometieron los datos de los empleados? ¿Estaba en riesgo la información patentada y cuáles son las potenciales repercusiones? |

# **Análisis técnico**

Esta sección es donde nos sumergimos profundamente en los aspectos técnicos, diseccionando los eventos que ocurrieron durante el incidente. Es probable que sea la parte más voluminosa del informe del incidente. Se deben abordar los siguientes puntos clave:

### **Sistemas y datos afectados**

Destaca todos los sistemas y datos a los que se accedieron o se comprometieron definitivamente durante el incidente. Si los datos se exfiltraron, especifique el volumen o la cantidad, si se puede determinar.

### **Fuentes y análisis de evidencia**

Enfatice la evidencia escrutada, los resultados y la metodología analítica empleada. Por ejemplo, si se confirmó un compromiso a través de registros de acceso web, incluya una captura de pantalla para la documentación. Mantener la integridad de la evidencia es crucial, especialmente en casos penales. Una mejor práctica es los archivos hash para garantizar su integridad.

### **Indicadores de compromiso (COI)**

Los COI son fundamentales para los compromisos potenciales de caza en nuestro entorno más amplio o incluso entre las organizaciones asociadas. También puede ser factible atribuir el ataque a un grupo de amenazas específico basado en los COI identificados. Estos pueden variar desde el tráfico de salida anormal hasta procesos desconocidos y tareas programadas iniciadas por el atacante.

### **Análisis de causa raíz**

Dentro de esta sección, detalla el análisis de causa raíz realizada y elaborada sobre la causa subyacente del incidente de seguridad (vulnerabilidades explotadas, puntos de falla, etc.).

### **Línea de tiempo técnico**

Este es un componente fundamental para comprender la secuencia de eventos del incidente. La línea de tiempo debe incluir:

- Reconocimiento
- Compromiso inicial
- Comunicaciones C2
- Enumeración
- Movimiento lateral
- Acceso de datos y exfiltración
- Despliegue o actividad de malware (incluida la inyección del proceso y la persistencia)
- Tiempos de contención
- Tiempos de erradicación
- Tiempos de recuperación

### **Naturaleza del ataque**

Profundizar en el tipo de ataque, así como las tácticas, técnicas y procedimientos (TTP) empleados por el atacante.

---

# **Análisis de impacto**

Proporcione una evaluación de los efectos adversos que el incidente tuvo en los datos, operaciones y reputación de la organización. Este análisis tiene como objetivo cuantificar y calificar el alcance del daño causado por el incidente, identificando qué sistemas, procesos o conjuntos de datos se han comprometido. También evalúa las posibles implicaciones comerciales, como la pérdida financiera, las sanciones regulatorias y el daño de reputación.

---

# **Análisis de respuesta y recuperación**

Describe las acciones específicas tomadas para contener el incidente de seguridad, erradicar la amenaza y restaurar las operaciones normales. Esta sección sirve como una descripción cronológica de las medidas implementadas para mitigar el impacto y evitar futuras ocurrencias de incidentes similares.

Aquí hay un desglose de lo que la sección "Respuesta y recuperación" generalmente incluye:

### **Acciones de respuesta inmediata**

**Revocación de acceso**

- `Identification of Compromised Accounts/Systems`: Una descripción detallada de cómo se identificaron las cuentas o sistemas comprometidos, incluidas las herramientas y metodologías utilizadas.
- `Timeframe`: El momento exacto en que se detectó el acceso no autorizado y posteriormente revocado, hasta el minuto si es posible.
- `Method of Revocation`: Explicación de los métodos técnicos utilizados para revocar el acceso, como deshabilitar las cuentas, cambiar los permisos o alterar las reglas del firewall.
- `Impact`: Evaluación de lo que el acceso a la revocación logró inmediatamente, incluida la prevención de la exfiltración de datos o el compromiso adicional del sistema.

**Estrategia de contención**

- `Short-term Containment`: Acciones inmediatas tomadas para aislar los sistemas afectados de la red para prevenir el movimiento lateral del actor de amenaza.
- `Long-term Containment`: Medidas estratégicas, como la segmentación de red o la implementación de la arquitectura de la confianza cero, dirigidas al aislamiento a largo plazo de los sistemas afectados.
- `Effectiveness`: Una evaluación de cuán efectivas fueron las estrategias de contención para limitar el impacto del incidente.

### **Medidas de erradicación**

**Eliminación de malware**

- `Identification`: Procedimientos detallados sobre cómo se identificó el malware o el código malicioso, incluido el uso de herramientas de detección y respuesta de punto final (EDR) o análisis forense.
- `Removal Techniques`: Herramientas específicas o métodos manuales utilizados para eliminar el malware.
- `Verification`: Pasos tomados para garantizar que el malware fuera completamente erradicado, como la verificación de la suma de verificación o el análisis heurístico.

**Parcheo del sistema**

- `Vulnerability Identification`: Cómo se descubrieron las vulnerabilidades, incluidos los identificadores de CVE si corresponde.
- `Patch Management`: Cuenta detallada del proceso de parcheo, incluidas las pruebas, la implementación y las etapas de verificación.
- `Fallback Procedures`: Pasos para revertir los parches en caso de que causen inestabilidad del sistema u otros problemas.

### **Pasos de recuperación**

**Restauración de datos**

- `Backup Validation`: Procedimientos para validar la integridad de las copias de seguridad antes de la restauración.
- `Restoration Process`: Cuenta paso a paso de cómo se restauraron los datos, incluidos los métodos de descifrado utilizados si los datos se encriptaron.
- `Data Integrity Checks`: Métodos utilizados para verificar la integridad de los datos restaurados.

**Validación del sistema**

- `Security Measures`: Acciones tomadas para garantizar que los sistemas estén seguros antes de ponerlos de nuevo en línea, como reconfigurar los firewalls o actualizar los sistemas de detección de intrusos (IDS).
- `Operational Checks`: Pruebas realizadas para confirmar que los sistemas están completamente operativos y funcionan como se esperaba en un entorno de producción.

### **Acciones posteriores a la incidente**

**Escucha**

- `Enhanced Monitoring Plans`: Planes detallados para el monitoreo continuo para detectar vulnerabilidades similares o patrones de ataque en el futuro.
- `Tools and Technologies`: Herramientas de monitoreo específicas que se emplearán y cómo se integran con los sistemas existentes para una vista holística.

**Lecciones aprendidas**

- `Gap Analysis`: Una evaluación exhaustiva de qué medidas de seguridad fallaron y por qué.
- `Recommendations for Improvement`: Recomendaciones concretas y procesables basadas en las lecciones aprendidas, categorizadas por prioridad y cronograma para la implementación.
- `Future Strategy`: Cambios a largo plazo en la política, arquitectura o capacitación de personal para evitar incidentes similares.

---

# **Diagramas**

Dado que la narración puede volverse extremadamente compleja, las ayudas visuales pueden ser invaluables para simplificar las complejidades del incidente:

- `Incident Flowchart`
    - Ilustrar la progresión del ataque, desde el punto de entrada inicial hasta su propagación en toda la red.
- `Affected Systems Map`
    - Representar la topología de la red, acentuando los nodos comprometidos. Use codificación de colores o anotaciones para indicar la gravedad de cada compromiso.
- `Attack Vector Diagram`
    - Utilice flechas, nodos y anotaciones para rastrear la navegación del atacante y las actividades (posteriores) de explotación a través de nuestras defensas visualmente.
    
    ![](https://assets.website-files.com/5ff66329429d880392f6cba2/62f4fbe4fe7a4e930f363c42_Attack%20Vector%20Scheme.jpg)
    

---

# **Apéndices**

Esta sección sirve como un repositorio de material complementario que proporciona contexto adicional, evidencia o detalles técnicos que son cruciales para una comprensión integral del incidente, su impacto y las acciones de respuesta tomadas. Esta sección a menudo se considera la columna vertebral del informe, que ofrece datos y artefactos sin procesar que pueden verificarse independientemente, lo que agrega así credibilidad y profundidad a la narrativa presentada en el cuerpo principal del informe.

El`Appendices`La sección puede incluir:

- Archivos de registro
- Diagramas de red (pre-incidente y posterior al incidente)
- Evidencia forense (imágenes de disco, vertederos de memoria, etc.)
- Fragmentos de código
- Lista de verificación de respuesta a incidentes
- Registros de comunicación
- Documentos legales y regulatorios (formularios de cumplimiento, NDA firmados por consultores externos, etc.)
- Glosario y acrónimos

---

# **Mejores prácticas**

- `Root Cause Analysis`: Siempre apunte a encontrar la causa raíz del incidente para evitar futuros ocurrencias.
- `Community Sharing`: Comparta detalles no sensibles con una comunidad de defensores para mejorar la ciberseguridad colectiva.
- `Regular Updates`: Mantenga a todas las partes interesadas actualizadas regularmente durante todo el proceso de respuesta a incidentes.
- `External Review`: Considere que los especialistas en ciberseguridad de terceros validen los hallazgos.

---

# **Conclusión**

Un informe de incidente meticulosamente elaborado no es negociable después de una violación o ataque de seguridad. Estos informes ofrecen un análisis exhaustivo de lo que salió mal, qué medidas fueron efectivas, las razones detrás de ellos y futuras estrategias preventivas.