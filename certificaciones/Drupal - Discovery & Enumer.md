# Drupal - Discovery & Enumeration

[Drupal](https://www.drupal.org/), lanzado en 2001 es el tercer y último CMS que cubriremos en nuestro recorrido por el mundo de las aplicaciones comunes. Drupal es otro CMS de código abierto que es popular entre las empresas y los desarrolladores. Drupal está escrito en PHP y admite el uso de MySQL o PostgreSQL para el backend. Además, SQLite se puede usar si no hay DBMS instalados. Al igual que WordPress, Drupal permite a los usuarios mejorar sus sitios web mediante el uso de temas y módulos. Al momento de escribir este artículo, el Proyecto Drupal tiene casi 43,000 módulos y 2,900 temas y es el tercer CMS más popular por cuota de mercado. Aquí hay algunos interesantes[estadística](https://websitebuilder.org/blog/drupal-statistics/)En Drupal reunido de varias fuentes:

- Alrededor del 1.5% de los sitios en Internet ejecutan Drupal (¡más de 1.1 millones de sitios!), El 5% de los 1 millón de sitios web principales en Internet y el 7% de los 10,000 principales sitios
- Drupal representa alrededor del 2.4% del mercado de CMS
- Está disponible en 100 idiomas
- Drupal está orientado a la comunidad y tiene más de 1.3 millones de miembros
- Drupal 8 fue construido por 3.290 contribuyentes, 1,288 empresas y ayuda de la comunidad
- 33 de las compañías Fortune 500 usan Drupal de alguna manera
- El 56% de los sitios web gubernamentales en todo el mundo usan Drupal
- El 23.8% de las universidades, colegios y escuelas usan Drupal en todo el mundo
- Algunas marcas importantes que usan Drupal incluyen: Tesla y Warner Bros Records

Según el Drupal[sitio web](https://www.drupal.org/project/usage/drupal)Hay poco 950,000 instancias de Drupal en uso al momento de escribir (distribuido desde la versión 5.x a la versión 9.3.x, al 5 de septiembre de 2021). Como podemos ver en estas estadísticas, el uso de Drupal se ha mantenido constantemente entre 900,000 y 1.1 millones de instancias entre junio de 2013 y septiembre de 2021. Estas estadísticas no tienen en cuenta`EVERY`instancia de drupal en uso en todo el mundo, pero más bien instancias que ejecutan el[Estado de actualización](https://www.drupal.org/project/update_status)Módulo, que se registra con Drupal.org Daily para buscar nuevas versiones de Drupal o actualizaciones a los módulos en uso.

---

# **Discovery/Footprinting**

Durante una prueba de penetración externa, encontramos lo que parece ser un CMS, pero sabemos por una revisión superficial que el sitio no está ejecutando WordPress o Joomla. Sabemos que los CMS a menudo son objetivos "jugosos", así que vamos a profundizar en este y ver qué podemos descubrir.

Se puede identificar un sitio web de Drupal de varias maneras, incluso por el mensaje de encabezado o pie de página`Powered by Drupal`, el logotipo de Drupal estándar, la presencia de un`CHANGELOG.txt`archivo o`README.txt file`, a través de la fuente de página, o pistas en el archivo robots.txt, como referencias a`/node`.

Drupal - Discovery & Enumeration

```
xnoxos@htb[/htb]$ curl -s http://drupal.inlanefreight.local | grep Drupal<meta name="Generator" content="Drupal 8 (https://www.drupal.org)" />
      <span>Powered by <a href="https://www.drupal.org">Drupal</a></span>

```

Otra forma de identificar Drupal CMS es a través de[nodos](https://www.drupal.org/docs/8/core/modules/node/about-nodes). Drupal indexa su contenido usando nodos. Un nodo puede contener cualquier cosa como una publicación de blog, encuesta, artículo, etc. Los Uris de la página suelen ser del formulario`/node/<nodeid>`.

![](https://academy.hackthebox.com/storage/modules/113/drupal_node.png)

Por ejemplo, se encuentra que la publicación de blog anterior está en`/node/1`. Esta representación es útil para identificar un sitio web de Drupal cuando se usa un tema personalizado.

Nota: No todas las instalaciones de Drupal se verán igual o mostrarán la página de inicio de sesión o incluso permitirán a los usuarios acceder a la página de inicio de sesión desde Internet.

Drupal admite tres tipos de usuarios de forma predeterminada:

1. `Administrator`: Este usuario tiene control completo sobre el sitio web de Drupal.
2. `Authenticated User`: Estos usuarios pueden iniciar sesión en el sitio web y realizar operaciones como agregar y editar artículos basados en sus permisos.
3. `Anonymous`: Todos los visitantes del sitio web son designados como anónimos. Por defecto, estos usuarios solo pueden leer publicaciones.

---

# **Enumeración**

Una vez que hemos descubierto una instancia de Drupal, podemos hacer una combinación de enumeración manual y basada en herramientas (automatizadas) para descubrir la versión, los complementos instalados y más. Dependiendo de la versión Drupal y cualquier medida de endurecimiento que se haya establecido, es posible que necesitemos probar varias formas de identificar el número de versión. Instalaciones más recientes de Drupal de acceso por bloque predeterminado al`CHANGELOG.txt`y`README.txt`archivos, por lo que es posible que necesitemos hacer una enumeración adicional. Veamos un ejemplo de enumerar el número de versión utilizando el`CHANGELOG.txt`archivo. Para hacerlo, podemos usar`cURL`junto con`grep`, `sed`, `head`, etc.

Drupal - Discovery & Enumeration

```
xnoxos@htb[/htb]$ curl -s http://drupal-acc.inlanefreight.local/CHANGELOG.txt | grep -m2 ""Drupal 7.57, 2018-02-21

```

Aquí hemos identificado una versión anterior de Drupal en uso. Probando esto contra la última versión de Drupal al momento de escribir, obtenemos una respuesta 404.

Drupal - Discovery & Enumeration

```
xnoxos@htb[/htb]$ curl -s http://drupal.inlanefreight.local/CHANGELOG.txt<!DOCTYPE html><html><head><title>404 Not Found</title></head><body><h1>Not Found</h1><p>The requested URL "http://drupal.inlanefreight.local/CHANGELOG.txt" was not found on this server.</p></body></html>

```

Hay varias otras cosas que podríamos verificar en esta instancia para identificar la versión. Probemos un escaneo con`droopescan`Como se muestra en la sección de enumeración de Joomla.`Droopescan`Tiene mucha más funcionalidad para Drupal que para Joomla.

Ejecutemos un escaneo contra el`http://drupal.inlanefreight.local`anfitrión.

Drupal - Discovery & Enumeration

```
xnoxos@htb[/htb]$ droopescan scan drupal -u http://drupal.inlanefreight.local[+] Plugins found:
    php http://drupal.inlanefreight.local/modules/php/
        http://drupal.inlanefreight.local/modules/php/LICENSE.txt

[+] No themes found.

[+] Possible version(s):
    8.9.0
    8.9.1

[+] Possible interesting urls found:
    Default admin - http://drupal.inlanefreight.local/user/login

[+] Scan finished (0:03:19.199526 elapsed)

```

Esta instancia parece estar ejecutando la versión`8.9.1`de Drupal. Al momento de escribir, este no fue lo último, ya que se lanzó en junio de 2020. Una búsqueda rápida de Drupal relacionado con Drupal[vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-1367/product_id-2387/Drupal-Drupal.html)No muestra nada aparente para esta versión central de Drupal. En este caso, luego querríamos ver complementos instalados o abusar de la funcionalidad incorporada.