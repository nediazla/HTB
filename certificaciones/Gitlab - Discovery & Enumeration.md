# Gitlab - Discovery & Enumeration

[Gitlab](https://about.gitlab.com/)es una herramienta de alojamiento de repositorios GIT basada en la web que proporciona capacidades de wiki, seguimiento de problemas y funcionalidad continua de integración e implementación de tuberías. Es de código abierto y originalmente escrito en Ruby, pero la pila de tecnología actual incluye Go, Ruby on Rails y Vue.js. Gitlab se lanzó por primera vez en 2014 y, a lo largo de los años, se ha convertido en una compañía de 1.400 personas con ingresos de $ 150 millones en 2020. Aunque la aplicación es gratuita y de código abierto, también ofrecen una versión empresarial pagada. Aquí hay algunos rápidos[estadísticas](https://about.gitlab.com/company/)Acerca de Gitlab:

- Al momento de escribir este artículo, la compañía tiene 1.466 empleados
- GitLab tiene más de 30 millones de usuarios registrados ubicados en 66 países
- La compañía publica la mayoría de sus procedimientos internos y OKRS públicamente en su sitio web
- Algunas compañías que usan Gitlab incluyen Drupal, Goldman Sachs, Hackerone, Ticketmaster, Nvidia, Siemens y[más](https://about.gitlab.com/customers/)

GITLAB es similar a GitHub y Bitbucket, que también son herramientas de repositorio Git basadas en la web. Se puede ver una comparación entre los tres[aquí](https://stackshare.io/stackups/bitbucket-vs-github-vs-gitlab).

Durante las pruebas de penetración internas y externas, es común encontrar datos interesantes en el repositorio de GitHub de una empresa o una instancia de Gitlab o Bitbucket de una empresa. Estos repositorios GIT pueden mantener el código disponible públicamente, como los scripts para interactuar con una API. Sin embargo, también podemos encontrar scripts o archivos de configuración que se comprometieron accidentalmente que contenían secretos ClearText, como contraseñas que podemos usar para nuestra ventaja. También podemos encontrarnos con claves privadas SSH. Podemos intentar usar la función de búsqueda para buscar usuarios, contraseñas, etc., las aplicaciones como GITLAB permiten repositorios públicos (que no requieren autenticación), repositorios internos (disponibles para usuarios autenticados) y repositorios privados (restringidos a usuarios específicos). También vale la pena examinar los repositorios públicos para datos confidenciales y, si la aplicación lo permite, registre una cuenta y busque si se puede acceder a repositorios internos interesantes. La mayoría de las empresas solo permitirán que un usuario con una dirección de correo electrónico de la empresa se registre y requerirá que un administrador autorice la cuenta, pero como veremos más adelante, se puede configurar una instancia de GitLab para permitir que cualquiera se registre y luego inicie sesión.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_signup_res.png)

Si podemos obtener credenciales de usuario de nuestra OSINT, podemos iniciar sesión en una instancia de GitLab. La autenticación de dos factores está deshabilitada de forma predeterminada.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_2fa.png)

---

# **Huella y descubrimiento**

Podemos determinar rápidamente que Gitlab está en uso en un entorno simplemente navegando a la URL de GitLab, y se dirigiremos a la página de inicio de sesión, que muestra el logotipo de GitLab.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_login.png)

La única forma de huella del número de versión de gitlab en uso es navegando a la`/help`Página Cuando se registra. Si la instancia de GitLab nos permite registrar una cuenta, podemos iniciar sesión y navegar a esta página para confirmar la versión. Si no podemos registrar una cuenta, es posible que tengamos que probar un exploit de bajo riesgo como[este](https://www.exploit-db.com/exploits/49821). No recomendamos lanzar varias exploits en una aplicación, por lo que si no tenemos forma de enumerar el número de versión (como una fecha en la página, la primera confirmación pública o registrando a un usuario), entonces debemos seguir buscando secretos y no probar múltiples exploits contra ella a ciegas. Ha habido algunas hazañas serias contra Gitlab[12.9.0](https://www.exploit-db.com/exploits/48431)y gitlab[11.4.7](https://www.exploit-db.com/exploits/49257)En los últimos años, así como Gitlab Community Edition[13.10.3](https://www.exploit-db.com/exploits/49821), [13.9.3](https://www.exploit-db.com/exploits/49944), y[13.10.2](https://www.exploit-db.com/exploits/49951).

---

# **Enumeración**

No hay mucho que podamos hacer contra Gitlab sin conocer el número de versión o estar iniciado. Lo primero que debemos probar es navegar por`/explore`Y vea si hay algún proyecto público que pueda contener algo interesante. Navegando a esta página, vemos un proyecto llamado`Inlanefreight dev`. Los proyectos públicos pueden ser interesantes porque podemos usarlos para obtener más información sobre la infraestructura de la compañía, encontrar el código de producción en el que podamos encontrar un error después de una revisión de código, credenciales codificadas, un script o un archivo de configuración que contiene credenciales u otros secretos, como una clave privada SSH o clave API.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_explore.png)

