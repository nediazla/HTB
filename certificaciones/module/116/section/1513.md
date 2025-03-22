# Latest DNS Vulnerabilities

Podemos encontrar miles de subdominios y dominios en la web. A menudo señalan que ya no son proveedores de servicios de terceros activos como AWS, GitHub y otros y, en el mejor de los casos, muestran un mensaje de error como confirmación de un servicio de terceros desactivado. Las grandes empresas y corporaciones también se ven afectadas una y otra vez. Las empresas a menudo cancelan los servicios de proveedores de terceros, pero olvidan eliminar los registros DNS asociados. Esto se debe a que no se incurren costos adicionales para una entrada DNS. Muchas plataformas de recompensa de errores bien conocidas, como[Hackerone](https://www.hackerone.com/), ya enumere explícitamente`Subdomain Takeover`Como categoría de recompensas. Con una búsqueda simple, podemos encontrar varias herramientas en Github, por ejemplo, que automatizan el descubrimiento de subdominios vulnerables o ayuden a crear una prueba de conceptos (`PoC`) que luego se puede enviar al programa de recompensas de errores de nuestra elección o la empresa afectada. Redhuntlabs hizo un[estudiar](https://redhuntlabs.com/blog/project-resonance-wave-1.html)Sobre esto en 2020, y descubrieron que más de 400,000 subdominios de 220 millones eran vulnerables a la adquisición de subdominios. El 62% de ellos pertenecían al sector de comercio electrónico.

### **Estudio de Redhuntlabs**

![](https://i0.wp.com/redhuntlabs.com/wp-content/uploads/2020/11/image-3.png)

Fuente: https://redhuntlabs.com/blog/project-resonance-wave-1.html

---

# **El concepto del ataque**

Uno de los mayores peligros de una adquisición de subdominios es que se puede lanzar una campaña de phishing que se considera parte del dominio oficial de la compañía objetivo. Por ejemplo, los clientes mirarían el enlace y verían que el dominio`customer-drive.inlanefreight.com`(que apunta a un cubo S3 inexistente de AWS) está detrás del dominio oficial`inlanefreight.com`y confía en él como cliente. Sin embargo, los clientes no saben que esta página ha sido reflejada o creada por un atacante para provocar un inicio de sesión por parte de los clientes de la compañía, por ejemplo.

Por lo tanto, si un atacante encuentra un`CNAME`Registro en los registros de DNS de la compañía que apunta a un subdominio que ya no existe y devuelve un`HTTP 404 error`, probablemente este subdominio pueda ser asumido por nosotros mediante el uso del proveedor de terceros. Se produce una adquisición del subdominio cuando un subdominio apunta a otro dominio utilizando el registro CNAME que actualmente no existe. Cuando un atacante registra este dominio inexistente, el subdominio apunta al registro de dominio por nosotros. Al hacer un solo cambio de DNS, nos hacemos el dueño de ese subdominio en particular, y después de eso, podemos administrar el subdominio como elegimos.

### **El concepto de ataques**

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Lo que sucede aquí es que el subdominio existente ya no apunta a un proveedor de terceros y, por lo tanto, ya no está ocupado por este proveedor. Casi cualquiera puede registrar este subdominio como propio. Visitar este subdominio y la presencia del registro CNAME en los líderes DNS de la compañía, en la mayoría de los casos, a cosas que funcionan como se esperaba. Sin embargo, el diseño y la función de este subdominio están en manos del atacante.

### **Iniciación de la adquisición del subdominio**

| **Paso** | **Adquisición del subdominio** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `1.` | La fuente, en este caso, es el nombre del subdominio que ya no es utilizado por la compañía que descubrimos. | `Source` |
| `2.` | El registro de este subdominio en el sitio del proveedor de terceros se realiza registrando y vinculando a propias fuentes. | `Process` |
| `3.` | Aquí, los privilegios recaen en el propietario del dominio principal y sus entradas en sus servidores DNS. En la mayoría de los casos, el proveedor de terceros no es responsable de si este subdominio es accesible a través de otros. | `Privileges` |
| `4.` | El registro y el enlace exitosos se realizan en nuestro servidor, que es el destino en este caso. | `Destination` |

Esto es cuando el ciclo comienza de nuevo, pero esta vez activar el reenvío al servidor que controlamos.

### **Activar el reenvío**

| **Paso** | **Adquisición del subdominio** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `5.` | El visitante del subdominio ingresa a la URL en su navegador, y el registro DNS obsoleto (CNAME) que no se ha eliminado se usa como fuente. | `Source` |
| `6.` | El servidor DNS se ve en su lista para ver si tiene conocimiento sobre este subdominio y, de ser así, el usuario se redirige al subdominio correspondiente (que está controlado por nosotros). | `Process` |
| `7.` | Los privilegios de esto ya están con los administradores que administran el dominio, ya que solo están autorizados para cambiar el dominio y sus servidores DNS. Dado que este subdominio está en la lista, el servidor DNS considera que el subdominio es confiable y reenvía al visitante. | `Privileges` |
| `8.` | El destino aquí es la persona que solicita la dirección IP del subdominio donde quieren reenviarse a través de la red. | `Destination` |

La adquisición del subdominio se puede usar no solo para el phishing sino también para muchos otros ataques. Estos incluyen, por ejemplo, robar cookies, falsificación de solicitudes de sitios cruzados (CSRF), abusar de CORS y derrotar la política de seguridad de contenido (CSP). Podemos ver algunos ejemplos de adquisiciones de subdominios en el[Sitio web de hackerone](https://hackerone.com/hacktivity?querystring=%22subdomain%20takeover%22), que se han ganado a los cazadores de errores considerables pagos.