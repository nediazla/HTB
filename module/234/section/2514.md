# Desarrollo de las reglas de Yara

En esta sección, cubriremos el desarrollo manual y automatizado de las reglas de Yara.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, ssh en la IP objetivo utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Vamos a sumergirnos en el mundo de las reglas de Yara usando una muestra llamada`svchost.exe`residiendo en el`/home/htb-student/Samples/YARASigma`directorio del objetivo de esta sección como ilustración. Queremos entender el proceso detrás de elaborar una regla de Yara, así que nos ensucie las manos.

Inicialmente, necesitamos realizar un análisis de cadenas en nuestra muestra de malware.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ strings svchost.exe!This program cannot be run in DOS mode.
UPX0
UPX1
UPX2
3.96
UPX!
8MZu
HcP<H
<1~o
VDgxt
D$ /OAUATUWVSH
15384
[^_]A\A]
^$<V
---SNIP---
X]_^[H
QRAPH
(AXZY
KERNEL32.DLL
msvcrt.dll
ExitProcess
GetProcAddress
LoadLibraryA
VirtualProtect
exit

```

From the first few strings, it becomes evident that the file is packed using the UPX (Ultimate Packer for eXecutables) packer. Given this discovery, we can incorporate UPX-related strings to formulate a basic YARA rule targeting samples packed via UPX.

Código:Yara

```
rule UPX_packed_executable
{
    meta:
    description = "Detects UPX-packed executables"

    strings:
    $string_1 = "UPX0"
    $string_2 = "UPX1"
    $string_3 = "UPX2"

    condition:
    all of them
}

```

Aquí hay un breve desglose de nuestra regla de Yara elaborada para detectar ejecutables llenos de UPX:

- `Rule Name`: `UPX_packed_executable`
- `Meta Description`: Proporciona una descripción de la regla, indicando que detecta ejecutables llenos de UPX.
- `Strings Section`: `strings`Define las cadenas que la regla buscará dentro de los archivos.
    - `$string_1`= "Upx0": coincide con la cadena`UPX0`dentro del archivo.
    - `$string_2`= "Upx1": coincide con la cadena`UPX1`dentro del archivo.
    - `$string_3`= "Upx2": coincide con la cadena`UPX2`dentro del archivo.
- `Condition`: `condition`Especifica los criterios que deben cumplirse para que la regla active una coincidencia.
    - `all of them`: Especifica que todas las cadenas definidas (`$string_1`, `$string_2`, y`$string_3`) se debe encontrar dentro del archivo.

En esencia, nuestro`UPX_packed_executable`Regla (ubicada dentro del objetivo de esta sección en`/home/htb-student/Rules/yara/upx_packed.yar`) escaneos para las cuerdas`UPX0`, `UPX1`, and `UPX2` inside a file. If the rule finds all three strings, it raises an alert, hinting that the file might be packed with the UPX packer. This rule is a handy tool when we're on the lookout for executables that have undergone compression or obfuscation using the UPX method.

# **Desarrollar una regla de Yara a través de Yargen**

Continuemos nuestra inmersión en el mundo de las reglas de Yara usando una muestra llamada`dharma_sample.exe`residiendo en el`/home/htb-student/Samples/YARASigma`directorio del objetivo de esta sección.

Una vez más, necesitamos realizar un análisis de cadenas en nuestra muestra de malware.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ strings dharma_sample.exe!This program cannot be run in DOS mode.
Rich
.text
`.rdata
@.data
9A s
---SNIP---
~?h@
~?hP
hz-A
u       jd
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@>@@@?456789:;<=@@@@@@@
@@@@@@
 !"#$%&'()*+,-./0123@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
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
C:\crysis\Release\PDB\payload.pdb
---SNIP---

```

Después de ejecutar el`strings`mandar`dharma_sample.exe`, vamos`C:\crysis\Release\PDB\payload.pdb`, que es bastante único. Junto con otras cuerdas distintas, podemos elaborar una regla de Yara más refinada. Empleemos`yarGen`para acelerar este proceso.

[yargen](https://github.com/Neo23x0/yarGen)es nuestra herramienta de referencia cuando necesitamos un generador de reglas Yara automático. Lo que lo convierte en una joya es su capacidad para producir reglas de Yara basadas en cuerdas que se encuentran en archivos maliciosos mientras se esquivan las cadenas comunes en el software benigno. Esto es posible porque Yargen viene equipado con una vasta base de datos de cadenas de artículos y códigos de operación. Antes de sumergirnos, necesitamos desempaquetar los archivos zip que contienen estas bases de datos.

Así es como ponemos a Yargen en funcionamiento:

- Descargue el último lanzamiento del`release`sección
- Instalar todas las dependencias con`pip install -r requirements.txt`
- Correr`python yarGen.py --update`Para descargar automáticamente las bases de datos incorporadas. Se guardarán en la subcarpeta './dbs' (descargar:`913 MB`).
- Ver ayuda con`python yarGen.py --help`Para obtener más información sobre los parámetros de la línea de comandos.

**Nota**: yargen se puede encontrar dentro del`/home/htb-student/yarGen-0.23.4`directorio del objetivo de esta sección.

Colocemos nuestra muestra en un`temp`directorio (hay uno disponible en`/home/htb-student/temp`dentro del objetivo de esta sección) y especifique la ruta utilizando los siguientes argumentos de línea de comandos.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ python3 yarGen.py -m /home/htb-student/temp -o htb_sample.yar------------------------------------------------------------------------
                   _____
    __ _____ _____/ ___/__ ___
   / // / _ `/ __/ (_ / -_) _ \
   \_, /\_,_/_/  \___/\__/_//_/
  /___/  Yara Rule Generator
         Florian Roth, July 2020, Version 0.23.3

  Note: Rules have to be post-processed
  See this post for details: https://medium.com/@cyb3rops/121d29322282
------------------------------------------------------------------------
[+] Using identifier 'temp'
[+] Using reference 'https://github.com/Neo23x0/yarGen'
[+] Using prefix 'temp'
[+] Processing PEStudio strings ...
[+] Reading goodware strings from database 'good-strings.db' ...
    (This could take some time and uses several Gigabytes of RAM depending on your db size)
[+] Loading ./dbs/good-imphashes-part3.db ...
[+] Total: 4029 / Added 4029 entries
[+] Loading ./dbs/good-strings-part9.db ...
[+] Total: 788 / Added 788 entries
[+] Loading ./dbs/good-strings-part8.db ...
[+] Total: 332082 / Added 331294 entries
[+] Loading ./dbs/good-imphashes-part4.db ...
[+] Total: 6426 / Added 2397 entries
[+] Loading ./dbs/good-strings-part2.db ...
[+] Total: 1703601 / Added 1371519 entries
[+] Loading ./dbs/good-exports-part2.db ...
[+] Total: 90960 / Added 90960 entries
[+] Loading ./dbs/good-strings-part4.db ...
[+] Total: 3860655 / Added 2157054 entries
[+] Loading ./dbs/good-exports-part4.db ...
[+] Total: 172718 / Added 81758 entries
[+] Loading ./dbs/good-exports-part7.db ...
[+] Total: 223584 / Added 50866 entries
[+] Loading ./dbs/good-strings-part6.db ...
[+] Total: 4571266 / Added 710611 entries
[+] Loading ./dbs/good-strings-part7.db ...
[+] Total: 5828908 / Added 1257642 entries
[+] Loading ./dbs/good-exports-part1.db ...
[+] Total: 293752 / Added 70168 entries
[+] Loading ./dbs/good-exports-part3.db ...
[+] Total: 326867 / Added 33115 entries
[+] Loading ./dbs/good-imphashes-part9.db ...
[+] Total: 6426 / Added 0 entries
[+] Loading ./dbs/good-exports-part9.db ...
[+] Total: 326867 / Added 0 entries
[+] Loading ./dbs/good-imphashes-part5.db ...
[+] Total: 13764 / Added 7338 entries
[+] Loading ./dbs/good-imphashes-part8.db ...
[+] Total: 13947 / Added 183 entries
[+] Loading ./dbs/good-imphashes-part6.db ...
[+] Total: 13976 / Added 29 entries
[+] Loading ./dbs/good-strings-part1.db ...
[+] Total: 6893854 / Added 1064946 entries
[+] Loading ./dbs/good-imphashes-part7.db ...
[+] Total: 17382 / Added 3406 entries
[+] Loading ./dbs/good-exports-part6.db ...
[+] Total: 328525 / Added 1658 entries
[+] Loading ./dbs/good-imphashes-part2.db ...
[+] Total: 18208 / Added 826 entries
[+] Loading ./dbs/good-exports-part8.db ...
[+] Total: 332359 / Added 3834 entries
[+] Loading ./dbs/good-strings-part3.db ...
[+] Total: 9152616 / Added 2258762 entries
[+] Loading ./dbs/good-strings-part5.db ...
[+] Total: 12284943 / Added 3132327 entries
[+] Loading ./dbs/good-imphashes-part1.db ...
[+] Total: 19764 / Added 1556 entries
[+] Loading ./dbs/good-exports-part5.db ...
[+] Total: 404321 / Added 71962 entries
[+] Processing malware files ...
[+] Processing /home/htb-student/temp/dharma_sample.exe ...
[+] Generating statistical data ...
[+] Generating Super Rules ... (a lot of magic)
[+] Generating Simple Rules ...
[-] Applying intelligent filters to string findings ...
[-] Filtering string set for /home/htb-student/temp/dharma_sample.exe ...
[=] Generated 1 SIMPLE rules.
[=] All rules written to htb_sample.yar
[+] yarGen run finished

```

**Desglose de comando**:

- `yarGen.py`: Este es el nombre del script de Yargen Python que se ejecutará.
- `m /home/htb-student/temp`: Esta opción especifica el directorio de origen donde se encuentran los archivos de muestra (por ejemplo, malware o archivos sospechosos). El script analizará estas muestras para generar reglas de Yara.
- `o htb_sample.yar`: Esta opción indica el nombre del archivo de salida para las reglas Yara generadas. En este caso, las reglas de Yara se guardarán en un archivo nombrado`htb_sample.yar`.

Las reglas de Yara resultantes se escribirán a la`htb_sample.yar`archivo dentro del`/home/htb-student/yarGen-0.23.4`directorio del objetivo de esta sección. Veamos el contenido de la regla generada.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ cat htb_sample.yar/*
   YARA Rule Set
   Author: yarGen Rule Generator
   Date: 2023-08-24
   Identifier: temp
   Reference: https://github.com/Neo23x0/yarGen
*/

/* Rule Set ----------------------------------------------------------------- */

rule dharma_sample {
   meta:
      description = "temp - file dharma_sample.exe"
      author = "yarGen Rule Generator"
      reference = "https://github.com/Neo23x0/yarGen"
      date = "2023-08-24"
      hash1 = "bff6a1000a86f8edf3673d576786ec75b80bed0c458a8ca0bd52d12b74099071"
   strings:
$x1 = "C:\\crysis\\Release\\PDB\\payload.pdb" fullword ascii$s2 = "sssssbs" fullword ascii$s3 = "sssssbsss" fullword ascii$s4 = "RSDS%~m" fullword ascii$s5 = "{RDqP^\\" fullword ascii$s6 = "QtVN$0w" fullword ascii$s7 = "Ffsc<{" fullword ascii$s8 = "^N3Y.H_K" fullword ascii$s9 = "tb#w\\6" fullword ascii$s10 = "-j6EPUc" fullword ascii$s11 = "8QS#5@3" fullword ascii$s12 = "h1+LI;d8" fullword ascii$s13 = "H;B cl" fullword ascii$s14 = "Wy]z@p]E" fullword ascii$s15 = "ipgypA" fullword ascii$s16 = "+>^wI{H" fullword ascii$s17 = "mF@S/]" fullword ascii$s18 = "OA_<8X-|" fullword ascii$s19 = "s+aL%M" fullword ascii$s20 = "sXtY9P" fullword ascii   condition:
      uint16(0) == 0x5a4d and filesize < 300KB and
      1 of ($x*) and 4 of them}

```

Ahora, por el momento de la verdad. Despararemos Yara con nuestra regla recién acuñada para ver si huele alguna parte cuando se ejecute contra un repositorio de muestra de malware ubicado en`home/htb-student/Samples/YARASigma`Dentro del objetivo de esta sección.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ yara htb_sample.yar /home/htb-student/Samples/YARASigma dharma_sample /home/htb-student/Samples/YARASigma/dharma_sample.exe
dharma_sample /home/htb-student/Samples/YARASigma/pdf_reader.exe
dharma_sample /home/htb-student/Samples/YARASigma/microsoft.com
dharma_sample /home/htb-student/Samples/YARASigma/check_updates.exe
dharma_sample /home/htb-student/Samples/YARASigma/KB5027505.exe

```

Como podemos ver, el`pdf_reader.exe`, `microsoft.com`, `check_updates.exe`, y`KB5027505.exe`Los archivos son detectados por esta regla (además de`dharma_sample.exe`por supuesto).

# **Desarrollar manualmente una regla de Yara**

### **Ejemplo 1: rata Zoxpng utilizada por APT17**

Vamos ahora un poco más profundos ...

Queremos desarrollar una regla de Yara para escanear una variación específica de la`ZoxPNG`Rata utilizada por`APT17`Residencia en:

- Una muestra llamada`legit.exe`residiendo en el`/home/htb-student/Samples/YARASigma`Directorio del objetivo de esta sección
- A[Publicación de Intezer](https://intezer.com/blog/research/evidence-aurora-operation-still-active-part-2-more-ties-uncovered-between-ccleaner-hack-chinese-hackers-2/)
- Análisis de cadenas
- Imphash
- Tamaño de archivo de muestra común

Comencemos con nuestros esfuerzos de análisis de cadenas de la siguiente manera.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ strings legit.exe!This program cannot be run in DOS mode.
Rich
.text
`.rdata
@.data
---SNIP---
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
 deflate 1.1.4 Copyright 1995-2002 Jean-loup Gailly

 inflate 1.1.4 Copyright 1995-2002 Mark Adler
Sleep
LocalAlloc
CloseHandle
GetLastError
VirtualFree
VirtualAlloc
GetProcAddress
LoadLibraryA
GetCurrentProcessId
GlobalMemoryStatusEx
GetCurrentProcess
GetACP
GetVersionExA
GetComputerNameA
GetTickCount
GetSystemTime
LocalFree
CreateProcessA
CreatePipe
TerminateProcess
ReadFile
PeekNamedPipe
WriteFile
SetFilePointer
CreateFileA
GetFileSize
GetDiskFreeSpaceExA
GetDriveTypeA
GetLogicalDriveStringsA
CreateDirectoryA
FindClose
FindNextFileA
FindFirstFileA
MoveFileExA
OpenProcess
KERNEL32.dll
LookupAccountSidA
ADVAPI32.dll
SHFileOperationA
SHELL32.dll
strcpy
rand
sprintf
memcpy
strncpy
srand
_snprintf
atoi
strcat
strlen
printf
memset
strchr
memcmp
MSVCRT.dll
_exit
_XcptFilter
exit
__p___initenv
__getmainargs
_initterm
__setusermatherr
_adjust_fdiv
__p__commode
__p__fmode
__set_app_type
_except_handler3
_controlfp
InternetCrackUrlA
InternetCloseHandle
InternetReadFile
HttpQueryInfoA
HttpSendRequestA
InternetSetOptionA
HttpAddRequestHeadersA
HttpOpenRequestA
InternetConnectA
InternetOpenA
WININET.dll
ObtainUserAgentString
urlmon.dll
WTSFreeMemory
WTSEnumerateProcessesA
WTSAPI32.dll
GetModuleFileNameExA
PSAPI.DLL
calloc
free
http://%s/imgres?q=A380&hl=en-US&sa=X&biw=1440&bih=809&tbm=isus&tbnid=aLW4-J8Q1lmYBM:&imgrefurl=http://%s&docid=1bi0Ti1ZVr4bEM&imgurl=http://%s/%04d-%02d/%04d%02d%02d%02d%02d%02d.png&w=800&h=600&ei=CnJcUcSBL4rFkQX444HYCw&zoom=1&ved=1t:3588,r:1,s:0,i:92&iact=rc&dur=368&page=1&tbnh=184&tbnw=259&start=0&ndsp=20&tx=114&ty=58
http://0.0.0.0/1
http://0.0.0.0/2
Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NETCLR 2.0.50727)
image/pjpeg
image/jpeg
image/x-xbitmap
image/gif
Content-Type: application/x-www-form-urlencoded
B64:[%s]
Step 11
Step 10
Step 9
Step 8
Step 7
Step 6
Content-Type: image/x-png
Step 5
Step 4
Connection: close
Accept-Encoding: gzip, deflate
Accept-Language: en-US
Pragma: no-cache
User-Agent:
Cookie: SESSIONID=%s
Step 3
HTTP/1.1
Step 2
POST
Step 1
Get URL Info Error
[IISEND=0x%08X][Recv:] 0x%08X %s
IISCMD Error:%d
hWritePipe2 Error:%d
kernel32.dll
QueryFullProcessImageName
Not Support This Function!
1.1.4
need dictionary
incorrect data check
incorrect header check
invalid window size
unknown compression method
incompatible version
buffer error
insufficient memory
data error
stream error
file error
stream end
invalid bit length repeat
too many length or distance symbols
invalid stored block lengths
invalid block type
invalid distance code
invalid literal/length code
incomplete dynamic bit lengths tree
oversubscribed dynamic bit lengths tree
incomplete literal/length tree
oversubscribed literal/length tree
empty distance tree with lengths
incomplete distance tree
oversubscribed distance tree
Z0X03
>0!0
Western Cape1
Durbanville1
Thawte1
Thawte Certification1
Thawte Timestamping CA0
121221000000Z
201230235959Z0^1
Symantec Corporation100.
'Symantec Time Stamping Services CA - G20
"W*o
]jxdE
`F~T
&0$0"
http://ocsp.thawte.com0
80604
.http://crl.thawte.com/ThawteTimestampingCA.crl0
TimeStamp-2048-10
y@b%
thawte, Inc.1(0&
Certification Services Division1806
/(c) 2006 thawte, Inc. - For authorized use only1
thawte Primary Root CA0
061117000000Z
360716235959Z0
thawte, Inc.1(0&
Certification Services Division1806
/(c) 2006 thawte, Inc. - For authorized use only1
thawte Primary Root CA0
l[HhIY7
tf/j8
S}+
>n)i
B0@0
WHP0
Thawte, Inc.1$0"Thawte Code Signing CA - G20
110622000000Z
130721235959Z0
Seoul1
Seongdong-gu1
        4NB Corp.1"0
tigation Development Team1
        4NB Corp.0
LO'%
oWx6
IB-8l3
40200
*http://cs-g2-crl.thawte.com/ThawteCSG2.crl0
&0$0"
http://ocsp.thawte.com0
}1JBAe
^b/1
a{b2
Ti,[\{
thawte, Inc.1(0&
Certification Services Division1806
/(c) 2006 thawte, Inc. - For authorized use only1
thawte Primary Root CA0
100208000000Z
200207235959Z0J1
Thawte, Inc.1$0"Thawte Code Signing CA - G20
,p&7E
rqD=X
n8}v
-0+0)
#http://crl.thawte.com/ThawtePCA.crl0&0$0"
http://ocsp.thawte.com0
VeriSignMPKI-2-100
---SNIP---
Symantec Corporation100.
'Symantec Time Stamping Services CA - G20
121018000000Z
201229235959Z0b1
Symantec Corporation1402
+Symantec Time Stamping Services Signer - G40
2oNW
a;EQ
g0e0*
http://ts-ocsp.ws.symantec.com07
+http://ts-aia.ws.symantec.com/tss-ca-g2.cer0<
50301
+http://ts-crl.ws.symantec.com/tss-ca-g2.crl0(
TimeStamp-2048-20
---SNIP---
Thawte, Inc.1$0"Thawte Code Signing CA - G2
1(0&
www.4nb.co.kr 0
#Ak_$>X[hL0r0^1
Symantec Corporation100.
'Symantec Time Stamping Services CA - G2
130529085845Z0#
*PU19
mOLT
}Jdf6
K%&J
(E~Q
Eev7aSp

```

