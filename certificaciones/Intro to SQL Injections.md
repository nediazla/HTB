# Intro to SQL Injections

Ahora que tenemos una idea general de cómo funcionan las consultas de MySQL y SQL, aprendemos sobre las inyecciones de SQL.

---

# **Uso de SQL en aplicaciones web**

Primero, veamos cómo las aplicaciones web usan bases de datos MySQL, en este caso, para almacenar y recuperar datos. Una vez que se instala y configuran un DBMS en el servidor de fondo y está en funcionamiento, las aplicaciones web pueden comenzar a utilizarlo para almacenar y recuperar datos.

Por ejemplo, dentro de un`PHP`Aplicación web, podemos conectarnos a nuestra base de datos y comenzar a usar el`MySQL`base de datos a través de`MySQL`Sintaxis, justo dentro`PHP`, como sigue:

Código:php

```php
$conn = new mysqli("localhost", "root", "password", "users");
$query = "select * from logins";
$result = $conn->query($query);

```

Luego, la salida de la consulta se almacenará en`$result`, y podemos imprimirlo en la página o usarlo de cualquier otra manera. El siguiente código PHP imprimirá todos los resultados devueltos de la consulta SQL en nuevas líneas:

Código:php

```php
while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
}

```

Las aplicaciones web también generalmente usan la entrada de usuario al recuperar datos. Por ejemplo, cuando un usuario usa la función de búsqueda para buscar a otros usuarios, su entrada de búsqueda se pasa a la aplicación web, que usa la entrada para buscar dentro de las bases de datos:

Código:php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);

```

`If we use user-input within an SQL query, and if not securely coded, it may cause a variety of issues, like SQL Injection vulnerabilities.`

---

# **¿Qué es una inyección?**

En el ejemplo anterior, aceptamos la entrada del usuario y la pasamos directamente a la consulta SQL sin desinfección.

La desinfección se refiere a la eliminación de cualquier caracteres especiales en la entrada del usuario, para romper cualquier intento de inyección.

La inyección ocurre cuando una aplicación malinterpreta la entrada del usuario como código real en lugar de una cadena, cambia el flujo de código y la ejecuta. Esto puede ocurrir escapando de los límites de entrada de usuario inyectando un carácter especial como (`'`), y luego escribir código para ser ejecutado, como el código JavaScript o SQL en inyecciones SQL. A menos que la entrada del usuario esté desinfectada, es muy probable que ejecute el código inyectado y ejecutelo.

---

# **Inyección SQL**

Se produce una inyección SQL cuando la entrada del usuario se ingresa en la cadena de consulta SQL sin desinfectar o filtrar correctamente la entrada. El ejemplo anterior mostró cómo se puede usar la entrada de usuario dentro de una consulta SQL, y no utilizó ninguna forma de desinfección de entrada:

Código:php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);

```

En casos típicos, el`searchInput`se ingresaría para completar la consulta, devolviendo el resultado esperado. Cualquier entrada que escribamos entra en la siguiente consulta SQL:

Código:sql

```sql
select * from logins where username like '%$searchInput'

```

Entonces, si ingresamos`admin`, se convierte en`'%admin'`. En este caso, si escribimos algún código SQL, solo se consideraría como un término de búsqueda. Por ejemplo, si ingresamos`SHOW DATABASES;`, se ejecutaría como`'%SHOW DATABASES;'`La aplicación web buscará nombres de usuario similar a`SHOW DATABASES;`. Sin embargo, como no hay desinfección, en este caso,**Podemos agregar una sola cita (`'`), que finalizará el campo de entrada de usuario, y después de él, podemos escribir un código SQL real**. Por ejemplo, si buscamos`1'; DROP TABLE users;`, la entrada de búsqueda sería:

Código:php

```php
'%1'; DROP TABLE users;'

```

Observe cómo agregamos una sola cotización (') después de "1", para escapar de los límites de la entrada del usuario en ('%$ SearchInput ').

Entonces, la consulta SQL final ejecutada sería la siguiente:

Código:sql

```sql
select * from logins where username like '%1'; DROP TABLE users;'

