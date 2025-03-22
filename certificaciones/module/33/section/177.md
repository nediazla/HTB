# Intoduccion

La mayoría de las aplicaciones web modernas utilizan una estructura de base de datos en el back-end. Dichas bases de datos se utilizan para almacenar y recuperar datos relacionados con la aplicación web, desde el contenido web real hasta la información y el contenido del usuario, etc. Para que las aplicaciones web dinámicas, la aplicación web debe interactuar con la base de datos en tiempo real. Como las solicitudes HTTP (s) llegan del usuario, el back-end de la aplicación web emitirá consultas a la base de datos para crear la respuesta. Estas consultas pueden incluir información de la solicitud HTTP (s) u otra información relevante.

![](https://academy.hackthebox.com/storage/modules/33/db_request_3.png)

Cuando la información proporcionada por el usuario se utiliza para construir la consulta a la base de datos, los usuarios maliciosos pueden engañar a la consulta para que se use para algo más de lo que el programador original pretendía, proporcionando al usuario acceso para consultar la base de datos utilizando un ataque conocido como inyección SQL (SQLI).

La inyección de SQL se refiere a ataques contra bases de datos relacionales como`MySQL`(Mientras que las inyecciones contra bases de datos no relacionales, como MongoDB, son inyección de NoSQL). Este módulo se centrará en`MySQL`para introducir conceptos de inyección SQL.

---

# **Inyección SQL (SQLI)**

Muchos tipos de vulnerabilidades de inyección son posibles dentro de las aplicaciones web, como inyección HTTP, inyección de código e inyección de comandos. Sin embargo, el ejemplo más común es la inyección SQL. Se produce una inyección SQL cuando un usuario malicioso intenta aprobar la entrada que cambia la consulta SQL final enviada por la aplicación web a la base de datos, lo que permite al usuario realizar otras consultas SQL involuntarias directamente en la base de datos.

Hay muchas formas de lograr esto. Para que funcione una inyección SQL, el atacante primero debe inyectar código SQL y luego subvertir la lógica de la aplicación web cambiando la consulta original o ejecutando una completamente nueva. Primero, el atacante tiene que inyectar código fuera de los límites de entrada del usuario esperados, por lo que no se ejecuta como una simple entrada del usuario. En el caso más básico, esto se hace inyectando una sola cita (`'`) o una cita doble (`"`) para escapar de los límites de la entrada del usuario e inyectar datos directamente en la consulta SQL.

Una vez que un atacante puede inyectar, tiene que buscar una forma de ejecutar una consulta SQL diferente. Esto se puede hacer utilizando el código SQL para inventar una consulta de trabajo que ejecute las consultas SQL previstas y las nuevas SQL. Hay muchas maneras de lograr esto, como usar[apilado](https://www.sqlinjection.net/stacked-queries/)consultas o usando[Unión](https://www.mysqltutorial.org/sql-union-mysql.aspx/)consultas. Finalmente, para recuperar la salida de nuestra nueva consulta, tenemos que interpretarla o capturarla en la parte delantera de la aplicación web.

---

# **Casos de uso e impacto**

Una inyección SQL puede tener un gran impacto, especialmente si los privilegios en el servidor de fondo y la base de datos son muy laxos.

Primero, podemos recuperar información secreta/confidencial que no debe ser visible para nosotros, como inicios de sesión y contraseñas de usuario o información de tarjeta de crédito, que luego se puede utilizar para otros fines maliciosos. Las inyecciones de SQL causan muchas contraseñas y violaciones de datos contra sitios web, que luego se reutilizan para robar cuentas de usuarios, acceder a otros servicios o realizar otras acciones nefastas.

Otro caso de uso de la inyección de SQL es subvertir la lógica de aplicación web prevista. El ejemplo más común de esto es evitar el inicio de sesión sin pasar un par válido de credenciales de nombre de usuario y contraseña. Otro ejemplo es acceder a las características bloqueadas a usuarios específicos, como los paneles de administración. Los atacantes también pueden leer y escribir archivos directamente en el servidor de back-end, lo que puede, a su vez, conducir a la colocación de puertas en el servidor de fondo, y obtener control directo sobre él, y eventualmente tomar el control sobre todo el sitio web.

---

# **Prevención**

Las inyecciones de SQL generalmente son causadas por aplicaciones web mal codificadas o privilegios de servidor de fondo y bases de datos mal aseguradas. Más adelante, discutiremos formas de reducir las posibilidades de ser vulnerables a las inyecciones de SQL a través de métodos de codificación seguros como la desinfección y validación de la entrada del usuario y los privilegios y el control de los usuarios de fondo adecuados.

Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/33/section/178)

Hoja de trucos