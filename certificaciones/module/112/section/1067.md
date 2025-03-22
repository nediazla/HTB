# SMB

`Server Message Block` (`SMB`) es un protocolo de cliente cliente que regula el acceso a archivos y directorios completos y otros recursos de red, como impresoras, enrutadores o interfaces lanzadas para la red. El intercambio de información entre diferentes procesos del sistema también se puede manejar en función del protocolo SMB.[SMB](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-smb/f210069c-7086-4dc2-885e-861d837df688)Primero estuvo disponible para un público más amplio, por ejemplo, como parte del Sistema Operativo OS/2 de red LAN Manager y LAN Server. Desde entonces, el área de aplicación principal del protocolo ha sido la serie de sistemas operativos de Windows en particular, cuyos servicios de red admiten SMB de una manera compatible hacia abajo, lo que significa que los dispositivos con ediciones más nuevas pueden comunicarse fácilmente con dispositivos que tienen un sistema operativo Microsoft más antiguo instalado. Con el proyecto de software libre Samba, también hay una solución que permite el uso de SMB en distribuciones de Linux y Unix y, por lo tanto, la comunicación multiplataforma a través de SMB.

El protocolo SMB permite al cliente comunicarse con otros participantes en la misma red para acceder a archivos o servicios compartidos con él en la red. El otro sistema también debe haber implementado el protocolo de red y haber recibido y procesado la solicitud del cliente utilizando una aplicación de servidor SMB. Antes de eso, sin embargo, ambas partes deben establecer una conexión, por lo que primero intercambian mensajes correspondientes.

