# Application Discovery & Enumeration

Para administrar efectivamente su red, una organización debe mantener (y actualizar continuamente) un inventario de activos que incluya todos los dispositivos conectados a la red (servidores, estaciones de trabajo, electrodomésticos de red, etc.), software instalado y aplicaciones en uso en todo el entorno. Si una organización no está segura de lo que está presente en su red, ¿cómo sabrá qué proteger y qué posibles agujeros existen? La organización debe saber si las aplicaciones están instaladas localmente o alojadas por un tercero, su nivel de parche actual, si están al final de la vida al final de la vida, pueden detectar cualquier aplicación deshonesta en la red (o "sombra de TI"), y tienen suficiente visibilidad en cada aplicación para garantizar que estén adecuadamente aseguradas con contraseñas strong (no defensoras) e ideal, la autenticación multifactor está en la configuración. Ciertas aplicaciones tienen portales administrativos que pueden restringirse solo a ser accesibles desde direcciones IP específicas o el propio host (localhost).

La realidad es que muchas organizaciones no saben todo en su red, y algunas organizaciones tienen muy poca visibilidad, y podemos ayudarlas con esto. La enumeración que realizamos puede ser muy beneficiosa para nuestros clientes para ayudarlos a mejorar o comenzar a construir un inventario de activos. Es muy probable que identifiquemos aplicaciones que se han olvidado, versiones de demostración de software que tal vez han tenido su licencia de prueba expirada y convertida a una versión que no requiere autenticación (en el caso de Splunk), aplicaciones con credenciales predeterminadas/débiles, aplicaciones no autorizadas/mal configuradas y aplicaciones que sufren de vulnerabilidades públicas. Podemos proporcionar estos datos a nuestros clientes como una combinación de los hallazgos en nuestros informes (es decir, una aplicación con credenciales predeterminadas`admin:admin`, como apéndices, como una lista de servicios identificados mapeados para hosts o datos de escaneo suplementario). Incluso podemos dar un paso más y educar a nuestros clientes sobre algunas de las herramientas que usamos diariamente para que puedan comenzar a realizar una reconocimiento periódica y proactiva de sus redes y encontrar brechas antes de los probadores de penetración, o peor, los atacantes, encontrarlas primero.

Como probadores de penetración, necesitamos tener fuertes habilidades de enumeración y poder obtener el "laico de la tierra" en cualquier red que comience con muy poca o ninguna información (descubrimiento de caja negra o simplemente un conjunto de rangos de CIDR). Por lo general, cuando nos conectamos a una red, comenzaremos con un barrido de ping para identificar "hosts en vivo". A partir de ahí, generalmente comenzaremos el escaneo de puertos dirigido y, eventualmente, un escaneo de puertos más profundo para identificar los servicios de ejecución. En una red con cientos o miles de hosts, estos datos de enumeración pueden volverse difíciles de manejar. Supongamos que realizamos una exploración de puertos NMAP para identificar servicios web comunes como:

