# IP Source & Destination Spoofing Attacks

Hay muchos casos en los que podríamos ver tráfico irregular para paquetes IPv4 e IPv6. En muchos de estos casos, esto podría hacerse a través de los campos IP de origen y destino. Siempre debemos considerar lo siguiente al analizar estos campos para nuestros esfuerzos de análisis de tráfico.

1. `The Source IP Address should always be from our subnet`Si notamos que un paquete entrante tiene una fuente de IP desde fuera de nuestra red de área local, esto puede ser un indicador de la elaboración de paquetes.
2. `The Source IP for outgoing traffic should always be from our subnet`Si la IP de origen es de un rango IP diferente al de nuestra propia red de área local, este puede ser un indicador del tráfico malicioso que se origina desde el interior de nuestra red.

Un atacante podría realizar estos ataques de fabricación de paquetes hacia las direcciones IP de origen y destino por muchas razones diferentes o resultados deseados. Aquí hay algunos que podemos buscar:

1. `Decoy Scanning`En un intento de evitar las restricciones de firewall, un atacante podría cambiar la IP de origen de los paquetes para enumerar más información sobre un host en otro segmento de red. Al cambiar la fuente a algo dentro de la misma subred que el host de destino, el atacante podría tener éxito en la evasión del firewall.
2. `Random Source Attack DDoS`A través de la creación de una fuente aleatoria, un atacante podría enviar toneladas de tráfico al mismo puerto del anfitrión de la víctima. Esto en muchos casos se utiliza para agotar los recursos de nuestros controles de red o en el host de destino.
3. `LAND Attacks`Los ataques terrestres funcionan de manera similar a los ataques de denegación de servicio de la fuente aleatoria en la naturaleza que la dirección fuente se establece en lo mismo que los hosts de destino. Al hacerlo, el atacante podría agotar los recursos de la red o causar bloqueos en el host objetivo.
4. `SMURF Attacks`Similar a los ataques de fuentes terrestres y aleatorios, los ataques de Pitufo trabajan a través del atacante que envía grandes cantidades de paquetes ICMP a muchos anfitriones diferentes. Sin embargo, en este caso, la dirección de origen está configurada en las máquinas de víctimas, y todos los hosts que reciben este paquete ICMP responden con una respuesta ICMP que causa el agotamiento de los recursos en la dirección de origen diseñada (víctima).
5. `Initialization Vector Generation`En redes inalámbricas más antiguas, como la privacidad equivalente con cable, un atacante puede capturar, descifrar, crear y reinyectar un paquete con una dirección IP de origen y destino modificado para generar vectores de inicialización para construir una tabla de descifrado para un ataque estadístico. Estos se pueden ver en la naturaleza al notar una cantidad excesiva de paquetes repetidos entre los hosts.

Es importante tener en cuenta que, a diferencia del envenenamiento de ARP, los ataques que exploraremos en esta sección derivan de las comunicaciones de capa IP y no necesariamente en el envenenamiento ARP. Sin embargo, estos ataques tienden a llevarse a cabo en conjunto para la mayoría de las actividades nefastas.

---

# **Encontrar intentos de escaneo de señuelo**

**Archivo PCAP relacionado**:

- `decoy_scanning_nmap.pcapng`

En pocas palabras, cuando un atacante quiere recopilar información, podría cambiar su dirección fuente para que sea la misma que otro anfitrión legítimo, o en algunos casos completamente diferente de cualquier anfitrión real. Esto es intentar evadir IDS/controles de firewall, y se puede observar fácilmente.

En el caso del escaneo de señuelo, notaremos algún comportamiento extraño.

1. `Initial Fragmentation from a fake address`
2. `Some TCP traffic from the legitimate source address`

