# Detección y prevención

¡Estamos en la pendiente descendente ahora! Tomemos un descanso de nuestro negocio súper espía de infiltrar a los anfitriones y echemos un vistazo al lado defensivo. Esta sección explora formas de detectar capas activas, buscar cargas útiles en un host y sobre el tráfico de red, y cómo estos ataques pueden ofuscarse para evitar nuestras defensas.

---

# **Escucha**

Cuando se trata de buscar e identificar capas activas, entrega y ejecución de carga útil, e intentos potenciales de subvertir nuestras defensas, tenemos muchas opciones diferentes para utilizar para detectar y responder a estos eventos. Antes de hablar sobre fuentes de datos y herramientas que podemos usar, tomemos un segundo para hablar sobre el[Marco de Mitre ATT y CK](https://attack.mitre.org/)y defina las técnicas y tácticas que utilizan los atacantes. El`ATT&CK Framework`Según lo definido por Miter, es "`a globally-accessible knowledge base of adversary tactics and techniques based on real-world observations`."

### **Marco ATT y CK**

![](https://academy.hackthebox.com/storage/modules/115/attack-framework.png)

Teniendo en cuenta el marco, tres de las técnicas más notables que podemos vincular con los conchas y las cargas útiles se enumeran a continuación en la tabla con descripciones.

---

### **Tácticas y técnicas de Mitre AtT & CK notables:**

| **Táctica / técnica** | **Descripción** |
| --- | --- |
| [Acceso inicial](https://attack.mitre.org/techniques/T1190/) | Los atacantes intentarán obtener acceso inicial comprometiendo un host o servicio de orientación pública, como aplicaciones web, servicios mal configurados, como SMB o protocolos de autenticación, y/o errores en un host de orientación pública que introduce una vulnerabilidad. Esto a menudo se hace en alguna forma de host de bastión y proporciona al atacante un punto de apoyo en la red, pero aún no es el acceso completo. Para obtener más información sobre el acceso inicial, especialmente a través de aplicaciones web, consulte el[Owasp Top Ten](https://owasp.org/www-project-top-ten/)o lea más en el marco Mitre ATT y CK. |
| [Ejecución](https://attack.mitre.org/tactics/TA0002) | Esta técnica depende del código suministrado y plantado por un atacante que se ejecuta en el anfitrión de la víctima.`The Shells & Payloads`El módulo se centra principalmente en esta táctica. Utilizamos muchas cargas útiles, métodos de entrega y soluciones de script de shell para acceder a un host. Esto puede ser cualquier cosa, desde la ejecución de comandos dentro de nuestro navegador web para obtener la ejecución y el acceso en una aplicación web, emitiendo una línea de una línea de PowerShell a través de PSEXEC, aprovechando una explotación publicada públicamente o un día cero en conjunción con un marco como Metasploit, o cargando un archivo a un host a través de muchos protocolos diferentes y llamando a recibir una llamada. |
| [Comando y control](https://attack.mitre.org/tactics/TA0011) | Comando y control (`C2`) se puede considerar como la culminación de nuestros esfuerzos dentro de este módulo. Obtenemos acceso a un host y establecemos algún mecanismo para el acceso continuo y/o interactivo a través de la ejecución del código, luego utilizamos ese acceso para realizar las acciones de los objetivos dentro de la red de víctimas. El uso de puertos y protocolos estándar dentro de la red de víctimas para emitir comandos y recibir la salida de la víctima es común. Esto puede aparecer como cualquier cosa, desde el tráfico web normal sobre HTTP/S, comandos emitidos a través de otros protocolos externos comunes como DNS y NTP, e incluso el uso de aplicaciones comunes permitidas como Slack, Discord o MS equipos para emitir comandos y recibir verificaciones. C2 puede tener varios niveles de sofisticación que varían desde los canales de texto claros básicos como NetCat hasta la utilización de protocolos cifrados y ofuscados junto con rutas de tráfico complejas a través de proxies, redirectores y VPN. |

---

# **Eventos para tener en cuenta:**

- `File uploads`: Especialmente con aplicaciones web, las cargas de archivos son un método común para adquirir un shell en un host además de la ejecución de comandos directos en el navegador. Preste atención a los registros de aplicaciones para determinar si alguien ha subido algo potencialmente malicioso. El uso de firewalls y antivirus puede agregar más capas a su postura de seguridad alrededor del sitio. Cualquier anfitrión expuesto a Internet desde su red debe estar suficientemente endurecido y monitoreado.
- `Suspicious non-admin user actions`: Buscar cosas simples como los usuarios normales que emiten comandos a través de Bash o CMD pueden ser un indicador significativo de compromiso. ¿Cuándo fue la última vez que un usuario promedio, y mucho menos un administrador, tuvo que emitir el comando?`whoami`en un anfitrión? Los usuarios que se conectan a una acción en otro host en la red sobre SMB que no es una participación de infraestructura normal también puede ser sospechoso. Este tipo de interacción generalmente es el host final al servidor de infraestructura, no end host to End host. Habilitar medidas de seguridad, como el registro de todas las interacciones del usuario, el registro de PowerShell y otras características que toman nota cuando se utiliza una interfaz de shell le proporcionará más información.
- `Anomalous network sessions`: Los usuarios tienden a tener un patrón que siguen para la interacción de red. Visitan los mismos sitios web, usan las mismas aplicaciones y, a menudo, realizan esas acciones varias veces al día, como un reloj. El registro y el análisis de los datos de Netflow pueden ser una excelente manera de detectar el tráfico de red anómalo. Mirando cosas como las principales conversadoras o visitas únicas al sitio, observar un latido en un puerto no estándar (como 4444, el puerto predeterminado utilizado por Meterpreter) y monitorear cualquier intento de inicio de sesión remoto o solicitudes de obtención / publicación a granel en pocas cantidades de tiempo pueden ser indicadores de compromiso o intento de explotación. El uso de herramientas como monitores de red, registros de firewall y SIEMS puede ayudar a dar un poco de orden al caos que es el tráfico de red.

---

# **Establecer la visibilidad de la red**

Al igual que identificarse y luego usar varios shells y cargas útiles,`detection` & `prevention`Requiere una comprensión detallada de los sistemas y el entorno de red general que está tratando de proteger. Siempre es esencial tener buenas prácticas de documentación para que las personas responsables de mantener el medio ambiente seguro puedan tener una visibilidad constante de los dispositivos, datos y flujo de tráfico en los entornos que administran. Desarrollar y mantener diagramas de topología de red visual puede ayudar a visualizar el flujo de tráfico de red. Herramientas más nuevas como[netbrain](https://www.netbraintech.com/)puede ser bueno para investigar mientras combinan diagramación visual que se puede lograr con herramientas como[Dibujo.io](https://draw.io/), documentación y gestión remota. Las topologías de red visual interactiva le permiten interactuar con los enrutadores, los firewalls de red, los aparatos ID/IPS, los conmutadores y los hosts (clientes). Herramientas como esta son más comunes de usar, ya que puede ser un desafío para mantener la visibilidad de la red actualizada, especialmente en entornos más grandes que están en constante crecimiento.

Algunos proveedores de dispositivos de red como Cisco Meraki, Ubiquiti, Check Point y Palo Alto están construyendo visibilidad de la capa 7 (como la capa 7 del modelo OSI) en sus dispositivos de red y mueven las capacidades de administración a los controladores de red basados en la nube. Esto significa que los administradores inician sesión en un portal web para administrar todos los dispositivos de red en el entorno. A menudo se proporciona un tablero visual con estos controladores de red basados en la nube, lo que hace que sea más fácil tener un`baseline`del uso del tráfico, protocolos de red, aplicaciones y tráfico entrante y saliente. Tener y comprender la línea de base de su red hará que cualquier desviación de la norma sea extremadamente visible. Cuanto más rápido pueda reaccionar y clasificar cualquier problema potencial, menos tiempo para posibles fugas, destrucción de datos o peor.

Tenga en cuenta que si una carga útil se ejecuta con éxito, deberá comunicarse a través de la red, por lo que es por eso que la visibilidad de la red es esencial dentro del contexto de los shells y las cargas útiles. Tener un dispositivo de seguridad de red capaz de[Inspección de paquetes profundos](https://en.wikipedia.org/wiki/Deep_packet_inspection)a menudo puede actuar como un antivirus para la red. Algunas de las cargas útiles que discutimos podrían ser detectadas y bloqueadas a nivel de red si se ejecutan con éxito en los hosts. Esto es especialmente fácil de detectar si el tráfico no está encriptado. Cuando usamos NetCat en las secciones de enlace e inverso, el tráfico que pasaba entre la fuente y el destino (destino) fue`not encrypted`. Alguien podría capturar ese tráfico y ver todos los comandos que enviamos entre nuestro cuadro de ataque y el objetivo, como se ve en los ejemplos a continuación.

Esta imagen muestra netflow entre dos hosts con frecuencia y en un puerto sospechoso (`4444`). Podemos decir que es el tráfico TCP básico, por lo que si tomamos medidas e inspeccionamos un poco, podemos ver lo que está sucediendo.

---

### **Tráfico sospechoso .. en texto claro**

![](https://academy.hackthebox.com/storage/modules/115/pcap-4444.png)

Observe ahora que ese mismo tráfico se ha ampliado, y podemos ver que alguien está usando`net`Comandos para crear un nuevo usuario en este host.

---

### **Siguiendo el tráfico**

![](https://academy.hackthebox.com/storage/modules/115/follow-sus.png)

Este es un excelente ejemplo de acceso básico y ejecución de comandos para ganar persistencia a través de la adición de un usuario al host. Independientemente del nombre`hacker`Al ser utilizado, si el registro de la línea de comandos está junto con los datos de NetFlow, podemos decir rápidamente que el usuario está realizando acciones potencialmente maliciosas y clasificación este evento para determinar si ha ocurrido un incidente o si esto es solo algún administrador que juega. Un dispositivo de seguridad moderno puede detectar, alertar y evitar más comunicaciones de red de ese host utilizando una inspección de paquetes profundos.

Hablando de antivirus, discutamos un poco la detección y protección del dispositivo final.

---

# **Protección de dispositivos finales**

`End devices`son los dispositivos que se conectan al "final" de una red. Esto significa que son la fuente o el destino de la transmisión de datos. Algunos ejemplos de dispositivos finales serían:

- Estaciones de trabajo (computadoras de empleados)
- Servidores (que proporciona varios servicios en la red)
- Impresoras
- Almacenamiento adjunto de la red (NAS)
- Cámaras
- Televisores inteligentes
- Altavoces inteligentes

Deberíamos priorizar la protección de este tipo de dispositivos, especialmente aquellos que ejecutan un sistema operativo con un`CLI`que se puede acceder de forma remota. La misma interfaz que facilita la administración y automatizar las tareas en un dispositivo puede hacer que sea un buen objetivo para los atacantes. Tan simple como parece, tener antivirus instalado y habilitado es un gran comienzo. El vector de ataque exitoso más común además de la configuración errónea es el elemento humano. Todo lo que se necesita es que un usuario haga clic en un enlace o abra un archivo, y puede verse comprometido. Tener monitoreo y alerta en sus dispositivos finales puede ayudar a detectar y evitar problemas antes de que ocurran.

En`Windows`sistemas,`Windows Defender`(También conocido como Windows Security o Microsoft Defender) está presente en la instalación y debe dejarse habilitado. Además, asegurar que el firewall de defensor quede habilitado con todos los perfiles (dominio, privado y público) que quedan en marcha. Solo haga excepciones para las solicitudes aprobadas basadas en un[proceso de gestión de cambios](https://www.atlassian.com/itsm/change-management). Establecer[gestión de parches](https://www.rapid7.com/fundamentals/patch-management/)Estrategia (si no se está estableciendo) para garantizar que todos los hosts reciban actualizaciones poco después de que Microsoft las publique. Todo esto se aplica a los servidores que alojan recursos y sitios web compartidos también. Aunque puede ralentizar el rendimiento, AV en un servidor puede evitar la ejecución de una carga útil y el establecimiento de una sesión de shell con un sistema de atacante malicioso.

---

# **Posibles mitigaciones:**

Considere la lista a continuación al considerar qué implementaciones puede establecer para mitigar muchos de estos vectores o exploits.

- `Application Sandboxing`: Al sandboxing sus aplicaciones que están expuestas al mundo, puede limitar el alcance del acceso y el daño que un atacante puede realizar si encuentran una vulnerabilidad o configuración errónea en la aplicación.
- `Least Privilege Permission Policies`: Limitar los permisos que los usuarios tienen puede contribuir en gran medida a ayudar a detener el acceso o el compromiso no autorizados. ¿Un usuario ordinario necesita acceso administrativo para realizar sus tareas diarias? ¿Qué pasa con el administrador del dominio? En realidad no, ¿verdad? Asegurar las políticas de seguridad y los permisos adecuados a menudo obstaculizarán, si no, detendrán un ataque directo.
- `Host Segmentation & Hardening`: Endurecer adecuadamente los hosts y la segregación de los hosts que requieran exposición a Internet pueden ayudar a garantizar que un atacante no pueda subir y moverse lateralmente a su red si obtienen acceso a un host de límite. Siguiendo las guías de endurecimiento de Stig y los hosts de colocación como servidores web, servidores VPN, etc., en un segmento de red DMZ o 'cuarentena' detendrá ese tipo de acceso y movimiento lateral.
- `Physical and Application Layer Firewalls`: Los firewalls pueden ser herramientas poderosas si se implementan adecuadamente. Las reglas entrantes y salientes adecuadas que solo permiten el tráfico establecido por primera vez desde su red, en los puertos aprobados para sus aplicaciones, y negar el tráfico entrante desde las direcciones de su red u otro espacio de IP prohibido puede paralizar muchos shells de enlace e inverso. Agrega un salto en la cadena de red, y las implementaciones de la red, como la traducción de direcciones de red (NAT), pueden romper la funcionalidad de una carga útil de shell si no se tiene en cuenta.

---

# **Sume todo**

Estas protecciones y mitigaciones no son los fines, todos para detener los ataques. Se requiere una fuerte postura de seguridad y una estrategia de defensa en la edad actual. Adaptar un enfoque de defensa en profundidad de su postura de seguridad ayudará a obstaculizar a los atacantes y garantizar que la fruta de bajo paso no pueda aprovecharse fácilmente.