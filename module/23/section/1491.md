# Basic Bypasses

En la sección anterior, vimos varios tipos de ataques que podemos usar para diferentes tipos de vulnerabilidades de LFI. En muchos casos, podemos enfrentar una aplicación web que aplique varias protecciones contra la inclusión de archivos, por lo que nuestras cargas útiles normales de LFI no funcionarían. Aún así, a menos que la aplicación web esté correctamente asegurada contra la entrada de los usuarios de LFI maliciosos, podemos evitar las protecciones en su lugar y alcanzar la inclusión de archivos.

---

# **Filtros transversales de ruta no recursiva**

Uno de los filtros más básicos contra LFI es un filtro de búsqueda y reemplazo, donde simplemente elimina las subcadenas de (`../`) para evitar los recorridos de ruta. Por ejemplo:

Código:php

```php
$language = str_replace('../', '', $_GET['language']);

```

Se supone que el código anterior evita el recorrido de la ruta y, por lo tanto, hace que LFI sea inútil. Si probamos las cargas de LFI que probamos en la sección anterior, obtenemos lo siguiente:

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist.png)

Vemos que todos`../`Se eliminaron las subcadenas, lo que resultó en una ruta final`./languages/etc/passwd`. Sin embargo, este filtro es muy inseguro, como no es`recursively removing`el`../`Subcadera, ya que ejecuta una sola vez en la cadena de entrada y no aplica el filtro en la cadena de salida. Por ejemplo, si usamos`....//`Como nuestra carga útil, el filtro eliminaría`../`y la cadena de salida sería`../`, lo que significa que aún podemos realizar el recorrido de la ruta. Intentemos aplicar esta lógica para incluir`/etc/passwd`de nuevo:

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist_passwd.png)

Como podemos ver, la inclusión fue exitosa esta vez, y podemos leer`/etc/passwd`exitosamente. El`....//`La subcadena no es el único bypass que podemos usar, como podemos usar`..././`o`....\/`y varias otras cargas útiles de LFI recursivas. Además, en algunos casos, escapar del carácter de corte hacia adelante también puede funcionar para evitar filtros transversales de ruta (por ejemplo,`....\/`), o agregando barras adicionales hacia adelante (por ejemplo,`....////`)

---

# **Codificación**

Algunos filtros web pueden evitar filtros de entrada que incluyen ciertos caracteres relacionados con LFI, como un punto`.`o un corte`/`utilizado para los recorridos de ruta. Sin embargo, algunos de estos filtros pueden pasar por alto mediante la codificación de URL que codifica nuestra entrada, de modo que ya no incluiría estos malos caracteres, pero aún así se decodificaría a nuestra cadena transversal de la ruta una vez que alcance la función vulnerable. Los filtros de PHP centrales en las versiones 5.3.4 y anteriores fueron específicamente vulnerables a este bypass, pero incluso en versiones más nuevas podemos encontrar filtros personalizados que pueden pasar por alto a través de la codificación de URL.

Si la aplicación web de destino no permitió

```
.
```

y

```
/
```

En nuestra entrada, podemos codificar la URL

```
../
```

en

```
%2e%2e%2f
```

, que puede omitir el filtro. Para hacerlo, podemos usar cualquier utilidad de codificador de URL en línea o usar la herramienta Burp Suite Decoder, de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/23/burp_url_encode.jpg)

**Nota:**Para que esto funcione, debemos codificar todos los personajes, incluidos los puntos. Algunos codificadores de URL pueden no codificar puntos, ya que se considera que son parte del esquema de URL.

Intentemos usar esta carga útil LFI codificada contra nuestra aplicación web vulnerable anterior que filtra`../`instrumentos de cuerda:

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist_passwd_filter.png)

Como podemos ver, también pudimos omitir con éxito el filtro y usar la traversal de la ruta para leer`/etc/passwd`. Además, también podemos usar Burp Decoder para codificar la cadena codificada una vez más para tener un`double encoded`Cadena, que también puede omitir otros tipos de filtros.

Puede consultar el[Inyecciones de comando](https://academy.hackthebox.com/module/details/109)Módulo para obtener más información sobre el omitido de varios caracteres en la lista negra, ya que las mismas técnicas también pueden usarse con LFI.

---

# **Rutas aprobadas**

Algunas aplicaciones web también pueden usar expresiones regulares para garantizar que el archivo que se incluye esté en una ruta específica. Por ejemplo, la aplicación web con la que hemos estado tratando solo puede aceptar rutas que están bajo el`./languages`Directorio, como sigue:

Código:php

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}

