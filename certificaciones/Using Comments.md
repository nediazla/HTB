# Using Comments

En esta sección, aprenderemos cómo usar comentarios para subvertir la lógica de consultas SQL más avanzadas y terminaremos con una consulta SQL de trabajo para evitar el proceso de autenticación de inicio de sesión.

---

# **Comentario**

Al igual que cualquier otro idioma, SQL también permite el uso de comentarios. Los comentarios se utilizan para documentar consultas o ignorar una determinada parte de la consulta. Podemos usar dos tipos de comentarios de línea con mysql`--` y`#`, además de un comentario en línea`/**/`(aunque esto no generalmente se usa en inyecciones SQL). El`--` se puede usar de la siguiente manera:

Usando comentarios

```
mysql> SELECT username FROM logins; -- Selects usernames from the logins table

+---------------+
| username      |
+---------------+
| admin         |
| administrator |
| john          |
| tom           |
+---------------+
4 rows in set (0.00 sec)

```

Nota: En SQL, usar dos guiones solo no es suficiente para comenzar un comentario. Entonces, tiene que haber un espacio vacío después de ellos, por lo que el comentario comienza con (-), con un espacio al final. Esto a veces se codifica como URL como (-+), ya que los espacios en las URL están codificados como (+). Para dejarlo claro, agregaremos otro (-) al final (--), para mostrar el uso de un personaje espacial.

El`#`El símbolo también se puede usar.

Usando comentarios

```
mysql> SELECT * FROM logins WHERE username = 'admin';# You can place anything here AND password = 'something'+----+----------+----------+---------------------+
| id | username | password | date_of_joining     |
+----+----------+----------+---------------------+
|  1 | admin    | p@ssw0rd | 2020-07-02 00:00:00 |
+----+----------+----------+---------------------+
1 row in set (0.00 sec)

```

Consejo: Si ingresa su carga útil en la URL dentro de un navegador, un símbolo (#) generalmente se considera una etiqueta, y no se pasará como parte de la URL. Para usar (#) como comentario dentro de un navegador, podemos usar '%23', que es un símbolo de URL codificado (#).

El servidor ignorará la parte de la consulta con`AND password = 'something'`durante la evaluación.

---

# **Auth bypass con comentarios**

Vamos a volver a nuestro ejemplo anterior e inyectar`admin'--` como nuestro nombre de usuario. La consulta final será:

Código:sql

```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';

```

Como podemos ver en el resaltado de la sintaxis, el nombre de usuario es ahora`admin`, y el resto de la consulta ahora se ignora como un comentario. Además, de esta manera, podemos asegurarnos de que la consulta no tenga ningún problema de sintaxis.

Intentemos usarlos en la página de inicio de sesión e inicie sesión con el nombre de usuario`admin'--` y cualquier cosa como contraseña:

![](https://academy.hackthebox.com/storage/modules/33/admin_dash.png)

Como vemos, pudimos evitar la autenticación, ya que la nueva consulta modificada verifica el nombre de usuario, sin otras condiciones.

---

# **Otro ejemplo**

SQL admite el uso de paréntesis si la aplicación necesita verificar las condiciones particulares antes de los demás. Las expresiones dentro del paréntesis tienen prioridad sobre otros operadores y se evalúan primero. Veamos un escenario como este:

![](https://academy.hackthebox.com/storage/modules/33/paranthesis_fail.png)

La consulta anterior asegura que la ID del usuario siempre sea mayor que 1, lo que evitará que cualquiera inicie sesión como administrador. Además, también vemos que la contraseña se ha hashado antes de ser utilizada en la consulta. Esto nos impedirá inyectar a través del campo de contraseña porque la entrada se cambia a un hash.

Intentemos iniciar sesión con credenciales válidas`admin / p@ssw0rd`para ver la respuesta.

![](https://academy.hackthebox.com/storage/modules/33/paranthesis_valid_fail.png)

Como se esperaba, el inicio de sesión falló a pesar de que suministramos credenciales válidas porque la identificación del administrador es igual a 1. Así que intentemos iniciar sesión con las credenciales de otro usuario, como`tom`.

![](https://academy.hackthebox.com/storage/modules/33/tom_login.png)

Iniciar sesión como usuario con una ID no igual a 1 fue exitoso. Entonces, ¿cómo podemos iniciar sesión como administrador? Sabemos por la sección anterior sobre comentarios que podemos usarlos para comentar el resto de la consulta. Entonces, intentemos usar`admin'--` como el nombre de usuario.

![](https://academy.hackthebox.com/storage/modules/33/paranthesis_error.png)

El inicio de sesión falló debido a un error de sintaxis, ya que uno cerrado no equilibró el paréntesis abierto. Para ejecutar la consulta con éxito, tendremos que agregar un paréntesis de cierre. Intentemos usar el nombre de usuario`admin')--` Para cerrar y comentar el resto.

![](https://academy.hackthebox.com/storage/modules/33/paranthesis_success.png)

La consulta fue exitosa y iniciamos sesión como administrador. La consulta final como resultado de nuestra entrada es:

Código:sql

```sql
SELECT * FROM logins where (username='admin')

```

La consulta anterior es como la del ejemplo anterior y devuelve la fila que contiene administrador.