# Search Engine Discovery

Los motores de búsqueda sirven como nuestras guías en el vasto paisaje de Internet, ayudándonos a navegar a través de la extensión de información aparentemente interminable. Sin embargo, más allá de su función principal de responder consultas cotidianas, los motores de búsqueda también tienen un tesoro de datos que puede ser invaluable para el reconocimiento web y la recopilación de información. Esta práctica, conocida como Descubrimiento de motores de búsqueda o recopilación de OSINT (inteligencia de código abierto), implica el uso de motores de búsqueda como herramientas poderosas para descubrir información sobre sitios web, organizaciones e individuos objetivo.

En esencia, el descubrimiento del motor de búsqueda aprovecha la inmensa potencia de los algoritmos de búsqueda para extraer datos que pueden no ser fácilmente visibles en los sitios web. Los profesionales e investigadores de seguridad pueden profundizar en la web indexada empleando operadores de búsqueda especializados, técnicas y herramientas, descubriendo todo, desde información de los empleados y documentos confidenciales hasta páginas de inicio de sesión ocultas y credenciales expuestas.

# **Por qué es importante el descubrimiento de motores de búsqueda**

Search Engine Discovery es un componente crucial del reconocimiento web por varias razones:

- `Open Source`: La información recopilada es de acceso público, lo que lo convierte en una forma legal y ética de obtener información sobre un objetivo.
- `Breadth of Information`: Los motores de búsqueda indexan una gran parte de la web, que ofrece una amplia gama de posibles fuentes de información.
- `Ease of Use`: Los motores de búsqueda son fáciles de usar y no requieren habilidades técnicas especializadas.
- `Cost-Effective`: Es un recurso gratuito y fácilmente disponible para la recopilación de información.

La información que puede reunir de los motores de búsqueda también se puede aplicar de varias maneras diferentes:

- `Security Assessment`: Identificar vulnerabilidades, datos expuestos y vectores de ataque potenciales.
- `Competitive Intelligence`: Recopilar información sobre productos, servicios y estrategias de los competidores.
- `Investigative Journalism`: Descubrir conexiones ocultas, transacciones financieras y prácticas poco éticas.
- `Threat Intelligence`: Identificar amenazas emergentes, rastrear actores maliciosos y predecir ataques potenciales.

Sin embargo, es importante tener en cuenta que el descubrimiento de motores de búsqueda tiene limitaciones. Los motores de búsqueda no indexan toda la información, y algunos datos pueden estar ocultos o protegidos deliberadamente.

# **Operadores de búsqueda**

Los operadores de búsqueda son como los códigos secretos de los motores de búsqueda. Estos comandos y modificadores especiales desbloquean un nuevo nivel de precisión y control, lo que le permite identificar tipos específicos de información en medio de la inmensidad de la web indexada.

Si bien la sintaxis exacta puede variar ligeramente entre los motores de búsqueda, los principios subyacentes siguen siendo consistentes. Vamos a profundizar en algunos operadores de búsqueda esenciales y avanzados:

| **Operador** | **Descripción del operador** | **Ejemplo** | **Descripción del ejemplo** |
| --- | --- | --- | --- |
| `site:` | Limita los resultados a un sitio web o dominio específico. | `site:example.com` | Encuentre todas las páginas de acceso público en Ejemplo.com. |
| `inurl:` | Encuentra páginas con un término específico en la URL. | `inurl:login` | Busque páginas de inicio de sesión en cualquier sitio web. |
| `filetype:` | Búsqueda de archivos de un tipo en particular. | `filetype:pdf` | Encuentre documentos PDF descargables. |
| `intitle:` | Encuentra páginas con un término específico en el título. | `intitle:"confidential report"` | Busque documentos titulados "Informe confidencial" o variaciones similares. |
| `intext:`o`inbody:` | Busca un término dentro del texto del cuerpo de las páginas. | `intext:"password reset"` | Identificar páginas web que contienen el término "restablecimiento de contraseña". |
| `cache:` | Muestra la versión en caché de una página web (si está disponible). | `cache:example.com` | Vea la versión en caché de Ejemplo.com para ver su contenido anterior. |
| `link:` | Encuentra páginas que se vinculan a una página web específica. | `link:example.com` | Identificar sitios web que vinculan a Ejemplo.com. |
| `related:` | Encuentra sitios web relacionados con una página web específica. | `related:example.com` | Descubra sitios web similares a Ejemplo.com. |
| `info:` | Proporciona un resumen de información sobre una página web. | `info:example.com` | Obtenga detalles básicos sobre Ejemplo.com, como su título y descripción. |
| `define:` | Proporciona definiciones de una palabra o frase. | `define:phishing` | Obtenga una definición de "phishing" de varias fuentes. |
| `numrange:` | Búsqueda de números dentro de un rango específico. | `site:example.com numrange:1000-2000` | Encuentre páginas en Ejemplo.com que contienen números entre 1000 y 2000. |
| `allintext:` | Encuentra páginas que contienen todas las palabras especificadas en el texto del cuerpo. | `allintext:admin password reset` | Busque páginas que contengan tanto "administrador" como "restablecer la contraseña" en el texto del cuerpo. |
| `allinurl:` | Encuentra páginas que contienen todas las palabras especificadas en la URL. | `allinurl:admin panel` | Busque páginas con "Admin" y "Panel" en la URL. |
| `allintitle:` | Encuentra páginas que contienen todas las palabras especificadas en el título. | `allintitle:confidential report 2023` | Busque páginas con "confidencial", "informe" y "2023" en el título. |
| `AND` | Los resultados de la estrecha al requerir que todos los términos estén presentes. | `site:example.com AND (inurl:admin OR inurl:login)` | Encuentre páginas de administración o inicie sesión específicamente en Ejemplo.com. |
| `OR` | Amplía los resultados al incluir páginas con cualquiera de los términos. | `"linux" OR "ubuntu" OR "debian"` | Busque páginas web que mencionen Linux, Ubuntu o Debian. |
| `NOT` | Excluye los resultados que contienen el término especificado. | `site:bank.com NOT inurl:login` | Encuentre páginas en Bank.com excluyendo las páginas de inicio de sesión. |
| `*`(comodín) | Representa cualquier carácter o palabra. | `site:socialnetwork.com filetype:pdf user* manual` | Busque manuales del usuario (Guía del usuario, Manual del usuario) en formato PDF en SocialNetwork.com. |
| `..`(búsqueda de rango) | Encuentra resultados dentro de un rango numérico especificado. | `site:ecommerce.com "price" 100..500` | Busque productos con un precio de entre 100 y 500 en un sitio web de comercio electrónico. |
| `" "`(comillas) | Busca frases exactas. | `"information security policy"` | Encuentre documentos que mencionen la frase exacta "Política de seguridad de la información". |
| `-`(señal menos) | Excluye los términos de los resultados de búsqueda. | `site:news.com -inurl:sports` | Busque artículos de noticias en News.com excluyendo contenido relacionado con el deporte. |

### **Dorking de Google**

Google Dorking, también conocido como Google Hacking, es una técnica que aprovecha el poder de los operadores de búsqueda para descubrir información confidencial, vulnerabilidades de seguridad o contenido oculto en los sitios web, utilizando la búsqueda en Google.

Aquí hay algunos ejemplos comunes de Google Dorks, para más ejemplos, consulte el[Base de datos de piratería de Google](https://www.exploit-db.com/google-hacking-database):

- Encontrar páginas de inicio de sesión:
    - `site:example.com inurl:login`
    - `site:example.com (inurl:login OR inurl:admin)`
- Identificación de archivos expuestos:
    - `site:example.com filetype:pdf`
    - `site:example.com (filetype:xls OR filetype:docx)`
- Descubrimiento de archivos de configuración:
    - `site:example.com inurl:config.php`
    - `site:example.com (ext:conf OR ext:cnf)`(Búsqueda de extensiones comúnmente utilizadas para archivos de configuración)
- Localización de copias de seguridad de la base de datos:
    - `site:example.com inurl:backup`
    - `site:example.com filetype:sql`

[Anterior](https://academy.hackthebox.com/module/144/section/3079)