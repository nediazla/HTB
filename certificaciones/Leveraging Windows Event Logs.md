# Leveraging Windows Event Logs

# **Detección de reconocimiento común de usuario/dominio**

# **Reconocimiento de dominio**

`Active Directory (AD) domain reconnaissance`Representa una etapa fundamental en el ciclo de vida cibernético. Durante esta fase, los adversarios se esfuerzan por recopilar información sobre el entorno objetivo, buscando comprender su arquitectura, topología de red, medidas de seguridad y posibles vulnerabilidades.

Mientras realizan un reconocimiento de dominio AD, los atacantes se centran en identificar componentes cruciales como controladores de dominio, cuentas de usuarios, grupos, relaciones de confianza, unidades organizativas (OUS), políticas grupales y otros objetos vitales. Al obtener información sobre el entorno publicitario, los atacantes pueden identificar objetivos de alto valor, aumentar sus privilegios y moverse lateralmente dentro de la red.

### **Reconocimiento de usuario/dominio utilizando ejecutables nativos de Windows**

Un ejemplo de reconocimiento del dominio AD es cuando un adversario ejecuta el`net group`comando obtener una lista de`Domain Administrators`.

![](https://academy.hackthebox.com/storage/modules/233/image63.png)

Las herramientas/comandos nativos comunes utilizados para el reconocimiento de dominio incluyen:

- `whoami /all`
- `wmic computersystem get domain`
- `net user /domain`
- `net group "Domain Admins" /domain`
- `arp -a`
- `nltest /domain_trusts`

Para la detección, los administradores pueden emplear PowerShell para monitorear scripts o cmdlets inusuales y procesar el monitoreo de la línea de comandos.

### **Reconocimiento de usuario/dominio utilizando Bloodhound/Sharphound**

[Sabueso](https://github.com/SpecterOps/BloodHound)es una herramienta de reconocimiento de dominio de código abierto creada para analizar y visualizar el entorno Active Directory (AD). Los atacantes emplean con frecuencia para discernir las rutas de ataque y los riesgos potenciales de seguridad dentro de la infraestructura publicitaria de una organización. Bloodhound aprovecha la teoría de gráficos y el mapeo de relaciones para dilucidar las relaciones de confianza, los permisos y las membresías grupales dentro del dominio AD.

![](https://academy.hackthebox.com/storage/modules/233/image1.png)

[Sharphound](https://github.com/BloodHoundAD/SharpHound)es un recopilador de datos C# para Bloodhound. Un ejemplo de uso incluye un adversario que ejecuta Sharphound con todos los métodos de recolección (`-c all`).

![](https://academy.hackthebox.com/storage/modules/233/image56.png)

### **Oportunidades de detección de sabuesos**

Bajo el capó, el coleccionista de sangre ejecuta numerosas consultas LDAP dirigidas al controlador de dominio, con el objetivo de acumular información sobre el dominio.

![](https://academy.hackthebox.com/storage/modules/233/image45.png)

Sin embargo, monitorear consultas LDAP puede ser un desafío. Por defecto, el registro de eventos de Windows no los registra. La mejor opción que Windows puede sugerir es emplear`Event 1644`- El registro de monitoreo de rendimiento LDAP. Incluso con él habilitado, Bloodhound puede no generar muchos de los eventos esperados.

![](https://academy.hackthebox.com/storage/modules/233/image81.png)

Un enfoque más confiable es utilizar el proveedor de Windows ETW`Microsoft-Windows-LDAP-Client`. Como se mostró anteriormente en el`SOC Analyst`camino,[Silketw y servicio de silks](https://github.com/mandiant/SilkETW)son envoltorios C# versátiles para ETW, diseñados para simplificar las complejidades de ETW, proporcionando una interfaz accesible para la investigación y la introspección.`SilkService`admite la salida al registro de eventos de Windows, que optimiza la digestión de registro. Otra característica útil es la capacidad de emplear`Yara`Reglas para cazar consultas ldap sospechosas.

![](https://academy.hackthebox.com/storage/modules/233/image57.png)

Además, el equipo de ATP de Microsoft ha compilado un[Lista de filtros LDAP utilizados con frecuencia por herramientas de reconocimiento](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/hunting-for-reconnaissance-activities-using-ldap-search-filters/ba-p/824726).

![](https://academy.hackthebox.com/storage/modules/233/image59.png)

Armado con esta lista de filtros LDAP, la actividad de los sabuesos se puede detectar de manera más eficiente.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detectar el usuario de usuario/dominio con Splunk**

Observará que se da un plazo específico al identificar cada ataque. Esto se hace para concentrarse en los eventos relevantes, evitando el abrumador volumen de eventos no relacionados.

Ahora exploremos cómo podemos identificar las técnicas de reconocimiento discutidas anteriormente, usando Splunk.

### **Detección de Recon Actualización de ejecutivos nativos de Windows**

**Periodo de tiempo**: `earliest=1690447949 latest=1690450687`

Detección de reconocimiento común de usuario/dominio

```
index=main source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventID=1 earliest=1690447949 latest=1690450687
| search process_name IN (arp.exe,chcp.com,ipconfig.exe,net.exe,net1.exe,nltest.exe,ping.exe,systeminfo.exe,whoami.exe) OR (process_name IN (cmd.exe,powershell.exe) AND process IN (*arp*,*chcp*,*ipconfig*,*net*,*net1*,*nltest*,*ping*,*systeminfo*,*whoami*))
| stats values(process) as process, min(_time) as _time by parent_process, parent_process_id, dest, user
| where mvcount(process) > 3

```

![](https://academy.hackthebox.com/storage/modules/233/2.png)

**Desglose de búsqueda**:

- `Filtering by Index and Source`: La búsqueda comienza seleccionando eventos del índice principal donde está la fuente`XmlWinEventLog:Microsoft-Windows-Sysmon/Operational`, que es el registro de eventos de Windows con formato XML para eventos Sysmon (Monitor de sistema). Sysmon es un controlador de servicio y dispositivo que registra la actividad del sistema al registro de eventos.
- `EventID Filter`: La búsqueda se filtra aún más para seleccionar eventos con un`Event ID`de`1`. En Sysmon, la identificación del evento 1 corresponde a`Process Creation`eventos, que registran datos sobre procesos recién creados.
- `Time Range Filter`: La búsqueda restringe el rango de tiempo de los eventos a los que ocurren entre las marcas de tiempo UNIX 1690447949 y 1690450687. Estas marcas de tiempo representan los tiempos más tempranos y últimos en los que ocurrieron los eventos.
- `Process Name Filter`: La búsqueda luego filtra los eventos para incluir solo aquellos donde el campo Process_Name es uno de una lista de nombres de procesos específicos (por ejemplo,`arp.exe`, `chcp.com`, `ipconfig.exe`, etc.) o donde el`process_name`el campo es`cmd.exe`o`powershell.exe`y el campo de proceso contiene ciertas subcadenas. Este paso está buscando eventos que involucren ciertos comandos relacionados con el sistema o la red, así como los eventos en los que estos comandos se ejecutaron desde un símbolo del sistema o una sesión de PowerShell.
- `Statistics`: El comando de estadísticas se utiliza para agregar eventos basados en los campos`parent_process`, `parent_process_id`, `dest`, y`user`. Para cada combinación única de estos campos, la búsqueda calcula las siguientes estadísticas:
    - `values(process) as process`: Esto captura todos los valores únicos del`process field`Como un campo multivalue nombrado`process`.
    - `min(_time) as _time`: Esto captura el tiempo más temprano (`_time`) que ocurrió un evento dentro de cada grupo.
- `Filtering by Process Count`: El comando Where se usa para filtrar los resultados para incluir solo aquellos donde el recuento del campo de proceso es mayor que`3`. Este paso está buscando casos en los que múltiples procesos (más de tres) fueron ejecutados por el mismo proceso principal.

### **Detectar Recon atacando a Bloodhound**

**Periodo de tiempo**: `earliest=1690195896 latest=1690285475`

Detección de reconocimiento común de usuario/dominio

```
index=main earliest=1690195896 latest=1690285475 source="WinEventLog:SilkService-Log"
| spath input=Message
| rename XmlEventData.* as *
| table _time, ComputerName, ProcessName, ProcessId, DistinguishedName, SearchFilter
| sort 0 _time
| search SearchFilter="*(samAccountType=805306368)*"
| stats min(_time) as _time, max(_time) as maxTime, count, values(SearchFilter) as SearchFilter by ComputerName, ProcessName, ProcessId
| where count > 10
| convert ctime(maxTime)

```

![](https://academy.hackthebox.com/storage/modules/233/1.png)

**Desglose de búsqueda**:

- `Filtering by Index and Source`: La búsqueda comienza seleccionando eventos del índice principal donde está la fuente`WinEventLog:SilkService-Log`. Esta fuente representa los datos de registro de eventos de Windows recopilados por`SilkETW`.
- `Time Range Filter`: La búsqueda restringe el rango de tiempo de los eventos a los que ocurren entre las marcas de tiempo UNIX 1690195896 y 1690285475. Estas marcas de tiempo representan los momentos más tempranos y últimos en los que ocurrieron los eventos.
- `Path Extraction`: El`spath`El comando se usa para extraer campos del`Message`campo, que probablemente contiene datos estructurados como`XML`o`JSON`. El`spath`El comando identifica y extrae automáticamente los campos en función de la estructura de datos.
- `Field Renaming`: El`rename`El comando se usa para cambiar el nombre de campos que comienzan con`XmlEventData.`a los nombres de campo equivalentes sin el`XmlEventData.`prefijo. Esto se hace para una referencia más fácil a los campos en etapas posteriores de la búsqueda.
- `Tabulating Results`: El`table`El comando se usa para mostrar los resultados en un formato tabular con las siguientes columnas:`_time`, `ComputerName`, `ProcessName`, `ProcessId`, `DistinguishedName`, y`SearchFilter`. El`table`El comando solo incluye estos campos en la salida.
- `Sorting`: El`sort`El comando se usa para ordenar los resultados en función del`_time`campo en orden ascendente (de más antiguo a nuevo). El`0`El argumento significa que no hay límite en el número de resultados a clasificar.
- `Search Filter`: El comando de búsqueda se usa para filtrar los resultados para incluir solo eventos donde el`SearchFilter`el campo contiene la cadena`(samAccountType=805306368)*`. Este paso está buscando eventos relacionados con consultas LDAP con una condición de filtro específica.
- `Statistics`: El`stats`El comando se utiliza para agregar eventos basados en los campos`ComputerName`, `ProcessName`, y`ProcessId`. Para cada combinación única de estos campos, la búsqueda calcula las siguientes estadísticas:
    - `min(_time) as _time`: El momento más temprano (`_time`) que ocurrió un evento dentro de cada grupo.
    - `max(_time) as maxTime`: La última vez (`_time`) que ocurrió un evento dentro de cada grupo.
    - `count`: El número de eventos dentro de cada grupo.
    - `values(SearchFilter) as SearchFilter`: Todos los valores únicos del`SearchFilter`campo dentro de cada grupo.
- `Filtering by Event Count`: El`where`El comando se usa para filtrar los resultados para incluir solo aquellos donde el`count`el campo es mayor que`10`. Este paso está buscando casos en los que el mismo proceso en la misma computadora hizo más de diez consultas de búsqueda con la condición del filtro especificada.
- `Time Conversion`: El`convert`El comando se usa para convertir el`maxTime`Campo desde el formato de marca de tiempo UNIX hasta formato legible por humanos (`ctime`).