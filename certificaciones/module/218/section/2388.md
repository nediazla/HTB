# Detecting Attacker Behavior With Splunk Based On TTPs

En el mundo de la ciberseguridad en constante evolución, la detección competente de amenazas es crucial. Esto requiere una comprensión profunda de las innumerables tácticas, técnicas y procedimientos (TTP) utilizados por adversarios potenciales, junto con un conocimiento profundo de nuestros propios sistemas de red y sus comportamientos típicos. La detección eficaz de amenazas a menudo gira en torno a la identificación de patrones que coinciden con comportamientos maliciosos conocidos o que divergen significativamente de las normas esperadas.

Al elaborar búsquedas SPL (lenguaje de procesamiento de búsqueda) relacionadas con la detección en Splunk, utilizamos dos enfoques principales:

- El primer enfoque se basa en TTP de adversarios conocidos, aprovechando nuestro amplio conocimiento de amenazas y vectores de ataque específicos. Esta estrategia es similar a jugar un juego de `spot the known`. Si una entidad se comporta de una manera que reconocemos como característica de una amenaza particular, llama nuestra atención.
- El segundo enfoque, `while still informed by an understanding of attacker TTPs`, se apoya en gran medida en el análisis estadístico y la detección de anomalías para identificar comportamientos anormales dentro del mar de actividad normal. Esta estrategia es más bien un juego de `spot the unusual`. En este caso, no nos basamos únicamente en el conocimiento preexistente de amenazas específicas. En cambio, hacemos un uso extensivo de técnicas matemáticas y estadísticas para resaltar anomalías, trabajando bajo la premisa de que la actividad maliciosa a menudo se manifestará como una aberración de la norma.

En conjunto, estos enfoques nos brindan un conjunto de herramientas integral para identificar y responder a un amplio espectro de amenazas a la ciberseguridad. Cada metodología ofrece ventajas únicas y, cuando se utilizan en conjunto, crean un mecanismo de detección sólido, capaz de identificar amenazas conocidas y al mismo tiempo revelar riesgos potenciales desconocidos.

Además, en ambos enfoques, `the key is to understand our data and environment, then carefully tune our queries and thresholds to balance the need for accurate detection with the desire to avoid false positives`. A través de la revisión y revisión continua de nuestras consultas de SPL, podemos mantener un alto nivel de preparación y postura de seguridad.

Ahora, profundicemos en estos dos enfoques.

Tenga en cuenta que las siguientes secciones no pertenecen a la ingeniería de detección. El énfasis en estas secciones está en comprender los dos enfoques distintos para construir búsquedas, en lugar del proceso real de analizar un ataque, identificar fuentes de registros relevantes y formular búsquedas. Además, las búsquedas proporcionadas no están ajustadas con precisión. Como se mencionó anteriormente, el ajuste requiere una comprensión profunda del medio ambiente y su actividad normal.

---

# **Elaboración de búsquedas de SPL basadas en TTP conocidos**

Como se mencionó anteriormente, el primer enfoque gira en torno a una comprensión integral del comportamiento de los atacantes conocidos y de los TTP. Con esta estrategia, nuestro enfoque está en reconocer patrones que hemos visto antes, que son indicativos de amenazas o vectores de ataque específicos.

A continuación se muestran algunos ejemplos de detección que siguen este enfoque.

