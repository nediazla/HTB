# Primer de redes - Capas 5-7

Hemos visto cómo las funciones de red de nivel inferior, ahora veamos algunos de los protocolos de capa superior que manejan nuestras aplicaciones. Se necesitan muchas aplicaciones y servicios diferentes para mantener una conexión de red y garantizar que los datos puedan transferirse entre los hosts. Esta sección describirá solo unos pocos.

---

# **Http**

Protocolo de transferencia de hipertexto (`HTTP`) es un protocolo de capa de aplicación sin estado que ha estado en uso desde 1990. HTTP permite la transferencia de datos en texto claro entre un cliente y un servidor a través de TCP. El cliente enviaría una solicitud HTTP al servidor, solicitando un recurso. Se establece una sesión y el servidor responde con los medios solicitados (HTML, imágenes, hipervínculos, video). HTTP utiliza puertos 80 u 8000 sobre TCP durante las operaciones normales. En circunstancias excepcionales, se puede modificar para usar puertos alternativos, o incluso a veces, UDP.

### **Métodos HTTP**

Para realizar operaciones, como obtener páginas web, solicitar elementos para descargar o publicar su tweet más reciente requieren el uso de métodos específicos. Estos métodos definen las acciones tomadas al solicitar un URI. Métodos:

| **Método** | **Descripción** |
| --- | --- |
| `HEAD` | `required` es un método seguro que solicita una respuesta del servidor similar a una solicitud GET, excepto que el cuerpo del mensaje no está incluido. Es una excelente manera de adquirir más información sobre el servidor y su estado operativo. |
| `GET` | `required` Get es el método más común utilizado. Solicita información y contenido del servidor. Por ejemplo, `GET http://10.1.1.1/Webserver/index.html` solicita la página index.html del servidor en función de nuestro URI suministrado. |
| `POST` | `optional`Post es una forma de enviar información a un servidor basado en los campos de la solicitud. Por ejemplo, enviar un mensaje a una publicación de Facebook o un foro de sitios web es una acción de publicación. La acción real tomada puede variar según el servidor, y debemos prestar atención a los códigos de respuesta enviados de vuelta para validar la acción. |
| `PUT` | `optional`Put llevará los datos adjuntos al mensaje y los colocará debajo del URI solicitado. Si un elemento aún no existe allí, creará uno con los datos suministrados. Si ya existe un objeto, el nuevo PUT se considerará el más actualizado, y el objeto se modificará para que coincida. La forma más fácil de visualizar las diferencias entre Put y Post es pensar en ello así; Put creará o actualizará un objeto en el URI suministrado, mientras que la publicación creará entidades infantiles en el URI proporcionado. La acción tomada se puede comparar con la diferencia entre crear un nuevo archivo frente a escribir comentarios sobre ese archivo en la misma página. |
| `DELETE` | `optional`Delete hace lo que su nombre lo indica. Eliminará el objeto en el URI dado. |
| `TRACE` | `optional`Permite el diagnóstico remoto del servidor. El servidor remoto eco de la misma solicitud que se envió en su respuesta si el método de seguimiento está habilitado. |
| `OPTIONS` | `optional` El método de opciones puede recopilar información sobre los métodos HTTP compatibles que el servidor reconoce. De esta manera, podemos determinar los requisitos para interactuar con un recurso o servidor específico sin solicitar datos u objetos de él. |
| `CONNECT` | `optional`Connect está reservado para su uso con proxies u otros dispositivos de seguridad como firewalls. Connect permite túneles a través de HTTP. (`SSL tunnels`) |

Observe que tenemos `required` o `optional` Listado junto a cada método. Como requisito por el estándar, Get and Head siempre debe funcionar y existir con implementaciones HTTP estándar. Esto es cierto solo para ellos. Los métodos traza, opciones, eliminación, put y post son funcionalidades opcionales que se puede permitir. Un ejemplo de esto es una página web de solo lectura como una publicación de blog. La PC del cliente puede solicitar un recurso desde la página pero no modificar, agregar o eliminar el recurso o los recursos.

Para obtener más información sobre HTTP como protocolo o cómo funciona, consulte`RFC:2616`.

---

# **Https**

