# Parameter Fuzzing - GET

Si ejecutamos un recursivo`ffuf`escanear`admin.academy.htb`, deberíamos encontrar`http://admin.academy.htb:PORT/admin/admin.php`. Si intentamos acceder a esta página, vemos lo siguiente:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_admin.jpg)

Eso indica que debe haber algo que identifique a los usuarios para verificar si tienen acceso para leer el`flag`. No iniciamos sesión, ni tenemos ninguna cookie que pueda verificarse en el backend. Entonces, tal vez hay una clave que podemos pasar a la página para leer el`flag`. Tales claves generalmente se pasarían como un`parameter`, usando un`GET`o`POST`Solicitud HTTP. Esta sección discutirá cómo luchar para tales parámetros hasta que identifiquemos un parámetro que puede ser aceptado por la página.

**Consejo:**Los parámetros de fuzzing pueden exponer parámetros no publicados que son accesibles públicamente. Tales parámetros tienden a ser menos probados y menos asegurados, por lo que es importante probar tales parámetros para las vulnerabilidades web que discutimos en otros módulos.

---

# **Obtener la solicitud Fuzzing**

De manera similar a cómo hemos estado confundiendo varias partes de un sitio web, utilizaremos`ffuf`para enumerar los parámetros. Primero comencemos con la pelusa para`GET`solicitudes, que generalmente se pasan justo después de la URL, con un`?`símbolo, como:

- `http://admin.academy.htb:PORT/admin/admin.php?param1=key`.

Entonces, todo lo que tenemos que hacer es reemplazar`param1`En el ejemplo anterior con`FUZZ`y reiniciar nuestro escaneo. Sin embargo, antes de que podamos comenzar, debemos elegir una lista de palabras apropiada. Una vez más,`SecLists`tiene exactamente eso en`/opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt`. Con eso, podemos ejecutar nuestro escaneo.

Una vez más, recuperaremos muchos resultados, por lo que filtraremos el tamaño de respuesta predeterminado que estamos obteniendo.

Fuzzing de parámetros - Obtener

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

<...SNIP...>                    [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]

```

Recibimos un golpe. Intentemos visitar la página y agregue esto`GET`parámetro, y vea si podemos leer la bandera ahora:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_admin_param1.jpg)

Como podemos ver, el único golpe que recibimos ha sido`deprecated`y parece no estar ya en uso.