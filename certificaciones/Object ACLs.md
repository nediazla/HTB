# Object ACLs

# **Descripción**

En Active Directory,[Listas de control de acceso (ACL)](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-lists)son tablas, o listas simples, que definen a los fideicomisarios que tienen acceso a un objeto específico y su tipo de acceso. Un administrador puede ser cualquier principal de seguridad, como una cuenta de usuario, grupo o sesión de inicio de sesión. Cada`access control list`tiene un conjunto de`access control entries` (`ACE`), y cada as define al administrador y el tipo de acceso que tiene el administrador. Por lo tanto, varios fideicomisarios pueden acceder a un objeto, ya que puede haber varios ases. Las listas de control de acceso también se utilizan para fines de auditoría, como la grabación del número de intentos de acceso a un objeto adjunto y el tipo de acceso. Un objeto seguido es cualquier objeto con nombre en Active Directory que contiene un descriptor de seguridad, que tiene la información de seguridad sobre el objeto, que incluye ACL.

Un ejemplo de un`Access Control Entry`es que, por defecto, AD le da a los administradores de dominio el derecho de modificar la contraseña de cada objeto. Sin embargo, los derechos también pueden delegarse a ciertos usuarios o grupos que pueden realizar una acción específica en otros objetos; Esto puede ser restablecidos de contraseña, modificación de la membresía del grupo o la eliminación de objetos. En organizaciones grandes, si es prácticamente imposible evitar que los usuarios no privilegiados terminen con derechos delegados, deben eliminar el error humano y tener documentación de proceso bien definida. Por ejemplo, suponga que un empleado debía cambiar (accidentalmente/intencionalmente) su departamento a las operaciones de seguridad. En ese caso, la organización debe tener un proceso para revocar todos los derechos y acceso a sistemas y aplicaciones. En entornos publicitarios de la vida real, a menudo encontraremos casos como:

- Todos los usuarios de dominio agregaron como administradores a todos los servidores
- Todos pueden modificar todos los objetos (tener plenos derechos para ellos).
- Todos los usuarios de dominio tienen acceso a las propiedades extendidas de la computadora que contienen las contraseñas de LAPS.

---

# **Ataque**

