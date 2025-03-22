# Administradores de contraseñas

Parece que todo requiere una contraseña hoy en día. Utilizamos contraseñas para nuestra Wi-Fi en el hogar, redes sociales, cuentas bancarias, correos electrónicos comerciales e incluso nuestras aplicaciones y sitios web favoritos. Según esto[Estudio de Nordpass](https://www.techradar.com/news/most-people-have-25-more-passwords-than-at-the-start-of-the-pandemic), la persona promedio tiene 100 contraseñas, que es una de las razones por las que la mayoría de las personas reutilizan contraseñas o crean contraseñas simples.

Con todo esto en mente, necesitamos contraseñas diferentes y seguras, pero no todos pueden memorizar cientos de contraseñas que cumplan con la complejidad requerida para que una contraseña sea segura. Necesitamos algo que pueda ayudarnos a organizar nuestras contraseñas de forma segura. A[Administrador de contraseñas](https://en.wikipedia.org/wiki/Password_manager)es una aplicación que permite a los usuarios almacenar sus contraseñas y secretos en una base de datos cifrada. Además de mantener seguros nuestras contraseñas y datos confidenciales, también tienen características para generar y administrar contraseñas robustas y únicas, 2FA, rellenar formularios web, integración del navegador, sincronización entre múltiples dispositivos, alertas de seguridad, entre otras características.

---

# **¿Cómo funciona un administrador de contraseñas?**

La implementación de los administradores de contraseñas varía según el fabricante, pero la mayoría funciona con una contraseña maestra para cifrar la base de datos.

El funcionamiento de cifrado y autenticación utilizando diferentes[Funciones criptográficas hash](https://en.wikipedia.org/wiki/Cryptographic_hash_function)y[Funciones de derivaciones clave](https://en.wikipedia.org/wiki/Key_derivation_function), para evitar el acceso no autorizado a nuestra base de datos de contraseña cifrada y su contenido. La forma en que funciona depende del fabricante y si el administrador de contraseñas está fuera de línea o en línea.

Desglosemos los administradores de contraseñas comunes y cómo funcionan.

---

# **Administradores de contraseñas en línea**

Uno de los elementos clave al decidir sobre un administrador de contraseñas es la conveniencia. Una persona típica tiene 3 o 4 dispositivos y los utiliza para iniciar sesión en diferentes sitios web, aplicaciones, etc. Un administrador de contraseñas en línea permite al usuario sincronizar su base de datos de contraseña cifrada entre dispositivos múltiples, la mayoría de ellos proporciona:

- Una aplicación móvil.
- Un complemento de navegador.
- Algunas otras características que discutiremos más adelante en esta sección.

Todos los proveedores de Administrador de contraseñas tienen su forma de administrar su implementación de seguridad, y generalmente proporcionan un documento técnico que describe cómo funciona. Puedes comprobar[Bitwarden](https://bitwarden.com/images/resources/security-white-paper-download.pdf), [1 paso de paso](https://1passwordstatic.com/files/security/1password-white-paper.pdf)y[Último paso](https://assets.cdngetgo.com/da/ce/d211c1074dea84e06cad6f2c8b8e/lastpass-technical-whitepaper.pdf)documentación como referencia, pero hay muchos otros. Hablemos de cómo funciona esto generalmente.

Una implementación común para los administradores de contraseñas en línea está derivando claves basadas en la contraseña maestra. Su propósito es proporcionar un[Cifrado de conocimiento cero](https://blog.cubbit.io/blog-posts/what-is-zero-knowledge-encryption), lo que significa que nadie, excepto usted (ni siquiera el proveedor de servicios), puede acceder a sus datos asegurados. Para lograr esto, comúnmente derivan la contraseña maestra. Usemos la implementación técnica de BitWarden para la derivación de la contraseña para explicar cómo funciona:

1. Clave maestra: creada por alguna función para convertir la contraseña maestra en un hash.
2. Master Password Hash: Creado por alguna función para convertir la contraseña maestra con una combinación de la tecla maestra en un hash para autenticarse en la nube.
3. Clave de descifrado: creada por alguna función utilizando la tecla maestra para formar una clave simétrica para descifrar elementos de bóveda.

![](https://academy.hackthebox.com/storage/modules/147/bitwarden_diagram.png)

Esta es una forma simple de ilustrar cómo funcionan los administradores de contraseñas, pero la implementación común es más compleja. Puede consultar los documentos técnicos anteriores o ver el[Cómo funcionan los administradores de contraseñas - ComputerPhile](https://www.youtube.com/watch?v=w68BBPDAWr8)video.

Los administradores de contraseñas en línea más populares son:

1. [1 paso de paso](https://1password.com/)
2. [Bitwarden](https://bitwarden.com/)
3. [Dashlane](https://www.dashlane.com/)
4. [Guardián](https://www.keepersecurity.com/)
5. [Último paso](https://www.lastpass.com/)
6. [Nordpass](https://nordpass.com/)
7. [Roboforma](https://www.roboform.com/)

---

# **Administradores de contraseñas locales**

Algunas compañías e individuos prefieren administrar su seguridad por diferentes razones y no depender de los servicios proporcionados por terceros. Los administradores de contraseñas locales ofrecen esta opción al almacenar la base de datos localmente y poner la responsabilidad del usuario para proteger su contenido y la ubicación donde se almacena.[Dashlane](https://www.dashlane.com/)escribió una publicación de blog[Almacenamiento del Administrador de contraseñas: Cloud versus local](https://blog.dashlane.com/password-storage-cloud-versus-local/)lo que puede ayudarlo a descubrir los pros y los contras de cada almacenamiento. La publicación del blog dice: "Al principio puede parecer que esto hace que el almacenamiento local sea más seguro que el almacenamiento en la nube, pero la ciberseguridad no es una disciplina simple". Puede usar este blog para comenzar su investigación y comprender qué método serviría mejor a los diferentes escenarios en los que necesita administrar contraseñas.

Los administradores de contraseñas locales encriptan el archivo de la base de datos utilizando una clave maestra. La clave maestra puede consistir en uno o múltiples componentes: una contraseña maestra, un archivo de clave, un nombre de usuario, contraseña, etc. Por lo general, se requieren todas las partes de la clave maestra para acceder a la base de datos.

El cifrado de los administradores de contraseñas locales es similar a las implementaciones en la nube. La diferencia más notable es la transmisión de datos y la autenticación. Para cifrar la base de datos, los administradores de contraseñas locales se centran en asegurar la base de datos local utilizando diferentes funciones de hash criptográfico (dependiendo del fabricante). También usan la función de derivación clave (sal aleatoria) para evitar claves de precomputación y obstaculizar el diccionario y los ataques de adivinanzas. Algunos ofrecen protección de memoria y protección de keylogger utilizando un escritorio seguro, similar al control de cuenta de usuario (UAC) de Windows.

Los administradores de contraseñas locales más populares son:

1. [Conserva](https://keepass.info/)
2. [Kwalletmanager](https://apps.kde.org/kwalletmanager5/)
3. [Servidor de contraseñas agradable](https://pleasantpasswords.com/)
4. [Contraseña segura](https://pwsafe.org/)

---

# **Características**

Imaginemos que usamos Linux, Android y Chrome OS. Accedemos a todas nuestras aplicaciones y sitios web desde cualquier dispositivo. Queremos sincronizar todas las contraseñas y asegurar notas en todos los dispositivos. Necesitamos protección adicional con 2FA, y nuestro presupuesto es 1USD mensual. Esa información puede ayudarnos a identificar el administrador de contraseñas correcto para nosotros.

Al decidir sobre un administrador de contraseñas en la nube o local, necesitamos comprender sus características,[Wikipedia](https://en.wikipedia.org/wiki/List_of_password_managers)Tiene una lista de administradores de contraseñas (en línea y locales), así como algunas de sus características. Aquí hay una lista de las características más comunes para los administradores de contraseñas:

1. [2fa](https://authy.com/what-is-2fa/)apoyo.
2. Multiplataforma (Android, iOS, Windows, Linux, Mac, etc.).
3. Extensión del navegador.
4. Iniciar sesión en autocompleto.
5. Capacidades de importación y exportación.
6. Generación de contraseñas.

---

# **Alternativas**

Las contraseñas son la forma más común de autenticación, pero no la única. A medida que aprendemos de este módulo, hay múltiples formas de comprometer una contraseña, grietas, adivinanzas, surf de hombros, etc., pero ¿qué pasa si no necesitamos una contraseña para iniciar sesión? ¿Es tal cosa posible?

Por defecto, la mayoría de los sistemas y aplicaciones operativos no admiten ninguna alternativa a una contraseña. Aún así, los administradores pueden usar proveedores o aplicaciones de identidad de terceros para configurar o mejorar la protección de la identidad en sus organizaciones. Algunas de las formas más comunes de asegurar identidades más allá de las contraseñas son:

1. [Autenticación multifactor](https://en.wikipedia.org/wiki/Multi-factor_authentication).
2. [Fido2](https://fidoalliance.org/fido2/)Abra el estándar de autenticación, que permite a los usuarios aprovechar dispositivos comunes como[Yubikey](https://www.yubico.com/), para autenticarse fácilmente. Para una lista de dispositivos más extendidos, puede ver[Proveedores de clave de seguridad de Microsoft Fido2](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-passwordless#fido2-security-key-providers).
3. [Contraseña única (OTP)](https://en.wikipedia.org/wiki/One-time_password).
4. [Contraseña de un solo tiempo basada en el tiempo (TOTP)](https://en.wikipedia.org/wiki/Time-based_one-time_password).
5. [Restricción de IP](https://news.gandi.net/en/2019/05/using-ip-restriction-to-help-secure-your-account/).
6. Cumplimiento del dispositivo. Ejemplos:[Gerente de punto final](https://www.petervanderwoude.nl/post/tag/device-compliance/)o[Espacio de trabajo uno](https://www.loginconsultants.com/enabling-the-device-compliance-with-workspace-one-uem-authentication-policy-in-workspace-one-access)

---

# **Sin contraseña**

Multiples empresas como[Microsoft](https://www.microsoft.com/en-us), [Auth0](https://auth0.com/), [Okta](https://www.okta.com/), [Identidad de ping](https://www.pingidentity.com/en.html), etc., están tratando de promover el[Sin contraseña](https://en.wikipedia.org/wiki/Passwordless_authentication)estrategia, para eliminar la contraseña como la forma de autenticación.

[Sin contraseña](https://www.pingidentity.com/en/resources/blog/posts/2021/what-does-passwordless-really-mean.html)La autenticación se logra cuando se utiliza un factor de autenticación que no sea una contraseña. Una contraseña es un factor de conocimiento, lo que significa que es algo que un usuario conoce. El problema con confiar solo en un factor de conocimiento es que es vulnerable al robo, el intercambio, el uso repetido, el mal uso y otros riesgos. La autenticación sin contraseña, en última instancia, no significa más contraseñas. En cambio, se basa en un factor de posesión, algo que un usuario tiene o un factor inherente, que un usuario es, para verificar la identidad del usuario con mayor garantía.

A medida que evolucionan las nuevas tecnologías y los estándares, necesitamos investigar y comprender los detalles de su implementación para comprender si esas alternativas proporcionarán o no la seguridad que necesitamos para el proceso de autenticación. Puede leer más sobre autenticación sin contraseña y estrategias de diferentes proveedores:

1. [Microsoft sin contraseña](https://www.microsoft.com/en-us/security/business/identity-access-management/passwordless-authentication)
2. [Auth0 sin contraseña](https://auth0.com/passwordless)
3. [Okta sin contraseña](https://www.okta.com/passwordless-authentication/)
4. [Pingidentalidad](https://www.pingidentity.com/en/resources/blog/posts/2021/what-does-passwordless-really-mean.html)

Hay muchas opciones cuando se trata de proteger las contraseñas. Elegir el correcto se reducirá a los requisitos del individuo o la empresa. Es común que las personas y las empresas usen diferentes métodos de protección de contraseñas para diversos fines.

[Anterior](https://academy.hackthebox.com/module/147/section/1331)