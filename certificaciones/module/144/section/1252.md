# Subdomains

Al explorar registros DNS, nos hemos centrado principalmente en el dominio principal (p. ej., `example.com`) y su información asociada. Sin embargo, debajo de la superficie de este dominio primario se encuentra una red potencial de subdominios. Estos subdominios son extensiones del dominio principal, muchas veces creados para organizar y separar diferentes secciones o funcionalidades de un sitio web. Por ejemplo, una empresa podría utilizar `blog.example.com` por su blog, `shop.example.com` para su tienda online, o `mail.example.com` por sus servicios de correo electrónico.

# **¿Por qué es esto importante para el reconocimiento web?**

Los subdominios suelen albergar información y recursos valiosos que no están directamente vinculados desde el sitio web principal. Esto puede incluir:

- `Development and Staging Environments`: Las empresas suelen utilizar subdominios para probar nuevas funciones o actualizaciones antes de implementarlas en el sitio principal. Debido a las relajadas medidas de seguridad, estos entornos a veces contienen vulnerabilidades o exponen información confidencial.
- `Hidden Login Portals`: Los subdominios pueden albergar paneles administrativos u otras páginas de inicio de sesión que no están destinadas a ser accesibles públicamente. Los atacantes que buscan acceso no autorizado pueden encontrarlos como objetivos atractivos.
- `Legacy Applications`: Las aplicaciones web más antiguas y olvidadas pueden residir en subdominios y contener potencialmente software obsoleto con vulnerabilidades conocidas.
- `Sensitive Information`: Los subdominios pueden exponer inadvertidamente documentos confidenciales, datos internos o archivos de configuración que podrían ser valiosos para los atacantes.

# **Enumeración de subdominios**

`Subdomain enumeration`es el proceso de identificar y enumerar sistemáticamente estos subdominios. Desde una perspectiva de DNS, los subdominios suelen estar representados por `A` (o `AAAA` para IPv6), que asignan el nombre del subdominio a su dirección IP correspondiente. Además, `CNAME`Los registros se pueden utilizar para crear alias para subdominios, apuntándolos a otros dominios o subdominios. Hay dos enfoques principales para la enumeración de subdominios:

### **1. Enumeración de subdominios activos**

Esto implica interactuar directamente con los servidores DNS del dominio de destino para descubrir subdominios. Un método es intentar un `DNS zone transfer`, donde un servidor mal configurado podría filtrar sin darse cuenta una lista completa de subdominios. Sin embargo, debido a las estrictas medidas de seguridad, esto rara vez tiene éxito.

Una técnica activa más común es `brute-force enumeration`, que implica probar sistemáticamente una lista de posibles nombres de subdominios con respecto al dominio de destino. Herramientas como `dnsenum`, `ffuf`, y `gobuster` puede automatizar este proceso, utilizando listas de palabras de nombres de subdominios comunes o listas generadas de forma personalizada basadas en patrones específicos.

### **2. Enumeración pasiva de subdominios**

Esto se basa en fuentes externas de información para descubrir subdominios sin consultar directamente los servidores DNS del objetivo. Un recurso valioso es `Certificate Transparency (CT) logs`, repositorios públicos de certificados SSL/TLS. Estos certificados suelen incluir una lista de subdominios asociados en su campo Nombre alternativo del sujeto (SAN), lo que proporciona un tesoro de objetivos potenciales.

Otro enfoque pasivo implica utilizar `search engines` como Google o DuckDuckGo. Empleando operadores de búsqueda especializados (p. ej.,`site:`), puede filtrar los resultados para mostrar solo los subdominios relacionados con el dominio de destino.

Además, varias bases de datos y herramientas en línea agregan datos DNS de múltiples fuentes, lo que le permite buscar subdominios sin interactuar directamente con el objetivo.

Cada uno de estos métodos tiene sus fortalezas y debilidades. La enumeración activa ofrece más control y potencial para un descubrimiento integral, pero puede ser más detectable. La enumeración pasiva es más sigilosa pero es posible que no descubra todos los subdominios existentes. La combinación de ambos enfoques proporciona una estrategia de enumeración de subdominios más exhaustiva y eficaz.