# Penetration Testing Overview

La TI es una parte integral de casi todas las empresas. La cantidad de datos críticos y confidenciales almacenados en los sistemas de TI crece constantemente, al igual que la dependencia del funcionamiento ininterrumpido de los sistemas de TI en uso. Por lo tanto, los ataques contra las redes corporativas, la interrupción de la disponibilidad del sistema y otras formas de causar daños significativos a una empresa (como los ataques de ransomware) son cada vez más comunes. La información importante de la empresa obtenida a través de brechas de seguridad y ciberataques puede venderse a competidores, filtrarse en foros públicos o usarse para otros fines nefastos. Las fallas del sistema se desencadenan deliberadamente porque son cada vez más difíciles de contrarrestar.

Una "prueba de penetración" (Pentest) es un intento de ataque organizado, dirigido y autorizado para probar la infraestructura de TI y sus defensores para determinar su susceptibilidad a las vulnerabilidades de seguridad de TI. Una prueba de penetración utiliza métodos y técnicas que utilizan los atacantes reales. Como evaluadores de penetración, aplicamos diversas técnicas y análisis para medir el impacto que una vulnerabilidad particular o una cadena de vulnerabilidades puede tener en la confidencialidad, integridad y disponibilidad de los sistemas y datos de TI de una organización.

- `Una prueba de penetración tiene como objetivo descubrir e identificar TODAS las vulnerabilidades en los sistemas bajo investigación y mejorar la seguridad de los sistemas evaluados.`

Otras evaluaciones, como una `evaluación de equipo rojo`, pueden basarse en escenarios y centrarse solo en las vulnerabilidades aprovechadas para alcanzar un objetivo final específico (es decir, acceder a la bandeja de entrada de correo electrónico del director ejecutivo u obtener una bandera plantada en un servidor crítico).

### **Gestión de riesgos**

En general, también forma parte de la "gestión de riesgos" de una empresa. El objetivo principal de la gestión de riesgos de seguridad informática es identificar, evaluar y mitigar cualquier riesgo potencial que pueda dañar la confidencialidad, integridad y disponibilidad de los sistemas de información y los datos de una organización y reducir el riesgo general a un nivel aceptable. Esto incluye identificar amenazas potenciales, evaluar sus riesgos y tomar las medidas necesarias para reducirlos o eliminarlos. Esto se hace implementando los controles y políticas de seguridad adecuados, incluido el control de acceso, el cifrado y otras medidas de seguridad. Al tomarse el tiempo para gestionar adecuadamente los riesgos de seguridad de los sistemas informáticos de una organización, es posible garantizar que los datos se mantengan seguros y protegidos.

Sin embargo, no podemos eliminar todos los riesgos. Sigue existiendo la naturaleza del riesgo inherente de una violación de seguridad que está presente incluso cuando la organización ha tomado todas las medidas razonables para gestionar el riesgo. Por lo tanto, algunos riesgos permanecerán. El riesgo inherente es el nivel de riesgo que está presente incluso cuando se implementan los controles de seguridad adecuados. Las empresas pueden aceptar, transferir, evitar y mitigar los riesgos de varias formas. Por ejemplo, pueden contratar un seguro para cubrir determinados riesgos, como desastres naturales o accidentes. Al firmar un contrato, también pueden transferir sus riesgos a otra parte, como un proveedor de servicios externo. Además, pueden implementar medidas preventivas para reducir la probabilidad de que se produzcan determinados riesgos y, si se producen, pueden poner en marcha procesos para minimizar su impacto. Por último, pueden utilizar instrumentos financieros, como derivados, para reducir las consecuencias económicas de riesgos específicos. Todas estas estrategias pueden ayudar a las empresas a gestionar eficazmente sus riesgos.

