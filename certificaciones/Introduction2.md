# Introduction

Bienvenido al`Attacking Web Applications with Ffuf`¡módulo!

Existen muchas herramientas y métodos para utilizar para directorio y combate de parámetros/falsificación bruta. En este módulo nos centraremos principalmente en el[fFUF](https://github.com/ffuf/ffuf)Herramienta para confuso web, ya que es una de las herramientas más comunes y confiables disponibles para la confusión web.

Se discutirán los siguientes temas:

- Fuzzing para directorios
- Fuzzing para archivos y extensiones
- Identificación de Vhosts ocultos
- Fuzzing para parámetros de PHP
- Fuzzing para valores de parámetros

Herramientas como`ffuf`Proporcionarnos una práctica forma automatizada de eliminar los componentes individuales de la aplicación web o una página web. Esto significa, por ejemplo, que usamos una lista que se utiliza para enviar solicitudes al servidor web si la página con el nombre de nuestra lista existe en el servidor web. Si obtenemos un código de respuesta 200, entonces sabemos que esta página existe en el servidor web, y podemos verlo manualmente.