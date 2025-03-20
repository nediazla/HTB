# Database Enumeration

En las secciones anteriores, aprendimos sobre diferentes consultas SQL en`MySQL`e inyecciones de SQL y cómo usarlas. Esta sección pondrá todo eso para usar y recopilar datos de la base de datos utilizando consultas SQL dentro de las inyecciones SQL.

---

# **Mysql huellas dactilares**

Antes de enumerar la base de datos, generalmente necesitamos identificar el tipo de DBM que estamos tratando. Esto se debe a que cada DBMS tiene consultas diferentes, y saber qué es nos ayudará a saber qué consultas usar.

Como suposición inicial, si el servidor web que vemos en las respuestas HTTP es`Apache`o`Nginx`, es una buena suposición que el servidor web se está ejecutando en Linux, por lo que es probable que el DBMS sea`MySQL`. Lo mismo también se aplica a Microsoft DBMS si el servidor web es`IIS`, por lo que es probable que sea`MSSQL`. Sin embargo, esta es una suposición descabellada, ya que se pueden usar muchas otras bases de datos en el sistema operativo o en el servidor web. Por lo tanto, hay diferentes consultas que podemos probar para hacer huellas digitales el tipo de base de datos con la que estamos tratando.

Mientras cubrimos`MySQL`En este módulo, huella digital`MySQL`bases de datos. Las siguientes consultas y su producción nos dirán que estamos tratando con`MySQL`:

| **Carga útil** | **Cuando usar** | **Salida esperada** | **Salida incorrecta** |
| --- | --- | --- | --- |
| `SELECT @@version` | Cuando tenemos la salida de consulta completa | Versión mysql 'es decir`10.3.22-MariaDB-1ubuntu1`' | En MSSQL devuelve la versión MSSQL. Error con otros DBMS. |
| `SELECT POW(1,1)` | Cuando solo tenemos salida numérica | `1` | Error con otros DBMS |
| `SELECT SLEEP(5)` | Ciego/sin salida | Retrasa la respuesta de la página durante 5 segundos y devoluciones`0`. | No retrasará la respuesta con otros DBMS |

Como vimos en el ejemplo de la sección anterior, cuando lo intentamos`@@version`, nos dio:

