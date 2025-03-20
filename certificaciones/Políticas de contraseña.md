# Políticas de contraseña

Ahora que hemos trabajado a través de numerosas formas de capturar credenciales y contraseñas, cubramos algunas mejores prácticas relacionadas con contraseñas y protección de identidad. Los límites de velocidad y las leyes de tráfico existen para que manejemos de manera segura. Sin ellos, conducir sería el caos. Lo mismo sucede cuando una empresa no tiene políticas adecuadas en su lugar; Todos podrían hacer lo que quieran sin consecuencias. Es por eso que los proveedores y administradores de servicios usan diferentes políticas y aplican métodos para hacerlos cumplir para una mejor seguridad.

Conozcamos a Mark, un nuevo empleado de InLaneFreight Corp. Mark, no funciona en él, y no es consciente del riesgo de una contraseña débil. Necesita establecer su contraseña para su correo electrónico comercial. Elige la contraseña`password123`. Sin embargo, recibe un error al decir que la contraseña no cumple con la política de contraseña de la compañía y un mensaje que le permite saber el requisito mínimo para que la contraseña sea más segura.

En este ejemplo, tenemos dos piezas esenciales, una definición de la política de contraseña y la aplicación. La definición es una guía, y la aplicación es la tecnología utilizada para hacer que los usuarios cumplan con la política. Ambos aspectos de la implementación de la política de contraseña son esenciales. Durante esta lección, exploraremos ambos y comprenderemos cómo podemos crear una política de contraseña efectiva y su implementación.

---

# **Política de contraseña**

