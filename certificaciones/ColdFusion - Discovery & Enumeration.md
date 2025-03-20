# ColdFusion - Discovery & Enumeration

Coldfusion es un lenguaje de programación y una plataforma de desarrollo de aplicaciones web basada en Java. Coldfusion fue desarrollado inicialmente por Allaire Corporation en 1995 y fue adquirida por Macromedia en 2001. Macromedia fue luego adquirida por Adobe Systems, que ahora posee y desarrolla Coldfusion.

Se utiliza para crear aplicaciones web dinámicas e interactivas que se pueden conectar a varias API y bases de datos como MySQL, Oracle y Microsoft SQL Server. Coldfusion se lanzó por primera vez en 1995 y desde entonces se ha convertido en una plataforma poderosa y versátil para el desarrollo web.

Lenguaje de marcado de ColdFusion (`CFML`) es el lenguaje de programación propietario utilizado en ColdFusion para desarrollar aplicaciones web dinámicas. Tiene una sintaxis similar a HTML, lo que facilita aprender para los desarrolladores web. CFML incluye etiquetas y funciones para la integración de la base de datos, servicios web, administración de correo electrónico y otras tareas comunes de desarrollo web. Su enfoque basado en etiquetas simplifica el desarrollo de aplicaciones al reducir la cantidad de código necesario para realizar tareas complejas. Por ejemplo, el`cfquery`La etiqueta puede ejecutar declaraciones SQL para recuperar datos de una base de datos:

Código:html

```html
<cfquery name="myQuery" datasource="myDataSource">
  SELECT *
  FROM myTable
</cfquery>
```

Los desarrolladores pueden usar el`cfloop`Etiqueta para iterar a través de los registros recuperados de la base de datos:

Código:html

```html
<cfloop query="myQuery"><p>#myQuery.firstName# #myQuery.lastName#</p></cfloop>
```

Gracias a sus funciones y características incorporadas, CFML permite a los desarrolladores crear una lógica comercial compleja utilizando un código mínimo. Además, Coldfusion admite otros lenguajes de programación, como JavaScript y Java, lo que permite a los desarrolladores usar su lenguaje de programación preferido dentro del entorno de ColdFusion.

Coldfusion también ofrece soporte para correo electrónico, manipulación PDF, gráficos y otras características de uso común. Las aplicaciones desarrolladas con ColdFusion pueden ejecutarse en cualquier servidor que admita su tiempo de ejecución. Está disponible para descargar desde el sitio web de Adobe y se puede instalar en sistemas operativos Windows, Mac o Linux. Las aplicaciones de ColdFusion también se pueden implementar en plataformas en la nube como Amazon Web Services o Microsoft Azure. Algunos de los propósitos y beneficios principales de ColdFusion incluyen:

| **Beneficios** | **Descripción** |
| --- | --- |
| `Developing data-driven web applications` | ColdFusion permite a los desarrolladores crear aplicaciones web ricas y receptivas fácilmente. Ofrece gestión de sesiones, manejo de formularios, depuración y más funciones. ColdFusion le permite aprovechar su conocimiento existente del idioma y combinarlo con características avanzadas para ayudarlo a construir aplicaciones web robustas rápidamente. |
| `Integrating with databases` | ColdFusion se integra fácilmente con bases de datos como Oracle, SQL Server y MySQL. ColdFusion proporciona conectividad avanzada de la base de datos y está diseñado para facilitar la recuperación, manipular y ver datos desde una base de datos y la web. |
| `Simplifying web content management` | Uno de los objetivos principales de ColdFusion es optimizar la gestión de contenido web. La plataforma ofrece generación dinámica de HTML y simplifica la creación de formularios, la reescritura de URL, la carga de archivos y el manejo de grandes formularios. Además, ColdFusion también admite AJAX al manejar automáticamente la serialización y la deserialización de los componentes habilitados para AJAX. |
| `Performance` | Coldfusion está diseñado para ser altamente rendimiento y está optimizado para baja latencia y alto rendimiento. Puede manejar una gran cantidad de solicitudes simultáneas mientras se mantiene un alto nivel de rendimiento. |
| `Collaboration` | Coldfusion ofrece características que permiten a los desarrolladores trabajar juntos en proyectos en tiempo real. Esto incluye compartir código, depuración, control de versiones y más. Esto permite un desarrollo más rápido y más eficiente, tiempo de mercado reducido y una entrega más rápida de proyectos. |

A pesar de ser menos popular que otras plataformas de desarrollo web, Coldfusion todavía es ampliamente utilizado por desarrolladores y organizaciones a nivel mundial. Gracias a su facilidad de uso, capacidades rápidas de desarrollo de aplicaciones e integración con otras tecnologías web, es una opción ideal para construir aplicaciones web de manera rápida y eficiente. Coldfusion ha evolucionado, con nuevas versiones lanzadas periódicamente desde su inicio.

La última versión estable de Coldfusion, al momento de escribir este artículo, es Coldfusion 2021, con Coldfusion 2023 a punto de entrar en alfa. Las versiones anteriores incluyen Coldfusion 2018, Coldfusion 2016 y Coldfusion 11, cada una con nuevas características y mejoras, como un mejor rendimiento, una integración más directa con otras plataformas, seguridad mejorada y mejorabilidad mejorada.

