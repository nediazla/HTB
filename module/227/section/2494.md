# Análisis estático en Linux

En el ámbito del análisis de malware, ejercemos un método llamado análisis estático para analizar el malware sin requerir su ejecución. Esto implica la investigación meticulosa del código de malware, los datos y los componentes estructurales, que sirven como un precursor vital para un análisis más detallado.

A través del análisis estático, nos esforzamos por extraer información fundamental que incluye:

- Tipo de archivo
- Archivo hash
- Instrumentos de cuerda
- Elementos integrados
- Información de empacador
- Importaciones
- Exportaciones
- Código de ensamblaje

![](https://academy.hackthebox.com/storage/modules/227/static_analysis_flow.png)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, ssh en la IP objetivo utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Identificar el tipo de archivo**

Nuestro primer puerto de escala en esta etapa es determinar la información rudimentaria sobre el espécimen de malware para sentar las bases para nuestra investigación. Dado que las extensiones de archivos se pueden manipular y cambiar, nuestra tarea es idear un método para identificar el tipo de archivo real que estamos encontrando. El establecimiento del tipo de archivo juega un papel integral en el análisis estático, asegurando que los procedimientos que aplicamos sean apropiados y los resultados obtenidos son precisos.

Usemos un malware basado en Windows llamado`Ransomware.wannacry.exe`residiendo en el`/home/htb-student/Samples/MalwareAnalysis`directorio del objetivo de esta sección como ilustración.

El comando para verificar el tipo de archivo de este malware sería el siguiente.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ file /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe/home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe: PE32 executable (GUI) Intel 80386, for MS Windows

```

También podemos hacer lo mismo revisando manualmente el encabezado con la ayuda del`hexdump`comando de la siguiente manera.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ hexdump -C /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe | more00000000  4d 5a 90 00 03 00 00 00  04 00 00 00 ff ff 00 00  |MZ..............|
00000010  b8 00 00 00 00 00 00 00  40 00 00 00 00 00 00 00  |........@.......|
00000020  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
00000030  00 00 00 00 00 00 00 00  00 00 00 00 f8 00 00 00  |................|
00000040  0e 1f ba 0e 00 b4 09 cd  21 b8 01 4c cd 21 54 68  |........!..L.!Th|
00000050  69 73 20 70 72 6f 67 72  61 6d 20 63 61 6e 6e 6f  |is program canno|
00000060  74 20 62 65 20 72 75 6e  20 69 6e 20 44 4f 53 20  |t be run in DOS |
00000070  6d 6f 64 65 2e 0d 0d 0a  24 00 00 00 00 00 00 00  |mode....$.......|00000080  55 3c 53 90 11 5d 3d c3  11 5d 3d c3 11 5d 3d c3  |U<S..]=..]=..]=.|
00000090  6a 41 31 c3 10 5d 3d c3  92 41 33 c3 15 5d 3d c3  |jA1..]=..A3..]=.|
000000a0  7e 42 37 c3 1a 5d 3d c3  7e 42 36 c3 10 5d 3d c3  |~B7..]=.~B6..]=.|
000000b0  7e 42 39 c3 15 5d 3d c3  d2 52 60 c3 1a 5d 3d c3  |~B9..]=..R`..]=.|
000000c0  11 5d 3c c3 4a 5d 3d c3  27 7b 36 c3 10 5d 3d c3  |.]<.J]=.'{6..]=.|
000000d0  d6 5b 3b c3 10 5d 3d c3  52 69 63 68 11 5d 3d c3  |.[;..]=.Rich.]=.|
000000e0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
000000f0  00 00 00 00 00 00 00 00  50 45 00 00 4c 01 04 00  |........PE..L...|
00000100  cc 8e e7 4c 00 00 00 00  00 00 00 00 e0 00 0f 01  |...L............|
00000110  0b 01 06 00 00 90 00 00  00 30 38 00 00 00 00 00  |.........08.....|
00000120  16 9a 00 00 00 10 00 00  00 a0 00 00 00 00 40 00  |..............@.|
00000130  00 10 00 00 00 10 00 00  04 00 00 00 00 00 00 00  |................|
00000140  04 00 00 00 00 00 00 00  00 b0 66 00 00 10 00 00  |..........f.....|
00000150  00 00 00 00 02 00 00 00  00 00 10 00 00 10 00 00  |................|
00000160  00 00 10 00 00 10 00 00  00 00 00 00 10 00 00 00  |................|
00000170  00 00 00 00 00 00 00 00  e0 a1 00 00 a0 00 00 00  |................|
00000180  00 00 31 00 54 a4 35 00  00 00 00 00 00 00 00 00  |..1.T.5.........|
00000190  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*

