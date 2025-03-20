# Creepy Crawlies

El rastreo web es vasto e intrincado, pero no tiene que embarcarse en este viaje solo. Una gran cantidad de herramientas de rastreo web están disponibles para ayudarlo, cada una con sus propias fortalezas y especialidades. Estas herramientas automatizan el proceso de rastreo, lo que lo hace más rápido y más eficiente, lo que le permite concentrarse en analizar los datos extraídos.

# **Rastreadores web populares**

1. `Burp Suite Spider`: BURP Suite, una plataforma de prueba de aplicaciones web ampliamente utilizada, incluye un poderoso rastreador activo llamado Spider. Spider se destaca para mapear aplicaciones web, identificar contenido oculto y descubrir vulnerabilidades potenciales.
2. `OWASP ZAP (Zed Attack Proxy)`: ZAP es un escáner de seguridad de aplicaciones web gratuitas y de código abierto. Se puede usar en modos automatizados y manuales e incluye un componente de araña para rastrear aplicaciones web e identificar posibles vulnerabilidades.
3. `Scrapy (Python Framework)`: Scrapy es un marco de Python versátil y escalable para construir rastreadores web personalizados. Proporciona características ricas para extraer datos estructurados de sitios web, manejar escenarios de rastreo complejos y automatizar el procesamiento de datos. Su flexibilidad lo hace ideal para tareas de reconocimiento a medida.
4. `Apache Nutch (Scalable Crawler)`: Nutch es un rastreador web de código abierto altamente extensible y escalable escrito en Java. Está diseñado para manejar rastreo masivo en toda la web o centrarse en dominios específicos. Si bien requiere más experiencia técnica para configurar y configurar, su potencia y flexibilidad lo convierten en un activo valioso para proyectos de reconocimiento a gran escala.

Adherirse a las prácticas de rastreo éticas y responsables es crucial sin importar qué herramienta elija. Siempre obtenga permiso antes de rastrear un sitio web, especialmente si planea realizar escaneos extensos o intrusivos. Tenga en cuenta los recursos del servidor del sitio web y evite sobrecargarlos con solicitudes excesivas.

# **Escrapaz**

Aprovecharemos Scrapy y una araña personalizada adaptada para el reconocimiento en`inlanefreight.com`. Si está interesado en más información sobre las técnicas de rastreo/araña, consulte el "[Uso de proxies web](https://academy.hackthebox.com/module/details/110)"Módulo, ya que forma parte de CBBH también.

### **Instalación de Scrapy**

Antes de comenzar, asegúrese de que tenga instalación de Scrapy en su sistema. Si no lo hace, puede instalarlo fácilmente con PIP, el instalador del paquete Python:

Rastreos espeluznantes

```
xnoxos@htb[/htb]$ pip3 install scrapy
```

Este comando descargará e instalará Scrapy junto con sus dependencias, preparando su entorno para construir nuestra araña.

### **Reconspider**

Primero, ejecute este comando en su terminal para descargar la araña de Screapy personalizada,`ReconSpider`y extraerlo al directorio de trabajo actual.

Rastreos espeluznantes

```
xnoxos@htb[/htb]$ wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zipxnoxos@htb[/htb]$ unzip ReconSpider.zip
```

Con los archivos extraídos, puede ejecutar`ReconSpider.py`Usando el siguiente comando:

Rastreos espeluznantes

```
xnoxos@htb[/htb]$ python3 ReconSpider.py http://inlanefreight.com
```

Reemplazar`inlanefreight.com`Con el dominio quieres araña. La araña arrastrará el objetivo y recopilará información valiosa.

### **Results.json**

Después de correr`ReconSpider.py`, los datos se guardarán en un archivo JSON,`results.json`. Este archivo se puede explorar utilizando cualquier editor de texto. A continuación se muestra la estructura del archivo JSON producido:

Código:json

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com",
        ...
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/",
        ...
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf",
        ...
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2",
        ...
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png",
        ...
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->",
        ...
    ]
}

```

Cada clave en el archivo JSON representa un tipo diferente de datos extraídos del sitio web de destino:

| **Llave json** | **Descripción** |
| --- | --- |
| `emails` | Enumera las direcciones de correo electrónico que se encuentran en el dominio. |
| `links` | Enumera URL de enlaces que se encuentran dentro del dominio. |
| `external_files` | Enumera las URL de archivos externos como PDFS. |
| `js_files` | Enumera las URL de los archivos JavaScript utilizados por el sitio web. |
| `form_fields` | Enumera los campos de formulario que se encuentran en el dominio (vacío en este ejemplo). |
| `images` | Enumera las URL de imágenes que se encuentran en el dominio. |
| `videos` | Enumera las URL de videos que se encuentran en el dominio (vacío en este ejemplo). |
| `audio` | Enumera las URL de los archivos de audio que se encuentran en el dominio (vacío en este ejemplo). |
| `comments` | Enumera los comentarios HTML encontrados en el código fuente. |

Al explorar esta estructura JSON, puede obtener información valiosa sobre la arquitectura, el contenido y los posibles puntos de interés de la aplicación web para una mayor investigación.