1. **Ejemplo: Detección de actividades de reconocimiento aprovechando los archivos binarios nativos de Windows**
    
    Los atacantes a menudo aprovechan los archivos binarios nativos de Windows (como `net.exe`) para obtener información sobre el entorno objetivo, identificar posibles oportunidades de escalada de privilegios y realizar movimientos laterales. `Sysmon Event ID 1` puede ayudar a identificar dicho comportamiento.
    
    ```powershell
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 Image=*\\ipconfig.exe OR Image=*\\net.exe OR Image=*\\whoami.exe OR Image=*\\netstat.exe OR Image=*\\nbtstat.exe OR Image=*\\hostname.exe OR Image=*\\tasklist.exe | stats count by Image,CommandLine | sort - count
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/156.png)
    
    Dentro de los resultados de la búsqueda, surgen indicios claros, destacando la utilización de binarios nativos de Windows con fines de reconocimiento.
    
2. **Ejemplo: Detección de solicitud de cargas útiles/herramientas maliciosas alojadas en dominios acreditados/incluidos en la lista blanca (como githubusercontent.com)**
    
    Los atacantes frecuentemente aprovechan el uso de `githubusercontent.com` como plataforma de alojamiento para sus cargas útiles. Esto se debe a la lista blanca común y a la permisibilidad del dominio por parte de los servidores proxy de la empresa. `Sysmon Event ID 22` puede ayudar a identificar dicho comportamiento.
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=22  QueryName="*github*" | stats count by Image, QueryName
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/141.png)
    
    Dentro de los resultados de la búsqueda surgen indicios claros, destacando la utilización de `githubusercontent.com` para fines de alojamiento de herramientas/carga útil.
    
3. **Ejemplo: detección de uso de PsExec**
    
    [PsExec](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec), una parte del [Componentes internos del sistema Windows](https://learn.microsoft.com/en-us/sysinternals/) suite, se concibió inicialmente como una utilidad para ayudar a los administradores de sistemas a administrar sistemas Windows remotos. Ofrece la comodidad de conectarse e interactuar con sistemas remotos a través de una interfaz de línea de comandos y está disponible para los miembros del grupo de administradores locales de una computadora.
    
    Las mismas características que hacen de PsExec una poderosa herramienta para administradores de sistemas también la convierten en una opción atractiva para actores malintencionados. Varias técnicas MITRE ATT&CK, incluidas `T1569.002 (System Services: Service Execution)`, `T1021.002 (Remote Services: SMB/Windows Admin Shares)`, y `T1570 (Lateral Tool Transfer)`, he visto PsExec en juego.
    
    A pesar de su fachada simple, PsExec tiene un gran impacto. Funciona copiando un ejecutable de servicio en el recurso compartido Admin$ oculto. Posteriormente, accede a la API del Administrador de control de servicios de Windows para iniciar el servicio. El servicio utiliza canalizaciones con nombre para vincularse a la herramienta PsExec. Lo más destacado es que PsExec se puede implementar tanto en máquinas locales como remotas, y puede permitir a un usuario actuar bajo la cuenta NT AUTHORITY\SYSTEM. estudiando [https://www.synacktiv.com/publications/traces-of-windows-remote-command-execution](https://www.synacktiv.com/publications/traces-of-windows-remote-command-execution) y [https://hurricanelabs.com/splunk-tutorials/splunking-with-sysmon-part-3-detecting-psexec-in-your-environment/](https://hurricanelabs.com/splunk-tutorials/splunking-with-sysmon-part-3-detecting-psexec-in-your-environment/) deducimos que `Sysmon Event ID 13`, `Sysmon Event ID 11`, y `Sysmon Event ID 17` o `Sysmon Event ID 18` puede ayudar a identificar el uso de PsExec.
    
    **Caso 1: Aprovechamiento del ID de evento 13 de Sysmon**
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=13 Image="C:\\Windows\\system32\\services.exe" TargetObject="HKLM\\System\\CurrentControlSet\\Services\\*\\ImagePath" | rex field=Details "(?<reg_file_name>[^\\\]+)$" | eval reg_file_name = lower(reg_file_name), file_name = if(isnull(file_name),reg_file_name,lower(file_name)) | stats values(Image) AS Image, values(Details) AS RegistryDetails, values(_time) AS EventTimes, count by file_name, ComputerName
    
    ```
    
    Analicemos cada parte de esta consulta:
    
    - `index="main" sourcetype="WinEventLog:Sysmon" EventCode=13 Image="C:\\Windows\\system32\\services.exe" TargetObject="HKLM\\System\\CurrentControlSet\\Services\\*\\ImagePath"`: Esta parte de la consulta consiste en seleccionar registros del `main` índice con el tipo de fuente de `WinEventLog:Sysmon`. Estamos buscando específicamente eventos con `EventCode=13`. En los registros de Sysmon, `EventCode 13` representa un evento en el que se estableció un valor de registro. El `Image` El campo está configurado en `C:\\Windows\\system32\\services.exe` para filtrar eventos en los que estuvo involucrado el proceso services.exe, que es el proceso de Windows responsable de manejar la creación y administración de servicios. El `TargetObject` El campo especifica las claves de registro que nos interesan. En este caso, estamos buscando cambios en el `ImagePath` valor bajo cualquier clave de servicio en `HKLM\\System\\CurrentControlSet\\Services`. El `ImagePath` El valor de registro de un servicio especifica la ruta al archivo ejecutable del servicio.
    - `| rex field=Details "(?<reg_file_name>[^\\\]+)$"`: El `rex` El comando aquí extrae el nombre del archivo del `Details` campo usando una expresión regular. el patrón `[^\\\]+)$` captura la parte de la ruta después de la última barra invertida, que suele ser el nombre del archivo. Este valor se almacena en un nuevo campo. `reg_file_name`.
    - `| eval file_name = if(isnull(file_name),reg_file_name,(file_name))`: Este comando de evaluación comprueba si el `file_name` el campo es `null`. Si es así, se establece `file_name` al valor de `reg_file_name` (el nombre del archivo que extrajimos del `Details` campo). Si `file_name` no es nulo, sigue siendo el mismo.
    - `| stats values(Image), values(Details), values(TargetObject), values(_time), values(EventCode), count by file_name, ComputerName`: Finalmente, el `stats` El comando agrega los datos por `file_name` y `ComputerName`. Para cada combinación única de `file_name` y `ComputerName`, recopila todos los valores únicos de `Image`, `Details`, `TargetObject`, y `_time`y cuenta el número de eventos.
    
    En resumen, esta consulta busca casos en los que el`services.exe`El proceso ha modificado el `ImagePath` valor de cualquier servicio. El resultado incluirá los detalles de estas modificaciones, incluido el nombre del servicio modificado, el nuevo valor de ImagePath y la hora de la modificación.
    
    ![](https://academy.hackthebox.com/storage/modules/218/142.png)
    
    entre los `less frequent` En los resultados de búsqueda, es evidente que hay indicios de ejecución similar a PsExec.
    
    **Caso 2: Aprovechamiento del ID de evento 11 de Sysmon**
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image=System | stats count by TargetFilename
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/143.png)
    
    Nuevamente, entre los `less frequent` En los resultados de búsqueda, es evidente que hay indicios de ejecución similar a PsExec.
    
    **Caso 3: Aprovechamiento del ID de evento 18 de Sysmon**
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=18 Image=System | stats count by PipeName
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/144.png)
    
    Esta vez, los resultados son más manejables de revisar y continúan sugiriendo un patrón de ejecución parecido a PsExec.
    
