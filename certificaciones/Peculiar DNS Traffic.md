# Peculiar DNS Traffic

El tráfico de DNS puede ser engorroso para inspeccionar, ya que muchas veces nuestros clientes generarán una tonelada, y las anormalidades a veces pueden enterrarse en el volumen de masa. Sin embargo, comprender el DNS y algunos signos directos de acciones maliciosas es importante en nuestros esfuerzos de análisis de tráfico.

# **Consultas DNS**

Las consultas DNS se usan cuando un cliente quiere resolver un nombre de dominio con una dirección IP, o al revés. Primero, podemos explorar el tipo de consulta más común, que son las búsquedas hacia adelante.

![](https://academy.hackthebox.com/storage/modules/229/DNS_forward_queries.jpg)

En términos generales, cuando un cliente inicia una consulta de búsqueda DNS Forward, hace los siguientes pasos.

- Pedido:
    - `Where is academy.hackthebox.com?`
- Respuesta:
    - `Well its at 192.168.10.6`

| **Paso** | **Descripción** |
| --- | --- |
| `1. Query Initiation` | Cuando el usuario quiere visitar algo como Academy.hackthebox.com, inicia una consulta DNS Forward. |
| `2. Local Cache Check` | Luego, el cliente verifica su caché DNS local para ver si ya ha resuelto el nombre de dominio en una dirección IP. Si no, continúa con lo siguiente. |
| `3. Recursive Query` | Luego, el cliente envía su consulta recursiva a su servidor DNS configurado (local o remoto). |
| `4. Root Servers` | El resolución DNS, si es necesario, comienza consultando los servidores de nombres raíz para encontrar los servidores de nombres autorizados para el dominio de nivel superior (TLD). Hay 13 servidores de raíz distribuidos en todo el mundo. |
| `5. TLD Servers` | El servidor raíz luego responde con los servidores de nombres autorizados para el TLD (también conocido como .com o .org) |
| `6. Authoritative Servers` | El resolución de DNS luego consulta los servidores de nombres autorizados del TLD para el dominio de segundo nivel (también conocido como HackTheBox.com). |
| `7. Domain Name's Authoritative Servers` | Finalmente, el resolución de DNS consulta los servidores de nombres autorizados de dominios para obtener la dirección IP asociada con el nombre de dominio solicitado (también conocido como Academy.hackTheBox.com). |
| `8. Response` | El resolución de DNS luego recibe la dirección IP (A o AAAA Registro) y la envía nuevamente al cliente que inició la consulta. |

### **Búsquedas/consultas inversas de DNS**

En el lado opuesto, tenemos búsqueda inversa. Estos ocurren cuando un cliente ya conoce la dirección IP y desea encontrar el FQDN correspondiente (nombre de dominio totalmente calificado).

- Pedido:
    - `What is your name 192.168.10.6?`
- Respuesta:
    - `Well its academy.hackthebox.com :)`

En este caso, los pasos son un poco menos complicados.

| **Paso** | **Descripción** |
| --- | --- |
| `1. Query Initiation` | El cliente envía una consulta inversa de DNS a su resolución DNS configurado (servidor) con la dirección IP que desea encontrar el nombre de dominio. |
| `2. Reverse Lookup Zones` | El resolución de DNS verifica si es autorizado para la zona de búsqueda inversa que corresponde al rango IP según lo determinado por la dirección IP recibida. AKA 192.0.2.1, la zona inversa sería 1.2.0.192.in-addr.arpa |
| `3. PTR Record Query` | El resolución de DNS luego busca un registro PTR en la zona de búsqueda inversa que corresponde a la dirección IP proporcionada. |
| `4. Response` | Si se encuentra un PTR coincidente, el servidor DNS (resolución) devuelve el FQDN de la IP para el cliente. |

![](https://academy.hackthebox.com/storage/modules/229/reverse-dns-lookup-diagram.png)

# **Tipos de registro de DNS**

DNS tiene muchos tipos de registro diferentes responsables de tener información diferente. Deberíamos estar familiarizados con estos, especialmente al monitorear el tráfico DNS.

| **Tipo de registro** | **Descripción** |
| --- | --- |
| `A`(DIRECCIÓN) | Este registro mapea un nombre de dominio a una dirección IPv4 |
| `AAAA`(Dirección IPv6) | Este registro mapea un nombre de dominio a una dirección IPv6 |
| `CNAME`(Nombre canónico) | Este registro crea un alias para el nombre de dominio. Aka Hello.com = World.com |
| `MX`(Intercambio de correo) | Este registro especifica el servidor de correo responsable de recibir mensajes de correo electrónico en nombre del dominio. |
| `NS`(Servidor de nombres) | Esto especifica los servidores de nombres autorizados para un dominio. |
| `PTR`(Puntero) | Esto se usa en consultas inversas para asignar una IP a un nombre de dominio. |
| `TXT`(Texto) | Esto se usa para especificar el texto asociado con el dominio |
| `SOA`(Inicio de la autoridad) | Esto contiene información administrativa sobre la zona |

# **Encontrar intentos de enumeración del DNS**

**Archivo PCAP relacionado**:

- `dns_enum_detection.pcapng`

Podríamos notar una cantidad significativa de tráfico DNS de un host cuando comenzamos a ver nuestra salida sin procesar en Wireshark.

- `dns`

![](https://academy.hackthebox.com/storage/modules/229/1-DNSTraffic.png)

Incluso podríamos notar que este tráfico concluyó con algo como`ANY`:

![](https://academy.hackthebox.com/storage/modules/229/2-DNSTraffic.png)

Esta sería una clara indicación de la enumeración del DNS y posiblemente incluso la enumeración del subdominio de un atacante.

# **Encontrar túneles DNS**

**Archivo PCAP relacionado**:

- `dns_tunneling.pcapng`

Por otro lado, podríamos notar una buena cantidad de registros de texto de un host. Esto podría indicar el túnel DNS. Al igual que el túnel ICMP, los atacantes pueden y han utilizado las consultas de búsqueda de DNS hacia adelante e inversa para realizar la exfiltración de datos. Lo hacen agregando los datos que les gustaría exfiltrarse como parte del campo TXT.

Si esto sucediera, podría parecer lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/3-DNSTraffic.png)

Si tuviéramos que cavar un poco más profundamente, podríamos notar algún texto fuera de lugar en el lado inferior derecho de nuestra pantalla.

![](https://academy.hackthebox.com/storage/modules/229/4-DNSTraffic.png)

Sin embargo, en muchos casos, estos datos pueden estar codificados o encriptados, y podríamos notar lo siguiente.

![](https://academy.hackthebox.com/storage/modules/229/5-DNSTraffic.png)

Podemos recuperar este valor de Wireshark localizándolo como lo siguiente y haciendo clic con el botón derecho en el valor para especificar para copiarlo.

![](https://academy.hackthebox.com/storage/modules/229/6-DNSTraffic.png)

Entonces, si tuviéramos que entrar en nuestra máquina Linux, en este caso podríamos utilizar algo como`base64 -d`Para recuperar el verdadero valor.

Tráfico de DNS peculiar

```
xnoxos@htb[/htb]$ echo 'VTBaU1EyVXhaSFprVjNocldETnNkbVJXT1cxaU0wb3pXVmhLYTFneU1XeFlNMUp2WVZoT1ptTklTbXhrU0ZJMVdETkNjMXBYUm5wYQpXREJMQ2c9PQo=' | base64 -d U0ZSQ2UxZHZkV3hrWDNsdmRWOW1iM0ozWVhKa1gyMWxYM1JvYVhOZmNISmxkSFI1WDNCc1pXRnpaWDBLCg==

```

Sin embargo, en algunos casos, los atacantes se duplicarán si no triple codificarán el valor que intentan exfiltrarse a través de la túnel DNS, por lo que es posible que necesitemos hacer lo siguiente.

Tráfico de DNS peculiar

```
xnoxos@htb[/htb]$ echo 'VTBaU1EyVXhaSFprVjNocldETnNkbVJXT1cxaU0wb3pXVmhLYTFneU1XeFlNMUp2WVZoT1ptTklTbXhrU0ZJMVdETkNjMXBYUm5wYQpXREJMQ2c9PQo=' | base64 -d | base64 -d | base64 -d
```

Sin embargo, es posible que necesitemos hacer más que solo Base64 decodificar estos valores, como en muchos casos, como se mencionó, estos valores podrían estar encriptados.

Los atacantes pueden realizar túneles DNS por las siguientes razones:

| **Paso** | **Descripción** |
| --- | --- |
| `1. Data Exfiltration` | Como se muestra arriba, el túnel DNS puede ser útil para los atacantes que intentan sacar datos de nuestra red sin ser atrapados. |
| `2. Command and Control` | Algunos agentes malware y maliciosos utilizarán túneles DNS en sistemas comprometidos para comunicarse de nuevo a sus servidores de comando y control. En particular, podríamos ver este método de uso en Botnets. |
| `3. Bypassing Firewalls and Proxies` | El túnel DNS permite a los atacantes evitar los firewalls y los proxies web que solo monitorean el tráfico HTTP/HTTPS. El tráfico DNS se permite tradicionalmente pasar por los límites de la red. Como tal, es importante que monitoreemos y controlemos este tráfico. |
| `4. Domain Generation Algorithms (DGAs)` | Algunos malware más avanzado utilizarán túneles DNS para comunicarse a sus servidores de comando y control que usan nombres de dominio generados dinámicamente a través de DGA. Esto hace que sea mucho más difícil para nosotros detectar y bloquear estos nombres de dominio. |

# **El sistema de archivos interplanetario y el túnel DNS**

En los últimos años se ha observado que los actores de amenaza avanzada utilizarán el sistema de archivos interplanetario para almacenar y extraer archivos maliciosos. Como tal, siempre debemos tener cuidado con el tráfico DNS y HTTP/HTTPS a URI como lo siguiente:

- `https://cloudflare-ipfs.com/ipfs/QmS6eyoGjENZTMxM7UdqBk6Z3U3TZPAVeJXdgp9VK4o1Sz`

Estas formas de ataques pueden ser excepcionalmente difíciles de detectar, ya que IPFS opera innatamente en base a pares. Para obtener más información, podemos investigar sobre IPF.

[Sistema de archivos interplanetario](https://developers.cloudflare.com/web3/ipfs-gateway/concepts/ipfs/)