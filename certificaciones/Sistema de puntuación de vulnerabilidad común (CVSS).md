
Hay varias formas de calificar o calcular las clasificaciones de gravedad de las vulnerabilidades. El[Sistema de puntuación de vulnerabilidad común (CVSS)](https://www.first.org/cvss/)es un estándar de la industria para realizar estos cálculos. Muchas herramientas de escaneo aplicarán estos puntajes a cada hallazgo como parte de los resultados del escaneo, pero es importante que comprendamos cómo se derivan estos puntajes en caso de que necesitemos calcular uno a mano o justificar la puntuación aplicada a una vulnerabilidad dada. El CVSS a menudo se usa junto con el llamado[Dread de Microsoft](https://en.wikipedia.org/wiki/DREAD_(risk_assessment_model)). `DREAD`es un sistema de evaluación de riesgos desarrollado por Microsoft para ayudar a los profesionales de seguridad de TI evaluar la gravedad de las amenazas y vulnerabilidades de seguridad. Se utiliza para realizar un análisis de riesgos mediante el uso de una escala de 10 puntos para evaluar la gravedad de las amenazas y vulnerabilidades de seguridad. Con esto, calculamos el riesgo de una amenaza o vulnerabilidad basada en cinco factores principales:

- Daño potencial
- Reproducibilidad
- Explotabilidad
- Usuarios afectados
- Descubrimiento

El modelo es esencial para la estrategia de seguridad de Microsoft y se utiliza para monitorear, evaluar y responder a amenazas de seguridad y vulnerabilidades en los productos de Microsoft. También sirve como referencia para que los profesionales y gerentes de seguridad de TI realicen su evaluación de riesgos y la priorización de las amenazas de seguridad y las vulnerabilidades.

---

# **Anotación de gravedad**

El sistema CVSS ayuda a clasificar la gravedad asociada con un problema y permite a las organizaciones priorizar los problemas basados en la calificación. La puntuación de CVSS consiste en el`exploitability and impact`de un problema. El`exploitability`Las medidas consisten en`access vector`, `access complexity`, y`authentication`. El`impact`Las métricas consisten en el`CIA triad`, incluido`confidentiality`, `integrity`, y`availability`.

![](https://academy.hackthebox.com/storage/modules/108/graphics/VulnerabilityAssessment_Diagram_08.png)

*Adaptado del gráfico original encontrado[aquí](https://www.first.org/cvss/v3-1/media/MetricGroups.svg).*

---

# **Grupo métrico base**

El grupo métrico base CVSS representa las características de vulnerabilidad y consiste en`exploitability`métricas y`impact`métrica.

### **Métricas de explotabilidad**

Las métricas de explotabilidad son una forma de evaluar los medios técnicos necesarios para explotar el problema utilizando las métricas a continuación:

- Vector de ataque
- Complejidad de ataque
- Se requieren privilegios
- Interacción de usuario

### **Métricas de impacto**

Las métricas de impacto representan las repercusiones de explotar con éxito un problema y lo que se ve afectado en un entorno, y se basa en la tríada de la CIA. La tríada de la CIA es un acrónimo de`Confidentiality`, `Integrity`, y`Availability`.

![](https://academy.hackthebox.com/storage/modules/108/graphics/cia_triad.png)

`Confidentiality Impact`se relaciona con la obtención de información y garantizar que solo las personas autorizadas tengan acceso. Por ejemplo, un valor de alto gravedad sería en el caso de un atacante que robe contraseñas o claves de cifrado. Un valor de bajo severidad se relacionaría con un atacante que tome información que puede no ser un activo vital para una organización.

`Integrity Impact`se relaciona con la información que no se cambia o manipula para mantener la precisión. Por ejemplo, una alta gravedad sería si un atacante modificara archivos comerciales cruciales en el entorno de una organización. Un valor de bajo severidad sería si un atacante no pudiera controlar específicamente el número de archivos cambiados o modificados.

`Availability Impact`se relaciona con tener información fácilmente alcanzable para los requisitos comerciales. Por ejemplo, un alto valor sería si un atacante causara que un entorno no estuviera completamente disponible para los negocios. Un valor bajo sería si un atacante no pudiera negar por completo el acceso a los activos comerciales y los usuarios aún pudieran acceder a algunos activos de la organización.

---

# **Grupo métrico temporal**

El`Temporal Metric Group`Detalla la disponibilidad de hazañas o parches con respecto al problema.

### **Explotar la madurez del código**

El`Exploit Code Maturity`La métrica representa la probabilidad de que un problema sea explotado en función de la facilidad de las técnicas de explotación. Hay varios valores métricos asociados con esta métrica, incluida`Not Defined`, `High`, `Functional`, `Proof-of-Concept`, y`Unproven`.

Un valor 'no definido' se relaciona con omitir esta métrica en particular. Un valor 'alto' representa una exploit que funciona constantemente para el problema y es fácilmente identificable con herramientas automatizadas. Un valor funcional indica que hay un código de exploit disponible para el público. Una prueba de concepto demuestra que hay un código de explotación POC disponible, pero requeriría cambios para que un atacante explote el problema con éxito.

### **Nivel de remediación**

El`Remediation level`se usa para identificar la priorización de una vulnerabilidad. Los valores métricos asociados con esta métrica incluyen`Not Defined`, `Unavailable`, `Workaround`, `Temporary Fix`, y`Official Fix`.

Un valor 'no definido' se relaciona con omitir esta métrica en particular. Un valor 'no disponible' indica que no hay un parche disponible para la vulnerabilidad. Un valor de 'solución' indica una solución no oficial liberada hasta un parche oficial del proveedor. Una 'solución temporal' significa que un proveedor oficial ha proporcionado una solución temporal, pero aún no ha lanzado un parche para el problema. Una 'solución oficial' indica que un proveedor ha lanzado un parche oficial para el problema para el público.

### **Informar confianza**

`Report Confidence`representa la validación de la vulnerabilidad y cuán precisos son los detalles técnicos del problema. Los valores métricos asociados con esta métrica incluyen`Not Defined`, `Confirmed`, `Reasonable`, y`Unknown`.

Un valor 'no definido' se relaciona con omitir esta métrica en particular. Un valor 'confirmado' indica que hay varias fuentes con información detallada que confirman la vulnerabilidad. Un valor 'razonable' indica que las fuentes han publicado información sobre la vulnerabilidad. Sin embargo, no hay una confianza completa de que alguien lograra el mismo resultado debido a la falta de detalles de reproducir la exploit para el problema.

---

# **Grupo métrico ambiental**

El grupo métrico ambiental representa la importancia de la vulnerabilidad de una organización, teniendo en cuenta la tríada de la CIA.

### **Métricas base modificadas**

El`Modified Base metrics`Representar las métricas que pueden modificarse si la organización afectada considera un riesgo más significativo en la confidencialidad, la integridad y la disponibilidad para su organización. Los valores asociados con esta métrica son`Not Defined`, `High`, `Medium`, y`Low`.

Un valor 'no definido' indicaría omitir esta métrica. Un valor 'alto' significaría que uno de los elementos de la tríada de la CIA tendría efectos astronómicos en la organización y los clientes en general. Un valor 'medio' indicaría que uno de los elementos de la tríada de la CIA tendría efectos significativos en la organización y los clientes en general. Un valor 'bajo' significaría que uno de los elementos de la tríada de la CIA tendría efectos mínimos en la organización y los clientes en general.

---

# **Cálculo de gravedad de CVSS**

El cálculo de una puntuación CVSS V3.1 tiene en cuenta todas las métricas discutidas en esta sección. La base de datos nacional de vulnerabilidad tiene una calculadora disponible para el público[aquí](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator).

### **Ejemplo de cálculo de CVSS**

Por ejemplo, para la vulnerabilidad de ejecución del código remoto de Windows Print Spooler, las métricas base CVSS es 8.8. Puede hacer referencia a los valores de cada valor métrico[aquí](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-34527).

---

# **Siguientes pasos**

A continuación, discutiremos cómo se clasifican las vulnerabilidades de una manera estándar que las herramientas de escaneo pueden usar para incluir una referencia externa a la vulnerabilidad particular.

[Anterior](https://academy.hackthebox.com/module/108/section/1027) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/108/section/1229)