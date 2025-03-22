# WordPress - Discovery & Enumeration

[WordPress](https://wordpress.org/), lanzado en 2003, es un sistema de gestión de contenido (CMS) de código abierto que puede usarse para múltiples propósitos. A menudo se usa para alojar blogs y foros. WordPress es altamente personalizable y amigable con SEO, lo que lo hace popular entre las empresas. Sin embargo, su personalización y naturaleza extensible lo hacen propenso a las vulnerabilidades a través de temas y complementos de terceros. WordPress está escrito en PHP y generalmente se ejecuta en Apache con MySQL como backend.

Al momento de escribir, WordPress representa alrededor del 32.5% de todos los sitios en Internet y es el CMS más popular por cuota de mercado. Aquí hay algunos interesantes[hechos](https://hostingtribunal.com/blog/wordpress-statistics/)sobre WordPress.

- WordPress ofrece más de 50,000 complementos y más de 4,100 temas con licencia de GPL
- Se han lanzado 317 versiones separadas de WordPress desde su lanzamiento inicial
- Aproximadamente 661 nuevos sitios web de WordPress se construyen todos los días
- Los blogs de WordPress están escritos en más de 120 idiomas
- Un estudio mostró que aproximadamente el 8% de los hacks de WordPress ocurren debido a contraseñas débiles, mientras que el 60% se debió a una versión anticuada de WordPress
- Según WPSCAN, de casi 4,000 vulnerabilidades conocidas, el 54% son de complementos, el 31.5% son de WordPress Core y el 14.5% son de temas de WordPress.
- Algunas marcas importantes que usan WordPress incluyen The New York Times, eBay, Sony, Forbes, Disney, Facebook, Mercedes-Benz y muchos más

Como podemos ver en estas estadísticas, WordPress es extremadamente frecuente en Internet y presenta una vasta superficie de ataque. Se nos garantiza que nos encontremos con WordPress durante muchas de nuestras evaluaciones de pruebas de penetración externa, y debemos entender cómo funciona, cómo enumerarlo y las diversas formas en que se puede atacar.

El[Hacking WordPress](https://academy.hackthebox.com/course/preview/hacking-wordpress)El módulo en HTB Academy va muy en profundidad en la estructura y función de WordPress y formas en que se puede abusar.

Imaginemos que durante una prueba de penetración externa, nos encontramos con una compañía que alberga su sitio web principal basado en WordPress. Al igual que muchas otras aplicaciones, WordPress tiene archivos individuales que nos permiten identificar esa aplicación. Además, los archivos, la estructura de la carpeta, los nombres de archivos y la funcionalidad de cada script PHP se pueden usar para descubrir incluso la versión instalada de WordPress. En esta aplicación web, de forma predeterminada, los metadatos se agregan de forma predeterminada en el código fuente HTML de la página web, que a veces incluso ya contiene la versión. Por lo tanto, veamos qué posibilidades tenemos para encontrar información más detallada sobre WordPress.

---

# **Descubrimiento/huella**

Una forma rápida de identificar un sitio de WordPress es navegando al`/robots.txt`archivo. Un típico robots.txt en una instalación de WordPress puede parecerse:

WordPress - Descubrimiento y enumeración

```
User-agent: *
Disallow: /wp-admin/
Allow: /wp-admin/admin-ajax.php
Disallow: /wp-content/uploads/wpforms/

Sitemap: https://inlanefreight.local/wp-sitemap.xml

```

Aquí la presencia del`/wp-admin`y`/wp-content`Los directorios serían un sorteo muerto que estamos tratando con WordPress. Típicamente intentando navegar al`wp-admin`El directorio nos redirigirá al`wp-login.php`página. Este es el portal de inicio de sesión para el back-end de la instancia de WordPress.

![](https://academy.hackthebox.com/storage/modules/113/wp-login2.png)

WordPress almacena sus complementos en el`wp-content/plugins`directorio. Esta carpeta es útil para enumerar complementos vulnerables. Los temas se almacenan en el`wp-content/themes`directorio. Estos archivos deben enumerarse cuidadosamente, ya que pueden conducir a RCE.

Hay cinco tipos de usuarios en una instalación estándar de WordPress.

1. Administrador: este usuario tiene acceso a características administrativas en el sitio web. Esto incluye agregar y eliminar usuarios y publicaciones, así como editar el código fuente.
2. Editor: un editor puede publicar y administrar publicaciones, incluidas las publicaciones de otros usuarios.
3. Autor: Pueden publicar y administrar sus propias publicaciones.
4. Colaborador: estos usuarios pueden escribir y administrar sus propias publicaciones, pero no pueden publicarlas.
5. Suscriptor: estos son usuarios estándar que pueden navegar en publicaciones y editar sus perfiles.

Obtener acceso a un administrador suele ser suficiente para obtener la ejecución del código en el servidor. Los editores y autores pueden tener acceso a ciertos complementos vulnerables, que los usuarios normales no.

---

# **Enumeración**

Otra forma rápida de identificar un sitio de WordPress es mirando la fuente de la página. Ver la página con`cURL`y Greping para`WordPress`Puede ayudarnos a confirmar que WordPress está en uso y presenta el número de versión, que debemos tener en cuenta para más adelante. Podemos enumerar WordPress utilizando una variedad de tácticas manuales y automatizadas.

WordPress - Descubrimiento y enumeración

```
xnoxos@htb[/htb]$ curl -s http://blog.inlanefreight.local | grep WordPress<meta name="generator" content="WordPress 5.8" /

```

Examinar el sitio y leer la fuente de la página nos dará sugerencias al tema en uso, complementos instalados e incluso nombres de usuario si los nombres de los autores se publican con publicaciones. Deberíamos pasar algún tiempo navegando manualmente el sitio y mirando a través de la fuente de la página para cada página, Grepping para el`wp-content`directorio,`themes`y`plugin`, y comience a construir una lista de puntos de datos interesantes.

Mirando la fuente de la página, podemos ver que el[Gravedad comercial](https://wordpress.org/themes/business-gravity/)El tema está en uso. Podemos ir más allá e intentar hacer huellas digitales el número de versión del tema y buscar cualquier vulnerabilidad conocida que lo afecte.

WordPress - Descubrimiento y enumeración

```
xnoxos@htb[/htb]$ curl -s http://blog.inlanefreight.local/ | grep themes<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.local/wp-content/themes/business-gravity/assets/vendors/bootstrap/css/bootstrap.min.css' type='text/css' media='all' />

```

A continuación, echemos un vistazo a los complementos que podemos descubrir.

WordPress - Descubrimiento y enumeración

```
xnoxos@htb[/htb]$ curl -s http://blog.inlanefreight.local/ | grep plugins<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.8' id='subscriber-js-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.8' id='validation-engine-en-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.8' id='validation-engine-js'></script>
		<link rel='stylesheet' id='mm_frontend-css'  href='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/css/mm_frontend.css?ver=5.8' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/js/index.js?ver=5.4.2' id='contact-form-7-js'></script>

```

De la salida anterior, sabemos que el[Formulario de contacto 7](https://wordpress.org/plugins/contact-form-7/)y[mail-masta](https://wordpress.org/plugins/mail-masta/)Se instalan complementos. El siguiente paso sería enumerar las versiones.

Navegar por`http://blog.inlanefreight.local/wp-content/plugins/mail-masta/`nos muestra que el listado de directorio está habilitado y que un`readme.txt`El archivo está presente. Estos archivos a menudo son útiles para hacer huellas digitales. Desde el ReadMe, parece que se instala la versión 1.0.0 del complemento, que sufre de un[Inclusión de archivos locales](https://www.exploit-db.com/exploits/50226)Vulnerabilidad que se publicó en agosto de 2021.

Cavemos un poco más. Verificar la fuente de la página de otra página, podemos ver que el[wpdiscuz](https://wpdiscuz.com/)El complemento está instalado y parece ser la versión 7.0.4

WordPress - Descubrimiento y enumeración

```
xnoxos@htb[/htb]$ curl -s http://blog.inlanefreight.local/?p=1 | grep plugins<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<link rel='stylesheet' id='wpdiscuz-frontend-css-css'  href='http://blog.inlanefreight.local/wp-content/plugins/wpdiscuz/themes/default/style.css?ver=7.0.4' type='text/css' media='all' />

```

Se muestra una búsqueda rápida de esta versión del complemento[este](https://www.exploit-db.com/exploits/49967)Vulnerabilidad de ejecución de código remoto no autenticado de junio de 2021. Observaremos esto hacia abajo y seguiremos adelante. Es importante en esta etapa no saltar por delante de nosotros mismos y comenzar a explotar el primer defecto posible que vemos, ya que hay muchas otras vulnerabilidades potenciales y configuraciones erróneas posibles en WordPress que no queremos perder.

---

# **Usuarios enumeradores**

También podemos hacer una enumeración manual de los usuarios. Como se mencionó anteriormente, la página de inicio de sesión de WordPress predeterminada se puede encontrar en`/wp-login.php`.

Un nombre de usuario válido y una contraseña no válida resulta en el siguiente mensaje:

![](https://academy.hackthebox.com/storage/modules/113/valid_user.png)

Sin embargo, un nombre de usuario no válido devuelve que no se encontró al usuario.

![](https://academy.hackthebox.com/storage/modules/113/invalid_user.png)

Esto hace que WordPress sea vulnerable a la enumeración del nombre de usuario, que puede usarse para obtener una lista de posibles nombres de usuario.

Recapitulemos. En esta etapa, hemos reunido los siguientes puntos de datos:

- El sitio parece estar ejecutando WordPress Core versión 5.8
- El tema instalado es la gravedad comercial
- Los siguientes complementos están en uso: Formulario de contacto 7, Mail-Masta, WPDiscuz
- La versión WPDiscuz parece ser 7.0.4, que sufre de una vulnerabilidad de ejecución de código remoto no autenticado
- La versión Mail-Masta parece ser 1.0.0, que sufre de una vulnerabilidad de inclusión de archivos locales
- El sitio de WordPress es vulnerable a la enumeración del usuario y al usuario`admin`se confirma que es un usuario válido

Tomemos las cosas un paso más y validemos/agregemos a algunos de nuestros puntos de datos con algunos escaneos de enumeración automatizados del sitio de WordPress. Una vez que completemos esto, debemos tener suficiente información en la mano para comenzar a planificar y montar nuestros ataques.

---

# **Wpscan**

[Wpscan](https://github.com/wpscanteam/wpscan)es una herramienta automatizada de escáner y enumeración de WordPress. Determina si los diversos temas y complementos utilizados por un blog están desactualizados o vulnerables. Se instala de forma predeterminada en el sistema operativo Parrot, pero también se puede instalar manualmente con`gem`.

WordPress - Descubrimiento y enumeración

```
xnoxos@htb[/htb]$ sudo gem install wpscan
```

WPSCAN también puede obtener información de vulnerabilidad de fuentes externas. Podemos obtener un token API de[Wpvulndb](https://wpvulndb.com/), que usa WPSCan para escanear para POC e informes. El plan gratuito permite hasta 75 solicitudes por día. Para usar la base de datos WPVULNDB, simplemente cree una cuenta y copie el token API en la página de usuarios. Este token se puede suministrar a WPSCAN utilizando el`--api-token parameter`.

Mecanografía`wpscan -h`mencionará el menú de ayuda.

WordPress - Descubrimiento y enumeración

```
xnoxos@htb[/htb]$ wpscan -h_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.7
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

Usage: wpscan [options]
        --url URL                                 The URL of the blog to scan
                                                  Allowed Protocols: http, https
                                                  Default Protocol if none provided: http
                                                  This option is mandatory unless update or help or hh or version is/are supplied
    -h, --help                                    Display the simple help and exit
        --hh                                      Display the full help and exit
        --version                                 Display the version and exit
    -v, --verbose                                 Verbose mode
        --[no-]banner                             Whether or not to display the banner
                                                  Default: true
    -o, --output FILE                             Output to FILE
    -f, --format FORMAT                           Output results in the format supplied
                                                  Available choices: json, cli-no-colour, cli-no-color, cli
        --detection-mode MODE                     Default: mixed
                                                  Available choices: mixed, passive, aggressive

<SNIP>

```

El`--enumerate`FLAG se utiliza para enumerar varios componentes de la aplicación WordPress, como complementos, temas y usuarios. Por defecto, WPSCAN enumera complementos vulnerables, temas, usuarios, medios y copias de seguridad. Sin embargo, se pueden suministrar argumentos específicos para restringir la enumeración a componentes específicos. Por ejemplo, todos los complementos pueden enumerarse utilizando los argumentos`--enumerate ap`. Invocemos un escaneo de enumeración normal contra un sitio web de WordPress con el`--enumerate`bandera y pase un token API de WPVULNDB con el`--api-token`bandera.

WordPress - Descubrimiento y enumeración

```
xnoxos@htb[/htb]$ sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token dEOFB<SNIP>

<SNIP>

[+] URL: http://blog.inlanefreight.local/ [10.129.42.195]
[+] Started: Thu Sep 16 23:11:43 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://blog.inlanefreight.local/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] WordPress readme found: http://blog.inlanefreight.local/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://blog.inlanefreight.local/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] WordPress version 5.8 identified (Insecure, released on 2021-07-20).
 | Found By: Rss Generator (Passive Detection)
 |  - http://blog.inlanefreight.local/?feed=rss2, <generator>https://wordpress.org/?v=5.8</generator>
 |  - http://blog.inlanefreight.local/?feed=comments-rss2, <generator>https://wordpress.org/?v=5.8</generator>
 |
 | [!] 3 vulnerabilities identified:
 |
 | [!] Title: WordPress 5.4 to 5.8 - Data Exposure via REST API
 |     Fixed in: 5.8.1
 |     References:
 |      - https://wpvulndb.com/vulnerabilities/38dd7e87-9a22-48e2-bab1-dc79448ecdfb
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-39200
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/ca4765c62c65acb732b574a6761bf5fd84595706
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-m9hc-7v5q-x8q5
 |
 | [!] Title: WordPress 5.4 to 5.8 - Authenticated XSS in Block Editor
 |     Fixed in: 5.8.1
 |     References:
 |      - https://wpvulndb.com/vulnerabilities/5b754676-20f5-4478-8fd3-6bc383145811
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-39201
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-wh69-25hr-h94v
 |
 | [!] Title: WordPress 5.4 to 5.8 -  Lodash Library Update
 |     Fixed in: 5.8.1
 |     References:
 |      - https://wpvulndb.com/vulnerabilities/5d6789db-e320-494b-81bb-e678674f4199
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/lodash/lodash/wiki/Changelog
 |      - https://github.com/WordPress/wordpress-develop/commit/fb7ecd92acef6c813c1fde6d9d24a21e02340689

[+] WordPress theme in use: transport-gravity
 | Location: http://blog.inlanefreight.local/wp-content/themes/transport-gravity/
 | Latest Version: 1.0.1 (up to date)
 | Last Updated: 2020-08-02T00:00:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/themes/transport-gravity/readme.txt
 | [!] Directory listing is enabled
 | Style URL: http://blog.inlanefreight.local/wp-content/themes/transport-gravity/style.css
 | Style Name: Transport Gravity
 | Style URI: https://keonthemes.com/downloads/transport-gravity/
 | Description: Transport Gravity is an enhanced child theme of Business Gravity. Transport Gravity is made for tran...
 | Author: Keon Themes
 | Author URI: https://keonthemes.com/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.0.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://blog.inlanefreight.local/wp-content/themes/transport-gravity/style.css, Match: 'Version: 1.0.1'

[+] Enumerating Vulnerable Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] mail-masta
 | Location: http://blog.inlanefreight.local/wp-content/plugins/mail-masta/
 | Latest Version: 1.0 (up to date)
 | Last Updated: 2014-09-19T07:52:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | [!] 2 vulnerabilities identified:
 |
 | [!] Title: Mail Masta <= 1.0 - Unauthenticated Local File Inclusion (LFI)

<SNIP>

| [!] Title: Mail Masta 1.0 - Multiple SQL Injection

 <SNIP

 | Version: 1.0 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt

<SNIP>

[i] User(s) Identified:

[+] by:
									admin
 | Found By: Author Posts - Display Name (Passive Detection)

[+] admin
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] john
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

```

WPSCAN utiliza varios métodos pasivos y activos para determinar versiones y vulnerabilidades, como se muestra en el informe anterior. El número predeterminado de subprocesos utilizados es`5`. Sin embargo, este valor se puede cambiar usando el`-t`bandera.

Este escaneo nos ayudó a confirmar algunas de las cosas que descubrimos de la enumeración manual (WordPress Core versión 5.8 y listado de directorio habilitado), nos mostraron que el tema que identificamos no era exactamente correcto (la gravedad de transporte en uso es un tema infantil de la gravedad comercial), descubrió otro nombre de usuario (John) y mostró que la enumeración automatizada por sí misma a menudo no es suficiente (perdido el WPDISCUZ y Contact Formins). WPSCAN proporciona información sobre vulnerabilidades conocidas. La salida del informe también contiene URL a los POC, lo que nos permitiría explotar estas vulnerabilidades.

El enfoque que adoptamos en esta sección, combinando la enumeración manual y automatizada, se puede aplicar a casi cualquier aplicación que descubramos. Los escáneres son geniales y son muy útiles, pero no pueden reemplazar el toque humano y una mente curiosa. Hacer nuestras habilidades de enumeración puede diferenciarnos de la multitud como excelentes probadores de penetración.

---

# **Avanzar**

A partir de los datos que recopilamos manualmente y usando WPSCan, ahora sabemos lo siguiente:

- El sitio ejecuta WordPress Core versión 5.8, que sufre de algunas vulnerabilidades que no parecen interesantes en este momento
- El tema instalado es la gravedad de transporte
- Los siguientes complementos están en uso: Formulario de contacto 7, Mail-Masta, WPDiscuz
- La versión WPDiscuz es 7.0.4, que sufre de una vulnerabilidad de ejecución de código remoto no autenticado
- La versión Mail-Masta es 1.0.0, que sufre de una vulnerabilidad local de inclusión de archivos, así como a la inyección de SQL
- El sitio de WordPress es vulnerable a la enumeración del usuario y los usuarios`admin`y`john`se confirman que son usuarios válidos
- El listado de directorio está habilitado en todo el sitio, lo que puede conducir a la exposición de datos confidenciales
- XML-RPC está habilitado, que se puede aprovechar para realizar un ataque con contraseña de falsificación bruta contra la página de inicio de sesión utilizando WPSCAN,[Metasploit](https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login), etc.

Con esta información anotada, pasemos a las cosas divertidas: ¡atacar a WordPress!