Luego usemos los hashes mencionados en la publicación de Intezer para identificar tamaños de muestra comunes. Parece que no hay muestras relacionadas cuyo tamaño sea mayor de 200 kb. Un ejemplo de una muestra identificada es el siguiente.[https://www.hybrid-analysis.com/sample/ee362a8161bd4420737775363bf5fa1305abac2ce39b903d63df0d7121ba60555050550](https://www.hybrid-analysis.com/sample/ee362a8161bd442073775363bf5fa1305abac2ce39b903d63df0d7121ba60550).

Finalmente, el ImpHash de la muestra se puede calcular de la siguiente manera, utilizando el`imphash_calc.py`script que reside en el`/home/htb-student`directorio del objetivo de esta sección.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ python3 imphash_calc.py /home/htb-student/Samples/YARASigma/legit.exe414bbd566b700ea021cfae3ad8f4d9b9

```

Una buena regla de Yara para detectar la variación mencionada de zoxpng reside en el`/home/htb-student/Rules/yara`directorio del objetivo de esta sección, guardado como`apt_apt17_mal_sep17_2.yar`.

Código:Yara

```
/*
   Yara Rule Set
   Author: Florian Roth
   Date: 2017-10-03
   Identifier: APT17 Oct 10
   Reference: https://goo.gl/puVc9q
*/

/* Rule Set ----------------------------------------------------------------- */

import "pe"

rule APT17_Malware_Oct17_Gen {
   meta:
      description = "Detects APT17 malware"
      license = "Detection Rule License 1.1 https://github.com/Neo23x0/signature-base/blob/master/LICENSE"
      author = "Florian Roth (Nextron Systems)"
      reference = "https://goo.gl/puVc9q"
      date = "2017-10-03"
      hash1 = "0375b4216334c85a4b29441a3d37e61d7797c2e1cb94b14cf6292449fb25c7b2"
      hash2 = "07f93e49c7015b68e2542fc591ad2b4a1bc01349f79d48db67c53938ad4b525d"
      hash3 = "ee362a8161bd442073775363bf5fa1305abac2ce39b903d63df0d7121ba60550"
   strings:
      $x1 = "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NETCLR 2.0.50727)" fullword ascii
      $x2 = "http://%s/imgres?q=A380&hl=en-US&sa=X&biw=1440&bih=809&tbm=isus&tbnid=aLW4-J8Q1lmYBM" ascii

      $s1 = "hWritePipe2 Error:%d" fullword ascii
      $s2 = "Not Support This Function!" fullword ascii
      $s3 = "Cookie: SESSIONID=%s" fullword ascii
      $s4 = "http://0.0.0.0/1" fullword ascii
      $s5 = "Content-Type: image/x-png" fullword ascii
      $s6 = "Accept-Language: en-US" fullword ascii
      $s7 = "IISCMD Error:%d" fullword ascii
      $s8 = "[IISEND=0x%08X][Recv:] 0x%08X %s" fullword ascii
   condition:
      ( uint16(0) == 0x5a4d and filesize < 200KB and (
            pe.imphash() == "414bbd566b700ea021cfae3ad8f4d9b9" or
            1 of ($x*) or
            6 of them
         )
      )
}

```

**Desglose de las reglas de Yara**:

- **`Rule Imports`**: Los módulos son extensiones a la funcionalidad central de Yara.
    - `import "pe"`: Importando el[Módulo de educación física](https://yara.readthedocs.io/en/stable/modules/pe.html)La regla de Yara obtiene acceso a un conjunto de funciones y estructuras especializadas que pueden inspeccionar y analizar los detalles de`PE`archivos. Esto hace que la regla sea más precisa cuando se trata de detectar características en los ejecutables de Windows.
- **`Rule Meta`**:
    - `description`: Nos dice el objetivo principal de la regla, que es detectar malware APT17.
    - `license`: Señala la ubicación y la versión de la licencia que rige el uso de esta regla de Yara.
    - `author`: La regla fue escrita por Florian Roth de Nextron Systems.
    - `reference`: Proporciona un enlace que entra en más detalles sobre el malware o el contexto de esta regla.
    - `date`: La fecha en que la regla se creó o se actualizó la última, en este caso, el 3 de octubre de 2017.
    - `hash1`, `hash2`, `hash3`: Valores hash, probablemente de muestras relacionadas con APT17, que el autor usó como referencias o como datos fundamentales para crear la regla.
- **`Rule Body`**: La regla contiene una serie de cadenas, que son indicadores potenciales del malware APT17. Estas cadenas se dividen en dos categorías
    - `$x* strings`
    - `$s* strings`
- **`Rule Condition`**: Este es el corazón de la regla, donde reside la lógica de detección real.
    - `uint16(0) == 0x5a4d`: Verifica si los dos primeros bytes del archivo son`MZ`, que es el número mágico para los ejecutables de Windows. Entonces, nos estamos centrando en detectar binarios de Windows.
    - `filesize < 200KB`: Limita la regla para escanear solo archivos pequeños, específicamente aquellos más pequeños que`200KB`.
    - `pe.imphash() == "414bbd566b700ea021cfae3ad8f4d9b9"`: Esto verifica el hash de importación (`imphash`) del archivo PE (ejecutable portátil). Los impraheses son excelentes para clasificar y agrupar muestras de malware basadas en las bibliotecas que importan.
    - `1 of ($x*)`: Al menos`one`del`$x strings`(de la sección de cadenas) debe estar presente en el archivo.
    - `6 of them`: Requiere que al menos`six`de las cuerdas (`from both $x and $s categories`) se encuentra dentro del archivo escaneado.

### **Ejemplo 2: Neurona utilizada por Turla**

Queremos desarrollar una regla de Yara para escanear en busca de instancias de`Neuron Service`utilizado por`Turla`Residencia en:

- Una muestra llamada`Microsoft.Exchange.Service.exe`residiendo en el`/home/htb-student/Samples/YARASigma`Directorio del objetivo de esta sección
- Un[Informe de análisis del Centro Nacional de Seguridad Cibernética](https://www.ncsc.gov.uk/static-assets/documents/Turla%20group%20using%20Neuron%20and%20Nautilus%20tools%20alongside%20Snake%20malware_1.pdf)

Dado que el informe menciona que tanto el cliente Neuron y el servicio neuronal están escritos utilizando el marco .NET, realizaremos .NET "revertir" en lugar de análisis de cadenas.

Esto se puede hacer usando el[monodis](https://www.mono-project.com/docs/tools+libraries/tools/monodis/)herramienta de la siguiente manera.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ monodis --output=code Microsoft.Exchange.Service.exe
```

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ cat code.assembly extern System.Configuration.Install
{
  .ver 4:0:0:0
  .publickeytoken = (B0 3F 5F 7F 11 D5 0A 3A ) // .?_....:
}
---SNIP---
  .class public auto ansi abstract sealed beforefieldinit StorageUtils
  } // end of class Utils.StorageUtils
