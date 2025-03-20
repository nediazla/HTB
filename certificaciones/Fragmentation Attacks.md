# Fragmentation Attacks

**Archivo PCAP relacionado**:

- `nmap_frag_fw_bypass.pcapng`

---

Cuando comenzamos a buscar anomalías de red, siempre debemos considerar la capa IP. En pocas palabras, la capa IP funciona en su capacidad para transferir paquetes de un salto a otro. Esta capa utiliza direcciones IP de origen y destino para comunicaciones interanjadas. Cuando examinamos este tráfico, podemos identificar las direcciones IP, ya que existen dentro del encabezado IP del paquete.

Sin embargo, es esencial tener en cuenta que esta capa no tiene mecanismos para identificar cuándo se pierden, caen los paquetes, caen o se manipulan. En cambio, debemos reconocer que estos percances son manejados por las capas de transporte o aplicación para estos datos. Para diseccionar estos paquetes, podemos explorar algunos de sus campos:

1. `Length - IP header length`: Este campo contiene la longitud total del encabezado IP.
2. `Total Length - IP Datagram/Packet Length`: Este campo especifica toda la longitud del paquete IP, incluidos los datos relevantes.
3. `Fragment Offset`: En muchos casos, cuando un paquete es lo suficientemente grande como para dividirse, el desplazamiento de fragmentación se establecerá para proporcionar instrucciones para volver a ensamblar el paquete tras la entrega al host de destino.
4. `Source and Destination IP Addresses`: Estos campos contienen las direcciones de origen (fuente) y de destino para los dos hosts comunicantes.

![](https://academy.hackthebox.com/storage/modules/229/IPheader.jpg)

# **Campos comúnmente abusados**

Innatamente, los atacantes pueden elaborar estos paquetes para causar problemas de comunicación. Tradicionalmente, un atacante podría intentar evadir los controles de IDS a través de la malformación o modificación de los paquetes. As such, diving into each one of these fields and understanding how we can detect their misuse will equip us with the tools to succeed in our traffic analysis efforts.

# **Abuso de fragmentación**

La fragmentación sirve como un medio para que nuestros anfitriones legítimos se comuniquen grandes conjuntos de datos entre sí dividiendo los paquetes y reensamblándolos al parecer. Esto se logra comúnmente mediante la configuración de una unidad de transmisión máxima (MTU). La MTU se usa como estándar para dividir estos paquetes grandes en tamaños iguales para acomodar toda la transmisión. Vale la pena señalar que el último paquete probablemente será más pequeño. Este campo proporciona instrucciones al host de destino sobre cómo puede volver a montar estos paquetes en orden lógico.

Comúnmente, los atacantes pueden abusar de este campo para los siguientes propósitos:

1. `IPS/IDS Evasion`Digamos, por ejemplo, que nuestros controles de detección de intrusos no vuelven a montar paquetes fragmentados. Bueno, para abreviar, un atacante podría dividir su NMAP u otras técnicas de enumeración para fragmentarse, y como tal podría evitar estos controles y volver a montar en el destino.
2. `Firewall Evasion`A través de la fragmentación, un atacante también podría evadir los controles de un firewall a través de la fragmentación. Una vez más, si el firewall no vuelve a montar estos paquetes antes de la entrega al host de destino, el intento de enumeración del atacante podría tener éxito.
3. `Firewall/IPS/IDS Resource Exhaustion`Suponga que un atacante debía crear su ataque para fragmentar paquetes a una MTU muy pequeña (10, 15, 20, etc.), el control de la red podría no volver a armar estos paquetes debido a limitaciones de recursos, y el atacante podría tener éxito en sus esfuerzos de enumeración.
4. `Denial of Service`Para los antiguos hosts, un atacante puede utilizar la fragmentación para enviar paquetes IP superiores a 65535 bytes a través de ping u otros comandos. Al hacerlo, el anfitrión de destino volverá a armar este paquete malicioso y experimentará innumerables problemas diferentes. Como tal, la condición resultante es exitosa negación de servicio del atacante.

Si nuestro mecanismo de red tuviera que funcionar correctamente. Debería hacer lo siguiente:

- `Delayed Reassembly`El IDS/IPS/Firewall debe actuar de lo mismo que el Host de destino, en el sentido de que espera a que lleguen todos los fragmentos para reconstruir la transmisión para realizar la inspección de paquetes.

# **Encontrar irregularidades en compensaciones de fragmentos**

Para comprender mejor la mecánica mencionada anteriormente, podemos abrir el archivo de captura de tráfico relacionado en Wireshark.

Ataques de fragmentación

```
xnoxos@htb[/htb]$ wireshark nmap_frag_fw_bypass.pcapng
```

Para empezar, podríamos notar que varias solicitudes de ICMP van a un host de otro, esto es indicativo de las solicitudes de inicio de un escaneo NMAP tradicional. Este es el comienzo del proceso de descubrimiento del host. Un atacante podría ejecutar un comando como este.

### **Enumeración del atacante**

Ataques de fragmentación

```
xnoxos@htb[/htb]$ nmap <host ip>

```

Al hacerlo, generarán lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/1-frag.png)

En segundo lugar, un atacante podría definir un tamaño máximo de la unidad de transmisión como este para fragmentar sus paquetes de escaneo de puertos.

Ataques de fragmentación

```
xnoxos@htb[/htb]$ nmap -f 10 <host ip>

```

Al hacerlo, generarán paquetes IP con un tamaño máximo de 10. Ver una tonelada de fragmentación de un host puede ser un indicador de este ataque, y se vería como lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/2-frag.png)

However, the more notable indicator of a fragmentation scan, regardless of its evasion use is the single host to many ports issues that it generates. Tomemos lo siguiente, por ejemplo.

![](https://academy.hackthebox.com/storage/modules/229/3-frag.png)

En este caso, el host de destino respondería con los primeros indicadores para puertos que no tienen un servicio activo que se ejecute (también conocido como puertos cerrados). Este patrón es una clara indicación de un escaneo fragmentado.

Si nuestro Wireshark no está reensamblando paquetes para nuestra inspección, podemos hacer un cambio rápido en nuestras preferencias para el protocolo IPv4.

![](https://academy.hackthebox.com/storage/modules/229/4-frag.png)