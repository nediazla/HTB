# Recursos de nubes

El uso de nubes, como[AWS](https://aws.amazon.com/), [GCP](https://cloud.google.com/), [Azur](https://azure.microsoft.com/en-us/), y otros, ahora es uno de los componentes esenciales para muchas empresas hoy en día. Después de todo, todas las empresas quieren poder hacer su trabajo desde cualquier lugar, por lo que necesitan un punto central para toda la gerencia. Por eso los servicios de`Amazon` (`AWS`), `Google` (`GCP`), y`Microsoft` (`Azure`) son ideales para este propósito.

Aunque los proveedores de la nube aseguran su infraestructura centralmente, esto no significa que las empresas estén libres de vulnerabilidades. Sin embargo, las configuraciones realizadas por los administradores pueden hacer que los recursos en la nube de la compañía sean vulnerables. Esto a menudo comienza con el`S3 buckets`(AWS),`blobs`(Azur),`cloud storage`(GCP), al que se puede acceder sin autenticación si se configuran incorrectamente.

### **Servidores alojados de la empresa**

Recursos de nubes

```
xnoxos@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;doneblog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250

```

A menudo, el almacenamiento en la nube se agrega a la lista DNS cuando otros empleados lo usan para fines administrativos. Este paso hace que sea mucho más fácil para los empleados alcanzarlos y administrarlos. Permanemos con el caso de que una empresa nos haya contratado, y durante la búsqueda de IP, ya hemos visto que una dirección IP pertenece a la`s3-website-us-west-2.amazonaws.com`servidor.

Sin embargo, hay muchas formas diferentes de encontrar dicho almacenamiento en la nube. Una de las más fáciles y utilizadas es la búsqueda de Google combinada con Google Dorks. Por ejemplo, podemos usar Google Dorks`inurl:`y`intext:`para reducir nuestra búsqueda a términos específicos. En el siguiente ejemplo, vemos áreas rojas censuradas que contienen el nombre de la empresa.

### **Búsqueda de Google para AWS**

![](https://academy.hackthebox.com/storage/modules/112/gsearch1.png)

### **Buscar en Google Azure**

![](https://academy.hackthebox.com/storage/modules/112/gsearch2.png)

Aquí ya podemos ver que los enlaces presentados por Google contienen PDF. Cuando buscamos una empresa que ya sepamos o deseemos saber, también nos encontraremos con otros archivos, como documentos de texto, presentaciones, códigos y muchos otros.

Dicho contenido también se incluye a menudo en el código fuente de las páginas web, desde donde se cargan las imágenes, los códigos JavaScript o el CSS. Este procedimiento a menudo alivia el servidor web y no almacena contenido innecesario.

### **Sitio web de destino - Código fuente**

![](https://academy.hackthebox.com/storage/modules/112/cloud3.png)

Proveedores de terceros como[dominio. Glass](https://domain.glass/)También puede contarnos mucho sobre la infraestructura de la compañía. Como efecto secundario positivo, también podemos ver que el estado de evaluación de seguridad de Cloudflare se ha clasificado como "seguro". Esto significa que ya hemos encontrado una medida de seguridad que se puede observar para la segunda capa (puerta de enlace).

### **Dominio. Resultados de Glass**

![](https://academy.hackthebox.com/storage/modules/112/cloud1.png)

Otro proveedor muy útil es[Grayhatwarfare](https://buckets.grayhatwarfare.com/). Podemos hacer muchas búsquedas diferentes, descubrir AWS, Azure y GCP Cloud Storage, e incluso clasificar y filtrar por formato de archivo. Por lo tanto, una vez que los hemos encontrado a través de Google, también podemos buscarlos en Grayhatwarefare y descubrir pasivamente qué archivos se almacenan en el almacenamiento en la nube dado.

### **Resultados de Grayhatwarfare**

![](https://academy.hackthebox.com/storage/modules/112/cloud2.png)

Muchas compañías también utilizan abreviaturas del nombre de la compañía, que luego se utilizan en consecuencia dentro de la infraestructura de TI. Dichos términos también son parte de un excelente enfoque para descubrir el nuevo almacenamiento en la nube de la empresa. También podemos buscar archivos simultáneamente para ver los archivos a los que se puede acceder al mismo tiempo.

### **Llaves de SSH privadas y públicas filtradas**

![](https://academy.hackthebox.com/storage/modules/112/ghw1.png)

A veces, cuando los empleados están sobrecargados de trabajo o bajo alta presión, los errores pueden ser fatales para toda la empresa. Estos errores incluso pueden conducir a las claves privadas de SSH, que cualquiera puede descargar e iniciar sesión en una o incluso más máquinas en la empresa sin usar una contraseña.

### **Clave privada SSH**

![](https://academy.hackthebox.com/storage/modules/112/ghw2.png)

[Anterior](https://academy.hackthebox.com/module/112/section/1061) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/112/section/1065)

Hoja de trucosRecursos