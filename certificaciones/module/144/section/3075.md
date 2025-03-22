# FingerPrinting

Las huellas dactilares se centran en extraer detalles técnicos sobre las tecnologías que alimentan un sitio web o aplicación web. Similar a cómo una huella digital identifica de forma única a una persona, las firmas digitales de los servidores web, los sistemas operativos y los componentes de software pueden revelar información crítica sobre la infraestructura de un objetivo y las posibles debilidades de seguridad. Este conocimiento faculta a los atacantes adaptar ataques y explotar vulnerabilidades específicas para las tecnologías identificadas.

Las huellas dactilares sirven como piedra angular del reconocimiento web por varias razones:

- `Targeted Attacks`: Al conocer las tecnologías específicas en uso, los atacantes pueden centrar sus esfuerzos en las exploits y vulnerabilidades que se sabe que afectan esos sistemas. Esto aumenta significativamente las posibilidades de un compromiso exitoso.
- `Identifying Misconfigurations`: Las huellas dactilares pueden exponer un software desactualizado o obsoleto, configuración predeterminada u otras debilidades que podrían no ser evidentes a través de otros métodos de reconocimiento.
- `Prioritising Targets`: Cuando se enfrenta a múltiples objetivos potenciales, las huellas dactilares ayuda a priorizar los esfuerzos al identificar los sistemas con más probabilidades de ser vulnerables o tener información valiosa.
- `Building a Comprehensive Profile`: La combinación de datos de huellas digitales con otros hallazgos de reconocimiento crea una visión holística de la infraestructura del objetivo, ayudando a comprender su postura general de seguridad y posibles vectores de ataque.

# **Técnicas de huellas dactilares**

Existen varias técnicas utilizadas para el servidor web y la tecnología de huellas dactilares:

- `Banner Grabbing`: Banner Groating implica analizar los banners presentados por servidores web y otros servicios. Estos banners a menudo revelan el software del servidor, los números de versión y otros detalles.
- `Analysing HTTP Headers`: Los encabezados HTTP transmitidos con cada solicitud y respuesta de la página web contienen una gran cantidad de información. El`Server`el encabezado generalmente revela el software del servidor web, mientras que el`X-Powered-By`El encabezado puede revelar tecnologías adicionales como lenguajes o marcos de secuencias de comandos.
- `Probing for Specific Responses`: Enviar solicitudes especialmente elaboradas al objetivo puede provocar respuestas únicas que revelen tecnologías o versiones específicas. Por ejemplo, ciertos mensajes o comportamientos de error son características de servidores web o componentes de software particulares.
- `Analysing Page Content`: El contenido de una página web, incluida su estructura, scripts y otros elementos, a menudo puede proporcionar pistas sobre las tecnologías subyacentes. Puede haber un encabezado de derechos de autor que indique un software específico que se está utilizando, por ejemplo.

Existen una variedad de herramientas que automatizan el proceso de huellas dactilares, combinando varias técnicas para identificar servidores web, sistemas operativos, sistemas de gestión de contenido y otras tecnologías:

| **Herramienta** | **Descripción** | **Características** |
| --- | --- | --- |
| `Wappalyzer` | Extensión del navegador y servicio en línea para perfiles de tecnología de sitios web. | Identifica una amplia gama de tecnologías web, que incluyen CMSS, marcos, herramientas de análisis y más. |
| `BuiltWith` | Web Technology Profiler que proporciona informes detallados sobre la pila de tecnología de un sitio web. | Ofrece planes gratuitos y pagados con diferentes niveles de detalle. |
| `WhatWeb` | Herramienta de línea de comandos para el sitio web Huellas dactilares. | Utiliza una vasta base de datos de firmas para identificar varias tecnologías web. |
| `Nmap` | Escáner de red versátil que se puede utilizar para varias tareas de reconocimiento, incluidos el servicio y las huellas digitales del sistema operativo. | Se puede usar con Scripts (NSE) para realizar huellas dactilares más especializadas. |
| `Netcraft` | Ofrece una gama de servicios de seguridad web, que incluyen huellas dactilares y informes de seguridad del sitio web. | Proporciona informes detallados sobre la tecnología de un sitio web, el proveedor de alojamiento y la postura de seguridad. |
| `wafw00f` | Herramienta de línea de comandos diseñada específicamente para identificar firewalls de aplicaciones web (WAFS). | Ayuda a determinar si un WAF está presente y, de ser así, su tipo y configuración. |

