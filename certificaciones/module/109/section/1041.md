# Command Injection Prevention

Ahora deberíamos tener una comprensión sólida de cómo ocurren las vulnerabilidades de inyección de comandos y cómo ciertas mitigaciones como los filtros de carácter y comando se pueden pasar por alto. Esta sección discutirá los métodos que podemos usar para evitar vulnerabilidades de inyección de comandos en nuestras aplicaciones web y configurar adecuadamente el servidor web para evitarlos.

---

# **Comandos del sistema**

Siempre debemos evitar usar funciones que ejecuten comandos del sistema, especialmente si estamos utilizando la entrada del usuario con ellos. Incluso cuando no estamos ingresando directamente la entrada del usuario en estas funciones, un usuario puede influir indirectamente en ellas, lo que eventualmente puede conducir a una vulnerabilidad por inyección de comandos.

En lugar de usar funciones de ejecución del comando del sistema, debemos usar funciones incorporadas que realicen la funcionalidad necesaria, ya que los lenguajes de fondo generalmente tienen implementaciones seguras de estos tipos de funcionalidades. Por ejemplo, supongamos que queríamos probar si un anfitrión en particular está vivo con`PHP`. En ese caso, podemos usar el`fsockopen`Funcionar en su lugar, que no debe ser explotable para ejecutar comandos de sistema arbitrarios.

Si necesitábamos ejecutar un comando del sistema, y no se puede encontrar ninguna función incorporada para realizar la misma funcionalidad, nunca debemos usar directamente la entrada del usuario con estas funciones, pero siempre debemos validar y desinfectar la entrada del usuario en el back-end. Además, debemos tratar de limitar nuestro uso de este tipo de funciones tanto como sea posible y solo usarlas cuando no haya una alternativa incorporada a la funcionalidad que necesitamos.

---

# **Validación de entrada**

Ya sea que use funciones incorporadas o funciones de ejecución de comandos del sistema, siempre debemos validar y luego desinfectar la entrada del usuario. La validación de la entrada se realiza para garantizar que coincida con el formato esperado para la entrada, de modo que la solicitud se niega si no coincide. En nuestra aplicación web de ejemplo, vimos que había un intento de validación de entrada en el front-end, pero`input validation should be done both on the front-end and on the back-end`.

En`PHP`, como muchos otros lenguajes de desarrollo web, se incorporan filtros para una variedad de formatos estándar, como correos electrónicos, URL e incluso IP, que se pueden usar con el`filter_var`función, como sigue:

Código:php

```php
if (filter_var($_GET['ip'], FILTER_VALIDATE_IP)) {
    // call function
} else {
    // deny request
}

```

Si queríamos validar un formato diferente no estándar, entonces podemos usar una expresión regular`regex`con el`preg_match`función. Lo mismo se puede lograr con`JavaScript`tanto para el front-end como para el back-end (es decir`NodeJS`), como sigue:

Código:javascript

```jsx
if(/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(ip)){
    // call function
}
else{
    // deny request
}

```

