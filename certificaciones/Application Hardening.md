# Application Hardening

El primer paso para cualquier organización debe ser crear un inventario de aplicación detallado (y preciso) de aplicaciones internas y externos. Esto se puede lograr de muchas maneras, y los equipos azules con un presupuesto podrían beneficiarse de herramientas de repuesto como NMAP y testigos oculares para ayudar en el proceso. Se pueden usar varias herramientas de código abierto y pagos para crear y mantener este inventario. Sin saber qué existe en el medio ambiente, ¡no sabremos qué proteger! La creación de este inventario puede exponer instancias de "Shadow It" (o instalaciones no autorizadas), aplicaciones desaprobadas que ya no son necesarias, o incluso problemas, como una versión de prueba de una herramienta que se convierte en una versión gratuita automáticamente (como Splunk cuando ya no requiere autenticación).

---

# **Consejos generales de endurecimiento**

Las aplicaciones discutidas en esta sección deben endurecerse para evitar el compromiso utilizando estas técnicas y otras. A continuación se presentan algunas medidas importantes que pueden ayudar a asegurar las implementaciones de WordPress, Drupal, Joomla, Tomcat, Jenkins, Osticket, GitLab, PRTG Network Monitor y Splunk en cualquier entorno.

- `Secure authentication`: Las aplicaciones deben hacer cumplir las contraseñas seguras durante el registro y la configuración, y se deben cambiar las contraseñas de cuenta administrativa predeterminada. Si es posible, las cuentas administrativas predeterminadas deben deshabilitarse, con nuevas cuentas administrativas personalizadas creadas. Algunas aplicaciones admiten inherentemente la autenticación 2FA, que debe ser obligatoria para al menos usuarios a nivel de administrador.
- `Access controls`: Los mecanismos de control de acceso adecuados deben implementarse por aplicación. Por ejemplo, las páginas de inicio de sesión no deben ser accesibles desde la red externa a menos que haya una razón comercial válida para este acceso. Del mismo modo, los permisos de archivo y carpeta se pueden configurar para negar cargas o implementaciones de aplicaciones.
- `Disable unsafe features`: Las características como la edición del código PHP en WordPress se pueden deshabilitar para evitar la ejecución del código si el servidor está comprometido.
- `Regular updates`: Las aplicaciones deben actualizarse regularmente, y los parches suministrados por los proveedores deben aplicarse lo antes posible.
- `Backups`: Los administradores del sistema siempre deben configurar las copias de seguridad del sitio web y las bases de datos, lo que permite que la aplicación se restablezca rápidamente en caso de compromiso.
- `Security monitoring`: Existen varias herramientas y complementos que se pueden usar para monitorear el estado y varios problemas relacionados con la seguridad para nuestras aplicaciones. Otra opción es un firewall de aplicación web (WAF). Si bien no es una bala de plata, un WAF puede ayudar a agregar una capa adicional de protección siempre que ya se hayan tomado todas las medidas anteriores.
- `LDAP integration with Active Directory`: La integración de aplicaciones con el inicio de sesión único de Active Directory puede aumentar la facilidad de acceso, proporcionar más funcionalidad de auditoría (especialmente si se sincroniza con Azure) y hacer que la administración de credenciales y cuentas de servicio sea más optimizada. También disminuye el número de cuentas y contraseñas que un usuario tendrá que recordar y dar un control de grano fino sobre la política de contraseña.

Every application that we discussed in this module (and beyond) should be following key hardening guidelines such as enabling multi-factor authentication for admins and users wherever possible, changing default admin user account names, limiting the number of admins, and how admins can access the site (ie, not from the open internet), enforce the principle of least privilege throughout the application, perform regular updates to address security vulnerabilities, taking regular backups to a secondary location to be Capaz de recuperarse rápidamente en caso de un ataque e implementar herramientas de monitoreo de seguridad que puedan detectar y bloquear la actividad maliciosa y tener en cuenta la falsificación bruta, entre otros ataques.

Finalmente, debemos tener cuidado con lo que exponemos a Internet. ¿Ese repositorio de Gitlab realmente necesita ser público? ¿Nuestro sistema de boletos debe ser accesible fuera de la red interna? Con estos controles en su lugar, tendremos una línea de base sólida para aplicar a todas las aplicaciones, independientemente de su función.