```

Para encontrar la ruta aprobada, podemos examinar las solicitudes enviadas por los formularios existentes y ver qué ruta usan para la funcionalidad web normal. Además, podemos borrar los directorios web en la misma ruta y probar diferentes hasta que obtengamos una coincidencia. Para evitar esto, podemos usar el recorrido de la ruta y comenzar nuestra carga útil con la ruta aprobada, y luego usar`../`Para volver al directorio raíz y leer el archivo que especificamos, de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist_passwd_filter.png)

Algunas aplicaciones web pueden aplicar este filtro junto con uno de los filtros anteriores, por lo que podemos combinar ambas técnicas comenzando nuestra carga útil con la ruta aprobada, y luego URL codifica nuestra carga útil o use la carga útil recursiva.

**Nota:**Todas las técnicas mencionadas hasta ahora deberían funcionar con cualquier vulnerabilidad de LFI, independientemente del lenguaje o marco de desarrollo de fondo.

---

# **Extensión adjunta**

Como se discutió en la sección anterior, algunas aplicaciones web agregan una extensión a nuestra cadena de entrada (por ejemplo,`.php`), para asegurarse de que el archivo que incluimos esté en la extensión esperada. Con versiones modernas de PHP, es posible que no podamos evitar esto y estaremos restringidos a solo archivos de lectura en esa extensión, lo que aún puede ser útil, como veremos en la siguiente sección (por ejemplo, para leer el código fuente).

Hay un par de otras técnicas que podemos usar, pero son`obsolete with modern versions of PHP and only work with PHP versions before 5.3/5.4`. Sin embargo, puede ser beneficioso mencionarlos, ya que algunas aplicaciones web aún pueden estar ejecutándose en servidores más antiguos, y estas técnicas pueden ser los únicos desvíos posibles.

### **Truncamiento de ruta**

En versiones anteriores de PHP, las cadenas definidas tienen una longitud máxima de 4096 caracteres, probablemente debido a la limitación de los sistemas de 32 bits. Si se pasa una cadena más larga, simplemente será`truncated`, y se ignorará cualquier personaje después de la longitud máxima. Además, PHP también solía eliminar cortes finales y puntos individuales en los nombres de rutas, por lo que si llamamos (`/etc/passwd/.`) Entonces el`/.`también se truncaría y PHP llamaría (`/etc/passwd`). PHP, y los sistemas Linux en general, también ignoran múltiples cortes en la ruta (por ejemplo,`////etc/passwd`es lo mismo que`/etc/passwd`). Del mismo modo, un atajo de directorio actual (`.`) en el medio de la ruta también se ignoraría (por ejemplo`/etc/./passwd`).

Si combinamos ambas limitaciones de PHP juntas, podemos crear cadenas muy largas que evalúen a una ruta correcta. Cada vez que alcanzamos la limitación de personajes 4096, la extensión agregada (`.php`) se truncaría, y tendríamos una ruta sin una extensión agregada. Finalmente, también es importante tener en cuenta que también tendríamos que`start the path with a non-existing directory`para que esta técnica funcione.

Un ejemplo de dicha carga útil sería el siguiente:

Código:url

```
?language=non_existing_directory/../../../etc/passwd/./././.[./ REPEATED ~2048 times]

```

Por supuesto, no tenemos que escribir manualmente`./`2048 veces (total de 4096 caracteres), pero podemos automatizar la creación de esta cadena con el siguiente comando:

Derivaciones básicas

```
xnoxos@htb[/htb]$ echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; donenon_existing_directory/../../../etc/passwd/./././<SNIP>././././

```

También podemos aumentar el recuento de`../`, ya que agregar más aún nos aterrizaría en el directorio raíz, como se explica en la sección anterior. Sin embargo, si usamos este método, debemos calcular la longitud completa de la cadena para garantizar solo`.php`se trunce y no nuestro archivo solicitado al final de la cadena (`/etc/passwd`). Es por eso que sería más fácil usar el primer método.

### **Bytes nulos**

Las versiones de PHP antes de 5.5 eran vulnerables a`null byte injection`, lo que significa que agregar un byte nulo (`%00`) al final de la cadena terminaría la cadena y no consideraría nada después de ella. Esto se debe a cómo se almacenan las cadenas en la memoria de bajo nivel, donde las cadenas en la memoria deben usar un byte nulo para indicar el final de la cadena, como se ve en los lenguajes de ensamblaje, C o C ++.

Para explotar esta vulnerabilidad, podemos finalizar nuestra carga útil con un byte nulo (por ejemplo,`/etc/passwd%00`), de modo que el camino final pasó a`include()`sería (`/etc/passwd%00.php`). De esta manera, aunque`.php`se agrega a nuestra cadena, cualquier cosa después de que el byte nulo se truncaría, por lo que la ruta utilizada sería realmente`/etc/passwd`, que nos lleva a evitar la extensión agregada.