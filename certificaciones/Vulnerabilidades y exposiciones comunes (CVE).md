# Vulnerabilidades y exposiciones comunes (CVE)

# **Lenguaje de evaluación de vulnerabilidad abierta (Oval)**

[Lenguaje de evaluación de vulnerabilidad abierta (Oval)](https://oval.mitre.org/)es un estándar internacional de seguridad de la información disponible públicamente utilizado para evaluar y detallar el estado actual y los problemas del sistema. Oval también es co-soportado por la Oficina de Ciberseguridad y Comunicaciones del Departamento de Seguridad Nacional de los Estados Unidos. Oval proporciona un idioma para comprender los atributos del sistema de codificación y varios repositorios de contenido compartidos dentro de la comunidad de seguridad. El repositorio ovalado tiene más de 7000 definiciones para uso público. Además, Oval también es utilizado por el Protocolo de automatización de contenido de seguridad (SCAP) del Instituto Nacional de Estándares y Tecnología de EE. UU. Que reúne ideas comunitarias para automatizar la gestión de vulnerabilidades, la medición y la garantía de los sistemas cumplen con el cumplimiento de las políticas.

### **Proceso ovalado**

![](https://academy.hackthebox.com/storage/modules/108/graphics/VulnerabilityAssessment_Diagram_05.png)

*Adaptado del gráfico original encontrado[aquí](https://oval.mitre.org/documents/docs-05/extras/0505Martin_f3.gif).*

El objetivo del lenguaje ovalado es tener una estructura de tres pasos durante el proceso de evaluación que consiste en:

- Identificar las configuraciones de un sistema para las pruebas
- Evaluación del estado del sistema actual
- Revelar la información en un informe

La información se puede describir en varios tipos de estados, incluidos:`Vulnerable`, `Non-compliant`, `Installed Asset`, y`Patched`.

### **Definiciones ovales**

Las definiciones ovales se registran en un formato XML para descubrir cualquier vulnerabilidad de software, configuraciones erróneas, programas e información adicional del sistema que toman la necesidad de explotar un sistema. Al tener la capacidad de identificar problemas sin explotar directamente el problema, una organización puede correlacionar qué sistemas deben parcharse en una red.

Las cuatro clases principales de definiciones ovales consisten en:

- `OVAL Vulnerability Definitions`: Identifica las vulnerabilidades del sistema
- `OVAL Compliance Definitions`: Identifica si las configuraciones actuales del sistema cumplen con los requisitos de política del sistema
- `OVAL Inventory Definitions`: Evalúa un sistema para ver si hay un software específico
- `OVAL Patch Definitions`: Identifica si un sistema tiene el parche apropiado

Además, el`OVAL ID Format`Consiste en un formato único que consiste en "Oval: Nombre de dominio de la organización: Tipo de identificación: Valor de identificación". El`ID Type`puede caer en varias categorías que incluyen: Definición (`def`), objeto (`obj`), estado (`ste`) y variable (`var`). Un ejemplo de un identificador único sería`oval:org.mitre.oval:obj:1116`.

Los escáneres como Nessus tienen la capacidad de usar Oval para configurar las plantillas de escaneo de cumplimiento de seguridad.

---

# **Vulnerabilidades y exposiciones comunes (CVE)**

[Vulnerabilidades y exposiciones comunes (CVE)](https://cve.mitre.org/)Es un catálogo de problemas de seguridad disponible públicamente patrocinado por el Departamento de Seguridad Nacional de los Estados Unidos (DHS). Cada problema de seguridad tiene un número de identificación CVE único asignado por la Autoridad de Numeración de CVE (CNA). El propósito de crear un número de ID de CVE único es crear una estandarización para una vulnerabilidad o exposición, ya que un investigador lo identifica. Un CVE consiste en información crítica sobre una vulnerabilidad o exposición, incluida una descripción y referencias sobre el problema. La información en una CVE permite al equipo de TI de una organización comprender cuán perjudicial podría ser un problema para su entorno.

El siguiente cuadro explica cómo se puede asignar una ID de CVE a una vulnerabilidad. Cualquier vulnerabilidad asignada una CVE debe ser independientemente fijable, afectar solo una base de código y ser reconocido y documentado por el proveedor relevante.

![](https://academy.hackthebox.com/storage/modules/108/cve/VulnerabilityAssessment_Diagram_01.png)

*Adaptado del gráfico original[aquí](https://www.balbix.com/app/uploads/what-is-a-CVE-1024x655.png).*

---

# **Etapas para obtener una cVE**

### **Etapa 1: Identifique si se requiere CVE y relevante**

Identifique si el problema encontrado es una vulnerabilidad. Según el equipo de CVE, "un código se indica una vulnerabilidad en el contexto del programa CVE que puede explotarse, lo que resulta en un impacto negativo para la confidencialidad, la integridad o la disponibilidad, y que requiere un cambio de codificación, cambio de especificación o deprecación de especificaciones para mitigar o abordar". Además, la investigación debe verificar que ya no hay una ID de CVE en la base de datos CVE.

### **Etapa 2: Comuníquese con el proveedor de productos afectado**

Un investigador debe asegurarse de haber hecho un esfuerzo de buena fe para contactar a un proveedor directamente. Los investigadores pueden hacer referencia a CVE[Documentos sobre prácticas de divulgación](https://cve.mitre.org/cve/researcher_reservation_guidelines#appendix#a)Para información adicional.

### **Etapa 3: Identifique si la solicitud debe ser para el proveedor CNA o de terceros CNA**

Si una empresa es parte de la participación de CNA, puede asignar una identificación de CVE para uno de sus productos. Si el problema es para un CNA participante, los investigadores pueden contactar a la organización CNA apropiada[aquí](https://cve.mitre.org/cve/request_id.html). Si el proveedor no es un CNA participante, un investigador debe intentar comunicarse con el coordinador de terceros del proveedor.

### **Etapa 4: Solicitud de ID de CVE a través del formulario web CVE**

El equipo CVE tiene un formulario que se puede completar en línea[aquí](https://cveform.mitre.org/)Si los métodos anteriores no funcionan para las solicitudes de CVE.

### **Etapa 5: Confirmación del formulario CVE**

Al enviar el formulario web CVE mencionado en la Etapa 4, un individuo recibirá un correo electrónico de confirmación. El equipo de CVE se comunicará con el solicitante si se requiere información adicional.

### **Etapa 6: Recepción de ID de CVE**

Tras la aprobación, el equipo CVE notificará al solicitante de una ID de CVE si se confirma la vulnerabilidad del producto afectado. Tenga en cuenta que la ID de CVE aún no es pública en esta etapa.

### **Etapa 7: Divulgación pública de ID de CVE**

Las ID de CVE se pueden anunciar al público tan pronto como los proveedores y las partes apropiados conozcan el problema para evitar la duplicación de las ID de CVE. Esta etapa asegura que todas las partes asociadas conozcan el problema antes de ser revelado públicamente.

### **Etapa 8: Anunciando la CVE**

El equipo de CVE solicita a los investigadores que comparten múltiples CVE que se aseguren de que cada CVE indique las diferentes vulnerabilidades. Se puede encontrar información adicional[aquí](https://cve.mitre.org/cve/researcher_reservation_guidelines).

### **Etapa 9: Proporcionar información al equipo CVE**

En esta etapa, el equipo de CVE solicita que el investigador ayude a proporcionar información adicional que se utilizará en la lista oficial de CVE en el sitio web. El[Base de datos de vulnerabilidad nacional de EE. UU. (NVD)](https://nvd.nist.gov/)Mantiene esta información en línea en su base de datos también.

---

# **Divulgación responsable**

Los investigadores y consultores de seguridad hacen referencia constantemente a la base de datos CVE, ya que consta de miles de vulnerabilidades que podrían aprovecharse para la explotación. Además, también hay momentos en que las personas pueden encontrarse con un problema que nunca han visto en la naturaleza o nunca ha revelado al profundizar en un software o programa específico.

La divulgación responsable es esencial en la comunidad de seguridad porque permite que una organización o investigador trabaje directamente con un proveedor que les proporciona los detalles del problema primero para garantizar que un parche esté disponible antes del anuncio de vulnerabilidad al mundo. Si un problema no se revela de manera responsable a un proveedor, los actores de amenaza real pueden aprovechar los problemas de uso criminal, también conocido como un`zero day`o un`0-day`.

---

# **Ejemplos**

### **CVE-2020-5902**

[CVE-2020-5902](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-5902)es una vulnerabilidad de ejecución de código remota no autenticada en la interfaz de usuario de administración de tráfico Big-IP (TMUI). El problema es explotable cuando TMUI está disponible a través del puerto de administración Big-IP y conduce a una adquisición completa del sistema, ya que un atacante podría ejecutar código, editar archivos y habilitar o deshabilitar servicios en el host remoto.

### **CVE-2021-34527**

[CVE-2021-34527](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-34527), también conocido como printnightmare, es una vulnerabilidad de ejecución de código remoto en el servicio de bote de impresión de Windows. El servicio de bote de impresión de Windows se puede abusar debido al servicio que maneja incorrectamente las operaciones de archivo de privilegios. El problema requiere que un usuario sea autenticado, pero permite la adquisición completa de un sistema desde la ejecución de código remoto o local. El problema es extremadamente peligroso, ya que permite que un atacante controle completamente un dominio, ya que explota los servidores (incluidos los controladores de dominio) y las estaciones de trabajo.

---

# **Conseguir práctico**

Ahora que hemos definido términos clave, discutimos los tipos de evaluación, la puntuación de vulnerabilidad y la divulgación, pasemos a familiarizarnos con dos herramientas populares de escaneo de vulnerabilidad: Nessus y OpenVas.