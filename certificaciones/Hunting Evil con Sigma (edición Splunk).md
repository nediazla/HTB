# Hunting Evil con Sigma (edición Splunk)

Como se discutió al introducir Sigma, las reglas de Sigma revolucionan nuestro enfoque para el análisis de registros y la detección de amenazas. Lo que estamos tratando aquí es una especie de piedra de Rosetta para los sistemas SIEM. Sigma es como un traductor universal que trae un nivel de abstracción a los registros de eventos, eliminando el elemento doloroso de los idiomas de consulta específicos de SIEM.

Validemos esta afirmación convirtiendo dos reglas Sigma en sus formatos SPL correspondientes y examinando los resultados.

### **Ejemplo 1: Caza de abuso de funciones minides para volcar la memoria de LSASS (comsvcs.dll a través de Rundll32)**

Una regla de Sigma nombrada`proc_access_win_lsass_dump_comsvcs_dll.yml`se puede encontrar dentro del`C:\Tools\chainsaw\sigma\rules\windows\process_access`directorio de la`previous`objetivo de la sección.

Esta regla de Sigma detecta a los adversarios que aprovechan el`MiniDump`Función de exportación de`comsvcs.dll`a través de`rundll32`Para realizar un volcado de memoria de LSASS.

Podemos traducir esta regla en una búsqueda Splunk con`sigmac`(disponible en`C:\Tools\sigma-0.21\tools`) como sigue.

Hunting Evil con Sigma (edición Splunk)

```powershell
PS C:\Tools\sigma-0.21\tools> python sigmac -t splunk C:\Tools\chainsaw\sigma\rules\windows\process_access\proc_access_win_lsass_dump_comsvcs_dll.yml -c .\config\splunk-windows.yml
(TargetImage="*\\lsass.exe" SourceImage="C:\\Windows\\System32\\rundll32.exe" CallTrace="*comsvcs.dll*")

```

Vamos a navegar ahora hasta la parte inferior de esta sección y hacer clic en`Click here to spawn the target system!`. Entonces, navegemos a`http://[Target IP]:8000`, abra la solicitud de "búsqueda e informes" y envíe la búsqueda de Splunk`sigmac`nos proporcionó.

![](https://academy.hackthebox.com/storage/modules/234/splunk_1.png)

La búsqueda de Splunk proporcionada por`sigmac`De hecho, pudo detectar el abuso de funciones minides para descargar la memoria de LSASS.

---

### **Ejemplo 2: caza de procesos de niños sospechosos de desove del bloc de notas**

Una regla de Sigma nombrada`proc_creation_win_notepad_susp_child.yml`se puede encontrar dentro del`C:\Rules\sigma`directorio de la`previous`objetivo de la sección.

Esta regla de Sigma detecta`notepad.exe`generando un proceso de niño sospechoso.

Podemos traducir esta regla en una búsqueda Splunk con`sigmac`(disponible en`C:\Tools\sigma-0.21\tools`) como sigue.

Hunting Evil con Sigma (edición Splunk)

```powershell
PS C:\Tools\sigma-0.21\tools> python sigmac -t splunk C:\Rules\sigma\proc_creation_win_notepad_susp_child.yml -c .\config\splunk-windows.yml
(ParentImage="*\\notepad.exe" (Image="*\\powershell.exe" OR Image="*\\pwsh.exe" OR Image="*\\cmd.exe" OR Image="*\\mshta.exe" OR Image="*\\cscript.exe" OR Image="*\\wscript.exe" OR Image="*\\taskkill.exe" OR Image="*\\regsvr32.exe" OR Image="*\\rundll32.exe" OR Image="*\\calc.exe"))

```

Vamos a navegar ahora hasta la parte inferior de esta sección y hacer clic en`Click here to spawn the target system!`, si aún no lo hemos hecho. Entonces, navegemos a`http://[Target IP]:8000`, abra la solicitud de "búsqueda e informes" y envíe la búsqueda de Splunk`sigmac`nos proporcionó.

![](https://academy.hackthebox.com/storage/modules/234/splunk_2.png)

La búsqueda de Splunk proporcionada por`sigmac`De hecho, fue capaz de detectar`notepad.exe`desove procesos sospechosos (como PowerShell).

---

Tenga en cuenta que con más frecuencia que no tendrá que manipular con los archivos de configuración de Sigma (disponibles dentro del`C:\Tools\sigma-0.21\tools\config`Directorio del objetivo de la sección anterior) para que las consultas SIEM sean fácilmente utilizables.