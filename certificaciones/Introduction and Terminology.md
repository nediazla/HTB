# Introduction and Terminology

# **¿Qué es Active Directory?**

`Active Directory` (`AD`) es un servicio de directorio para entornos empresariales de Windows que Microsoft lanzó oficialmente en 2000 con Windows Server 2000. Microsoft ha mejorado incrementalmente AD con la versión de cada nueva versión del sistema operativo del servidor. Basado en los Protocolos X.500 y LDAP que lo hicieron antes (que todavía se utilizan de alguna forma hoy), AD es una estructura jerárquica distribuida que permite la administración centralizada de los recursos de una organización, incluidos usuarios, computadoras, grupos, dispositivos de red y Archivo acciones, políticas grupales, dispositivos y fideicomisos. AD proporciona funcionalidades de autenticación, contabilidad y autorización dentro de un entorno empresarial de Windows. También permite a los administradores administrar los permisos y el acceso a los recursos de la red.

Active Directory está tan extendido que es por un margen la gestión de identidad y acceso más utilizada (`IAM`) Solución en todo el mundo. Por esta razón, la gran mayoría de las aplicaciones empresariales se integran y operan sin problemas con Active Directory. Active Directory es el servicio más crítico en cualquier empresa. Un compromiso de un entorno de Active Directory significa acceso sin restricciones a todos sus sistemas y datos, violando su `CIA` (`Confidentiality`, `Integrity`, y `Availability`). Los investigadores descubren y divulgan constantemente vulnerabilidades en AD. a través de estas vulnerabilidades, los actores de amenaza pueden utilizar malware conocido como ransomware para mantener el rehén de datos de una organización para el rescate realizando operaciones criptográficas (`encryption`) Por lo tanto, lo hace inútil hasta que pagan una tarifa para comprar una clave de descifrado (`not advised`) u obtener la clave de descifrado con la ayuda de profesionales de seguridad de TI. Sin embargo, si pensamos, un compromiso de Active Directory significa el compromiso de todas las aplicaciones, sistemas y datos en lugar de un solo sistema o servicio.

Veamos las vulnerabilidades reveladas públicamente durante los últimos tres años (2020 a 2022). Microsoft tiene más de 3000, y alrededor de 9000 desde 1999, lo que significa un increíble crecimiento de investigación y vulnerabilidades en los últimos años. La práctica más aparente para mantener segura activa directorio es garantizar que sean adecuados `Patch Management` está en su lugar, ya que Patch Management está planteando desafíos para las organizaciones de todo el mundo. Para este módulo, asumiremos que la gestión del parche se realiza bien (la gestión adecuada del parche es crucial para la capacidad de resistir un compromiso) y centrarnos en otros ataques y vulnerabilidades que podemos encontrar. Nos centraremos en exhibir ataques que abusan de configuraciones erróneas comunes y características de activo, especialmente los que son muy comunes/familiares pero increíblemente difíciles de eliminar. Además, las protecciones discutidas aquí tienen como objetivo armarnos para el futuro, ayudándonos a crear una higiene cibernética adecuada. Si estas pensando `Defence in depth`, `Network segmentation`, y similares, entonces estás en el camino correcto.

