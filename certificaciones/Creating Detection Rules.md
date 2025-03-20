# Creating Detection Rules

Habiendo descubierto ahora las tácticas, técnicas y procedimientos (TTP) empleados por este malware, podemos proceder a diseñar reglas de detección, como`Yara`y`Sigma`normas.

Si bien comenzaremos a profundizar en los conceptos de desarrollo de reglas de Yara y Sigma en esta sección, solo rascaremos la superficie. Estos son temas extensos con mucha profundidad, lo que requiere un estudio exhaustivo. Por lo tanto, dedicaremos un módulo completo llamado 'Yara & Sigma para analistas de SOC' para ayudarlo a dominar realmente estas áreas cruciales de defensa cibernética.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, ssh en la IP objetivo utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Yara**

`YARA (Yet Another Recursive Acronym)`, una herramienta de coincidencia de patrones de código abierto ampliamente utilizada y el marco de detección y clasificación de malware basado en reglas, nos permite crear reglas personalizadas para detectar patrones o características específicos en archivos, procesos o memoria. Para redactar un`YARA`Regla para nuestra muestra, necesitaremos examinar el comportamiento, las características o las cadenas/patrones específicos exclusivos de la muestra que nuestro objetivo es detectar.

Aquí hay un simple ejemplo de un`YARA`regla que coincida con la presencia de la cadena`Sandbox detected`en un proceso. Te recordamos que`shell.exe`demostrado tal comportamiento.

Código:Yara

```
rule Shell_Sandbox_Detection {
    strings:
        $sandbox_string = "Sandbox detected"
    condition:
        $sandbox_string
}

```

Ahora agregemos muchas más cuerdas y patrones a la regla para mejorarlo.

Podemos utilizar el`yarGen`herramienta, que automatiza el proceso de generación`YARA`Reglas, con el objetivo principal de elaborar las mejores reglas posibles para el postprocesamiento manual. Esto, sin embargo, requiere una astuta preselección automática y un analista humano exigente para generar una regla robusta.

Primero creemos un nuevo directorio llamado`Test`dentro del`/home/htb-student/Samples/MalwareAnalysis`directorio del objetivo de esta sección y luego copiemos`shell.exe`(residiendo en el`/home/htb-student/Samples/MalwareAnalysis`directorio) al recién creado`Test`directorio de la siguiente manera.

Creación de reglas de detección

```
xnoxos@htb[/htb]$ mkdir /home/htb-student/Samples/MalwareAnalysis/Test
```

Creación de reglas de detección

```
xnoxos@htb[/htb]$ cp /home/htb-student/Samples/MalwareAnalysis/shell.exe /home/htb-student/Samples/MalwareAnalysis/Test/
```

Para crear automáticamente una regla Yara para`shell.exe`Deberíamos ejecutar lo siguiente (dentro del`/home/htb-student/yarGen-0.23.4`directorio).

Creación de reglas de detección

```
xnoxos@htb[/htb]$ sudo python3 yarGen.py -m /home/htb-student/Samples/MalwareAnalysis/Test/------------------------------------------------------------------------
                   _____
    __ _____ _____/ ___/__ ___
   / // / _ `/ __/ (_ / -_) _ \
   \_, /\_,_/_/  \___/\__/_//_/
  /___/  Yara Rule Generator
         Florian Roth, July 2020, Version 0.23.3

  Note: Rules have to be post-processed
  See this post for details: https://medium.com/@cyb3rops/121d29322282
------------------------------------------------------------------------
[+] Using identifier 'Test'
[+] Using reference 'https://github.com/Neo23x0/yarGen'
[+] Using prefix 'Test'
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
[+] Processing /home/htb-student/Samples/MalwareAnalysis/Test/shell.exe ...
[+] Generating statistical data ...
[+] Generating Super Rules ... (a lot of magic)
[+] Generating Simple Rules ...
[-] Applying intelligent filters to string findings ...
[-] Filtering string set for /home/htb-student/Samples/MalwareAnalysis/Test/shell.exe ...
[=] Generated 1 SIMPLE rules.
[=] All rules written to yargen_rules.yar
[+] yarGen run finished

```

Notaremos que un archivo llamado`yargen_rule.yar`se genera por`yarGen`que incorpora cadenas únicas, que se extraen e insertan automáticamente en la regla.

Creación de reglas de detección

```
xnoxos@htb[/htb]$ cat yargen_rules.yar /*
   YARA Rule Set
   Author: yarGen Rule Generator
   Date: 2023-08-02
   Identifier: Test
   Reference: https://github.com/Neo23x0/yarGen
*/

/* Rule Set ----------------------------------------------------------------- */

