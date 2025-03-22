# Filtering Results

Hasta ahora, no hemos estado utilizando ningún filtrado para nuestro`ffuf`, y los resultados se filtran automáticamente de forma predeterminada por su código HTTP, que filtra el código`404 NOT FOUND`, y mantiene el resto. Sin embargo, como vimos en nuestra carrera anterior de`ffuf`, podemos obtener muchas respuestas con código`200`. Entonces, en este caso, tendremos que filtrar los resultados en función de otro factor, que aprenderemos en esta sección.

---

# **Filtración**

`Ffuf`Proporciona la opción de hacer coincidir o filtrar un código HTTP específico, tamaño de respuesta o cantidad de palabras. Podemos ver eso con`ffuf -h`:

Resultados filtrantes

```
xnoxos@htb[/htb]$ ffuf -h...SNIP...
MATCHER OPTIONS:
  -mc              Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403)
  -ml              Match amount of lines in response
  -mr              Match regexp
  -ms              Match HTTP response size
  -mw              Match amount of words in response

FILTER OPTIONS:
  -fc              Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fl              Filter by amount of lines in response. Comma separated list of line counts and ranges
  -fr              Filter regexp
  -fs              Filter HTTP response size. Comma separated list of sizes and ranges
  -fw              Filter by amount of words in response. Comma separated list of word counts and ranges
<...SNIP...>

```

En este caso, no podemos usar la coincidencia, ya que no sabemos cuál sería el tamaño de respuesta de otros VHosts. Conocemos el tamaño de la respuesta de los resultados incorrectos, que, como se ve en la prueba anterior, es`900`, y podemos filtrarlo con`-fs 900`. Ahora, repitamos el mismo comando anterior, agregue el indicador anterior y veamos lo que obtenemos:

Resultados filtrantes

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900       /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://academy.htb:PORT/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.academy.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: 900
________________________________________________

<...SNIP...>
admin                   [Status: 200, Size: 0, Words: 1, Lines: 1]
:: Progress: [4997/4997] :: Job [1/1] :: 1249 req/sec :: Duration: [0:00:04] :: Errors: 0 ::

```

Podemos verificar eso visitando la página y viendo si podemos conectarnos a ella:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_blog.jpg)

Nota 1: No olvide agregar "admin.academy.htb" a "/etc/hosts".

Nota 2: Si se ha reiniciado su ejercicio, asegúrese de tener el puerto correcto al visitar el sitio web.

Vemos que podemos acceder a la página, pero obtenemos una página vacía, a diferencia de lo que tenemos con`academy.htb`, por lo tanto, confirmar que esto es de hecho un VHOST diferente. Incluso podemos visitar`https://admin.academy.htb:PORT/blog/index.php`, y veremos que obtendríamos un`404 PAGE NOT FOUND`, confirmando que ahora estamos en un VHOST diferente.

Intente ejecutar un escaneo recursivo en`admin.academy.htb`, y vea qué páginas puede identificar.