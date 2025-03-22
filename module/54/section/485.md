# Directory Fuzzing

Ahora que entendemos el concepto de lucha web y conocemos nuestra lista de palabras, deberíamos estar listos para comenzar a usar`ffuf`Para encontrar directorios de sitios web.

---

# **FFUF**

`Ffuf`está preinstalado en su instancia de Pwnbox. Si desea usarlo en su propia máquina, puede usar "`apt install ffuf -y`"O descargarlo y úselo desde su[Repositorio de Github](https://github.com/ffuf/ffuf.git). Como nuevo usuario de esta herramienta, comenzaremos por emitir el`ffuf -h`Comandar para ver cómo se pueden usar las herramientas:

Directorio Fuzzing

```
xnoxos@htb[/htb]$ ffuf -hHTTP OPTIONS:
  -H               Header `"Name: Value"`, separated by colon. Multiple -H flags are accepted.
  -X               HTTP method to use (default: GET)
  -b               Cookie data `"NAME1=VALUE1; NAME2=VALUE2"` for copy as curl functionality.
  -d               POST data
  -recursion       Scan recursively. Only FUZZ keyword is supported, and URL (-u) has to end in it. (default: false)
  -recursion-depth Maximum recursion depth. (default: 0)
  -u               Target URL
...SNIP...

MATCHER OPTIONS:
  -mc              Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403)
  -ms              Match HTTP response size
...SNIP...

FILTER OPTIONS:
  -fc              Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fs              Filter HTTP response size. Comma separated list of sizes and ranges
...SNIP...

INPUT OPTIONS:
...SNIP...
  -w               Wordlist file path and (optional) keyword separated by colon. eg. '/path/to/wordlist:KEYWORD'

OUTPUT OPTIONS:
  -o               Write output to file
...SNIP...

EXAMPLE USAGE:
  Fuzz file paths from wordlist.txt, match all responses but filter out those with content-size 42.
  Colored, verbose output.
    ffuf -w wordlist.txt -u https://example.org/FUZZ -mc all -fs 42 -c -v
...SNIP...

```

Como podemos ver, el`help`La salida es bastante grande, por lo que solo mantuvimos las opciones que pueden volverse relevantes para nosotros en este módulo.

---

# **Directorio Fuzzing**

Como podemos ver en el ejemplo anterior, las dos opciones principales son`-w`para listas de palabras y`-u`para la url. Podemos asignar una lista de palabras a una palabra clave para referirnos a ella donde queremos luchar. Por ejemplo, podemos elegir nuestra lista de palabras y asignar la palabra clave`FUZZ`a él agregando`:FUZZ`Después de eso:

Directorio Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ
```

A continuación, como queremos ser confundidos para los directorios web, podemos colocar el`FUZZ`palabra clave donde el directorio estaría dentro de nuestra URL, con:

Directorio Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w <SNIP> -u http://SERVER_IP:PORT/FUZZ

```

Ahora, comencemos nuestro objetivo en la pregunta a continuación y ejecutemos nuestro comando final en él:

Directorio Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/FUZZ
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

<SNIP>
blog                    [Status: 301, Size: 326, Words: 20, Lines: 10]
:: Progress: [87651/87651] :: Job [1/1] :: 9739 req/sec :: Duration: [0:00:09] :: Errors: 0 ::

```

Vemos que`ffuf`Probado para casi 90k URL en menos de 10 segundos. Esta velocidad puede variar según su velocidad de Internet y ping si usa`ffuf`En su máquina, pero aún debería ser extremadamente rápido.

Incluso podemos hacerlo ir más rápido si tenemos prisa al aumentar el número de hilos a 200, por ejemplo, con`-t 200`, pero esto no se recomienda, especialmente cuando se usa en un sitio remoto, ya que puede interrumpirlo y causar un`Denial of Service`, o derribar su conexión a Internet en casos severos. Obtenemos un par de éxitos, y podemos visitar uno de ellos para verificar que existe:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_blog.jpg)

Obtenemos una página vacía, lo que indica que el directorio no tiene una página dedicada, pero también muestra que sí tenemos acceso a ella, ya que no obtenemos un código HTTP`404 Not Found`o`403 Access Denied`. En la siguiente sección, buscaremos páginas en este directorio para ver si está realmente vacío o tiene archivos y páginas ocultas.