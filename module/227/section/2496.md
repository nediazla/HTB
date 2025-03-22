# Debugging

La depuración agrega una capa dinámica e interactiva al análisis de código, ofreciendo una visión en tiempo real del comportamiento de malware. Empodera a los analistas para confirmar sus descubrimientos, testigos de impactos en tiempo de ejecución y profundizar su comprensión de la ejecución del programa. La unión del análisis de código y la depuración permite una comprensión integral del malware, lo que lleva a la exposición efectiva del comportamiento dañino.

Podríamos desplegar un depurador como`x64dbg`, una herramienta fácil de usar adaptada para analizar y depurar ejecutables de Windows de 64 bits. Viene equipado con una interfaz gráfica para visualizar el código desmontado, implementar puntos de interrupción, examinar la memoria y los registros y controlar la ejecución de programas.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Depuración

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

Aquí le mostramos cómo ejecutar una muestra dentro`x64dbg`para familiarizarse con sus operaciones.

- Lanzamiento`x64dbg`.
- En la parte superior del`x64dbg`interfaz, haga clic en el`File`menú.
- Seleccionar`Open`Para elegir el archivo ejecutable, deseamos depurar.
- Explore al directorio que contiene el ejecutable y seleccione.
- Opcionalmente, los argumentos de línea de comandos o el directorio de trabajo se pueden especificar en el cuadro de diálogo que aparece.
- Hacer clic`OK`Para cargar el ejecutable en`x64dbg`.

Al abrir, la ventana predeterminada se detiene en un punto de interrupción predeterminado en el punto de entrada del programa.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_001_views.png)

Cargando un ejecutable en`x64dbg`Revela la visión de desmontaje, mostrando las instrucciones de ensamblaje del programa, ayudando así a comprender el flujo de código. A la derecha, la ventana de registro divulga los valores de los registros de CPU, arrojando luz sobre el estado del programa. Debajo de la ventana de registro, la vista de pila muestra el marco de pila actual, lo que permite la inspección de llamadas de función y variables locales. Por último, en la esquina inferior izquierda, encontramos la vista de volcado de memoria, que proporciona una representación pictórica de la memoria del programa, facilitando el análisis de estructuras y variables de datos.

# **Simulando los servicios de Internet**

El papel de`INetSim`Al simular los servicios típicos de Internet en nuestro entorno de prueba restringido es fundamental. Ofrece soporte para una multitud de servicios, abarcando`DNS`, `HTTP`, `FTP`, `SMTP`, entre otros. Podemos ajustarlo para reproducir respuestas específicas, lo que permite un examen más personalizado del comportamiento del malware. Nuestro enfoque implicará mantener`InetSim`operativo para que pueda interceptar cualquier`DNS`, `HTTP`u otras solicitudes que emanan de la muestra de malware (`shell.exe`), proporcionándole así respuestas sintéticas controladas.

**Nota**: Se recomienda encarecidamente que usemos su propia VM/máquina para ejecutar`InetSim`. Nuestra VM/Machine debe conectarse a VPN utilizando el archivo de configuración VPN proporcionado que reside al final de esta sección.

Deberíamos configurar`INetSim`como sigue.

Depuración

```
xnoxos@htb[/htb]$ sudo nano /etc/inetsim/inetsim.conf
```

El siguiente debe ser sin comodidad y especificado.

Depuración

```
service_bind_address <Our machine's/VM's TUN IP>
dns_default_ip <Our machine's/VM's TUN IP>
dns_default_hostname www
dns_default_domainname iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com

```

Iniciativo`INetSim`implica ejecutar el siguiente comando.

Depuración

```
xnoxos@htb[/htb]$ sudo inetsim INetSim 1.3.2 (2020-05-19) by Matthias Eckert & Thomas Hungenberg
Using log directory:      /var/log/inetsim/
Using data directory:     /var/lib/inetsim/
Using report directory:   /var/log/inetsim/report/
Using configuration file: /etc/inetsim/inetsim.conf
Parsing configuration file.
Configuration file parsed successfully.
=== INetSim main process started (PID 34711) ===
Session ID:     34711
Listening on:   0.0.0.0
Real Date/Time: 2023-06-11 00:18:44
Fake Date/Time: 2023-06-11 00:18:44 (Delta: 0 seconds)
 Forking services...
  * dns_53_tcp_udp - started (PID 34715)
  * smtps_465_tcp - started (PID 34719)
  * pop3_110_tcp - started (PID 34720)
  * smtp_25_tcp - started (PID 34718)
  * http_80_tcp - started (PID 34716)
  * ftp_21_tcp - started (PID 34722)
  * https_443_tcp - started (PID 34717)
  * pop3s_995_tcp - started (PID 34721)
  * ftps_990_tcp - started (PID 34723)
 done.
Simulation running.

```