---SNIP---
           default void ExecCMD (string path, string key, unsigned int8[] cmd, class Utils.Config cfg, class [mscorlib]System.Threading.ManualResetEvent mre)  cil managed
        IL_0028:  ldsfld class [System.Core]System.Runtime.CompilerServices.CallSite`1<class [mscorlib]System.Func`5<class [System.Core]System.Runtime.CompilerServices.CallSite,class [mscorlib]System.Type,object,class Utils.Config,class Utils.CommandScript>> Utils.Storage/'<ExecCMD>o__SiteContainer0'::'<>p__Site1'
        IL_0070:  stsfld class [System.Core]System.Runtime.CompilerServices.CallSite`1<class [mscorlib]System.Func`5<class [System.Core]System.Runtime.CompilerServices.CallSite,class [mscorlib]System.Type,object,class Utils.Config,class Utils.CommandScript>> Utils.Storage/'<ExecCMD>o__SiteContainer0'::'<>p__Site1'
        IL_0075:  ldsfld class [System.Core]System.Runtime.CompilerServices.CallSite`1<class [mscorlib]System.Func`5<class [System.Core]System.Runtime.CompilerServices.CallSite,class [mscorlib]System.Type,object,class Utils.Config,class Utils.CommandScript>> Utils.Storage/'<ExecCMD>o__SiteContainer0'::'<>p__Site1'
        IL_007f:  ldsfld class [System.Core]System.Runtime.CompilerServices.CallSite`1<class [mscorlib]System.Func`5<class [System.Core]System.Runtime.CompilerServices.CallSite,class [mscorlib]System.Type,object,class Utils.Config,class Utils.CommandScript>> Utils.Storage/'<ExecCMD>o__SiteContainer0'::'<>p__Site1'
    } // end of method Storage::ExecCMD
  .class nested private auto ansi abstract sealed beforefieldinit '<ExecCMD>o__SiteContainer0'
  } // end of class <ExecCMD>o__SiteContainer0
        IL_0077:  call void class Utils.Storage::ExecCMD(string, string, unsigned int8[], class Utils.Config, class [mscorlib]System.Threading.ManualResetEvent)
