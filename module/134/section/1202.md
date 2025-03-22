# IDOR Prevention

Aprendimos varias formas de identificar y explotar las vulnerabilidades IDOR en páginas web, funciones web y llamadas de API. En este momento, deberíamos haber entendido que las vulnerabilidades IDOR son causadas principalmente por un control de acceso inadecuado en los servidores de fondo. Para evitar tales vulnerabilidades, primero tenemos que construir un sistema de control de acceso a nivel de objeto y luego usar referencias seguras para nuestros objetos al almacenarlos y llamarlos.

---

# **Control de acceso a nivel de objeto**

Un sistema de control de acceso debe estar en el núcleo de cualquier aplicación web, ya que puede afectar todo su diseño y estructura. Para controlar adecuadamente cada área de la aplicación web, su diseño debe respaldar la segmentación de roles y permisos de manera centralizada. Sin embargo, el control de acceso es un tema vasto, por lo que solo nos centraremos en su papel en las vulnerabilidades de IDOR, representada en`Object-Level`Mecanismos de control de acceso.

Los roles y permisos de los usuarios son una parte vital de cualquier sistema de control de acceso, que se realiza plenamente en un sistema de control de acceso basado en roles (RBAC). Para evitar explotar las vulnerabilidades de IDOR, debemos mapear el RBAC a todos los objetos y recursos. El servidor de back-end puede permitir o negar cada solicitud, dependiendo de si el papel del solicitante tiene suficientes privilegios para acceder al objeto o al recurso.

Una vez que se ha implementado un RBAC, a cada usuario se le asignará un rol que tenga ciertos privilegios. Con cada solicitud que el usuario realiza, sus roles y privilegios se probarían para ver si tienen acceso al objeto que solicitan. Solo se les permitiría acceder a él si tienen derecho a hacerlo.

Hay muchas formas de implementar un sistema RBAC y asignarlo a los objetos y recursos de la aplicación web, y diseñarlo en el núcleo de la estructura de la aplicación web es un arte para perfeccionar. El siguiente es un código de muestra de cómo una aplicación web puede comparar los roles de usuario con los objetos para permitir o negar el control de acceso:

Código:javascript

```jsx
match /api/profile/{userId} {
    allow read, write: if user.isAuth == true
    && (user.uid == userId || user.roles == 'admin');
}

```

El ejemplo anterior usa el`user`token, que puede ser`mapped from the HTTP request made to the RBAC`Para recuperar los diversos roles y privilegios del usuario. Entonces, solo permite el acceso de lectura/escritura si el usuario`uid`en el sistema RBAC coincide con el`uid`En el punto final de la API solicitan. Además, si un usuario tiene`admin`Como su papel en el RBAC de back-end, se les permite el acceso de lectura/escritura.

En nuestros ataques anteriores, vimos ejemplos del rol del usuario almacenado en los detalles del usuario o en su cookie, los cuales están bajo el control del usuario y pueden ser manipulados para aumentar sus privilegios de acceso. El ejemplo anterior demuestra un enfoque más seguro para mapear los roles de los usuarios, ya que el usuario privilegia`were not be passed through the HTTP request`, pero mapeó directamente desde el RBAC en el back-end utilizando el token de sesión iniciada por el usuario como mecanismo de autenticación.

Hay mucho más para acceder a los sistemas de control y los RBAC, ya que pueden ser algunos de los sistemas más desafiantes para diseñar. Sin embargo, esto debería darnos una idea de cómo debemos controlar el acceso de los usuarios sobre los objetos y recursos de las aplicaciones web.

---

# **Referencia de objetos**

Mientras que el problema central con IDOR radica en el control de acceso roto (`Insecure`), tener acceso a referencias directas a objetos (`Direct Object Referencing`) permite enumerar y explotar estas vulnerabilidades de control de acceso. Todavía podemos usar referencias directas, pero solo si tenemos un sistema de control de acceso sólido implementado.

Incluso después de construir un sistema de control de acceso sólido, nunca debemos usar referencias de objetos en texto claro o patrones simples (por ejemplo,`uid=1`). Siempre debemos usar referencias fuertes y únicas, como hashes salados o`UUID`'s. Por ejemplo, podemos usar`UUID V4`generar una identificación fuertemente aleatorizada para cualquier elemento, que se parece a (`89c9b29b-d19f-4515-b2dd-abb6e693eb20`). Entonces, podemos mapear esto`UUID`al objeto está haciendo referencia en la base de datos de back-end, y cada vez que esto`UUID`se llama, la base de datos de back-end sabría qué objeto devolver. El siguiente ejemplo de código PHP nos muestra cómo puede funcionar esto:

Código:php

```php
$uid = intval($_REQUEST['uid']);
$query = "SELECT url FROM documents where uid=" . $uid;
$result = mysqli_query($conn, $query);
$row = mysqli_fetch_array($result));
echo "<a href='" . $row['url'] . "' target='_blank'></a>";

```

Además, como hemos visto anteriormente en el módulo, nunca debemos calcular los hashes en el front-end. Debemos generarlos cuando se crea un objeto y almacenarlos en la base de datos de back-end. Luego, debemos crear mapas de bases de datos para habilitar la referencia cruzada rápida de objetos y referencias.

Finalmente, debemos tener en cuenta que usar`UUID`S puede dejar que las vulnerabilidades de Idor no se detecten, ya que hace que sea más difícil probar las vulnerabilidades de IDOR. Esta es la razón por la cual la referencia fuerte de objetos es siempre el segundo paso después de implementar un sistema de control de acceso sólido. Además, algunas de las técnicas que aprendimos en este módulo funcionarían incluso con referencias únicas si el sistema de control de acceso está roto, como repetir la solicitud de un usuario con la sesión de otro usuario, como hemos visto anteriormente.

Si implementamos ambos mecanismos de seguridad, debemos ser relativamente seguros contra las vulnerabilidades de IDOR.