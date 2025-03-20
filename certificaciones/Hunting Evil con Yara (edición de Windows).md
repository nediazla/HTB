# Hunting Evil con Yara (edición de Windows)

En esta sección, exploraremos el uso de Yara en los sistemas de Windows para identificar amenazas tanto en el disco como en la memoria.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Caza de ejecutables maliciosos en disco con Yara**

Como vimos en la sección anterior, Yara es un arma potente en el arsenal de los profesionales de ciberseguridad para detectar y caza ejecutables maliciosos en un disco. Con las reglas personalizadas de Yara o las establecidas a nuestra disposición, podemos identificar archivos sospechosos o potencialmente maliciosos basados en patrones, rasgos o comportamientos distintos.

Usaremos una muestra que analizamos anteriormente`dharma_sample.exe`residiendo en el`C:\Samples\YARASigma`directorio del objetivo de esta sección.

Primero examinaremos la muestra de malware dentro de un editor hexadecimal (`HxD`, ubicado en`C:\Program Files\HxD`) para identificar la cadena descubierta previamente`C:\crysis\Release\PDB\payload.pdb`.

![](https://academy.hackthebox.com/storage/modules/234/1.png)

![](https://academy.hackthebox.com/storage/modules/234/2.png)

Si nos desplazamos casi hasta el fondo, notaremos otro aparentemente único`sssssbsss`cadena.

![](https://academy.hackthebox.com/storage/modules/234/3.png)

En el futuro, elaboraremos una regla basada en estos patrones y luego utilizaremos la utilidad de Yara para recorrer el sistema de archivos para ejecutables similares.

---

**Nota**: En una máquina Linux la`hexdump`La utilidad podría haberse utilizado para identificar los bytes hexadecimales antes mencionados de la siguiente manera.

Hunting Evil con Yara (edición de Windows)

```
remnux@remnux:~$ hexdump dharma_sample.exe -C | grep crysis -n33140-0000c7e0  52 00 43 6c 6f 73 65 48  61 6e 64 6c 65 00 4b 45  |R.CloseHandle.KE|
3141-0000c7f0  52 4e 45 4c 33 32 2e 64  6c 6c 00 00 52 53 44 53  |RNEL32.dll..RSDS|
3142-0000c800  25 7e 6d 90 fc 96 43 42  8e c3 87 23 6b 61 a4 92  |%~m...CB...#ka..|3143:0000c810  03 00 00 00 43 3a 5c 63  72 79 73 69 73 5c 52 65  |....C:\crysis\Re|
3144-0000c820  6c 65 61 73 65 5c 50 44  42 5c 70 61 79 6c 6f 61  |lease\PDB\payloa|
3145-0000c830  64 2e 70 64 62 00 00 00  00 00 00 00 00 00 00 00  |d.pdb...........|
3146-0000c840  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|

```

Hunting Evil con Yara (edición de Windows)

```
remnux@remnux:~$ hexdump dharma_sample.exe -C | grep sssssbsss -n35738-00016be0  3d 00 00 00 26 00 00 00  73 73 73 64 00 00 00 00  |=...&...sssd....|
5739-00016bf0  26 61 6c 6c 3d 00 00 00  73 64 00 00 2d 00 61 00  |&all=...sd..-.a.|
5740-00016c00  00 00 00 00 73 00 73 00  62 00 73 00 73 00 00 00  |....s.s.b.s.s...|
5741:00016c10  73 73 73 73 73 62 73 73  73 00 00 00 73 73 73 73  |sssssbsss...ssss|
5742-00016c20  73 62 73 00 22 00 00 00  22 00 00 00 5c 00 00 00  |sbs."..."...\...|
5743-00016c30  5c 00 00 00 5c 00 00 00  5c 00 00 00 5c 00 00 00  |\...\...\...\...|
5744-00016c40  22 00 00 00 20 00 22 00  00 00 00 00 5c 00 00 00  |"... .".....\...|

```

---

Incorporemos todos los bytes hexadecimales identificados en una regla, mejorando nuestra capacidad de detectar esta cadena en cualquier ejecutable basado en disco.

Código:Yara

```
rule ransomware_dharma {

    meta:
        author = "Madhukar Raina"
        version = "1.0"
        description = "Simple rule to detect strings from Dharma ransomware"
        reference = "https://www.virustotal.com/gui/file/bff6a1000a86f8edf3673d576786ec75b80bed0c458a8ca0bd52d12b74099071/behavior"

    strings:
        $string_pdb = {  433A5C6372797369735C52656C656173655C5044425C7061796C6F61642E706462 }
        $string_ssss = { 73 73 73 73 73 62 73 73 73 }

        condition: all of them
}

```

Esta regla (`dharma_ransomware.yar`) se puede encontrar dentro del`C:\Rules\yara`directorio del objetivo de esta sección.

Iniciando el ejecutable de Yara con esta regla, observemos si destaca otras muestras análogas en el disco.

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Users\htb-student> yara64.exe -s C:\Rules\yara\dharma_ransomware.yar C:\Samples\YARASigma\ -r 2>null
ransomware_dharma C:\Samples\YARASigma\\dharma_sample.exe
0xc814:$string_pdb: 43 3A 5C 63 72 79 73 69 73 5C 52 65 6C 65 61 73 65 5C 50 44 42 5C 70 61 79 6C 6F 61 64 2E 70 64 620x16c10:$string_ssss: 73 73 73 73 73 62 73 73 73ransomware_dharma C:\Samples\YARASigma\\check_updates.exe
0xc814:$string_pdb: 43 3A 5C 63 72 79 73 69 73 5C 52 65 6C 65 61 73 65 5C 50 44 42 5C 70 61 79 6C 6F 61 64 2E 70 64 620x16c10:$string_ssss: 73 73 73 73 73 62 73 73 73ransomware_dharma C:\Samples\YARASigma\\microsoft.com
0xc814:$string_pdb: 43 3A 5C 63 72 79 73 69 73 5C 52 65 6C 65 61 73 65 5C 50 44 42 5C 70 61 79 6C 6F 61 64 2E 70 64 620x16c10:$string_ssss: 73 73 73 73 73 62 73 73 73ransomware_dharma C:\Samples\YARASigma\\KB5027505.exe
0xc814:$string_pdb: 43 3A 5C 63 72 79 73 69 73 5C 52 65 6C 65 61 73 65 5C 50 44 42 5C 70 61 79 6C 6F 61 64 2E 70 64 620x16c10:$string_ssss: 73 73 73 73 73 62 73 73 73ransomware_dharma C:\Samples\YARASigma\\pdf_reader.exe
0xc814:$string_pdb: 43 3A 5C 63 72 79 73 69 73 5C 52 65 6C 65 61 73 65 5C 50 44 42 5C 70 61 79 6C 6F 61 64 2E 70 64 620x16c10:$string_ssss: 73 73 73 73 73 62 73 73 73
```

**Desglose de comando**:

- `yara64.exe`: Se refiere al ejecutable Yara64, que es el escáner Yara diseñado específicamente para sistemas de 64 bits.
- `s C:\Rules\yara\dharma_ransomware.yar`: Especifica el archivo de reglas Yara que se utilizará para escanear. En este caso, el archivo de reglas nombrado`dharma_ransomware.yar`ubicado en el`C:\Rules\yara`Se proporciona directorio.
- `C:\Samples\YARASigma`: Especifica la ruta o directorio a escanear por Yara. En este caso, el directorio que se escanea es`C:\Samples\YARASigma`.
- `r`: Indica que la operación de escaneo debe realizarse de manera recursiva, lo que significa que Yara escaneará archivos dentro de los subdirectorios del directorio especificado también.
- `2>nul`: Redirige la salida de error (transmisión`2`) a un dispositivo nulo, ocultando efectivamente cualquier mensaje de error que pueda ocurrir durante el proceso de escaneo.

Como podemos ver, el`pdf_reader.exe`, `microsoft.com`, `check_updates.exe`, y`KB5027505.exe`Los archivos son detectados por esta regla (además de`dharma_sample.exe`por supuesto).

Ahora, pivotemos, aplicando las reglas de Yara a los procesos vivos.

# **Caza del mal dentro de los procesos de carrera con Yara**

Para determinar si el malware acecha en procesos en curso, desataremos el escáner Yara en los procesos activos del sistema. Demostremos el uso de una regla de Yara que se dirige a la capa de shell Metasploit de MetaSploit, que se cree que está al acecho en un proceso de ejecución.

**Fuente de reglas de Yara**: [https://github.com/cuckoosandbox/community/blob/master/data/yara/shellcode/metasploit.yar](https://github.com/cuckoosandbox/community/blob/master/data/yara/shellcode/metasploit.yar)

Código:Yara

```
rule meterpreter_reverse_tcp_shellcode {
    meta:
        author = "FDD @ Cuckoo sandbox"
        description = "Rule for metasploit's  meterpreter reverse tcp raw shellcode"

    strings:
        $s1 = { fce8 8?00 0000 60 }     // shellcode prologe in metasploit
        $s2 = { 648b ??30 }             // mov edx, fs:[???+0x30]
        $s3 = { 4c77 2607 }             // kernel32 checksum
        $s4 = "ws2_"                    // ws2_32.dll
        $s5 = { 2980 6b00 }             // WSAStartUp checksum
        $s6 = { ea0f dfe0 }             // WSASocket checksum
        $s7 = { 99a5 7461 }             // connect checksum

    condition:
        5 of them
}

```

Usaremos una muestra que analizamos anteriormente`htb_sample_shell.exe`residiendo en el`C:\Samples\YARASigma`Directorio del Targe de esta sección

`htb_sample_shell.exe`inyecta la capa de shell MetaSploit Meterpreter en el`cmdkey.exe`proceso. Activemos, asegurando una inyección exitosa.

**Nota**: Asegúrese de lanzar PowerShell como administrador.

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Samples\YARASigma> .\htb_sample_shell.exe

<-- Hack the box sample for yara signatures -->

[+] Parent process with PID 7972 is created : C:\Samples\YARASigma\htb_sample_shell.exe
[+] Child process with PID 9084 is created : C:\Windows\System32\cmdkey.exe
[+] Shellcode is written at address 000002686B1C0000 in remote process C:\Windows\System32\cmdkey.exe
[+] Remote thread to execute the shellcode is started with thread ID 368

Press enter key to terminate...

```

Con la inyección ejecutada, escanemos todos los procesos del sistema activo de la siguiente manera, a través de otro terminal de PowerShell (`Run as administrator`).

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Windows\system32> Get-Process | ForEach-Object { "Scanning with Yara for meterpreter shellcode on PID "+$_.id; & "yara64.exe" "C:\Rules\yara\meterpreter_shellcode.yar" $_.id }Scanning with Yara for meterpreter shellcode on PID 9000
Scanning with Yara for meterpreter shellcode on PID 9016
Scanning with Yara for meterpreter shellcode on PID 4940
Scanning with Yara for meterpreter shellcode on PID 5716
Scanning with Yara for meterpreter shellcode on PID 9084
meterpreter_reverse_tcp_shellcode 9084
Scanning with Yara for meterpreter shellcode on PID 7112
Scanning with Yara for meterpreter shellcode on PID 8400
Scanning with Yara for meterpreter shellcode on PID 9180
Scanning with Yara for meterpreter shellcode on PID 416
error scanning 416: can not attach to process (try running as root)
Scanning with Yara for meterpreter shellcode on PID 492
error scanning 492: can not attach to process (try running as root)
Scanning with Yara for meterpreter shellcode on PID 1824
error scanning 1824: can not attach to process (try running as root)
Scanning with Yara for meterpreter shellcode on PID 8268
Scanning with Yara for meterpreter shellcode on PID 3940
Scanning with Yara for meterpreter shellcode on PID 7960
Scanning with Yara for meterpreter shellcode on PID 988
Scanning with Yara for meterpreter shellcode on PID 6276
Scanning with Yara for meterpreter shellcode on PID 4228
Scanning with Yara for meterpreter shellcode on PID 772
Scanning with Yara for meterpreter shellcode on PID 780
Scanning with Yara for meterpreter shellcode on PID 1192
Scanning with Yara for meterpreter shellcode on PID 7972
meterpreter_reverse_tcp_shellcode 7972
Scanning with Yara for meterpreter shellcode on PID 0
error scanning 0: could not open file
Scanning with Yara for meterpreter shellcode on PID 6788
Scanning with Yara for meterpreter shellcode on PID 924
Scanning with Yara for meterpreter shellcode on PID 636
Scanning with Yara for meterpreter shellcode on PID 1780
error scanning 1780: can not attach to process (try running as root)

```

Estamos aprovechando un guión conciso de PowerShell. El`Get-Process`El comando obtiene procesos de ejecución y con la ayuda del símbolo de la tubería (`|`), estos datos se funns en el bloque de script (`{...}`). Aquí,`ForEach-Object`disecciona cada proceso, provocando`yara64.exe`Para aplicar nuestra regla Yara sobre la memoria de cada proceso.

Observe si el niño procesa (`PID 9084`) es marcado.

A partir de los resultados, el código de shell de meterpreter parece haberse infiltrado en un proceso con`PID 9084`. También podemos guiar el escáner Yara con un PID específico de la siguiente manera.

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Windows\system32> yara64.exe C:\Rules\yara\meterpreter_shellcode.yar 9084 --print-strings
meterpreter_reverse_tcp_shellcode 9084
0x2686b1c0104:$s3: 4C 77 26 070xe3fea7fef8:$s4: ws2_0x2686b1c00d9:$s4: ws2_0x7ffbfdad4490:$s4: ws2_0x7ffbfe85723f:$s4: ws2_0x7ffbfe857f07:$s4: ws2_0x7ffbfe8580a7:$s4: ws2_0x7ffbfe858147:$s4: ws2_0x7ffbfe8581b7:$s4: ws2_0x7ffbfe8581f7:$s4: ws2_0x7ffbfe858227:$s4: ws2_0x7ffbfe85833f:$s4: ws2_0x7ffbfe8587cf:$s4: ws2_0x7ffbfe8588c7:$s4: ws2_0x7ffbfe858917:$s4: ws2_0x7ffbfe858947:$s4: ws2_0x7ffbfe858987:$s4: ws2_0x7ffbfe8589d7:$s4: ws2_0x7ffbfe858a17:$s4: ws2_0x7ffbfe859c90:$s4: ws2_0x7ffbfe859cc6:$s4: ws2_0x7ffbfe859cee:$s4: ws2_0x7ffbfe859d16:$s4: ws2_0x7ffbfe859d42:$s4: ws2_0x2686b1c0115:$s5: 29 80 6B 000x2686b1c0135:$s6: EA 0F DF E00x2686b1c014a:$s7: 99 A5 74 61
```

La captura de pantalla a continuación muestra una descripción general de lo que discutimos anteriormente.

![](https://academy.hackthebox.com/storage/modules/234/4.png)

# **Caza del mal dentro de los datos de ETW con Yara**

En el módulo titulado`Windows Event Logs & Finding Evil`exploramos`ETW`e introducido[Silketw](https://github.com/mandiant/SilkETW). En esta sección, volveremos a los datos de ETW, destacando cómo Yara se puede usar para filtrar o etiquetar ciertos eventos.

Un resumen rápido primero. Según Microsoft,`Event Tracing For Windows (ETW)`es una instalación de rastreo de alta velocidad de uso general proporcionado por el sistema operativo. Utilizando un mecanismo de amortiguación y registro implementado en el núcleo, ETW proporciona un mecanismo de rastreo para eventos planteados tanto por aplicaciones en modo de usuario como por controladores de dispositivos en modo de kernel.

![](https://academy.hackthebox.com/storage/modules/234/yara_etw.png)

- `Controllers`: Los controladores poseen funcionalidades que abarcan iniciando y terminando sesiones de trazas. También tienen la capacidad de habilitar o deshabilitar a los proveedores dentro de una traza específica.
- `Providers`: Los proveedores son cruciales, ya que generan eventos y los canalizan a las sesiones de ETW designadas.
- `Consumers`: Los consumidores son los suscriptores de eventos específicos. Aprovechan estos eventos y luego los reciben para un procesamiento o análisis en profundidad.

### **Proveedores útiles**

- `Microsoft-Windows-Kernel-Process`: Este proveedor de ETW es fundamental para monitorear la actividad relacionada con el proceso dentro del núcleo de Windows. Puede ayudar a detectar comportamientos de proceso inusuales, como inyección de procesos, hueco de procesos y otras tácticas comúnmente utilizadas por malware y amenazas persistentes avanzadas (APT).
- `Microsoft-Windows-Kernel-File`: Como su nombre indica, este proveedor se centra en las operaciones relacionadas con los archivos. Se puede emplear para escenarios de detección que involucran acceso a archivos no autorizado, cambios en archivos críticos del sistema o operaciones sospechosas de archivos indicativos de exfiltración o actividad de ransomware.
- `Microsoft-Windows-Kernel-Network`: Este proveedor de ETW ofrece visibilidad en la actividad relacionada con la red a nivel del núcleo. Es especialmente útil para detectar ataques basados en la red, como la exfiltración de datos, las conexiones de red no autorizadas y los posibles signos de comunicación de comando y control (C2).
- `Microsoft-Windows-SMBClient/SMBServer`: Estos proveedores monitorean la actividad del cliente y el servidor de los proveedores del servidor del servidor, proporcionando información sobre el intercambio de archivos y la comunicación de red. Se pueden usar para detectar patrones de tráfico SMB inusuales, lo que puede indicar el movimiento lateral o la exfiltración de datos.
- `Microsoft-Windows-DotNETRuntime`: Este proveedor se centra en los eventos de tiempo de ejecución de .NET, lo que lo hace ideal para identificar anomalías en la ejecución de la aplicación .NET, la explotación potencial de las vulnerabilidades de .NET o la carga de ensamblaje de .NET malicioso.
- `OpenSSH`: Monitoreo El proveedor de ETW de OpenSSH puede proporcionar información importante sobre los intentos de conexión de Shell (SSH), autenticaciones exitosas y fallidas, y posibles ataques de fuerza bruta.
- `Microsoft-Windows-VPN-Client`: Este proveedor permite el seguimiento de eventos de cliente de la red privada virtual (VPN). Puede ser útil para identificar conexiones VPN no autorizadas o sospechosas.
- `Microsoft-Windows-PowerShell`: Este proveedor de ETW rastrea la ejecución de PowerShell y la actividad de comando, lo que lo hace invaluable para detectar el uso sospechoso de PowerShell, el registro del bloque de script y el mal uso o explotación potencial.
- `Microsoft-Windows-Kernel-Registry`: Este proveedor monitorea las operaciones de registro, por lo que es útil para los escenarios de detección relacionados con los cambios en las claves de registro, a menudo asociados con mecanismos de persistencia, instalación de malware o cambios de configuración del sistema.
- `Microsoft-Windows-CodeIntegrity`: Este proveedor monitorea el código y las verificaciones de integridad del controlador, que pueden ser clave para identificar los intentos de cargar controladores o código no firmados o maliciosos.
- `Microsoft-Antimalware-Service`: Este proveedor de ETW se puede emplear para detectar problemas potenciales con el servicio de antimalware, incluidos los servicios para discapacitados, los cambios de configuración o las posibles técnicas de evasión empleadas por el malware.
- `WinRM`: Monitorear el proveedor de administración remota de Windows (WINRM) puede revelar una actividad de administración remota no autorizada o sospechosa, a menudo indicativa de movimiento lateral o ejecución de comandos remotos.
- `Microsoft-Windows-TerminalServices-LocalSessionManager`: Este proveedor rastrea las sesiones locales de servicios terminales, lo que lo hace útil para detectar actividad de escritorio remoto no autorizada o sospechosa.
- `Microsoft-Windows-Security-Mitigations`: Este proveedor realiza pestañas sobre la efectividad y las operaciones de las mitigaciones de seguridad. Es esencial para identificar posibles intentos de derivación de estos controles de seguridad.
- `Microsoft-Windows-DNS-Client`: Este proveedor de ETW ofrece visibilidad sobre la actividad del cliente DNS, que es crucial para detectar ataques basados en DNS, incluida la túnel DNS o las solicitudes inusuales de DNS que pueden indicar la comunicación C2.
- `Microsoft-Antimalware-Protection`: Este proveedor monitorea las operaciones de los mecanismos de protección de antimalware. Se puede utilizar para detectar cualquier problema con estos mecanismos, como características de protección para discapacitados, cambios de configuración o signos de técnicas de evasión empleadas por actores maliciosos.

### **Escaneo de reglas de Yara en ETW (usando Silketw)**

SILKETW es una herramienta de código abierto para trabajar con datos de rastreo de eventos para Windows (ETW). SILKETW proporciona una mejor visibilidad y análisis de eventos de Windows para el monitoreo de seguridad, la caza de amenazas y los propósitos de respuesta a incidentes. La mejor parte de Silketw es que también tiene una opción para integrar las reglas de Yara. Incluye la funcionalidad de Yara para filtrar o etiquetar datos de eventos.

Silketw reside en el`C:\Tools\SilkETW\v8\SilkETW`directorio del objetivo de esta sección.

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Tools\SilkETW\v8\SilkETW> .\SilkETW.exe -h

███████╗██╗██╗   ██╗  ██╗███████╗████████╗██╗    ██╗
██╔════╝██║██║   ██║ ██╔╝██╔════╝╚══██╔══╝██║    ██║
███████╗██║██║   █████╔╝ █████╗     ██║   ██║ █╗ ██║
╚════██║██║██║   ██╔═██╗ ██╔══╝     ██║   ██║███╗██║
███████║██║█████╗██║  ██╗███████╗   ██║   ╚███╔███╔╝
╚══════╝╚═╝╚════╝╚═╝  ╚═╝╚══════╝   ╚═╝    ╚══╝╚══╝
                  [v0.8 - Ruben Boonen => @FuzzySec]

 >--~~--> Args? <--~~--<

-h  (--help)          This help menu
-s  (--silk)          Trivia about Silk
-t  (--type)          Specify if we are using a Kernel or User collector
-kk (--kernelkeyword) Valid keywords: Process, Thread, ImageLoad, ProcessCounters, ContextSwitch,
                      DeferedProcedureCalls, Interrupt, SystemCall, DiskIO, DiskFileIO, DiskIOInit,
                      Dispatcher, Memory, MemoryHardFaults, VirtualAlloc, VAMap, NetworkTCPIP, Registry,
                      AdvancedLocalProcedureCalls, SplitIO, Handle, Driver, OS, Profile, Default,
                      ThreadTime, FileIO, FileIOInit, Verbose, All, IOQueue, ThreadPriority,
                      ReferenceSet, PMCProfile, NonContainer
-uk (--userkeyword)   Define a mask of valid keywords, eg 0x2038 -> JitKeyword|InteropKeyword|
                      LoaderKeyword|NGenKeyword
-pn (--providername)  User ETW provider name, eg "Microsoft-Windows-DotNETRuntime" or its
                      corresponding GUID eg "e13c0d23-ccbc-4e12-931b-d9cc2eee27e4"
-l  (--level)         Logging level: Always, Critical, Error, Warning, Informational, Verbose
-ot (--outputtype)    Output type: POST to "URL", write to "file" or write to "eventlog"
-p  (--path)          Full output file path or URL. Event logs are automatically written to
                      "Applications and Services Logs\SilkETW-Log"
-f  (--filter)        Filter types: None, EventName, ProcessID, ProcessName, Opcode
-fv (--filtervalue)   Filter type capture value, eg "svchost" for ProcessName
-y  (--yara)          Full path to folder containing Yara rules
-yo (--yaraoptions)   Either record "All" events or only "Matches"

 >--~~--> Usage? <--~~--<

# Use a VirtualAlloc Kernel collector, POST results to ElasticsearchSilkETW.exe -t kernel -kk VirtualAlloc -ot url -p https://some.elk:9200/valloc/_doc/

# Use a Process Kernel collector, filter on PIDSilkETW.exe -t kernel -kk Process -ot url -p https://some.elk:9200/kproc/_doc/ -f ProcessID -fv 11223

# Use a .Net User collector, specify mask, filter on EventName, write to fileSilkETW.exe -t user -pn Microsoft-Windows-DotNETRuntime -uk 0x2038 -ot file -p C:\Some\Path\out.json -f EventName -fv Method/LoadVerbose

# Use a DNS User collector, specify log level, write to fileSilkETW.exe -t user -pn Microsoft-Windows-DNS-Client -l Always -ot file -p C:\Some\Path\out.json

# Use an LDAP User collector, perform Yara matching, POST matches to ElasticsearchSilkETW.exe -t user -pn Microsoft-Windows-Ldap-Client -ot url -p https://some.elk:9200/ldap/_doc/ -y C:\Some\Yara\Rule\Folder -yo matches

# Specify "Microsoft-Windows-COM-Perf" by its GUID, write results to the event logSilkETW.exe -t user -pn b8d6861b-d20f-4eec-bbae-87e0dd80602b -ot eventlog

```

El menú de ayuda proporciona muchos ejemplos de cómo podemos usar la herramienta. Experimentemos con algunas de las opciones de escaneo de Yara en algunos proveedores de ETW.

### **Ejemplo 1: escaneo de reglas de Yara en datos ETW de Microsoft-Windows-Powershell**

El siguiente comando ejecuta la herramienta SILKETW con opciones específicas para realizar el rastreo y el análisis de eventos en eventos relacionados con PowerShell en Windows.

**Nota**: Asegúrese de lanzar PowerShell como administrador.

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Tools\SilkETW\v8\SilkETW> .\SilkETW.exe -t user -pn Microsoft-Windows-PowerShell -ot file -p ./etw_ps_logs.json -l verbose -y C:\Rules\yara  -yo Matches

███████╗██╗██╗   ██╗  ██╗███████╗████████╗██╗    ██╗
██╔════╝██║██║   ██║ ██╔╝██╔════╝╚══██╔══╝██║    ██║
███████╗██║██║   █████╔╝ █████╗     ██║   ██║ █╗ ██║
╚════██║██║██║   ██╔═██╗ ██╔══╝     ██║   ██║███╗██║
███████║██║█████╗██║  ██╗███████╗   ██║   ╚███╔███╔╝
╚══════╝╚═╝╚════╝╚═╝  ╚═╝╚══════╝   ╚═╝    ╚══╝╚══╝
                  [v0.8 - Ruben Boonen => @FuzzySec]

[+] Collector parameter validation success..
[>] Starting trace collector (Ctrl-c to stop)..
[?] Events captured: 0

```

**Desglose de comando**:

- `t user`: Especifica el modo de rastreo de eventos. En este caso, se establece en "Usuario", lo que indica que la herramienta trazará eventos en modo de usuario (eventos generados por aplicaciones de usuario).
- `pn Microsoft-Windows-PowerShell`: Especifica el nombre del proveedor o registro de eventos que desea rastrear. En este comando, se dirige a eventos del proveedor "Microsoft-Windows-Powershell", que es responsable de generar eventos relacionados con la actividad de PowerShell.
- `ot file`: Especifica el formato de salida para los datos del evento recopilado. En este caso, se establece en "archivo", lo que significa que la herramienta guardará los datos del evento en un archivo.
- `p ./etw_ps_logs.json`: Especifica la ruta del archivo de salida y el nombre de archivo. La herramienta guardará los datos del evento recopilados en formato JSON en un archivo llamado "ETW_PS_LOGS.JSON" en el directorio actual.
- `l verbose`: Establece el nivel de registro en "textualmente". Esta opción permite información de registro más detallada durante el proceso de rastreo y análisis de eventos.
- `y C:\Rules\yara`: Habilita el escaneo de Yara y especifica una ruta que contiene reglas de Yara. Esta opción indica que la herramienta realizará el escaneo Yara en los datos del evento recopilado.
- `yo Matches`: Especifica la opción de salida de Yara. En este caso, se establece en "coincidencias", lo que significa que la herramienta mostrará coincidencias de Yara encontradas durante el proceso de escaneo.

Dentro del`C:\Rules\yara`Directorio del objetivo de esta sección hay un archivo de reglas de Yara nombrado`etw_powershell_hello.yar`Eso busca ciertas cuerdas en los bloques de guiones de PowerShell.

Código:Yara

```
rule powershell_hello_world_yara {
	strings:
		$s0 = "Write-Host" ascii wide nocase
		$s1 = "Hello" ascii wide nocase
		$s2 = "from" ascii wide nocase
		$s3 = "PowerShell" ascii wide nocase
	condition:
		3 of ($s*)
}

```

Ahora ejecutemos el siguiente comando PowerShell a través de otra terminal de PowerShell y veamos si será detectado por Silketw (donde se ha cargado la regla de Yara mencionada).

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Users\htb-student> Invoke-Command -ScriptBlock {Write-Host "Hello from PowerShell"}

```

¡Tenemos un partido!

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Tools\SilkETW\v8\SilkETW> .\SilkETW.exe -t user -pn Microsoft-Windows-PowerShell -ot file -p ./etw_ps_logs.json -l verbose -y C:\Rules\yara  -yo Matches

███████╗██╗██╗   ██╗  ██╗███████╗████████╗██╗    ██╗
██╔════╝██║██║   ██║ ██╔╝██╔════╝╚══██╔══╝██║    ██║
███████╗██║██║   █████╔╝ █████╗     ██║   ██║ █╗ ██║
╚════██║██║██║   ██╔═██╗ ██╔══╝     ██║   ██║███╗██║
███████║██║█████╗██║  ██╗███████╗   ██║   ╚███╔███╔╝
╚══════╝╚═╝╚════╝╚═╝  ╚═╝╚══════╝   ╚═╝    ╚══╝╚══╝
                  [v0.8 - Ruben Boonen => @FuzzySec]

[+] Collector parameter validation success..
[>] Starting trace collector (Ctrl-c to stop)..
[?] Events captured: 28
     -> Yara match: powershell_hello_world_yara
     -> Yara match: powershell_hello_world_yara

```

### **Ejemplo 2: escaneo de reglas de Yara en datos ETW de Microsoft-Windows-DNS-Client**

El siguiente comando ejecuta la herramienta SILKETW con opciones específicas para realizar el rastreo y el análisis de eventos en eventos relacionados con DNS en Windows.

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Tools\SilkETW\v8\SilkETW> .\SilkETW.exe -t user -pn Microsoft-Windows-DNS-Client -ot file -p ./etw_dns_logs.json -l verbose -y C:\Rules\yara  -yo Matches

███████╗██╗██╗   ██╗  ██╗███████╗████████╗██╗    ██╗
██╔════╝██║██║   ██║ ██╔╝██╔════╝╚══██╔══╝██║    ██║
███████╗██║██║   █████╔╝ █████╗     ██║   ██║ █╗ ██║
╚════██║██║██║   ██╔═██╗ ██╔══╝     ██║   ██║███╗██║
███████║██║█████╗██║  ██╗███████╗   ██║   ╚███╔███╔╝
╚══════╝╚═╝╚════╝╚═╝  ╚═╝╚══════╝   ╚═╝    ╚══╝╚══╝
                  [v0.8 - Ruben Boonen => @FuzzySec]

[+] Collector parameter validation success..
[>] Starting trace collector (Ctrl-c to stop)..
[?] Events captured: 0

```

Dentro del`C:\Rules\yara`Directorio del objetivo de esta sección hay un archivo de reglas de Yara nombrado`etw_dns_wannacry.yar`Eso busca un dominio codificado que exista en muestras de ransomware WannaCry en eventos DNS.

Código:Yara

```
rule dns_wannacry_domain {
	strings:
		$s1 = "iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com" ascii wide nocase
	condition:
		$s1
}

```

Ahora ejecutemos el siguiente comando a través de otra terminal de PowerShell y veamos si será detectado por Silketw (donde se ha cargado la regla de Yara mencionada).

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Users\htb-student> ping iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com
Reply from 104.17.244.81: bytes=32 time=14ms TTL=56
Reply from 104.17.244.81: bytes=32 time=14ms TTL=56
Reply from 104.17.244.81: bytes=32 time=14ms TTL=56
Reply from 104.17.244.81: bytes=32 time=14ms TTL=56

Ping statistics for 104.17.244.81:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 14ms, Maximum = 14ms, Average = 14ms

```

¡Tenemos un partido!

Hunting Evil con Yara (edición de Windows)

```powershell
PS C:\Tools\SilkETW\v8\SilkETW> .\SilkETW.exe -t user -pn Microsoft-Windows-DNS-Client -ot file -p ./etw_dns_logs.json -l verbose -y C:\Rules\yara  -yo Matches

███████╗██╗██╗   ██╗  ██╗███████╗████████╗██╗    ██╗
██╔════╝██║██║   ██║ ██╔╝██╔════╝╚══██╔══╝██║    ██║
███████╗██║██║   █████╔╝ █████╗     ██║   ██║ █╗ ██║
╚════██║██║██║   ██╔═██╗ ██╔══╝     ██║   ██║███╗██║
███████║██║█████╗██║  ██╗███████╗   ██║   ╚███╔███╔╝
╚══════╝╚═╝╚════╝╚═╝  ╚═╝╚══════╝   ╚═╝    ╚══╝╚══╝
                  [v0.8 - Ruben Boonen => @FuzzySec]

[+] Collector parameter validation success..
[>] Starting trace collector (Ctrl-c to stop)..
[?] Events captured: 60
     -> Yara match: dns_wannacry_domain
     -> Yara match: dns_wannacry_domain
```