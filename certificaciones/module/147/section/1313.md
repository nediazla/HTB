# Almacenamiento de credenciales

Cada aplicación que admite mecanismos de autenticación compara las entradas/credenciales dadas con bases de datos locales o remotas. En el caso de las bases de datos locales, estas credenciales se almacenan localmente en el sistema. Las aplicaciones web a menudo son vulnerables a las inyecciones de SQL, lo que puede conducir al peor de los casos en el que los atacantes ven la totalidad de los datos de una organización en texto plano.

Hay muchas listas de palabras diferentes que contienen las contraseñas más utilizadas. Un ejemplo de una de estas listas es`rockyou.txt`. Esta lista incluye alrededor de 14 millones de contraseñas únicas, y se creó después de una violación de datos de la compañía Rockyou, que contenía un total de 32 millones de cuentas de usuario. La compañía Rockyou almacenó todas las credenciales en texto sin formato en su base de datos, que los atacantes podían ver. Después de un exitoso ataque de inyección SQL.

También sabemos que cada sistema operativo admite este tipo de mecanismos de autenticación. Por lo tanto, las credenciales almacenadas se almacenan localmente. Veamos cómo se crean, almacenan y administran los sistemas basados en Windows y Linux con más detalle.

---

# **Linux**

Como ya sabemos, los sistemas basados en Linux manejan todo en forma de un archivo. En consecuencia, las contraseñas también se almacenan encriptadas en un archivo. Este archivo se llama el`shadow`archivo y se encuentra en`/etc/shadow`y es parte del sistema de administración de usuarios de Linux. Además, estas contraseñas se almacenan comúnmente en forma de`hashes`. Un ejemplo puede verse así:

### **Archivo sombra**

Almacenamiento de credenciales

```
root@htb:~# cat /etc/shadow...SNIP...
htb-student:$y$j9T$3QSBB6CbHEu...SNIP...f8Ms:18955:0:99999:7:::
```

El`/etc/shadow`El archivo tiene un formato único en el que se ingresan y guardan las entradas cuando se crean nuevos usuarios.

|  |  |  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| HTB-Estudente: | $ y $ j9t $ 3qsbb6cbheu ... snip ... f8ms: | 18955: | 0: | 99999: | 7: | : | : | : |
| `<username>`: | `<encrypted password>`: | `<day of last change>`: | `<min age>`: | `<max age>`: | `<warning period>`: | `<inactivity period>`: | `<expiration date>`: | `<reserved field>` |

El cifrado de la contraseña en este archivo está formateado de la siguiente manera:

|  |  |  |
| --- | --- | --- |
| `$ <id>` | `$ <salt>` | `$ <hashed>` |
| `$ y` | `$ j9T` | `$ 3QSBB6CbHEu...SNIP...f8Ms` |

El tipo (`id`) es el método hash criptográfico utilizado para cifrar la contraseña. En el pasado, se usaron muchos métodos de hash criptográfico diferentes en el pasado y todavía son utilizados por algunos sistemas hoy en día.

