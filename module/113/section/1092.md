# Splunk - Discovery & Enumeration

Splunk es una herramienta de análisis de registros utilizada para recopilar, analizar y visualizar datos. Aunque originalmente no pretende ser una herramienta SIEM, Splunk a menudo se usa para el monitoreo de seguridad y el análisis comercial. Las implementaciones de Splunk a menudo se usan para albergar datos confidenciales y podrían proporcionar una gran cantidad de información para un atacante si se compromete. Históricamente, Splunk no ha sufrido muchas vulnerabilidades conocidas además de una vulnerabilidad de divulgación de información (CVE-2018-11409) y una vulnerabilidad de ejecución de código remoto autenticado en versiones muy antiguas (CVE-201142). Aquí hay algunos[detalles](https://www.splunk.com/en_us/customers.html)Acerca de Splunk:

- Splunk se fundó en 2003, se volvió rentable por primera vez en 2009 y tuvo su oferta pública inicial (IPO) en 2012 en Nasdaq bajo el símbolo SPLK
- Splunk tiene más de 7,500 empleados e ingresos anuales de casi $ 2.4 mil millones
- En 2020, Splunk fue nombrado en la lista Fortune 1000
- Los clientes de Splunk incluyen 92 empresas en la lista Fortune 100
- [Splunkbase](https://splunkbase.splunk.com/)Permite a los usuarios de Splunk descargar aplicaciones y complementos para Splunk. A partir de 2021, hay más de 2,000 aplicaciones disponibles

La mayoría de las veces veremos a Splunk durante nuestras evaluaciones, especialmente en grandes entornos corporativos durante las pruebas de penetración interna. Lo hemos visto expuesto externamente, pero esto es más raro. Splunk no sufre de muchas vulnerabilidades explotables y se apresura a parchar ningún problema. El mayor enfoque de Splunk durante una evaluación sería la autenticación débil o nula porque el acceso de administrador a Splunk nos brinda la capacidad de implementar aplicaciones personalizadas que se pueden utilizar para comprometer rápidamente un servidor Splunk y posiblemente otros hosts en la red dependiendo de la forma en que se configura Splunk.

---

# **Descubrimiento/huella**

Splunk prevalece en las redes internas y a menudo se ejecuta como root en Linux o sistema en los sistemas de Windows. Si bien es poco común, podemos encontrarnos con Splunk externamente a veces. Imaginemos que descubrimos una instancia olvidada de Splunk en nuestro informe Aquatone que desde entonces se ha convertido automáticamente a la versión gratuita, que no requiere autenticación. Dado que aún tenemos que ponernos un punto de apoyo en la red interna, centremos nuestra atención en Splunk y veamos si podemos convertir este acceso en RCE.

El servidor web de Splunk se ejecuta de forma predeterminada en el puerto 8000. En versiones anteriores de Splunk, las credenciales predeterminadas son`admin:changeme`, que se muestran convenientemente en la página de inicio de sesión.

![](https://academy.hackthebox.com/storage/modules/113/changme.png)

La última versión de Splunk establece credenciales durante el proceso de instalación. Si las credenciales predeterminadas no funcionan, vale la pena verificar las contraseñas débiles comunes como`admin`, `Welcome`, `Welcome1`, `Password123`, etc.

![](https://academy.hackthebox.com/storage/modules/113/splunk_login.png)

Podemos descubrir Splunk con un escaneo de servicio NMAP rápido. Aquí podemos ver que NMAP identificó el`Splunkd httpd`Servicio en el puerto 8000 y el puerto 8089, el puerto de administración de Splunk para la comunicación con la API Splunk REST.

Splunk - Discovery & Enumeration

```
xnoxos@htb[/htb]$ sudo nmap -sV 10.129.201.50Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-22 08:43 EDT
Nmap scan report for 10.129.201.50
Host is up (0.11s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp open  ssl/http      Splunkd httpd
8080/tcp open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp open  ssl/http      Splunkd httpd
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 39.22 seconds

```

---

# **Enumeración**

La prueba de Splunk Enterprise se convierte en una versión gratuita después de 60 días, lo que no requiere autenticación. No es raro que los administradores del sistema instalen una prueba de Splunk para probarla, que posteriormente se olvida. Esto se convertirá automáticamente a la versión gratuita que no tiene ninguna forma de autenticación, introduciendo un orificio de seguridad en el entorno. Algunas organizaciones pueden optar por la versión gratuita debido a limitaciones presupuestarias, no comprender completamente las implicaciones de no tener gestión de usuarios/roles.

![](https://academy.hackthebox.com/storage/modules/113/license_group.png)

Una vez que se inició sesión en Splunk (o que haya accedido a una instancia de Splunk Free), podemos explorar datos, ejecutar informes, crear paneles, instalar aplicaciones desde la biblioteca Splunkbase e instalar aplicaciones personalizadas.

![](https://academy.hackthebox.com/storage/modules/113/splunk_home.png)

Splunk tiene múltiples formas de ejecutar código, como aplicaciones Django del lado del servidor, puntos finales REST, entradas con secuencia de comandos y scripts de alerta. Un método común para obtener la ejecución de código remoto en un servidor Splunk es mediante el uso de una entrada con secuencia de comandos. Estos están diseñados para ayudar a integrar Splunk con fuentes de datos como API o servidores de archivos que requieren métodos personalizados para acceder. Las entradas con secuencia de comandos están destinadas a ejecutar estos scripts, con Stdout proporcionado como entrada a Splunk.

Como Splunk se puede instalar en hosts de Windows o Linux, se pueden crear entradas escritas para ejecutar scripts bash, powerShell o lotes. Además, cada instalación de Splunk viene con Python instalada, por lo que los scripts de Python se pueden ejecutar en cualquier sistema Splunk. Una forma rápida de obtener RCE es crear una entrada con guión que le indique a Splunk que ejecute un script de carcasa inversa de Python. Cubriremos esto en la siguiente sección.

Además de esta funcionalidad incorporada, Splunk ha sufrido varias vulnerabilidades públicas a lo largo de los años, como esta[SSRF](https://www.exploit-db.com/exploits/40895)Eso podría usarse para obtener acceso no autorizado a la API de REST Splunk. Al momento de escribir, Splunk ha[47](https://www.cvedetails.com/vulnerability-list/vendor_id-10963/Splunk.html)CVE. Si realizamos un escaneo de vulnerabilidad contra Splunk durante una prueba de penetración, a menudo veremos muchas vulnerabilidades no explotables devueltas. Es por eso que es importante comprender cómo abusar de la funcionalidad incorporada.