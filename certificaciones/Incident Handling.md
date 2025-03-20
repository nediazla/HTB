# Incident Handling

# **Definición y alcance del manejo de incidentes**

El manejo de incidentes (IH) se ha convertido en una parte importante de la capacidad defensiva de una organización contra el cibercrimen. Si bien se implementan constantemente medidas de protección para prevenir o reducir la cantidad de incidentes de seguridad, una capacidad de manejo de incidentes es sin lugar a dudas una necesidad para cualquier organización que no pueda permitirse comprometer la confidencialidad, integridad o disponibilidad de sus datos. Algunas organizaciones optan por implementar esta capacidad internamente, mientras que otras dependen de proveedores externos para que las respalden, de forma continua o cuando sea necesario. Antes de sumergirnos en el mundo de los incidentes de seguridad, definamos algunos términos y establezcamos una comprensión común de ellos.

Un `event` Es una acción que ocurre en un sistema o red. Ejemplos de eventos son:

- Un usuario enviando un correo electrónico
- Un clic del ratón
- Un firewall que permite una solicitud de conexión.

Un `incident` es un evento con una consecuencia negativa. Un ejemplo de incidente es una caída del sistema. Otro ejemplo es el acceso no autorizado a datos confidenciales. También pueden producirse incidentes por catástrofes naturales, cortes de energía, etc.

No existe una definición única de lo que es un incidente de seguridad de TI y, por lo tanto, varía entre organizaciones. Definimos un incidente de seguridad de TI como un evento con una clara intención de causar daño que se realiza contra un sistema informático. Ejemplos de incidentes son:

- Robo de datos
- robo de fondos
- Acceso no autorizado a datos
- Instalación y uso de malware y herramientas de acceso remoto.

`Incident handling is a clearly defined set of procedures to manage and respond to security incidents in a computer or network environment.`

Es importante tener en cuenta que la gestión de incidentes no se limita únicamente a los incidentes de intrusión.

Otros tipos de incidentes, como los causados ​​por personas internas maliciosas, problemas de disponibilidad y pérdida de propiedad intelectual, también entran dentro del alcance del manejo de incidentes. Un plan integral de manejo de incidentes debe abordar varios tipos de incidentes y proporcionar medidas adecuadas para identificarlos, contenerlos, erradicarlos y recuperarse de ellos para restaurar las operaciones comerciales normales de la manera más rápida y eficiente posible.

Tenga en cuenta que puede no quedar inmediatamente claro que un evento es un incidente hasta que se realice una investigación inicial. Dicho esto, hay algunos eventos sospechosos que deben tratarse como incidentes a menos que se demuestre lo contrario.

---

# **Valor del manejo de incidentes y notas genéricas**

Los incidentes de seguridad de TI frecuentemente implican el compromiso de datos personales y comerciales y, por lo tanto, es crucial responder de manera rápida y efectiva. En algunos incidentes, el impacto puede limitarse a unos pocos dispositivos, mientras que en otros puede verse comprometida una gran parte del medio ambiente. Un gran beneficio de tener un equipo de manejo de incidentes (a menudo denominado equipo de respuesta a incidentes) que se encargue de los eventos es que una fuerza laboral capacitada responderá sistemáticamente y, por lo tanto, se tomarán las medidas adecuadas. De hecho, el objetivo de dichos equipos es minimizar el robo de información o la interrupción de servicios que el incidente está provocando. Esto se logra realizando investigaciones y pasos de remediación, que discutiremos con más profundidad en breve. En general, las decisiones que se tomen antes, durante y después de un incidente afectarán su impacto.

Debido a que diferentes incidentes tendrán diferentes impactos en la organización, debemos comprender la importancia de la priorización. Los incidentes con mayor gravedad requerirán atención inmediata y la asignación de recursos para ellos, mientras que otros con una calificación inferior también pueden requerir una investigación inicial para comprender si en realidad estamos tratando con un incidente de seguridad de TI.

El equipo de gestión de incidentes está dirigido por un gestor de incidentes. Esta función a menudo se asigna a un gerente de SOC, CISO/CIO o proveedor externo (confiable), y esta persona generalmente también tiene la capacidad de dirigir otras unidades de negocios. El gestor de incidentes debe poder obtener información o tener el mandato de exigir a cualquier empleado de la organización que realice una actividad de manera oportuna, si es necesario. El administrador de incidentes es el único punto de comunicación que rastrea las actividades realizadas durante la investigación y su estado de finalización.

Uno de los recursos más utilizados en el manejo de incidentes es [Guía de manejo de incidentes de seguridad informática del NIST](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf). El documento tiene como objetivo ayudar a las organizaciones a mitigar los riesgos de los incidentes de seguridad informática proporcionando directrices prácticas para responder a los incidentes de forma eficaz y eficiente.