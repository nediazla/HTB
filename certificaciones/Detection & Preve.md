# Detection & Prevention

A lo largo de este módulo, hemos dominado varias técnicas diferentes que se pueden usar a partir de un`offensive perspective`. Como probadores de penetración, también debemos preocuparnos por las mitigaciones y detecciones que se pueden implementar para ayudar a los defensores a detener este tipo de TTP. Esto es crucial ya que se espera que proporcionemos a nuestros clientes soluciones o soluciones potenciales a los problemas que encontramos y explotamos durante nuestras evaluaciones. Algunos pueden ser:

- Cambios de hardware físico
- Cambios en la infraestructura de la red
- Modificaciones para alojar líneas de base

Esta sección cubrirá algunas de estas correcciones y lo que significan para nosotros y para los que están a cargo de defender la red.

---

# **Establecer una línea de base**

Comprender todo lo presente y sucediendo en un entorno de red es vital. Como defensores, deberíamos poder rápidamente`identify`y`investigate`Cualquier host nuevo que aparezca en nuestra red, cualquier herramienta o aplicación nuevas que se instalen en hosts fuera de nuestro catálogo de aplicaciones, y cualquier tráfico de red nuevo o único generado. Una auditoría de todo lo que se enumera a continuación debe realizarse anualmente, si no cada pocos meses, para garantizar que sus registros estén actualizados. Entre algunas de las consideraciones con las que podemos comenzar se encuentran:

### **Cosas para documentar y rastrear**

- Records DNS, copias de seguridad del dispositivo de red y configuraciones DHCP
- Inventario de aplicaciones completa y actual
- Una lista de todos los hosts empresariales y su ubicación
- Usuarios que tienen permisos elevados
- Una lista de cualquier host de doble salto (más de una interfaz de red)
- Mantener un diagrama de red visual de su entorno

