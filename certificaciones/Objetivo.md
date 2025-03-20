# Objetivos

`Targets`son identificadores de sistema operativo únicos tomados de las versiones de aquellos sistemas operativos específicos que adaptan el módulo de explotación seleccionado para ejecutarse en esa versión particular del sistema operativo. El`show targets`El comando emitido dentro de una vista del módulo de Exploit mostrará todos los objetivos vulnerables disponibles para ese exploit específico, al tiempo que emite el mismo comando en el menú raíz, fuera de cualquier módulo de exploit seleccionado, nos hará saber que primero debemos seleccionar un módulo de exploit.

### **MSF - Mostrar objetivos**

Objetivos

```
msf6 > show targets

[-] No exploit module selected.

```

Al mirar nuestro módulo de exploit anterior, esto sería lo que vemos:

Objetivos

```
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

---

# **Seleccionando un objetivo**

Podemos ver que solo hay un tipo general de conjunto de objetivos para este tipo de exploit. ¿Qué pasa si cambiamos el módulo de exploit a algo que necesita rangos objetivo más específicos? La siguiente exploit está dirigida a:

- `MS12-063 Microsoft Internet Explorer execCommand Use-After-Free Vulnerability`.

Si queremos obtener más información sobre este módulo específico y lo que hace la vulnerabilidad detrás de él, podemos usar el`info`dominio. Este comando puede ayudarnos cuando no estamos seguros de los orígenes o la funcionalidad de diferentes exploits o módulos auxiliares. Teniendo en cuenta que siempre se considera la mejor práctica auditar nuestro código para cualquier generación de artefactos o 'características adicionales', el`info`El comando debe ser uno de los primeros pasos que tomamos al usar un nuevo módulo. De esta manera, podemos familiarizarnos con la funcionalidad de exploit al tiempo que aseguramos un entorno de trabajo seguro y limpio tanto para nuestros clientes como para nosotros.

### **MSF - Selección de destino**

Objetivos

```
msf6 exploit(windows/browser/ie_execcommand_uaf) > info

       Name: MS12-063 Microsoft Internet Explorer execCommand Use-After-Free Vulnerability
     Module: exploit/windows/browser/ie_execcommand_uaf
   Platform: Windows
       Arch:
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Good
  Disclosed: 2012-09-14

Provided by:
  unknown
  eromang
  binjo
  sinn3r <sinn3r@metasploit.com>
  juan vazquez <juan.vazquez@metasploit.com>

Available targets:
  Id  Name
  --  ----
  0   Automatic
  1   IE 7 on Windows XP SP3
  2   IE 8 on Windows XP SP3
  3   IE 7 on Windows Vista
  4   IE 8 on Windows Vista
  5   IE 8 on Windows 7
  6   IE 9 on Windows 7

Check supported:
  No

Basic options:
  Name       Current Setting  Required  Description
  ----       ---------------  --------  -----------
  OBFUSCATE  false            no        Enable JavaScript obfuscation
  SRVHOST    0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
  SRVPORT    8080             yes       The local port to listen on.
  SSL        false            no        Negotiate SSL for incoming connections
  SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
  URIPATH                     no        The URI to use for this exploit (default is random)

Payload information:

Description:
  This module exploits a vulnerability found in Microsoft Internet
  Explorer (MSIE). When rendering an HTML page, the CMshtmlEd object
  gets deleted in an unexpected manner, but the same memory is reused
  again later in the CMshtmlEd::Exec() function, leading to a
  use-after-free condition. Please note that this vulnerability has
  been exploited since Sep 14, 2012. Also, note that
  presently, this module has some target dependencies for the ROP
  chain to be valid. For WinXP SP3 with IE8, msvcrt must be present
  (as it is by default). For Vista or Win7 with IE8, or Win7 with IE9,
  JRE 1.6.x or below must be installed (which is often the case).

References:
  https://cvedetails.com/cve/CVE-2012-4969/
  OSVDB (85532)
  https://docs.microsoft.com/en-us/security-updates/SecurityBulletins/2012/MS12-063
  http://technet.microsoft.com/en-us/security/advisory/2757760
  http://eromang.zataz.com/2012/09/16/zero-day-season-is-really-not-over-yet/

```

Mirando la descripción, podemos tener una idea general de lo que este exploit logrará para nosotros. Teniendo esto en cuenta, luego querríamos verificar qué versiones son vulnerables a esta exploit.

Objetivos

```
msf6 exploit(windows/browser/ie_execcommand_uaf) > options

