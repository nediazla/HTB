# Análisis estático en Windows

En este segmento, nuestro enfoque estará en reproducir algunas de las tareas de análisis estático que llevamos a cabo en una máquina Linux, pero esta vez, emplearemos una máquina de Windows.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Análisis estático en Windows

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

# **Identificar el tipo de archivo**

Nuestro primer puerto de escala en esta etapa es determinar la información rudimentaria sobre el espécimen de malware para sentar las bases para nuestra investigación. Dado que las extensiones de archivos se pueden manipular y cambiar, nuestra tarea es idear un método para identificar el tipo de archivo real que estamos encontrando. El establecimiento del tipo de archivo juega un papel integral en el análisis estático, asegurando que los procedimientos que aplicamos sean apropiados y los resultados obtenidos son precisos.

Usemos un malware basado en Windows llamado`Ransomware.wannacry.exe`residiendo en el`C:\Samples\MalwareAnalysis`directorio del objetivo de esta sección como ilustración.

Podemos usar una solución como`CFF Explorer`(disponible en`C:\Tools\Explorer Suite`) para verificar el tipo de archivo de este malware de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/227/filetype1.png)

En un sistema de Windows, la presencia de la cadena ASCII`MZ`(En hexadecimal:`4D 5A`) al comienzo de un archivo (conocido como "número mágico") denota un archivo ejecutable.`MZ`significa Mark Zbikowski, un arquitecto clave de MS-DOS.

# **Huellas dactilares de malware**

En esta etapa, nuestra misión es crear un identificador único para la muestra de malware. Esto generalmente toma la forma de un hash criptográfico - MD5, SHA1 o SHA256.

Se emplea huellas dactilares para numerosos fines, que abarca:

- Identificación y seguimiento de muestras de malware
- Escanear un sistema completo para la presencia de malware idéntico
- Confirmación de encuentros y análisis anteriores del mismo malware
- Compartir con las partes interesadas como COI (indicadores de compromiso) o como parte de los informes de inteligencia de amenazas

Como ilustración, para verificar el hash del archivo MD5 del malware mencionado antes de usar el`Get-FileHash`PowerShell Cmdlet como sigue.

Análisis estático en Windows

```powershell
PS C:\Users\htb-student> Get-FileHash -Algorithm MD5 C:\Samples\MalwareAnalysis\Ransomware.wannacry.exe

Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
MD5             DB349B97C37D22F5EA1D1841E3C89EB4                                       C:\Samples\MalwareAnalysis\Ra...

```

Para verificar el hash del archivo SHA256 del malware mencionado anteriormente, el comando sería el siguiente.

Análisis estático en Windows

```powershell
PS C:\Users\htb-student> Get-FileHash -Algorithm SHA256 C:\Samples\MalwareAnalysis\Ransomware.wannacry.exe

Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
SHA256          24D004A104D4D54034DBCFFC2A4B19A11F39008A575AA614EA04703480B1022C       C:\Samples\MalwareAnalysis\Ra...

```

# **File Hash Lookup**

El paso resultante implica verificar el hash de archivo producido en el paso anterior contra escáneres de malware en línea y cajas de arena como Cuckoo Sandbox. Por ejemplo, Virustotal, un motor de escaneo de malware en línea, que colabora con varios proveedores de antivirus, nos permite buscar el hash del archivo. Este paso nos ayuda a comparar nuestros resultados con el conocimiento existente sobre la muestra de malware.