### **Nmap - descubrimiento web**

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list
```

Podemos encontrar una enorme cantidad de hosts con servicios que se ejecutan solo en los puertos 80 y 443. ¿Qué hacemos con estos datos? Tamizar a través de los datos de enumeración a mano en un entorno grande sería demasiado lento, especialmente porque la mayoría de las evaluaciones están bajo restricciones de tiempo estrictas. Navegar a cada puerto IP/HostName + también sería muy ineficiente.

Por suerte para nosotros, existen varias herramientas excelentes que pueden ayudar enormemente en este proceso. Dos herramientas fenomenales que cada probador debe tener en su arsenal son[Testigo ocular](https://github.com/FortyNorthSecurity/EyeWitness)y[Aguatano](https://github.com/michenriksen/aquatone). Ambas herramientas se pueden alimentar con la salida RAW NMAP XML de escaneo (Aquatone también puede tomar Masscan XML; el testigo ocular puede tomar la salida de Nessus XML) y usarse para inspeccionar rápidamente todos los hosts ejecutando aplicaciones web y tomar capturas de pantalla de cada una. Las capturas de pantalla se ensamblan en un informe que podemos trabajar en el navegador web para evaluar la superficie del ataque web.

Estas capturas de pantalla pueden ayudarnos a reducir potencialmente 100 de hosts y construir una lista más específica de aplicaciones que deberíamos pasar más tiempo enumerando y atacando. Estas herramientas están disponibles tanto para Windows como para Linux, por lo que podemos utilizarlas en lo que sea que elijamos para nuestro cuadro de ataque en un entorno determinado. Pasemos a través de algunos ejemplos de cada uno para crear un inventario de aplicaciones presentes en el objetivo`INLANEFREIGHT.LOCAL`dominio.

---

# **Organizarse**

Aunque cubriremos la notificación, los informes y la documentación en un módulo separado, vale la pena aprovechar la oportunidad para seleccionar una aplicación de notificación si no lo hemos hecho y comenzar a configurarlo para registrar mejor los datos que estamos recopilando en esta fase. El módulo[Empezando](https://academy.hackthebox.com/course/preview/getting-started)Discute varias aplicaciones de notificación. Si no ha elegido uno en este momento, sería un excelente momento para comenzar. Herramientas como OneNote, Evernote, noción, Cherrytree, etc., son buenas opciones, y se trata de preferencia personal. Independientemente de la herramienta que elija, debemos trabajar en nuestra metodología de notificación en este momento y crear plantillas que podemos usar en nuestra herramienta de elección configurada para cada tipo de evaluación.

Para esta sección, desglosaría el`Enumeration & Discovery`Sección de mi cuaderno en un separado`Application Discovery`sección. Aquí crearía subsecciones para el alcance, los escaneos (NMAP, Nessus, Masscan, etc.), capturas de pantalla de aplicación y hosts interesantes/notables para profundizar más tarde. Es importante para sellar la hora y la fecha en cada escaneo que realizamos y guardar toda la salida y la sintaxis de escaneo exacta que se realizó y los hosts dirigidos. Esto puede ser útil más adelante si el cliente tiene alguna pregunta sobre la actividad que vio durante la evaluación. Estar organizado desde el principio y mantener registros y notas detallados nos ayudará enormemente con el informe final. Por lo general, configuré el esqueleto del informe al comienzo de la evaluación junto con mi cuaderno para que pueda comenzar a completar ciertas secciones del informe mientras espero que finalice un escaneo. Todo esto ahorrará tiempo al final del compromiso, nos hará más tiempo para las cosas divertidas (¡pruebas de configuraciones erróneas y exploits!), Y asegurar que seamos lo más minuciosos posible.

Una estructura de ejemplo OneNote (también aplicable a otras herramientas) puede parecer lo siguiente para la fase de descubrimiento:

`External Penetration Test - <Client Name>`

- `Scope`(incluidas las direcciones/rangos IP en el alcance, las URL, los hosts frágiles, los plazos de prueba y cualquier limitaciones u otra información relativa que necesitemos a mano)
- `Client Points of Contact`
- `Credentials`
- `Discovery/Enumeration`
    - `Scans`
    - `Live hosts`
- `Application Discovery`
    - `Scans`
    - `Interesting/Notable Hosts`
- `Exploitation`
    - `<Hostname or IP>`
    - `<Hostname or IP>`
- `Post-Exploitation`
    - `<Hostname or IP>`
    - `<<Hostname or IP>`

Nos referiremos a esta estructura a lo largo del módulo, por lo que sería un ejercicio muy beneficioso replicar esto y registrar todo nuestro trabajo en este módulo como si estuviéramos trabajando a través de una participación real. Esto nos ayudará a refinar nuestra metodología de documentación, una habilidad esencial para un probador de penetración exitoso. Tener notas a las que consultar desde cada sección será útil cuando llegue a las tres evaluaciones de habilidades al final del módulo y seremos extremadamente útil a medida que avanzamos en el`Penetration Tester`camino.

---

# **Enumeración inicial**

Supongamos que nuestro cliente nos proporcionó el siguiente alcance:

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ cat scope_list app.inlanefreight.local
dev.inlanefreight.local
drupal-dev.inlanefreight.local
drupal-qa.inlanefreight.local
drupal-acc.inlanefreight.local
drupal.inlanefreight.local
blog-dev.inlanefreight.local
blog.inlanefreight.local
app-dev.inlanefreight.local
jenkins-dev.inlanefreight.local
jenkins.inlanefreight.local
web01.inlanefreight.local
gitlab-dev.inlanefreight.local
gitlab.inlanefreight.local
support-dev.inlanefreight.local
support.inlanefreight.local
inlanefreight.local
10.129.201.50

```

