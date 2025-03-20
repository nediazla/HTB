# XXE Prevention

Hemos visto que las vulnerabilidades XXE ocurren principalmente cuando una entrada XML insegura hace referencia a una entidad externa, que finalmente se explota para leer archivos confidenciales y realizar otras acciones. Prevenir las vulnerabilidades XXE es relativamente más fácil que prevenir otras vulnerabilidades web, ya que son causadas principalmente por bibliotecas XML obsoletas.

---

# **Evitar componentes obsoletos**

Si bien otras vulnerabilidades web de validación de entrada generalmente se evitan a través de prácticas de codificación seguras (p. Ej., XSS, IDOR, SQLI, inyección de OS), esto no es completamente necesario para evitar las vulnerabilidades XXE. Esto se debe a que los desarrolladores de la web generalmente no manejan la entrada XML, sino por las bibliotecas XML incorporadas. Entonces, si una aplicación web es vulnerable a XXE, esto es muy probable que se deba a una biblioteca XML obsoleta que analiza los datos XML.

Por ejemplo, PHP's[libxml_disable_entity_loader](https://www.php.net/manual/en/function.libxml-disable-entity-loader.php)La función está en desuso, ya que permite que un desarrollador habilite entidades externas de manera insegura, lo que conduce a XXE vulnerabilidades. Si visitamos la documentación de PHP para esta función, vemos la siguiente advertencia:

**Advertencia**

Esta función ha sido*Desapercibido*A partir de PHP 8.0.0. Confiar en esta función está muy desanimado.

Además, incluso los editores de código comunes (por ejemplo, VSCODE) resaltarán que esta función específica está en desuso y nos advertirá no lo use:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_deprecated_warning.jpg)

**Nota:**Puede encontrar un informe detallado de todas las bibliotecas XML vulnerables, con recomendaciones sobre la actualización y el uso de funciones seguras, en[Hoja de trucos de prevención XXE de OWASP](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html#php).

Además de actualizar las bibliotecas XML, también debemos actualizar cualquier componente que analice la entrada XML, como bibliotecas API como SOAP. Además, cualquier documento o procesador de archivos que pueda realizar un análisis XML, como procesadores de imágenes SVG o procesadores de documentos PDF, también puede ser vulnerable a las vulnerabilidades XXE, y debemos actualizarlos también.

Estos problemas no son exclusivos solo de las bibliotecas XML, ya que lo mismo se aplica a todos los demás componentes web (por ejemplo, anticuado`Node Modules`). Además de los administradores de paquetes comunes (por ejemplo,`npm`), los editores de código comunes notificarán a los desarrolladores web sobre el uso de componentes obsoletos y sugerirán otras alternativas. Al final,`using the latest XML libraries and web development components can greatly help reduce various web vulnerabilities`, incluyendo xxe.

---

# **Uso de configuraciones XML seguras**

Además de usar las últimas bibliotecas XML, ciertas configuraciones XML para aplicaciones web pueden ayudar a reducir la posibilidad de explotación XXE. Estos incluyen:

- Deshabilitar la referencia personalizada`Document Type Definitions (DTDs)`
- Deshabilitar la referencia`External XML Entities`
- Desactivar`Parameter Entity`tratamiento
- Deshabilitar el apoyo para`XInclude`
- Prevenir`Entity Reference Loops`

Otra cosa que vimos fue la explotación XXE basada en errores. Por lo tanto, siempre debemos tener un manejo de excepción adecuado en nuestras aplicaciones web y`should always disable displaying runtime errors in web servers`.

Dichas configuraciones deben ser otra capa de protección si perdemos la actualización de algunas bibliotecas XML y también deberíamos evitar la explotación XXE. Sin embargo, aún podemos estar utilizando bibliotecas vulnerables en tales casos y solo aplicando soluciones contra la explotación, lo que no es ideal.

Con los diversos problemas y vulnerabilidades introducidas por los datos XML, muchos también recomiendan`using other formats, such as JSON or YAML`. Esto también incluye evitar los estándares API que se basan en XML (por ejemplo, SOAP) y el uso de API basadas en JSON (por ejemplo, REST).

Finalmente, el uso de firewalls de aplicaciones web (WAFS) es otra capa de protección contra la explotación XXE. Sin embargo, nunca debemos confiar completamente en WAFS y dejar el back-end vulnerable, ya que los WAF siempre se pueden pasar por alto.