# **Huellas dactilares inlanefreight.com**

Aplicemos nuestro conocimiento de huellas dactilares para descubrir el ADN digital de nuestro host especialmente diseñado,`inlanefreight.com`. Aprovecharemos técnicas manuales y automatizadas para recopilar información sobre su servidor web, tecnologías y vulnerabilidades potenciales.

### **Pancarta**

Nuestro primer paso es recopilar información directamente del servidor web. Podemos hacer esto usando el`curl`comando con el`-I`bandera (o`--head`) Para obtener solo los encabezados HTTP, no todo el contenido de la página.

Huellas dactilares

```
xnoxos@htb[/htb]$ curl -I inlanefreight.com
```

La salida incluirá el banner del servidor, revelando el software del servidor web y el número de versión:

Huellas dactilares

```
xnoxos@htb[/htb]$ curl -I inlanefreight.comHTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:07:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: https://inlanefreight.com/
Content-Type: text/html; charset=iso-8859-1

```

En este caso, vemos que`inlanefreight.com`se está ejecutando`Apache/2.4.41`, específicamente el`Ubuntu`versión. Esta información es nuestra primera pista, insinuando la pila de tecnología subyacente. También está tratando de redirigir a`https://inlanefreight.com/`Así que toma esos pancartas también

Huellas dactilares

```
xnoxos@htb[/htb]$ curl -I https://inlanefreight.comHTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:12:12 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
Content-Type: text/html; charset=UTF-8

```

Ahora tenemos un encabezado realmente interesante, el servidor está tratando de redirigirnos nuevamente, pero esta vez vemos que es`WordPress`que está haciendo la redirección a`https://www.inlanefreight.com/`

Huellas dactilares

```
xnoxos@htb[/htb]$ curl -I https://www.inlanefreight.comHTTP/1.1 200 OK
Date: Fri, 31 May 2024 12:12:26 GMT
Server: Apache/2.4.41 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8

```

Algunos encabezados más interesantes, incluido un camino interesante que contiene`wp-json`. El`wp-`El prefijo es común a WordPress.

### **WAFW00F**

`Web Application Firewalls` (`WAFs`) son soluciones de seguridad diseñadas para proteger las aplicaciones web de varios ataques. Antes de continuar con más huellas dactilares, es crucial determinar si`inlanefreight.com`Emplea un WAF, ya que podría interferir con nuestras sondas o potencialmente bloquear nuestras solicitudes.

Para detectar la presencia de un WAF, usaremos el`wafw00f`herramienta. Para instalar`wafw00f`, puedes usar pip3:

Huellas dactilares

```
xnoxos@htb[/htb]$ pip3 install git+https://github.com/EnableSecurity/wafw00f
```

Una vez que esté instalado, pase el dominio que desea verificar como argumento a la herramienta:

Huellas dactilares

```
xnoxos@htb[/htb]$ wafw00f inlanefreight.com                ______
               /      \
              (  W00f! )
               \  ____/
               ,,    __            404 Hack Not Found
           |`-.__   / /                      __     __
           /"  _/  /_/                       \ \   / /
          *===*    /                          \ \_/ /  405 Not Allowed
         /     )__//                           \   /
    /|  /     /---`                        403 Forbidden
    \\/`   \ |                                 / _ \
    `\    /_\\_              502 Bad Gateway  / / \ \  500 Internal Error
      `_____``-`                             /_/   \_\

                        ~ WAFW00F : v2.2.0 ~
        The Web Application Firewall Fingerprinting Toolkit

[*] Checking https://inlanefreight.com
[+] The site https://inlanefreight.com is behind Wordfence (Defiant) WAF.
[~] Number of requests: 2

```

El`wafw00f`escanear`inlanefreight.com`revela que el sitio web está protegido por el`Wordfence Web Application Firewall` (`WAF`), desarrollado por Defiant.

Esto significa que el sitio tiene una capa de seguridad adicional que podría bloquear o filtrar nuestros intentos de reconocimiento. En un escenario del mundo real, sería crucial tener esto en cuenta a medida que avanza con una investigación adicional, ya que puede necesitar adaptar técnicas para evitar o evadir los mecanismos de detección de la WAF.