![](https://academy.hackthebox.com/storage/modules/229/1-decoy.png)

En segundo lugar, en este ataque, el atacante podría estar intentando encubrir su dirección con un señuelo, pero las respuestas para múltiples puertos cerrados aún se dirigirán hacia ellos con las primeras banderas denotadas para TCP.

![](https://academy.hackthebox.com/storage/modules/229/2-decoy.png)

Definitivamente lo notaremos en el caso de un gran bloque portuario que no tiene servicios que se ejecuten en el anfitrión de la víctima.

![](https://academy.hackthebox.com/storage/modules/229/3-decoy.png)

Como tal, otra forma simple en que podemos evitar este ataque más allá de simplemente detectarlo a través de nuestros esfuerzos de análisis de tráfico es la siguiente.

1. `Have our IDS/IPS/Firewall act as the destination host would`En el sentido de que la reconstrucción de los paquetes da una clara indicación de actividad maliciosa.
2. `Watch for connections started by one host, and taken over by another`Después de todo, el atacante tiene que revelar su verdadera dirección fuente para ver que un puerto está abierto. Este es un comportamiento extraño y podemos definir nuestras reglas para evitarlo.

# **Encontrar ataques de fuentes aleatorios**

**Archivo PCAP relacionado**:

- `ICMP_rand_source.pcapng`
- `ICMP_rand_source_larg_data.pcapng`
- `TCP_rand_source_attacks.pcapng`

En el lado opuesto de las cosas, podemos comenzar a explorar los ataques de denegación de servicio a través de la suplantación de la fuente de origen y las direcciones de destino. Uno de los ejemplos principales y notables son los ataques de fuentes aleatorias. Estos se pueden realizar en muchos sabores diferentes. Sin embargo, en particular, esto se puede hacer como lo opuesto a un ataque de Pitufo, en el que muchos anfitriones hacer ping a un anfitrión que no existe, y el anfitrión ping -ping retrocederá a todos los demás y no recibirá respuesta.

![](https://academy.hackthebox.com/storage/modules/229/1-random-source.png)

También debemos considerar que los atacantes podrían fragmentar estas comunicaciones de hosts aleatorios para extraer más agotamiento de recursos.

![](https://academy.hackthebox.com/storage/modules/229/2-random-source.png)

Sin embargo, en muchos casos, como los ataques terrestres, los atacantes serán utilizados por los atacantes para agotar los recursos a un servicio específico en un puerto. En lugar de falsificar que la dirección fuente sea la misma que el destino, el atacante podría aleatorizarlos. Podríamos notar lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/3-random-source.png)

En este caso, tenemos algunos indicadores de comportamiento nefasto:

1. `Single Port Utilization from random hosts`
2. `Incremental Base Port with a lack of randomization`
3. `Identical Length Fields`

En muchos casos del mundo real, como un servidor web, podemos tener muchos usuarios diferentes utilizando el mismo puerto. Sin embargo, estas solicitudes son contrarias a nuestros indicadores. De tal manera que tengan diferentes longitudes y los puertos base no exhibirán este comportamiento.

# **Encontrar ataques de Pitufo**

Los ataques de Pitufo son un ataque de denegación de servicio distribuido notable, en la naturaleza que operan, haciendo que los anfitriones aleatorios vuelvan a tomar el anfitrión de la víctima. En pocas palabras, un atacante los lleva a cabo como lo siguiente:

1. `The attacker will send an ICMP request to live hosts with a spoofed address of the victim host`
2. `The live hosts will respond to the legitimate victim host with an ICMP reply`
3. `This may cause resource exhaustion on the victim host`

Una de las cosas que podemos buscar en nuestro comportamiento de tráfico es una cantidad excesiva de respuestas de ICMP de un solo anfitrión a nuestro anfitrión afectado. A veces, los atacantes incluirán fragmentación y datos sobre estas solicitudes de ICMP para que el volumen de tráfico sea más grande.

![](https://academy.hackthebox.com/storage/modules/229/1-SMURF.png)

Podríamos notar muchos anfitriones diferentes que hacen ping a nuestro único anfitrión, y en este caso representa la naturaleza básica de los ataques de Pitufo.

![](https://academy.hackthebox.com/storage/modules/229/smurf.png)

**Image From**: [https://techofide.com/blogs/what-is-smurf-attack-what-is-the-enial-of-service-attack-practical-ddos-attack-step-step-guide/](https://techofide.com/blogs/what-is-smurf-attack-what-is-the-denial-of-service-attack-practical-ddos-attack-step-by-step-guide/)

# **Encontrar ataques terrestres**

**Archivo PCAP relacionado**:

- `LAND-DoS.pcapng`

Los ataques terrestres operan a través de un atacante que falsifica que la dirección IP de origen sea la misma que el destino. Estos ataques de denegación de servicio funcionan a través del gran volumen de tráfico y la reutilización de puertos. Esencialmente, si todos los puertos base están ocupados, hace que las conexiones reales sean mucho más difíciles de establecer para nuestro anfitrión afectado.

![](https://academy.hackthebox.com/storage/modules/229/1-LAND.png)