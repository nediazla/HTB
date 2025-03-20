# Introduction

Las vulnerabilidades son descubiertas comúnmente por personas que usan y entienden la tecnología, un protocolo o un servicio. A medida que evolucionemos en este campo, encontraremos diferentes servicios para interactuar, y necesitaremos evolucionar y aprender nuevas tecnologías constantemente.

Para tener éxito en atacar un servicio, necesitamos saber su propósito, cómo interactuar con él, qué herramientas podemos usar y qué podemos hacer con él. Esta sección se centrará en los servicios comunes y en cómo podemos interactuar con ellos.

---

# **Servicios para compartir el archivo**

Un servicio de intercambio de archivos es un tipo de servicio que proporciona, media y monitorea la transferencia de archivos de computadora. Hace años, las empresas comúnmente utilizan solo servicios internos para compartir archivos, como SMB, NFS, FTP, TFTP, SFTP, pero a medida que crece la adopción en la nube, la mayoría de las empresas ahora también tienen servicios en la nube de terceros como Dropbox, Google Drive, OneDrive, SharePoint u otras formas de almacenamiento de archivos como AWS S3, Azure Blob Storage o Google Cloud Storage. Estaremos expuestos a una mezcla de servicios de intercambio de archivos internos y externos, y debemos estar familiarizados con ellos.

Esta sección se centrará en los servicios internos, pero esto puede aplicarse al almacenamiento en la nube sincronizado localmente a servidores y estaciones de trabajo.

---

# **Bloque de mensajes del servidor (SMB)**

SMB se usa comúnmente en las redes de Windows, y a menudo encontraremos carpetas de compartir en una red de Windows. Podemos interactuar con SMB usando la GUI, CLI o herramientas. Cubremos algunas formas comunes de interactuar con SMB usando Windows y Linux.

### **Windows**

Hay diferentes formas en que podemos interactuar con una carpeta compartida usando Windows, y exploraremos un par de ellas. En Windows GUI, podemos presionar`[WINKEY] + [R]`Para abrir el cuadro de diálogo Ejecutar y escriba la ubicación de compartir el archivo, por ejemplo:`\\192.168.220.129\Finance\`

![](https://academy.hackthebox.com/storage/modules/116/windows_run_sharefolder2.jpg)

Supongamos que la carpeta compartida permite la autenticación anónima, o estamos autenticados con un usuario que tiene privilegios sobre esa carpeta compartida. En ese caso, no recibiremos ninguna forma de solicitud de autenticación, y mostrará el contenido de la carpeta compartida.

![](https://academy.hackthebox.com/storage/modules/116/finance_share_folder2.jpg)

Si no tenemos acceso, recibiremos una solicitud de autenticación.

![](https://academy.hackthebox.com/storage/modules/116/auth_request_share_folder2.jpg)

Windows tiene dos shells de línea de comandos: el[Shell de comando](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands)y[Powershell](https://docs.microsoft.com/en-us/powershell/scripting/overview). Cada shell es un programa de software que proporciona una comunicación directa entre nosotros y el sistema operativo o la aplicación, proporcionando un entorno para automatizar las operaciones de TI.

Discutamos algunos comandos para interactuar con el archivo compartido usando el shell de comando (`CMD`) y`PowerShell`. El comando[prostituta](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/dir)Muestra una lista de los archivos y subdirectorios de un directorio.

### **Windows CMD - Dir**

Interactuar con servicios comunes

```
C:\htb> dir \\192.168.220.129\Finance\

Volume in drive \\192.168.220.129\Finance has no label.
Volume Serial Number is ABCD-EFAA

Directory of \\192.168.220.129\Finance

02/23/2022  11:35 AM    <DIR>          Contracts
               0 File(s)          4,096 bytes
               1 Dir(s)  15,207,469,056 bytes free

```

El comando[uso neto](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/gg651155(v=ws.11))Conecta una computadora o desconecta una computadora de un recurso compartido o muestra información sobre las conexiones de la computadora. Podemos conectarnos a un archivo compartido con el siguiente comando y asignar su contenido a la letra de la unidad`n`.

### **Windows CMD - Uso de la red**

Interactuar con servicios comunes

```
C:\htb> net use n: \\192.168.220.129\Finance

The command completed successfully.

```

También podemos proporcionar un nombre de usuario y una contraseña para autenticar a la acción.

Interactuar con servicios comunes

```
C:\htb> net use n: \\192.168.220.129\Finance /user:plaintext Password123

The command completed successfully.

