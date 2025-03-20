# Verb Tampering Prevention

Después de ver algunas formas de explotar las vulnerabilidades de manipulación de verbos, veamos cómo podemos protegernos contra este tipo de ataques evitando la manipulación de los verbos. Las configuraciones inseguras y la codificación insegura son los que generalmente introducen vulnerabilidades de manipulación de verbos. En esta sección, analizaremos muestras de código y configuraciones vulnerables y discutiremos cómo podemos parcharlos.

---

# **Configuración insegura**

Las vulnerabilidades de manipulación de verbos HTTP pueden ocurrir en la mayoría de los servidores web modernos, incluidos`Apache`, `Tomcat`, y`ASP.NET`. La vulnerabilidad generalmente ocurre cuando limitamos la autorización de una página a un conjunto particular de verbos/métodos HTTP, lo que deja los otros métodos restantes sin protección.

El siguiente es un ejemplo de una configuración vulnerable para un servidor web Apache, que se encuentra en el archivo de configuración del sitio (por ejemplo,`000-default.conf`), o en un`.htaccess`Archivo de configuración de la página web:

Código:xml

```xml
<Directory "/var/www/html/admin">
    AuthType Basic
    AuthName "Admin Panel"
    AuthUserFile /etc/apache2/.htpasswd
    <Limit GET>
        Require valid-user
    </Limit></Directory>
```

Como podemos ver, esta configuración está configurando las configuraciones de autorización para el`admin`Directorio web. Sin embargo, como el`<Limit GET>`Se está utilizando la palabra clave, el`Require valid-user`la configuración solo se aplicará a`GET`solicitudes, dejando la página accesible a través de`POST`solicitudes. Incluso si ambos`GET`y`POST`se especificaron, esto dejaría la página accesible a través de otros métodos, como`HEAD`o`OPTIONS`.

El siguiente ejemplo muestra la misma vulnerabilidad para un`Tomcat`Configuración del servidor web, que se puede encontrar en el`web.xml`Archivo para una determinada aplicación web de Java:

Código:xml

```xml
<security-constraint><web-resource-collection><url-pattern>/admin/*</url-pattern><http-method>GET</http-method></web-resource-collection><auth-constraint><role-name>admin</role-name></auth-constraint></security-constraint>
```

Podemos ver que la autorización se limita solo a la`GET`método con`http-method`, que deja la página accesible a través de otros métodos HTTP.

Finalmente, el siguiente es un ejemplo para un`ASP.NET`configuración encontrada en el`web.config`Archivo de una aplicación web:

Código:xml

```xml
<system.web><authorization><allow verbs="GET" roles="admin"><deny verbs="GET" users="*"></deny></allow></authorization></system.web>
```

Una vez más, el`allow`y`deny`el alcance se limita al`GET`Método, que deja la aplicación web accesible a través de otros métodos HTTP.

Los ejemplos anteriores muestran que no es seguro limitar la configuración de autorización a un verbo HTTP específico. Es por eso que siempre debemos evitar restringir la autorización a un método HTTP particular y siempre permitir/negar todos los verbos y métodos HTTP.

Si queremos especificar un solo método, podemos usar palabras clave seguras, como`LimitExcept`En Apache,`http-method-omission` en Tomcat y`add`/`remove`en ASP.NET, que cubren todos los verbos excepto los especificados.

Finalmente, para evitar ataques similares, generalmente deberíamos`consider disabling/denying all HEAD requests`A menos que la aplicación web lo requiera específicamente.

---

# **Codificación insegura**

Si bien identificar y parchear las configuraciones inseguras del servidor web es relativamente fácil, hacer lo mismo para un código inseguro es mucho más desafiante. Esto se debe a que para identificar esta vulnerabilidad en el código, necesitamos encontrar inconsistencias en el uso de parámetros HTTP en todas las funciones, como en algunos casos, esto puede conducir a funcionalidades y filtros sin protección.

Consideremos lo siguiente`PHP`código de nuestro`File Manager`ejercicio:

Código:php

```php
if (isset($_REQUEST['filename'])) {
    if (!preg_match('/[^A-Za-z0-9. _-]/', $_POST['filename'])) {
        system("touch " . $_REQUEST['filename']);
    } else {
        echo "Malicious Request Denied!";
    }
}

```

Si solo estuviéramos considerando vulnerabilidades de inyección de comandos, diríamos que esto está codificado de forma segura. El`preg_match`La función busca los caracteres especiales no deseados y no permite que la entrada ingrese al comando si se encuentran caracteres especiales. Sin embargo, el error fatal cometido en este caso no se debe a las inyecciones de comando, sino debido a la`inconsistent use of HTTP methods`.

Vemos que el`preg_match`Filtro solo verifica caracteres especiales en`POST`parámetros con`$_POST['filename']`. Sin embargo, la final`system`El comando usa el`$_REQUEST['filename']`variable, que cubre ambos`GET`y`POST`parámetros. Entonces, en la sección anterior, cuando enviamos nuestra entrada maliciosa a través de un`GET`Solicitud, no fue detenido por el`preg_match`función, como el`POST`Los parámetros estaban vacíos y, por lo tanto, no contenían caracteres especiales. Una vez que llegamos al`system`La función, sin embargo, utilizó cualquier parámetro encontrado en la solicitud y nuestro`GET`Los parámetros se utilizaron en el comando, y finalmente condujo a la inyección de comando.

Este ejemplo básico nos muestra cómo las inconsistencias menores en el uso de métodos HTTP pueden conducir a vulnerabilidades críticas. En una aplicación web de producción, este tipo de vulnerabilidades no serán tan obvias. Probablemente se extenderían a través de la aplicación web y no estarán en dos líneas consecutivas como nosotros aquí. En cambio, la aplicación web probablemente tendrá una función especial para verificar las inyecciones y una función diferente para crear archivos. Esta separación del código dificulta atrapar este tipo de inconsistencias y, por lo tanto, pueden sobrevivir a la producción.

Para evitar las vulnerabilidades de manipulación de verbos HTTP en nuestro código,`we must be consistent with our use of HTTP methods`y asegúrese de que siempre se use el mismo método para cualquier funcionalidad específica en la aplicación web. Siempre se recomienda`expand the scope of testing in security filters`Probando todos los parámetros de solicitud. Esto se puede hacer con las siguientes funciones y variables:

| **Idioma** | **Función** |
| --- | --- |
| Php | `$_REQUEST['param']` |
| Java | `request.getParameter('param')` |
| DO# | `Request['param']` |

Si nuestro alcance en las funciones relacionadas con la seguridad cubre todos los métodos, debemos evitar tales vulnerabilidades o las derivaciones de filtro.

[Anterior](https://academy.hackthebox.com/module/134/section/1178)