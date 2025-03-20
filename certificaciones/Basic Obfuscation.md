# Basic Obfuscation

La ofuscación del código generalmente no se realiza manualmente, ya que existen muchas herramientas para varios lenguajes que realizan la ofuscación automática del código. Se pueden encontrar muchas herramientas en línea para hacerlo, aunque muchos actores maliciosos y desarrolladores profesionales desarrollan sus propias herramientas de ofuscación para hacerlo más difícil.

---

# **Ejecutando código JavaScript**

Tomemos la siguiente línea de código como ejemplo e intentemos ofuscarla:

Código: javascript

```jsx
console.log('HTB JavaScript Deobfuscation Module');

```

Primero, probemos la ejecución de este código en texto sin cifrar para verlo funcionar en acción. podemos ir a [JSConsola](https://jsconsole.com/), pegue el código y presione enter, y vea su resultado:

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_jsconsole_1_1.jpg)

Vemos que esta línea de código se imprime. `HTB JavaScript Deobfuscation Module`, que se realiza utilizando el `console.log()` función.

---

# **Minimizar el código JavaScript**

Una forma común de reducir la legibilidad de un fragmento de código JavaScript y al mismo tiempo mantenerlo completamente funcional es la minificación de JavaScript.`Code minification`significa tener el código completo en una sola línea (a menudo muy larga). `Code minification` es más útil para códigos más largos, ya que si nuestro código solo constara de una sola línea, no se vería muy diferente cuando se minimice.

Muchas herramientas pueden ayudarnos a minimizar el código JavaScript, como [minificador de javascript](https://javascript-minifier.com/). Simplemente copiamos nuestro código y hacemos clic `Minify`, y obtenemos la salida minimizada a la derecha:

![](https://academy.hackthebox.com/storage/modules/41/js_minify_1.jpg)

Una vez más, podemos copiar el código minimizado a [JSConsola](https://jsconsole.com/), lo ejecutamos y vemos que se ejecuta como se esperaba. Normalmente, el código JavaScript minimizado se guarda con la extensión`.min.js`.

Nota: La minificación de código no es exclusiva de JavaScript y se puede aplicar a muchos otros lenguajes, como se puede ver en[minificador de javascript](https://javascript-minifier.com/).

---

# **Empaquetando código JavaScript**

Ahora, ofusquemos nuestra línea de código para hacerla más oscura y difícil de leer. Primero, intentaremos [EmbellecerHerramientas](http://beautifytools.com/javascript-obfuscator.php) para ofuscar nuestro código:

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_obfuscator.jpg)

Código: javascript

```jsx
eval(function(p,a,c,k,e,d){e=function(c){return c};if(!''.replace(/^/,String)){while(c--){d[c]=k[c]||c}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('5.4(\'3 2 1 0\');',6,6,'Module|Deobfuscation|JavaScript|HTB|log|console'.split('|'),0,{}))

```

Vemos que nuestro código se volvió mucho más confuso y difícil de leer. Podemos copiar este código en [https://jsconsole.com](https://jsconsole.com/), para verificar que todavía hace su función principal:

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_jsconsole_3_1.jpg)

Vemos que obtenemos el mismo resultado.

Nota: El tipo de ofuscación anterior se conoce como "empaquetado", que generalmente se reconoce por los seis argumentos de función utilizados en la función inicial "función (p,a,c,k,e,d)".

A `packer` La herramienta de ofuscación generalmente intenta convertir todas las palabras y símbolos del código en una lista o diccionario y luego hacer referencia a ellos usando el`(p,a,c,k,e,d)`función para reconstruir el código original durante la ejecución. El `(p,a,c,k,e,d)` puede ser diferente de un empacador a otro. Sin embargo, suele contener un orden determinado en el que se empaquetaron las palabras y símbolos del código original para saber ordenarlos durante la ejecución.

Si bien un empaquetador hace un gran trabajo al reducir la legibilidad del código, aún podemos ver sus cadenas principales escritas en texto sin cifrar, lo que puede revelar algunas de sus funciones. Es por eso que es posible que queramos buscar mejores formas de ofuscar nuestro código.

[Anterior](https://academy.hackthebox.com/module/41/section/440) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/41/section/448)

Hoja de trucos