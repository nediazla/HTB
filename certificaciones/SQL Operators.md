# SQL Operators

A veces, las expresiones con una sola condición no son suficientes para satisfacer los requisitos del usuario. Para eso, SQL admite[Operadores lógicos](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html)para usar múltiples condiciones a la vez. Los operadores lógicos más comunes son`AND`, `OR`, y`NOT`.

---

# **Y operador**

El`AND`El operador toma dos condiciones y devoluciones`true`o`false`Basado en su evaluación:

Código:sql

```sql
condition1 AND condition2

```

El resultado del`AND`la operación es`true`Si y solo si ambos`condition1`y`condition2`evaluar a`true`:

Operadores de SQL

```
mysql> SELECT 1 = 1 AND 'test' = 'test';

+---------------------------+
| 1 = 1 AND 'test' = 'test' |
+---------------------------+
|                         1 |
+---------------------------+
1 row in set (0.00 sec)

mysql> SELECT 1 = 1 AND 'test' = 'abc';

+--------------------------+
| 1 = 1 AND 'test' = 'abc' |
+--------------------------+
|                        0 |
+--------------------------+
1 row in set (0.00 sec)

```

En términos mysql, cualquiera`non-zero`El valor se considera`true`, y generalmente devuelve el valor`1`para indicar`true`. `0`se considera`false`. Como podemos ver en el ejemplo anterior, la primera consulta regresó`true`Como ambas expresiones fueron evaluadas como`true`. Sin embargo, la segunda consulta regresó`false`Como la segunda condición`'test' = 'abc'`es`false`.

---

# **U operador**

El`OR`El operador también toma dos expresiones y devuelve`true`Cuando al menos uno de ellos evalúa a`true`:

Operadores de SQL

```
mysql> SELECT 1 = 1 OR 'test' = 'abc';

+-------------------------+
| 1 = 1 OR 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set (0.00 sec)

mysql> SELECT 1 = 2 OR 'test' = 'abc';

+-------------------------+
| 1 = 2 OR 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set (0.00 sec)

```

Las consultas anteriores demuestran cómo el`OR`Operador trabaja. La primera consulta evaluada a`true`como la condición`1 = 1`es`true`. La segunda consulta tiene dos`false`condiciones, lo que resulta en`false`producción.

---

# **No operador**

El`NOT`El operador simplemente alterna un`boolean`valor 'es decir`true`se convierte en`false`y viceversa:

Operadores de SQL

```
mysql> SELECT NOT 1 = 1;

+-----------+
| NOT 1 = 1 |
+-----------+
|         0 |
+-----------+
1 row in set (0.00 sec)

mysql> SELECT NOT 1 = 2;

+-----------+
| NOT 1 = 2 |
+-----------+
|         1 |
+-----------+
1 row in set (0.00 sec)

```

Como se ve en los ejemplos anteriores, la primera consulta resultó en`false`porque es el inverso de la evaluación de`1 = 1`, que es`true`, entonces su inverso es`false`. Por otro lado, la segunda consulta regresó`true`, como el inverso de`1 = 2`'Que es`false`' es`true`.

---

# **Operadores de símbolos**

El`AND`, `OR`y`NOT`Los operadores también pueden representarse como`&&`, `||`y`!`, respectivamente. Los siguientes son los mismos ejemplos anteriores, mediante el uso de los operadores de símbolos:

Operadores de SQL

```
mysql> SELECT 1 = 1 && 'test' = 'abc';

+-------------------------+
| 1 = 1 && 'test' = 'abc' |
+-------------------------+
|                       0 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT 1 = 1 || 'test' = 'abc';

+-------------------------+
| 1 = 1 || 'test' = 'abc' |
+-------------------------+
|                       1 |
+-------------------------+
1 row in set, 1 warning (0.00 sec)

mysql> SELECT 1 != 1;

+--------+
| 1 != 1 |
+--------+
|      0 |
+--------+
1 row in set (0.00 sec)

```

---

# **Operadores en consultas**

Veamos cómo se pueden usar estos operadores en consultas. La siguiente consulta enumera todos los registros donde el`username`no es`john`:

Operadores de SQL

```
mysql> SELECT * FROM logins WHERE username != 'john';

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
3 rows in set (0.00 sec)

```

La siguiente consulta selecciona a los usuarios que tienen su`id`más que`1`Y`username`No igual a`john`:

Operadores de SQL

```
mysql> SELECT * FROM logins WHERE username != 'john' AND id > 1;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  4 | tom           | tom123!    | 2020-07-02 11:47:16 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)

```

---

# **Precedencia de operadores múltiples**

SQL admite varias otras operaciones, como la adición, la división y las operaciones bitwise. Por lo tanto, una consulta podría tener múltiples expresiones con múltiples operaciones a la vez. El orden de estas operaciones se decide a través de la precedencia del operador.

Aquí hay una lista de operaciones comunes y su precedencia, como se ve en el[Documentación de mariadb](https://mariadb.com/kb/en/operator-precedence/):

- División (`/`), Multiplicación () y módulo (`%`)
- Suma (`+`) y resta ()
- Comparación (`=`, `>`, `<`, `<=`, `>=`, `!=`, `LIKE`)
- NO (`!`)
- Y (`&&`)
- O (`||`)

Las operaciones en la parte superior se evalúan antes de las que están en la parte inferior de la lista. Veamos un ejemplo:

Código:sql

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 3 - 2;

```

La consulta tiene cuatro operaciones:`!=`, `AND`, `>`, y`-`. Desde la precedencia del operador, sabemos que la subtracción es lo primero, por lo que primero evaluará`3 - 2`a`1`:

Código:sql

```sql
SELECT * FROM logins WHERE username != 'tom' AND id > 1;

```

A continuación, tenemos dos operaciones de comparación,`>`y`!=`. Ambos son de la misma precedencia y serán evaluados juntos. Entonces, devolverá todos los registros donde el nombre de usuario no está`tom`, y todos los registros donde el`id`es mayor que 1 y luego aplica`AND`Para devolver todos los registros con ambas condiciones:

Operadores de SQL

```
mysql> select * from logins where username != 'tom' AND id > 3 - 2;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  2 | administrator | adm1n_p@ss | 2020-07-03 12:03:53 |
|  3 | john          | john123!   | 2020-07-03 12:03:57 |
+----+---------------+------------+---------------------+
2 rows in set (0.00 sec)

```

Veremos algunos otros escenarios de precedencia del operador en las próximas secciones.