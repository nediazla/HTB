# Source Code

La mayoría de los sitios web hoy en día utilizan JavaScript para realizar sus funciones. Mientras `HTML` se utiliza para determinar los principales campos y parámetros del sitio web, y `CSS` se utiliza para determinar su diseño, `JavaScript` se utiliza para realizar cualquier función necesaria para ejecutar el sitio web. Esto sucede en segundo plano y solo vemos la bonita interfaz del sitio web e interactuamos con él.

Aunque todo este código fuente está disponible en el lado del cliente, nuestros navegadores lo procesan, por lo que a menudo no prestamos atención al código fuente HTML. Sin embargo, si quisiéramos comprender las funcionalidades del lado del cliente de una determinada página, normalmente comenzamos echando un vistazo al código fuente de la página. Esta sección mostrará cómo podemos descubrir el código fuente que contiene todo esto y comprender su uso general.

---

# **HTML**

Comenzaremos iniciando el ejercicio a continuación, abriremos Firefox en nuestro PwnBox y visitaremos la URL que se muestra en la pregunta:

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_mainsite.jpg)

Como podemos ver, el sitio web dice `Secret Serial Generator`, sin tener ningún campo de entrada ni mostrar ninguna funcionalidad clara. Entonces, nuestro siguiente paso es alcanzar su código fuente. Podemos hacerlo presionando `[CTRL + U]`, que debería abrir la vista fuente del sitio web:

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_mainsite_source_1.jpg)

Como podemos ver, podemos ver el `HTML` Código fuente del sitio web.

---

# **CSS**

`CSS`El código está definido. `internally` dentro del mismo `HTML` archivo entre `<style>` elementos, o definidos `externally` en un separado`.css`archivo y referenciado dentro del `HTML` código.

En este caso, vemos que el `CSS` está definido internamente, como se ve en el siguiente fragmento de código:

Código: HTML

```html
    <style>
        *,
        html {
            margin: 0;
            padding: 0;
            border: 0;
        }
        ...SNIP...
        h1 {
            font-size: 144px;
        }
        p {
            font-size: 64px;
        }
    </style>
```

si una pagina `CSS` El estilo está definido externamente, el exterior `.css` se hace referencia al archivo con el `<link>` etiqueta dentro del encabezado HTML, de la siguiente manera:

Código: HTML

```html
<head><link rel="stylesheet" href="style.css"></head>
```

---

# **javascript**

El mismo concepto se aplica a `JavaScript`. Se puede escribir internamente entre `<script>` elementos o escritos en un separado `.js` archivo y referenciado dentro del `HTML` código.

Podemos ver en nuestro `HTML` fuente que el `.js` Se hace referencia al archivo externamente:

Código: HTML

```html
<script src="secret.js"></script>
```

Podemos consultar el script haciendo clic en `secret.js`, que debería llevarnos directamente al script. Cuando lo visitamos, vemos que el código es muy complicado y no se puede comprender:

Código: javascript

```jsx
eval(function (p, a, c, k, e, d) { e = function (c) { '...SNIP... |true|function'.split('|'), 0, {}))

```

La razón detrás de esto es `code obfuscation`. ¿Qué es? ¿Cómo se hace? ¿Dónde se usa?