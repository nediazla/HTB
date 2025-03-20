# IP Time-to-Live Attacks

**Archivo PCAP relacionado**:

- `ip_ttl.pcapng`

---

Los ataques de tiempo de vida se utilizan principalmente como un medio de evasión por parte de los atacantes. Básicamente hablando, el atacante establecerá intencionalmente un TTL muy bajo en sus paquetes IP para intentar evadir los firewalls, IDS y los sistemas IPS. Estos funcionan como lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/ttl-attack-diagram.png)

1. El atacante creará un paquete IP con un valor TTL intencionalmente bajo (1, 2, 3 y así sucesivamente).
2. A través de cada host que este paquete pase a través de este valor TTL se disminuirá por uno hasta que alcance cero.
3. Al llegar a cero, este paquete se descartará. El atacante intentará desecharse este paquete antes de que llegue a un firewall o un sistema de filtrado para evitar la detección/controles.
4. Cuando los paquetes caducan, los enrutadores a lo largo de la ruta generan el tiempo ICMP excedieron los mensajes y los envían de nuevo a la dirección IP de origen.

### **Encontrar irregularidades en IP TTL**

Para empezar, podemos comenzar a tirar nuestro tráfico y abrirlo en Wireshark. Detectar esto en pequeñas cantidades puede ser difícil, pero afortunadamente para los atacantes de EE. UU. La mayoría de las veces utilizará la manipulación TTL en los esfuerzos de escaneo de puertos. De inmediato podríamos notar algo como lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/1-ttl.png)

Sin embargo, también podríamos notar un mensaje SYN y ACK devuelto de uno de nuestros puertos de servicio legítimos en nuestro host afectado. Al hacerlo, el atacante podría haber evadido con éxito uno de nuestros controles de firewall.

![](https://academy.hackthebox.com/storage/modules/229/2-ttl.png)

Entonces, si abramos uno de estos paquetes, podríamos ver de manera realista por qué es esto. Supongamos que abrimos la pestaña IPv4 en Wireshark para cualquiera de estos paquetes. Podríamos notar un TTL muy bajo como el siguiente.

![](https://academy.hackthebox.com/storage/modules/229/3-ttl.png)

Como tal, podemos implementar un control que descarte o filtra paquetes que no tienen un TTL lo suficientemente alto. Al hacerlo, podemos evitar estas formas de ataques de elaboración de paquetes IP.