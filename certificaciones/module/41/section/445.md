# Decoding

Después de hacer el ejercicio de la sección anterior, obtuvimos un extraño bloque de texto que parece estar codificado:

Descodificación

```
xnoxos@htb[/htb]$ curl http://SERVER_IP:PORT/serial.php -X POST -d "param1=sample"ZG8gdGhlIGV4ZXJjaXNlLCBkb24ndCBjb3B5IGFuZCBwYXN0ZSA7KQo=

```

Este es otro aspecto importante de la ofuscación al que nos referimos en `More Obfuscation` en el `Advanced Obfuscation` sección. Muchas técnicas pueden ofuscar aún más el código y hacerlo menos legible para los humanos y menos detectable para los sistemas. Por esa razón, muy a menudo encontrará código ofuscado que contiene bloques de texto codificados que se decodifican durante la ejecución. Cubriremos 3 de los métodos de codificación de texto más utilizados:

- `base64`
- `hex`
- `rot13`

---

# **Base64**

`base64`La codificación se utiliza generalmente para reducir el uso de caracteres especiales, ya que cualquier carácter codificado en `base64` estaría representado en caracteres alfanuméricos, además de `+`y `/` solo. Independientemente de la entrada, incluso si está en formato binario, la cadena codificada en base64 resultante solo los usará.

### **Base de localización64**

`base64`Las cadenas codificadas se detectan fácilmente ya que solo contienen caracteres alfanuméricos. Sin embargo, el rasgo más distintivo de `base64` es su relleno usando `=` personajes. la longitud de `base64` Las cadenas codificadas deben ser múltiplos de 4. Si la salida resultante tiene solo 3 caracteres, por ejemplo, un extra `=`se agrega como relleno, y así sucesivamente.

### **Codificación Base64**

Para codificar cualquier texto en `base64` en Linux, podemos repetirlo y canalizarlo con '`|`' a`base64`:

Descodificación

```
xnoxos@htb[/htb]$ echo https://www.hackthebox.eu/ | base64aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K

```

### **Decodificación Base64**

Si queremos decodificar alguna `base64` cadena codificada, podemos usar `base64 -d`, de la siguiente manera:

Descodificación

```
xnoxos@htb[/htb]$ echo aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K | base64 -dhttps://www.hackthebox.eu/

```

---

# **Maleficio**

Otro método de codificación común es `hex` codificación, que codifica cada carácter en su `hex` orden en el `ASCII` mesa. Por ejemplo,`a`es `61` en hexadecimal, `b` es `62`, `c` es `63`, etcétera. Puedes encontrar el completo `ASCII` tabla en Linux usando el `man ascii` dominio.

### **Hexágono de detección**

Cualquier cadena codificada en `hex` estaría compuesto únicamente de caracteres hexadecimales, que son solo 16 caracteres: 0-9 y a-f. Eso hace que detectar`hex`cadenas codificadas tan fáciles como detectar `base64` cadenas codificadas.

### **Codificación hexadecimal**

Para codificar cualquier cadena en `hex` En Linux podemos usar el `xxd -p` dominio:

Descodificación

```
xnoxos@htb[/htb]$ echo https://www.hackthebox.eu/ | xxd -p68747470733a2f2f7777772e6861636b746865626f782e65752f0a

```

### **Decodificación hexadecimal**

para decodificar un `hex` cadena codificada, podemos usar el `xxd -p -r` dominio:

Descodificación

```
xnoxos@htb[/htb]$ echo 68747470733a2f2f7777772e6861636b746865626f782e65752f0a | xxd -p -rhttps://www.hackthebox.eu/

```

---

# **César/Rot13**

Otra técnica de codificación común (y muy antigua) es el cifrado César, que desplaza cada letra en un número fijo. Por ejemplo, cambiar 1 carácter hace`a`convertirse `b`, y `b` se convierte `c`, etcétera. Muchas variaciones del cifrado César utilizan un número diferente de desplazamientos, el más común de los cuales es`rot13`, que desplaza cada carácter 13 veces hacia adelante.

### **Detectando a César/Rot13**

Aunque este método de codificación hace que cualquier texto parezca aleatorio, aún es posible detectarlo porque cada carácter está asignado a un carácter específico. Por ejemplo, en`rot13`, `http://www`se convierte `uggc://jjj`, que todavía guarda algunas similitudes y puede ser reconocido como tal.

### **Codificación Rot13**

No hay un comando específico en Linux para hacer `rot13` codificación. Sin embargo, es bastante fácil crear nuestro propio comando para realizar el cambio de personaje:

Descodificación

```
xnoxos@htb[/htb]$ echo https://www.hackthebox.eu/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'uggcf://jjj.unpxgurobk.rh/

```

### **Decodificación Rot13**

También podemos usar el mismo comando anterior para decodificar rot13:

Descodificación

```
xnoxos@htb[/htb]$ echo uggcf://jjj.unpxgurobk.rh/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'https://www.hackthebox.eu/

```

Otra opción para codificar/decodificar rot13 sería usar una herramienta en línea, como [podredumbre13](https://rot13.com/).

---

# **Otros tipos de codificación**

Hay cientos de otros métodos de codificación que podemos encontrar en línea. Si bien estos son los más comunes, en ocasiones nos encontraremos con otros métodos de codificación, que pueden requerir cierta experiencia para identificarlos y decodificarlos.

`If you face any similar types of encoding, first try to determine the type of encoding, and then look for online tools to decode it.`

Algunas herramientas pueden ayudarnos a determinar automáticamente el tipo de codificación, como [Identificador de cifrado](https://www.boxentriq.com/code-breaking/cipher-identifier). Pruebe las cadenas codificadas arriba con[Identificador de cifrado](https://www.boxentriq.com/code-breaking/cipher-identifier), para ver si puede identificar correctamente el método de codificación.

Además de la codificación, muchas herramientas de ofuscación utilizan cifrado, que consiste en codificar una cadena usando una clave, lo que puede hacer que el código ofuscado sea muy difícil de aplicar ingeniería inversa y desofuscar, especialmente si la clave de descifrado no está almacenada dentro del propio script.