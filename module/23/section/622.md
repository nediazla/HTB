# File Inclusion Prevention

Este módulo ha discutido varias formas de detectar y explotar las vulnerabilidades de inclusión de archivos, junto con diferentes derivaciones de seguridad y técnicas de ejecución de código remoto que podemos utilizar. Con esa comprensión de cómo identificar las vulnerabilidades de inclusión de archivos a través de pruebas de penetración, ahora debemos aprender cómo parchear estas vulnerabilidades y endurecer nuestros sistemas para reducir las posibilidades de su ocurrencia y reducir el impacto si lo hacen.

---

# **Prevención de inclusión de archivo**

Lo más efectivo que podemos hacer para reducir las vulnerabilidades de inclusión de archivos es evitar transmitir cualquier entrada controlada por el usuario en cualquier función o API de inclusión de archivos. La página debe poder cargar dinámicamente activos en el back-end, sin interacción de usuario en absoluto. Además, en la primera sección de este módulo, discutimos diferentes funciones que pueden utilizarse para incluir otros archivos dentro de una página y mencionamos los privilegios que tiene cada función. Cada vez que se usa cualquiera de estas funciones, debemos asegurarnos de que ninguna entrada del usuario esté entrando directamente en ellas. Por supuesto, esta lista de funciones no es completa, por lo que generalmente debemos considerar cualquier función que pueda leer archivos.

En algunos casos, esto puede no ser factible, ya que puede requerir cambiar toda la arquitectura de una aplicación web existente. En tales casos, debemos utilizar una lista blanca limitada de entradas de usuario permitidas y hacer coincidir cada entrada al archivo que se cargará, mientras que tenemos un valor predeterminado para todas las demás entradas. Si estamos tratando con una aplicación web existente, podemos crear una lista blanca que contenga todas las rutas existentes utilizadas en el front-end, y luego utilizar esta lista para que coincida con la entrada del usuario. Tal lista blanca puede tener muchas formas, como una tabla de base de datos que coincide con IDS con archivos, un`case-match`Script que coincide con los nombres con los archivos, o incluso un mapa JSON estático con nombres y archivos que se pueden igualar.

Una vez que esto se implementa, la entrada del usuario no está entrando en la función, pero los archivos coincidentes se utilizan en la función, lo que evita las vulnerabilidades de inclusión de archivos.

---

# **Prevención del recorrido del directorio**

Si los atacantes pueden controlar el directorio, pueden escapar de la aplicación web y atacar algo con lo que están más familiarizados o usar un`universal attack chain`. Como hemos discutido a lo largo del módulo, el recorrido del directorio podría permitir que los atacantes hagan cualquiera de los siguientes:

- Leer`/etc/passwd`y potencialmente encontrar claves SSH o conocer nombres de usuario válidos para un ataque con contraseña de spray
- Encuentre otros servicios en la caja como Tomcat y lea el`tomcat-users.xml`archivo
- Descubra cookies de sesión de PHP válidas y realice un secuestro de sesiones
- Lea la configuración actual de la aplicación web y el código fuente

La mejor manera de evitar el recorrido del directorio es utilizar la herramienta incorporada de su lenguaje de programación (o marco) para extraer solo el nombre de archivo. Por ejemplo, PHP tiene`basename()`, que leerá la ruta y solo devolverá la porción del nombre de archivo. Si solo se da un nombre de archivo, entonces devolverá solo el nombre de archivo. Si solo se da la ruta, tratará lo que sea después de la final / como el nombre de archivo. La desventaja de este método es que si la aplicación necesita ingresar cualquier directorios, no podrá hacerlo.

