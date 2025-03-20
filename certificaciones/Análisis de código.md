# Análisis de código

# **Ingeniería inversa y análisis de código**

`Reverse engineering`es un proceso que nos lleva debajo de la superficie de los archivos ejecutables o el código de la máquina compilado, lo que nos permite decodificar su funcionalidad, rasgos de comportamiento y estructura. Con la ausencia del código fuente, pasamos al análisis de las instrucciones del código desmontado, también conocido como`assembly code analysis`. Este nivel más profundo de comprensión nos ayuda a descubrir funcionalidades oscuras o evasivas que permanecen ocultas incluso después del análisis estático y dinámico.

Para desenredar la compleja Web of Machine Code, recurrimos a un dúo de herramientas potentes:`Disassemblers`y`Debuggers`.

- A`Disassembler`es nuestra herramienta de elección cuando deseamos realizar un análisis estático del código, lo que significa que no necesitamos ejecutar el código. Este tipo de análisis es invaluable, ya que nos ayuda a comprender la estructura y la lógica del código sin activar funcionalidades potencialmente dañinas. Algunos ejemplos principales de desarmadores incluyen`IDA`, `Cutter`, y`Ghidra`.
- A`Debugger`, por otro lado, tiene un doble propósito. Al igual que un desmontador, decodifica el código de la máquina en las instrucciones de ensamblaje. Además, nos permite ejecutar el código de manera controlada, proceder con instrucción por instrucción, saltar a ubicaciones específicas o detener el flujo de ejecución en puntos designados utilizando puntos de interrupción. Los ejemplos de debuggers incluyen`x32dbg`, `x64dbg`, `IDA`, y`OllyDbg`.

Demos un paso atrás y entendamos el desafío que tenemos ante nosotros. El viaje del código de los idiomas de alto nivel legibles por humanos, como C o C ++, hasta`machine code`es un boleto unidireccional, guiado por el compilador.`Machine code`, un lenguaje binario que las computadoras procesan directamente, es una narrativa críptica para los analistas humanos. Aquí es donde entra en juego el lenguaje de ensamblaje, actuando como un puente entre nosotros y el código de la máquina, lo que nos permite decodificar la historia de este último.

Un desmontaje transforma el código de la máquina nuevamente en el lenguaje de ensamblaje, que nos presenta una secuencia legible de instrucciones. Comprender el ensamblaje y sus mnemónicos es fundamental para diseccionar la funcionalidad del malware.

---

`Code analysis`es el proceso de analizar y descifrar el comportamiento y la funcionalidad de un programa o binario compilado. Esto implica analizar las instrucciones, el flujo de control y las estructuras de datos dentro del código, en última instancia, arrojando luz sobre el propósito, la funcionalidad y el potencial`indicators of compromise (IOCs)`.

Comprender un programa o una pieza de malware a menudo requiere que revertamos el proceso de compilación. Aquí es donde`Disassembly`entra en la imagen. Al convertir el código de la máquina nuevamente en instrucciones del lenguaje de ensamblaje, terminamos con un conjunto de instrucciones que son simbólicas y mnemónicas, lo que nos permite decodificar la lógica y el funcionamiento del programa.