```

En un sistema de Windows, la presencia de la cadena ASCII`MZ`(En hexadecimal:`4D 5A`) al comienzo de un archivo (conocido como "número mágico") denota un archivo ejecutable.`MZ`significa Mark Zbikowski, un arquitecto clave de MS-DOS.

# **Huellas dactilares de malware**

En esta etapa, nuestra misión es crear un identificador único para la muestra de malware. Esto generalmente toma la forma de un hash criptográfico - MD5, SHA1 o SHA256.

Se emplea huellas dactilares para numerosos fines, que abarca:

- Identificación y seguimiento de muestras de malware
- Escanear un sistema completo para la presencia de malware idéntico
- Confirmación de encuentros y análisis anteriores del mismo malware
- Compartir con las partes interesadas como COI (indicadores de compromiso) o como parte de los informes de inteligencia de amenazas

Como ilustración, para verificar el hash del archivo MD5 del malware mencionado anteriormente, el comando sería el siguiente.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ md5sum /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exedb349b97c37d22f5ea1d1841e3c89eb4  /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe

```

Para verificar el hash del archivo SHA256 del malware mencionado anteriormente, el comando sería el siguiente.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ sha256sum /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe24d004a104d4d54034dbcffc2a4b19a11f39008a575aa614ea04703480b1022c  /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe

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