Module options (exploit/windows/browser/ie_execcommand_uaf):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   OBFUSCATE  false            no        Enable JavaScript obfuscation
   SRVHOST    0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT    8080             yes       The local port to listen on.
   SSL        false            no        Negotiate SSL for incoming connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   URIPATH                     no        The URI to use for this exploit (default is random)

Exploit target:

   Id  Name
   --  ----
   0   Automatic

msf6 exploit(windows/browser/ie_execcommand_uaf) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   Automatic
   1   IE 7 on Windows XP SP3
   2   IE 8 on Windows XP SP3
   3   IE 7 on Windows Vista
   4   IE 8 on Windows Vista
   5   IE 8 on Windows 7
   6   IE 9 on Windows 7

```

Vemos opciones para diferentes versiones de Internet Explorer y varias versiones de Windows. Dejando la selección a`Automatic`Le informará a MSFConsole que necesita realizar la detección de servicios en el objetivo dado antes de lanzar un ataque exitoso.

Sin embargo, si sabemos qué versiones se están ejecutando en nuestro objetivo, podemos usar el`set target <index no.>`comando elegir un objetivo de la lista.

Objetivos

```
msf6 exploit(windows/browser/ie_execcommand_uaf) > show targets

Exploit targets:

   Id  Name
   --  ----
   0   Automatic
   1   IE 7 on Windows XP SP3
   2   IE 8 on Windows XP SP3
   3   IE 7 on Windows Vista
   4   IE 8 on Windows Vista
   5   IE 8 on Windows 7
   6   IE 9 on Windows 7

msf6 exploit(windows/browser/ie_execcommand_uaf) > set target 6

target => 6

```

---

# **Tipos de objetivos**

Hay una gran variedad de tipos de objetivos. Cada objetivo puede variar de otro por paquete de servicio, versión del sistema operativo e incluso la versión de idioma. Todo depende de la dirección de retorno y otros parámetros en el objetivo o dentro del módulo de explotación.

La dirección de devolución puede variar porque un paquete de idioma particular cambia de direcciones, hay una versión de software diferente disponible o las direcciones se cambian debido a los ganchos. Todo está determinado por el tipo de dirección de retorno requerida para identificar el objetivo. Esta dirección puede ser`jmp esp`, un salto a un registro específico que identifica el objetivo o un`pop/pop/ret`. Para obtener más información sobre el tema de las direcciones de devolución, consulte el[Se desborda el búfer basado en pila en Windows x86](https://academy.hackthebox.com/module/89/section/931)módulo. Los comentarios en el código del módulo de explotación pueden ayudarnos a determinar por qué se define el objetivo.

Para identificar un objetivo correctamente, tendremos que:

- Obtener una copia de los binarios objetivo
- Use msfpescan para localizar una dirección de devolución adecuada

Más adelante en el módulo, profundizaremos en el desarrollo de explotación, la generación de carga útil e identificación de objetivos.

[Anterior](https://academy.hackthebox.com/module/39/section/404)**+10 Streak pts** Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/39/section/407)

Hoja de trucos

**Tabla de contenido
Introducción**[Prefacio](https://academy.hackthebox.com/module/39/section/381)[Introducción a MetaSploit](https://academy.hackthebox.com/module/39/section/383)[Introducción a MSFConsole](https://academy.hackthebox.com/module/39/section/384)
**Componentes MSF**  [Módulos](https://academy.hackthebox.com/module/39/section/404)[Objetivos](https://academy.hackthebox.com/module/39/section/408)  [Cargas útiles](https://academy.hackthebox.com/module/39/section/407)[Codificadores](https://academy.hackthebox.com/module/39/section/409)[Bases de datos](https://academy.hackthebox.com/module/39/section/411)[Plugins y mixins](https://academy.hackthebox.com/module/39/section/413)
**Sesiones MSF**  [Sesiones y trabajos](https://academy.hackthebox.com/module/39/section/415)  [Meterpréter](https://academy.hackthebox.com/module/39/section/414)
**Características adicionales**[Escribir e importar módulos](https://academy.hackthebox.com/module/39/section/417)[Introducción a MSFVENOM](https://academy.hackthebox.com/module/39/section/418)[Firewall e IDS/IPS evasión](https://academy.hackthebox.com/module/39/section/416)[Actualizaciones de trabajo de metasploit - agosto de 2020](https://academy.hackthebox.com/module/39/section/493)

### **Mi estación de trabajo**

Interactuar Terminar Reiniciar