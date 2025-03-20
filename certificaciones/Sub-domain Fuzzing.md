# Sub-domain Fuzzing

En esta sección, aprenderemos a usar`ffuf`para identificar subdominios (es decir,`*.website.com`) para cualquier sitio web.

---

# **Subdominios**

Un subdominio es cualquier sitio web subyacente a otro dominio. Por ejemplo,`https://photos.google.com`es el`photos`subdominio de`google.com`.

En este caso, simplemente estamos revisando diferentes sitios web para ver si existen al verificar si tienen un registro público de DNS que nos redirigiría a una IP del servidor de trabajo. Entonces, ejecutemos un escaneo y veamos si obtenemos algún golpe. Antes de que podamos comenzar nuestro escaneo, necesitamos dos cosas:

- A`wordlist`
- A`target`

Afortunadamente para nosotros, en el`SecLists`Repo, hay una sección específica para las listas de palabras del subdominio, que consiste en palabras comunes generalmente utilizadas para subdominios. Podemos encontrarlo en`/opt/useful/seclists/Discovery/DNS/`. En nuestro caso, estaríamos usando una lista de palabras más corta, que es`subdomains-top1million-5000.txt`. Si queremos extender nuestro escaneo, podemos elegir una lista más grande.

En cuanto a nuestro objetivo, usaremos`inlanefreight.com`como nuestro objetivo y ejecutar nuestro escaneo en él. Usemos`ffuf`y colocar el`FUZZ`Palabra clave en el lugar de los subdominios, y vea si obtenemos algún golpe:

Subdominio borroso

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : https://FUZZ.inlanefreight.com/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 381ms]
    * FUZZ: support

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 385ms]
    * FUZZ: ns3

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 402ms]
    * FUZZ: blog

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 180ms]
    * FUZZ: my

[Status: 200, Size: 22266, Words: 2903, Lines: 316, Duration: 589ms]
    * FUZZ: www

<...SNIP...>

```

Vemos que obtenemos algunos golpes. Ahora, podemos intentar ejecutar lo mismo en`academy.htb`Y vea si tenemos algún golpe:

Subdominio borroso

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : https://FUZZ.academy.htb/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

:: Progress: [4997/4997] :: Job [1/1] :: 131 req/sec :: Duration: [0:00:38] :: Errors: 4997 ::

```

Vemos que no obtenemos ningún golpe. ¿Significa esto que no hay subdominio debajo`academy.htb`? - No.

Esto significa que no hay`public`subdominios debajo`academy.htb`, ya que no tiene un registro público de DNS, como se mencionó anteriormente. Aunque agregamos`academy.htb`a nuestro`/etc/hosts`archivo, solo agregamos el dominio principal, así que cuando`ffuf`está buscando otros subdominios, no los encontrará en`/etc/hosts`, y le preguntará al DNS público, lo que obviamente no los tendrá.