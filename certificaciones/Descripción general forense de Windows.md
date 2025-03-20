# Descripción general forense de Windows

En esta sección, proporcionaremos una descripción concisa de los artefactos clave de Windows y los procedimientos forenses.

# **NTFS**

NTFS (nuevo sistema de archivos de tecnología) es un sistema de archivos propietario desarrollado por Microsoft como parte de su familia Windows NT System Family. Se introdujo con la versión de Windows NT 3.1 en 1993, y desde entonces se ha convertido en el sistema de archivos predeterminado y más ampliamente utilizado en los sistemas operativos modernos de Windows, incluidos Windows XP, Windows 7, Windows 8, Windows 10 y sus contrapartes del servidor.

NTFS fue diseñado para abordar varias limitaciones de su predecesor, el sistema de archivos FAT (Tabla de asignación de archivos). Introdujo numerosas características y mejoras que mejoraron las capacidades de confiabilidad, rendimiento, seguridad y almacenamiento del sistema de archivos.

Estos son algunos de los artefactos forenses clave que los investigadores digitales a menudo analizan cuando trabajan con sistemas de archivos NTFS:

- `File Metadata`: NTFS almacena metadatos extensos para cada archivo, incluido el tiempo de creación, el tiempo de modificación, el tiempo de acceso e información de atributos (como los atributos de archivo de solo lectura, oculto o del archivo). Analizar estas marcas de tiempo puede ayudar a establecer plazos y reconstruir las actividades del usuario.
- `MFT Entries`: La tabla de archivos maestros (MFT) es un componente crucial de NTFS que almacena metadatos para todos los archivos y directorios en un volumen. Examinar las entradas MFT proporciona información sobre los nombres de archivos, tamaños, marcas de tiempo y ubicaciones de almacenamiento de datos. Cuando se eliminan los archivos, sus entradas MFT están marcadas como disponibles, pero los datos pueden permanecer en el disco hasta que se sobrescriban.
- `File Slack and Unallocated Space`: El espacio no asignado en un volumen NTFS puede contener restos de archivos eliminados o fragmentos de datos. File Slack se refiere a la parte no utilizada de un clúster que puede contener datos de un archivo anterior. Las herramientas forenses digitales pueden ayudar a recuperar y analizar datos de estas áreas.
- `File Signatures`: Los encabezados y las firmas de archivos pueden ser útiles para identificar tipos de archivos incluso cuando las extensiones de archivos se han cambiado u oscurecido. Esta información es crítica para reconstruir los tipos de archivos presentes en un sistema.
- `USN Journal`: El Número de secuencia de actualización (USN) Journal es un registro mantenido por NTFS para registrar los cambios realizados en archivos y directorios. Los investigadores forenses pueden analizar la revista USN para rastrear modificaciones de archivos, deleciones y renombros.
- `LNK Files`: Los archivos de acceso directo de Windows (archivos LNK) contienen información sobre el archivo de destino o el programa, así como las marcas de tiempo y los metadatos. Estos archivos pueden proporcionar información sobre archivos recientemente accedidos o programas ejecutados.
- `Prefetch Files`: Windows genera los archivos preventivos para mejorar el rendimiento de inicio de las aplicaciones. Estos archivos pueden indicar qué programas se han ejecutado en el sistema y cuándo fueron ejecutados por última vez.
- `Registry Hives`: Si bien no está directamente relacionado con el sistema de archivos, las colmenas de Windows Registry contienen una configuración importante e información del sistema. Las actividades maliciosas o los cambios no autorizados pueden dejar rastros en el registro, que los investigadores forenses analizan para comprender las modificaciones del sistema.
- `Shellbags`: Los bobs de cáscaras son entradas de registro que almacenan configuraciones de vista de carpeta, como posiciones de ventana y preferencias de clasificación. El análisis de los bomba de cáscaras puede revelar patrones de navegación de usuarios e identificar potencialmente las carpetas accedidas.
- `Thumbnail Cache`: Caches de miniatura almacenan avances en miniatura de imágenes y documentos. Estos cachés pueden revelar archivos que fueron vistos recientemente, incluso si los archivos originales se han eliminado.
- `Recycle Bin`: El contenedor de reciclaje contiene archivos que se han eliminado del sistema de archivos. Analizar el contenedor de reciclaje puede ayudar a recuperar archivos eliminados y proporcionar información sobre las acciones del usuario.
- `Alternate Data Streams (ADS)`: Los anuncios son flujos adicionales de datos asociados con archivos. Los actores maliciosos pueden usar anuncios para ocultar datos, y los investigadores forenses deben examinar estas corrientes para garantizar un análisis exhaustivo.
- `Volume Shadow Copies`: NTFS admite Copias de sombra de volumen, que son instantáneas del sistema de archivos en diferentes puntos en el tiempo. Estas copias pueden ser valiosas para la recuperación de datos y el análisis de los cambios realizados con el tiempo.
- `Security Descriptors and ACLs`: Las listas de control de acceso (ACL) y los descriptores de seguridad determinan los permisos de archivo y carpeta. Analizar estos artefactos ayuda a comprender los derechos de acceso de los usuarios y posibles violaciones de seguridad.

