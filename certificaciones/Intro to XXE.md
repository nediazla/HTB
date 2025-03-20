# Intro to XXE

`XML External Entity (XXE) Injection`Las vulnerabilidades ocurren cuando los datos XML se toman de una entrada controlada por el usuario sin desinfectarlo o analizarlo de manera segura, lo que puede permitirnos usar características XML para realizar acciones maliciosas. Las vulnerabilidades XXE pueden causar daños considerables a una aplicación web y su servidor de fondo, desde divulgar archivos confidenciales hasta cerrar el servidor de fondo, por lo que se considera uno de los[Top 10 riesgos de seguridad web](https://owasp.org/www-project-top-ten/)por Owasp.

---

# **Xml**

`Extensible Markup Language (XML)`es un lenguaje de marcado común (similar a HTML y SGML) diseñado para la transferencia y almacenamiento flexibles de datos y documentos en varios tipos de aplicaciones. XML no se centra en mostrar datos, sino principalmente en almacenar los datos de los documentos y representar estructuras de datos. Los documentos XML están formados por árboles de elementos, donde cada elemento es esencialmente denotado por un`tag`, y el primer elemento se llama el`root element`, mientras que otros elementos son`child elements`.

Aquí vemos un ejemplo básico de un documento XML que representa una estructura de documento de correo electrónico:

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<email><date>01-01-2022</date><time>10:00 am UTC</time><sender>john@inlanefreight.com</sender><recipients><to>HR@inlanefreight.com</to><cc><to>billing@inlanefreight.com</to><to>payslips@inlanefreight.com</to></cc></recipients><body>
  Hello,
      Kindly share with me the invoice for the payment made on January 1, 2022.
  Regards,
  John
  </body></email>
```

El ejemplo anterior muestra algunos de los elementos clave de un documento XML, como:

| **Llave** | **Definición** | **Ejemplo** |
| --- | --- | --- |
| `Tag` | Las claves de un documento XML, generalmente envuelto con (`<`/`>`) caracteres. | `<date>` |
| `Entity` | Variables XML, generalmente envueltas con (`&`/`;`) caracteres. | `&lt;` |
| `Element` | El elemento raíz o cualquiera de sus elementos infantiles, y su valor se almacena entre una etiqueta de inicio y una etiqueta final. | `<date>01-01-2022</date>` |
| `Attribute` | Especificaciones opcionales para cualquier elemento almacenado en las etiquetas, que puede ser utilizada por el analizador XML. | `version="1.0"`/`encoding="UTF-8"` |
| `Declaration` | Por lo general, la primera línea de un documento XML, y define la versión XML y la codificación para usarlo al analizarlo. | `<?xml version="1.0" encoding="UTF-8"?>` |

Además, algunos caracteres se utilizan como parte de una estructura de documentos XML, como`<`, `>`, `&`, o`"`. Entonces, si necesitamos usarlos en un documento XML, debemos reemplazarlos con sus referencias de entidad correspondientes (por ejemplo,`&lt;`, `&gt;`, `&amp;`, `&quot;`). Finalmente, podemos escribir comentarios en los documentos XML entre`<!--`y`-->`, similar a los documentos HTML.

---

# **XML DTD**

`XML Document Type Definition (DTD)`Permite la validación de un documento XML contra una estructura de documento predefinida. La estructura del documento predefinida se puede definir en el documento mismo o en un archivo externo. El siguiente es un ejemplo de DTD para el documento XML que vimos anteriormente:

Código:xml

```xml
<!DOCTYPE email [
  <!ELEMENT email (date, time, sender, recipients, body)>
  <!ELEMENT recipients (to, cc?)>
  <!ELEMENT cc (to*)>
  <!ELEMENT date (#PCDATA)>
  <!ELEMENT time (#PCDATA)>
  <!ELEMENT sender (#PCDATA)>
  <!ELEMENT to  (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>

```

Como podemos ver, el DTD está declarando la raíz`email`elemento con el`ELEMENT`Tipo de declaración y luego denotando sus elementos infantiles. Después de eso, cada uno de los elementos infantiles también se declara, donde algunos de ellos también tienen elementos infantiles, mientras que otros solo pueden contener datos sin procesar (como se denota por`PCDATA`).

El DTD anterior se puede colocar dentro del documento XML en sí, justo después del`XML Declaration`en la primera línea. De lo contrario, se puede almacenar en un archivo externo (por ejemplo,`email.dtd`), y luego referenciado dentro del documento XML con el`SYSTEM`Palabra clave, como sigue:

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "email.dtd">

```

También es posible hacer referencia a un DTD a través de una URL, de la siguiente manera:

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "http://inlanefreight.com/email.dtd">

```

Esto es relativamente similar a cómo los documentos HTML definen y hacen referencia a los scripts de JavaScript y CSS.

---

# **Entidades XML**

También podemos definir entidades personalizadas (es decir, variables XML) en DTD XML, para permitir la refactorización de variables y reducir los datos repetitivos. Esto se puede hacer con el uso de la`ENTITY`Palabra clave, que es seguida por el nombre de la entidad y su valor, como sigue:

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>

```

Una vez que definimos una entidad, se puede hacer referencia en un documento XML entre un ampers y`&`y un semicolón`;`(p.ej`&company;`). Cada vez que se hace referencia a una entidad, el analizador XML la reemplazará con su valor. Lo más interesante, sin embargo, podemos`reference External XML Entities`con el`SYSTEM`Palabra clave, que es seguida por la ruta de la entidad externa, como sigue:

Código:xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>

```

**Nota:**También podemos usar el`PUBLIC`palabra clave en lugar de`SYSTEM`para cargar recursos externos, que se utiliza con entidades y estándares declarados públicamente, como un código de idioma (`lang="en"`). En este módulo, usaremos`SYSTEM`, pero deberíamos poder usar en la mayoría de los casos.

Esto funciona de manera similar a las entidades XML internas definidas dentro de los documentos. Cuando hacemos referencia a una entidad externa (por ejemplo`&signature;`), el analizador reemplazará la entidad con su valor almacenado en el archivo externo (por ejemplo,`signature.txt`). `When the XML file is parsed on the server-side, in cases like SOAP (XML) APIs or web forms, then an entity can reference a file stored on the back-end server, which may eventually be disclosed to us when we reference the entity`.

En la siguiente sección, veremos cómo podemos usar entidades XML externas para leer archivos locales o incluso realizar más acciones maliciosas.