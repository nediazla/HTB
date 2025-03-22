# Recursive Fuzzing

Hasta ahora, hemos estado luchando por directorios, luego en estos directorios y luego luchando por los archivos. Sin embargo, si tuviéramos docenas de directorios, cada uno con sus propios subdirectorios y archivos, esto llevaría mucho tiempo completar. Para poder automatizar esto, utilizaremos lo que se conoce como`recursive fuzzing`.

---

# **Banderas recursivas**

Cuando escaneamos recursivamente, inicia automáticamente otro escaneo en cualquier directorios recientemente identificados que puedan tener en sus páginas hasta que haya confundido el sitio web principal y todos sus subdirectorios.

Algunos sitios web pueden tener un gran árbol de subdirectorios, como`/login/user/content/uploads/...etc`, y esto expandirá el árbol de escaneo y puede tardar mucho en escanearlos a todos. Por eso siempre se recomienda especificar un`depth`a nuestro escaneo recursivo, de modo que no escanee directorios que sean más profundos que esa profundidad. Una vez que luchamos por los primeros directorios, podemos elegir los directorios más interesantes y ejecutar otro escaneo para dirigir mejor nuestro escaneo.

En`ffuf`, podemos habilitar escaneo recursivo con el`-recursion`bandera, y podemos especificar la profundidad con el`-recursion-depth`bandera. Si especificamos`-recursion-depth 1`, solo borrará los directorios principales y sus subdirectorios directos. Si se identifican algún sub-sub-directorios (como`/login/user`, no los borrará para páginas). Al usar recursión en`ffuf`, podemos especificar nuestra extensión con`-e .php`

Nota: Todavía podemos usar `.php` como extensión de nuestra página, ya que estas extensiones suelen ser en todo el sitio.

Finalmente, también agregaremos la bandera`-v`Para generar las URL completas. De lo contrario, puede ser difícil saber cuál`.php`El archivo se encuentra en qué directorio.

---

# **Escaneo recursivo**

Repitamos el primer comando que usamos, agregue los indicadores de recursión mientras especifica`.php`Como nuestra extensión, y vea qué resultados obtenemos:

Fuzzing recursivo

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v        /'___\  /'___\           /'___\
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
 :: Extensions       : .php
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

[Status: 200, Size: 986, Words: 423, Lines: 56] | URL | http://SERVER_IP:PORT/
    * FUZZ:

[INFO] Adding a new job to the queue: http://SERVER_IP:PORT/forum/FUZZ
[Status: 200, Size: 986, Words: 423, Lines: 56] | URL | http://SERVER_IP:PORT/index.php
    * FUZZ: index.php

[Status: 301, Size: 326, Words: 20, Lines: 10] | URL | http://SERVER_IP:PORT/blog | --> | http://SERVER_IP:PORT/blog/
    * FUZZ: blog

<...SNIP...>
[Status: 200, Size: 0, Words: 1, Lines: 1] | URL | http://SERVER_IP:PORT/blog/index.php
    * FUZZ: index.php

[Status: 200, Size: 0, Words: 1, Lines: 1] | URL | http://SERVER_IP:PORT/blog/
    * FUZZ:

<...SNIP...>

```

Como podemos ver esta vez, el escaneo tardó mucho más, envió casi seis veces el número de solicitudes, y la lista de palabras duplicó su tamaño (una vez con`.php`y una vez sin). Aún así, obtuvimos una gran cantidad de resultados, incluidos todos los resultados que identificamos anteriormente, todos con una sola línea de comando.