Tenga en cuenta que también podemos usar el[pezón](https://pypi.org/project/pefile/)Módulo de Python para calcular el imphash de un archivo de la siguiente manera.

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

Para verificar el imphash de los mencionados[Wanna](https://www.fortinet.com/resources/cyberglossary/wannacry-ransomware-attack)Malware El comando sería el siguiente.`imphash_calc.py`(disponible en`/home/htb-student`) contiene el código Python anterior.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ python3 imphash_calc.py /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe9ecee117164e0b870a53dd187cdd7174

```

### **Hashing Fuzzy (ssdeep)**

`Fuzzy Hashing (SSDEEP)`, también conocido como hashis-Poistise (CTPH) activado por el contexto (CTPH), es una técnica de hash diseñada para calcular un valor hash indicativo de similitud de contenido entre dos archivos. Esta técnica disecciona un archivo en bloques de tamaño fijo más pequeños y calcula un hash para cada bloque. Los valores hash resultantes se consolidan para generar el hash difuso final.

El`SSDEEP`El algoritmo asigna más peso a secuencias más largas de bloques comunes, lo que lo hace altamente efectivo para identificar archivos que han sufrido modificaciones menores, o son similares pero no idénticas, como diferentes variaciones de una muestra maliciosa.

Podemos encontrar el`SSDEEP`hash de un malware en el`Details`Pestaña de los resultados virustotales.

También podemos usar el`ssdeep`comandar para calcular el`SSDEEP`hash de un archivo. Para comprobar el`SSDEEP`El hash del malware WannaCry mencionado, el comando sería el siguiente.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ ssdeep /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exessdeep,1.1--blocksize:hash:hash,filename
98304:wDqPoBhz1aRxcSUDk36SAEdhvxWa9P593R8yAVp2g3R:wDqPe1Cxcxk3ZAEUadzR8yc4gB,"/home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exe"

```

![](https://academy.hackthebox.com/storage/modules/227/ssdeep_vt1.png)

Los argumentos de la línea de comandos`-pb`se puede utilizar para iniciar el modo de coincidencia en`SSDEEP`(Mientras estamos en el directorio donde se almacenan las muestras de malware -`/home/htb-student/Samples/MalwareAnalysis`en nuestro caso).

Análisis estático en Linux

```
xnoxos@htb[/htb]$ ssdeep -pb *potato.exe matches svchost.exe (99)

svchost.exe matches potato.exe (99)

```

- `p`denota un modo de juego bonito y`b`se usa para mostrar solo los nombres de archivo, excluyendo rutas completas.

En el ejemplo anterior, se observó una similitud del 99% entre dos muestras de malware (`svchost.exe`y`potato.exe`) usando`SSDEEP`.

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

Como ilustración, para verificar los hashes de la sección MD5 y SHA256 PE de un ejecutable WannaCry almacenado en el`/home/htb-student/Samples/MalwareAnalysis`Directorio, el comando sería el siguiente.`section_hashing.py`(disponible en`/home/htb-student`) contiene el código Python anterior.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ python3 section_hashing.py /home/htb-student/Samples/MalwareAnalysis/Ransomware.wannacry.exeb'.text\x00\x00\x00' MD5 hash: c7613102e2ecec5dcefc144f83189153
b'.text\x00\x00\x00' SHA256 hash: 7609ecc798a357dd1a2f0134f9a6ea06511a8885ec322c7acd0d84c569398678
b'.rdata\x00\x00' MD5 hash: d8037d744b539326c06e897625751cc9
b'.rdata\x00\x00' SHA256 hash: 532e9419f23eaf5eb0e8828b211a7164cbf80ad54461bc748c1ec2349552e6a2
b'.data\x00\x00\x00' MD5 hash: 22a8598dc29cad7078c291e94612ce26
b'.data\x00\x00\x00' SHA256 hash: 6f93fb1b241a990ecc281f9c782f0da471628f6068925aaf580c1b1de86bce8a
b'.rsrc\x00\x00\x00' MD5 hash: 12e1bd7375d82cca3a51ca48fe22d1a9
b'.rsrc\x00\x00\x00' SHA256 hash: 1efe677209c1284357ef0c7996a1318b7de3836dfb11f97d85335d6d3b8a8e42

```

# **Análisis de cadenas**

En esta fase, nuestro objetivo es extraer cadenas (ASCII y unicode) de un binario. Las cadenas pueden proporcionar pistas y una valiosa información sobre la funcionalidad del malware. Ocasionalmente, podemos desenterrar cuerdas integradas únicas en una muestra de malware, como:

- Nombres de archivo integrados (por ejemplo, archivos eliminados)
- Direcciones IP o nombres de dominio
- Rutas o claves de registro
- Funciones de la API de Windows
- Argumentos de línea de comandos
- Información única que podría insinuar un actor de amenaza particular

El Linux`strings`El comando se puede implementar para mostrar las cadenas contenidas dentro de un malware. Por ejemplo, el siguiente comando revelará cadenas para una muestra de ransomware llamada`dharma_sample.exe`residiendo en el`/home/htb-student/Samples/MalwareAnalysis`directorio del objetivo de esta sección.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ strings -n 15 /home/htb-student/Samples/MalwareAnalysis/dharma_sample.exe!This program cannot be run in DOS mode.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@>@@@?456789:;<=@@@@@@@
!"#$%&'()*+,-./0123@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
WaitForSingleObject
InitializeCriticalSectionAndSpinCount
LeaveCriticalSection
EnterCriticalSection
C:\crysis\Release\PDB\payload.pdb
0123456789ABCDEF

```

- `n`Especifica imprimir una secuencia de al menos el número especificado: en nuestro caso,`15`.

Ocasionalmente, el análisis de cadenas puede facilitar el enlace de una muestra de malware a un grupo de amenazas específico si se identifican similitudes significativas. Para[ejemplo](https://thedfirreport.com/2020/06/16/the-little-ransomware-that-couldnt-dharma/), en el enlace proporcionado, se usó una cadena que contiene una ruta PDB para vincular la muestra de malware a la familia de ransomware Dharma/Crysis.

![](https://academy.hackthebox.com/storage/modules/227/dharma.png)

---

Cabe señalar que existe otra solución de análisis de cadenas llamada`FLOSS`. `FLOSS`, abreviatura de "FireEye Labs ofuscated Streding Solucion", es una herramienta desarrollada por el equipo de FireEye's Flare para desobfuscar automáticamente las cadenas en malware. Está diseñado para complementar el uso de herramientas de cadena tradicionales, como el comando Strings en sistemas basados en UNIX, que pueden perder las cadenas ofuscadas que comúnmente utilizan el malware para evadir la detección.

Por ejemplo, el siguiente comando revelará cadenas para una muestra de ransomware llamada`dharma_sample.exe`residiendo en el`/home/htb-student/Samples/MalwareAnalysis`directorio del objetivo de esta sección.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ floss /home/htb-student/Samples/MalwareAnalysis/dharma_sample.exeINFO: floss: extracting static strings...
finding decoding function features: 100%|███████████████████████████████████████| 238/238 [00:00<00:00, 838.37 functions/s, skipped 5 library functions (2%)]
INFO: floss.stackstrings: extracting stackstrings from 223 functions
INFO: floss.results: %sh(
extracting stackstrings: 100%|████████████████████████████████████████████████████████████████████████████████████| 223/223 [00:01<00:00, 133.51 functions/s]
INFO: floss.tightstrings: extracting tightstrings from 10 functions...
extracting tightstrings from function 0x4065e0: 100%|████████████████████████████████████████████████████████████████| 10/10 [00:01<00:00,  5.91 functions/s]
INFO: floss.string_decoder: decoding strings
INFO: floss.results: EEED
INFO: floss.results: EEEDnnn
INFO: floss.results: uOKm
INFO: floss.results: %sh(
INFO: floss.results: uBIA
INFO: floss.results: uBIA
INFO: floss.results: \t\t\t\t\t\t\t\t
emulating function 0x405840 (call 4/9): 100%|████████████████████████████████████████████████████████████████████████| 25/25 [00:11<00:00,  2.19 functions/s]
INFO: floss: finished execution after 23.56 seconds

FLARE FLOSS RESULTS (version v2.0.0-0-gdd9bea8)
+------------------------+------------------------------------------------------------------------------------+
| file path              | /home/htb-student/Samples/MalwareAnalysis/dharma_sample.exe                        |
| extracted strings      |                                                                                    |
|  static strings        | 720                                                                                |
|  stack strings         | 1                                                                                  |
|  tight strings         | 0                                                                                  |
|  decoded strings       | 7                                                                                  |
+------------------------+------------------------------------------------------------------------------------+

------------------------------
| FLOSS STATIC STRINGS (720) |
------------------------------
-----------------------------
| FLOSS ASCII STRINGS (716) |
-----------------------------
!This program cannot be run in DOS mode.
Rich
.text
`.rdata
@.data
9A s
9A$vA +B$
---SNIP---
+o*7
0123456789ABCDEF

------------------------------
| FLOSS UTF-16LE STRINGS (4) |
------------------------------
jjjj
%sh(
ssbss
0123456789ABCDEF

---------------------------
| FLOSS STACK STRINGS (1) |
---------------------------
%sh(

---------------------------
| FLOSS TIGHT STRINGS (0) |
---------------------------

-----------------------------
| FLOSS DECODED STRINGS (7) |
-----------------------------
EEED
EEEDnnn
uOKm
%sh(
uBIA
uBIA
\t\t\t\t\t\t\t\t

```

# **Desempaquetado de malware lleno de upx**

En nuestro análisis estático, podríamos tropezar con una muestra de malware que ha sido comprimida u ofuscada utilizando una técnica denominada empaque. El embalaje sirve varios propósitos:

- Ofusiona el código, lo que hace que sea más difícil discernir su estructura o funcionalidad.
- Reduce el tamaño del ejecutable, lo que hace que sea más rápido transferir o menos conspicuo.
- Confiere a los investigadores de seguridad al obstaculizar los intentos tradicionales de ingeniería inversa.

Esto puede afectar el análisis de cadenas porque las referencias a las cadenas generalmente están oscurecidas o eliminadas. También reemplaza o camufla las secciones de PE convencionales con un trozo de cargador compacto, que recupera el código original de una sección de datos comprimido. Como resultado, el archivo de malware se vuelve más pequeño y más difícil de analizar, ya que el código original no es directamente observable.

Un empacador popular utilizado en muchas variantes de malware es el`Ultimate Packer for Executables (UPX)`.

Primero veamos qué sucede cuando ejecutamos el`strings`comando en una muestra de malware llena de upx llamada`credential_stealer.exe`residiendo en el`/home/htb-student/Samples/MalwareAnalysis/packed`directorio del objetivo de esta sección.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ strings /home/htb-student/Samples/MalwareAnalysis/packed/credential_stealer.exe!This program cannot be run in DOS mode.
UPX0
UPX1
UPX2
3.96
UPX!
8MZu
HcP<H
VDgxt
$ /uXOAUATUWVSH
%0rv
o?H9
c`fG
[^_]A\A]
> -P
        fo{Wnl
c9"^$!=v/7>
07ZC
_L$AAlmug.%(
#8%,Xe]'^
---SNIP---

```

Observe las cuerdas que incluyen`UPX`y tenga en cuenta que el resto de la salida no produce ninguna información valiosa sobre la funcionalidad del malware.

Podemos desempaquetar el malware utilizando el`UPX`herramienta con el siguiente comando (mientras estamos en el directorio donde se almacenan las muestras de malware empaquetadas -`/home/htb-student/Samples/MalwareAnalysis/packed`en nuestro caso).

Análisis estático en Linux

```
xnoxos@htb[/htb]$ upx -d -o unpacked_credential_stealer.exe credential_stealer.exe                       Ultimate Packer for eXecutables
                          Copyright (C) 1996 - 2020
UPX 3.96        Markus Oberhumer, Laszlo Molnar & John Reiser   Jan 23rd 2020

        File size         Ratio      Format      Name
   --------------------   ------   -----------   -----------
     16896 <-      8704   51.52%    win64/pe     unpacked_credential_stealer.exe

Unpacked 1 file.

```

Vamos a ejecutar el`strings`comando en la muestra desempaquetada.

Análisis estático en Linux

```
xnoxos@htb[/htb]$ strings unpacked_credential_stealer.exe!This program cannot be run in DOS mode.
.text
P`.data
.rdata
`@.pdata
0@.xdata
0@.bss
.idata
.CRT
.tls
---SNIP---
AVAUATH
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