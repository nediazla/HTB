# Desarrollo de reglas de Sigma

En esta sección, cubriremos el desarrollo manual de reglas Sigma.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Desarrollar manualmente una regla de Sigma**

### **Ejemplo 1: Dumping de credenciales de LSASS**

Vamos a sumergirnos en el mundo de las reglas de Sigma usando una muestra llamada`shell.exe`(una versión renombrada de[mimikatz](https://en.wikipedia.org/wiki/Mimikatz)) residiendo en el`C:\Samples\YARASigma`directorio del objetivo de esta sección como ilustración. Queremos entender el proceso detrás de elaborar una regla de Sigma, así que vamos a ensuciarnos las manos.

Después de ejecutar`shell.exe`de la siguiente manera, recopilamos los eventos más críticos y los guardamos como`lab_events.evtx`dentro del`C:\Events\YARASigma`directorio del objetivo de esta sección.

El proceso creado por`shell.exe`(Mimikatz) intentará acceder a la memoria de proceso de`lsass.exe`. La herramienta de monitoreo del sistema[Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)se ejecutaba en segundo plano y capturó esta actividad en los registros de eventos (ID de evento[10](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon#event-id-10-processaccess)).

Desarrollo de reglas de Sigma

```
C:\Samples\YARASigma>shell.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )## \ / ##       > https://blog.gentilkiwi.com/mimikatz '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz# sekurlsa::logonpasswords---SNIP---
Authentication Id : 0 ; 100080 (00000000:000186f0)
Session           : Interactive from 1
User Name         : htb-student
Domain            : DESKTOP-VJF8GH8
Logon Server      : DESKTOP-VJF8GH8
Logon Time        : 8/25/2023 2:17:20 PM
SID               : S-1-5-21-1412399592-1502967738-1150298762-1001
        msv :
         [00000003] Primary
         * Username : htb-student
         * Domain   : .
         * NTLM     : 3c0e5d303ec84884ad5c3b7876a06ea6
         * SHA1     : b2978f9abc2f356e45cb66ec39510b1ccca08a0e
        tspkg :
        wdigest :
         * Username : htb-student
         * Domain   : DESKTOP-VJF8GH8
         * Password : (null)
        kerberos :
         * Username : htb-student
         * Domain   : DESKTOP-VJF8GH8
         * Password : (null)
        ssp :
        credman :
        cloudap :

Authentication Id : 0 ; 100004 (00000000:000186a4)
Session           : Interactive from 1
User Name         : htb-student
Domain            : DESKTOP-VJF8GH8
Logon Server      : DESKTOP-VJF8GH8
Logon Time        : 8/25/2023 2:17:20 PM
SID               : S-1-5-21-1412399592-1502967738-1150298762-1001
        msv :
         [00000003] Primary
         * Username : htb-student
         * Domain   : .
         * NTLM     : 3c0e5d303ec84884ad5c3b7876a06ea6
         * SHA1     : b2978f9abc2f356e45cb66ec39510b1ccca08a0e
        tspkg :
        wdigest :
         * Username : htb-student
         * Domain   : DESKTOP-VJF8GH8
         * Password : (null)
        kerberos :
         * Username : htb-student
         * Domain   : DESKTOP-VJF8GH8
         * Password : HTB_@cademy_stdnt!
        ssp :
        credman :
        cloudap :
---SNIP---

```

En primer lugar, sysmon`Event ID 10`se activa cuando un proceso accede a otro proceso, y registra los indicadores de permiso en el`GrantedAccess`campo. Este registro de eventos contiene dos campos importantes,`TargetImage`y`GrantedAccess`. En un escenario típico de vertido de memoria LSASS, el proceso malicioso necesita permisos específicos para acceder al espacio de memoria del proceso LSASS. Estos permisos a menudo son acceso de lectura/escritura, entre otras cosas.

![](https://academy.hackthebox.com/storage/modules/234/sigma_evt_log.png)

Ahora, ¿por qué es`0x1010`¿Crucial aquí? Esta bandera hexadecimal esencialmente combina`PROCESS_VM_READ (0x0010)`y`PROCESS_QUERY_INFORMATION (0x0400)`permisos. Para traducir eso: el proceso está solicitando acceso de lectura a la memoria virtual de LSASS y la capacidad de consultar cierta información del proceso. Mientras`0x0410`es la bandera de Acccess más común utilizada para leer la memoria LSASS,`0x1010`Implica tanto la lectura como la consulta de información del proceso y también se observa con frecuencia durante los ataques de descarga de credenciales.

Entonces, ¿cómo podemos armar esta información para la detección? Bueno, en nuestra pila de monitoreo de seguridad, configuraríamos sysmon para marcar o alertar en cualquier`Event ID 10`donde el`TargetImage`es`lsass.exe`y`GrantedAccess`está configurado en`0x1010`.

A continuación se puede encontrar una regla Sigma que verifica las condiciones mencionadas anteriormente.

Código:yaml

```yaml
title: LSASS Access with rare GrantedAccess flag
status: experimental
description: This rule will detect when a process tries to access LSASS memory with suspicious access flag 0x1010
date: 2023/07/08
tags:
    - attack.credential_access
    - attack.t1003.001
logsource:
    category: process_access
    product: windows
detection:
    selection:
        TargetImage|endswith: '\lsass.exe'
        GrantedAccess|endswith: '0x1010'
    condition: selection

```

**Desglose de reglas de Sigma**

- `title`: Este título ofrece una visión general concisa del objetivo de la regla, específicamente destinado a detectar interacciones con la memoria LSASS que involucra un indicador de acceso particular.

Código:yaml

```yaml
title: LSASS Access with rare GrantedAccess flag

```

- `status`: Este campo señala que la regla está en la fase de prueba, lo que sugiere que puede ser necesaria el ajuste o validación adicional adicional.

Código:yaml

```yaml
status: experimental

```

- `description`: Descripción de la regla.

Código:yaml

```yaml
description: This rule will detect when a process tries to access LSASS memory with suspicious access flag 0x1010

```

- `date`: Este campo marca la fecha en que la regla se actualizó o se creó originalmente.

Código:yaml

```yaml
date: 2023/07/08

```

- `tags`: La regla está etiquetada con`attack.credential_access`y`attack.t1003.001`. Estas etiquetas ayudan a clasificar la regla basada en técnicas de ataque conocidas o tácticas relacionadas con el acceso a las credenciales.

Código:yaml

```yaml
tags:
	- attack.credential_access
	- attack.t1003.001`

```

- `logsource`: El Logsource especifica la fuente de registro que la regla está destinada a analizar. Contiene`category`como`process_access`lo que indica que la regla se centra en eventos de registro relacionados con el acceso al proceso (`Sysmon Event ID 10`, si usamos los archivos de configuración predeterminados de Sigma). También,`product: windows`Especifica que la regla está diseñada específicamente para los sistemas operativos de Windows.

Código:yaml

```yaml
logsource:
	category: process_access
	product: windows

```

- `detection`: La sección de detección define las condiciones que deben cumplirse para que la regla active una alerta. La parte de selección especifica los criterios para seleccionar eventos de registro relevantes donde el`TargetImage`el campo termina con`\lsass.exe`y`GrantedAccess`El campo termina con el valor hexadecimal`0x1010`. El`GrantedAccess`El campo representa los derechos de acceso o permisos asociados con el proceso. En este caso, se dirige a eventos con un indicador de acceso específico de`0x1010`. Finalmente, la parte de condición especifica que los criterios de selección deben cumplirse para que la regla active una alerta. En este caso, ambos`TargetImage`y`GrantedAccess`Se deben cumplir los criterios.

Código:yaml

```yaml
detection:
	selection:
        TargetImage|endswith: '\lsass.exe'
        GrantedAccess|endswith: '0x1010'
    condition: selection

```

---

La primera regla de Sigma anterior se puede encontrar dentro del`C:\Rules\sigma`directorio del objetivo de esta sección como`proc_access_win_lsass_access.yml`. Exploremos el`sigmac`Herramienta que puede ayudarnos a transformar esta regla en consultas o configuraciones compatibles con una multitud de SIEM, soluciones de gestión de registros y otras herramientas de análisis de seguridad.

El`sigmac`La herramienta se puede encontrar dentro del`C:\Tools\sigma-0.21\tools`directorio del objetivo de esta sección.

Supongamos que queríamos convertir nuestra regla de sigma en un PowerShell (`Get-WinEvent`) consulta. Esto podría haberse logrado con la ayuda de`sigmac`como sigue.

Desarrollo de reglas de Sigma

```powershell
PS C:\Tools\sigma-0.21\tools> python sigmac -t powershell 'C:\Rules\sigma\proc_access_win_lsass_access.yml'
Get-WinEvent | where {($_.ID -eq "10" -and $_.message -match "TargetImage.*.*\\lsass.exe" -and $_.message -match "GrantedAccess.*.*0x1010") } | select TimeCreated,Id,RecordId,ProcessId,MachineName,Message
```

Ajustemos la consulta de Get-Winevent anterior especificando el archivo .evtx que está relacionado con el acceso de LSASS por otro proceso (`lab_events.evtx`dentro del`C:\Events\YARASigma`directorio del objetivo de esta sección) y vea si identificará el evento Sysmon (`ID 10`) que analizamos al comienzo de esta sección.

**Nota**: Abra una terminal de PowerShell como administrador para ejecutar la consulta.

Desarrollo de reglas de Sigma

```powershell
PS C:\Tools\sigma-0.21\tools> Get-WinEvent -Path C:\Events\YARASigma\lab_events.evtx | where {($_.ID -eq "10" -and $_.message -match "TargetImage.*.*\\lsass.exe" -and $_.message -match "GrantedAccess.*.*0x1010") } | select TimeCreated,Id,RecordId,ProcessId,MachineName,MessageTimeCreated : 7/9/2023 7:44:14 AM
Id          : 10
RecordId    : 7810
ProcessId   : 3324
MachineName : RDSEMVM01
Message     : Process accessed:
              RuleName:
              UtcTime: 2023-07-09 14:44:14.260
              SourceProcessGUID: {e7bf76b7-c7ba-64aa-0000-0010e8e9a602}
              SourceProcessId: 1884
              SourceThreadId: 7872
              SourceImage: C:\htb\samples\shell.exe
              TargetProcessGUID: {e7bf76b7-d7ec-6496-0000-001027d60000}
              TargetProcessId: 668
              TargetImage: C:\Windows\system32\lsass.exe
              GrantedAccess: 0x1010
              CallTrace: C:\Windows\SYSTEM32\ntdll.dll+9d4c4|C:\Windows\System32\KERNELBASE.dll+2c13e|C:\htb\samples\sh
              ell.exe+c291e|C:\htb\samples\shell.exe+c2cf5|C:\htb\samples\shell.exe+c285d|C:\htb\samples\shell.exe+85a4
              4|C:\htb\samples\shell.exe+8587c|C:\htb\samples\shell.exe+85647|C:\htb\samples\shell.exe+c97a5|C:\Windows
              \System32\KERNEL32.DLL+17034|C:\Windows\SYSTEM32\ntdll.dll+526a1
              SourceUser: %12
              TargetUser: %13

```

¡El evento Sysmon relacionado con ID 10 se identifica con éxito!

---

Pero no nos detengamos allí; recuerde, los falsos positivos son enemigos del monitoreo de seguridad efectivo.

- También debemos referirse cruzados el`SourceImage`(El proceso que inicia el acceso) en una lista de procesos seguros y conocidos que comúnmente interactúan con LSASS.
- Si vemos un proceso desconocido o inusual que intenta leer LSASS con un`GrantedAccess`que termina con`10`, `30`, `50`, `70`, `90`, `B0`, `D0`, `F0`, `18`, `38`, `58`, `78`, `98`, `B8`, `D8`, `F8`, `1A`, `3A`, `5A`, `7A`, `9A`, `BA`, `DA`, `FA`, `0x14C2`, y`FF`(Estos sufijos provienen de estudiar el`GrantedAccess`Los valores que requieren varias técnicas de vertido de credenciales de LSASS), eso es una bandera roja, y nuestro protocolo de respuesta a incidentes debería activar.
- Especialmente, si el`SourceImage`reside en caminos sospechosos que contienen,`\Temp\`, `\Users\Public\`, `\PerfLogs\`, `\AppData\`, `\htb\`etc. Esa es otra bandera roja, y nuestro protocolo de respuesta a incidentes debería comenzar.

Una versión más robusta de la regla de sigma que creamos tener en cuenta los puntos anteriores se puede encontrar dentro del`C:\Rules\sigma`directorio del objetivo de esta sección como`proc_access_win_lsass_access_robust.yml`

Código:yaml

```yaml
title: LSASS Access From Program in Potentially Suspicious Folder
id: fa34b441-961a-42fa-a100-ecc28c886725
status: experimental
description: Detects process access to LSASS memory with suspicious access flags and from a potentially suspicious folder
references:
    - https://docs.microsoft.com/en-us/windows/win32/procthread/process-security-and-access-rights
    - https://onedrive.live.com/view.aspx?resid=D026B4699190F1E6!2843&ithint=file%2cpptx&app=PowerPoint&authkey=!AMvCRTKB_V1J5ow
    - https://web.archive.org/web/20230208123920/https://cyberwardog.blogspot.com/2017/03/chronicles-of-threat-hunter-hunting-for_22.html
    - https://www.slideshare.net/heirhabarov/hunting-for-credentials-dumping-in-windows-environment
    - http://security-research.dyndns.org/pub/slides/FIRST2017/FIRST-2017_Tom-Ueltschi_Sysmon_FINAL_notes.pdf
author: Florian Roth (Nextron Systems)
date: 2021/11/27
modified: 2023/05/05
tags:
    - attack.credential_access
    - attack.t1003.001
    - attack.s0002
logsource:
    category: process_access
    product: windows
detection:
    selection:
        TargetImage|endswith: '\lsass.exe'
        GrantedAccess|endswith:
            - '10'
            - '30'
            - '50'
            - '70'
            - '90'
            - 'B0'
            - 'D0'
            - 'F0'
            - '18'
            - '38'
            - '58'
            - '78'
            - '98'
            - 'B8'
            - 'D8'
            - 'F8'
            - '1A'
            - '3A'
            - '5A'
            - '7A'
            - '9A'
            - 'BA'
            - 'DA'
            - 'FA'
            - '0x14C2'  # https://github.com/b4rtik/ATPMiniDump/blob/76304f93b390af3bb66e4f451ca16562a479bdc9/ATPMiniDump/ATPMiniDump.c
            - 'FF'
        SourceImage|contains:
            - '\Temp\'
            - '\Users\Public\'
            - '\PerfLogs\'
            - '\AppData\'
            - '\htb\'
    filter_optional_generic_appdata:
        SourceImage|startswith: 'C:\Users\'
        SourceImage|contains: '\AppData\Local\'
        SourceImage|endswith:
            - '\Microsoft VS Code\Code.exe'
            - '\software_reporter_tool.exe'
            - '\DropboxUpdate.exe'
            - '\MBAMInstallerService.exe'
            - '\WebexMTA.exe'
            - '\WebEx\WebexHost.exe'
            - '\JetBrains\Toolbox\bin\jetbrains-toolbox.exe'
        GrantedAccess: '0x410'
    filter_optional_dropbox_1:
        SourceImage|startswith: 'C:\Windows\Temp\'
        SourceImage|endswith: '.tmp\DropboxUpdate.exe'
        GrantedAccess:
            - '0x410'
            - '0x1410'
    filter_optional_dropbox_2:
        SourceImage|startswith: 'C:\Users\'
        SourceImage|contains: '\AppData\Local\Temp\'
        SourceImage|endswith: '.tmp\DropboxUpdate.exe'
        GrantedAccess: '0x1410'
    filter_optional_dropbox_3:
        SourceImage|startswith:
            - 'C:\Program Files (x86)\Dropbox\'
            - 'C:\Program Files\Dropbox\'
        SourceImage|endswith: '\DropboxUpdate.exe'
        GrantedAccess: '0x1410'
    filter_optional_nextron:
        SourceImage|startswith:
            - 'C:\Windows\Temp\asgard2-agent\'
            - 'C:\Windows\Temp\asgard2-agent-sc\'
        SourceImage|endswith:
            - '\thor64.exe'
            - '\thor.exe'
            - '\aurora-agent-64.exe'
            - '\aurora-agent.exe'
        GrantedAccess:
            - '0x1fffff'
            - '0x1010'
            - '0x101010'
    filter_optional_ms_products:
        SourceImage|startswith: 'C:\Users\'
        SourceImage|contains|all:
            - '\AppData\Local\Temp\'
            - '\vs_bootstrapper_'
        GrantedAccess: '0x1410'
    filter_optional_chrome_update:
        SourceImage|startswith: 'C:\Program Files (x86)\Google\Temp\'
        SourceImage|endswith: '.tmp\GoogleUpdate.exe'
        GrantedAccess:
            - '0x410'
            - '0x1410'
    filter_optional_keybase:
        SourceImage|startswith: 'C:\Users\'
        SourceImage|endswith: \AppData\Local\Keybase\keybase.exe
        GrantedAccess: '0x1fffff'
    filter_optional_avira:
        SourceImage|contains: '\AppData\Local\Temp\is-'
        SourceImage|endswith: '.tmp\avira_system_speedup.tmp'
        GrantedAccess: '0x1410'
    filter_optional_viberpc_updater:
        SourceImage|startswith: 'C:\Users\'
        SourceImage|contains: '\AppData\Roaming\ViberPC\'
        SourceImage|endswith: '\updater.exe'
        TargetImage|endswith: '\winlogon.exe'
        GrantedAccess: '0x1fffff'
    filter_optional_adobe_arm_helper:
        SourceImage|startswith:  # Example path: 'C:\Program Files (x86)\Common Files\Adobe\ARM\1.0\Temp\2092867405\AdobeARMHelper.exe'
            - 'C:\Program Files\Common Files\Adobe\ARM\'
            - 'C:\Program Files (x86)\Common Files\Adobe\ARM\'
        SourceImage|endswith: '\AdobeARMHelper.exe'
        GrantedAccess: '0x1410'
    condition: selection and not 1 of filter_optional_*
fields:
    - User
    - SourceImage
    - GrantedAccess
falsepositives:
    - Updaters and installers are typical false positives. Apply custom filters depending on your environment
level: medium

```

Observe cómo la condición filtra falsos positivos (selección`and not 1 of filter_optional_*`).

---

### **Ejemplo 2: Múltiples inicios de sesión fallidos de una sola fuente (basado en el evento 4776)**

Según Microsoft,[Evento 4776](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4776)genera cada vez que ocurre una validación de credencial utilizando la autenticación NTLM.

Este evento ocurre solo en la computadora que es autorizada para las credenciales proporcionadas. Para las cuentas de dominio, el controlador de dominio es autorizado. Para las cuentas locales, la computadora local es autorizada.

Muestra intentos de validación de credenciales exitosos y sin éxito.

Muestra solo el nombre de la computadora (`Source Workstation`) desde el cual se realizó el intento de autenticación (fuente de autenticación). Por ejemplo, si se autentica de Client-1 a Server-1 usando una cuenta de dominio, verá Client-1 en el`Source Workstation`campo. La información sobre la computadora de destino (Server-1) no se presenta en este evento.

Si falla un intento de validación de credencial, verá un evento de falla con el valor del parámetro del código de error que no es igual a`0x0`.

`lab_events_2.evtx`dentro del`C:\Events\YARASigma`El directorio del objetivo de esta sección contiene eventos relacionados con múltiples intentos de inicio de sesión fallidos contra`NOUSER`(gracias a[mdecrevoisier](https://twitter.com/mdecrevoisier)).

![](https://academy.hackthebox.com/storage/modules/234/4776.png)

![](https://academy.hackthebox.com/storage/modules/234/4776_2.png)

Se puede encontrar una regla de sigma válida para detectar múltiples intentos de inicio de sesión fallidos que se originan en la misma fuente dentro del`C:\Rules\sigma`directorio del objetivo de esta sección, guardado como`win_security_susp_failed_logons_single_source2.yml`

Código:yaml

```yaml
title: Failed NTLM Logins with Different Accounts from Single Source System
id: 6309ffc4-8fa2-47cf-96b8-a2f72e58e538
related:
    - id: e98374a6-e2d9-4076-9b5c-11bdb2569995
      type: derived
status: unsupported
description: Detects suspicious failed logins with different user accounts from a single source system
author: Florian Roth (Nextron Systems)
date: 2017/01/10
modified: 2023/02/24
tags:
    - attack.persistence
    - attack.privilege_escalation
    - attack.t1078
logsource:
    product: windows
    service: security
detection:
    selection2:
        EventID: 4776
        TargetUserName: '*'
        Workstation: '*'
    condition: selection2 | count(TargetUserName) by Workstation > 3
falsepositives:
    - Terminal servers
    - Jump servers
    - Other multiuser systems like Citrix server farms
    - Workstations with frequently changing users
level: medium

```

**Desglose de reglas de Sigma**:

- `logsource`: Esta sección especifica que la regla está destinada a los sistemas de Windows (`product: windows`) y se centra solo en`Security`registros de eventos (`service: security`).

Código:yaml

```yaml
logsource:
    product: windows
    service: security

```

- `detection`: `selection2`es esencialmente el filtro. Está buscando registros con eventid`4776` (`EventID: 4776`) independientemente del`TargetUserName`o`Workstation`valores (`TargetUserName: '*'`, `Workstation: '*'`). `condition`Cuenta instancias de`TargetUserName`agrupado por`Workstation`y verifica si una estación de trabajo tiene más de`three`intentos de inicio de sesión fallidos.

---

# **Recursos de desarrollo de reglas de Sigma**

Como puede imaginar, el mejor recurso de desarrollo de reglas de Sigma es la documentación oficial, que se puede encontrar en los siguientes enlaces.

- [https://github.com/sigmahq/sigma/wiki/rule-creation-guide](https://github.com/SigmaHQ/sigma/wiki/Rule-Creation-Guide)
- [https://github.com/sigmahq/sigma-specification](https://github.com/SigmaHQ/sigma-specification)

La siguiente serie de artículos es el siguiente mejor recurso sobre el desarrollo de reglas de Sigma.

- [https://tech-en.netlify.app/articles/en510480/](https://tech-en.netlify.app/articles/en510480/)
- [https://tech-en.netlify.app/articles/en513032/](https://tech-en.netlify.app/articles/en513032/)
- [https://tech-en.netlify.app/articles/en515532/](https://tech-en.netlify.app/articles/en515532/)

---

En la próxima sección, exploraremos cómo usar las reglas de Sigma para el análisis a gran escala de registros de eventos con el objetivo de identificar rápidamente las amenazas.