```

Con la carpeta compartida asignada como la`n`Drive, podemos ejecutar comandos de Windows como si esta carpeta compartida estuviera en nuestra computadora local. Encontremos cuántos archivos contienen la carpeta compartida y sus subdirectorios.

### **Windows CMD - Dir**

Interactuar con servicios comunes

```
C:\htb> dir n: /a-d /s /b | find /c ":\"

29302

```

Encontramos 29,302 archivos. Caminemos por el comando:

Interactuar con servicios comunes

```
dir n: /a-d /s /b | find /c ":\"

```

| **Sintaxis** | **Descripción** |
| --- | --- |
| `dir` | Solicitud |
| `n:` | Directorio o impulso para buscar |
| `/a-d` | `/a`es el atributo y`-d`significa no directorios |
| `/s` | Muestra archivos en un directorio especificado y todos los subdirectorios |
| `/b` | Utiliza formato desnudo (sin información o resumen de encabezado) |

El siguiente comando`| find /c ":\\"`procesar la salida de`dir n: /a-d /s /b`Para contar cuántos archivos existen en el directorio y los subdirectorios. Puedes usar`dir /?`para ver la ayuda completa. La búsqueda a través de 29,302 archivos lleva mucho tiempo, las utilidades de scripting y línea de comandos pueden ayudarnos a acelerar la búsqueda. Con`dir`Podemos buscar nombres específicos en archivos como:

- credir
- contraseña
- usuarios
- misterios
- llave
- Extensiones de archivo comunes para el código fuente como: .cs, .c, .go, .java, .php, .asp, .aspx, .html.

Interactuar con servicios comunes

```
C:\htb>dir n:\*cred* /s /b

n:\Contracts\private\credentials.txt

C:\htb>dir n:\*secret* /s /b

n:\Contracts\private\secret.txt

```

Si queremos buscar una palabra específica dentro de un archivo de texto, podemos usar[Findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr).

### **Windows CMD - Findstr**

Interactuar con servicios comunes

```
c:\htb>findstr /s /i cred n:\*.*

n:\Contracts\private\secret.txt:file with all credentials
n:\Contracts\private\credentials.txt:admin:SecureCredentials!

```

Podemos encontrar más`findstr`ejemplos[aquí](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr#examples).

### **Windows PowerShell**

PowerShell fue diseñado para extender las capacidades del shell de comando para ejecutar los comandos de PowerShell llamados`cmdlets`. Los cmdlets son similares a los comandos de Windows, pero proporcionan un lenguaje de secuencias de comandos más extensible. Podemos ejecutar los comandos de Windows y los cmdlets de PowerShell en PowerShell, pero el shell de comandos solo puede ejecutar comandos de Windows y no los cmdlets de PowerShell. Replicemos los mismos comandos ahora usando PowerShell.

### **Windows PowerShell**

Interactuar con servicios comunes

```powershell
PS C:\htb> Get-ChildItem \\192.168.220.129\Finance\

    Directory: \\192.168.220.129\Finance

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2/23/2022   3:27 PM                Contracts

```

En lugar de`net use`, podemos usar`New-PSDrive`en PowerShell.

Interactuar con servicios comunes

```powershell
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"

Name           Used (GB)     Free (GB) Provider      Root                                               CurrentLocation
----           ---------     --------- --------      ----                                               ---------------
N                                      FileSystem    \\192.168.220.129\Finance

```

Para proporcionar un nombre de usuario y contraseña con PowerShell, necesitamos crear un[Objeto PSCredential](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.pscredential). Ofrece una forma centralizada de administrar nombres de usuario, contraseñas y credenciales.

### **Windows PowerShell - Objeto PSCredential**

Interactuar con servicios comunes

```powershell
PS C:\htb> $username = 'plaintext'PS C:\htb> $password = 'Password123'PS C:\htb> $secpassword = ConvertTo-SecureString $password -AsPlainText -ForcePS C:\htb> $cred = New-Object System.Management.Automation.PSCredential $username, $secpasswordPS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $credName           Used (GB)     Free (GB) Provider      Root                                                              CurrentLocation
----           ---------     --------- --------      ----                                                              ---------------
N                                      FileSystem    \\192.168.220.129\Finance

```

En PowerShell, podemos usar el comando`Get-ChildItem`o la variante corta`gci`en lugar del comando`dir`.

### **Windows PowerShell - GCI**

Interactuar con servicios comunes

```powershell
PS C:\htb> N:
PS N:\> (Get-ChildItem -File -Recurse | Measure-Object).Count

29302

```

Podemos usar la propiedad`-Include`Para encontrar elementos específicos del directorio especificado por el parámetro de ruta.

Interactuar con servicios comunes

```powershell
PS C:\htb> Get-ChildItem -Recurse -Path N:\ -Include *cred* -File

    Directory: N:\Contracts\private

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/23/2022   4:36 PM             25 credentials.txt

