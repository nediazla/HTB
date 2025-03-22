# osTicket

[Osticket](https://osticket.com/)es un sistema de ticketing de soporte de código abierto. Se puede comparar con sistemas como JIRA, OTRS, Rastreador de solicitudes y SpiceWorks. Osticket puede integrar las consultas de los usuarios desde el correo electrónico, el teléfono y los formularios basados en la web en una interfaz web. Osticket está escrito en PHP y usa un backend de MySQL. Se puede instalar en Windows o Linux. Aunque no hay una cantidad considerable de información de mercado fácilmente disponible sobre Osticket, una búsqueda rápida de Google para`Helpdesk software - powered by osTicket`Devuelve aproximadamente 44,000 resultados, muchos de los cuales parecen ser empresas, sistemas escolares, universidades, gobierno local, etc., utilizando la aplicación. Osticket incluso se mostró brevemente en el programa[Sr. Robot](https://forum.osticket.com/d/86225-osticket-on-usas-mr-robot-s01e08).

Además de aprender sobre enumerar y atacar a Osticket, el propósito de esta sección también es presentarle el mundo de los sistemas de boletos de apoyo y por qué no deben pasarse por alto durante nuestras evaluaciones.

---

# **Huella/descubrimiento/enumeración**

Mirando hacia atrás en nuestro escaneo de testigos oculares desde anteriormente, notamos una captura de pantalla de una instancia de Osticket que también muestra que una galleta llamada`OSTSESSID`se estableció al visitar la página.

![](https://academy.hackthebox.com/storage/modules/113/osticket_eyewitness.png)

Además, la mayoría de las instalaciones de Osticket mostrarán el logotipo de Osticket con la frase`powered by`delante de él en el pie de página de la página. El pie de página también puede contener las palabras`Support Ticket System`.

![](https://academy.hackthebox.com/storage/modules/113/osticket_main.png)

Un escaneo NMAP solo mostrará información sobre el servidor web, como Apache o IIS, y no nos ayudará a presionar la aplicación.

`osTicket`es una aplicación web que es altamente mantenida y servicio. Si miramos el[CVES](https://www.cvedetails.com/vendor/2292/Osticket.html)Encontrados durante décadas, no encontraremos muchas vulnerabilidades y hazañas que Osticket podría tener. Este es un excelente ejemplo para mostrar lo importante que es comprender cómo funciona una aplicación web. Incluso si la aplicación no es vulnerable, aún se puede usar para nuestros propósitos. Aquí podemos dividir las funciones principales en las capas:

| **`1. User input`** | **`2. Processing`** | **`3. Solution`** |
| --- | --- | --- |

### **Entrada de usuario**

La función central de Osticket es informar a los empleados de la compañía sobre un problema para que un problema pueda resolverse con el servicio u otros componentes. Una ventaja significativa que tenemos aquí es que la aplicación es de código abierto. Por lo tanto, tenemos muchos tutoriales y ejemplos disponibles para ver más de cerca la aplicación. Por ejemplo, del Osticket[documentación](https://docs.osticket.com/en/latest/Getting%20Started/Post-Installation.html), podemos ver que solo el personal y los usuarios con privilegios de administrador pueden acceder al panel de administración. Entonces, si nuestra empresa objetivo usa esta o una aplicación similar, podemos causar un problema y "jugar tonto" y comunicarnos con el personal de la compañía. La "falta de conocimiento" simulada de los servicios ofrecidos por la compañía en combinación con un problema técnico es un enfoque generalizado de ingeniería social para obtener más información de la empresa.

### **Tratamiento**

Como personal o administradores, intentan reproducir errores significativos para encontrar el núcleo del problema. El procesamiento finalmente se realiza internamente en un entorno aislado que tendrá una configuración muy similar a los sistemas de producción. Suponga que el personal y los administradores sospechan que hay un error interno que puede estar afectando el negocio. En ese caso, entrarán en más detalles para descubrir posibles errores de código y abordar problemas más significativos.

### **Solución**

Dependiendo de la profundidad del problema, es muy probable que otros miembros del personal de los departamentos técnicos estén involucrados en la correspondencia por correo electrónico. Esto nos dará nuevas direcciones de correo electrónico para usar contra el panel de administración de Osticket (en el peor de los casos) y los nombres de usuario potenciales con los que podemos realizar OSINT o intentar aplicar a otros servicios de la compañía.

---

# **Atacando a Osticket**

Una búsqueda de Osticket en Exploit-DB muestra varios problemas, incluida la inclusión de archivos remotos, la inyección de SQL, la carga arbitraria de archivos, XSS, etc. Osticket versión 1.14.1 sufre de[CVE-2020-24881](https://nvd.nist.gov/vuln/detail/CVE-2020-24881)que fue una vulnerabilidad SSRF. Si se explota, este tipo de defecto puede aprovecharse para obtener acceso a recursos internos o realizar escaneo de puertos internos.

Además de las vulnerabilidades relacionadas con la aplicación web, los portales de soporte a veces se pueden utilizar para obtener una dirección de correo electrónico para un dominio de la empresa, que se puede utilizar para registrarse en otras aplicaciones expuestas que requieren una verificación de correo electrónico. Como se mencionó anteriormente en el módulo, esto se ilustra en la caja de lanzamiento semanal HTB[Entrega](https://0xdf.gitlab.io/2021/05/22/htb-delivery.html)con un tutorial de video[aquí](https://www.youtube.com/watch?v=gbs43E71mFM).

Caminemos por un ejemplo rápido, que está relacionado con esto[Excelente publicación de blog](https://medium.com/intigriti/how-i-hacked-hundreds-of-companies-through-their-helpdesk-b7680ddc2d4c)cual[@ippsec](https://twitter.com/ippsec)También se mencionó una inspiración para la entrega de su caja que recomiendo visitar después de leer esta sección.

Supongamos que encontramos un servicio expuesto, como el servidor Slack de una empresa o GitLab, que requiere una dirección de correo electrónico de compañía válida para unirse. Muchas compañías tienen un correo electrónico de soporte como`support@inlanefreight.local`y los correos electrónicos enviados a esto están disponibles en portales de soporte en línea que pueden variar desde Zendesk hasta una herramienta personalizada interna. Además, un portal de soporte puede asignar una dirección de correo electrónico interna temporal a un nuevo boleto para que los usuarios puedan verificar rápidamente su estado.

Si nos encontramos con un portal de atención al cliente durante nuestra evaluación y podemos enviar un nuevo boleto, podemos obtener una dirección de correo electrónico válida de la empresa.

![](https://academy.hackthebox.com/storage/modules/113/new_ticket.png)

Esta es una versión modificada de Osticket como ejemplo, pero podemos ver que se proporcionó una dirección de correo electrónico.

![](https://academy.hackthebox.com/storage/modules/113/ticket_email.png)

Ahora, si iniciamos sesión, podemos ver información sobre el boleto y las formas de publicar una respuesta. Si la compañía configuró su software de servicio de ayuda para correlacionar los números de boletos con los correos electrónicos, entonces cualquier correo electrónico enviado al correo electrónico que recibimos al registrarnos,`940288@inlanefreight.local`, aparecería aquí. Con esta configuración, si podemos encontrar un portal externo como un wiki, un servicio de chat (Slack, Mattermost, Rocket.Chat) o un repositorio Git como Gitlab o Bitbucket, podemos usar este correo electrónico para registrar un portal de soporte de cuenta y la mesa de ayuda para recibir un correo electrónico de confirmación de registro.

![](https://academy.hackthebox.com/storage/modules/113/ost_tickets.png)

---

# **Osticket: exposición a datos confidenciales**

Digamos que estamos en una prueba de penetración externa. Durante nuestra recopilación de OSINT e información, descubrimos varias credenciales de usuario utilizando la herramienta[Desaliñado](http://dehashed.com/)(Para nuestros propósitos, los datos de muestra a continuación son ficticios).

Osticket

```
xnoxos@htb[/htb]$ sudo python3 dehashed.py -q inlanefreight.local -pid : 5996447501
email : julie.clayton@inlanefreight.local
username : jclayton
password : JulieC8765!
hashed_password :
name : Julie Clayton
vin :
address :
phone :
database_name : ModBSolutions

id : 7344467234
email : kevin@inlanefreight.local
username : kgrimes
password : Fish1ng_s3ason!
hashed_password :
name : Kevin Grimes
vin :
address :
phone :
database_name : MyFitnessPal

<SNIP>

```

Este volcado muestra contraseñas de ClearText para dos usuarios diferentes:`jclayton`y`kgrimes`. En este punto, también hemos realizado la enumeración del subdominio y nos encontramos con varios interesantes.

Osticket

```
xnoxos@htb[/htb]$ cat ilfreight_subdomainsvpn.inlanefreight.local
support.inlanefreight.local
ns1.inlanefreight.local
mail.inlanefreight.local
apps.inlanefreight.local
ftp.inlanefreight.local
dev.inlanefreight.local
ir.inlanefreight.local
auth.inlanefreight.local
careers.inlanefreight.local
portal-stage.inlanefreight.local
dns1.inlanefreight.local
dns2.inlanefreight.local
meet.inlanefreight.local
portal-test.inlanefreight.local
home.inlanefreight.local
legacy.inlanefreight.local

```

Navegamos a cada subdominio y encontramos que muchos están desaparecidos, pero el`support.inlanefreight.local`y`vpn.inlanefreight.local`son activos y muy prometedores.`Support.inlanefreight.local`está organizando una instancia de Osticket, y`vpn.inlanefreight.local`es un portal web Barracuda SSL VPN que no parece estar utilizando autenticación multifactor.

![](https://academy.hackthebox.com/storage/modules/113/osticket_admin.png)

Probemos las credenciales para`jclayton`. Sin suerte. Luego probamos las credenciales para`kgrimes`y no tienen éxito, pero notan que la página de inicio de sesión también acepta una dirección de correo electrónico, intentamos`kevin@inlanefreight.local`¡Y obtenga un inicio de sesión exitoso!

![](https://academy.hackthebox.com/storage/modules/113/osticket_kevin.png)

El usuario`kevin`parece ser un agente de soporte pero no tiene boletos abiertos. ¿Quizás ya no están activos? En una empresa ocupada, esperaríamos ver algunos boletos abiertos. Cavando un poco, encontramos un boleto cerrado, una conversación entre un empleado remoto y el agente de soporte.

![](https://academy.hackthebox.com/storage/modules/113/osticket_ticket.png)

El empleado afirma que fueron bloqueados de su cuenta VPN y le pide al agente que la reinicie. Luego, el agente le dice al usuario que la contraseña se restableció a la contraseña estándar de la nueva contraseña de Joiner. El usuario no tiene esta contraseña y le pide al agente que los llame para que les proporcione la contraseña (¡conciencia sólida de seguridad!). El agente luego comete un error y envía la contraseña al usuario directamente a través del portal. Desde aquí, podríamos probar esta contraseña contra el portal VPN expuesto ya que el usuario puede no haberlo cambiado.

Además, el agente de soporte afirma que esta es la contraseña estándar dada a los nuevos carpinistas y establece la contraseña del usuario en este valor. Hemos estado en muchas organizaciones donde el servicio de ayuda utiliza una contraseña estándar para nuevos usuarios y restos de contraseña. A menudo, la política de contraseña de dominio es laxa y no obliga al usuario a cambiar en el siguiente inicio de sesión. Si este es el caso, puede funcionar para otros usuarios. Aunque fuera del alcance de este módulo, en este escenario, valdría la pena usar herramientas como[LinkedIn2Username](https://github.com/initstring/linkedin2username)Para crear una lista de usuarios de empleados de la empresa e intentar un ataque de pulverización de contraseña contra el punto final VPN con esta contraseña estándar.

Muchas aplicaciones como Osticket también contienen una libreta de direcciones. También valdría la pena exportar todos los correos electrónicos/nombres de usuario desde la libreta de direcciones como parte de nuestra enumeración, ya que también podrían resultar útiles en un ataque como la pulverización de contraseñas.

---

# **Pensamientos de cierre**

Aunque esta sección mostró algunos escenarios ficticios, se basan en cosas que es probable que veamos en el mundo real. Cuando nos encontramos con portales de soporte (especialmente externos), debemos probar la funcionalidad y ver si podemos hacer cosas como crear un boleto y tener una dirección de correo electrónico de la compañía legítima que nos asigne. A partir de ahí, podemos usar la dirección de correo electrónico para iniciar sesión en otros servicios de la compañía y obtener acceso a datos confidenciales.

Esta sección también muestra los peligros de la reutilización de la contraseña y los tipos de datos que probablemente encontraremos si podemos obtener acceso a la cola de boletos de soporte de un agente de la mesa de ayuda. Las organizaciones pueden evitar este tipo de fuga de información tomando algunos pasos relativamente fáciles:

- Limite qué aplicaciones están expuestas externamente
- Hacer cumplir la autenticación multifactor en todos los portales externos
- Proporcionar capacitación sobre el conocimiento de la seguridad a todos los empleados y aconsejarles que no usen sus correos electrónicos corporativos para registrarse en los servicios de terceros.
- Hacer cumplir una política de contraseña segura en Active Directory y en todas las aplicaciones, no permitir palabras comunes como variaciones de`welcome`, y`password`, el nombre de la compañía, y temporadas y meses
- Requiere que un usuario cambie su contraseña después de su inicio de sesión inicial y expire periódicamente las contraseñas del usuario