La siguiente imagen muestra los resultados de[Virusta](https://www.virustotal.com/gui/home/search)Después de que se presentó el archivo de archivo SHA256 del malware antes mencionado.

![](https://academy.hackthebox.com/storage/modules/227/virustotal_scan.png)

Aunque un archivo hash como MD5, SHA1 o SHA256 es valioso para identificar muestras idénticas con nombres dispares, se queda corto cuando se identifica muestras de malware similares. Esto se debe principalmente a que un autor de malware puede alterar el valor del hash del archivo haciendo modificaciones menores al código y lo recompensa.

No obstante, existen técnicas que pueden ayudar a identificar muestras similares:

### **Indicador de importación (Imphash)**

`IMPHASH`, Una abreviatura de "hash de importación", es un hash criptográfico calculado a partir de las funciones de importación de un archivo ejecutable portátil de Windows (PE). Su algoritmo funciona al convertir primero todos los nombres de funciones importados en minúsculas. Después de esto, los nombres de DLL y los nombres de funciones se fusionan y se organizan en orden alfabético. Finalmente, se genera un hash MD5 a partir de la cadena resultante. Por lo tanto, dos archivos PE con funciones de importación idénticas, en la misma secuencia, compartirán un`IMPHASH`valor.

Podemos encontrar el`IMPHASH`en el`Details`Pestaña de los resultados virustotales.

![](https://academy.hackthebox.com/storage/modules/227/imphash_vt1.png)

Tenga en cuenta que también podemos usar el[pezón](https://pypi.org/project/pefile/)Módulo python para calcular el`IMPHASH`de un archivo de la siguiente manera.

Código:pitón

```python
import sys
import pefile
import peutils

pe_file = sys.argv[1]
pe = pefile.PE(pe_file)
imphash = pe.get_imphash()

print(imphash)

```

Para comprobar el`IMPHASH`del malware WannaCry mencionado el comando sería el siguiente.`imphash_calc.py`Contiene el código Python anterior.

Análisis estático en Windows

```
C:\Scripts> python imphash_calc.py C:\Samples\MalwareAnalysis\Ransomware.wannacry.exe
9ecee117164e0b870a53dd187cdd7174

```

### **Hashing Fuzzy (ssdeep)**

`Fuzzy Hashing (SSDEEP)`, también conocido como hashis-Poistise (CTPH) activado por el contexto (CTPH), es una técnica de hash diseñada para calcular un valor hash indicativo de similitud de contenido entre dos archivos. Esta técnica disecciona un archivo en bloques de tamaño fijo más pequeños y calcula un hash para cada bloque. Los valores hash resultantes se consolidan para generar el hash difuso final.

El`SSDEEP`El algoritmo asigna más peso a secuencias más largas de bloques comunes, lo que lo hace altamente efectivo para identificar archivos que han sufrido modificaciones menores, o son similares pero no idénticas, como diferentes variaciones de una muestra maliciosa.

Podemos encontrar el`SSDEEP`hash de un malware en el`Details`Pestaña de los resultados virustotales.

También podemos usar el`ssdeep`herramienta (disponible en`C:\Tools\ssdeep-2.14.1`) para calcular el`SSDEEP`hash de un archivo. Para comprobar el`SSDEEP`El hash del malware WannaCry mencionado, el comando sería el siguiente.

Análisis estático en Windows

```
C:\Tools\ssdeep-2.14.1> ssdeep.exe C:\Samples\MalwareAnalysis\Ransomware.wannacry.exe
ssdeep,1.1--blocksize:hash:hash,filename
98304:wDqPoBhz1aRxcSUDk36SAEdhvxWa9P593R8yAVp2g3R:wDqPe1Cxcxk3ZAEUadzR8yc4gB,"C:\Samples\MalwareAnalysis\Ransomware.wannacry.exe"

```

![](https://academy.hackthebox.com/storage/modules/227/win_ssdeep.png)

### **Sección de hash (secciones de PE de hash)**

`Section hashing`, (Hiscing Secciones de PE) es una técnica poderosa que permite a los analistas identificar secciones de un archivo ejecutable portátil (PE) que se ha modificado. Esto puede ser particularmente útil para identificar variaciones menores en muestras de malware, una táctica común empleada por los atacantes para evadir la detección.

El`Section Hashing`La técnica funciona calculando el hash criptográfico de cada una de estas secciones. Al comparar dos archivos PE, si el hash de las secciones correspondientes en los dos archivos coincide, sugiere que la sección particular no se ha modificado entre las dos versiones del archivo.

Aplicando`section hashing`, los analistas de seguridad pueden identificar partes de un archivo PE que han sido alterados o alterados. Esto puede ayudar a identificar muestras de malware similares, incluso si se han modificado ligeramente para evadir los métodos de detección basados en la firma tradicionales.

Herramientas como`pefile`En Python se puede usar para realizar`section hashing`. En Python, por ejemplo, puede usar el módulo PeFile para acceder y hash los datos en secciones individuales de un archivo PE de la siguiente manera.

Código:pitón

```python
import sys
import pefile
pe_file = sys.argv[1]
pe = pefile.PE(pe_file)
for section in pe.sections:
    print (section.Name, "MD5 hash:", section.get_hash_md5())
    print (section.Name, "SHA256 hash:", section.get_hash_sha256())

```

Recuerda que mientras`section hashing`es una técnica poderosa, no es infalible. Los autores de malware pueden emplear tácticas como la ofuscación del nombre de la sección o generar dinámicamente los nombres de la sección para tratar de evitar este tipo de análisis.

Como ilustración, para verificar el hash del archivo MD5 del malware mencionado. Podemos usar`pestudio`(disponible en`C:\Tools\pestudio\pestudio`) como sigue.

![](https://academy.hackthebox.com/storage/modules/227/win_section_hashing.png)

# **Análisis de cadenas**

En esta fase, nuestro objetivo es extraer cadenas (ASCII y unicode) de un binario. Las cadenas pueden proporcionar pistas y una valiosa información sobre la funcionalidad del malware. Ocasionalmente, podemos desenterrar cuerdas integradas únicas en una muestra de malware, como:

- Nombres de archivo integrados (por ejemplo, archivos eliminados)
- Direcciones IP o nombres de dominio
- Rutas o claves de registro
- Funciones de la API de Windows
- Argumentos de línea de comandos
- Información única que podría insinuar un actor de amenaza particular

Las ventanas`strings`binario de`Sysinternals`Se puede implementar para mostrar las cadenas contenidas dentro de un malware. Por ejemplo, el siguiente comando revelará cadenas para una muestra de ransomware llamada`dharma_sample.exe`residiendo en el`C:\Samples\MalwareAnalysis`directorio del objetivo de esta sección.

Análisis estático en Windows

```
C:\Users\htb-student> strings C:\Samples\MalwareAnalysis\dharma_sample.exe

Strings v2.54 - Search for ANSI and Unicode strings in binary images.
Copyright (C) 1999-2021 Mark Russinovich
Sysinternals - www.sysinternals.com

!This program cannot be run in DOS mode.
gaT
Rich
.text
`.rdata
@.data
HQh
9A s
9A$v
---SNIP---
GetProcAddress
LoadLibraryA
WaitForSingleObject
InitializeCriticalSectionAndSpinCount
LeaveCriticalSection
GetLastError
EnterCriticalSection
ReleaseMutex
CloseHandle
KERNEL32.dll
RSDS%~m
#ka
C:\crysis\Release\PDB\payload.pdb
---SNIP---

```

Ocasionalmente, el análisis de cadenas puede facilitar el enlace de una muestra de malware a un grupo de amenazas específico si se identifican similitudes significativas. Para[ejemplo](https://thedfirreport.com/2020/06/16/the-little-ransomware-that-couldnt-dharma/), en el enlace proporcionado, se usó una cadena que contiene una ruta PDB para vincular la muestra de malware a la familia de ransomware Dharma/Crysis.

![](https://academy.hackthebox.com/storage/modules/227/dharma.png)

---

Cabe señalar que el`FLOSS`La herramienta también está disponible para los sistemas operativos de Windows.

El siguiente comando revelará cadenas para una muestra de malware llamada`shell.exe`residiendo en el`C:\Samples\MalwareAnalysis`directorio del objetivo de esta sección.

Análisis estático en Windows

```
C:\Samples\MalwareAnalysis> floss shell.exe
INFO: floss: extracting static strings...
finding decoding function features: 100%|████████████████████████████████████████████| 85/85 [00:00<00:00, 1361.51 functions/s, skipped 0 library functions]
INFO: floss.stackstrings: extracting stackstrings from 56 functions
INFO: floss.results: AQAPRQVH1
INFO: floss.results: JJM1
INFO: floss.results: RAQH
INFO: floss.results: AXAX^YZAXAYAZH
INFO: floss.results: XAYZH
INFO: floss.results: ws232
extracting stackstrings: 100%|██████████████████████████████████████████████████████████████████████████████████████| 56/56 [00:00<00:00, 81.46 functions/s]
INFO: floss.tightstrings: extracting tightstrings from 4 functions...
extracting tightstrings from function 0x402a90: 100%|█████████████████████████████████████████████████████████████████| 4/4 [00:00<00:00, 25.59 functions/s]
INFO: floss.string_decoder: decoding strings
emulating function 0x402a90 (call 1/1): 100%|███████████████████████████████████████████████████████████████████████| 22/22 [00:14<00:00,  1.51 functions/s]
INFO: floss: finished execution after 25.20 seconds

FLARE FLOSS RESULTS (version v2.3.0-0-g037fc4b)

+------------------------+------------------------------------------------------------------------------------+
| file path              | shell.exe                                                                          |
| extracted strings      |                                                                                    |
|  static strings        | 254                                                                                |
|  stack strings         | 6                                                                                  |
|  tight strings         | 0                                                                                  |
|  decoded strings       | 0                                                                                  |
+------------------------+------------------------------------------------------------------------------------+

 ──────────────────────
  FLOSS STATIC STRINGS
 ──────────────────────

+-----------------------------------+
| FLOSS STATIC STRINGS: ASCII (254) |
+-----------------------------------+

!This program cannot be run in DOS mode.
.text
P`.data
.rdata
`@.pdata
0@.xdata
0@.bss
.idata
.CRT
.tls
8MZu
HcP<H
D$ H
AUATUWVSH
D$ L
---SNIP---
C:\Windows\System32\notepad.exe
Message
Connection sent to C2
[-] Error code is : %lu
AQAPRQVH1
JJM1
RAQH
AXAX^YZAXAYAZH
XAYZH
ws2_32
PPM1
APAPH
WWWM1
VPAPAPAPI
Windows-Update/7.6.7600.256 %s
1Lbcfr7sAHTD9CgdQo3HTMTkV8LK4ZnX71
open
SOFTWARE\Microsoft\Windows\CurrentVersion\Run
WindowsUpdater
---SNIP---
TEMP
svchost.exe
%s\%s
http://ms-windows-update.com/svchost.exe
45.33.32.156
Sandbox detected
iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com
SOFTWARE\VMware, Inc.\VMware Tools
InstallPath
C:\Program Files\VMware\VMware Tools\
Failed to open the registry key.
Unknown error
Argument domain error (DOMAIN)
Overflow range error (OVERFLOW)
Partial loss of significance (PLOSS)
Total loss of significance (TLOSS)
The result is too small to be represented (UNDERFLOW)
Argument singularity (SIGN)
_matherr(): %s in %s(%g, %g)  (retval=%g)
Mingw-w64 runtime failure:
Address %p has no image-section
  VirtualQuery failed for %d bytes at address %p
  VirtualProtect failed with code 0x%x
  Unknown pseudo relocation protocol version %d.
  Unknown pseudo relocation bit size %d.
.pdata
RegCloseKey
RegOpenKeyExA
RegQueryValueExA
RegSetValueExA
CloseHandle
CreateFileA
CreateProcessA
CreateRemoteThread
DeleteCriticalSection
EnterCriticalSection
GetComputerNameA
GetCurrentProcess
GetCurrentProcessId
GetCurrentThreadId
GetLastError
GetStartupInfoA
GetSystemTimeAsFileTime
GetTickCount
InitializeCriticalSection
LeaveCriticalSection
OpenProcess
QueryPerformanceCounter
RtlAddFunctionTable
RtlCaptureContext
RtlLookupFunctionEntry
RtlVirtualUnwind
SetUnhandledExceptionFilter
Sleep
TerminateProcess
TlsGetValue
UnhandledExceptionFilter
VirtualAllocEx
VirtualProtect
VirtualQuery
WriteFile
WriteProcessMemory
__C_specific_handler
__getmainargs
__initenv
__iob_func
__lconv_init
__set_app_type
__setusermatherr
_acmdln
_amsg_exit
_cexit
_fmode
_initterm
_onexit
_vsnprintf
abort
calloc
exit
fprintf
free
fwrite
getenv
malloc
memcpy
printf
puts
signal
sprintf
strcmp
strlen
strncmp
vfprintf
ShellExecuteA
MessageBoxA
InternetCloseHandle
InternetOpenA
InternetOpenUrlA
InternetReadFile
WSACleanup
WSAStartup
closesocket
connect
freeaddrinfo
getaddrinfo
htons
inet_addr
socket
ADVAPI32.dll
KERNEL32.dll
msvcrt.dll
SHELL32.dll
USER32.dll
WININET.dll
WS2_32.dll

+------------------------------------+
| FLOSS STATIC STRINGS: UTF-16LE (0) |
+------------------------------------+

 ─────────────────────
  FLOSS STACK STRINGS
 ─────────────────────

AQAPRQVH1
JJM1
RAQH
AXAX^YZAXAYAZH
XAYZH
ws232

 ─────────────────────
  FLOSS TIGHT STRINGS
 ─────────────────────

 ───────────────────────
  FLOSS DECODED STRINGS
 ───────────────────────

```

# **Desempaquetado de malware lleno de upx**

En nuestro análisis estático, podríamos tropezar con una muestra de malware que ha sido comprimida u ofuscada utilizando una técnica denominada empaque. El embalaje sirve varios propósitos:

- Ofusiona el código, lo que hace que sea más difícil discernir su estructura o funcionalidad.
- Reduce el tamaño del ejecutable, lo que hace que sea más rápido transferir o menos conspicuo.
- Confiere a los investigadores de seguridad al obstaculizar los intentos tradicionales de ingeniería inversa.

Esto puede afectar el análisis de cadenas porque las referencias a las cadenas generalmente están oscurecidas o eliminadas. También reemplaza o camufla las secciones de PE convencionales con un trozo de cargador compacto, que recupera el código original de una sección de datos comprimido. Como resultado, el archivo de malware se vuelve más pequeño y más difícil de analizar, ya que el código original no es directamente observable.

Un empacador popular utilizado en muchas variantes de malware es el`Ultimate Packer for Executables (UPX)`.

Primero veamos qué sucede cuando ejecutamos el`strings`comando en una muestra de malware llena de upx llamada`credential_stealer.exe`residiendo en el`C:\Samples\MalwareAnalysis\packed`directorio del objetivo de esta sección.

Análisis estático en Windows

```
C:\Users\htb-student> strings C:\Samples\MalwareAnalysis\packed\credential_stealer.exe

Strings v2.54 - Search for ANSI and Unicode strings in binary images.
Copyright (C) 1999-2021 Mark Russinovich
Sysinternals - www.sysinternals.com

!This program cannot be run in DOS mode.
UPX0
UPX1
UPX2
3.96
UPX!
ff.
8MZu
HcP<H
tY)
L~o
tK1
7c0
VDgxt
amE
8#v
$ /uX
OAUATUWVSH
Z6L
<=h
%0rv
o?H9
7sk
3H{
HZu
'.}
c|/
c`fG
Iq%
[^_]A\A]
> -P
fo{Wnl
c9"^$!=
;\V
%&m
')A
v/7>
07ZC
_L$AAl
mug.%(
t%n
#8%,X
e]'^
(hk
Dks
zC:
Vj<
w~5
m<6
|$PD
c(t
\3_
---SNIP---

```

Observe las cuerdas que incluyen`UPX`y tenga en cuenta que el resto de la salida no produce ninguna información valiosa sobre la funcionalidad del malware.

![](https://academy.hackthebox.com/storage/modules/227/upx_sections.png)

Podemos desempaquetar el malware utilizando el`UPX`herramienta (disponible en`C:\Tools\upx\upx-4.0.2-win64`) con el siguiente comando.

Análisis estático en Windows

```
C:\Tools\upx\upx-4.0.2-win64> upx -d -o unpacked_credential_stealer.exe C:\Samples\MalwareAnalysis\packed\credential_stealer.exe
                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2023
UPX 4.0.2       Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 30th 2023

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
     16896 <-      8704   51.52%    win64/pe     unpacked_credential_stealer.exe

Unpacked 1 file.

```

Vamos a ejecutar el`strings`comando en la muestra desempaquetada.

Análisis estático en Windows

```
C:\Tools\upx\upx-4.0.2-win64> strings unpacked_credential_stealer.exe

Strings v2.54 - Search for ANSI and Unicode strings in binary images.
Copyright (C) 1999-2021 Mark Russinovich
Sysinternals - www.sysinternals.com

!This program cannot be run in DOS mode.
.text
P`.data
.rdata
`@.pdata
0@.xdata
0@.bss
.idata
.CRT
.tls
ff.
8MZu
HcP<H
---SNIP---
D$(
D$
D$0
D$(
D$
t'H
%5T
@A\A]A^
SeDebugPrivilege
SE Debug Privilege is adjusted
lsass.exe
Searching lsass PID
Lsass PID is: %lu
Error is - %lu
lsassmem.dmp
LSASS Memory is dumped successfully
Err 2: %lu
@u@
`p@
Unknown error
Argument domain error (DOMAIN)
Overflow range error (OVERFLOW)
Partial loss of significance (PLOSS)
Total loss of significance (TLOSS)
The result is too small to be represented (UNDERFLOW)
Argument singularity (SIGN)
_matherr(): %s in %s(%g, %g)  (retval=%g)
Mingw-w64 runtime failure:
Address %p has no image-section
  VirtualQuery failed for %d bytes at address %p
  VirtualProtect failed with code 0x%x
  Unknown pseudo relocation protocol version %d.
  Unknown pseudo relocation bit size %d.
.pdata
 0@
00@
`E@
`E@
@v@
hy@
`y@
@p@
0v@
Pp@
AdjustTokenPrivileges
LookupPrivilegeValueA
OpenProcessToken
MiniDumpWriteDump
CloseHandle
CreateFileA
CreateToolhelp32Snapshot
DeleteCriticalSection
EnterCriticalSection
GetCurrentProcess
GetCurrentProcessId
GetCurrentThreadId
GetLastError
GetStartupInfoA
GetSystemTimeAsFileTime
GetTickCount
InitializeCriticalSection
LeaveCriticalSection
OpenProcess
Process32First
Process32Next
QueryPerformanceCounter
RtlAddFunctionTable
RtlCaptureContext
RtlLookupFunctionEntry
RtlVirtualUnwind
SetUnhandledExceptionFilter
Sleep
TerminateProcess
TlsGetValue
UnhandledExceptionFilter
VirtualProtect
VirtualQuery
__C_specific_handler
__getmainargs
__initenv
__iob_func
__lconv_init
__set_app_type
__setusermatherr
_acmdln
_amsg_exit
_cexit
_fmode
_initterm
_onexit
abort
calloc
exit
fprintf
free
fwrite
malloc
memcpy
printf
puts
signal
strcmp
strlen
strncmp
vfprintf
ADVAPI32.dll
dbghelp.dll
KERNEL32.DLL
msvcrt.dll

```

Ahora, observamos una salida más comprensible que incluye las cadenas reales presentes en la muestra.