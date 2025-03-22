# Local File Inclusion (LFI)

Ahora que entendemos qué son las vulnerabilidades de inclusión de archivos y cómo ocurren, podemos comenzar a aprender cómo podemos explotar estas vulnerabilidades en diferentes escenarios para poder leer el contenido de los archivos locales en el servidor de fondo.

---

# **LFI básico**

El ejercicio que tenemos al final de esta sección nos muestra un ejemplo de una aplicación web que permite a los usuarios establecer su idioma en inglés o español:

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_lang.png)

Si seleccionamos un idioma haciendo clic en él (por ejemplo,`Spanish`), vemos que el texto del contenido cambia al español:

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_es.png)

También notamos que la URL incluye una`language`parámetro que ahora está configurado en el idioma que seleccionamos (`es.php`). Hay varias formas en que el contenido podría cambiarse para que coincida con el lenguaje que especificamos. Puede estar extrayendo el contenido de una tabla de base de datos diferente basada en el parámetro especificado, o puede estar cargando una versión completamente diferente de la aplicación web. Sin embargo, como se discutió anteriormente, cargar parte de la página utilizando motores de plantilla es el método más fácil y más común utilizado.

Por lo tanto, si la aplicación web está extrayendo un archivo que ahora se está incluidos en la página, podemos cambiar el archivo que se extrae para leer el contenido de un archivo local diferente. Dos archivos legibles comunes que están disponibles en la mayoría de los servidores de fondo son`/etc/passwd`en Linux y`C:\Windows\boot.ini`En Windows. Entonces, cambiemos el parámetro desde`es`a`/etc/passwd`:

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_lang_passwd.png)

Como podemos ver, la página es realmente vulnerable, y podemos leer el contenido del`passwd`Archifique e identifique lo que existen los usuarios en el servidor de back-end.

---

# **Transversal de la ruta**

En el ejemplo anterior, leemos un archivo especificando su`absolute path`(p.ej`/etc/passwd`). Esto funcionaría si toda la entrada se usara dentro del`include()`función sin ninguna adición, como el siguiente ejemplo:

Código:php

```php
include($_GET['language']);

```

En este caso, si intentamos leer`/etc/passwd`, entonces el`include()`la función buscaría ese archivo directamente. Sin embargo, en muchas ocasiones, los desarrolladores web pueden agregar o prevenir una cadena al`language`parámetro. Por ejemplo, el`language`El parámetro puede usarse para el nombre de archivo, y se puede agregar después de un directorio, como sigue:

Código:php

```php
include("./languages/" . $_GET['language']);

```

En este caso, si intentamos leer`/etc/passwd`, entonces el camino pasó a`include()`sería (`./languages//etc/passwd`), y como este archivo no existe, no podremos leer nada:

