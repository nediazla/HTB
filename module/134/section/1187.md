# Bypassing Encoded References

En la sección anterior, vimos un ejemplo de un IDOR que usa UID de empleados en texto claro, lo que facilita la enumerar. En algunos casos, las aplicaciones web dificultan o codifican sus referencias de objetos, lo que dificulta la enumeración, pero aún puede ser posible.

Volvamos al`Employee Manager`aplicación web para probar el`Contracts`Funcionalidad:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_contracts.jpg)

Si hacemos clic en el`Employment_contract.pdf`Archivo, comienza a descargar el archivo. La solicitud interceptada en Burp se ve de la siguiente manera:

![](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_download_contract.jpg)

Vemos que está enviando un`POST`solicitar`download.php`Con los siguientes datos:

Código:php

```php
contract=cdd96d3cc73d1dbdaffa03cc6cd7339b

```

Usando un`download.php`El script para descargar archivos es una práctica común para evitar vincular directamente a los archivos, ya que puede ser explotable con múltiples ataques web. En este caso, la aplicación web no está enviando la referencia directa en ClearText, sino`md5`formato. Los hashes son funciones unidireccionales, por lo que no podemos decodificarlos para ver sus valores originales.

Podemos intentar tener varios valores, como`uid`, `username`, `filename`, y muchos otros, y ver si alguno de sus`md5`Los hashes coinciden con el valor anterior. Si encontramos una coincidencia, podemos replicarla para otros usuarios y recopilar sus archivos. Por ejemplo, intentemos comparar el`md5`hash de nuestro`uid`, y vea si coincide con el hash anterior:

Omitiendo referencias codificadas

```
xnoxos@htb[/htb]$ echo -n 1 | md5sumc4ca4238a0b923820dcc509a6f75849b -

```

Desafortunadamente, los hashes no coinciden. Podemos intentar esto con varios otros campos, pero ninguno de ellos coincide con nuestro hash. En casos avanzados, también podemos utilizar`Burp Comparer`y confunde varios valores y luego compare cada uno con nuestro hash para ver si encontramos alguna coincidencia. En este caso, el`md5`El hash podría ser por un valor único o una combinación de valores, lo que sería muy difícil de predecir, lo que hace que esta referencia directa sea un`Secure Direct Object Reference`. Sin embargo, hay un defecto fatal en esta aplicación web.

---

# **Divulgación de funciones**

Como la mayoría de las aplicaciones web modernas se desarrollan utilizando marcos JavaScript, como`Angular`, `React`, o`Vue.js`, muchos desarrolladores web pueden cometer el error de realizar funciones sensibles en el front-end, lo que los expondría a los atacantes. Por ejemplo, si el hash anterior se estaba calculando en el front-end, podemos estudiar la función y luego replicar lo que está haciendo para calcular el mismo hash. Afortunadamente para nosotros, este es precisamente el caso en esta aplicación web.

Si echamos un vistazo al enlace en el código fuente, vemos que está llamando a una función de JavaScript con`javascript:downloadContract('1')`. Mirando el`downloadContract()`función En el código fuente, vemos lo siguiente:

Código:javascript

```jsx
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}

```

Esta función parece estar enviando un`POST`Solicitar con el`contract`Parámetro, que es lo que vimos arriba. El valor que está enviando es un`md5`hash usando el`CryptoJS`Biblioteca, que también coincide con la solicitud que vimos anteriormente. Entonces, lo único que queda por ver es qué valor se está dando.

En este caso, el valor que se ha hecho es`btoa(uid)`, que es el`base64`cadena codificada del`uid`variable, que es un argumento de entrada para la función. Volviendo al enlace anterior donde se llamaba la función, lo vemos llamando`downloadContract('1')`. Entonces, el valor final que se usa en el`POST`la solicitud es el`base64`cadena codificada de`1`, que era entonces`md5`Hashed.

Podemos probar esto por`base64`codificando nuestro`uid=1`, y luego lo hashes con`md5`, como sigue:

Omitiendo referencias codificadas

```
xnoxos@htb[/htb]$ echo -n 1 | base64 -w 0 | md5sumcdd96d3cc73d1dbdaffa03cc6cd7339b -

```

**Consejo:**Estamos usando el`-n`marcar con`echo`, y el`-w 0`marcar con`base64`, para evitar agregar nuevas líneas, para poder calcular el`md5`hash del mismo valor, sin hash nuevas líneas, ya que eso cambiaría la final`md5`picadillo.

Como podemos ver, este hash coincide con el hash en nuestra solicitud, lo que significa que hemos revertido con éxito la técnica de hash utilizada en las referencias de objetos, convirtiéndolos en Idor. Con eso, podemos comenzar a enumerar los contratos de otros empleados utilizando el mismo método de hashing que utilizamos anteriormente.`Before continuing, try to write a script similar to what we used in the previous section to enumerate all contracts`.

---

# **Enumeración masiva**

Una vez más, escribamos un script bash simple para recuperar todos los contratos de empleados. La mayoría de las veces, este es el método más fácil y eficiente para enumerar datos y archivos a través de vulnerabilidades IDOR. En casos más avanzados, podemos utilizar herramientas como`Burp Intruder`o`ZAP Fuzzer`, pero un simple script bash debería ser el mejor curso para nuestro ejercicio.

Podemos comenzar calculando el hash para cada uno de los primeros diez empleados que usan el mismo comando anterior mientras usan`tr -d`Para eliminar el arrastre`-` personajes, como sigue:

Omitiendo referencias codificadas

```
xnoxos@htb[/htb]$ for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; donecdd96d3cc73d1dbdaffa03cc6cd7339b
0b7e7dee87b1c3b98e72131173dfbbbf
0b24df25fe628797b3a50ae0724d2730
f7947d50da7a043693a592b4db43b0a1
8b9af1f7f76daf0f02bd9c48c4a2e3d0
006d1236aee3f92b8322299796ba1989
b523ff8d1ced96cef9c86492e790c2fb
d477819d240e7d3dd9499ed8d23e7158
3e57e65a34ffcb2e93cb545d024f5bde
5d4aace023dc088767b4e08c79415dcd

```

A continuación, podemos hacer un`POST`solicitar`download.php`con cada uno de los hashes anteriores como el`contract`valor, que debería darnos nuestro guión final:

Código:intento

```bash
#!/bin/bashfor i in {1..10}; do
    for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do
        curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
    done
done

```

Con eso, podemos ejecutar el guión, y debería descargar todos los contratos para los empleados 1-10:

Omitiendo referencias codificadas

```
xnoxos@htb[/htb]$ bash ./exploit.shxnoxos@htb[/htb]$ ls -1contract_006d1236aee3f92b8322299796ba1989.pdf
contract_0b24df25fe628797b3a50ae0724d2730.pdf
contract_0b7e7dee87b1c3b98e72131173dfbbbf.pdf
contract_3e57e65a34ffcb2e93cb545d024f5bde.pdf
contract_5d4aace023dc088767b4e08c79415dcd.pdf
contract_8b9af1f7f76daf0f02bd9c48c4a2e3d0.pdf
contract_b523ff8d1ced96cef9c86492e790c2fb.pdf
contract_cdd96d3cc73d1dbdaffa03cc6cd7339b.pdf
contract_d477819d240e7d3dd9499ed8d23e7158.pdf
contract_f7947d50da7a043693a592b4db43b0a1.pdf

```

Como podemos ver, debido a que podríamos revertir la técnica de Hashing utilizada en las referencias de objetos, ahora podemos explotar con éxito la vulnerabilidad IDOR para recuperar los contratos de todos los demás usuarios.