# Mitigating SQL Injection

Hemos aprendido sobre las inyecciones de SQL, por qué ocurren y cómo podemos explotarlas. También debemos aprender a evitar este tipo de vulnerabilidades en nuestro código y parcharlas cuando se encuentran. Veamos algunos ejemplos de cómo se puede mitigar la inyección SQL.

---

# **Desinfección de entrada**

Aquí está el fragmento del código de la sección Bypass de autenticación que discutimos anteriormente:

Código:php

```php
<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];

  $query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
  echo "Executing query: " . $query . "<br /><br />";

  if (!mysqli_query($conn ,$query))
  {
          die('Error: ' . mysqli_error($conn));
  }

  $result = mysqli_query($conn, $query);
  $row = mysqli_fetch_array($result);
<SNIP>

```

Como podemos ver, el guión toma el`username`y`password`de la solicitud de publicación y la pasa a la consulta directamente. Esto permitirá que un atacante inyecte todo lo que desee y explotará la aplicación. La inyección se puede evitar desinfectando cualquier entrada del usuario, lo que hace que las consultas inyectadas inyecten. Las bibliotecas proporcionan múltiples funciones para lograr esto, uno de ello es el[mysqli_real_escape_string ()](https://www.php.net/manual/en/mysqli.real-escape-string.php)función. Esta función escapa de caracteres como`'`y`"`, por lo que no tienen ningún significado especial.

Código:php

```php
<SNIP>
$username = mysqli_real_escape_string($conn, $_POST['username']);
$password = mysqli_real_escape_string($conn, $_POST['password']);

$query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
echo "Executing query: " . $query . "<br /><br />";
<SNIP>

```

El fragmento anterior muestra cómo se puede usar la función.

![](https://academy.hackthebox.com/storage/modules/33/mysqli_escape.png)

Como se esperaba, la inyección ya no funciona debido a escapar de las cotizaciones individuales. Un ejemplo similar es el[PG_ESCAPE_STRING ()](https://www.php.net/manual/en/function.pg-escape-string.php)que solía escapar de las consultas PostgreSQL.

---

# **Validación de entrada**

La entrada del usuario también se puede validar en función de los datos utilizados para consultar para garantizar que coincida con la entrada esperada. Por ejemplo, al tomar un correo electrónico como entrada, podemos validar que la entrada está en forma de`...@email.com`, etcétera.

Considere el siguiente fragmento de código de la página de puertos, que utilizamos`UNION`inyecciones en:

Código:php

```php
<?phpif (isset($_GET["port_code"])) {
	$q = "Select * from ports where port_code ilike '%" . $_GET["port_code"] . "%'";
	$result = pg_query($conn,$q);

	if (!$result)
	{
   		die("</table></div><p style='font-size: 15px;'>" . pg_last_error($conn). "</p>");
	}
<SNIP>
?>
```

Vemos el parámetro Get`port_code`siendo utilizado en la consulta directamente. Ya se sabe que un código de puerto consiste solo en letras o espacios. Podemos restringir la entrada del usuario solo a estos caracteres, lo que evitará la inyección de consultas. Se puede usar una expresión regular para validar la entrada:

Código:php

```php
<SNIP>
$pattern = "/^[A-Za-z\s]+$/";
$code = $_GET["port_code"];

if(!preg_match($pattern, $code)) {
  die("</table></div><p style='font-size: 15px;'>Invalid input! Please try again.</p>");
}

$q = "Select * from ports where port_code ilike '%" . $code . "%'";
<SNIP>

```

El código se modifica para usar el[preg_match ()](https://www.php.net/manual/en/function.preg-match.php)función, que verifica si la entrada coincide con el patrón dado o no. El patrón utilizado es`[A-Za-z\s]+`, que solo coincidirá con cadenas que contienen letras y espacios. Cualquier otro personaje dará como resultado la terminación del guión.

![](https://academy.hackthebox.com/storage/modules/33/postgres_copy_write.png)

Podemos probar la siguiente inyección:

Código:sql

```sql
'; SELECT 1,2,3,4-- -

```

![](https://academy.hackthebox.com/storage/modules/33/postgres_copy_write.png)

Como se ve en las imágenes anteriores, el servidor rechazó la entrada con consultas inyectadas.

---

# **Privilegios de usuario**

Como se discutió inicialmente, el software DBMS permite la creación de usuarios con permisos de grano fino. Debemos asegurarnos de que el usuario que consulta la base de datos solo tenga permisos mínimos.

Los superusadores y usuarios con privilegios administrativos nunca deben usarse con aplicaciones web. Estas cuentas tienen acceso a funciones y características, lo que podría conducir al compromiso del servidor.

Mitigación de la inyección de SQL

```
MariaDB [(none)]> CREATE USER 'reader'@'localhost';

Query OK, 0 rows affected (0.002 sec)

MariaDB [(none)]> GRANT SELECT ON ilfreight.ports TO 'reader'@'localhost' IDENTIFIED BY 'p@ssw0Rd!!';

Query OK, 0 rows affected (0.000 sec)

```

Los comandos anteriores agregan un nuevo usuario de mariadb llamado`reader`a quien solo se le concede`SELECT`privilegios en el`ports`mesa. Podemos verificar los permisos para este usuario iniciando sesión:

Mitigación de la inyección de SQL

```
xnoxos@htb[/htb]$ mysql -u reader -pMariaDB [(none)]> use ilfreight;
MariaDB [ilfreight]> SHOW TABLES;

+---------------------+
| Tables_in_ilfreight |
+---------------------+
| ports               |
+---------------------+
1 row in set (0.000 sec)

MariaDB [ilfreight]> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| information_schema |
| ilfreight          |
+--------------------+
2 rows in set (0.000 sec)

MariaDB [ilfreight]> SELECT * FROM ilfreight.credentials;
ERROR 1142 (42000): SELECT command denied to user 'reader'@'localhost' for table 'credentials'

```

El fragmento anterior confirma que el`reader`El usuario no puede consultar otras tablas en el`ilfreight`base de datos. El usuario solo tiene acceso al`ports`tabla que necesita la aplicación.

---

# **Firewall de aplicaciones web**

Los firewalls de aplicaciones web (WAF) se utilizan para detectar información maliciosa y rechazar cualquier solicitud HTTP que los contenga. Esto ayuda a prevenir la inyección de SQL incluso cuando la lógica de la aplicación es defectuosa. WAFS puede ser de código abierto (modificación) o premium (CloudFlare). La mayoría de ellos tienen reglas predeterminadas configuradas en función de los ataques web comunes. Por ejemplo, cualquier solicitud que contenga la cadena`INFORMATION_SCHEMA`sería rechazado, ya que se usa comúnmente mientras se explota la inyección de SQL.

---

# **Consultas parametrizadas**

Otra forma de garantizar que la entrada se desinfecte de forma segura es mediante las consultas parametrizadas. Las consultas parametrizadas contienen marcadores de posición para los datos de entrada, que luego escapan y transmiten los controladores. En lugar de pasar directamente los datos a la consulta SQL, usamos marcadores de posición y luego los llenamos con funciones de PHP.

Considere el siguiente código modificado:

Código:php

```php
<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];

  $query = "SELECT * FROM logins WHERE username=? AND password = ?" ;
  $stmt = mysqli_prepare($conn, $query);
  mysqli_stmt_bind_param($stmt, 'ss', $username, $password);
  mysqli_stmt_execute($stmt);
  $result = mysqli_stmt_get_result($stmt);

  $row = mysqli_fetch_array($result);
  mysqli_stmt_close($stmt);
<SNIP>

```

La consulta se modifica para contener dos marcadores de posición, marcados con`?`donde se colocarán el nombre de usuario y la contraseña. Luego vinculamos el nombre de usuario y la contraseña a la consulta utilizando el[mysqli_stmt_bind_param ()](https://www.php.net/manual/en/mysqli-stmt.bind-param.php)función. Esto escapará de forma segura cualquier cita y colocará los valores en la consulta.

---

# **Conclusión**

La lista anterior no es exhaustiva, y aún podría ser posible explotar la inyección de SQL en función de la lógica de la aplicación. Los ejemplos de código mostrados se basan en PHP, pero la lógica se aplica en todos los idiomas y bibliotecas comunes.