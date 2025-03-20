# Windows -THERAL

Para realizar un análisis de malware efectivo, es esencial una comprensión profunda de Windows -Internals. Los sistemas operativos de Windows funcionan en dos modos principales:

- `User Mode`: Este modo es donde operan la mayoría de las aplicaciones y procesos de usuario. Las aplicaciones en modo de usuario tienen acceso limitado a los recursos del sistema y deben interactuar con el sistema operativo a través de interfaces de programación de aplicaciones (API). Estos procesos se aislan entre sí y no pueden acceder directamente al hardware o las funciones críticas del sistema. Sin embargo, en este modo, el malware aún puede manipular archivos, configuraciones de registro, conexiones de red y otros recursos accesibles para el usuario, y puede intentar escalar privilegios para obtener más control sobre el sistema.
- `Kernel Mode`: En contraste, el modo Kernel es un modo muy privilegiado donde se ejecuta el núcleo de Windows. El núcleo tiene acceso sin restricciones a recursos del sistema, hardware y funciones críticas. Proporciona servicios básicos del sistema operativo, gestiona los recursos del sistema y hace cumplir la seguridad y la estabilidad. Los controladores de dispositivos, que facilitan la comunicación con los dispositivos de hardware, también se ejecutan en modo kernel. Si el malware opera en modo núcleo, gana control elevado y puede manipular el comportamiento del sistema, ocultar su presencia, interceptar llamadas al sistema y alterar los mecanismos de seguridad.

# **Arquitectura de Windows a un alto nivel**

La imagen a continuación muestra una versión simplificada de la arquitectura de Windows.

