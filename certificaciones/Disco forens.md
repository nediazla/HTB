# Disco forense

Como hemos destacado anteriormente, adherirse a la secuencia de la volatilidad de los datos es crucial. Es imperativo que analicemos cada byte para detectar las huellas sutiles que dejan los ciber adversarios. Habiendo cubierto la memoria forense, ahora cambiemos nuestra atención al área de`disk forensics`(Examen y análisis de imágenes de disco).

Muchas herramientas forenses de disco, tanto comerciales como de código abierto, vienen llenos de características. Sin embargo, para los equipos de respuesta a incidentes, se destacan ciertas funcionalidades:

- `File Structure Insight`: Poder navegar y ver la jerarquía de archivos del disco es crucial. Las herramientas forenses de primer nivel deben mostrar esta estructura, permitiendo un acceso rápido a archivos específicos, especialmente en ubicaciones conocidas en un sistema sospechoso.
- `Hex Viewer`: Para esos momentos en los que necesitas acercarte a tus datos, es esencial ver archivos en hexadecimal. Esta capacidad es especialmente útil cuando se trata de amenazas como malware a medida o hazañas únicas.
- `Web Artifacts Analysis`: Con tantos datos de usuario vinculados a actividades web, una herramienta forense debe examinar y presentar estos datos de manera eficiente. Es un cambio de juego cuando estás reconstruyendo eventos que llevan a un usuario que aterriza en un sitio web malicioso.
- `Email Carving`: A veces, el sendero conduce a amenazas internas. Tal vez sea un empleado deshonesto o simplemente alguien que se resbaló. En tales casos, los correos electrónicos a menudo contienen la clave. Una herramienta que puede extraer y presentar este datos de transmisión de datos, lo que hace que sea más fácil conectar los puntos.
- `Image Viewer`: A veces, las imágenes almacenadas en sistemas pueden contar una historia propia. Ya sea para verificaciones de políticas o inmersiones más profundas, tener un espectador incorporado es una bendición.
- `Metadata Analysis`: Detalles como las marcas de tiempo de creación de archivos, los hashes y la ubicación del disco pueden ser invaluables. Considere un escenario en el que intenta coincidir con la hora de lanzamiento de una aplicación con una alerta de malware. Dichas correlaciones pueden ser el Linchpin en su investigación.

Ingresar[Autopsia](https://www.autopsy.com/): Una plataforma forense fácil de usar construida sobre el conjunto de herramientas de kit de detectives de código abierto. Refleja muchas características que encontraría en sus homólogos comerciales: evaluaciones de línea de tiempo, búsqueda de palabras clave, recuperaciones de artefactos web y por correo electrónico, y la capacidad de analizar resultados basados en hashes de archivos maliciosos conocidos.

Una vez que haya cargado una imagen forense y haya procesado los datos, verá los artefactos forenses perfectamente organizados en el panel lateral. Desde aquí, puedes:

![](https://academy.hackthebox.com/storage/modules/237/image16.png)

- Sumergirse en`Data Sources`para explorar archivos y directorios.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image1_.png)
    
- Examinar`Web Artifacts`.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image80.png)
    
- Controlar`Attached Devices`.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image8_.png)
    
- Recuperar`Deleted Files`.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image51.png)
    
- Conducta`Keyword Searches`.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image75.png)
    
    ![](https://academy.hackthebox.com/storage/modules/237/image23.png)
    
- Usar`Keyword Lists`para búsquedas específicas.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image72.png)
    
    ![](https://academy.hackthebox.com/storage/modules/237/image52.png)
    
- Emprender`Timeline Analysis`para mapear eventos.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image99.png)
    

---

Utilizaremos en gran medida la autopsia en la próxima sección "Escenario forense digital práctico".