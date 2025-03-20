El`File Transfer Protocol` (`FTP`) es uno de los protocolos más antiguos en Internet. El FTP se ejecuta dentro de la capa de aplicación de la pila de protocolo TCP/IP. Por lo tanto, está en la misma capa que`HTTP`o`POP`. Estos protocolos también funcionan con el soporte de navegadores o clientes de correo electrónico para realizar sus servicios. También hay programas FTP especiales para el protocolo de transferencia de archivos.

Imaginemos que queremos subir archivos locales a un servidor y descargar otros archivos utilizando el[Ftp](https://datatracker.ietf.org/doc/html/rfc959)protocolo. En una conexión FTP, se abren dos canales. Primero, el cliente y el servidor establecen un canal de control a través de`TCP port 21`. El cliente envía comandos al servidor, y el servidor devuelve los códigos de estado. Entonces ambos participantes de la comunicación pueden establecer el canal de datos a través de`TCP port 20`. Este canal se utiliza exclusivamente para la transmisión de datos, y el protocolo observa los errores durante este proceso. Si se rompe una conexión durante la transmisión, el transporte se puede reanudar después del contacto restablecido.

Se hace una distinción entre`active`y`passive`Ftp. En la variante activa, el cliente establece la conexión como se describe a través del puerto TCP 21 y, por lo tanto, informa al servidor a través de qué puerto del lado del cliente el servidor puede transmitir sus respuestas. Sin embargo, si un firewall protege al cliente, el servidor no puede responder porque todas las conexiones externas están bloqueadas. Para este propósito, el`passive mode`ha sido desarrollado. Aquí, el servidor anuncia un puerto a través del cual el cliente puede establecer el canal de datos. Dado que el cliente inicia la conexión en este método, el firewall no bloquea la transferencia.

El FTP sabe diferente[comandos](https://web.archive.org/web/20230326204635/https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/)y códigos de estado. No todos estos comandos se implementan constantemente en el servidor. Por ejemplo, el lado del cliente instruye al lado del servidor a cargar o descargar archivos, organizar directorios o eliminar archivos. El servidor responde en cada caso con un código de estado que indica si el comando se implementó correctamente. Se puede encontrar una lista de posibles códigos de estado[aquí](https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes).

Por lo general, necesitamos credenciales para usar FTP en un servidor. También necesitamos saber que FTP es un`clear-text`Protocolo que a veces se puede oler si las condiciones en la red son correctas. Sin embargo, también existe la posibilidad de que ofrece un servidor`anonymous FTP`. El operador del servidor luego permite a cualquier usuario cargar o descargar archivos a través de FTP sin usar una contraseña. Dado que existen riesgos de seguridad asociados con dicho servidor FTP público, las opciones para los usuarios suelen ser limitadas.

---

# **TFTP**

`Trivial File Transfer Protocol` (`TFTP`) es más simple que FTP y realiza transferencias de archivos entre los procesos del cliente y el servidor. Sin embargo,`does not`Proporcione autenticación del usuario y otras características valiosas compatibles con FTP. Además, mientras FTP usa TCP, TFTP usa`UDP`, convirtiéndolo en un protocolo poco confiable y haciendo que use la recuperación de la capa de aplicación asistida por UDP.

Esto se refleja, por ejemplo, en el hecho de que TFTP, a diferencia de FTP, no requiere la autenticación del usuario. No admite el inicio de sesión protegido a través de contraseñas y establece límites en el acceso basado únicamente en los permisos de lectura y escritura de un archivo en el sistema operativo. Prácticamente, esto hace que TFTP funcione exclusivamente en directorios y con archivos que se han compartido con todos los usuarios y pueden leerse y escribir a nivel mundial. Debido a la falta de seguridad, TFTP, a diferencia de FTP, solo se puede usar en redes locales y protegidas.

Echemos un vistazo a algunos comandos de`TFTP`:

| **Comandos** | **Descripción** |
| --- | --- |
| `connect` | Establece el host remoto y opcionalmente el puerto, para transferencias de archivos. |
| `get` | Transfiere un archivo o conjunto de archivos del host remoto al host local. |
| `put` | Transfiere un archivo o conjunto de archivos del host local al host remoto. |
| `quit` | Sale tftp. |
| `status` | Muestra el estado actual de TFTP, incluido el modo de transferencia actual (ASCII o binario), estado de conexión, valor de tiempo de espera, etc. |
| `verbose` | GIRA MODO LETARIO, que muestra información adicional durante la transferencia de archivos, o desactiva. |

A diferencia del cliente FTP,`TFTP`no tiene funcionalidad de listado de directorio.

---

# **Configuración predeterminada**

Uno de los servidores FTP más utilizados en las distribuciones basadas en Linux es[VSFTPD](https://security.appspot.com/vsftpd.html). La configuración predeterminada de VSFTPD se puede encontrar en`/etc/vsftpd.conf`y algunas configuraciones ya están predefinidas de forma predeterminada. Se recomienda instalar el servidor VSFTPD en una VM y analizar más de cerca esta configuración.

### **Instalar VSFTPD**

Ftp

```
xnoxos@htb[/htb]$ sudo apt install vsftpd
```

El servidor VSFTPD es solo uno de los pocos servidores FTP disponibles para nosotros. Hay muchas alternativas diferentes, que también traen, entre otras cosas, muchas más funciones y opciones de configuración con ellas. Usaremos el servidor VSFTPD porque es una excelente manera de mostrar las posibilidades de configuración de un servidor FTP de una manera simple y fácil de entender sin entrar en los detalles de las páginas del hombre. Si observamos el archivo de configuración de VSFTPD, veremos muchas opciones y configuraciones que se comentan o comentan. Sin embargo, el archivo de configuración no contiene todas las configuraciones posibles que se pueden hacer. Los existentes y faltantes se pueden encontrar en el[página del hombre](http://vsftpd.beasts.org/vsftpd_conf.html).

### **archivo de configuración vsftpd**

Ftp

```
xnoxos@htb[/htb]$ cat /etc/vsftpd.conf | grep -v "#"
```

| **Configuración** | **Descripción** |
| --- | --- |
| `listen=NO` | ¿Corre desde Inetd o como un demonio independiente? |
| `listen_ipv6=YES` | ¿Escuchar en IPv6? |
| `anonymous_enable=NO` | Habilitar el acceso anónimo? |
| `local_enable=YES` | ¿Permiten a los usuarios locales iniciar sesión? |
| `dirmessage_enable=YES` | ¿Muestra mensajes de Active Directory cuando los usuarios entran en ciertos directorios? |
| `use_localtime=YES` | ¿Usar la hora local? |
| `xferlog_enable=YES` | ¿Activar el registro de cargas/descargas? |
| `connect_from_port_20=YES` | ¿Conectarse desde el puerto 20? |
| `secure_chroot_dir=/var/run/vsftpd/empty` | Nombre de un directorio vacío |
| `pam_service_name=vsftpd` | Esta cadena es el nombre del servicio PAM VSFTPD utilizará. |
| `rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem` | Las últimas tres opciones especifican la ubicación del certificado RSA para usar para las conexiones cifradas SSL. |
| `rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key` |  |
| `ssl_enable=NO` |  |

Además, hay un archivo llamado`/etc/ftpusers`que también debemos prestar atención, ya que este archivo se utiliza para negar a ciertos usuarios el acceso al servicio FTP. En el siguiente ejemplo, los usuarios`guest`, `john`, y`kevin`No se les permite iniciar sesión en el servicio FTP, incluso si existen en el sistema Linux.

### **Ftpusers**

Ftp

```
xnoxos@htb[/htb]$ cat /etc/ftpusersguest
john
kevin

```

---

# **Configuración peligrosa**

Hay muchas configuraciones relacionadas con la seguridad diferentes que podemos hacer en cada servidor FTP. Estos pueden tener varios propósitos, como las conexiones de prueba a través de los firewalls, las rutas de prueba y los mecanismos de autenticación. Uno de estos mecanismos de autenticación es el`anonymous`usuario. Esto a menudo se usa para permitir que todos en la red interna compartan archivos y datos sin acceder a las computadoras de los demás. Con vsftpd, el[Configuración opcional](http://vsftpd.beasts.org/vsftpd_conf.html)que se puede agregar al archivo de configuración para el inicio de sesión anónimo se ve así:

| **Configuración** | **Descripción** |
| --- | --- |
| `anonymous_enable=YES` | Permitiendo el inicio de sesión anónimo? |
| `anon_upload_enable=YES` | ¿Permitir que Anonymous cargue archivos? |
| `anon_mkdir_write_enable=YES` | ¿Permitir que Anónimo cree nuevos directorios? |
| `no_anon_password=YES` | ¿No le piden contraseña a Anonymous? |
| `anon_root=/home/username/ftp` | Directorio para Anónimo. |
| `write_enable=YES` | Permitir el uso de los comandos FTP: Stor, Dele, RNFR, RNTO, MKD, RMD, AppE y Sitio? |

Con el cliente FTP estándar (`ftp`), podemos acceder al servidor FTP en consecuencia e iniciar sesión con el usuario anónimo si se ha utilizado la configuración que se muestra arriba. El uso de la cuenta anónima puede ocurrir en entornos e infraestructuras internos donde todos los participantes son conocidos. El acceso a este tipo de servicio se puede establecer temporalmente o con la configuración para acelerar el intercambio de archivos.

Tan pronto como nos conectamos al servidor VSFTPD, el`response code 220`se muestra con el banner del servidor FTP. A menudo este banner contiene la descripción del`service`e incluso el`version`de eso. También nos dice qué tipo de sistema es el servidor FTP. Una de las configuraciones más comunes de los servidores FTP es permitir`anonymous`Acceso, que no requiere credenciales legítimas, pero proporciona acceso a algunos archivos. Incluso si no podemos descargarlos, a veces solo enumerar el contenido es suficiente para generar más ideas y anotar información que nos ayudará en otro enfoque.

### **Iniciar sesión anónimo**

Ftp

```
xnoxos@htb[/htb]$ ftp 10.129.14.136Connected to 10.129.14.136.
220 "Welcome to the HTB Academy vsFTP service."
Name (10.129.14.136:cry0l1t3): anonymous

230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls

200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.

```

Sin embargo, para obtener la primera descripción general de la configuración del servidor, podemos usar el siguiente comando:

### **Estado de VSFTPD**

Ftp

```
ftp> status

Connected to 10.129.14.136.
No proxy connection.
Connecting using address family: any.
Mode: stream; Type: binary; Form: non-print; Structure: file
Verbose: on; Bell: off; Prompting: on; Globbing: on
Store unique: off; Receive unique: off
Case: off; CR stripping: on
Quote control characters: on
Ntrans: off
Nmap: off
Hash mark printing: off; Use of PORT cmds: on
Tick counter printing: off

```

Algunos comandos deben usarse ocasionalmente, ya que estos harán que el servidor nos muestre más información que podemos usar para nuestros propósitos. Estos comandos incluyen`debug`y`trace`.

### **VSFTPD Salida detallada**

Ftp

```
ftp> debug

Debugging on (debug=1).

ftp> trace

Packet tracing on.

ftp> ls

---> PORT 10,10,14,4,188,195
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.

```

| **Configuración** | **Descripción** |
| --- | --- |
| `dirmessage_enable=YES` | ¿Mostrar un mensaje cuando ingresan por primera vez un nuevo directorio? |
| `chown_uploads=YES` | ¿Cambiar la propiedad de los archivos cargados de anónimo? |
| `chown_username=username` | Usuario que recibe propiedad de archivos cargados de forma anónima. |
| `local_enable=YES` | Habilitar a los usuarios locales para iniciar sesión? |
| `chroot_local_user=YES` | ¿Coloque a los usuarios locales en su directorio de inicio? |
| `chroot_list_enable=YES` | ¿Usa una lista de usuarios locales que se colocarán en su directorio de inicio? |

| **Configuración** | **Descripción** |
| --- | --- |
| `hide_ids=YES` | Toda la información de usuario y grupo en listados de directorio se mostrará como "FTP". |
| `ls_recurse_enable=YES` | Permite el uso de listados de recursión. |

En el siguiente ejemplo, podemos ver que si el`hide_ids=YES`La configuración está presente, la representación de UID y GUID del servicio se sobrescribirá, lo que nos dificulta identificarnos con los derechos que estos archivos se escriben y se cargan.

### **Ocultando identificaciones - sí**

Ftp

```
ftp> ls

---> TYPE A
200 Switching to ASCII mode.
ftp: setsockopt (ignored): Permission denied
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 ftp     ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp     ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp     ftp            0 Sep 15 14:57 testupload.txt
226 Directory send OK.

```

Esta configuración es una característica de seguridad para evitar que los nombres de usuario locales se revelen. Con los nombres de usuario, podríamos atacar los servicios como FTP y SSH y muchos otros con un ataque de fuerza bruta en teoría. Sin embargo, en realidad,[fail2ban](https://en.wikipedia.org/wiki/Fail2ban)Las soluciones ahora son una implementación estándar de cualquier infraestructura que registre la dirección IP y bloquea todo el acceso a la infraestructura después de un cierto número de intentos de inicio de sesión fallidos.

Otra configuración útil que podemos usar para nuestros propósitos es el`ls_recurse_enable=YES`. Esto a menudo se establece en el servidor VSFTPD para tener una mejor descripción general de la estructura del directorio FTP, ya que nos permite ver todo el contenido visible a la vez.

### **Listado recursivo**

Ftp

```
ftp> ls -R

---> PORT 10,10,14,4,222,149
200 PORT command successful. Consider using PASV.
---> LIST -R
150 Here comes the directory listing.
.:
-rw-rw-r--    1 ftp      ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp      ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp      ftp            0 Sep 15 14:57 testupload.txt

./Clients:
drwx------    2 ftp      ftp          4096 Sep 16 18:04 HackTheBox
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:00 Inlanefreight

./Clients/HackTheBox:
-rw-r--r--    1 ftp      ftp         34872 Sep 16 18:04 appointments.xlsx
-rw-r--r--    1 ftp      ftp        498123 Sep 16 18:04 contract.docx
-rw-r--r--    1 ftp      ftp        478237 Sep 16 18:04 contract.pdf
-rw-r--r--    1 ftp      ftp           348 Sep 16 18:04 meetings.txt

./Clients/Inlanefreight:
-rw-r--r--    1 ftp      ftp         14211 Sep 16 18:00 appointments.xlsx
-rw-r--r--    1 ftp      ftp         37882 Sep 16 17:58 contract.docx
-rw-r--r--    1 ftp      ftp            89 Sep 16 17:58 meetings.txt
-rw-r--r--    1 ftp      ftp        483293 Sep 16 17:59 proposal.pptx

./Documents:
-rw-r--r--    1 ftp      ftp         23211 Sep 16 18:05 appointments-template.xlsx
-rw-r--r--    1 ftp      ftp         32521 Sep 16 18:05 contract-template.docx
-rw-r--r--    1 ftp      ftp        453312 Sep 16 18:05 contract-template.pdf

./Employees:
226 Directory send OK.

```

`Downloading`Los archivos de dicho servidor FTP son una de las principales características, así como`uploading`archivos creados por nosotros. Esto nos permite, por ejemplo, usar vulnerabilidades de LFI para hacer que el host ejecute comandos del sistema. Además de los archivos, podemos ver, descargar e inspeccionar. Los ataques también son posibles con los registros FTP, lo que lleva a`Remote Command Execution` (`RCE`). Esto se aplica a los servicios FTP y a todos los que podemos detectar durante nuestra fase de enumeración.

### **Descargar un archivo**

Ftp

```
ftp> ls

200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rwxrwxrwx    1 ftp      ftp             0 Sep 16 17:24 Calendar.pptx
drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees
-rwxrwxrwx    1 ftp      ftp            41 Sep 18 15:58 Important Notes.txt
226 Directory send OK.

ftp> get Important\ Notes.txt

local: Important Notes.txt remote: Important Notes.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for Important Notes.txt (41 bytes).
226 Transfer complete.
41 bytes received in 0.00 secs (606.6525 kB/s)

ftp> exit

221 Goodbye.

```

Ftp

```
xnoxos@htb[/htb]$ ls | grep Notes.txt'Important Notes.txt'

```

También podemos descargar todos los archivos y carpetas a los que tenemos acceso a la vez. Esto es especialmente útil si el servidor FTP tiene muchos archivos diferentes en una estructura de carpeta más grande. Sin embargo, esto puede causar alarmas porque nadie de la compañía generalmente quiere descargar todos los archivos y contenido a la vez.

### **Descargue todos los archivos disponibles**

Ftp

```
xnoxos@htb[/htb]$ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/
           => ‘10.129.14.136/.listing’
Connecting to 10.129.14.136:21... connected.
Logging in as anonymous ... Logged in!
==> SYST ... done.    ==> PWD ... done.
==> TYPE I ... done.  ==> CWD not needed.
==> PORT ... done.    ==> LIST ... done.
12.12.1.136/.listing           [ <=>                                  ]     466  --.-KB/s    in 0s

2021-09-19 14:45:58 (65,8 MB/s) - ‘10.129.14.136/.listing’ saved [466]
--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/Calendar.pptx
           => ‘10.129.14.136/Calendar.pptx’
==> CWD not required.
==> SIZE Calendar.pptx ... done.
==> PORT ... done.    ==> RETR Calendar.pptx ... done.

...SNIP...

2021-09-19 14:45:58 (48,3 MB/s) - ‘10.129.14.136/Employees/.listing’ saved [119]

FINISHED --2021-09-19 14:45:58--
Total wall clock time: 0,03s
Downloaded: 15 files, 1,7K in 0,001s (3,02 MB/s)

```

Una vez que hayamos descargado todos los archivos,`wget`Creará un directorio con el nombre de la dirección IP de nuestro objetivo. Todos los archivos descargados se almacenan allí, que luego podemos inspeccionar localmente.

Ftp

```
xnoxos@htb[/htb]$ tree ..
└── 10.129.14.136
    ├── Calendar.pptx
    ├── Clients
    │   └── Inlanefreight
    │       ├── appointments.xlsx
    │       ├── contract.docx
    │       ├── meetings.txt
    │       └── proposal.pptx
    ├── Documents
    │   ├── appointments-template.xlsx
    │   ├── contract-template.docx
    │   └── contract-template.pdf
    ├── Employees
    └── Important Notes.txt

5 directories, 9 files

```

A continuación, podemos verificar si tenemos los permisos para cargar archivos en el servidor FTP. Especialmente con los servidores web, es común que los archivos estén sincronizados, y los desarrolladores tienen acceso rápido a los archivos. FTP a menudo se usa para este propósito, y la mayoría de las veces, los errores de configuración se encuentran en los servidores que los administradores creen que no se pueden descubrir. La actitud de que no se puede acceder a los componentes de la red internos desde el exterior significa que el endurecimiento de los sistemas internos a menudo se descuida y conduce a configuraciones erróneas.

La capacidad de cargar archivos en el servidor FTP conectado a un servidor web aumenta la probabilidad de obtener acceso directo al servidor web e incluso a un shell inverso que nos permite ejecutar comandos internos del sistema y tal vez incluso aumentar nuestros privilegios.

### **Subir un archivo**

Ftp

```
xnoxos@htb[/htb]$ touch testupload.txt
```

Con el`PUT`Comando, podemos cargar archivos en la carpeta actual en el servidor FTP.

Ftp

```
ftp> put testupload.txt

local: testupload.txt remote: testupload.txt
---> PORT 10,10,14,4,184,33
200 PORT command successful. Consider using PASV.
---> STOR testupload.txt
150 Ok to send data.
226 Transfer complete.

ftp> ls

---> TYPE A
200 Switching to ASCII mode.
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 1002     133             0 Sep 15 14:57 testupload.txt
226 Directory send OK.

```

---

# **Huella del servicio**

La huella utilizando varios escáneres de red también es un enfoque práctico y generalizado. Estas herramientas nos facilitan identificar diferentes servicios, incluso si no son accesibles en los puertos estándar. Una de las herramientas más utilizadas para este propósito es NMAP. Nmap también trae el[Motor de secuencias de comandos NMAP](https://nmap.org/book/nse.html) (`NSE`), un conjunto de muchos scripts diferentes escritos para servicios específicos. Se puede encontrar más información sobre las capacidades de NMAP y NSE en el[Enumeración de red con NMAP](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap)módulo. Podemos actualizar esta base de datos de scripts NSE con el comando que se muestra.

### **Scripts NMAP FTP**

Ftp

```
xnoxos@htb[/htb]$ sudo nmap --script-updatedbStarting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:49 CEST
NSE: Updating rule database.
NSE: Script Database updated successfully.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.28 seconds

```

Todos los scripts de NSE se encuentran en el Pwnbox en`/usr/share/nmap/scripts/`, pero en nuestros sistemas, podemos encontrarlos usando un comando simple en nuestro sistema.

Ftp

```
xnoxos@htb[/htb]$ find / -type f -name ftp*2>/dev/null | grep scripts/usr/share/nmap/scripts/ftp-syst.nse
/usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse
/usr/share/nmap/scripts/ftp-proftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-bounce.nse
/usr/share/nmap/scripts/ftp-libopie.nse
/usr/share/nmap/scripts/ftp-anon.nse
/usr/share/nmap/scripts/ftp-brute.nse

```

Como ya sabemos, el servidor FTP generalmente se ejecuta en el puerto TCP estándar 21, que podemos escanear usando NMAP. También usamos el escaneo de la versión (`-sV`), escaneo agresivo (`-A`), y el escaneo de script predeterminado (`-sC`) contra nuestro objetivo`10.129.14.136`.

### **Nmap**

Ftp

```
xnoxos@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-16 18:12 CEST
Nmap scan report for 10.129.14.136
Host is up (0.00013s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rwxrwxrwx    1 ftp      ftp       8138592 Sep 16 17:24 Calendar.pptx [NSE: writeable]
| drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees [NSE: writeable]
| -rwxrwxrwx    1 ftp      ftp            41 Sep 16 17:24 Important Notes.txt [NSE: writeable]
|_-rwxrwxrwx    1 ftp      ftp             0 Sep 15 14:57 testupload.txt [NSE: writeable]
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to 10.10.14.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status

```

El escaneo de script predeterminado se basa en las huellas digitales, las respuestas y los puertos estándar de los servicios. Una vez que NMAP ha detectado el servicio, ejecuta los scripts marcados uno tras otro, proporcionando información diferente. Por ejemplo, el[FTP-Anon](https://nmap.org/nsedoc/scripts/ftp-anon.html)El script NSE verifica si el servidor FTP permite el acceso anónimo. Si es así, el contenido del directorio raíz FTP se representa para el usuario anónimo.

El`ftp-syst`, por ejemplo, ejecuta el`STAT`Comando, que muestra información sobre el estado del servidor FTP. Esto incluye configuraciones, así como la versión del servidor FTP. NMAP también proporciona la capacidad de rastrear el progreso de los scripts de NSE a nivel de red si usamos el`--script-trace`opción en nuestros escaneos. Esto nos permite ver qué comandos envía NMAP, qué puertos se usan y qué respuestas recibimos del servidor escaneado.

### **Traza de script nmap**

Ftp

```
xnoxos@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-traceStarting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:54 CEST
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 16 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 24 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 32 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD#1 [10.129.14.136:21] (timeout: 7000ms) EID 42NSOCK INFO [11.4640s] nsock_read(): Read request from IOD#2 [10.129.14.136:21] (timeout: 9000ms) EID 50NSOCK INFO [11.4640s] nsock_read(): Read request from IOD#3 [10.129.14.136:21] (timeout: 7000ms) EID 58NSOCK INFO [11.4640s] nsock_read(): Read request from IOD#4 [10.129.14.136:21] (timeout: 11000ms) EID 66NSE: TCP 10.10.14.4:54226 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54228 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54230 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54232 > 10.129.14.136:21 | CONNECT
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 50 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 58 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSE: TCP 10.10.14.4:54228 < 10.129.14.136:21 | 220 Welcome to HTB-Academy FTP service.

```

El historial de escaneo muestra que cuatro escaneos paralelos diferentes se ejecutan contra el servicio, con varios tiempos de espera. Para los scripts NSE, vemos que nuestra máquina local usa otros puertos de salida (`54226`, `54228`, `54230`, `54232`) y primero inicia la conexión con el`CONNECT`dominio. Desde la primera respuesta del servidor, podemos ver que estamos recibiendo el banner del servidor a nuestro segundo script NSE (`54228`) desde el servidor FTP de destino. Si es necesario, podemos, por supuesto, usar otras aplicaciones como`netcat`o`telnet`para interactuar con el servidor FTP.

### **Interacción de servicio**

Ftp

```
xnoxos@htb[/htb]$ nc -nv 10.129.14.136 21
```

Ftp

```
xnoxos@htb[/htb]$ telnet 10.129.14.136 21
```

Se ve ligeramente diferente si el servidor FTP se ejecuta con el cifrado TLS/SSL. Porque entonces necesitamos un cliente que pueda manejar TLS/SSL. Para esto, podemos usar el cliente`openssl`y comunicarse con el servidor FTP. Lo bueno de usar`openssl`es que podemos ver el certificado SSL, que también puede ser útil.

Ftp

```
xnoxos@htb[/htb]$ openssl s_client -connect 10.129.14.136:21 -starttls ftpCONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1

depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb

 i:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
---

Server certificate

-----BEGIN CERTIFICATE-----

MIIENTCCAx2gAwIBAgIUD+SlFZAWzX5yLs2q3ZcfdsRQqMYwDQYJKoZIhvcNAQEL
...SNIP...

```

Esto se debe a que el certificado SSL nos permite reconocer el`hostname`, por ejemplo, y en la mayoría de los casos también un`email address`para la organización o empresa. Además, si la compañía tiene varias ubicaciones en todo el mundo, los certificados también se pueden crear para ubicaciones específicas, que también se pueden identificar utilizando el certificado SSL.