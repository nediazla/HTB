# ARP Spoofing & Abnormality Detection

**Archivo PCAP relacionado**:

- `ARP_Spoof.pcapng`

---

El`Address Resolution Protocol (ARP)`ha sido una utilidad de larga data explotada por los atacantes para lanzar ataques de hombre en el medio y de denegación de servicio, entre otros. Dada esta prevalencia, ARP forma un punto focal cuando realizamos un análisis de tráfico, siendo a menudo el primer protocolo que analizamos. Muchos ataques basados ​​en ARP se transmiten, no se dirigen específicamente a los hosts, lo que los hace más detectables a través de nuestras técnicas de olfateo de paquetes.

# **Cómo funciona el protocolo de resolución de direcciones**

Antes de identificar las anomalías de ARP, primero debemos comprender cómo funciona este protocolo en su operación estándar o "vainilla".

![](https://academy.hackthebox.com/storage/modules/229/ARP-protocol.png)

En nuestra red, los hosts deben conocer la dirección física (dirección MAC) a la que deben enviar sus datos. Esta necesidad dio a luz a ARP. Averigamos esto con un proceso paso a paso.

| **Paso** | **Descripción** |
| --- | --- |
| `1` | Imagine que nuestra primera computadora, u host A, necesita enviar datos a nuestra segunda computadora, Host B. Para lograr una transmisión exitosa, el host A debe determinar la dirección física del host B. |
| `2` | El anfitrión A comienza consultando su lista de direcciones conocidas, el caché ARP, para verificar si ya posee esta dirección física. |
| `3` | En el caso de que la dirección correspondiente a la IP deseada no esté en el caché ARP, aloja una solicitud de ARP a todas las máquinas en la subred, preguntando: "¿Quién contiene la IP xxxx?" |
| `4` | El host B responde a este mensaje con una respuesta ARP: "Hola, Host A, mi IP es xxxx y se asigna a la dirección MAC aa: aa: aa: aa: aa: aa". |
| `5` | Al recibir esta respuesta, aloje A actualiza su caché ARP con el nuevo mapeo IP-to-MAC. |
| `6` | Ocasionalmente, un host puede instalar una nueva interfaz, o la dirección IP previamente asignada al host puede expirar, lo que requiere una actualización y reasignación del caché ARP. Dichas instancias podrían introducir complicaciones cuando analizamos nuestro tráfico de red. |

---

# **Envenenamiento y suplantación de ARP**

En un escenario ideal, los controles robustos estarían en su lugar para frustrar estos ataques, pero en realidad, esto no siempre es factible. Para comprender nuestros indicadores de compromiso (COI) de manera más efectiva, profundicemos en el comportamiento de los ataques de envenenamiento y suplantación de ARP.

![](https://academy.hackthebox.com/storage/modules/229/ARP-spoofing-poisoning.png)

Detectar estos ataques puede ser un desafío, ya que imitan la estructura de comunicación del tráfico ARP estándar. Sin embargo, ciertas solicitudes y respuestas de ARP pueden revelar su naturaleza nefasta. Ilustramos cómo funcionan estos ataques, lo que nos permite identificarlos mejor durante nuestro análisis de tráfico.

| **Paso** | **Descripción** |
| --- | --- |
| `1` | Considere una red con tres máquinas: la computadora de la víctima, el enrutador y la máquina del atacante. |
| `2` | El atacante inicia su esquema de envenenamiento de caché ARP enviando mensajes ARP falsificados tanto a la computadora de la víctima como al enrutador. |
| `3` | El mensaje a la computadora de la víctima afirma que la dirección IP de la puerta de enlace (enrutador) corresponde a la dirección física de la máquina del atacante. |
| `4` | Por el contrario, el mensaje del enrutador afirma que la dirección IP de la máquina de la víctima se asigna a la dirección física de la máquina del atacante. |
| `5` | Al ejecutar con éxito estas solicitudes, el atacante puede corromper el caché ARP tanto en la máquina de la víctima como en el enrutador, lo que hace que todos los datos sean mal dirigidos a la máquina del atacante. |
| `6` | Si el atacante configura el reenvío del tráfico, puede aumentar la situación de una denegación de servicio a un ataque de hombre en el medio. |
| `7` | Al examinar otras capas de nuestro modelo de red, podríamos descubrir ataques adicionales. El atacante podría realizar la falsificación de DNS para redirigir las solicitudes web a un sitio falso o realizar la extracción de SSL para intentar la intercepción de datos confidenciales en tránsito. |

Detectar estos ataques es un aspecto, pero evitarlos es un desafío completamente diferente. Podríamos defender estos ataques con controles como:

1. `Static ARP Entries`: Al no permitir reescrituras fáciles y envenenamiento del caché ARP, podemos obstaculizar estos ataques. Esto, sin embargo, requiere un mayor mantenimiento y supervisión en nuestro entorno de red.
2. `Switch and Router Port Security`: La implementación de controles de perfil de red y otras medidas pueden garantizar que solo los dispositivos autorizados puedan conectarse a puertos específicos en nuestros dispositivos de red, bloqueando efectivamente las máquinas que intentan suplantación/envenenamiento ARP.

---

# **Instalación y arranque tcpdump**

Para capturar este tráfico de manera efectiva, especialmente en ausencia de software de monitoreo de red configurado, podemos emplear herramientas como`tcpdump`y`Wireshark`, o simplemente`Wireshark`para hosts de Windows.

Normalmente podemos encontrar`tcpdump`ubicado en`/usr/sbin/tcpdump`. Sin embargo, si la herramienta no está instalada, se puede instalar utilizando el comando apropiado, que se proporcionará en función de los requisitos específicos del sistema.

### **Tcpdump**

Detección de suplantación de ARP y anormalidad

```
xnoxos@htb[/htb]$ sudo apt install tcpdump -y
```

Para iniciar la captura de tráfico, podemos emplear la herramienta de línea de comandos`tcpdump`, especificando nuestra interfaz de red con el`-i`cambiar y dictar el nombre del archivo de captura de salida utilizando el`-w`cambiar.

Detección de suplantación de ARP y anormalidad

```
xnoxos@htb[/htb]$ sudo tcpdump -i eth0 -w filename.pcapng
```

# **Encontrar la falsificación de ARP**

Para detectar ataques de suplantación de ARP, necesitaremos abrir el archivo de captura de tráfico relacionado (`ARP_Spoof.pcapng`) de los recursos de este módulo utilizando Wireshark.

Detección de suplantación de ARP y anormalidad

```
xnoxos@htb[/htb]$ wireshark ARP_Spoof.pcapng
```

Una vez que hemos navegado a Wireshark, podemos optimizar nuestra opinión para centrarnos únicamente en las solicitudes y respuestas de ARP empleando el filtro`arp.opcode`.

![](https://academy.hackthebox.com/storage/modules/229/ARP_Spoof_1.png)

Una bandera roja clave que necesitamos monitorear es cualquier anomalía en el tráfico que emana de un host específico. Por ejemplo, un anfitrión que transmite incesantemente las solicitudes de ARP y las respuestas a otro anfitrión podría ser un signo revelador de suplantación de ARP.

En tal escenario, podríamos identificar que la dirección MAC`08:00:27:53:0C:BA is behaving suspiciously`.

Para determinar esto, podemos ajustar nuestro análisis para inspeccionar únicamente las interacciones, tanto las solicitudes como las respuestas, entre la máquina del atacante, la máquina de la víctima y el enrutador. La funcionalidad de código de operación en`Wireshark`puede simplificar este proceso.

1. `Opcode == 1`: Esto representa todos los tipos de solicitudes ARP
2. `Opcode == 2`: Esto significa todo tipo de respuestas ARP

Como paso preliminar, podríamos analizar las solicitudes enviadas con el siguiente filtro.

- `arp.opcode == 1`

![](https://academy.hackthebox.com/storage/modules/229/ARP_Spoof_2.png)

Casi al instante, deberíamos notar una bandera roja: una duplicación de direcciones, acompañada de un mensaje de advertencia. Si profundizamos en los detalles del mensaje de error dentro de Wireshark, deberíamos poder extraer información adicional.

![](https://academy.hackthebox.com/storage/modules/229/ARP_Spoof_3.png)

Tras la inspección inmediata, podríamos discernir que una dirección IP se asigna a dos direcciones MAC diferentes. Podemos validar esto en un sistema Linux ejecutando los comandos apropiados.

### **Arp**

Detección de suplantación de ARP y anormalidad

```
xnoxos@htb[/htb]$ arp -a | grep 50:eb:f6:ec:0e:7f? (192.168.10.4) at 50:eb:f6:ec:0e:7f [ether] on eth0

```

Detección de suplantación de ARP y anormalidad

```
xnoxos@htb[/htb]$ arp -a | grep 08:00:27:53:0c:ba? (192.168.10.4) at 08:00:27:53:0c:ba [ether] on eth0

```

En esta situación, podríamos identificar que nuestro caché ARP, de hecho, contiene ambas direcciones MAC asignadas a la misma dirección IP, una anomalía que garantiza nuestra atención inmediata.

Para analizar más registros duplicados, podemos utilizar el filtro de Wireshark posterior.

- `arp.duplicate-address-detected && arp.opcode == 2`

---

# **Identificación de las direcciones IP originales**

Una pregunta crucial que necesitamos plantear es, ¿cuáles fueron las direcciones IP iniciales de estos dispositivos? Comprender esto nos ayuda a determinar qué dispositivo alteró su dirección IP a través de la suplantación de Mac. Después de todo, si este ataque se realizó exclusivamente a través de ARP, la dirección IP de la máquina de la víctima debe permanecer consistente. Por el contrario, la máquina del atacante podría poseer una dirección IP histórica diferente.

Podemos desenterrar esta información dentro de una solicitud ARP y acelerar el proceso de descubrimiento utilizando este filtro Wireshark.

- `(arp.opcode) && ((eth.src == 08:00:27:53:0c:ba) || (eth.dst == 08:00:27:53:0c:ba))`

![](https://academy.hackthebox.com/storage/modules/229/ARP_Spoof_4.png)

En este caso, podríamos notar instantáneamente que la dirección MAC`08:00:27:53:0c:ba`inicialmente estaba vinculado a la dirección IP`192.168.10.5`, pero esto se cambió recientemente a`192.168.10.4`. Esta transición es indicativa de un intento deliberado de suplantación de ARP o envenenamiento de caché.

Además, examinar el tráfico de estas direcciones MAC con el siguiente filtro Wireshark puede resultar perspicaz:

- `eth.addr == 50:eb:f6:ec:0e:7f or eth.addr == 08:00:27:53:0c:ba`

![](https://academy.hackthebox.com/storage/modules/229/ARP_Spoof_5.png)

De inmediato, podríamos notar algunas inconsistencias con las conexiones TCP. Si las conexiones TCP están disminuyendo constantemente, es una indicación de que el atacante no está reenviando el tráfico entre la víctima y el enrutador.

Si el atacante es, de hecho, reenviar el tráfico y operar como un hombre en el medio, podríamos observar transmisiones idénticas o casi simétricas de la víctima al atacante y del atacante al enrutador.