Si esta es la primera vez que aprende sobre Active Directory o escucha estos Términos, consulte el [Introducción a Active Directory](https://academy.hackthebox.com/module/details/74) Módulo para una mirada más profunda a la estructura y función de AD, objetos AD, etc. y también [Active Directory - Enumeración y ataques](https://academy.hackthebox.com/module/details/143) para fortalecer su conocimiento y obtener una visión general de algunos ataques comunes.

---

# **Refresco**

Para asegurarnos de que estemos familiarizados con los conceptos básicos, revisemos una actualización rápida de los términos.

A `domain` es un grupo de objetos que comparten la misma base de datos de anuncios, como usuarios o dispositivos.

A `tree` es uno o más dominios agrupados. Piense en esto como los dominios `test.local`, `staging.test.local`, y `preprod.test.local`, que estará en el mismo árbol debajo `test.local`. Múltiples árboles pueden existir en esta notación.

A `forest` es un grupo de múltiples árboles. Este es el nivel más alto, que se compone de todos los dominios.

`Organizational Units` (`OU`) son contenedores Active Directory que contienen grupos de usuarios, computadoras y otros OU.

`Trust`Se puede definir como acceso entre recursos para obtener permiso/acceso a recursos en otro dominio.

`Domain Controller` es (generalmente) el administrador del Active Directory utilizado para configurar todo el Directorio. El papel del controlador de dominio es proporcionar autenticación y autorización a diferentes servicios y usuarios. En Active Directory, el controlador de dominio tiene la máxima prioridad y tiene la mayor autoridad/privilegios.

`Active Directory Data Store`Contiene archivos y procesos de base de datos que almacenan y administra información de directorio para usuarios, servicios y aplicaciones. Active Directory Data Store contiene el archivo`NTDS.DIT`, el archivo más crítico dentro de un entorno publicitario; Los controladores de dominio lo almacenan en el `%SystemRoot%\NTDS` carpeta.

A `regular AD user account` Sin privilegios adicionales se pueden utilizar para enumerar la mayoría de los objetos contenidos dentro de AD, incluidos, entre otros::

- `Domain Computers`
- `Domain Users`
- `Domain Group Information`
- `Default Domain Policy`
- `Domain Functional Levels`
- `Password Policy`
- `Group Policy Objects`(GPOS)
- `Kerberos Delegation`
- `Domain Trusts`
- `Access Control Lists`(ACLS)

Aunque la configuración de AD permite que este comportamiento predeterminado se modifique/no permita, sus implicaciones pueden resultar en un desglose completo de aplicaciones, servicios y Active Directory en sí.

`LDAP` es un protocolo que los sistemas en el entorno de red usan para comunicarse con Active Directory. El controlador (s) de dominio ejecuta LDAP y escucha constantemente las solicitudes de la red.

`Authentication`En los entornos de Windows:

- Nombre de usuario/contraseña, almacenado o transmitido como hash de contraseña (`LM`, `NTLM`, `NetNTLMv1`/`NetNTLMv2`).
- `Kerberos` Tickets (Implementación de Microsoft del Protocolo Kerberos). Kerberos actúa como un tercero de confianza, trabajando con un controlador de dominio (DC) para autenticar a los clientes que intentan acceder a los servicios. El flujo de trabajo de autenticación de Kerberos gira en torno a los boletos que sirven como prueba de identidad criptográfica que los clientes intercambian entre sí, servicios y DC.
- `Authentication over LDAP`. La autenticación se permite a través del nombre de usuario/contraseña tradicional o los certificados de usuario o computadora.

`Key Distribution Center` (`KDC`): Un servicio Kerberos instalado en un DC que crea boletos. Los componentes del KDC son el servidor de autenticación (AS) y el servidor de devolución de boletos (TGS).

`Kerberos Tickets`son fichas que sirven como prueba de identidad (creada por el KDC):

- `TGT` es una prueba de que el cliente envió información válida del usuario al KDC.
- `TGS`se crea para cada servicio al cliente (con un TGT válido) quiere acceder.

`KDC key` es una clave de cifrado que demuestra que el TGT es válido. AD crea la tecla KDC de la contraseña de hash de la `KRBTGT` cuenta, la primera cuenta creada en un dominio AD. Aunque es un usuario deshabilitado, KRBTGT tiene el propósito vital de almacenar secretos que son claves generadas al azar en forma de hashes de contraseña. Es posible que nunca sepa lo que representa el valor de contraseña real (incluso si intentamos configurarlo en un valor conocido, AD lo anulará automáticamente a uno aleatorio).

Cada dominio contiene los grupos `Domain admins` y `Administrators`, los grupos más privilegiados en amplio acceso. Por defecto, AD agrega que los miembros de los administradores de dominio son administradores en todas las máquinas unidas por dominio y, por lo tanto, otorgan los derechos de iniciar sesión. Si bien el grupo de "administradores" del dominio solo puede iniciar sesión en controladores de dominio de forma predeterminada, pueden administrar cualquier objeto de Active Directory (por ejemplo, todos los servidores y, por lo tanto, asignarse los derechos para iniciar sesión). El dominio más alto en un bosque también contiene un objeto, el grupo`Enterprise Admins`, que tiene permisos sobre todos los dominios en el bosque.

Los grupos predeterminados en Active Directory son muy privilegiados y tienen un riesgo oculto. Por ejemplo, considere el grupo `Account Operators`. Al preguntarles a los administradores de anuncios cuál es la razón para asignarlo a los usuarios/súper usuarios, responderán que facilita el trabajo de la 'mesa de servicio', ya que pueden restablecer las contraseñas de los usuarios. En lugar de crear un nuevo grupo y delegar ese derecho específico a las unidades de organización que contienen cuentas de usuario, violan el principio de menor privilegio y pone en peligro a todos los usuarios. Posteriormente, esto incluirá una ruta de escalada de operadores de cuentas a administradores de dominio, el más común es a través de las cuentas de usuario 'MSOL_' que Azure AD Connect crea tras la instalación. Estas cuentas se colocan en el contenedor predeterminado de 'usuarios', donde los 'operadores de cuentas' pueden modificar los objetos de usuario.

Es esencial resaltar que Windows tiene múltiples tipos de inicio de sesión: 'cómo' los usuarios inician sesión en una máquina, que puede ser, por ejemplo, interactiva, mientras que un usuario está físicamente presente en un dispositivo o remotamente a través de RDP. Los tipos de inicio de sesión son esenciales para saber porque dejarán un "rastro" atrás en los sistemas accedidos. Esta traza es el nombre de usuario y la contraseña utilizados. Como regla general, los tipos de inicio de sesión, excepto 'Inicio de sesión de red, Tipo 3', deje credenciales en el sistema autenticado y conectado. Microsoft proporciona una lista completa de tipos de inicio de sesión[aquí](https://learn.microsoft.com/en-us/windows-server/identity/securing-privileged-access/reference-tools-logon-types).

Para interactuar con Active Directory, que vive en controladores de dominio, debemos hablar su idioma, LDAP. Cualquier consulta se realiza enviando un mensaje específicamente elaborado en LDAP a un controlador de dominio, como obtener información del usuario y la membresía de un grupo. Al principio de su vida, Microsoft se dio cuenta de que LDAP no es un lenguaje "bonito", y lanzaron herramientas gráficas que pueden presentar datos en una interfaz amigable y convertir "clics del mouse" en consultas LDAP. Microsoft desarrolló el`Remote Server Administration Tools`(RSAT), permitiendo la capacidad de interactuar con Active Directory localmente en el controlador de dominio o de forma remota desde otro objeto de computadora. Las herramientas más populares son `Active Directory Users and Computers` (que permite ver objetos de visualización/mudanza/edición/creación de accesibles como usuarios, grupos y computadoras) y `Group Management Policy` (que permite la creación y modificación de las políticas grupales).

Los puertos de red importantes en cualquier entorno de Windows incluyen (memorizarlos es enormemente beneficioso):

- 53:`DNS`.
- 88: `Kerberos`.
- 135: `WMI`/`RPC`.
- 137-139 y 445:`SMB`.
- 389 y 636: `LDAP`.
- 3389: `RDP`
- 5985 y 5896:`PowerShell Remoting` (`WinRM`)

---

# **Vista del mundo real**

Cada organización, que ha (intentada) en algún momento aumentar su madurez, ha pasado por ejercicios que clasifican sus sistemas. La clasificación define el `importance` de cada sistema para el negocio, como `ERP`, `CRM`, y `backups`. Una empresa se basa en esto para cumplir con éxito con sus objetivos y es significativamente diferente de una organización a otra. En Active Directory, cualquier roles, servicios y características adicionales que se 'agregan' además de lo que sale de la caja debe clasificarse. Esta clasificación es necesaria para garantizar que establezcamos la barra para la cual el servicio, si se ve comprometido, presenta un riesgo de escalada hacia el resto de Active Directory. En esta vista de diseño, debemos asegurarnos de que cualquier servicio que permita una escalada directa (o indirecta) se trata de manera similar como si fuera un controlador de dominio/directorio activo. Active Directory es masivo, complejo y pesado: los riesgos potenciales de escalada están bajo cada roca. Active Directory proporcionará servicios como DNS, PKI y Endpoint Configuration Manager en una organización empresarial. Si un atacante obtuviera derechos administrativos de estos servicios, indirectamente tendría medios para intensificar sus privilegios a los de un administrador del`entire forest`. Demostraremos esto a través de algunas rutas de ataque descritas más adelante en el módulo.

Sin embargo, Active Directory tiene limitaciones. Desafortunadamente, estas limitaciones son un punto 'débil' y expanden nuestra superficie de ataque, algunas nacidas por complejidad, otras por diseño y otras debido a la compatibilidad legado y hacia atrás. En aras de la integridad, a continuación hay tres ejemplos de cada uno:

1. `Complexity`El ejemplo más simple es descubrir miembros del grupo anidados. Es fácil perderse al mirar quién es miembro de un grupo, miembro de otro grupo y miembro de otro grupo. Si bien puede pensar que esta cadena termina eventualmente, muchos entornos tienen a todos los "usuarios de dominio" indirectamente miembro de "administradores de dominio".
2. `Design`Active Directory permite administrar máquinas de forma remota a través de objetos de política de grupo (GPOS). Adedantes GPOS en una compartir/carpeta de red única llamada `SYSVOL`, donde todos los dispositivos unidos por dominio extraen la configuración de ellos. Debido a que es una carpeta compartida de red, los clientes acceden a SYSVOL a través del protocolo SMB y transfieren información almacenada. Por lo tanto, para que una máquina use nuevas configuraciones, debe llamar a un controlador de dominio y extraer configuraciones de Sysvol; este es un proceso sistemático, que de forma predeterminada ocurre cada 90 minutos. Cada dispositivo debe tener un controlador de dominio 'a la vista' para extraer estos datos. La desventaja de esto es que el protocolo SMB también permite la ejecución del código (un shell de comando remoto, donde los comandos se ejecutarán en el controlador de dominio), por lo que siempre que tengamos un conjunto de credenciales válidas, podemos ejecutar constantemente el código a través de SMB en los controladores de dominio de forma remota. Este puerto/protocolo está disponible para todas las máquinas hacia los controladores de dominio. (Además, SMB no está bien ajustado (generalmente activo directorio) para los conceptos de mordustes cero). Si un atacante tiene un buen conjunto de credenciales privilegiadas, puede ejecutar código como esa cuenta en controladores de dominio a través de SMB (¡al menos!).
3. `Legacy` - Windows is made with a primary focus: it works out of the box for most of Microsoft's customers. Windows is `not` secure by default. A legacy example is that Windows ships with the broadcasting - DNS-like protocols `NetBIOS`y `LLMNR` habilitado por defecto. Estos protocolos están destinados a ser utilizados si falla DNS. Sin embargo, están activos incluso cuando no lo hace. Sin embargo, debido a su diseño, transmiten credenciales de usuarios en el cable (nombres de usuario, contraseñas, hash de contraseña), que pueden proporcionar efectivamente credenciales privilegiadas a cualquier persona que escuche en el cable simplemente estando allí. Este[blog](https://www.a2secure.com/en/blog/how-to-use-responder-to-capture-netntlm-and-grab-a-shell/) Demuestra el abuso de capturar credenciales en el cable.