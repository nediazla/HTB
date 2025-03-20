# ICMP Tunneling

**Archivo PCAP relacionado**:

- `icmp_tunneling.pcapng`

---

El túnel es una técnica empleada por los adversarios para exfiltrar datos de una ubicación a otra. Hay muchos tipos diferentes de túneles, y cada tipo diferente utiliza un protocolo diferente. Comúnmente, los atacantes pueden utilizar proxies para evitar nuestros controles de red o protocolos que nuestros sistemas y controles permiten.

# **Conceptos básicos de túneles**

Esencialmente, cuando un atacante quiere comunicar datos a otro anfitrión, puede emplear túneles. En muchos casos, podríamos notar esto a través del atacante que posee algún mando y control sobre una de nuestras máquinas. Como se señaló, el túnel se puede realizar de muchas maneras diferentes. Uno de los tipos más comunes es el túnel SSH. Sin embargo, se puede observar de manera similar basada en proxy, http, https, DNS y otros tipos de manera similar.

![](https://academy.hackthebox.com/storage/modules/229/basic-tunnel-1.png)

La idea detrás del túnel es que un atacante podrá expandir su comando y control y evitar nuestros controles de red a través del protocolo de su elección.

# **Túnel ICMP**

En el caso de la túnel ICMP, un atacante agregará datos que desea exfiltrarse al mundo exterior u otro host en el campo de datos en una solicitud ICMP. Esto se hace con la intención de ocultar estos datos entre un tipo de protocolo común como ICMP, y con suerte se pierde dentro del tráfico de red.

![](https://academy.hackthebox.com/storage/modules/229/icmp_ping_example.jpg)

# **Encontrar túneles ICMP**

Dado que el túnel ICMP se realiza principalmente a través de un atacante que agrega datos al campo de datos para ICMP, podemos encontrarlo mirando el contenido de los datos por solicitud y respuesta.

![](https://academy.hackthebox.com/storage/modules/229/1-ICMP-tunneling.png)

Podemos filtrar nuestra captura de Wireshark solo a solicitudes y respuestas ICMP ingresando ICMP en la barra de filtro.

![](https://academy.hackthebox.com/storage/modules/229/2-ICMP-tunneling.png)

Supongamos que notamos que la fragmentación ocurrió dentro de nuestro tráfico ICMP como lo es arriba, esto indicaría una gran cantidad de datos que se transfieren a través de ICMP. Para comprender este comportamiento, debemos observar una solicitud ICMP normal. Podemos tener en cuenta que los datos son algo razonables como 48 bytes.

![](https://academy.hackthebox.com/storage/modules/229/3-ICMP-tunneling.png)

Sin embargo, una solicitud de ICMP sospechosa puede tener una gran longitud de datos como 38000 bytes.

![](https://academy.hackthebox.com/storage/modules/229/4-ICMP-tunneling.png)

Si nos gustaría echar un vistazo a los datos en tránsito, podemos mirar en el lado derecho de nuestra pantalla en Wireshark. En este caso, podríamos notar algo como un nombre de usuario y una contraseña que se está haciendo sonar a un host externo o interno. Esta es una indicación directa del túnel ICMP.

![](https://academy.hackthebox.com/storage/modules/229/5-ICMP-tunneling.png)

Por otro lado, los adversarios más avanzados utilizarán la codificación o el cifrado al transmitir datos exfiltrados, incluso en el caso de la túnel ICMP. Supongamos que notamos lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/6-ICMP-tunneling.png)

Podríamos copiar este valor de Wireshark y decodificarlo dentro de Linux con la utilidad Base64.

Túnel ICMP

```
xnoxos@htb[/htb]$ echo 'VGhpcyBpcyBhIHNlY3VyZSBrZXk6IEtleTEyMzQ1Njc4OQo=' | base64 -d
```

Este también sería un caso en el que se observa el túnel ICMP. En muchos casos, si la longitud de los datos de ICMP es mayor que 48-bytes, sabemos que algo sospechoso está sucediendo y siempre debemos investigarlo.

# **Prevención de túneles ICMP**

Para evitar que ocurra el túnel ICMP, podemos realizar las siguientes acciones.

1. `Block ICMP Requests`Simplemente, si no está permitido ICMP, los atacantes no podrán utilizarlo.
2. `Inspect ICMP Requests and Replies for Data`Desmirar datos o inspeccionar datos para contenido malicioso en estas solicitudes y respuestas puede permitirnos una mejor visión de nuestro entorno y la capacidad de evitar esta exfiltración de datos.