# **Registros de eventos de Windows**

`Windows Event Logs`son una parte intrínseca del sistema operativo de Windows, almacenando registros de diferentes componentes del sistema, incluido el sistema mismo, las aplicaciones que se ejecutan en él, proveedores de ETW, servicios y otros.

El registro de eventos de Windows ofrece capacidades integrales de registro para errores de aplicación, eventos de seguridad e información de diagnóstico. Como profesionales de ciberseguridad, aprovechamos estos registros ampliamente para el análisis y la detección de intrusiones.

Las tácticas adversas desde el compromiso inicial utilizando malware u otras exploits, hasta el acceso de credenciales, la elevación de privilegios y el movimiento lateral utilizando las herramientas internas del sistema operativo de Windows a menudo se capturan a través de registros de eventos de Windows.

Al ver los registros de eventos de Windows disponibles, los investigadores pueden tener una buena idea de lo que se está registrando e incluso buscar entradas de registro específicas. Para acceder a los registros directamente para el análisis fuera de línea, los investigadores deben navegar a la ruta de archivo predeterminada para el almacenamiento de registro en`C:\Windows\System32\winevt\logs`.

El análisis de los registros de eventos de Windows se ha abordado en los módulos titulados`Windows Event Logs & Finding Evil`y`YARA & Sigma for SOC Analysts`.

# **Artefactos de ejecución**

`Windows execution artifacts`Consulte las trazas y la evidencia que quedan en un sistema operativo de Windows cuando se ejecutan programas y procesos. Estos artefactos proporcionan información valiosa sobre la ejecución de aplicaciones, scripts y otros componentes de software, que pueden ser cruciales en las investigaciones forenses digitales, la respuesta de incidentes y el análisis de seguridad cibernética. Al examinar los artefactos de ejecución, los investigadores pueden reconstruir plazos, identificar actividades maliciosas y establecer patrones de comportamiento. Aquí hay algunos tipos comunes de artefactos de ejecución de Windows:

