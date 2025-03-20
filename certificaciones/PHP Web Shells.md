# PHP Web Shells

Preprocesador de hipertexto o[Php](https://www.php.net/)es un lenguaje de secuencias de comandos de uso general de código abierto que generalmente se utiliza como parte de una pila web que alimenta un sitio web. En el momento de este escrito (octubre de 2021), PHP es el más popular`server-side programming language`. Según un[encuesta reciente](https://w3techs.com/technologies/details/pl-php)realizado por W3Techs, "PHP se utiliza por`78.6%`de todos los sitios web cuyo lenguaje de programación del lado del servidor conocemos ".

Consideremos un ejemplo práctico de completar la cuenta de usuario y los campos de contraseña en un formulario web de inicio de sesión.

### **Página de inicio de sesión de PHP**

![](https://academy.hackthebox.com/storage/modules/115/rconfig.png)

¿Recuerda el servidor RCONFIG de anteriormente en este módulo? Utiliza PHP. Podemos ver un`login.php`archivo. Entonces, cuando seleccionamos el botón de inicio de sesión después de completar el campo Nombre de usuario y contraseña, esa información se procesa del lado del servidor usando PHP. Saber que un servidor web está utilizando PHP nos da a Pentesters una pista de que podemos obtener un shell web basado en PHP en este sistema. Trabajemos a través de este concepto práctico.

---

# **Práctico con un shell web basado en PHP.**

Dado que PHP procesa el código y los comandos en el lado del servidor, podemos usar cargas útiles preescritas para obtener un shell a través del navegador o iniciar una sesión de shell inversa con nuestro cuadro de ataque. En este caso, aprovecharemos la vulnerabilidad en RCONFIG 3.9.6 para cargar manualmente un shell web PHP e interactuar con el host Linux subyacente. Además de toda la funcionalidad mencionada anteriormente, RCONFIG permite a los administradores agregar dispositivos de red y clasificarlos por el proveedor. Adelante e inicie sesión en RConfig con las credenciales predeterminadas (Admin: Admin), luego navegue a`Devices` > `Vendors`y hacer clic`Add Vendor`.

### **Pestaña de proveedores**

![](https://academy.hackthebox.com/storage/modules/115/vendors_tab.png)

Estaremos usando[Shell Whitewinterwolf's PHP Web Shell](https://github.com/WhiteWinterWolf/wwwolf-php-webshell). Podemos descargar esto o copiar y pegar el código fuente en un`.php`archivo. Tenga en cuenta que el tipo de archivo es significativo, ya que pronto presenciaremos. Nuestro objetivo es cargar el shell web PHP a través del logotipo del proveedor`browse`botón. Intentar hacer esto inicialmente fallará ya que RCONFIG está buscando el tipo de archivo. Solo permitirá cargar tipos de archivos de imagen (.png, .jpg, .gif, etc.). Sin embargo, podemos omitir esto utilizando`Burp Suite`.

Inicie BURP Suite, navegue hasta el menú de configuración de red del navegador y complete la configuración del proxy.`127.0.0.1`irá en el campo de la dirección IP y`8080`Irá al campo portuario para garantizar que todas las solicitudes pasen a través de BURP (recuerde que Burp actúa como proxy web).

### **Configuración proxy**

![](https://academy.hackthebox.com/storage/modules/115/proxy_settings.png)

Nuestro objetivo es cambiar el`content-type`Para evitar la restricción de tipo de archivo en la carga de archivos para ser "presentados" como el logotipo del proveedor para que podamos navegar a ese archivo y tener nuestro shell web.

---

# **Omitiendo la restricción del tipo de archivo**

Con BURP Open y la configuración del proxy de nuestro navegador web correctamente configurados, ahora podemos cargar el shell web PHP. Haga clic en el botón Examinar, navegue hasta donde sea que nuestro archivo .php se almacene en nuestro cuadro de ataque y seleccione abrir y`Save`(Es posible que necesitemos aceptar el certificado de Portswigger). Parecerá que la página web está colgando, pero eso es solo porque necesitamos decirle a Burp que reenvíe las solicitudes HTTP. Reenvíe las solicitudes hasta que vea la solicitud de publicación que contiene nuestra carga de archivo. Se verá así:

### **Solicitud postal**

![](https://academy.hackthebox.com/storage/modules/115/burp.png)

Como se menciona en una sección anterior, notará que algunas cargas útiles tienen comentarios del autor que explican el uso, proporcionan felicitaciones y enlaces a blogs personales. Esto puede regalarnos, por lo que no siempre es mejor dejar los comentarios en su lugar. Cambiaremos el tipo de contenido desde`application/x-php`a`image/gif`. Esto esencialmente "engañará" al servidor y nos permitirá cargar el archivo .php, sin pasar por la restricción de tipo de archivo. Una vez que hacemos esto, podemos seleccionar`Forward`dos veces, y se enviará el archivo. Podemos apagar el Interceptor Burp ahora y volver al navegador para ver los resultados.

### **Vendor agregado**

![](https://academy.hackthebox.com/storage/modules/115/added_vendor.png)

El mensaje: 'Se agregó el nuevo proveedor Netven a la base de datos', nos permite saber que nuestra carga de archivos fue exitosa. También podemos ver la entrada del proveedor de redes con el logotipo que muestra un papel rasgado. Esto significa que RCONFIG no reconoció el tipo de archivo como una imagen, por lo que no se realizó por defecto a esa imagen. Ahora podemos intentar usar nuestro shell web. Usando el navegador, navegue a este directorio en el servidor RCONFIG:

`/images/vendor/connect.php`

Esto ejecuta la carga útil y nos proporciona una sesión de shell no interactiva completamente en el navegador, lo que nos permite ejecutar comandos en el sistema operativo subyacente.

### **Éxito webshell**

![](https://academy.hackthebox.com/storage/modules/115/web_shell_now.png)

---

# **Consideraciones al tratar con shells web**

Al utilizar los shells web, considere los siguientes problemas potenciales que pueden surgir durante su proceso de prueba de penetración:

- Las aplicaciones web a veces eliminan automáticamente archivos después de un período predefinido
- Interactividad limitada con el sistema operativo en términos de navegar el sistema de archivos, descargar y cargar archivos, encadenar los comandos juntos puede no funcionar (por ejemplo.`whoami && hostname`), ralentizar el progreso, especialmente cuando se realiza la inestabilidad potencial de enumeración a través de un shell web no interactivo
- Mayor probabilidad de dejar una prueba de que tuvimos éxito en nuestro ataque

Dependiendo del tipo de compromiso (es decir, una evaluación evasiva de caja negra), es posible que necesitemos intentar no ser detectados y`cover our tracks`. A menudo estamos ayudando a nuestros clientes a probar sus capacidades para detectar una amenaza en vivo, por lo que debemos emular lo más posible los métodos que un atacante malicioso puede intentar, incluido el intento de operar sigilosamente. Esto ayudará a nuestro cliente y nos ahorrará a largo plazo desde que se descubra los archivos después de que termine un período de compromiso. En la mayoría de los casos, al intentar obtener una sesión de shell con un objetivo, sería prudente establecer un shell inverso y luego eliminar la carga útil ejecutada. Además, debemos documentar todos los métodos que intentamos, qué funcionó y qué no funcionó, e incluso los nombres de las cargas y archivos que intentamos usar. Podríamos incluir un hash de Sha1Sum o MD5 del nombre del archivo, la carga de ubicaciones en nuestros informes como prueba y proporcionar atribución.

`Now let's test our understanding with some challenge questions`.