rule _home_htb_student_Samples_MalwareAnalysis_Test_shell {
   meta:
      description = "Test - file shell.exe"
      author = "yarGen Rule Generator"
      reference = "https://github.com/Neo23x0/yarGen"
      date = "2023-08-02"
      hash1 = "bd841e796feed0088ae670284ab991f212cf709f2391310a85443b2ed1312bda"
   strings:
$x1 = "C:\\Windows\\System32\\cmd.exe" fullword ascii$s2 = "http://ms-windows-update.com/svchost.exe" fullword ascii$s3 = "C:\\Windows\\System32\\notepad.exe" fullword ascii$s4 = "/k ping 127.0.0.1 -n 5" fullword ascii$s5 = "iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com" fullword ascii$s6 = "  VirtualQuery failed for %d bytes at address %p" fullword ascii$s7 = "[-] Error code is : %lu" fullword ascii$s8 = "C:\\Program Files\\VMware\\VMware Tools\\" fullword ascii$s9 = "Failed to open the registry key." fullword ascii$s10 = "  VirtualProtect failed with code 0x%x" fullword ascii$s11 = "Connection sent to C2" fullword ascii$s12 = "VPAPAPAPI" fullword ascii$s13 = "AWAVAUATVSH" fullword ascii$s14 = "45.33.32.156" fullword ascii$s15 = "  Unknown pseudo relocation protocol version %d." fullword ascii$s16 = "AQAPRQVH1" fullword ascii$s17 = "connect" fullword ascii /* Goodware String - occured 429 times */$s18 = "socket" fullword ascii /* Goodware String - occured 452 times */$s19 = "tSIcK<L" fullword ascii$s20 = "Windows-Update/7.6.7600.256 %s" fullword ascii   condition:
      uint16(0) == 0x5a4d and filesize < 60KB and
      1 of ($x*) and 4 of them}

```

Podemos revisar la regla y modificarla según sea necesario, agregando más cuerdas y condiciones para mejorar su confiabilidad y efectividad.

---

# **Detección de malware utilizando las reglas de Yara**

Luego podemos usar esta regla para escanear un directorio de la siguiente manera.

Creación de reglas de detección

```
xnoxos@htb[/htb]$ yara /home/htb-student/yarGen-0.23.4/yargen_rules.yar /home/htb-student/Samples/MalwareAnalysis/home_htb_student_Samples_MalwareAnalysis_Test_shell /home/htb-student/Samples/MalwareAnalysis//shell.exe

```

Notaremos que`shell.exe`se devuelve!

**A continuación se presentan algunas referencias para las reglas de Yara**:

- Documentación de Yara:[https://yara.readthedocs.io/en/stable/writingrules.html](https://yara.readthedocs.io/en/stable/writingrules.html)
- Recursos de Yara -[https://github.com/incest/awesome-yara](https://github.com/InQuest/awesome-yara)
- El informe DFIR -[https://github.com/the-dfir-report/yara-rules](https://github.com/The-DFIR-Report/Yara-Rules)

# **Sigma**

`Sigma`es un formato de regla integral y estandarizado ampliamente utilizado por los analistas de seguridad y`Security Information and Event Management (SIEM)`sistemas. El objetivo es detectar e identificar patrones o comportamientos específicos que puedan significar amenazas o eventos de seguridad. El formato estandarizado de`Sigma`Las reglas permiten a los equipos de seguridad definir y difundir la lógica de detección en diversas plataformas de seguridad.

Para construir un`Sigma`Regla basada en ciertas acciones, por ejemplo, que suelta un archivo en una ubicación temporal, podemos diseñar una regla de muestra en este sentido.

Código:sigma

```
title: Suspicious File Drop in Users Temp Location
status: experimental
description: Detects suspicious activity where a file is dropped in the temp location

logsource:
    category: process_creation
detection:
    selection:
        TargetFilename:
            - '*\\AppData\\Local\\Temp\\svchost.exe'
    condition: selection
    level: high

falsepositives:
    - Legitimate exe file drops in temp location

```

En este caso, la regla está diseñada para identificar cuándo el archivo`svchost.exe`se deja caer en el`Temp`directorio.

Durante el análisis, es ventajoso tener un agente de monitoreo del sistema que funcione continuamente. En este contexto, hemos elegido`Sysmon`para reunir los registros.`Sysmon`es una herramienta poderosa que captura datos detallados de eventos y ayuda en la creación de`Sigma`normas. Sus categorías de registro abarcan la creación de procesos (`EventID 1`), conexión de red (`EventID 3`), creación de archivos (`EventID 11`), Modificación de registro (`EventID 13`), entre otros. El escrutinio de estos eventos ayuda a identificar indicadores de compromiso (`IOC`s) y comprensión de patrones de comportamiento, facilitando así la elaboración de reglas de detección efectivas.

Por ejemplo,`Sysmon`ha recopilado registros como`process creation`, `process access`, `file creation`, y`network connection`, entre otros, en respuesta a las actividades realizadas por`shell.exe`. Esta información compilada demuestra instrumental para mejorar nuestra comprensión del comportamiento de la muestra y desarrollar reglas de detección más precisas y efectivas.

**Proceso Crear registros**:

![](https://academy.hackthebox.com/storage/modules/227/sysmon_01_process.png)

**Registros de acceso a procesos**(no configurado en los objetivos de Windows de este módulo):

![](https://academy.hackthebox.com/storage/modules/227/sysmon_10_access.png)

**Registros de creación de archivos**:

![](https://academy.hackthebox.com/storage/modules/227/sysmon_11_file.png)

**Registros de conexión de red**:

![](https://academy.hackthebox.com/storage/modules/227/sysmon_03_network.png)

**A continuación se presentan algunas referencias para las reglas de Sigma**:

- Documentación de Sigma:[https://github.com/sigmahq/sigma/wiki/specification](https://github.com/SigmaHQ/sigma/wiki/Specification)
- Sigma Resources -[https://github.com/sigmahq/sigma/tree/master/rules](https://github.com/SigmaHQ/sigma/tree/master/rules)
- El informe DFIR -[https://github.com/the-dfir-report/sigma-rules/tree/main/rules](https://github.com/The-DFIR-Report/Sigma-Rules/tree/main/rules)