```

El`Select-String`CMDLET utiliza una coincidencia de expresión regular para buscar patrones de texto en cadenas de entrada y archivos. Podemos usar`Select-String`similar a`grep`en unix o`findstr.exe`En Windows.

### **Windows PowerShell - Seleccione la cuerda**

Interactuar con servicios comunes

```powershell
PS C:\htb> Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List

N:\Contracts\private\secret.txt:1:file with all credentials
N:\Contracts\private\credentials.txt:1:admin:SecureCredentials!

```

CLI permite operaciones de TI para automatizar tareas rutinarias como administración de cuentas de usuario, copias de seguridad nocturnas o interacción con muchos archivos. Podemos realizar operaciones de manera más eficiente utilizando scripts que la interfaz de usuario o la GUI.

### **Linux**

Las máquinas Linux (UNIX) también se pueden usar para explorar y montar acciones SMB. Tenga en cuenta que esto se puede hacer si el servidor de destino es una máquina de Windows o un servidor Samba. Aunque algunas distribuciones de Linux admiten una GUI, nos centraremos en las utilidades y herramientas de línea de comandos de Linux para interactuar con SMB. Cubramos cómo montar las acciones de SMB para interactuar con directorios y archivos localmente.

### **Linux - Monte**

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ sudo mkdir /mnt/Financexnoxos@htb[/htb]$ sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
```

Como alternativa, podemos usar un archivo de credencial.

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile
```

El archivo`credentialfile`tiene que ser estructurado así:

### **Cedencial de credencial**

Código:TXT

```
username=plaintext
password=Password123
domain=.

```

Nota: necesitamos instalar`cifs-utils`para conectarse a una carpeta SMB Share. Para instalarlo podemos ejecutar desde la línea de comando`sudo apt install cifs-utils`.

Una vez que se monta una carpeta compartida, puede usar herramientas comunes de Linux como`find`o`grep`para interactuar con la estructura del archivo. Busquemos un nombre de archivo que contenga la cadena`cred`:

### **Linux - Encuentra**

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ find /mnt/Finance/ -name *cred*/mnt/Finance/Contracts/private/credentials.txt

```

A continuación, busque archivos que contengan la cadena`cred`:

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ grep -rn /mnt/Finance/ -ie cred/mnt/Finance/Contracts/private/credentials.txt:1:admin:SecureCredentials!
/mnt/Finance/Contracts/private/secret.txt:1:file with all credentials

```

---

# **Otros servicios**

Hay otros servicios de intercambio de archivos como FTP, TFTP y NFS que podemos adjuntar (montar) utilizando diferentes herramientas y comandos. Sin embargo, una vez que montamos un servicio de intercambio de archivos, debemos entender que podemos usar las herramientas disponibles en Linux o Windows para interactuar con archivos y directorios. A medida que descubramos nuevos servicios de intercambio de archivos, necesitaremos investigar cómo funcionan y qué herramientas podemos usar para interactuar con ellos.

### **Correo electrónico**

Por lo general, necesitamos dos protocolos para enviar y recibir mensajes, uno para enviar y otro para recibir. El Simple Mail Transfer Protocol (SMTP) es un protocolo de entrega de correo electrónico utilizado para enviar correo a través de Internet. Del mismo modo, se debe utilizar un protocolo de apoyo para recuperar un correo electrónico de un servicio. Hay dos protocolos principales que podemos usar POP3 e IMAP.

Podemos usar un cliente de correo como[Evolución](https://wiki.gnome.org/Apps/Evolution), el administrador oficial de información personal y el cliente de correo para el entorno de escritorio GNOME. Podemos interactuar con un servidor de correo electrónico para enviar o recibir mensajes con un cliente de correo. Para instalar Evolution, podemos usar el siguiente comando:

### **Linux - Instalar evolución**

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ sudo apt-get install evolution...SNIP...

```

Nota: Si aparece un error al iniciar la evolución que indica "BWrap: no se puede crear el archivo en ...", use este comando para iniciar la evolución`export WEBKIT_FORCE_SANDBOX=0 && evolution`.

### **Video: conectarse a IMAP y SMTP usando Evolution**

Haga clic en la imagen a continuación para ver una breve demostración de video.