Junto con el seguimiento de los elementos anteriores, mantener un diagrama de red visual de su entorno actualizado puede ser altamente efectivo al solucionar problemas o responder a un incidente.[Netbrain](https://www.netbraintech.com/)es un excelente ejemplo de una herramienta que puede proporcionar esta funcionalidad y acceso interactivo a todos los electrodomésticos en el diagrama. Si queremos una forma de documentar nuestro entorno de red visualmente, podemos usar una herramienta gratuita como[diagramas.net](https://app.diagrams.net/). Por último, para nuestra línea de base, es imprescindible comprender qué activos son críticos para el funcionamiento de su organización y monitorear esos activos.

---

# **Personas, procesos y tecnología**

El endurecimiento de la red se puede organizar en las categorías*Gente*, *Proceso,*y*Tecnología*. Estas medidas de endurecimiento abarcarán el hardware, el software y los aspectos humanos de cualquier red. Comencemos con el`human` (`People`) aspecto.

### **Gente**

Incluso en el entorno más endurecido, los usuarios a menudo se consideran el enlace más débil. Hacer cumplir las mejores prácticas de seguridad para usuarios y administradores estándar evitará "victorias fáciles" para pentesteros y atacantes maliciosos. También debemos esforzarnos por mantenernos a sí mismos y a los usuarios a los que servimos educados y conscientes de las amenazas. Las medidas a continuación son una excelente manera de comenzar el proceso de asegurar el elemento humano de cualquier entorno empresarial.

### **Byod y otras preocupaciones**

Traiga su propio dispositivo (BYOD) se está volviendo frecuente en la fuerza laboral actual. Con la mayor aceptación del trabajo remoto y los arreglos de trabajo híbridos, más personas están utilizando sus dispositivos personales para realizar tareas relacionadas con el trabajo. Esto presenta riesgos únicos para las organizaciones porque sus empleados pueden conectarse con redes y recursos compartidos de la organización. La organización tiene una capacidad limitada para administrar y asegurar un dispositivo de propiedad personal, como una computadora portátil o teléfono inteligente, dejando la responsabilidad de asegurar el dispositivo en gran medida con el propietario. Si el propietario del dispositivo sigue las malas prácticas de seguridad, no solo se ponen en riesgo de compromiso, sino que ahora también pueden extender estos mismos riesgos a sus empleadores. Considere el ejemplo práctico a continuación para construir una perspectiva sobre esto:

Escenario: Nick es un gerente de logística trabajador y dedicado para InLaneFreight. Ha trabajado mucho a lo largo de los años, y la compañía confía en él lo suficiente como para permitirle trabajar desde casa tres días a la semana. Al igual que muchos empleados de LaneFreight, Nick también aprovecha la voluntad de InLaneFreight de permitir que los empleados usen sus propios dispositivos para tareas relacionadas con el trabajo en el hogar y en los entornos de red de la oficina. Nick también disfruta de los juegos de juego y, a veces, los videojuegos ilegalmente torrentes. Un juego que descargó e instaló también instaló malware que le dio a un atacante acceso remoto a su computadora portátil. Cuando Nick entra en la oficina, se conecta a la red WiFi que extiende el acceso a la red de empleados. Cualquiera puede alcanzar los controladores de dominio, las acciones de archivo, las impresoras y otros recursos de red importantes de esta red. Debido a que hay malware en el sistema de Nick, el atacante también tiene acceso a estos recursos de red y puede intentar pivotar en la red de InLaneFreight debido a las malas prácticas de seguridad de Nick en su computadora personal.

Usando`multi-factor authentication`(Algo que tiene, algo que sabe, algo que es, ubicación, etc.) son factores excelentes a considerar al implementar mecanismos de autenticación. Implementar dos o más factores para la autenticación (especialmente para cuentas administrativas y acceso) es una excelente manera de dificultar que un atacante obtenga acceso completo a una cuenta si la contraseña de un usuario o el hash se comprometen.

Además de garantizar que sus usuarios no puedan causar daño, debemos considerar nuestras políticas y procedimientos para el acceso y el control del dominio. Las organizaciones más grandes también deben considerar construir un equipo de Centro de Operación de Seguridad (SOC) o usar un`SOC as a Service`para monitorear constantemente lo que está sucediendo dentro del entorno de TI 24/7. Las tecnologías defensivas modernas han recorrido un largo camino y pueden ayudar con muchas tácticas defensivas diferentes, pero necesitamos operadores humanos para garantizar que funcionen como se supone que deben hacerlo.`Incident response`es algo en lo que aún no podemos automatizar completamente el elemento humano. Entonces tener un apropiado`incident response plan`Listo es esencial para estar preparado para una violación.

---

### **Procesos**

Mantener y hacer cumplir las políticas y procedimientos puede afectar significativamente la postura de seguridad general de una organización. Es casi imposible responsabilizar a los empleados de una organización sin políticas definidas. Hace que sea difícil responder a un incidente sin procedimientos definidos y practicados como un`disaster recovery plan`. Los elementos a continuación pueden ayudar a comenzar a definir los`processes`, `policies`, y`procedures`relacionado con la obtención de sus usuarios y entorno de red.

- Políticas y procedimientos adecuados para el monitoreo y gestión de los activos
    - Las auditorías del host, el uso de etiquetas de activos y los inventarios de activos periódicos pueden ayudar a garantizar que los hosts no se pierdan
- Políticas de control de acceso (aprovisionamiento de la cuenta de usuario/desaprobación), mecanismos de autenticación de múltiples factores
- Procesos para el aprovisionamiento y el desmantelamiento de hosts (es decir, guía de endurecimiento de seguridad de referencia, imágenes de oro)
- Procesos de gestión de cambios para documentar formalmente`who did what`y`when they did it`

### **Tecnología**

Verifique periódicamente la red para ver las configuraciones erróneas heredadas y las nuevas amenazas emergentes. A medida que se realizan cambios en un entorno, asegúrese de que no se introduzcan configuraciones erróneas comunes al prestar atención a cualquier vulnerabilidad introducida por herramientas o aplicaciones utilizadas en el entorno. Si es posible, intente parchear o mitigar esos riesgos con el entendimiento de que la tríada de la CIA es un acto de equilibrio, y la aceptación del riesgo que presenta una vulnerabilidad puede ser la mejor opción para su entorno.

---

# **Desde el exterior moviéndose**

Cuando trabajan con una organización para ayudarlos a evaluar la postura de seguridad de su entorno, puede ser útil comenzar desde el exterior y avanzar. A medida que los probadores de penetración y los profesionales de la seguridad, queremos que nuestros clientes tomen nuestros hallazgos y recomendaciones lo suficientemente en serio como para informar sus decisiones en el futuro. Queremos que entiendan que los problemas que descubrimos también pueden ser encontrados por individuos o grupos con intenciones menos honorables. Consideremos esto a través de un ejercicio mental utilizando el esquema a continuación. Siéntase libre de usar estas preguntas y consideraciones candentes para comenzar una conversación con amigos, miembros del equipo o si está solo, tome algunas notas y presente el diseño más seguro que se le ocurra:

### **Perímetro primero**

- `What exactly are we protecting?`
- `What are the most valuable assets the organization owns that need securing?`
- `What can be considered the perimeter of our network?`
- `What devices & services can be accessed from the Internet? (Public-facing)`
- `How can we detect & prevent when an attacker is attempting an attack?`
- `How can we make sure the right person &/or team receives alerts as soon as something isn't right?`
- `Who on our team is responsible for monitoring alerts and any actions our technical controls flag as potentially malicious?`
- `Do we have any external trusts with outside partners?`
- `What types of authentication mechanisms are we using?`
- `Do we require Out-of-Band (OOB) management for our infrastructure. If so, who has access permissions?`
- `Do we have a Disaster Recovery plan?`

Al considerar estas preguntas con respecto al perímetro, podemos enfrentar la realidad de que una organización tiene infraestructura que podría basarse en locales y/o en la nube. La mayoría de las organizaciones en el día moderno operan infraestructuras de nubes híbridas. Esto significa que algunas de las tecnologías utilizadas por las organizaciones pueden estar ejecutadas en infraestructura de red y servidor propiedad de la organización, y algunas pueden ser alojadas por un proveedor de nube de terceros (AWS, Azure, GCP, etc.).

- Interfaz externa en un firewall
    - Capacidades de firewall de próxima generación
        - Bloqueo de conexiones sospechosas por IP
        - Asegurar que solo las personas aprobadas se conecten a VPN
        - Construyendo la capacidad de desconectar rápidamente las conexiones sospechosas sin interrumpir las funciones comerciales

---

### **Consideraciones internas**

Muchas de las preguntas que solicitamos consideraciones externas se aplican a nuestro entorno interno. Hay algunas diferencias; Sin embargo, hay muchas rutas diferentes para garantizar la defensa exitosa de nuestras redes. Consideremos lo siguiente:

- `Are any hosts that require exposure to the internet properly hardened and placed in a DMZ network?`
- `Are we using Intrusion Detection and Prevention systems within our environment?`
- `How are our networks configured? Are different teams confined to their own network segments?`
- `Do we have separate networks for production and management networks?`
- `How are we tracking approved employees who have remote access to admin/management networks?`
- `How are we correlating the data we are receiving from our infrastructure defenses and end-points?`
- `Are we utilizing host-based IDS, IPS, and event logs?`

Nuestra mejor oportunidad de detectar, detenerse y potencialmente prevenir un ataque a menudo depende de nuestra capacidad para mantener la visibilidad dentro de nuestro entorno. Una implementación adecuada de SIEM para correlacionar y analizar nuestros registros de huéspedes e infraestructura puede ser muy útil. Combine eso con una segmentación de red adecuada, y se vuelve infinitamente más difícil para un atacante obtener un punto de apoyo y pivote a los objetivos. Cosas simples, como garantizar que Steve de HR no pueda ver o acceder a la infraestructura de red, como interruptores y enrutadores o paneles de administración para sitios web internos, puede evitar el uso de usuarios estándar para el movimiento lateral.

---

# **Descomposición de la inglete**

Como una mirada diferente a esto, hemos desglosado las principales acciones que practicamos en este módulo y los controles mapeados basados en el TTP y una etiqueta MITER. Cada etiqueta corresponde con una sección del[Enterprise ATT & CK Matrix](https://attack.mitre.org/tactics/enterprise/)encontrado aquí. Cualquier etiqueta marcada como`TA`corresponde a una táctica general, mientras que una etiqueta marcada como`T###`es una técnica que se encuentra en la matriz bajo tácticas.

| **TTP** | **Etiqueta de inglete** | **Descripción** |
| --- | --- | --- |
| `External Remote Services` | T1133 | Tenemos opciones de prevención al tratar con el uso de servicios remotos externos.`First`, tener un firewall adecuado para segmentar nuestro entorno del resto de Internet y controlar el flujo del tráfico es imprescindible.`Second`, deshabilitar y bloquear cualquier protocolo de tráfico interno para llegar al mundo siempre es una buena práctica.`Third`, usando una VPN o algún otro mecanismo que requiere que un host sea`logically`Ubicada dentro de la red antes de que obtenga acceso a esos servicios, es una excelente manera de garantizar que no esté filtrando datos que no debería. |
| `Remote Services` | T1021 | La autenticación multifactor puede contribuir en gran medida al intentar mitigar el uso no autorizado de servicios remotos como SSH y RDP. Incluso si se tomara la contraseña de un usuario, el atacante aún necesitaría una forma de adquirir la cadena de su MFA de elección. Limitar las cuentas de los usuarios con permisos de acceso remoto y separar las tareas en cuanto a quién puede acceder de forma remota a qué partes de una red pueden recorrer un largo camino. Utilizar su firewall en red y el firewall incorporado en sus hosts para limitar las conexiones entrantes/salientes para servicios remotos es una victoria fácil para los defensores. Detendrá el intento de conexión a menos que sea de una red interna o externa autorizada. Cuando se trata de dispositivos de infraestructura como enrutadores e interruptores, solo exponer los servicios y puertos de gestión remota a una red fuera de banda (OOB) es una mejor práctica que siempre debe seguirse. Hacer esto asegura que cualquier persona que haya comprometido las redes empresariales no pueda simplemente saltar del host de un usuario regular a la infraestructura. |
| `Use of Non-Standard Ports` | T1571 | Esta técnica puede ser difícil de atrapar. Los atacantes a menudo usan un protocolo común como`HTTP`o`HTTPS`para comunicarse con su entorno. Es difícil ver lo que está sucediendo, especialmente con el uso de HTTPS, pero los emparejamientos de protocolos como estos con un puerto no estándar (44`4`en lugar de 44`3`, por ejemplo) puede darnos propinas a algo sospechoso. Los atacantes a menudo intentarán trabajar de esta manera, por lo que tener un sólido`baseline`De lo que los puertos/protocolos se usan comúnmente en su entorno pueden ser muy útiles al tratar de detectar lo malo. El uso de alguna forma de un sistema de prevención o detección de intrusos de red también puede ayudar a detectar y cerrar el tráfico potencialmente malicioso. |
| `Protocol Tunneling` | T1572 | Este es un problema interesante de abordar. Muchos actores utilizan túneles de protocolo para ocultar sus canales de comunicación. A menudo veremos cosas como que practicamos en este módulo (túnel en otro tráfico a través de un túnel SSH) e incluso el uso de protocolos como DNS para pasar instrucciones de fuentes externas a un anfitrión interno a la red. Es imprescindible tomar el tiempo para bloquear qué puertos y protocolos pueden hablar en/salir de sus redes. Si tiene un dominio en ejecución y está alojando un servidor DC y DNS, sus hosts no deben tener ninguna razón para alcanzar externamente para la resolución de nombres. No permitir la resolución DNS de la web (excepto a hosts específicos como el servidor DNS) puede ayudar con un problema como este. Tener una buena solución de monitoreo también puede observar los patrones de tráfico y lo que se conoce como`Beaconing`. Incluso si el tráfico está encriptado, es posible que veamos las solicitudes que ocurren en un patrón con el tiempo. Este es un rasgo común de un canal C2. |
| `Proxy Use` | T1090 | El uso de un punto de proxy es un lugar común entre los actores de amenaza. Muchos usarán un punto de proxy o distribuirán su tráfico en múltiples hosts para que no expongan directamente su infraestructura. Al usar un proxy, no hay una conexión directa del entorno de la víctima al anfitrión del atacante en un momento dado. La detección y prevención del uso de proxy es un poco difícil, ya que toma un conocimiento íntimo del flujo neto común dentro de su entorno. La ruta más efectiva es mantener una lista de dominios y direcciones IP permitidas/bloqueadas. Cualquier cosa que no se permita explícitamente se bloqueará hasta que deje pasar el tráfico. |
| `LOTL` | N / A | Puede ser difícil detectar a un atacante mientras utilizan los recursos disponibles. Aquí es donde tener una línea de base del tráfico de red y el comportamiento del usuario es útil. Si sus defensores entienden cómo es el día a día normal para su red, tiene la oportunidad de detectar lo anormal. Observar los shells de comandos y utilizar una solución EDR y AV configurada correctamente contribuirá en gran medida a proporcionarle visibilidad. Tener alguna forma de monitoreo y registro de redes de redes en un sistema común como un SIEM que los defensores verifican, contribuirá en gran medida a ver un ataque en las etapas iniciales en lugar de después del hecho. |