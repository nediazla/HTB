# Teoría de la protección

`Confidentiality`, `Integrity`, y`Availability`están en el corazón del papel de todos los practicantes de Infosec. Sin mantener un equilibrio entre ellos, no podemos garantizar la seguridad de nuestras empresas. Mantenemos este saldo asegurando la auditoría y la cuenta (contabilidad) para cada archivo, objeto y host en nuestro entorno; Al validar los usuarios, tienen permisos correctos (autorización) para ver y utilizar esos elementos; y garantizar que la identidad de cada usuario esté validada (autenticación) antes de otorgarles acceso a cualquier recurso empresarial. La mayoría de las violaciones se pueden volver a perder uno de esos tres principios. Este módulo se centrará en atacar y evitar el principio de`Authentication`Al comprometer las contraseñas de usuario en muchos sistemas operativos, aplicaciones y tipos de cifrado diferentes. Tomemos un segundo para discutir la autenticación y sus componentes con un poco más de detalle antes de sumergirse en la parte emocionante,`attacking passwords`.

---

# **Autenticación**

La autenticación, en su núcleo, es la validación de su identidad al presentar una combinación de tres factores principales a un mecanismo de validación. Ellos son;

1. Algo que sabe (una contraseña, contraseña, pin, etc.).
2. Algo que tiene (una tarjeta de identificación, clave de seguridad u otras herramientas de MFA).
3. Algo que usted es (su yo físico, nombre de usuario, dirección de correo electrónico u otros identificadores).

El proceso puede requerir cualquiera o todos estos descriptores de autenticación. Estos métodos se determinarán en función de la gravedad de la información o los sistemas a los que se accede y cuánta protección necesitan. Por ejemplo, los médicos a menudo deben utilizar una tarjeta de acceso común (CAC) emparejada con un código de pin o contraseña para acceder a cualquier terminal que ingrese o almacene los datos de los pacientes. Dependiendo de la madurez de la postura de seguridad de la organización, podrían requerir los tres tipos (un CAC, contraseña y PIN de una aplicación de autenticador, por ejemplo).

Otro ejemplo simple de esto es el acceso a nuestra dirección de correo electrónico. La prueba de información, en este caso, sería el conocimiento de la dirección de correo electrónico en sí y la contraseña asociada. Por ejemplo, un teléfono celular con`2FA`se puede usar. El tercer aspecto también puede desempeñar un papel: la presencia del usuario a través del reconocimiento biométrico, como una huella digital o reconocimiento facial.

In the previous example, the password is the authentication identifier that can be bypassed with different TTPs. This level is about authenticating the identity. Usually, only the owner and authenticating authority know the password. Authorization is carried out if the correct password is given to the authentication authority. Authorization, in this case, is the set of permissions that the user is granted upon successful login.

---

# **El uso de contraseñas**

El método de autenticación más común y ampliamente utilizado sigue siendo el uso de contraseñas, pero ¿qué es una contraseña? Una contraseña o frase de pases puede definirse generalmente como`a combination of letters, numbers, and symbols in a string for identity validation.`Por ejemplo, si trabajamos con contraseñas y tomamos una contraseña estándar de 8 dígitos que consiste solo en letras y números de mayúsculas.`36⁸` (`208,827,064,576`) diferentes combinaciones de contraseñas.

Siendo realistas, no necesita ser una combinación de esas cosas. Podría ser una letra de una canción o poema, una línea de un libro, una frase que puedes recordar, o incluso palabras generadas al azar concatenadas juntas como "Treedogevilelefant". La clave es que cumpla o supere los estándares de seguridad establecidos por su organización. El uso de múltiples capas para establecer la identidad puede hacer que todo el proceso de autenticación sea complicado y costoso. Agregar complejidad al proceso de autenticación crea un mayor esfuerzo que puede aumentar el estrés y la carga de trabajo que una persona puede tener durante una jornada laboral típica. Los sistemas complejos a menudo pueden requerir procesos manuales inconvenientes o pasos adicionales que podrían complicar significativamente la interacción y`user experience` (`UX`). Considere el proceso de compras en una tienda en línea. Crear una cuenta en el sitio web de la tienda puede hacer que los procesos de autenticación y pago sean mucho más rápido que ingresar manualmente su información personal cada vez que desee realizar una compra. Por esta razón, el uso de un nombre de usuario y una contraseña para asegurar una cuenta es el método más extendido de autenticación que veremos una y otra vez, teniendo en cuenta este equilibrio de conveniencia y seguridad.

Pandasecurity ha compilado[estadística](https://www.pandasecurity.com/en/mediacenter/tips/password-statistics/)En varios aspectos de las contraseñas que nos dan una buena visión general de cómo y de qué manera se usan las contraseñas en todo el mundo. De interés para nosotros sería la entrada que describe`24% of Americans`he usado contraseñas como`password`, `Qwerty`, o`123456`. Entonces, en teoría, podríamos comprometer con éxito sistemas utilizando estas tres contraseñas en muchas organizaciones diferentes debido a su uso generalizado.

Otro interesante[estadística](https://storage.googleapis.com/gweb-uniblog-publish-prod/documents/PasswordCheckup-HarrisPoll-InfographicFINAL.pdf)fue creado por Google. Esta estadística nos muestra, por ejemplo, otras contraseñas utilizadas por el 24% de los estadounidenses. También podemos ver que`22%`usó su`name`, y`33%`usó el nombre de su`pet`o`children`. Otra estadística crítica para nosotros es el`password re-use`de una contraseña ya utilizada para más de una cuenta,`66%`. Esto significa que el 66% de todos los estadounidenses, según esta estadística, han utilizado la misma contraseña para múltiples plataformas. Por lo tanto, una vez que hayamos obtenido o adivinado una contraseña, existe un 66% de posibilidades de que podamos usarla para autenticarnos en otras plataformas con la ID del usuario (nombre de usuario o dirección de correo electrónico). Esto, por supuesto, requeriría que podamos adivinar la ID de usuario del usuario, que, en muchos casos, no es difícil de hacer.

Un aspecto de esta estadística que es algo más difícil de entender es que solo el 45% de los estadounidenses cambiarían sus contraseñas después de una violación de datos. Esto, a su vez, significa que`55% still keep the password`a pesar de que ya se ha filtrado. También podemos verificar si una de nuestras direcciones de correo electrónico se ve afectada por varias infracciones de datos. Una de las fuentes más conocidas para esto es[Haveibeenpwned](https://haveibeenpwned.com/). Ingresamos una dirección de correo electrónico en el sitio web de HaveibeenPwned, y verifica su base de datos si la dirección de correo electrónico ya se ha visto afectada por cualquier violación de datos informada. Si este es el caso, veremos una lista de todas las infracciones en las que aparece nuestra dirección de correo electrónico.

---

# **Cavando**

Ahora que hemos definido qué es una contraseña, cómo las usamos y los principios de seguridad comunes, sumergirnos en cómo almacenamos contraseñas y otras credenciales.