Podemos comenzar con un escaneo NMAP de puertos web comunes. Por lo general, haré un escaneo inicial con puertos`80,443,8000,8080,8180,8888,10000`y luego ejecute el testigo ocular o el acuato (o ambos dependiendo de los resultados del primero) contra este escaneo inicial. Al revisar el informe de captura de pantalla de los puertos más comunes, puedo ejecutar un escaneo NMAP más completo contra los 10,000 puertos superiores o todos los puertos TCP, dependiendo del tamaño del alcance. Dado que la enumeración es un proceso iterativo, ejecutaremos una herramienta de captura de pantalla web con cualquier escaneos NMAP posteriores que realizamos para garantizar la máxima cobertura.

En una prueba de penetración de alcance completo no evasivo, generalmente también ejecutaré un escaneo de Nessus para darle al cliente la mayor cantidad de dinero, pero debemos poder realizar evaluaciones sin depender de las herramientas de escaneo. A pesar de que la mayoría de las evaluaciones tienen un tiempo limitado (y a menudo no se encuentran adecuadamente para el tamaño del entorno), podemos proporcionar a nuestros clientes el máximo valor al establecer una metodología de enumeración repetible y exhaustiva que se puede aplicar a todos los entornos que cubrimos. Necesitamos ser eficientes durante la etapa de recopilación/descubrimiento de información sin tomar atajos que puedan dejar fallas críticas sin descubrir. La metodología y las herramientas preferidas de todos variarán un poco, y debemos esforzarnos por crear una que funcione bien para nosotros mientras llega al mismo objetivo final.

Todos los escaneos que realizamos durante un compromiso no evasivo son recopilar datos como entradas para nuestro proceso de validación manual y prueba manual. No debemos confiar únicamente en los escáneres, ya que el elemento humano en las pruebas de penetración es esencial. A menudo encontramos las vulnerabilidades y configuraciones erróneas más únicas y severas solo a través de pruebas manuales exhaustivas.

Invencemos en la lista de alcance mencionada anteriormente con un escaneo NMAP que generalmente descubrirá la mayoría de las aplicaciones web en un entorno. Por supuesto, realizaremos escaneos más profundos más adelante, pero esto nos dará un buen punto de partida.

Nota: No se pueden acceder a todos los hosts en la lista de alcance anterior al generar el objetivo a continuación. Habrá ejercicios separados, similares, al final de esta sección para reproducir gran parte de lo que se muestra aquí.

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ sudo  nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-07 21:49 EDT
Stats: 0:00:07 elapsed; 1 hosts completed (4 up), 4 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 81.24% done; ETC: 21:49 (0:00:01 remaining)

