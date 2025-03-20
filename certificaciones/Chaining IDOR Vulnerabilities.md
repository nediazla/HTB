# Chaining IDOR Vulnerabilities

Por lo general, un

```
GET
```

La solicitud al punto final de la API debe devolver los detalles del usuario solicitado, por lo que podemos intentar llamarlo para ver si podemos recuperar los detalles de nuestro usuario. También notamos que después de que se carga la página, obtiene los detalles del usuario con un

```
GET
```

Solicitar al mismo punto final de la API:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_get_api.jpg)

Como se mencionó en la sección anterior, la única forma de autorización en nuestras solicitudes HTTP es la`role=employee`Cookie, como la solicitud HTTP no contiene ninguna otra forma de autorización específica del usuario, como un token JWT, por ejemplo. Incluso si un token existía, a menos que se comparara activamente con los detalles del objeto solicitado por un sistema de control de acceso de fondo, aún podemos recuperar los detalles de otros usuarios.

---

# **Divulgación de información**

Enviemos un`GET`Solicitar con otro`uid`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_get_another_user.jpg)

Como podemos ver, esto devolvió los detalles de otro usuario, con el suyo`uuid`y`role`, confirmando un`IDOR Information Disclosure vulnerability`:

Código:json

```json
{
    "uid": "2",
    "uuid": "4a9bd19b3b8676199592a346051f950c",
    "role": "employee",
    "full_name": "Iona Franklyn",
    "email": "i_franklyn@employees.htb",
    "about": "It takes 20 years to build a reputation and few minutes of cyber-incident to ruin it."
}

```

Esto nos proporciona nuevos detalles, especialmente el`uuid`, que no pudimos calcular antes y, por lo tanto, no pudimos cambiar los detalles de otros usuarios.

---

# **Modificar los detalles de otros usuarios**

Ahora, con el usuario`uuid`En cuestión, podemos cambiar los detalles de este usuario enviando un`PUT`solicitar`/profile/api.php/profile/2`Con los detalles anteriores junto con cualquier modificación que hicimos, de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_modify_another_user.jpg)

Esta vez no recibimos ningún mensaje de error de control de acceso, y cuando intentamos`GET`Los detalles del usuario nuevamente, vemos que realmente actualizamos sus detalles:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_new_another_user_details.jpg)

Además de permitirnos ver detalles potencialmente sensibles, la capacidad de modificar los detalles de otro usuario también nos permite realizar varios otros ataques. Un tipo de ataque es`modifying a user's email address`y luego solicitar un enlace de restablecimiento de contraseña, que se enviará a la dirección de correo electrónico que especificamos, lo que nos permite tomar el control sobre su cuenta. Otro ataque potencial es`placing an XSS payload in the 'about' field`, que se ejecutaría una vez que el usuario visite su`Edit profile`Página, lo que nos permite atacar al usuario de diferentes maneras.

---

# **Encadenamiento de dos vulnerabilidades**

Dado que hemos identificado una vulnerabilidad de divulgación de información IDOR, también podemos enumerar a todos los usuarios y buscar otros`roles`, idealmente un rol de administrador.`Try to write a script to enumerate all users, similarly to what we did previously`.

Una vez que enumeremos a todos los usuarios, encontraremos un usuario administrador con los siguientes detalles:

Código:json

```json
{
    "uid": "X",
    "uuid": "a36fa9e66e85f2dd6f5e13cad45248ae",
    "role": "web_admin",
    "full_name": "administrator",
    "email": "webadmin@employees.htb",
    "about": "HTB{FLAG}"
}

```

Podemos modificar los detalles del administrador y luego realizar uno de los ataques anteriores para hacerse cargo de su cuenta. Sin embargo, como ahora conocemos el nombre de rol de administrador (`web_admin`), podemos configurarlo a nuestro usuario para que podamos crear nuevos usuarios o eliminar los usuarios actuales. Para hacerlo, interceptaremos la solicitud cuando hagamos clic en el`Update profile`botón y cambiar nuestro papel a`web_admin`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_modify_our_role.jpg)

Esta vez, no obtenemos el`Invalid role`Mensaje de error, ni recibimos ningún mensaje de error de control de acceso, lo que significa que no hay medidas de control de acceso de fondo a qué roles podemos establecer para nuestro usuario. Si nosotros`GET`Nuestro usuario Detals, vemos que nuestro`role`de hecho se ha establecido en`web_admin`:

Código:json

```json
{
    "uid": "1",
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "web_admin",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}

```

Ahora, podemos actualizar la página para actualizar nuestra cookie, o establecerla manualmente como`Cookie: role=web_admin`y luego interceptar el`Update`Solicite crear un nuevo usuario y ver si se nos permitiría hacerlo:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_create_new_user_2.jpg)

Esta vez no recibimos un mensaje de error. Si enviamos un`GET`Solicitud para el nuevo usuario, vemos que se ha creado correctamente:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_get_new_user.jpg)

Al combinar la información que obtuvimos del`IDOR Information Disclosure vulnerability`con un`IDOR Insecure Function Calls`Ataque en un punto final de la API, podríamos modificar los detalles de otros usuarios y crear/eliminar a los usuarios mientras pasan por alto varias verificaciones de control de acceso en su lugar. En muchas ocasiones, la información que filtramos a través de las vulnerabilidades IDOR se puede utilizar en otros ataques, como IDOR o XSS, lo que lleva a ataques más sofisticados o evitando los mecanismos de seguridad existentes.

Con nuestro nuevo`role`, también podemos realizar tareas masivas para cambiar campos específicos para todos los usuarios, como colocar las cargas de XSS en sus perfiles o cambiar su correo electrónico a un correo electrónico que especificamos.`Try to write a script that changes all users' email to an email you choose.`. Puedes hacerlo recuperando su`uuids`Y luego enviando un`PUT`Solicitud para cada uno con el nuevo correo electrónico.