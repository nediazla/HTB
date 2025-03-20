# Introduction

Muchos idiomas modernos de fondo, como`PHP`, `Javascript`, o`Java`, use los parámetros HTTP para especificar lo que se muestra en la página web, que permite la creación de páginas web dinámicas, reduce el tamaño general del script y simplifica el código. En tales casos, los parámetros se utilizan para especificar qué recurso se muestra en la página. Si tales funcionalidades no están codificadas de forma segura, un atacante puede manipular estos parámetros para mostrar el contenido de cualquier archivo local en el servidor de alojamiento, lo que lleva a un[Inclusión de archivos locales (LFI)](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)vulnerabilidad.

---

# **Inclusión de archivos locales (LFI)**

El lugar más común en el que generalmente encontramos LFI dentro son motores de plantilla. Para que la mayoría de la aplicación web se vea igual cuando se navegue entre páginas, un motor de plantilla muestra una página que muestra las partes estáticas comunes, como la`header`, `navigation bar`, y`footer`, y luego carga dinámicamente otro contenido que cambia entre páginas. De lo contrario, cada página del servidor debería modificarse cuando se realicen cambios en cualquiera de las partes estáticas. Por eso a menudo vemos un parámetro como`/index.php?page=about`, dónde`index.php`Establece contenido estático (eg encabezado/pie de página), y luego solo extrae el contenido dinámico especificado en el parámetro, que en este caso se puede leer desde un archivo llamado`about.php`. Como tenemos control sobre el`about`Porción de la solicitud, es posible que la aplicación web tome otros archivos y los muestre en la página.

Las vulnerabilidades de LFI pueden conducir a la divulgación del código fuente, la exposición a los datos confidenciales e incluso la ejecución del código remoto bajo ciertas condiciones. Fugas del código fuente puede permitir a los atacantes probar el código para otras vulnerabilidades, lo que puede revelar vulnerabilidades previamente desconocidas. Además, la fuga de datos confidenciales puede permitir a los atacantes enumerar el servidor remoto para otras debilidades o incluso fugas credenciales y claves que pueden permitirles acceder al servidor remoto directamente. En condiciones específicas, LFI también puede permitir a los atacantes ejecutar código en el servidor remoto, lo que puede comprometer todo el servidor de fondo y cualquier otro servidor conectado a él.

---

# **Ejemplos de código vulnerable**

Veamos algunos ejemplos de código vulnerables a la inclusión de archivos para comprender cómo ocurren tales vulnerabilidades. Como se mencionó anteriormente, las vulnerabilidades de inclusión de archivos pueden ocurrir en muchos de los servidores web y marcos de desarrollo más populares, como`PHP`, `NodeJS`, `Java`, `.Net`, y muchos otros. Cada uno de ellos tiene un enfoque ligeramente diferente para incluir archivos locales, pero todos comparten una cosa común: cargar un archivo desde una ruta especificada.

Tal archivo podría ser un encabezado dinámico o contenido diferente basado en el idioma especificado por el usuario. Por ejemplo, la página puede tener un`?language`Obtenga el parámetro, y si un usuario cambia el idioma desde un menú desplegable, entonces se devolvería la misma página pero con una diferente`language`parámetro (por ejemplo`?language=es`). En tales casos, cambiar el idioma puede cambiar el directorio que la aplicación web está cargando las páginas desde (por ejemplo,`/en/`o`/es/`). Si tenemos control sobre la ruta que se está cargando, entonces podemos explotar esta vulnerabilidad para leer otros archivos y potencialmente alcanzar la ejecución del código remoto.

### **Php**

En`PHP`, podemos usar el`include()`Funciona para cargar un archivo local o remoto a medida que cargamos una página. Si el`path`pasó a la`include()`se toma de un parámetro controlado por el usuario, como un`GET`parámetro y`the code does not explicitly filter and sanitize the user input`, entonces el código se vuelve vulnerable a la inclusión de archivos. El siguiente fragmento de código muestra un ejemplo de eso:

Código:php

```php
if (isset($_GET['language'])) {
    include($_GET['language']);
}

```

Vemos que el`language`El parámetro se pasa directamente al`include()`función. Entonces, cualquier camino que pasamos en el`language`El parámetro se cargará en la página, incluidos los archivos locales en el servidor de fondo. Esto no es exclusivo para el`include()`Función, ya que hay muchas otras funciones de PHP que conducirían a la misma vulnerabilidad si tuviéramos control sobre el camino que se le pasó a ellas. Tales funciones incluyen`include_once()`, `require()`, `require_once()`, `file_get_contents()`, y varios otros también.

**Nota:**En este módulo, nos centraremos principalmente en aplicaciones web de PHP que se ejecutan en un servidor de back-end de Linux. Sin embargo, la mayoría de las técnicas y ataques funcionarían en la mayoría de otros marcos, por lo que nuestros ejemplos serían los mismos con una aplicación web escrita en cualquier otro idioma.

### **Nodejs**

Al igual que el caso con PHP, los servidores web NodeJS también pueden cargar contenido en función de los parámetros HTTP. El siguiente es un ejemplo básico de cómo un parámetro Get`language`se usa para controlar qué datos se escriben en una página:

Código:javascript

```jsx
if(req.query.language) {
    fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
        res.write(data);
    });
}

```