---SNIP---
        IL_0029:  ldftn void class Utils.Storage::KillOldThread()
           default void KillOldThread ()  cil managed
    } // end of method Storage::KillOldThread
---SNIP---

        IL_0201:  ldstr "EncryptScript"
        IL_04a4:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
        IL_0eff:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
        IL_0f4e:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
        IL_0fec:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
        IL_102f:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
        IL_0018:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
        IL_00b1:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
          IL_009a:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
          IL_0142:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
           default unsigned int8[] EncryptScript (unsigned int8[] pwd, unsigned int8[] data)  cil managed
    } // end of method Crypt::EncryptScript
            IL_00a0:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
          IL_0052:  call unsigned int8[] class Utils.Crypt::EncryptScript(unsigned int8[], unsigned int8[])
---SNIP---

```

Al pasar por lo anterior, podemos identificar funciones y clases dentro del ensamblaje de .NET.

**Nota**: Una mejor solución de inversión sería cargar el ensamblaje de .NET (`Microsoft.Exchange.Service.exe`) en un depurador de .NET y editor de ensamblaje como[dnspy](https://github.com/dnSpy/dnSpy).

![](https://academy.hackthebox.com/storage/modules/234/dnSpy.png)

Una buena regla de Yara para identificar casos de servicio de neuronas reside en el`/home/htb-student/Rules/yara`directorio del objetivo de esta sección, guardado como`neuron_1.yar`.

Código:Yara

```
rule neuron_functions_classes_and_vars {
 meta:
   description = "Rule for detection of Neuron based on .NET functions and class names"
   author = "NCSC UK"
   reference = "https://www.ncsc.gov.uk/file/2691/download?token=RzXWTuAB"
   reference2 = "https://www.ncsc.gov.uk/alerts/turla-group-malware"
   hash = "d1d7a96fcadc137e80ad866c838502713db9cdfe59939342b8e3beacf9c7fe29"
 strings:
   $class1 = "StorageUtils" ascii
   $class2 = "WebServer" ascii
   $class3 = "StorageFile" ascii
   $class4 = "StorageScript" ascii
   $class5 = "ServerConfig" ascii
   $class6 = "CommandScript" ascii
   $class7 = "MSExchangeService" ascii
   $class8 = "W3WPDIAG" ascii
   $func1 = "AddConfigAsString" ascii
   $func2 = "DelConfigAsString" ascii
   $func3 = "GetConfigAsString" ascii
   $func4 = "EncryptScript" ascii
   $func5 = "ExecCMD" ascii
   $func6 = "KillOldThread" ascii
   $func7 = "FindSPath" ascii
   $dotnetMagic = "BSJB" ascii
 condition:
   (uint16(0) == 0x5A4D and uint16(uint32(0x3c)) == 0x4550) and $dotnetMagic and 6 of them
}

