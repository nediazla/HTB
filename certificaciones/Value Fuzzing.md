# Value Fuzzing

Después de borrar un parámetro de trabajo, ahora tenemos que confundir el valor correcto que devolvería el`flag`contenido que necesitamos. Esta sección discutirá los valores de los parámetros que deben ser bastante similares a la confusión para los parámetros, una vez que desarrollemos nuestra lista de palabras.

---

# **Lista de palabras personalizada**

Cuando se trata de valores de parámetros borrosos, es posible que no siempre encontremos una lista de palabras prefabricada que funcione para nosotros, ya que cada parámetro esperaría un cierto tipo de valor.

Para algunos parámetros, como los nombres de usuario, podemos encontrar una lista de palabras prefabricada para posibles nombres de usuario, o podemos crear el nuestro propio en función de los usuarios que pueden estar utilizando el sitio web. Para tales casos, podemos buscar varias listas de palabras bajo el`seclists`directorio e intente encontrar uno que pueda contener valores que coincidan con el parámetro al que estamos dirigidos. En otros casos, como los parámetros personalizados, es posible que tengamos que desarrollar nuestra propia lista de palabras. En este caso, podemos adivinar que el`id`El parámetro puede aceptar una entrada de número de algún tipo. Estas ID pueden estar en un formato personalizado, o pueden ser secuenciales, como de 1-1000 o 1-1000000, y así sucesivamente. Comenzaremos con una lista de palabras que contiene todos los números de 1-1000.

Hay muchas maneras de crear esta lista de palabras, desde escribir manualmente las ID en un archivo, o escribirla usando Bash o Python. La forma más simple es usar el siguiente comando en Bash que escribe todos los números de 1-1000 a un archivo:

Valor Fuzzing

```
xnoxos@htb[/htb]$ for i in $(seq 1 1000); do echo $i >> ids.txt; done
```

Una vez que ejecutamos nuestro comando, debemos tener nuestra lista de palabras lista:

Valor Fuzzing

```
xnoxos@htb[/htb]$ cat ids.txt1
2
3
4
5
6
<...SNIP...>

```

Ahora podemos pasar a Fuzzing para valores.

---

# **Valor Fuzzing**

Nuestro comando debe ser bastante similar al`POST`comando que solíamos luchar por los parámetros, pero nuestro`FUZZ`Se debe poner la palabra clave donde estaría el valor del parámetro, y usaremos el`ids.txt`Lista de palabras que acabamos de crear, como sigue:

Valor Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.0.2
________________________________________________

 :: Method           : POST
 :: URL              : http://admin.academy.htb:30794/admin/admin.php
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : id=FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

<...SNIP...>                      [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]

```

Vemos que recibimos un golpe de inmediato. Finalmente podemos enviar otro`POST`Solicitud utilizando`curl`, Como lo hicimos en la sección anterior, use el`id`Valor que acabamos de encontrar y recogemos la bandera.