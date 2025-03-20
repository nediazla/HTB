# Metodología de enumeración

Los procesos complejos deben tener una metodología estandarizada que nos ayude a mantener nuestros rodamientos y evitar omitir cualquier aspecto por error. Especialmente con la variedad de casos que los sistemas objetivo nos pueden ofrecer, es casi impredecible cómo se debe diseñar nuestro enfoque. Por lo tanto, la mayoría de los probadores de penetración siguen sus hábitos y los pasos con los que se sienten más cómodos y familiarizados. Sin embargo, esta no es una metodología estandarizada, sino un enfoque basado en la experiencia.

Sabemos que las pruebas de penetración y, por lo tanto, la enumeración, es un proceso dinámico. En consecuencia, hemos desarrollado una metodología de enumeración estática para pruebas de penetración externa e interna que incluye dinámicas libres y permite una amplia gama de cambios y adaptaciones al entorno dado. Esta metodología está anidada en 6 capas y representa, metafóricamente, límites que intentamos pasar con el proceso de enumeración. Todo el proceso de enumeración se divide en tres niveles diferentes:

| **`Infrastructure-based enumeration`** | **`Host-based enumeration`** | **`OS-based enumeration`** |
| --- | --- | --- |

![](https://academy.hackthebox.com/storage/modules/112/enum-method3.png)

Nota: Los componentes de cada capa que se muestra representan las categorías principales y no una lista completa de todos los componentes para buscar. Además, debe mencionarse aquí que la primera y la segunda capa (presencia en Internet, puerta de enlace) no se aplica al intranet, como una infraestructura de activo directorio. Las capas de infraestructura interna se cubrirán en otros módulos.

Considere estas líneas como algún tipo de obstáculo, como un muro, por ejemplo. Lo que hacemos aquí es mirar a su alrededor para averiguar dónde está la entrada, o la brecha que podemos encajar, o escalar para acercarse a nuestro objetivo. Teóricamente, también es posible pasar por la pared de cabeza, pero muy a menudo, sucede que el lugar que hemos roto la brecha con mucho esfuerzo y tiempo con la fuerza no nos trae mucho porque no hay entrada en este punto de la pared para pasar al siguiente muro.

Estas capas están diseñadas de la siguiente manera:

| **Capa** | **Descripción** | **Categorías de información** |
| --- | --- | --- |
| `1. Internet Presence` | Identificación de la presencia en Internet e infraestructura accesible externamente. | Dominios, subdominios, VHosts, ASN, bloques de red, direcciones IP, instancias de nubes, medidas de seguridad |
| `2. Gateway` | Identifique las posibles medidas de seguridad para proteger la infraestructura externa e interna de la Compañía. | Firewalls, DMZ, IPS/IDS, EDR, proxies, NAC, segmentación de red, VPN, CloudFlare |
| `3. Accessible Services` | Identificar interfaces y servicios accesibles que se alojan externamente o internamente. | Tipo de servicio, funcionalidad, configuración, puerto, versión, interfaz |
| `4. Processes` | Identifique los procesos internos, las fuentes y los destinos asociados con los servicios. | PID, datos procesados, tareas, fuente, destino |
| `5. Privileges` | Identificación de los permisos y privilegios internos a los servicios accesibles. | Grupos, usuarios, permisos, restricciones, entorno |
| `6. OS Setup` | Identificación de los componentes internos y la configuración de sistemas. | Tipo de sistema operativo, nivel de parche, configuración de red, entorno del sistema operativo, archivos de configuración, archivos privados confidenciales |

Nota importante: El aspecto humano y la información que pueden obtener los empleados que usan OSINT se han eliminado de la capa de "presencia en Internet" por simplicidad.

Finalmente podemos imaginar toda la prueba de penetración en forma de laberinto en el que tenemos que identificar los huecos y encontrar la forma de meternos de la manera más rápida y efectiva posible. Este tipo de laberinto puede verse algo así:

![](https://academy.hackthebox.com/storage/modules/112/pentest-labyrinth.png)

Los cuadrados representan los huecos/vulnerabilidades.

Como probablemente ya hemos notado, podemos ver que encontraremos un espacio y muy probablemente varios. El hecho interesante y muy común es que no todas las brechas que encontramos pueden llevarnos al interior. Todas las pruebas de penetración son limitadas en el tiempo, pero siempre debemos tener en cuenta que una creencia de que casi siempre hay una forma de entrar. Incluso después de una prueba de penetración de cuatro semanas, no podemos decir 100% que no hay más vulnerabilidades. Alguien que ha estado estudiando la empresa durante meses y analizarla probablemente tendrá una comprensión mucho mayor de las aplicaciones y la estructura de las que pudimos ganar en las pocas semanas que pasamos en la evaluación. Un excelente y reciente ejemplo de esto es el[CyberAt Attle on Solarwinds](https://www.rpc.senate.gov/policy-papers/the-solarwinds-cyberattack), que sucedió no hace mucho tiempo. Esta es otra excelente razón para una metodología que debe excluir tales casos.

Supongamos que se nos ha pedido que realicemos una prueba de penetración externa de "caja negra". Una vez que todos los elementos del contrato necesarios se hayan cumplido completamente, nuestra prueba de penetración comenzará en el momento especificado.

---

# **Capa No.1: Presencia en Internet**

La primera capa que tenemos que pasar es la capa de "presencia en Internet", donde nos enfocamos en encontrar los objetivos que podemos investigar. Si el alcance en el contrato nos permite buscar hosts adicionales, esta capa es aún más crítica que solo para objetivos fijos. En esta capa, utilizamos diferentes técnicas para encontrar dominios, subdominios, bloques de red y muchos otros componentes e información que presentan la presencia de la empresa y su infraestructura en Internet.

`The goal of this layer is to identify all possible target systems and interfaces that can be tested.`

---

# **Capa No.2: puerta de enlace**

Aquí tratamos de comprender la interfaz del objetivo accesible, cómo está protegido y dónde se encuentra en la red. Debido a la diversidad, diferentes funcionalidades y algunos procedimientos particulares, entraremos en más detalles sobre esta capa en otros módulos.

`The goal is to understand what we are dealing with and what we have to watch out for.`

---

# **Capa No.3: Servicios accesibles**

En el caso de los servicios accesibles, examinamos cada destino para todos los servicios que ofrece. Cada uno de estos servicios tiene un propósito específico que ha sido instalado por una razón particular por el administrador. Cada servicio tiene ciertas funciones, que, por lo tanto, también conducen a resultados específicos. Para trabajar de manera efectiva con ellos, necesitamos saber cómo funcionan. De lo contrario, necesitamos aprender a entenderlos.

`This layer aims to understand the reason and functionality of the target system and gain the necessary knowledge to communicate with it and exploit it for our purposes effectively.`

Esta es la parte de la enumeración con la que trataremos principalmente en este módulo.

---

# **Capa No.4: procesos**

Cada vez que se ejecuta un comando o función, se procesan los datos, ya sea ingresados por el usuario o generado por el sistema. Esto inicia un proceso que tiene que realizar tareas específicas, y tales tareas tienen al menos una fuente y un objetivo.

`The goal here is to understand these factors and identify the dependencies between them.`

---

# **Capa No.5: Privilegios**

Cada servicio se ejecuta a través de un usuario específico en un grupo particular con permisos y privilegios definidos por el administrador o el sistema. Estos privilegios a menudo nos proporcionan funciones que pasan por alto los administradores. Esto a menudo sucede en las infraestructuras de Active Directory y muchos otros entornos de administración específicos de casos y servidores donde los usuarios son responsables de múltiples áreas de administración.

`It is crucial to identify these and understand what is and is not possible with these privileges.`

---

# **Capa No.6: Configuración del sistema operativo**

Aquí recopilamos información sobre el sistema operativo real y su configuración utilizando el acceso interno. Esto nos brinda una buena visión general de la seguridad interna de los sistemas y refleja las habilidades y capacidades de los equipos administrativos de la compañía.

`The goal here is to see how the administrators manage the systems and what sensitive internal information we can glean from them.`

---

# **Metodología de enumeración en la práctica**

Una metodología resume todos los procedimientos sistemáticos para obtener conocimiento dentro de los límites de un objetivo dado. Es importante tener en cuenta que una metodología no es una guía paso a paso, sino que, como lo indica la definición, un resumen de los procedimientos sistemáticos. En nuestro caso, la metodología de enumeración es el enfoque sistemático para explorar un objetivo dado.

La forma en que se identifican los componentes individuales y la información obtenida en esta metodología es un aspecto dinámico y creciente que cambia constantemente y, por lo tanto, puede diferir. Un excelente ejemplo de esto es utilizar herramientas de recolección de información de servidores web. Hay innumerables herramientas diferentes para esto, y cada una de ellas tiene un enfoque específico y, por lo tanto, ofrece resultados individuales que difieren de otras aplicaciones. El objetivo, sin embargo, es el mismo. Por lo tanto, la colección de herramientas y comandos no es parte de la metodología real, sino de una hoja de trucos a la que podemos referirnos utilizando los comandos y herramientas enumeradas en casos dados.