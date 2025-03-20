# Credentials in Shares

# **Descripción**

Las credenciales expuestas en las acciones de la red son (probablemente) la configuración errónea más encontrada en Active Directory hasta la fecha. Cualquier empresa mediana/grande sin duda tendrá credenciales expuestas, aunque también puede suceder en pequeñas empresas. Casi parece que nos estamos moviendo de "No dejes tu contraseña en una nota post-it en tu pantalla" para "no dejar credenciales y tokens de autorización sin cifrar en todas partes".

A menudo encontramos credenciales en acciones de red dentro de scripts y archivos de configuración (Batch, CMD, PowerShell, Conf, INI y config). Por el contrario, las credenciales en la máquina local de un usuario residen principalmente en archivos de texto, hojas de Excel o documentos de Word. La principal diferencia entre el almacenamiento de credenciales en acciones y máquinas es que el primero presenta un riesgo significativamente mayor, ya que cada usuario puede acceder. Cada usuario puede ser accesible para una parte de la red por cuatro razones principales:

- Un usuario administrativo inicialmente crea las acciones con acceso a la baja adecuadamente, pero finalmente lo abre a todos. Otro administrador del servidor también podría ser el culpable. No obstante, la participación finalmente se abre a`Everyone`o`Users,`y recuerde que un servidor`Users`grupo contiene`Domain users`como su miembro en entornos de Active Directory. Por lo tanto, cada usuario de dominio tendrá al menos acceso de lectura (se supone erróneamente que agregar 'usuarios' dará acceso solo a aquellos locales al servidor o administradores).
- El administrador agregar scripts con credenciales a una acción no es consciente de que es una carpeta compartida. Muchos administradores prueban sus guiones en un`scripts`carpeta en el`C:\`conducir; Sin embargo, si la carpeta se comparte (por ejemplo, con`Users`), entonces los datos dentro de los scripts también están expuestos en la red.
- Otro ejemplo es crear a propósito una parte abierta para mover datos a un servidor (por ejemplo, una aplicación o algunos otros archivos) y olvidar cerrarlos más tarde.
- Finalmente, en el caso de las acciones ocultas (carpetas cuyo nombre termina con un signo de dólar`$`), existe una idea errónea de que los usuarios no pueden encontrar la carpeta a menos que sepan dónde existe; El malentendido proviene del hecho de que`Explorer`en Windows no muestra archivos o carpetas cuyo nombre termine con un`$`, sin embargo, cualquier otra herramienta lo mostrará.

---

# **Ataque**

El primer paso es identificar qué acciones existen en un dominio. Hay muchas herramientas disponibles que pueden lograr esto, como PowerView's[Invocar-sharefinder](https://github.com/darkoperator/Veil-PowerView/blob/master/PowerView/functions/Invoke-ShareFinder.ps1). Esta función permite especificar que las acciones predeterminadas deben filtrarse (como`c$`y`IPC$`) y también verifique si el usuario invocador tiene acceso al resto de las acciones que encuentra. La salida final contiene una lista de acciones no defectuosas que la cuenta de usuario actual tiene al menos lectura de acceso a:

Credenciales en acciones

```powershell
PS C:\Users\bob\Downloads> Invoke-ShareFinder -domain eagle.local -ExcludeStandard -CheckShareAccess

\\DC2.eagle.local\NETLOGON      - Logon server share
\\DC2.eagle.local\SYSVOL        - Logon server share
\\WS001.eagle.local\Share       -
\\WS001.eagle.local\Users       -
\\Server01.eagle.local\dev$     -
\\DC1.eagle.local\NETLOGON      - Logon server share
\\DC1.eagle.local\SYSVOL        - Logon server share

