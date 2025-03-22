# Rastreo

`Crawling`, a menudo llamado`spidering`, es el`automated process of systematically browsing the World Wide Web`. Similar a cómo una araña navega por su web, un rastreador web sigue los enlaces de una página a otra, recopilando información. Estos rastreadores son esencialmente bots que utilizan algoritmos predefinidos para descubrir e indexar páginas web, haciéndolos accesibles a través de motores de búsqueda o para otros fines como el análisis de datos y el reconocimiento web.

# **Cómo funcionan los rastreadores web**

La operación básica de un rastreador web es sencillo pero poderoso. Comienza con una URL de semilla, que es la página web inicial para gatear. El rastreador obtiene esta página, analiza su contenido y extrae todos sus enlaces. Luego agrega estos enlaces a una cola y los rastrea, repitiendo el proceso iterativamente. Dependiendo de su alcance y configuración, el rastreador puede explorar un sitio web completo o incluso una gran parte de la web.

1. `Homepage`: Empiezas con la página de inicio que contiene`link1`, `link2`, y`link3`.
    
    Código:TXT
    
    ```
    Homepage
    ├── link1
    ├── link2
    └── link3
    
    ```
    
2. `Visiting link1`: Visitar`link1`muestra la página de inicio,`link2`, y también`link4`y`link5`.
    
    Código:TXT
    
    ```
    link1 Page
    ├── Homepage
    ├── link2
    ├── link4
    └── link5
    
    ```
    
3. `Continuing the Crawl`: El rastreador continúa siguiendo estos enlaces sistemáticamente, recopilando todas las páginas accesibles y sus enlaces.

Este ejemplo ilustra cómo un rastreador web descubre y recopila información siguiendo sistemáticamente los enlaces, distinguiéndolo de la confusión, lo que implica adivinar los posibles enlaces.

Hay dos tipos principales de estrategias de rastreo.

### **Estreñido**

[](https://mermaid.ink/svg/pako:eNo90D0PgjAQBuC_0twsg98Jgwkf6oKJgThZhkpPIEohpR0M4b970shNd09uuHsHKFqJ4EOpRVexJOWqtw83ZIiS3dKEK0YV3K-iRLbMuUIluQqY5x1Y6HSV_yFysCYIJ4gdbGY4OtgSRBOcHOxmODvYE8ACGtSNqCXdOPwu4WAqbJCDT60U-sWBq5H2hDVt9lEF-EZbXIBubVmB_xTvnibbSWEwrgX91syKsjatvrgIpiTGL-8RVcQ)

`Breadth-first crawling`Prioriza explorar el ancho de un sitio web antes de profundizar. Comienza arrastrando todos los enlaces en la página de semillas, luego pasa a los enlaces en esas páginas, y así sucesivamente. Esto es útil para obtener una visión general de la estructura y el contenido de un sitio web.

### **Crawling de profundidad**

[](https://mermaid.ink/svg/pako:eNo9zz0PgjAQBuC_0twsg18LgwlfGyYG4uQ5VHoC0RZS2sEQ_rsnTezU98mlvXeGZlAEMbRWjp0oKzSTf4RQEylxrUo0gk9yu8iWxPaOhoxCk4goOok06I41XSELsGfIVsgDHBjyFYoAR4YivCEEGtiAJqtlr3iZ-fclgutIE0LMVyXtCwHNwnPSu6H-mAZiZz1twA6-7SB-yvfEyY9KOsp7ySX0X0n1brDn0HWtvHwB2SFOww)

En contraste,`depth-first crawling`Prioriza la profundidad sobre la amplitud. Sigue una sola ruta de enlaces lo más posible antes de retroceder y explorar otros caminos. Esto puede ser útil para encontrar contenido específico o llegar a la estructura de un sitio web.

La elección de la estrategia depende de los objetivos específicos del proceso de rastreo.

# **Extracción de información valiosa**

Los rastreadores pueden extraer una amplia gama de datos, cada uno de los cuales tiene un propósito específico en el proceso de reconocimiento:

- `Links (Internal and External)`: Estos son los bloques de construcción fundamentales de la web, conectando páginas dentro de un sitio web (`internal links`) y a otros sitios web (`external links`). Los rastreadores recopilan meticulosamente estos enlaces, lo que le permite mapear la estructura de un sitio web, descubrir páginas ocultas e identificar relaciones con recursos externos.
- `Comments`: Las secciones de comentarios en blogs, foros u otras páginas interactivas pueden ser una mina de oro de la información. Los usuarios a menudo revelan inadvertidamente detalles confidenciales, procesos internos o indicios de vulnerabilidades en sus comentarios.
- `Metadata`: Los metadatos se refieren a`data about data`. En el contexto de las páginas web, incluye información como títulos de página, descripciones, palabras clave, nombres de autores y fechas. Estos metadatos pueden proporcionar un contexto valioso sobre el contenido, el propósito y la relevancia de una página para sus objetivos de reconocimiento.
- `Sensitive Files`: Los rastreadores web se pueden configurar para buscar activamente archivos confidenciales que puedan estar expuestos inadvertidamente en un sitio web. Esto incluye`backup files`(p.ej,`.bak`, `.old`), `configuration files`(p.ej,`web.config`, `settings.php`), `log files`(p.ej,`error_log`, `access_log`), y otros archivos que contienen contraseñas,`API keys`, u otra información confidencial. Examinar cuidadosamente los archivos extraídos, especialmente los archivos de copia de seguridad y configuración, puede revelar un tesoro de información confidencial, como`database credentials`, `encryption keys`, o incluso fragmentos de código fuente.

### **La importancia del contexto**

Comprender el contexto que rodea los datos extraídos es primordial.

Una sola información, como un comentario que menciona una versión de software específica, puede no parecer significativa por sí sola. Sin embargo, cuando se combina con otros hallazgos, como una versión obsoleta que figura en metadatos o un archivo de configuración potencialmente vulnerable descubierto a través del rastreo, puede transformarse en un indicador crítico de una posible vulnerabilidad.

El verdadero valor de los datos extraídos radica en conectar los puntos y construir una imagen integral del panorama digital del objetivo.

Por ejemplo, una lista de enlaces extraídos podría parecer mundano inicialmente. Pero tras un examen más detallado, notas un patrón: varias URL apuntan a un directorio llamado`/files/`. Esto desencadena su curiosidad, y usted decide visitar manualmente el directorio. Para su sorpresa, encuentra que la navegación del directorio está habilitada, exponiendo una gran cantidad de archivos, incluidos archivos de copia de seguridad, documentos internos y datos potencialmente confidenciales. Este descubrimiento no hubiera sido posible simplemente mirando enlaces individuales de forma aislada; El análisis contextual lo llevó a este hallazgo crítico.

Del mismo modo, los comentarios aparentemente inocuos pueden ganar importancia cuando se correlacionan con otros descubrimientos. Un comentario que menciona un "servidor de archivos" podría no plantear ninguna bandera roja inicialmente. Sin embargo, cuando se combina con el descubrimiento mencionado de la`/files/`Directorio, refuerza la posibilidad de que el servidor de archivos sea accesible públicamente, lo que puede exponer información confidencial o datos confidenciales.

Por lo tanto, es esencial abordar el análisis de datos de manera integral, considerando las relaciones entre diferentes puntos de datos y sus posibles implicaciones para sus objetivos de reconocimiento.