Para identificar posibles ACL abusables, usaremos[Sabueso](https://github.com/BloodHoundAD/BloodHound)para graficar las relaciones entre los objetos y[Sharphound](https://github.com/BloodHoundAD/SharpHound)para escanear el entorno y pasar`All`hacia`-c`parámetro (versión corta de`CollectionMethod`):

ACL de objetos

```powershell
PS C:\Users\bob\Downloads> .\SharpHound.exe -c All

2022-12-19T14:16:39.1749601+01:00|INFORMATION|This version of SharpHound is compatible with the 4.2 Release of BloodHound
2022-12-19T14:16:39.3312221+01:00|INFORMATION|Resolved Collection Methods: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote
2022-12-19T14:16:39.3468314+01:00|INFORMATION|Initializing SharpHound at 14.16 on 19/12/2022
2022-12-19T14:16:39.5187113+01:00|INFORMATION|Flags: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote
2022-12-19T14:16:39.7530826+01:00|INFORMATION|Beginning LDAP search for eagle.local
2022-12-19T14:16:39.7999574+01:00|INFORMATION|Producer has finished, closing LDAP channel
2022-12-19T14:16:39.7999574+01:00|INFORMATION|LDAP channel closed, waiting for consumers
2022-12-19T14:17:09.8937530+01:00|INFORMATION|Status: 0 objects finished (+0 0)/s -- Using 36 MB RAM
2022-12-19T14:17:28.4874698+01:00|INFORMATION|Consumers finished, closing output channel
2022-12-19T14:17:28.5343302+01:00|INFORMATION|Output channel closed, waiting for output task to complete
Closing writers
2022-12-19T14:17:28.6124768+01:00|INFORMATION|Status: 114 objects finished (+114 2.375)/s -- Using 46 MB RAM
2022-12-19T14:17:28.6124768+01:00|INFORMATION|Enumeration finished in 00:00:48.8638030
2022-12-19T14:17:28.6905842+01:00|INFORMATION|Saving cache with stats: 74 ID to type mappings.
 76 name to SID mappings.
 1 machine sid mappings.
 2 sid to domain mappings.
 0 global catalog mappings.
2022-12-19T14:17:28.6905842+01:00|INFORMATION|SharpHound Enumeration Completed at 14.17 on 19/12/2022! Happy Graphing!

```

![](https://academy.hackthebox.com/storage/modules/176/A12/Sharphound.png)

El archivo zip generado por`SharpHound`entonces se puede visualizar en`BloodHound`. En lugar de buscar cada ACL mal configurado en el entorno, nos centraremos en posibles rutas de escalada que se originan en el usuario Bob (nuestro usuario inicial, que ya nos habíamos comprometido y tenemos un control completo). Por lo tanto, la siguiente imagen demuestra el acceso diferente que Bob tiene al entorno:

![](https://academy.hackthebox.com/storage/modules/176/A12/bloodhound.png)

Bob tiene plenos derechos sobre el usuario ANNI y el servidor de computadoras01. A continuación se muestra lo que Bob puede hacer con cada uno de estos:

1. Caso 1: Derechos completos sobre el usuario Anni. En este caso, Bob puede modificar el objeto ANNI especificando algún valor de bono SPN y luego realizar el ataque de Kerberoast en su contra (si recuerdas, el éxito de este ataque depende de la fuerza de la contraseña). Sin embargo, Bob también puede modificar la contraseña del usuario ANNI y luego iniciar sesión como esa cuenta, por lo tanto, heredar directamente y poder realizar todo lo que Anni puede (si Anni es un administrador de dominio, entonces Bob tendría los mismos derechos).
2. Caso 2: El control total sobre un objeto de computadora también puede ser fructífero. Si`LAPS`se usa en el entorno, entonces Bob puede obtener la contraseña almacenada en los atributos y autenticarse como la cuenta de administrador local a este servidor. Otro camino de escalada está abusando `Resource-Based Kerberos Delegation`, permitiendo que Bob se autentique como cualquiera a este servidor. Recuerde que del ataque anterior, Server01 se confía en la delegación sin restricciones, por lo que si Bob obtuvo los derechos administrativos en este servidor, tiene una posible ruta de escalada para comprometer la identidad de un controlador de dominio u otro objeto de computadora sensible.

También podemos usar[Adaclscanner](https://github.com/canix1/ADACLScanner)para crear informes de`discretionary access control lists` (`DACLs`) y`system access control lists` (`SACLs`).

---

# **Prevención**

Hay tres cosas que podemos hacer:

- Comenzar`continuous assessment`Detectar si esto es un problema en el entorno publicitario.
- `Educate`Empleados con altos privilegios para evitar hacer esto.
- `Automate`tanto como sea posible desde el proceso de gestión de acceso, y solo asigna acceso privilegiado a cuentas administrativas; Esto garantiza que los administradores no editen manualmente cuentas que reducen el riesgo de introducir derechos delegados a usuarios no privilegiados.

---

# **Detección**

Afortunadamente, tenemos varias formas de detectar si los objetos AD se modifican. Desafortunadamente, los eventos generados para objetos modificados están incompletos, ya que no proporcionan visibilidad granular sobre lo que se cambió. Por ejemplo, en el primer caso descrito anteriormente, Bob modificó Anni agregando un valor SPN. Al hacerlo, Bob tendrá los medios para realizar kerberoasting contra Anni. Cuando se agrega el valor SPN, un evento con la identificación`4738`, "Se cambió una cuenta de usuario", se genera. Sin embargo, este evento no demuestra todas las propiedades de usuario modificadas, incluido el SPN. Por lo tanto, el evento solo notifica sobre la modificación de un usuario, pero no especifica qué se cambió exactamente (aunque tiene una buena cantidad de campos que pueden ser útiles). A continuación se muestra el evento que se generará si Bob agrega algún valor SPN falso al objeto de usuario de Anni:

![](https://academy.hackthebox.com/storage/modules/176/A12/detect1.png)

Sin embargo, utilizando este evento, podemos saber si un usuario no privilegiado realiza acciones privilegiadas en otro usuario. Si, por ejemplo, todos los usuarios privilegiados tienen una convención de nombres que comienza con "AdminXXXX", entonces cualquier cambio no asociado con "AdminXXXX" es sospechoso. Si un abuso de ACL conduce a un restablecimiento de contraseña, la identificación del evento`4724`se registrará.

Del mismo modo, si Bob realice el segundo escenario, un evento con ID`4742`se generaría, lo que desafortunadamente también está limitado en la información que puede proporcionar; Sin embargo, notifica sobre la acción que la cuenta de usuario BOB está comprometida y se usa maliciosamente. Lo siguiente fue la identificación del evento`4742`Generado cuando Bob modificado es servidor01:

![](https://academy.hackthebox.com/storage/modules/176/A12/detect2.png)

---

# **Mielero**

Las ACL mal configuradas pueden ser un mecanismo efectivo de detección para un comportamiento sospechoso. Hay dos formas de abordar esto:

1. Asigne ACL relativamente altas a una cuenta de usuario utilizada como honeypot a través de una técnica discutida anteriormente, por ejemplo, un usuario cuyo`fake`Las credenciales están expuestas en el campo Descripción. Tener ACL asignados a la cuenta puede provocar que un adversario intente y verifique si la contraseña expuesta de la cuenta es válida, ya que tiene un alto potencial.
2. Tener una cuenta que`everyone`o muchos usuarios pueden modificar. Este usuario idealmente será un usuario de honeypot, con cierta actividad para imitar a los usuarios reales. Cualquier cambio que ocurra en este usuario de Honeypot sea malicioso ya que no hay una razón válida para que nadie realice ninguna acción (excepto los administradores, que ocasionalmente puede necesitar restablecer la contraseña de la cuenta para que la cuenta se vea realista). Por lo tanto, cualquier ID de evento`4738`Asociado con el usuario de Honeypot debe activar una alerta. Además, las organizaciones maduras pueden deshabilitar inmediatamente al usuario que realiza el cambio e iniciar una investigación forense en el dispositivo fuente. En aras de la integridad, si volvemos al ejemplo de Bob/Anni, se detectaría un cambio en la cuenta de Anni de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/176/A12/detect1.png)