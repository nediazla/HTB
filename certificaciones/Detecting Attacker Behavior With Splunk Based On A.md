# Detecting Attacker Behavior With Splunk Based On Analytics

Como se mencionó anteriormente, el segundo enfoque se inclina fuertemente en el análisis estadístico y la detección de anomalías para identificar un comportamiento anormal. Mediante perfilar `normal` Comportamiento e identificación de desviaciones de esta línea de base, podemos descubrir actividades sospechosas que pueden significar una intrusión. Estos modelos de detección estadística, aunque impulsados ​​por datos, están formados invariablemente por la comprensión más amplia de las técnicas, tácticas y procedimientos de atacantes (TTP).

Un buen ejemplo de este enfoque en Splunk es el uso del `streamstats` dominio. Este comando nos permite realizar análisis en tiempo real en los datos, lo que puede ser útil para identificar patrones o tendencias inusuales.

Considere un escenario en el que estamos monitoreando el número de conexiones de red iniciadas por un proceso dentro de un cierto marco de tiempo.

Detección del comportamiento del atacante con Splunk basado en análisis

```
index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 | bin _time span=1h | stats count as NetworkConnections by _time, Image | streamstats time_window=24h avg(NetworkConnections) as avg stdev(NetworkConnections) as stdev by Image | eval isOutlier=if(NetworkConnections > (avg + (0.5*stdev)), 1, 0) | search isOutlier=1

```

En esta búsqueda:

- Comenzamos enfocándonos en eventos de conexión de red (`EventCode=3`), y luego agrupe estos eventos en intervalos por hora (`bin` puede verse como un`bucket`alias). Para cada imagen de proceso única (`Image`), Calculamos el número de eventos de conexión de red por cubo de tiempo.
- Luego usamos el `streamstats` Comando para calcular un promedio continuo y una desviación estándar del número de conexiones de red durante un período de 24 horas para cada imagen de proceso única. Esto nos da una línea de base dinámica para comparar cada punto de datos.
- El `eval` El comando se usa para crear un nuevo campo, `isOutlier`, y le asigna un valor de `1` Para cualquier evento en el que el número de conexiones de red esté a más de 0.5 desviaciones estándar del promedio. Esto etiqueta estos eventos como estadísticamente anómalos y potencialmente indicativos de actividad sospechosa.
- Por último, el `search` El comando filtra nuestros resultados para incluir solo los valores atípicos, es decir, los eventos donde `isOutlier` iguales `1`.

Al monitorear las anomalías en las conexiones de red iniciadas por los procesos, podemos detectar actividades potencialmente maliciosas, como los intentos de comunicación de comando y control o exfiltración de datos. Sin embargo, como con cualquier método de detección de anomalías, es importante recordar que puede producir falsos positivos y debe calibrarse de acuerdo con los detalles de su entorno.

![](https://academy.hackthebox.com/storage/modules/218/159.png)

Tras un examen más detallado de los resultados, observamos la presencia de numerosos procesos sospechosos que se identificaron previamente, aunque no todos son evidentes.

# **Crafting SPL Búsquedas basadas en análisis**

A continuación hay algunos ejemplos de detección más que siguen este enfoque.

1. **Ejemplo: detección de comandos anormalmente largos**
    
    Los atacantes con frecuencia emplean comandos excesivamente largos como parte de sus operaciones para lograr sus objetivos.
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" Image=*cmd.exe | eval len=len(CommandLine) | table User, len, CommandLine | sort - len
    
    ```
    
    Después de revisar los resultados, notamos una actividad benigna que se puede filtrar para reducir el ruido. Aplicemos las siguientes modificaciones a la búsqueda.
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" Image=*cmd.exe ParentImage!="*msiexec.exe" ParentImage!="*explorer.exe" | eval len=len(CommandLine) | table User, len, CommandLine | sort - len
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/160.png)
    
    Una vez más, observamos la recurrencia de la actividad maliciosa que identificamos anteriormente durante nuestra investigación.
    
2. **Ejemplo: Detección de actividad anormal de CMD.exe**
    
    La siguiente búsqueda identifica inusual `cmd.exe` actividad dentro de un cierto rango de tiempo. Usa el `bucket` Comando para agrupar eventos por hora, calcula el `count`, `average`, y `standard deviation` de `cmd.exe` ejecuciones y indicadores de banderas.
    
    ```
    index="main" EventCode=1 (CommandLine="*cmd.exe*") | bucket _time span=1h | stats count as cmdCount by _time User CommandLine | eventstats avg(cmdCount) as avg stdev(cmdCount) as stdev | eval isOutlier=if(cmdCount > avg+1.5*stdev, 1, 0) | search isOutlier=1
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/161.png)
    
    Tras un examen más detallado de los resultados, observamos la presencia de comandos sospechosos que se identificaron previamente, aunque no todos son evidentes.
    
