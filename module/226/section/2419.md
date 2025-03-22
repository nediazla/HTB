# Introduction To IDS/IPS

En las operaciones de monitoreo de seguridad de red (NSM), el uso de`Intrusion Detection Systems (IDS)`y`Intrusion Prevention Systems (IPS)`es primordial. El propósito de estos sistemas no es solo identificar posibles amenazas sino también mitigar su impacto.

Un`IDS`es un dispositivo o aplicación que monitorea las actividades de red o sistema para actividades maliciosas o violaciones de políticas y produce informes principalmente a una estación de gestión. Tal sistema nos da una idea clara de lo que está sucediendo dentro de nuestra red, asegurando que tengamos visibilidad en cualquier acción potencialmente dañina. Cabe señalar que un IDS no evita una intrusión, sino que nos alerta cuando se produce uno.

- El IDS opera en dos modos principales: detección basada en la firma y detección basada en anomalías. En`signature-based detection`, El IDS reconoce patrones malos, como firmas de malware y patrones de ataque previamente identificados. Sin embargo, este método se limita solo a amenazas conocidas. Por esta razón, también implementamos`anomaly-based detection`, que establece una línea de base del comportamiento normal y envía una alerta cuando detecta el comportamiento que se desvía de esta línea de base. Es un enfoque más proactivo, pero es susceptible a falsos positivos, de ahí que usamos ambos métodos para equilibrarse entre sí.

Por otro lado, un`IPS`Se sienta directamente detrás del firewall y proporciona una capa adicional de protección. No solo monitorea pasivamente el tráfico de la red, sino que evita activamente cualquier amenaza potencial detectada. Tal sistema no solo nos alerta de intrusos, sino que también les impide que ingresen.

- Un IPS también opera en un modo similar al IDS, que ofrece detección basada en la firma y basada en anomalías. Una vez que se detecta una amenaza potencial, se requieren acciones como dejar paquetes maliciosos, bloquear el tráfico de la red y restablecer la conexión. El objetivo es interrumpir o detener las actividades que se consideran peligrosas para nuestra red o en contra de nuestra política.

Al implementar IDS e IP, generalmente se integran en la red en diferentes puntos, cada uno con su lugar óptimo dependiendo de su función y el diseño general de la red. Los dispositivos IDS e IPS generalmente se colocan detrás del firewall, más cerca de los recursos que protegen. A medida que ambos funcionan examinando el tráfico de la red, tiene sentido colocarlos donde puedan ver la mayor cantidad posible del tráfico relevante, que generalmente se encuentra en el lado interno de la red del firewall.

- Los sistemas de detección de intrusos (IDS) monitorean pasivamente el tráfico de red, detectan posibles amenazas y generan alertas. Al colocarlos detrás del firewall, podemos asegurarnos de que analicen el tráfico que ya ha pasado la primera línea de defensa, lo que nos permite centrarnos en amenazas potencialmente más sutiles o complejas que han pasado por alto el firewall.
- Los sistemas de prevención de intrusos (IPS), por otro lado, intervienen activamente para detener las amenazas detectadas. Esto significa que deben colocarse en un punto de la red donde no solo pueden ver el tráfico potencialmente malicioso, sino que también tienen la autoridad para detenerlo. Esto generalmente se logra colocándolos en línea en la red, a menudo directamente detrás del firewall.

La implementación puede variar según los requisitos específicos de la red y el tipo de tráfico que necesitamos monitorear. IDS/IPS también se pueden implementar en el nivel de host, conocidos como sistemas de detección de intrusos basados ​​en host (HID) y sistemas de prevención de intrusos basados ​​en host (HIPS), que monitorean el tráfico entrante y saliente del host individual para cualquier actividad sospechosa.

Tenga en cuenta que la colocación de estos sistemas es una parte integral de una estrategia de defensa en profundidad, donde se utilizan múltiples capas de medidas de seguridad para proteger la red. La arquitectura exacta dependerá de varios factores, incluida la naturaleza de la red, la sensibilidad de los datos y el panorama de amenazas.

# **Actualizaciones de IDS/IPS**

Además, para garantizar que estos sistemas funcionen en su mejor momento, los actualizamos constantemente con las últimas firmas de amenazas y ajustamos sus algoritmos de detección de anomalías. Esto requiere un esfuerzo diligente y continuo de nuestro equipo de seguridad, pero es absolutamente esencial dado el panorama de amenazas en evolución continuamente en evolución.

También es importante mencionar el papel de los sistemas de información de seguridad y gestión de eventos (SIEM) en nuestras operaciones de monitoreo de seguridad de red. Al recopilar y agregar registros de ID e IPS junto con otros dispositivos en nuestra red, podemos correlacionar los eventos (analizar las relaciones) de diferentes fuentes y usar análisis avanzados para detectar ataques complejos y coordinados. De esta manera, tenemos una visión completa y unificada de la seguridad de nuestra red, lo que nos permite responder rápidamente a las amenazas.

# **Venir**

En este módulo, exploraremos los fundamentos de`Suricata`, `Snort`, y`Zeek`, junto con proporcionar información sobre el desarrollo de la firma y la detección de intrusiones para cada uno de estos sistemas.