# Deobfuscation

Ahora que entendemos cómo funciona la ofuscación de código, comencemos nuestro aprendizaje sobre la desofuscación. Así como existen herramientas para ofuscar el código automáticamente, existen herramientas para embellecer y desofuscar el código automáticamente.

---

# **Embellecer**

Vemos que el código actual que tenemos está todo escrito en una sola línea. Esto se conoce como `Minified JavaScript` código. Para formatear correctamente el código, necesitamos `Beautify` nuestro código. El método más básico para hacerlo es a través de nuestro `Browser Dev Tools`.

Por ejemplo, si estuviéramos usando Firefox, podemos abrir el depurador del navegador con [

```
CTRL+SHIFT+Z
```

], y luego haga clic en nuestro script

```
secret.js
```

. Esto mostrará el script en su formato original, pero podemos hacer clic en el botón '

```
{ }
```

' botón en la parte inferior, que

```
Pretty Print
```

el script en su formato JavaScript adecuado:

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_pretty_print.jpg)

Además, podemos utilizar muchas herramientas en línea o complementos de edición de código, como [mas bonita](https://prettier.io/playground/) o [embellecedor](https://beautifier.io/). Copiemos el `secret.js` guion:

Código: javascript

```jsx
eval(function (p, a, c, k, e, d) { e = function (c) { return c.toString(36) }; if (!''.replace(/^/, String)) { while (c--) { d[c.toString(a)] = k[c] || c.toString(a) } k = [function (e) { return d[e] }]; e = function () { return '\\w+' }; c = 1 }; while (c--) { if (k[c]) { p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]) } } return p }('g 4(){0 5="6{7!}";0 1=8 a();0 2="/9.c";1.d("e",2,f);1.b(3)}', 17, 17, 'var|xhr|url|null|generateSerial|flag|HTB|flag|new|serial|XMLHttpRequest|send|php|open|POST|true|function'.split('|'), 0, {}))

```

Podemos ver que ambos sitios web hacen un buen trabajo formateando el código:

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_prettier_1.jpg)

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_beautifier_1.jpg)

Sin embargo, el código todavía no es muy fácil de leer. Esto se debe a que el código que estamos tratando no sólo fue minimizado sino también ofuscado. Por lo tanto, simplemente formatear o embellecer el código no será suficiente. Para ello, necesitaremos herramientas para desofuscar el código.

---

# **Desofuscar**

Podemos encontrar muchas buenas herramientas en línea para desofuscar el código JavaScript y convertirlo en algo que podamos entender. Una buena herramienta es [Desempaquetador](https://matthewfl.com/unPacker.html). Intentemos copiar nuestro código ofuscado anteriormente y ejecutarlo en UnPacker haciendo clic en `UnPack` botón.

Consejo: asegúrese de no dejar líneas vacías antes del script, ya que puede afectar el proceso de desofuscación y dar resultados inexactos.

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_unpacker_1.jpg)

Podemos ver que esta herramienta hace un trabajo mucho mejor al desofuscar el código JavaScript y nos brindó un resultado que podemos entender:

Código: javascript

```jsx
function generateSerial() {
  ...SNIP...
  var xhr = new XMLHttpRequest;
  var url = "/serial.php";
  xhr.open("POST", url, true);
  xhr.send(null);
};

```

Como se mencionó anteriormente, el método de ofuscación utilizado anteriormente es `packing`. Otra forma de `unpacking` dicho código es para encontrar el`return`valor al final y uso `console.log` para imprimirlo en lugar de ejecutarlo.

---

# **Ingeniería inversa**

Aunque estas herramientas están haciendo un buen trabajo hasta ahora al limpiar el código y convertirlo en algo que podamos entender, una vez que el código se vuelve más ofuscado y codificado, será mucho más difícil para las herramientas automatizadas limpiarlo. Esto es especialmente cierto si el código se ofuscó mediante una herramienta de ofuscación personalizada.

Tendríamos que aplicar ingeniería inversa manualmente al código para comprender cómo se ofuscó y su funcionalidad en tales casos. Si está interesado en saber más sobre la desofuscación avanzada de JavaScript y la ingeniería inversa, puede consultar el[Codificación segura 101](https://academy.hackthebox.com/module/details/38) módulo, que debería cubrir a fondo este tema.