![](https://academy.hackthebox.com/storage/modules/116/ConnectToIMAPandSMTP.jpg)

Podemos usar el nombre de dominio o la dirección IP del servidor de correo. Si el servidor usa SMTPS o IMAP, necesitaremos el método de cifrado apropiado (TLS en un puerto dedicado o STARTTLS después de conectarse). Podemos usar el`Check for Supported Types`opción en autenticación para confirmar si el servidor admite nuestro método seleccionado.

### **Bases de datos**

Las bases de datos generalmente se utilizan en empresas, y la mayoría de las empresas las usan para almacenar y administrar información. Existen diferentes tipos de bases de datos, como bases de datos jerárquicas, bases de datos NoSQL (o no relacionales) y bases de datos relacionales SQL. Nos centraremos en las bases de datos relacionales SQL y las dos bases de datos relacionales más comunes llamadas MySQL y MSSQL. Tenemos tres formas comunes de interactuar con bases de datos:

|  |  |
| --- | --- |
| `1.` | Utilidades de línea de comandos (`mysql`o`sqsh`) |
| `2.` | Lenguajes de programación |
| `3.` | Una aplicación GUI para interactuar con bases de datos como Heidisql, MySQL Workbench o SQL Server Management Studio. |

### **Ejemplo de MySQL**

![](https://academy.hackthebox.com/storage/modules/116/3_way_to_interact_with_MySQL.png)

Exploremos las utilidades de la línea de comandos y una aplicación GUI.

---

# **Utilidades de línea de comando**

### **MSSQL**

Interactuar con[MSSQL (Microsoft SQL Server)](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)Con Linux podemos usar[sqsh](https://en.wikipedia.org/wiki/Sqsh)o[sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility)Si está utilizando Windows.`Sqsh`es mucho más que un aviso amistoso. Está destinado a proporcionar gran parte de la funcionalidad proporcionada por un shell de comando, como variables, alias, redirección, tuberías, traseros, control de trabajo, historial, sustitución de comandos y configuración dinámica. Podemos iniciar una sesión SQL interactiva de la siguiente manera:

### **Linux - SQSH**

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ sqsh -S 10.129.20.13 -U username -P Password123
```

El`sqlcmd`La utilidad le permite ingresar las declaraciones de Transact-SQL, los procedimientos del sistema y los archivos de script a través de una variedad de modos disponibles:

- En el símbolo del sistema.
- En el editor de consultas en modo SQLCMD.
- En un archivo de script de Windows.
- En un paso de trabajo del sistema operativo (cmd.exe) de un trabajo de agente de servidor SQL.

### **Windows - SQLCMD**

Interactuar con servicios comunes

```
C:\htb> sqlcmd -S 10.129.20.13 -U username -P Password123

```

Para aprender más sobre`sqlcmd`uso, puedes ver[Documentación de Microsoft](https://docs.microsoft.com/en-us/sql/ssms/scripting/sqlcmd-use-the-utility).

### **Mysql**

Interactuar con[Mysql](https://en.wikipedia.org/wiki/MySQL), podemos usar binarios mysql para Linux (`mysql`) o Windows (`mysql.exe`). MySQL viene preinstalado en algunas distribuciones de Linux, pero podemos instalar binarios mysql para Linux o Windows usando esto[guía](https://dev.mysql.com/doc/mysql-getting-started/en/#mysql-getting-started-installing). Inicie una sesión SQL interactiva usando Linux:

### **Linux - mysql**

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ mysql -u username -pPassword123 -h 10.129.20.13
```

Podemos iniciar fácilmente una sesión SQL interactiva usando Windows:

### **Windows - MySQL**

Interactuar con servicios comunes

```
C:\htb> mysql.exe -u username -pPassword123 -h 10.129.20.13

```

### **Aplicación GUI**

Los motores de base de datos comúnmente tienen su propia aplicación GUI. Mysql tiene[MySQL Workbench](https://dev.mysql.com/downloads/workbench/)y MSSQL tiene[SQL Server Management Studio o SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms), podemos instalar esas herramientas en nuestro host de ataque y conectarnos a la base de datos. SSMS solo es compatible con Windows. Una alternativa es usar herramientas comunitarias como[dbeaver](https://github.com/dbeaver/dbeaver). [dbeaver](https://github.com/dbeaver/dbeaver)es una herramienta de base de datos multiplataforma para Linux, MacOS y Windows que admite conectarse a múltiples motores de bases de datos como MSSQL, MySQL, PostgreSQL, entre otros, lo que nos facilita, como atacante, interactuar con los servidores de bases de datos comunes.

Para instalar[dbeaver](https://github.com/dbeaver/dbeaver)Usando un paquete Debian podemos descargar el paquete .deb de versión desde[https://github.com/dbeaver/dbeaver/releases](https://github.com/dbeaver/dbeaver/releases)y ejecute el siguiente comando:

### **Instalar dbeaver**

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ sudo dpkg -i dbeaver-<version>.deb

```

Para iniciar el uso de la aplicación:

### **Ejecutar dbeaver**

Interactuar con servicios comunes

```
xnoxos@htb[/htb]$ dbeaver &
```

Para conectarse a una base de datos, necesitaremos un conjunto de credenciales, el número de IP y el puerto de destino de la base de datos, y el motor de la base de datos al que estamos tratando de conectar (MySQL, MSSQL u otro).

### **Video: conectarse a MSSQL DB usando DBeaver**

Haga clic en la imagen a continuación para obtener una breve demostración de video de la conexión a una base de datos MSSQL utilizando`dbeaver`.

![](https://academy.hackthebox.com/storage/modules/116/ConnectToMSSQL.jpg)

Haga clic en la imagen a continuación para obtener una breve demostración de video de conectarse a una base de datos MySQL usando`dbeaver`.

### **Video: conectarse a MySQL DB usando DBeaver**

![](https://academy.hackthebox.com/storage/modules/116/ConnectToMYSQL.jpg)

Una vez que tenemos acceso a la base de datos utilizando una utilidad de línea de comandos o una aplicación GUI, podemos usar Common[Declaraciones de transact-sql](https://docs.microsoft.com/en-us/sql/t-sql/statements/statements?view=sql-server-ver15)para enumerar bases de datos y tablas que contienen información confidencial, como nombres de usuario y contraseñas. Si tenemos los privilegios correctos, podríamos ejecutar comandos como la cuenta de servicio MSSQL. Más adelante en este módulo, discutiremos las declaraciones y ataques comunes de transact-SQL para las bases de datos MSSQL y MySQL.

### **Herramientas**

Es crucial familiarizarse con las utilidades de línea de comandos predeterminadas disponibles para interactuar con diferentes servicios. Sin embargo, a medida que avanzamos en el campo, encontraremos herramientas que pueden ayudarnos a ser más eficientes. La comunidad comúnmente crea esas herramientas. Aunque, eventualmente, tendremos ideas sobre cómo se puede mejorar una herramienta o para crear nuestras propias herramientas, incluso si no somos desarrolladores a tiempo completo, más familiarizamos con la piratería. Cuanto más aprendemos, más nos encontramos buscando una herramienta que no exista, que puede ser una oportunidad para aprender y crear nuestras herramientas.

### **Herramientas para interactuar con servicios comunes**

| **SMB** | **Ftp** | **Correo electrónico** | **Bases de datos** |
| --- | --- | --- | --- |
| [smbclient](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html) | [ftp](https://linux.die.net/man/1/ftp) | [Trueno](https://www.thunderbird.net/en-US/) | [MSSQL-Cli](https://github.com/dbcli/mssql-cli) |
| [Crackmapexec](https://github.com/byt3bl33d3r/CrackMapExec) | [LFTP](https://lftp.yar.ru/) | [Garras](https://www.claws-mail.org/) | [mycli](https://github.com/dbcli/mycli) |
| [Smbmap](https://github.com/ShawnDEvans/smbmap) | [NCFTP](https://www.ncftp.com/) | [Engranaje](https://wiki.gnome.org/Apps/Geary) | [mssqlclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py) |
| [Embalsar](https://github.com/SecureAuthCorp/impacket) | [filezilla](https://filezilla-project.org/) | [Correal](https://getmailspring.com/) | [dbeaver](https://github.com/dbeaver/dbeaver) |
| [psexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) | [cruzar](http://www.crossftp.com/) | [chucho](http://www.mutt.org/) | [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) |
| [smbexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) |  | [MailUtils](https://mailutils.org/) | [SQL Server Management Studio o SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms) |
|  |  | [Sendemail](https://github.com/mogaal/sendemail) |  |
|  |  | [swaks](http://www.jetmore.org/john/code/swaks/) |  |
|  |  | [sendmail](https://en.wikipedia.org/wiki/Sendmail) |  |

---

# **Solución general de problemas**

Dependiendo de la versión de Windows o Linux con la que estemos trabajando o la orientación, podemos encontrar diferentes problemas al intentar conectarnos a un servicio.

Algunas razones por las que es posible que no tengamos acceso a un recurso:

- Autenticación
- Privilegios
- Conexión de red
- Reglas de firewall
- Soporte de protocolo

Tenga en cuenta que podemos encontrar diferentes errores dependiendo del servicio al que apuntemos. Podemos usar los códigos de error para nuestra ventaja y buscar documentación oficial o foros donde las personas resolvieron un problema similar al nuestro.