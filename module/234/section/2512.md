# Reglas de Sigma y Sigma

`Sigma`es un formato de firma genérica utilizado para describir las reglas de detección para el análisis de registro y los sistemas SIEM. Permite a los analistas de SOC crear y compartir reglas que ayuden a identificar patrones o comportamientos específicos que indican amenazas de seguridad o actividades maliciosas. Las reglas de Sigma generalmente se escriben en formato YAML y se pueden usar con varias herramientas y plataformas de seguridad.

Los analistas de SOC utilizan reglas Sigma para definir y detectar eventos de seguridad mediante el análisis de datos de registro generados por varios sistemas, como firewalls, sistemas de detección de intrusos y soluciones de protección de punto final. Estas reglas se pueden personalizar para que coincidan con casos de uso específicos y pueden incluir condiciones, filtros y otros parámetros para determinar cuándo un evento debe activar una alerta.

La principal ventaja de las reglas Sigma es su portabilidad y compatibilidad con múltiples sistemas de análisis de SIEM y log, lo que permite a los analistas escribir reglas una vez y usarlas en diferentes plataformas.

Sigma puede considerarse como formato estandarizado para que los analistas creen y compartan reglas de detección. Ayuda a convertir los COI en consultas y puede integrarse fácilmente con herramientas de seguridad, incluidos SIEM y EDRS. Las reglas de Sigma se pueden usar para detectar actividades sospechosas en varias fuentes de registro. Esto también ayuda a desarrollar procesos eficientes para la detección como código mediante la automatización de la creación y la implementación de reglas de detección.

