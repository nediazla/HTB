# Union Injection

Ahora que sabemos cómo funciona la cláusula de la Unión y cómo usarla, permítanos aprender cómo utilizarla en nuestras inyecciones SQL. Tomemos el siguiente ejemplo:

![](https://academy.hackthebox.com/storage/modules/33/ports_cn.png)

Vemos una inyección potencial de SQL en los parámetros de búsqueda. Aplicamos los pasos de descubrimiento SQLI inyectando una sola cita (`'`), y recibimos un error:

![](https://academy.hackthebox.com/storage/modules/33/ports_quote.png)

Como causamos un error, esto puede significar que la página es vulnerable a la inyección SQL. Este escenario es ideal para la explotación a través de la inyección de la Unión, ya que podemos ver los resultados de nuestras consultas.

---

# **Detectar el número de columnas**

Antes de seguir adelante y explotar consultas basadas en la Unión, necesitamos encontrar el número de columnas seleccionadas por el servidor. Hay dos métodos para detectar el número de columnas:

- Usando`ORDER BY`
- Usando`UNION`

### **Usando orden por**

La primera forma de detectar el número de columnas es a través del`ORDER BY`función, que discutimos anteriormente. Tenemos que inyectar una consulta que clasifica los resultados mediante una columna que especificamos: 'es decir, columna 1, columna 2, etc.', hasta que recibamos un error diciendo que la columna especificada no existe.

Por ejemplo, podemos comenzar con`order by 1`, ordene por la primera columna y tenga éxito, ya que la tabla debe tener al menos una columna. Entonces lo haremos`order by 2`y luego`order by 3`Hasta que llegamos a un número que devuelva un error, o la página no muestra ninguna salida, lo que significa que este número de columna no existe. La columna exitosa final por la que ordenamos con éxito nos da el número total de columnas.

Si fallamos en`order by 4`, esto significa que la tabla tiene tres columnas, que es el número de columnas que pudimos ordenar con éxito. Volvamos a nuestro ejemplo anterior e intentemos lo mismo, con la siguiente carga útil:

Código:sql

```sql
' order by 1-- -

```

Recordatorio: estamos agregando un tablero adicional (-) al final, para mostrarle que hay un espacio después (-).

Como vemos, obtenemos un resultado normal:

![](https://academy.hackthebox.com/storage/modules/33/ports_cn.png)

A continuación, intentemos ordenar por la segunda columna, con la siguiente carga útil:

Código:sql

```sql
' order by 2-- -

```

Todavía obtenemos los resultados. Notamos que están ordenados de manera diferente, como se esperaba:

![](https://academy.hackthebox.com/storage/modules/33/order_by_2.jpg)

Hacemos lo mismo para la columna`3`y`4`Y recupere los resultados. Sin embargo, cuando intentamos`ORDER BY`columna 5, recibimos el siguiente error:

![](https://academy.hackthebox.com/storage/modules/33/order_by_5.jpg)

Esto significa que esta tabla tiene exactamente 4 columnas.

### **Uso de la Unión**

El otro método es intentar una inyección de la Unión con un número diferente de columnas hasta que recuperemos con éxito los resultados. El primer método siempre devuelve los resultados hasta que presionamos un error, mientras que este método siempre da un error hasta que obtengamos un éxito. Podemos comenzar inyectando una columna de 3`UNION`consulta:

Código:sql

```sql
cn' UNION select 1,2,3-- -

```

Recibimos un error que dice que el número de columnas no coincide:

![](https://academy.hackthebox.com/storage/modules/33/ports_columns_diff.png)

Entonces, probemos cuatro columnas y veamos la respuesta:

Código:sql

```sql
cn' UNION select 1,2,3,4-- -

```

![](https://academy.hackthebox.com/storage/modules/33/ports_columns_correct.png)

Esta vez obtenemos con éxito los resultados, lo que significa una vez más que la tabla tiene 4 columnas. Podemos usar cualquiera de los métodos para determinar el número de columnas. Una vez que sabemos el número de columnas, sabemos cómo formar nuestra carga útil, y podemos proceder al siguiente paso.

---

# **Ubicación de la inyección**

Si bien una consulta puede devolver varias columnas, la aplicación web solo puede mostrar algunas de ellas. Entonces, si inyectamos nuestra consulta en una columna que no está impresa en la página, no obtendremos su salida. Es por eso que necesitamos determinar qué columnas se imprimen en la página, para determinar dónde colocar nuestra inyección. En el ejemplo anterior, mientras la consulta inyectada devolvió 1, 2, 3 y 4, vimos solo 2, 3 y 4 que nos volvieron a mostrar en la página como datos de salida:

![](https://academy.hackthebox.com/storage/modules/33/ports_columns_correct.png)

Es muy común que no todas las columnas se mostrarán al usuario. Por ejemplo, el campo ID a menudo se usa para unir diferentes tablas juntas, pero el usuario no necesita verlo. Esto nos dice que las columnas 2 y 3, y 4 se imprimen para colocar nuestra inyección en cualquiera de ellos.`We cannot place our injection at the beginning, or its output will not be printed.`

Este es el beneficio de usar números como datos basura, ya que facilita el seguimiento de las columnas, por lo que sabemos en qué columna colocar nuestra consulta. Para probar que podemos obtener datos reales de la base de datos 'en lugar de solo números', podemos usar el`@@version`SQL consulta como prueba y colóquela en la segunda columna en lugar del número 2:

Código:sql

```sql
cn' UNION select 1,@@version,3,4-- -

```

![](https://academy.hackthebox.com/storage/modules/33/db_version_1.jpg)

Como podemos ver, podemos mostrar la versión de la base de datos. Ahora sabemos cómo formar nuestras cargas útiles de inyección SQL Union para obtener con éxito la salida de nuestra consulta impresa en la página. En la siguiente sección, discutiremos cómo enumerar la base de datos y obtener datos de otras tablas y bases de datos.