```

**Desglose de las reglas de Yara**:

- **`Strings Section`**:
    - `$class1 = "StorageUtils" ascii`a`$class8 = "W3WPDIAG" ascii`: Estas son ocho cadenas ASCII correspondientes a los nombres de clase dentro del ensamblaje .NET.
    - `$func1 = "AddConfigAsString" ascii`a`$func7 = "FindSPath" ascii`: Estas siete cadenas ASCII representan nombres de clase o funciones dentro del ensamblaje .NET.
    - `$dotnetMagic = "BSJB" ascii`: Esta firma está presente en el encabezado CLI (infraestructura de lenguaje común) de los binarios .NET, y su presencia puede usarse para indicar que el archivo es un ensamblaje .NET. Específicamente, está en el campo de la firma del encabezado CLI, que sigue el encabezado PE y las tablas adicionales.
- **`Condition Section`**:
    - `uint16(0) == 0x5A4D`: Esto verifica si los dos primeros bytes al comienzo del archivo son`MZ`, un número mágico que indica un formato de ejecutable portátil de Windows (PE).
    - `uint16(uint32(0x3c)) == 0x4550`: Un cheque de dos pasos. Primero, lee un valor de 32 bits (4 bytes) de la compensación`0x3c`del archivo. En los archivos PE, este desplazamiento generalmente contiene un puntero al encabezado PE. Luego verifica si los dos bytes en ese puntero son`PE` (`0x4550`), indicando un encabezado PE válido. Esto asegura que el archivo sea un formato de PE legítimo y no corrupto u ofuscado.
    - `$dotnetMagic`: Verifica la presencia del`BSJB`cadena. Esta firma está presente en el encabezado CLI (infraestructura de lenguaje común) de los binarios .NET, y su presencia puede usarse para indicar que el archivo es un ensamblaje .NET.
    - `6 of them`: Esta condición establece que se deben encontrar al menos seis de las cadenas definidas (clases o funciones) dentro del archivo. Esto asegura que incluso si algunas firmas están ausentes o han sido modificadas, la regla aún se activará si queda un número sustancial.

### **Ejemplo 3: Stonedrill utilizado en los ataques de Shamoon 2.0**

Queremos desarrollar una regla de Yara para escanear en busca de instancias de`Stonedrill`utilizado en`Shamoon 2.0`ataques basados en:

- Una muestra llamada`sham2.exe`residiendo en el`/home/htb-student/Samples/YARASigma`Directorio del objetivo de esta sección
- Un[Informe de análisis de Kaspersky](https://kas.pr/9s5A)

El informe menciona:*... Muchas muestras tenían una adicional`encrypted`recurso con un nombre específico, aunque no único`101`.*

Encriptado/comprimido/ofuscado en archivos PE generalmente significa alto[entropía](https://cocomelonc.github.io/malware/2022/11/05/malware-analysis-6.html). Podemos usar el`entropy_pe_section.py`script que reside en el`/home/htb-student`El directorio del objetivo de esta sección para verificar si la sección de recursos de nuestra muestra contiene algo encriptado/comprimido de la siguiente manera.

Desarrollo de las reglas de Yara

```
xnoxos@htb[/htb]$ python3 entropy_pe_section.py -f /home/htb-student/Samples/YARASigma/sham2.exe        virtual address: 0x1000
        virtual size: 0x25f86
        raw size: 0x26000
        entropy: 6.4093453613451885
