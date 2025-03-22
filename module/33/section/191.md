# Query Results

En esta sección, aprenderemos cómo controlar la salida de resultados de cualquier consulta.

---

# **Resultados de clasificación**

Podemos ordenar los resultados de cualquier consulta usando[Ordenar](https://dev.mysql.com/doc/refman/8.0/en/order-by-optimization.html)y especificando la columna para ordenar por:

Resultados de la consulta

```
mysql> SELECT * FROM logins ORDER BY password;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)

```

Por defecto, el tipo se realiza en orden ascendente, pero también podemos ordenar los resultados por`ASC`o`DESC`:

Resultados de la consulta

```
mysql> SELECT * FROM logins ORDER BY password DESC;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)

```

También es posible ordenar por varias columnas, tener un tipo secundario para valores duplicados en una columna:

Resultados de la consulta

```
mysql> SELECT * FROM logins ORDER BY password DESC, id ASC;

+----+---------------+-----------------+---------------------+
| id | username      | password        | date_of_joining     |
+----+---------------+-----------------+---------------------+
|  1 | admin         | p@ssw0rd        | 2020-07-02 00:00:00 |
|  2 | administrator | change_password | 2020-07-02 11:30:50 |
|  3 | john          | change_password | 2020-07-02 11:47:16 |
|  4 | tom           | change_password | 2020-07-02 11:50:20 |
+----+---------------+-----------------+---------------------+
4 rows in set (0.00 sec)

```

---

# **Limitar los resultados**

En caso de que nuestra consulta devuelva una gran cantidad de registros, podemos[LÍMITE](https://dev.mysql.com/doc/refman/8.0/en/limit-optimization.html)los resultados de lo que queremos solo, usando`LIMIT`y el número de registros que queremos:

Resultados de la consulta

```
mysql> SELECT * FROM logins LIMIT 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)

```

Si quisiéramos limitar los resultados con un desplazamiento, podríamos especificar el desplazamiento antes del recuento de límites:

Resultados de la consulta

```
mysql> SELECT * FROM logins LIMIT 1, 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)

```

Nota: El desplazamiento marca el orden del primer registro que se incluirá, a partir de 0. Para lo anterior, inicia e incluye el segundo registro, y devuelve dos valores.

---

# **Donde cláusula**

Para filtrar o buscar datos específicos, podemos usar condiciones con el`SELECT`declaración utilizando el[DÓNDE](https://dev.mysql.com/doc/refman/8.0/en/where-optimization.html)cláusula, para ajustar los resultados:

Código:sql

```sql
SELECT * FROM table_name WHERE <condition>;

```

La consulta anterior devolverá todos los registros que satisfacen la condición dada. Veamos un ejemplo:

Resultados de la consulta

```
mysql> SELECT * FROM logins WHERE id > 1;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
3 rows in set (0.00 sec)

```

El ejemplo anterior selecciona todos los registros donde el valor de`id`es mayor que`1`. Como podemos ver, la primera fila con su`id`Como 1 fue omitido de la salida. Podemos hacer algo similar para los nombres de usuario:

Resultados de la consulta

```
mysql> SELECT * FROM logins where username = 'admin';

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  1 | admin    | p@ssw0rd | 2020-07-02 00:00:00 |
+----+----------+----------+---------------------+
1 row in set (0.00 sec)

```

La consulta anterior selecciona el registro donde está el nombre de usuario`admin`. Podemos usar el`UPDATE`Declaración para actualizar ciertos registros que cumplan con una condición específica.

Nota: Los tipos de datos de cadena y fecha deben estar rodeados de una sola cita (') o cotizaciones dobles ("), mientras que los números se pueden usar directamente.

---

# **Cláusula como**

Otra cláusula SQL útil es[COMO](https://dev.mysql.com/doc/refman/8.0/en/pattern-matching.html), habilitando la selección de registros haciendo coincidir un determinado patrón. La consulta a continuación recupera todos los registros con nombres de usuario que comienzan con`admin`:

Resultados de la consulta

```
mysql> SELECT * FROM logins WHERE username LIKE 'admin%';

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  4 | administrator | adm1n_p@ss | 2020-07-02 15:19:02 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)

```

El`%`El símbolo actúa como un comodín y coincide con todos los personajes después`admin`. Se usa para que coincida con cero o más caracteres. Del mismo modo, el`_`El símbolo se usa para que coincida exactamente con un carácter. La siguiente consulta coincide con todos los nombres de usuario con exactamente tres caracteres en ellos, que en este caso fue`tom`:

Resultados de la consulta

```
mysql> SELECT * FROM logins WHERE username like '___';

+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  3 | tom      | tom123!  | 2020-07-02 15:18:56 |
+----+----------+----------+---------------------+
1 row in set (0.01 sec)
```