3. **Ejemplo: Detección de procesos que cargan un gran número de DLL en un tiempo específico**
    
    No es raro que el malware cargue múltiples DLL en rápida sucesión. El siguiente SPL puede ayudar a monitorear este comportamiento.
    
    ```
    index="main" EventCode=7 | bucket _time span=1h | stats dc(ImageLoaded) as unique_dlls_loaded by _time, Image | where unique_dlls_loaded > 3 | stats count by Image, unique_dlls_loaded
    
    ```
    
    Después de revisar los resultados, notamos una actividad benigna que se puede filtrar para reducir el ruido. Aplicemos las siguientes modificaciones a la búsqueda.
    
    ```
    index="main" EventCode=7 NOT (Image="C:\\Windows\\System32*") NOT (Image="C:\\Program Files (x86)*") NOT (Image="C:\\Program Files*") NOT (Image="C:\\ProgramData*") NOT (Image="C:\\Users\\waldo\\AppData*")| bucket _time span=1h | stats dc(ImageLoaded) as unique_dlls_loaded by _time, Image | where unique_dlls_loaded > 3 | stats count by Image, unique_dlls_loaded | sort - unique_dlls_loaded
    
    ```
    
    - `index="main" EventCode=7 NOT (Image="C:\\Windows\\System32*") NOT (Image="C:\\Program Files (x86)*") NOT (Image="C:\\Program Files*") NOT (Image="C:\\ProgramData*") NOT (Image="C:\\Users\\waldo\\AppData*")`: Esta parte de la consulta es responsable de obtener todos los eventos del`main`índice donde `EventCode` es `7` (Eventos cargados de imagen en registros de Sysmon). El `NOT` Los filtros están excluyendo eventos de rutas benignas conocidas (como "Windows \ System32", "Archivos de programa", "ProgramData" y el directorio "AppData" de un usuario específico).
    - `| bucket _time span=1h`: Este comando se utiliza para agrupar los eventos en cubos de tiempo de una hora de duración. Esto se utiliza para analizar los datos en intervalos por hora.
    - `| stats dc(ImageLoaded) as unique_dlls_loaded by _time, Image`: El `stats` El comando se utiliza para realizar operaciones estadísticas en los eventos. Aquí, `dc(ImageLoaded)` calcula el recuento distinto de DLL cargados (`ImageLoaded`) para cada imagen de proceso (`Image`) en cada cubo de tiempo de una hora.
    - `| where unique_dlls_loaded > 3`: Este filtro excluye los resultados donde es el número de DLL únicas cargadas por un proceso dentro de una hora `3 or less`. Esto se basa en la suposición de que el software legítimo generalmente carga DLL a una velocidad moderada, mientras que el malware podría cargar rápidamente muchas DLL diferentes.
    - `| stats count by Image, unique_dlls_loaded`: Este comando calcula el número de veces cada proceso (`Image`) se ha cargado `more than 3 unique DLLs` en una hora.
    - `| sort - unique_dlls_loaded`: Finalmente, este comando clasifica los resultados en orden descendente en función del número de DLL únicos cargados (`unique_dlls_loaded`).
    
    ![](https://academy.hackthebox.com/storage/modules/218/162.png)
    
    Tras un examen más detallado de los resultados, observamos la presencia de procesos sospechosos que se identificaron previamente, aunque no todos son evidentes.
    
    Es importante tener en cuenta que este comportamiento también puede ser exhibido por un software legítimo en numerosos casos, por lo que serían necesarios el contexto y la investigación adicional para confirmar la actividad maliciosa.
    
4. **Ejemplo: detección de transacciones donde se ha creado el mismo proceso más de una vez en la misma computadora**
    
    Queremos correlacionar eventos donde el mismo proceso (`Image`) se ejecuta en la misma computadora (`ComputerName`) Dado que esto podría indicar anormalidades dependiendo de la naturaleza de los procesos involucrados. Como siempre, el contexto y la investigación adicional serían necesarias para confirmar si es realmente malicioso o simplemente un hecho benigno. El siguiente SPL puede ayudar a monitorear este comportamiento.
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | transaction ComputerName, Image | where mvcount(ProcessGuid) > 1 | stats count by Image, ParentImage
    
    ```
    
    - `index="main" sourcetype="WinEventLog:Sysmon" EventCode=1`: Esta parte de la consulta obtiene todos los eventos de creación de procesos de Sysmon (`EventCode=1`) de la `main` índice. Sysmon Event Code 1 representa un evento de creación de procesos, que incluye detalles como el proceso que se inició, sus argumentos de línea de comandos, el usuario que lo inició y el proceso del que se inició.
    - `| transaction ComputerName, Image`: El comando de transacción se utiliza para agrupar eventos relacionados en función de los valores de campo compartidos. En este caso, los eventos se agrupan si comparten lo mismo`ComputerName`y `Image` valores. Esto puede ayudar a unir todos los eventos de creación de procesos asociados con un programa específico en una computadora específica.
    - `| where mvcount(ProcessGuid) > 1`: Este comando filtra los resultados para incluir solo transacciones en las que más de un proceso único GUID (`ProcessGuid`) está asociado con la misma imagen del programa (`Image`) en la misma computadora (`ComputerName`). Esto normalmente representaría casos en los que se inició el mismo programa más de una vez.
    - `| stats count by Image, ParentImage`: Finalmente, este comando de estadísticas se usa para contar el número de tales instancias por la imagen del programa (`Image`) y su imagen de proceso principal (`ParentImage`).
    
    ![](https://academy.hackthebox.com/storage/modules/218/163.png)
    
    Vamos a profundizar en la relación entre `rundll32.exe` y `svchost.exe` (Dado que este par tiene el más alto `count` número).
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1  | transaction ComputerName, Image  | where mvcount(ProcessGuid) > 1 | search Image="C:\\Windows\\System32\\rundll32.exe" ParentImage="C:\\Windows\\System32\\svchost.exe" | table CommandLine, ParentCommandLine
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/218/164.png)
    
    Después de un escrutinio cuidadoso de los resultados, se hace evidente que no solo identificamos la presencia de comandos sospechosos previamente identificados sino también nuevos.
    

---

Al establecer un perfil de comportamiento "normal" y utilizar un modelo estadístico para identificar desviaciones de una línea de base, podríamos haber detectado el compromiso de nuestro entorno más rápidamente, especialmente con una comprensión profunda de las tácticas, técnicas y procedimientos (TTP) de atacantes. Sin embargo, es importante reconocer que confiar únicamente en este enfoque al elaborar consultas es inadecuado.