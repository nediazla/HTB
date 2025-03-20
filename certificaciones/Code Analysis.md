# Code Analysis

Ahora que hemos desofuscado el código, podemos empezar a revisarlo:

Código: javascript

```jsx
'use strict';
function generateSerial() {
  ...SNIP...
  var xhr = new XMLHttpRequest;
  var url = "/serial.php";
  xhr.open("POST", url, true);
  xhr.send(null);
};

```

Vemos que el `secret.js` el archivo contiene solo una función, `generateSerial`.

---

# **Solicitudes HTTP**

Veamos cada línea del `generateSerial` función.

### **Variables de código**

La función comienza definiendo una variable. `xhr`, que crea un objeto de `XMLHttpRequest`. Como es posible que no sepamos exactamente qué `XMLHttpRequest`lo hace en JavaScript, busquemos en Google `XMLHttpRequest` para ver para qué sirve.

Después de leer sobre esto, vemos que es una función de JavaScript que maneja solicitudes web.

La segunda variable definida es la `URL` variable, que contiene una URL para `/serial.php`, que debería estar en el mismo dominio, ya que no se especificó ningún dominio.

### **Funciones de código**

A continuación, vemos que `xhr.open` se usa con `"POST"` y `URL`. Podemos buscar en Google esta función una vez más y vemos que abre la solicitud HTTP definida '`GET` o `POST`' hacia `URL`, y luego la siguiente línea `xhr.send` enviaría la solicitud.

Entonces, todos `generateSerial` lo que está haciendo es simplemente enviar un `POST` solicitar a `/serial.php`, sin incluir ningún `POST` datos o recuperar algo a cambio.

Es posible que los desarrolladores hayan implementado esta función cada vez que necesitan generar una serie, como al hacer clic en un determinado `Generate Serial` botón, por ejemplo. Sin embargo, dado que no vimos ningún elemento HTML similar que genere publicaciones seriadas, los desarrolladores aún no deben haber usado esta función y la guardaron para uso futuro.

Con el uso de desofuscación y análisis de código, pudimos descubrir esta función. Ahora podemos intentar replicar su funcionalidad para ver si se maneja en el lado del servidor al enviar un`POST`pedido. Si la función está habilitada y manejada en el lado del servidor, podemos descubrir una funcionalidad inédita, que generalmente tiende a tener errores y vulnerabilidades.

[Anterior](https://academy.hackthebox.com/module/41/section/442) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/41/section/444)

Hoja de trucos