A[Política de contraseña](https://en.wikipedia.org/wiki/Password_policy)es un conjunto de reglas diseñadas para mejorar la seguridad informática al alentar a los usuarios a emplear contraseñas seguras y usarlas adecuadamente en función de la definición de la empresa. El alcance de una política de contraseña no se limita a los requisitos mínimos de contraseña, sino todo el ciclo de vida de una contraseña (como manipulación, almacenamiento y transmisión).

---

# **Normas de política de contraseña**

Debido al cumplimiento y las mejores prácticas, muchas empresas usan[Estándares de seguridad de TI](https://en.wikipedia.org/wiki/IT_security_standards). Aunque cumplir con un estándar no significa que somos 100% seguros, es una práctica común dentro de la industria que define una línea de base de controles de seguridad para las organizaciones. Esa no debería ser la única forma de medir la efectividad de los controles de seguridad organizacionales.

Algunos estándares de seguridad incluyen una sección para políticas de contraseña o pautas de contraseña. Aquí hay una lista de los más comunes:

1. [NIST SP800-63B](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-63b.pdf)
2. [Guía de política de contraseña de cis](https://www.cisecurity.org/insights/white-papers/cis-password-policy-guide)
3. [PCI DSS](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)

Podemos usar esos estándares para comprender diferentes perspectivas de las políticas de contraseña. Después de eso, podemos usar esta información para crear nuestra política de contraseña. Tomemos un caso de uso donde diferentes estándares usen un enfoque diferente,`password expiration`.

`Change your password periodically (e.g., 90 days) to be more secure`Puede ser una frase que escuchamos un par de veces, pero la verdad es que no todas las empresas están utilizando esta política. Algunas compañías solo requieren que sus usuarios cambien sus contraseñas cuando hay evidencia de compromiso. Si observamos algunos de los estándares anteriores, algunos requieren que los usuarios cambien la contraseña periódicamente, y otros no. Debemos detenernos y pensar, desafiar los estándares y definir qué es lo mejor para nuestras necesidades.

---

# **Recomendaciones de política de contraseña**

Permítanos crear una política de contraseña de muestra para ilustrar algunas cosas importantes para tener en cuenta al crear una política de contraseña. Nuestra política de contraseña de muestra indica que todas las contraseñas deben:

- Mínimo de 8 caracteres.
- Incluya letras mayúsculas y minúsculas.
- Incluya al menos un número.
- Incluya al menos un personaje especial.
- No debería ser el nombre de usuario.
- Debe cambiarse cada 60 días.

Nuestro nuevo empleado, Mark, que recibió un error al crear el correo electrónico con la contraseña`password123`, ahora elige la siguiente contraseña`Inlanefreight01!`y registra con éxito su cuenta. Aunque esta contraseña cumple con las políticas de la empresa, no es segura y fácilmente adivinable porque utiliza el nombre de la empresa como parte de la contraseña. Aprendimos en la sección "Mutaciones de contraseña" que esta es una práctica común de los empleados, y los atacantes son conscientes de esto.

Una vez que esta contraseña alcanza el tiempo de vencimiento, Mark puede cambiar de 01 a 02, y su contraseña cumple con la política de contraseña de la empresa, pero la contraseña es casi la misma. Debido a esto, los profesionales de la seguridad tienen una discusión abierta sobre el vencimiento de la contraseña y cuándo exigir que un usuario cambie su contraseña.

Según este ejemplo, debemos incluir, como parte de nuestras políticas de contraseña, algunas palabras en la lista negra, que incluyen, entre otros::

- Nombre de la empresa
- Palabras comunes asociadas con la empresa
- Nombres de meses
- Nombres de estaciones
- Variaciones en la palabra Bienvenido y contraseña
- Palabras comunes y adivinables como contraseña, 123456 y ABCDE

---

# **Cumplimiento de la política de contraseña**

Una política de contraseña es una guía que define cómo debemos crear, manipular y almacenar contraseñas en la organización. Para aplicar esta guía, necesitamos aplicarla, usar la tecnología a nuestra disposición o adquirir lo que necesita hacer que esto funcione. La mayoría de las aplicaciones y los gerentes de identidad proporcionan métodos para aplicar nuestra política de contraseña.

Por ejemplo, si usamos Active Directory para la autenticación, necesitamos configurar un[Política de contraseña de Active Directory GPO](https://activedirectorypro.com/how-to-configure-a-domain-password-policy/), para hacer cumplir nuestros usuarios para cumplir con nuestra política de contraseña.

Una vez que se cubre el aspecto técnico, necesitamos comunicar la política a la empresa y crear procesos y procedimientos para garantizar que nuestra política de contraseña se aplique en todas partes.

---

# **Creando una buena contraseña**

Crear una buena contraseña puede ser fácil. Usemos[Contraseña](https://www.passwordmonster.com/), un sitio web que nos ayuda a probar cuán sólidos son nuestras contraseñas y[Generador de contraseña de 1 Password](https://1password.com/password-generator/), otro sitio web para generar contraseñas seguras.

![](https://academy.hackthebox.com/storage/modules/147/strong_password_1.png)

`CjDC2x[U`fue la contraseña generada por la herramienta, y es una buena contraseña. Tomaría mucho tiempo descifrar y probablemente no se adivinaría u obtener en un ataque de pulverización de contraseña, pero es difícil de recordar.

Podemos crear buenas contraseñas con palabras ordinarias, frases e incluso canciones que nos gusten. Aquí hay un ejemplo de una buena contraseña`This is my secure password`o`The name of my dog is Poppy`. Podemos combinar esas contraseñas con caracteres especiales para que sean más complejos, como`()The name of my dog is Poppy!`. Aunque es difícil de adivinar, debemos tener en cuenta que los atacantes pueden usar OSINT para aprender sobre nosotros, y debemos tener esto en cuenta al crear contraseñas.

![](https://academy.hackthebox.com/storage/modules/147/strong_password_phrase.png)

Con este método, podemos crear y memorizar 3, 4 o más contraseñas, pero a medida que aumente la lista, será difícil recordar todas nuestras contraseñas. En la siguiente sección, discutiremos el uso de un administrador de contraseñas para ayudarnos a crear y mantener la gran cantidad de contraseñas que tenemos.