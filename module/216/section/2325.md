# Tapping Into ETW

# **Ejemplo de detección 1: detección de relaciones extrañas entre padres e hijos**

Las relaciones anormales entre padres e hijos entre procesos pueden ser indicativas de actividades maliciosas. En entornos estándar de Windows, ciertos procesos nunca llaman ni generan otros. Por ejemplo, es muy poco probable que "calc.exe" genere "cmd.exe" en un entorno normal de Windows. Comprender estas relaciones típicas entre padres e hijos puede ayudar a detectar anomalías. Samir Bousseaden ha compartido un mapa mental revelador que presenta las relaciones comunes entre padres e hijos, al que se puede hacer referencia. [aquí](https://twitter.com/SBousseaden/status/1195373669930983424).

Al utilizar Process Hacker, podemos explorar las relaciones entre padres e hijos dentro de Windows. Ordenar los procesos por menús desplegables en la vista Procesos revela una representación jerárquica de las relaciones.

![](https://academy.hackthebox.com/storage/modules/216/image34.png)

El análisis de estas relaciones en entornos estándar y personalizados nos permite identificar desviaciones de los patrones normales. Por ejemplo, si observamos el proceso "spoolsv.exe" creando "whoami.exe" en lugar del comportamiento esperado de crear un "conhost", genera sospechas.

![](https://academy.hackthebox.com/storage/modules/216/image35.png)

Para mostrar una relación extraña entre padres e hijos, donde "cmd.exe" parece haber sido creado por "spoolsv.exe" `with no accompanying arguments`, utilizaremos una técnica de ataque llamada Parent PID Spoofing. La suplantación de PID principal se puede ejecutar a través del[proyecto psgetsystem](https://github.com/decoder-it/psgetsystem) de la siguiente manera.

Aprovechando ETW

```powershell
PS C:\Tools\psgetsystem> powershell -ep bypass
PS C:\Tools\psgetsystem> Import-Module .\psgetsys.ps1
PS C:\Tools\psgetsystem> [MyProcess]::CreateProcessFromParent([Process ID of spoolsv.exe],"C:\Windows\System32\cmd.exe","")

```

![](https://academy.hackthebox.com/storage/modules/216/image47.png)

Debido a la técnica de suplantación de PID principal que empleamos, el Evento 1 de Sysmon muestra incorrectamente `spoolsv.exe` como padre de `cmd.exe`. Sin embargo, en realidad fue`powershell.exe`que creó `cmd.exe`.

Como hemos comentado anteriormente, aunque Sysmon y los registros de eventos proporcionan telemetría valiosa para buscar y crear reglas de alerta, no son las únicas fuentes de información. Comencemos recopilando datos de la`Microsoft-Windows-Kernel-Process`proveedor usando [SedaETW](https://github.com/mandiant/SilkETW) (el proveedor puede ser identificado usando `logman` como describimos anteriormente, `logman.exe query providers | findstr "Process"`). Después de eso, podemos proceder a simular el ataque nuevamente para evaluar si ETW puede brindarnos información más precisa sobre la ejecución del mismo.`cmd.exe`.

Aprovechando ETW

```
c:\Tools\SilkETW_SilkService_v8\v8\SilkETW>SilkETW.exe -t user -pn Microsoft-Windows-Kernel-Process -ot file -p C:\windows\temp\etw.json

```

![](https://academy.hackthebox.com/storage/modules/216/image48.png)

El `etw.json` archivo (que incluye datos del `Microsoft-Windows-Kernel-Process` proveedor) parece contener información sobre `powershell.exe` siendo quien creó`cmd.exe`.

![](https://academy.hackthebox.com/storage/modules/216/image49.png)

Cabe señalar que los registros de eventos de SilkETW pueden ser ingeridos y vistos por el Visor de eventos de Windows a través de `SilkService` para proporcionarnos una visibilidad más profunda y extensa de las acciones realizadas en un sistema.

# **Ejemplo de detección 2: detección de carga de ensamblados .NET maliciosos**

Tradicionalmente, los adversarios empleaban una estrategia conocida como ["Vivir de la tierra" (LotL)](https://www.attackiq.com/2023/03/16/hiding-in-plain-sight/), explotando herramientas legítimas del sistema, como PowerShell, para llevar a cabo sus operaciones maliciosas. Este enfoque reduce el riesgo de detección, ya que implica el uso de herramientas nativas del sistema y, por lo tanto, es menos probable que generen sospechas.

Sin embargo, la comunidad de la ciberseguridad ha adaptado y desarrollado contramedidas contra esta estrategia.

En respuesta a estos avances defensivos, los atacantes han desarrollado un nuevo enfoque que Mandiant califica como ["Trae tu propia tierra" (BYOL)](https://www.mandiant.com/resources/blog/bring-your-own-land-novel-red-teaming-technique). En lugar de depender de las herramientas que ya están presentes en el sistema de la víctima, los actores de amenazas y los evaluadores de penetración que emulan estas tácticas ahora emplean ensamblados .NET ejecutados completamente en la memoria. Esto implica crear herramientas personalizadas utilizando lenguajes como C#, haciéndolas independientes de las herramientas preexistentes en el sistema de destino. Las tierras "Trae tu propia tierra" son bastante efectivas por las siguientes razones:

- Cada sistema Windows viene equipado con una determinada versión de .NET preinstalada de forma predeterminada.
- Una característica destacada de .NET es su naturaleza administrada, que alivia la necesidad de que los programadores manejen manualmente la administración de la memoria. Este atributo es parte del proceso de ejecución de código administrado del marco, donde Common Language Runtime (CLR) asume la responsabilidad de operaciones clave a nivel del sistema, como la recolección de basura, eliminando pérdidas de memoria y garantizando una utilización más eficiente de los recursos.
- Una de las ventajas interesantes de utilizar ensamblados .NET es su capacidad de cargarse directamente en la memoria. Esto significa que no es necesario escribir físicamente un ejecutable o DLL en el disco; en cambio, se ejecuta directamente en la memoria. Este comportamiento minimiza los artefactos que quedan en el sistema y puede ayudar a evitar algunas formas de detección que dependen de la inspección de archivos escritos en el disco.
- Microsoft ha integrado una amplia gama de bibliotecas en el marco .NET para abordar numerosos desafíos de programación comunes. Estas bibliotecas incluyen funcionalidades para establecer conexiones HTTP, implementar operaciones criptográficas y habilitar la comunicación entre procesos (IPC), como canalizaciones con nombre. Estas herramientas prediseñadas agilizan el proceso de desarrollo, reducen la probabilidad de errores y facilitan la creación de aplicaciones sólidas y eficientes. Además, para un actor de amenazas, estas ricas funciones proporcionan un conjunto de herramientas para crear métodos de ataque más sofisticados y encubiertos.

Un poderoso ejemplo de esta estrategia BYOL es la ["ejecutar-ensamblaje"](https://www.cobaltstrike.com/blog/cobalt-strike-3-11-the-snake-that-eats-its-tail/) comando implementado en CobaltStrike, una plataforma de software ampliamente utilizada para simulaciones de adversarios y operaciones de equipo rojo. El comando 'ejecutar-ensamblaje' de CobaltStrike permite al usuario ejecutar ensamblados .NET directamente desde la memoria, lo que lo convierte en una herramienta ideal para implementar una estrategia BYOL.

De manera similar a cómo detectamos la ejecución de scripts de PowerShell no administrados mediante la observación de anomalías `clr.dll` y `clrjit.dll` actividad de carga en procesos que normalmente no los requerirían, podemos emplear un enfoque similar para identificar la carga de ensamblados .NET maliciosos. Esto se logra mediante el escrutinio de la actividad relacionada con la carga de[DLL asociadas a .NET](https://redhead0ntherun.medium.com/detecting-net-c-injection-execute-assembly-1894dbb04ff7), específicamente `clr.dll` y`mscoree.dll`.

Monitorear la carga de dichas bibliotecas puede ayudar a revelar intentos de ejecutar ensamblados .NET en contextos inusuales o inesperados, lo que puede ser un signo de actividad maliciosa. Este tipo de comportamiento de carga de DLL a menudo se puede detectar aprovechando el ID de evento 7 de Sysmon, que corresponde a los eventos "Imagen cargada".

Con fines demostrativos, emulemos una carga de ensamblado .NET malicioso ejecutando una versión precompilada de [cinturón de seguridad](https://github.com/GhostPack/Seatbelt) que reside en el disco.`Seatbelt`es un ensamblado .NET muy conocido, a menudo empleado por adversarios que lo cargan y ejecutan en la memoria para obtener conocimiento de la situación en un sistema comprometido.

Aprovechando ETW

```powershell
PS C:\Tools\GhostPack Compiled Binaries>.\Seatbelt.exe TokenPrivileges

                        %&&@@@&&
                        &&&&&&&%%%,                       #&&@@@@@@%%%%%%###############%                        &%&   %&%%                        &////(((&%%%%%#%################//((((###%%%%%%%%%%%%%%%%%%%%%%%%%%######%%%#%%####%  &%%**#                      @////(((&%%%%%%######################(((((((((((((((((((#%#%%%%%%%#######%#%%#######  %&%,,,,,,,,,,,,,,,,         @////(((&%%%%%#%#####################(((((((((((((((((((#%#%%%%%%#####%%#%#%%#######  %%%,,,,,,  ,,.   ,,         @////(((&%%%%%%%######################(#(((#(#((((((((((#####%%%####################  &%%......  ...   ..         @////(((&%%%%%%%###############%######((#(#(####((((((((#######%##########%#########  %%%......  ...   ..         @////(((&%%%%%#########################(#(#######((########%##%%####################  &%%...............          @////(((&%%%%%%%%##############%#######(#########((##########%######################  %%%..                       @////(((&%%%%%%%################                        &%&   %%%%%      Seatbelt         %////(((&%%%%%%%%#############*                        &%%&&&%%%%%        v1.2.1         ,(((&%%%%%%%%%%%%%%%%%,
                         #%%%%##,====== TokenPrivileges ======

Current Token's Privileges

                     SeIncreaseQuotaPrivilege:  DISABLED
                          SeSecurityPrivilege:  DISABLED
                     SeTakeOwnershipPrivilege:  DISABLED
                        SeLoadDriverPrivilege:  DISABLED
                     SeSystemProfilePrivilege:  DISABLED
                        SeSystemtimePrivilege:  DISABLED
              SeProfileSingleProcessPrivilege:  DISABLED
              SeIncreaseBasePriorityPrivilege:  DISABLED
                    SeCreatePagefilePrivilege:  DISABLED
                            SeBackupPrivilege:  DISABLED
                           SeRestorePrivilege:  DISABLED
                          SeShutdownPrivilege:  DISABLED
                             SeDebugPrivilege:  SE_PRIVILEGE_ENABLED
                 SeSystemEnvironmentPrivilege:  DISABLED
                      SeChangeNotifyPrivilege:  SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
                    SeRemoteShutdownPrivilege:  DISABLED
                            SeUndockPrivilege:  DISABLED
                      SeManageVolumePrivilege:  DISABLED
                       SeImpersonatePrivilege:  SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
                      SeCreateGlobalPrivilege:  SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
                SeIncreaseWorkingSetPrivilege:  DISABLED
                          SeTimeZonePrivilege:  DISABLED
                SeCreateSymbolicLinkPrivilege:  DISABLED
    SeDelegateSessionUserImpersonatePrivilege:  DISABLED

```

Suponiendo que tenemos Sysmon configurado apropiadamente para registrar eventos de carga de imágenes (ID de evento 7), la ejecución de 'Seatbelt.exe' activaría la carga de archivos DLL clave relacionados con .NET, como 'clr.dll' y 'mscoree.dll'. Sysmon, observando atentamente las actividades del sistema, registrará estas operaciones de carga de DLL como registros de ID de evento 7.

![](https://academy.hackthebox.com/storage/modules/216/image77.png)

![](https://academy.hackthebox.com/storage/modules/216/image78.png)

Como ya se mencionó, confiar únicamente en Sysmon Event ID 7 para detectar ataques puede ser un desafío debido al gran volumen de eventos que genera (especialmente si no se configura correctamente). Además, si bien nos informa sobre las DLL que se cargan, no proporciona detalles granulares sobre el contenido real del ensamblado .NET cargado.

Para aumentar nuestra visibilidad y obtener información más profunda sobre el ensamblaje real que se está cargando, podemos aprovechar nuevamente el seguimiento de eventos para Windows (ETW) y específicamente el `Microsoft-Windows-DotNETRuntime` proveedor.

Usemos SilkETW para recopilar datos del `Microsoft-Windows-DotNETRuntime` proveedor. Después de eso, podemos proceder a simular el ataque nuevamente para evaluar si ETW puede proporcionarnos inteligencia más detallada y procesable con respecto a la carga y ejecución del ensamblaje .NET 'Seatbelt'.

Aprovechando ETW

```
c:\Tools\SilkETW_SilkService_v8\v8\SilkETW>SilkETW.exe -t user -pn Microsoft-Windows-DotNETRuntime -uk 0x2038 -ot file -p C:\windows\temp\etw.json

```

El `etw.json` archivo (que incluye datos del `Microsoft-Windows-DotNETRuntime` proveedor) parece contener una gran cantidad de información sobre el ensamblado cargado, incluidos los nombres de los métodos.

![](https://academy.hackthebox.com/storage/modules/216/image79.png)

Vale la pena señalar que en nuestra configuración actual de SilkETW, no capturamos la totalidad de los eventos del proveedor "Microsoft-Windows-DotNETRuntime". En lugar de ello, nos dirigimos selectivamente a un subconjunto específico (indicado por `0x2038`), que incluye: `JitKeyword`, `InteropKeyword`, `LoaderKeyword`, y`NGenKeyword`.

- El `JitKeyword` se relaciona con los eventos de compilación Just-In-Time (JIT), y proporciona información sobre los métodos que se compilan en tiempo de ejecución. Esto podría resultar particularmente útil para comprender el flujo de ejecución del ensamblado .NET.
- El `InteropKeyword` se refiere a eventos de interoperabilidad, que entran en juego cuando el código administrado interactúa con el código no administrado. Estos eventos podrían proporcionar información sobre posibles interacciones con API nativas u otros componentes no administrados.
- `LoaderKeyword`Los eventos proporcionan detalles sobre el proceso de carga de ensamblados dentro del tiempo de ejecución de .NET, lo que puede ser vital para comprender qué ensamblados .NET se están cargando y potencialmente ejecutando.
- Por último, el `NGenKeyword` Corresponde a los eventos Native Image Generator (NGen), que se ocupan de la creación y el uso de ensamblados .NET precompilados. Monitorearlos podría ayudar a detectar escenarios en los que los atacantes utilizan ensamblados .NET precompilados para evadir las detecciones relacionadas con JIT.

Este [publicación de blog](https://medium.com/threat-hunters-forge/threat-hunting-with-etw-events-and-helk-part-1-installing-silketw-6eb74815e4a0) proporciona perspectivas valiosas sobre SilkETW así como la identificación de malware basado en .NET.

---

# **Ejercicio práctico**

Navegue hasta el final de esta sección y haga clic en`Click here to spawn the target system!`

Luego, RDP a `[Target IP]` utilizando las credenciales proporcionadas y responda la siguiente pregunta.

Aprovechando ETW

```
xnoxos@htb[/htb]$ xfreerdp /u:Administrator /p:'HTB_@cad3my_lab_W1n10_r00t!@0' /v:[Target IP] /dynamic-resolution
```