Al navegar al proyecto, parece un proyecto de ejemplo y puede no contener nada útil, aunque siempre vale la pena cavar.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_example.png)

Desde aquí, podemos explorar cada una de las páginas vinculadas en la parte superior izquierda`groups`, `snippets`, y`help`. También podemos usar la funcionalidad de búsqueda y ver si podemos descubrir otros proyectos. Una vez que hayamos terminado de cavar lo que está disponible externamente, debemos verificar y ver si podemos registrar una cuenta y acceder a proyectos adicionales. Supongamos que la organización no configuró GITLAB solo para permitir que los correos electrónicos de la compañía se registren o requieren que un administrador apruebe una nueva cuenta. En ese caso, podemos acceder a datos adicionales.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_signup.png)

También podemos usar el formulario de registro para enumerar a los usuarios válidos (más sobre esto en la siguiente sección). Si podemos hacer una lista de usuarios válidos, podríamos intentar adivinar contraseñas débiles o posiblemente reutilizar las credenciales que encontramos de un volcado de contraseña utilizando una herramienta como`Dehashed`como se ve en la sección Osticket. Aquí podemos ver al usuario`root`se toma. Veremos otro ejemplo de enumeración del nombre de usuario en la siguiente sección. En este caso particular de Gitlab (y probablemente otros), también podemos enumerar los correos electrónicos. Si intentamos registrarnos con un correo electrónico que ya se ha tomado, recibiremos el error`1 error prohibited this user from being saved: Email has already been taken`. Al momento de escribir, esta técnica de enumeración de nombre de usuario funciona con la última versión de GitLab. Incluso si el`Sign-up enabled`La casilla de verificación se borra dentro de la página de configuración en`Sign-up restrictions`, todavía podemos navegar al`/users/sign_up`Página y usuarios enumerados, pero no podrán registrar a un usuario.

Se pueden establecer algunas mitigaciones para esto, como hacer cumplir 2FA en todas las cuentas de usuario, utilizando`Fail2Ban`Para bloquear los intentos de inicio de sesión fallidos que son indicativos de ataques de forzamiento bruto e incluso restringir qué direcciones IP pueden acceder a una instancia de GitLab si debe ser accesible fuera de la red corporativa interna.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_taken2.png)

Sigamos adelante y registrémonos con las credenciales`hacker:Welcome`Y inicie sesión y hurgando. Tan pronto como completemos el registro, registramos y nos llevan a la página del tablero de proyectos. Si vamos al`/explore`página ahora, notamos que ahora hay un proyecto interno`Inlanefreight website`Disponible para nosotros. Cavando un poco, este parece ser un sitio web estático para la empresa. Supongamos que este era otro tipo de aplicación (como PHP). En ese caso, posiblemente podríamos descargar la fuente y revisarla para vulnerabilidades o funcionalidad oculta o encontrar credenciales u otros datos confidenciales.

![](https://academy.hackthebox.com/storage/modules/113/gitlab_internal.png)

En un escenario del mundo real, podemos encontrar una cantidad considerable de datos confidenciales si podemos registrar y obtener acceso a cualquiera de sus repositorios. Como este[blog](https://tillsongalloway.com/finding-sensitive-information-on-github/index.html)explica que hay una cantidad considerable de datos que podemos descubrir en Gitlab, GitHub, etc.

---

# **Adelante**

Esta sección nos muestra la importancia (y el poder) de la enumeración y que no todas las aplicaciones que descubrimos deben ser directamente explotables para ser muy interesantes y útiles para nosotros durante un compromiso. Esto es especialmente cierto en las pruebas de penetración externa donde la superficie de ataque suele ser considerablemente más pequeña que una evaluación interna. Es posible que necesitemos recopilar datos de dos o más fuentes para montar un ataque exitoso.