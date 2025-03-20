# Scenario

Somos probadores de penetración trabajando para`CAT-5 Security`. Después de algunos compromisos exitosos con el equipo, los miembros más senior quieren ver qué tan bien podemos hacer una evaluación por nuestra cuenta. El líder del equipo nos envió el siguiente correo electrónico que detallaba lo que necesitamos lograr.

### **Correo electrónico de tareas**

![](https://academy.hackthebox.com/storage/modules/143/scenario-email.png)

Este módulo nos permitirá practicar nuestras habilidades (tanto anteriores como recién acuñadas) con estas tareas. La evaluación final para este módulo es la ejecución de`two`Pruebas de penetración interna contra la compañía InlaneFreight. Durante estas evaluaciones, trabajaremos a través de una prueba de penetración interna que simula desde una posición de violación externa y una segunda que comienza con un cuadro de ataque dentro de la red interna como los clientes a menudo solicitan. Completar las evaluaciones de habilidades significa la finalización exitosa de las tareas mencionadas en el documento de alcance y el correo electrónico de tarea anterior. Al hacerlo, demostraremos una comprensión firme de muchos conceptos automatizados y manuales de ataque publicitario y enumeración, conocimiento y experiencia con una amplia gama de herramientas y la capacidad de interpretar datos recopilados de un entorno publicitario para tomar decisiones críticas para avanzar en la evaluación. El contenido en este módulo está destinado a cubrir los conceptos de enumeración del núcleo necesarios para que cualquiera tenga éxito en realizar pruebas de penetración interna en entornos de Active Directory. También cubriremos muchas de las técnicas de ataque más comunes en gran profundidad mientras trabajamos a través de algunos conceptos más avanzados como un imprimador para el material centrado en AD que se cubrirán en módulos más avanzados.

A continuación encontrará un documento de alcance completo para el compromiso que contiene toda la información pertinente proporcionada por el cliente.

---

# **Alcance de la evaluación**

La siguiente`IPs`, `hosts`, y`domains`definido a continuación conforma el alcance de la evaluación.

### **En alcance para la evaluación**

| **Rango/dominio** | **Descripción** |
| --- | --- |
| `INLANEFREIGHT.LOCAL` | Dominio del cliente para incluir servicios publicitarios y web. |
| `LOGISTICS.INLANEFREIGHT.LOCAL` | Subdominio del cliente |
| `FREIGHTLOGISTICS.LOCAL` | Compañía subsidiaria propiedad de InLaneFreight. Fideicomiso forestal externo con inlanefreight.local |
| `172.16.5.0/23` | Subred interna en el alcance. |
|  |  |

### **Fuera del alcance**

- `Any other subdomains of INLANEFREIGHT.LOCAL`
- `Any subdomains of FREIGHTLOGISTICS.LOCAL`
- `Any phishing or social engineering attacks`
- `Any other IPS/domains/subdomains not explicitly mentioned`
- `Any types of attacks against the real-world inlanefreight.com website outside of passive enumeration shown in this module`

---

# **Métodos utilizados**

Los siguientes métodos están autorizados para evaluar InLaneFreight y sus sistemas:

### **Recopilación de información externa (comprobaciones pasivas)**

La recopilación de información externa está autorizada para demostrar los riesgos asociados con la información que se puede recopilar sobre la empresa de Internet. Para simular un ataque del mundo real, CAT-5 y sus evaluadores llevarán a cabo información externa que se recopila desde una perspectiva anónima en Internet sin información proporcionada de antemano con respecto a la integridad fuera de lo que se proporciona dentro de este documento.

CAT-5 realizará una enumeración pasiva para descubrir información que puede ayudar con las pruebas internas. Las pruebas emplearán varios grados de recopilación de información de recursos de código abierto para identificar datos de accesibles públicos que pueden representar un riesgo para infringir y ayudar con la prueba de penetración interna. No se realizarán una enumeración activa, escaneos de puertos o ataques contra direcciones IP del "mundo real" orientados a Internet o el sitio web ubicado en`https://www.inlanefreight.com`.

### **Prueba interna**

La parte de evaluación interna está diseñada para demostrar los riesgos asociados con las vulnerabilidades en los hosts y servicios internos (`Active Directory specifically`) intentando emular a los vectores de ataque desde dentro del área de operaciones de Inlanefreight. El resultado permitirá que la vida sea evaluada los riesgos de las vulnerabilidades internas y el impacto potencial de una vulnerabilidad explotada con éxito.

Para simular un ataque del mundo real, CAT-5 llevará a cabo la evaluación desde una perspectiva interna no confiable sin información anticipada fuera de lo que se proporciona en esta documentación y se descubre a partir de pruebas externas. Las pruebas comenzarán desde una posición anónima en la red interna con el objetivo de obtener credenciales de usuario de dominio, enumerando el dominio interno, ganando un punto de apoyo y moviéndose lateral y verticalmente para lograr el compromiso de todos los dominios internos en el alcance. Los sistemas informáticos y las operaciones de red no se interrumpirán intencionalmente durante la prueba.

### **Prueba de contraseña**

Los archivos de contraseña capturados de los dispositivos InLaneFreight, o proporcionados por la organización, pueden cargarse en estaciones de trabajo fuera de línea para el descifrado y utilizar para obtener más acceso y lograr los objetivos de evaluación. En ningún momento se revelará un archivo de contraseña capturado o las contraseñas descifradas a las personas que no participan oficialmente en la evaluación. Todos los datos se almacenarán de forma segura en los sistemas de propiedad y aprobado CAT-5 y se conservarán por un período de tiempo definido en el contrato oficial entre CAT-5 e InLaneFreight.

---

Proporcionamos la documentación de alcance anterior para que nos acostumbremos a ver este estilo de documentación. A medida que avanzamos a través de nuestras carreras INFOSEC, especialmente en el lado ofensivo, será común recibir documentos de alcance y reglas de compromiso (ROE) que describen este tipo de información.

---

# **El escenario está establecido**

Ahora que tenemos nuestro alcance claramente definido para este módulo, podemos sumergirnos para explorar la enumeración de Active Directory y los vectores de ataque. Ahora, sumergamos para realizar una enumeración externa pasiva contra InlaneTright.

[Anterior](https://academy.hackthebox.com/module/143/section/1517)