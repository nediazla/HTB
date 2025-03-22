# DNS Records

Una vez que accedimos a la página debajo`/blog`, recibimos un mensaje diciendo`Admin panel moved to academy.htb`. Si visitamos el sitio web en nuestro navegador, obtenemos`can’t connect to the server at www.academy.htb`:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_cant_connect_academy.jpg)

Esto se debe a que los ejercicios que hacemos no son sitios web públicos a los que pueda acceder a cualquier persona que no sea sitios web locales dentro de HTB. Los navegadores solo entienden cómo ir a IPS, y si les proporcionamos una URL, intentan asignar la URL a una IP mirando el local`/etc/hosts`Archivo y el DNS público`Domain Name System`. Si la URL no está en ninguno de los dos, no sabría cómo conectarse a ella.

Si visitamos la IP directamente, el navegador va directamente a esa IP y sabe cómo conectarse a ella. Pero en este caso, le decimos que vaya a`academy.htb`, entonces mira el local`/etc/hosts`archivo y no encuentra ninguna mención de ello. Pregunta al DNS público al respecto (como el DNS de Google`8.8.8.8`) y no encuentra ninguna mención de ello, ya que no es un sitio web público, y finalmente no se conecta. Entonces, para conectarse a`academy.htb`, tendríamos que agregarlo a nuestro`/etc/hosts`archivo. Podemos lograrlo con el siguiente comando:

Registros de DNS

```
xnoxos@htb[/htb]$ sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'
```

Ahora podemos visitar el sitio web (no olvide agregar el puerto en la URL) y ver que podemos llegar al sitio web:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_main_site.jpg)

Sin embargo, obtenemos el mismo sitio web que obtuvimos cuando visitamos la IP directamente, así que`academy.htb`es el mismo dominio que hemos estado probando hasta ahora. Podemos verificar eso visitando`/blog/index.php`, y vea que podemos acceder a la página.

Cuando ejecutamos nuestras pruebas en esta IP, no encontramos nada sobre`admin`o paneles, incluso cuando hicimos un`recursive`escanear en nuestro objetivo.`So, in this case, we start looking for sub-domains under '*.academy.htb' and see if we find anything, which is what we will attempt in the next section.`

[Anterior](https://academy.hackthebox.com/module/54/section/483) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/54/section/488)

Hoja de trucos