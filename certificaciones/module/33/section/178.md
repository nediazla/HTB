# Intro to Databases

Antes de conocer las inyecciones de SQL, necesitamos obtener más información sobre bases de datos y lenguaje de consulta estructurado (SQL), qué bases de datos realizarán las consultas necesarias. Las aplicaciones web utilizan bases de datos de back-end para almacenar varios contenidos e información relacionadas con la aplicación web. Esto puede ser activos de aplicaciones web centrales, como imágenes y archivos, contenido, como publicaciones y actualizaciones, o datos de usuario, como nombres de usuario y contraseñas.

Hay muchos tipos diferentes de bases de datos, cada una de las cuales se ajusta a un tipo particular de uso. Tradicionalmente, una aplicación utilizó bases de datos basadas en archivos, que fue muy lenta con el aumento de tamaño. Esto llevó a la adopción de`Database Management Systems` (`DBMS`).

---

# **Sistemas de gestión de bases de datos**

Un sistema de gestión de bases de datos (DBMS) ayuda a crear, definir, alojar y administrar bases de datos. Se diseñaron con el tiempo varios tipos de DBM, como DBMS relacionales basados en archivos (RDBMS), NoSQL, Graph Based y Key/Value.

Hay múltiples formas de interactuar con un DBMS, como herramientas de línea de comandos, interfaces gráficas o incluso API (interfaces de programación de aplicaciones). DBMS se usa en varios sectores bancarios, finanzas y educación para registrar grandes cantidades de datos. Algunas de las características esenciales de un DBMS incluyen:

| **Característica** | **Descripción** |
| --- | --- |
| `Concurrency` | Una aplicación del mundo real podría tener múltiples usuarios interactuando con ella simultáneamente. Un DBMS se asegura de que estas interacciones concurrentes tengan éxito sin corromper ni perder ningún dato. |
| `Consistency` | Con tantas interacciones concurrentes, el DBMS debe asegurarse de que los datos sigan siendo consistentes y válidos en toda la base de datos. |
| `Security` | DBMS proporciona controles de seguridad de grano fino a través de la autenticación y los permisos de los usuarios. Esto evitará la visualización o edición no autorizada de datos confidenciales. |
| `Reliability` | Es fácil hacer una copia de seguridad de las bases de datos y las vuelve a un estado anterior en caso de pérdida de datos o incumplimiento. |
| `Structured Query Language` | SQL simplifica la interacción del usuario con la base de datos con una sintaxis intuitiva que admite varias operaciones. |

---

# **Arquitectura**

El siguiente diagrama detalla una arquitectura de dos niveles.

![](https://academy.hackthebox.com/storage/modules/33/db_2.png)

`Tier I`Por lo general, consta de aplicaciones del lado del cliente, como sitios web o programas de GUI. Estas aplicaciones consisten en interacciones de alto nivel, como inicio de sesión o comentarios del usuario. Los datos de estas interacciones se pasan a`Tier II`a través de llamadas API u otras solicitudes.

El segundo nivel es el middleware, que interpreta estos eventos y los coloca en una forma requerida por el DBMS. Finalmente, la capa de aplicación utiliza bibliotecas y controladores específicos basados en el tipo de DBMS para interactuar con ellos. El DBMS recibe consultas del segundo nivel y realiza las operaciones solicitadas. Estas operaciones pueden incluir inserción, recuperación, eliminación o actualización de datos. Después del procesamiento, el DBMS devuelve los datos o códigos de error solicitados en caso de consultas no válidas.

Es posible alojar el servidor de aplicaciones y el DBMS en el mismo host. Sin embargo, las bases de datos con grandes cantidades de datos que respaldan a muchos usuarios generalmente se alojan por separado para mejorar el rendimiento y la escalabilidad.