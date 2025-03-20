# Code Obfuscation

Antes de empezar a aprender sobre `deobfuscation`, primero debemos aprender sobre `code obfuscation`. Sin comprender cómo se ofusca el código, es posible que no podamos desofuscarlo correctamente, especialmente si se ofuscó utilizando un ofuscador personalizado.

---

# **¿Qué es la ofuscación?**

La ofuscación es una técnica utilizada para hacer que un guión sea más difícil de leer para los humanos, pero le permite funcionar igual desde un punto de vista técnico, aunque el rendimiento puede ser más lento. Por lo general, esto se logra automáticamente mediante el uso de una herramienta de ofuscación, que toma el código como entrada e intenta reescribir el código de una manera que sea mucho más difícil de leer, dependiendo de su diseño.

Por ejemplo, los ofuscadores de código a menudo convierten el código en un diccionario de todas las palabras y símbolos utilizados dentro del código y luego intentan reconstruir el código original durante la ejecución haciendo referencia a cada palabra y símbolo del diccionario. El siguiente es un ejemplo de un código JavaScript simple que se ofusca:

![](https://academy.hackthebox.com/storage/modules/41/obfuscation_example.jpg)

Los códigos escritos en muchos idiomas se publican y ejecutan sin ser compilados en `interpreted` idiomas, como `Python`, `PHP`, y `JavaScript`. Mientras `Python` y `PHP` normalmente residen en el lado del servidor y, por lo tanto, están ocultos para los usuarios finales,`JavaScript`generalmente se usa dentro de los navegadores en el `client-side`, y el código se envía al usuario y se ejecuta en texto sin cifrar. Esta es la razón por la que la ofuscación se utiliza muy a menudo con`JavaScript`.

---

# **Casos de uso**

Hay muchas razones por las que los desarrolladores pueden considerar ofuscar su código. Una razón común es ocultar el código original y sus funciones para evitar que se reutilice o copie sin el permiso del desarrollador, lo que dificulta la ingeniería inversa de la funcionalidad original del código. Otra razón es proporcionar una capa de seguridad cuando se trata de autenticación o cifrado para evitar ataques a vulnerabilidades que puedan encontrarse dentro del código.

`It must be noted that doing authentication or encryption on the client-side is not recommended, as code is more prone to attacks this way.`

Sin embargo, el uso más común de la ofuscación es para acciones maliciosas. Es común que los atacantes y los actores maliciosos oculten sus scripts maliciosos para evitar que los sistemas de detección y prevención de intrusiones detecten sus scripts. En la siguiente sección, aprenderemos cómo ofuscar un código JavaScript simple e intentaremos ejecutarlo antes y después de la ofuscación para notar cualquier diferencia.