![](https://academy.hackthebox.com/storage/modules/23/traversal_passwd_failed.png)

Como se esperaba, el error verboso devuelto nos muestra la cadena pasada al`include()`función, afirmando que no hay`/etc/passwd`En el directorio de idiomas.

**Nota:**Solo estamos permitiendo errores de PHP en esta aplicación web para fines educativos, por lo que podemos comprender adecuadamente cómo la aplicación web maneja nuestra entrada. Para las aplicaciones web de producción, tales errores nunca deben mostrarse. Además, todos nuestros ataques deberían ser posibles sin errores, ya que no confían en ellos.

Podemos omitir fácilmente esta restricción atravesando directorios utilizando`relative paths`. Para hacerlo, podemos agregar`../`Antes de nuestro nombre de archivo, que se refiere al directorio principal. Por ejemplo, si la ruta completa del directorio de idiomas es`/var/www/html/languages/`, luego usando`../index.php`se referiría al`index.php`Archivo en el directorio principal (es decir,`/var/www/html/index.php`).

Entonces, podemos usar este truco para regresar varios directorios hasta llegar a la ruta de la raíz (es decir,`/`) y luego especifique nuestra ruta de archivo absoluto (por ejemplo`../../../../etc/passwd`), y el archivo debería existir:

![](https://academy.hackthebox.com/storage/modules/23/traversal_passwd.png)

Como podemos ver, esta vez pudimos leer el archivo independientemente del directorio en el que estábamos. Este truco funcionaría incluso si todo el parámetro se usara en el`include()`función, por lo que podemos predecirnos a esta técnica, y debería funcionar en ambos casos. Además, si estuviéramos en la ruta de la raíz (`/`) y usado`../`Entonces todavía permaneceríamos en la ruta de la raíz. Entonces, si no estábamos seguros del directorio en la que está la aplicación web, podemos agregar`../`Muchas veces, y no debería romper el camino (¡incluso si lo hacemos cien veces!).

**Consejo:**Siempre puede ser útil ser eficiente y no agregar innecesario`../`Varias veces, especialmente si estábamos escribiendo un informe o escribiendo un exploit. Entonces, siempre intente encontrar el número mínimo de`../`que funciona y úsalo. También puede calcular cuántos directorios está lejos de la ruta de la raíz y usar tantos. Por ejemplo, con`/var/www/html/`somos`3`directorios lejos de la ruta de la raíz, para que podamos usar`../`3 veces (es decir,`../../../`).

---

# **Prefijo de nombre de archivo**

En nuestro ejemplo anterior, usamos el`language`parámetro después del directorio, por lo que podríamos atravesar el camino para leer el`passwd`archivo. En algunas ocasiones, nuestra entrada puede agregarse después de una cadena diferente. Por ejemplo, se puede usar con un prefijo para obtener el nombre de archivo completo, como el siguiente ejemplo:

Código:php

```php
include("lang_" . $_GET['language']);

```

En este caso, si intentamos atravesar el directorio con`../../../etc/passwd`, la cadena final sería`lang_../../../etc/passwd`, que no es válido:

![](https://academy.hackthebox.com/storage/modules/23/lfi_another_example1.png)

Como se esperaba, el error nos dice que este archivo no existe. Entonces, en lugar de usar directamente el recorrido de la ruta, podemos prefijar un`/`Antes de nuestra carga útil, y esto debería considerar el prefijo como un directorio, y luego debemos evitar el nombre de archivo y poder atravesar directorios:

![](https://academy.hackthebox.com/storage/modules/23/lfi_another_example_passwd1.png)

**Nota:**Esto puede no siempre funcionar, como en este ejemplo un directorio llamado`lang_/`puede no existir, por lo que nuestro camino relativo puede no ser correcto. Además,`any prefix appended to our input may break some file inclusion techniques`Discutiremos en las próximas secciones, como usar envoltorios PHP y filtros o RFI.

---

# **Extensiones adjuntas**

Otro ejemplo muy común es cuando una extensión se agrega al`language`Parámetro, como sigue:

Código:php

```php
include($_GET['language'] . ".php");

```

Esto es bastante común, como en este caso, no tendríamos que escribir la extensión cada vez que necesitamos cambiar el idioma. Esto también puede ser más seguro, ya que puede restringirnos a solo incluir archivos PHP. En este caso, si intentamos leer`/etc/passwd`, entonces el archivo incluido sería`/etc/passwd.php`, que no existe:

![](https://academy.hackthebox.com/storage/modules/23/lfi_extension_failed.png)

Hay varias técnicas que podemos usar para evitar esto, y las discutiremos en las próximas secciones.

**Ejercicio:**Intente leer cualquier archivo PHP (por ejemplo, index.php) a través de LFI, y vea si obtendría su código fuente o si el archivo se representa como HTML.

---

# **Ataques de segundo orden**

Como podemos ver, los ataques LFI pueden venir en diferentes formas. Otro ataque común, y un poco más avanzado, LFI es un`Second Order Attack`. Esto ocurre porque muchas funcionalidades de la aplicación web pueden estar extrayendo los archivos del servidor de fondo basados en parámetros controlados por el usuario.

Por ejemplo, una aplicación web puede permitirnos descargar nuestro avatar a través de una URL como (`/profile/$username/avatar.png`). Si elaboramos un nombre de usuario LFI malicioso (por ejemplo,`../../../etc/passwd`), entonces puede ser posible cambiar el archivo que se está tirando a otro archivo local en el servidor y tomarlo en lugar de nuestro avatar.

En este caso, estaríamos envenenando una entrada de base de datos con una carga útil LFI maliciosa en nuestro nombre de usuario. Luego, otra funcionalidad de la aplicación web utilizaría esta entrada envenenada para realizar nuestro ataque (es decir, descargue nuestro avatar en función del valor del nombre de usuario). Por eso este ataque se llama`Second-Order`ataque.

Los desarrolladores a menudo pasan por alto estas vulnerabilidades, ya que pueden proteger contra la entrada directa del usuario (por ejemplo, de un`?page`Parámetro), pero pueden confiar en los valores extraídos de su base de datos, como nuestro nombre de usuario en este caso. Si logramos envenenar nuestro nombre de usuario durante nuestro registro, entonces el ataque sería posible.

Explotar las vulnerabilidades de LFI utilizando ataques de segundo orden es similar a lo que hemos discutido en esta sección. La única varianza es que necesitamos detectar una función que extraiga un archivo en función de un valor que controlamos indirectamente y luego intentamos controlar ese valor para explotar la vulnerabilidad.

**Nota:**Todas las técnicas mencionadas en esta sección deben funcionar con cualquier vulnerabilidad de LFI, independientemente del lenguaje o marco de desarrollo de back-end.