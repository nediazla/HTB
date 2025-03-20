# Types of Databases

Las bases de datos, en general, se clasifican en`Relational Databases`y`Non-Relational Databases`. Solo las bases de datos relacionales utilizan SQL, mientras que las bases de datos no relacionales utilizan una variedad de métodos para las comunicaciones.

---

# **Bases de datos relacionales**

Una base de datos relacional es el tipo más común de base de datos. Utiliza un esquema, una plantilla, para dictar la estructura de datos almacenada en la base de datos. Por ejemplo, podemos imaginar una empresa que venda productos a sus clientes que tengan algún tipo de conocimiento almacenado sobre dónde van esos productos, a quién y en qué cantidad. Sin embargo, esto a menudo se hace en el back-end y sin información obvia en el front-end. Se pueden usar diferentes tipos de bases de datos relacionales para cada enfoque. Por ejemplo, la primera tabla puede almacenar y mostrar información básica al cliente, la segunda el número de productos vendidos y su costo, y la tercera tabla para enumerar quién compró esos productos y con qué datos de pago.

Las tablas en una base de datos relacional están asociadas con claves que proporcionan un resumen de base de datos rápido o acceso a la fila o columna específica cuando se deben revisar los datos específicos. Estas tablas, también llamadas entidades, están todas relacionadas entre sí. Por ejemplo, la tabla de información del cliente puede proporcionar a cada cliente una identificación específica que pueda indicar todo lo que necesitamos saber sobre ese cliente, como una dirección, nombre e información de contacto. Además, la tabla Descripción del producto puede asignar una ID específica a cada producto. La tabla que almacena todos los pedidos solo necesitaría registrar estas ID y su cantidad. Cualquier cambio en estas tablas las afectará a todas, pero previsible y sistemáticamente.

Sin embargo, al procesar una base de datos integrada, se requiere un concepto para vincular una tabla a otra usando su clave, llamada`relational database management system` (`RDBMS`). Muchas compañías que inicialmente usan conceptos diferentes están cambiando al concepto RDBMS porque este concepto es fácil de aprender, usar y comprender. Inicialmente, este concepto fue utilizado solo por grandes empresas. Sin embargo, muchos tipos de bases de datos ahora implementan el concepto RDBMS, como Microsoft Access, MySQL, SQL Server, Oracle, PostgreSQL y muchos otros.

Por ejemplo, podemos tener un`users`tabla en una base de datos relacional que contiene columnas como`id`, `username`, `first_name`, `last_name`y otros. El`id`se puede usar como la clave de la tabla. Otra mesa,`posts`, puede contener publicaciones hechas por todos los usuarios, con columnas como`id`, `user_id`, `date`, `content`, etcétera.

![](https://academy.hackthebox.com/storage/modules/75/web_apps_relational_db.jpg)

Podemos vincular el`id`desde`users`mesa al`user_id`en el`posts`Tabla para recuperar los detalles del usuario para cada publicación sin almacenar todos los detalles del usuario con cada publicación. Una tabla puede tener más de una clave, ya que otra columna se puede usar como clave para vincular con otra tabla. Entonces, por ejemplo, el`id`La columna se puede usar como clave para vincular el`posts`Tabla a otra tabla que contiene comentarios, cada uno de los cuales pertenece a una publicación en particular, y así sucesivamente.

La relación entre tablas dentro de una base de datos se llama esquema.

De esta manera, al usar bases de datos relacionales, se vuelve rápido y fácil recuperar todos los datos sobre un elemento particular de todas las bases de datos. Entonces, por ejemplo, podemos recuperar todos los detalles vinculados a un usuario específico de todas las tablas con una sola consulta. Esto hace que las bases de datos relacionales sean muy rápidas y confiables para grandes conjuntos de datos con una estructura clara y un diseño y una gestión eficiente de datos. El ejemplo más común de bases de datos relacionales es`MySQL`, que cubriremos en este módulo.

---

# **Bases de datos no relacionales**

Una base de datos no relacional (también llamada`NoSQL`Base de datos) no usa tablas, filas y columnas o claves principales, relaciones o esquemas. En su lugar, una base de datos NoSQL almacena datos utilizando varios modelos de almacenamiento, dependiendo del tipo de datos almacenados. Debido a la falta de una estructura definida para la base de datos, las bases de datos NoSQL son muy escalables y flexibles. Por lo tanto, cuando se trata de conjuntos de datos que no están muy bien definidos y estructurados, una base de datos NoSQL sería la mejor opción para almacenar dichos datos. Hay cuatro modelos de almacenamiento comunes para bases de datos NoSQL:

- Valor clave
- Basado en documentos
- Columna abierta
- Gráfico

Cada uno de los modelos anteriores tiene una forma diferente de almacenar datos. Por ejemplo, el

```
Key-Value
```

El modelo generalmente almacena datos en JSON o XML, y tiene una clave para cada par, y almacena todos sus datos como su valor:

![](https://academy.hackthebox.com/storage/modules/75/web_apps_non-relational_db.jpg)

El ejemplo anterior se puede representar usando JSON como:

Código:json

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}

```

Se parece a un elemento de diccionario en idiomas como`Python`o`PHP`(es decir`{'key':'value'}`), donde el`key`suele ser una cadena y el`value`puede ser una cadena, diccionario o cualquier objeto de clase.

El ejemplo más común de una base de datos NoSQL es`MongoDB`.

Las bases de datos no relacionales tienen un método diferente para la inyección, conocido como inyecciones de NoSQL. Las inyecciones de SQL son completamente diferentes a las inyecciones de NoSQL. Las inyecciones de NoSQL se cubrirán en un módulo posterior.

[Anterior](https://academy.hackthebox.com/module/33/section/178) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/33/section/183)

Hoja de trucos