Durante un pentest, preparamos una documentación detallada sobre los pasos seguidos y los resultados obtenidos. Sin embargo, es responsabilidad del cliente o del operador de sus sistemas bajo investigación rectificar las vulnerabilidades encontradas. Nuestro papel es el de asesores de confianza para informar sobre las vulnerabilidades, los pasos detallados de reproducción y proporcionar recomendaciones de remediación adecuadas, pero no entramos y aplicamos parches o hacemos cambios de código, etc. Es importante tener en cuenta que un pentest no supervisa la infraestructura o los sistemas de TI, sino una instantánea momentánea del estado de seguridad. Una declaración a este respecto debería reflejarse en nuestro informe de prueba de penetración.

### **Evaluaciones de vulnerabilidad**

El "análisis de vulnerabilidad" es un término genérico que puede incluir evaluaciones de vulnerabilidad o seguridad y pruebas de penetración. A diferencia de una prueba de penetración, las evaluaciones de vulnerabilidad o seguridad se realizan utilizando herramientas puramente automatizadas. Los sistemas se verifican en busca de problemas conocidos y vulnerabilidades de seguridad ejecutando herramientas de escaneo como [Nessus](https://www.tenable.com/products/nessus), [Qualys](https://www.qualys.com/apps/vulnerability-management/), [OpenVAS](https://www.openvas.org/) y similares. En la mayoría de los casos, estas verificaciones automatizadas no pueden adaptar los ataques a las configuraciones del sistema de destino. Por eso, las pruebas manuales realizadas por un evaluador humano experimentado son esenciales.

Por otro lado, una prueba de penetración es una combinación de pruebas/validaciones automatizadas y manuales y se realiza después de una recopilación de información exhaustiva, en la mayoría de los casos, manual. Se adapta y ajusta individualmente al sistema que se está probando. La planificación, ejecución y selección de las herramientas utilizadas son mucho más complejas en un pentest. Tanto las pruebas de penetración como otras evaluaciones de seguridad solo se pueden llevar a cabo después de un acuerdo mutuo entre la empresa contratante y la organización que emplea al evaluador de penetración. Esto se debe a que las pruebas individuales y las actividades realizadas durante el pentest podrían tratarse como delitos penales si el evaluador no tiene autorización escrita explícita para atacar los sistemas del cliente. La organización que encarga la prueba de penetración solo puede solicitar pruebas contra sus propios activos. Si están utilizando terceros para alojar sitios web u otra infraestructura, deben obtener la aprobación escrita explícita de estas entidades en la mayoría de los casos. Empresas como Amazon ya no requieren autorización previa para probar ciertos servicios según esta [política](https://aws.amazon.com/security/penetration-testing/), si una empresa está utilizando AWS para alojar parte o la totalidad de su infraestructura. Esto varía de un proveedor a otro, por lo que siempre es mejor confirmar la propiedad de los activos con el cliente durante la fase de alcance y verificar si los terceros que utilizan requieren un proceso de solicitud por escrito antes de realizar cualquier prueba.

Un pentest exitoso requiere una cantidad considerable de organización y preparación. Debe haber un modelo de proceso sencillo que podamos seguir y, al mismo tiempo, adaptarnos a las necesidades de nuestros clientes, ya que cada entorno con el que nos encontremos será diferente y tendrá sus propios matices. En algunos casos, podemos trabajar con clientes que nunca han experimentado un pentest antes, y tenemos que ser capaces de explicar este proceso en detalle para asegurarnos de que tengan una comprensión clara de nuestras actividades planificadas, y los ayudamos a delimitar la evaluación con precisión.

En principio, los empleados no son informados sobre las próximas pruebas de penetración. Sin embargo, los gerentes pueden decidir informar a sus empleados sobre las pruebas. Esto se debe a que los empleados tienen derecho a saber cuándo no tienen expectativas de privacidad.

Porque nosotros, como probadores de penetración, podemos encontrar datos personales, como nombres, direcciones, salarios y mucho más. Lo mejor que podemos hacer para defender la [Ley de Protección de Datos](https://www.gov.uk/data-protection) es mantener esta información privada. Otro ejemplo sería que obtengamos acceso a una base de datos con números de tarjetas de crédito, nombres y códigos CVV. Por ello, recomendamos a nuestros clientes que mejoren y cambien las contraseñas lo antes posible y cifren los datos en la base de datos.

---

# **Testing Methods**

An essential part of the process is the starting point from which we should perform our pentest. Each pentest can be performed from two different perspectives:

- `External` or `Internal`

### **Prueba de penetración externa**

Muchas pruebas de penetración se realizan desde una perspectiva externa o como usuario anónimo en Internet. La mayoría de los clientes quieren asegurarse de estar lo más protegidos posible contra ataques a su perímetro de red externo. Podemos realizar pruebas desde nuestro propio host (con suerte, utilizando una conexión VPN para evitar que nuestro ISP nos bloquee) o desde un VPS. A algunos clientes no les importará el sigilo, mientras que otros solicitarán que procedamos lo más silenciosamente posible y nos acerquemos a los sistemas de destino para evitar que los firewalls y los sistemas IDS/IPS los bloqueen y evitar que se active una alarma. Es posible que soliciten un enfoque sigiloso o "híbrido" en el que nos volvamos gradualmente "más ruidosos" para probar sus capacidades de detección. En última instancia, nuestro objetivo aquí es acceder a los hosts externos, obtener datos confidenciales o acceder a la red interna.

### **Prueba de penetración interna**

A diferencia de una prueba de penetración externa, una prueba de penetración interna es cuando realizamos pruebas desde dentro de la red corporativa. Esta etapa puede ejecutarse después de haber penetrado con éxito en la red corporativa mediante el pentest externo o partiendo de un supuesto escenario de vulneración. Los pentest internos también pueden acceder a sistemas aislados sin acceso a Internet, lo que normalmente requiere nuestra presencia física en las instalaciones del cliente.

---

# **Tipos de pruebas de penetración**

No importa cómo empecemos la prueba de penetración, el tipo de prueba de penetración juega un papel importante. Este tipo determina cuánta información se pone a nuestra disposición. Podemos reducir estos tipos a los siguientes:

| **Type** | **Information Provided** |
| --- | --- |
| `Blackbox` | `Minimal`. Sólo se proporciona la información esencial, como direcciones IP y dominios. |
| `Greybox` | `Extended`.En este caso, se nos proporciona información adicional, como URL específicas, nombres de host, subredes y similares. |
| `Whitebox` | `Máximo`. Aquí se nos revela todo. Esto nos da una visión interna de toda la estructura, lo que nos permite preparar un ataque utilizando información interna. Es posible que nos den configuraciones detalladas, credenciales de administrador, código fuente de la aplicación web, etc. |
| `Red-Teaming` | Puede incluir pruebas físicas e ingeniería social, entre otras cosas. Puede combinarse con cualquiera de los tipos anteriores. |
| `Purple-Teaming` | Puede combinarse con cualquiera de los tipos anteriores, pero se centra en trabajar en estrecha colaboración con los defensores. |

Cuanto menos información nos proporcionen, más largo y complejo será el enfoque. Por ejemplo, para una prueba de penetración de caja negra, primero debemos obtener una descripción general de qué servidores, hosts y servicios están presentes en la infraestructura, especialmente si se prueban redes enteras. Este tipo de reconocimiento puede llevar una cantidad considerable de tiempo, especialmente si el cliente ha solicitado un enfoque más sigiloso para las pruebas.

---

# **Tipos de entornos de prueba**

Además del método de prueba y el tipo de prueba, otra consideración es qué se va a probar, lo que se puede resumir en las siguientes categorías:

|  |  |  |  |  |
| --- | --- | --- | --- | --- |
| Network | Web App | Mobile | API | Thick Clients |
| IoT | Cloud | Source Code | Physical Security | Employees |
| Hosts | Server | Security Policies | Firewalls | IDS/IPS |

Es importante tener en cuenta que estas categorías a menudo se pueden mezclar. Todos los componentes de prueba enumerados pueden estar incluidos según el tipo de prueba que se realizará. Ahora cambiaremos de tema y cubriremos el proceso de penetración en profundidad para ver cómo se desglosa cada fase y cómo depende de la anterior.