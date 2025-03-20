# XSS almacenado

Antes de aprender cómo descubrir las vulnerabilidades de XSS y utilizarlas para varios ataques, primero debemos comprender los diferentes tipos de vulnerabilidades de XSS y sus diferencias para saber cuál usar en cada tipo de ataque.

El primer y más crítico tipo de vulnerabilidad XSS es`Stored XSS`o`Persistent XSS`. Si nuestra carga útil XSS inyectada se almacena en la base de datos de back-end y se recupera al visitar la página, esto significa que nuestro ataque XSS es persistente y puede afectar a cualquier usuario que visite la página.

Esto hace que este tipo de XSS sea el más crítico, ya que afecta a un público mucho más amplio ya que cualquier usuario que visita la página sería víctima de este ataque. Además, el XSS almacenado puede no ser fácilmente extraíble, y la carga útil puede necesitar eliminar la base de datos de back-end.

Podemos iniciar el servidor a continuación para ver y practicar un ejemplo de XSS almacenado. Como podemos ver, la página web es una simple`To-Do List`aplicación a la que podemos agregar elementos. Podemos intentar escribir`test`y presionando Intro/Regreso para agregar un nuevo elemento y vea cómo la página lo maneja:

![](https://academy.hackthebox.com/storage/modules/103/xss_stored_xss.jpg)

Como podemos ver, nuestra entrada se mostró en la página. Si no se aplicó desinfectación o filtrado a nuestra entrada, la página podría ser vulnerable a XSS.

---

# **XSS Prueba de cargas útiles**

Podemos probar si la página es vulnerable a XSS con la siguiente carga útil de XSS básica:

Código:html

```html
<script>alert(window.origin)</script>
```

Utilizamos esta carga útil, ya que es un método muy fácil de hacer saber cuándo se ha ejecutado con éxito nuestra carga útil XSS. Supongamos que la página permite cualquier entrada y no realiza desinfectación en ella. En ese caso, la alerta debe aparecer con la URL de la página en la que se está ejecutando, directamente después de ingresar nuestra carga útil o cuando actualizamos la página:

![](https://academy.hackthebox.com/storage/modules/103/xss_stored_xss_alert.jpg)

Como podemos ver, realmente obtuvimos la alerta, lo que significa que la página es vulnerable a XSS, ya que nuestra carga útil se ejecutó con éxito. Podemos confirmar esto más adelante mirando la fuente de la página haciendo clic [`CTRL+U`] o haciendo clic derecho y seleccionando`View Page Source`, y deberíamos ver nuestra carga útil en la fuente de la página:

Código:html

```html
<div></div><ul class="list-unstyled" id="todo"><ul><script>alert(window.origin)</script></ul></ul>
```

**Consejo:**Muchas aplicaciones web modernas utilizan iFrames de dominio cruzado para manejar la entrada del usuario, de modo que incluso si el formulario web es vulnerable a XSS, no sería una vulnerabilidad en la aplicación web principal. Por eso estamos mostrando el valor de`window.origin`en el cuadro de alerta, en lugar de un valor estático como`1`. En este caso, el cuadro de alerta revelaría la URL en la que se está ejecutando y confirmará qué forma es la vulnerable, en caso de que se usara un iframe.

Como algunos navegadores modernos pueden bloquear el`alert()`JavaScript Funcion En ubicaciones específicas, puede ser útil conocer algunas otras cargas útiles de XSS básicas para verificar la existencia de XSS. Una de esas XSS Payload es`<plaintext>`, que dejará de representar el código HTML que lo viene y lo mostrará como texto sin formato. Otra carga útil fácil de hacer es`<script>print()</script>`Eso aparecerá el diálogo de impresión del navegador, que es poco probable que sea bloqueado por cualquier navegador. Intente usar estas cargas útiles para ver cómo funciona cada uno. Puede usar el botón RESET para eliminar las cargas útiles actuales.

Para ver si la carga útil es persistente y almacenada en el back-end, podemos actualizar la página y ver si recibimos la alerta nuevamente. Si lo hacemos, veríamos que seguimos recibiendo la alerta incluso a lo largo de las actualizaciones de la página, confirmando que esto es realmente un`Stored/Persistent XSS`vulnerabilidad. Esto no es exclusivo de nosotros, ya que cualquier usuario que visita la página activará la carga útil XSS y obtendrá la misma alerta.