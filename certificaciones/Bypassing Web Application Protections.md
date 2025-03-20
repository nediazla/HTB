# Bypassing Web Application Protections

No habrá ninguna protección implementada en el lado objetivo en un escenario ideal, por lo que no evita la explotación automática. De lo contrario, podemos esperar problemas al ejecutar una herramienta automatizada de cualquier tipo contra dicho objetivo. Sin embargo, muchos mecanismos se incorporan a SQLMAP, lo que puede ayudarnos a pasar por alto con éxito tales protecciones.

---

# **Bypass token anti-CSRF**

Una de las primeras líneas de defensa contra el uso de herramientas de automatización es la incorporación de tokens anti-CSRF (es decir, falsificación de solicitudes de sitios cruzados) en todas las solicitudes HTTP, especialmente las generadas como resultado del llenado de forma web.

En la mayoría de los términos básicos, cada solicitud HTTP en dicho escenario debe tener un valor de token (válido) disponible solo si el usuario realmente visitó y usó la página. Si bien la idea original era la prevención de escenarios con enlaces maliciosos, donde solo abrir estos enlaces habría consecuencias no deseadas para los usuarios no informados (p. Ej.

Sin embargo, SQLMAP tiene opciones que pueden ayudar a evitar la protección anti-CSRF. A saber, la opción más importante es`--csrf-token`. Al especificar el nombre del parámetro token (que ya debería estar disponible dentro de los datos de solicitud proporcionados), SQLMAP intentará analizar automáticamente el contenido de respuesta de destino y buscar valores de token frescos para que pueda usarlos en la siguiente solicitud.

Además, incluso en un caso en el que el usuario no especifique explícitamente el nombre del token a través de`--csrf-token`, si uno de los parámetros proporcionados contiene alguno de los infijos comunes (es decir,`csrf`, `xsrf`, `token`), se le solicitará al usuario si se actualiza en solicitudes adicionales:

Omitiendo las protecciones de aplicaciones web

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.9}
|_ -| . [']     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 22:18:01 /2020-09-18/

POST parameter 'csrf-token' appears to hold anti-CSRF token. Do you want sqlmap to automatically update it in further requests? [y/N] y

```

---

# **Valor Onpass de valor único**

En algunos casos, la aplicación web solo puede requerir valores únicos para proporcionar dentro de los parámetros predefinidos. Tal mecanismo es similar a la técnica anti-CSRF descrita anteriormente, excepto que no es necesario analizar el contenido de la página web. Por lo tanto, simplemente garantizar que cada solicitud tenga un valor único para un parámetro predefinido, la aplicación web puede evitar fácilmente los intentos de CSRF y al mismo tiempo evitando algunas de las herramientas de automatización. Para esto, la opción`--randomize`debe usarse, señalando el nombre del parámetro que contiene un valor que debe ser aleatorizado antes de enviarse:

Omitiendo las protecciones de aplicaciones web

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp --batch -v 5 | grep URIURI: http://www.example.com:80/?id=1&rp=99954
URI: http://www.example.com:80/?id=1&rp=87216
URI: http://www.example.com:80/?id=9030&rp=36456
URI: http://www.example.com:80/?id=1.%2C%29%29%27.%28%28%2C%22&rp=16689
URI: http://www.example.com:80/?id=1%27xaFUVK%3C%27%22%3EHKtQrg&rp=40049
URI: http://www.example.com:80/?id=1%29%20AND%209368%3D6381%20AND%20%287422%3D7422&rp=95185

```

---

# **Bypass de parámetro calculado**

Otro mecanismo similar es cuando una aplicación web espera que se calcule un valor de parámetro adecuado en función de otros valores de parámetros. La mayoría de las veces, un valor de parámetro debe contener el digest de mensajes (por ejemplo,`h=MD5(id)`) de otro. Para evitar esto, la opción`--eval`debe usarse, donde se evalúa un código de pitón válido justo antes de que se envíe la solicitud al objetivo:

Omitiendo las protecciones de aplicaciones web

```
xnoxos@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch -v 5 | grep URIURI: http://www.example.com:80/?id=1&h=c4ca4238a0b923820dcc509a6f75849b
URI: http://www.example.com:80/?id=1&h=c4ca4238a0b923820dcc509a6f75849b
URI: http://www.example.com:80/?id=9061&h=4d7e0d72898ae7ea3593eb5ebf20c744
URI: http://www.example.com:80/?id=1%2C.%2C%27%22.%2C%28.%29&h=620460a56536e2d32fb2f4842ad5a08d
URI: http://www.example.com:80/?id=1%27MyipGP%3C%27%22%3EibjjSu&h=db7c815825b14d67aaa32da09b8b2d42
URI: http://www.example.com:80/?id=1%29%20AND%209978%socks4://177.39.187.70:33283ssocks4://177.39.187.70:332833D1232%20AND%20%284955%3D4955&h=02312acd4ebe69e2528382dfff7fc5cc

```

---

# **Dirección IP ocultando**

En caso de que queramos ocultar nuestra dirección IP, o si una determinada aplicación web tiene un mecanismo de protección de que las listas negras de nuestra dirección IP actual, podemos intentar usar un proxy o el Tor de la red de anonimato. Se puede configurar un proxy con la opción`--proxy`(p.ej`--proxy="socks4://177.39.187.70:33283"`), donde debemos agregar un proxy de trabajo.

Además de eso, si tenemos una lista de proxies, podemos proporcionarlos a SQLMAP con la opción`--proxy-file`. De esta manera, SQLMAP irá secuencialmente a través de la lista, y en el caso de cualquier problema (por ejemplo, lista negra de la dirección IP), simplemente saltará de la corriente a la siguiente desde la lista. La otra opción es el uso de la red para proporcionar un anonimato fácil de usar, donde nuestra IP puede aparecer en una gran lista de nodos de salida de TOR. Cuando se instala correctamente en la máquina local, debe haber un`SOCKS4`Servicio proxy en el puerto local 9050 o 9150. Usando el interruptor`--tor`, SQLMAP intentará encontrar automáticamente el puerto local y usarlo adecuadamente.

Si quisiéramos asegurarnos de que Tor se esté utilizando correctamente, para evitar el comportamiento no deseado, podríamos usar el interruptor`--check-tor`. En tales casos, SQLMAP se conectará a la`https://check.torproject.org/`y verifique la respuesta del resultado previsto (es decir,`Congratulations`aparece dentro).

---

# **Bypass de WAF**

Cada vez que ejecutamos SQLMAP, como parte de las pruebas iniciales, SQLMAP envía una carga útil de aspecto malicioso predefinido utilizando un nombre de parámetro inexistente (por ejemplo,`?pfov=...`) Probar la existencia de un WAF (firewall de aplicación web). Habrá un cambio sustancial en la respuesta en comparación con el original en caso de cualquier protección entre el usuario y el objetivo. Por ejemplo, si se implementa una de las soluciones de WAF más populares (modificación), debería haber un`406 - Not Acceptable`respuesta después de tal solicitud.

En caso de una detección positiva, para identificar el mecanismo de protección real, SQLMAP utiliza una biblioteca de terceros[identywaf](https://github.com/stamparm/identYwaf), que contiene las firmas de 80 soluciones WAF diferentes. Si quisiéramos omitir esta prueba heurística por completo (es decir, para producir menos ruido), podemos usar Switch`--skip-waf`.

---

# **Bypass de lista negra de agente de usuario**

En caso de problemas inmediatos (p. Ej.`User-agent: sqlmap/1.4.9 (http://sqlmap.org)`).

Esto es trivial para omitir con el interruptor`--random-agent`, que cambia el agente de usuario predeterminado con un valor elegido al azar de un gran grupo de valores utilizados por los navegadores.

Nota: Si se detecta alguna forma de protección durante la ejecución, podemos esperar problemas con el objetivo, incluso otros mecanismos de seguridad. La razón principal es el desarrollo continuo y las nuevas mejoras en tales protecciones, dejando un espacio de maniobra cada vez más pequeño para los atacantes.

---

# **Scripts de manipulación**

Finalmente, uno de los mecanismos más populares implementados en SQLMAP para evitar las soluciones WAF/IPS son los llamados scripts "Tamper". Los scripts de Tamper son un tipo especial de scripts (Python) escritos para modificar solicitudes justo antes de ser enviados al objetivo, en la mayoría de los casos para evitar cierta protección.

Por ejemplo, uno de los guiones de manipulación más populares[entre](https://github.com/sqlmapproject/sqlmap/blob/master/tamper/between.py)está reemplazando todas las ocurrencias de mayor que el operador (`>`) con`NOT BETWEEN 0 AND #`, y el operador igual (`=`) con`BETWEEN # AND #`. De esta manera, muchos mecanismos de protección primitivos (centrados principalmente en prevenir los ataques XSS) se omiten fácilmente, al menos para fines SQLI.

Los scripts de manipulación se pueden encadenar, uno tras otro, dentro del`--tamper`Opción (por ejemplo`--tamper=between,randomcase`), donde se ejecutan en función de su prioridad predefinida. Una prioridad está predefinida para evitar cualquier comportamiento no deseado, ya que algunos scripts modifican las cargas útiles modificando su sintaxis SQL (por ejemplo,[ifnull2ifisnull](https://github.com/sqlmapproject/sqlmap/blob/master/tamper/ifnull2ifisnull.py)). En contraste, a algunos scripts de manipulación no les importa el contenido interno (por ejemplo,[appendnullbyte](https://github.com/sqlmapproject/sqlmap/blob/master/tamper/appendnullbyte.py)).

Los scripts de Tamper pueden modificar cualquier parte de la solicitud, aunque la mayoría cambia el contenido de carga útil. Los scripts de manipulación más notables son los siguientes:

| **Secuencia de tamper** | **Descripción** |
| --- | --- |
| `0eunion` | Reemplaza instancias deSindicato cone0union |
| `base64encode` | Base64-Engodia a todos los personajes de una carga útil dada |
| `between` | Reemplaza mayor que el operador (`>`) con`NOT BETWEEN 0 AND #`e igual al operador (`=`) con`BETWEEN # AND #` |
| `commalesslimit` | Reemplaza instancias (mysql) como`LIMIT M, N`con`LIMIT N OFFSET M`contrapartida |
| `equaltolike` | Reemplaza todas las ocurrencias de operador igual (`=`) con`LIKE`contrapartida |
| `halfversionedmorekeywords` | Agrega (mysql) comentario versionado antes de cada palabra clave |
| `modsecurityversioned` | Abraza la consulta completa con (mysql) comentario versionado |
| `modsecurityzeroversioned` | Abraza la consulta completa con (mysql) comentario a versiones cero |
| `percentage` | Agrega un signo porcentual (`%`) frente a cada carácter (por ejemplo, selección ->%s%e%l%e%c%t) |
| `plus2concat` | Reemplaza el operador más (`+`) con (mssql) función concat () contraparte |
| `randomcase` | Reemplaza cada carácter de palabra clave con un valor de caso aleatorio (por ejemplo, seleccionar -> seleccionar) |
| `space2comment` | Reemplaza el carácter espacial ( ``) con comentarios `/ |
| `space2dash` | Reemplaza el carácter espacial ( ``) con un comentario de tablero (`--`) seguido de una cadena aleatoria y una nueva línea (`\n`) |
| `space2hash` | Reemplaza (mysql) instancias de carácter espacial ( ``) con un personaje de libra (`#`) seguido de una cadena aleatoria y una nueva línea (`\n`) |
| `space2mssqlblank` | Reemplaza (mssql) instancias de carácter espacial ( ``) con un carácter en blanco aleatorio de un conjunto válido de caracteres alternativos |
| `space2plus` | Reemplaza el carácter espacial ( ``) con más (`+`) |
| `space2randomblank` | Reemplaza el carácter espacial ( ``) con un carácter en blanco aleatorio de un conjunto válido de caracteres alternativos |
| `symboliclogical` | Reemplaza y o o o a los operadores lógicos con sus contrapartes simbólicas (`&&`y`||`) |
| `versionedkeywords` | Encierra cada palabra clave sin función con (mysql) comentario versionado |
| `versionedmorekeywords` | Encierra cada palabra clave con (mysql) comentario versionado |

Para obtener una lista completa de scripts de tamper implementados, junto con la descripción como se indica anteriormente, Switch`--list-tampers`se puede usar. También podemos desarrollar scripts de manipulación personalizados para cualquier tipo de ataque personalizado, como un SQLI de segundo orden.

---

# **Bypass misceláneos**

De otros mecanismos de derivación de protección, también hay dos más que deben mencionarse. El primero es el`Chunked`Codificación de transferencia, encendido utilizando el interruptor`--chunked`, que divide el cuerpo de la solicitud posterior a los llamados "trozos". Las palabras clave SQL en la lista negra se dividen entre trozos de una manera que la solicitud que las contenga puede pasar desapercibida.

El otro mecanismos de derivación es el`HTTP parameter pollution` (`HPP`), donde las cargas útiles se dividen de manera similar que en el caso de`--chunked`Entre los valores de diferentes parámetros con nombre (p. Ej.`?id=1&id=UNION&id=SELECT&id=username,password&id=FROM&id=users...`), que están concatenados por la plataforma objetivo si la admiten (por ejemplo,`ASP`).