Si crea su propia función para hacer este método, es posible que no tenga en cuenta un caso de borde extraño. Por ejemplo, en su terminal Bash, vaya a su directorio de inicio (CD ~) y ejecute el comando`cat .?/.*/.?/etc/passwd`. Verás que Bash permite el`?`y`*`comodines para ser utilizados como`.`. Ahora escriba`php -a`Para ingresar el intérprete de la línea de comandos PHP y ejecutar`echo file_get_contents('.?/.*/.?/etc/passwd');`. Verá que PHP no tiene el mismo comportamiento con los comodines, si reemplaza`?`y`*`con`.`, el comando funcionará como se esperaba. Esto demuestra que hay un borde de casos con nuestra función anterior, si tenemos PHP ejecutar Bash con el`system()`Función, el atacante podría evitar nuestro directorio de prevención transversal. Si usamos funciones nativas en el marco en el que estamos, existe la posibilidad de que otros usuarios capten casos de borde como este y lo arregle antes de que se explote en nuestra aplicación web.

Además, podemos desinfectar la entrada del usuario para eliminar recursivamente cualquier intento de atravesar directorios, de la siguiente manera:

Código:php

```php
while(substr_count($input, '../', 0)) {
    $input = str_replace('../', '', $input);
};

```

Como podemos ver, este código elimina recursivamente`../`subcontratos, entonces incluso si la cadena resultante contiene`../`Todavía lo eliminaría, lo que evitaría algunos de los desvíos que intentamos en este módulo.

---

# **Configuración del servidor web**

También se pueden utilizar varias configuraciones para reducir el impacto de las vulnerabilidades de inclusión de archivos en caso de que ocurran. Por ejemplo, debemos deshabilitar globalmente la inclusión de archivos remotos. En Php esto se puede hacer al establecer`allow_url_fopen`y`allow_url_include`Afirmación.

A menudo también es posible bloquear las aplicaciones web a su directorio raíz web, evitando que accedan a los archivos no relacionados con la WEB. La forma más común de hacer esto en la edad actual es ejecutar la aplicación dentro de`Docker`. Sin embargo, si esa no es una opción, muchos idiomas a menudo tienen una manera de evitar el acceso a archivos fuera del directorio web. En PHP que se puede hacer agregando`open_basedir = /var/www`En el archivo php.ini. Además, debe asegurarse de que ciertos módulos potencialmente peligrosos estén deshabilitados, como[PHP esperar](https://www.php.net/manual/en/wrappers.expect.php) [mod_userdir](https://httpd.apache.org/docs/2.4/mod/mod_userdir.html).

Si se aplican estas configuraciones, debe evitar el acceso a archivos fuera de la carpeta de aplicaciones web, por lo que incluso si se identifica una vulnerabilidad de LFI, su impacto se reduciría.

---

# **Firewall de aplicación web (WAF)**

La forma universal de endurecer las aplicaciones es utilizar un firewall de aplicación web (WAF), como`ModSecurity`. Al tratar con WAFS, lo más importante a evitar es falsos positivos y bloquear las solicitudes no maliciosas. ModSecurity minimiza los falsos positivos al ofrecer un`permissive`modo, que solo informará cosas que habría bloqueado. Esto permite a los defensores sintonizar las reglas para asegurarse de que no se bloquee ninguna solicitud legítima. Incluso si la organización nunca quiere convertir el WAF en "modo de bloqueo", solo tenerlo en modo permisivo puede ser una señal de advertencia temprana de que su aplicación está siendo atacada.

Finalmente, es importante recordar que el propósito de endurecer es darle a la aplicación un caparazón exterior más fuerte, por lo que cuando ocurre un ataque, los defensores tienen tiempo para defenderse. Según el[Informe de FireEye M-Trends de 2020](https://content.fireeye.com/m-trends/rpt-m-trends-2020), el tiempo promedio que tardó en detectar hackers fue de 30 días. Con el endurecimiento adecuado, los atacantes dejarán muchos más letreros, y la organización con suerte detectará estos eventos aún más rápido.

Es importante comprender que el objetivo de endurecer es no hacer que su sistema no sea agitable, lo que significa que no puede descuidar ver registros sobre un sistema endurecido porque es "seguro". Los sistemas endurecidos deben probarse continuamente, especialmente después de que se libere un día cero para una aplicación relacionada con su sistema (ex: Struts Apache, Rails, Django, etc.). En la mayoría de los casos, el día cero funcionaría, pero gracias al endurecimiento, puede generar registros únicos, lo que hizo posible confirmar si el exploit se usó contra el sistema o no.