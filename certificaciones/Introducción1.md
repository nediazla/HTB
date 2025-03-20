# Introducción

A medida que las aplicaciones web se vuelven más avanzadas y más comunes, también lo hacen las vulnerabilidades de las aplicaciones web. Entre los tipos más comunes de vulnerabilidades de aplicaciones web se encuentran[Scripting de sitios cruzados (XSS)](https://owasp.org/www-community/attacks/xss/)vulnerabilidades. Las vulnerabilidades de XSS aprovechan una falla en la desinfección de la entrada del usuario para "escribir" el código JavaScript en la página y ejecutarlo en el lado del cliente, lo que lleva a varios tipos de ataques.

---

# **Que es xss**

Una aplicación web típica funciona al recibir el código HTML del servidor de back-end y presentarla en el navegador de Internet del lado del cliente. Cuando una aplicación web vulnerable no desinfecta correctamente la entrada del usuario, un usuario malicioso puede inyectar un código JavaScript adicional en un campo de entrada (por ejemplo, comentarios/respuesta), por lo que una vez que otro usuario ve la misma página, sin saberlo, ejecutan el código de JavaScript malicioso.

Las vulnerabilidades de XSS se ejecutan únicamente en el lado del cliente y, por lo tanto, no afectan directamente al servidor de fondo. Solo pueden afectar al usuario ejecutando la vulnerabilidad. El impacto directo de las vulnerabilidades de XSS en el servidor de back-end puede ser relativamente bajo, pero se encuentran muy comúnmente en las aplicaciones web, por lo que esto equivale a un riesgo medio (`low impact + high probability = medium risk`), que siempre debemos intentar`reduce`riesgo al detectar, remediar y prevenir de manera proactiva este tipo de vulnerabilidades.

![](https://academy.hackthebox.com/storage/modules/103/xss_risk_chart_1.jpg)

---

# **Ataques XSS**

Las vulnerabilidades de XSS pueden facilitar una amplia gama de ataques, que puede ser cualquier cosa que pueda ejecutarse a través del código JavaScript del navegador. Un ejemplo básico de un ataque XSS es hacer que el usuario objetivo envíe involuntariamente su cookie de sesión al servidor web del atacante. Otro ejemplo es que el navegador del objetivo ejecute llamadas API que conducen a una acción maliciosa, como cambiar la contraseña del usuario a una contraseña de la elección del atacante. Hay muchos otros tipos de ataques XSS, desde la minería de bitcoin hasta la visualización de anuncios.

A medida que los ataques XSS ejecutan el código JavaScript dentro del navegador, se limitan al motor JS del navegador (es decir, V8 en Chrome). No pueden ejecutar el código JavaScript de todo el sistema para hacer algo como la ejecución del código a nivel del sistema. En los navegadores modernos, también se limitan al mismo dominio del sitio web vulnerable. Sin embargo, poder ejecutar JavaScript en el navegador de un usuario aún puede conducir a una amplia variedad de ataques, como se mencionó anteriormente. Además de esto, si un investigador calificado identifica una vulnerabilidad binaria en un navegador web (por ejemplo, un desbordamiento de montón en Chrome), puede utilizar una vulnerabilidad XSS para ejecutar una exploit de JavaScript en el navegador del objetivo, que eventualmente se separa del Sandbox del navegador y se ejecuta en el código en la máquina del usuario.

Las vulnerabilidades de XSS se pueden encontrar en casi todas las aplicaciones web modernas y se han explotado activamente durante las últimas dos décadas. Un ejemplo de XSS conocido es el[Samy Worm](https://en.wikipedia.org/wiki/Samy_(computer_worm)), que era un gusano basado en el navegador que explotó una vulnerabilidad XSS almacenada en el sitio web de redes sociales Myspace en 2005. Ejecutó al ver una página web infectada publicando un mensaje en la página de Myspace de la víctima que decía: "Samy es mi héroe". El mensaje en sí también contenía la misma carga útil de JavaScript para volver a publicar el mismo mensaje cuando otros lo vean. En un solo día, más de un millón de usuarios de MySpace recibieron este mensaje en sus páginas. A pesar de que esta carga útil específica no hizo ningún daño real, la vulnerabilidad podría haberse utilizado para fines mucho más nefastos, como robar la información de la tarjeta de crédito de los usuarios, instalar registradores clave en sus navegadores o incluso explotar una vulnerabilidad binaria en los navegadores web de los usuarios (que era más común en los navegadores web en ese momento).

En 2014, un investigador de seguridad identificó accidentalmente un[Vulnerabilidad XSS](https://blog.sucuri.net/2014/06/serious-cross-site-scripting-vulnerability-in-tweetdeck-twitter.html)En el tablero TweetDeck Twitter. Esta vulnerabilidad fue explotada para crear un[Tweet de auto-retrelo](https://twitter.com/derGeruhn/status/476764918763749376)en Twitter, que llevó al tweet a ser retuiteado más de 38,000 veces en menos de dos minutos. Finalmente, obligó a Twitter a[Apague temporalmente TweetDeck](https://www.theguardian.com/technology/2014/jun/11/twitter-tweetdeck-xss-flaw-users-vulnerable)Mientras reparaban la vulnerabilidad.

Hasta el día de hoy, incluso las aplicaciones web más destacadas tienen vulnerabilidades XSS que pueden ser explotadas. Incluso la página del motor de búsqueda de Google tenía múltiples vulnerabilidades XSS en su barra de búsqueda, la más reciente de las cuales estaba en[2019](https://www.acunetix.com/blog/web-security-zone/mutation-xss-in-google-search/)Cuando se encontró una vulnerabilidad XSS en la biblioteca XML. Además, el servidor Apache, el servidor web más utilizado en Internet, una vez informó un[Vulnerabilidad XSS](https://blogs.apache.org/infra/entry/apache_org_04_09_2010)Eso se estaba explotando activamente para robar contraseñas de los usuarios de ciertas compañías. Todo esto nos dice que las vulnerabilidades de XSS deben tomarse en serio, y se debe dedicar una buena cantidad de esfuerzo a detectarlas y prevenirlas.

---

# **Tipos de XSS**

Hay tres tipos principales de vulnerabilidades XSS:

| **Tipo** | **Descripción** |
| --- | --- |
| `Stored (Persistent) XSS` | El tipo más crítico de XSS, que ocurre cuando la entrada del usuario se almacena en la base de datos de back-end y luego se muestra al recuperar (por ejemplo, publicaciones o comentarios) |
| `Reflected (Non-Persistent) XSS` | Ocurre cuando la entrada del usuario se muestra en la página después de ser procesada por el servidor de backend, pero sin almacenarse (por ejemplo, resultado de búsqueda o mensaje de error) |
| `DOM-based XSS` | Otro tipo XSS no persistente que ocurre cuando la entrada del usuario se muestra directamente en el navegador y se procesa por completo en el lado del cliente, sin alcanzar el servidor de fondo (por ejemplo, a través de parámetros HTTP o etiquetas HTTP del lado del cliente) |

Cubriremos cada uno de estos tipos en las próximas secciones y trabajaremos a través de ejercicios para ver cómo ocurre cada uno de ellos, y luego también veremos cómo cada uno de ellos se puede utilizar en los ataques.