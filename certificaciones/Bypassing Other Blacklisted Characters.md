# Bypassing Other Blacklisted Characters

Además de los operadores de inyección y los personajes espaciales, un personaje muy comúnmente en la lista negra es el corte (`/`) o barra de chaqueta (`\`) Carácter, ya que es necesario especificar directorios en Linux o Windows. Podemos utilizar varias técnicas para producir cualquier personaje que queramos mientras evitamos el uso de personajes en la lista negra.

---

# **Linux**

Hay muchas técnicas que podemos utilizar para tener cortes en nuestra carga útil. Una de esas técnicas que podemos usar para reemplazar las bases (`or any other character`) es a través de`Linux Environment Variables`Como lo hicimos con`${IFS}`. Mientras`${IFS}`se reemplaza directamente con un espacio, no existe tal entorno variable para barras o semicolones. Sin embargo, estos caracteres pueden usarse en una variable de entorno, y podemos especificar`start`y`length`de nuestra cadena para que coincida exactamente con este personaje.

Por ejemplo, si miramos el`$PATH`Variable de entorno en Linux, puede parecerse a lo siguiente:

Pasando a otros personajes con lista negra

```
xnoxos@htb[/htb]$ echo ${PATH}/usr/local/bin:/usr/bin:/bin:/usr/games

```

Entonces, si comenzamos en el`0`personaje, y solo tome una cadena de longitud`1`, terminaremos con solo el`/`personaje, que podemos usar en nuestra carga útil:

Pasando a otros personajes con lista negra

```
xnoxos@htb[/htb]$ echo ${PATH:0:1}/

```

**Nota:**Cuando usamos el comando anterior en nuestra carga útil, no agregaremos`echo`, ya que solo lo estamos usando en este caso para mostrar el carácter salido.

Podemos hacer lo mismo con el`$HOME`o`$PWD`Variables de entorno también. También podemos usar el mismo concepto para obtener un carácter de semi-colon, que se utilizará como operador de inyección. Por ejemplo, el siguiente comando nos da un semi-colon:

Pasando a otros personajes con lista negra

```
xnoxos@htb[/htb]$ echo ${LS_COLORS:10:1};

```

Ejercicio: trate de comprender cómo el comando anterior resultó en un semi-colon y luego úselo en la carga útil para usarlo como operador de inyección. Sugerencia: el`printenv`El comando imprime todas las variables de entorno en Linux, por lo que puede ver cuáles pueden contener caracteres útiles, y luego tratar de reducir la cadena solo a ese personaje.

Entonces, intentemos usar variables de entorno para agregar un semi-colon y un espacio a nuestra carga útil (

```
127.0.0.1${LS_COLORS:10:1}${IFS}
```

) como nuestra carga útil, y vea si podemos omitir el filtro:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_5.jpg)

Como podemos ver, esta vez pasamos con éxito el filtro de caracteres esta vez.

---

# **Windows**

El mismo concepto también funciona en Windows. Por ejemplo, para producir un corte en`Windows Command Line (CMD)`, podemos`echo`una variable de Windows (`%HOMEPATH%` -> `\Users\htb-student`), y luego especifique una posición inicial (`~6` -> `\htb-student`), y finalmente especificando una posición final negativa, que en este caso es la longitud del nombre de usuario`htb-student` (`-11` -> `\`) :

Pasando a otros personajes con lista negra

```
C:\htb> echo %HOMEPATH:~6,-11%

\

```

Podemos lograr lo mismo usando las mismas variables en`Windows PowerShell`. Con PowerShell, una palabra se considera una matriz, por lo que tenemos que especificar el índice del personaje que necesitamos. Como solo necesitamos un personaje, no tenemos que especificar las posiciones de inicio y finalización:

Pasando a otros personajes con lista negra

```powershell
PS C:\htb> $env:HOMEPATH[0]\

PS C:\htb> $env:PROGRAMFILES[10]PS C:\htb>

```

También podemos usar el`Get-ChildItem Env:`PowerShell Command para imprimir todas las variables de entorno y luego elegir una de ellas para producir un personaje que necesitamos.`Try to be creative and find different commands to produce similar characters.`

---

# **Cambio de personajes**

Hay otras técnicas para producir los caracteres requeridos sin usarlos, como`shifting characters`. Por ejemplo, el siguiente comando de Linux cambia el personaje que pasamos`1`. Entonces, todo lo que tenemos que hacer es encontrar el personaje en la tabla ASCII que es justo antes de nuestro personaje necesario (podemos obtenerlo con`man ascii`), luego agrégalo en lugar de`[`En el siguiente ejemplo. De esta manera, el último personaje impreso sería el que necesitamos:

Pasando a otros personajes con lista negra

```
xnoxos@htb[/htb]$ man ascii     # \ is on 92, before it is [ on 91xnoxos@htb[/htb]$ echo $(tr '!-}' '"-~'<<<[)

\

```

Podemos usar comandos PowerShell para lograr el mismo resultado en Windows, aunque pueden ser bastante más largos que los de Linux.

Ejercicio: trate de usar la técnica de cambio de caracteres para producir un semi-colon`;`personaje. Primero encuentre el carácter antes que en la tabla ASCII y luego úselo en el comando anterior.