Un recurso más elaborado para configurar`INetSim`es lo siguiente:[https://medium.com/@xnymia/malware-analysis-first-steps-creating-your-lab-21b769fb2a64](https://medium.com/@xNymia/malware-analysis-first-steps-creating-your-lab-21b769fb2a64)

Finalmente, el DNS del Target del objetivo se debe apuntar a la máquina/VM donde`INetSim`está corriendo.

![](https://academy.hackthebox.com/storage/modules/227/point_.png)

# **Aplicando los parches para evitar las verificaciones de sandbox**

Dado que las verificaciones de Sandbox obstaculizan la ejecución directa del malware en la máquina, necesitamos parchear estas verificaciones para eludir la detección de sandbox. Así es como podemos esquivar las comprobaciones de detección de sandbox mientras depuramos con`x64dbg`. Varios métodos pueden llevarnos a las instrucciones donde se realiza la detección de sandbox. Discutiremos algunos de estos.

### **Copiando la dirección de IDA**

Durante el análisis del código, observamos la verificación de detección de sandbox relacionada con la clave del registro. Podemos extraer la dirección del primero`cmp`instrucción directamente de`IDA`.

Para encontrar la dirección, volvamos a las ventanas de Ida, abra la primera función que habíamos cambiado el nombre como`assumed_Main`, y busca el`cmp`instrucción. Para ver las direcciones, podemos hacer la transición de la vista gráfica a la vista de texto presionando el botón de la barra espaciadora.

Esto expone la dirección (como se destaca en la siguiente captura de pantalla)

Podemos copiar la dirección`00000000004032C8`de`IDA`.

Código:IDA

```
.text:00000000004032C8                 cmp     [rsp+148h+Type], 1

```

En`x64dbg`, podemos hacer clic derecho en cualquier lugar de la vista de desmontaje (CPU) y seleccionar`Go to` > `Expression`. Alternativamente, podemos presionar`Ctrl+G`(ir a expresión) como un atajo.

Podemos ingresar la dirección copiada aquí, como se muestra en la captura de pantalla. Esto nos navega a la instrucción de comparación donde podemos implementar cambios.

![](https://academy.hackthebox.com/storage/modules/227/ida_027_addresscp.png)

### **Buscando a través de las cuerdas.**

Veamos`Sandbox detected`en el`String references`, y establecer un`breakpoint`, de modo que cuando llegamos a la ejecución, la ejecución debe detenerse en este punto.

Para hacer esto, primero haga clic en el`Run`Botón una vez y luego haga clic con el botón derecho en cualquier lugar de la vista de desmontaje y elija`Search for` > `Current Module` > `String references`.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_002_strings_.png)

A continuación, podemos agregar un punto de interrupción para marcar la ubicación, luego estudiar las instrucciones antes de este sandbox`MessageBox`para discernir cómo se realizó el salto a la impresión de instrucciones`Sandbox detected`.

Comencemos agregando un punto de interrupción en el último`Sandbox detected`cadena de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_002_strings__.png)

Luego podemos hacer doble clic en la cadena para ir a la dirección donde las instrucciones de imprimir`Sandbox detected`están ubicados.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_003_cmp.png)

Como se observó, un`cmp`La instrucción está presente sobre esto`MessageBox`que compara el valor con`1`Después de que se haya realizado una comparación de ruta de registro. Modificamos este valor de comparación para que coincida con`0`en cambio. Esto se puede hacer colocando el cursor en esa instrucción y presionando`Spacebar`en el teclado. Esto nos permite editar las instrucciones del código de ensamblaje.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_004_assemble_patch1.png)

