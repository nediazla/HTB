# Principios de enumeración

La enumeración es un término ampliamente utilizado en seguridad cibernética. Es representa la recopilación de información utilizando métodos activos (escaneos) y pasivos (uso de proveedores de terceros). Es importante tener en cuenta que OSINT es un procedimiento independiente y debe realizarse por separado de la enumeración porque`OSINT is based exclusively on passive information gathering`y no implica una enumeración activa del objetivo dado. La enumeración es un bucle en el que recopilamos repetidamente la información en función de los datos que tenemos o ya hemos descubierto.

La información se puede recopilar de dominios, direcciones IP, servicios accesibles y muchas otras fuentes.

Una vez que hemos identificado objetivos en la infraestructura de nuestro cliente, necesitamos examinar los servicios y protocolos individuales. En la mayoría de los casos, estos son servicios que permiten la comunicación entre los clientes, la infraestructura, la administración y los empleados.

Si imaginamos que hemos sido contratados para investigar la seguridad de TI de una empresa, comenzaremos a desarrollar una comprensión general de la funcionalidad de la empresa. Por ejemplo, necesitamos comprender cómo está estructurado la empresa, qué servicios y proveedores de terceros usan, qué medidas de seguridad pueden estar en su lugar y más. Aquí es donde esta etapa puede malinterpretarse un poco porque la mayoría de las personas se centran en lo obvio e intentan abrirse camino hacia los sistemas de la compañía en lugar de comprender cómo se establece la infraestructura y qué aspectos y servicios técnicos son necesarios para poder ofrecer un servicio específico.

Un ejemplo de un enfoque tan incorrecto podría ser que después de encontrar servicios de autenticación como SSH, RDP, WinRM y similares, tratamos de hacer una fuerza bruta con contraseñas y nombres de usuario comunes/débiles. Desafortunadamente, el forzo bruto es un método ruidoso y puede conducir fácilmente a la lista negra, lo que hace imposible las pruebas más imposibles. Principalmente, esto puede suceder si no sabemos sobre las medidas de seguridad defensivas de la compañía y su infraestructura. Algunos pueden sonreír ante este enfoque, pero la experiencia ha demostrado que demasiados probadores adoptan este tipo de enfoque.

`Our goal is not to get at the systems but to find all the ways to get there.`

Podemos pensar en esto como una analogía de un cazador de tesoros que se prepara para su expedición. No solo agarraba una pala y comenzaba a cavar en algún lugar aleatorio, sino que planearía su equipo y estudiaría mapas y aprendía sobre el terreno que tiene que cubrir y dónde puede estar el tesoro para que pueda traer las herramientas adecuadas. Si va a cavar agujeros en todas partes, causará daños, perderá el tiempo y la energía, y probablemente nunca alcanzará su objetivo. Lo mismo puede decirse para comprender la infraestructura interna y externa de una empresa, mapearla y formular cuidadosamente nuestro plan de ataque.

Los principios de enumeración se basan en algunas preguntas que facilitarán todas nuestras investigaciones en cualquier situación concebible. En la mayoría de los casos, el enfoque principal de muchos probadores de penetración es en lo que pueden ver y no en lo que no pueden ver. Sin embargo, incluso lo que no podemos ver es relevante para nosotros y puede ser de gran importancia. La diferencia aquí es que comenzamos a ver los componentes y aspectos que no son visibles a primera vista con nuestra experiencia.

- ¿Qué podemos ver?
- ¿Qué razones podemos tener para verlo?
- ¿Qué imagen hace lo que vemos crea para nosotros?
- ¿Qué ganamos de él?
- ¿Cómo podemos usarlo?
- ¿Qué no podemos ver?
- ¿Qué razones pueden haber que no vemos?
- ¿Qué resultados de la imagen para nosotros de lo que no vemos?

Un aspecto importante que no debe confundirse aquí es que siempre hay excepciones a las reglas. Los principios, sin embargo, no cambian. Otra ventaja de estos principios es que podemos ver en las tareas prácticas que no nos faltan habilidades de prueba de penetración, sino comprensión técnica cuando de repente no sabemos cómo proceder porque nuestra tarea principal no es explotar las máquinas, sino encontrar cómo pueden ser explotados.

| **`No.`** | **`Principle`** |
| --- | --- |
| 1. | Hay más de lo que parece. Considere todos los puntos de vista. |
| 2. | Distinguir entre lo que vemos y lo que no vemos. |
| 3. | Siempre hay formas de obtener más información. Comprender el objetivo. |

Para familiarizarnos con estos principios, debemos escribir estas preguntas y principios donde siempre podemos verlas y consultarlas con facilidad.

Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/112/section/1185)

Hoja de trucosRecursos