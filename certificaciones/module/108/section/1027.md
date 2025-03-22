# Estándares de evaluación

Tanto las pruebas de penetración como las evaluaciones de vulnerabilidad deben cumplir con los estándares específicos para ser acreditados y aceptados por los gobiernos y las autoridades legales. Dichos estándares ayudan a garantizar que la evaluación se realice a fondo de manera generalmente acordada para aumentar la eficiencia de estas evaluaciones y reducir la probabilidad de un ataque contra la organización.

---

# **Normas de cumplimiento**

Cada organismo de cumplimiento regulatorio tiene sus propios estándares de seguridad de la información que las organizaciones deben cumplir para mantener su acreditación. Los grandes jugadores de cumplimiento en la seguridad de la información son`PCI`, `HIPAA`, `FISMA`, y`ISO 27001`.

Estas acreditaciones son necesarias porque certifica que una organización ha tenido un proveedor de terceros evaluar su entorno. Las organizaciones también confían en estas acreditaciones para las operaciones comerciales, ya que algunas compañías no harán negocios sin acreditaciones específicas de las organizaciones.

### **Estándar de seguridad de datos de la industria de tarjetas de pago (PCI DSS)**

El[Estándar de seguridad de datos de la industria de tarjetas de pago (PCI DSS)](https://www.pcisecuritystandards.org/pci_security/)es un estándar comúnmente conocido en seguridad de la información que implementa requisitos para las organizaciones que manejan tarjetas de crédito. Si bien no es una regulación gubernamental, las organizaciones que almacenan, procesan o transmiten datos del titular de la tarjeta aún deben implementar directrices PCI DSS. Esto incluiría bancos o tiendas en línea que manejan sus propias soluciones de pago (por ejemplo, Amazon).

Los requisitos de PCI DSS incluyen escaneo interno y externo de los activos. Por ejemplo, los datos de la tarjeta de crédito que se están procesando o transmitiendo se deben realizar en un entorno de datos del titular de la tarjeta (CDE). El entorno CDE debe estar adecuadamente segmentado de los activos normales. Los entornos CDE están segmentados del entorno regular de una organización para proteger los datos del titular de la tarjeta de ser comprometidos durante un ataque y limitar el acceso interno a los datos.

![](https://academy.hackthebox.com/storage/modules/108/graphics/PCI-DSS-Goals.png)

[Fuente](https://adktechs.com/wp-content/uploads/2019/06/PCI-DSS-Goals.png)

### **Ley de Portabilidad y Responsabilidad del Seguro de Salud (HIPAA)**

`HIPAA`es el[Ley de Portabilidad y Responsabilidad del Seguro de Salud](https://www.hhs.gov/programs/hipaa/index.html), que se utiliza para proteger los datos de los pacientes. HIPAA no necesariamente requiere escaneos o evaluaciones de vulnerabilidad; Sin embargo, se requiere una evaluación de riesgos e identificación de vulnerabilidad para mantener la acreditación de HIPAA.

### **Ley Federal de Gestión de Seguridad de la Información (FISMA)**

El[Ley Federal de Gestión de Seguridad de la Información (FISMA)](https://www.cisa.gov/federal-information-security-modernization-act)es un conjunto de estándares y pautas utilizadas para salvaguardar las operaciones e información del gobierno. La Ley requiere que una organización proporcione documentación y prueba de un programa de gestión de vulnerabilidad para mantener la disponibilidad, confidencialidad e integridad adecuadas de los sistemas de tecnología de la información.

### **ISO 27001**

`ISO 27001`es un estándar utilizado en todo el mundo para administrar la seguridad de la información.[ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)Requiere que las organizaciones realicen escaneos externos e internos trimestrales.

Aunque el cumplimiento es esencial, no debe impulsar un programa de gestión de vulnerabilidad. La gestión de vulnerabilidad debe considerar la singularidad de un entorno y el apetito de riesgo asociado a una organización.

El`International Organization for Standardization` (`ISO`) Mantiene los estándares técnicos para casi cualquier cosa que puedas imaginar. El[ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)Se trata de seguridad estándar con la seguridad de la información. El cumplimiento de ISO 27001 depende de mantener un sistema efectivo de gestión de seguridad de la información. Para garantizar el cumplimiento, las organizaciones pueden realizar pruebas de penetración de manera cuidadosamente diseñada.

---

# **Estándares de prueba de penetración**

Las pruebas de penetración no deben realizarse sin ninguna`rules`o`guidelines`. Siempre debe haber un alcance específicamente definido para un Pentest, y el propietario de una red debe tener un`signed legal contract`con Pentesters que describen lo que se les permite hacer y lo que no se les permite hacer. El pentesting también debe realizarse de tal manera que se haga un daño mínimo a las computadoras y redes de una empresa. Los probadores de penetración deben evitar realizar cambios siempre que sea posible (como cambiar una contraseña de cuenta) y limitar la cantidad de datos eliminados de la red de un cliente. Por ejemplo, en lugar de eliminar documentos confidenciales de un archivo compartido, una captura de pantalla de los nombres de la carpeta debería ser suficiente para demostrar el riesgo.

Además del alcance y las legalidades, también hay varios estándares Pentesting, dependiendo de qué tipo de sistema informático se esté evaluando. Estas son algunos de los estándares más comunes que puede usar como Pentester.

### **Ptes**

El[Estándar de ejecución de pruebas de penetración](http://www.pentest-standard.org/index.php/Main_Page) (`PTES`) se puede aplicar a todos los tipos de pruebas de penetración. Describe las fases de una prueba de penetración y cómo deben realizarse. Estas son las secciones en los PTE:

- Interacciones previas al compromiso
- Recopilación de inteligencia
- Modelado de amenazas
- Análisis de vulnerabilidad
- Explotación
- Posterior a la explotación
- Informes

### **Osstmm**

`OSSTMM`es el`Open Source Security Testing Methodology Manual`, otro conjunto de pautas que los pentestres pueden usar para garantizar que estén haciendo su trabajo correctamente. Se puede usar junto con otros estándares más pentest.

[Osstmm](https://www.isecom.org/OSSTMM.3.pdf)se divide en cinco canales diferentes para cinco áreas diferentes de pentesting:

1. Seguridad humana (los seres humanos están sujetos a hazañas de ingeniería social)
2. Seguridad física
3. Comunicaciones inalámbricas (incluidas, entre otros, tecnologías como WiFi y Bluetooth)
4. Telecomunicaciones
5. Redes de datos

### **Nist**

El`NIST` (`National Institute of Standards and Technology`) es bien conocido por su[Marco de ciberseguridad NIST](https://www.nist.gov/cyberframework), un sistema para diseñar políticas y procedimientos de respuesta a incidentes. NIST también tiene un marco de prueba de penetración. Las fases del marco NIST incluyen:

- Planificación
- Descubrimiento
- Ataque
- Informes

### **Owasp**

`OWASP`representa el[Abra el proyecto de seguridad de aplicaciones web](https://owasp.org/). Por lo general, son la organización de referencia para definir los estándares de prueba y clasificar los riesgos para las aplicaciones web.

OWASP mantiene algunos estándares y guías útiles diferentes para evaluar diversas tecnologías:

- [Guía de pruebas de seguridad web (WSTG)](https://owasp.org/www-project-web-security-testing-guide/)
- [Guía de pruebas de seguridad móvil (MSTG)](https://owasp.org/www-project-mobile-security-testing-guide/)
- [Metodología de prueba de seguridad de firmware](https://github.com/scriptingxss/owasp-fstm)