```

![](https://academy.hackthebox.com/storage/modules/176/A5/sharescan.png)

[](https://www.notion.so)

El entorno del patio de recreo tiene pocas acciones en el dominio, sin embargo, en los entornos de producción, el número puede alcanzar miles (lo que requiere largos períodos para analizar).

La imagen de arriba muestra una parte con el nombre`dev$`. Debido al signo del dólar, si tuviéramos que navegar el servidor que contiene la acción usando Windows Explorer, se nos presentaría una lista vacía (acciones como`C$`y`IPC$`Aunque esté disponible por defecto, Explorer no los muestra debido al signo del dólar):

[](https://www.notion.so)

![](https://academy.hackthebox.com/storage/modules/176/A5/sharefileless.png)

Sin embargo, ya que tenemos el`UNC`ruta desde la salida, si le navegamos, podremos ver el contenido dentro de la compartir:

[](https://www.notion.so)

![](https://academy.hackthebox.com/storage/modules/176/A5/sharecontent.png)

Existen algunas herramientas automatizadas, como[Sauroneye](https://github.com/vivami/SauronEye), que puede analizar una colección de archivos y recoger palabras coincidentes. Sin embargo, debido a que hay pocas acciones en el patio de recreo, adoptaremos un enfoque más manual (`Living Off the Land`) y use el comando incorporado`findstr`para este ataque. Al correr`findstr`, especificaremos los siguientes argumentos:

- `/s`fuerzas para buscar en el directorio actual y todos los subdirectorios
- `/i`ignora el caso en el término de búsqueda
- `/m`Muestra solo el nombre de archivo para un archivo que coincida con el término. Necesitamos esto en entornos de producción reales debido a las enormes cantidades de texto que se devuelven. Por ejemplo, esto puede ser miles de líneas en los scripts de PowerShell que contienen el`PassThru`parámetro al coincidir con la cadena`pass`.
- El`term`Eso define lo que estamos buscando. Los buenos candidatos incluyen`pass`, `pw`, y el`NETBIOS`nombre del dominio. En el entorno del patio de recreo, es`eagle`. Los objetivos atractivos para esta búsqueda serían tipos de archivos como`.bat`, `.cmd`, `.ps1`, `.conf`, `.config`, y`.ini`. Aquí está como`findstr`se puede ejecutar para mostrar la ruta de los archivos con una coincidencia que contiene`pass`en relación con la ubicación actual:

Credenciales en acciones

```powershell
PS C:\Users\bob\Downloads> cd \\Server01.eagle.local\dev$
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /m /s /i "pass" *.bat
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /m /s /i "pass" *.cmd
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /m /s /i "pass" *.inisetup.ini
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /m /s /i "pass" *.config
4\5\4\web.config
```

![](https://academy.hackthebox.com/storage/modules/176/A5/scan1.png)

[](https://www.notion.so)

Del mismo modo, podemos ejecutar`findstr`buscando`pw`Como el término de búsqueda. Tenga en cuenta que si eliminamos el argumento `/m ', mostrará la línea exacta en el archivo donde ocurrió la coincidencia:

Credenciales en acciones

```powershell
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /m /s /i "pw" *.config

5\2\3\microsoft.config
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /s /i "pw" *.config
5\2\3\microsoft.config:pw BANANANANANANANANANANANANNAANANANANAS

```