![](https://academy.hackthebox.com/storage/modules/33/db_version_1.jpg)

La salida`10.3.22-MariaDB-1ubuntu1`significa que estamos lidiando con un`MariaDB`DBMS similar a MySQL. Como tenemos la salida de consulta directa, no tendremos que probar las otras cargas útiles. En cambio, podemos probarlos y ver lo que obtenemos.

---

# **Information_schema Database**

Para extraer datos de las tablas usando`UNION SELECT`, necesitamos formar adecuadamente nuestro`SELECT`consultas. Para hacerlo, necesitamos la siguiente información:

- Lista de bases de datos
- Lista de tablas dentro de cada base de datos
- Lista de columnas dentro de cada tabla

Con la información anterior, podemos formar nuestro`SELECT`Declaración para volcar los datos de cualquier columna en cualquier tabla dentro de cualquier base de datos dentro del DBMS. Aquí es donde podemos utilizar el`INFORMATION_SCHEMA`Base de datos.

El[Información_schema](https://dev.mysql.com/doc/refman/8.0/en/information-schema-introduction.html)La base de datos contiene metadatos sobre las bases de datos y tablas presentes en el servidor. Esta base de datos juega un papel crucial mientras explota las vulnerabilidades de inyección SQL. Como esta es una base de datos diferente, no podemos llamar a sus tablas directamente con un`SELECT`declaración. Si solo especificamos el nombre de una tabla para un`SELECT`Declaración, buscará tablas dentro de la misma base de datos.

Entonces, para hacer referencia a una tabla presente en otro DB, podemos usar el punto '`.`'Operador. Por ejemplo, a`SELECT`una mesa`users`presente en una base de datos nombrada`my_database`, podemos usar:

Código:sql

```sql
SELECT * FROM my_database.users;

```

Del mismo modo, podemos mirar las tablas presentes en el`INFORMATION_SCHEMA`Base de datos.

---

# **Esquema**

Para comenzar nuestra enumeración, debemos encontrar qué bases de datos están disponibles en el DBMS. La mesa[Esquema](https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html)en el`INFORMATION_SCHEMA`La base de datos contiene información sobre todas las bases de datos en el servidor. Se utiliza para obtener nombres de bases de datos para que luego podamos consultarlos. El`SCHEMA_NAME`La columna contiene todos los nombres de la base de datos actualmente presentes.

Primero probemos esto en una base de datos local para ver cómo se usa la consulta:

Enumeración de la base de datos

```
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
6 rows in set (0.01 sec)

```

Vemos el`ilfreight`y`dev`bases de datos.

Nota: Las primeras tres bases de datos son bases de datos MySQL predeterminadas y están presentes en cualquier servidor, por lo que generalmente las ignoramos durante la enumeración de DB. A veces también hay un cuarto DB 'SYS'.

Ahora, hagamos lo mismo usando un`UNION`Inyección SQL, con la siguiente carga útil:

Código:sql

```sql
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -

```

![](https://academy.hackthebox.com/storage/modules/33/ports_dbs.png)

Una vez más, vemos dos bases de datos,`ilfreight`y`dev`, aparte de los predeterminados. Descubra de qué base de datos se ejecuta la aplicación web para recuperar datos de puertos. Podemos encontrar la base de datos actual con el`SELECT database()`consulta. Podemos hacer esto de manera similar a cómo encontramos la versión DBMS en la sección anterior:

Código:sql

```sql
cn' UNION select 1,database(),2,3-- -

```

![](https://academy.hackthebox.com/storage/modules/33/db_name.jpg)

Vemos que el nombre de la base de datos es`ilfreight`. Sin embargo, la otra base de datos (`dev`) parece interesante. Entonces, intentemos recuperar las tablas de ella.

---

# **Mesas**

Antes de volcar los datos del`dev`base de datos, necesitamos obtener una lista de las tablas para consultarlas con un`SELECT`declaración. Para encontrar todas las tablas dentro de una base de datos, podemos usar el`TABLES`mesa en el`INFORMATION_SCHEMA`Base de datos.

El[Mesas](https://dev.mysql.com/doc/refman/8.0/en/information-schema-tables-table.html)La tabla contiene información sobre todas las tablas en toda la base de datos. Esta tabla contiene múltiples columnas, pero estamos interesados en el`TABLE_SCHEMA`y`TABLE_NAME`columnas. El`TABLE_NAME`columna almacena nombres de tabla, mientras que el`TABLE_SCHEMA`Puntos de columna a la base de datos a la que pertenece cada tabla. Esto se puede hacer de manera similar a cómo encontramos los nombres de la base de datos. Por ejemplo, podemos usar la siguiente carga útil para encontrar las tablas dentro del`dev`base de datos:

Código:sql

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -

```

Tenga en cuenta cómo reemplazamos los números '2' y '3' con 'table_name' y 'table_schema', para obtener la salida de ambas columnas en la misma consulta.

![](https://academy.hackthebox.com/storage/modules/33/ports_tables_1.jpg)

Nota: Agregamos una condición (donde table_schema = 'dev') para que solo devuelva las tablas de la base de datos 'Dev', de lo contrario obtendríamos todas las tablas en todas las bases de datos, que pueden ser muchas.

Vemos cuatro tablas en la base de datos de desarrollo, a saber`credentials`, `framework`, `pages`, y`posts`. Por ejemplo, el`credentials`La tabla podría contener información confidencial para investigarla.

---

# **Columnas**

Para volcar los datos del`credentials`tabla, primero debemos encontrar los nombres de la columna en la tabla, que se pueden encontrar en el`COLUMNS`mesa en el`INFORMATION_SCHEMA`base de datos. El[Columnas](https://dev.mysql.com/doc/refman/8.0/en/information-schema-columns-table.html)La tabla contiene información sobre todas las columnas presentes en todas las bases de datos. Esto nos ayuda a encontrar los nombres de la columna para consultar una tabla. El`COLUMN_NAME`, `TABLE_NAME`, y`TABLE_SCHEMA`Las columnas se pueden usar para lograr esto. Como lo hicimos antes, probemos esta carga útil para encontrar los nombres de la columna en el`credentials`mesa:

Código:sql

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -

```

![](https://academy.hackthebox.com/storage/modules/33/ports_columns_1.jpg)

La tabla tiene dos columnas nombradas`username`y`password`. Podemos usar esta información y volcar datos de la tabla.

---

# **Datos**

Ahora que tenemos toda la información, podemos formar nuestra`UNION`consulta para volcar los datos del`username`y`password`columnas del`credentials`mesa en el`dev`base de datos. Podemos colocar`username`y`password`en lugar de las columnas 2 y 3:

Código:sql

```sql
cn' UNION select 1, username, password, 4 from dev.credentials-- -

```

Recuerde: no olvide usar el operador DOT para referirse a las 'credenciales' en la base de datos 'DEV', ya que estamos ejecutando en la base de datos 'ilfreight', como se discutió anteriormente.

![](https://academy.hackthebox.com/storage/modules/33/ports_credentials_1.png)

Pudimos obtener todas las entradas en el`credentials`Tabla, que contiene información confidencial como hashs de contraseña y una clave API.