Al igual que cualquier tecnología orientada a la web, Coldfusion ha sido históricamente vulnerable a varios tipos de ataques, como inyección SQL, XSS, recorrido por directorio, derivación de autenticación y cargas de archivos arbitrarios. Para mejorar la seguridad de ColdFusion, los desarrolladores deben implementar prácticas de codificación seguras, verificaciones de validación de entrada y configurar correctamente los servidores web y los firewalls. Aquí hay algunas vulnerabilidades conocidas de ColdFusion:

1. CVE-2021-21087: No permite la carga de código fuente JSP
2. CVE-2020-24453: Configuración errónea de integración de Active Directory
3. CVE-2020-24450: Vulnerabilidad de inyección de comandos
4. CVE-2020-24449: Vulnerabilidad arbitraria de lectura de archivos
5. CVE-2019-15909: vulnerabilidad de secuencias de comandos de sitios cruzados (XSS)

Coldfusion expone unos pocos puertos de forma predeterminada:

| **Número de puerto** | **Protocolo** | **Descripción** |
| --- | --- | --- |
| 80 | Http | Se utiliza para la comunicación HTTP no segura entre el servidor web y el navegador web. |
| 443 | Https | Se utiliza para la comunicación HTTP segura entre el servidor web y el navegador web. Cifra la comunicación entre el servidor web y el navegador web. |
| 1935 | RPC | Utilizado para la comunicación de cliente-servidor. El protocolo de llamada de procedimiento remoto (RPC) permite que un programa solicite información de otro programa en un dispositivo de red diferente. |
| 25 | Smtp | El protocolo de transferencia de correo simple (SMTP) se utiliza para enviar mensajes de correo electrónico. |
| 8500 | Ssl | Se utiliza para la comunicación del servidor a través de Secure Socket Layer (SSL). |
| 5500 | Monitor de servidor | Se utiliza para la administración remota del servidor ColdFusion. |

Es importante tener en cuenta que los puertos predeterminados se pueden cambiar durante la instalación o la configuración.

---

# **Enumeración**

Durante una enumeración de pruebas de penetración, existen varias maneras para identificar si una aplicación web usa ColdFusion. Aquí hay algunos métodos que se pueden usar:

| **Método** | **Descripción** |
| --- | --- |
| `Port Scanning` | ColdFusion generalmente usa el puerto 80 para HTTP y el puerto 443 para HTTPS de forma predeterminada. Por lo tanto, escanear para estos puertos puede indicar la presencia de un servidor ColdFusion. NMAP podría identificar ColdFusion durante un escaneo de servicios específicamente. |
| `File Extensions` | Las páginas de ColdFusion generalmente usan las extensiones de archivo ".cfm" o ".cfc". Si encuentra páginas con estas extensiones de archivo, podría ser un indicador de que la aplicación está usando ColdFusion. |
| `HTTP Headers` | Verifique los encabezados de respuesta HTTP de la aplicación web. Coldfusion generalmente establece encabezados específicos, como "Server: ColdFusion" o "X-Powered-by: ColdFusion", que puede ayudar a identificar la tecnología que se utiliza. |
| `Error Messages` | Si la aplicación usa ColdFusion y hay errores, los mensajes de error pueden contener referencias a etiquetas o funciones específicas de ColdFusion. |
| `Default Files` | ColdFusion crea varios archivos predeterminados durante la instalación, como "Admin.CFM" o "CFIDE/Administrator/Index.cfm". Encontrar estos archivos en el servidor web puede indicar que la aplicación web se ejecuta en ColdFusion. |

### **Puertos NMAP y resultados de escaneo de servicios**

Coldfusion - Discovery & Enumeration

```
xnoxos@htb[/htb]$ nmap -p- -sC -Pn 10.129.247.30 --openStarting Nmap 7.92 ( https://nmap.org ) at 2023-03-13 11:45 GMT
Nmap scan report for 10.129.247.30
Host is up (0.028s latency).
Not shown: 65532 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
135/tcp   open  msrpc
8500/tcp  open  fmtp
49154/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 350.38 seconds

```

The port scan results show three open ports. Two Windows RPC services, and one running on `8500`. As we know, `8500` is a default port that ColdFusion uses for SSL. Navigating to the `IP:8500`Enumera 2 directorios,`CFIDE`y`cfdocs,`En la raíz, lo que indica que ColdFusion se está ejecutando en el puerto 8500.

Navegar por la estructura un poco muestra mucha información interesante, desde archivos con un claro`.cfm`extensión a mensajes de error y páginas de inicio de sesión.

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/cfindex.png)

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/CFIDE.png)

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/CFError.png)

El`/CFIDE/administrator`La ruta, sin embargo, carga la página de inicio de sesión del administrador ColdFusion 8. Ahora sabemos con certeza que`ColdFusion 8`se está ejecutando en el servidor.

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/CF8.png)

---

Nota: Existe la posibilidad de que la máquina virtual tome períodos prolongados de tiempo para responder (hasta 90s), sea paciente