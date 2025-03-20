# HTTP/HTTPs Service Enumeration Detection

**Archivo PCAP relacionado**:

- `basic_fuzzing.pcapng`

---

Muchas veces, podríamos notar un tráfico extraño a nuestros servidores web. En uno de estos casos, podríamos ver que un host está generando tráfico excesivo con HTTP o HTTPS. A los atacantes les gusta abusar de la capa de transporte muchas veces, ya que las aplicaciones que se ejecutan en nuestros servidores pueden ser vulnerables a diferentes ataques. Como tal, debemos comprender cómo reconocer los pasos que tomará un atacante para recopilar información, explotar y abusar de nuestros servidores web.

En términos generales, podemos detectar e identificar intentos confusos a través de los siguientes

1. `Excessive HTTP/HTTPs traffic from one host`
2. `Referencing our web server's access logs for the same behavior`

Principalmente, los atacantes intentarán borrar nuestro servidor para recopilar información antes de intentar lanzar un ataque. Podríamos tener un`Web Application Firewall`Sin embargo, en su lugar para evitar esto, en algunos casos podríamos no, especialmente si este servidor es interno.

# **Encontrar fuzzing de directorio**

Los atacantes utilizaron el fuzzing del directorio para encontrar todas las páginas y ubicaciones web posibles en nuestras aplicaciones web. Podemos encontrar esto durante nuestro análisis de tráfico limitando nuestra vista de Wireshark solo al tráfico HTTP.

- `http`

![](https://academy.hackthebox.com/storage/modules/229/2-HTTP-Enum.png)

En segundo lugar, si quisiéramos eliminar las respuestas de nuestro servidor, simplemente podríamos especificar`http.request`

![](https://academy.hackthebox.com/storage/modules/229/3-HTTP-Enum.png)

El fuzzing del directorio es bastante simple de detectar, como lo hará en la mayoría de los casos mostrar las siguientes señales

1. `A host will repeatedly attempt to access files on our web server which do not exist (response 404)`.
2. `A host will send these in rapid succession`.

También siempre podemos hacer referencia a este tráfico dentro de nuestros registros de acceso en nuestro servidor web. Para Apache, esto se vería como los siguientes dos ejemplos. Para usar GREP, podríamos filtrar así:

Enumeración del servicio HTTP/HTTPS

```
xnoxos@htb[/htb]$ cat access.log | grep "192.168.10.5"192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /randomfile1 HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /frand2 HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.bash_history HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.bashrc HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.cache HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.config HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.cvs HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.cvsignore HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.forward HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
...SNIP...

```

Y para usar AWK, podríamos hacer lo siguiente

Enumeración del servicio HTTP/HTTPS

```
xnoxos@htb[/htb]$ cat access.log | awk '$1 == "192.168.10.5"'192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /randomfile1 HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /frand2 HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.bash_history HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.bashrc HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.cache HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.config HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.cvs HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.cvsignore HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.forward HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.git/HEAD HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.history HTTP/1.1" 404 435 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
192.168.10.5 - - [18/Jul/2023:12:58:07 -0600] "GET /.hta HTTP/1.1" 403 438 "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
...SNIP...

```

# **Encontrar otras técnicas confusas**

Sin embargo, hay otros tipos de confusos que los atacantes podrían emplear contra nuestros servidores web. Algunos de estos podrían incluir elementos dinámicos o estáticos confusos de nuestras páginas web, como campos de identificación. O en algunos otros casos, el atacante podría buscar vulnerabilidades de IDOR en nuestro sitio, especialmente si estamos manejando el análisis de JSON (cambiando`return=max`a`return=min`).

Para limitar el tráfico a un solo anfitrión, podemos emplear el siguiente filtro:

- `http.request and ((ip.src_host == <suspected IP>) or (ip.dst_host == <suspected IP>))`

![](https://academy.hackthebox.com/storage/modules/229/4-HTTP-Enum.png)

En segundo lugar, siempre podemos construir una imagen general haciendo clic derecho en cualquiera de estas solicitudes, seguiremos y seguir y seguir la transmisión HTTP.

![](https://academy.hackthebox.com/storage/modules/229/4a-HTTP-Enum.png)

Supongamos que notamos que se enviaron muchas solicitudes en rápida sucesión, esto indicaría un intento confuso, y deberíamos llevar a cabo esfuerzos de investigación adicionales contra el anfitrión en cuestión.

Sin embargo, a veces los atacantes harán lo siguiente para evitar la detección

1. `Stagger these responses across a longer period of time.`
2. `Send these responses from multiple hosts or source addresses.`

# **Prevención de intentos confusos**

Podemos tratar de evitar intentos confusos de los adversarios realizando las siguientes acciones.

1. `Maintain our virtualhost or web access configurations to return the proper response codes to throw off these scanners.`
2. `Establish rules to prohibit these IP addresses from accessing our server through our web application firewall.`