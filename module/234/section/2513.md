# Reglas de Yara y Yara

`YARA`es una poderosa herramienta de coincidencia de patrones y formato de regla utilizado para identificar y clasificar archivos basados en patrones, características o contenido específicos. Los analistas de SOC comúnmente usan las reglas de Yara para detectar y clasificar muestras de malware, archivos sospechosos o indicadores de compromiso (COI).

Las reglas de Yara generalmente se escriben en una sintaxis de regla que define las condiciones y patrones que se coincidirán con los archivos. Estas reglas pueden incluir varios elementos, como cadenas, expresiones regulares y operadores lógicos booleanos, lo que permite a los analistas crear reglas de detección complejas y precisas. Es importante tener en cuenta que las reglas de Yara pueden reconocer los patrones textuales y binarios, y también pueden aplicarse a las actividades forenses de memoria.

Cuando se aplica, Yara escanea archivos o directorios y los combina con las reglas definidas. Si un archivo coincide con un patrón o condición específica, puede activar una alerta o justificar un examen más detallado como una posible amenaza de seguridad.

![](https://academy.hackthebox.com/storage/modules/234/yara_intro.png)

Las reglas de Yara son especialmente útiles para los analistas de SOC al analizar muestras de malware, realizar investigaciones forenses o realizar actividades de caza de amenazas. La flexibilidad y la extensibilidad de Yara lo convierten en una herramienta valiosa en la comunidad de seguridad cibernética.

# **Usos de Yara**

- **`Malware Detection and Classification`**: Yara se usa comúnmente para detectar e identificar muestras de malware basadas en patrones, características o indicadores específicos. Las reglas de Yara se pueden crear para que coincidan con firmas de malware conocidas, comportamientos o propiedades de archivo, ayudando en la identificación de archivos maliciosos y potencialmente evitando un mayor compromiso. Dentro del alcance de los forenses digitales, Yara también puede identificar patrones sospechosos o maliciosos en imágenes de memoria capturadas.
- **`File Analysis and Classification`**: Yara es valioso para analizar y clasificar archivos basados en patrones o atributos específicos. Los analistas pueden crear reglas de Yara para clasificar archivos mediante diferentes formatos de archivo o tipo de archivo, versión, metadatos, empacadores u otras características. Esta capacidad es útil en análisis forense, investigación de malware o identificación de tipos de archivos específicos dentro de grandes conjuntos de datos.
- **`Indicator of Compromise (IOC) Detection`**: Yara se puede emplear para buscar indicadores específicos de compromiso (IOC) dentro de archivos o directorios. Al definir las reglas de Yara que se dirigen a patrones de COI específicos, como nombres de archivos, claves de registro o artefactos de red, los equipos de seguridad pueden identificar posibles violaciones de seguridad o ataques continuos.
- **`Community-driven Rule Sharing`**: Con Yara, tenemos la capacidad de aprovechar una comunidad que regularmente contribuye y comparte sus reglas de detección. Esto asegura que actualicemos y refinemos constantemente nuestros mecanismos de detección.
- **`Create Custom Security Solutions`**: Al combinar las reglas de Yara con otras técnicas como análisis estático y dinámico, sandboxing y monitoreo de comportamiento, se pueden crear soluciones de seguridad efectivas.
- **`Custom Yara Signatures/Rules`**: Yara nos permite crear reglas personalizadas adaptadas a las necesidades y el entorno específicos de nuestra organización. Al implementar estas reglas personalizadas en nuestra infraestructura de seguridad, como las soluciones antivirus o detección de puntos finales y respuesta (EDR), podemos mejorar nuestras capacidades de defensa. Las reglas personalizadas de Yara pueden ayudar a identificar amenazas únicas o específicas que sean específicas de los activos, aplicaciones o la industria de nuestra organización.
- **`Incident Response`**: Yara ayuda a la respuesta a incidentes al permitir a los analistas buscar y analizar rápidamente archivos o imágenes de memoria para patrones o indicadores específicos. Al aplicar las reglas de Yara durante el proceso de investigación, los analistas pueden identificar artefactos relevantes, determinar el alcance de un incidente y recopilar información crítica para ayudar en los esfuerzos de remediación.
- **`Proactive Threat Hunting`**: En lugar de esperar una alerta, podemos aprovechar a Yara para realizar búsquedas proactivas en nuestros entornos, buscando posibles amenazas o restos de infecciones pasadas.

# **¿Cómo funciona Yara?**

En resumen, el motor Yara Scan, equipado con módulos Yara, escanea un conjunto de archivos comparando su contenido con los patrones definidos en un conjunto de reglas. Cuando un archivo coincide con los patrones y condiciones especificados en una regla Yara, se considera un archivo detectado. Este proceso permite a los analistas identificar eficientemente archivos que exhiben comportamientos o características específicas, ayudando en la detección de malware, la identificación del COI y la caza de amenazas. Este flujo se demuestra en el diagrama a continuación.

![](https://academy.hackthebox.com/storage/modules/234/yara_working.png)

En el diagrama anterior, podemos ver que el motor Yara Scan, que usa módulos Yara, coincide con los patrones definidos en un conjunto de reglas contra un conjunto de archivos, lo que resulta en la detección de archivos que cumplen con los patrones y condiciones especificados. Así es como las reglas de Yara son útiles para identificar las amenazas. Entendamos esto con más detalle.

- **`Set of Rules (containing suspicious patterns)`**: En primer lugar, tenemos una o más reglas de Yara, que son creadas por analistas de seguridad. Estas reglas definen patrones, características o indicadores específicos que deben coincidir dentro de los archivos. Las reglas pueden incluir cadenas, expresiones regulares, secuencias de bytes y otros indicadores de interés. Las reglas generalmente se almacenan en un formato de archivo de regla Yara (por ejemplo,`.yara`o`.yar`Archivo) para una fácil gestión y reutilización.
- **`Set of Files (for scanning)`**: Un conjunto de archivos, como ejecutables, documentos u otros archivos binarios o basados en texto, se proporcionan como entrada al motor Yara Scan. Los archivos se pueden almacenar en un disco local, dentro de un directorio, o incluso dentro de imágenes de memoria o capturas de tráfico de red.
- **`YARA Scan Engine`**: El motor Yara Scan es el componente central responsable de realizar el escaneo y la coincidencia de archivos reales con las reglas Yara definidas. Utiliza módulos Yara, que son conjuntos de algoritmos y técnicas, para comparar eficientemente el contenido de los archivos con los patrones especificados en las reglas.
- **`Scanning and Matching`**: El motor Yara Scan itera a través de cada archivo en el conjunto, uno a la vez. Para cada archivo, analiza el byte de contenido por byte, buscando coincidencias contra los patrones definidos en las reglas de Yara. El motor Yara Scan utiliza diversas técnicas de coincidencia, que incluyen coincidencia de cadenas, expresiones regulares y coincidencia binaria, para identificar patrones e indicadores dentro de los archivos.
- **`Detection of Files`**: Cuando un archivo coincide con los patrones y condiciones especificados en una regla Yara, se considera un archivo detectado. El motor de escaneo de Yara registra información sobre la coincidencia, como la regla coincidente, la ruta del archivo y el desplazamiento dentro del archivo donde se produjo la coincidencia y proporciona salida que indica la detección, que puede procesarse, registrarse o utilizar aún más para acciones posteriores.

# **Estructura de reglas de yara**

Vamos a sumergirnos en la estructura de una regla de Yara. Las reglas de Yara consisten en varios componentes que definen las condiciones y patrones que coinciden dentro de los archivos. Un ejemplo de una regla de Yara es el siguiente:

Código:Yara

```
rule my_rule {

    meta:
        author = "Author Name"
        description = "example rule"
        hash = ""

    strings:
        $string1 = "test"
        $string2 = "rule"
        $string3 = "htb"

    condition:
        all of them
}

```

Cada regla en Yara comienza con la palabra clave`rule`seguido de un`rule identifier`. Los identificadores de reglas son sensibles a los casos, donde el primer carácter no puede ser un dígito y no puede exceder los 128 caracteres.

Las siguientes palabras clave están reservadas y no se pueden usar como identificador:

![](https://academy.hackthebox.com/storage/modules/234/yara_keywords.png)

Ahora, repasemos la estructura de las reglas de Yara usando una regla que identifica las cadenas asociadas con el[Wanna](https://www.mandiant.com/resources/blog/wannacry-malware-profile)Ransomware como ejemplo. La siguiente regla instruye a Yara que marque cualquier archivo que contenga las tres cadenas especificadas como`Ransomware_WannaCry`.

Código:Yara

```
rule Ransomware_WannaCry {

    meta:
        author = "Madhukar Raina"
        version = "1.0"
        description = "Simple rule to detect strings from WannaCry ransomware"
        reference = "https://www.virustotal.com/gui/file/ed01ebfbc9eb5bbea545af4d01bf5f1071661840480439c6e5babe8e080e41aa/behavior"

    strings:
        $wannacry_payload_str1 = "tasksche.exe" fullword ascii
        $wannacry_payload_str2 = "www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com" ascii
        $wannacry_payload_str3 = "mssecsvc.exe" fullword ascii

    condition:
        all of them
}

```

Esa es una estructura básica de una regla de Yara. Comienza con un`header`que contiene`metadata`, seguido de`conditions`que definen el contexto de los archivos a coincidir y un`body`Eso especifica los patrones o indicadores a encontrar. El uso de metadatos y`tags`Ayuda a organizar y documentar las reglas de manera efectiva.

**Desglose de las reglas de Yara**:

1. **`Rule Header`**: El encabezado de la regla proporciona metadatos e identifica la regla. Normalmente incluye:
    - `Rule name`: Un nombre descriptivo para la regla.
    - `Rule tags`: Etiquetas o etiquetas opcionales para clasificar la regla.
    - `Rule metadata`: Información adicional como autor, descripción y fecha de creación.
    
    **Ejemplo**:
    
    Código:Yara
    
    ```
    rule Ransomware_WannaCry {
        meta:
      ...
    }
    
    ```
    
2. **`Rule Meta`**: La meta sección de la regla permite la definición de metadatos adicionales para la regla. Estos metadatos pueden incluir información sobre el autor, las referencias, la versión, etc. de la regla.
    
    **Ejemplo**:
    
    Código:Yara
    
    ```
    rule Ransomware_WannaCry {
        meta:
            author = "Madhukar Raina"
            version = "1.0"
            description = "Simple rule to detect strings from WannaCry ransomware"
            reference = 	"https://www.virustotal.com/gui/file/ed01ebfbc9eb5bbea545af4d01bf5f1071661840480439c6e5babe8e080e41aa/behavior"
        ...
    }
    
    ```
    
3. **`Rule Body`**: El cuerpo de la regla contiene los patrones o indicadores que coinciden dentro de los archivos. Aquí es donde se define la lógica de detección real.
    
    **Ejemplo**:
    
    Código:Yara
    
    ```
    rule Ransomware_WannaCry {
    
        ...
    
        strings:
            $wannacry_payload_str1 = "tasksche.exe" fullword ascii
            $wannacry_payload_str2 = "www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com" ascii
            $wannacry_payload_str3 = "mssecsvc.exe" fullword ascii
    
        ...
    
    }
    
    ```
    
4. **`Rule Conditions`**: Las condiciones de la regla definen el contexto o las características de los archivos a emparejarse. Las condiciones pueden basarse en propiedades del archivo, cadenas u otros indicadores. Las condiciones se especifican dentro de la sección de condición.
    
    **Ejemplo**:
    
    Código:Yara
    
    ```
    rule Ransomware_WannaCry {
        ...
    
        condition:
            all of them
    }
    
    ```
    
    En esta regla de Yara, la sección de condición simplemente establece`all of them`, lo que significa que todas las cadenas definidas en la regla deben estar presentes para que la regla active una coincidencia.
    
    Proporcionemos un ejemplo más de una condición que especifica que el tamaño del archivo del archivo analizado debe ser menor que`100`Kilobytes (KB).
    
    Código:Yara
    
    ```
        condition:
            filesize < 100KB and (uint16(0) == 0x5A4D or uint16(0) == 0x4D5A)
    
    ```
    
    Esta condición también especifica que los primeros 2 bytes del archivo deben ser`0x5A4D`(Ascii`MZ`) o`0x4D5A`(Ascii`ZM`), usando`uint16(0)`:
    
    Código:Yara
    
    ```
    uint16(offset)
    
    ```
    
    Aquí está como`uint16(0)`obras:
    
    - `uint16`: Esto indica el tipo de datos que se extraerá, que es un`16`Bit Unsigned Integer (`2`bytes).
    - `(0)`: El valor dentro de los paréntesis representa el desplazamiento desde donde debe comenzar la extracción. En este caso,`0`significa que la función extraerá el valor de 16 bits a partir del comienzo de los datos que se están escaneando. La condición utiliza UINT16 (0) para comparar los primeros 2 bytes del archivo con valores específicos.

---

Es importante tener en cuenta que Yara proporciona una sintaxis flexible y extensible, lo que permite características y técnicas más avanzadas como modificadores, operadores lógicos y módulos externos. Estas características pueden mejorar la expresividad y efectividad de las reglas de Yara para escenarios de detección específicos.

Recuerde que las reglas de Yara se pueden personalizar para adaptarse a nuestros casos de uso específicos y necesidades de detección. La práctica y la experimentación regulares mejorarán aún más nuestra comprensión y competencia con la creación de reglas de Yara.[Documentación de Yara](https://yara.readthedocs.io/en/latest/)contener más detalles en profundidad.

[Anterior](https://academy.hackthebox.com/module/234/section/2511)