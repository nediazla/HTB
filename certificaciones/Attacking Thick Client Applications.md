# Attacking Thick Client Applications

Las aplicaciones de clientes gruesos son las aplicaciones que se instalan localmente en nuestras computadoras. A diferencia de las aplicaciones del cliente delgada que se ejecutan en un servidor remoto y se puede acceder a través del navegador web, estas aplicaciones no requieren acceso a Internet para la ejecución, y funcionan mejor en potencia de procesamiento, memoria y capacidad de almacenamiento. Las aplicaciones de clientes gruesas suelen ser aplicaciones utilizadas en entornos empresariales creados para cumplir finos específicos. Dichas aplicaciones incluyen sistemas de gestión de proyectos, sistemas de gestión de relaciones con el cliente, herramientas de gestión de inventario y otro software de productividad. Estas aplicaciones generalmente se desarrollan con Java, C ++, .NET o Microsoft Silverlight.

Una medida de seguridad crítica que, por ejemplo,`Java`ha es una tecnología llamada`sandbox`. Sandbox es un entorno virtual que permite que el código no confiable, como el código descargado de Internet, se ejecute de manera segura en el sistema de un usuario sin presentar un riesgo de seguridad. Además, aísla el código no confiable, evitando que acceda o modifique los recursos del sistema y otras aplicaciones sin la autorización adecuada. Además de eso, también hay`Java API restrictions`y`Code Signing`Eso ayuda a crear un entorno más seguro.

En`.NET`medio ambiente, un`thick client`, también conocido como un`rich`cliente o`fat`Cliente, se refiere a una aplicación que realiza una cantidad significativa de procesamiento en el lado del cliente en lugar de confiar únicamente en el servidor para todas las tareas de procesamiento. Como resultado, los clientes gruesos pueden proporcionar un mejor rendimiento, más características y mejores experiencias de usuario en comparación con sus`thin client`contrapartes, que dependen en gran medida del servidor para el procesamiento y el almacenamiento de datos.

Algunos ejemplos de aplicaciones de clientes gruesos son navegadores web, reproductores de medios, software de chat y videojuegos. Algunas aplicaciones de clientes gruesos generalmente están disponibles para comprar o descargar de forma gratuita a través de su sitio web oficial o tiendas de aplicaciones de terceros, mientras que otras aplicaciones personalizadas que se han creado para una compañía específica, se pueden entregar directamente del departamento de TI que ha desarrollado el software. La implementación y el mantenimiento de aplicaciones de clientes gruesas pueden ser más difícil que las aplicaciones del cliente delgada, ya que los parches y las actualizaciones deben hacerse localmente a la computadora del usuario. Algunas características de las aplicaciones de clientes gruesos son:

- Software independiente.
- Trabajando sin acceso a Internet.
- Almacenar datos localmente.
- Menos seguro.
- Consumiendo más recursos.
- Más caro.

Las aplicaciones de clientes gruesos se pueden clasificar en una arquitectura de dos niveles y de tres niveles. En la arquitectura de dos niveles, la aplicación se instala localmente en la computadora y se comunica directamente con la base de datos. En la arquitectura de tres niveles, las aplicaciones también se instalan localmente en la computadora, pero para interactuar con las bases de datos, primero se comunican con un servidor de aplicaciones, generalmente utilizando el protocolo HTTP/HTTPS. En este caso, el servidor de aplicaciones y la base de datos pueden ubicarse en la misma red o en Internet. Esto es algo que hace que la arquitectura de tres niveles sea más segura ya que los atacantes no podrán comunicarse directamente con la base de datos. La siguiente imagen muestra las diferencias entre las aplicaciones de arquitectura de dos niveles y tres niveles.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients/arch_tiers.png)

Dado que se descarga una gran parte de las aplicaciones de clientes gruesos de Internet, no hay una manera suficiente para garantizar que los usuarios descarguen la aplicación oficial, y eso plantea preocupaciones de seguridad. Vulnerabilidades específicas de la web como XSS, CSRF y Clickjacking, no se aplican a las aplicaciones de clientes gruesos. Sin embargo, las aplicaciones de clientes gruesas se consideran menos seguras que las aplicaciones web, ya que se aplican muchos ataques, que incluyen:

- Manejo de errores incorrecto.
- Datos confidenciales codificados.
- Dll secuestro.
- Desbordamiento del búfer.
- Inyección SQL.
- Almacenamiento inseguro.
- Gestión de sesiones.

---

# **Pasos de prueba de penetración**

Las aplicaciones de los clientes gruesas se consideran más complejas que otras, y la superficie de ataque puede ser grande. Las pruebas de penetración de aplicación de cliente gruesas se pueden realizar tanto utilizando herramientas automatizadas como manualmente. Los siguientes pasos generalmente se siguen al probar las gruesas aplicaciones del cliente.

### **Recopilación de información**

En este paso, los probadores de penetración tienen que identificar la arquitectura de la aplicación, los lenguajes de programación y los marcos que se han utilizado, y comprender cómo funcionan la aplicación y la infraestructura. También deben identificar las tecnologías que se utilizan en los lados del cliente y del servidor y encontrar puntos de entrada y entradas de usuario. Los probadores también deben buscar la identificación de vulnerabilidades comunes como las que mencionamos anteriormente al final del[Acerca de](https://academy.hackthebox.com/module/113/section/2139##About)sección. Las siguientes herramientas nos ayudarán a recopilar información.

|  |  |  |  |
| --- | --- | --- | --- |
| [Explorador de CFF](https://ntcore.com/?page_id=388) | [Detectarlo fácil](https://github.com/horsicq/Detect-It-Easy) | [Monitor de proceso](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon) | [Instrumentos de cuerda](https://learn.microsoft.com/en-us/sysinternals/downloads/strings) |

### **Ataques del lado del cliente**

Aunque los clientes gruesos realizan un procesamiento significativo y almacenamiento de datos en el lado del cliente, aún se comunican con los servidores para diversas tareas, como la sincronización de datos o el acceso a recursos compartidos. Esta interacción con servidores y otros sistemas externos puede exponer a los clientes gruesos a vulnerabilidades similares a las que se encuentran en las aplicaciones web, incluida la inyección de comandos, el control de acceso débil e inyección de SQL.

La información confidencial, como los nombres de usuario y las contraseñas, tokens o cadenas para la comunicación con otros servicios, puede almacenarse en los archivos locales de la aplicación. Las credenciales codificadas y otra información confidencial también se pueden encontrar en el código fuente de la aplicación, por lo que el análisis estático es un paso necesario al probar la aplicación. Usando las herramientas adecuadas, podemos invertir en ingeniería y examinar aplicaciones .NET y Java, incluidas EXE, DLL, JAR, clase, guerra y otros formatos de archivo. El análisis dinámico también debe realizarse en este paso, ya que las aplicaciones de clientes gruesas también almacenan información confidencial en la memoria.

|  |  |  |  |
| --- | --- | --- | --- |
| [Ghidra](https://www.ghidra-sre.org/) | [IDA](https://hex-rays.com/ida-pro/) | [Ollydbg](http://www.ollydbg.de/) | [Radare2](https://www.radare.org/r/index.html) |
| [dnspy](https://github.com/dnSpy/dnSpy) | [x64dbg](https://x64dbg.com/) | [Jadx](https://github.com/skylot/jadx) | [Frida](https://frida.re/) |

### **Ataques laterales de la red**

Si la aplicación se comunica con un servidor local o remoto, el análisis de tráfico de red nos ayudará a capturar información confidencial que podría transferirse a través de la conexión HTTP/HTTPS o TCP/UDP, y nos dará una mejor comprensión de cómo está funcionando esa aplicación. Los probadores de penetración que realizan un análisis de tráfico en aplicaciones de clientes gruesos deben estar familiarizados con herramientas como:

|  |  |  |  |
| --- | --- | --- | --- |
| [Wireshark](https://www.wireshark.org/) | [tcpdump](https://www.tcpdump.org/) | [Tcpview](https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview) | [Buque de eructo](https://portswigger.net/burp) |

### **Ataques del lado del servidor**

Los ataques del lado del servidor en aplicaciones de clientes gruesos son similares a los ataques de aplicaciones web, y los probadores de penetración deben prestar atención a los más comunes, incluida la mayoría de los diez mejores de OWASP.

---

# **Recuperar credenciales codificadas de aplicaciones de cliente grueso**

El siguiente escenario nos guía a través de enumeraciones y explotando una aplicación de cliente gruesa, para moverse lateralmente dentro de una red corporativa durante las pruebas de penetración. El escenario comienza después de haber obtenido acceso a un servicio SMB expuesto.

Explorando el`NETLOGON`La participación del servicio SMB revela`RestartOracle-Service.exe`entre otros archivos. Descargar el ejecutable localmente y ejecutarlo a través de la línea de comando, parece que no se ejecuta o ejecuta algo oculto.

Atacando aplicaciones gruesas de clientes

```
C:\Apps>.\Restart-OracleService.exe
C:\Apps>

```

Descargar la herramienta`ProcMon64`de[Sysinternals](https://learn.microsoft.com/en-gb/sysinternals/downloads/procmon)y el monitoreo del proceso revela que el ejecutable de hecho crea un archivo temperno en`C:\Users\Matt\AppData\Local\Temp`.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients/procmon.png)

Para capturar los archivos, se requiere cambiar los permisos del`Temp`carpeta para no permitir las eliminaciones de archivos. Para hacer esto, hacemos clic con el botón derecho en la carpeta`C:\Users\Matt\AppData\Local\Temp`y bajo`Properties` -> `Security` -> `Advanced` -> `cybervaca` -> `Disable inheritance` -> `Convert inherited permissions into explicit permissions on this object` -> `Edit` -> `Show advanced permissions`, deseleccionamos el`Delete subfolders and files`, y`Delete`casillas de verificación.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients/change-perms.png)

Finalmente, hacemos clic`OK` -> `Apply` -> `OK` -> `OK`en las ventanas abiertas. Una vez que se han aplicado los permisos de carpeta, simplemente volvemos a ejecutar el`Restart-OracleService.exe`y revisa el`temp`carpeta. El archivo`6F39.bat`se crea bajo el`C:\Users\cybervaca\AppData\Local\Temp\2`. Los nombres de los archivos generados son aleatorios cada vez que se ejecuta el servicio.

Atacando aplicaciones gruesas de clientes

```
C:\Apps>dir C:\Users\cybervaca\AppData\Local\Temp\2

...SNIP...
04/03/2023  02:09 PM         1,730,212 6F39.bat
04/03/2023  02:09 PM                 0 6F39.tmp

```

Enumerar el contenido del`6F39`El archivo por lotes revela lo siguiente.

Código:lote

```
@shift /0@echo off

if %username% == matt goto correcto
if %username% == frankytech goto correcto
if %username% == ev4si0n goto correcto
goto error

:correcto
echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
<SNIP>
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
powershell.exe -exec bypass -file c:\programdata\monta.ps1
del c:\programdata\monta.ps1
del c:\programdata\oracle.txt
c:\programdata\restart-service.exedel c:\programdata\restart-service.exe

```

Inspeccionar el contenido del archivo revela que el archivo por lotes está dejando caer dos archivos y eliminar antes de que alguien pueda obtener acceso a las sobras. Podemos intentar recuperar el contenido de los 2 archivos, modificando el script de lotes y eliminando la eliminación.

Código:lote

```
@shift /0@echo off

echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
<SNIP>
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1

```

Después de ejecutar el script por lotes haciendo doble clic en él, esperamos unos minutos para detectar el`oracle.txt`Archivo que contiene otro archivo lleno de líneas Base64 y el script`monta.ps1`que contiene el siguiente contenido, en el directorio`c:\programdata\`. Enumerar el contenido del archivo`monta.ps1`revela el siguiente código.

Atacando aplicaciones gruesas de clientes

```powershell
C:\>  cat C:\programdata\monta.ps1

$salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida))
```

Este script simplemente lee el contenido del`oracle.txt`archivo y lo decodifica al`restart-service.exe`ejecutable. Ejecutar este script nos da un ejecutable final que podemos analizar más a fondo.

Atacando aplicaciones gruesas de clientes

```powershell
C:\>  ls C:\programdata\

Mode                LastWriteTime         Length Name
<SNIP>
-a----        3/24/2023   1:01 PM            273 monta.ps1
-a----        3/24/2023   1:01 PM         601066 oracle.txt
-a----        3/24/2023   1:17 PM         432273 restart-service.exe

```

Ahora al ejecutar`restart-service.exe`Nos presentan el banner`Restart Oracle`creado por`HelpDesk`en 2010.

Atacando aplicaciones gruesas de clientes

```powershell
C:\>  .\restart-service.exe

    ____            __             __     ____                  __
   / __ \___  _____/ /_____ ______/ /_   / __ \_________ ______/ /__
  / /_/ / _ \/ ___/ __/ __ `/ ___/ __/  / / / / ___/ __ `/ ___/ / _ \
 / _, _/  __(__  ) /_/ /_/ / /  / /_   / /_/ / /  / /_/ / /__/ /  __/
/_/ |_|\___/____/\__/\__,_/_/   \__/   \____/_/   \__,_/\___/_/\___/

                                                by @HelpDesk 2010

PS C:\ProgramData>

```

Inspeccionar la ejecución del ejecutable a través de`ProcMon64`muestra que está consultando múltiples cosas en el registro y no muestra nada sólido para pasar.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients/proc-restart.png)

Comencemos`x64dbg`, navegar a`Options` -> `Preferences`, y desmarque todo excepto`Exit Breakpoint`:

![](https://academy.hackthebox.com/storage/modules/113/Exit_Breakpoint_1.png)

Al desmarcar las otras opciones, la depuración comenzará directamente desde el punto de salida de la aplicación, y evitaremos pasar por cualquier`dll`archivos que se cargan antes de que comience la aplicación. Entonces, podemos seleccionar`file` -> `open`y seleccione el`restart-service.exe`para importarlo y comenzar la depuración. Una vez importado, hacemos clic derecho dentro del`CPU`Ver y`Follow in Memory Map`:

![](https://academy.hackthebox.com/storage/modules/113/Follow-In-Memory-Map.png)

Verificar los mapas de memoria en esta etapa de la ejecución, de particular interés es el mapa con un tamaño de`0000000000003000`con un tipo de`MAP`y protección establecida para`-RW--`.

![](https://academy.hackthebox.com/storage/modules/113/Identify-Memory-Map.png)

Los archivos mapeados de memoria permiten que las aplicaciones accedan a archivos grandes sin tener que leer o escribir todo el archivo en la memoria a la vez. En cambio, el archivo se asigna a una región de memoria que la aplicación puede leer y escribir como si fuera un búfer regular en la memoria. Este podría ser un lugar para buscar credenciales codificadas.

Si hagamos doble clic en él, veremos los bytes mágicos`MZ`en el`ASCII`columna que indica que el archivo es un[Ejecutable de DOS MZ](https://en.wikipedia.org/wiki/DOS_MZ_executable).

![](https://academy.hackthebox.com/storage/modules/113/thick_clients/magic_bytes_3.png)

Volvamos al panel de mapa de memoria, luego expulsemos el elemento asignado recién descubierto de la memoria a un archivo de volcado haciendo clic derecho en la dirección y seleccionando`Dump Memory to File`. Correr`strings`En el archivo exportado revela información interesante.

Atacando aplicaciones gruesas de clientes

```powershell
C:\> C:\TOOLS\Strings\strings64.exe .\restart-service_00000000001E0000.bin

<SNIP>
"#Mz\V
).NETFramework,Version=v4.0,Profile=Client
FrameworkDisplayName
.NET Framework 4 Client Profile
<SNIP>

```

Leer la salida revela que el volcado contiene un`.NET`ejecutable. Podemos usar`De4Dot`Para revertir`.NET`ejecutables nuevamente al código fuente arrastrando el`restart-service_00000000001E0000.bin`en el`de4dot`ejecutable.

Atacando aplicaciones gruesas de clientes

```
de4dot v3.1.41592.3405

Detected Unknown Obfuscator (C:\Users\cybervaca\Desktop\restart-service_00000000001E0000.bin)
Cleaning C:\Users\cybervaca\Desktop\restart-service_00000000001E0000.bin
Renaming all obfuscated symbols
Saving C:\Users\cybervaca\Desktop\restart-service_00000000001E0000-cleaned.bin

Press any key to exit...

```

Ahora, podemos leer el código fuente de la aplicación exportada arrastrándola y dejándolo en el`DnSpy`ejecutable.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients/souce-code_hidden.png)

Con el código fuente revelado, podemos entender que este binario es un`runas.exe`con el único propósito de reiniciar el servicio Oracle utilizando credenciales codificadas.