Solo como`PHP`, con`NodeJS`, también podemos usar bibliotecas para validar varios formatos estándar, como[IS-IP](https://www.npmjs.com/package/is-ip)Por ejemplo, que podemos instalar con`npm`, y luego usa el`isIp(ip)`función en nuestro código. Puedes leer los manuales de otros idiomas, como[.NETO](https://learn.microsoft.com/en-us/aspnet/web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites)o[Java](https://docs.oracle.com/cd/E13226_01/workshop/docs81/doc/en/workshop/guide/netui/guide/conValidatingUserInput.html?skipReload=true), para averiguar cómo validar la entrada del usuario en cada idioma respectivo.

---

# **Desinfección de entrada**

La parte más crítica para prevenir cualquier vulnerabilidad de inyección es la desinfección de la entrada, lo que significa eliminar cualquier caracteres especiales no necesarios de la entrada del usuario. La desinfección de entrada siempre se realiza después de la validación de entrada. Incluso después de validar que la entrada del usuario proporcionada está en el formato adecuado, aún debemos realizar desinfectación y eliminar los caracteres especiales que no se requieran para el formato específico, ya que hay casos en que la validación de la entrada puede fallar (por ejemplo, una mala regla).

En nuestro código de ejemplo, vimos que cuando estábamos lidiando con filtros de carácter y comando, estaba en la lista negra de ciertas palabras y las buscaba en la entrada del usuario. En general, este no es un enfoque lo suficientemente bueno para prevenir inyecciones, y debemos usar funciones incorporadas para eliminar cualquier caracteres especiales. Podemos usar`preg_replace`Para eliminar los caracteres especiales de la entrada del usuario, de la siguiente manera:

Código:php

```php
$ip = preg_replace('/[^A-Za-z0-9.]/', '', $_GET['ip']);

```

Como podemos ver, la regex anterior solo permite caracteres alfanuméricos (`A-Za-z0-9`) y permite un personaje de puntos (`.`) según sea necesario para IPS. Cualquier otro personaje se eliminará de la cadena. Lo mismo se puede hacer con`JavaScript`, como sigue:

Código:javascript

```jsx
var ip = ip.replace(/[^A-Za-z0-9.]/g, '');

```

También podemos usar la biblioteca Dompurify para un`NodeJS`back-end, como sigue:

Código:javascript

```jsx
import DOMPurify from 'dompurify';
var ip = DOMPurify.sanitize(ip);

```

En ciertos casos, es posible que deseemos permitir todos los caracteres especiales (por ejemplo, comentarios de los usuarios), entonces podemos usar lo mismo`filter_var`función que utilizamos con la validación de entrada y usa el`escapeshellcmd`Filtrar para escapar de cualquier caracteres especiales, para que no puedan causar ninguna inyección. Para`NodeJS`, simplemente podemos usar el`escape(ip)`función.`However, as we have seen in this module, escaping special characters is usually not considered a secure practice, as it can often be bypassed through various techniques`.

Para obtener más información sobre la validación y la desinfección del usuario para evitar inyecciones de comandos, puede consultar el[Codificación segura 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript)Módulo, que cubre cómo auditar el código fuente de una aplicación web para identificar vulnerabilidades de inyección de comandos, y luego funciona en parchear correctamente este tipo de vulnerabilidades.

---

# **Configuración del servidor**

Finalmente, debemos asegurarnos de que nuestro servidor de fondo esté configurado de forma segura para reducir el impacto en caso de que el servidor web se vea comprometido. Algunas de las configuraciones que podemos implementar son:

- Use el firewall de aplicación web incorporada del servidor web (por ejemplo, en Apache`mod_security`), además de un WAF externo (por ejemplo,`Cloudflare`, `Fortinet`, `Imperva`..)
- Cumplir con el[Principio de menor privilegio (POLP)](https://en.wikipedia.org/wiki/Principle_of_least_privilege)Al ejecutar el servidor web como un usuario de bajo privilegiado (por ejemplo,`www-data`)
- Evitar que ciertas funciones sean ejecutadas por el servidor web (por ejemplo, en PHP`disable_functions=system,...`)
- Limite el alcance accesible por la aplicación web a su carpeta (por ejemplo, PHP`open_basedir = '/var/www/html'`)
- Rechazar solicitudes de doble codificación y caracteres no ASCII en URLS
- Evite el uso de bibliotecas y módulos sensibles/obsoletos (por ejemplo,[PHP CGI](https://www.php.net/manual/en/install.unix.commandline.php))

Al final, incluso después de todas estas mitigaciones y configuraciones de seguridad, tenemos que realizar las técnicas de prueba de penetración que aprendimos en este módulo para ver si alguna funcionalidad de la aplicación web aún puede ser vulnerable a la inyección de comandos. Como algunas aplicaciones web tienen millones de líneas de código, cualquier error en cualquier línea de código puede ser suficiente para introducir una vulnerabilidad. Por lo tanto, debemos tratar de asegurar la aplicación web complementando las mejores prácticas de codificación segura con pruebas de penetración exhaustivas.

[Anterior](https://academy.hackthebox.com/module/109/section/1040)