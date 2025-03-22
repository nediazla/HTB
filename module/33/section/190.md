# SQL Statements

Ahora que entendemos cómo usar el`mysql`Utilidad y crear bases de datos y tablas, veamos algunas de las declaraciones SQL esenciales y sus usos.

---

# **Declaración de insertar**

El[INSERTAR](https://dev.mysql.com/doc/refman/8.0/en/insert.html)La declaración se usa para agregar nuevos registros a una tabla determinada. La declaración siguiente a la siguiente sintaxis:

Código:sql

```sql
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);

```

La sintaxis anterior requiere que el usuario complete los valores para todas las columnas presentes en la tabla.

Declaraciones SQL

```
mysql> INSERT INTO logins VALUES(1, 'admin', 'p@ssw0rd', '2020-07-02');

Query OK, 1 row affected (0.00 sec)

```

El ejemplo anterior muestra cómo agregar un nuevo inicio de sesión a la tabla de inicios de sesión, con los valores apropiados para cada columna. Sin embargo, podemos omitir columnas de llenado con valores predeterminados, como`id`y`date_of_joining`. Esto se puede hacer especificando los nombres de la columna para insertar valores en una tabla selectivamente:

Código:sql

```sql
INSERT INTO table_name(column2, column3, ...) VALUES (column2_value, column3_value, ...);

```

Nota: omitir columnas con la restricción 'no nula' dará como resultado un error, ya que es un valor requerido.

Podemos hacer lo mismo para insertar valores en el`logins`mesa:

Declaraciones SQL

```
mysql> INSERT INTO logins(username, password) VALUES('administrator', 'adm1n_p@ss');

Query OK, 1 row affected (0.00 sec)

```

Insertamos un par de nombre de usuario de nombre de usuario en el ejemplo anterior mientras saltamos el`id`y`date_of_joining`columnas.

Nota: Los ejemplos insertan contraseñas de ClearText en la tabla, solo para demostración. Esta es una mala práctica, ya que las contraseñas siempre deben ser hash/encriptadas antes del almacenamiento.

También podemos insertar múltiples registros a la vez separándolos con una coma:

Declaraciones SQL

```
mysql> INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');

Query OK, 2 rows affected (0.00 sec)
Records: 2  Duplicates: 0  Warnings: 0

```

La consulta anterior insertó dos nuevos registros a la vez.

---

# **Declaración de selección**

Ahora que hemos insertado datos en tablas, vemos cómo recuperar datos con el[SELECCIONAR](https://dev.mysql.com/doc/refman/8.0/en/select.html)declaración. Esta declaración también se puede usar para muchos otros fines, que encontraremos más adelante. La sintaxis general para ver la tabla completa es la siguiente:

Código:sql

```sql
SELECT * FROM table_name;

```

El símbolo de asterisco (*) actúa como un comodín y selecciona todas las columnas. El`FROM`La palabra clave se usa para denotar la tabla para seleccionar. También es posible ver los datos presentes en columnas específicas:

Código:sql

```sql
SELECT column1, column2 FROM table_name;

```

La consulta anterior seleccionará los datos presentes en Column1 y Column2 solamente.

Declaraciones SQL

```
mysql> SELECT * FROM logins;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)

mysql> SELECT username,password FROM logins;

+---------------+------------+
| username      | password   |
+---------------+------------+
| admin         | p@ssw0rd   |
| administrator | adm1n_p@ss |
| john          | john123!   |
| tom           | tom123!    |
+---------------+------------+
4 rows in set (0.00 sec)

```

La primera consulta en el ejemplo anterior analiza todos los registros presentes en la tabla de inicios de sesión. Podemos ver los cuatro registros que se ingresaron antes. La segunda consulta selecciona solo las columnas de nombre de usuario y contraseña mientras se omite las otras dos.

---

# **Declaración de caída**

Podemos usar[GOTA](https://dev.mysql.com/doc/refman/8.0/en/drop-table.html)Para eliminar tablas y bases de datos del servidor.

Declaraciones SQL

```
mysql> DROP TABLE logins;

Query OK, 0 rows affected (0.01 sec)

mysql> SHOW TABLES;

Empty set (0.00 sec)

```

Como podemos ver, la mesa fue eliminada por completo.

La declaración 'Drop' eliminará de forma permanente y completamente la tabla sin confirmación, por lo que debe usarse con precaución.

---

# **Declaración alterar**

Finalmente, podemos usar[ALTERAR](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html)Para cambiar el nombre de cualquier tabla y cualquiera de sus campos o para eliminar o agregar una nueva columna a una tabla existente. El siguiente ejemplo agrega una nueva columna`newColumn`hacia`logins`mesa usando`ADD`:

Declaraciones SQL

```
mysql> ALTER TABLE logins ADD newColumn INT;

Query OK, 0 rows affected (0.01 sec)

```

Para cambiar el nombre de una columna, podemos usar`RENAME COLUMN`:

Declaraciones SQL

```
mysql> ALTER TABLE logins RENAME COLUMN newColumn TO newerColumn;

Query OK, 0 rows affected (0.01 sec)

```

También podemos cambiar el tipo de datos de una columna con`MODIFY`:

Declaraciones SQL

```
mysql> ALTER TABLE logins MODIFY newerColumn DATE;

Query OK, 0 rows affected (0.01 sec)

```

Finalmente, podemos soltar una columna usando`DROP`:

Declaraciones SQL

```
mysql> ALTER TABLE logins DROP newerColumn;

Query OK, 0 rows affected (0.01 sec)

```

Podemos usar cualquiera de las declaraciones anteriores con cualquier tabla existente, siempre que tengamos suficientes privilegios para hacerlo.

---

# **Declaración de actualización**

Mientras`ALTER`se utiliza para cambiar las propiedades de una tabla, el[ACTUALIZAR](https://dev.mysql.com/doc/refman/8.0/en/update.html)La declaración se puede utilizar para actualizar registros específicos dentro de una tabla, según ciertas condiciones. Su sintaxis general es:

Código:sql

```sql
UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE <condition>;

```

Especificamos el nombre de la tabla, cada columna y su nuevo valor, y la condición para actualizar los registros. Veamos un ejemplo:

Declaraciones SQL

```
mysql> UPDATE logins SET password = 'change_password' WHERE id > 1;

Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> SELECT * FROM logins;

+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:47:16 |
+----+---------------+-----------------+---------------------+
4 rows in set (0.00 sec)

```

La consulta anterior actualizó todas las contraseñas en todos los registros donde la ID era más significativa que 1.

Nota: Tenemos que especificar la cláusula 'Where' con actualización, para especificar qué registros se actualizan. La cláusula 'Where' se discutirá a continuación.