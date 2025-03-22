# Running SQLMap on an HTTP Request

SQLMAP tiene numerosas opciones e interruptores que se pueden usar para configurar correctamente la solicitud (HTTP) antes de su uso.

En muchos casos, los errores simples como olvidar proporcionar valores de cookies adecuados, una configuración excesiva con una larga línea de comandos o una declaración inadecuada de datos posteriores al formateado, evitarán la detección y explotación correctas de la potencial vulnerabilidad SQLI.

---

# **Comandos de curl**

Una de las mejores y más fáciles formas de configurar correctamente una solicitud SQLMAP contra el objetivo específico (es decir, la solicitud web con parámetros en el interior) es utilizando

```
Copy as cURL
```

característica desde el panel de red (monitor) dentro de las herramientas de desarrollador de Chrome, Edge o Firefox:

![](https://academy.hackthebox.com/storage/modules/58/M5UVR6n.png)

Pegando el contenido del portapapeles (`Ctrl-V`) en la línea de comando y cambiando el comando original`curl`a`sqlmap`, podemos usar SQLMAP con el idéntico`curl`dominio:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'
```

Al proporcionar datos para las pruebas en SQLMAP, debe haber un valor de parámetro que pueda evaluarse para la vulnerabilidad SQLI o opciones/conmutadores especializados para la búsqueda de parámetros automáticos (por ejemplo,`--crawl`, `--forms`o`-g`).

---

# **Obtener/publicar solicitudes**

En el escenario más común,`GET`Los parámetros se proporcionan con el uso de la opción`-u`/`--url`, como en el ejemplo anterior. En cuanto a las pruebas`POST`datos, el`--data`La bandera se puede usar, como sigue:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

En tales casos,`POST`parámetros`uid`y`name`será probado para la vulnerabilidad SQLI. Por ejemplo, si tenemos una indicación clara de que el parámetro`uid`es propenso a una vulnerabilidad SQLI, podríamos reducir las pruebas solo a este parámetro usando`-p uid`. De lo contrario, podríamos marcarlo dentro de los datos proporcionados con el uso del marcador especial`*`como sigue:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```

---

# **Solicitudes completas de HTTP**

Si necesitamos especificar una solicitud HTTP compleja con muchos valores de encabezado diferentes y un cuerpo postal alargado, podemos usar el`-r`bandera. Con esta opción, SQLMAP se proporciona con el "archivo de solicitud", que contiene toda la solicitud HTTP dentro de un solo archivo textual. En un escenario común, dicha solicitud HTTP se puede capturar desde una aplicación de proxy especializada (por ejemplo,`Burp`) y escrito en el archivo de solicitud, como sigue:

![](https://academy.hackthebox.com/storage/modules/58/x7ND6VQ.png)

Un ejemplo de una solicitud HTTP capturada con`Burp`se vería como:

Código:http

```
GET /?id=1 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
DNT: 1
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947"
Cache-Control: max-age=0

```

Podemos copiar manualmente la solicitud HTTP desde adentro`Burp`y escribirlo en un archivo, o podemos hacer clic derecho en la solicitud dentro de`Burp`y elegir`Copy to file`. Otra forma de capturar la solicitud HTTP completa sería mediante el uso del navegador, como se mencionó anteriormente en la sección, y la elección de la opción`Copy` > `Copy Request Headers`y luego pegar la solicitud en un archivo.

Para ejecutar SQLMAP con un archivo de solicitud HTTP, usamos el`-r`bandera, como sigue:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap -r req.txt        ___
       __H__
 ___ ___["]_____ ___ ___  {1.4.9}
|_ -| . [(]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 14:32:59 /2020-09-11/

[14:32:59] [INFO] parsing HTTP request from 'req.txt'
[14:32:59] [INFO] testing connection to the target URL
[14:32:59] [INFO] testing if the target URL content is stable
[14:33:00] [INFO] target URL content is stable

```

Consejo: De manera similar al caso con la opción '--data', dentro del archivo de solicitud guardado, podemos especificar el parámetro que queremos inyectar con un asterisco (*), como '/? Id =*'.

---

# **Solicitudes personalizadas de SQLMAP**

Si queríamos elaborar solicitudes complicadas manualmente, existen numerosos interruptores y opciones para ajustar SQLMAP.

Por ejemplo, si hay un requisito para especificar el valor de cookie (sesión) para`PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c`opción`--cookie`se utilizaría de la siguiente manera:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

El mismo efecto se puede hacer con el uso de la opción`-H/--header`:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

Podemos aplicar lo mismo a opciones como`--host`, `--referer`, y`-A/--user-agent`, que se utilizan para especificar los mismos valores de encabezados HTTP.

Además, hay un interruptor`--random-agent`diseñado para seleccionar aleatoriamente un`User-agent`Valor de encabezado de la base de datos incluida de los valores regulares del navegador. Este es un cambio importante para recordar, ya que cada vez más soluciones de protección eliminan automáticamente todo el tráfico HTTP que contiene el valor de usuario de usuario SQLMAP predeterminado reconocible (por ejemplo,`User-agent: sqlmap/1.4.9.12#dev (http://sqlmap.org)`). Alternativamente, el`--mobile`El interruptor se puede usar para imitar el teléfono inteligente usando ese mismo valor de encabezado.

Si bien SQLMAP, por defecto, se dirige solo a los parámetros HTTP, es posible probar los encabezados para la vulnerabilidad SQLI. La forma más fácil es especificar la marca de inyección "personalizada" después del valor del encabezado (por ejemplo,`--cookie="id=1*"`). El mismo principio se aplica a cualquier otra parte de la solicitud.

Además, si quisiéramos especificar un método HTTP alternativo, que no sea`GET`y`POST`(p.ej,`PUT`), podemos utilizar la opción`--method`, como sigue:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap -u www.target.com --data='id=1' --method PUT
```

---

# **Solicitudes HTTP personalizadas**

Aparte de los datos de forma más comunes`POST`Estilo del cuerpo (por ejemplo`id=1`), SQLMAP también es compatible con JSON Formated (por ejemplo`{"id":1}`) y XML formateado (por ejemplo`<element><id>1</id></element>`) Solicitudes HTTP.

El apoyo para estos formatos se implementa de manera "relajada"; Por lo tanto, no hay restricciones estrictas sobre cómo se almacenan los valores de los parámetros en el interior. En caso de que el`POST`el cuerpo es relativamente simple y corto, la opción`--data`será suficiente.

Sin embargo, en el caso de un cuerpo complejo o largo, podemos usar una vez más el`-r`opción:

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ cat req.txtHTTP / HTTP/1.0
Host: www.example.com

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "Example JSON",
      "body": "Just an example",
      "created": "2020-05-22T14:56:29.000Z",
      "updated": "2020-05-22T14:56:28.000Z"
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "user"}
      }
    }
  }]
}

```

Ejecutar SQLMAP en una solicitud HTTP

```
xnoxos@htb[/htb]$ sqlmap -r req.txt        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.4.9}
|_ -| . [)]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 00:03:44 /2020-09-15/

[00:03:44] [INFO] parsing HTTP request from 'req.txt'
JSON data found in HTTP body. Do you want to process it? [Y/n/q]
[00:03:45] [INFO] testing connection to the target URL
[00:03:45] [INFO] testing if the target URL content is stable
[00:03:46] [INFO] testing if HTTP parameter 'JSON type' is dynamic
[00:03:46] [WARNING] HTTP parameter 'JSON type' does not appear to be dynamic
[00:03:46] [WARNING] heuristic (basic) test shows that HTTP parameter 'JSON type' might not be injectable
```