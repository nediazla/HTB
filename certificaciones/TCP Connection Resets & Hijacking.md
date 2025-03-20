# TCP Connection Resets & Hijacking

Desafortunadamente, TCP no proporciona el nivel de protección para evitar que nuestros anfitriones tengan sus conexiones terminadas o secuestradas por un atacante. Como tal, podríamos notar que una conexión es terminada por un paquete RST o secuestrada a través del secuestro de conexión.

# **Terminación de conexión TCP**

**Archivo PCAP relacionado**:

- `RST_Attack.pcapng`

Supongamos que un adversario quería causar condiciones de denegación de servicio dentro de nuestra red. Podrían emplear un ataque de inyección de paquetes TCP RST simple o la terminación de la conexión TCP en términos simples.

Este ataque es una combinación de algunas condiciones:

1. `The attacker will spoof the source address to be the affected machine's`
2. `The attacker will modify the TCP packet to contain the RST flag to terminate the connection`
3. `The attacker will specify the destination port to be the same as one currently in use by one of our machines.`

Como tal, podríamos notar una cantidad excesiva de paquetes que van a un puerto.

![](https://academy.hackthebox.com/storage/modules/229/1-RST.png)

Una forma en que podemos verificar que este es un ataque TCP RST es a través de la dirección física del transmisor de estos paquetes RST TCP. Supongamos, la dirección IP 192.168.10.4 está registrada en aa: aa: aa: aa: aa: aa en nuestra lista de dispositivos de red, y notamos una Mac completamente diferente que los envía como lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/2-RST.png)

Esto indicaría actividad maliciosa dentro de nuestra red, y podríamos concluir que este es probablemente un ataque TCP primero. Sin embargo, vale la pena señalar que un atacante podría falsificar su dirección MAC para evadir aún más la detección. En este caso, podríamos notar retransmisiones y otros problemas como vimos en la sección de envenenamiento de ARP.

---

# **Secuestro de conexión TCP**

**Archivo PCAP relacionado**:

- `TCP-hijacking.pcap`

Para actores más avanzados, pueden emplear el secuestro de conexión TCP. En este caso, el atacante monitoreará activamente la conexión objetivo que desean secuestrar.

El atacante realizará la predicción del número de secuencia para inyectar sus paquetes maliciosos en el orden correcto. Durante esta inyección, falsificarán la dirección de origen para que sea la misma que nuestra máquina afectada.

El atacante necesitará bloquear los ACK para llegar a la máquina afectada para continuar el secuestro. Hacen esto, ya sea retrasando o bloqueando los paquetes ACK. Como tal, este ataque se emplea muy comúnmente con envenenamiento por ARP, y podríamos notar lo siguiente en nuestro análisis de tráfico.

![](https://academy.hackthebox.com/storage/modules/229/4-RST.png)