Como podemos ver, cualquier parámetro que pase de la URL sea utilizado por el`readfile`función, que luego escribe el contenido del archivo en la respuesta HTTP. Otro ejemplo es el`render()`función en el`Express.js`estructura. El siguiente ejemplo muestra cómo el`language`El parámetro se utiliza para determinar qué directorio extraer el`about.html`Página de:

Código:js

```
app.get("/about/:language", function(req, res) {
    res.render(`/${req.params.language}/about.html`);
});

```

A diferencia de nuestros ejemplos anteriores donde se especificaron los parámetros GET después de un (`?`) Carácter en la URL, el ejemplo anterior toma el parámetro de la ruta de la URL (por ejemplo,`/about/en`o`/about/es`). Ya que el parámetro se usa directamente dentro del`render()`Función Para especificar el archivo renderizado, podemos cambiar la URL para mostrar un archivo diferente.

### **Java**

El mismo concepto se aplica a muchos otros servidores web. Los siguientes ejemplos muestran cómo las aplicaciones web para un servidor web Java pueden incluir archivos locales basados en el parámetro especificado, utilizando el`include`función:

Código:JSP

```
<c:if test="${not empty param.language}">
    <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>

```

El`include`La función puede tomar un archivo o una URL de página como su argumento y luego convertir el objeto en la plantilla frontal, similar a las que vimos anteriormente con NodeJS. El`import`La función también se puede usar para representar un archivo local o una URL, como el siguiente ejemplo:

Código:JSP

```
<c:import url= "<%= request.getParameter('language') %>"/>

```

### **.NETO**

Finalmente, tomemos un ejemplo de cómo pueden ocurrir vulnerabilidades de inclusión de archivos en aplicaciones web .NET. El`Response.WriteFile`La función funciona de manera muy similar a todos nuestros ejemplos anteriores, ya que toma una ruta de archivo para su entrada y escribe su contenido a la respuesta. La ruta se puede recuperar de un parámetro GET para la carga de contenido dinámico, de la siguiente manera:

Código:CS

```
@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
    <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %>
}

```

Además, el`@Html.Partial()`La función también se puede utilizar para representar el archivo especificado como parte de la plantilla frontal, de manera similar a lo que vimos anteriormente:

Código:CS

```
@Html.Partial(HttpContext.Request.Query['language'])

```

Finalmente, el`include`La función puede usarse para representar archivos locales o URL remotas, y también puede ejecutar los archivos especificados:

Código:CS

```
<!--#include file="<% HttpContext.Request.Query['language'] %>"-->

```

# **Leer vs ejecutar**

De todos los ejemplos anteriores, podemos ver que las vulnerabilidades de inclusión de archivos pueden ocurrir en cualquier servidor web y cualquier marcado de desarrollo, ya que todos ellos proporcionan funcionalidades para cargar contenido dinámico y manejar plantillas frontales.

Lo más importante a tener en cuenta es que`some of the above functions only read the content of the specified files, while others also execute the specified files`. Además, algunos de ellos permiten especificar URL remotas, mientras que otras solo trabajan con archivos locales en el servidor de back-end.

La siguiente tabla muestra qué funciones pueden ejecutar archivos y cuáles solo leen contenido del archivo:

| **Función** | **Leer contenido** | **Ejecutar** | **URL remota** |
| --- | --- | --- | --- |
| **Php** |  |  |  |
| `include()`/`include_once()` | ✅ | ✅ | ✅ |
| `require()`/`require_once()` | ✅ | ✅ | ❌ |
| `file_get_contents()` | ✅ | ❌ | ✅ |
| `fopen()`/`file()` | ✅ | ❌ | ❌ |
| **Nodejs** |  |  |  |
| `fs.readFile()` | ✅ | ❌ | ❌ |
| `fs.sendFile()` | ✅ | ❌ | ❌ |
| `res.render()` | ✅ | ✅ | ❌ |
| **Java** |  |  |  |
| `include` | ✅ | ❌ | ❌ |
| `import` | ✅ | ✅ | ✅ |
| **.NETO** |  |  |  |
| `@Html.Partial()` | ✅ | ❌ | ❌ |
| `@Html.RemotePartial()` | ✅ | ❌ | ✅ |
| `Response.WriteFile()` | ✅ | ❌ | ❌ |
| `include` | ✅ | ✅ | ✅ |

Esta es una diferencia significativa a la observación, ya que la ejecución de archivos puede permitirnos ejecutar funciones y eventualmente conducir a la ejecución del código, mientras que solo leer el contenido del archivo solo nos permitiría leer el código fuente sin la ejecución del código. Además, si tuviéramos acceso al código fuente en un ejercicio Whitebox o en una auditoría de código, saber estas acciones nos ayuda a identificar posibles vulnerabilidades de inclusión de archivos, especialmente si tuvieron una entrada controlada por el usuario.

En todos los casos, las vulnerabilidades de inclusión de archivos son críticas y eventualmente pueden conducir a comprometer todo el servidor de fondo. Incluso si solo pudiéramos leer el código fuente de la aplicación web, aún puede permitirnos comprometer la aplicación web, ya que puede revelar otras vulnerabilidades como se mencionó anteriormente, y el código fuente también puede contener claves de bases de datos, credenciales de administración u otra información confidencial.