.rdata
        virtual address: 0x27000
        virtual size: 0x62d2
        raw size: 0x6400
        entropy: 4.913675128870228
.data
        virtual address: 0x2e000
        virtual size: 0xb744
        raw size: 0x9000
        entropy: 1.039771174750106
.rsrc
        virtual address: 0x3a000
        virtual size: 0xc888
        raw size: 0xca00
        entropy: 7.976847940518103

```

Notamos que la sección de recursos (`.rsrc`) tiene alta entropía (`8.0`es el valor máximo de entropía). Podemos dar por sentado que la sección de recursos contiene algo sospechoso.

Una buena regla de Yara para identificar instancias de Stonedrill reside en el`/home/htb-student/Rules/yara`directorio del objetivo de esta sección, guardado como`stonedrill.yar`.

Código:Yara

```
import "pe"
import "math"

rule susp_file_enumerator_with_encrypted_resource_101 {
meta:
  copyright = "Kaspersky Lab"
  description = "Generic detection for samples that enumerate files with encrypted resource called 101"
  reference = "https://securelist.com/from-shamoon-to-stonedrill/77725/"
  hash = "2cd0a5f1e9bcce6807e57ec8477d222a"
  hash = "c843046e54b755ec63ccb09d0a689674"
  version = "1.4"
strings:
  $mz = "This program cannot be run in DOS mode."
  $a1 = "FindFirstFile" ascii wide nocase
  $a2 = "FindNextFile" ascii wide nocase
  $a3 = "FindResource" ascii wide nocase
  $a4 = "LoadResource" ascii wide nocase

condition:
uint16(0) == 0x5A4D and
all of them and
filesize < 700000 and
pe.number_of_sections > 4 and
pe.number_of_signatures == 0 and
pe.number_of_resources > 1 and pe.number_of_resources < 15 and for any i in (0..pe.number_of_resources - 1):
( (math.entropy(pe.resources[i].offset, pe.resources[i].length) > 7.8) and pe.resources[i].id == 101 and
pe.resources[i].length > 20000 and
pe.resources[i].language == 0 and
not ($mz in (pe.resources[i].offset..pe.resources[i].offset + pe.resources[i].length))
)
}