Podemos cambiar el valor de comparación de`0x1`a`0x0`. Cambiar la comparación con`0`puede cambiar el flujo de control del código, y no debe saltar a la dirección donde`MessageBox`se muestra.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_004_patch1.png)

Al hacer clic en`Run`en`x64dbg`o presionando`F9`, no alcanzará el punto de interrupción para el primer código de mensaje de detección de sandbox. Esto significa que parcheamos con éxito las instrucciones.

De manera similar, podemos agregar un punto de interrupción en la siguiente función de detección de sandbox antes de imprimir un`MessageBox`también. Para hacer eso, el punto de interrupción debe colocarse en el`second to last` `Sandbox detected`cadena (`0000000000402F13`). Si hagamos doble clic en esta cadena, notaremos que hay un`jump`Instrucción que podemos omitir, dirigiendo el flujo de ejecución a la siguiente instrucción que llama a otra función. Eso es exactamente lo que necesitamos, en lugar de la detección de sandbox`MessageBox`, salta a otra función.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_005_patch2.png)

Podemos alterar la instrucción de`je shell.402F09`a`jne shell.402F09`.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_006_jne.png)

`shell.exe`Realiza la detección de sandbox al verificar la conectividad a Internet. El objetivo de esta sección no tiene conectividad a Internet. Por esta razón, también debemos parchear este método de detección de sandbox. Podemos hacerlo haciendo clic en el primero`Sandbox detected`cadena (`0000000000402CBD`) y parchear la siguiente instrucción.