![](https://academy.hackthebox.com/storage/modules/227/disassembly.png)

Los desapsigadores son nuestros aliados en este proceso. Estas herramientas especializadas toman el código binario, generan las instrucciones de ensamblaje correspondientes y, a menudo, las complementan con contexto adicional, como direcciones de memoria, nombres de funciones y análisis de flujo de control. Una de esas herramientas poderosas es[IDA](https://hex-rays.com/ida-free/), un desascado y depurador ampliamente utilizado reverenciado por sus características de análisis avanzados. Admite múltiples formatos y arquitecturas de archivos ejecutables, presentando una vista completa de desmontaje y potentes capacidades de análisis.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Análisis de código

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

# **Ejemplo de análisis de código: shell.exe**

Persistamos con el análisis de la`shell.exe`Muestra de malware que reside en el`C:\Samples\MalwareAnalysis`directorio del objetivo de esta sección. Hasta este punto, hemos descubierto que conduce`sandbox detection`, y que incluye un posible mecanismo de sueño: un`5-second ping`retraso: antes de ejecutar sus operaciones previstas.

### **Importar una muestra de malware en el desmontador - IDA**

Para la siguiente etapa de nuestra investigación, debemos analizar el código en`IDA`Para determinar sus acciones adicionales y descubrir cómo eludir la verificación de sandbox empleada por la muestra de malware.

Podemos iniciar`IDA`ya sea haciendo doble clic en el`IDA`atajo que se coloca en el escritorio o haciendo clic con el botón derecho y seleccionando`Run as administrator`Para garantizar los derechos de acceso adecuados. Al principio, mostrará la información de la licencia y posteriormente nos pedirá que abra un nuevo ejecutable para su análisis.

A continuación, opta por`New`y seleccione el`shell.exe`muestra que reside en el`C:\Samples\MalwareAnalysis`Directorio del objetivo de esta sección para diseccionar.

![](https://academy.hackthebox.com/storage/modules/227/ida_intro.png)

El`Load a new file`Cuadro de diálogo que aparece a continuación es donde podemos seleccionar la arquitectura del procesador. Elija el correcto y haga clic`OK`. Por defecto,`IDA`determina el tipo de procesador apropiado.

![](https://academy.hackthebox.com/storage/modules/227/ida_intro_processor.png)

Después de que golpeamos`OK`, `IDA`Cargará el archivo ejecutable en la memoria y desmontará el código de la máquina para representar la salida desmontada para nosotros. La captura de pantalla a continuación ilustra las diferentes vistas en`IDA`.

![](https://academy.hackthebox.com/storage/modules/227/ida_intro_views.png)

Una vez que se carga el ejecutable y se completa el análisis, el código desmontado de la muestra`shell.exe`se exhibirá en general`IDA-View`ventana. Podemos atravesar el código utilizando las teclas del cursor o la barra de desplazamiento y acercarse o salir con la rueda del mouse o los controles de zoom.

### **Vistas de texto y gráficos**

El código desmontado se presenta en dos modos, a saber`Graph view`y el`Text view`. La vista predeterminada es el`Graph view`, que proporciona una ilustración gráfica de los bloques básicos de la función y sus interconexiones. Los bloques básicos son secuencias de instrucciones con un solo punto de entrada y salida. Estos bloques básicos se simbolizan como nodos en la vista del gráfico, con las conexiones entre ellos como bordes.

Para alternar entre el gráfico y las vistas de texto, simplemente presione el`spacebar`botón.

- El`Graph view`Ofrece una representación pictórica del flujo de control del programa, facilitando una mejor comprensión del flujo de ejecución, identificación de bucles, condicionales y saltos, y una visualización de cómo el programa se ramifica o se ramifica a través de diferentes rutas de código.
    
    ![](https://academy.hackthebox.com/storage/modules/227/ida_graph_view.png)
    
    Las funciones se muestran como`nodes`en el`Graph view`. Cada función se representa como un nodo distinto con un identificador único y detalles adicionales como el`function name`, `address`, y`size`.
    
- El`Text view`Muestra las instrucciones de ensamblaje junto con sus direcciones de memoria correspondientes. Cada línea en el`Text view`representa un`instruction or a data element`en el código, comenzando con el`section name:virtual address`formato (por ejemplo,`.text:00000000004014F0`, donde está el nombre de la sección`.text`y la dirección virtual es`00000000004014F0`).
    
    Código:IDA
    
    ```
    text:00000000004014F0 ; =============== S U B R O U T I N E =======================================
    text:00000000004014F0
    text:00000000004014F0
    text:00000000004014F0                 public start
    text:00000000004014F0 start           proc near               ; DATA XREF: .pdata:000000000040603C↓o
    text:00000000004014F0
    text:00000000004014F0 ; FUNCTION CHUNK AT 			.text:00000000004022A0 SIZE 000001B0 BYTES
    text:00000000004014F0
    text:00000000004014F0 ; __unwind { // __C_specific_handler
    text:00000000004014F0                 sub     rsp, 28h
    text:00000000004014F4
    text:00000000004014F4 loc_4014F4:                             ; DATA XREF: .xdata:0000000000407058↓o
    text:00000000004014F4 ;   __try { // __except at loc_40150C
    text:00000000004014F4                 mov     rax, cs:off_405850
    text:00000000004014FB                 mov     dword ptr [rax], 0
    text:0000000000401501                 call    sub_401650
    text:0000000000401506                 call    sub_401180
    text:000000000040150B                 nop
    text:000000000040150B ;   } // starts at 4014F4
    
    ```
    
    ![](https://academy.hackthebox.com/storage/modules/227/ida_text_view.png)
    
    `IDA`'s`Text view`Emplea flechas para significar diferentes tipos de instrucciones y saltos de flujo de control. Aquí hay algunas flechas comúnmente vistas y sus interpretaciones:
    
    - `Solid Arrow (→)`: Una flecha sólida denota un salto directo o instrucción de rama, lo que indica un cambio incondicional en el flujo del programa donde la ejecución se mueve de una ubicación a otra. Esto ocurre cuando un salto o instrucción de rama como`jmp`o`call`se encuentra.
    - `Dashed Arrow (---→)`: Una flecha discontinua representa un salto condicional o instrucción de rama, lo que sugiere que el flujo del programa podría cambiar en función de una condición específica. El destino del salto depende del resultado de la condición. Por ejemplo, un`jz`(Jump si cero) La instrucción activará un salto solo si una comparación anterior produjo un valor cero.
        
        ![](https://academy.hackthebox.com/storage/modules/227/ida_text_view_arrows.png)
        

Por defecto, IDA inicialmente exhibe la función principal o la función en el punto de entrada designado del programa. Sin embargo, tenemos la libertad de explorar y examinar otras funciones en la vista del gráfico.

### **Reconociendo la función principal en IDA**

La siguiente captura de pantalla demuestra el`start`función, que es el punto de entrada del programa y generalmente es responsable de configurar el entorno de tiempo de ejecución antes de invocar el real`main`función. Esta es la inicial`start`función que se muestra por IDA después de cargar el ejecutable.

![](https://academy.hackthebox.com/storage/modules/227/ida__001_start.png)

Nuestro objetivo es localizar la función principal real, lo que requiere una mayor exploración del desmontaje. Buscaremos llamadas de funciones o saltos que conducen a otras funciones, ya que es probable que una de ellas sea la función principal.`IDA`La vista gráfica, las referencias cruzadas o la lista de funciones pueden ayudar a navegar a través del desmontaje e identificar el`main`función.

Sin embargo, para llegar al`main`función, primero debemos entender la función de este`start`función. Esta función consiste principalmente en algún código de inicialización, manejo de excepciones y llamadas de función. Eventualmente salta al`loc_40150C`Etiqueta, que es un manejador de excepción. Por lo tanto, podemos inferir que esta no es la función principal real donde la lógica del programa generalmente reside. Inspeccionaremos las otras llamadas de función para identificar el`main`función.

El código comienza restando`0x28`(40 en decimal) del`rsp`(Puntador de pila) Regístrese, creando efectivamente espacio en la pila para variables locales y preservando el contenido de la pila anterior.

Código:IDA

```
public start
start proc near

; FUNCTION CHUNK AT .text:00000000004022A0 SIZE 000001B0 BYTES

; __unwind { // __C_specific_handler
sub     rsp, 28h

```

El bloque medio de la captura de pantalla anterior representa un mecanismo de manejo de excepciones que utiliza`structured exception handling (SEH)`en el código. El`__try`y`__except`Las palabras clave sugieren la configuración de un bloque de manejo de excepciones. Dentro de esto, el posterior`call`Instrucciones Llamar a dos subrutinas (funciones) nombradas`sub_401650`y`sub_401180`, respectivamente. Estos son nombres de marcadores de posición generados automáticamente por IDA para denotar subrutinas, ubicaciones de programas y datos. Los nombres autogenerados generalmente tienen uno de los siguientes prefijos seguidos de sus direcciones virtuales correspondientes:`sub_<virtual_address>`o`loc_<virtual_address>`etc.

Código:IDA

```
loc_4014F4:
;   __try { // __except at loc_40150C
mov     rax, cs:off_405850
mov     dword ptr [rax], 0
call    sub_401650         ; Will inspect this function
call    sub_401180         ; Will inspect this function
nop
;   } // starts at 4014F4

-----------------------------------------------

loc_40150C:
;   __except(TopLevelExceptionFilter) // owned by 4014F4
nop
add     rsp, 28h
retn
; } // starts at 4014F0
start endp

```

### **Navegar por funciones en IDA**

Inspeccionemos el contenido de estas dos funciones`sub_401650`y`sub_401180`navegando dentro de cada función para leer el código desmontado.

![](https://academy.hackthebox.com/storage/modules/227/ida_function_calls.png)

Inicialmente abriremos la primera función/subrutina`sub_401650`. Para ingresar a una función en`IDA`Vista de desmontaje, coloque el cursor en la instrucción que representa la llamada de función (o instrucción de salto) que queremos seguir, luego haga clic derecho en la instrucción y seleccione`Jump to Operand`Desde el menú contextual. Alternativamente, podemos presionar el`Enter`Key en nuestro teclado.

![](https://academy.hackthebox.com/storage/modules/227/ida_function_enter.png)

Entonces,`IDA`nos guiará a la ubicación objetivo de la llamada de salto o función, llevándonos al inicio de la función llamada o al destino del salto.

Ahora que estamos dentro de la primera función/subrutina`sub_401650`, se esfuerzemos por entenderlo para determinar si es el`main`función. Si no, navegaremos a través de otras funciones y discerniremos la llamada al`main`función.

En esta subrutina`sub_401650`, podemos ver instrucciones de llamadas a las funciones como`GetSystemTimeAsFileTime`, `GetCurrentProcessId`, `GetCurrentThreadId`, `GetTickCount`, y`QueryPerformanceCounter`. Este patrón se observa con frecuencia al comienzo del código ejecutable desmontado y generalmente consiste en configurar el marco de pila inicial y llevar a cabo algunas tareas de inicialización relacionadas con el sistema.

![](https://academy.hackthebox.com/storage/modules/227/ida__002_initial_stack.png)

El tipo de instrucciones detalladas aquí se encuentran típicamente en el código ejecutable producido por compiladores dirigidos a la arquitectura x86/x64. Cuando el sistema operativo cargue y ejecuta un ejecutable, cae al sistema operativo para preparar el entorno de ejecución para el programa. Este proceso involucra tareas como la configuración de la pila, la inicialización de registro y la preparación de estructuras de datos relevantes del sistema.

En términos generales, esta sección del código es parte de la configuración del entorno de ejecución inicial, llevando a cabo las tareas de inicialización relacionadas con el sistema necesarias antes de que se ejecute la lógica principal del programa. El objetivo aquí es garantizar que el programa se lance en un estado consistente, con acceso a los recursos e información del sistema necesarios. Para aclarar, aquí no es donde reside la lógica principal del programa, por lo que necesitamos explorar otras llamadas de funciones para identificar el`main`función.

Volvamos y abramos la segunda subrutina,`sub_401180`, para examinar su contenido.

---

Para retroceder a la función anterior que estábamos examinando, podemos presionar el`Esc`clave en nuestro teclado, o alternativamente, podemos hacer clic en el`Jump Back`botón en la barra de herramientas.

![](https://academy.hackthebox.com/storage/modules/227/ida_go_back.png)

`IDA`nos transportaremos de regreso a la función anterior que estábamos inspeccionando`(loc_4014F4`), llevándonos a donde estábamos antes de cambiar a la función o ubicación actual. Ahora estamos de vuelta en la ubicación anterior, que contiene las instrucciones de llamada a la función actual,`sub_401650`, así como otra función,`sub_401180`.

Desde aquí, podemos colocar el cursor en la instrucción de llamar`sub_401180`y presionar`Enter`.

![](https://academy.hackthebox.com/storage/modules/227/ida_go_back_enter.png)

Esto nos guiará a la función`sub_401180`, donde nos esforzaremos por identificar el`main`función en la que se encuentra la lógica del programa.

![](https://academy.hackthebox.com/storage/modules/227/ida__003_startupinfo.png)

Tras el examen, podemos observar que esta función parece estar implicada en inicializar el`StartupInfo`Estructura y realización de ciertas comprobaciones en relación con su valor. El`rep stosq`La instrucción anula un bloque de memoria, mientras que las instrucciones posteriores modifican el contenido de los registros y ejecutan saltos condicionales según los valores de registro. Esto no parece ser el`main`función en la que reside la lógica del programa, pero contiene algunos`call`instrucciones que potencialmente podrían llevarnos a la`main`función. Investigaremos todos los`call`Instrucciones antes de la devolución de esta función.

Necesitamos desplazarnos al punto final de esta función y comenzar a buscar`call`Instrucciones del One Bottommost.

Al desplazarse hacia arriba desde el punto final de este bloque (donde regresa la función), observamos una llamada a otra subrutina,`sub_403250`, antes de la devolución de esta función.

![](https://academy.hackthebox.com/storage/modules/227/ida_004_intmain.png)

Nuestro objetivo es atravesar las llamadas de la función que preceden a la salida del programa para localizar la función principal, que podría contener el código inicial para la verificación del registro (detección de sandbox) que presenciamos en el monitor de procesos y las cadenas.

Ahora debemos navegar a la función`sub_403250`para investigar su contenido. Para ingresar esta función, debemos colocar el cursor en la instrucción de llamada a continuación:

Código:IDA

```
call    sub_403250

```

Podemos hacer clic derecho en las instrucciones y seleccionar`Jump to Operand`Desde el menú contextual, o alternativamente, podemos presionar el`Enter`llave. Esta acción revelará la función desmontada para`sub_403250`.

![](https://academy.hackthebox.com/storage/modules/227/ida_005_main.png)

Al revisar las instrucciones, parece que la función está consultando el registro del valor asociado con el`SOFTWARE\\VMware, Inc.\\VMware Tools`ruta y realización de una comparación para discernir si VMware Tools está instalado en la máquina. En términos generales, parece probable que este sea el`main`función, que se hizo referencia en el monitor de proceso y las cadenas.

Podemos observar que la consulta del registro se realiza utilizando la función`RegOpenKeyExA`, como se muestra en la instrucción`call cs:RegOpenKeyExA`En el código desmontado que sigue:

Código:IDA

```
xor     r8d, r8d        ; ulOptions
mov     [rsp+148h+cbData], 100h
mov     [rsp+148h+phkResult], rax ; phkResult
mov     r9d, 20019h     ; samDesired
lea     rdx, aSoftwareVmware ; "SOFTWARE\\VMware, Inc.\\VMware Tools"
mov     rcx, 0FFFFFFFF80000002h ; hKey
call    cs:RegOpenKeyExA

```

En el bloque de código anterior, la instrucción final,`call cs:RegOpenKeyExA`, es presumiblemente una representación del`RegOpenKeyExA`llamada de función, precedida por`cs`. La función`RegOpenKeyExA`es una parte de la API de registro de Windows y se utiliza para abrir un mango a una clave de registro especificada. Esta función permite el acceso al registro de Windows. El`A`en el nombre de la función significa que es el`ANSI version`de la función, que opera en cadenas codificadas por ANSI.

En`IDA`, `cs`es un registro de segmento que generalmente se refiere al segmento de código. Cuando hacemos clic en`cs:RegOpenKeyExA`y presionar`Enter`, esta acción nos lleva al`.idata`Sección, que incluye datos relacionados con la importación y la dirección de importación de la función`RegOpenKeyExA`. En este escenario, el`RegOpenKeyExA`La función se importa de una biblioteca externa (advapi32.dll), con su dirección almacenada en el`.idata`sección para uso futuro.

![](https://academy.hackthebox.com/storage/modules/227/ida_winapi.png)

Código:IDA

```
.idata:0000000000409370 ; LSTATUS (__stdcall *RegOpenKeyExA)(HKEY hKey, LPCSTR lpSubKey, DWORD ulOptions, REGSAM samDesired, PHKEY phkResult)
.idata:0000000000409370                 extrn RegOpenKeyExA:qword
.idata:0000000000409370                                         ; CODE XREF: sub_403160+3E↑p
.idata:0000000000409370                                         ; sub_403220+3C↑p
.idata:0000000000409370                                         ; DATA XREF: ...

```

Esta no es la dirección real del`RegOpenKeyExA`función, sino más bien la dirección de la entrada en el`IAT (Import Address Table)`para`RegOpenKeyExA`. La entrada IAT alberga la dirección que se resolverá dinámicamente en tiempo de ejecución para señalar la implementación de la función real en el DLL respectivo (en este caso, advapi32.dll).

La línea`extrn RegOpenKeyExA:qword`indica que`RegOpenKeyExA`es un símbolo externo a resolver en tiempo de ejecución. Esto alerta al ensamblador de que la función se define en otro módulo o biblioteca, y el enlazador manejará la resolución de su dirección durante el proceso de enlace.

[Referencia: https://learn.microsoft.com/en-us/windows/win32/debug/pe-format#import-address-table](https://learn.microsoft.com/en-us/windows/win32/debug/pe-format#import-address-table)

En realidad,`cs:RegOpenKeyExA`es un medio para acceder al`IAT`entrada para`RegOpenKeyExA`en el segmento de código utilizando una referencia relativa. La dirección real de`RegOpenKeyExA`Será resuelto y almacenado en el IAT durante el tiempo de ejecución por el enlazador/cargador dinámico del sistema operativo.

Según la estructura general de esta función, podemos conjeturar que este es lo posible`main`función. Vamos a cambiarlo a`assumed_Main`Para recordar fácilmente en caso de que encontremos referencias a esta función en el futuro.

---

Cambiar el nombre de una función en`IDA`, debemos proceder de la siguiente manera:

- Coloque el cursor en el nombre de la función (`sub_403250`) o la línea que contiene la definición de función. Entonces, presione el`N`clave en el teclado, o haga clic con el botón derecho y seleccione`Rename`Desde el menú contextual.
- Ingrese el nuevo nombre para la función y presione`Enter`.

IDA actualizará el nombre de la función a lo largo de la vista de desmontaje y cualquier referencia a la función dentro del binario.

**Nota**: Renombrar una función en`IDA`no modifica el archivo binario real. Solo altera la representación dentro`IDA`Análisis.

![](https://academy.hackthebox.com/storage/modules/227/ida_006_assumed_main.png)

Vamos a profundizar ahora en las instrucciones presentes en este bloque de código.

Podemos identificar dos llamadas de función que emanen de esta función (`sub_401610`y`sub_403110`) antes de llamar a la función API de Windows`RegOpenKeyExA`. Examinemos ambos antes de avanzar a las funciones de Winapi.

Profundicemos en estas funciones dirigiendo el cursor a sus respectivos`call`Instrucciones y tapping`Enter`para vislumbrarlo.

Comience examinando el código desmontado para la primera subrutina`sub_401610`. Iniciar el viaje hacia la subrutina presionando`Enter`en la instrucción de llamadas para`sub_401610`.

![](https://academy.hackthebox.com/storage/modules/227/ida_enter_init_checks_.png)

Nos encontramos en la primera subrutina`sub_401610`, que examina el valor de una variable (`cs:dword_408030`). Si su valor es cero, se redefine como uno. Posteriormente redirige a`sub_4015A0`.

![](https://academy.hackthebox.com/storage/modules/227/ida_007_init_checks.png)

Los siguientes detalles de instrucciones`sub_401610`. Se esfuerzemos por comprender sus matices.

Código:IDA

```
sub_401610 proc near

mov     eax, cs:dword_408030
test    eax, eax
jz      short loc_401620

loc_401620:
mov     cs:dword_408030, 1
jmp     sub_4015A0
sub_401610 endp

```

Inicia transferir el valor de la variable`dword_408030`en el`eax`registro. Luego realiza un`bitwise AND`operación con`eax`y en sí mismo, esencialmente evaluando si el valor es`zero`. Si el resultado de la instrucción de prueba anterior considera`eax`como`zero`, redirige a`sub_4015A0`. Diseccionemos más su código.

Código:IDA

```
sub_4015A0 proc near

push    rsi
push    rbx
sub     rsp, 28h
mov     rdx, cs:off_405730
mov     rax, [rdx]
mov     ecx, eax
cmp     eax, 0FFFFFFFFh
jz      short loc_4015F0

```

Presionando`Enter`mientras que el cursor está en el nombre de la función`sub_4015A0`, navegamos al código desmontado, revelando que la función comienza presionando los valores del`rsi`y`rbx`se registra en la pila, preservando los valores de registro. Posteriormente, asigna espacio en la pila restando`28h (40 decimal)`bytes del puntero de la pila (`rsp`). Luego recupera un puntero de función de la dirección encapsulada en`off_405730`y lo guarda en el`rax`registro.

En esencia, parecen ejecutar las verificaciones de inicialización y las operaciones relacionadas con los punteros de la función antes de que el programa proceda a llamar a la segunda subrutina`sub_403110`y la función Winapi para operaciones de registro. Esta no es la función principal real que aloja la lógica del programa, por lo que analizaremos otras llamadas de función para identificar el`main`función.

Podemos cambiar el nombre de esta función como`initCheck`para nuestro recuerdo presionando`N`y escribiendo el nuevo nombre de la función.

En este punto, o presionamos el`Esc`clave o seleccione el`Jump Back`Botón en la barra de herramientas para volver a la segunda subrutina`sub_403110`y explorar su funcionamiento interno.

Una vez que hemos vuelto a la función anterior (`assumed_Main`), debemos colocar el cursor en el`call sub_403110`instrucción y golpe`Enter`.

![](https://academy.hackthebox.com/storage/modules/227/correction.png)

Esta transición nos lleva en el código desmontado para esta función. Vamos a examinarlo para determinar su funcionamiento.

![](https://academy.hackthebox.com/storage/modules/227/ida_008_shellexecutea.png)

Las variables`Parameters`, `File`y`Operation`son variables de cadena guardadas en el`.rdata`sección del ejecutable. El`lea`Las instrucciones se utilizan para obtener las direcciones de memoria de estas cadenas, que posteriormente se pasan como argumentos al`ShellExecuteA`función. Este bloque de código es responsable de un`sleep`duración de`5 seconds`. Después de eso, vuelve a la función anterior. Habiendo entendido el código, podemos cambiar el nombre de esta función como`pingSleep`Al hacer clic con el botón derecho y eligiendo el cambio de nombre.

Ahora que hemos encontrado algunas referencias para las funciones de la API de Windows, aclaremos cómo se interpretan las funciones de Winapi en el código desmontado.

---

Después de investigar las operaciones dentro de las dos llamadas de función (`sub_401610`y`sub_403110`) desde esta función y antes de invocar la función de la API de Windows`RegOpenKeyExA`, inspeccionemos las llamadas realizadas a la función de Winapi`RegOpenKeyExA`. En esto`IDA`Vista de desmontaje, los argumentos pasados a la llamada de función de Winapi se representan por encima del`call`instrucción. Esta convención estándar en Disimblars ofrece una representación lúcida de la llamada de función junto con sus argumentos correspondientes.

La función API de Windows,`RegOpenKeyExA`, se utiliza aquí para desbloquear una clave de registro. La sintaxis de esta función, según la documentación de Microsoft, se presenta a continuación.

Código:IDA

```
LSTATUS RegOpenKeyExA(
  [in]           HKEY   hKey,
  [in, optional] LPCSTR lpSubKey,
  [in]           DWORD  ulOptions,
  [in]           REGSAM samDesired,
  [out]          PHKEY  phkResult
);

```

![](https://academy.hackthebox.com/storage/modules/227/msdn_001_regopenkey.png)

Deconstruyamos el código para esta función como aparece en el`IDA`Vista desmontada.

Código:IDA

```
lea     rax, [rsp+148h+hKey]      ; Calculate the address of hKey
xor     r8d, r8d                  ; Clear r8d register (ulOptions)
mov     [rsp+148h+phkResult], rax ; Store the calculated address of hKey in phkResult
mov     r9d, 20019h               ; Set samDesired to 0x20019h (which is KEY_READ in MS-DOCS)
lea     rdx, aSoftwareVmware      ; Load address of string "SOFTWARE\\VMware, Inc.\\VMware Tools"
mov     rcx, 0FFFFFFFF80000002h   ; Set hKey to 0xFFFFFFFF80000002h (HKEY_LOCAL_MACHINE)
call    cs:RegOpenKeyExA          ; Call the RegOpenKeyExA function
test    eax, eax                  ; Check the return value
jnz     short loc_40330F          ; Jump if the return value is not zero (error condition)

```

El`lea`La instrucción calcula la dirección del`hKey`variable, presumiblemente un mango de una clave de registro. Entonces,`mov rcx, 0FFFFFFFF80000002h`empuje`HKEY_LOCAL_MACHINE`Como el primer argumento (`rcx`) a la función. El`lea rdx, aSoftwareVmware`La instrucción emplea el`load effective address (LEA)`operación para calcular la dirección efectiva de la ubicación de memoria que almacena la cadena`Software\\VMware, Inc.\\VMware Tools`. Esta dirección calculada se guarda en el`rdx`Regístrese, el segundo argumento de la función.

El tercer argumento a esta función se pasa a la`r8d`Regístrese a través de la instrucción`xor r8d, r8d`que vacía el`r8d`Regístrese implementando un`XOR`operación consigo misma, restableciéndolo efectivamente a cero. En el contexto de este código, indica que el tercer argumento (`ulOptions`) pasó al`RegOpenKeyExA`la función tiene un valor de`0`.

El cuarto argumento es`mov r9d, 20019h`, correspondiente a`KEY_READ`en[MS-Docs](https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry-key-security-and-access-rights).

El quinto argumento,`phkResult`, está en la pila. Al agregar`rsp+148h`al puntero de la pila base`rsp`, el código accede a la ubicación de memoria en la pila donde el`phkResult`El parámetro reside. El`mov [rsp+148h+phkResult], rax`La instrucción duplica el valor de`rax`(que contiene la dirección de`hKey`) a la ubicación de memoria señalada por`phkResult`, esencialmente almacenar la dirección de`hKey`en`phkResult`(que se pasa a la siguiente función como el primer argumento).

A partir de este punto, cada vez que nos topamos con una referencia de función de Winapi en el código, recurriremos a la documentación de Microsoft para que esa función comprenda su sintaxis, parámetros y el valor de retorno. Esto nos ayudará a comprender los valores probables en los registros cuando se invocan estas funciones.

¿Deberíamos desplazarnos hacia abajo por la vista del gráfico, encontramos la próxima función de Winapi?`RegQueryValueExA`que recupera el tipo y los datos para el nombre de valor especificado asociado con una clave de registro abierto. Se compara los datos clave, y en una coincidencia, un cuadro de mensaje que indica`Sandbox Detected`se muestra. Si no coincide, entonces redirige a otra subrutina`sub_402EA0`. También rectificaremos esta detección de sandbox en el depurador más tarde. La imagen a continuación describe el flujo general de esta operación.

![](https://academy.hackthebox.com/storage/modules/227/ida_009_after_sandbox.png)

Presionemos`Enter`En la próxima instrucción de llamadas para la función`sub_402EA0`para permitirnos analizar esta subrutina y descubrir sus operaciones.

![](https://academy.hackthebox.com/storage/modules/227/ida_010_getaddrinfo.png)

Al presionar`Enter`, descubrimos su funcionalidad. Esta subrutina parece ejecutar operaciones relacionadas con la red utilizando el`Windows Sockets API (Winsock)`. Inicialmente invoca el`WSAStartup`función para configurar el`Winsock`biblioteca, luego llama al`WSAAPI`función`getaddrinfo`que se utiliza para obtener información de dirección para el nombre del nodo especificado (`pNodeName`) basado en las sugerencias proporcionadas`pHints`. La subrutina verifica el éxito de la resolución de la dirección utilizando el`getaddrinfo`función.

Si el`getaddrinfo`la función produce un valor de retorno de`zero`(indicando éxito), esto implica que la dirección se ha resuelto con éxito a una IP. Después de este evento, si es realmente exitoso, la secuencia salta a un cuadro de mensajes que se muestra`Sandbox detected`. Si no, dirige el flujo a la subrutina`sub_402D00`.

Posteriormente, provoca la invocación del`WSACleanup`función. Esta acción inicia la limpieza de recursos relacionados con`Winsock`, independientemente de si el proceso de resolución de direcciones fue exitoso o no exitoso. En aras de la claridad, bautizaremos esta función como`DomainSandboxCheck`.

**Posible IOC**: Tenga en cuenta el nombre de dominio`iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea[.]com`Como componente del potencial`IOC`s.

Para explorar las consecuencias de evitar la verificación de sandbox, profundizaremos en la subrutina`sub_402D00`. Podemos analizar esta subrutina golpeando`Enter`en el consecuente`call`instrucción relacionada con el`sub_402D00`función. Una imagen adjunta a continuación muestra el código desmontado para esta función.

![](https://academy.hackthebox.com/storage/modules/227/ida_011_func_check.png)

Esta función primero reserva el espacio en la pila para variables locales antes de llamar`sub_402C20`, una función distinta. La salida de esta función se almacena dentro del`eax`registro. Dependiendo de los resultados derivados del`sub_402C20`función, la secuencia regresa (`retn`) o salta a`sub_402D20`.

En consecuencia, seleccionaremos la primera función resaltada,`sub_402C20`, presionando`Enter`para examinar sus instrucciones. Tras un análisis exhaustivo de`sub_402C20`, volveremos a este bloque para evaluar la segunda función resaltada,`sub_402D20`.

![](https://academy.hackthebox.com/storage/modules/227/ida_012_socketconnect.png)

Al golpear`Enter`, Somos recibidos con sus instrucciones retratadas en la imagen de arriba. Esta función inicia el`Winsock`biblioteca, genera un socket y se conecta a la dirección IP`45.33.32.156`Vía puerto`31337`. Evalúa el valor de retorno (`eax`) para determinar si la conexión fue exitosa. Sin embargo, hay un giro; Invocación posterior a la función, la instrucción`inc eax`incrementa el`eax`Registrarse el valor de`1`. Posterior al`inc eax`instrucción, el código evalúa el valor de`eax`usando el`jnz (jump if not zero)`instrucción.

Si la conexión al puerto mencionado y la dirección IP falla, esta función debe devolver`-1`, como se especifica en la documentación.

![](https://academy.hackthebox.com/storage/modules/227/ida_013_socketerror.png)

Código:IDA

```
call    cs:connect
inc     eax
jnz     short loc_402CD0

```

Dado que`eax`se incrementa por`1`Llamada posterior a la función, esto debería reducirse a`0`. En consecuencia, el`MessageBox`imprimirá`Sandbox detected`. Esto implica que la función está examinando el estado de la conexión a Internet.

![](https://academy.hackthebox.com/storage/modules/227/ida_014_jump_check.png)

Si, por otro lado, la conexión es exitosa, producirá un`non-zero`valor, lo que solicita que el código salte a`loc_402CD0`. Esta ubicación alberga una llamada a otra función,`sub_402F40`. Con una comprensión clara de las operaciones de esta función, la cambiaremos como`InternetSandboxCheck`.

**Posible IOC**: Recuerde anotar esta dirección IP`45.33.32.156`y puerto`31337`como componentes del potencial`IOC`s.

A continuación, procederemos a funcionar`sub_402F40`para descifrar sus operaciones. Podemos hacer esto haciendo clic derecho y seleccionando`Jump to Operand`, o presionando`Enter`en su`call`instrucción.

![](https://academy.hackthebox.com/storage/modules/227/ida_015_svchost.png)

Esta función requiere el`getenv`función (con`rcx`actuando como el pasador de argumento para`TEMP`) y guarda su resultado en el`eax`registro. Esta acción recupera el`TEMP`Valor de la variable de entorno.

Código:IDA

```
lea     rcx, VarName    ; "TEMP"
call    getenv

```

Para verificar la salida, podemos usar PowerShell para imprimir el`TEMP`Valor de la variable de entorno.

Análisis de código

```powershell
PS C:\> Get-ChildItem env:TEMP

Name                           Value
----                           -----
TEMP                           C:\Users\htb-student\AppData\Local\Temp

```

Luego emplea el`sprintf`función para agregar el obtenido`TEMP`camino a la cadena`svchost.exe`, produciendo una ruta de archivo completa. A partir de entonces, el`GetComputerNameA`Se llama a la función para recuperar el nombre de la computadora, que luego se almacena en un búfer.

Si el nombre de la computadora es inexistente, se salta a la etiqueta`loc_4030F8`(que alberga instrucciones para regresar). Por el contrario, si el nombre de la computadora no está vacío (`non-zero`valor), el código progresa a la instrucción posterior como se muestra en el lado izquierdo de la imagen.

![](https://academy.hackthebox.com/storage/modules/227/ida_016_svchost_download.png)

En las instrucciones posteriores, encontramos una llamada a la función`sub_403220`. Podemos acceder a él haciendo doble clic en el nombre de la función.

El lado izquierdo de la imagen adjunta arriba muestra la función`sub_403220`, que formatea una cadena que alberga una personalización`user-agent`valor con la cadena`Windows-Update/7.6.7600.256 %s`. El`%s`El marcador de posición se reemplaza con el nombre de la computadora obtenido previamente, que se transmite a esta función en el`rcx`registro.

![](https://academy.hackthebox.com/storage/modules/227/ida_016_svchost_temp.png)

Ahora, el valor completo se lee`Windows-Update/7.6.7600.256 HOSTNAME`, dónde`HOSTNAME`es el resultado de`GetComputerNameA`(el nombre de la computadora).

Es crucial notar esta costumbre única`user-agent`, en el que el nombre de host también se transmite en la solicitud cuando el malware inicia una conexión de red.

Volver a la función anterior, posteriormente llama al`InternetOpenA`Función de Winapi para comenzar una sesión de acceso a Internet y configurar los parámetros para el`InternetOpenUrlA`función. Luego procede a llamar a este último para abrir la URL`http://ms-windows-update.com/svchost.exe`.

**Posible IOC**: Tenga en cuenta esta URL`http[:]//ms-windows-update[.]com/svchost[.]exe`como potencial`IOC`. El malware está descargando un ejecutable adicional de esta ubicación.

Si la URL se abre con éxito, el código salta a la etiqueta`loc_40301E`. Probemos las instrucciones en`loc_40301E`haciendo doble clic en él.

![](https://academy.hackthebox.com/storage/modules/227/ida_017_createfile.png)

Al abrir la función, observamos una llamada a la función de la API de Windows`CreateFileA`, que se utiliza para generar un archivo en el sistema local, designando la ruta de archivo obtenida previamente.

El código luego ingresa a un bucle, invocando repetidamente el`InternetReadFile`función para extraer datos de la URL abierta`http[:]//ms-windows-update[.]com/svchost[.]exe`. Si la operación de lectura de datos es exitosa, el código avanza para escribir los datos recibidos en el archivo creado (`svchost.exe`ubicado en el`TEMP`directorio) usando el`WriteFile`función.

Tenga en cuenta esta técnica única, donde el malware descarga y deposita un archivo ejecutable`svchost.exe`en el`temp`directorio.

El bucle mencionado anteriormente se ilustra en la imagen a continuación.

![](https://academy.hackthebox.com/storage/modules/227/ida_018_readfile.png)

Después de la operación de redacción de datos, el código se vuelve en bicicleta para leer más datos hasta el`InternetReadFile`La función devuelve un valor que indica el final del flujo de datos. Una vez que todos los datos han sido leídos y escritos, el archivo abierto y las manijas de Internet se cierran utilizando las funciones apropiadas (`CloseHandle`y`InternetCloseHandle`). Posteriormente, el código salta a`loc_4030D3`, donde recurre a la función`sub_403190`.

Hacemos hacer sonar dos veces`sub_403190`para presentar su contenido.

![](https://academy.hackthebox.com/storage/modules/227/ida_018_writefile.png)

La función`sub_403190`ahora está expuesto, revelando una serie de llamadas de Winapi relacionadas con modificaciones de registro, como`RegOpenKeyExA`y`RegSetValueExA`.

![](https://academy.hackthebox.com/storage/modules/227/ida_19_reg.png)

Parece que esta función coloca el archivo (`svchost.exe`ubicado en el`TEMP`directorio) en la ruta clave del registro`SOFTWARE\Microsoft\Windows\CurrentVersion\Run`con el nombre del valor`WindowsUpdater`, luego sella la clave de registro. Esta técnica es empleada con frecuencia por malware y aplicaciones legítimas para mantener su control sobre el sistema a través de los reinicios, asegurando la operación automática cada vez que el sistema se inicia o un usuario inicia sesión. Hemos tomado la libertad de renombrar esta función en`IDA`a`persistence_registry`En aras de la claridad.

![](https://academy.hackthebox.com/storage/modules/227/ida_020_regvalue.png)

**Posible IOC**: Destaca esta técnica en la que el malware modifica el registro para lograr la persistencia. Lo hace agregando una entrada para`svchost.exe`bajo el`WindowsUpdater`nombre en el`SOFTWARE\Microsoft\Windows\CurrentVersion\Run`Clave de registro.

Al establecer el registro, inicia otra función,`sub_403150`, que establece en movimiento el archivo retirado`svchost.exe`y me funns un argumento. Una búsqueda rudimentaria en Google sugiere que este argumento podría ser un`Bitcoin wallet address`. Por lo tanto, es razonable postular que el ejecutable caído podría ser un minero de monedas.

Al rebobinar nuestros pasos e inspeccionar las funciones sistemáticamente, podemos identificar cualquier función residual que aún no hemos analizado. El`Esc`clave o el`Jump Back`El botón en la barra de herramientas facilita este seguimiento inverso.

![](https://academy.hackthebox.com/storage/modules/227/ida_021_pendingfunction.png)

Después de rastrear el código analizado, hemos llegado a este bloque, donde una subrutina`sub_402D20`está pendiente para el análisis. Así que hagamos doble clic para abrirlo y ver qué hay dentro de él.

![](https://academy.hackthebox.com/storage/modules/227/ida_022_notepad.png)

Al abrir la subrutina, está claro que está configurando los parámetros necesarios para el`CreateProcessA`función para generar un nuevo proceso. Luego procede a instigar un nuevo proceso,`notepad.exe`, situado en el`C:\Windows\System32`directorio.

Aquí está la sintaxis para la función Createprocessa.

Código:IDA

```
BOOL CreateProcessA(
  [in, optional]      LPCSTR                lpApplicationName,
  [in, out, optional] LPSTR                 lpCommandLine,
  [in, optional]      LPSECURITY_ATTRIBUTES lpProcessAttributes,
  [in, optional]      LPSECURITY_ATTRIBUTES lpThreadAttributes,
  [in]                BOOL                  bInheritHandles,
  [in]                DWORD                 dwCreationFlags,
  [in, optional]      LPVOID                lpEnvironment,
  [in, optional]      LPCSTR                lpCurrentDirectory,
  [in]                LPSTARTUPINFOA        lpStartupInfo,
  [out]               LPPROCESS_INFORMATION lpProcessInformation
);

```

Con`rdx`observado en el código, vemos que el segundo argumento a esta función está identificado como`C:\\Windows\\System32\\notepad.exe`.

![](https://academy.hackthebox.com/storage/modules/227/ida_createProcess.png)

Observamos en el`CreateProcessA`documentación de funciones que un`nonzero`El valor de retorno indica una ejecución exitosa de la función. En consecuencia, si tiene éxito, no saltará a`loc_402E89`pero continuará al siguiente bloque de instrucciones.

![](https://academy.hackthebox.com/storage/modules/227/ida_023_procInj.png)

El posterior bloque de instrucciones sugiere un tipo común de inyección de proceso, en el que`shellcode`se inserta en el proceso recién creado utilizando`VirtualAllocEx`, `WriteProcessMemory`, y`CreateRemoteThread`funciones.

Desciamos la inyección del proceso basada en nuestras observaciones del código.

Un nuevo`notepad.exe`El proceso se fabrica a través del`CreateProcessA`función. Después de esto, la memoria se asigna dentro de este proceso utilizando`VirtualAllocEx`. El`shellcode`Luego se inscribe en la memoria asignada del proceso remoto`notepad.exe`Usando la función Winapi`WriteProcessMemory`. Por último, se establece un hilo remoto en`notepad.exe`, iniciando el`shellcode`ejecución a través del`CreateRemoteThread`función.

Si la inyección es triunfante, se manifiesta un cuadro de mensaje, declarando`Connection sent to C2`. Por el contrario, un mensaje de error aparece en caso de falla.

![](https://academy.hackthebox.com/storage/modules/227/ida_023_conn_sent.png)

En aras de la facilidad, cambiemos el nombre de la función`sub_402D20`como`process_Injection`.

Al principio de esta función, podemos detectar una dirección desconocida`unk_405057`, cuya dirección efectiva se carga en el`rsi`Regístrese a través de la instrucción`lea rsi, unk_405057`. Ejecutado antes de las funciones de Winapi llaman a la inyección del proceso, la razón para cargar la dirección efectiva en`rsi`podría ser múltiple: podría funcionar como un puntero de acceso de datos o como un argumento de llamada de función. Sin embargo, existe la posibilidad de que esta dirección alberga potencial`shellcode`. Verificaremos esto cuando`debugging`Estas funciones de Winapi utilizan un depurador como`x64dbg`.

![](https://academy.hackthebox.com/storage/modules/227/ida_024_shellcode.png)

Al analizar y renombrar esta función de inyección del proceso, continuaremos volviendo sobre nuestros pasos a las funciones anteriores para garantizar que no se haya pasado por alto ninguna función.

![](https://academy.hackthebox.com/storage/modules/227/ida_025_backtomain.png)

`IDA`también ofrece una característica que visualiza el flujo de ejecución entre funciones en un ejecutable a través de un`call flow graph`. Esta potente herramienta visual ayuda a los analistas a navegar y comprender el flujo de control y las interacciones entre las funciones.

Aquí le mostramos cómo generar y examinar el gráfico para identificar los enlaces entre diferentes funciones:

- Cambie a la vista de desmontaje.
- Localizar el`View`menú en la parte superior del`IDA`interfaz.
- Flotar sobre el`Graphs`opción.
- Desde el submenú, elija`Function calls`.

![](https://academy.hackthebox.com/storage/modules/227/ida_graph_flow.png)

`IDA`Luego forjará el gráfico de flujo de llamadas de función para todas las funciones en el binario y lo presentará en una nueva ventana. Este gráfico ofrece una descripción general de las llamadas realizadas entre las diversas funciones en el programa, lo que nos permite analizar el flujo de control y las dependencias entre las funciones. Un ejemplo de cómo aparece este gráfico se muestra en la captura de pantalla a continuación.

![](https://academy.hackthebox.com/storage/modules/227/ida_026_callflowgraph.png)

Al contrario de ver el gráfico de relaciones para todas las llamadas de funciones, también podemos centrarnos en funciones específicas. Para generar el gráfico de referencia para el flujo de llamadas de función relacionada con una función específica, se pueden seguir estos pasos.

- Navegue a la función cuyo gráfico de flujo de llamadas de función deseamos examinar.
- Para abrir la función en la vista de desmontaje, haga doble clic en el nombre de la función o presione`Enter`.
- En la vista de desmontaje, haga clic con el botón derecho en cualquier lugar y opte por cualquiera`Xrefs graph to...`o`Xrefs graph from...`, según si queremos observar las llamadas de función realizadas por la función seleccionada o las llamadas de función que conduce a la función seleccionada.
- `IDA`Elaborará el gráfico de flujo de llamadas de función y lo exhibirá en una nueva ventana.

Mirando hacia el futuro, profundizaremos en la depuración en la sección posterior. Allí, lanzaremos el ejecutable dentro de un depurador y estableceremos puntos de interrupción sobre las instrucciones requeridas y seleccione las funciones críticas de Winapi. Esta estrategia nos permite comprender y administrar el flujo de ejecución en tiempo real a medida que opera el programa.