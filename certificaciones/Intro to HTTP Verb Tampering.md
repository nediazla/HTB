# Intro to HTTP Verb Tampering

El`HTTP`Protocolo funciona aceptando varios métodos HTTP como`verbs`Al comienzo de una solicitud HTTP. Dependiendo de la configuración del servidor web, las aplicaciones web se pueden escribir para aceptar ciertos métodos HTTP para sus diversas funcionalidades y realizar una acción particular basada en el tipo de solicitud.

Mientras que los programadores consideran principalmente los dos métodos HTTP más utilizados,`GET`y`POST`, cualquier cliente puede enviar cualquier otro método en sus solicitudes HTTP y luego ver cómo el servidor web maneja estos métodos. Suponga que tanto la aplicación web como el servidor web de back-end se configuran solo para aceptar`GET`y`POST`solicitudes. En ese caso, enviar una solicitud diferente hará que se muestre una página de error de servidor web, lo que no es una vulnerabilidad severa en sí misma (aparte de proporcionar una mala experiencia del usuario y potencialmente conducir a la divulgación de información). Por otro lado, si las configuraciones del servidor web no están restringidas para aceptar solo los métodos HTTP requeridos por el servidor web (por ejemplo,`GET`/`POST`), y la aplicación web no está desarrollada para manejar otros tipos de solicitudes HTTP (por ejemplo,`HEAD`, `PUT`), entonces podemos explotar esta configuración insegura para obtener acceso a las funcionalidades a las que no tenemos acceso, o incluso evitar ciertos controles de seguridad.

---

# **Manipulación de verbos http**

Para entender`HTTP Verb Tampering`Primero debemos aprender sobre los diferentes métodos aceptados por el protocolo HTTP. HTTP tiene[9 verbos diferentes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)Los servidores web pueden aceptar como métodos HTTP. Otro que`GET`y`POST`, los siguientes son algunos de los verbos HTTP de uso común:

| **Verbo** | **Descripción** |
| --- | --- |
| `HEAD` | Idéntico a una solicitud GET, pero su respuesta solo contiene el`headers`, sin el cuerpo de respuesta |
| `PUT` | Writes the request payload to the specified location |
| `DELETE` | Elimina el recurso en la ubicación especificada |
| `OPTIONS` | Muestra diferentes opciones aceptadas por un servidor web, como los verbos HTTP aceptados |
| `PATCH` | Aplicar modificaciones parciales al recurso en la ubicación especificada |

Como puede imaginar, algunos de los métodos anteriores pueden realizar funcionalidades muy sensibles, como la escritura (`PUT`) o eliminar (`DELETE`) Archivos al directorio Webroot en el servidor de back-end. Como se discutió en el[Solicitudes web](https://academy.hackthebox.com/course/preview/web-requests)Módulo, si un servidor web no está configurado de forma segura para administrar estos métodos, podemos usarlos para obtener control sobre el servidor de fondo. Sin embargo, lo que hace que los ataques de manipulación de verbos HTTP sean más comunes (y, por lo tanto, más críticos), es que son causados por una configuración errónea en el servidor web de fondo o en la aplicación web, cualquiera de los cuales puede causar la vulnerabilidad.

---

# **Configuraciones inseguras**

Las configuraciones inseguras del servidor web causan el primer tipo de vulnerabilidades de manipulación de verbos HTTP. La configuración de autenticación de un servidor web puede limitarse a métodos HTTP específicos, que dejarían algunos métodos HTTP accesibles sin autenticación. Por ejemplo, un administrador del sistema puede usar la siguiente configuración para requerir autenticación en una página web en particular:

Código:xml

```xml
<Limit GET POST>
    Require valid-user
</Limit>
```

Como podemos ver, a pesar de que la configuración especifica ambos`GET`y`POST`Solicitudes para el método de autenticación, un atacante aún puede usar un método HTTP diferente (como`HEAD`) para omitir este mecanismo de autenticación por completo, como será en la siguiente sección. Esto eventualmente lleva a un bypass de autenticación y permite a los atacantes acceder a páginas web y dominios a los que no deberían tener acceso.

---

# **Codificación insegura**

Las prácticas de codificación inseguros causan el otro tipo de vulnerabilidades de manipulación de verbos HTTP (aunque algunos pueden no considerar esta manipulación de verbo). Esto puede ocurrir cuando un desarrollador web aplica filtros específicos para mitigar vulnerabilidades particulares sin cubrir todos los métodos HTTP con ese filtro. Por ejemplo, si se descubrió que una página web era vulnerable a una vulnerabilidad de inyección SQL, y el desarrollador de back-end mitigó la vulnerabilidad de inyección SQL por los siguientes filtros de desinfección de entrada aplicando:

Código:php

```php
$pattern = "/^[A-Za-z\s]+$/";

if(preg_match($pattern, $_GET["code"])) {
    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
    ...SNIP...
}

```

Podemos ver que el filtro de desinfección solo se está probando en el`GET`parámetro. Si las solicitudes GET no contienen ningún mal personaje, entonces la consulta se ejecutaría. Sin embargo, cuando se ejecuta la consulta, el`$_REQUEST["code"]`Se están utilizando parámetros, que también pueden contener`POST`parámetros,`leading to an inconsistency in the use of HTTP Verbs`. En este caso, un atacante puede usar un`POST`Solicitud de realizar inyección SQL, en cuyo caso el`GET`Los parámetros estarían vacíos (no incluirán ningún mal estado). La solicitud pasaría el filtro de seguridad, lo que haría que la función aún sea vulnerable a la inyección SQL.

Si bien las dos vulnerabilidades anteriores se encuentran en público, la segunda es mucho más común, ya que se debe a errores cometidos en la codificación, mientras que el primero generalmente se evita mediante configuraciones de servidor web seguras, ya que la documentación a menudo advierte contra él. En las próximas secciones, veremos ejemplos de ambos tipos y cómo explotarlos.