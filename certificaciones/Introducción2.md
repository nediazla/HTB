# Introducción

Las claves y las contraseñas, el equivalente moderno de las cerraduras y las combinaciones, aseguran el mundo digital. Pero, ¿qué pasa si alguien intenta todas las combinaciones posibles hasta que encuentre la que abre la puerta? Que, en esencia, es`brute forcing`.

# **¿Qué es el forzamiento bruto?**

En ciberseguridad, el forzamiento bruto es un método de prueba y error utilizado para descifrar contraseñas, credenciales de inicio de sesión o claves de cifrado. Implica probar sistemáticamente todas las combinaciones posibles de caracteres hasta que se encuentre el correcto. El proceso puede compararse con un ladrón que intente cada llave en un llavero gigante hasta que encuentre el que desbloquea el cofre del tesoro.

El éxito de un ataque de fuerza bruta depende de varios factores, incluidos:

- El`complexity`de la contraseña o clave. Las contraseñas más largas con una combinación de letras, números y símbolos en minúsculas y minúsculas son exponencialmente más complejas para romper.
- El`computational power`Disponible para el atacante. Las computadoras modernas y el hardware especializado pueden probar miles de millones de combinaciones por segundo, reduciendo significativamente el tiempo necesario para un ataque exitoso.
- El`security measures`en su lugar. Los bloqueos de cuentas, Captchas y otras defensas pueden ralentizar o incluso frustrar los intentos de fuerza bruta.

# **Cómo funciona el forzamiento bruto**

El proceso de fuerza bruta se puede visualizar de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/57/1n.png)

1. `Start`: El atacante inicia el proceso de fuerza bruta, a menudo con la ayuda de software especializado.
2. `Generate Possible Combination`: El software genera una contraseña potencial o una combinación de clave basada en parámetros predefinidos, como conjuntos de caracteres y longitud.
3. `Apply Combination`: La combinación generada se intenta con el sistema de destino, como un formulario de inicio de sesión o un archivo encriptado.
4. `Check if Successful`: El sistema evalúa el intento de combinación. Si coincide con la contraseña o clave almacenada, se otorga el acceso. De lo contrario, el proceso continúa.
5. `Access Granted`: El atacante gana acceso no autorizado al sistema o datos.
6. `End`: El proceso repite, genere y prueba nuevas combinaciones hasta que se encuentre la correcta o el atacante se rinda.

# **Tipos de forzamiento bruto**

El forzamiento bruto no es una entidad monolítica, sino una colección de técnicas diversas, cada una con sus fortalezas, debilidades y casos de uso ideales. Comprender estas variaciones es crucial tanto para los atacantes como para los defensores, ya que permite al primero elegir el enfoque más efectivo y el segundo para implementar contramedidas dirigidas. La siguiente tabla proporciona una descripción comparativa de varios métodos de forzamiento bruto:

| **Método** | **Descripción** | **Ejemplo** | **Mejor usado cuando ...** |
| --- | --- | --- | --- |
| `Simple Brute Force` | Intenta sistemáticamente todas las combinaciones posibles de caracteres dentro de un conjunto de caracteres y rango de longitud definidos. | Probar todas las combinaciones de letras minúsculas de 'a' a 'z' para contraseñas de longitud 4 a 6. | No hay información previa sobre la contraseña disponible, y los recursos computacionales son abundantes. |
| `Dictionary Attack` | Utiliza una lista precompilada de palabras, frases y contraseñas comunes. | Probar contraseñas desde una lista como 'Rockyou.txt' en un formulario de inicio de sesión. | El objetivo probablemente usará una contraseña débil o fácilmente adivinable basada en patrones comunes. |
| `Hybrid Attack` | Combina elementos de la fuerza bruta y los ataques de diccionario, a menudo agregando o prependiendo personajes a las palabras del diccionario. | Agregar números o caracteres especiales al final de las palabras de una lista de diccionario. | El objetivo puede usar una versión ligeramente modificada de una contraseña común. |
| `Credential Stuffing` | Aproveche las credenciales filtradas de un servicio para intentar acceso a otros servicios, suponiendo que los usuarios reutilicen las contraseñas. | Uso de una lista de nombres de usuario y contraseñas filtradas de una violación de datos para intentar iniciar sesión en varias cuentas en línea. | Hay un gran conjunto de credenciales filtradas disponibles, y se sospecha que el objetivo reutiliza contraseñas en múltiples servicios. |
| `Password Spraying` | Intentos de un pequeño conjunto de contraseñas de uso común en una gran cantidad de nombres de usuario. | Probar contraseñas como 'Password123' o 'Qwerty' contra todos los nombres de usuario en una organización. | Las políticas de bloqueo de la cuenta están en su lugar, y el atacante tiene como objetivo evitar la detección de la extensión de intentos en múltiples cuentas. |
| `Rainbow Table Attack` | Utiliza tablas de hash de contraseña precomputadas para revertir los hashes y recuperar las contraseñas de texto sin formato rápidamente. | Los hashes previos a la computar para todas las contraseñas posibles de una cierta longitud y conjunto de caracteres, luego comparando hashes capturados contra la tabla para encontrar coincidencias. | Una gran cantidad de hash de contraseña deben ser agrietados, y el espacio de almacenamiento para las tablas Rainbow está disponible. |
| `Reverse Brute Force` | Se dirige una sola contraseña contra múltiples nombres de usuario, a menudo utilizados junto con ataques de relleno de credenciales. | Uso de una contraseña filtrada de un servicio para intentar iniciar sesión en múltiples cuentas con diferentes nombres de usuario. | Existe una fuerte sospecha de que se está reutilizando una contraseña particular en múltiples cuentas. |
| `Distributed Brute Force` | Distribuye la carga de trabajo de forzamiento bruto en múltiples computadoras o dispositivos para acelerar el proceso. | El uso de un clúster de computadoras para realizar un ataque de fuerza bruta aumenta significativamente el número de combinaciones que pueden probarse por segundo. | La contraseña o clave de destino es altamente compleja, y una sola máquina carece del poder computacional para romperla dentro de un plazo razonable. |

# **El papel del forzamiento bruto en las pruebas de penetración**

Las pruebas de penetración, o piratería ética, es una medida de seguridad cibernética proactiva que simula ataques del mundo real para identificar y abordar las vulnerabilidades antes de que los actores maliciosos puedan explotarlos. El forzamiento bruto es una herramienta crucial en este proceso, particularmente al evaluar la resiliencia de los mecanismos de autenticación basados en contraseña.

Si bien las pruebas de penetración abarcan una variedad de técnicas, el forzamiento bruto a menudo se emplea estratégicamente cuando:

- `Other avenues are exhausted`: Los intentos iniciales de obtener acceso, como explotar vulnerabilidades conocidas o utilizar tácticas de ingeniería social, pueden resultar sin éxito. En tales escenarios, el forzamiento bruto es una alternativa viable para superar las barreras de contraseña.
- `Password policies are weak`: Si el sistema de destino emplea políticas de contraseña laxa, aumenta la probabilidad de que los usuarios tengan contraseñas débiles o fácilmente adivinables. El forzamiento bruto puede exponer efectivamente estas vulnerabilidades.
- `Specific accounts are targeted`: En algunos casos, los probadores de penetración pueden centrarse en comprometer cuentas de usuario específicas, como aquellas con privilegios elevados. El forzamiento bruto se puede adaptar para apuntar a estas cuentas directamente.