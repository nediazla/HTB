# Subverting Query Logic

---

Ahora que tenemos una idea básica sobre cómo funcionan las declaraciones SQL, comenzar con la inyección SQL. Antes de comenzar a ejecutar consultas SQL completas, primero aprenderemos a modificar la consulta original inyectando el`OR`operador y uso de comentarios de SQL para subvertir la lógica de la consulta original. Un ejemplo básico de esto es evitar la autenticación web, que demostraremos en esta sección.

---

# **Bypass de autenticación**

Considere la siguiente página de inicio de sesión del administrador.

![](https://academy.hackthebox.com/storage/modules/33/admin_panel.png)

Podemos iniciar sesión con las credenciales del administrador`admin / p@ssw0rd`.

![](https://academy.hackthebox.com/storage/modules/33/admin_creds.png)

La página también muestra la consulta SQL que se ejecuta para comprender mejor cómo subvertiremos la lógica de consulta. Nuestro objetivo es iniciar sesión como usuario administrador sin usar la contraseña existente. Como podemos ver, la consulta SQL actual que se está ejecutando es:

Código:sql

```sql
SELECT * FROM logins WHERE username='admin' AND password = 'p@ssw0rd';

```

La página toma las credenciales, luego usa el`AND`Operador para seleccionar registros que coincidan con el nombre de usuario y la contraseña dados. Si el`MySQL`la base de datos devuelve registros coincidentes, las credenciales son válidas, por lo que el`PHP`El código evaluaría la condición del intento de inicio de sesión como`true`. Si la condición se evalúa a`true`, se devuelve el registro de administrador y nuestro inicio de sesión está validado. Veamos qué sucede cuando ingresamos credenciales incorrectas.

![](https://academy.hackthebox.com/storage/modules/33/admin_incorrect.png)

Como se esperaba, el inicio de sesión falló debido a la contraseña incorrecta que conduce a un`false`resultado de la`AND`operación.

---

# **Descubrimiento sqli**

Antes de comenzar a subvertir la lógica de la aplicación web e intentar omitir la autenticación, primero tenemos que probar si el formulario de inicio de sesión es vulnerable a la inyección SQL. Para hacer eso, intentaremos agregar una de las cargas útiles a continuación después de nuestro nombre de usuario y ver si causa algún error o cambia cómo se comporta la página:

| **Carga útil** | **URL codificada** |
| --- | --- |
| `'` | `%27` |
| `"` | `%22` |
| `#` | `%23` |
| `;` | `%3B` |
| `)` | `%29` |

Nota: En algunos casos, es posible que tengamos que usar la versión codificada por URL de la carga útil. Un ejemplo de esto es cuando colocamos nuestra carga útil directamente en la URL 'es decir, la solicitud de obtención de HTTP'.

Entonces, comencemos por inyectar una sola cotización:

![](https://academy.hackthebox.com/storage/modules/33/quote_error.png)

Vemos que se lanzó un error SQL en lugar del`Login Failed`mensaje. La página arrojó un error porque la consulta resultante fue:

Código:sql

```sql
SELECT * FROM logins WHERE username=''' AND password = 'something';

```

Como se discutió en la sección anterior, la cita que ingresamos dio como resultado un número impar de citas, causando un error de sintaxis. Una opción sería comentar el resto de la consulta y escribir el resto de la consulta como parte de nuestra inyección para formar una consulta de trabajo. Otra opción es usar un número par de citas dentro de nuestra consulta inyectada, de modo que la consulta final aún funcionaría.

---

# **O inyección**

Necesitaríamos la consulta siempre para regresar`true`, independientemente del nombre de usuario y la contraseña ingresados, para evitar la autenticación. Para hacer esto, podemos abusar del`OR`operador en nuestra inyección SQL.

Como se discutió anteriormente, la documentación de MySQL para[Precedencia de operación](https://dev.mysql.com/doc/refman/8.0/en/operator-precedence.html)afirma que el`AND`El operador sería evaluado antes del`OR`operador. Esto significa que si hay al menos uno`TRUE`condición en toda la consulta junto con un`OR`operador, toda la consulta evaluará para`TRUE`desde el`OR`Devoluciones del operador`TRUE`Si uno de sus operandos es`TRUE`.

Un ejemplo de una condición que siempre regresará`true`es`'1'='1'`. Sin embargo, para mantener la consulta SQL funcionando y mantener un número uniforme de citas, en lugar de usar ('1' = '1'), eliminaremos la última cita y usaremos ('1' = '1), por lo que la cita única restante de la consulta original estaría en su lugar.

Entonces, si inyectamos la siguiente condición y tenemos un`OR`operador entre él y la condición original, siempre debe regresar`true`:

Código:sql

```sql
admin' or '1'='1

```

La consulta final debe ser la siguiente:

Código:sql

```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';

```

Esto significa lo siguiente:

- Si el nombre de usuario es`adminOR`
- Si`1=1`devolver`true`'que siempre regresa`true`'`AND`
- Si la contraseña es`something`

![](https://academy.hackthebox.com/storage/modules/33/or_inject_diagram.png)

El`AND`El operador será evaluado primero y volverá`false`. Entonces, el`OR`El operador se evaluaría, y si alguna de las declaraciones es`true`, volvería`true`. Desde`1=1`siempre regresa`true`, esta consulta volverá`true`, y nos otorgará acceso.

Nota: La carga útil que usamos anteriormente es una de las muchas cargas de omisión de autores que podemos usar para subvertir la lógica de autenticación. Puede encontrar una lista completa de cargas útiles de derivación de autores SQLI en[Alligos de carga útil](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass), cada uno de los cuales funciona en un cierto tipo de consultas SQL.

---

# **Auth bypass con o operador**

Intentemos esto como el nombre de usuario y veamos la respuesta.

![](https://academy.hackthebox.com/storage/modules/33/inject_success.png)

Pudimos iniciar sesión con éxito como administrador. Sin embargo, ¿qué pasaría si no conociéramos un nombre de usuario válido? Probemos la misma solicitud con un nombre de usuario diferente esta vez.

![](https://academy.hackthebox.com/storage/modules/33/notadmin_fail.png)

El inicio de sesión falló porque`notAdmin`no existe en la tabla y dio como resultado una consulta falsa en general.

![](https://academy.hackthebox.com/storage/modules/33/notadmin_diagram_1.png)

Para iniciar sesión con éxito una vez más, necesitaremos un general`true`consulta. Esto se puede lograr inyectando un`OR`condición en el campo de contraseña, por lo que siempre volverá`true`. Probemos`something' or '1'='1`como la contraseña.

![](https://academy.hackthebox.com/storage/modules/33/password_or_injection.png)

El adicional`OR`la condición dio como resultado un`true`consulta en general, como el`WHERE`La cláusula devuelve todo en la tabla, y el usuario presente en la primera fila se inicia sesión. En este caso, ya que ambas condiciones devolverán`true`, no tenemos que proporcionar un nombre de usuario y contraseña de prueba y podemos comenzar directamente con el`'`inyección e iniciar sesión con solo`' or '1' = '1`.

![](https://academy.hackthebox.com/storage/modules/33/basic_auth_bypass.png)

Esto funciona desde que la consulta evalúa a`true`Independientemente del nombre de usuario o contraseña.