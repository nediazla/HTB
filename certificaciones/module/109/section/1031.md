# Introducción a las inyecciones de comando

Una vulnerabilidad de inyección de comandos se encuentra entre los tipos más críticos de vulnerabilidades. Nos permite ejecutar comandos del sistema directamente en el servidor de alojamiento de back-end, lo que podría conducir a comprometer toda la red. Si una aplicación web utiliza la entrada controlada por el usuario para ejecutar un comando de sistema en el servidor de fondo para recuperar y devolver una salida específica, podemos inyectar una carga útil maliciosa para subvertir el comando previsto y ejecutar nuestros comandos.

---

# **¿Qué son las inyecciones?**

Las vulnerabilidades de inyección se consideran el riesgo número 3 en[Los 10 principales riesgos de la aplicación web de OWASP](https://owasp.org/www-project-top-ten/), dado su alto impacto y lo comunes que son. La inyección ocurre cuando la entrada controlada por el usuario se malinterpreta como parte de la consulta o código web que se ejecuta, lo que puede conducir a subvertir el resultado previsto de la consulta a un resultado diferente que sea útil para el atacante.

Hay muchos tipos de inyecciones que se encuentran en las aplicaciones web, dependiendo del tipo de consulta web que se ejecuta. Los siguientes son algunos de los tipos más comunes de inyecciones:

| **Inyección** | **Descripción** |
| --- | --- |
| Inyección de comando de OS | Ocurre cuando la entrada del usuario se usa directamente como parte de un comando OS. |
| Inyección de código | Ocurre cuando la entrada del usuario está directamente dentro de una función que evalúa el código. |
| Inyecciones de SQL | Ocurre cuando la entrada del usuario se usa directamente como parte de una consulta SQL. |
| Inyección de secuencias de comandos de sitio cruzado/HTML | Ocurre cuando se muestra la entrada exacta del usuario en una página web. |

There are many other types of injections other than the above, like `LDAP injection`, `NoSQL Injection`, `HTTP Header Injection`, `XPath Injection`, `IMAP Injection`, `ORM Injection`y otros. Cada vez que la entrada del usuario se usa dentro de una consulta sin desinfectar correctamente, es posible escapar de los límites de la cadena de entrada del usuario a la consulta principal y manipularla para cambiar su propósito previsto. Es por eso que a medida que se introduzcan más tecnologías web en las aplicaciones web, veremos nuevos tipos de inyecciones introducidas en las aplicaciones web.

---

# **Inyecciones de comando de OS**

Cuando se trata de inyecciones de comandos del sistema operativo, la entrada del usuario que controlamos debe entrar directa o indirectamente (o de alguna manera afectar) una consulta web que ejecuta comandos del sistema. Todos los lenguajes de programación web tienen diferentes funciones que permiten al desarrollador ejecutar comandos del sistema operativo directamente en el servidor de back-end siempre que sea necesario. Esto puede usarse para varios fines, como instalar complementos o ejecutar ciertas aplicaciones.

### **Ejemplo de PHP**

Por ejemplo, una aplicación web escrita en`PHP`puede usar el`exec`, `system`, `shell_exec`, `passthru`, o`popen`Funciona para ejecutar comandos directamente en el servidor de back-end, cada uno con un caso de uso ligeramente diferente. El siguiente código es un ejemplo de código PHP que es vulnerable a las inyecciones de comandos:

Código:php

```php
<?phpif (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>
```

Quizás una aplicación web en particular tiene una funcionalidad que permite a los usuarios crear una nueva`.pdf`documento que se crea en el`/tmp`Directorio con un nombre de archivo suministrado por el usuario y luego puede ser utilizado por la aplicación web para fines de procesamiento de documentos. Sin embargo, como entrada del usuario del`filename`parámetro en el`GET`la solicitud se usa directamente con el`touch`Comando (sin ser desinfectado o escapado primero), la aplicación web se vuelve vulnerable a la inyección de comando OS. Este defecto se puede explotar para ejecutar comandos del sistema arbitrarios en el servidor de fondo.

### **Ejemplo de NodeJS**

Esto no es exclusivo de`PHP`Solo, pero puede ocurrir en cualquier marco o lenguaje de desarrollo web. Por ejemplo, si se desarrolla una aplicación web en`NodeJS`, un desarrollador puede usar`child_process.exec`o`child_process.spawn`Para el mismo propósito. El siguiente ejemplo realiza una funcionalidad similar a lo que discutimos anteriormente:

Código:javascript

```jsx
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})

```

El código anterior también es vulnerable a una vulnerabilidad de inyección de comando, ya que utiliza el`filename`Parámetro del`GET`Solicite como parte del comando sin desinfectarlo primero. Ambos`PHP`y`NodeJS`Las aplicaciones web se pueden explotar utilizando los mismos métodos de inyección de comando.

Del mismo modo, otros lenguajes de programación de desarrollo web tienen funciones similares utilizadas para los mismos fines y, si es vulnerable, pueden explotarse utilizando los mismos métodos de inyección de comandos. Además, las vulnerabilidades de inyección de comandos no son exclusivas de las aplicaciones web, pero también pueden afectar a otros binarios y clientes gruesos si pasan la entrada del usuario insanitizada a una función que ejecuta comandos del sistema, que también pueden explotarse con los mismos métodos de inyección de comandos.

La siguiente sección discutirá diferentes métodos para detectar y explotar vulnerabilidades de inyección de comandos en aplicaciones