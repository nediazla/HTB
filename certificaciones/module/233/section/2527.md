# Detectar el paso de pase

# **Pases**

`Pass-the-Hash`es una técnica utilizada por los atacantes para autenticarse en un sistema en red utilizando el`NTLM`hash de la contraseña de un usuario en lugar de la contraseña de texto sin formato. El ataque aprovecha la forma en que Windows almacena hashas de contraseña en la memoria, lo que permite a los adversarios con acceso administrativo para capturar el hash y reutilizarlo para el movimiento lateral dentro de la red.

### **Pasos de ataque:**

- El atacante emplea herramientas como`Mimikatz`para extraer el`NTLM`Hash de un usuario actualmente inició sesión en el sistema comprometido. Tenga en cuenta que se requieren privilegios de administrador local en el sistema para extraer el hash del usuario.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image65.png)
    
- Armado con el`NTLM`Hash, el atacante puede autenticarse como el usuario dirigido en otros sistemas o recursos de red sin necesidad de conocer la contraseña real.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image52.png)
    
- Utilizando la sesión autenticada, el atacante puede moverse lateralmente dentro de la red, obteniendo acceso no autorizado a otros sistemas y recursos.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image62.png)
    

### **Tokens de acceso de Windows y credenciales alternativas**

Un`access token`es una estructura de datos que define el contexto de seguridad de un proceso o hilo. Contiene información sobre la identidad y los privilegios de la cuenta de usuario asociada. Cuando un usuario inicia sesión, el sistema verifica la contraseña del usuario al compararla con la información almacenada en una base de datos de seguridad. Si la contraseña se autentica, el sistema genera un token de acceso. Posteriormente, cualquier proceso ejecutado en nombre de ese usuario posee una copia de este token de acceso. (**Fuente**: [https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-tokens))

`Alternate Credentials`Proporcione una forma de proporcionar diferentes credenciales de inicio de sesión (nombre de usuario y contraseña) para acciones o procesos específicos sin alterar la sesión de inicio de sesión principal del usuario. Esto permite a un usuario o proceso ejecutar ciertos comandos o acceder a recursos como un usuario diferente sin cerrar la sesión o cambiar de cuentas de usuario. El`runas`El comando es una herramienta de línea de comandos de Windows que permite a los usuarios ejecutar comandos como otro usuario. Cuando el`runas`Se ejecuta el comando, se genera un nuevo token de acceso, que se puede verificar con el`whoami`dominio.

