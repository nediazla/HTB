# Ataques de diccionario

Si bien es integral, el enfoque de fuerza bruta puede llevar mucho tiempo e intensivo en recursos, especialmente cuando se trata de contraseñas complejas. Ahí es donde entran los ataques del diccionario.

# **El poder de las palabras**

La efectividad de un ataque de diccionario radica en su capacidad para explotar la tendencia humana para priorizar las contraseñas memorables sobre las seguras. A pesar de las advertencias repetidas, muchas personas continúan optando por contraseñas en función de la información fácilmente disponible, como palabras de diccionario, frases comunes, nombres o patrones fácilmente adivinables. Esta previsibilidad los hace vulnerables a los ataques de diccionario, donde los atacantes prueban sistemáticamente una lista predefinida de contraseñas potenciales contra el sistema objetivo.

El éxito de un ataque de diccionario depende de la calidad y especificidad de la lista de palabras utilizada. Una lista de palabras bien elaborada adaptada al público o el sistema objetivo puede aumentar significativamente la probabilidad de una violación exitosa. Por ejemplo, si el objetivo es un sistema frecuentado por los jugadores, una lista de palabras enriquecida con terminología y jerga relacionadas con el juego resultaría más efectiva que un diccionario genérico. Cuanto más de cerca la lista de palabras refleje las posibles opciones de contraseña del objetivo, mayores serán las posibilidades de un ataque exitoso.

En esencia, el concepto de un ataque de diccionario se basa en la comprensión de la psicología humana y las prácticas de contraseña comunes. Al aprovechar esta idea, los atacantes pueden descifrar de eficiencia las contraseñas que de otro modo podrían requerir un ataque de fuerza bruta poco práctica. En este contexto, el poder de las palabras reside en su capacidad para explotar la previsibilidad humana y comprometer medidas de seguridad sólidas.

# **Fuerza bruta vs. Ataque del diccionario**

La distinción fundamental entre una fuerza bruta y un ataque de diccionario radica en su metodología para generar posibles candidatos de contraseña:

- `Brute Force`: Un ataque de fuerza bruta pura prueba sistemáticamente*Cada combinación posible*de caracteres dentro de un conjunto y longitud predeterminados. Si bien este enfoque garantiza un éxito eventual dado suficiente tiempo, puede ser extremadamente lento, particularmente contra contraseñas más largas o complejas.
- `Dictionary Attack`: En marcado contraste, un ataque de diccionario emplea una lista precompilada de palabras y frases, reduciendo drásticamente el espacio de búsqueda. Esta metodología dirigida da como resultado un ataque mucho más eficiente y rápido, especialmente cuando se sospecha que la contraseña de destino es una palabra o frase común.

| **Característica** | **Ataque del diccionario** | **Ataque de fuerza bruta** | **Explicación** |
| --- | --- | --- | --- |
| `Efficiency` | Considerablemente más rápido y más eficiente en recursos. | Puede ser extremadamente lento y intensivo en recursos. | Los ataques de diccionario aprovechan una lista predefinida, reduciendo significativamente el espacio de búsqueda en comparación con la fuerza bruta. |
| `Targeting` | Altamente adaptable y se puede adaptar a objetivos o sistemas específicos. | No hay capacidad de orientación inherente. | Las listas de palabras pueden incorporar información relevante para el objetivo (por ejemplo, nombre de la empresa, nombres de empleados), lo que aumenta la tasa de éxito. |
| `Effectiveness` | Excepcionalmente efectivo contra contraseñas débiles o de uso común. | Efectivo contra todas las contraseñas que reciben tiempo y recursos suficientes. | Si la contraseña de destino está dentro del diccionario, se descubrirá rápidamente. La fuerza bruta, aunque universalmente aplicable, puede no ser práctica para contraseñas complejas debido al gran volumen de combinaciones. |
| `Limitations` | Ineficaz contra contraseñas complejas generadas al azar. | A menudo poco práctico para contraseñas largas o altamente complejas. | Es poco probable que una contraseña verdaderamente aleatoria aparezca en cualquier diccionario, lo que hace que este ataque sea inútil. El número astronómico de posibles combinaciones para largas contraseñas puede hacer que los ataques de fuerza bruta sean inviables. |

Considere un escenario hipotético en el que un atacante se dirige al portal de inicio de sesión de empleados de una empresa. El atacante podría construir una lista de palabras especializada que incorpore lo siguiente:

- Contraseñas débiles y de uso común (por ejemplo, "Password123", "Qwerty")
- El nombre de la empresa y las variaciones de los mismos
- Nombres de empleados o departamentos
- Jerga específica de la industria

Al desplegar esta lista de palabras específica en un ataque de diccionario, el atacante eleva significativamente su probabilidad de descifrar con éxito las contraseñas de los empleados en comparación con un esfuerzo de fuerza bruta puramente aleatoria.

# **Construir y utilizar listas de palabras**

Las listas de palabras se pueden obtener de varias fuentes, que incluyen:

- `Publicly Available Lists`: Internet aloja una gran cantidad de listas de palabras libremente accesibles, que abarcan colecciones de contraseñas de uso común, credenciales filtradas de infracciones de datos y otros datos potencialmente valiosos. Repositorios como[Listas](https://github.com/danielmiessler/SecLists/tree/master/Passwords)Ofrezca varias listas de palabras que atienden a varios escenarios de ataque.
- `Custom-Built Lists`: Los probadores de penetración pueden elaborar sus listas de palabras aprovechando la información obtenida durante la fase de reconocimiento. Esto puede incluir detalles sobre los intereses del objetivo, los pasatiempos, la información personal o cualquier otro datos para la creación de contraseñas.
- `Specialized Lists`: Las listas de palabras pueden refinarse aún más para dirigir industrias, aplicaciones o incluso empresas individuales. Estas listas especializadas aumentan la probabilidad de éxito al centrarse en las contraseñas que tienen más probabilidades de usarse dentro de un contexto particular.
- `Pre-existing Lists`: Ciertas herramientas y marcos vienen preenvasados con listas de palabras comúnmente utilizadas. Por ejemplo, las distribuciones de pruebas de penetración como Parrotsec a menudo incluyen listas de palabras como`rockyou.txt`, una colección masiva de contraseñas filtradas, fácilmente disponibles para su uso.

Aquí hay una tabla de algunas de las listas de palabras más útiles para iniciar sesión en bruto:

| **Lista de palabras** | **Descripción** | **Uso típico** | **Fuente** |
| --- | --- | --- | --- |
| `rockyou.txt` | Una lista de palabras de contraseña popular que contiene millones de contraseñas filtradas de la violación de Rockyou. | Comúnmente utilizado para la contraseña de los ataques de fuerza bruta. | [Conjunto de datos de ritmo de rockyou](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt) |
| `top-usernames-shortlist.txt` | Una lista concisa de los nombres de usuario más comunes. | Adecuado para intentos de nombre de usuario de fuerza bruta rápida. | [Listas](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt) |
| `xato-net-10-million-usernames.txt` | Una lista más extensa de 10 millones de nombres de usuario. | Utilizado para el forzamiento bruto completo de nombre de usuario. | [Listas](https://github.com/danielmiessler/SecLists/blob/master/Usernames/xato-net-10-million-usernames.txt) |
| `2023-200_most_used_passwords.txt` | Una lista de las 200 contraseñas más utilizadas a partir de 2023. | Efectivo para atacar contraseñas comúnmente reutilizadas. | [Listas](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt) |
| `Default-Credentials/default-passwords.txt` | Una lista de nombres de usuario y contraseñas predeterminados comúnmente utilizados en enrutadores, software y otros dispositivos. | Ideal para probar las credenciales predeterminadas. | [Listas](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/default-passwords.txt) |

# **Lanzar un diccionario al problema**

**Para seguir, inicie el sistema de destino a través de la sección de preguntas en la parte inferior de la página.**

La aplicación de instancia crea una ruta (`/diccionario`) que maneja las solicitudes posteriores. Espera un parámetro `contraseña` en los datos de formulario de la solicitud. Al recibir una solicitud, compara la contraseña enviada con el valor esperado. Si hay una coincidencia, responde con un objeto JSON que contiene un mensaje de éxito y el indicador. De lo contrario, devuelve un mensaje de error con un código de estado 401 (no autorizado).

Copie y pegue este script de Python a continuación como`dictionary-solver.py`en tu máquina. Solo necesita modificar las variables IP y puerto para que coincidan con la información de su sistema de destino.

Código:pitón

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Download a list of common passwords from the web and split it into lines
passwords = requests.get("https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/500-worst-passwords.txt").text.splitlines()

# Try each password from the list
for password in passwords:
    print(f"Attempted password: {password}")

    # Send a POST request to the server with the password
    response = requests.post(f"http://{ip}:{port}/dictionary", data={'password': password})

    # Check if the server responds with success and contains the 'flag'
    if response.ok and 'flag' in response.json():
        print(f"Correct password found: {password}")
        print(f"Flag: {response.json()['flag']}")
        break

```

El guión de Python orquesta el ataque del diccionario. Realiza los siguientes pasos:

1. `Downloads the Wordlist`: Primero, el script obtiene una lista de palabras de 500 comúnmente utilizadas (y por lo tanto débiles) contraseñas de las cecarias utilizando el`requests`biblioteca.
2. `Iterates and Submits Passwords`: Luego itera a través de cada contraseña en la lista de palabras descargada. Para cada contraseña, envía una solicitud de publicación a la aplicación Flask`/dictionary`punto final, incluida la contraseña en los datos del formulario de la solicitud.
3. `Analyzes Responses`: El script verifica el código de estado de respuesta después de cada solicitud. Si son 200 (OK), examina aún más el contenido de respuesta. Si la respuesta contiene la tecla "Bandera", significa un inicio de sesión exitoso. El script luego imprime la contraseña descubierta y la bandera capturada.
4. `Continues or Terminates`: Si la respuesta no indica el éxito, el script procede a la siguiente contraseña en la lista de palabras. Este proceso continúa hasta que se encuentra la contraseña correcta o se agota la lista de palabras completa.

Ataques de diccionario

```
xnoxos@htb[/htb]$ python3 dictionary-solver.py...
Attempted password: turtle
Attempted password: tiffany
Attempted password: golf
Attempted password: bear
Attempted password: tiger
Correct password found: ...
Flag: HTB{...}
```