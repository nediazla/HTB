# IDOR in Insecure APIs

Hasta ahora, solo hemos estado utilizando vulnerabilidades IDOR para acceder a archivos y recursos que están fuera del acceso de nuestro usuario. Sin embargo, las vulnerabilidades de IDOR también pueden existir en llamadas de funciones y API, y explotarlas nos permitiría realizar varias acciones como otros usuarios.

Mientras`IDOR Information Disclosure Vulnerabilities`Permítanos leer varios tipos de recursos,`IDOR Insecure Function Calls`Permitemos llamar a API o ejecutar funciones como otro usuario. Dichas funciones y API se pueden utilizar para cambiar la información privada de otro usuario, restablecer la contraseña de otro usuario o incluso comprar artículos utilizando la información de pago de otro usuario. En muchos casos, podemos obtener cierta información a través de una vulnerabilidad de divulgación de información y luego utilizando esta información con las vulnerabilidades de llamadas de la función insegura IDOR, como veremos más adelante en el módulo.

---

# **Identificación de API inseguras**

Volviendo a nuestro`Employee Manager`Aplicación web, podemos comenzar a probar el`Edit Profile`Página para las vulnerabilidades de IDOR:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_employee_manager.jpg)

Cuando hacemos clic en el`Edit Profile`Botón, nos llevan a una página para editar información de nuestro perfil de usuario, a saber,`Full Name`, `Email`, y`About Me`, que es una característica común en muchas aplicaciones web:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_edit_profile.jpg)

Podemos cambiar cualquiera de los detalles en nuestro perfil y hacer clic`Update profile`, y veremos que se actualizan y persisten a través de actualizaciones, lo que significa que se actualizan en una base de datos en alguna parte. Intercepemos el`Update`Solicitar en Burp y míralo:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_update_request.jpg)

Vemos que la página está enviando un`PUT`solicitud al`/profile/api.php/profile/1`Punto final de la API.`PUT`Las solicitudes generalmente se usan en API para actualizar los detalles del elemento, mientras que`POST`se usa para crear nuevos elementos,`DELETE`para eliminar artículos y`GET`Para recuperar los detalles del artículo. Entonces, un`PUT`Solicitud de la`Update profile`se espera la función. El bit interesante son los parámetros JSON que está enviando:

Código:json

```json
{
    "uid": 1,
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "employee",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}

```

Vemos que el`PUT`La solicitud incluye algunos parámetros ocultos, como`uid`, `uuid`, y lo más interesante`role`, que está establecido en`employee`. La aplicación web también parece estar configurando los privilegios de acceso al usuario (por ejemplo,`role`) en el lado del cliente, en forma de nuestro`Cookie: role=employee`Cookie, que parece reflejar el`role`especificado para nuestro usuario. Este es un problema de seguridad común. Los privilegios de control de acceso se envían como parte de la solicitud HTTP del cliente, ya sea como una cookie o como parte de la solicitud JSON, dejándola bajo el control del cliente, lo que podría manipularse para obtener más privilegios.

Entonces, a menos que la aplicación web tenga un sistema de control de acceso sólido en el back-end,`we should be able to set an arbitrary role for our user, which may grant us more privileges`. Sin embargo, ¿cómo sabríamos qué otros roles existen?

---

# **Explotando API inseguras**

Sabemos que podemos cambiar el`full_name`, `email`, y`about`Parámetros, ya que estos son los que están bajo nuestro control en la forma HTML en el`/profile`Página web. Entonces, intentemos manipular los otros parámetros.

Hay algunas cosas que podríamos probar en este caso:

1. Cambiar nuestro`uid`a otro usuario`uid`, de modo que podamos tomar sus cuentas
2. Cambiar los detalles de otro usuario, lo que puede permitirnos realizar varios ataques web
3. Cree nuevos usuarios con detalles arbitrarios o elimine los usuarios existentes
4. Cambiar nuestro papel a un papel más privilegiado (por ejemplo,`admin`) poder realizar más acciones

Comencemos por cambiar nuestro`uid`a otro usuario`uid`(p.ej`"uid": 2`). Sin embargo, cualquier número que establezcamos que no sea el nuestro`uid`nos da una respuesta de`uid mismatch`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_uid_mismatch.jpg)

La aplicación web parece estar comparando la solicitud`uid`al punto final de la API (`/1`). Esto significa que una forma de control de acceso en el back-end nos impide cambiar arbitrariamente algunos parámetros JSON, lo que podría ser necesario para evitar que la aplicación web se bloquee o devuelva los errores.

Quizás podamos intentar cambiar los detalles de otro usuario. Cambiaremos el punto final de la API a`/profile/api.php/profile/2`y cambiar`"uid": 2`Para evitar el anterior`uid mismatch`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_uuid_mismatch.jpg)

Como podemos ver, esta vez, recibimos un mensaje de error diciendo`uuid mismatch`. La aplicación web parece estar verificando si el`uuid`valor que estamos enviando coincidencias del usuario`uuid`. Dado que estamos enviando el nuestro`uuid`, nuestra solicitud está fallando. Esta parece ser otra forma de control de acceso para evitar que los usuarios cambien los detalles de otro usuario.

A continuación, veamos si podemos crear un nuevo usuario con un`POST`Solicitar al punto final de la API. Podemos cambiar el método de solicitud a`POST`, cambia el`uid`a un nuevo`uid`, y envíe la solicitud al punto final API del nuevo`uid`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_create_new_user_1.jpg)

Recibimos un mensaje de error diciendo`Creating new employees is for admins only`. Lo mismo sucede cuando enviamos un`Delete`Solicitud, a medida que avanzamos`Deleting employees is for admins only`. La aplicación web podría estar revisando nuestra autorización a través del`role=employee`Cookie porque esta parece ser la única forma de autorización en la solicitud HTTP.

Finalmente, intentemos cambiar nuestro

```
role
```

a

```
admin
```

/

```
administrator
```

para obtener mayores privilegios. Desafortunadamente, sin conocer un válido

```
role
```

Nombre, tenemos

```
Invalid role
```

en la respuesta http, y nuestro

```
role
```

no se actualiza:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_invalid_role.jpg)

Entonces,`all of our attempts appear to have failed`. No podemos crear o eliminar a los usuarios ya que no podemos cambiar nuestro`role`. No podemos cambiar el nuestro`uid`, ya que hay medidas preventivas en el back-end que no podemos controlar, ni podemos cambiar los detalles de otro usuario por la misma razón.`So, is the web application secure against IDOR attacks?`.

Hasta ahora, solo hemos estado probando el`IDOR Insecure Function Calls`. Sin embargo, no hemos probado la API`GET`solicitar`IDOR Information Disclosure Vulnerabilities`. Si no hubo un sistema de control de acceso robusto, podríamos leer los detalles de otros usuarios, lo que puede ayudarnos con los ataques anteriores que intentamos.

`Try to test the API against IDOR Information Disclosure vulnerabilities by attempting to get other users' details with GET requests`. Si la API es vulnerable, podemos filtrar los detalles de otros usuarios y luego usar esta información para completar nuestros ataques IDOR en las llamadas de función.