![](https://academy.hackthebox.com/storage/modules/233/image15.png)

El`runas`El comando también contiene una bandera interesante`/netonly`. Este indicador indica que la información del usuario especificada es solo para acceso remoto. A pesar de que el`whoami`El comando devuelve el nombre de usuario original, el engendrado`cmd.exe`todavía puede acceder a la carpeta de raíz del controlador de dominio.

![](https://academy.hackthebox.com/storage/modules/233/image5.png)

Cada`access token`Referencias a`LogonSession`Generado en el inicio de sesión del usuario. Este`LogonSession`La estructura de seguridad contiene información como nombre de usuario, dominio y autenticación (`NTHash/LMHash`), y se usa cuando el proceso intenta acceder a recursos remotos. Cuando el`netonly`Se utiliza la bandera, el proceso tiene lo mismo`access token`Pero un diferente`LogonSession`.

![](https://academy.hackthebox.com/storage/modules/233/image34.png)

### **Oportunidades de detección de pases**

Desde la perspectiva de registro de eventos de Windows, los siguientes registros se generan cuando el`runas`el comando se ejecuta:

- Cuando`runas`El comando se ejecuta sin el`/netonly`bandera -`Event ID 4624 (Logon)`con`LogonType 2 (interactive)`.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image38.png)
    
- Cuando`runas`El comando se ejecuta con el`/netonly`bandera -`Event ID 4624 (Logon)`con`LogonType 9 (NewCredentials)`.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image32.png)
    

La detección simple implicaría buscar`Event ID 4624`y`LogonType 9`, pero como se mencionó anteriormente, podría haber algunos falsos positivos relacionados con`runas`uso.

La principal diferencia entre`runas`con el`netonly`bandera y el`Pass-the-Hash`El ataque es que en el último caso,`Mimikatz`accederá al`LSASS`Procesar la memoria para cambiar`LogonSession`Materiales de credenciales. Por lo tanto, la detección inicial se puede mejorar correlacionando`User Logon with NewCredentials`eventos con`Sysmon Process Access Event Code 10`.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detección de pases-inh para Splunk**

Ahora exploremos cómo podemos identificar el paso de pase, usando Splunk.

Antes de pasar a revisar las búsquedas, consulte[este](https://blog.netwrix.com/2021/11/30/how-to-detect-pass-the-hash-attacks)fuente para obtener una mejor comprensión de dónde la parte de búsqueda`Logon_Process=seclogo`originado de.

**Periodo de tiempo**: `earliest=1690450689 latest=1690451116`

Detectar el paso de pase

```
index=main earliest=1690450708 latest=1690451116 source="WinEventLog:Security" EventCode=4624 Logon_Type=9 Logon_Process=seclogo
| table _time, ComputerName, EventCode, user, Network_Account_Domain, Network_Account_Name, Logon_Type, Logon_Process

```

![](https://academy.hackthebox.com/storage/modules/233/13.png)

---

Como ya se mencionó, podemos mejorar la búsqueda anterior agregando acceso de memoria LSASS a la mezcla de la siguiente manera.

Detectar el paso de pase

```
index=main earliest=1690450689 latest=1690451116 (source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=10 TargetImage="C:\\Windows\\system32\\lsass.exe" SourceImage!="C:\\ProgramData\\Microsoft\\Windows Defender\\platform\\*\\MsMpEng.exe") OR (source="WinEventLog:Security" EventCode=4624 Logon_Type=9 Logon_Process=seclogo)
| sort _time, RecordNumber
| transaction host maxspan=1m endswith=(EventCode=4624) startswith=(EventCode=10)
| stats count by _time, Computer, SourceImage, SourceProcessId, Network_Account_Domain, Network_Account_Name, Logon_Type, Logon_Process
| fields - count

```

![](https://academy.hackthebox.com/storage/modules/233/14.png)

**Desglose de búsqueda**:

- `index=main earliest=1690450689 latest=1690451116`: Filtra la búsqueda para incluir solo eventos del`main`índice que ocurrió entre las marcas de tiempo de época más tempranas y más recientes especificadas.
- `(source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=10 TargetImage="C:\\Windows\\system32\\lsass.exe" SourceImage!="C:\\ProgramData\\Microsoft\\Windows Defender\\platform\\*\\MsMpEng.exe")`: Filtra la búsqueda para incluir solo`Sysmon`Eventos de registro operativo con un`EventCode`de`10`(Acceso a procesos). Se reduce aún más los resultados a los eventos donde el`TargetImage`es`C:\Windows\system32\lsass.exe`(indicando que el`lsass.exe`se está accediendo al proceso) y el`SourceImage`no es un proceso legítimo conocido desde el directorio de defensa de Windows.
- `OR (source="WinEventLog:Security" EventCode=4624 Logon_Type=9 Logon_Process=seclogo)`: Filtra la búsqueda para incluir también eventos de registro de eventos de seguridad con un`EventCode`de`4624`(Iniciar sesión),`Logon_Type`de`9`(NewCredentials), y`Logon_Process`de`seclogo`.
- `| sort _time, RecordNumber`: Clasifica los eventos basados en el`_time`campo y luego el`RecordNumber`campo.
- `| transaction host maxspan=1m endswith=(EventCode=4624) startswith=(EventCode=10)`: Grupos eventos relacionados con los basados en el`host`campo, con un período de tiempo máximo de`1`minuto entre los eventos de inicio y final. Este comando se utiliza para asociar la orientación de eventos de acceso a procesos`lsass.exe`con eventos de inicio de sesión remoto.
- `| stats count by _time, Computer, SourceImage, SourceProcessId, Network_Account_Domain, Network_Account_Name, Logon_Type, Logon_Process`: Agregue los eventos en función de los campos especificados, contando el número de ocurrencias para cada combinación de valores de campo.
- `| fields - count`: Elimina el`count`campo de los resultados.