Http seguro (`HTTPS`) es una modificación del protocolo HTTP diseñado para utilizar la seguridad de la capa de transporte (`TLS`) o una capa de enchufes asegurar (`SSL`) con aplicaciones más antiguas para la seguridad de los datos. TLS se utiliza como un mecanismo de cifrado para asegurar las comunicaciones entre un cliente y un servidor. TLS puede envolver el tráfico HTTP regular dentro de TLS, lo que significa que podemos cifrar toda nuestra conversación, no solo los datos enviados o solicitados. Antes de que el mecanismo TLS estuviera en su lugar, fuimos vulnerables a los ataques de hombre en el medio y otros tipos de reconocimiento o secuestro, lo que significa que cualquier persona en la misma LAN que el cliente o servidor podría ver el tráfico web si estuvieran escuchando en el cable. Ahora podemos implementar la seguridad en el navegador que permite a todos cifrar sus hábitos web, solicitudes de búsqueda, sesiones o transferencias de datos, transacciones bancarias y mucho más.

Aunque es HTTP en su base, HTTPS utiliza los puertos 443 y 8443 en lugar del puerto estándar 80. Esta es una forma simple para que el cliente indique al servidor que desea establecer una conexión segura. Veamos una salida del tráfico HTTPS y discernir cómo un`TLS handshake`Funciones por un minuto.

### **Tls Handshake a través de HTTPS**

