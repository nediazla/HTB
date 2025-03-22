# Tomcat - Discovery & Enumeration

[Apache Tomcat](https://tomcat.apache.org/)es un servidor web de código abierto que aloja aplicaciones escritas en Java. Tomcat fue diseñado inicialmente para ejecutar scripts Java Servlets y Java Server Pages (JSP). Sin embargo, su popularidad aumentó en los marcos basados en Java y ahora es ampliamente utilizada por marcos como el resorte y las herramientas como Gradle. Según los datos recopilados por[Construido con](https://trends.builtwith.com/Web-Server/Apache-Tomcat-Coyote)Hay más de 220,000 sitios web de Tomcat en vivo en este momento. Aquí hay algunas estadísticas más interesantes:

- Builtwith ha recopilado datos que muestran que más de 904,000 sitios web han estado utilizando Tomcat
- El 1.22% de los 1 millón principales de sitios web usan TomCat, mientras que el 3.8% de los 100k principales sitios web son
- Tomcat tiene posición[# 13](https://webtechsurvey.com/technology/apache-tomcat)para servidores web por participación de mercado
- Algunas organizaciones que usan Tomcat incluyen Alibaba, la Oficina de Patentes y Marcas de los Estados Unidos (USPTO), la Cruz Roja Americana y el LA Times

Tomcat a menudo es menos apto para estar expuesto a Internet (aunque). Lo vemos de vez en cuando en Pentests externos y podemos hacer un excelente punto de apoyo en la red interna. Es mucho más común ver a Tomcat (y múltiples instancias, para el caso) durante los pentests internos. Por lo general, ocupará el primer lugar en "objetivos de alto valor" dentro de un informe de testigos oculares, y la mayoría de las veces, al menos una instancia interna está configurada con credenciales débiles o predeterminadas. Más sobre eso más tarde.

---

# **Descubrimiento/huella**

Durante nuestra prueba de penetración externa, ejecutamos testigo ocular y vemos un host en "Targets de alto valor de alto valor". La herramienta cree que el anfitrión está ejecutando Tomcat, pero debemos confirmar para planificar nuestros ataques. Si estamos tratando con TomCat en la red externa, esto podría ser un punto de apoyo fácil en el entorno de red interna.

Los servidores TomCat pueden ser identificados por el encabezado del servidor en la respuesta HTTP. Si el servidor está operando detrás de un proxy inverso, solicitar una página no válida debe revelar el servidor y la versión. Aquí podemos ver esa versión de Tomcat`9.0.30` is in use.

![](https://academy.hackthebox.com/storage/modules/113/tomcat_invalid.png)

Las páginas de error personalizadas pueden estar en uso que no filtran la información de esta versión. En este caso, otro método para detectar un servidor y versión de Tomcat es a través del`/docs`página.

Tomcat - Discovery & Enumeration

```
xnoxos@htb[/htb]$ curl -s http://app-dev.inlanefreight.local:8080/docs/ | grep Tomcat <html lang="en"><head><META http-equiv="Content-Type" content="text/html; charset=UTF-8"><link href="./images/docs-stylesheet.css" rel="stylesheet" type="text/css"><title>Apache Tomcat 9 (9.0.30) - Documentation Index</title><meta name="author"

<SNIP>

```

Esta es la página de documentación predeterminada, que los administradores no pueden eliminar. Aquí está la estructura de carpeta general de una instalación de Tomcat.

Tomcat - Discovery & Enumeration

```
├── bin
├── conf
│   ├── catalina.policy
│   ├── catalina.properties
│   ├── context.xml
│   ├── tomcat-users.xml
│   ├── tomcat-users.xsd
│   └── web.xml
├── lib
├── logs
├── temp
├── webapps
│   ├── manager
│   │   ├── images
│   │   ├── META-INF
│   │   └── WEB-INF
|   |       └── web.xml
│   └── ROOT
│       └── WEB-INF
└── work
    └── Catalina
        └── localhost

```

El`bin`La carpeta almacena scripts y binarios necesarios para iniciar y ejecutar un servidor Tomcat. El`conf`Carpeta almacena varios archivos de configuración utilizados por Tomcat. El`tomcat-users.xml`El archivo almacena las credenciales de los usuarios y sus roles asignados. El`lib`La carpeta contiene los diversos archivos JAR necesarios para el funcionamiento correcto de TomCat. El`logs`y`temp`Carpetas almacenan archivos de registro temporales. El`webapps`La carpeta es la raíz web predeterminada de TomCat y aloja todas las aplicaciones. El`work`La carpeta actúa como un caché y se usa para almacenar datos durante el tiempo de ejecución.

Cada carpeta dentro`webapps`se espera que tenga la siguiente estructura.

Tomcat - Discovery & Enumeration

```
webapps/customapp
├── images
├── index.jsp
├── META-INF
│   └── context.xml
├── status.xsd
└── WEB-INF
    ├── jsp
    |   └── admin.jsp
    └── web.xml
    └── lib
    |    └── jdbc_drivers.jar
    └── classes
        └── AdminServlet.class

```

El archivo más importante entre estos es`WEB-INF/web.xml`, que se conoce como el descriptor de implementación. Este archivo almacena información sobre las rutas utilizadas por la aplicación y las clases que manejan estas rutas. Todas las clases compiladas utilizadas por la aplicación deben almacenarse en el`WEB-INF/classes`carpeta. Estas clases pueden contener una lógica comercial importante, así como información confidencial. Cualquier vulnerabilidad en estos archivos puede conducir a un compromiso total del sitio web. El`lib`Carpeta almacena las bibliotecas necesarias por esa aplicación en particular. El`jsp`tiendas de carpetas[Páginas del servidor de Yakarta (JSP)](https://en.wikipedia.org/wiki/Jakarta_Server_Pages), anteriormente conocido como`JavaServer Pages`, que se puede comparar con los archivos PHP en un servidor Apache.

Aquí hay un archivo Web.xml de ejemplo.

Código:xml

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">

<web-app><servlet><servlet-name>AdminServlet</servlet-name><servlet-class>com.inlanefreight.api.AdminServlet</servlet-class></servlet><servlet-mapping><servlet-name>AdminServlet</servlet-name><url-pattern>/admin</url-pattern></servlet-mapping></web-app>
```

El`web.xml`La configuración anterior define un nuevo servlet llamado`AdminServlet`que se asigna a la clase`com.inlanefreight.api.AdminServlet`. Java usa la notación de puntos para crear nombres de paquetes, lo que significa que la ruta en el disco para la clase definida anteriormente sería:

- `classes/com/inlanefreight/api/AdminServlet.class`

A continuación, se crea una nueva asignación de servlet para mapear solicitudes a`/admin`con`AdminServlet`. Esta configuración enviará cualquier solicitud recibida para`/admin`hacia`AdminServlet.class`clase para procesamiento. El`web.xml`Descriptor contiene mucha información confidencial y es un archivo importante para verificar al aprovechar una vulnerabilidad de inclusión de archivos locales (LFI).

El`tomcat-users.xml`El archivo se usa para permitir o no permitir el acceso al`/manager`y`host-manager`páginas de administración.

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<SNIP>

<tomcat-users xmlns="http://tomcat.apache.org/xml"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"version="1.0"><!--
  By default, no user is included in the "manager-gui" role required
  to operate the "/manager/html" web application.  If you wish to use this app,
  you must define such a user - the username and password are arbitrary.

  Built-in Tomcat manager roles:
    - manager-gui    - allows access to the HTML GUI and the status pages
    - manager-script - allows access to the HTTP API and the status pages
    - manager-jmx    - allows access to the JMX proxy and the status pages
    - manager-status - allows access to the status pages only

  The users below are wrapped in a comment and are therefore ignored. If you
  wish to configure one or more of these users for use with the manager web
  application, do not forget to remove the <!.. ..> that surrounds them. You
  will also need to set the passwords to something appropriate.
-->

 <SNIP>

!-- user manager can access only manager section -->
<role rolename="manager-gui" /><user username="tomcat" password="tomcat" roles="manager-gui" /><!-- user admin can access manager and admin section both -->
<role rolename="admin-gui" /><user username="admin" password="admin" roles="manager-gui,admin-gui" /></tomcat-users>
```

El archivo nos muestra lo que cada uno de los roles`manager-gui`, `manager-script`, `manager-jmx`, y`manager-status`proporcionar acceso a. En este ejemplo, podemos ver que un usuario`tomcat`con la contraseña`tomcat`tiene el`manager-gui`papel y una segunda contraseña débil`admin`está configurado para la cuenta de usuario`admin`

---

# **Enumeración**

Después de hacer huellas digitales la instancia de Tomcat, a menos que tenga una vulnerabilidad conocida, generalmente queremos buscar el`/manager`y el`/host-manager`páginas. Podemos intentar localizarlos con una herramienta como`Gobuster`o simplemente navegar directamente a ellos.

Tomcat - Discovery & Enumeration

```
xnoxos@htb[/htb]$ gobuster dir -u http://web01.inlanefreight.local:8180/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt ===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://web01.inlanefreight.local:8180/
[+] Threads:        10
[+] Wordlist:       /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2021/09/21 17:34:54 Starting gobuster
===============================================================
/docs (Status: 302)
/examples (Status: 302)
/manager (Status: 302)
Progress: 49959 / 87665 (56.99%)^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2021/09/21 17:44:29 Finished
===============================================================

```

Es posible que podamos iniciar sesión en uno de estos utilizando credenciales débiles como`tomcat:tomcat`, `admin:admin`, etc. Si estos primeros intentos no funcionan, podemos probar un ataque con contraseña Brute Force contra la página de inicio de sesión, cubierto en la siguiente sección. Si tenemos éxito en iniciar sesión, podemos cargar un[Recursos de aplicaciones web o archivo de aplicaciones web (guerra)](https://en.wikipedia.org/wiki/WAR_(file_format)#:~:text=In%20software%20engineering%2C%20a%20WAR,that%20together%20constitute%20a%20web)Archivo que contiene un shell web JSP y obtenga la ejecución del código remoto en el servidor Tomcat.

Ahora que hemos aprendido sobre la estructura y la función de Tomcat, atacémoslo abusando de la funcionalidad incorporada y explotando una vulnerabilidad bien conocida que afectó las versiones específicas de Tomcat.