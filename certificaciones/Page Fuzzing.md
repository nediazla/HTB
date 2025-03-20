# Page Fuzzing

Ahora entendemos el uso básico de`ffuf`a través de la utilización de listas de palabras y palabras clave. A continuación, aprenderemos a localizar páginas.

Nota: También podemos generar el mismo objetivo de la sección anterior para los ejemplos de esta sección.

---

# **Extensión Fuzza**

En la sección anterior, descubrimos que teníamos acceso a`/blog`, pero el directorio devolvió una página vacía, y no podemos localizar manualmente ningún enlace o página. Por lo tanto, una vez más utilizaremos fuzzing web para ver si el directorio contiene páginas ocultas. Sin embargo, antes de comenzar, debemos averiguar qué tipos de páginas utiliza el sitio web, como`.html`, `.aspx`, `.php`, o algo más.

Una forma común de identificarlo es encontrar el tipo de servidor a través de los encabezados de respuesta HTTP y adivinando la extensión. Por ejemplo, si el servidor es`apache`, entonces puede ser`.php`, o si era`IIS`, entonces podría ser`.asp`o`.aspx`, etcétera. Sin embargo, este método no es muy práctico. Entonces, volveremos a utilizar`ffuf`Para borrar la extensión, similar a la forma en que luchamos por los directorios. En lugar de colocar el`FUZZ`palabra clave donde estaría el nombre del directorio, lo colocaríamos donde estaría la extensión`.FUZZ`, y use una lista de palabras para extensiones comunes. Podemos utilizar la siguiente lista de palabras en`SecLists`Para extensiones:

Page Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ <SNIP>

```

Antes de comenzar a borrar, debemos especificar qué archivo esa extensión sería al final. Siempre podemos usar dos listas de palabras y tener una palabra clave única para cada uno, y luego hacer`FUZZ_1.FUZZ_2`para luchar por ambos. Sin embargo, hay un archivo que siempre podemos encontrar en la mayoría de los sitios web, que es`index.*`, por lo que lo usaremos como nuestras extensiones de archivo y fuzz.

Nota: La lista de palabras que elegimos ya contiene un punto (.), Por lo que no tendremos que agregar el punto después del "índice" en nuestra confusión.

Ahora, podemos volver a ejecutar nuestro comando, colocando cuidadosamente nuestro`FUZZ`Palabra clave donde estaría la extensión después`index`:

Page Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/blog/indexFUZZ
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 5
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

.php                    [Status: 200, Size: 0, Words: 1, Lines: 1]
.phps                   [Status: 403, Size: 283, Words: 20, Lines: 10]
:: Progress: [39/39] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::

```

Recibimos un par de éxitos, pero solo`.php`nos da una respuesta con el código`200`. ¡Excelente! Ahora sabemos que este sitio web se ejecuta en`PHP`para comenzar a borrar por`PHP`archivos.

---

# **Page Fuzzing**

Ahora usaremos el mismo concepto de palabras clave que hemos estado usando con`ffuf`, usar`.php`Como la extensión, coloque nuestro`FUZZ`Palabra clave donde debe estar el nombre de archivo, y use la misma lista de palabras que utilizamos para directorios confusos:

Page Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/blog/FUZZ.php
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

index                   [Status: 200, Size: 0, Words: 1, Lines: 1]
REDACTED                [Status: 200, Size: 465, Words: 42, Lines: 15]
:: Progress: [87651/87651] :: Job [1/1] :: 5843 req/sec :: Duration: [0:00:15] :: Errors: 0 ::

```

Tenemos un par de éxitos; Ambos tienen un código HTTP 200, lo que significa que podemos acceder a ellos. index.php tiene un tamaño de 0, lo que indica que es una página vacía, mientras que la otra no, lo que significa que tiene contenido. Podemos visitar cualquiera de estas páginas para verificar esto:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_login.jpg)