- `Prefetch Files`: Windows mantiene una carpeta de captación previa que contiene metadatos sobre la ejecución de varias aplicaciones. Preplicar los archivos de registro de información, como rutas de archivo, recuentos de ejecución y marcas de tiempo de cuándo se ejecutaron las aplicaciones. Analizar archivos de pre -captación puede revelar un historial de programas ejecutados y el orden en que se ejecutaron.
- `Shimcache`: Shimcache es un mecanismo de Windows que registra información sobre la ejecución del programa para ayudar con las optimizaciones de compatibilidad y rendimiento. Registra detalles como rutas de archivo, marcas de tiempo de ejecución y banderas que indican si se ejecutó un programa. Shimcache puede ayudar a los investigadores a identificar programas ejecutados recientemente y sus archivos asociados.
- `Amcache`: AMCACHE es una base de datos introducida en Windows 8 que almacena información sobre aplicaciones y ejecutables instalados. Incluye detalles como rutas de archivos, tamaños, firmas digitales y marcas de tiempo de cuándo se ejecutaron las aplicaciones por última vez. Analizar el Amcache puede proporcionar información sobre el historial de ejecución del programa e identificar software potencialmente sospechoso o no autorizado.
- `UserAssist`: UserAssist es una clave de registro que mantiene información sobre los programas ejecutados por los usuarios. Registra detalles como nombres de aplicaciones, recuentos de ejecución y marcas de tiempo. Análisis de artefactos de usuarios de usuarios puede revelar un historial de aplicaciones ejecutadas y actividad del usuario.
- `RunMRU Lists`: Las listas RunMru (más recientemente utilizadas) en la información de la tienda de registro de Windows sobre programas ejecutados recientemente desde varias ubicaciones, como el`Run`y`RunOnce`llaves. Estas listas pueden indicar qué programas se ejecutaron, cuándo fueron ejecutados y potencialmente revelan la actividad del usuario.
- `Jump Lists`: Jump enumera la información de almacenamiento sobre archivos, carpetas y tareas recientemente accedidas asociadas con aplicaciones específicas. Pueden proporcionar información sobre las actividades del usuario y los archivos recientemente utilizados.
- `Shortcut (LNK) Files`: Los archivos de acceso directo pueden contener información sobre el ejecutable de destino, las rutas de archivos, las marcas de tiempo y las interacciones del usuario. Analizar archivos LNK puede revelar detalles sobre programas ejecutados y el contexto en el que se ejecutaron.
- `Recent Items`: La carpeta de elementos reciente mantiene una lista de archivos recientemente abiertos. Puede proporcionar información sobre documentos recientemente accedidos y actividad del usuario.
- `Windows Event Logs`: Varios registros de eventos de Windows, como la seguridad, la aplicación y los registros del sistema, registran eventos relacionados con la ejecución del programa, incluida la creación y terminación de procesos, los bloqueos de aplicaciones y más.

| **Artefacto** | **Clave de ubicación/registro** | **Datos almacenados** |
| --- | --- | --- |
| Pedir archivos | C: \ Windows \ Prefetch | Metadatos sobre aplicaciones ejecutadas (rutas de archivo, marcas de tiempo, recuento de ejecución) |
| Shimcache | Registro: HKEY_LOCAL_MACHINE \ SYSTEM \ CurrentControlset \ Control \ Session Manager \ AppCompatCache | Detalles de ejecución del programa (rutas de archivo, marcas de tiempo, banderas) |
| Amcache | C: \ Windows \ AppCompat \ Programas \ Amcache.hve (Binary Registry Hive) | Detalles de la aplicación (rutas de archivo, tamaños, firmas digitales, marcas de tiempo) |
| Usuario | Registro: HKEY_CURRENT_USER \ Software \ Microsoft \ Windows \ CurrentVersion \ Explorer \ UserAssist | Detalles ejecutados del programa (nombres de aplicaciones, recuentos de ejecución, marcas de tiempo) |
| Listas de runmru | Registro: HKEY_CURRENT_USER \ Software \ Microsoft \ Windows \ CurrentVersion \ Explorer \ RunMru | Programas ejecutados recientemente y sus líneas de comando |
| Listas de salto | Carpetas específicas del usuario (por ejemplo, %AppData %\ Microsoft \ Windows \ reciente) | Archivos, carpetas y tareas recientemente accedidos asociados con aplicaciones |
| Archivos de acceso directo (LNK) | Varias ubicaciones (por ejemplo, escritorio, menú de inicio) | Ejecutable de destino, rutas de archivo, marcas de tiempo, interacciones del usuario |
| Artículos recientes | Carpetas específicas del usuario (por ejemplo, %AppData %\ Microsoft \ Windows \ reciente) | Archivos de acceso recientemente |
| Registros de eventos de Windows | C: \ Windows \ System32 \ Winevt \ Logs | Varios registros de eventos que contienen creación de procesos, terminación y otros eventos |

# **Artifactos de persistencia de Windows**