![](https://academy.hackthebox.com/storage/modules/227/patch3.png)

![](https://academy.hackthebox.com/storage/modules/227/patch3_.png)

Ahora, cuando presionamos`Run`, el parcheado`shell.exe`procede más, descarga el ejecutable predeterminado desde`INetSim`, y lo ejecuta.

![](https://academy.hackthebox.com/storage/modules/227/both_messagebox.png)

Con las verificaciones de sandbox omitidas, se presenta la funcionalidad real. Podemos guardar el ejecutable parcheado presionando`Ctrl+P`y hacer clic en`Patch File`. Esta acción almacena el archivo parcheado, que omite las verificaciones de sandbox.

![](https://academy.hackthebox.com/storage/modules/227/patch3__.png)

Realizamos este proceso para asegurarnos de que la próxima vez que ejecutemos el archivo parchado guardado, se ejecute directamente sin las verificaciones de Sandbox, y podemos observar todos los eventos en`ProcessMonitor`.

# **Análisis del tráfico de malware**

Tenga en cuenta que el análisis de tráfico no solo puede ser, sino que idealmente debe incorporarse como una parte integral del análisis dinámico.

Empleemos ahora`Wireshark`, para capturar y examinar el tráfico de red generado por el malware. Tenga en cuenta el tráfico codificado por colores: el rojo corresponde al tráfico de cliente a servir, mientras que Blue denota los intercambios de servidor a cliente.

Examinar la solicitud HTTP revela que la muestra de malware agrega el nombre de host de la computadora al campo de agente de usuario (en este caso fue`RDSEMVM01`).

![](https://academy.hackthebox.com/storage/modules/227/wireshark_request.png)

Al inspeccionar la respuesta HTTP, se hace evidente que`InetSim`ha devuelto su binario predeterminado como respuesta al malware.

![](https://academy.hackthebox.com/storage/modules/227/wireshark_response.png)

La solicitud del malware para`svchost.exe`solicita el binario predeterminado de`InetSim`. Este binario responde con un`MessageBox`Con el mensaje:`This is the INetSim default binary`.

Además, DNS solicita un dominio aleatorio y la dirección`ms-windows-update[.]com`fueron enviados por el malware, con`INetSim`respondiendo con respuestas falsas (en este caso`INetSim`estaba corriendo`10.10.10.100`).

![](https://academy.hackthebox.com/storage/modules/227/wireshark_dns.png)

# **Análisis de la región de inyección y memoria de procesos**

En el viaje del análisis de código, descubrimos que nuestro ejecutable realiza la inyección de procesos en`notepad.exe`y muestra un`MessageBox`que indica`Connection sent to C2`.

Para sondear más profundamente en la inyección del proceso, proponemos establecer puntos de ruptura en las funciones de Winapi`VirtualAllocEx`, `WriteProcessMemory`, y`CreateRemoteThread`. Estos puntos de interrupción nos permitirán analizar el contenido mantenido en los registros durante la inyección del proceso. Aquí está el procedimiento para establecer estos puntos de interrupción:

- Acceder al`x64dbg`interfaz y navegar al`Symbols`pestaña, ubicada en la parte superior.
- En el cuadro de búsqueda de símbolos, busque el nombre de DLL deseado en los nombres de la izquierda y las funciones, como`VirtualAllocEx`, `WriteProcessMemory`, y`CreateRemoteThread`, a la derecha dentro del`Kernel32.dll`Dll.
- A medida que los nombres de funciones se materializan en los resultados de búsqueda, haga clic con el botón derecho y seleccione`Toggle breakpoint`Desde el menú contextual para cada función. Un atajo alternativo es presionar`F2`.

La ejecución de estos pasos establece un punto de interrupción en el punto de entrada de cada función. Replicaremos estos pasos para todas las funciones que pretendemos analizar.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_008_bp_wpm.png)

Después de establecer puntos de interrupción, presionamos`F9`o seleccionar`Run`desde la barra de herramientas hasta llegar al punto de interrupción para`WriteProcessMemory`. Hasta este momento`notepad`ha sido lanzado, pero el`shellcode`Todavía no se ha escrito en la memoria del bloc de notas.

### **Adjuntar otro proceso en ejecución en x64dbg**

Para profundizar más, abramos otra instancia de`x64dbg`y adjuntarlo a`notepad.exe`.

- Iniciar una nueva instancia de`x64dbg`.
- Navegar al`File`menú y seleccionar`Attach`o usar el`Alt + A`Atajo de teclado.
- En el`Attach`Cuadro de diálogo, aparecerá una lista de procesos en ejecución. Elegir`notepad.exe`de la lista.
- Haga clic en el`Attach`botón para comenzar el proceso de archivo adjunto.

Una vez que el archivo adjunto es exitoso,`x64dbg`Inicia la depuración del proceso de destino, y la ventana principal muestra el código de ensamblaje junto con otra información de depuración.

Ahora, podemos establecer puntos de interrupción, atravesar el código, inspeccionar registros y memoria, y estudiar el comportamiento del proceso de nota de nota.`x64dbg`.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_009_attach.png)

El segundo argumento de`WriteProcessMemory`es`lpBaseAddress`que contiene un puntero a la dirección base en el proceso especificado al que se escriben los datos. En nuestro caso, debería estar en el`RDX`registro.

![](https://academy.hackthebox.com/storage/modules/227/msdn_002_writeprocessmemory.png)

Al invocar el`WriteProcessMemory`función, el`rdx`Registro posee el`lpBaseAddress`parámetro. Este parámetro representa la dirección dentro del espacio de direcciones del proceso de destino donde se escribirán los datos.

Nuestro objetivo es examinar los registros cuando el`WriteProcessMemory`la función se invoca en el`x64dbg`instancia ejecutando el`shell.exe`proceso. Esto revelará la dirección dentro`notepad.exe`donde se escribirá el shellcode.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_010_register_wpm.png)

Copiamos esta dirección para examinar su contenido en el volcado de memoria del adjunto`notepad.exe`proceso en el segundo`x64dbg`instancia.

Ahora seleccionamos`Go to` > `Expression`haciendo clic derecho en cualquier lugar del volcado de memoria en el segundo`x64dbg`instancia ejecutando`notepad.exe`.

Con la dirección copiada ingresada, se muestra el contenido en esta dirección (haciendo clic derecho en la dirección y eligiendo`Follow in Dump` > `Selected Address`), que actualmente está vacío.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_011_shellcode_write.png)

A continuación, ejecutamos shell.exe en el primero`x64dbg`instancia haciendo clic en el`Run`botón. Observamos lo que se inscribe en esta región de memoria de`notepad.exe`.

![](https://academy.hackthebox.com/storage/modules/227/x64dbg_shellcodecopy_.png)

Después de su ejecución, identificamos el inyectado`shellcode`, que se alinea con lo que descubrimos anteriormente durante el análisis de código. Podemos verificar esto en`Process Hacker`y guárdelo en un archivo para un examen posterior.