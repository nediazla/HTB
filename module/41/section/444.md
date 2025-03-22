# HTTP Requests

En la sección anterior descubrimos que el `secret.js` La función principal es enviar un mensaje vacío. `POST` solicitar a `/serial.php`. En esta sección, intentaremos hacer lo mismo usando `cURL` enviar un `POST` solicitar a `/serial.php`. Para aprender más sobre `cURL` y solicitudes web, puedes consultar el [Solicitudes web](https://academy.hackthebox.com/module/details/35) módulo.

---

# **rizo**

`cURL`es una potente herramienta de línea de comandos utilizada en distribuciones de Linux, macOS e incluso en las últimas versiones de Windows PowerShell. Podemos solicitar cualquier sitio web simplemente proporcionando su URL y lo obtendremos en formato de texto, de la siguiente manera:

Solicitudes HTTP

```
xnoxos@htb[/htb]$ curl http://SERVER_IP:PORT/</html>
<!DOCTYPE html>

<head>
    <title>Secret Serial Generator</title>
    <style>
        *,
        html {
            margin: 0;
            padding: 0;
            border: 0;
...SNIP...
        <h1>Secret Serial Generator</h1>
        <p>This page generates secret serials!</p>
    </div>
</body>

</html>

```

Esto es lo mismo `HTML` Revisamos cuando verificamos el código fuente en la primera sección.

---

# **Solicitud de publicación**

para enviar un `POST` solicitud, debemos agregar el `-X POST` bandera a nuestro comando, y debería enviar un `POST` pedido:

Solicitudes HTTP

```
xnoxos@htb[/htb]$ curl -s http://SERVER_IP:PORT/ -X POST
```

Consejo: Agregamos el indicador "-s" para reducir la saturación de la respuesta con datos innecesarios.

Sin embargo, `POST` La solicitud generalmente contiene `POST` datos. Para enviar datos podemos utilizar el botón "`-d "param1=sample"`"marcar e incluir nuestros datos para cada parámetro, de la siguiente manera:

Solicitudes HTTP

```
xnoxos@htb[/htb]$ curl -s http://SERVER_IP:PORT/ -X POST -d "param1=sample"
```

Ahora que sabemos cómo utilizar `cURL` enviar basico `POST` solicitudes, en la siguiente sección, utilizaremos esto para replicar lo que `server.js` está haciendo para comprender mejor su propósito.