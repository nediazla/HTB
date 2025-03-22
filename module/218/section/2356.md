# Introduction To Splunk & SPL

# **¿Qué es Splunk?**

Splunk es una solución de software de análisis de datos robusta, versátil y altamente escalable conocida por su capacidad para ingerir, indexar, analizar y visualizar cantidades masivas de datos de máquinas. Splunk tiene la capacidad de impulsar una amplia gama de iniciativas, que abarcan ciberseguridad, cumplimiento, canales de datos, monitoreo de TI, observabilidad, así como gestión empresarial y de TI general.

![](https://academy.hackthebox.com/storage/modules/218/101.png)

![](https://academy.hackthebox.com/storage/modules/218/102.png)

Splunk's (Empresa Splunk) `architecture` consta de varias capas que trabajan juntas para recopilar, indexar, buscar, analizar y visualizar datos. La arquitectura se puede dividir en los siguientes componentes principales:

- `Forwarders`: Los transportistas son responsables de la recopilación de datos. Recopilan datos de la máquina de diversas fuentes y los envían a los indexadores. Los tipos de reenviadores utilizados en Splunk son:
    - `Universal Forwarder (UF)`: Este es un agente liviano que recopila datos y los reenvía a los indexadores de Splunk sin ningún procesamiento previo. Los Universal Forwarders son paquetes de software individuales que se pueden instalar fácilmente en fuentes remotas sin afectar significativamente el rendimiento de la red o del host.
    - `Heavy Forwarder (HF)`: Este agente sirve para recopilar datos de fuentes remotas, especialmente para tareas intensivas de agregación de datos que involucran fuentes como firewalls o puntos de enrutamiento/filtrado de datos. De acuerdo a[esplexicón](https://docs.splunk.com/Splexicon:Heavyforwarder), los reenviadores pesados ​​se destacan de otros tipos de reenviadores porque analizan los datos antes de reenviarlos, lo que les permite enrutar datos según criterios específicos, como el origen o el tipo de evento. También pueden indexar datos localmente y al mismo tiempo reenviarlos a otro indexador. Normalmente, los Heavy Forwarders se implementan como "nodos de recopilación de datos" dedicados para el acceso a datos API/script y son compatibles exclusivamente con Splunk Enterprise.
    - Tenga en cuenta que hay `HTTP Event Collectors (HECs)` disponible para recopilar datos directamente de las aplicaciones de forma escalable. Los HEC funcionan mediante el uso de métodos API sin formato o JSON basados ​​en tokens. En este proceso, los datos se envían directamente al nivel del indexador para su posterior procesamiento.
- `Indexers`: Los indexadores reciben datos de los reenviadores, los organizan y los almacenan en índices. Mientras indexan datos, los indexadores generan conjuntos de directorios categorizados por antigüedad, donde cada directorio contiene datos sin procesar comprimidos y los índices correspondientes que apuntan a los datos sin procesar. También procesan consultas de búsqueda de los usuarios y devuelven resultados.
- `Search Heads`: Los cabezales de búsqueda coordinan los trabajos de búsqueda, los envían a los indexadores y combinan los resultados. También proporcionan una interfaz para que los usuarios interactúen con Splunk. En cabezales de búsqueda, `Knowledge Objects` se puede diseñar para extraer campos complementarios y manipular datos sin modificar los datos del índice original. Es importante mencionar que Search Heads también ofrece varias herramientas para enriquecer la experiencia de búsqueda, incluidos informes, paneles y visualizaciones.
- `Deployment Server`: Gestiona la configuración de reenviadores, distribuye aplicaciones y actualizaciones.
- `Cluster Master`: El maestro del clúster coordina las actividades de los indexadores en un entorno agrupado, lo que garantiza la replicación de datos y la afinidad de búsqueda.
- `License Master`: Gestiona los detalles de la licencia de la plataforma Splunk.

de spunk `key components` incluir:

- `Splunk Web Interface`: Esta es la interfaz gráfica a través de la cual los usuarios pueden interactuar con Splunk, realizando tareas como búsquedas, creación de alertas, paneles e informes.
- `Search Processing Language (SPL)`: el lenguaje de consulta para Splunk, que permite a los usuarios buscar, filtrar y manipular los datos indexados.
- `Apps and Add-ons`: Las aplicaciones brindan funcionalidades específicas dentro de Splunk, mientras que los complementos amplían las capacidades o se integran con otros sistemas. Las aplicaciones Splunk permiten la coexistencia de múltiples espacios de trabajo en una sola instancia de Splunk, atendiendo a diferentes casos de uso y roles de usuario. Estas aplicaciones listas para usar se pueden encontrar en[Base Splunk](https://splunkbase.splunk.com/), proporcionando funcionalidades adicionales y soluciones preconfiguradas. Los complementos de tecnología Splunk sirven como una capa de abstracción para los métodos de recopilación de datos. A menudo incluyen extracciones de campos relevantes, lo que permite la funcionalidad de esquemas sobre la marcha. Además, los complementos de tecnología abarcan archivos de configuración pertinentes (props/transforms) y scripts o binarios de soporte. Una aplicación Splunk, por otro lado, puede verse como una solución integral que normalmente utiliza uno o más complementos tecnológicos para mejorar sus capacidades.
- `Knowledge Objects`: Estos incluyen campos, etiquetas, tipos de eventos, búsquedas, macros, modelos de datos y alertas que mejoran los datos en Splunk, facilitando su búsqueda y análisis.

---

# **Splunk como solución SIEM**

Cuando se trata de ciberseguridad, Splunk puede desempeñar un papel crucial como solución de gestión de registros, pero su verdadero valor reside en sus capacidades de gestión de eventos e información de seguridad (SIEM) basadas en análisis. Splunk como solución SIEM puede ayudar en el análisis de datos históricos y en tiempo real, el monitoreo de la ciberseguridad, la respuesta a incidentes y la búsqueda de amenazas. Además, permite a las organizaciones mejorar sus capacidades de detección aprovechando el análisis del comportamiento del usuario.

Como se mencionó, Splunk Processing Language (SPL) es un lenguaje que contiene más de cien comandos, funciones, argumentos y cláusulas. Es la columna vertebral del análisis de datos en Splunk y se utiliza para buscar, filtrar, transformar y visualizar datos.

Supongamos que `main` es un índice que contiene registros de seguridad de Windows y Sysmon, entre otros.

1. **Búsqueda básica**
    
    El aspecto más fundamental de SPL es la búsqueda. De forma predeterminada, una búsqueda devuelve todos los eventos, pero se puede limitar con palabras clave, operadores booleanos, operadores de comparación y caracteres comodín. Por ejemplo, una búsqueda de error devolvería todos los eventos que contengan esa palabra.
    
    Operadores booleanos `AND`, `OR`, y `NOT` Se utilizan para consultas más específicas.
    
    El `search` El comando suele estar implícito al inicio de cada consulta SPL y normalmente no se escribe. Sin embargo, aquí hay un ejemplo que utiliza una sintaxis de búsqueda explícita:
    
    ```
    search index="main" "UNKNOWN"
    
    ```
    
    Al especificar el índice como `main`, la consulta limita la búsqueda solo a los eventos almacenados en el `main` índice. El término `UNKNOWN` Luego se utiliza como palabra clave para filtrar y recuperar eventos que incluyen este término específico.
    
    **Nota**: comodines (`*`) puede reemplazar cualquier número de caracteres en búsquedas y valores de campo. Ejemplo (sintaxis de búsqueda implícita):
    
    ```
    index="main" "*UNKNOWN*"
    
    ```
    
    Esta consulta SPL buscará dentro del `main` índice de eventos que contienen el término `UNKNOWN` en cualquier lugar de los datos del evento.
    
2. **Campos y operadores de comparación**
    
    Splunk identifica automáticamente ciertos datos como campos (como `source`, `sourcetype`, `host`, `EventCode`, etc.), y los usuarios pueden definir manualmente campos adicionales. Estos campos se pueden utilizar con operadores de comparación (`=`, `!=`, `<`, `>`, `<=`, `>=`) para búsquedas más precisas. Ejemplo:
    
    ```
    index="main" EventCode!=1
    
    ```
    
    Esta consulta SPL (Splunk Processing Language) se utiliza para buscar dentro del `main` índice de eventos que no `not` tener un `EventCode` valor de `1`.
    
3. **El comando de campos**
    
    El `fields` El comando especifica qué campos deben incluirse o excluirse en los resultados de la búsqueda. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | fields - User
    
    ```
    
    Después de recuperar todos los eventos de creación de procesos del `main` índice, el `fields` El comando excluye el `User` campo de los resultados de la búsqueda. Por lo tanto, los resultados contendrán todos los campos que normalmente se encuentran en el [ID de evento de Sysmon 1](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90001) registros, excepto el usuario que inició el proceso. Tenga en cuenta que utilizar `sourcetype` restringe el alcance exclusivamente a los registros de eventos de Sysmon.
    
4. **El comando de la tabla**
    
    El `table` El comando presenta los resultados de la búsqueda en formato tabular. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | table _time, host, Image
    
    ```
    
    Esta consulta devuelve eventos de creación de procesos y luego organiza los campos seleccionados (_time, host e Image) en un formato tabular. `_time` es la marca de tiempo del evento, `host` es el nombre del anfitrión donde ocurrió el evento, y `Image` es el nombre del archivo ejecutable que representa el proceso.
    
5. **El comando de cambio de nombre**
    
    El `rename` El comando cambia el nombre de un campo en los resultados de la búsqueda. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | rename Image as Process
    
    ```
    
    Este comando cambia el nombre del `Image` campo a `Process` en los resultados de la búsqueda. `Image` El campo en los registros de Sysmon representa el nombre del archivo ejecutable para el proceso. Al cambiarle el nombre, todas las referencias posteriores a `Process` ahora se referiría a lo que originalmente era el`Image`campo.
    
6. **El comando de deduplicación**
    
    El comando 'dedup' elimina eventos duplicados. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | dedup Image
    
    ```
    
    El `dedup` El comando elimina las entradas duplicadas según el `Image` campo de los eventos de creación del proceso. Esto significa que si el mismo proceso (`Image`) se crea varias veces, aparecerá solo una vez en los resultados, eliminando efectivamente la repetición.
    
7. **El comando de clasificación**
    
    El `sort` El comando ordena los resultados de la búsqueda. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | sort - _time
    
    ```
    
    Este comando ordena todos los eventos de creación de procesos en el `main` índice en orden descendente de sus marcas de tiempo (_time), es decir, los eventos más recientes se muestran primero.
    
8. **El comando de estadísticas**
    
    El `stats` El comando realiza operaciones estadísticas. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 | stats count by _time, Image
    
    ```
    
    Esta consulta devolverá una tabla donde cada fila representa una combinación única de una marca de tiempo (`_time`) y un proceso (`Image`). La columna de recuento indica la cantidad de eventos de conexión de red que ocurrieron para ese proceso específico en ese momento específico.
    
    Sin embargo, es un desafío visualizar estos datos a lo largo del tiempo para cada proceso porque los datos de cada proceso están intercalados en la tabla. Necesitaríamos filtrar manualmente por proceso (`Image`) para ver los recuentos a lo largo del tiempo para cada uno.
    
9. **El comando gráfico**
    
    El `chart` El comando crea una visualización de datos basada en operaciones estadísticas. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=3 | chart count by _time, Image
    
    ```
    
    Esta consulta devolverá una tabla donde cada fila representa una marca de tiempo única (`_time`) y cada columna representa un proceso único (`Image`). Los valores de celda indican la cantidad de eventos de conexión de red que ocurrieron para cada proceso en cada momento específico.
    
    Con el `chart` comando, puede visualizar fácilmente los datos a lo largo del tiempo para cada proceso porque cada proceso tiene su propia columna. Puede ver rápidamente de un vistazo el recuento de eventos de conexión de red a lo largo del tiempo para cada proceso.
    
10. **El comando de evaluación**
    
    El `eval` El comando crea o redefine campos. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | eval Process_Path=lower(Image)
    
    ```
    
    Este comando crea un nuevo campo. `Process_Path` que contiene la versión en minúsculas del `Image` campo. No cambia lo real `Image` campo, pero crea un nuevo campo que se puede utilizar en operaciones posteriores o con fines de visualización.
    
11. **El comando rex**
    
    El `rex` El comando extrae nuevos campos de los existentes utilizando expresiones regulares. Ejemplo:
    
    ```
    	index="main" EventCode=4662 | rex max_match=0 "[^%](?<guid>{.*})" | table guid
    
    ```
    
    - `index="main" EventCode=4662`filtra los eventos a aquellos en el `main` índice con el `EventCode` igual a `4662`. Esto limita la búsqueda a eventos específicos con el EventCode especificado.
    - `rex max_match=0 "[^%](?<guid>{.*})"`utiliza el comando rex para extraer valores que coincidan con el patrón de los campos de eventos. El patrón de expresiones regulares`{.*}`busca subcadenas que comiencen con `{` y terminar con `}`. El `[^%]` parte asegura que el partido no comienza con un `%` personaje. El valor capturado entre llaves se asigna al grupo de captura nombrado`guid`.
    - `table guid`muestra los GUID extraídos en la salida. Este comando se utiliza para formatear los resultados y mostrar solo el `guid` campo.
    - El `max_match=0` La opción garantiza que todas las apariciones del patrón se extraigan de cada evento. De forma predeterminada, el comando rex solo extrae la primera aparición.
    
    Esto resulta útil porque los GUID no se extraen automáticamente de los registros de eventos 4662.
    
12. **El comando de búsqueda**
    
    El `lookup` El comando enriquece los datos con fuentes externas. Ejemplo:
    
    Supongamos que el siguiente archivo CSV llamado`malware_lookup.csv`.
    
    ```
    filename, is_malware
    notepad.exe, false
    cmd.exe, false
    powershell.exe, false
    sharphound.exe, true
    randomfile.exe, true
    
    ```
    
    Este archivo CSV debe agregarse como una nueva tabla de búsqueda de la siguiente manera.
    
    ![](https://academy.hackthebox.com/storage/modules/218/107.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/108.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/109.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/110.png)
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | rex field=Image "(?P<filename>[^\\\]+)$" | eval filename=lower(filename) | lookup malware_lookup.csv filename OUTPUTNEW is_malware | table filename, is_malware
    
    ```
    
    - `index="main" sourcetype="WinEventLog:Sysmon" EventCode=1`: Este es el criterio de búsqueda. Está buscando registros de Sysmon (identificados por el tipo de fuente) con un EventCode de 1 (que representa eventos de creación de procesos) en el índice "principal".
    - `| rex field=Image "(?P<filename>[^\\\]+)$"`: Este comando utiliza la expresión regular (regex) para extraer una parte del campo Imagen. El campo Imagen en los registros de Sysmon EventCode=1 normalmente contiene la ruta completa del archivo del proceso. Esta expresión regular dice: Capture todo después de la última barra invertida (que debería ser el nombre del archivo) y guárdelo como nombre de archivo.
    - `| eval filename=lower(filename)`: Este comando toma el nombre del archivo que se acaba de extraer y lo convierte a minúsculas. La función lower() se utiliza para garantizar que la búsqueda no distinga entre mayúsculas y minúsculas.
    - `| lookup malware_lookup.csv filename OUTPUTNEW is_malware`: Este comando realiza una operación de búsqueda utilizando el nombre del archivo como clave. Se espera que la tabla de búsqueda (malware_lookup.csv) contenga una lista de nombres de archivos de ejecutables maliciosos conocidos. Si se encuentra una coincidencia en la tabla de búsqueda, se agrega el nuevo campo is_malware al evento, que indica si el proceso se considera malicioso según la tabla de búsqueda.`<-- filename in this part of the query is the first column title in the CSV`.
    - `| table filename, is_malware`: Este comando formatea la salida para mostrar solo los campos nombre de archivo e is_malware. Si is_malware no está presente en una fila, significa que no se encontró ninguna coincidencia en la tabla de búsqueda para ese nombre de archivo.
    
    En resumen, esta consulta extrae los nombres de archivos de procesos recién creados, los convierte a minúsculas, los compara con una lista de nombres de archivos maliciosos conocidos y presenta los hallazgos en una tabla.
    
    Un equivalente que también elimina duplicados es el siguiente.
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | eval filename=mvdedup(split(Image, "\\")) | eval filename=mvindex(filename, -1) | eval filename=lower(filename) | lookup malware_lookup.csv filename OUTPUTNEW is_malware | table filename, is_malware | dedup filename, is_malware
    
    ```
    
    - `index="main" sourcetype="WinEventLog:Sysmon" EventCode=1`: Este comando es el criterio de búsqueda. Está tirando de la `main` índice donde está el tipo de fuente `WinEventLog:Sysmon` y el `EventCode` es `1`. El sistema`EventCode`de `1` indica un evento de creación de proceso.
    - `| eval filename=mvdedup(split(Image, "\\"))`: Este comando está dividiendo el `Image` campo, que contiene la ruta del archivo, en varios elementos en cada barra invertida y convirtiéndolo en un campo de valores múltiples. El`mvdedup`La función se utiliza para eliminar cualquier duplicado en este campo de valores múltiples.
    - `| eval filename=mvindex(filename, -1)`: Aquí, el `mvindex` La función se utiliza para seleccionar el último elemento del campo multivalor generado en el paso anterior. En el contexto de una ruta de archivo, normalmente sería el nombre del archivo real.
    - `| eval filename=lower(filename)`: Este comando toma el campo de nombre de archivo y lo convierte a minúsculas usando la función inferior. Esto se hace para garantizar que la búsqueda no distinga entre mayúsculas y minúsculas y para estandarizar los datos.
    - `| lookup malware_lookup.csv filename OUTPUTNEW is_malware`: este comando realiza una operación de búsqueda. El `lookup` El comando está tomando el `filename` campo y comprobar si coincide con alguna entrada en el`malware_lookup.csv`tabla de búsqueda. Si hay una coincidencia, agrega un nuevo campo, `is_malware`, al evento, indicando si el proceso está marcado como malicioso.
    - `| table filename, is_malware`: El `table` El comando se utiliza para formatear la salida, en este caso mostrando solo el`filename`y `is_malware` campos en formato tabular.
    - `| dedup filename, is_malware`: este comando elimina cualquier evento duplicado según los campos nombre de archivo e is_malware. En otras palabras, si hay varias entradas idénticas para el`filename`y`is_malware`campos en los resultados de búsqueda, el `dedup` El comando retendrá solo la primera aparición y eliminará todos los duplicados posteriores.
    
    En resumen, esta consulta SPL busca en los registros de Sysmon eventos de creación de procesos, extrae el `file name` desde `Image` campo, lo convierte a minúsculas, lo compara con una lista de malware conocido del `malware_lookup.csv` archivo y luego muestra los resultados en una tabla, eliminando cualquier duplicado según el `filename` y `is_malware` campos.
    
13. **El comando de búsqueda de entrada**
    
    El `inputlookup` El comando recupera datos de un archivo de búsqueda sin unirlos a los resultados de la búsqueda. Ejemplo:
    
    ```
    | inputlookup malware_lookup.csv
    
    ```
    
    Este comando recupera todos los registros del `malware_lookup.csv` archivo. El resultado no se combina con ningún resultado de búsqueda, pero se puede utilizar para verificar el contenido del archivo de búsqueda o para operaciones posteriores, como filtrar o combinar con otros conjuntos de datos.
    
14. **Rango de tiempo**
    
    Cada evento en Splunk tiene una marca de tiempo. Usando el selector de rango de tiempo o el `earliest` y `latest` comandos, puede limitar las búsquedas a períodos de tiempo específicos. Ejemplo:
    
    ```
    index="main" earliest=-7d EventCode!=1
    
    ```
    
    Al combinar el `index="main"` condición con `earliest=-7d` y `EventCode!=1`, la consulta recuperará eventos del `main` índice que ocurrió en los últimos siete días y no tiene un `EventCode` valor de`1`.
    
15. **El comando de transacción**
    
    El `transaction` El comando se usa en Splunk para agrupar eventos que comparten características comunes en transacciones, a menudo se usa para rastrear sesiones o actividades de usuarios que abarcan múltiples eventos. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" (EventCode=1 OR EventCode=3) | transaction Image startswith=eval(EventCode=1) endswith=eval(EventCode=3) maxspan=1m | table Image |  dedup Image
    
    ```
    
    - `index="main" sourcetype="WinEventLog:Sysmon" (EventCode=1 OR EventCode=3)`: Este es el criterio de búsqueda. Está tirando del `main` índice donde está el tipo de fuente `WinEventLog:Sysmon` y el `EventCode` es cualquiera `1` o `3`. En los registros de Sysmon,`EventCode 1`se refiere a un evento de creación de proceso, y `EventCode 3` se refiere a un evento de conexión de red.
    - `| transaction Image startswith=eval(EventCode=1) endswith=eval(EventCode=3) maxspan=1m`: El comando de transacción se utiliza aquí para agrupar eventos según el campo Imagen, que representa el ejecutable o script involucrado en el evento. Esta agrupación está sujeta a las condiciones: la transacción comienza con un evento donde`EventCode`es `1` y termina con un evento donde `EventCode` es `3`. El `maxspan=1m` La cláusula limita la transacción a eventos que ocurren dentro de un período de 1 minuto. El comando de transacción puede vincular eventos relacionados para proporcionar una mejor comprensión de las secuencias de actividades que suceden dentro de un sistema.
    - `| table Image`: Este comando formatea la salida en una tabla, mostrando solo el `Image` campo.
    - `| dedup Image`: Finalmente, el `dedup` El comando elimina las entradas duplicadas del conjunto de resultados. Aquí, está eliminando cualquier duplicado. `Image` valores. El comando mantiene solo la primera aparición y elimina los duplicados posteriores según la`Image`campo.
    
    En resumen, esta consulta tiene como objetivo identificar secuencias de actividades (creación de procesos seguida de una conexión de red) asociadas con el mismo ejecutable o script dentro de una ventana de 1 minuto. Presenta los resultados en formato de tabla, lo que garantiza que los ejecutables/scripts enumerados sean únicos. La consulta puede ser valiosa en la búsqueda de amenazas, particularmente cuando se buscan indicadores de compromiso, como secuencias rápidas de creación de procesos y eventos de conexión de red iniciados por el mismo ejecutable.
    
16. **Subbúsquedas**
    
    Una subbúsqueda en Splunk es una búsqueda anidada dentro de otra búsqueda. Se utiliza para calcular un conjunto de resultados que luego se utilizan en la búsqueda externa. Ejemplo:
    
    ```
    index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 NOT [ search index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | top limit=100 Image | fields Image ] | table _time, Image, CommandLine, User, ComputerName
    
    ```
    
    En esta consulta:
    
    - `index="main" sourcetype="WinEventLog:Sysmon" EventCode=1`: La búsqueda principal que recupera `EventCode=1 (Process Creation)` eventos.
    - `NOT []`: Los corchetes contienen la subbúsqueda. Al colocar `NOT` antes, la búsqueda principal excluirá los resultados que devuelva la subbúsqueda.
    - `search index="main" sourcetype="WinEventLog:Sysmon" EventCode=1 | top limit=100 Image | fields Image`: La subbúsqueda que recupera `EventCode=1 (Process Creation)` eventos, luego usa el `top` comando para devolver los 100 más comunes `Image` (proceso) nombres.
    - `table _time, Image, CommandLine, User, Computer`: Esto presenta los resultados finales como una tabla, mostrando la marca de tiempo del evento (`_time`), el nombre del proceso (`Image`), la línea de comando utilizada para ejecutar el proceso (`CommandLine`), el usuario que ejecutó el proceso (`User`), y la computadora en la que ocurrió el evento (`ComputerName`).
    
    Esta consulta puede ayudar a resaltar procesos inusuales o raros, que pueden valer la pena investigar en busca de posibles actividades maliciosas. Asegúrese de ajustar el límite en la subbúsqueda según sea necesario para adaptarse a su entorno.
    
    Como nota, este tipo de búsqueda puede generar mucho ruido en entornos donde con frecuencia se crean procesos nuevos y únicos, por lo que es importante un ajuste y un contexto cuidadosos.
    

---

Esto es sólo la punta del iceberg cuando se trata de SPL. Su amplio conjunto de comandos y su sintaxis flexible brindan capacidades integrales de análisis de datos. Como ocurre con cualquier idioma, el dominio viene con la práctica y la experiencia. Encuentre a continuación algunos recursos excelentes para comenzar:

- [https://docs.splunk.com/Documentation/SCS/current/SearchReference/Introduction](https://docs.splunk.com/Documentation/SCS/current/SearchReference/Introduction)
- [https://docs.splunk.com/Documentation/SplunkCloud/latest/SearchReference/](https://docs.splunk.com/Documentation/SplunkCloud/latest/SearchReference/)
- [https://docs.splunk.com/Documentation/SplunkCloud/latest/Search/](https://docs.splunk.com/Documentation/SplunkCloud/latest/Search/)

---

# **Cómo identificar los datos disponibles**

**Enfoque 1 de identificación de datos y campos: aprovechar la aplicación de búsqueda e informes (SPL) de Splunk**

En cualquier sistema sólido de gestión de eventos e información de seguridad (SIEM) como Splunk, comprender las fuentes de datos disponibles, los datos que proporcionan y los campos dentro de ellas es fundamental para aprovechar el sistema de manera efectiva. En Splunk, utilizamos principalmente la aplicación Búsqueda e informes para hacer esto. Profundicemos en cómo podemos identificar tipos de fuentes de datos, datos y campos dentro de Splunk.

Splunk puede ingerir una amplia variedad de fuentes de datos. Clasificamos estas fuentes de datos en tipos de fuentes que dictan cómo Splunk formatea los datos entrantes. Para identificar los tipos de fuentes disponibles, podemos ejecutar el siguiente comando SPL,`after selecting the suitable time range in the time picker of the Search & Reporting application`.

Introducción a Splunk y SPL

```
| eventcount summarize=false index=* | table index

```

Esta consulta utiliza `eventcount` para contar eventos en todos los índices, entonces `summarize=false` se utiliza para mostrar los recuentos de cada índice por separado y, finalmente, el `table` El comando se utiliza para presentar los datos en forma tabular.

Introducción a Splunk y SPL

```
| metadata type=sourcetypes

```

Esta búsqueda utiliza el `metadata` comando, que nos proporciona varias estadísticas sobre campos indexados específicos. Aquí nos centramos en `sourcetypes`. El resultado es una lista de todos `sourcetypes` en nuestro entorno Splunk, junto con metadatos adicionales, como la primera vez que se vio un tipo de fuente (`firstTime`), la última vez que fue visto (`lastTime`) y el número de hosts (`totalCount`).

Para una vista más sencilla, podemos utilizar la siguiente búsqueda.

Introducción a Splunk y SPL

```
| metadata type=sourcetypes index=* | table sourcetype

```

Aquí, el `metadata` El comando recupera metadatos sobre los datos en nuestros índices. El `type=sourcetypes` El argumento le dice a Splunk que devuelva metadatos sobre los tipos de origen. El `table` El comando se utiliza para presentar los datos en forma tabular.

Introducción a Splunk y SPL

```
| metadata type=sources index=* | table source

```

Este comando devuelve una lista de todas las fuentes de datos en el entorno de Splunk.

Una vez que conocemos nuestros tipos de fuentes, podemos investigar el tipo de datos que contienen. Digamos que estamos interesados ​​en un tipo de fuente llamado`WinEventLog:Security`, podemos usar el comando de tabla para presentar los datos sin procesar de la siguiente manera.

Introducción a Splunk y SPL

```
sourcetype="WinEventLog:Security" | table _raw

```

El `table` El comando genera una tabla con los campos especificados como columnas. Aquí, `_raw` representa los datos del evento sin procesar. Este comando devolverá los datos sin procesar para el tipo de fuente especificado.

Splunk extrae automáticamente un conjunto de campos predeterminados para cada evento que indexa, pero también puede extraer campos adicionales según el tipo de fuente de los datos. Para ver todos los campos disponibles en un tipo de fuente específico, podemos usar el `fields` dominio.

Introducción a Splunk y SPL

```
sourcetype="WinEventLog:Security" | table *

```

Este comando genera una tabla con todos los campos disponibles en el `WinEventLog:Security` tipo de fuente. Sin embargo, tenga cuidado, ya que el uso de `table *` Puede resultar en una tabla muy amplia si nuestros eventos tienen una gran cantidad de campos. Es posible que esto no sea visualmente práctico o efectivo para el análisis de datos.

Un mejor enfoque es identificar los campos que le interesan utilizando el `fields` comando como se mencionó anteriormente, y luego especificando esos nombres de campo en el `table` dominio. Ejemplo:

Introducción a Splunk y SPL

```
sourcetype="WinEventLog:Security" | fields Account_Name, EventCode | table Account_Name, EventCode

```

Si queremos ver una lista de nombres de campos solamente, sin los datos, podemos usar el `fieldsummary` comando en su lugar.

Introducción a Splunk y SPL

```
sourcetype="WinEventLog:Security" | fieldsummary

```

Esta búsqueda devolverá una tabla que incluye todos los campos encontrados en los eventos devueltos por la búsqueda (en todo el tipo de fuente que hayamos especificado). La tabla incluye varias columnas de información sobre cada campo:

- `field`: El nombre del campo.
- `count`: el número de eventos que contienen el campo.
- `distinct_count`: el número de valores distintos en el campo.
- `is_exact`: Si el recuento es exacto o estimado.
- `max`: El valor máximo del campo.
- `mean`: El valor medio del campo.
- `min`: El valor mínimo del campo.
- `numeric_count`: el número de valores numéricos en el campo.
- `stdev`: La desviación estándar del campo.
- `values`: Valores de muestra del campo.

También podremos ver:

- `modes`: Los valores más comunes del campo.
- `numBuckets`: el número de depósitos utilizados para estimar el recuento distinto.

Tenga en cuenta que los valores proporcionados por el `fieldsummary` El comando se calcula en función de los eventos devueltos por nuestra búsqueda. Entonces, si queremos ver todos los campos dentro de un área específica`sourcetype`, debemos asegurarnos de que nuestro rango de tiempo sea lo suficientemente grande como para capturar todos los campos posibles.

Introducción a Splunk y SPL

```
index=* sourcetype=* | bucket _time span=1d | stats count by _time, index, sourcetype | sort - _time

```

A veces, es posible que queramos saber cómo se distribuyen los eventos a lo largo del tiempo. Esta consulta recupera todos los datos (`index=* sourcetype=*`), entonces `bucket` El comando se utiliza para agrupar los eventos según el `_time` campo en cubos de 1 día. El `stats` El comando luego cuenta el número de eventos para cada día (`_time`), `index`, y `sourcetype`. Por último, el `sort` El comando ordena el resultado en orden descendente de `_time`.

Introducción a Splunk y SPL

```
index=* sourcetype=* | rare limit=10 index, sourcetype

```

El `rare` El comando puede ayudarnos a identificar tipos de eventos poco comunes, que podrían ser indicativos de un comportamiento anormal. Esta consulta recupera todos los datos y encuentra las 10 combinaciones más raras de índices y tipos de fuente.

Introducción a Splunk y SPL

```
index="main" | rare limit=20 useother=f ParentImage

```

Este comando muestra los 20 valores menos comunes del `ParentImage` campo.

Introducción a Splunk y SPL

```
index=* sourcetype=* | fieldsummary | where count < 100 | table field, count, distinct_count

```

Una consulta más compleja puede proporcionar un resumen detallado de los campos. Esta búsqueda muestra un resumen de todos los campos (`fieldsummary`), filtra los campos que aparecen en menos de 100 eventos (`where count < 100`), y luego muestra una tabla (`table`) que muestra el nombre del campo, el recuento total y el recuento distinto.

Introducción a Splunk y SPL

```
index=* | sistats count by index, sourcetype, source, host

```

También podemos utilizar el `sistats` comando para explorar la diversidad de eventos. Este comando cuenta la cantidad de eventos por índice, tipo de fuente, fuente y host, lo que puede proporcionarnos una imagen clara de la diversidad y distribución de nuestros datos.

Introducción a Splunk y SPL

```
index=* sourcetype=* | rare limit=10 field1, field2, field3

```

El comando raro también se puede utilizar para encontrar combinaciones poco comunes de valores de campo. Reemplazar `field1`, `field2`, `field3` con los campos de interés. Este comando mostrará las 10 combinaciones más raras de estos campos.

Al combinar los comandos y técnicas de SPL anteriores, podemos explorar y comprender los tipos de fuentes de datos, los datos que contienen y los campos que contienen. Esta comprensión es la base sobre la cual construimos búsquedas, informes, alertas y paneles efectivos en Splunk.

Por último, recuerde seguir las políticas de gobierno de datos de su organización al explorar datos y tipos de fuentes para asegurarse de cumplir con todas las pautas de privacidad y seguridad necesarias.

Puede aplicar cada una de las búsquedas mencionadas anteriormente con éxito contra los datos dentro de la instancia de Splunk del objetivo que puede generar a continuación. Recomendamos encarecidamente ejecutar e incluso modificar estas búsquedas para familiarizarse con SPL (lenguaje de procesamiento de búsqueda) y sus capacidades.

---

**Enfoque 2 de identificación de datos y campos: aprovechar la interfaz de usuario de Splunk**

Al usar el `Search & Reporting` La interfaz de usuario de la aplicación, identificar los tipos de fuentes de datos disponibles, los datos que contienen y los campos dentro de ellos se convierte en una tarea que implica interactuar con varias secciones de la interfaz de usuario. Examinemos cómo podemos utilizar eficazmente la interfaz web de Splunk para identificar estos elementos.

- `Data Sources`: Lo primero que queremos identificar son nuestras fuentes de datos. Podemos hacer esto navegando a la `Settings` menú, que generalmente se encuentra en la esquina superior derecha de nuestra instancia de Splunk. En el menú desplegable encontraremos`Data inputs`. Al hacer clic en `Data inputs`, veremos una lista de varios métodos de entrada de datos, incluidos archivos y directorios, recopilador de eventos HTTP, reenviadores, scripts y muchos más. Estos representan las diversas fuentes a través de las cuales se pueden introducir datos en Splunk. Al hacer clic en cada uno de estos, obtendremos una descripción general de las fuentes de datos que se utilizan.
- `Data (Events)`: Ahora pasemos a identificar los datos en sí, es decir, los eventos. Para esto, queremos navegar hasta el `Search & Reporting` aplicación. Al explorar los acontecimientos en el `Fast` modo, podemos escanear rápidamente nuestros datos. El `Verbose` El modo, por otro lado, nos permite profundizar en los detalles de cada evento, incluidos sus datos sin procesar y todos los campos que Splunk ha extraído de él.  En la barra de búsqueda, simplemente podríamos poner `` y pulsamos buscar, que mostrará todos los datos que hemos indexado. Sin embargo, suele ser una gran cantidad de datos y puede que no sea la forma más eficaz de hacerlo. Un mejor enfoque podría ser aprovechar el selector de tiempo y seleccionar un rango de tiempo más pequeño (tengamos cuidado al hacerlo para no perder ningún registro histórico importante/útil).
    
    ![](https://academy.hackthebox.com/storage/modules/218/111.png)
    
- `Fields`: Por último, para identificar los campos de nuestros datos, veamos un evento en detalle. Podemos hacer clic en cualquier evento en nuestros resultados de búsqueda para expandirlo.  También podemos ver en el lado izquierdo de la aplicación "Búsqueda e informes" dos categorías de campos: `Selected Fields` y `Interesting Fields`. `Selected Fields` son campos que siempre se muestran en los eventos (como `host`, `source`, y `sourcetype`), mientras `Interesting Fields` Son aquellos que aparecen en al menos el 20% de los eventos. Al hacer clic `All fields`, podemos ver todos los campos presentes en nuestros eventos.
    
    ![](https://academy.hackthebox.com/storage/modules/218/fields1.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/fields.png)
    
- `Data Models`: Los modelos de datos proporcionan una vista organizada y jerárquica de nuestros datos, simplificando conjuntos de datos complejos en estructuras comprensibles. Están diseñados para facilitar la creación de informes, visualizaciones y paneles significativos sin necesidad de una comprensión profunda de las fuentes de datos subyacentes o la necesidad de escribir consultas SPL complejas. Así es como podemos utilizar la función Modelos de datos para identificar y comprender nuestros datos:
    - `Accessing Data Models`: Para acceder a Modelos de Datos, hacemos clic en el `Settings` pestaña en la esquina superior derecha de la interfaz web de Splunk. Luego seleccionamos `Data Models` bajo el `Knowledge` sección. Esto nos llevará a la página de gestión de Modelos de datos.`<-- If it appears empty, please execute a search and navigate to the Data Models page again.`
    - `Understanding Existing Data Models`: En la página de gestión de Modelos de datos, podemos ver una lista de Modelos de datos disponibles. Estos pueden incluir modelos creados por nosotros mismos, nuestro equipo o modelos proporcionados por Splunk Apps. Cada modelo de datos está asociado con un contexto de aplicación específico y está diseñado para describir los datos estructurados relacionados con el propósito de la aplicación.
    - `Exploring Data Models`: Al hacer clic en el nombre de un modelo de datos, somos llevados a la `Data Model Editor`. Aquí es donde reside el verdadero poder de los modelos de datos. Aquí podemos ver la estructura jerárquica del modelo de datos, que se divide en`objects`. Cada objeto representa una parte específica de nuestros datos y contiene `fields` que son relevantes para ese objeto.  Por ejemplo, si tenemos un modelo de datos que describe el tráfico web, podríamos ver objetos como `Web Transactions`, `Web Errors`, etc. Dentro de estos objetos, encontraremos campos como `status`, `url`, `user`, etc.
        
        ![](https://academy.hackthebox.com/storage/modules/218/112.png)
        
- `Pivots`: Los pivotes son una característica extremadamente poderosa en Splunk que nos permite crear informes y visualizaciones complejos sin escribir consultas SPL. Proporcionan una interfaz interactiva de arrastrar y soltar para definir y refinar nuestros criterios de presentación de datos. Como tal, también son una herramienta fantástica para identificar y explorar los datos y campos disponibles dentro de nuestro entorno Splunk. Para comenzar con Pivots para identificar datos y campos disponibles, podemos usar el`Pivot`botón que aparece cuando estamos navegando por un modelo de datos particular en el`Data Models`página.
    
    ![](https://academy.hackthebox.com/storage/modules/218/115.png)
    
    ![](https://academy.hackthebox.com/storage/modules/218/pivot.png)