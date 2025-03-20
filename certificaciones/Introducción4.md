# Introducción

---

`Web Reconnaissance` es la base de una evaluación de seguridad exhaustiva. Este proceso implica la recopilación sistemática y meticulosa de información sobre un sitio web o aplicación web de destino. Piense en ello como la fase preparatoria antes de profundizar en un análisis más profundo y una posible explotación. Constituye una parte fundamental del "`Information Gathering`"Fase del Proceso de Pruebas de Penetración.

![](https://academy.hackthebox.com/storage/modules/144/PT-process.png)

Los objetivos principales del reconocimiento web incluyen:

- `Identifying Assets`: Descubrir todos los componentes del objetivo de acceso público, como páginas web, subdominios, direcciones IP y tecnologías utilizadas. Este paso proporciona una descripción general completa de la presencia en línea del objetivo.
- `Discovering Hidden Information`: Localizar información confidencial que podría quedar expuesta inadvertidamente, incluidos archivos de respaldo, archivos de configuración o documentación interna. Estos hallazgos pueden revelar información valiosa y posibles puntos de entrada para ataques.
- `Analysing the Attack Surface`: Examinar la superficie de ataque del objetivo para identificar posibles vulnerabilidades y debilidades. Esto implica evaluar las tecnologías utilizadas, las configuraciones y los posibles puntos de entrada para la explotación.
- `Gathering Intelligence`: Recopilación de información que puede aprovecharse para una mayor explotación o ataques de ingeniería social. Esto incluye identificar personal clave, direcciones de correo electrónico o patrones de comportamiento que podrían explotarse.

Los atacantes aprovechan esta información para personalizar sus ataques, lo que les permite atacar debilidades específicas y eludir las medidas de seguridad. Por el contrario, los defensores utilizan el reconocimiento para identificar y parchear vulnerabilidades de forma proactiva antes de que los actores maliciosos puedan aprovecharlas.

# **Tipos de reconocimiento**

El reconocimiento web abarca dos metodologías fundamentales: `active` y `passive` reconocimiento. Cada enfoque ofrece distintas ventajas y desafíos, y comprender sus diferencias es crucial para recopilar información adecuada.

### **Reconocimiento activo**

En reconocimiento activo, el atacante `directly interacts with the target system` para recopilar información. Esta interacción puede tomar varias formas:

| **Técnica** | **Descripción** | **Ejemplo** | **Herramientas** | **Riesgo de detección** |
| --- | --- | --- | --- | --- |
| `Port Scanning` | Identificar puertos abiertos y servicios que se ejecutan en el objetivo. | Usar Nmap para escanear un servidor web en busca de puertos abiertos como 80 (HTTP) y 443 (HTTPS). | Nmap, Masscan, Unicornscan | Alto: la interacción directa con el objetivo puede activar sistemas de detección de intrusos (IDS) y firewalls. |
| `Vulnerability Scanning` | Sondear el objetivo en busca de vulnerabilidades conocidas, como software obsoleto o configuraciones incorrectas. | Ejecutar Nessus en una aplicación web para verificar fallas de inyección SQL o vulnerabilidades de secuencias de comandos entre sitios (XSS). | Nessus, OpenVAS, Nikto | Alto: los escáneres de vulnerabilidades envían cargas útiles de exploits que las soluciones de seguridad pueden detectar. |
| `Network Mapping` | Mapear la topología de red del objetivo, incluidos los dispositivos conectados y sus relaciones. | Usar traceroute para determinar la ruta que toman los paquetes para llegar al servidor de destino, revelando posibles saltos de red e infraestructura. | Traceroute, Nmap | Medio a alto: el tráfico de red excesivo o inusual puede generar sospechas. |
| `Banner Grabbing` | Recuperar información de los banners mostrados por los servicios que se ejecutan en el objetivo. | Conexión a un servidor web en el puerto 80 y examen del banner HTTP para identificar el software y la versión del servidor web. | Netcat, rizo | Bajo: la captura de banners normalmente implica una interacción mínima, pero aún así se puede registrar. |
| `OS Fingerprinting` | Identificar el sistema operativo que se ejecuta en el objetivo. | Usando las capacidades de detección del sistema operativo de Nmap (`-O`) para determinar si el objetivo ejecuta Windows, Linux u otro sistema operativo. | Nmap, Xprobe2 | Bajo: la toma de huellas digitales del sistema operativo suele ser pasiva, pero se pueden detectar algunas técnicas avanzadas. |
| `Service Enumeration` | Determinar las versiones específicas de los servicios que se ejecutan en puertos abiertos. | Usando la detección de versión del servicio de Nmap (`-sV`) para determinar si un servidor web ejecuta Apache 2.4.50 o Nginx 1.18.0. | Nmapa | Bajo: similar a la captura de banners, la enumeración de servicios se puede registrar, pero es menos probable que active alertas. |
| `Web Spidering` | Rastrear el sitio web de destino para identificar páginas web, directorios y archivos. | Ejecutar un rastreador web como Burp Suite Spider u OWASP ZAP Spider para trazar la estructura de un sitio web y descubrir recursos ocultos. | Araña Burp Suite, Araña OWASP ZAP, Scrapy (personalizable) | Bajo a medio: se puede detectar si el comportamiento del rastreador no se configura cuidadosamente para imitar el tráfico legítimo. |

El reconocimiento activo proporciona una visión directa y, a menudo, más completa de la infraestructura y la situación de seguridad del objetivo. Sin embargo, también conlleva un mayor riesgo de detección, ya que las interacciones con el objetivo pueden desencadenar alertas o generar sospechas.

### **Reconocimiento pasivo**

Por el contrario, el reconocimiento pasivo implica recopilar información sobre el objetivo. `without directly interacting` con eso. Esto se basa en el análisis de información y recursos disponibles públicamente, como:

| **Técnica** | **Descripción** | **Ejemplo** | **Herramientas** | **Riesgo de detección** |
| --- | --- | --- | --- | --- |
| `Search Engine Queries` | Utilizar motores de búsqueda para descubrir información sobre el objetivo, incluidos sitios web, perfiles de redes sociales y artículos de noticias. | Buscando en Google "`[Target Name] employees`" para encontrar información de empleados o perfiles de redes sociales. | Google, DuckDuckGo, Bing y motores de búsqueda especializados (por ejemplo, Shodan) | Muy bajo: las consultas en los motores de búsqueda son una actividad normal en Internet y es poco probable que activen alertas. |
| `WHOIS Lookups` | Consultar bases de datos de WHOIS para recuperar detalles de registro de dominio. | Realizar una búsqueda de WHOIS en un dominio de destino para encontrar el nombre del registrante, la información de contacto y los servidores de nombres. | herramienta de línea de comandos whois, servicios de búsqueda de WHOIS en línea | Muy bajo: las consultas de WHOIS son legítimas y no levantan sospechas. |
| `DNS` | Análisis de registros DNS para identificar subdominios, servidores de correo y otra infraestructura. | Usando `dig` para enumerar subdominios de un dominio de destino. | cavar, nslookup, anfitrión, dnsenum, feroz, dnsrecon | Muy bajo: las consultas de DNS son esenciales para la navegación por Internet y, por lo general, no se marcan como sospechosas. |
| `Web Archive Analysis` | Examinar instantáneas históricas del sitio web del objetivo para identificar cambios, vulnerabilidades o información oculta. | Usar Wayback Machine para ver versiones anteriores de un sitio web de destino y ver cómo ha cambiado con el tiempo. | Máquina Wayback | Muy bajo: acceder a versiones archivadas de sitios web es una actividad normal. |
| `Social Media Analysis` | Recopilar información de plataformas de redes sociales como LinkedIn, Twitter o Facebook. | Buscar en LinkedIn empleados de una organización objetivo para conocer sus funciones, responsabilidades y posibles objetivos de ingeniería social. | LinkedIn, Twitter, Facebook, herramientas OSINT especializadas | Muy bajo: el acceso a perfiles públicos de redes sociales no se considera intrusivo. |
| `Code Repositories` | Analizar repositorios de código de acceso público como GitHub en busca de credenciales expuestas o vulnerabilidades. | Buscar en GitHub fragmentos de código o repositorios relacionados con el objetivo que puedan contener información confidencial o vulnerabilidades de código. | GitHub, GitLab | Muy bajo: los repositorios de código están destinados al acceso público y buscarlos no resulta sospechoso. |

El reconocimiento pasivo generalmente se considera más sigiloso y menos probable que active alarmas que el reconocimiento activo. Sin embargo, puede proporcionar información menos completa, ya que se basa en lo que ya es de acceso público.

En este módulo profundizaremos en las herramientas y técnicas esenciales utilizadas en el reconocimiento web, comenzando por WHOIS. Comprender el protocolo WHOIS proporciona una puerta de entrada para acceder a información vital sobre registros de dominio, detalles de propiedad y la infraestructura digital de los objetivos. Este conocimiento fundamental prepara el escenario para métodos de reconocimiento más avanzados que exploraremos más adelante.