# SIEM Visualization Example 1: Failed Logon Attempts (All Users)

Los paneles de las soluciones SIEM sirven como contenedores para múltiples visualizaciones, lo que nos permite organizar y mostrar datos de manera significativa.

En esta y las siguientes secciones, crearemos un panel y algunas visualizaciones desde cero.

---

# **Desarrollando nuestro primer panel y visualización**

Navegue hasta el final de esta sección y haga clic en `Click here to spawn the target system!`

Ahora, navega hasta `http://[Target IP]:5601`, haga clic en el botón de navegación lateral y haga clic en "Panel de control".

Elimine el panel de "Alertas SOC" existente de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/211/visualization29.png)

Al volver a visitar la página del Panel, se nos presentará un mensaje que indica que actualmente no existen paneles. Además, habrá una opción disponible para crear un nuevo Panel y su primera visualización. Para iniciar la creación de nuestro primer panel simplemente debemos hacer clic en el botón "Crear nuevo panel".

![](https://academy.hackthebox.com/storage/modules/211/dashboard.png)

Ahora, para iniciar la creación de nuestra primera visualización, simplemente debemos hacer clic en el botón "Crear visualización".

![](https://academy.hackthebox.com/storage/modules/211/visualization.png)

Al iniciar la creación de nuestra primera visualización, aparecerá la siguiente nueva ventana con varias opciones y configuraciones.

Antes de proceder con cualquier configuración, es importante que primero hagamos clic en el icono del calendario para abrir el selector de hora. Luego, debemos especificar el rango de fechas como "últimos 15 años". Finalmente, podemos hacer clic en el botón "Aplicar" para aplicar el rango de fechas especificado a los datos.

![](https://academy.hackthebox.com/storage/modules/211/visualization1.png)

Hay cuatro cosas que debemos notar en esta ventana:

1. Una opción de filtro que nos permite filtrar los datos antes de crear un gráfico. Por ejemplo, si nuestro objetivo es mostrar intentos fallidos de inicio de sesión, podemos usar un filtro para considerar solo los ID de eventos que coincidan.`4625 – Failed logon attempt on a Windows system`. La siguiente imagen muestra cómo podemos especificar dicho filtro.
    
    ![](https://academy.hackthebox.com/storage/modules/211/visualization2.png)
    
2. Este campo indica el conjunto de datos (índice) que vamos a utilizar. Es común que los datos de varias fuentes de infraestructura se separen en diferentes índices, como red, Windows, Linux, etc. En este ejemplo en particular, especificaremos`windows*`en el "Patrón de índice".
3. Esta barra de búsqueda nos brinda la capacidad de verificar dos veces la existencia de un campo específico dentro de nuestro conjunto de datos, lo que sirve como otra forma de asegurarnos de que estamos viendo los datos correctos. Por ejemplo, digamos que estamos interesados ​​en el`user.name.keyword`campo. Podemos usar la barra de búsqueda para realizar una búsqueda rápidamente y verificar si este campo está presente y descubierto dentro de nuestro conjunto de datos seleccionado. Esto nos permite confirmar que estamos accediendo al campo deseado y trabajando con datos precisos.
    
    ![](https://academy.hackthebox.com/storage/modules/211/visualization11.png)
    
    "Por qué `user.name.keyword` y no `user.name`?", te preguntarás. Deberíamos utilizar el `.keyword` cuando se trata de agregaciones. Por favor consulte esto[pregunta de desbordamiento de pila](https://stackoverflow.com/questions/48869795/difference-between-a-field-and-the-field-keyword) para una respuesta más elaborada.
    
4. Por último, este menú desplegable nos permite seleccionar el tipo de visualización que queremos crear. La opción predeterminada que se muestra en la imagen anterior es "Barra vertical apilada". Si hacemos clic en ese botón, se revelarán opciones adicionales disponibles (imagen redactada porque no todas las opciones caben en la pantalla). De esta lista ampliada podremos elegir el tipo de visualización deseada que mejor se adapte a nuestros requerimientos y necesidades de presentación de datos.
    
    ![](https://academy.hackthebox.com/storage/modules/211/visualization4.png)
    

---

Para esta visualización, seleccionemos la opción "Tabla". Luego de seleccionar la “Tabla”, podemos proceder a hacer clic en la opción “Filas”. Esto nos permitirá elegir los elementos de datos específicos que queremos incluir en la vista de tabla.

![](https://academy.hackthebox.com/storage/modules/211/visualization5.png)

Configuremos los ajustes de "Filas" de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/211/visualization6.png)

**Nota**: Lo notarás `Rank by Alphabetical` y no `Rank by Count of records` como en la captura de pantalla anterior. Esto está bien. Cuando realice la siguiente configuración a continuación,`Count of records`estará disponible.

Avanzando, cerremos la ventana "Filas" y procedamos a ingresar a la configuración de "Métricas".

![](https://academy.hackthebox.com/storage/modules/211/visualization7.png)

En la ventana "Métricas", seleccionemos "contar" como la métrica deseada.

![](https://academy.hackthebox.com/storage/modules/211/visualization8.png)

Tan pronto como seleccionemos "Contar" como métrica, observaremos que la tabla se llena con datos (suponiendo que haya eventos presentes en el conjunto de datos seleccionado).

![](https://academy.hackthebox.com/storage/modules/211/visualization9.png)

Una última adición a la tabla es incluir otra configuración de "Filas" para mostrar la máquina donde ocurrió el intento fallido de inicio de sesión. Para ello seleccionaremos el`host.hostname.keyword`, que representa la computadora que informa el intento fallido de inicio de sesión. Esto nos permitirá mostrar el nombre del host o el nombre de la máquina junto con el recuento de intentos fallidos de inicio de sesión, como se muestra en la imagen.

![](https://academy.hackthebox.com/storage/modules/211/visualization12.png)

Ahora podemos ver tres columnas en la tabla, que contienen la siguiente información:

1. El nombre de usuario de las personas que inician sesión (Nota: actualmente muestra tanto usuarios como computadoras. Idealmente, se debería implementar un filtro para excluir dispositivos informáticos y mostrar solo usuarios).
2. La máquina en la que se produjo el intento de inicio de sesión.
3. La cantidad de veces que ocurrió el evento (según el período de tiempo especificado o el conjunto de datos completo, según la configuración).

Finalmente, haz clic en “Guardar y regresar”, y observarás que la nueva visualización se agrega al tablero, apareciendo como se muestra en la siguiente imagen.

![](https://academy.hackthebox.com/storage/modules/211/visualization13.png)

No olvidemos guardar también el panel. Podemos hacerlo simplemente pulsando en el botón "Guardar".

![](https://academy.hackthebox.com/storage/modules/211/visualization15.png)

---

# **Refinando la visualización**

Supongamos que el administrador del SOC sugirió las siguientes mejoras:

- Se deben especificar nombres de columnas más claros en la visualización.
- El tipo de inicio de sesión debe incluirse en la visualización.
- Los resultados de la visualización deben ordenarse.
- Los nombres de usuario DESKTOP-DPOESND, WIN-OK9BH1BCKSD y WIN-RMMGJA7T9TC no deben monitorearse
- [cuentas de computadora](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/service-accounts-computer) no debe ser monitoreado (no es una buena práctica)

Perfeccionemos la visualización que creamos para que cumpla con las sugerencias anteriores.

Navegar a `http://[Target IP]:5601`, haga clic en el botón de navegación lateral y haga clic en "Panel de control".

El panel que creamos anteriormente debería estar visible. Hagamos clic en el icono "lápiz"/editar.

![](https://academy.hackthebox.com/storage/modules/211/visualization16.png)

Ahora hagamos clic en el botón "engranaje" en la esquina superior derecha de nuestra visualización y luego hagamos clic en "Editar lente".

![](https://academy.hackthebox.com/storage/modules/211/visualization18.png)

Los "valores principales de usuario.nombre.palabra clave" deben cambiarse de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/211/visualization19.png)

![](https://academy.hackthebox.com/storage/modules/211/visualization17.png)

Los "valores principales de host.hostname.keyword" deben cambiarse de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/211/visualization20.png)

El "Tipo de inicio de sesión" se puede agregar de la siguiente manera (usaremos el `winlog.logon.type.keyword` campo).

![](https://academy.hackthebox.com/storage/modules/211/visualization21.png)

![](https://academy.hackthebox.com/storage/modules/211/visualization22.png)

El "recuento de registros" debe cambiarse de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/211/visualization23.png)

Podemos introducir la clasificación de resultados de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/211/visualization25.png)

Lo único que tenemos que hacer ahora es pulsar en "Guardar y regresar".

Los nombres de usuario DESKTOP-DPOESND, WIN-OK9BH1BCKSD y WIN-RMMGJA7T9TC se pueden excluir especificando filtros adicionales de la siguiente manera.

![](https://academy.hackthebox.com/storage/modules/211/visualization24.png)

Las cuentas de computadora se pueden excluir especificando la siguiente consulta KQL y haciendo clic en el botón "Actualizar".

Ejemplo 1 de visualización SIEM: intentos fallidos de inicio de sesión (todos los usuarios)

```
NOT user.name: *$ AND winlog.channel.keyword: Security
```

El `AND winlog.channel.keyword: Security` parte es garantizar que no se tengan en cuenta registros no relacionados.

![](https://academy.hackthebox.com/storage/modules/211/visualization34.png)

Esta es nuestra visualización después de todos los refinamientos que realizamos.

![](https://academy.hackthebox.com/storage/modules/211/visualization35.png)

Finalmente, demos un título a nuestra visualización haciendo clic en "Sin título".

![](https://academy.hackthebox.com/storage/modules/211/visualization36.png)

No olvides hacer clic en el botón "Guardar" (el que está en la parte superior derecha de la ventana).

Espere entre 3 y 5 minutos para que Kibana esté disponible después de generar el objetivo de las preguntas a continuación.