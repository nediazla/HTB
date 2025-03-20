# Web Archives

En el mundo digital acelerado, los sitios web van y vienen, dejando solo rastros fugaces de su existencia. Sin embargo, gracias a la[Máquina Wayback de Internet Archive](https://web.archive.org/), tenemos una oportunidad única para volver a visitar el pasado y explorar las huellas digitales de los sitios web como antes.

### **¿Cuál es la máquina Wayback?**

![](https://academy.hackthebox.com/storage/modules/144/wayback.png)

`The Wayback Machine`es un archivo digital de la World Wide Web y otra información en Internet. Fundada por Internet Archive, una organización sin fines de lucro, ha estado archivando sitios web desde 1996.

Permite a los usuarios "retroceder en el tiempo" y ver instantáneas de los sitios web como aparecieron en varios puntos de su historia. Estas instantáneas, conocidas como capturas o archivos, proporcionan una visión de las versiones pasadas de un sitio web, incluidos su diseño, contenido y funcionalidad.

### **¿Cómo funciona la máquina Wayback?**

Wayback Machine opera utilizando rastreadores web para capturar instantáneas de sitios web a intervalos regulares automáticamente. Estos rastreadores navegan a través de la web, siguiendo enlaces y páginas de indexación, al igual que cómo funcionan los rastreadores de motores de búsqueda. Sin embargo, en lugar de simplemente indexar la información para fines de búsqueda, la máquina Wayback almacena todo el contenido de las páginas, incluidos HTML, CSS, JavaScript, imágenes y otros recursos.

La operación de Wayback Machine se puede visualizar como un proceso de tres pasos:

[](https://mermaid.ink/svg/pako:eNpNjkEOgjAQRa_SzBou0IUJ4lI3uqQsJu1IG2lLhlZjCHe3YGLc_f9m8vMW0NEQSBgYJyvOVxWarmV8jS4Mvajrgzh2DWvrnhtQ4b_t57ZrtKZ53gBU4Ik9OlMWFxWEUJAseVIgSzTIDwUqrOUPc4q3d9AgE2eqgGMeLMg7jnNpeTKY6OSwaPkfJeNS5MtXePdeP1LGQQs)

1. `Crawling`: The Wayback Machine emplea rastreadores web automatizados, a menudo llamados "bots", para navegar por Internet sistemáticamente. Estos bots siguen enlaces de una página web a otra, como cómo hacer clic en Hyperlinks para explorar un sitio web. Sin embargo, en lugar de solo leer el contenido, estos bots descargan copias de las páginas web que encuentran.
2. `Archiving`: Las páginas web descargadas, junto con sus recursos asociados, como imágenes, hojas de estilo y scripts, se almacenan en el vasto archivo de la máquina Wayback Machine. Cada página web capturada está vinculada a una fecha y hora específicas, creando una instantánea histórica del sitio web en ese momento. Este proceso de archivo ocurre a intervalos regulares, a veces diarios, semanales o mensuales, dependiendo de la popularidad y frecuencia de actualizaciones del sitio web.
3. `Accessing`: Los usuarios pueden acceder a estas instantáneas archivadas a través de la interfaz Wayback Machine. Al ingresar a la URL de un sitio web y seleccionar una fecha, puede ver cómo el sitio web observó ese punto específico. Wayback Machine le permite navegar por las páginas individuales y proporciona herramientas para buscar términos específicos dentro del contenido archivado o descargar sitios web archivados completos para el análisis fuera de línea.

La frecuencia con la que la máquina Wayback archiva un sitio web varía. Algunos sitios web pueden archivarse varias veces al día, mientras que otros solo pueden tener algunas instantáneas repartidas durante varios años. Los factores que influyen en esta frecuencia incluyen la popularidad del sitio web, su tasa de cambio y los recursos disponibles para el archivo de Internet.

Es importante tener en cuenta que Wayback Machine no captura todas las páginas web en línea. Prioriza sitios web que se consideran de valor cultural, histórico o de investigación. Además, los propietarios de sitios web pueden solicitar que su contenido se excluya de la máquina Wayback, aunque esto no siempre está garantizado.

# **Por qué la máquina Wayback es importante para el reconocimiento web**

Wayback Machine es un tesoro para el reconocimiento web, que ofrece información que puede ser fundamental en varios escenarios. Su importancia radica en su capacidad para presentar el pasado de un sitio web, proporcionando ideas valiosas que pueden no ser evidentes en su estado actual:

1. `Uncovering Hidden Assets and Vulnerabilities`: The Wayback Machine le permite descubrir páginas web, directorios, archivos o subdominios web que podrían no ser accesibles en el sitio web actual, lo que puede exponer información confidencial o defectos de seguridad.
2. `Tracking Changes and Identifying Patterns`: Al comparar las instantáneas históricas, puede observar cómo ha evolucionado el sitio web, revelando cambios en la estructura, el contenido, las tecnologías y las posibles vulnerabilidades.
3. `Gathering Intelligence`: El contenido archivado puede ser una fuente valiosa de OSINT, proporcionando información sobre las actividades pasadas del objetivo, las estrategias de marketing, los empleados y las opciones de tecnología.
4. `Stealthy Reconnaissance`: Acceder a las instantáneas archivadas es una actividad pasiva que no interactúa directamente con la infraestructura del objetivo, por lo que es una forma menos detectable de recopilar información.

# **Ir a Wayback en HTB**

Podemos ver la primera versión archivada de HackTheBox ingresando la página que estamos buscando en la máquina Wayback y seleccionando la fecha de captura más temprana disponible, siendo`2017-06-10 @ 04h23:01`

![](https://academy.hackthebox.com/storage/modules/144/wayback-htb.png)