| **IDENTIFICACIÓN** | **Algoritmo de hash criptográfico** |
| --- | --- |
| `$1$` | [MD5](https://en.wikipedia.org/wiki/MD5) |
| `$2a$` | [Pescado](https://en.wikipedia.org/wiki/Blowfish_(cipher)) |
| `$5$` | [SHA-256](https://en.wikipedia.org/wiki/SHA-2) |
| `$6$` | [SHA-512](https://en.wikipedia.org/wiki/SHA-2) |
| `$sha1$` | [Sha1Crypt](https://en.wikipedia.org/wiki/SHA-1) |
| `$y$` | [Yescrypt](https://github.com/openwall/yescrypt) |
| `$gy$` | [Gost-yescrypt](https://www.openwall.com/lists/yescrypt/2019/06/30/1) |
| `$7$` | [Scrypt](https://en.wikipedia.org/wiki/Scrypt) |

Sin embargo, algunos archivos más pertenecen al sistema de administración de usuarios de Linux. Los otros dos archivos son`/etc/passwd`y`/etc/group`. En el pasado, la contraseña encriptada se almacenó junto con el nombre de usuario en el`/etc/passwd`Archivo, pero esto fue cada vez más reconocido como un problema de seguridad porque todos los usuarios pueden ver el archivo en el sistema y debe ser legible. El`/etc/shadow`El usuario solo puede leer el archivo`root`.

### **PASSWD FILE**

Almacenamiento de credenciales

```
xnoxos@htb[/htb]$ cat /etc/passwd...SNIP...
htb-student:x:1000:1000:,,,:/home/htb-student:/bin/bash

```

|  |  |  |  |  |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| `htb-student:` | `x:` | `1000:` | `1000:` | `,,,:` | `/home/htb-student:` | `/bin/bash` |
| `<username>:` | `<password>:` | `<uid>:` | `<gid>:` | `<comment>:` | `<home directory>:` | `<cmd executed after logging in>` |

El`x`En el campo de contraseña indica que la contraseña cifrada está en el`/etc/shadow`archivo. Sin embargo, la redirección al`/etc/shadow`El archivo no hace que los usuarios del sistema sean invulnerables porque si los derechos de este archivo se establecen incorrectamente, el archivo se puede manipular para que el usuario`root`no necesita escribir una contraseña para iniciar sesión. Por lo tanto, un campo vacío significa que podemos iniciar sesión con el nombre de usuario sin ingresar una contraseña.

- [Auth de usuarios de Linux](https://tldp.org/HOWTO/pdf/User-Authentication-HOWTO.pdf)

---

# **Proceso de autenticación de Windows**

El[Proceso de autenticación del cliente de Windows](https://docs.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication)Las a menudo pueden ser más complicadas que con los sistemas Linux y consta de muchos módulos diferentes que realizan los procesos completos de inicio de sesión, recuperación y verificación. Además, hay muchos procedimientos de autenticación diferentes y complejos en el sistema de Windows, como la autenticación de Kerberos. El[Autoridad de seguridad local](https://learn.microsoft.com/en-us/windows-server/security/credentials-protection-and-management/configuring-additional-lsa-protection) (`LSA`) es un subsistema protegido que autentica a los usuarios y los registra en la computadora local. Además, la LSA mantiene información sobre todos los aspectos de la seguridad local en una computadora. También proporciona varios servicios para traducir entre nombres e identificaciones de seguridad (`SIDs`).

El subsistema de seguridad realiza un seguimiento de las políticas y cuentas de seguridad que residen en un sistema informático. En el caso de un controlador de dominio, estas políticas y cuentas se aplican al dominio donde se encuentra el controlador de dominio. Estas políticas y cuentas se almacenan en Active Directory. Además, el subsistema LSA proporciona servicios para verificar el acceso a los objetos, verificar los permisos de los usuarios y generar mensajes de monitoreo.

### **Diagrama de proceso de autenticación de Windows**

![](https://academy.hackthebox.com/storage/modules/147/Auth_process1.png)

El inicio de sesión interactivo local se realiza mediante la interacción entre el proceso de inicio de sesión ([Winlogon](https://www.microsoftpressstore.com/articles/article.aspx?p=2228450&seqNum=8)), el proceso de interfaz de usuario de inicio de sesión (`LogonUI`), el`credential providers`, `LSASS`, uno o más`authentication packages`, y`SAM`o`Active Directory`. Los paquetes de autenticación, en este caso, son las bibliotecas de enlace dinámico (`DLLs`) que realizan verificaciones de autenticación. Por ejemplo, para los inicios de sesión interactivos e interactivos sin dominio, el paquete de autenticación`Msv1_0.dll`se usa.

`Winlogon`es un proceso confiable responsable de administrar las interacciones de usuario relacionadas con la seguridad. Estos incluyen:

- Lanzamiento de Logonui para ingresar contraseñas al inicio de sesión
- Cambiar contraseñas
- Bloquear y desbloquear la estación de trabajo

Se basa en los proveedores de credenciales instalados en el sistema para obtener el nombre o la contraseña de la cuenta de un usuario. Los proveedores de credenciales son`COM`Objetos que se encuentran en DLLS.

Winlogon es el único proceso que intercepta solicitudes de inicio de sesión desde el teclado enviado a través de un mensaje RPC de Win32k.sys. WinLOGon inicia inmediatamente la aplicación Logonui en el inicio de sesión para mostrar la interfaz de usuario para el inicio de sesión. Después de que Winlogon obtiene un nombre de usuario y una contraseña de los proveedores de credenciales, llama a LSASS para autenticar al usuario que intenta iniciar sesión.

### **LSass**

[Servicio del subsistema de la Autoridad de Seguridad local](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service) (`LSASS`) es una colección de muchos módulos y tiene acceso a todos los procesos de autenticación que se pueden encontrar en`%SystemRoot%\System32\Lsass.exe`. Este servicio es responsable de la política de seguridad del sistema local, la autenticación del usuario y el envío de registros de auditoría de seguridad al`Event log`. En otras palabras, es la bóveda de los sistemas operativos basados en Windows, y podemos encontrar una ilustración más detallada de la arquitectura LSASS[aquí](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/cc961760(v=technet.10)?redirectedfrom=MSDN).

| **Paquetes de autenticación** | **Descripción** |
| --- | --- |
| `Lsasrv.dll` | El servicio de servidor LSA hace cumplir las políticas de seguridad y actúa como el administrador de paquetes de seguridad para el LSA. El LSA contiene la función de negociación, que selecciona el protocolo NTLM o Kerberos después de determinar qué protocolo debe tener éxito. |
| `Msv1_0.dll` | Paquete de autenticación para registros de máquinas locales que no requieren autenticación personalizada. |
| `Samsrv.dll` | El Gerente de Cuentas de Seguridad (SAM) almacena cuentas de seguridad locales, aplica políticas almacenadas localmente y admite API. |
| `Kerberos.dll` | Paquete de seguridad cargado por la LSA para la autenticación basada en Kerberos en una máquina. |
| `Netlogon.dll` | Servicio de inicio de sesión basado en la red. |
| `Ntdsa.dll` | Esta biblioteca se utiliza para crear nuevos registros y carpetas en el registro de Windows. |

Fuente:[Microsoft Docs](https://docs.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication).

Cada sesión de inicio de sesión interactiva crea una instancia separada del servicio WinLogon. El[Identificación y autenticación gráfica](https://docs.microsoft.com/en-us/windows/win32/secauthn/gina) (`GINA`) La arquitectura se carga en el área de proceso utilizada por Winlogon, recibe y procesa las credenciales e invoca las interfaces de autenticación a través de[Lsalogonuser](https://docs.microsoft.com/en-us/windows/win32/api/ntsecapi/nf-ntsecapi-lsalogonuser)función.

### **Base de datos SAM**

El[Gerente de cuenta de seguridad](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc756748(v=ws.10)?redirectedfrom=MSDN) (`SAM`) es un archivo de base de datos en los sistemas operativos de Windows que almacena las contraseñas de los usuarios. Se puede usar para autenticar usuarios locales y remotos. SAM utiliza medidas criptográficas para evitar que los usuarios no autenticados accedan al sistema. Las contraseñas de usuario se almacenan en un formato hash en una estructura de registro como un`LM`hash o un`NTLM`picadillo. Este archivo se encuentra en`%SystemRoot%/system32/config/SAM`y está montado en HKLM/SAM. Se requieren permisos a nivel del sistema para verlo.

Los sistemas de Windows se pueden asignar a un grupo de trabajo o dominio durante la configuración. Si el sistema se ha asignado a un grupo de trabajo, maneja la base de datos SAM localmente y almacena a todos los usuarios existentes localmente en esta base de datos. Sin embargo, si el sistema se ha unido a un dominio, el controlador de dominio (`DC`) debe validar las credenciales de la base de datos de Active Directory (`ntds.dit`), que se almacena en`%SystemRoot%\ntds.dit`.

Microsoft introdujo una función de seguridad en Windows NT 4.0 para ayudar a mejorar la seguridad de la base de datos SAM contra el descifrado de software fuera de línea. Este es el`SYSKEY` (`syskey.exe`) Característica que, cuando está habilitada, cifra parcialmente la copia del disco duro del archivo SAM para que los valores de hash de contraseña para todas las cuentas locales almacenadas en el SAM estén encriptados con una clave.

### **Gerente de credencial**

![](https://academy.hackthebox.com/storage/modules/147/authn_credman_credprov.png)

Fuente:[Microsoft Docs](https://docs.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication).

Credential Manager es una característica integrada en todos los sistemas operativos de Windows que permite a los usuarios guardar las credenciales que utilizan para acceder a varios recursos y sitios web de red. Las credenciales guardadas se almacenan en función de los perfiles de usuario en cada usuario`Credential Locker`. Las credenciales están encriptadas y almacenadas en la siguiente ubicación:

Almacenamiento de credenciales

```powershell
PS C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\

```

Existen varios métodos para descifrar las credenciales guardadas utilizando Credential Manager. Practicaremos prácticos con algunos de estos métodos en este módulo.

### **NTDS**

Es muy común encontrar entornos de red donde los sistemas de Windows se unen a un dominio de Windows. Esto es común porque facilita que los administradores administren todos los sistemas propiedad de sus respectivas organizaciones (gestión centralizada). En estos casos, los sistemas de Windows enviarán todas las solicitudes de inicio de sesión a controladores de dominio que pertenecen al mismo bosque de Active Directory. Cada controlador de dominio aloja un archivo llamado`NTDS.dit`que se mantiene sincronizado en todos los controladores de dominio con la excepción de[Controladores de dominio de solo lectura](https://docs.microsoft.com/en-us/windows/win32/ad/rodc-and-active-directory-schema). Ntds.dit es un archivo de base de datos que almacena los datos en Active Directory, que incluye, entre otros,:

- Cuentas de usuario (nombre de usuario y contraseña hash)
- Cuentas grupales
- Cuentas de computadora
- Objetos de política grupal

Practicaremos métodos que nos permiten extraer credenciales del archivo ntds.dit más adelante en este módulo.

Ahora que hemos pasado por un manual sobre conceptos de almacenamiento de credenciales, estudiemos los diversos ataques que podemos realizar para extraer credenciales para promover nuestro acceso durante las evaluaciones.