La persistencia de Windows se refiere a las técnicas y mecanismos utilizados por los atacantes para garantizar su presencia y control no autorizados sobre un sistema comprometido, lo que les permite mantener el acceso y el control incluso después de la intrusión inicial. Estos métodos de persistencia explotan varios componentes del sistema, como claves de registro, procesos de inicio, tareas programadas y servicios, lo que permite a los actores maliciosos resistir reinicios y medidas de seguridad mientras continúan llevando a cabo sus objetivos sin ser detectados.

**Registro**

Las ventanas`Registry`actúa como una base de datos crucial, almacenando configuraciones críticas del sistema para el sistema operativo Windows. Esto abarca configuraciones para dispositivos, seguridad, servicios e incluso el almacenamiento de configuraciones de seguridad de la cuenta de usuario en el administrador de cuentas de seguridad (`SAM`). Dada su importancia, no sorprende que los adversarios a menudo se dirigen al registro de Windows para establecer la persistencia. Por lo tanto, es esencial inspeccionar rutinariamente las claves de autorununas de registro.

Ejemplo de`Autorun`claves utilizadas para la persistencia:

- **`Run/RunOnce Keys`**
    - HKEY_CURRENT_USER \ Software \ Microsoft \ Windows \ CurrentVersion \ Run
    - HKEY_CURRENT_USER \ Software \ Microsoft \ Windows \ CurrentVersion \ Runonce
    - HKEY_LOCAL_MACHINE \ Software \ Microsoft \ Windows \ CurrentVersion \ Run
    - HKEY_LOCAL_MACHINE \ Software \ Microsoft \ Windows \ CurrentVersion \ Runonce
    - HKEY_LOCAL_MACHINE \ Software \ Microsoft \ Windows \ CurrentVersion \ Policies \
- **`Keys used by WinLogon Process`**
    - HKEY_LOCAL_MACHINE \ Software \ Microsoft \ Windows NT \ CurrentVersion \ WinLogon
    - HKEY_LOCAL_MACHINE \ Software \ Microsoft \ Windows NT \ CurrentVersion \ Winlogon \ Shell
- **`Startup Keys`**
    - HKEY_CURRENT_USER \ Software \ Microsoft \ Windows \ CurrentVersion \ Explorer \ Carpetas de shell de usuario
    - HKEY_CURRENT_USER \ Software \ Microsoft \ Windows \ CurrentVersion \ Explorer \ SHELL FOPPERS
    - HKEY_LOCAL_MACHINE \ Software \ Microsoft \ Windows \ CurrentVersion \ Explorer \ SHELL FOPPERS
    - HKEY_LOCAL_MACHINE \ Software \ Microsoft \ Windows \ CurrentVersion \ Explorer \ User

**Schtasks**

Windows proporciona una función que permite programas programar tareas específicas. Estas tareas residen en`C:\Windows\System32\Tasks`, con cada uno guardado como un archivo XML. Este archivo detalla el creador, el tiempo o el activador de la tarea, y la ruta al comando o programa establecido para ejecutarse. Para analizar las tareas programadas, debemos navegar a`C:\Windows\System32\Tasks`y examine el contenido de los archivos XML.

**Servicios**

`Services`En Windows son fundamentales para mantener procesos en un sistema, lo que permite que los componentes del software funcionen en segundo plano sin intervención del usuario. Los actores maliciosos a menudo manipulan o crean servicios deshonestos para garantizar la persistencia y retener el acceso no autorizado. La ubicación del registro para vigilar es:`HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services`.

# **Forense del navegador web**

Al sumergirse en el forense del navegador web, es una disciplina centrada en el análisis de los restos dejados por los navegadores web. Estos restos pueden arrojar luz sobre las acciones del usuario, los compromisos en línea y los comportamientos potencialmente dañinos. Algunos de los artefactos forenses del navegador fundamental incluyen:

- `Browsing History`: Registros de sitios web visitados, incluidos URL, títulos, marcas de tiempo y frecuencia de visita.
- `Cookies`: Pequeños archivos de datos almacenados por sitios web en el dispositivo de un usuario, que contiene información como detalles de la sesión, preferencias y tokens de autenticación.
- `Cache`: Copias almacenadas en caché de páginas web, imágenes y otro contenido visitado por el usuario. Puede revelar sitios web accedidos incluso si se borra el historial.
- `Bookmarks/Favorites`: Enlaces guardados a sitios web o páginas de interés visitados con frecuencia.
- `Download History`: Registros de archivos descargados, incluidas URL de origen, nombres de archivo y marcas de tiempo.
- `Autofill Data`: Información ingresada automáticamente en formularios, como nombres, direcciones y contraseñas.
- `Search History`: Consultas ingresadas en motores de búsqueda, junto con términos de búsqueda y marcas de tiempo.
- `Session Data`: Información sobre sesiones de navegación activa, pestañas y ventanas.
- `Typed URLs`: Las URL ingresaron directamente en la barra de direcciones.
- `Form Data`: Información ingresada en formularios web, como credenciales de inicio de sesión y consultas de búsqueda.
- `Passwords`: Contraseñas guardadas o autofestimadas para sitios web.
- `Web Storage`: Datos de almacenamiento locales utilizados por sitios web para diversos fines.
- `Favicons`: Pequeños iconos asociados con sitios web, que pueden revelar sitios visitados.
- `Tab Recovery Data`: Información sobre pestañas y sesiones abiertas que se pueden restaurar después de un bloqueo de navegador.
- `Extensions and Add-ons`: Extensiones de navegador instaladas y sus configuraciones.

# **Srum**

Cambiar de marcha a`SRUM`(Monitor de uso de recursos del sistema), es una característica introducida en Windows 8 y versiones posteriores. Srum rastrea meticulosamente la utilización de recursos y los patrones de uso de la aplicación. Los datos se encuentran en un archivo de base de datos nombrado`sru.db`encontrado en el`C:\Windows\System32\sru`directorio. Esta base de datos formateada por SQLite permite el almacenamiento de datos estructurado y la recuperación eficiente de datos. Los registros de SRUM, organizados por intervalos de tiempo, pueden ayudar a reconstruir la aplicación y el uso de recursos sobre duraciones específicas.

Las facetas clave de Srum Forensics abarcan:

- `Application Profiling`: SRUM puede proporcionar una vista integral de las aplicaciones y procesos que se han ejecutado en un sistema de Windows. Registra detalles como nombres ejecutables, rutas de archivos, marcas de tiempo y métricas de uso de recursos. Esta información es crucial para comprender el panorama del software en un sistema, identificar aplicaciones potencialmente maliciosas o no autorizadas, y reconstruir las actividades del usuario.
- `Resource Consumption`: SRUM captura datos sobre el tiempo de la CPU, el uso de la red y el consumo de memoria para cada aplicación y proceso. Estos datos son invaluables para investigar actividades intensivas en recursos, identificar patrones inusuales de consumo de recursos y detectar posibles problemas de rendimiento causados por aplicaciones específicas.
- `Timeline Reconstruction`: Al analizar los datos de SRUM, los expertos en forenses digitales pueden crear plazos de ejecución de aplicaciones y procesos, uso de recursos y actividades del sistema. Esta reconstrucción de la línea de tiempo es fundamental para comprender la secuencia de eventos, identificar comportamientos sospechosos y establecer una imagen clara de las interacciones y acciones del usuario.
- `User and System Context`: Los datos de SRUM incluyen identificadores de usuarios, que ayudan a atribuir actividades a usuarios específicos. Esto puede ayudar en el análisis del comportamiento del usuario y determinar si ciertas acciones fueron realizadas por usuarios legítimos o actores de amenazas potenciales.
- `Malware Analysis and Detection`: Los datos de SRUM se pueden usar para identificar aplicaciones inusuales o no autorizadas que pueden ser indicativas de malware o actividades maliciosas. Se pueden detectar picos repentinos en el uso de recursos, patrones de aplicación anormales o instalaciones de software no autorizadas a través del análisis SRUM.
- `Incident Response`: Durante la respuesta a los incidentes, SRUM puede proporcionar información rápida sobre las actividades recientes de aplicaciones y procesos, lo que permite a los analistas identificar rápidamente las posibles amenazas y responder de manera efectiva.

[Anterior](https://academy.hackthebox.com/module/237/section/2608)