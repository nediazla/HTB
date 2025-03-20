# Injecting Commands

Hasta ahora, hemos encontrado el`Host Checker`La aplicación web será potencialmente vulnerable a las inyecciones de comandos y discutió varios métodos de inyección que podemos utilizar para explotar la aplicación web. Entonces, comencemos nuestros intentos de inyección de comando con el operador de semi-colon (`;`).

---

# **Inyectando nuestro comando**

Podemos agregar un semi-colon después de nuestra IP de entrada`127.0.0.1`, y luego agregar nuestro comando (por ejemplo`whoami`), de modo que la carga útil final que usaremos es (`127.0.0.1; whoami`), y el comando final a ejecutar sería:

Código:intento

```bash
ping -c 1 127.0.0.1; whoami

```

Primero, intentemos ejecutar el comando anterior en nuestra VM Linux para asegurar que se ejecute:

Comandos de inyección

```
21y4d@htb[/htb]$ ping -c 1 127.0.0.1; whoamiPING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=1.03 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 1.034/1.034/1.034/0.000 ms
21y4d

```

Como podemos ver, el comando final se ejecuta con éxito, y obtenemos la salida de ambos comandos (como se menciona en la tabla anterior para

```
;
```

). Ahora, podemos intentar usar nuestra carga útil anterior en el

```
Host Checker
```

Aplicación web:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_injection.jpg)

Como podemos ver, la aplicación web rechazó nuestra entrada, ya que parece solo aceptar la entrada en un formato IP. Sin embargo, desde el aspecto del mensaje de error, parece que se origina en el front-end en lugar del back-end. Podemos verificar esto con el`Firefox Developer Tools`Al hacer clic`[CTRL + SHIFT + E]`Para mostrar la pestaña de red y luego hacer clic en el`Check`botón de nuevo:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_injection_network.jpg)

Como podemos ver, no se realizaron nuevas solicitudes de red cuando hicimos clic en el`Check`Botón, sin embargo, recibimos un mensaje de error. Esto indica que el`user input validation is happening on the front-end`.

Este parece ser un intento de evitar que enviemos cargas útiles maliciosas solo permitiendo la entrada del usuario en formato IP.`However, it is very common for developers only to perform input validation on the front-end while not validating or sanitizing the input on the back-end.`Esto ocurre por varias razones, como tener dos equipos diferentes trabajando en el front-end/back-end o confiar en la validación delantera para evitar cargas útiles maliciosas.

Sin embargo, como veremos, las validaciones frontales generalmente no son suficientes para evitar inyecciones, ya que pueden pasar por alto muy fácilmente enviando solicitudes HTTP personalizadas directamente al back-end.

---

# **Omitiendo la validación de front-end**

El método más fácil para personalizar las solicitudes HTTP que se envían al servidor de back-end es utilizar un proxy web que pueda interceptar las solicitudes HTTP enviadas por la aplicación. Para hacerlo, podemos comenzar`Burp Suite`o`ZAP`y configure Firefox para representar el tráfico a través de ellos. Luego, podemos habilitar la función Proxy Intercept, enviar una solicitud estándar de la aplicación web con cualquier IP (por ejemplo`127.0.0.1`), y envíe la solicitud HTTP interceptada a`repeater`Al hacer clic`[CTRL + R]`, y debemos tener la solicitud HTTP de personalización:

### **Solicitud de publicación de Burp**

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_repeater_1.jpg)

Ahora podemos personalizar nuestra solicitud HTTP y enviarla para ver cómo la maneja la aplicación web. Comenzaremos usando la misma carga útil anterior (`127.0.0.1; whoami`). También debemos encender URL nuestra carga útil para garantizar que se envíe como pretendemos. Podemos hacerlo seleccionando la carga útil y luego haciendo clic en`[CTRL + U]`. Finalmente, podemos hacer clic`Send`Para enviar nuestra solicitud HTTP:

### **Solicitud de publicación de Burp**

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_repeater_2.jpg)

Como podemos ver, la respuesta que obtuvimos esta vez contiene la salida del`ping`comando y el resultado del`whoami`dominio,`meaning that we successfully injected our new command`.