![](https://academy.hackthebox.com/storage/modules/81/https.png)

En los primeros paquetes, podemos ver que el cliente establece una sesión en el servidor utilizando el puerto 443 `boxed in blue`. Esto le indica al servidor que desea usar HTTPS como el protocolo de comunicación de la aplicación.

Una vez que se inicia una sesión a través de TCP, se envía un TLS Clienthello a continuación para comenzar el apretón de manos TLS. Durante el apretón de manos, se acuerdan varios parámetros, incluido el identificador de sesión, el certificado de pares X509, el algoritmo de compresión que se utilizará, el algoritmo de cifrado de especificaciones de cifrado, si la sesión es reanudable y un secreto maestro de 48 bytes compartido entre el cliente y el servidor y el servidor a Validar la sesión.

Una vez que se establece la sesión, todos los datos y métodos se enviarán a través de la conexión TLS y aparecerán como datos de aplicación TLS`as seen in the red box`. TLS todavía está utilizando TCP como protocolo de transporte, por lo que aún veremos paquetes de reconocimiento de la corriente que se acerca al puerto 443.

Para resumir el apretón de manos:

1. Mensajes de Hello Hello de intercambio de clientes y servidores para acordar los parámetros de conexión.
2. Intercambio del cliente y servidor Los parámetros criptográficos necesarios para establecer un secreto más praster.
3. El cliente y el servidor intercambiarán certificados X.509 e información criptográfica que permite la autenticación dentro de la sesión.
4. Genere un secreto maestro desde el secreto PremaSaster e intercambió valores aleatorios.
5. Problema del cliente y servidor Los parámetros de seguridad negociados en la parte de la capa de registro del protocolo TLS.
6. El cliente y el servidor verifican que su par ha calculado los mismos parámetros de seguridad y que el apretón de manos ocurrió sin manipular por un atacante.

El cifrado en sí mismo es un tema complejo y largo que merece su propio módulo. Esta sección es un resumen simple de cómo HTTP y TLS proporcionan seguridad dentro del protocolo de aplicación HTTPS. Para obtener más información sobre cómo funciona HTTPS y cómo TLS realiza operaciones de seguridad, consulte`RFC:2246`.

---

# **Ftp**

Protocolo de transferencia de archivos (`FTP`) es un protocolo de capa de aplicación que permite una transferencia rápida de datos entre los dispositivos informáticos. FTP se puede utilizar desde la línea de comandos, el navegador web o a través de un cliente FTP gráfico como FileZilla. FTP en sí se establece como un protocolo inseguro, y la mayoría de los usuarios se han movido para utilizar herramientas como SFTP para transferir archivos a través de canales seguros. Como una nota que se mueve hacia el futuro, la mayoría de los navegadores web modernos han eliminado el soporte para FTP a partir de 2020.

Cuando pensamos en la comunicación entre los hosts, generalmente pensamos en un cliente y un servidor que habla sobre un solo socket. A través de este socket, tanto el cliente como el servidor envían comandos y datos a través del mismo enlace. En este aspecto, FTP es único ya que utiliza múltiples puertos a la vez. FTP usa los puertos 20 y 21 sobre TCP. El puerto 20 se utiliza para la transferencia de datos, mientras que el puerto 21 se utiliza para emitir comandos que controlan la sesión FTP. En lo que respecta a la autenticación, FTP admite la autenticación del usuario y permite el acceso anónimo si está configurado.

FTP es capaz de ejecutarse en dos modos diferentes, `active` o `passive`. Active es el método operativo predeterminado utilizado por FTP, lo que significa que el servidor escucha un comando de control`PORT`Desde el cliente, indicando qué puerto usar para la transferencia de datos. El modo pasivo nos permite acceder a los servidores FTP ubicados detrás de los firewalls o un enlace habilitado para NAT que hace imposible las conexiones TCP directas. En este caso, el cliente enviaría el `PASV` coman y espere una respuesta del servidor informando al cliente qué IP y puerto utilizarán para la conexión del canal de transferencia de datos.

### **Ejemplos de comando y respuesta FTP**

![](https://academy.hackthebox.com/storage/modules/81/ftp-example.png)

La imagen de arriba muestra varios ejemplos de solicitudes emitidas sobre el canal de comando FTP `green arrows`, y las respuestas enviadas desde el servidor FTP`blue arrows`. Todo esto es algo bastante estándar. Para obtener una lista de cada comando y lo que está haciendo, consulte la tabla a continuación.

Al mirar el tráfico FTP, algunos comandos comunes que podemos ver pasados ​​sobre el puerto 21 incluyen:

### **Comandos FTP**

| **Dominio** | **Descripción** |
| --- | --- |
| `USER` | Especifica al usuario para iniciar sesión como. |
| `PASS` | Envía la contraseña para el usuario que intenta iniciar sesión. |
| `PORT` | Cuando está en modo activo, esto cambiará el puerto de datos utilizado. |
| `PASV` | Cambia la conexión al servidor desde el modo activo a pasivo. |
| `LIST` | Muestra una lista de los archivos en el directorio actual. |
| `CWD` | cambiará el directorio de trabajo actual a uno especificado. |
| `PWD` | Imprime el directorio en el que está trabajando actualmente. |
| `SIZE` | devolverá el tamaño de un archivo especificado. |
| `RETR` | Recupera el archivo del servidor FTP. |
| `QUIT` | termina la sesión. |

Esta no es una lista exhaustiva de los posibles comandos de control FTP que se pueden ver. Estos pueden variar según la aplicación FTP o el shell en uso. Para obtener más información sobre FTP, consulte `RFC:959`.

---

# **SMB**

Bloque de mensajes del servidor (`SMB`) es un protocolo más visto en entornos empresariales de Windows que permite compartir recursos entre hosts sobre arquitecturas de redes comunes. SMB es un protocolo orientado a la conexión que requiere la autenticación del usuario del host al recurso para garantizar que el usuario tenga permisos correctos para usar ese recurso o realizar acciones. En el pasado, SMB utilizó NetBIOS como su mecanismo de transporte sobre los puertos UDP 137 y 138. Dado que los cambios modernos, SMB ahora admite el transporte TCP directo sobre el puerto 445, NetBIOS sobre el puerto TCP 139 e incluso el protocolo QUIC.

Como usuario, SMB nos proporciona un acceso fácil y conveniente a recursos como impresoras, unidades compartidas, servidores de autenticación y más. Por esta razón, SMB también es muy atractivo para los posibles atacantes.

Al igual que cualquier otra aplicación que use TCP como mecanismo de transporte, realizará funciones estándar como el apretón de manos de tres vías y el reconocimiento de paquetes recibidos. Tomemos un segundo para ver un tráfico de SMB para familiarizarnos.

### **SMB en el cable**

![](https://academy.hackthebox.com/storage/modules/81/smb-actions.png)

Mirando la imagen de arriba, podemos ver que realiza el apretón de manos TCP cada vez que establece una sesión `orange boxes`. Al mirar los puertos de origen y destino `blue box`Se está utilizando el puerto 445, lo que indica el tráfico SMB sobre TCP. Si miramos el `green boxes,` El campo de información nos dice un poco sobre lo que está sucediendo en la comunicación SMB. En este ejemplo, hay muchos errores, que es un ejemplo de algo para profundizar. Una o dos fallas de autores de un usuario son relativamente comunes, pero un gran clúster de ellas que se repiten puede indicar a una persona potencial no autorizada que intenta acceder a la cuenta de un usuario o usar sus credenciales para moverse. Esta es una táctica común de atacantes, agarra a un usuario autenticado, roba sus credenciales, las utiliza para moverse lateralmente o acceder a recursos a los que generalmente se les negaría el acceso.

Este es solo un ejemplo de uso de SMB. Otra cosa común que veremos es el acceso a los archivos compartidos entre servidores y hosts. En su mayor parte, esta es una comunicación regular. Sin embargo, si vemos un archivo de acceso al host en otros hosts, esto no es común. Preste atención a quién solicita conexiones, dónde y qué están haciendo.