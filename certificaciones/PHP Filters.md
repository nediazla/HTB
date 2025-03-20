# PHP Filters

Muchas aplicaciones web populares se desarrollan en PHP, junto con varias aplicaciones web personalizadas construidas con diferentes marcos de PHP, como Laravel o Symfony. Si identificamos una vulnerabilidad de LFI en aplicaciones web de PHP, entonces podemos utilizar diferentes[Envoltorios PHP](https://www.php.net/manual/en/wrappers.php.php)para poder extender nuestra explotación de LFI e incluso potencialmente alcanzar la ejecución del código remoto.

Los envoltorios PHP nos permiten acceder a diferentes flujos de E/S en el nivel de aplicación, como entrada/salida estándar, descriptores de archivos y flujos de memoria. Esto tiene muchos usos para los desarrolladores de PHP. Aún así, como probadores de penetración web, podemos utilizar estos envoltorios para extender nuestros ataques de explotación y poder leer archivos de código fuente de PHP o incluso ejecutar comandos del sistema. Esto no solo es beneficioso con los ataques LFI, sino también con otros ataques web como XXE, como se cubre en el[Ataques web](https://academy.hackthebox.com/module/details/134)módulo.

En esta sección, veremos cómo se utilizan los filtros PHP básicos para leer el código fuente de PHP, y en la siguiente sección, veremos cómo los diferentes envoltorios PHP pueden ayudarnos a obtener la ejecución del código remoto a través de las vulnerabilidades de LFI.

---

# **Filtros de entrada**

[Filtros PHP](https://www.php.net/manual/en/filters.php)son un tipo de envoltorios PHP, donde podemos pasar diferentes tipos de entrada y hacer que el filtro se filtre. Para usar transmisiones de envoltorio PHP, podemos usar el`php://`esquema en nuestra cadena, y podemos acceder a la envoltura de filtro PHP con`php://filter/`.

El`filter`Wrapper tiene varios parámetros, pero los principales que requerimos para nuestro ataque son`resource`y`read`. El`resource`Se requiere parámetro para envoltorios de filtros, y con él podemos especificar la secuencia que nos gustaría aplicar el filtro (por ejemplo, un archivo local), mientras que el`read`El parámetro puede aplicar diferentes filtros en el recurso de entrada, por lo que podemos usarlo para especificar qué filtro queremos aplicar en nuestro recurso.

Hay cuatro tipos diferentes de filtros disponibles para su uso, que son[Filtros de cadena](https://www.php.net/manual/en/filters.string.php), [Filtros de conversión](https://www.php.net/manual/en/filters.convert.php), [Filtros de compresión](https://www.php.net/manual/en/filters.compression.php), y[Filtros de cifrado](https://www.php.net/manual/en/filters.encryption.php). Puede leer más sobre cada filtro en su enlace respectivo, pero el filtro que es útil para los ataques LFI es el`convert.base64-encode`Filtro, debajo`Conversion Filters`.

---

# **Fuzzing para archivos PHP**

El primer paso sería luchar para diferentes páginas de PHP disponibles con una herramienta como`ffuf`o`gobuster`, como cubierto en el[Atacando aplicaciones web con FFUF](https://academy.hackthebox.com/module/details/54)módulo:

Filtros PHP

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php

...SNIP...

index                   [Status: 200, Size: 2652, Words: 690, Lines: 64]
config                  [Status: 302, Size: 0, Words: 1, Lines: 1]

```

**Consejo:**A diferencia del uso normal de la aplicación web, no estamos restringidos a las páginas con el Código de Respuesta HTTP 200, ya que tenemos acceso local a la inclusión de archivos, por lo que deberíamos escanear todos los códigos, incluidos las páginas `301`,` 302` y `403`, y deberíamos poder leer su código fuente también.

Incluso después de leer las fuentes de cualquier archivo identificado, podemos`scan them for other referenced PHP files`, y luego leerlos también, hasta que podamos capturar la mayoría de la fuente de la aplicación web o tener una imagen precisa de lo que hace. También es posible comenzar leyendo`index.php`y escanearlo para obtener más referencias, etc., pero la confusa para los archivos PHP puede revelar algunos archivos que de otro modo no se pueden encontrar de esa manera.

---

# **Inclusión estándar de PHP**

En secciones anteriores, si intentó incluir cualquier archivo PHP a través de LFI, habría notado que el archivo PHP incluido se ejecuta y finalmente se representa como una página HTML normal. Por ejemplo, intentemos incluir el`config.php`página (`.php`extensión adjunta por la aplicación web):

![](https://academy.hackthebox.com/storage/modules/23/lfi_config_failed.png)

Como podemos ver, obtenemos un resultado vacío en lugar de nuestra cadena LFI, desde el`config.php`Lo más probable es que solo establezca la configuración de la aplicación web y no representa ninguna salida HTML.

Esto puede ser útil en ciertos casos, como acceder a páginas PHP locales, no tenemos acceso (es decir, SSRF), pero en la mayoría de los casos, estaríamos más interesados en leer el código fuente de PHP a través de LFI, ya que los códigos fuente tienden a revelar información importante sobre la aplicación web. Aquí es donde el`base64`El filtro PHP se vuelve útil, ya que podemos usarlo para codificar el archivo PHP, y luego obtendríamos el código fuente codificado en lugar de que se ejecute y renderice. Esto es especialmente útil para los casos en los que estamos tratando con LFI con extensiones de PHP adjuntas, porque podemos restringirnos a incluir solo archivos PHP, como se discutió en la sección anterior.

**Nota:**Lo mismo se aplica a los lenguajes de aplicaciones web distintas de PHP, siempre que la función vulnerable pueda ejecutar archivos. De lo contrario, obtendríamos directamente el código fuente y no necesitaríamos usar filtros/funciones adicionales para leer el código fuente. Consulte la tabla de funciones en la Sección 1 para ver qué funciones tienen qué privilegios.

---

# **Divulgación del código fuente**

Una vez que tenemos una lista de posibles archivos PHP que queremos leer, podemos comenzar a revelar sus fuentes con el`base64`Filtro PHP. Intentemos leer el código fuente de`config.php`Usando el filtro Base64, especificando`convert.base64-encode`para el`read`parámetro y`config`para el`resource`Parámetro, como sigue:

Código:url

```
php://filter/read=convert.base64-encode/resource=config

```

![](https://academy.hackthebox.com/storage/modules/23/lfi_config_wrapper.png)

**Nota:**Dejamos intencionalmente el archivo de recursos al final de nuestra cadena, como el`.php`La extensión se adjunta automáticamente al final de nuestra cadena de entrada, lo que haría que el recurso que especificamos sea`config.php`.

Como podemos ver, a diferencia de nuestro intento con LFI normal, el uso del filtro Base64 devolvió una cadena codificada en lugar del resultado vacío que vimos anteriormente. Ahora podemos decodificar esta cadena para obtener el contenido del código fuente de`config.php`, como sigue:

Filtros PHP

```
xnoxos@htb[/htb]$ echo 'PD9waHAK...SNIP...KICB9Ciov' | base64 -d...SNIP...

if ($_SERVER['REQUEST_METHOD'] == 'GET' && realpath(__FILE__) == realpath($_SERVER['SCRIPT_FILENAME'])) {  header('HTTP/1.0 403 Forbidden', TRUE, 403);
  die(header('location: /index.php'));
}

...SNIP...

```

**Consejo:**Al copiar la cadena codificada Base64, asegúrese de copiar toda la cadena o no decodificará completamente. Puede ver la fuente de la página para asegurarse de copiar toda la cadena.

Ahora podemos investigar este archivo para obtener información confidencial como credenciales o claves de la base de datos y comenzar a identificar más referencias y luego revelar sus fuentes.