![](https://academy.hackthebox.com/storage/modules/234/sigma_intro.png)

### **Usos de Sigma**

- **`Universal Log Analytics Tool`**: Podemos escribir reglas de detección una vez y luego convertirlas en varios formatos de herramientas de análisis SIEM y LOG, ahorrándonos la tarea repetitiva de reescribir la lógica en diferentes plataformas.
- **`Community-driven Rule Sharing`**: Con Sigma, tenemos la capacidad de aprovechar una comunidad que regularmente contribuye y comparte sus reglas de detección. Esto asegura que actualicemos y refinemos constantemente nuestros mecanismos de detección.
- **`Incident Response`**: Sigma ayuda en la respuesta a incidentes al permitir a los analistas buscar y analizar rápidamente los registros para patrones o indicadores específicos.
- **`Proactive Threat Hunting`**: Podemos usar reglas Sigma para sesiones de caza de amenazas proactivas. Al aprovechar patrones específicos, podemos revisar nuestros conjuntos de datos para identificar anomalías o signos de actividad adversa.
- **`Seamless Integration with Automation Tools`**: Al convertir las reglas Sigma en formatos apropiados, podemos integrarlas sin problemas con nuestras plataformas SOAR y otras herramientas de automatización, lo que permite respuestas automatizadas basadas en detecciones específicas.
- **`Customization for Specific Environments`**: La flexibilidad de las reglas Sigma significa que podemos adaptarlas de acuerdo con las características únicas de nuestro entorno. Las reglas personalizadas pueden abordar las amenazas o escenarios específicos que nos preocupa.
- **`Gap Identification`**: Al alinear nuestro conjunto de reglas con la comunidad más amplia, podemos realizar un análisis de brechas, identificando áreas donde nuestras capacidades de detección pueden necesitar mejoras.

# **¿Cómo funciona Sigma?**

En el fondo, Sigma se trata de expresar patrones que se encuentran en los eventos log de manera estructurada. Entonces, en lugar de tener una variedad de descripciones de reglas dispersas en varios formatos patentados, con Sigma, tenemos un estándar abierto y unificado. Este formato unificado se convierte en la lengua franca para la detección de amenazas basada en log.

Las reglas de Sigma están escritas en Yaml. Cada regla de Sigma describe un patrón particular de eventos log que podrían correlacionarse con la actividad maliciosa. La regla abarca un título, descripción, fuente de registro y el patrón en sí.

Pero aquí viene la magia. El verdadero poder de Sigma se encuentra en su convertibilidad. Podríamos preguntar: "Si se trata de un formato estándar, ¿cómo lo usamos con nuestras herramientas y plataformas de registro específicas?" Ahí es donde el convertidor sigma (`sigmac`) entran. Este convertidor es la pieza clave de nuestro flujo de trabajo, transformando nuestras reglas Sigma en consultas o configuraciones compatibles con una multitud de SIEM, soluciones de gestión de registros y otras herramientas de análisis de seguridad.

Con`sigmac`, podemos tomar una regla escrita en el formato Sigma y traducirla para Elasticsearch, QRadar, Splunk y muchos más, casi instantáneamente.

**Nota**: [pysigma](https://github.com/SigmaHQ/pySigma)se está convirtiendo cada vez más en la opción de referencia para la traducción de reglas, como`sigmac`ahora se considera obsoleto.

# **Estructura de reglas de sigma**

Como ya se mencionó, los archivos de reglas de Sigma se escriben en formato YAML. A continuación se muestra la estructura de una regla Sigma.

![](https://academy.hackthebox.com/storage/modules/234/sigma_structure.png)

**Fuente**: [https://github.com/sigmahq/sigma/wiki/specification](https://github.com/SigmaHQ/sigma/wiki/Specification)

Comprendamos la estructura de una regla Sigma con un ejemplo.

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
description: Detects potential LethalHTA technique where "mshta.exe" is spawned by an "svchost.exe" process
references:
    - https://codewhitesec.blogspot.com/2018/07/lethalhta.html
author: Markus Neis
date: 2018/06/07
tags:
    - attack.defense_evasion
    - attack.t1218.005
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        ParentImage|endswith: '\svchost.exe'
        Image|endswith: '\mshta.exe'
    condition: selection
falsepositives:
    - Unknown
level: high

```

La siguiente captura de pantalla muestra los diferentes componentes que forman una regla Sigma:

![](https://academy.hackthebox.com/storage/modules/234/sigma_structure_example.png)

**Desglose de reglas de Sigma**(Residencia en[Especificación de Sigma](https://github.com/SigmaHQ/sigma-specification/blob/main/specification/sigma-rules-specification.md)):

- `title`: Un breve título para la regla que debe contener lo que se supone que debe detectar la regla (máx. 256 caracteres)

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
...

```

- `id`: Las reglas de Sigma deben identificarse mediante un identificador único globalmente único en el atributo de identificación. Para este propósito[UUID generados al azar (versión 4)](https://www.uuidgenerator.net/version4)se recomiendan pero no son obligatorios.

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
...

```

- `status`(Opcional): declara el estado de la regla.
    - `stable`: La regla no produjo ningún falso positivo obvio en múltiples entornos durante un largo período de tiempo
    - `test`: La regla no muestra ningún falso positivo obvio en un conjunto limitado de sistemas de prueba
    - `experimental`: Una nueva regla que no se ha probado fuera de los entornos de laboratorio y podría conducir a muchos falsos positivos
    - `deprecated`: La regla es reemplazar o cubrir otro. El enlace entre las reglas se realiza a través del campo relacionado.
    - `unsupported`: La regla no se puede usar en su estado actual (registro de correlación especial, campos caseros, etc.)

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
...

```

- `description`(Opcional): una breve descripción de la regla y la actividad maliciosa que se puede detectar (máx. 65,535 caracteres)

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
description: Detects potential LethalHTA technique where "mshta.exe" is spawned by an "svchost.exe" process
...

```

- `references`(Opcional): citas a la fuente original desde la cual se inspiró la regla. Estos pueden incluir publicaciones de blog, artículos académicos, presentaciones o incluso tweets.

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
description: Detects potential LethalHTA technique where "mshta.exe" is spawned by an "svchost.exe" process
references:
    - https://codewhitesec.blogspot.com/2018/07/lethalhta.html
...

```

- `author`(Opcional): Creador de la regla (puede ser un nombre, apodo, identificador de Twitter, etc.).

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
description: Detects potential LethalHTA technique where "mshta.exe" is spawned by an "svchost.exe" process
references:
    - https://codewhitesec.blogspot.com/2018/07/lethalhta.html
author: Markus Neis
...

```

- `date`(Opcional): Fecha de creación de reglas. Use el formato`YYYY/MM/DD`.

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
description: Detects potential LethalHTA technique where "mshta.exe" is spawned by an "svchost.exe" process
references:
    - https://codewhitesec.blogspot.com/2018/07/lethalhta.html
author: Markus Neis
date: 2018/06/07
...

```

- `logsource`: Esta sección describe los datos de registro en los que se debe aplicar la detección. Describe la fuente de registro, la plataforma, la aplicación y el tipo que se requiere en la detección. Se puede encontrar más información en el siguiente enlace:[https://github.com/sigmahq/sigma/tree/master/documentation/logsource-guides](https://github.com/SigmaHQ/sigma/tree/master/documentation/logsource-guides)
    
    Consiste en tres atributos que los convertidores evalúan automáticamente y un número arbitrario de elementos opcionales. Recomendamos usar un valor de "definición" cuando sea necesaria una explicación adicional.
    
    - `category`: El`category`El valor se usa para seleccionar todos los archivos de registro escritos por un cierto grupo de productos, como firewalls o registros de servidores web. El convertidor automático utilizará la palabra clave como selector para múltiples índices. Ejemplos:`firewall`, `web`, `antivirus`, etc.
    - `product`: El`product`El valor se usa para seleccionar todas las salidas de registro de un determinado producto, por ejemplo, todos los tipos de registro de eventos de Windows, incluidos`Security`, `System`, `Application`y tipos más nuevos como`AppLocker`y`Windows Defender`. Ejemplos:`windows`, `apache`, `check point fw1`, etc.
    - `service`: El`service`El valor se usa para seleccionar solo un subconjunto de los registros de un producto, como el`sshd`en Linux o el`Security`Registro de eventos en los sistemas de Windows. Ejemplos:`sshd`, `applocker`, etc.

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
description: Detects potential LethalHTA technique where "mshta.exe" is spawned by an "svchost.exe" process
references:
    - https://codewhitesec.blogspot.com/2018/07/lethalhta.html
author: Markus Neis
date: 2018/06/07
logsource:
	category: process_creation
	product: windows
...

```

- `detection`: Un conjunto de identificadores de búsqueda que representan propiedades de las búsquedas en los datos de registro. La detección está compuesta por dos componentes:
    - Identificadores de búsqueda
    - Condición
    
    ![](https://academy.hackthebox.com/storage/modules/234/001.jpg)
    
    **Fuente**: [blusapphire.io](https://docs.blusapphire.io/sigma-rules/sigma-detection-attributes)
    

Código:yaml

```yaml
title: Potential LethalHTA Technique Execution
id: ed5d72a6-f8f4-479d-ba79-02f6a80d7471
status: test
description: Detects potential LethalHTA technique where "mshta.exe" is spawned by an "svchost.exe" process
references:
    - https://codewhitesec.blogspot.com/2018/07/lethalhta.html
author: Markus Neis
date: 2018/06/07
logsource:
	category: process_creation
	product: windows
detection:
	selection:
		ParentImage|endswith: '\svchost.exe'
        Image|endswith: '\mshta.exe'
    condition: selection
...

```

---

Los valores contenidos en las reglas Sigma pueden modificarse mediante modificadores de valor. Los modificadores de valor se agregan después del nombre del campo con un carácter de tubería (`|`) como separador y también se puede encadenar, por ejemplo`fieldname|mod1|mod2: value`. Los modificadores de valor se aplican en el orden dado al valor.

El comportamiento de los identificadores de búsqueda se cambia mediante modificadores de valor como se muestra en la tabla a continuación:

| **Modificador de valor** | **Explicación** | **Ejemplo** |
| --- | --- | --- |
| contiene | Agrega comodín (`*`) caracteres alrededor del valor (s) | Línea de comandos | contiene |
| todo | Enlace todos los elementos de una lista con una lógica "y" (en lugar del predeterminado "o") | Línea de comandos | contiene | todos |
| comienzo | Agrega un comodín (`*`) personaje al final del valor del campo | ParentIMage | Inicio con |
| poner fin a | Agrega un comodín (`*`) personaje al comienzo del valor del campo | Imagen | Endswith |
| re: | Este valor se maneja como expresión regular por backends | Línea de comandos | re: '\ [string \] \ s*\ $ verbosepreference |

**Fuente**: [blusapphire.io](https://docs.blusapphire.io/sigma-rules/sigma-detection-attributes)

Los identificadores de búsqueda incluyen múltiples valores en dos estructuras de datos diferentes:

1. `Lists`, que puede contener:
    - `strings`que se aplican al mensaje de registro completo y están vinculados con una lógica`OR`.
    - `maps`(vea abajo). Todos los elementos del mapa de una lista están vinculados con una lógica`OR`.
    
    ![](https://academy.hackthebox.com/storage/modules/234/002.jpg)
    
    **Fuente**: [blusapphire.io](https://docs.blusapphire.io/sigma-rules/sigma-detection-attributes)
    

Lista de ejemplo de cuerdas que coinciden con`evilservice`o`svchost.exe -n evil`.

Código:yaml

```yaml
detection:
 	keywords:
		- evilservice
 		- svchost.exe -n evil

```

Lista de ejemplo de mapas que coinciden en el archivo de imagen`example.exe`o en un ejecutable cuya descripción contiene la cadena`Test executable`.

Código:yaml

```yaml
detection:
	selection:
 		- Image|endswith: '\example.exe'
		- Description|contains: 'Test executable'

```

1. `Maps`: Mapas (o diccionarios) consisten en pares de clave/valor, en el que la clave es un campo en los datos de registro y el valor de una cadena o valor entero. Todos los elementos de un mapa se unen con una lógica`AND`.

Ejemplo que coincida con el registro de eventos`Security`y (`Event ID 517`o`Event ID 1102`)

Código:yaml

```yaml
detection:
    selection:
  	    EventLog: Security
  	    EventID:
  	      - 517
     	  - 1102
	condition: selection

```

Ejemplo que coincida con el registro de eventos`Security`y`Event ID 4679`y`TicketOptions 0x40810000`y`TicketEncryption 0x17`.

Código:yaml

```yaml
detection:
	selection:
		EventLog: Security
		EventID: 4769
		TicketOptions: '0x40810000'
		TicketEncryption: '0x17'
	condition: selection

```

- `Condition`: La condición define cómo los campos están relacionados entre sí. Si hay algo que filtrar, se puede definir en la condición. Utiliza varios operadores para definir relaciones para múltiples campos que se explican a continuación.

| **Operador** | **Ejemplo** |
| --- | --- |
| Lógico y/o | `keywords1 or keywords2` |
| 1/todos ellos | `all of them` |
| 1/todo el patrón de identificación de búsqueda | `all of selection*` |
| 1/todo el patrón de búsqueda de ID | `all of filter_*` |
| Negación con 'no' | `keywords and not filters` |
| Brackets - Orden de operación '()' | `selection1 and (keywords1 or keywords2)` |

**Fuente**: [blusapphire.io](https://docs.blusapphire.io/sigma-rules/sigma-detection-attributes)

Ejemplo de una condición:

Código:yaml

```yaml
condition: selection1 or selection2 or selection3

```

---

# **Las mejores prácticas del desarrollo de reglas de Sigma**

[Repositorio de especificaciones de Sigma](https://github.com/SigmaHQ/sigma-specification/blob/version_2/Sigma_specification.md)Contiene todo lo que pueda necesitar en torno a las reglas de Sigma.

Las mejores prácticas del desarrollo de reglas de Sigma y las trampas comunes se pueden encontrar en[Guía de creación de reglas de Sigma](https://github.com/SigmaHQ/sigma/wiki/Rule-Creation-Guide).

[Anterior](https://academy.hackthebox.com/module/234/section/2575)