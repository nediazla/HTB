# Bypassing Blacklisted Commands

Hemos discutido varios métodos para pasar por alto los filtros de un solo personaje. Sin embargo, existen diferentes métodos cuando se trata de pasar por alto los comandos con lista negra. Una lista negra de comando generalmente consiste en un conjunto de palabras, y si podemos ofuscar nuestros comandos y hacer que se vean diferentes, podemos evitar los filtros.

Existen varios métodos de ofuscación de comandos que varían en la complejidad, ya que tocaremos más adelante con las herramientas de ofuscación de comandos. Cubriremos algunas técnicas básicas que pueden permitirnos cambiar el aspecto de nuestro comando para evitar los filtros manualmente.

---

# **Comandos de la lista negra**

Hasta ahora, hemos pasado por alto con éxito el filtro de caracteres para los caracteres del espacio y el semi-colon en nuestra carga útil. Entonces, volvamos a nuestra primera carga útil y vuelvamos a agregar la

```
whoami
```

Comandar para ver si se ejecuta:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_1.jpg)

Vemos que a pesar de que usamos personajes que no están bloqueados por la aplicación web, la solicitud se bloquea nuevamente una vez que agregamos nuestro comando. Esto probablemente se deba a otro tipo de filtro, que es un filtro de lista negra de comando.

Un filtro de lista negra de comando básico en`PHP`se vería como lo siguiente:

Código:php

```php
$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}

```

Como podemos ver, está comprobando cada palabra de la entrada del usuario para ver si coincide con alguna de las palabras en la lista negra. Sin embargo, este código está buscando una coincidencia exacta del comando proporcionado, por lo que si enviamos un comando ligeramente diferente, es posible que no se bloquee. Afortunadamente, podemos utilizar varias técnicas de ofuscación que ejecutarán nuestro comando sin usar la palabra de comando exacta.

---

# **Linux y Windows**

Una técnica de ofuscación muy común y fácil es insertar ciertos caracteres dentro de nuestro comando que generalmente son ignorados por shells de comando como`Bash`o`PowerShell`y ejecutará el mismo comando que si no estuvieran allí. Algunos de estos personajes son una sola cota`'`y una doble cita`"`, además de algunos otros.

Los más fáciles de usar son las citas, y funcionan en los servidores Linux y Windows. Por ejemplo, si queremos ofuscar el`whoami`Comando, podemos insertar citas individuales entre sus caracteres, como sigue:

Omitiendo comandos con lista negra

```
21y4d@htb[/htb]$ w'h'o'am'i21y4d

```

Lo mismo funciona con cotas dobles también:

Omitiendo comandos con lista negra

```
21y4d@htb[/htb]$ w"h"o"am"i21y4d

```

Las cosas importantes para recordar son que`we cannot mix types of quotes`y`the number of quotes must be even`. Podemos probar uno de los anteriores en nuestra carga útil (`127.0.0.1%0aw'h'o'am'i`) y ver si funciona:

### **Solicitud de publicación de Burp**

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_2.jpg)

Como podemos ver, este método realmente funciona.

---

# **Solo Linux**

Podemos insertar algunos otros caracteres solo de Linux en el medio de los comandos, y el`bash`Shell los ignoraría y ejecutaría el comando. Estos personajes incluyen la barra insegura`\`y el carácter de parámetro posicional`$@`. Esto funciona exactamente como lo hizo con las citas, pero en este caso,`the number of characters do not have to be even`, y podemos insertar solo uno de ellos si queremos:

Código:intento

```bash
who$@ami
w\ho\am\i

```

Ejercicio: pruebe los dos ejemplos anteriores en su carga útil y vea si funcionan para evitar el filtro de comando. Si no lo hacen, esto puede indicar que puede haber usado un carácter filtrado. ¿Sería capaz de evitar eso también, utilizando las técnicas que aprendimos en la sección anterior?

---

# **Solo Windows**

También hay algunos caracteres de Windows solo que podemos insertar en el medio de los comandos que no afectan el resultado, como un careto (`^`) Carácter, como podemos ver en el siguiente ejemplo:

Omitiendo comandos con lista negra

```
C:\htb> who^ami

21y4d

```

En la siguiente sección, discutiremos algunas técnicas más avanzadas para la ofuscación de comandos y la omisión del filtro.