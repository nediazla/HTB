# Threat Intelligence Fundamentals

# **Definición de inteligencia contra amenazas cibernéticas**

`Cyber Threat Intelligence (CTI)` representa un activo vital en nuestro arsenal, ya que proporciona información esencial para fortalecer nuestras defensas contra los ciberataques. El objetivo principal de nuestro equipo CTI es hacer la transición de nuestras estrategias de defensa de medidas meramente reactivas a una postura más proactiva y anticipatoria. Aportan información crucial a nuestro Centro de Operaciones de Seguridad (SOC).

Cuatro principios fundamentales hacen de CTI una parte integral de nuestra estrategia de ciberseguridad:

![](https://academy.hackthebox.com/storage/modules/214/cti.png)

- `Relevance`: El mundo cibernético está inundado de diversas fuentes de información, desde publicaciones en redes sociales e informes de proveedores de seguridad hasta conocimientos compartidos de organizaciones similares. Sin embargo, el verdadero valor de esta información radica en su relevancia para nuestra organización. Por ejemplo, si se informa una vulnerabilidad en un software que nosotros o nuestras organizaciones asociadas de confianza no utilizamos, la urgencia de implementar medidas defensivas naturalmente disminuye.
- `Timeliness`: La rápida comunicación de inteligencia a nuestro equipo de defensa es crucial para la implementación de medidas de mitigación efectivas. El valor de la información se deprecia con el tiempo: los datos recién descubiertos son más valiosos y los indicadores "antiguos" pierden su relevancia, ya que es posible que el adversario ya no los utilice o que la organización afectada los haya resuelto.
- `Actionability`: Los datos analizados por un analista de CTI deberían generar información útil para nuestro equipo de defensa. Si la inteligencia no ofrece directrices claras para la acción, su valor disminuye. La inteligencia debe ser analizada hasta que proporcione información relevante, oportuna y procesable para la defensa de nuestra red. La inteligencia no procesable puede conducir a un ciclo que se perpetúa a sí mismo de análisis no productivo, a menudo denominado "cono de helado que se lame a sí mismo".
- `Accuracy`: Antes de difundir cualquier información de inteligencia, se debe verificar su exactitud. Los indicadores incorrectos, las atribuciones erróneas o las tácticas, técnicas y procedimientos (TTP) defectuosos pueden provocar una pérdida de tiempo y recursos valiosos. Si la exactitud de cualquier información es incierta, se debe etiquetar con un indicador de confianza, asegurando que nuestro equipo de defensa esté al tanto de posibles imprecisiones.

Cuando estos cuatro factores hacen sinergia, la inteligencia obtenida nos permite:

- Obtenga información sobre posibles operaciones y campañas de adversarios que podrían estar dirigidas a nuestra organización.
- Enriquezca nuestro conjunto de datos mediante análisis realizados por analistas de CTI y otros defensores de la red.
- Descubrir los TTP del adversario, lo que permitirá el desarrollo de medidas de mitigación efectivas y mejorará nuestra comprensión del comportamiento del adversario.
- Proporcionar a los tomadores de decisiones dentro de nuestra organización información pertinente para una toma de decisiones informada e impactante relacionada con las operaciones comerciales.

---

# **La diferencia entre inteligencia de amenazas y caza de amenazas**

Threat Intelligence y Threat Hunting representan dos especialidades distintas, aunque intrínsecamente interconectadas, dentro del ámbito de la ciberseguridad. Si bien cumplen funciones separadas, ambos contribuyen significativamente al desarrollo de un analista de seguridad integral. Sin embargo, es importante tener en cuenta que no se reemplazan entre sí.

Inteligencia de amenazas (predictiva): el objetivo principal aquí es anticipar los movimientos del adversario, determinar sus objetivos y discernir sus métodos de adquisición de información. El adversario tiene un objetivo específico, y como equipo involucrado en Threat Intelligence, nuestra misión es predecir:

- La ubicación del ataque previsto.
- El momento del ataque
- Las estrategias operativas que empleará el adversario.
- Los objetivos finales del adversario

Threat Hunting (reactivo y proactivo): Sí, los dos términos son opuestos, pero resumen la esencia de Threat Hunting. Un evento o incidente iniciador, ya sea que ocurra dentro de nuestra red o en una red de una industria similar, incita a nuestro equipo a lanzar una operación para determinar si un adversario está presente en la red, o si uno estaba presente y evadió la detección.

En última instancia, Threat Intelligence y Threat Hunting se refuerzan mutuamente, fortaleciendo la postura general de defensa de la red de nuestra organización. A medida que nuestro equipo de Threat Intelligence analiza las actividades de los adversarios y desarrolla perfiles integrales de adversarios, esta información se puede compartir con nuestros analistas de Threat Hunting para informar sus operaciones. Por el contrario, los hallazgos de las operaciones de Threat Hunting pueden dotar a nuestros analistas de Threat Intelligence de datos adicionales para refinar su inteligencia y mejorar la precisión de sus predicciones.

---

# **Criterios de inteligencia sobre amenazas cibernéticas**

¿Qué es lo que realmente hace que Cyber ​​Threat Intelligence (CTI) sea valioso? ¿Qué problemas resuelve? Como se mencionó anteriormente, para que la CTI sea eficaz, debe ser viable, oportuna, relevante y precisa. Estos cuatro elementos forman la base de una CTI sólida que, en última instancia, proporciona visibilidad de las operaciones del adversario. Además, una CTI bien construida genera beneficios secundarios, tales como:

- Comprensión de las amenazas a nuestra organización y entidades asociadas.
- Conocimientos potenciales sobre la red de nuestra organización.
- Mayor conciencia de problemas potenciales que pueden haber pasado desapercibidos.

Además, desde un punto de vista de liderazgo, una CTI de alta calidad ayuda a cumplir el objetivo empresarial de minimizar el riesgo tanto como sea posible. A medida que se recopila y analiza la inteligencia sobre un adversario que tiene como objetivo nuestro negocio, se permite al liderazgo evaluar adecuadamente el riesgo, formular un plan de acción de contingencia si ocurre un incidente y, en última instancia, enmarcar el problema y difundir la información de una manera coherente y significativa.

![](https://academy.hackthebox.com/storage/modules/214/cti2.png)

A medida que se recopila esta información, se transforma en inteligencia. Luego, esta inteligencia se puede clasificar en tres categorías diferentes, cada una con distintos grados de relevancia para diferentes equipos dentro de nuestra organización. Estas categorías son:

- Inteligencia Estratégica
- Inteligencia operativa
- Inteligencia táctica

En el siguiente diagrama, la intersección ideal está justo en el centro. En esta coyuntura de convergencia, el analista de Cyber ​​Threat Intelligence (CTI) está equipado para ofrecer el retrato más completo y detallado del adversario y su modus operandi.

![](https://academy.hackthebox.com/storage/modules/214/cti3.png)

`Strategic Intelligence`se caracteriza por:

- Ser consumido por ejecutivos de alto nivel, vicepresidentes y otros líderes de la empresa.
- Con el objetivo de alinear la inteligencia directamente con los riesgos de la empresa para informar las decisiones
- Proporcionar una visión general de las operaciones del adversario a lo largo del tiempo.
- Mapeo de TTP y Modus Operandi (MO) del adversario
- ¿Esforzándose por responder al ¿Quién? y ¿Por qué?
- `Example`: Un informe que contenga inteligencia estratégica podría describir la amenaza que representa APT28 (también conocido como Fancy Bear), un actor-estado-nación vinculado al gobierno ruso. Este informe podría cubrir las campañas pasadas del grupo, sus motivaciones (como el espionaje político), sus objetivos (como gobiernos, militares y organizaciones de seguridad) y estrategias a largo plazo. El informe también podría explorar cómo el grupo adapta sus tácticas y herramientas a lo largo del tiempo, basándose en datos históricos y el contexto geopolítico.

`Operational Intelligence`se caracteriza por:

- Incluyendo también los TTP de un adversario (similar a la inteligencia estratégica)
- Proporcionar información sobre campañas adversarias.
- Ofreciendo más detalles que los que se encuentran en los informes de inteligencia estratégica.
- Producido para personal directivo de nivel medio.
- Trabajando para responder al ¿Cómo? y donde?
- `Example`: Un informe que contenga inteligencia operativa puede proporcionar un análisis detallado de una campaña de ransomware realizada por el grupo REvil. Incluiría cómo el grupo obtiene acceso inicial (por ejemplo, mediante phishing o explotación de vulnerabilidades), sus tácticas de movimiento lateral (como el volcado de credenciales y la explotación de herramientas de administración de Windows) y sus métodos para ejecutar la carga útil del ransomware (tal vez fuera de horario para maximizar el daño y cifrar tantos sistemas como sea posible).

`Tactical Intelligence`se caracteriza por:

- Entrega de información procesable inmediata
- Proporcionado a los defensores de la red para una acción rápida
- Incluyendo detalles técnicos sobre ataques que han ocurrido o podrían ocurrir en un futuro próximo.
- `Example`: Un informe que contenga inteligencia táctica podría incluir direcciones IP específicas, URL o dominios vinculados a los servidores de comando y control de REvil, hashes de muestras conocidas de ransomware REvil, rutas de archivos específicas, claves de registro o mutex asociados con REvil, o incluso cadenas distintivas dentro el código ransomware. Este tipo de información puede ser utilizada directamente por las tecnologías de seguridad y los servicios de respuesta a incidentes para detectar, prevenir y responder a amenazas específicas.

Es fundamental comprender que existe cierto grado de superposición entre estos tres tipos de inteligencia. Por eso representamos la inteligencia en un diagrama de Venn. La inteligencia táctica contribuye a formar una imagen operativa y una visión estratégica. Lo contrario también es cierto.

---

# **Cómo revisar un informe de inteligencia táctica sobre amenazas**

Interpretar informes de inteligencia de amenazas cargados de inteligencia táctica e Indicadores de Compromiso (IOC) es una tarea que requiere una metodología estructurada para optimizar nuestra capacidad de respuesta como analistas de SOC o cazadores de amenazas. Profundicemos en un proceso procedimental en profundidad utilizando un escenario teórico que involucra un informe de inteligencia de amenazas sobre una elaborada campaña de malware de Emotet:

- `Comprehending the Report's Scope and Narrative`: La fase inicial de interpretación del informe implica comprender su contexto más amplio. Supongamos que nuestro informe aclara una campaña de Emotet en curso dirigida a empresas de nuestro sector. El informe puede ofrecer información a nivel macro sobre los objetivos de los atacantes y los tipos de entidades en su punto de mira. Al comprender la narrativa, podemos evaluar la pertinencia de la amenaza a nuestro propio negocio.
- `Spotting and Classifying the IOCs`: La inteligencia táctica normalmente abarca una lista de COI vinculados a la amenaza. En el contexto de nuestro escenario de Emotet, estas podrían incluir direcciones IP vinculadas a servidores de comando y control (C2), hashes de archivos de las cargas útiles de Emotet, direcciones de correo electrónico o líneas de asunto aprovechadas en campañas de phishing, URL de sitios web engañosos o registros distintos. alteraciones por parte del malware. Deberíamos dividir estos IOC en categorías para una comprensión más comprensible y resultados procesables: IOC basados ​​en red (IP, dominios), IOC basados ​​en host (hashes de archivos, claves de registro) y IOC basados ​​en correo electrónico (direcciones de correo electrónico, líneas de asunto). Además, los IOC también podrían contener nombres Mutex generados por el malware, hashes de certificados SSL, llamadas API específicas realizadas por el malware o incluso patrones en el tráfico de la red (como agentes de usuario específicos, encabezados HTTP o patrones de solicitud de DNS). Además, los IOC se pueden aumentar con datos complementarios. Por ejemplo, las direcciones IP se pueden complementar con datos de geolocalización, información de WHOIS o dominios asociados.
- `Comprehending the Attack's Lifecycle`: El informe probablemente describirá las tácticas, técnicas y procedimientos (TTP) implementados por los atacantes, correspondientemente asignados al marco MITRE ATT&CK. Para la campaña de Emotet, podría comenzar con un correo electrónico de phishing (Acceso inicial), proceder a ejecutar la carga útil (Ejecución), establecer persistencia (Persistencia), ejecutar tácticas de evasión de defensa (Evasión de defensa) y, en última instancia, exfiltrar datos o implementar herramientas secundarias. cargas útiles (Comando y Control). Comprender este ciclo de vida nos ayuda a pronosticar los movimientos del atacante y formular una respuesta eficaz.
- `Analysis and Validation of IOCs`: No todos los IOC tienen la misma utilidad o precisión. Necesitamos autenticarlos, generalmente mediante referencias cruzadas con bases de datos o fuentes de inteligencia de amenazas adicionales, como VirusTotal o OTX de AlienVault. También debemos contemplar la edad de los COI. Los más antiguos pueden no ser tan pertinentes si el atacante ha modificado su infraestructura o tácticas. Además, contextualizar las COI es fundamental para su correcta interpretación. Por ejemplo, una dirección IP empleada como servidor C2 también puede alojar sitios web legítimos debido al uso compartido de IP en entornos de nube. Los analistas también deberían considerar la confiabilidad de la fuente y si el COI ha estado en la lista blanca en el pasado. En última instancia, comprender la tasa de falsos positivos es crucial para evitar la fatiga de alerta.
- `Incorporating the IOCs into our Security Infrastructure`: Una vez autenticados, podemos integrar estos IOC en nuestras soluciones de seguridad. Esto podría implicar actualizar las reglas de firewall con direcciones IP o dominios maliciosos, incorporar hashes de archivos en nuestra solución de detección y respuesta de endpoints (EDR) o crear nuevas firmas IDS/IPS. Para los IOC basados ​​en correo electrónico, podemos actualizar nuestro portal de seguridad de correo electrónico o nuestra solución antispam. Al implementar IOC, debemos considerar el impacto potencial en las operaciones comerciales. Por ejemplo, bloquear una dirección IP podría afectar un servicio crítico para el negocio. En tales casos, podría ser más apropiado alertar en lugar de bloquear. Además, todos los cambios deben documentarse y aprobarse siguiendo los procedimientos de gestión de cambios para mantener la integridad del sistema y evitar interrupciones involuntarias.
- `Proactive Threat Hunting`: Equipados con información del informe, podemos buscar de manera proactiva signos de la amenaza de Emotet en nuestro entorno. Esto podría implicar buscar registros de conexiones de red a los servidores C2, escanear puntos finales en busca de hashes de archivos identificados o verificar registros de correo electrónico en busca de indicadores de correo electrónico de phishing. La búsqueda de amenazas no debería limitarse a la búsqueda de IOC. También deberíamos buscar señales más amplias de los TTP descritos en el informe. Por ejemplo, Emotet suele emplear PowerShell para la ejecución y evasión. Por lo tanto, podríamos buscar actividad sospechosa de PowerShell, incluso si no coincide directamente con un IOC. Este enfoque ayuda a detectar variantes de la amenaza que no están cubiertas por los IOC específicos del informe.
- `Continuous Monitoring and Learning`: Después de implementar los IOC, debemos monitorear continuamente nuestro entorno para detectar cualquier impacto. Cualquier detección debe desencadenar un proceso de respuesta a incidentes predefinido. Además, debemos utilizar la información obtenida del informe para mejorar nuestra postura de seguridad. Esto podría implicar educar a los usuarios sobre las tácticas de phishing empleadas por el grupo Emotet o mejorar nuestras reglas de detección para detectar las técnicas de evasión específicas empleadas por este malware. Si bien sin duda deberíamos aprender de cada informe, también deberíamos contribuir a la comunidad de inteligencia sobre amenazas. Si descubrimos nuevos IOC o TTP, estos deben compartirse con plataformas de inteligencia de amenazas e ISAC/ISAO (centros/organizaciones de análisis e intercambio de información) para ayudar a otras organizaciones a defenderse contra la amenaza.

Este proceso meticuloso, paso a paso, si bien se adapta a nuestro ejemplo de Emotet, se puede aplicar a cualquier informe de inteligencia de amenazas que contenga inteligencia táctica y COI. El secreto es ser sistemático, integral y proactivo en nuestro enfoque para maximizar el valor que obtenemos de estos informes.