Nmap scan report for app.inlanefreight.local (10.129.42.195)
Host is up (0.12s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap scan report for app-dev.inlanefreight.local (10.129.201.58)
Host is up (0.12s latency).
Not shown: 993 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8000/tcp open  http-alt
8009/tcp open  ajp13
8080/tcp open  http-proxy
8180/tcp open  unknown
8888/tcp open  sun-answerbook

Nmap scan report for gitlab-dev.inlanefreight.local (10.129.201.88)
Host is up (0.12s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8081/tcp open  blackice-icecap

Nmap scan report for 10.129.201.50
Host is up (0.13s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
5357/tcp open  wsdapi
8000/tcp open  http-alt
8080/tcp open  http-proxy
8089/tcp open  unknown

<SNIP>

```

Como podemos ver, identificamos varios hosts que ejecutan servidores web en varios puertos. A partir de los resultados, podemos inferir que uno de los hosts es Windows y el resto son Linux (pero no puede estar 100% seguro en esta etapa). Preste una atención particularmente cercana a los nombres de host también. En este laboratorio, estamos utilizando VHOSTS para simular los subdominios de una empresa. Anfitriones con`dev`Como parte del FQDN, vale la pena anotar, ya que pueden estar ejecutando características no probadas o tener cosas como el modo de depuración habilitado. A veces los nombres de host no nos dirán demasiado, como`app.inlanefreight.local`. Podemos inferir que es un servidor de aplicaciones, pero necesitaría realizar una enumeración adicional para identificar qué aplicaciones (s) se están ejecutando en él.

También quisiéramos agregar`gitlab-dev.inlanefreight.local`A nuestra lista de "Hosts Interester" para profundizar una vez que completemos la fase de descubrimiento. Es posible que podamos acceder a Repos Public Git que podrían contener información confidencial, como credenciales o pistas que pueden llevarnos a otros subdominios/VHosts. No es raro encontrar instancias de GitLab que nos permitan registrar a un usuario sin requerir la aprobación del administrador para activar la cuenta. Podemos encontrar repositorios adicionales después de iniciar sesión. También valdría la pena verificar los confirmaciones anteriores para datos como credenciales que cubriremos más en detalle más adelante en este módulo cuando profundicemos en GitLab.

Enumerando a uno de los hosts más utilizando un escaneo de servicio nmap (`-sV`) contra los 1,000 puertos superiores predeterminados pueden decirnos más sobre lo que se está ejecutando en el servidor web.

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ sudo nmap --open -sV 10.129.201.50Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-07 21:58 EDT
Nmap scan report for 10.129.201.50
Host is up (0.13s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp open  http          Splunkd httpd
8080/tcp open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp open  ssl/http      Splunkd httpd (free license; remote login disabled)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.63 seconds

```

De la salida anterior, podemos ver que un servidor web IIS se está ejecutando en el puerto predeterminado 80, y parece que`Splunk`se ejecuta en el puerto 8000/8089, mientras que`PRTG Network Monitor`está presente en el puerto 8080. Si estuviéramos en un entorno de tamaño medio a grande, este tipo de enumeración sería ineficiente. Podría resultar en que nos falten una aplicación web que puede resultar crítica para el éxito del compromiso.

---

# **Usando testigos oculares**

Primero es el testigo ocular. Como se mencionó anteriormente, el testigo ocular puede tomar la salida XML de NMAP y Nessus y crear un informe con capturas de pantalla de cada aplicación web presente en los diversos puertos utilizando Selenium. También llevará las cosas un paso más allá y clasificará las aplicaciones cuando sea posible, las huele digitales y sugerirá credenciales predeterminadas basadas en la aplicación. También se le puede dar una lista de direcciones IP y URL y que se les diga que prepense previamente`http://`y`https://`al frente de cada uno. Realizará la resolución DNS para IPS y se le puede dar un conjunto específico de puertos para intentar conectarse y capturar la captura de pantalla.

Podemos instalar testigos oculares a través de apt:

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ sudo apt install eyewitness
```

o clonar el[repositorio](https://github.com/FortyNorthSecurity/EyeWitness), navegue al`Python/setup`directorio y ejecutar el`setup.sh`Script de instalación. El testigo ocular también se puede ejecutar desde un contenedor Docker, y hay una versión de Windows disponible, que se puede compilar con Visual Studio.

Correr`eyewitness -h`nos mostrará las opciones disponibles para nosotros:

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ eyewitness -husage: EyeWitness.py [--web] [-f Filename] [-x Filename.xml]
                     [--single Single URL] [--no-dns] [--timeout Timeout]
                     [--jitter# of Seconds] [--delay # of Seconds]                     [--threads# of Threads]                     [--max-retries Max retries on a timeout]
                     [-d Directory Name] [--results Hosts Per Page]
                     [--no-prompt] [--user-agent User Agent]
                     [--difference Difference Threshold]
                     [--proxy-ip 127.0.0.1] [--proxy-port 8080]
                     [--proxy-type socks5] [--show-selenium] [--resolve]
                     [--add-http-ports ADD_HTTP_PORTS]
                     [--add-https-ports ADD_HTTPS_PORTS]
                     [--only-ports ONLY_PORTS] [--prepend-https]
                     [--selenium-log-path SELENIUM_LOG_PATH] [--resume ew.db]
                     [--ocr]

EyeWitness is a tool used to capture screenshots from a list of URLs

Protocols:
  --web                 HTTP Screenshot using Selenium

Input Options:
  -f Filename           Line-separated file containing URLs to capture
  -x Filename.xml       Nmap XML or .Nessus file
  --single Single URL   Single URL/Host to capture
  --no-dns              Skip DNS resolution when connecting to websites

Timing Options:
  --timeout Timeout     Maximum number of seconds to wait while requesting a
                        web page (Default: 7)
  --jitter# of Seconds                        Randomize URLs and add a random delay between requests
  --delay# of Seconds  Delay between the opening of the navigator and taking                        the screenshot
  --threads# of Threads                        Number of threads to use while using file based input
  --max-retries Max retries on a timeout
                        Max retries on timeouts

<SNIP>

```

Ejecutemos el valor predeterminado`--web`Opción para tomar capturas de pantalla utilizando la salida NMAP XML de Discovery Scan como entrada.

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness#################################################################################                                  EyeWitness                                  ##################################################################################           FortyNorth Security - https://www.fortynorthsecurity.com           #################################################################################Starting Web Requests (26 Hosts)
Attempting to screenshot http://app.inlanefreight.local
Attempting to screenshot http://app-dev.inlanefreight.local
Attempting to screenshot http://app-dev.inlanefreight.local:8000
Attempting to screenshot http://app-dev.inlanefreight.local:8080
Attempting to screenshot http://gitlab-dev.inlanefreight.local
Attempting to screenshot http://10.129.201.50
Attempting to screenshot http://10.129.201.50:8000
Attempting to screenshot http://10.129.201.50:8080
Attempting to screenshot http://dev.inlanefreight.local
Attempting to screenshot http://jenkins-dev.inlanefreight.local
Attempting to screenshot http://jenkins-dev.inlanefreight.local:8000
Attempting to screenshot http://jenkins-dev.inlanefreight.local:8080
Attempting to screenshot http://support-dev.inlanefreight.local
Attempting to screenshot http://drupal-dev.inlanefreight.local
[*] Hit timeout limit when connecting to http://10.129.201.50:8000, retrying
Attempting to screenshot http://jenkins.inlanefreight.local
Attempting to screenshot http://jenkins.inlanefreight.local:8000
Attempting to screenshot http://jenkins.inlanefreight.local:8080
Attempting to screenshot http://support.inlanefreight.local
[*] Completed 15 out of 26 services
Attempting to screenshot http://drupal-qa.inlanefreight.local
Attempting to screenshot http://web01.inlanefreight.local
Attempting to screenshot http://web01.inlanefreight.local:8000
Attempting to screenshot http://web01.inlanefreight.local:8080
Attempting to screenshot http://inlanefreight.local
Attempting to screenshot http://drupal-acc.inlanefreight.local
Attempting to screenshot http://drupal.inlanefreight.local
Attempting to screenshot http://blog-dev.inlanefreight.local
Finished in 57.859838008880615 seconds

[*] Done! Report written in the /home/mrb3n/Projects/inlanfreight/inlanefreight_eyewitness folder!
Would you like to open the report now? [Y/n]

```

---

# **Usando Aquatone**

[Aguatano](https://github.com/michenriksen/aquatone), como se mencionó anteriormente, es similar al testigo ocular y puede tomar capturas de pantalla cuando se le proporciona un`.txt`Archivo de hosts o un NMAP`.xml`archivo con el`-nmap`bandera. Podemos compilar Aquatone por nuestra cuenta o descargar un binario precompilado. Después de descargar el binario, solo necesitamos extraerlo, y estamos listos para comenzar.

Nota:`Aquatone`actualmente está bajo desarrollo activo en un nuevo[tenedor](https://github.com/shelld3v/aquatone), centrándose en mejoras y mejoras de características. Consulte la guía de instalación proporcionada en el repositorio.

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
```

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ unzip aquatone_linux_amd64_1.7.0.zip Archive:  aquatone_linux_amd64_1.7.0.zip
  inflating: aquatone
  inflating: README.md
  inflating: LICENSE.txt

```

Podemos moverlo a una ubicación en nuestro`$PATH`como`/usr/local/bin`Para poder llamar a la herramienta desde cualquier lugar o simplemente soltar el binario en nuestro directorio de trabajo (digamos, escaneos). Es la preferencia personal pero, por lo general, la más eficiente para construir nuestras máquinas virtuales de ataque con la mayoría de las herramientas disponibles para usar sin tener que cambiar constantemente los directorios o llamarlos desde otros directorios.

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ echo $PATH/home/mrb3n/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games

```

En este ejemplo, proporcionamos la herramienta lo mismo`web_discovery.xml`Salida de NMAP especificando el`-nmap`bandera, y nos vamos a las carreras.

Descubrimiento y enumeración de la aplicación

```
xnoxos@htb[/htb]$ cat web_discovery.xml | ./aquatone -nmapaquatone v1.7.0 started at 2021-09-07T22:31:03-04:00

Targets    : 65
Threads    : 6
Ports      : 80, 443, 8000, 8080, 8443
Output dir : .

http://web01.inlanefreight.local:8000/: 403 Forbidden
http://app.inlanefreight.local/: 200 OK
http://jenkins.inlanefreight.local/: 403 Forbidden
http://app-dev.inlanefreight.local/: 200
http://app-dev.inlanefreight.local/: 200
http://app-dev.inlanefreight.local:8000/: 403 Forbidden
http://jenkins.inlanefreight.local:8000/: 403 Forbidden
http://web01.inlanefreight.local:8080/: 200
http://app-dev.inlanefreight.local:8000/: 403 Forbidden
http://10.129.201.50:8000/: 200 OK

<SNIP>

http://web01.inlanefreight.local:8000/: screenshot successful
http://app.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local/: screenshot successful
http://jenkins.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local:8000/: screenshot successful
http://jenkins.inlanefreight.local:8000/: screenshot successful
http://app-dev.inlanefreight.local:8000/: screenshot successful
http://app-dev.inlanefreight.local:8080/: screenshot successful
http://app.inlanefreight.local/: screenshot successful

<SNIP>

Calculating page structures... done
Clustering similar pages... done
Generating HTML report... done

Writing session file...Time:
 - Started at  : 2021-09-07T22:31:03-04:00
 - Finished at : 2021-09-07T22:31:36-04:00
 - Duration    : 33s

Requests:
 - Successful : 65
 - Failed     : 0

 - 2xx : 47
 - 3xx : 0
 - 4xx : 18
 - 5xx : 0

Screenshots:
 - Successful : 65
 - Failed     : 0

Wrote HTML report to: aquatone_report.html

```

---

# **Interpretando los resultados**

Incluso con los 26 hosts anteriores, este informe nos ahorrará tiempo. ¡Ahora imagine un entorno con 500 o 5,000 anfitriones! Después de abrir el informe, vemos que el informe está organizado en categorías, con`High Value Targets`Siendo primero y típicamente los anfitriones más "jugosos" para perseguir. He realizado testigos oculares en entornos muy grandes y generé informes con cientos de páginas que tardan horas en pasar. A menudo, los informes muy grandes tendrán anfitriones interesantes enterrados en lo profundo de ellos, por lo que vale la pena revisar todo y empujar/investigar cualquier aplicación con la que no estemos familiarizados. Encontré el`ManageEngine OpManager`Aplicación mencionada en la sección Introducción enterrada profundamente en un informe muy grande durante una prueba de penetración externa. Esta instancia se dejó configurada con las credenciales predeterminadas`admin:admin`y se fue abierto a Internet. Pude iniciar sesión y lograr la ejecución del código ejecutando un script PowerShell. La aplicación OpManager se estaba ejecutando en el contexto de una cuenta de administrador de dominio que condujo al compromiso total de la red interna.

En el siguiente informe, me emocionaría de inmediato ver a Tomcat en cualquier evaluación (pero especialmente durante una prueba de penetración externa) e intentaría credenciales predeterminados en el`/manager`y`/host-manager`puntos finales. Si podemos acceder, podemos cargar un archivo de guerra malicioso y lograr la ejecución de código remoto en el host subyacente usando[Código JSP](https://en.wikipedia.org/wiki/Jakarta_Server_Pages). Más sobre esto más adelante en el módulo.

![](https://academy.hackthebox.com/storage/modules/113/eyewitness4.png)

Continuando a través del informe, parece el principal`http://inlanefreight.local`El sitio web es el siguiente. Las aplicaciones web personalizadas siempre valen la pena probar, ya que pueden contener una amplia variedad de vulnerabilidades. Aquí también me interesaría ver si el sitio web estaba ejecutando un CMS popular como WordPress, Joomla o Drupal. La próxima aplicación,`http://support-dev.inlanefreight.local`, es interesante porque parece estar corriendo[Osticket](https://osticket.com/), que ha sufrido varias vulnerabilidades severas a lo largo de los años. Los sistemas de boletos de soporte son de particular interés porque podemos iniciar sesión y obtener acceso a información confidencial. Si la ingeniería social está en alcance, podemos interactuar con el personal de atención al cliente o incluso manipular el sistema para registrar una dirección de correo electrónico válida para el dominio de la compañía que podemos aprovechar para obtener acceso a otros servicios.

Esta última pieza se demostró en la caja de lanzamiento semanal HTB[Entrega](https://0xdf.gitlab.io/2021/05/22/htb-delivery.html)por[Ippsec](https://www.youtube.com/watch?v=gbs43E71mFM). Vale la pena estudiar esta caja en particular, ya que muestra lo que es posible explorando la funcionalidad incorporada de ciertas aplicaciones comunes. Cubriremos a Osticket más en profundidad más adelante en este módulo.

![](https://academy.hackthebox.com/storage/modules/113/eyewitness3.png)

Durante una evaluación, continuaría revisando el informe, observando a los anfitriones interesantes, incluidos la URL y el nombre/versión de la aplicación para más adelante. Es importante en este momento recordar que todavía estamos en la fase de recopilación de información, y cada pequeño detalle podría hacer o romper nuestra evaluación. No debemos quedar descuidados y comenzar a atacar a los anfitriones de inmediato, ya que podemos terminar en una madriguera de conejo y perder algo crucial más adelante en el informe. Durante una prueba de penetración externa, esperaría ver una combinación de aplicaciones personalizadas, algunas CMS, tal vez aplicaciones como Tomcat, Jenkins y Splunk, portales de acceso remoto como servicios remotos de escritorio (RDS), puntos finales SSL VPN, Acceso web de Outlook (OWA), O365, tal vez algún tipo de página de registro de dispositivos de redes de borde, etc., etc.

Su kilometraje puede variar, y a veces nos encontraremos con aplicaciones que absolutamente no deben estar expuestas, como una sola página con un botón de carga de archivo que encontré una vez con un mensaje que indicó: "Solo cargue los archivos .zip y .tar.gz". Yo, por supuesto, no escuché esta advertencia (ya que esto estaba en el alcance durante una prueba de penetración sancionada por el cliente) y procedí a cargar una prueba`.aspx`archivo. Para mi sorpresa, no había una especie de validación del lado del cliente o back-end, y el archivo pareció cargarse. Haciendo un directorio rápido, pude localizar un`/files`directorio que tenía un listado de directorio habilitado, y mi`test.aspx`El archivo estaba ahí. Desde aquí, procedí a subir un`.aspx`Web Shell y ganó un punto de apoyo en el entorno interno. Este ejemplo muestra que no debemos dejar piedra sin mover y que puede haber un tesoro absoluto de datos para nosotros en nuestros datos de descubrimiento de aplicaciones.

Durante una prueba de penetración interna, veremos gran parte de lo mismo, pero a menudo también veremos muchas páginas de inicio de sesión de la impresora (que a veces podemos aprovechar para obtener credenciales de ClearText LDAP), ESXI y VCenter Login Portals, ILO e IDRAC Login Page, una plethora de dispositivos de red, dispositivos de IOT, Phons, Phonseries internos de IP, Portalios de Seguridad, y Muy de Seguridad de Aplicatorios.

---

# **Avanzar**

Ahora que hemos trabajado a través de nuestra metodología de descubrimiento de aplicaciones y establecemos nuestra estructura de notificación en algunas de las aplicaciones más comunes que encontraremos una y otra vez. Tenga en cuenta que este módulo no puede cubrir todas las aplicaciones que enfrentaremos. Por el contrario, nuestro objetivo es cubrir los muy frecuentes y aprender sobre vulnerabilidades comunes, configuraciones erróneas y abusar de su funcionalidad incorporada.

Puedo garantizar que enfrentará al menos algunas, si no todas, de estas aplicaciones durante su carrera como probador de penetración. La metodología y la mentalidad de explorar estas aplicaciones son aún más importantes, que desarrollaremos y mejoraremos a lo largo de este módulo y probar durante las evaluaciones de habilidades al final. Muchos evaluadores tienen grandes habilidades técnicas, pero habilidades blandas, como un sonido, y una metodología repetible, junto con la organización, atención al detalle, una comunicación fuerte y una notificación/documentación exhaustiva y los informes pueden diferenciarnos y ayudar a generar confianza en nuestros habilidades tanto de nuestros empleadores como de nuestros clientes.