```

**Desglose de las reglas de Yara**:

- **`Rule Imports`**: Los módulos son extensiones a la funcionalidad central de Yara.
    - `import "pe"`: Importando el[Módulo de educación física](https://yara.readthedocs.io/en/stable/modules/pe.html)La regla de Yara obtiene acceso a un conjunto de funciones y estructuras especializadas que pueden inspeccionar y analizar los detalles de`PE`archivos. Esto hace que la regla sea más precisa cuando se trata de detectar características en los ejecutables de Windows.
    - `import "math"`: Importa el módulo de matemáticas, proporcionando funciones matemáticas como cálculos de entropía.
- **`Rule Meta`**:
    - `copyright = "Kaspersky Lab"`: La regla fue escrita o tenía derechos de autor por Kaspersky Lab.
    - `description = "Generic detection for samples that enumerate files with encrypted resource called 101"`: La regla tiene como objetivo detectar muestras que enumeran los archivos y tienen un recurso cifrado con el identificador "101".
    - `reference = "https://securelist.com/from-shamoon-to-stonedrill/77725/"`: Proporciona una URL para un contexto o información adicional sobre la regla.
    - `hash`: Se dan dos hashes, probablemente como ejemplos de archivos maliciosos conocidos que coinciden con esta regla.
    - `version = "1.4"`: El número de versión de la regla Yara.
- **`Strings Section`**:
    - `$mz = "This program cannot be run in DOS mode."`: La cadena ASCII que típicamente aparece en la parte de stub DOS de un archivo PE.
    - `$a1 = "FindFirstFile", $a2 = "FindNextFile"`: Cadenas para funciones de API de Windows utilizadas para enumerar archivos. El uso de`FindFirstFileW`y`FindNextFileW`Las funciones API se pueden identificar a través del análisis de cadenas.
    - `$a3 = "FindResource", $a4 = "LoadResource"`: Como ya se mencionó, las muestras de Stonedrill cuentan con recursos cifrados. Estas cadenas se pueden encontrar a través del análisis de cadenas y están relacionadas con las funciones de la API de Windows utilizadas para manejar los recursos dentro del ejecutable.
- **`Rule Condition`**:
    - `uint16(0) == 0x5A4D`: Comprueba si los dos primeros bytes del archivo son "MZ", lo que indica un archivo de Windows PE.
    - `all of them`: Todas las cadenas $ A1, $ A2, $ A3, $ A4 deben estar presentes en el archivo.
    - `filesize < 700000`: El tamaño del archivo debe ser menor que`700,000`bytes.
    - `pe.number_of_sections > 4`: El archivo PE debe tener más de`four`secciones.
    - `pe.number_of_signatures == 0`: El archivo no debe firmarse digitalmente.
    - `pe.number_of_resources > 1 and pe.number_of_resources < 15`: El archivo debe contener más de uno pero menos que`15`recursos.
    - `for any i in (0..pe.number_of_resources - 1): ( (math.entropy(pe.resources[i].offset, pe.resources[i].length) > 7.8) and pe.resources[i].id == 101 and pe.resources[i].length > 20000 and pe.resources[i].language == 0 and not ($mz in (pe.resources[i].offset..pe.resources[i].offset + pe.resources[i].length)))`: Revise cada recurso en el archivo y verifique si la entropía de los datos de recursos es más que`7.8`y el identificador de recursos es`101`y la longitud del recurso es mayor que`20,000`bytes y el identificador de lenguaje del recurso es`0`y la cadena STUB DOS no está presente en el recurso. No es necesario que todos los recursos coincidan con la condición; Solo un recurso que cumple con todos los criterios es suficiente para que la regla general de Yara sea un partido.

# **Recursos de desarrollo de reglas de Yara**

Como puede imaginar, el mejor recurso de desarrollo de reglas de Yara es la documentación oficial, que se puede encontrar al siguiente[enlace](https://yara.readthedocs.io/en/latest/writingrules.html).

El siguiente mejor recurso sobre el desarrollo efectivo de las reglas de Yara proviene[Kaspersky](https://www.slideshare.net/KasperskyLabGlobal/upping-the-apt-hunting-game-learn-the-best-yara-practices-from-kaspersky).

A continuación se presentan algunas publicaciones de blog que ofrecen una explicación más detallada sobre cómo usar`yarGen`Para el desarrollo de reglas de Yara:

- [Cómo escribir reglas simples pero sólidas yara - parte 1](https://www.nextron-systems.com/2015/02/16/write-simple-sound-yara-rules/)
- [Cómo escribir reglas simples pero sólidas yara - Parte 2](https://www.nextron-systems.com/2015/10/17/how-to-write-simple-but-sound-yara-rules-part-2/)
- [Cómo escribir reglas simples pero sólidas yara - Parte 3](https://www.nextron-systems.com/2016/04/15/how-to-write-simple-but-sound-yara-rules-part-3/)

`yarGen`es una gran herramienta para desarrollar algunas buenas reglas de Yara extrayendo patrones únicos. Una vez que la regla se desarrolla con la ayuda de Yargen, definitivamente necesitamos revisar y agregar/eliminar algunos patrones más para que sea una regla efectiva.

En[este](https://cyb3rops.medium.com/how-to-post-process-yara-rules-generated-by-yargen-121d29322282)Blog Post, Florian Roth ha mencionado que el objetivo principal de Yargen es desarrollar las mejores reglas posibles para el postprocesamiento manual que puede sonar como una tarea tediosa, pero la combinación de preselección automática inteligente y un analista humano crítico supera tanto el proceso de generación totalmente manual como completamente automático.

---

Continuando hacia adelante, profundizaremos en el uso de Yara, ampliando nuestra búsqueda de amenazas desde nuestro sistema de archivos hasta la memoria y también dentro de las imágenes de memoria.