4. **Ejemplo: Detección de utilización de archivos comprimidos para transferir herramientas o filtración de datos**
    
    Los atacantes pueden emplear `zip`, `rar`, o `7z` archivos para transferir herramientas a un host comprometido o extraer datos del mismo. La siguiente búsqueda examina la creación de `zip`, `rar`, o `7z` archivos, con resultados ordenados en orden descendente según el recuento.
    
    ```
    index="main" EventCode=11 (TargetFilename="*.zip" OR TargetFilename="*.rar" OR TargetFilename="*.7z") | stats count by ComputerName, User, TargetFilename | sort - count
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/145.png)
    
    Dentro de los resultados de la búsqueda, surgen indicaciones claras, destacando el uso de archivos de almacenamiento para fines de transferencia de herramientas y/o exfiltración de datos.
    
5. **Ejemplo: Detección del uso de PowerShell o MS Edge para descargar cargas útiles/herramientas**
    
    Los atacantes pueden aprovechar PowerShell para descargar cargas útiles y herramientas adicionales, o engañar a los usuarios para que descarguen malware a través de navegadores web. Las siguientes búsquedas de SPL examinan archivos descargados a través de PowerShell o MS Edge.
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image="*powershell.exe*" |  stats count by Image, TargetFilename |  sort + count
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/155.png)
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=11 Image="*msedge.exe" TargetFilename=*"Zone.Identifier" |  stats count by TargetFilename |  sort + count
    
    ```
    
    El `*Zone.Identifier` es indicativo de un archivo descargado de Internet u otra fuente potencialmente no confiable. Windows usa este identificador de zona para rastrear las zonas de seguridad de un archivo. El `Zone.Identifier` es un ADS (Alternate Data Stream) que contiene metadatos sobre dónde se descargó el archivo y su configuración de seguridad.
    
    ![](https://academy.hackthebox.com/storage/modules/218/147.png)
    
    En ambos resultados de búsqueda, surgen indicaciones claras que destacan el uso de PowerShell y MS Edge para fines de descarga de herramientas/carga útil.
    
6. **Ejemplo: Detección de ejecución en lugares atípicos o sospechosos**
    
    La siguiente búsqueda de SPL está diseñada para identificar cualquier creación de proceso (`EventCode=1`) que ocurre en el usuario `Downloads` carpeta.
    
    ```
    index="main" EventCode=1 | regex Image="C:\\\\Users\\\\.*\\\\Downloads\\\\.*" |  stats count by Image
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/148.png)
    
    dentro del `less frequent` resultados de búsqueda, surgen indicaciones claras, destacando la ejecución de un usuario `Downloads` carpeta.
    
7. **Ejemplo: Detección de archivos ejecutables o DLL creados fuera del directorio de Windows**
    
    El siguiente SPL identifica una posible actividad de malware al verificar la creación de archivos ejecutables y DLL fuera del directorio de Windows. Luego agrupa y cuenta estas actividades por usuario y nombre de archivo de destino.
    
    ```
    index="main" EventCode=11 (TargetFilename="*.exe" OR TargetFilename="*.dll") TargetFilename!="*\\windows\\*" | stats count by User, TargetFilename | sort + count
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/149.png)
    
    dentro del `less frequent` En los resultados de la búsqueda surgen indicaciones claras, destacando la creación de ejecutables fuera del directorio de Windows.
    
