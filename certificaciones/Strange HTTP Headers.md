# Strange HTTP Headers

**Archivo PCAP relacionado**:

- `CRLF_and_host_header_manipulation.pcapng`

---

Es posible que no notemos nada como confuso de inmediato al analizar el tráfico de nuestro servidor web. Sin embargo, esto no siempre indica que no está sucediendo nada malo. En cambio, siempre podemos vernos un poco más profundos. Para hacerlo, podríamos buscar un comportamiento extraño entre las solicitudes HTTP. Algunos de los cuales son encabezados extraños como

1. `Weird Hosts (Host: )`
2. `Unusual HTTP Verbs`
3. `Changed User Agents`

# **Encontrar encabezados anfitriones extraños**

Para comenzar, como lo haríamos normalmente, podemos limitar nuestra vista en Wireshark a solo respuestas y solicitudes HTTP.

- `http`

![](https://academy.hackthebox.com/storage/modules/229/1-http-headers.png)

Luego, podemos encontrar cualquier encabezado de host irregular con el siguiente comando. Especificamos la dirección IP real de nuestro servidor web para excluir cualquier entrada que use este encabezado real. Si tuviéramos que hacer esto para un servidor web externo, podríamos especificar el nombre de dominio aquí.

- `http.request and (!(http.host == "192.168.10.7"))`

![](https://academy.hackthebox.com/storage/modules/229/2-http-headers.png)

Supongamos que notamos que este filtro devolvió algunos resultados, podríamos cavar en estas solicitudes HTTP un poco más profundas para averiguar qué anfitriones podrían haber intentado usar estos malos actores. Podríamos notar comúnmente`127.0.0.1`.

![](https://academy.hackthebox.com/storage/modules/229/3-http-headers.png)

O en su lugar, algo como Admin.

![](https://academy.hackthebox.com/storage/modules/229/4-http-headers.png)

Los atacantes intentarán usar diferentes encabezados de host para obtener niveles de acceso que normalmente no lograrían a través del host legítimo. Pueden usar herramientas proxy como BURP Suite u otros para modificarlas antes de enviarlas al servidor. Para evitar una explotación exitosa más allá de la detección de estos eventos, siempre debemos hacer lo siguiente.

1. `Ensure that our virtualhosts or access configurations are setup correctly to prevent this form of access.`
2. `Ensure that our web server is up to date.`

# **Análisis del código 400 y el contrabando de solicitud**

También podríamos notar algunas respuestas malas de nuestro servidor web, como el código 400S. Estos códigos indican una mala solicitud del cliente, por lo que pueden ser un buen lugar para comenzar al detectar acciones maliciosas a través de HTTP/HTTPS. Para filtrar para estos, podemos usar lo siguiente

- `http.response.code == 400`

![](https://academy.hackthebox.com/storage/modules/229/6-http-headers.png)

Supongamos que deberíamos seguir una de estas transmisiones HTTP, podríamos notar lo siguiente del cliente.

![](https://academy.hackthebox.com/storage/modules/229/5-http-headers.png)

Esto se conoce comúnmente como el contrabando de solicitudes HTTP o CRLF (alimentación de la línea de retorno del carro). Esencialmente, un atacante intentará lo siguiente.

- `GET%20%2flogin.php%3fid%3d1%20HTTP%2f1.1%0d%0aHost%3a%20192.168.10.5%0d%0a%0d%0aGET%20%2fuploads%2fcmd2.php%20HTTP%2f1.1%0d%0aHost%3a%20127.0.0.1%3a8080%0d%0a%0d%0a%20HTTP%2f1.1 Host: 192.168.10.5`

Que será decodificado por nuestro servidor así.

Código:descifrado

```
GET /login.php?id=1 HTTP/1.1
Host: 192.168.10.5

GET /uploads/cmd2.php HTTP/1.1
Host: 127.0.0.1:8080

 HTTP/1.1
Host: 192.168.10.5

```

Esencialmente, en los casos en que nuestras configuraciones sean vulnerables, la primera solicitud se realizará y la segunda solicitud también será poco después. Esto puede dar a un atacante niveles de acceso que normalmente prohibiríamos. Esto ocurre debido a que nuestra configuración se parece a la siguiente.

# **Configuración de Apache**

Código:TXT

```
<VirtualHost *:80>

    RewriteEngine on
    RewriteRule "^/categories/(.*)" "http://192.168.10.100:8080/categories.php?id=$1" [P]
    ProxyPassReverse "/categories/" "http://192.168.10.100:8080/"

</VirtualHost>

```

[CVE-2023-25690](https://github.com/dhmosfunk/CVE-2023-25690-POC)

Como tal, observar estos Código 400 puede dar una indicación clara a las acciones adversas durante nuestros esfuerzos de análisis de tráfico. Además, notaríamos si un atacante tiene éxito con este ataque al encontrar el código`200` (`success`) en respuesta a una de las solicitudes que se ven así.