```

Como podemos ver en el resaltado de sintaxis, podemos escapar de los límites de la consulta original y hacer que nuestra consulta recién inyectada se ejecute también.`Once the query is run, the` usuarios `table will get deleted.`

Nota: En el ejemplo anterior, en aras de la simplicidad, agregamos otra consulta SQL después de un semicolón (;). Aunque esto en realidad no es posible con MySQL, es posible con MSSQL y PostgreSQL. En las próximas secciones, discutiremos los métodos reales para inyectar consultas SQL en MySQL.

---

# **Errores de sintaxis**

El ejemplo anterior de inyección SQL devolvería un error:

Código:php

```php
Error: near line 1: near "'": syntax error

```

Esto se debe al último personaje de final, donde tenemos una sola cita adicional (`'`) que no está cerrado, lo que causa un error de sintaxis SQL cuando se ejecuta:

Código:sql

```sql
select * from logins where username like '%1'; DROP TABLE users;'

```

En este caso, solo teníamos un personaje final, ya que nuestra opinión de la consulta de búsqueda estaba cerca del final de la consulta SQL. Sin embargo, la entrada del usuario generalmente va en el medio de la consulta SQL, y el resto de la consulta SQL original viene después de ella.

Para tener una inyección exitosa, debemos asegurarnos de que la consulta SQL recién modificada aún sea válida y no tenga ningún error de sintaxis después de nuestra inyección. En la mayoría de los casos, no tendríamos acceso al código fuente para encontrar la consulta SQL original y desarrollar una inyección SQL adecuada para hacer una consulta SQL válida. Entonces, ¿cómo podríamos inyectar en la consulta SQL con éxito?

Una respuesta es usando`comments`, y discutiremos esto en una sección posterior. Otra es hacer que la sintaxis de la consulta funcione pasando en múltiples citas individuales, como discutiremos a continuación (`'`).

Ahora que entendemos los conceptos básicos de las inyecciones de SQL, comencemos a aprender algunos usos prácticos.

---

# **Tipos de inyecciones SQL**

Las inyecciones de SQL se clasifican en función de cómo y dónde recuperamos su salida.

![](https://academy.hackthebox.com/storage/modules/33/types_of_sqli.jpg)

En casos simples, la salida de la consulta prevista y la nueva se puede imprimir directamente en la parte delantera, y podemos leerlo directamente. Esto se conoce como`In-band`Inyección SQL, y tiene dos tipos:`Union Based`y`Error Based`.

Con`Union Based`Inyección de SQL, es posible que tengamos que especificar la ubicación exacta, 'es decir, columna', que podemos leer, por lo que la consulta dirigirá la salida que se imprima allí. Para`Error Based`Inyección SQL, se usa cuando podemos obtener el`PHP`o`SQL`Errores en el front-end, por lo que podemos causar intencionalmente un error SQL que devuelva la salida de nuestra consulta.

En casos más complicados, es posible que no se imprima la salida, por lo que podemos utilizar la lógica SQL para recuperar el carácter de salida por carácter. Esto se conoce como`Blind`Inyección SQL, y también tiene dos tipos:`Boolean Based`y`Time Based`.

Con`Boolean Based`Inyección SQL, podemos usar declaraciones condicionales SQL para controlar si la página devuelve cualquier salida, 'es decir, respuesta de consulta original', si nuestra declaración condicional regresa`true`. Para`Time Based`Inyecciones de SQL, utilizamos declaraciones condicionales SQL que retrasan la respuesta de la página si la declaración condicional regresa`true`usando el`Sleep()`función.

Finalmente, en algunos casos, es posible que no tengamos acceso directo a la salida, por lo que es posible que tengamos que dirigir la salida a una ubicación remota, 'es decir, registro de DNS' e intentar recuperarla desde allí. Esto se conoce como`Out-of-band`Inyección SQL.

En este módulo, solo nos centraremos en introducir inyecciones de SQL al aprender sobre`Union Based`Inyección SQL.

[Anterior](https://academy.hackthebox.com/module/33/section/192) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/33/section/194)

Hoja de trucos