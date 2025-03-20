# Intro to MySQL

Este módulo introduce la inyección de SQL a través de`MySQL`, y es crucial aprender más sobre`MySQL`y SQL para comprender cómo funcionan las inyecciones de SQL y utilizarlas adecuadamente. Por lo tanto, esta sección cubrirá algunos de los conceptos básicos y la sintaxis de MySQL/SQL y los ejemplos utilizados dentro de las bases de datos MySQL/MariadB.

---

# **Lenguaje de consulta estructurada (SQL)**

La sintaxis SQL puede diferir de una RDBMS a otra. Sin embargo, todos están obligados a seguir el[Estándar ISO](https://en.wikipedia.org/wiki/ISO/IEC_9075)para lenguaje de consulta estructurada. Seguiremos la sintaxis mysql/mariadb para los ejemplos mostrados. SQL se puede usar para realizar las siguientes acciones:

- Recuperar datos
- Actualizar datos
- Eliminar datos
- Crear nuevas tablas y bases de datos
- Agregar / eliminar usuarios
- Asignar permisos a estos usuarios

---

# **Línea de comando**

El`mysql`La utilidad se utiliza para autenticarse e interactuar con una base de datos MySQL/mariadb. El`-u`La bandera se usa para suministrar el nombre de usuario y el`-p`bandera para la contraseña. El`-p`El indicador debe pasar vacío, por lo que se nos solicita que ingresemos la contraseña y no la pase directamente en la línea de comando, ya que podría almacenarse en ClearText en el archivo BASH_HISTORY.

Introducción a mysql

```
xnoxos@htb[/htb]$ mysql -u root -pEnter password: <password>
...SNIP...

mysql>

```

Nuevamente, también es posible usar la contraseña directamente en el comando, aunque esto debe evitarse, ya que podría llevar a la contraseña que se mantiene en registros e historial de terminal:

Introducción a mysql

```
xnoxos@htb[/htb]$ mysql -u root -p<password>

...SNIP...

mysql>

```

Consejo: no debería haber espacios entre '-p' y la contraseña.

Los ejemplos anteriores nos inician como el superusador, es decir, ","`root`"Con la contraseña"`password`, "Para tener privilegios para ejecutar todos los comandos. Otros usuarios de DBMS tendrían ciertos privilegios a qué declaraciones pueden ejecutar. Podemos ver qué privilegios tenemos utilizando el[Show subvenciones](https://dev.mysql.com/doc/refman/8.0/en/show-grants.html)comando que discutiremos más adelante.

Cuando no especificamos un host, se debe poner en forma predeterminada al`localhost`servidor. Podemos especificar un host y puerto remotos utilizando el`-h`y`-P`banderas.

Introducción a mysql

```
xnoxos@htb[/htb]$ mysql -u root -h docker.hackthebox.eu -P 3306 -p Enter password:
...SNIP...

mysql>

```

Nota: El puerto predeterminado MySQL/Mariadb es (3306), pero se puede configurar en otro puerto. Se especifica utilizando una mayúscula `P`, a diferencia de la minúscula` P` utilizada para las contraseñas.

Nota: Para seguir los ejemplos, intente usar la herramienta 'MySQL' en su Pwnbox para iniciar sesión en los DBM que se encuentran en la pregunta al final de la sección, utilizando su IP y puerto. Use 'root' para el nombre de usuario y 'contraseña' para la contraseña.

---

# **Creación de una base de datos**

Una vez que iniciamos sesión en la base de datos utilizando el`mysql`Utilidad, podemos comenzar a usar consultas SQL para interactuar con el DBMS. Por ejemplo, se puede crear una nueva base de datos dentro de MySQL DBMS usando el[Crear base de datos](https://dev.mysql.com/doc/refman/5.7/en/create-database.html)declaración.

Introducción a mysql

```
mysql> CREATE DATABASE users;

Query OK, 1 row affected (0.02 sec)

```

MySQL espera que las consultas de línea de comandos sean terminadas con un semi-colon. El ejemplo anterior creó una nueva base de datos llamada`users`. Podemos ver la lista de bases de datos con[Mostrar bases de datos](https://dev.mysql.com/doc/refman/8.0/en/show-databases.html), y podemos cambiar a la`users`base de datos con el`USE`declaración:

Introducción a mysql

```
mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| users              |
+--------------------+

mysql> USE users;

Database changed

```

Las declaraciones de SQL no son sensibles a los casos, lo que significa 'usar usuarios'; y 'usar usuarios;' Consulte el mismo comando. Sin embargo, el nombre de la base de datos es sensible a mayúsculas, por lo que no podemos hacer 'usar usuarios'; en lugar de 'usar usuarios;'. Por lo tanto, es una buena práctica especificar declaraciones en mayúsculas para evitar confusiones.

---

# **Mesas**

DBMS almacena datos en forma de tablas. Una tabla está compuesta de filas horizontales y columnas verticales. La intersección de una fila y una columna se llama celda. Cada tabla se crea con un conjunto fijo de columnas, donde cada columna es de un tipo de datos particular.

Un tipo de datos define qué tipo de valor debe mantener una columna. Los ejemplos comunes son`numbers`, `strings`, `date`, `time`, y`binary data`. También podría haber tipos de datos específicos de DBMS. Se puede encontrar una lista completa de tipos de datos en MySQL[aquí](https://dev.mysql.com/doc/refman/8.0/en/data-types.html). Por ejemplo, creemos una tabla llamada`logins`para almacenar datos de usuarios, utilizando el[Crear mesa](https://dev.mysql.com/doc/refman/8.0/en/creating-tables.html)Consulta SQL:

Código:sql

```sql
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
    );

```

Como podemos ver, el`CREATE TABLE`La consulta primero especifica el nombre de la tabla, y luego (dentro de los paréntesis) especificamos cada columna por su nombre y su tipo de datos, todos están separados. Después del nombre y el tipo, podemos especificar propiedades específicas, como se discutirá más adelante.

Introducción a mysql

```
mysql> CREATE TABLE logins (
    ->     id INT,
    ->     username VARCHAR(100),
    ->     password VARCHAR(100),
    ->     date_of_joining DATETIME
    ->     );
Query OK, 0 rows affected (0.03 sec)

```

Las consultas SQL anteriores crean una tabla llamada`logins`con cuatro columnas. La primera columna,`id`es un entero. Las siguientes dos columnas,`username`y`password`se establecen en cadenas de 100 caracteres cada una. Cualquier entrada más larga que esto dará como resultado un error. El`date_of_joining`columna de tipo`DATETIME`almacena la fecha en que se agregó una entrada.

Introducción a mysql

```
mysql> SHOW TABLES;

+-----------------+
| Tables_in_users |
+-----------------+
| logins          |
+-----------------+
1 row in set (0.00 sec)

```

Se puede obtener una lista de tablas en la base de datos actual utilizando el`SHOW TABLES`declaración. Además, el[DESCRIBIR](https://dev.mysql.com/doc/refman/8.0/en/describe.html)La palabra clave se utiliza para enumerar la estructura de la tabla con sus campos y tipos de datos.

Introducción a mysql

```
mysql> DESCRIBE logins;

+-----------------+--------------+
| Field           | Type         |
+-----------------+--------------+
| id              | int          |
| username        | varchar(100) |
| password        | varchar(100) |
| date_of_joining | date         |
+-----------------+--------------+
4 rows in set (0.00 sec)

```

### **Propiedades de tabla**

Dentro del`CREATE TABLE`consulta, hay muchos[propiedades](https://dev.mysql.com/doc/refman/8.0/en/create-table.html)Eso se puede configurar para la tabla y cada columna. Por ejemplo, podemos establecer el`id`columna a auto-incremento utilizando el`AUTO_INCREMENT`Palabra clave, que incrementa automáticamente el ID por uno cada vez que se agrega un nuevo elemento a la tabla:

Código:sql

```sql
    id INT NOT NULL AUTO_INCREMENT,

```

El`NOT NULL`La restricción asegura que una columna en particular nunca quede vacía 'es decir, campo requerido'. También podemos usar el`UNIQUE`restricción para garantizar que el elemento insertado sea siempre único. Por ejemplo, si lo usamos con el`username`Columna, podemos asegurarnos de que no hay dos usuarios que tengan el mismo nombre de usuario:

Código:sql

```sql
    username VARCHAR(100) UNIQUE NOT NULL,

```

Otra palabra clave importante es la`DEFAULT`Palabra clave, que se utiliza para especificar el valor predeterminado. Por ejemplo, dentro del`date_of_joining`columna, podemos establecer el valor predeterminado en[Ahora()](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_now), que en MySQL devuelve la fecha y hora actuales:

Código:sql

```sql
    date_of_joining DATETIME DEFAULT NOW(),

```

Finalmente, una de las propiedades más importantes es`PRIMARY KEY`, que podemos usar para identificar de manera única cada registro en la tabla, refiriéndose a todos los datos de un registro dentro de una tabla para bases de datos relacionales, como se discutió anteriormente en la sección anterior. Podemos hacer el`id`columna el`PRIMARY KEY`Para esta tabla:

Código:sql

```sql
    PRIMARY KEY (id)

```

La final`CREATE TABLE`La consulta será la siguiente:

Código:sql

```sql
CREATE TABLE logins (
    id INT NOT NULL AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL,
    date_of_joining DATETIME DEFAULT NOW(),
    PRIMARY KEY (id)
    );

```

Nota: Permita 10-15 segundos para que los servidores en las preguntas inicien, para permitir suficiente tiempo para que Apache/MySQL inicie y se ejecute.