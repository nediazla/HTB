# SQLMap Output Description

Al final de la sección anterior, la salida SQLMAP nos mostró mucha información durante su escaneo. Estos datos suelen ser cruciales de entender, ya que nos guía a través del proceso de inyección SQL automatizado. Esto nos muestra exactamente qué tipo de vulnerabilidades está explotando SQLMAP, lo que nos ayuda a informar qué tipo de inyección tiene la aplicación web. Esto también puede ser útil si quisiéramos explotar manualmente la aplicación web una vez que SQLMAP determina el tipo de inyección y el parámetro vulnerable.

---

# **Descripción de los mensajes de registro**

Los siguientes son algunos de los mensajes más comunes que generalmente se encuentran durante un escaneo de SQLMAP, junto con un ejemplo de cada uno del ejercicio anterior y su descripción.

### **El contenido de URL es estable**

`Log Message:`

- "El contenido de URL objetivo es estable"

Esto significa que no hay cambios importantes entre las respuestas en caso de solicitudes idénticas continuas. Esto es importante desde el punto de vista de automatización ya que, en caso de respuestas estables, es más fácil detectar diferencias causadas por los posibles intentos de SQLI. Si bien la estabilidad es importante, SQLMAP tiene mecanismos avanzados para eliminar automáticamente el "ruido" potencial que podría provenir de objetivos potencialmente inestables.

### **El parámetro parece ser dinámico**

`Log Message:`

- "Obtener el parámetro 'id' parece ser dinámico"

Siempre se desea que el parámetro probado sea "dinámico", ya que es una señal de que cualquier cambio realizado en su valor daría como resultado un cambio en la respuesta; Por lo tanto, el parámetro puede estar vinculado a una base de datos. En caso de que la salida sea "estática" y no cambie, podría ser un indicador de que el valor del parámetro probado no es procesado por el objetivo, al menos en el contexto actual.

### **El parámetro puede ser inyectable**

`Log Message:`"La prueba heurística (básica) muestra que obtener 'id' de los parámetros podría ser inyectable (posibles dbms: 'mysql')"

Como se discutió anteriormente, los errores de DBMS son una buena indicación del potencial SQLI. En este caso, hubo un error MySQL cuando SQLMAP envía un valor intencionalmente inválido (por ejemplo`?id=1",)..).))'`), que indica que el parámetro probado podría ser inyectable SQLI y que el objetivo podría ser MySQL. Cabe señalar que esto no es una prueba de SQLI, sino solo una indicación de que el mecanismo de detección debe probarse en la ejecución posterior.

### **El parámetro puede ser vulnerable a los ataques XSS**

`Log Message:`

- "La prueba heurística (XSS) muestra que obtener 'id' de los parámetros podría ser vulnerable a los ataques de secuencias de comandos de sitios cruzados (XSS)"

Si bien no es su propósito principal, SQLMAP también ejecuta una prueba heurística rápida para la presencia de una vulnerabilidad XSS. En pruebas a gran escala, donde se están probando muchos parámetros con SQLMAP, es bueno tener este tipo de controles heurísticos rápidos, especialmente si no se encuentran vulnerabilidades SQLI.

### **DBMS de back-end es '...'**

`Log Message:`

- "Parece que el DBMS de back-end es 'mysql'. ¿Quieres omitir cargas útiles de prueba específicas para otros DBMS? [S/N]"

En una ejecución normal, SQLMAP prueba para todos los DBMS compatibles. En caso de que exista una clara indicación de que el objetivo está utilizando los DBM específicos, podemos reducir las cargas útiles a ese DBMS específico.

### **Level/risk values**

`Log Message:`

- "Para las pruebas restantes, ¿desea incluir todas las pruebas para valores de nivel '1) y riesgo (1) de extensión' MySQL '? [S/N]"

Si existe una clara indicación de que el objetivo usa los DBM específicos, también es posible extender las pruebas para las mismas DBM específicas más allá de las pruebas regulares.

Básicamente, esto significa ejecutar todas las cargas útiles de inyección SQL para ese DBMS específico, mientras que si no se detectara DBMS, solo se probaron las cargas útiles principales.

### **Valores reflexivos encontrados**

`Log Message:`

- "reflective value(s) found and filtering out"

Solo una advertencia de que partes de las cargas útiles usadas se encuentran en la respuesta. Este comportamiento podría causar problemas a las herramientas de automatización, ya que representa la basura. Sin embargo, SQLMAP tiene mecanismos de filtrado para eliminar dicha basura antes de comparar el contenido de la página original.

### **El parámetro parece ser inyectable**

`Log Message:`

- "Obtener el parámetro 'id' parece ser 'y ciego a base de booleano, donde o tener cláusula' inyectable (con --string =" luther ")"

