# Introduction to Web Attacks

Como las aplicaciones web se están volviendo muy comunes y se utilizan para la mayoría de las empresas, la importancia de protegerlas contra ataques maliciosos también se vuelve más crítica. A medida que las aplicaciones web modernas se vuelven más complejas y avanzadas, también lo hacen los tipos de ataques utilizados contra ellas. Esto lleva a una amplia superficie de ataque para la mayoría de las empresas hoy, por lo que los ataques web son los tipos más comunes de ataques contra las empresas. Proteger las aplicaciones web se está convirtiendo en una de las principales prioridades para cualquier departamento de TI.

Atacar las aplicaciones web orientadas externas puede resultar en un compromiso de la red interna de las empresas, lo que eventualmente puede conducir a activos robados o servicios interrumpidos. Potencialmente puede causar un desastre financiero para la empresa. Incluso si una empresa no tiene aplicaciones web que enfrentan externas, probablemente utilice aplicaciones web internas, o puntos finales de API externos, los cuales son vulnerables a los mismos tipos de ataques y pueden aprovecharse para lograr los mismos objetivos.

Mientras que otros módulos de la Academia HTB cubrieron varios temas sobre aplicaciones web y varios tipos de técnicas de explotación web, en este módulo, cubriremos otros tres ataques web que se pueden encontrar en cualquier aplicación web, lo que puede conducir a un compromiso. Discutiremos cómo detectar, explotar y evitar cada uno de estos tres ataques.

---

# **Ataques web**

### **Manipulación de verbos http**

El primer ataque web discutido en este módulo es[Manipulación de verbos http](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/03-Testing_for_HTTP_Verb_Tampering). Un ataque de manipulación de verbos HTTP explota servidores web que aceptan muchos verbos y métodos HTTP. Esto puede explotarse enviando solicitudes maliciosas utilizando métodos inesperados, lo que puede conducir a evitar el mecanismo de autorización de la aplicación web o incluso evitar sus controles de seguridad contra otros ataques web. Los ataques de manipulación de verbos HTTP son uno de los muchos otros ataques HTTP que pueden usarse para explotar las configuraciones del servidor web enviando solicitudes HTTP maliciosas.

### **Referencias de objetos directos inseguros (IDOR)**

El segundo ataque discutido en este módulo es[Referencias de objetos directos inseguros (IDOR)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References). IDOR se encuentra entre las vulnerabilidades web más comunes y puede llevar a acceder a datos a los que los atacantes no deben acceder a los atacantes. Lo que hace que este ataque sea muy común es esencialmente la falta de un sistema de control de acceso sólido en el back-end. A medida que las aplicaciones web almacenan los archivos e información de los usuarios, pueden usar números secuenciales o ID de usuario para identificar cada elemento. Supongamos que la aplicación web carece de un mecanismo de control de acceso sólido y expone referencias directas a archivos y recursos. En ese caso, podemos acceder a los archivos e información de otros usuarios simplemente adivinando o calculando sus ID de archivo.

### **Inyección de entidad externa XML (xxe)**

El tercer y último ataque web que discutiremos es[Inyección de entidad externa XML (xxe)](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing). Muchas aplicaciones web procesan datos XML como parte de su funcionalidad. Supongamos que una aplicación web utiliza bibliotecas XML obsoletas para analizar y procesar datos de entrada XML del usuario front-end. En ese caso, puede ser posible enviar datos XML maliciosos para divulgar archivos locales almacenados en el servidor de fondo. Estos archivos pueden ser archivos de configuración que pueden contener información confidencial como contraseñas o incluso el código fuente de la aplicación web, lo que nos permitiría realizar una prueba de penetración de Whitebox en la aplicación web para identificar más vulnerabilidades. Los ataques XXE incluso se pueden aprovechar para robar las credenciales del servidor de alojamiento, lo que comprometería todo el servidor y permitiría la ejecución de código remoto.

Comencemos discutiendo el primero de estos ataques en la siguiente sección.