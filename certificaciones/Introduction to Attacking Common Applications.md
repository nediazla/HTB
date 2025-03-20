# Introduction to Attacking Common Applications

Las aplicaciones basadas en la web son frecuentes en la mayoría de los entornos, si no en todos, que encontramos como probadores de penetración. Durante nuestras evaluaciones, nos encontraremos con una amplia variedad de aplicaciones web, como sistemas de gestión de contenido (CMS), aplicaciones web personalizadas, portales de intranet utilizados por desarrolladores y sistemas, repositorios de código, herramientas de monitoreo de redes, sistemas de boletos, wikis, bases de conocimiento, rastreadores de problemas, aplicaciones de contenedores de servidores y más. Es común encontrar las mismas aplicaciones en muchos entornos diferentes. Si bien una aplicación puede no ser vulnerable en un entorno, puede estar mal configurado o sin parches en el siguiente. Un asesor debe tener una firme comprensión de enumerar y atacar las aplicaciones comunes cubiertas en este módulo.

Las aplicaciones web son aplicaciones interactivas a las que se puede acceder a través de navegadores web. Las aplicaciones web generalmente adoptan una arquitectura de cliente cliente para ejecutar y manejar interacciones. Por lo general, están formados por componentes frontales (la interfaz del sitio web o "lo que ve el usuario") que se ejecutan en el lado del cliente (navegador) y otros componentes de fondo (código fuente de la aplicación web) que se ejecutan en el lado del servidor (servidor de fondo/bases de datos). Para un estudio en profundidad de la estructura y función de las aplicaciones web, consulte el[Introducción a las aplicaciones web](https://academy.hackthebox.com/course/preview/introduction-to-web-applications)módulo.

Todos los tipos de aplicaciones web (comerciales, de código abierto y personalizado) pueden sufrir los mismos tipos de vulnerabilidades y configuraciones erróneas, a saber, los 10 principales riesgos de aplicaciones web cubiertas en el[OWASP TOP 10](https://owasp.org/www-project-top-ten/). Si bien podemos encontrar versiones vulnerables de muchas aplicaciones comunes que sufren vulnerabilidades (públicas) conocidas como inyección SQL, XSS, errores de ejecución de código remoto, lectura de archivos locales y carga de archivos sin restricciones, es igualmente importante que comprendamos cómo podemos abusar de la funcionalidad incorporada de muchas de estas aplicaciones para lograr la ejecución del código remoto.

A medida que las organizaciones continúan endureciendo su perímetro externo y limitan los servicios expuestos, las aplicaciones web se están convirtiendo en un objetivo más atractivo para los actores maliciosos y los probadores de penetración por igual. Cada vez más empresas están haciendo la transición al trabajo remoto y exponiendo aplicaciones (intencionalmente o involuntariamente) al mundo exterior. Las aplicaciones discutidas en este módulo generalmente tienen la misma probabilidad de estar expuestas en la red externa como la red interna. Estas aplicaciones pueden servir como un punto de apoyo al entorno interno durante una evaluación externa o como un punto de apoyo, movimiento lateral o un problema adicional para informar a nuestro cliente durante una evaluación interna.

[El estado de seguridad de la aplicación en 2021](https://blog.barracuda.com/2021/05/18/report-the-state-of-application-security-in-2021/)fue una encuesta de investigación encargada por Barracuda para recopilar información de los tomadores de decisiones relacionados con la seguridad de la aplicación. La encuesta incluye respuestas de 750 tomadores de decisiones en empresas con 500 o más empleados en todo el mundo. Los hallazgos de la encuesta fueron asombrosos: el 72% de los encuestados declaró que su organización sufrió al menos una violación debido a una vulnerabilidad de la aplicación, el 32% sufrió dos infracciones y el 14% sufrió tres. Las organizaciones encuestadas desglosaron sus desafíos de la siguiente manera: ataques BOT (43%), ataques de la cadena de suministro de software (39%), detección de vulnerabilidades (38%) y asegurando API (37%). Este módulo se centrará en vulnerabilidades conocidas y configuraciones erróneas en aplicaciones comerciales y de código abierto (versiones gratuitas demostradas en este módulo), que constituyen un gran porcentaje de los ataques exitosos que las organizaciones enfrentan regularmente.

---

# **Datos de aplicación**

Este módulo estudiará varias aplicaciones comunes en profundidad, al tiempo que cubrirá brevemente otras menos comunes (pero aún se ve a menudo). Solo algunas de las categorías de aplicaciones que podemos encontrar durante una evaluación dada que podemos aprovechar para obtener un punto de apoyo o obtener acceso a datos confidenciales incluyen:

| **Categoría** | **Aplicaciones** |
| --- | --- |
| [Administración de contenido web](https://enlyft.com/tech/web-content-management) | Joomla, Drupal, WordPress, Dotnetnuke, etc. |
| [Servidores de aplicaciones](https://enlyft.com/tech/application-servers) | Apache Tomcat, Phusion Passenger, Oracle Weblogic, IBM WebSphere, etc. |
| [Información de seguridad y gestión de eventos (SIEM)](https://enlyft.com/tech/security-information-and-event-management-siem) | Splunk, Trustwave, Logrhythm, etc. |
| [Gestión de redes](https://enlyft.com/tech/network-management) | PRTG Network Monitor, ManagenEngine Opmanger, etc. |
| [Gestión de TI](https://enlyft.com/tech/it-management-software) | Nagios, Puppet, Zabbix, ManaginEngine ServiceSk Plus, etc. |
| [Marcos de software](https://enlyft.com/tech/software-frameworks) | JBoss, Axis2, etc. |
| [Gestión de servicio al cliente](https://enlyft.com/tech/customer-service-management) | Osticket, Zendesk, etc. |
| [Motores de búsqueda](https://enlyft.com/tech/search-engines) | Elasticsearch, Apache Solr, etc. |
| [Gestión de configuración de software](https://enlyft.com/tech/software-configuration-management) | Atlassian Jira, Github, Gitlab, Bugzilla, Bugsnag, Bitbucket, etc. |
| [Herramientas de desarrollo de software](https://enlyft.com/tech/software-development-tools) | Jenkins, Atlassian Confluence, Phpmyadmin, etc. |
| [Integración de aplicaciones empresariales](https://enlyft.com/tech/enterprise-application-integration) | Oracle Fusion Middleware, BizTalk Server, Apache Activemq, etc. |

Como puede ver navegar por los enlaces para cada categoría anterior, hay[Miles de aplicaciones](https://enlyft.com/tech/)que podemos encontrar durante una evaluación dada. Muchos de estos sufren hazañas públicamente conocidas o tienen funcionalidad que se puede abusar de obtener la ejecución de código remoto, robar credenciales o acceder a la información confidencial con o sin credenciales válidas. Este módulo cubrirá las aplicaciones más frecuentes que vemos repetidamente durante las evaluaciones internas y externas.

Echemos un vistazo al sitio web de Enlyft. Podemos ver, por ejemplo, pudieron recopilar datos sobre más de 3,7 millones de compañías que están utilizando[WordPress](https://enlyft.com/tech/products/wordpress)lo que representa casi el 70% de la participación de mercado en todo el mundo para las aplicaciones de gestión de contenido web para todas las empresas encuestadas. Para la herramienta Siem[Flojo](https://enlyft.com/tech/products/splunk)fue utilizado por 22,174 de las compañías encuestadas y representaban casi el 30% de la cuota de mercado para las herramientas SIEM. Si bien las aplicaciones restantes que cubriremos representan una cuota de mercado mucho más pequeña para su categoría respectiva, todavía las veo a menudo, y las habilidades aprendidas aquí pueden aplicarse a muchas situaciones diferentes.

Mientras trabaja a través de los ejemplos de la sección, las preguntas y las evaluaciones de habilidades, haga un esfuerzo concertado para aprender cómo funcionan estas aplicaciones y*por qué*Existen vulnerabilidades específicas y malhiguraciones en lugar de simplemente reproducir los ejemplos para moverse rápidamente a través del módulo. Estas habilidades lo beneficiarán enormemente y probablemente podrían ayudarlo a identificar rutas de ataque en diferentes aplicaciones que encuentre durante una evaluación por primera vez. Todavía me encuentro con aplicaciones que solo he visto algunas veces o nunca antes, y acercarme a ellas con esta mentalidad a menudo me ha ayudado a lograr ataques o encontrar una manera de abusar de la funcionalidad incorporada.

---

# **Una historia rápida**

Por ejemplo, durante una prueba de penetración externa, encontré el[Aplicación de OSS del repositorio de Nexus](https://www.sonatype.com/products/repository-oss)de Sonatype, que nunca había visto antes. Rápidamente descubrí que las credenciales de administración predeterminadas de`admin:admin123`Para esa versión no se había cambiado, y pude iniciar sesión y pinchar alrededor de la funcionalidad de administración. En esta versión, aproveché la API como un usuario autenticado para obtener la ejecución del código remoto en el sistema. Encontré esta aplicación en otra evaluación, pude iniciar sesión con credenciales predeterminadas una vez más. Esta vez pudo abusar del[Tareas](https://help.sonatype.com/en/tasks.html#UUID-53669de9-914f-58da-0ba3-9e129cd2e3de_id_Tasks-Admin-Executescript)funcionalidad (que se deshabilitó la primera vez que encontré esta aplicación) y escriba un rápido[Groovy](https://groovy-lang.org/) [guion](https://help.sonatype.com/en/writing-scripts.html)En la sintaxis de Java para ejecutar un script y obtener la ejecución de código remoto. Esto es similar a cómo abusaremos de los Jenkins[consola de guiones](https://www.jenkins.io/doc/book/managing/script-console/)Más adelante en este módulo. He encontrado muchas otras aplicaciones, como[Opmanager](https://www.manageengine.com/products/applications_manager/me-opmanager-monitoring.html)Desde ManagementEngine que le permite ejecutar un script como el usuario en el que se ejecuta la aplicación (generalmente la poderosa cuenta de la autoridad NT \ System) y obtener un punto de apoyo. Nunca debemos pasar por alto las aplicaciones durante una evaluación interna y externa, ya que pueden ser nuestra única forma "en un entorno relativamente bien mantenido.

---

# **Aplicaciones comunes**

Por lo general, me encuentro con al menos una de las aplicaciones a continuación, que cubriremos en profundidad a lo largo de las secciones del módulo. Si bien no podemos cubrir todas las aplicaciones posibles que podamos encontrar, las habilidades enseñadas en este módulo nos prepararán para abordar todas las aplicaciones con un ojo crítico y evaluarlas para las vulnerabilidades públicas y las configuraciones erróneas.

| **Solicitud** | **Descripción** |
| --- | --- |
| WordPress | [WordPress](https://wordpress.org/)es un sistema de gestión de contenido (CMS) de código abierto que puede usarse para múltiples propósitos. A menudo se usa para alojar blogs y foros. WordPress es altamente personalizable y amigable con SEO, lo que lo hace popular entre las empresas. Sin embargo, su personalización y naturaleza extensible lo hacen propenso a las vulnerabilidades a través de temas y complementos de terceros. WordPress está escrito en PHP y generalmente se ejecuta en Apache con MySQL como backend. |
| Drupal | [Drupal](https://www.drupal.org/)es otro CMS de código abierto que es popular entre las empresas y los desarrolladores. Drupal está escrito en PHP y admite el uso de MySQL o PostgreSQL para el backend. Además, SQLite se puede usar si no hay DBMS instalados. Al igual que WordPress, Drupal permite a los usuarios mejorar sus sitios web mediante el uso de temas y módulos. |
| Joomla | [Joomla](https://www.joomla.org/)es otro CMS de código abierto escritos en PHP que generalmente usa MySQL pero se puede hacer para ejecutarse con PostgreSQL o SQLite. Joomla se puede utilizar para blogs, foros de discusión, comercio electrónico y más. Joomla se puede personalizar en gran medida con temas y extensiones y se estima que es el tercer CMS más usado en Internet después de WordPress y Shopify. |
| Gato | [Apache Tomcat](https://tomcat.apache.org/)es un servidor web de código abierto que aloja aplicaciones escritas en Java. Tomcat fue diseñado inicialmente para ejecutar scripts Java Servlets y Java Server Pages (JSP). Sin embargo, su popularidad aumentó con los marcos basados en Java y ahora es ampliamente utilizado por marcos como el resorte y las herramientas como Gradle. |
| Jenkins | [Jenkins](https://jenkins.io/)es un servidor de automatización de código abierto escrito en Java que ayuda a los desarrolladores a construir y probar sus proyectos de software continuamente. Es un sistema basado en servidor que se ejecuta en contenedores de servlet como Tomcat. A lo largo de los años, los investigadores han descubierto varias vulnerabilidades en Jenkins, incluidas algunas que permiten la ejecución de código remoto sin requerir autenticación. |
| Flojo | Splunk es una herramienta de análisis de registros utilizada para recopilar, analizar y visualizar datos. Aunque originalmente no pretende ser una herramienta SIEM, Splunk a menudo se usa para el monitoreo de seguridad y el análisis comercial. Las implementaciones de Splunk a menudo se usan para albergar datos confidenciales y podrían proporcionar una gran cantidad de información para un atacante si se compromete. Históricamente, Splunk no ha sufrido una cantidad considerable de vulnerabilidades conocidas además de una vulnerabilidad de divulgación de información ([CVE-2018-11409](https://nvd.nist.gov/vuln/detail/CVE-2018-11409)), y una vulnerabilidad de ejecución de código remoto autenticado en versiones muy antiguas ([CVE-2011-4642](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-4642)). |
| Monitor de red PRTG | [Monitor de red PRTG](https://www.paessler.com/prtg)es un sistema de monitoreo de red sin agente que se puede utilizar para monitorear las métricas como el tiempo de actividad, el uso de ancho de banda y más de una variedad de dispositivos, como enrutadores, conmutadores, servidores, etc., utiliza un modo de descubrimiento automático para escanear una red y luego aprovechar protocolos como ICMP, WMI, SNMP y NETFLOW para comunicar y recoger datos descubiertos. PRTG está escrito en[Delfi](https://en.wikipedia.org/wiki/Delphi_(software)). |
| Osticket | [Osticket](https://osticket.com/)es un sistema de ticketing de soporte de código abierto ampliamente utilizado. Se puede utilizar para administrar boletos de servicio al cliente recibidos por correo electrónico, teléfono y la interfaz web. Osticket está escrito en PHP y puede ejecutarse en Apache o IIS con MySQL como backend. |
| Gitlab | [Gitlab](https://about.gitlab.com/)es una plataforma de desarrollo de software de código abierto con un administrador de repositorio Git, control de versiones, seguimiento de problemas, revisión de código, integración e implementación continua, y más. Originalmente fue escrito en Ruby, pero ahora utiliza Ruby on Rails, Go y Vue.js. GitLab ofrece versiones de la comunidad (gratuita) y empresas del software. |

---

# **Objetivos de módulo**

A lo largo de las secciones del módulo, nos referiremos a URL como`http://app.inlanefreight.local`. Para simular un entorno grande y realista con múltiples servidores web, utilizamos VHOSTS para albergar las aplicaciones web. Dado que estos Vhosts se mapean a un directorio diferente en el mismo host, tenemos que hacer entradas manuales en nuestro`/etc/hosts`Archivo en el Pwnbox o VM de ataque local para interactuar con el laboratorio. Esto debe hacerse para cualquier ejemplo que muestre escaneos o capturas de pantalla utilizando un FQDN. Secciones como Splunk que solo usan la dirección IP del objetivo del objetivo no requerirán una entrada de archivo hosts, y puede interactuar con la dirección IP generada y el puerto asociado.

Para hacer esto rápidamente, podríamos ejecutar lo siguiente:

Introducción al ataque de aplicaciones comunes

```
xnoxos@htb[/htb]$ IP=10.129.42.195xnoxos@htb[/htb]$ printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts
```

Después de este comando, nuestro`/etc/hosts`El archivo se vería como lo siguiente (en un Pwnbox recientemente generado):

Introducción al ataque de aplicaciones comunes

```
xnoxos@htb[/htb]$ cat /etc/hosts# Your system has configured 'manage_etc_hosts' as True.# As a result, if you wish for changes to this file to persist# then you will need to either# a.) make changes to the master file in /etc/cloud/templates/hosts.debian.tmpl# b.) change or remove the value of 'manage_etc_hosts' in#     /etc/cloud/cloud.cfg or cloud-config from user-data#
127.0.1.1 htb-9zftpkslke.htb-cloud.com htb-9zftpkslke
127.0.0.1 localhost

# The following lines are desirable for IPv6 capable hosts::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts

10.129.42.195	app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local

```

Es posible que desee escribir su propio script o editar el archivo hosts a mano, lo cual está bien.

Si genera un objetivo durante una sección y no puede acceder a él directamente a través de la IP, ¡asegúrese de verificar su archivo de hosts y actualizar cualquier entrada!

Los ejercicios del módulo que requieren VHOSTS mostrarán una lista que puede usar para editar su archivo de hosts después de generar la VM de destino en la parte inferior de la sección respectiva.