En las redes IP, SMB utiliza el protocolo TCP para este propósito, que proporciona un apretón de manos de tres vías entre el cliente y el servidor antes de que finalmente se establezca una conexión. Las especificaciones del protocolo TCP también rigen el transporte posterior de datos. Podemos echar un vistazo a algunos ejemplos[aquí](https://web.archive.org/web/20240815212710/https://winprotocoldoc.blob.core.windows.net/productionwindowsarchives/MS-SMB2/%5BMS-SMB2%5D.pdf#%5B%7B%22num%22%3A920%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C69%2C738%2C0%5D).

Un servidor SMB puede proporcionar partes arbitrarias de su sistema de archivos local como acciones. Por lo tanto, la jerarquía visible para un cliente es parcialmente independiente de la estructura en el servidor. Los derechos de acceso se definen por`Access Control Lists` (`ACL`). Se pueden controlar de manera fina en función de atributos como`execute`, `read`, y`full access`para usuarios individuales o grupos de usuarios. Los ACL se definen en función de las acciones y, por lo tanto, no corresponden a los derechos asignados localmente en el servidor.

---

# **Samba**

Como se mencionó anteriormente, existe una implementación alternativa del servidor SMB llamado Samba, que se desarrolla para sistemas operativos basados en UNIX. Samba implementa el sistema de archivos de Internet común (`CIFS`) Protocolo de red.[CIFS](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-cifs/934c2faa-54af-4526-ac74-6a24d126724e)es un dialecto de SMB, lo que significa que es una implementación específica del protocolo SMB creado originalmente por Microsoft. Esto le permite a Samba comunicarse de manera efectiva con los nuevos sistemas de Windows. Por lo tanto, a menudo se conoce como SMB/CIFS.

Sin embargo,`CIFS`se considera una versión específica del protocolo SMB, principalmente alineando con`SMB version 1`. Cuando los comandos SMB se transmiten a través de Samba a un servicio Netbios anterior, las conexiones generalmente ocurren sobre los puertos TCP`137`, `138`, y`139`. En contraste, CIFS opera a través del puerto TCP`445`exclusivamente. Hay varias versiones de SMB, incluidas versiones más nuevas como`SMB 2`y`SMB 3`, que ofrecen mejoras y se prefieren en las infraestructuras modernas, mientras que versiones más antiguas como`SMB 1` (`CIFS`) se consideran anticuados pero aún se pueden usar en entornos específicos.

| **Versión SMB** | **Compatible** | **Características** |
| --- | --- | --- |
| CIFS | Windows NT 4.0 | Comunicación a través de la interfaz Netbios |
| SMB 1.0 | Windows 2000 | Conexión directa a través de TCP |
| SMB 2.0 | Windows Vista, Windows Server 2008 | Actualizaciones de rendimiento, firma mejorada de mensajes, almacenamiento de almacenamiento en caché |
| SMB 2.1 | Windows 7, Windows Server 2008 R2 | Mecanismos de bloqueo |
| SMB 3.0 | Windows 8, Windows Server 2012 | Conexiones multicanal, cifrado de extremo a extremo, acceso a almacenamiento remoto |
| SMB 3.0.2 | Windows 8.1, Windows Server 2012 R2 |  |
| SMB 3.1.1 | Windows 10, Windows Server 2016 | Verificación de integridad, cifrado AES-128 |

Con la versión 3, el servidor Samba ganó la capacidad de ser un miembro completo de un dominio de Active Directory. Con la versión 4, Samba incluso proporciona un controlador de dominio de Active Directory. Contiene varios llamados demonios para este propósito, que son programas de fondo UNIX. El SMB Server Daemon (`smbd`) perteneciente a Samba proporciona las dos primeras funcionalidades, mientras que el Daemon del bloque de mensajes NetBios (`nmbd`) implementa las dos últimas funcionalidades. El servicio SMB controla estos dos programas de fondo.

Sabemos que Samba es adecuado para los sistemas Linux y Windows. En una red, cada host participa en el mismo`workgroup`. Un grupo de trabajo es un nombre grupal que identifica una colección arbitraria de computadoras y sus recursos en una red SMB. Puede haber múltiples grupos de trabajo en la red en un momento dado. IBM desarrolló un`application programming interface` (`API`) para computadoras de red llamadas`Network Basic Input/Output System` (`NetBIOS`). La API NetBIOS proporcionó un plan para una aplicación para conectar y compartir datos con otras computadoras. En un entorno de Netbios, cuando una máquina se conecta en línea, necesita un nombre, que se realiza a través del llamado`name registration`procedimiento. Cada host se reserva su nombre de host en la red o el[Servidor de nombres de netbios](https://networkencyclopedia.com/netbios-name-server-nbns/) (`NBNS`) se usa para este propósito. También se ha mejorado a[Servicio de nombres de Internet de Windows](https://networkencyclopedia.com/windows-internet-name-service-wins/) (`WINS`).

---

# **Configuración predeterminada**

Como podemos imaginar, Samba ofrece una amplia gama de[ajustes](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html)que podemos configurar. Nuevamente, definimos la configuración a través de un archivo de texto donde podemos obtener una descripción general de algunas de las configuraciones. Estas configuraciones se ven como la siguiente cuando se filtran:

### **Configuración predeterminada**

SMB

```
xnoxos@htb[/htb]$ cat /etc/samba/smb.conf | grep -v "#\|\;" [global]
   workgroup = DEV.INFREIGHT.HTB
   server string = DEVSMB
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d

   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes

   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

[print$]   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no

```

Vemos configuraciones globales y dos acciones destinadas a impresoras. La configuración global es la configuración del servidor SMB disponible que se utiliza para todas las acciones. Sin embargo, en las acciones individuales, la configuración global se puede sobrescribir, que se puede configurar con alta probabilidad incluso incorrectamente. Veamos algunas de las configuraciones para comprender cómo se configuran las acciones en Samba.

| **Configuración** | **Descripción** |
| --- | --- |
| `[sharename]` | El nombre de la red compartir. |
| `workgroup = WORKGROUP/DOMAIN` | Grupo de trabajo que aparecerá cuando los clientes consulten. |
| `path = /path/here/` | El directorio al que se le dará acceso al usuario. |
| `server string = STRING` | La cadena que aparecerá cuando se inicia una conexión. |
| `unix password sync = yes` | ¿Sincronizar la contraseña Unix con la contraseña SMB? |
| `usershare allow guests = yes` | ¿Permitir que los usuarios no autorenticados accedan a la participación definida? |
| `map to guest = bad user` | ¿Qué hacer cuando una solicitud de inicio de sesión de usuario no coincide con un usuario de UNIX válido? |
| `browseable = yes` | ¿Se debe mostrar esta acción en la lista de acciones disponibles? |
| `guest ok = yes` | ¿Permitir conectarse al servicio sin usar una contraseña? |
| `read only = yes` | ¿Permiten a los usuarios leer solo archivos? |
| `create mask = 0700` | ¿Qué permisos deben establecerse para archivos recién creados? |

---

# **Configuración peligrosa**

Algunas de las configuraciones anteriores ya traen algunas opciones sensibles. Sin embargo, supongamos que cuestionamos la configuración enumerada a continuación y nos preguntamos qué podrían ganar los empleados de ellos, así como a los atacantes. En ese caso, veremos qué ventajas y desventajas traen la configuración con ellos. Tomemos el escenario`browseable = yes`Como ejemplo. Si nosotros, como administradores, adoptamos este entorno, los empleados de la compañía tendrán la comodidad de poder mirar las carpetas individuales con el contenido. Muchas carpetas eventualmente se utilizan para una mejor organización y estructura. Si el empleado puede navegar por las acciones, el atacante también podrá hacerlo después de un acceso exitoso.

| **Configuración** | **Descripción** |
| --- | --- |
| `browseable = yes` | ¿Permitir el listado de acciones disponibles en la acción actual? |
| `read only = no` | ¿Prohibir la creación y modificación de los archivos? |
| `writable = yes` | ¿Permitir a los usuarios crear y modificar archivos? |
| `guest ok = yes` | ¿Permitir conectarse al servicio sin usar una contraseña? |
| `enable privileges = yes` | ¿Privilegios de honor asignados a SID específico? |
| `create mask = 0777` | ¿Qué permisos deben asignarse a los archivos recién creados? |
| `directory mask = 0777` | ¿Qué permisos deben asignarse a los directorios recién creados? |
| `logon script = script.sh` | ¿Qué script debe ejecutarse en el inicio de sesión del usuario? |
| `magic script = script.sh` | ¿Qué script debe ejecutarse cuando se cierre el script? |
| `magic output = script.out` | ¿Dónde debe almacenarse la salida del guión mágico? |

Creemos una parte llamado`[notes]`y algunos otros y ver cómo las configuraciones afectan nuestro proceso de enumeración. Usaremos todas las configuraciones anteriores y las aplicaremos a esta acción. Por ejemplo, esta configuración a menudo se aplica, aunque solo sea para fines de prueba. Si es entonces una subred interna de un equipo pequeño en un departamento grande, esta configuración a menudo se conserva o se olvida de restablecer. Esto lleva al hecho de que podemos navegar por todas las acciones y, con alta probabilidad, incluso descargarlas e inspeccionarlas.

### **Ejemplo de compartir**

SMB

```
...SNIP...

[notes]
	comment = CheckIT
	path = /mnt/notes/

	browseable = yes
	read only = no
	writable = yes
	guest ok = yes

	enable privileges = yes
	create mask = 0777
	directory mask = 0777

```

Se recomienda ver las páginas del hombre para Samba y configurarlo nosotros mismos y experimentar con la configuración. Luego descubriremos aspectos potenciales que serán interesantes para nosotros como probador de penetración. Además, cuanto más familiarizamos con el servidor Samba y SMB, más fácil será encontrar nuestro camino en el entorno y usarlo para nuestros propósitos. Una vez que hemos ajustado`/etc/samba/smb.conf`A nuestras necesidades, tenemos que reiniciar el servicio en el servidor.

### **Reiniciar samba**

SMB

```
root@samba:~# sudo systemctl restart smbd
```

Ahora podemos mostrar una lista (`-L`) de las acciones del servidor con el`smbclient`comando desde nuestro host. Usamos el llamado`null session` (`-N`), que es`anonymous`Acceso sin la entrada de usuarios existentes o contraseñas válidas.

### **SmbClient - Conectarse con la participación**

SMB

```
xnoxos@htb[/htb]$ smbclient -N -L //10.129.14.128        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)SMB1 disabled -- no workgroup available

```

Podemos ver que ahora tenemos cinco acciones diferentes en el servidor Samba del resultado. Así`print$`y un`IPC$`ya están incluidos de forma predeterminada en la configuración básica, como ya hemos visto. Desde que lidiamos con el`[notes]`Comparta, iniciamos e inspeccionemos con el mismo programa de cliente. Si no estamos familiarizados con el programa del cliente, podemos usar el`help`Comando sobre el inicio de sesión exitoso, enumerando todos los comandos posibles que podemos ejecutar.

SMB

```
xnoxos@htb[/htb]$ smbclient //10.129.14.128/notesEnter WORKGROUP\<username>'s password:
Anonymous login successful
Try "help" to get a list of possible commands.

smb: \> help

?              allinfo        altname        archive        backup
blocksize      cancel         case_sensitive cd             chmod
chown          close          del            deltree        dir
du             echo           exit           get            getfacl
geteas         hardlink       help           history        iosize
lcd            link           lock           lowercase      ls
l              mask           md             mget           mkdir
more           mput           newer          notify         open
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir
posix_unlink   posix_whoami   print          prompt         put
pwd            q              queue          quit           readlink
rd             recurse        reget          rename         reput
rm             rmdir          showacls       setea          setmode
scopy          stat           symlink        tar            tarmode
timeout        translate      unlock         volume         vuid
wdel           logon          listconnect    showconnect    tcon
tdis           tid            utimes         logoff         ..
!

smb: \> ls

  .                                   D        0  Wed Sep 22 18:17:51 2021
  ..                                  D        0  Wed Sep 22 12:03:59 2021
  prep-prod.txt                       N       71  Sun Sep 19 15:45:21 2021

                30313412 blocks of size 1024. 16480084 blocks available

```

Una vez que hemos descubierto archivos o carpetas interesantes, podemos descargarlos usando el`get`dominio. SMBClient también nos permite ejecutar comandos del sistema local utilizando un signo de exclamación al principio (`!<cmd>`) sin interrumpir la conexión.

### **Descargar archivos de SMB**

SMB

```
smb: \> get prep-prod.txt

getting file \prep-prod.txt of size 71 as prep-prod.txt (8,7 KiloBytes/sec)
(average 8,7 KiloBytes/sec)

smb: \> !ls

prep-prod.txt

smb: \> !cat prep-prod.txt

[] check your code with the templates
[] run code-assessment.py
[] …

```

Desde el punto de vista administrativo, podemos verificar estas conexiones usando`smbstatus`. Además de la versión de Samba, también podemos ver quién, de qué anfitrión y cuál compartir el cliente está conectado. Esto es especialmente importante una vez que hayamos ingresado una subred (tal vez incluso una aislada) al que los demás aún pueden acceder.

Por ejemplo, con seguridad a nivel de dominio, el servidor Samba actúa como miembro de un dominio de Windows. Cada dominio tiene al menos un controlador de dominio, generalmente un servidor Windows NT que proporciona autenticación de contraseña. Este controlador de dominio proporciona al grupo de trabajo un servidor de contraseñas definitivo. Los controladores de dominio realizan un seguimiento de los usuarios y las contraseñas en su cuenta`NTDS.dit`y`Security Authentication Module` (`SAM`) y autenticar a cada usuario cuando inicie sesión por primera vez y desean acceder a la parte de otra máquina.

### **Estado de samba**

SMB

```
root@samba:~# smbstatusSamba version 4.11.6-Ubuntu
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing
----------------------------------------------------------------------------------------------------------------------------------------
75691   sambauser    samba        10.10.14.4 (ipv4:10.10.14.4:45564)      SMB3_11           -                    -

Service      pid     Machine       Connected at                     Encryption   Signing
---------------------------------------------------------------------------------------------
notes        75691   10.10.14.4   Do Sep 23 00:12:06 2021 CEST     -            -

No locked files

```

---

# **Huella del servicio**

Volvamos a una de nuestras herramientas de enumeración. NMAP también tiene muchas opciones y scripts de NSE que pueden ayudarnos a examinar el servicio SMB del objetivo más de cerca y obtener más información. Sin embargo, la desventaja es que estos escaneos pueden llevar mucho tiempo. Por lo tanto, también se recomienda observar el servicio manualmente, principalmente porque podemos encontrar muchos más detalles de lo que NMAP podría mostrarnos. Primero, sin embargo, veamos qué puede encontrar NMAP en nuestro servidor de samba de destino, donde creamos el`[notes]`Compartir para fines de prueba.

### **Nmap**

SMB

```
xnoxos@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for sharing.inlanefreight.htb (10.129.14.128)
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

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.35 seconds

```

Podemos ver por los resultados que no es mucho que NMAP nos haya proporcionado aquí. Por lo tanto, debemos recurrir a otras herramientas que nos permitan interactuar manualmente con el SMB y enviar solicitudes específicas de información. Una de las prácticas herramientas para esto es`rpcclient`. Esta es una herramienta para realizar funciones MS-RPC.

El[Llamada de procedimiento remoto](https://www.geeksforgeeks.org/remote-procedure-call-rpc-in-operating-system/) (`RPC`) es un concepto y, por lo tanto, también una herramienta central para realizar estructuras operativas y compartidas de trabajo en redes y arquitecturas de cliente-servidor. El proceso de comunicación a través de RPC incluye los parámetros de aprobación y el retorno de un valor de función.

### **Rpcclient**

SMB

```
xnoxos@htb[/htb]$ rpcclient -U "" 10.129.14.128Enter WORKGROUP\'s password:
rpcclient$>
```

El`rpcclient`Nos ofrece muchas solicitudes diferentes con las que podemos ejecutar funciones específicas en el servidor SMB para obtener información. Se puede encontrar una lista completa de todas estas funciones en el[página del hombre](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html)del rpcclient.

| **Consulta** | **Descripción** |
| --- | --- |
| `srvinfo` | Información del servidor. |
| `enumdomains` | Enumere todos los dominios que se implementan en la red. |
| `querydominfo` | Proporciona información de dominio, servidor y usuario de dominios implementados. |
| `netshareenumall` | Enumera todas las acciones disponibles. |
| `netsharegetinfo <share>` | Proporciona información sobre una participación específica. |
| `enumdomusers` | Enumera a todos los usuarios de dominio. |
| `queryuser <RID>` | Proporciona información sobre un usuario específico. |

### **Rpcclient - enumeración**

SMB

```
rpcclient$> srvinfo        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03

rpcclient$> enumdomainsname:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]

rpcclient$> querydominfoDomain:         DEVOPS
Server:         DEVSMB
Comment:        DEVSM
Total Users:    2
Total Groups:   0
Total Aliases:  0
Sequence No:    1632361158
Force Logoff:   -1
Domain Server State:    0x1
Server Role:    ROLE_DOMAIN_PDC
Unknown 3:      0x1

rpcclient$> netshareenumallnetname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: home
        remark: INFREIGHT Samba
        path:   C:\home\
        password:
netname: dev
        remark: DEVenv
        path:   C:\home\sambauser\dev\
        password:
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
netname: IPC$
        remark: IPC Service (DEVSM)
        path:   C:\tmp
        password:

rpcclient$> netsharegetinfo notesnetname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
        type:   0x0
        perms:  0
        max_uses:       -1
        num_uses:       1
revision: 1
type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE
DACL
        ACL     Num ACEs:       1       revision:       2
        ---
        ACE
                type: ACCESS ALLOWED (0) flags: 0x00
                Specific bits: 0x1ff
                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS
                SID: S-1-1-0

```

Estos ejemplos nos muestran qué información se puede filtrar a los usuarios anónimos. Una vez un`anonymous`El usuario tiene acceso a un servicio de red, solo se necesita un error darles demasiados permisos o demasiada visibilidad para poner a toda la red en un riesgo significativo.

Lo más importante es que el acceso anónimo a dichos servicios también puede conducir al descubrimiento de otros usuarios, que pueden ser atacados con forcedura bruta en el caso más agresivo. Los humanos son más propensos a errores que los procesos de computadora configurados correctamente, y la falta de conciencia de seguridad y pereza a menudo conduce a contraseñas débiles que se pueden romper fácilmente. Veamos cómo podemos enumerar a los usuarios utilizando el`rpcclient`.

### **RPCClient - Enumeración del usuario**

SMB

```
rpcclient$> enumdomusersuser:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]

rpcclient$> queryuser 0x3e9        User Name   :   cry0l1t3
        Full Name   :   cry0l1t3
        Home Drive  :   \\devsmb\cry0l1t3
        Dir Drive   :
        Profile Path:   \\devsmb\cry0l1t3\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:50:56 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:50:56 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e9
        group_rid:      0x201
        acb_info :      0x00000014
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...

rpcclient$> queryuser 0x3e8        User Name   :   mrb3n
        Full Name   :
        Home Drive  :   \\devsmb\mrb3n
        Dir Drive   :
        Profile Path:   \\devsmb\mrb3n\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:47:59 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:47:59 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e8
        group_rid:      0x201
        acb_info :      0x00000010
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...

```

Luego podemos usar los resultados para identificar el RID del grupo, que luego podemos usar para recuperar información de todo el grupo.

### **RPCClient - Información grupal**

SMB

```
rpcclient$> querygroup 0x201        Group Name:     None
        Description:    Ordinary Users
        Group Attribute:7
        Num Members:2

```

Sin embargo, también puede suceder que no todos los comandos están disponibles para nosotros, y tenemos ciertas restricciones basadas en el usuario. Sin embargo, la consulta`queryuser <RID>`se permite principalmente en función del Rid. Por lo tanto, podemos usar el rpcclient para forzar bruto a los Rids a obtener información. Debido a que no sepamos quién ha sido asignado a qué RID, sabemos que obtendremos información al respecto tan pronto como consultemos un RID asignado. Hay varias formas y herramientas que podemos usar para esto. Para quedarse con la herramienta, podemos crear un`For-loop`usando`Bash`donde enviamos un comando al servicio usando RPCClient y filtramos los resultados.

### **Bruto forzando los usuarios de los usuarios**

SMB

```
xnoxos@htb[/htb]$ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done        User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201

        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201

        User Name   :   cry0l1t3
        user_rid :      0x3e9
        group_rid:      0x201

```

Una alternativa a esto sería un guión de Python de[Embalsar](https://github.com/SecureAuthCorp/impacket)llamado[samrdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/samrdump.py).

### **Impacket - samrdump.py**

SMB

```
xnoxos@htb[/htb]$ samrdump.py 10.129.14.128Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.129.14.128
Found domain(s):
 . DEVSMB
 . Builtin
[*] Looking up users in domain DEVSMB
Found user: mrb3n, uid = 1000
Found user: cry0l1t3, uid = 1001
mrb3n (1000)/FullName:
mrb3n (1000)/UserComment:
mrb3n (1000)/PrimaryGroupId: 513
mrb3n (1000)/BadPasswordCount: 0
mrb3n (1000)/LogonCount: 0
mrb3n (1000)/PasswordLastSet: 2021-09-22 17:47:59
mrb3n (1000)/PasswordDoesNotExpire: False
mrb3n (1000)/AccountIsDisabled: False
mrb3n (1000)/ScriptPath:
cry0l1t3 (1001)/FullName: cry0l1t3
cry0l1t3 (1001)/UserComment:
cry0l1t3 (1001)/PrimaryGroupId: 513
cry0l1t3 (1001)/BadPasswordCount: 0
cry0l1t3 (1001)/LogonCount: 0
cry0l1t3 (1001)/PasswordLastSet: 2021-09-22 17:50:56
cry0l1t3 (1001)/PasswordDoesNotExpire: False
cry0l1t3 (1001)/AccountIsDisabled: False
cry0l1t3 (1001)/ScriptPath:
[*] Received 2 entries.

```

La información que ya hemos obtenido`rpcclient`También se puede obtener utilizando otras herramientas. Por ejemplo, el[Smbmap](https://github.com/ShawnDEvans/smbmap)y[Crackmapexec](https://github.com/byt3bl33d3r/CrackMapExec)Las herramientas también son ampliamente utilizadas y útiles para la enumeración de los servicios de SMB.

### **Smbmap**

SMB

```
xnoxos@htb[/htb]$ smbmap -H 10.129.14.128[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers        home                                                    NO ACCESS       INFREIGHT Samba
        dev                                                     NO ACCESS       DEVenv
        notes                                                   NO ACCESS       CheckIT
        IPC$                                                    NO ACCESS       IPC Service (DEVSM)
```

### **Crackmapexec**

SMB

```
xnoxos@htb[/htb]$ crackmapexec smb 10.129.14.128 --shares -u '' -p ''SMB         10.129.14.128   445    DEVSMB           [*] Windows 6.1 Build 0 (name:DEVSMB) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.14.128   445    DEVSMB           [+] \:
SMB         10.129.14.128   445    DEVSMB           [+] Enumerated shares
SMB         10.129.14.128   445    DEVSMB           Share           Permissions     Remark
SMB         10.129.14.128   445    DEVSMB           -----           -----------     ------
SMB         10.129.14.128   445    DEVSMB           print$                          Printer DriversSMB         10.129.14.128   445    DEVSMB           home                            INFREIGHT Samba
SMB         10.129.14.128   445    DEVSMB           dev                             DEVenv
SMB         10.129.14.128   445    DEVSMB           notes           READ,WRITE      CheckIT
SMB         10.129.14.128   445    DEVSMB           IPC$                            IPC Service (DEVSM)
```

Otra herramienta que vale la pena mencionar es la llamada[enum4linux-ng](https://github.com/cddmp/enum4linux-ng), que se basa en una herramienta más antigua, Enum4linux. Esta herramienta automatiza muchas de las consultas, pero no todas, y puede devolver una gran cantidad de información.

### **Enum4linux -ng - instalación**

SMB

```
xnoxos@htb[/htb]$ git clone https://github.com/cddmp/enum4linux-ng.gitxnoxos@htb[/htb]$ cd enum4linux-ngxnoxos@htb[/htb]$ pip3 install -r requirements.txt
```

### **Enum4linux -ng - enumeración**

SMB

```
xnoxos@htb[/htb]$ ./enum4linux-ng.py 10.129.14.128 -AENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.14.128
[*] Username ......... ''
[*] Random Username .. 'juzgtcsu'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Service Scan on 10.129.14.128    |
 =====================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    NetBIOS Names and Workgroup for 10.129.14.128    |
 =====================================================
[+] Got domain/workgroup name: DEVOPS
[+] Full NetBIOS names information:
- DEVSMB          <00> -         H <ACTIVE>  Workstation Service
- DEVSMB          <03> -         H <ACTIVE>  Messenger Service
- DEVSMB          <20> -         H <ACTIVE>  File Server Service
- ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE>  Master Browser
- DEVOPS          <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
- DEVOPS          <1d> -         H <ACTIVE>  Master Browser
- DEVOPS          <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
- MAC Address = 00-00-00-00-00-00

 ==========================================
|    SMB Dialect Check on 10.129.14.128    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
SMB 1.0: false
SMB 2.02: true
SMB 2.1: true
SMB 3.0: true
SMB1 only: false
Preferred dialect: SMB 3.0
SMB signing required: false

 ==========================================
|    RPC Session Check on 10.129.14.128    |
 ==========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user session
[+] Server allows session using username 'juzgtcsu', password ''
[H] Rerunning enumeration with user 'juzgtcsu' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.14.128    |
 ====================================================
[+] Domain: DEVOPS
[+] SID: NULL SID
[+] Host is part of a workgroup (not a domain)

 ============================================================
|    Domain Information via SMB session for 10.129.14.128    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DEVSMB
NetBIOS domain name: ''
DNS domain: ''
FQDN: htb

 ================================================
|    OS Information via RPC for 10.129.14.128    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Windows 7, Windows Server 2008 R2
OS version: '6.1'
OS release: ''
OS build: '0'
Native OS: not supported
Native LAN manager: not supported
Platform id: '500'
Server type: '0x809a03'
Server type string: Wk Sv PrQ Unx NT SNT DEVSM

 ======================================
|    Users via RPC on 10.129.14.128    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[+] Found 2 users via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 2 users via 'enumdomusers'
[+] After merging user results we have 2 users total:
'1000':
  username: mrb3n
  name: ''
  acb: '0x00000010'
  description: ''
'1001':
  username: cry0l1t3
  name: cry0l1t3
  acb: '0x00000014'
  description: ''

 =======================================
|    Groups via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating local groups
[+] Found 0 group(s) via 'enumalsgroups domain'
[*] Enumerating builtin groups
[+] Found 0 group(s) via 'enumalsgroups builtin'
[*] Enumerating domain groups
[+] Found 0 group(s) via 'enumdomgroups'

 =======================================
|    Shares via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating shares
[+] Found 5 share(s):
IPC$:  comment: IPC Service (DEVSM)
  type: IPC
dev:
  comment: DEVenv
  type: Disk
home:
  comment: INFREIGHT Samba
  type: Disk
notes:
  comment: CheckIT
  type: Disk
print$:  comment: Printer Drivers
  type: Disk
[*] Testing share IPC$
[-] Could not check share: STATUS_OBJECT_NAME_NOT_FOUND
[*] Testing share dev
[-] Share doesn't exist
[*] Testing share home
[+] Mapping: OK, Listing: OK
[*] Testing share notes
[+] Mapping: OK, Listing: OK
[*] Testing share print$
[+] Mapping: DENIED, Listing: N/A

 ==========================================
|    Policies via RPC for 10.129.14.128    |
 ==========================================
[*] Trying port 445/tcp
[+] Found policy:
domain_password_information:
  pw_history_length: None
  min_pw_length: 5
  min_pw_age: none
  max_pw_age: 49710 days 6 hours 21 minutes
  pw_properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
domain_lockout_information:
  lockout_observation_window: 30 minutes
  lockout_duration: 30 minutes
  lockout_threshold: None
domain_logoff_information:
  force_logoff_time: 49710 days 6 hours 21 minutes

 ==========================================
|    Printers via RPC for 10.129.14.128    |
 ==========================================
[+] No printers returned (this is not an error)

Completed after 0.61 seconds

```

Necesitamos usar más de dos herramientas para la enumeración. Debido a que puede suceder que debido a la programación de las herramientas, obtenemos información diferente que tenemos que verificar manualmente. Por lo tanto, nunca debemos confiar solo en herramientas automatizadas donde no sabemos con precisión cómo se escribieron.