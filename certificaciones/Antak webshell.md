# Antak webshell

# **ASPX y un consejo de aprendizaje rápido**

Antes de sumergirnos en conceptos y ejercicios de Shell ASPX, debemos tomarnos el tiempo para cubrir un recurso de aprendizaje que pueda ayudar a reforzar la mayoría de los conceptos cubiertos aquí en la Academia HTB. Ocasionalmente puede ser un desafío visualizar un concepto utilizando un solo método de aprendizaje. Es bueno complementar la lectura para ver demostraciones y realizar una práctica como lo hemos estado haciendo hasta ahora. Los tutoriales de video pueden ser una forma increíble de aprender conceptos, además de que se pueden consumir casualmente (almorzar, acostarse en la cama, sentarse en el sofá, etc.). Un gran recurso para usar en el aprendizaje es`IPPSEC's`sitio de blog[Ippsec.rocks](https://ippsec.rocks/?#). El sitio es una poderosa herramienta de aprendizaje. Tomemos, por ejemplo, el concepto de conchas web. Podemos usar su sitio para escribir el concepto que queremos aprender, como ASPX.

![](https://academy.hackthebox.com/storage/modules/115/ippsecrocks.png)

Su sitio rastrea las descripciones de cada uno de los videos que ha publicado en YouTube y recomienda una marca de tiempo asociada con esa palabra clave. Cuando hacemos clic en uno de los enlaces, nos llevará a esa sección del video donde se demuestra este concepto. Es como un motor de búsqueda para aprender habilidades de piratería. Para obtener una buena comprensión básica de lo que es un shell web ASPX, veamos la breve porción de la demostración de IPPSEC de la caja retirada[Cereal](https://www.youtube.com/watch?v=04ZBIioD5pA&t=4677s). El enlace debe iniciarnos en la marca de 1 hora de 17 minutos. Observe la marca de 1 hora de 17 minutos hasta la marca de 1 hora de 20 minutos.

Notaremos que cargó el archivo a través de HTTP y luego navegó al archivo usando el navegador web. Esto le dio la capacidad de enviar comandos y recibir salida del sistema operativo Windows subyacente.

`How does aspx work?`

---

# **ASPX explicó**

`Active Server Page Extended` (`ASPX`) es un tipo de archivo/extensión escrita para[ASP.NET Framework de Microsoft](https://docs.microsoft.com/en-us/aspnet/overview). En un servidor web que ejecuta el marco ASP.NET, se pueden generar páginas de formulario web para que los usuarios ingresen datos. En el lado del servidor, la información se convertirá en HTML. Podemos aprovechar esto utilizando un shell web basado en ASPX para controlar el sistema operativo Windows subyacente. Testemos en esta primera mano utilizando el WebShell de Antak.

---

# **Antak webshell**

Antak es una shell web shell incorporada asp.net incluida en el[Proyecto Nishang](https://github.com/samratashok/nishang). Nishang es un conjunto de herramientas de PowerShell ofensivo que puede proporcionar opciones para cualquier parte de su Pentest. Dado que estamos enfocados en las aplicaciones web por el momento, mantengamos nuestros ojos seguidos`Antak`. Antak utiliza PowerShell para interactuar con el host, por lo que es excelente para adquirir un shell web en un servidor de Windows. La interfaz de usuario incluso tiene el tema de PowerShell. Es hora de bucear y experimentar con Antak.

---

# **Trabajando con Antak**

Los archivos antak se pueden encontrar en el`/usr/share/nishang/Antak-WebShell`directorio.

Antak webshell

```
xnoxos@htb[/htb]$ ls /usr/share/nishang/Antak-WebShellantak.aspx  Readme.md

```

Antak Web Shell funciona como una consola PowerShell. Sin embargo, ejecutará cada comando como un nuevo proceso. También puede ejecutar scripts en la memoria y codificar los comandos que envía. Como shell web, Antak es una herramienta bastante poderosa.

---

# **Manifestación Antak**

Ahora que entendemos qué es Antak y cómo funciona, pongámoslo a prueba con la misma aplicación web de la sección Laudanum. Si desea seguir con esta demostración, deberá agregar una entrada a su`/etc/hosts`Archivo en su VM de ataque o dentro de Pwnbox para el host que estamos atacando. Esa entrada debe leer:`<target ip> status.inlanefreight.local`. Una vez hecho esto, siempre que esté en la VPN o usando Pwnbox, también puede jugar y explorar esta demostración.

### **Mueva una copia para la modificación**

Antak webshell

```
xnoxos@htb[/htb]$ cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx
```

Asegúrese de establecer credenciales para acceder al shell web. Modificar`line 14`, agregando un usuario (flecha verde) y contraseña (flecha naranja). Esto entra en juego cuando navegas con tu shell web, al igual que Laudanum. Esto puede ayudar a hacer que sus operaciones sean más seguras asegurando que las personas al azar no puedan tropezar con el uso del shell. Puede ser prudente eliminar el arte y los comentarios ASCII del archivo. Estos elementos en una carga útil a menudo se firman y pueden alertar a los defensores/AV a lo que está haciendo.

### **Modificar el shell para su uso**

![](https://academy.hackthebox.com/storage/modules/115/antak-changes.png)

En aras de demostrar la herramienta, la estamos cargando al mismo portal de estado que utilizamos para Laudanum. Ese host era un host de Windows, por lo que nuestro shell debería funcionar bien con PowerShell. Cargue el archivo y luego navegue a la página para su uso. Le dará un mensaje de usuario y contraseña. Recuerde, con esta aplicación web, los archivos se almacenan en el`\\files\`directorio. Cuando navegas al`upload.aspx`Archivo, debería ver un mensaje como tenemos a continuación.

### **Éxito de la cáscara**

![](https://academy.hackthebox.com/storage/modules/115/antak-creds-prompt.png)

Como se ve en la siguiente imagen, se nos otorgará acceso si nuestras credenciales se ingresan correctamente.

![](https://academy.hackthebox.com/storage/modules/115/antak-success.png)

Ahora que tenemos acceso, podemos utilizar los comandos de PowerShell para navegar y tomar medidas contra el anfitrión. Podemos emitir comandos básicos desde la ventana Antak Shell, cargar y descargar archivos, codificar y ejecutar scripts, y mucho más (flecha verde a continuación). Esta es una excelente manera de utilizar una red web para ofrecernos una devolución de llamada a nuestra plataforma de comando y control. Podríamos cargar la carga útil a través de la función de carga o usar un PowerShell One-Liner para descargar y ejecutar el shell para nosotros. Si se siente inseguro por dónde comenzar, emita el comando`help`En la ventana rápida (flecha naranja) a continuación.

### **Emisión de comandos**

![](https://academy.hackthebox.com/storage/modules/115/antak-commands.png)