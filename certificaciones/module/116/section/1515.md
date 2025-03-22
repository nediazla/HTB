# Finding Sensitive Information

Al atacar un servicio, generalmente desempeñamos un papel de detective, y necesitamos recopilar tanta información como sea posible y observar cuidadosamente los detalles. Por lo tanto, cada información es esencial.

Imaginemos que estamos en un compromiso con un cliente, estamos dirigidos a correo electrónico, FTP, bases de datos y almacenamiento, y nuestro objetivo es obtener la ejecución de código remoto (RCE) en cualquiera de estos servicios. Comenzamos la enumeración y probamos el acceso anónimo a todos los servicios, y solo FTP tiene acceso anónimo. Encontramos un archivo vacío dentro del servicio FTP, pero con el nombre`johnsmith`, probamos`johnsmith`Como el usuario y la contraseña de FTP, pero no funcionó. Intentamos lo mismo con el servicio de correo electrónico, y iniciamos sesión con éxito. Con el acceso al correo electrónico, comenzamos a buscar correos electrónicos que contengan la palabra`password`, encontramos muchos, pero uno de ellos contiene las credenciales de John para la base de datos MSSQL. Accedemos a la base de datos y usamos la funcionalidad incorporada para ejecutar comandos y obtener con éxito RCE en el servidor de la base de datos. Cumplimos con éxito nuestro objetivo.

Un servicio mal configurado nos permite acceder a una información que inicialmente puede parecer insignificante,`johnsmith`, pero esa información nos abrió las puertas para que descubramos más información y finalmente obtenga la ejecución del código remoto en el servidor de la base de datos. Esta es la importancia de prestar atención a cada información, cada detalle, a medida que enumeramos y atacamos los servicios comunes.

La información confidencial puede incluir, pero no se limita a:

- Nombres de usuario.
- Direcciones de correo electrónico.
- Contraseñas.
- DNS Records.
- Direcciones IP.
- Código fuente.
- Archivos de configuración.
- Pii.

Este módulo cubrirá algunos servicios comunes donde podemos encontrar información interesante y descubrir diferentes métodos y herramientas que podemos usar para automatizar nuestro proceso de descubrimiento. Estos servicios incluyen:

- Archivar acciones.
- Correo electrónico.
- Bases de datos.

---

### **Comprensión de lo que tenemos que buscar**

Cada objetivo es único, y necesitamos familiarizarnos con nuestro objetivo, sus procesos, procedimientos, modelo de negocio y propósito. Una vez que entendemos nuestro objetivo, podemos pensar qué información es esencial para ellos y qué tipo de información es útil para nuestro ataque.

Hay dos elementos clave para encontrar información confidencial:

1. Necesitamos entender el servicio y cómo funciona.
2. Necesitamos saber lo que estamos buscando.