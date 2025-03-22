# Analyzing Evil With Sysmon & Event Logs

En nuestra búsqueda de una ciberseguridad sólida, es fundamental comprender cómo identificar y analizar eventos maliciosos de manera efectiva. Basándonos en nuestra exploración anterior de eventos benignos, ahora profundizaremos en el ámbito de las actividades maliciosas y descubriremos técnicas de detección.

---

# **Conceptos básicos de Sysmon**

Al investigar eventos maliciosos, varios ID de eventos sirven como indicadores comunes de compromiso. Por ejemplo, `Event ID 4624` proporciona información sobre nuevos eventos de inicio de sesión, lo que nos permite monitorear y detectar patrones de inicio de sesión y acceso de usuarios sospechosos. Similarmente, `Event ID 4688` proporciona información sobre procesos recién creados, lo que ayuda a identificar inicios de procesos inusuales o maliciosos. Para mejorar nuestra cobertura de registro de eventos, podemos ampliar las capacidades incorporando Sysmon, que ofrece capacidades adicionales de registro de eventos.

`System Monitor (Sysmon)`es un servicio del sistema de Windows y un controlador de dispositivo que permanece residente durante los reinicios del sistema para monitorear y registrar la actividad del sistema en el registro de eventos de Windows. Sysmon proporciona información detallada sobre la creación de procesos, conexiones de red, cambios en el tiempo de creación de archivos y más.

Los componentes principales de Sysmon incluyen:

- Un servicio de Windows para monitorear la actividad del sistema.
- Un controlador de dispositivo que ayuda a capturar los datos de actividad del sistema.
- Un registro de eventos para mostrar los datos de actividad capturados.

La capacidad única de Sysmon radica en su capacidad para registrar información que normalmente no aparece en los registros de eventos de seguridad, y esto lo convierte en una herramienta poderosa para el monitoreo profundo del sistema y el análisis forense de ciberseguridad.

Sysmon clasifica diferentes tipos de actividad del sistema utilizando ID de eventos, donde cada ID corresponde a un tipo específico de evento. Por ejemplo,`Event ID 1`corresponde a eventos de "Creación de Proceso", y `Event ID 3` se refiere a eventos de "Conexión de red". Puede encontrar la lista completa de ID de eventos de Sysmon[aquí](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon).

Para un control más granular sobre qué eventos se registran, Sysmon utiliza un archivo de configuración basado en XML. El archivo de configuración le permite incluir o excluir ciertos tipos de eventos basados ​​en diferentes atributos como nombres de procesos, direcciones IP, etc. Podemos consultar ejemplos populares de archivos de configuración útiles de Sysmon:

- Para una configuración completa, podemos visitar: [https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config). <-- `We will use this one in this section!`
- Otra opción es: [https://github.com/olafhartong/sysmon-modular](https://github.com/olafhartong/sysmon-modular), que proporciona un enfoque modular.

Para comenzar, puede instalar Sysmon descargándolo de la documentación oficial de Microsoft ([https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)). Una vez descargado, abra un símbolo del sistema de administrador y ejecute el siguiente comando para instalar Sysmon.

Analizando el mal con Sysmon y registros de eventos

```
C:\Tools\Sysmon> sysmon.exe -i -accepteula -h md5,sha256,imphash -l -n

```

![](https://academy.hackthebox.com/storage/modules/216/image13.png)

Para utilizar una configuración Sysmon personalizada, ejecute lo siguiente después de instalar Sysmon.

Analizando el mal con Sysmon y registros de eventos

```
C:\Tools\Sysmon> sysmon.exe -c filename.xml

```

**Nota**: Cabe señalar que [Sysmon para Linux](https://github.com/Sysinternals/SysmonForLinux) también existe.

---

# **Ejemplo de detección 1: detección de secuestro de DLL**

En nuestro caso de uso específico, nuestro objetivo es detectar un secuestro de DLL. Los ID de registro de eventos de Sysmon relevantes para los secuestros de DLL se pueden encontrar en la documentación de Sysmon ([https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)). Para detectar un secuestro de DLL, debemos centrarnos en`Event Type 7`, que corresponde a eventos de carga del módulo. Para lograr esto, necesitamos modificar el`sysmonconfig-export.xml`Archivo de configuración de Sysmon que descargamos de [https://github.com/SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config).

Al examinar la configuración modificada, podemos observar que el comentario "incluir" significa eventos que deben incluirse.

![](https://academy.hackthebox.com/storage/modules/216/image14.png)

En el caso de detectar secuestros de DLL, cambiamos "incluir" por "excluir" para garantizar que no se excluya nada, lo que nos permite capturar los datos necesarios.

![](https://academy.hackthebox.com/storage/modules/216/image15.png)

Para utilizar la configuración Sysmon actualizada, ejecute lo siguiente.

Analizando el mal con Sysmon y registros de eventos

```
C:\Tools\Sysmon> sysmon.exe -c sysmonconfig-export.xml

```

![](https://academy.hackthebox.com/storage/modules/216/image16.png)

Con la configuración de Sysmon modificada, podemos comenzar a observar eventos de carga de imágenes. Para ver estos eventos, navegue hasta el Visor de eventos y acceda a "Aplicaciones y servicios" -> "Microsoft" -> "Windows" -> "Sysmon". Una verificación rápida revelará la presencia del ID del evento objetivo.

![](https://academy.hackthebox.com/storage/modules/216/image17.png)

Veamos ahora cómo se ve un evento Sysmon ID 7.

![](https://academy.hackthebox.com/storage/modules/216/image18.png)

El registro de eventos contiene el estado de firma de la DLL (en este caso, está firmada por Microsoft), el proceso o la imagen responsable de cargar la DLL y la DLL específica que se cargó. En nuestro ejemplo, observamos que "MMC.exe" cargó "psapi.dll", que también está firmado por Microsoft. Ambos archivos se encuentran en el directorio System32.

Ahora, procedamos a construir un mecanismo de detección. Para obtener más información sobre los secuestros de DLL, es fundamental realizar una investigación. Nos topamos con un informativo.[publicación de blog](https://www.wietzebeukema.nl/blog/hijacking-dlls-in-windows) que proporciona una lista exhaustiva de varias técnicas de secuestro de DLL. Para nuestra detección, nos centraremos en un secuestro específico que involucra al ejecutable vulnerable calc.exe y una lista de archivos DLL que pueden ser secuestrados.

![](https://academy.hackthebox.com/storage/modules/216/image19.png)

Intentemos el secuestro usando "calc.exe" y "WININET.dll" como ejemplo. Para simplificar el proceso, podemos utilizar el "hola mundo" de Stephen Fewer.[DLL reflectante](https://github.com/stephenfewer/ReflectiveDLLInjection/tree/master/bin). Cabe señalar que el secuestro de DLL no requiere DLL reflectantes.

Siguiendo los pasos requeridos, que implican cambiar el nombre `reflective_dll.x64.dll` a `WININET.dll`, moviéndose `calc.exe` de`C:\Windows\System32`junto con `WININET.dll` a un directorio grabable (como el `Desktop` carpeta), y ejecutando `calc.exe`, logramos el éxito. En lugar de la aplicación Calculadora, una[Cuadro de mensaje](https://learn.microsoft.com/en-us/windows/win32/api/winuser/nf-winuser-messageboxa) se muestra.

![](https://academy.hackthebox.com/storage/modules/216/image20.png)

A continuación, analizamos el impacto del secuestro. Primero, filtramos los registros de eventos para centrarnos en `Event ID 7`, que representa eventos de carga del módulo, haciendo clic en "Filtrar registro actual...".

![](https://academy.hackthebox.com/storage/modules/216/image21.png)

Posteriormente, buscamos instancias de "calc.exe", haciendo clic en "Buscar...", para identificar la carga de DLL asociada con nuestro secuestro.

![](https://academy.hackthebox.com/storage/modules/216/image43.png)

Los resultados de Sysmon proporcionan información valiosa. Ahora podemos observar varios indicadores de compromiso (IOC) para crear reglas de detección efectivas. Sin embargo, antes de seguir adelante, comparemos esto con una carga de autenticación de "wininet.dll" mediante "calc.exe".

![](https://academy.hackthebox.com/storage/modules/216/image23.png)

Exploremos estos COI:

1. "calc.exe", ubicado originalmente en System32, no debería encontrarse en un directorio donde se pueda escribir. Por lo tanto, una copia de "calc.exe" en un directorio grabable sirve como IOC, ya que siempre debería residir en System32 o potencialmente en Syswow64.
2. "WININET.dll", ubicado originalmente en System32, no debe cargarse fuera de System32 mediante calc.exe. Si se producen instancias de carga de "WININET.dll" fuera de System32 con "calc.exe" como proceso principal, indica un secuestro de DLL dentro de calc.exe. Si bien es necesario tener precaución al alertar sobre todos los casos de carga de "WININET.dll" fuera de System32 (ya que algunas aplicaciones pueden empaquetar versiones de DLL específicas para mayor estabilidad), en el caso de "calc.exe", podemos afirmar con seguridad que se trata de un secuestro debido a El nombre inmutable de la DLL, que los atacantes no pueden modificar para evadir la detección.
3. El "WININET.dll" original está firmado por Microsoft, mientras que nuestra DLL inyectada permanece sin firmar.

Estos tres potentes IOC proporcionan un medio eficaz para detectar un secuestro de DLL que involucre a calc.exe. Es importante tener en cuenta que, si bien Sysmon y los registros de eventos ofrecen telemetría valiosa para buscar y crear reglas de alerta, no son las únicas fuentes de información.

---

# **Ejemplo de detección 2: detección de inyección de PowerShell/C-Sharp no administrada**

Antes de profundizar en las técnicas de detección, comprendamos brevemente C# y su entorno de ejecución. C# se considera un lenguaje "administrado", lo que significa que requiere un tiempo de ejecución de backend para ejecutar su código. El[Tiempo de ejecución de lenguaje común (CLR)](https://docs.microsoft.com/en-us/dotnet/standard/clr) sirve como este entorno de ejecución.[código administrado](https://docs.microsoft.com/en-us/dotnet/standard/managed-code) no se ejecuta directamente como ensamblaje; en cambio, se compila en un formato de código de bytes que el tiempo de ejecución procesa y ejecuta. En consecuencia, un proceso administrado depende de CLR para ejecutar código C#.

Como defensores, podemos aprovechar este conocimiento para detectar inyecciones o ejecuciones inusuales de C# dentro de nuestro entorno. Para lograr esto, podemos utilizar una útil utilidad llamada[Hacker de procesos](https://processhacker.sourceforge.io/).

![](https://academy.hackthebox.com/storage/modules/216/image24.png)

Al utilizar Process Hacker, podemos observar una variedad de procesos dentro de nuestro entorno. Al clasificar los procesos por nombre, podemos identificar interesantes distinciones codificadas por colores. En particular, "powershell.exe", un proceso administrado, está resaltado en verde en comparación con otros procesos. Al pasar el cursor sobre powershell.exe, se muestra la etiqueta "El proceso está administrado (.NET)", lo que confirma su estado administrado.

![](https://academy.hackthebox.com/storage/modules/216/image25.png)

Examinar las cargas del módulo para `powershell.exe`, haciendo clic derecho en `powershell.exe`, haciendo clic en "Propiedades" y navegando a "Módulos", podemos encontrar información relevante.

![](https://academy.hackthebox.com/storage/modules/216/image26.png)

La presencia de "Microsoft .NET Runtime...", `clr.dll`, y `clrjit.dll` debería llamar nuestra atención. Estas 2 DLL se utilizan cuando se ejecuta código C# como parte del tiempo de ejecución para ejecutar el código de bytes. Si observamos estas DLL cargadas en procesos que normalmente no las requieren, sugiere una posible[ejecutar-ensamblaje](https://www.cobaltstrike.com/blog/cobalt-strike-3-11-the-snake-that-eats-its-tail/) o [inyección de PowerShell no administrada](https://www.youtube.com/watch?v=7tvfb9poTKg&ab_channel=RaphaelMudge) ataque.

Para mostrar la inyección de PowerShell no administrada, podemos inyectar un [DLL no administrada tipo PowerShell](https://github.com/leechristensen/UnmanagedPowerShell) en un proceso aleatorio, como`spoolsv.exe`. Podemos hacerlo utilizando el [Proyecto PSInject](https://github.com/EmpireProject/PSInject) de la siguiente manera.

Analizando el mal con Sysmon y registros de eventos

```powershell
 powershell -ep bypass
 Import-Module .\Invoke-PSInject.ps1
 Invoke-PSInject -ProcId [Process ID of spoolsv.exe] -PoshCode "V3JpdGUtSG9zdCAiSGVsbG8sIEd1cnU5OSEi"

```

![](https://academy.hackthebox.com/storage/modules/216/image27.png)

Después de la inyección, observamos que "spoolsv.exe" pasa de un estado no administrado a uno administrado.

![](https://academy.hackthebox.com/storage/modules/216/image28.png)

Además, al consultar la pestaña "Módulos" relacionada de Process Hacker y Sysmon `Event ID 7`, podemos examinar la información de carga de la DLL para validar la presencia de las DLL antes mencionadas.

![](https://academy.hackthebox.com/storage/modules/216/image29.png)

![](https://academy.hackthebox.com/storage/modules/216/image30.png)

---

# **Ejemplo de detección 3: detección de volcado de credenciales**

Otro aspecto crítico de la ciberseguridad es la detección de actividades de dumping de credenciales. Una herramienta ampliamente utilizada para el volcado de credenciales es[Mimikatz](https://github.com/gentilkiwi/mimikatz), que ofrece varios métodos para extraer credenciales de Windows. Un comando específico, "sekurlsa::logonpasswords", permite volcar hashes de contraseñas o contraseñas de texto sin formato accediendo al[Servicio del Subsistema de Autoridad de Seguridad Local (LSASS)](https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service). LSASS es responsable de administrar las credenciales de los usuarios y es el objetivo principal de las herramientas de volcado de credenciales como Mimikatz.

El ataque se puede ejecutar de la siguiente manera.

Analizando el mal con Sysmon y registros de eventos

```
C:\Tools\Mimikatz> mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #18362 Feb 29 2020 11:13:36
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::logonpasswords

Authentication Id : 0 ; 1128191 (00000000:001136ff)
Session           : RemoteInteractive from 2
User Name         : Administrator
Domain            : DESKTOP-NU10MTO
Logon Server      : DESKTOP-NU10MTO
Logon Time        : 5/31/2023 4:14:41 PM
SID               : S-1-5-21-2712802632-2324259492-1677155984-500
        msv :
         [00000003] Primary
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * NTLM     : XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
         * SHA1     : XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX0812156b
        tspkg :
        wdigest :
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * Password : (null)
        kerberos :
         * Username : Administrator
         * Domain   : DESKTOP-NU10MTO
         * Password : (null)
        ssp :   KO
        credman :

```

Como podemos ver, el resultado del comando "sekurlsa::logonpasswords" proporciona información valiosa sobre las credenciales comprometidas.

Para detectar esta actividad, podemos confiar en un evento Sysmon diferente. En lugar de centrarnos en las cargas de DLL, centramos nuestra atención en los eventos de acceso al proceso. Al comprobar`Sysmon event ID 10`, que representa eventos de "ProcessAccess", podemos identificar cualquier intento sospechoso de acceder a LSASS.

![](https://academy.hackthebox.com/storage/modules/216/image32.png)

![](https://academy.hackthebox.com/storage/modules/216/image33.png)

Por ejemplo, si observamos un archivo aleatorio ("AgentEXE" en este caso) de una carpeta aleatoria ("Descargas" en este caso) intentando acceder a LSASS, indica un comportamiento inusual. Además, el `SourceUser` siendo diferente del `TargetUser` (por ejemplo, "waldo" como el`SourceUser`y "SISTEMA" como el `TargetUser`) enfatiza aún más la anormalidad. También vale la pena señalar que, como parte del proceso de volcado de credenciales basado en mimikatz, el usuario debe solicitar[Privilegios de SeDebug](https://devblogs.microsoft.com/oldnewthing/20080314-00/?p=23113). Como sugiere el nombre, se usa principalmente para depurar. Este puede ser otro Indicador de Compromiso (IOC).

Tenga en cuenta que algunos procesos legítimos pueden acceder a LSASS, como procesos relacionados con la autenticación o herramientas de seguridad como AV o EDR.