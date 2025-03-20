# SIEM Use Case Development

# **¿Qué es un caso de uso SIEM?**

La utilización de casos de uso de SIEM es un aspecto fundamental a la hora de diseñar una estrategia de ciberseguridad sólida, ya que permiten la identificación y detección efectiva de posibles incidentes de seguridad. Los casos de uso están diseñados para ilustrar situaciones específicas en las que se puede aplicar un producto o servicio y pueden variar desde escenarios generales, como intentos fallidos de inicio de sesión, hasta escenarios más complejos, como la detección de un brote de ransomware.

![](https://academy.hackthebox.com/storage/modules/211/usecase1.png)

Por ejemplo, considere una situación en la que un usuario llamado Rob experimenta 10 intentos fallidos de autenticación consecutivos. Estos eventos podrían originarse en el usuario real que olvidó sus credenciales o en un actor malicioso que intenta ingresar por la fuerza bruta en la cuenta. En cualquier caso, estos 10 eventos se envían al sistema SIEM, que luego los correlaciona en un solo evento y activa una alerta al equipo SOC en la categoría de caso de uso de "fuerza bruta".

Según los datos de registro generados dentro del SIEM, el equipo SOC es responsable de tomar las medidas adecuadas. Este ejemplo demuestra sólo uno de los muchos casos de uso posibles que se pueden desarrollar, que van desde situaciones sencillas hasta situaciones más complejas.

---

# **Ciclo de vida de desarrollo de casos de uso de SIEM**

Se deben considerar las siguientes etapas críticas al desarrollar cualquier caso de uso:

![](https://academy.hackthebox.com/storage/modules/211/usecase2.png)

1. `Requirements`: Comprender el propósito o necesidad del caso de uso, señalando el escenario específico para el cual se necesita una alerta o notificación. Los requisitos pueden ser propuestos por clientes, analistas o empleados. Por ejemplo, el objetivo podría ser diseñar un caso de uso de detección para un ataque de fuerza bruta que active una alerta después de 10 errores de inicio de sesión consecutivos en 4 minutos.
2. `Data Points`: Identifique todos los puntos de datos dentro de la red donde se puede usar una cuenta de usuario para iniciar sesión. Recopile información sobre las fuentes de datos que generan registros para intentos de acceso no autorizados o fallas de inicio de sesión. Por ejemplo, los datos pueden provenir de máquinas Windows, máquinas Linux, puntos finales, servidores o aplicaciones. Asegúrese de que los registros capturen detalles esenciales como usuario, marca de tiempo, origen, destino, etc.
3. `Log Validation`: Verifique y valide los registros, asegurándose de que contengan toda la información crucial, como usuario, marca de tiempo, origen, destino, nombre de la máquina y nombre de la aplicación. Confirme que se reciban todos los registros durante varios eventos de autenticación de usuario para puntos de datos críticos, incluida la autenticación local, basada en web, de aplicaciones, VPN y OWA (Outlook).
4. `Design and Implementation`: Después de identificar y verificar todos los registros con diferentes puntos de datos y fuentes, comience a diseñar el caso de uso definiendo las condiciones bajo las cuales se debe activar una alerta. Considere tres parámetros principales: condición, agregación y prioridad. Por ejemplo, en un caso de uso de ataque de fuerza bruta, cree una alerta para 10 errores de inicio de sesión en 4 minutos mientras considera la agregación para evitar falsos positivos y establece la prioridad de las alertas en función de los privilegios del usuario objetivo.
5. `Documentation`: Los procedimientos operativos estándar (SOP) detallan los procesos estándar que los analistas deben seguir cuando trabajan en alertas. Esto incluye condiciones, agregaciones, prioridades e información sobre otros equipos a los que los analistas deben informar actividades. El SOP también contiene la matriz de escalamiento.
6. `Onboarding`: Comience con la etapa de desarrollo antes de mover la alerta directamente al entorno de producción. Identifique y aborde cualquier brecha para reducir los falsos positivos y luego continúe con la producción.
7. `Periodic Update/Fine-tuning`: Obtenga comentarios periódicos de los analistas y mantenga reglas de correlación actualizadas mediante la inclusión en la lista blanca. Refine y optimice continuamente el caso de uso para garantizar su eficacia y precisión.

---

# **Cómo crear casos de uso de SIEM**

- Comprenda sus necesidades, riesgos y establezca alertas para monitorear todos los sistemas necesarios en consecuencia.
- Determine la prioridad y el impacto, luego asigne la alerta a la cadena de eliminación o al marco MITRE.
- Establezca el Tiempo de Detección (TTD) y el Tiempo de Respuesta (TTR) de la alerta para evaluar la efectividad del SIEM y el desempeño de los analistas.
- Cree un procedimiento operativo estándar (SOP) para gestionar alertas.
- Describa el proceso para refinar las alertas basadas en el monitoreo SIEM.
- Desarrollar un Plan de Respuesta a Incidentes (IRP) para abordar incidentes verdaderamente positivos.
- Establezca acuerdos de nivel de servicio (SLA) y acuerdos de nivel operativo (OLA) entre equipos para manejar alertas y seguir el IRP.
- Implementar y mantener un proceso de auditoría para la gestión de alertas y reportes de incidentes por parte de los analistas.
- Cree documentación para revisar el estado de registro de máquinas o sistemas, la base para crear alertas y su frecuencia de activación.
- Establecer un documento de base de conocimientos para obtener información esencial y actualizaciones de las herramientas de gestión de casos.

---

# **Ejemplo 1 (motor de compilación de Microsoft iniciado por una aplicación de Office)**

Ahora, exploremos un ejemplo práctico utilizando la pila Elastic como solución SIEM para ayudar a comprender cómo asignar cada uno de los puntos anteriores.

![](https://academy.hackthebox.com/storage/modules/211/us1.png)

En la instantánea proporcionada (caso de uso de detección), debemos determinar nuestro riesgo y el objetivo de nuestros esfuerzos de monitoreo.

MSBuild, parte de Microsoft Build Engine, es un sistema de compilación de software que ensambla aplicaciones según su archivo de entrada XML. Normalmente, Microsoft Visual Studio genera el archivo de entrada, pero .NET framework y otros compiladores también pueden compilar aplicaciones sin él. Atacantes[explotar MSBuild](https://blog.talosintelligence.com/building-bypass-with-msbuild/)La capacidad de incluir código fuente malicioso dentro de su configuración o archivo de proyecto.

Al monitorear los argumentos de la línea de comandos de ejecución del proceso, es crucial investigar los casos en los que un navegador web o un ejecutable de Microsoft Office inicia MSBuild. Este comportamiento sospechoso sugiere una posible infracción. Una vez que se establece una línea de base, las llamadas inusuales a MSBuild deberían ser fácilmente identificables y relativamente raras, lo que evitará una mayor carga de trabajo para el equipo.

Para abordar este riesgo, creamos un caso de uso de detección en nuestra solución SIEM que monitorea instancias de MSBuild iniciadas por Excel o Word, ya que este comportamiento podría indicar la ejecución de una carga útil de script malicioso.

A continuación, definamos la prioridad, el impacto y asignemos la alerta a la cadena de eliminación o al marco MITRE.

Dada la inteligencia de riesgos y amenazas mencionada anteriormente, esta técnica, conocida como binarios Living-off-the-land ([LoLBin](https://www.cynet.com/attack-techniques-hands-on/what-are-lolbins-and-how-do-attackers-use-them-in-fileless-attacks)), representa una amenaza significativa si se detecta, lo que la convierte en una categoría de alto riesgo global. En consecuencia, le asignamos una gravedad ALTA, aunque esto puede variar según el contexto y el panorama específicos de su organización.

Con respecto al mapeo MITRE, este caso de uso implica eludir las técnicas de detección mediante el uso de LoLBins, lo que se incluye en la Evasión de Defensa ([TA0005](https://attack.mitre.org/tactics/TA0005/)) táctica, la ejecución de proxy de Trusted Developer Utilities ([T1127](https://attack.mitre.org/techniques/T1127/)) técnica y la ejecución de proxy de Trusted Developer Utilities: MSBuild ([T1127.001](https://attack.mitre.org/techniques/T1127/001/)) subtécnica. Además, la ejecución del binario de MSBuild en el punto final también se incluye en la Ejecución ([TA0002](https://attack.mitre.org/tactics/TA0002/)) táctica.

Para definir TTD y TTR, debemos centrarnos en el intervalo de ejecución de la regla y el proceso de ingesta de datos analizado anteriormente. Para este ejemplo, configuramos la regla para que se ejecute cada cinco minutos, monitoreando todos los registros entrantes.

Al crear un SOP y documentar el manejo de alertas, considere lo siguiente:

- nombre.proceso
- proceso.padre.nombre
- evento.acción
- Máquina donde se detectó la alerta.
- usuario asociado a la máquina
- actividad del usuario dentro de +/- 2 días de la generación de la alerta
- Después de recopilar esta información, los defensores deben interactuar con el usuario y examinar su máquina para analizar los registros del sistema, los registros del antivirus y los registros del proxy del SIEM para obtener una visibilidad completa.

El equipo SOC debe documentar todos los puntos anteriores, junto con el Plan de respuesta a incidentes, para que los manejadores de incidentes puedan hacer referencia a ellos durante el análisis.

Para ajustar las reglas, es esencial comprender las condiciones que pueden desencadenar falsos positivos. Por ejemplo, si bien Build Engine es común entre los desarrolladores de Windows, su uso por parte de personas que no son ingenieros es inusual. Excluir de la regla nombres legítimos de procesos principales ayuda a evitar falsos positivos. Más adelante se proporcionarán más detalles sobre el ajuste de las reglas SIEM.

---

# **Ejemplo 2 (MSBuild realizando conexiones de red)**

En el ejemplo 1 se analizó una regla y un caso de uso de detección de alta gravedad. Ahora, examinemos un caso de uso de gravedad media utilizando una solución SIEM para comprender mejor cómo cada puntero contribuye a la efectividad de los casos de uso.

![](https://academy.hackthebox.com/storage/modules/211/us2.png)

En la instantánea proporcionada, debemos determinar nuestro riesgo y qué estamos tratando de monitorear.

Como en el ejemplo 1, nos centramos nuevamente en el binario MsBuild.exe. Sin embargo, esta vez, consideramos el escenario en el que una máquina intenta una comunicación saliente con una dirección IP remota o potencialmente maliciosa, y el proceso detrás de esa conexión es MsBuild.exe. Esto daría la alarma, ya que puede indicar actividad adversa. Los adversarios suelen aprovechar MsBuild para ejecutar código y evadir la detección.

Para abordar este riesgo, necesitamos una solución de monitoreo capaz de detectar casos en los que MsBuild es responsable de conexiones salientes maliciosas. Creamos un caso de uso de detección en nuestra solución SIEM para este propósito.

A continuación, definamos la prioridad, el impacto y asignemos la alerta a la cadena de eliminación o al marco MITRE.

A diferencia del ejemplo anterior, esta situación podría ocurrir siempre que MsBuild.exe establezca una conexión saliente. También es posible que este proceso se conecte a una dirección IP legítima, como una IP de Microsoft para actualizaciones. Por lo tanto, podríamos encontrar más falsos positivos a menos que implementemos un proceso sólido de inteligencia de amenazas. En consecuencia, deberíamos asignar a esta regla de detección una gravedad MEDIA en lugar de ALTA.

Como en el Ejemplo 1, para eliminar esta amenaza en particular es necesario que los atacantes ejecuten el binario MsBuild en el endpoint, lo que se incluye en la táctica de Ejecución (TA0002).

La mayoría de los demás consejos siguen siendo los mismos, pero el SOP y el Plan de respuesta a incidentes diferirán al manejar este tipo específico de alerta. Los defensores deberán centrarse en event.action, dirección IP y reputación de la IP, entre otros factores.

[Anterior](https://academy.hackthebox.com/module/211/section/2252)