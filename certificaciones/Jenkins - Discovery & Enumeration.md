# Jenkins - Discovery & Enumeration

[Jenkins](https://www.jenkins.io/)es un servidor de automatización de código abierto escrito en Java que ayuda a los desarrolladores a construir y probar sus proyectos de software continuamente. Es un sistema basado en servidor que se ejecuta en contenedores de servlet como Tomcat. A lo largo de los años, los investigadores han descubierto varias vulnerabilidades en Jenkins, incluidas algunas que permiten la ejecución de código remoto sin requerir autenticación. Jenkins es un[integración continua](https://en.wikipedia.org/wiki/Continuous_integration)servidor. Aquí hay algunos puntos interesantes sobre Jenkins:

- Jenkins fue nombrado originalmente Hudson (lanzado en 2005) y pasó a llamarse en 2011 después de una disputa con Oracle
- [Datos](https://discovery.hgdata.com/product/jenkins)muestra que más de 86,000 empresas usan Jenkins
- Jenkins es utilizado por compañías conocidas como Facebook, Netflix, Udemy, Robinhood y LinkedIn
- Tiene más de 300 complementos para admitir proyectos de construcción y prueba.

---

# **Descubrimiento/huella**

Supongamos que estamos trabajando en una prueba de penetración interna y hemos completado nuestros escaneos de descubrimiento web. Notamos que lo que creemos es una instancia de Jenkins y sabemos que a menudo se instala en los servidores de Windows que se ejecutan como la cuenta de sistema todopoderoso. Si podemos obtener acceso a través de Jenkins y obtener la ejecución del código remoto como cuenta del sistema, tendríamos un punto de apoyo en Active Directory para comenzar la enumeración del entorno de dominio.

Jenkins se ejecuta en el puerto Tomcat 8080 de forma predeterminada. También utiliza el puerto 5000 para adjuntar servidores de esclavos. Este puerto se utiliza para comunicarse entre maestros y esclavos. Jenkins puede usar una base de datos local, LDAP, base de datos de usuarios de UNIX, delegar seguridad en un contenedor de servlet o no usar autenticación en absoluto. Los administradores también pueden permitir que los usuarios creen cuentas.

---

# **Enumeración**

![](https://academy.hackthebox.com/storage/modules/113/jenkins_global_security.png)

La instalación predeterminada generalmente usa la base de datos de Jenkins para almacenar credenciales y no permite a los usuarios registrar una cuenta. Podemos hacer huellas digitales Jenkins rápidamente en la página de inicio de sesión de Telltale.

![](https://academy.hackthebox.com/storage/modules/113/jenkins_login.png)

Podemos encontrar una instancia de Jenkins que usa credenciales débiles o predeterminadas como`admin:admin`o no tiene ningún tipo de autenticación habilitado. No es raro encontrar instancias de Jenkins que no requieran ninguna autenticación durante una prueba de penetración interna. Si bien es raros, nos hemos encontrado con Jenkins durante las pruebas de penetración externa que pudimos atacar.