![](https://academy.hackthebox.com/storage/modules/176/A5/scan2.png)

[](https://www.notion.so)

Un término de búsqueda obvio y poco común es el`NetBIOS`nombre del dominio. Comandos como`runas`y`net`Tome contraseñas como argumento posicional en la línea de comando en lugar de pasarlas a través de`pass`, `pw`, o cualquier otro término. Generalmente se define como`/user:DOMAIN\USERNAME PASSWORD`. Aquí está la búsqueda ejecutada en el dominio del patio de recreo:

Credenciales en acciones

```powershell
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /m /s /i "eagle" *.ps1

2\4\4\Software\connect.ps1
PS Microsoft.PowerShell.Core\FileSystem::\\Server01.eagle.local\dev$> findstr /s /i "eagle" *.ps1
2\4\4\Software\connect.ps1:net use E: \\DC1\sharedScripts /user:eagle\Administrator Slavi123
```

![](https://academy.hackthebox.com/storage/modules/176/A5/scan3.png)

[](https://www.notion.so)

El último comando lee el archivo de texto y muestra su contenido, incluidas las credenciales; Esta sería las credenciales de cuenta incorporadas del dominio (no es raro encontrar derechos de administrador de dominio en estos archivos de script).

Tenga en cuenta que ejecutar`findstr`con los mismos argumentos es recogido recientemente por`Windows Defender`como comportamiento sospechoso.

---

# **Prevención**

La mejor práctica para evitar estos ataques es bloquear cada acción en el dominio para que no haya permisos sueltos.

Técnicamente, no hay forma de evitar que los usuarios los dejen en scripts u otros archivos expuestos, por lo que es necesario realizar escaneos regulares (por ejemplo, semanales) en entornos AD para identificar nuevas acciones abiertas o credenciales expuestas en las más antiguas.

---

# **Detección**

Comprender y analizar el comportamiento de los usuarios es la mejor técnica de detección para abusar de las credenciales descubiertas en las acciones. Supongamos que sabemos el tiempo y la ubicación del inicio de sesión de los usuarios a través del análisis de datos. En ese caso, será sin esfuerzo alertar sobre comportamientos aparentemente sospechosos, por ejemplo, el "administrador" de cuenta descubierta en el ataque descrito anteriormente. Si fuéramos una organización madura que usaba`Privileged Access Workstation`, estaríamos alertas a usuarios privilegiados que no se autentican de esas máquinas. Estas serían alertas sobre ID de eventos`4624`/`4625`(inicio de sesión fallido y exitoso) y`4768`(Kerberos TGT solicitado).

A continuación se muestra un ejemplo de un inicio de sesión exitoso con ID de eventos`4624`Para la cuenta del administrador:

[](https://www.notion.so)

![](https://academy.hackthebox.com/storage/modules/176/A5/Detect4624.png)

Del mismo modo, si se usaron kerberos para la autenticación, ID de evento`4768`se generaría:

[](https://www.notion.so)

![](https://academy.hackthebox.com/storage/modules/176/A5/Detect4768.png)

Otra técnica de detección es descubrir el`one-to-many`conexiones, por ejemplo, cuando`Invoke-ShareFinder`Escanea cada dispositivo de dominio para obtener una lista de sus acciones de red. Sería anormal que una estación de trabajo se conecte a 100 o incluso 1000 de otros dispositivos simultáneamente.

---

# **Mielero**

Este ataque proporciona otra excelente razón para dejar a un usuario de honeypot en entornos publicitarios: un nombre de usuario semipivilegiado con un`wrong`contraseña. Un adversario solo puede descubrir esto si la contraseña se cambió después de la última modificación del archivo que contiene esta contraseña falsa expuesta. A continuación se muestra una buena configuración para la cuenta:

- A`service account`Eso fue creado hace más de 2 años. El último cambio de contraseña debería ser hace al menos un año.
- El último tiempo de modificación del archivo que contiene el`fake`La contraseña debe ser después del último cambio de contraseña de la cuenta. Debido a que es una contraseña falsa, no existe el riesgo de que un agente de amenazas comprometa la cuenta.
- La cuenta todavía está activa en el entorno.
- El guión que contiene las credenciales debe ser realista. (Por ejemplo, si elegimos un`MSSQL service account`, a`connection string`puede exponer las credenciales).

Debido a que la contraseña proporcionada es incorrecta, principalmente esperaríamos intentos de inicio de sesión fallidos. Tres ID de evento (`4625`, `4771`, y`4776`) puede indicar esto. Así es como se ven desde nuestro entorno de juegos si un atacante intenta autenticarse con la cuenta`svc-iis`y una contraseña incorrecta:

- `4625`

[](https://www.notion.so)

![](https://academy.hackthebox.com/storage/modules/176/A5/honeypot4dot3.png)

- `4771`

[](https://www.notion.so)

![](https://academy.hackthebox.com/storage/modules/176/A5/honeypot4.png)

- `4776`

[https://www.notion.so](https://www.notion.so)

![](https://academy.hackthebox.com/storage/modules/176/A5/honeypot4dot2.png)