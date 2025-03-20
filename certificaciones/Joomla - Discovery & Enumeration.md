# Joomla - Discovery & Enumeration

[Joomla](https://www.joomla.org/), lanzado en agosto de 2005, es otro CMS gratuito y de código abierto utilizados para foros de discusión, galerías de fotos, comercio electrónico, comunidades basadas en usuarios y más. Está escrito en PHP y usa MySQL en el backend. Al igual que WordPress, Joomla se puede mejorar con más de 7,000 extensiones y más de 1,000 plantillas. Hay hasta 2.5 millones de sitios en Internet con Joomla. Aquí hay algunos interesantes[estadística](https://websitebuilder.org/blog/joomla-statistics/)Sobre Joomla:

- Joomla representa el 3.5% de la cuota de mercado de CMS
- Joomla es 100% libre y significa "todos juntos" en Swahili (ortografía fonética de "Jumla")
- La comunidad de Joomla tiene cerca de 700,000 en sus foros en línea
- Joomla impulsa el 3% de todos los sitios web en Internet, casi 25,000 de los 1 millón de sitios principales en todo el mundo (solo el 10% del alcance de WordPress)
- Algunas organizaciones notables que usan Joomla incluyen eBay, Yamaha, la Universidad de Harvard y el gobierno del Reino Unido
- Con los años, 770 desarrolladores diferentes han contribuido a Joomla

Joomla recoge un poco de anónimo[estadísticas de uso](https://developer.joomla.org/about/stats.html)tales como el desglose de las versiones de Joomla, PHP y la base de datos y los sistemas operativos del servidor en uso en las instalaciones de Joomla. Estos datos se pueden consultar a través de su público[API](https://developer.joomla.org/about/stats/api.html).

¡Consultando esta API, podemos ver más de 2.7 millones de instalaciones de Joomla!

```
[!bash!]$ curl -s https://developer.joomla.org/stats/cms_version | python3 -m json.tool{
    "data": {
        "cms_version": {
            "3.0": 0,
            "3.1": 0,
            "3.10": 3.49,
            "3.2": 0.01,
            "3.3": 0.02,
            "3.4": 0.05,
            "3.5": 13,
            "3.6": 24.29,
            "3.7": 8.5,
            "3.8": 18.84,
            "3.9": 30.28,
            "4.0": 1.52,
            "4.1": 0
        },
        "total": 2776276
    }
}

```

---

# **Descubrimiento/huella**

Supongamos que nos encontramos con un sitio de comercio electrónico durante una prueba de penetración externa. A primera vista, no estamos exactamente seguros de lo que se está ejecutando, pero no parece estar completamente personalizado. Si podemos hacer huellas digitales en qué se está ejecutando el sitio, podemos descubrir vulnerabilidades o mal configuraciones. Según la información limitada, suponemos que el sitio está ejecutando Joomla, pero debemos confirmar ese hecho y luego descubrir el número de versión y otra información, como temas y complementos instalados.

A menudo podemos hacer huellas digitales a Joomla mirando la fuente de la página, lo que nos dice que estamos tratando con un sitio de Joomla.

```
[!bash!]$ curl -s http://dev.inlanefreight.local/ | grep Joomla	<meta name="generator" content="Joomla! - Open Source Content Management" />

<SNIP>

```

El`robots.txt`El archivo para un sitio de Joomla a menudo se verá así:

```
# If the Joomla site is installed within a folder# eg www.example.com/joomla/ then the robots.txt file# MUST be moved to the site root# eg www.example.com/robots.txt# AND the joomla folder name MUST be prefixed to all of the# paths.# eg the Disallow rule for the /administrator/ folder MUST# be changed to read# Disallow: /joomla/administrator/#
# For more information about the robots.txt standard, see:# https://www.robotstxt.org/orig.htmlUser-agent: *
Disallow: /administrator/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/

```

También a menudo podemos ver el revelador Joomla Favicon (pero no siempre). Podemos hacer huellas digitales de la versión de Joomla si la`README.txt`El archivo está presente.

```
[!bash!]$ curl -s http://dev.inlanefreight.local/README.txt | head -n 51- What is this?
	* This is a Joomla! installation/upgrade package to version 3.x
	* Joomla! Official site: https://www.joomla.org
	* Joomla! 3.9 version history - https://docs.joomla.org/Special:MyLanguage/Joomla_3.9_version_history
	* Detailed changes in the Changelog: https://github.com/joomla/joomla-cms/commits/staging

```

En ciertas instalaciones de Joomla, es posible que podamos hacer huellas digitales de la versión de los archivos JavaScript en el`media/system/js/`directorio o navegando a`administrator/manifests/files/joomla.xml`.

```
[!bash!]$ curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml | xmllint --format -<?xml version="1.0" encoding="UTF-8"?>
<extension version="3.6" type="file" method="upgrade">
  <name>files_joomla</name>
  <author>Joomla! Project</author>
  <authorEmail>admin@joomla.org</authorEmail>
  <authorUrl>www.joomla.org</authorUrl>
  <copyright>(C) 2005 - 2019 Open Source Matters. All rights reserved</copyright>
  <license>GNU General Public License version 2 or later; see LICENSE.txt</license>
  <version>3.9.4</version>
  <creationDate>March 2019</creationDate>

 <SNIP>

```

El`cache.xml`El archivo puede ayudar a darnos la versión aproximada. Se encuentra en`plugins/system/cache/cache.xml`.

---

# **Enumeración**

Probemos[goteo](https://github.com/droope/droopescan), un escáner basado en complementos que funciona para Silverstripe, WordPress y Drupal con funcionalidad limitada para Joomla y Moodle.

Podemos clonar el repositorio de git e instalarlo manualmente o instalar a través de`pip`.

```
[!bash!]$ sudo pip3 install droopescanCollecting droopescan
  Downloading droopescan-1.45.1-py2.py3-none-any.whl (514 kB)
     |████████████████████████████████| 514 kB 5.8 MB/s

<SNIP>

```

Una vez que se completa la instalación, podemos confirmar que la herramienta está funcionando ejecutando`droopescan -h`.

```
[!bash!]$ droopescan -husage: droopescan (sub-commands ...) [options ...] {arguments ...}

    |
 ___| ___  ___  ___  ___  ___  ___  ___  ___  ___
|   )|   )|   )|   )|   )|___)|___ |    |   )|   )
|__/ |    |__/ |__/ |__/ |__   __/ |__  |__/||  /
                    |
=================================================

commands:

  scan
    cms scanning functionality.

  stats
    shows scanner status & capabilities.

optional arguments:
  -h, --help  show this help message and exit
  --debug     toggle debug output
  --quiet     suppress all output

Example invocations:
  droopescan scan drupal -u URL_HERE
  droopescan scan silverstripe -u URL_HERE

More info:
  droopescan scan --help

Please see the README file for information regarding proxies.

```

Podemos acceder a un menú de ayuda más detallado escribiendo`droopescan scan --help`.

Ejecutemos un escaneo y veamos qué aparece.

```
[!bash!]$ droopescan scan joomla --url http://dev.inlanefreight.local/[+] Possible version(s):
    3.8.10
    3.8.11
    3.8.11-rc
    3.8.12
    3.8.12-rc
    3.8.13
    3.8.7
    3.8.7-rc
    3.8.8
    3.8.8-rc
    3.8.9
    3.8.9-rc

[+] Possible interesting urls found:
    Detailed version information. - http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml
    Login page. - http://dev.inlanefreight.local/administrator/
    License file. - http://dev.inlanefreight.local/LICENSE.txt
    Version attribute contains approx version - http://dev.inlanefreight.local/plugins/system/cache/cache.xml

[+] Scan finished (0:00:01.523369 elapsed)

```

Como podemos ver, no apareció mucha información aparte del posible número de versión. También podemos probar[Joomlascano](https://github.com/drego85/JoomlaScan), que es una herramienta de Python inspirada en el ahora desaparecido OWASP[joomscan](https://github.com/OWASP/joomscan)herramienta.`JoomlaScan`está un poco desactualizado y requiere que Python2.7 se ejecute. Podemos ejecutarlo primero asegurándonos de que se instalen algunas dependencias.

```
[!bash!]$ sudo python2.7 -m pip install urllib3[!bash!]$ sudo python2.7 -m pip install certifi[!bash!]$ sudo python2.7 -m pip install bs4
```

Si bien es un poco desactualizado, puede ser útil en nuestra enumeración. Ejecutemos un escaneo.

```
[!bash!]$ python2.7 joomlascan.py -u http://dev.inlanefreight.local-------------------------------------------
      	     Joomla Scan
   Usage: python joomlascan.py <target>
    Version 0.5beta - Database Entries 1233
         created by Andrea Draghetti
-------------------------------------------
Robots file found: 	 	 > http://dev.inlanefreight.local/robots.txt
No Error Log found

Start scan...with 10 concurrent threads!
Component found: com_actionlogs	 > http://dev.inlanefreight.local/index.php?option=com_actionlogs
	 On the administrator components
Component found: com_admin	 > http://dev.inlanefreight.local/index.php?option=com_admin
	 On the administrator components
Component found: com_ajax	 > http://dev.inlanefreight.local/index.php?option=com_ajax
	 But possibly it is not active or protected
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_actionlogs/actionlogs.xml
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_admin/admin.xml
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_ajax/ajax.xml
	 Explorable Directory 	 > http://dev.inlanefreight.local/components/com_actionlogs/
	 Explorable Directory 	 > http://dev.inlanefreight.local/administrator/components/com_actionlogs/
	 Explorable Directory 	 > http://dev.inlanefreight.local/components/com_admin/
	 Explorable Directory 	 > http://dev.inlanefreight.local/administrator/components/com_admin/
Component found: com_banners	 > http://dev.inlanefreight.local/index.php?option=com_banners
	 But possibly it is not active or protected
	 Explorable Directory 	 > http://dev.inlanefreight.local/components/com_ajax/
	 Explorable Directory 	 > http://dev.inlanefreight.local/administrator/components/com_ajax/
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_banners/banners.xml

<SNIP>

```

Si bien no es tan valioso como Droopescan, esta herramienta puede ayudarnos a encontrar directorios y archivos accesibles y puede ayudar con las huellas digitales de extensiones instaladas. En este punto, sabemos que estamos tratando con Joomla`3.9.4`. El portal de inicio de sesión del administrador se encuentra en`http://dev.inlanefreight.local/administrator/index.php`. Los intentos de enumeración del usuario devuelven un mensaje de error genérico.

```
Warning
Username and password do not match or you do not have an account yet.

```

La cuenta de administrador predeterminada en las instalaciones de Joomla es`admin`, pero la contraseña se establece en el momento de la instalación, por lo que la única forma en que podemos esperar ingresar al back-end de administrador es si la cuenta está configurada con una contraseña muy débil/común y podemos entrar con algunas conjeturas o forceduras brutas. Podemos usar esto[guion](https://github.com/ajnik/joomla-bruteforce)intentar bruto forzar el inicio de sesión.

```
[!bash!]$ sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
admin:admin

```

Y recibimos un éxito con las credenciales`admin:admin`. ¡Alguien no ha estado siguiendo las mejores prácticas!