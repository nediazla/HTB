# ARP Scanning & Denial-of-Service

Podríamos discernir comportamientos aberrantes adicionales dentro de las solicitudes y respuestas de ARP. Es de conocimiento común que el envenenamiento y la suplantación de suplantación de la mayoría de los ARP.`denial-of-service (DoS)`y`man-in-the-middle (MITM)`ataques. Sin embargo, los adversarios también podrían explotar ARP para la recopilación de información. Afortunadamente, poseemos las habilidades para detectar y evaluar estas tácticas después de procedimientos similares.

# **Señales de escaneo ARP**

**Archivo PCAP relacionado**:

- `ARP_Scan.pcapng`

Algunas banderas rojas típicas indicativas de escaneo ARP son:

1. `Broadcast ARP requests sent to sequential IP addresses (.1,.2,.3,...)`
2. `Broadcast ARP requests sent to non-existent hosts`
3. `Potentially, an unusual volume of ARP traffic originating from a malicious or compromised host`

# **Encontrar escaneo ARP**

Sin demora, si tuviéramos que abrir el archivo de captura de tráfico relacionado (`ARP_Scan.pcapng`) en Wireshark y aplique el filtro`arp.opcode`, podríamos observar lo siguiente:

![](https://academy.hackthebox.com/storage/modules/229/ARP_Scan_1.png)

Es posible detectar que las solicitudes ARP están siendo propagadas por un solo host a todas las direcciones IP de manera secuencial. Este patrón es sintomático del escaneo ARP y es una característica común de escáneres ampliamente utilizados como`Nmap`.

Además, podemos discernir que los anfitriones activos responden a estas solicitudes a través de sus respuestas ARP. Esto podría indicar la ejecución exitosa de la táctica de recopilación de información por parte del atacante.

---

# **Identificar la negación de servicio**

**Archivo PCAP relacionado**:

- `ARP_Poison.pcapng`

Un atacante puede explotar el escaneo ARP para compilar una lista de hosts en vivo. Al adquirir esta lista, el atacante podría alterar su estrategia para negar el servicio a todas estas máquinas. Esencialmente, se esforzarán por contaminar una subred completa y manipularán tantos cachés ARP como sea posible. Esta estrategia también es plausible para un atacante que busca establecer una posición de hombre en el medio.

![](https://academy.hackthebox.com/storage/modules/229/ARP_DoS_1.png)

De inmediato, podríamos tener en cuenta que el tráfico ARP del atacante puede cambiar su enfoque hacia declarar nuevas direcciones físicas para todas las direcciones IP en vivo. La intención aquí es corromper el caché ARP del enrutador.

Por el contrario, podemos presenciar la asignación duplicada de`192.168.10.1`a los dispositivos del cliente. Esto indica que el atacante está intentando corromper el caché ARP de estos dispositivos de víctimas con la intención de obstruir el tráfico en ambas direcciones.

![](https://academy.hackthebox.com/storage/modules/229/ARP_DoS_2.png)

# **Respondiendo a los ataques ARP**

Al identificar cualquiera de estas anomalías relacionadas con ARP, podríamos cuestionar el curso de acción adecuado para contrarrestar estas amenazas. Aquí hay un par de posibilidades:

1. `Tracing and Identification`: En primer lugar, la máquina del atacante es una entidad física ubicada en algún lugar. Si logramos localizarlo, podríamos detener sus actividades. En ocasiones, podríamos descubrir que la máquina que orquesta el ataque está comprometida y bajo control remoto.
2. `Containment`: Para obstaculizar cualquier exfiltración adicional de la información por parte del atacante, podríamos contemplar la desconexión o aislar el área impactada a nivel de interruptor o enrutador. Esta acción podría terminar efectivamente un ataque de DOS o MITM en su fuente.

Los ataques de capa de enlace a menudo vuelan debajo del radar. Si bien pueden parecer insignificantes para identificar e investigar, su detección podría ser fundamental para prevenir la exfiltración de datos de capas superiores del modelo OSI.