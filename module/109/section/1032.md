# Detection

El proceso de detección de vulnerabilidades básicas de inyección de comandos del sistema operativo es el mismo proceso para explotar tales vulnerabilidades. Intentamos agregar nuestro comando a través de varios métodos de inyección. Si la salida del comando cambia del resultado habitual previsto, hemos explotado con éxito la vulnerabilidad. Esto puede no ser cierto para las vulnerabilidades de inyección de comandos más avanzadas porque podemos utilizar varios métodos fuzzantes o revisiones de código para identificar posibles vulnerabilidades de inyección de comandos. Luego podemos construir gradualmente nuestra carga útil hasta que logremos la inyección de comandos. Este módulo se centrará en las inyecciones de comando básicas, donde controlamos la entrada del usuario que se está utilizando directamente en una ejecución de comandos del sistema una función sin ninguna desinfección.

Para demostrar esto, usaremos el ejercicio encontrado al final de esta sección.

---

# **Detección de inyección de comandos**

Cuando visitamos la aplicación web en el siguiente ejercicio, vemos un

```
Host Checker
```

Utilidad que parece pedirnos una IP para verificar si está viva o no:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_exercise_1.jpg)

Podemos intentar ingresar a la IP localhost

```
127.0.0.1
```

Para verificar la funcionalidad y, como se esperaba, devuelve la salida del

```
ping
```

Comando que nos dice que el Hosthost está realmente vivo:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_exercise_2.jpg)

Aunque no tenemos acceso al código fuente de la aplicación web, podemos suponer con confianza que la IP que ingresamos está entrando en un`ping`Comando Dado que la salida que recibimos sugiere eso. Como el resultado muestra un solo paquete transmitido en el comando ping, el comando utilizado puede ser el siguiente:

Código:intento

```bash
ping -c 1 OUR_INPUT

```

Si nuestra entrada no se desinfecta y escapa antes de que se use con el`ping`Comando, podemos inyectar otro comando arbitrario. Por lo tanto, intentemos ver si la aplicación web es vulnerable a la inyección de comando OS.

---

# **Métodos de inyección de comandos**

Para inyectar un comando adicional a la prevista, podemos usar cualquiera de los siguientes operadores:

| **Operador de inyección** | **Carácter de inyección** | **Personaje codificado por la URL** | **Comando ejecutado** |
| --- | --- | --- | --- |
| Punto y coma | `;` | `%3b` | Ambos |
| Nueva línea | `\n` | `%0a` | Ambos |
| Fondo | `&` | `%26` | Ambos (la segunda salida generalmente se muestra primero) |
| Tubo | `|` | `%7c` | Ambos (solo se muestra la segunda salida) |
| Y | `&&` | `%26%26` | Ambos (solo si primero tiene éxito) |
| O | `||` | `%7c%7c` | Segundo (solo si primero falla) |
| Submarino | ```` | `%60%60` | Ambos (solo Linux) |
| Submarino | `$()` | `%24%28%29` | Ambos (solo Linux) |

Podemos usar cualquiera de estos operadores para inyectar otro comando para que`both`o`either`de los comandos se ejecutan.`We would write our expected input (e.g., an IP), then use any of the above operators, and then write our new command.`

Consejo: Además de lo anterior, hay algunos operadores solo de Unix, que funcionarían en Linux y MacOS, pero no funcionarían en Windows, como envolver nuestro comando inyectado con backticks dobles (````) o con un operador de subhorro (`$()`).

En general, para la inyección de comandos básicos, todos estos operadores se pueden usar para inyecciones de comandos`regardless of the web application language, framework, or back-end server`. Entonces, si estamos inyectando en un`PHP`Aplicación web que se ejecuta en un`Linux`servidor, o un`.Net`Aplicación web que se ejecuta en un`Windows`servidor de back-end, o un`NodeJS`Aplicación web que se ejecuta en un`macOS`Servidor de back-end, nuestras inyecciones deberían funcionar independientemente.

NOTA: La única excepción puede ser el semi-colon`;`, que no funcionará si el comando se estaba ejecutando con`Windows Command Line (CMD)`, pero aún funcionaría si se ejecutara con`Windows PowerShell`.

En la siguiente sección, intentaremos usar uno de los operadores de inyección anteriores para explotar el`Host Checker`ejercicio.