### **Nikto**

`Nikto`es un poderoso escáner de servidor web de código abierto. Además de su función principal como herramienta de evaluación de vulnerabilidad,`Nikto's`Las capacidades de las peleas dactilares proporcionan información sobre la pila de tecnología de un sitio web.

`Nikto`está preinstalado en Pwnbox, pero si necesita instalarlo, puede ejecutar los siguientes comandos:

Huellas dactilares

```
xnoxos@htb[/htb]$ sudo apt update && sudo apt install -y perlxnoxos@htb[/htb]$ git clone https://github.com/sullo/niktoxnoxos@htb[/htb]$ cd nikto/programxnoxos@htb[/htb]$ chmod +x ./nikto.pl
```

Para escanear`inlanefreight.com`usando`Nikto`, solo ejecutando los módulos de huellas digitales, ejecute el siguiente comando:

Huellas dactilares

```
xnoxos@htb[/htb]$ nikto -h inlanefreight.com -Tuning b
```

El`-h`El indicador especifica el host de destino. El`-Tuning b`la bandera dice`Nikto`solo ejecutar los módulos de identificación de software.

`Nikto`Luego iniciará una serie de pruebas, intentando identificar software obsoleto, archivos o configuraciones inseguros, y otros riesgos de seguridad potenciales.

Huellas dactilares

```
xnoxos@htb[/htb]$ nikto -h inlanefreight.com -Tuning b- Nikto v2.5.0
---------------------------------------------------------------------------
+ Multiple IPs found: 134.209.24.248, 2a03:b0c0:1:e0::32c:b001
+ Target IP:          134.209.24.248
+ Target Hostname:    www.inlanefreight.com
+ Target Port:        443
---------------------------------------------------------------------------
+ SSL Info:        Subject:  /CN=inlanefreight.com
                   Altnames: inlanefreight.com, www.inlanefreight.com
                   Ciphers:  TLS_AES_256_GCM_SHA384
                   Issuer:   /C=US/O=Let's Encrypt/CN=R3
+ Start Time:         2024-05-31 13:35:54 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache/2.4.41 (Ubuntu)
+ /: Link header found with value: ARRAY(0x558e78790248). See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link
+ /: The site uses TLS and the Strict-Transport-Security HTTP header is not defined. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ /index.php?: Uncommon header 'x-redirect-by' found, with contents: WordPress.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /: The Content-Encoding header is set to "deflate" which may mean that the server is vulnerable to the BREACH attack. See: http://breachattack.com/
+ Apache/2.4.41 appears to be outdated (current is at least 2.4.59). Apache 2.2.34 is the EOL for the 2.x branch.
+ /: Web Server returns a valid response with junk HTTP methods which may cause false positives.
+ /license.txt: License file found may identify site software.
+ /: A Wordpress installation was found.
+ /wp-login.php?action=register: Cookie wordpress_test_cookie created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
+ /wp-login.php:X-Frame-Options header is deprecated and has been replaced with the Content-Security-Policy HTTP header with the frame-ancestors directive instead. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /wp-login.php: Wordpress login found.
+ 1316 requests: 0 error(s) and 12 item(s) reported on remote host
+ End Time:           2024-05-31 13:47:27 (GMT0) (693 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested

```

El escaneo de reconocimiento`inlanefreight.com`revela varios hallazgos clave:

- `IPs`: El sitio web se resuelve tanto para IPv4 (`134.209.24.248`) y ipv6 (`2a03:b0c0:1:e0::32c:b001`) direcciones.
- `Server Technology`: El sitio web se ejecuta en`Apache/2.4.41 (Ubuntu)`
- `WordPress Presence`: El escaneo identificó una instalación de WordPress, incluida la página de inicio de sesión (`/wp-login.php`). Esto sugiere que el sitio podría ser un objetivo potencial para las exploits comunes relacionadas con WordPress.
- `Information Disclosure`: La presencia de un`license.txt`El archivo podría revelar detalles adicionales sobre los componentes de software del sitio web.
- `Headers`: Se encontraron varios encabezados no estándar o inseguros, incluido un`Strict-Transport-Security`encabezado y una potencialmente insegura`x-redirect-by`encabezamiento.