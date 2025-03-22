# Caza del mal con Sigma (edición de motosierra)

En la ciberseguridad, el tiempo es de la esencia. El análisis rápido nos permite no solo identificarnos sino también responder a las amenazas antes de que se intensifiquen.

Cuando estamos en contra del reloj, corriendo para encontrar una aguja en un pajar de registros de eventos de Windows sin acceso a un SIEM, las reglas de Sigma combinadas con herramientas como[Motosiería](https://github.com/WithSecureLabs/chainsaw)y[Circolito](https://github.com/wagga40/Zircolite)son nuestros mejores aliados.

Ambas herramientas nos permiten usar reglas Sigma para escanear no solo una, sino múltiples archivos EVTX simultáneamente, ofreciendo un escaneo más amplio y integral de una manera muy eficiente.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Escaneo de registros de eventos de Windows con motosierra**

Chainsaw es una herramienta gratuita diseñada para identificar rápidamente las amenazas de seguridad en los registros de eventos de Windows. Esta herramienta permite búsquedas eficientes de registro de eventos basadas en palabras clave y está equipada con soporte integrado para reglas de detección de Sigma, así como reglas de motosierra personalizada. Por lo tanto, sirve como un activo valioso para validar nuestras reglas Sigma aplicándolas a registros de eventos reales. Vamos a descargar la motosierra del repositorio oficial de GitHub y ejecutarla con algunas reglas de Sigma:

Motosierra se puede encontrar dentro del`C:\Tools\chainsaw`directorio del objetivo de esta sección.

Primero ejecutemos la motosierra con`-h`bandera para ver el menú de ayuda.

Caza del mal con Sigma (edición de motosierra)

```powershell
PS C:\Tools\chainsaw> .\chainsaw_x86_64-pc-windows-msvc.exe -h
Rapidly work with Forensic Artefacts

Usage: chainsaw_x86_64-pc-windows-msvc.exe [OPTIONS] <COMMAND>

Commands:
  dump     Dump an artefact into a different format
  hunt     Hunt through artefacts using detection rules for threat detection
  lint     Lint provided rules to ensure that they load correctly
  search   Search through forensic artefacts for keywords
  analyse  Perform various analyses on artifacts
  help     Print this message or the help of the given subcommand(s)

Options:
      --no-banner                  Hide Chainsaw's banner
      --num-threads <NUM_THREADS>  Limit the thread number (default: num of CPUs)
  -h, --help                       Print help
  -V, --version                    Print version

Examples:

    Hunt with Sigma and Chainsaw Rules:
        ./chainsaw hunt evtx_attack_samples/ -s sigma/ --mapping mappings/sigma-event-logs-all.yml -r rules/

    Hunt with Sigma rules and output in JSON:
        ./chainsaw hunt evtx_attack_samples/ -s sigma/ --mapping mappings/sigma-event-logs-all.yml --json

    Search for the case-insensitive word 'mimikatz':
        ./chainsaw search mimikatz -i evtx_attack_samples/

    Search for Powershell Script Block Events (EventID 4014):
        ./chainsaw search -t 'Event.System.EventID: =4104' evtx_attack_samples/

```

---

### **Ejemplo 1: caza de inicios de sesión fallidos de múltiples fallas de una sola fuente con Sigma**

Ponamos que la motosierra funcione aplicando nuestra regla de Sigma más reciente,`win_security_susp_failed_logons_single_source2.yml`(disponible en`C:\Rules\sigma`), a`lab_events_2.evtx`(disponible en`C:\Events\YARASigma\lab_events_2.evtx`) que contiene múltiples intentos de inicio de sesión fallidos de la misma fuente.

Caza del mal con Sigma (edición de motosierra)

```powershell
PS C:\Tools\chainsaw> .\chainsaw_x86_64-pc-windows-msvc.exe hunt C:\Events\YARASigma\lab_events_2.evtx -s C:\Rules\sigma\win_security_susp_failed_logons_single_source2.yml --mapping .\mappings\sigma-event-logs-all.yml

 ██████╗██╗  ██╗ █████╗ ██╗███╗   ██╗███████╗ █████╗ ██╗    ██╗
██╔════╝██║  ██║██╔══██╗██║████╗  ██║██╔════╝██╔══██╗██║    ██║
██║     ███████║███████║██║██╔██╗ ██║███████╗███████║██║ █╗ ██║
██║     ██╔══██║██╔══██║██║██║╚██╗██║╚════██║██╔══██║██║███╗██║
╚██████╗██║  ██║██║  ██║██║██║ ╚████║███████║██║  ██║╚███╔███╔╝
 ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═══╝╚══════╝╚═╝  ╚═╝ ╚══╝╚══╝
    By Countercept (@FranticTyping, @AlexKornitzer)

[+] Loading detection rules from: C:\Rules\sigma\win_security_susp_failed_logons_single_source2.yml
[+] Loaded 1 detection rules
[+] Loading forensic artefacts from: C:\Events\YARASigma\lab_events_2.evtx (extensions: .evt, .evtx)
[+] Loaded 1 forensic artefacts (69.6 KB)
[+] Hunting: [========================================] 1/1 -
[+] Group: Sigma
┌─────────────────────┬───────────────────────────┬───────┬────────────────────────────────┬──────────┬───────────┬─────────────────┬────────────────────────────────┐
│      timestamp      │        detections         │ count │     Event.System.Provider      │ Event ID │ Record ID │    Computer     │           Event Data           │
├─────────────────────┼───────────────────────────┼───────┼────────────────────────────────┼──────────┼───────────┼─────────────────┼────────────────────────────────┤
│ 2021-05-20 12:49:52 │ + Failed NTLM Logins with │ 5     │ Microsoft-Windows-Security-Aud │ 4776     │ 1861986   │ fs01.offsec.lan │ PackageName: MICROSOFT_AUTHENT │
│                     │ Different Accounts from   │       │ iting                          │          │           │                 │ ICATION_PACKAGE_V1_0           │
│                     │ Single Source System      │       │                                │          │           │                 │ Status: '0xc0000064'           │
│                     │                           │       │                                │          │           │                 │ TargetUserName: NOUSER         │
│                     │                           │       │                                │          │           │                 │ Workstation: FS01              │
└─────────────────────┴───────────────────────────┴───────┴────────────────────────────────┴──────────┴───────────┴─────────────────┴────────────────────────────────┘

[+] 1 Detections found on 1 documents
PS C:\Tools\chainsaw>

```

Nuestra regla de Sigma pudo identificar los múltiples intentos de inicio de sesión fallidos contra`NOUSER`.

Usando el`-s`Parámetro, podemos especificar un directorio que contenga reglas de detección de Sigma (o una regla de detección de Sigma) y la motosierra se cargará, convertirá y ejecutará automáticamente estas reglas con los registros de eventos proporcionados. El archivo de asignación (especificado a través del`--mapping`Parámetro) le dice a Chainsaw qué campos en los registros de eventos para usar para la coincidencia de reglas.

---

### **Ejemplo 2: Huntando el tamaño anormal de la línea de comando PowerShell con Sigma (basado en el ID de evento 4688)**

En primer lugar, preparemos el escenario al reconocer que PowerShell, siendo un lenguaje de secuencias de comandos altamente flexible, es un objetivo atractivo para los atacantes. Su profunda integración con las API de Windows y el marco .NET lo convierten en un candidato ideal para una variedad de actividades posteriores a la explotación.

Para ocultar sus acciones, los atacantes utilizan capas de codificación complejas o cmdlets de mal uso para fines para los que no estaban diseñados. Esto lleva a comandos de PowerShell anormalmente largos que a menudo incorporan codificación Base64, fusión de cadenas y varias variables que contienen partes fragmentadas del comando.

Se puede encontrar una regla de sigma que puede detectar líneas de comando de PowerShell anormalmente largas dentro del`C:\Rules\sigma`directorio del objetivo de esta sección, guardado como`proc_creation_win_powershell_abnormal_commandline_size.yml`.

Código:yaml

```yaml
title: Unusually Long PowerShell CommandLine
id: d0d28567-4b9a-45e2-8bbc-fb1b66a1f7f6
status: test
description: Detects unusually long PowerShell command lines with a length of 1000 characters or more
references:
    - https://speakerdeck.com/heirhabarov/hunting-for-powershell-abuse
author: oscd.community, Natalia Shornikova / HTB Academy, Dimitrios Bougioukas
date: 2020/10/06
modified: 2023/04/14
tags:
    - attack.execution
    - attack.t1059.001
    - detection.threat_hunting
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        EventID: 4688
        NewProcessName|endswith:
            - '\powershell.exe'
            - '\pwsh.exe'
            - '\cmd.exe'
    selection_powershell:
        CommandLine|contains:
            - 'powershell.exe'
            - 'pwsh.exe'
    selection_length:
        CommandLine|re: '.{1000,}'
    condition: selection and selection_powershell and selection_length
falsepositives:
    - Unknown
level: low

```

**Desglose de reglas de Sigma**:

- `logsource`: La regla analiza los registros en la categoría de[proceso_creation](https://github.com/SigmaHQ/sigma/blob/master/documentation/logsource-guides/windows/category/process_creation.md)y está diseñado para trabajar en máquinas de Windows.

Código:yaml

```yaml
logsource:
    category: process_creation
    product: windows

```

- `detection`: El`selection`Verificación de la sección si algún evento de Windows con ID[4688](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4688)existir y también verifica si el`NewProcessName`el campo termina con`\powershell.exe`, `\pwsh.exe`, o`\cmd.exe`. El`selection_powershell`Comprobaciones de la sección Si la línea de comandos ejecutada incluye ejecutables relacionados con PowerShell y finalmente, el`selection_length`verificación de la sección si el`CommandLine`campo de la`4688`El evento contiene 1,000 caracteres o más. El`condition`verificación de la sección si los criterios de selección dentro del`selection`, `selection_powershell`, y`selection_length`todas las secciones se cumplen.

Código:yaml

```yaml
detection:
    selection:
        EventID: 4688
        NewProcessName|endswith:
            - '\powershell.exe'
            - '\pwsh.exe'
            - '\cmd.exe'
    selection_powershell:
        CommandLine|contains:
            - 'powershell.exe'
            - 'pwsh.exe'
    selection_length:
        CommandLine|re: '.{1000,}'
    condition: selection and selection_powershell and selection_length

```

Ponamos que la motosierra funcione aplicando la regla de Sigma mencionada anteriormente,`proc_creation_win_powershell_abnormal_commandline_size.yml`(disponible en`C:\Rules\sigma`), a`lab_events_3.evtx`(disponible en`C:\Events\YARASigma\lab_events_3.evtx`, gracias a[mdecrevoisier](https://twitter.com/mdecrevoisier)) que contiene`4688`Eventos con comandos anormalmente largos de PowerShell.

![](https://academy.hackthebox.com/storage/modules/234/4688.png)

Caza del mal con Sigma (edición de motosierra)

```powershell
PS C:\Tools\chainsaw> .\chainsaw_x86_64-pc-windows-msvc.exe hunt C:\Events\YARASigma\lab_events_3.evtx -s C:\Rules\sigma\proc_creation_win_powershell_abnormal_commandline_size.yml --mapping .\mappings\sigma-event-logs-all.yml

 ██████╗██╗  ██╗ █████╗ ██╗███╗   ██╗███████╗ █████╗ ██╗    ██╗
██╔════╝██║  ██║██╔══██╗██║████╗  ██║██╔════╝██╔══██╗██║    ██║
██║     ███████║███████║██║██╔██╗ ██║███████╗███████║██║ █╗ ██║
██║     ██╔══██║██╔══██║██║██║╚██╗██║╚════██║██╔══██║██║███╗██║
╚██████╗██║  ██║██║  ██║██║██║ ╚████║███████║██║  ██║╚███╔███╔╝
 ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═══╝╚══════╝╚═╝  ╚═╝ ╚══╝╚══╝
    By Countercept (@FranticTyping, @AlexKornitzer)

[+] Loading detection rules from: C:\Rules\sigma\proc_creation_win_powershell_abnormal_commandline_size.yml
[+] Loaded 1 detection rules
[+] Loading forensic artefacts from: C:\Events\YARASigma\lab_events_3.evtx (extensions: .evt, .evtx)
[+] Loaded 1 forensic artefacts (69.6 KB)
[+] Hunting: [========================================] 1/1 -
[+] 0 Detections found on 0 documents

```

Nuestro sigma no parece ser capaz de identificar los comandos de PowerShell anormalmente largos dentro de estos`4688`eventos.

¿Significa esto que nuestra regla Sigma es defectuosa? ¡No! Como se discutió anteriormente, el archivo de mapeo de Chainsaw (especificado a través del`--mapping`Parámetro) le dice qué campos en los registros de eventos se utilizarán para la coincidencia de reglas.

Parece el`NewProcessName`faltaba campo en el`sigma-event-logs-all.yml`archivo de asignación.

Presentamos el`NewProcessName`campo en un`sigma-event-logs-all-new.yml`mapeo de archivo dentro del`C:\Tools\chainsaw\mappings`directorio del objetivo de esta sección.

Vamos a ejecutar Chainsaw nuevamente con este nuevo archivo de asignación.

Caza del mal con Sigma (edición de motosierra)

```powershell
PS C:\Tools\chainsaw> .\chainsaw_x86_64-pc-windows-msvc.exe hunt C:\Events\YARASigma\lab_events_3.evtx -s C:\Rules\sigma\proc_creation_win_powershell_abnormal_commandline_size.yml --mapping .\mappings\sigma-event-logs-all-new.yml

 ██████╗██╗  ██╗ █████╗ ██╗███╗   ██╗███████╗ █████╗ ██╗    ██╗
██╔════╝██║  ██║██╔══██╗██║████╗  ██║██╔════╝██╔══██╗██║    ██║
██║     ███████║███████║██║██╔██╗ ██║███████╗███████║██║ █╗ ██║
██║     ██╔══██║██╔══██║██║██║╚██╗██║╚════██║██╔══██║██║███╗██║
╚██████╗██║  ██║██║  ██║██║██║ ╚████║███████║██║  ██║╚███╔███╔╝
 ╚═════╝╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚═╝  ╚═══╝╚══════╝╚═╝  ╚═╝ ╚══╝╚══╝
    By Countercept (@FranticTyping, @AlexKornitzer)

[+] Loading detection rules from: C:\Rules\sigma\proc_creation_win_powershell_abnormal_commandline_size.yml
[+] Loaded 1 detection rules
[+] Loading forensic artefacts from: C:\Events\YARASigma\lab_events_3.evtx (extensions: .evtx, .evt)
[+] Loaded 1 forensic artefacts (69.6 KB)
[+] Hunting: [========================================] 1/1 -
[+] Group: Sigma
┌─────────────────────┬─────────────────────────────┬───────┬────────────────────────────────┬──────────┬───────────┬─────────────────────┬──────────────────────────────────┐
│      timestamp      │         detections          │ count │     Event.System.Provider      │ Event ID │ Record ID │      Computer       │            Event Data            │
├─────────────────────┼─────────────────────────────┼───────┼────────────────────────────────┼──────────┼───────────┼─────────────────────┼──────────────────────────────────┤
│ 2021-04-22 08:51:04 │ + Unusually Long PowerShell │ 1     │ Microsoft-Windows-Security-Aud │ 4688     │ 435121    │ fs03vuln.offsec.lan │ CommandLine: powershell.exe -n   │
│                     │ CommandLine                 │       │ iting                          │          │           │                     │ op -w hidden -noni -c "if([Int   │
│                     │                             │       │                                │          │           │                     │ Ptr]::Size -eq 4){$b='powershe   │
│                     │                             │       │                                │          │           │                     │ ll.exe'}else{$b=$env:windir+'\   │
│                     │                             │       │                                │          │           │                     │ syswow64\WindowsPowerShell\v1.   │
│                     │                             │       │                                │          │           │                     │ 0\powershell.exe'};$s=New-Obje   ││                     │                             │       │                                │          │           │                     │ ct System.Diagnostics.ProcessS   │
│                     │                             │       │                                │          │           │                     │ tartInfo;$s.FileName=$b;$s.Arg   ││                     │                             │       │                                │          │           │                     │ uments='-noni -nop -w hidden -   │
│                     │                             │       │                                │          │           │                     │ c &([scriptblock]::create((New   │
│                     │                             │       │                                │          │           │                     │ -Object System.IO.StreamReader   │
│                     │                             │       │                                │          │           │                     │ (New-Object System.IO.Compress   │
│                     │                             │       │                                │          │           │                     │ ion.GzipStream((New-Object Sys   │
│                     │                             │       │                                │          │           │                     │ tem.IO.MemoryStream(,[System.C   │
│                     │                             │       │                                │          │           │                     │ onvert]::FromBase64String(''H4   │
│                     │                             │       │                                │          │           │                     │ sIAPg2gWACA7VWbW+bSBD+nEj5D6iy   │
│                     │                             │       │                                │          │           │                     │ ...                              │
│                     │                             │       │                                │          │           │                     │ (use --full to show all content) │
│                     │                             │       │                                │          │           │                     │ NewProcessId: '0x7f0'            │
│                     │                             │       │                                │          │           │                     │ NewProcessName: C:\Windows\Sys   │
│                     │                             │       │                                │          │           │                     │ tem32\WindowsPowerShell\v1.0\p   │
│                     │                             │       │                                │          │           │                     │ owershell.exe                    │
│                     │                             │       │                                │          │           │                     │ ProcessId: '0x6e8'               │
│                     │                             │       │                                │          │           │                     │ SubjectDomainName: OFFSEC        │
│                     │                             │       │                                │          │           │                     │ SubjectLogonId: '0x3e7'          │
│                     │                             │       │                                │          │           │                     │ SubjectUserName: FS03VULN$       ││                     │                             │       │                                │          │           │                     │ SubjectUserSid: S-1-5-18         │
│                     │                             │       │                                │          │           │                     │ TokenElevationType: '%%1936'     │
├─────────────────────┼─────────────────────────────┼───────┼────────────────────────────────┼──────────┼───────────┼─────────────────────┼──────────────────────────────────┤
│ 2021-04-22 08:51:04 │ + Unusually Long PowerShell │ 1     │ Microsoft-Windows-Security-Aud │ 4688     │ 435120    │ fs03vuln.offsec.lan │ CommandLine: C:\Windows\system   │
│                     │ CommandLine                 │       │ iting                          │          │           │                     │ 32\cmd.exe /b /c start /b /min   │
│                     │                             │       │                                │          │           │                     │  powershell.exe -nop -w hidden   │
│                     │                             │       │                                │          │           │                     │  -noni -c "if([IntPtr]::Size -   │
│                     │                             │       │                                │          │           │                     │ eq 4){$b='powershell.exe'}else   ││                     │                             │       │                                │          │           │                     │ {$b=$env:windir+'\syswow64\Win   │
│                     │                             │       │                                │          │           │                     │ dowsPowerShell\v1.0\powershell   │
│                     │                             │       │                                │          │           │                     │ .exe'};$s=New-Object System.Di   ││                     │                             │       │                                │          │           │                     │ agnostics.ProcessStartInfo;$s.   ││                     │                             │       │                                │          │           │                     │ FileName=$b;$s.Arguments='-non   │
│                     │                             │       │                                │          │           │                     │ i -nop -w hidden -c &([scriptb   │
│                     │                             │       │                                │          │           │                     │ lock]::create((New-Object Syst   │
│                     │                             │       │                                │          │           │                     │ em.IO.StreamReader(New-Object    │
│                     │                             │       │                                │          │           │                     │ System.IO.Compression.GzipStre   │
│                     │                             │       │                                │          │           │                     │ am((New-Object System.IO.Memor   │
│                     │                             │       │                                │          │           │                     │ yStream(,[System.Convert]::Fro   │
│                     │                             │       │                                │          │           │                     │ ...                              │
│                     │                             │       │                                │          │           │                     │ (use --full to show all content) │
│                     │                             │       │                                │          │           │                     │ NewProcessId: '0x6e8'            │
│                     │                             │       │                                │          │           │                     │ NewProcessName: C:\Windows\Sys   │
│                     │                             │       │                                │          │           │                     │ tem32\cmd.exe                    │
│                     │                             │       │                                │          │           │                     │ ProcessId: '0x1d0'               │
│                     │                             │       │                                │          │           │                     │ SubjectDomainName: OFFSEC        │
│                     │                             │       │                                │          │           │                     │ SubjectLogonId: '0x3e7'          │
│                     │                             │       │                                │          │           │                     │ SubjectUserName: FS03VULN$       │
│                     │                             │       │                                │          │           │                     │ SubjectUserSid: S-1-5-18         │
│                     │                             │       │                                │          │           │                     │ TokenElevationType: '%%1936'     │
├─────────────────────┼─────────────────────────────┼───────┼────────────────────────────────┼──────────┼───────────┼─────────────────────┼──────────────────────────────────┤
│ 2021-04-22 08:51:05 │ + Unusually Long PowerShell │ 1     │ Microsoft-Windows-Security-Aud │ 4688     │ 435124    │ fs03vuln.offsec.lan │ CommandLine: '"C:\Windows\sysw   │
│                     │ CommandLine                 │       │ iting                          │          │           │                     │ ow64\WindowsPowerShell\v1.0\po   │
│                     │                             │       │                                │          │           │                     │ wershell.exe" -noni -nop -w hi   ││                     │                             │       │                                │          │           │                     │ dden -c &([scriptblock]::creat   │
│                     │                             │       │                                │          │           │                     │ e((New-Object System.IO.Stream   │
│                     │                             │       │                                │          │           │                     │ Reader(New-Object System.IO.Co   │
│                     │                             │       │                                │          │           │                     │ mpression.GzipStream((New-Obje   │
│                     │                             │       │                                │          │           │                     │ ct System.IO.MemoryStream(,[Sy   │
│                     │                             │       │                                │          │           │                     │ stem.Convert]::FromBase64Strin   │
│                     │                             │       │                                │          │           │                     │ g(''H4sIAPg2gWACA7VWbW+bSBD+nE   │
│                     │                             │       │                                │          │           │                     │ j5D6iyBKiOIbbbvEiVbgFju4kdbBI7   │
│                     │                             │       │                                │          │           │                     │ sWud1rCGbRbWgSWO0/a/32CgTa/pXX   │
│                     │                             │       │                                │          │           │                     │ vSIb/sy8zszDPPzrDKYk9QHku+w91M   │
│                     │                             │       │                                │          │           │                     │ +nSwv+fgBEeSUouy9fqkLtXSsaPu7c   │
│                     │                             │       │                                │          │           │                     │ FGjXd7+K30TlLmaL22eIRpvDg7M7Mk   │
│                     │                             │       │                                │          │           │                     │ IbEo5o0uEShNSbRklKSKKn2WpiFJyO   │
│                     │                             │       │                                │          │           │                     │ ...                              │
│                     │                             │       │                                │          │           │                     │ (use --full to show all content) │
│                     │                             │       │                                │          │           │                     │ NewProcessId: '0x8f0'            │
│                     │                             │       │                                │          │           │                     │ NewProcessName: C:\Windows\Sys   │
│                     │                             │       │                                │          │           │                     │ WOW64\WindowsPowerShell\v1.0\p   │
│                     │                             │       │                                │          │           │                     │ owershell.exe                    │
│                     │                             │       │                                │          │           │                     │ ProcessId: '0x7f0'               │
│                     │                             │       │                                │          │           │                     │ SubjectDomainName: OFFSEC        │
│                     │                             │       │                                │          │           │                     │ SubjectLogonId: '0x3e7'          │
│                     │                             │       │                                │          │           │                     │ SubjectUserName: FS03VULN$       ││                     │                             │       │                                │          │           │                     │ SubjectUserSid: S-1-5-18         │
│                     │                             │       │                                │          │           │                     │ TokenElevationType: '%%1936'     │
└─────────────────────┴─────────────────────────────┴───────┴────────────────────────────────┴──────────┴───────────┴─────────────────────┴──────────────────────────────────┘

[+] 3 Detections found on 3 documents

```

Nuestra regla de sigma descubrió con éxito los tres comandos de PowerShell anormalmente largos que existen dentro de`lab_events_3.evtx`

¡Recuerde que la configuración cuando se trata de usar o transmitir reglas Sigma es de suma importancia!