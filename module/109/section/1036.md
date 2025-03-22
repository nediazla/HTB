# Bypassing Space Filters

Existen numerosas formas de detectar intentos de inyección, y hay múltiples métodos para evitar estas detecciones. Demostraremos el concepto de detección y cómo funciona el omisión utilizando Linux como ejemplo. Aprenderemos cómo utilizar estos desvíos y eventualmente podremos prevenirlos. Una vez que tengamos una buena comprensión de cómo funcionan, podemos pasar por varias fuentes en Internet para descubrir otros tipos de derivaciones y aprender a mitigarlos.

---

# **Bypass Operadores con lista negra**

Veremos que la mayoría de los operadores de inyección están en la lista negra. Sin embargo, el personaje de nueva línea generalmente no está en la lista negra, ya que puede ser necesario en la carga útil en sí. Sabemos que el personaje de nueva línea funciona para agregar nuestros comandos tanto en Linux como en Windows, así que intentemos usarlo como nuestro operador de inyección:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_operator.jpg)

Como podemos ver, a pesar de que nuestra carga útil incluyó un carácter de nueva línea, nuestra solicitud no fue negada y obtuvimos la salida del comando ping,`which means that this character is not blacklisted, and we can use it as our injection operator`. Comencemos discutiendo cómo omitir un personaje comúnmente en la lista negra: un personaje espacial.

---

# **Bypass espacios con lista negra**

Ahora que tenemos un operador de inyección de trabajo, modifiquemos nuestra carga útil original y la enviemos nuevamente como (

```
127.0.0.1%0a whoami
```

):

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_1.jpg)

Como podemos ver, todavía obtenemos un

```
invalid input
```

Mensaje de error, lo que significa que todavía tenemos otros filtros para omitir. Entonces, como lo hicimos antes, agregemos solo el siguiente carácter (que es un espacio) y vea si causó la solicitud denegada:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_2.jpg)

Como podemos ver, el personaje espacial también está en la lista negra. Un espacio es un carácter comúnmente en la lista negra, especialmente si la entrada no debe contener ningún espacio, como una IP, por ejemplo. ¡Aún así, hay muchas formas de agregar un personaje espacial sin usar el personaje espacial!

### **Usando pestañas**

El uso de pestañas (%09) en lugar de espacios es una técnica que puede funcionar, ya que tanto Linux como Windows aceptan comandos con pestañas entre argumentos, y se ejecutan igual. Entonces, intentemos usar una pestaña en lugar del carácter espacial (

```
127.0.0.1%0a%09
```

) y ver si se acepta nuestra solicitud:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_3.jpg)

Como podemos ver, omitimos con éxito el filtro de caracteres espacial utilizando una pestaña. Veamos otro método para reemplazar los caracteres espaciales.

### **Usando $ ifs**

El uso de la variable de entorno Linux ($ IFS) también puede funcionar ya que su valor predeterminado es un espacio y una pestaña, que funcionaría entre los argumentos de comando. Entonces, si usamos`${IFS}`Cuando los espacios deben estar, la variable debe reemplazarse automáticamente con un espacio, y nuestro comando debería funcionar.

Usemos

```
${IFS}
```

y vea si funciona (

```
127.0.0.1%0a${IFS}
```

):

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_4.jpg)

Vemos que nuestra solicitud no fue negada esta vez, y pasamos por alto el filtro de espacio nuevamente.

### **Usando la expansión de la abrazadera**

Hay muchos otros métodos que podemos utilizar para evitar filtros espaciales. Por ejemplo, podemos usar el`Bash Brace Expansion`Característica, que agrega automáticamente espacios entre argumentos envueltos entre aparatos ortopédicos, de la siguiente manera:

Pasando por alto los filtros espaciales

```
xnoxos@htb[/htb]$ {ls,-la}total 0
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 07:37 .
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 13:01 ..

```

Como podemos ver, el comando se ejecutó con éxito sin tener espacios en él. Podemos utilizar el mismo método en las derivaciones del filtro de inyección de comandos, utilizando la expansión de la abrazadera en nuestros argumentos de comando, como (`127.0.0.1%0a{ls,-la}`). Para descubrir más derivaciones de filtro de espacio, consulte el[Entrega de carga útil](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space)Página sobre la escritura de comandos sin espacios.

**Ejercicio:**Intente buscar otros métodos para evitar filtros espaciales y úselos con el`Host Checker`Aplicación web para aprender cómo funcionan.