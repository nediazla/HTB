# Identifying Filters

Como hemos visto en la sección anterior, incluso si los desarrolladores intentan asegurar la aplicación web contra las inyecciones, aún puede ser explotable si no se codificó de forma segura. Otro tipo de mitigación de inyección es utilizar caracteres y palabras en la lista negra en el fondo para detectar intentos de inyección y negar la solicitud si alguna solicitud los contenía. Otra capa además de esto es utilizar los firewalls de aplicaciones web (WAFS), que pueden tener un alcance más amplio y varios métodos de detección de inyección y evitar otros ataques como inyecciones SQL o ataques XSS.

Esta sección analizará algunos ejemplos de cómo se pueden detectar y bloquear las inyecciones de comandos y cómo podemos identificar lo que se está bloqueando.

---

# **Detección de filtro/WAF**

Comencemos visitando la aplicación web en el ejercicio al final de esta sección. Vemos lo mismo

```
Host Checker
```

Aplicación web que hemos estado explotando, pero ahora tiene algunas mitigaciones bajo la manga. Podemos ver que si probamos los operadores anteriores que probamos, como (

```
;
```

,

```
&&
```

,

```
||
```

), recibimos el mensaje de error

```
invalid input
```

:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_1.jpg)

Esto indica que algo que enviamos activó un mecanismo de seguridad establecido que negó nuestra solicitud. Este mensaje de error se puede mostrar de varias maneras. En este caso, lo vemos en el campo donde se muestra la salida, lo que significa que fue detectado y evitado por el`PHP`aplicación web en sí.`If the error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a WAF`.

Vamos a ver la carga útil que enviamos:

Código:intento

```bash
127.0.0.1; whoami

```

Aparte de la IP (que sabemos que no está en la lista negra), enviamos:

1. Un personaje de semicolón`;`
2. Un personaje espacial
3. A`whoami`dominio

Entonces, la aplicación web tampoco`detected a blacklisted character`o`detected a blacklisted command`, o ambos. Entonces, veamos cómo omitir cada uno.

---

# **Personajes en la lista negra**

Una aplicación web puede tener una lista de caracteres en la lista negra, y si el comando los contiene, negaría la solicitud. El`PHP`El código puede parecerse a lo siguiente:

Código:php

```php
$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}

```

Si algún personaje en la cadena que enviamos coincide con un personaje en la lista negra, nuestra solicitud es denegada. Antes de comenzar nuestros intentos de omitir el filtro, debemos intentar identificar qué carácter causó la solicitud denegada.

---

# **Identificar el personaje en la lista negra**

Reduzcamos nuestra solicitud a un personaje a la vez y veamos cuándo se bloquea. Sabemos que el (

```
127.0.0.1
```

) La carga útil funciona, así que comencemos agregando el semi-colon (

```
127.0.0.1;
```

):

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_2.jpg)

Todavía tenemos un`invalid input`, error que significa que un semi-colon está en la lista negra. Entonces, veamos si todos los operadores de inyección que discutimos anteriormente están en la lista negra.