También debemos realizar cheques y actualizaciones regulares de nuestro inventario de aplicaciones para asegurarnos de que no estamos exponiendo aplicaciones en la red interna o externa que ya no se necesitan o que tengan fallas de seguridad severas. Finalmente, realice evaluaciones regulares para buscar vulnerabilidades de seguridad y configuraciones erróneas, así como la exposición de datos confidenciales. Siga las recomendaciones de remediación incluidas en sus informes de pruebas de penetración y verifique periódicamente los mismos tipos de defectos descubiertos por sus probadores de penetración. Algunos podrían estar relacionados con el proceso, lo que requiere un cambio de mentalidad para que la organización se vuelva más consciente de la seguridad.

---

# **Consejos de endurecimiento específicos de la aplicación**

Aunque los conceptos generales para el endurecimiento de la aplicación se aplican a todas las aplicaciones que discutimos en este módulo y encontraremos en el mundo real, podemos tomar algunas medidas más específicas. Aquí hay algunos:

| **Solicitud** | **Categoría de endurecimiento** | **Discusión** |
| --- | --- | --- |
| [WordPress](https://wordpress.org/support/article/hardening-wordpress/) | Monitoreo de seguridad | Utilice un complemento de seguridad como[Ficción de Word](https://www.wordfence.com/)que incluye monitoreo de seguridad, bloqueo de actividades sospechosas, bloqueo de países, autenticación de dos factores y más |
| [Joomla](https://docs.joomla.org/Security_Checklist/Joomla!_Setup) | Controles de acceso | Un complemento como[Adminexile](https://extensions.joomla.org/extension/adminexile/)se puede utilizar para requerir una clave secreta para iniciar sesión en la página de administración de Joomla, como`http://joomla.inlanefreight.local/administrator?thisismysecretkey` |
| [Drupal](https://www.drupal.org/docs/security-in-drupal) | Controles de acceso | Deshabilitar, esconder o mover el[página de inicio de sesión de administrador](https://www.drupal.org/docs/7/managing-users/hide-user-login) |
| [Gato](https://tomcat.apache.org/tomcat-9.0-doc/security-howto.html) | Controles de acceso | Limite el acceso a las aplicaciones de Tomcat Manager y Host-Manager a solo LocalHost. Si estos deben estar expuestos externamente, aplique la lista blanca IP y establezca una contraseña muy segura y un nombre de usuario no estándar. |
| [Jenkins](https://www.jenkins.io/doc/book/security/securing-jenkins/) | Controles de acceso | Configurar permisos utilizando el[Complemento de estrategia de autorización de matriz](https://plugins.jenkins.io/matrix-auth) |
| [Flojo](https://docs.splunk.com/Documentation/Splunk/8.2.2/Security/Hardeningstandards) | Actualizaciones regulares | Asegúrese de cambiar la contraseña predeterminada y asegúrese de que Splunk tenga una licencia adecuada para hacer cumplir la autenticación |
| [Monitor de red PRTG](https://kb.paessler.com/en/topic/61108-what-security-features-does-prtg-include) | Autenticación segura | Asegúrese de mantenerse actualizado y cambiar la contraseña PRTG predeterminada |
| Osticket | Controles de acceso | Limite el acceso desde Internet si es posible |
| [Gitlab](https://about.gitlab.com/blog/2020/05/20/gitlab-instance-security-best-practices/) | Autenticación segura | Hacer cumplir las restricciones de registro, como requerir la aprobación del administrador para los nuevos registros, configurar dominios permitidos y denegados |

---

# **Conclusión**

En este módulo, cubrimos un área crítica de pruebas de penetración: aplicaciones comunes. Las aplicaciones web presentan una enorme superficie de ataque y a menudo se pasan por alto. Durante una prueba de penetración externa, a menudo, la mayoría de nuestros objetivos son aplicaciones. Debemos comprender cómo descubrir aplicaciones (y organizar nuestros datos de escaneo para procesarlos de manera eficiente), versiones de huella, descubrir vulnerabilidades conocidas y aprovechar la funcionalidad incorporada. A muchas organizaciones les va bien con la gestión de parches y vulnerabilidad, pero a menudo pasan por alto problemas, como credenciales débiles para acceder a Tomcat Manager o a una impresora con credenciales predeterminadas para la aplicación de administración web, donde podemos obtener credenciales LDAP para usar como punto de apoyo en la red interna. Las tres evaluaciones de habilidades que siguen están destinadas a poner en prueba el proceso de descubrimiento y enumeración de la aplicación.