Este mensaje indica que el parámetro parece ser inyectable, aunque todavía existe la posibilidad de que sea un hallazgo falso positivo. En el caso de tipos SQLI ciegos y similares a base de booleanos (por ejemplo, ciegas basadas en el tiempo), donde existe una alta probabilidad de falsos positivos, al final de la ejecución, SQLMAP realiza pruebas extensas que consisten en verificaciones lógicas simples para la eliminación de hallazgos falsos positivos.

Además,`with --string="luther"`indica que SQLMAP reconoció y usó la aparición del valor de cadena constante`luther`en la respuesta para distinguir`TRUE`de`FALSE`respuestas. Este es un hallazgo importante porque en tales casos, no hay necesidad de el uso de mecanismos internos avanzados, como la dinamicidad/eliminación de reflexión o la comparación difusa de las respuestas, que no pueden considerarse falsas positivas.

### **Modelo estadístico de comparación basado en el tiempo**

`Log Message:`

- "La comparación basada en el tiempo requiere un modelo estadístico más grande, espere ........... (hecho)"

SQLMAP utiliza un modelo estadístico para el reconocimiento de respuestas objetivo regulares y (deliberadamente) retrasadas. Para que este modelo funcione, existe el requisito de recopilar un número suficiente de tiempos de respuesta regulares. De esta manera, SQLMAP puede distinguir estadísticamente entre el retraso deliberado incluso en los entornos de red de alta latencia.

### **Extender las pruebas de la técnica de inyección de la consulta de la Unión**

`Log Message:`

- "Extender automáticamente los rangos para las pruebas de la técnica de inyección de consulta de la Unión, ya que se encuentra al menos otra técnica (potencial)".

Los cheques SQLI sindicales requieren considerablemente más solicitudes de reconocimiento exitoso de la carga útil utilizable que otros tipos SQLI. Para reducir el tiempo de prueba por parámetro, especialmente si el objetivo no parece ser inyectable, el número de solicitudes se limita a un valor constante (es decir, 10) para este tipo de verificación. Sin embargo, si existe una buena posibilidad de que el objetivo sea vulnerable, especialmente porque se encuentra una técnica SQLI (potencial), SQLMAP extiende el número predeterminado de solicitudes de consulta sindical SQLI, debido a una mayor expectativa de éxito.

### **La técnica parece ser utilizable**

`Log Message:`

- "El pedido por la técnica parece ser utilizable. Esto debería reducir el tiempo necesario para encontrar el número correcto de columnas de consulta. Extiendo automáticamente el rango para la prueba de técnica de inyección de consulta de la Unión actual"

Como verificación heurística para el tipo SQLI de Union-Query, antes del real`UNION`Se envían cargas útiles, una técnica conocida como`ORDER BY`se verifica la usabilidad. En caso de que sea utilizable, SQLMAP puede reconocer rápidamente el número correcto de`UNION`columnas realizando el enfoque de búsqueda binaria.

Note that this depends on the affected table in the vulnerable query.

### **El parámetro es vulnerable**

`Log Message:`

- "Obtener el parámetro 'id' es vulnerable. ¿Quieres seguir probando a los demás (si los hay)? [S/N]"

Este es uno de los mensajes más importantes de SQLMAP, ya que significa que se encontró que el parámetro era vulnerable a las inyecciones de SQL. En los casos regulares, el usuario solo puede querer encontrar al menos un punto de inyección (es decir, parámetro) utilizable contra el objetivo. Sin embargo, si estuviéramos ejecutando una prueba extensa en la aplicación web y queremos informar todas las vulnerabilidades potenciales, podemos continuar buscando todos los parámetros vulnerables.

### **SQLMAP identificó puntos de inyección**

`Log Message:`

- "SQLMAP identificó los siguientes puntos de inyección con un total de 46 solicitudes de HTTP (s):"

A continuación se muestra una lista de todos los puntos de inyección con tipo, título y carga útil, que representa la prueba final de detección y explotación exitosas de las vulnerabilidades SQLI encontradas. Cabe señalar que SQLMAP enumera solo aquellos hallazgos que son probablemente explotables (es decir, utilizables).

### **Datos registrados en archivos de texto**

`Log Message:`

- "Datos obtenidos registrados a archivos de texto en '/home/user/.sqlmap/output/www.example.com'"

Esto indica la ubicación del sistema de archivos local utilizada para almacenar todos los registros, sesiones y datos de salida para un objetivo específico, en este caso,`www.example.com`. Después de tal ejecución inicial, donde el punto de inyección se detecta con éxito, todos los detalles para futuras ejecuciones se almacenan dentro de los archivos de sesión del mismo directorio. Esto significa que SQLMAP intenta reducir las solicitudes de destino requeridas tanto como sea posible, dependiendo de los datos de los archivos de sesión.

[Anterior](https://academy.hackthebox.com/module/58/section/694)