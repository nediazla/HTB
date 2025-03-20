# Bypassing Basic Authentication

La explotación de vulnerabilidades de manipulación de verbos HTTP suele ser un proceso relativamente sencillo. Solo necesitamos probar métodos HTTP alternativos para ver cómo los manejan el servidor web y la aplicación web. Si bien muchas herramientas automatizadas de escaneo de vulnerabilidad pueden identificar consistentemente las vulnerabilidades de manipulación de verbos HTTP causadas por configuraciones de servidores inseguros, generalmente se pierden la identificación de vulnerabilidades de manipulación HTTP causadas por la codificación insegura. Esto se debe a que el primer tipo se puede identificar fácilmente una vez que omitimos una página de autenticación, mientras que el otro necesita pruebas activas para ver si podemos evitar los filtros de seguridad en su lugar.

El primer tipo de vulnerabilidad de manipulación de verbos HTTP es causada principalmente por`Insecure Web Server Configurations`y explotar esta vulnerabilidad puede permitirnos omitir el indicador de autenticación básica HTTP en ciertas páginas.

---

# **Identificar**

Cuando comenzamos el ejercicio al final de esta sección, vemos que tenemos un básico`File Manager`aplicación web, en la que podemos agregar nuevos archivos escribiendo sus nombres y presionando`enter`:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_add.jpg)

Sin embargo, supongamos que intentamos eliminar todos los archivos haciendo clic en el rojo`Reset`botón. En ese caso, vemos que esta funcionalidad parece estar restringida solo para usuarios autenticados, a medida que obtenemos lo siguiente`HTTP Basic Auth`inmediato:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_reset.jpg)

Como no tenemos credenciales, obtendremos un`401 Unauthorized`página:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_unauthorized.jpg)

Entonces, veamos si podemos evitar esto con un ataque de manipulación de verbos HTTP. Para hacerlo, necesitamos identificar qué páginas están restringidas por esta autenticación. Si examinamos la solicitud HTTP después de hacer clic en el botón Restablecer o observamos la URL que el botón navega después de hacer clic, vemos que está en`/admin/reset.php`. Entonces, o el`/admin`El directorio está restringido solo a usuarios autenticados, o solo al`/admin/reset.php`La página es. Podemos confirmar esto visitando el`/admin`Directorio, y de hecho nos solicitan que vuelvan a iniciar sesión. Esto significa que el completo`/admin`El directorio está restringido.

---

# **Explotar**

Para intentar explotar la página, necesitamos identificar el método de solicitud HTTP utilizado por la aplicación web. Podemos interceptar la solicitud en Burp Suite y examinarla:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_unauthorized_request.jpg)

Como la página usa un

```
GET
```

Solicitud, podemos enviar un

```
POST
```

Solicitar y ver si la página web permite

```
POST
```

solicitudes (es decir, si la autenticación cubre

```
POST
```

solicitudes). Para hacerlo, podemos hacer clic derecho en la solicitud interceptada en Burp y seleccionar

```
Change Request Method
```

, y cambiará automáticamente la solicitud a un

```
POST
```

pedido:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_change_request.jpg)

Una vez que lo hacemos, podemos hacer clic`Forward`y examine la página en nuestro navegador. Desafortunadamente, todavía nos solicitan iniciar sesión y obtendremos un`401 Unauthorized`página si no proporcionamos las credenciales:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_reset.jpg)

Entonces, parece que las configuraciones del servidor web cubren ambos`GET`y`POST`solicitudes. Sin embargo, como hemos aprendido anteriormente, podemos utilizar muchos otros métodos HTTP, especialmente el`HEAD`método, que es idéntico a un`GET`Solicite pero no devuelve el cuerpo en la respuesta HTTP. Si esto es exitoso, es posible que no recibamos ningún resultado, sino el`reset`La función aún debe ser ejecutada, que es nuestro objetivo principal.

Para ver si el servidor acepta`HEAD`Solicitudes, podemos enviar un`OPTIONS`Solicite y vea qué métodos HTTP se aceptan, de la siguiente manera:

Omitiendo la autenticación básica

```
xnoxos@htb[/htb]$ curl -i -X OPTIONS http://SERVER_IP:PORT/HTTP/1.1 200 OK
Date:
Server: Apache/2.4.41 (Ubuntu)
Allow: POST,OPTIONS,HEAD,GET
Content-Length: 0
Content-Type: httpd/unix-directory

```

Como podemos ver, la respuesta muestra`Allow: POST,OPTIONS,HEAD,GET`, lo que significa que el servidor web realmente acepta`HEAD`Solicitudes, que es la configuración predeterminada para muchos servidores web. Entonces, intentemos interceptar el`reset`solicitar nuevamente, y esta vez use un`HEAD`Solicite ver cómo el servidor web lo maneja:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_HEAD_request.jpg)

Una vez que cambiamos`POST`a`HEAD`y reenviar la solicitud, veremos que ya no obtenemos un mensaje de inicio de sesión o un`401 Unauthorized`página y obtener una salida vacía en su lugar, como se esperaba con un`HEAD`pedido. Si volvemos al`File Manager`Aplicación web, veremos que todos los archivos se han eliminado, lo que significa que activamos con éxito el`Reset`funcionalidad sin tener acceso de administrador ni ninguna credencial:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_after_reset.jpg)

Intente probar otros métodos HTTP y vea cuáles pueden evitar con éxito el mensaje de autenticación.