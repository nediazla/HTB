# Dynamic Port Forwarding with SSH and SOCKS Tunneling

# **Reenvío de puertos en contexto**

`Port forwarding`es una técnica que nos permite redirigir una solicitud de comunicación de un puerto a otro. El reenvío de puertos utiliza TCP como la capa de comunicación principal para proporcionar comunicación interactiva para el puerto reenviado. Sin embargo, diferentes protocolos de capa de aplicación como SSH o incluso[MEDIAS](https://en.wikipedia.org/wiki/SOCKS)(Capa de no aplicación) se puede usar para encapsular el tráfico reenviado. Esto puede ser efectivo para evitar los firewalls y usar los servicios existentes en su host comprometido para pivot a otras redes.

---

# **Reenvío de puertos local SSH**

Tomemos un ejemplo de la imagen a continuación.

![](https://academy.hackthebox.com/storage/modules/158/11.png)

Nota: Cada diagrama de red presentado en este módulo está diseñado para ilustrar conceptos discutidos en la sección asociada. El direccionamiento IP que se muestra en los diagramas no siempre coincidirá con los entornos de laboratorio exactamente. ¡Asegúrese de concentrarse en comprender el concepto, y encontrará que los diagramas resultarán muy útiles! Después de leer esta sección, asegúrese de hacer referencia a la imagen anterior nuevamente para reforzar los conceptos.

Tenemos nuestro host de ataque (10.10.15.x) y un servidor de Ubuntu de destino (10.129.xx), que nos hemos comprometido. Escanearemos el servidor Ubuntu de destino utilizando NMAP para buscar puertos abiertos.

### **Escaneo del objetivo pivot**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ nmap -sT -p22,3306 10.129.202.64Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-24 12:12 EST
Nmap scan report for 10.129.202.64
Host is up (0.12s latency).

PORT     STATE  SERVICE
22/tcp   open   ssh
3306/tcp closed mysql

Nmap done: 1 IP address (1 host up) scanned in 0.68 seconds

```

La salida NMAP muestra que el puerto SSH está abierto. Para acceder al servicio MySQL, podemos ssh en el servidor y acceder a MySQL desde el interior del servidor Ubuntu, o podemos portarlo a nuestro localhost en el puerto`1234`y acceder a él localmente. Un beneficio de acceder a él localmente es que si queremos ejecutar un exploit remoto en el servicio MySQL, no podremos hacerlo sin el reenvío de puertos. Esto se debe a que MySQL se aloja localmente en el servidor Ubuntu en el puerto`3306`. Por lo tanto, utilizaremos el siguiente comando para reenviar nuestro puerto local (1234) sobre SSH al servidor Ubuntu.

### **Ejecutando el puerto local hacia adelante**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ ssh -L 1234:localhost:3306 ubuntu@10.129.202.64ubuntu@10.129.202.64's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 24 Feb 2022 05:23:20 PM UTC

  System load:             0.0
  Usage of /:              28.4% of 13.72GB
  Memory usage:            34%
  Swap usage:              0%
  Processes:               175
  Users logged in:         1
  IPv4 address for ens192: 10.129.202.64
  IPv6 address for ens192: dead:beef::250:56ff:feb9:52eb
  IPv4 address for ens224: 172.16.5.129

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

66 updates can be applied immediately.
45 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

```

El`-L`El comando le dice al cliente SSH que solicite al servidor SSH que reenvíe todos los datos que enviamos a través del puerto`1234`a`localhost:3306`en el servidor Ubuntu. Al hacer esto, deberíamos poder acceder al servicio MySQL localmente en el puerto 1234. Podemos usar NetStat o NMAP para consultar a nuestro host local en el puerto 1234 para verificar si el servicio MySQL fue reenviado.

### **Confirmando el puerto hacia adelante con NetStat**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ netstat -antp | grep 1234(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 127.0.0.1:1234          0.0.0.0:*               LISTEN      4034/ssh
tcp6       0      0 ::1:1234                :::*                    LISTEN      4034/ssh

```

### **Confirmando el puerto hacia adelante con NMAP**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ nmap -v -sV -p1234 localhostStarting Nmap 7.92 ( https://nmap.org ) at 2022-02-24 12:18 EST
NSE: Loaded 45 scripts for scanning.
Initiating Ping Scan at 12:18
Scanning localhost (127.0.0.1) [2 ports]
Completed Ping Scan at 12:18, 0.01s elapsed (1 total hosts)
Initiating Connect Scan at 12:18
Scanning localhost (127.0.0.1) [1 port]
Discovered open port 1234/tcp on 127.0.0.1
Completed Connect Scan at 12:18, 0.01s elapsed (1 total ports)
Initiating Service scan at 12:18
Scanning 1 service on localhost (127.0.0.1)
Completed Service scan at 12:18, 0.12s elapsed (1 service on 1 host)
NSE: Script scanning 127.0.0.1.
Initiating NSE at 12:18
Completed NSE at 12:18, 0.01s elapsed
Initiating NSE at 12:18
Completed NSE at 12:18, 0.00s elapsed
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0080s latency).
Other addresses for localhost (not scanned): ::1

PORT     STATE SERVICE VERSION
1234/tcp open  mysql   MySQL 8.0.28-0ubuntu0.20.04.3

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.18 seconds

```

Del mismo modo, si queremos reenviar múltiples puertos desde el servidor Ubuntu a su localhost, puede hacerlo incluyendo el`local port:server:port`argumento a tu comando ssh. Por ejemplo, el siguiente comando reenvía el puerto 80 del servidor web Apache al puerto local de su host de ataque en`8080`.

### **Reenvío de múltiples puertos**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ ssh -L 1234:localhost:3306 -L 8080:localhost:80 ubuntu@10.129.202.64
```

---

# **Configurar para pivote**

Ahora, si escribes`ifconfig`En el host de Ubuntu, encontrará que este servidor tiene múltiples NIC:

- Uno conectado a nuestro host de ataque (`ens192`)
- Uno que se comunica con otros hosts dentro de una red diferente (`ens224`)
- La interfaz de bucleback (`lo`).

### **Buscando oportunidades para pivotar usando ifconfig**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
ubuntu@WEB01:~$ ifconfig ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.129.202.64  netmask 255.255.0.0  broadcast 10.129.255.255
        inet6 dead:beef::250:56ff:feb9:52eb  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::250:56ff:feb9:52eb  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:52:eb  txqueuelen 1000  (Ethernet)
        RX packets 35571  bytes 177919049 (177.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10452  bytes 1474767 (1.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens224: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.5.129  netmask 255.255.254.0  broadcast 172.16.5.255
        inet6 fe80::250:56ff:feb9:a9aa  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:a9:aa  txqueuelen 1000  (Ethernet)
        RX packets 8251  bytes 1125190 (1.1 MB)
        RX errors 0  dropped 40  overruns 0  frame 0
        TX packets 1538  bytes 123584 (123.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 270  bytes 22432 (22.4 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 270  bytes 22432 (22.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

A diferencia del escenario anterior en el que sabíamos a qué puerto acceder, en nuestro escenario actual, no sabemos qué servicios se encuentran al otro lado de la red. Entonces, podemos escanear rangos más pequeños de IP en la red (`172.16.5.1-200`) red o toda la subred (`172.16.5.0/23`). No podemos realizar este escaneo directamente desde nuestro host de ataque porque no tiene rutas para el`172.16.5.0/23`red. Para hacer esto, tendremos que realizar`dynamic port forwarding`y`pivot`Nuestros paquetes de red a través del servidor Ubuntu. Podemos hacer esto comenzando un`SOCKS listener`en nuestro`local host`(Host de ataque personal o Pwnbox) y luego configure SSH para reenviar ese tráfico a través de SSH a la red (172.16.5.0/23) después de conectarse al host de destino.

Esto se llama`SSH tunneling`encima`SOCKS proxy`. Calcetines significa`Socket Secure`, un protocolo que ayuda a comunicarse con servidores donde tiene restricciones de firewall en su lugar. A diferencia de la mayoría de los casos en los que iniciaría una conexión para conectarse a un servicio, en el caso de los calcetines, el tráfico inicial es generado por un cliente de calcetines, que se conecta al servidor de calcetines controlado por el usuario que desea acceder a un servicio en el lado del cliente. Una vez que se establece la conexión, el tráfico de red se puede enrutar a través del servidor Socks en nombre del cliente conectado.

Esta técnica a menudo se usa para eludir las restricciones establecidas por los firewalls, y permitir que una entidad externa omita el firewall y acceda a un servicio dentro del entorno de firewalled. Un beneficio más de usar el proxy de calcetines para pivotar y reenviar datos es que los proxies de calcetines pueden pivotar mediante la creación de una ruta a un servidor externo desde`NAT networks`. Los proxies de calcetines son actualmente de dos tipos:`SOCKS4`y`SOCKS5`. SOCKS4 no proporciona ninguna autenticación y soporte UDP, mientras que SOCKS5 lo proporciona. Tomemos un ejemplo de la imagen a continuación donde tenemos una red NAT'd de 172.16.5.0/23, a la que no podemos acceder directamente.

![](https://academy.hackthebox.com/storage/modules/158/22.png)

En la imagen de arriba, el host de ataque inicia el cliente SSH y solicita al servidor SSH para permitirle enviar algunos datos TCP a través del enchufe SSH. El servidor SSH responde con un reconocimiento, y el cliente SSH luego comienza a escuchar`localhost:9050`. Cualquier información que envíe aquí se transmitirán a toda la red (172.16.5.0/23) a través de SSH. Podemos usar el siguiente comando para realizar este reenvío de puerto dinámico.

### **Habilitando el reenvío de puertos dinámicos con SSH**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ ssh -D 9050 ubuntu@10.129.202.64
```

El`-D`El argumento solicita el servidor SSH para habilitar el reenvío de puertos dinámicos. Una vez que tengamos esto habilitado, requeriremos una herramienta que pueda enrutar los paquetes de cualquier herramienta a través del puerto`9050`. Podemos hacer esto usando la herramienta`proxychains`, que es capaz de redirigir las conexiones TCP a través de los servidores proxy HTTP/HTTPS y también nos permite encadenar múltiples servidores proxy juntos. Usando proxyChains, también podemos ocultar la dirección IP del host de solicitud ya que el host receptor solo verá la IP del host Pivot. Proxychains a menudo se usa para forzar una aplicación`TCP traffic`pasar por proxies alojados como`SOCKS4`/`SOCKS5`, `TOR`, o`HTTP`/`HTTPS`proxies.

Para informar a ProxyChains que debemos usar el puerto 9050, debemos modificar el archivo de configuración de proxychains ubicado en`/etc/proxychains.conf`. Podemos agregar`socks4 127.0.0.1 9050`a la última línea si aún no está allí.

### **Compras /etc/proxychains.conf**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ tail -4 /etc/proxychains.conf# meanwile# defaults set to "tor"socks4 	127.0.0.1 9050

```

Ahora, cuando comience NMAP con ProxyChains utilizando el siguiente comando, enrutará todos los paquetes de NMAP al puerto local 9050, donde nuestro cliente SSH está escuchando, que reenviará todos los paquetes sobre SSH a la red 172.16.5.0/23.

### **Usar nmap con proxychains**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ proxychains nmap -v -sn 172.16.5.1-200ProxyChains-3.1 (http://proxychains.sf.net)

Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-24 12:30 EST
Initiating Ping Scan at 12:30
Scanning 10 hosts [2 ports/host]
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.2:80-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.5:80-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.6:80-<--timeout
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0

<SNIP>

```

Se llama a esta parte de empacar todos sus datos de NMAP utilizando proxyChains y reenviarlos a un servidor remoto`SOCKS tunneling`. Una nota más importante para recordar aquí es que solo podemos realizar un`full TCP connect scan`sobre proxychains. La razón de esto es que el proxychains no puede entender los paquetes parciales. Si envía paquetes parciales como medios escaneos, devolverá resultados incorrectos. También debemos asegurarnos de que somos conscientes del hecho de que`host-alive`Los cheques pueden no funcionar contra los objetivos de Windows porque el firewall de Windows Defender bloquea las solicitudes ICMP (pings tradicionales) de forma predeterminada.

[Un escaneo de conexión TCP completo](https://nmap.org/book/scan-methods-connect-scan.html)Sin hacer ping en una gama de red completa tomará mucho tiempo. Entonces, para este módulo, nos centraremos principalmente en escanear hosts individuales, o rangos de hosts más pequeños que sabemos que están vivos, lo que en este caso será un host de Windows en`172.16.5.19`.

Realizaremos un escaneo de sistema remoto utilizando el siguiente comando.

### **Enumerando el objetivo de Windows a través de proxychains**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ proxychains nmap -v -Pn -sT 172.16.5.19ProxyChains-3.1 (http://proxychains.sf.net)
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-24 12:33 EST
Initiating Parallel DNS resolution of 1 host. at 12:33
Completed Parallel DNS resolution of 1 host. at 12:33, 0.15s elapsed
Initiating Connect Scan at 12:33
Scanning 172.16.5.19 [1000 ports]
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:1720-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:587-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:445-<><>-OK
Discovered open port 445/tcp on 172.16.5.19
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:8080-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:23-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:135-<><>-OK
Discovered open port 135/tcp on 172.16.5.19
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:110-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:21-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:554-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-1172.16.5.19:25-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:5900-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:1025-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:143-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:199-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:993-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:995-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
Discovered open port 3389/tcp on 172.16.5.19
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:443-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:80-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:113-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:8888-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:139-<><>-OK
Discovered open port 139/tcp on 172.16.5.19

```

El escaneo NMAP muestra varios puertos abiertos, uno de los cuales es`RDP port`(3389). Similar a la exploración nmap, también podemos pivotar`msfconsole`a través de proxychains para realizar escaneos RDP vulnerables utilizando módulos auxiliares de metasploit. Podemos iniciar msfconsole con proxychains.

---

# **Uso de Metasploit con proxychains**

También podemos abrir el metasploit usando proxychains y enviar todo el tráfico asociado a través del proxy que hemos establecido.

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ proxychains msfconsoleProxyChains-3.1 (http://proxychains.sf.net)

     .~+P``````-o+:.                                      -o+:.
.+oooyysyyssyyssyddh++os-`````                        ```````````````          `
+++++++++++++++++++++++sydhyoyso/:.````...`...-///::+ohhyosyyosyy/+om++:ooo///o
++++///////~~~~///////++++++++++++++++ooyysoyysosso+++++++++++++++++++///oossosy
--.`                 .-.-...-////+++++++++++++++////////~~//////++++++++++++///
                                `...............`              `...-/////...`

                                  .::::::::::-.                     .::::::-
                                .hmMMMMMMMMMMNddds\...//M\\.../hddddmMMMMMMNo
                                 :Nm-/NMMMMMMMMMMMMM$$NMMMMm&&MMMMMMMMMMMMMMy                                 .sm/`-yMMMMMMMMMMMM$$MMMMMN&&MMMMMMMMMMMMMh`                                  -Nd`  :MMMMMMMMMMM$$MMMMMN&&MMMMMMMMMMMMh`                                   -Nh` .yMMMMMMMMMM$$MMMMMN&&MMMMMMMMMMMm/    `oo/``-hd:  ``                 .sNd  :MMMMMMMMMM$$MMMMMN&&MMMMMMMMMMm/      .yNmMMh//+syysso-``````       -mh` :MMMMMMMMMM$$MMMMMN&&MMMMMMMMMMd    .shMMMMN//dmNMMMMMMMMMMMMs`     `:```-o++++oooo+:/ooooo+:+o+++oooo++/
    `///omh//dMMMMMMMMMMMMMMMN/:::::/+ooso--/ydh//+s+/ossssso:--syN///os:
          /MMMMMMMMMMMMMMMMMMd.     `/++-.-yy/...osydh/-+oo:-`o//...oyodh+
          -hMMmssddd+:dMMmNMMh.     `.-=mmk.//^^^\\.^^`:++:^^o://^^^\\`::
          .sMMmo.    -dMd--:mN/`           ||--X--||          ||--X--||
........../yddy/:...+hmo-...hdd:............\\=v=//............\\=v=//.........
================================================================================
=====================+--------------------------------+=========================
=====================| Session one died of dysentery. |=========================
=====================+--------------------------------+=========================
================================================================================

                     Press ENTER to size up the situation

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%% Date: April 25, 1848 %%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%% Weather: It's always cool in the lab %%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%% Health: Overweight %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%% Caffeine: 12975 mg %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%% Hacked: All the things %%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

                        Press SPACE BAR to continue

       =[ metasploit v6.1.27-dev                          ]
+ -- --=[ 2196 exploits - 1162 auxiliary - 400 post       ]
+ -- --=[ 596 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: Adapter names can be used for IP params
set LHOST eth0

msf6 >

```

Usemos el`rdp_scanner`Módulo auxiliar para verificar si el host en la red interna está escuchando en 3389.

### **Uso del módulo rdp_scanner**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
msf6 > search rdp_scanner

Matching Modules
================

#  Name                               Disclosure Date  Rank    Check  Description   -  ----                               ---------------  ----    -----  -----------
   0  auxiliary/scanner/rdp/rdp_scanner                   normal  No     Identify endpoints speaking the Remote Desktop Protocol (RDP)

Interact with a module by name or index. For example info 0, use 0 or use auxiliary/scanner/rdp/rdp_scanner

msf6 > use 0
msf6 auxiliary(scanner/rdp/rdp_scanner) > set rhosts 172.16.5.19
rhosts => 172.16.5.19
msf6 auxiliary(scanner/rdp/rdp_scanner) > run
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK

[*] 172.16.5.19:3389      - Detected RDP on 172.16.5.19:3389      (name:DC01) (domain:DC01) (domain_fqdn:DC01) (server_fqdn:DC01) (os_version:10.0.17763) (Requires NLA: No)
[*] 172.16.5.19:3389      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

En la parte inferior de la salida anterior, podemos ver el puerto RDP abierto con la versión del sistema operativo Windows.

Dependiendo del nivel de acceso que tenemos a este host durante una evaluación, podemos intentar ejecutar una exploit o iniciar sesión utilizando credenciales reunidas. Para este módulo, iniciaremos sesión en el host remoto de Windows a través del túnel de calcetines. Esto se puede hacer usando`xfreerdp`. El usuario en nuestro caso es`victor,`y la contraseña es`pass@123`

### **Uso de xfreerdp con proxychains**

Reenvío de puertos dinámico con túneles SSH y calcetines

```
xnoxos@htb[/htb]$ proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123ProxyChains-3.1 (http://proxychains.sf.net)
[13:02:42:481] [4829:4830] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state
[13:02:42:482] [4829:4830] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr
[13:02:42:482] [4829:4830] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd
[13:02:42:482] [4829:4830] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr

```

El comando XFREERDP requerirá que se acepte un certificado RDP antes de establecer con éxito la sesión. Después de aceptarlo, debemos tener una sesión RDP, girando a través del servidor Ubuntu.

### **Pivote RDP exitoso**

![](https://academy.hackthebox.com/storage/modules/158/proxychaining.png)

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurada para que la conexión a su objetivo funcione sin problemas.