8. **Ejemplo: Detección de binarios legítimos con errores ortográficos**
    
    Los atacantes a menudo disfrazan sus archivos binarios maliciosos escribiendo mal intencionadamente los legítimos para pasar desapercibidos y evitar ser detectados. El propósito de la siguiente búsqueda de SPL es detectar posibles errores ortográficos en los términos legítimos. `PSEXESVC.exe` binario, comúnmente utilizado por `PsExec`. Al examinar el `Image`, `ParentImage`, `CommandLine` y `ParentCommandLine` campos, la búsqueda tiene como objetivo identificar casos donde las variaciones de `psexe` se utilizan, lo que potencialmente indica la presencia de archivos binarios maliciosos que intentan hacerse pasar por el binario legítimo del servicio PsExec.
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 (CommandLine="*psexe*.exe" NOT (CommandLine="*PSEXESVC.exe" OR CommandLine="*PsExec64.exe")) OR (ParentCommandLine="*psexe*.exe" NOT (ParentCommandLine="*PSEXESVC.exe" OR ParentCommandLine="*PsExec64.exe")) OR (ParentImage="*psexe*.exe" NOT (ParentImage="*PSEXESVC.exe" OR ParentImage="*PsExec64.exe")) OR (Image="*psexe*.exe" NOT (Image="*PSEXESVC.exe" OR Image="*PsExec64.exe")) |  table Image, CommandLine, ParentImage, ParentCommandLine
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/150.png)
    
    Dentro de los resultados de la búsqueda surgen claros indicios, destacando la falta de ortografía de `PSEXESVC.exe` con fines de evasión.
    
9. **Ejemplo: Detección de uso de puertos no estándar para comunicaciones/transferencias**
    
    Los atacantes suelen utilizar puertos no estándar durante sus operaciones. La siguiente búsqueda de SPL detecta conexiones de red sospechosas a puertos no estándar al excluir los puertos web y de transferencia de archivos estándar (80, 443, 22, 21). El `stats` El comando agrega estas conexiones y se ordenan en orden descendente por`count`.
    
    ```
    index="main" EventCode=3 NOT (DestinationPort=80 OR DestinationPort=443 OR DestinationPort=22 OR DestinationPort=21) | stats count by SourceIp, DestinationIp, DestinationPort | sort - count
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/151.png)
    
    Dentro de los resultados de la búsqueda, surgen indicios claros, destacando el uso de puertos de comunicación no estándar o con fines de transferencia de herramientas.
    

---

A estas alturas debería ser evidente que con una comprensión integral de las tácticas, técnicas y procedimientos (TTP) de los atacantes, podríamos haber detectado el compromiso de nuestro entorno más rápidamente. Sin embargo, es esencial tener en cuenta que elaborar búsquedas basadas únicamente en los TTP de los atacantes es insuficiente, ya que los adversarios evolucionan continuamente y emplean TTP oscuros o desconocidos para evitar la detección.