# Parameter Fuzzing - POST

La principal diferencia entre`POST`solicitudes y`GET`las solicitudes es que`POST`Las solicitudes no se pasan con la URL y no se pueden agregar simplemente después de un`?`símbolo.`POST`Se pasan solicitudes en el`data`campo dentro de la solicitud HTTP. Mira el[Solicitudes web](https://academy.hackthebox.com/module/details/35)Módulo para obtener más información sobre las solicitudes HTTP.

Para borrar el`data`hacer`ffuf`, podemos usar el`-d`bandera, como vimos anteriormente en la salida de`ffuf -h`. También tenemos que agregar`-X POST`para enviar`POST`solicitudes.

Consejo: En PHP, "Post" Data "Type de contenido" solo puede aceptar "Aplicación/X-WWW-Form-Urlencoded". Entonces, podemos establecer eso en "ffuf" con "-h 'contenido de tipo: aplicación/x-www-form-urlencoded'".

Entonces, repitamos lo que hicimos antes, pero coloque nuestro`FUZZ`palabra clave después del`-d`bandera:

```
[!bash!]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : POST
 :: URL              : http://admin.academy.htb:PORT/admin/admin.php
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : FUZZ=key
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

id                      [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
<...SNIP...>

```

Como podemos ver esta vez, obtuvimos un par de hits, el mismo que obtuvimos cuando se confunde`GET`y otro parámetro, que es`id`. Veamos qué obtenemos si enviamos un`POST`Solicitar con el`id`parámetro. Podemos hacer eso con`curl`, como sigue:

```
[!bash!]$ curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'<div class='center'><p>Invalid id!</p></div>
<...SNIP...>

```

Como podemos ver, el mensaje ahora dice`Invalid id!`.

[Anterior](https://academy.hackthebox.com/module/54/section/490) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/54/section/505)

Hoja de trucos