![](https://academy.hackthebox.com/storage/modules/227/windows_architecture.png)

La arquitectura de Windows simplificada comprende componentes tanto en modo de usuario como en modo de núcleo, cada uno con distintas responsabilidades en el funcionamiento del sistema.

### **Componentes de modo de usuario**

`User-mode components`son aquellas partes del sistema operativo que no tienen acceso directo a las estructuras de datos de hardware o kernel. Interactúan con los recursos del sistema a través de API y llamadas del sistema. Discutamos algunos de ellos:

- `System Support Processes`: Estos son componentes esenciales que proporcionan funcionalidades y servicios cruciales como procesos de inicio de sesión (`winlogon.exe`), Gerente de sesión (`smss.exe`) y gerente de control de servicio (`services.exe`). Estos no son servicios de Windows, pero son necesarios para el funcionamiento adecuado del sistema.
- `Service Processes`: Estos procesos alojan servicios de Windows como el`Windows Update Service`, `Task Scheduler`, y`Print Spooler`servicios. Por lo general, se ejecutan en segundo plano, ejecutando tareas de acuerdo con su configuración y parámetros.
- `User Applications`: Estos son los procesos creados por los programas de usuario, incluidas las aplicaciones de 32 bits y 64 bits. Interactúan con el sistema operativo a través de[API](https://en.wikipedia.org/wiki/Windows_API)proporcionado por Windows. Estas llamadas de API se redirigen a[Ntdll.dll](https://en.wikipedia.org/wiki/Microsoft_Windows_library_files#NTDLL.DLL), activando una transición del modo de usuario al modo del núcleo, donde se ejecuta la llamada del sistema. Luego se devuelve el resultado a la aplicación en modo de usuario, y se produce una transición de regreso al modo de usuario.
- `Environment Subsystems`: Estos componentes son responsables de proporcionar entornos de ejecución para tipos específicos de aplicaciones o procesos. Incluyen el[Subsistema Win32](https://en.wikipedia.org/wiki/Architecture_of_Windows_NT#Win32_environment_subsystem), [Posix](https://en.wikipedia.org/wiki/Microsoft_POSIX_subsystem), y[OS/2](https://en.wikipedia.org/wiki/OS/2).
- `Subsystem DLLs`: Estas bibliotecas de enlace dinámico traducen las funciones documentadas en llamadas apropiadas del sistema nativo interno, implementadas principalmente en`NTDLL.DLL`. Los ejemplos incluyen`kernelbase.dll`, `user32.dll`, `wininet.dll`, y`advapi32.dll`.

### **Componentes del modo de kernel**

`Kernel-mode components`son aquellas partes del sistema operativo que tienen acceso directo a las estructuras de datos de hardware y kernel. Estos incluyen:

- `Executive`: Esta capa superior en modo kernel se accede a través de funciones de`NTDLL.DLL`. Consiste en componentes como el`I/O Manager`, `Object Manager`, `Security Reference Monitor`, `Process Manager`y otros, gestionando los aspectos centrales del sistema operativo, como operaciones de E/S, gestión de objetos, seguridad y procesos. Primero ejecuta algunas verificaciones, y luego pasa la llamada al kernel, o llama al controlador de dispositivo apropiado para realizar la operación solicitada.
- `Kernel`: Este componente gestiona los recursos del sistema, proporcionando servicios de bajo nivel como`thread scheduling`, `interrupt and exception dispatching`, y`multiprocessor synchronization`.
- `Device Drivers`: Estos componentes de software permiten que el sistema operativo interactúe con los dispositivos de hardware. Sirven como intermediarios, lo que permite que el sistema administre y controle los recursos de hardware y software.
- `Hardware Abstraction Layer (HAL)`: Este componente proporciona una capa de abstracción entre los dispositivos de hardware y el sistema operativo. Permite a los desarrolladores de software interactuar con el hardware de manera consistente e independiente de la plataforma.
- `Windowing and Graphics System (Win32k.sys)`: Este subsistema es responsable de administrar la interfaz gráfica de usuario (GUI) y representar elementos visuales en la pantalla.

Ahora, discutamos en lo que sucede detrás de escena cuando una aplicación de usuario llama a una función API de Windows.

# **Flujo de llamadas de la API de Windows**

El malware a menudo utiliza llamadas de API de Windows para interactuar con el sistema y llevar a cabo operaciones maliciosas. Al comprender los detalles internos de las funciones de API, sus parámetros y el comportamiento esperado, los analistas pueden identificar el uso de API sospechoso o no autorizado.

Consideremos un ejemplo de un flujo de llamadas de API de Windows, donde una aplicación en modo de usuario intenta acceder a operaciones privilegiadas y recursos del sistema utilizando el[Leer Función de ProcessMemory](https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-readprocessmemory). Esta función permite que un proceso lea la memoria de un proceso diferente.

![](https://academy.hackthebox.com/storage/modules/227/wininternals_syscall.png)

Cuando se llama a esta función, algunos parámetros requeridos también se pasan a ella, como el mango del proceso de destino, la dirección de origen para leer, un búfer en su propio espacio de memoria para almacenar los datos de lectura y el número de bytes para leer. A continuación se muestra la sintaxis de`ReadProcessMemory`Funciona Winapi según la documentación de Microsoft.

Windows -THERAL

```
BOOL ReadProcessMemory(
  [in]  HANDLE  hProcess,
  [in]  LPCVOID lpBaseAddress,
  [out] LPVOID  lpBuffer,
  [in]  SIZE_T  nSize,
  [out] SIZE_T  *lpNumberOfBytesRead
);

```

![](https://academy.hackthebox.com/storage/modules/227/msdn_003_readprocessmemory.png)

`ReadProcessMemory`es una función de la API de Windows que pertenece al`kernel32.dll`biblioteca. Entonces, esta llamada se invoca a través del`kernel32.dll`Módulo que sirve como interfaz de modo de usuario a la API de Windows. Internamente, el`kernel32.dll`El módulo interactúa con el`NTDLL.DLL`Módulo, que proporciona una interfaz de nivel inferior al kernel de Windows. Luego, esta solicitud de función se traduce a la llamada API nativa correspondiente, que es`NtReadVirtualMemory`. La siguiente captura de pantalla de`x64dbg`Demuestra cómo se ve esto en un depurador.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_readprocessmemory.png)

El`NTDLL.DLL`El módulo utiliza llamadas al sistema (syscalls).

![](https://academy.hackthebox.com/storage/modules/227/msdn_004_syscall_instruction.png)

El`syscall`La instrucción desencadena la llamada del sistema utilizando los parámetros establecidos en las instrucciones anteriores. Transfiere el control del modo de usuario al modo del núcleo, donde el núcleo realiza la operación solicitada después de validar los parámetros y verificar los derechos de acceso del proceso de llamada.

Si la solicitud está autorizada, el subproceso se hace pasar del modo de usuario al modo del núcleo. El núcleo mantiene una mesa conocida como la`System Service Descriptor Table (SSDT)`o el`syscall table (System Call Table)`, que es una estructura de datos que contiene consejos para las diversas rutinas de servicio del sistema. Estas rutinas son responsables del manejo de llamadas del sistema realizadas por aplicaciones en modo de usuario. Cada entrada en la tabla SYSCall corresponde a un número específico de llamadas del sistema, y el puntero asociado apunta a la función del núcleo correspondiente que implementa la operación solicitada.

El syscall responsable de`ReadProcessMemory`se ejecuta en el núcleo, donde se aprovechan los mecanismos de administración de memoria de Windows y aislamiento de procesos. El kernel realiza las validaciones necesarias, las verificaciones de acceso y las operaciones de memoria para leer la memoria del proceso de destino. El kernel recupera las páginas de memoria física correspondientes a las direcciones virtuales solicitadas y copia los datos en el búfer proporcionado.

Una vez que el núcleo ha terminado de leer la memoria, hace la transición del hilo al modo de usuario y el control se devuelve a la aplicación del modo de usuario original. Luego, la aplicación puede acceder a los datos que se leyeron desde la memoria del proceso de destino y continuar su ejecución.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Windows -THERAL

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

# **Ejecutable portátil**

Los sistemas operativos de Windows emplean el`Portable Executable (PE)`formato para encapsular programas ejecutables,`DLLs (Dynamic Link Libraries)`y otros componentes del sistema integral. En el ámbito del análisis de malware, una comprensión intrincada del formato de archivo PE es indispensable. Nos permite obtener información significativa sobre la estructura, las operaciones y las posibles actividades malignas del ejecutable integradas dentro del archivo.

Los archivos de PE acomodan una amplia variedad de tipos de datos, incluidos`executables (.exe)`, `dynamic link libraries (.dll)`, `kernel modules (.srv)`, `control panel applications (.cpl)`, y muchos más. El formato del archivo PE es fundamentalmente una estructura de datos que contiene la información vital requerida para que el cargador del sistema operativo Windows administre el código ejecutable, cargándolo efectivamente en la memoria.

### **Secciones de educación física**

La estructura de educación física también alberga un`Section Table`, un elemento que comprende varias secciones dedicadas a propósitos distintos. Las secciones son esencialmente los repositorios donde se almacena el contenido real del archivo, incluidos los datos, los recursos utilizados por el programa y el código ejecutable. El`.text`La sección a menudo está bajo escrutinio de posibles artefactos relacionados con ataques de inyección.

Las secciones comunes de PE incluyen:

- `Text Section (.text)`: El centro donde reside el código ejecutable del programa.
- `Data Section (.data)`: Un almacenamiento para variables de datos globales y estáticas inicializadas.
- `Read-only initialized data (.rdata)`: Alberga datos de solo lectura, como valores constantes, literales de cadena y variables globales y estáticas inicializadas.
- `Exception information (.pdata)`: Una colección de entradas de la tabla de funciones utilizadas para el manejo de excepciones.
- `BSS Section (.bss)`: Posee variables de datos globales y estáticas no inicializadas.
- `Resource Section (.rsrc)`: Salvaguardar recursos como imágenes, iconos, cadenas e información de versión.
- `Import Section (.idata)`: Detalles sobre funciones importadas de otras DLL.
- `Export Section (.edata)`: Información sobre funciones exportadas por el ejecutable.
- `Relocation Section (.reloc)`: Detalles para reubicar el código y los datos del ejecutable cuando se cargan en una dirección de memoria diferente.

Podemos visualizar las secciones de un ejecutable portátil utilizando una herramienta como`pestudio`como se demuestra a continuación.

![](https://academy.hackthebox.com/storage/modules/227/pe_sections.png)

La profundización del formato de archivo ejecutable portátil (PE) es fundamental para el análisis de malware, ofreciendo información sobre la estructura del archivo, el análisis de código, las funciones de importación y exportación, el análisis de recursos, las técnicas anti-análisis y la extracción de indicadores de compromiso. Nuestra comprensión de esta base allana el camino para un análisis eficaz de malware.

# **Procesos**

En los términos más simples, un proceso es una instancia de un programa de ejecución. Representa una porción de la ejecución de un programa en la memoria y consta de varios recursos, incluidos la memoria, los manijas de archivos, los hilos y los contextos de seguridad.

![](https://academy.hackthebox.com/storage/modules/227/process_internals.png)

Cada proceso se caracteriza por:

- `A unique PID (Process Identifier)`: Se asigna un identificador de proceso único (PID) a cada proceso dentro del sistema operativo. Este identificador numérico facilita el seguimiento y la gestión del proceso por parte del sistema operativo.
- `Virtual Address Space (VAS)`: En el sistema operativo Windows, cada proceso se asigna su propio espacio de direcciones virtual, ofreciendo una vista virtualizada de la memoria para el proceso. El VAS se sección en segmentos, incluidos los segmentos de código, datos y pila, lo que permite el proceso de acceso a la memoria aislada.
- `Executable Code (Image File on Disk)`: El código ejecutable, o el archivo de imagen, significa el archivo ejecutable binario almacenado en el disco. Aloja las instrucciones y recursos necesarios para que el proceso funcione.
- `Table of Handles to System Objects`: Los procesos mantienen una tabla de manijas, un catálogo de referencia para varios objetos del sistema. Los objetos del sistema pueden abarcar archivos, dispositivos, claves de registro, objetos de sincronización y otros recursos.
- `Security Context (Access Token)`: Cada proceso tiene un contexto de seguridad asociado con él, incorporado por un`Access Token`. Este`Access Token`Encapsula información sobre los privilegios de seguridad del proceso, incluida la cuenta de usuario bajo la cual opera el proceso y los derechos de acceso otorgados al proceso.
- `One or More Threads Running in its Context`: Los procesos consisten en uno o más hilos, donde un hilo incorpora una unidad de ejecución dentro del proceso. Los hilos permiten la ejecución concurrente dentro del proceso y facilitan la multitarea.

# **Biblioteca de enlace dinámico (DLL)**

Una biblioteca de enlace dinámico (DLL) es un tipo de PE que representa la "implementación de Microsoft del concepto de biblioteca compartida en el sistema operativo Microsoft Windows". Las DLL exponen una variedad de funciones que pueden ser explotadas por malware, que analizaremos más adelante. Primero, desentrañemos las funciones de importación y exportación en una DLL.

### **Funciones de importación**

- Las funciones de importación son funcionalidades a las que un vinculado dinámicamente se vincula desde bibliotecas o módulos externos durante el tiempo de ejecución. Estas funciones permiten que el binario aproveche las funcionalidades ofrecidas por estas bibliotecas.
- Durante el análisis de malware, el examen de las funciones de importación puede arrojar luz sobre las bibliotecas o módulos externos en los que depende el malware. Esta información ayuda a identificar las API que el malware podría interactuar, y también los recursos como el sistema de archivos, los procesos, el registro, etc.
- Al identificar funciones específicas importadas, es posible determinar las acciones que el malware puede realizar, como operaciones de archivos, comunicación de red, manipulación de registro y más.
- Los nombres de las funciones de importación o los hash pueden servir como COI (indicadores de compromiso) que ayudan a identificar variantes de malware o muestras relacionadas.

A continuación se muestra un ejemplo de inyección de proceso de identificación utilizando las importaciones de DLL y los nombres de funciones:

![](https://academy.hackthebox.com/storage/modules/227/process_injection_fn.png)

En este diagrama, el proceso de malware (`shell.exe`) realiza la inyección de proceso para inyectar código en un proceso de destino (`notepad.exe`) Uso de las siguientes funciones importadas de la DLL`kernel32.exe`:

- `OpenProcess`: Abre un mango al proceso de destino (notepad.exe), proporcionando los derechos de acceso necesarios para manipular su memoria.
- `VirtualAllocEx`: Asigna un bloque de memoria dentro del espacio de direcciones del proceso de destino para almacenar el código inyectado.
- `WriteProcessMemory`: Escribe el código deseado en el bloque de memoria asignado del proceso de destino.
- `CreateRemoteThread`: Crea un nuevo hilo dentro del proceso de destino, especificando el punto de entrada del código inyectado como punto de partida.

Como resultado, el código inyectado se ejecuta dentro del contexto del proceso de destino por el hilo remoto recientemente creado. Esta técnica permite que el malware ejecute código arbitrario dentro del proceso de destino.

Las funciones anteriores son las funciones de Winapi (API de Windows). No se preocupe por las funciones de Winapi a partir de ahora. Los discutiremos en detalle más adelante.

Podemos examinar las importaciones de DLL de`shell.exe`(residiendo en el`C:\Samples\MalwareAnalysis`directorio) usando`CFF Explorer`(disponible en`C:\Tools\Explorer Suite`) como sigue.

![](https://academy.hackthebox.com/storage/modules/227/imports_.png)

### **Funciones de exportación**

- Las funciones de exportación son las funciones que un binario expone para el uso de otros módulos o aplicaciones.
- Estas funciones proporcionan una interfaz para que otro software interactúe con el binario.

---

En la siguiente captura de pantalla, podemos ver un ejemplo de importaciones de DLL (usando`CFF Explorer`) y exportaciones (usando`x64dbg` - `Symbols`pestaña):

- `Imports`: Esto muestra las dlls y sus funciones importadas por un ejecutable`Utilman.exe`.
- `Exports`: Esto muestra las funciones exportadas por un DLL`Kernel32.dll`.

![](https://academy.hackthebox.com/storage/modules/227/dll_imports_exports.png)

En el contexto del análisis de malware, la comprensión de las funciones de importación y exportación ayuda a discernir el comportamiento, las capacidades e interacciones del binario con entidades externas. Produce información valiosa para la detección de amenazas, la clasificación y la medición del impacto del malware en el sistema.