# Detección de escaneo de puertos NMAP

`Port scanning with Nmap`es una técnica clave en el conjunto de herramientas de atacantes y probadores de penetración por igual. En esencia, lo que estamos haciendo con NMAP es sondear sistemas en red para puertos abiertos: estas son las 'puertas' a través de la cual los datos entran y salen de un sistema. Los puertos abiertos se pueden comparar con las puertas que pueden desbloquearse en un edificio, puertas que los atacantes podrían usar para obtener acceso.

Cuando usamos NMAP para escaneo de puertos, básicamente estamos iniciando una serie de solicitudes de conexión. Intentamos sistemáticamente establecer un apretón de manos TCP con cada puerto en el espacio de direcciones del objetivo. Si la conexión es exitosa, indica que el puerto está abierto. Aquí es donde se pone interesante. Cuando nos conectamos a un puerto abierto, el servicio que escucha en ese puerto podría enviar un "banner"; esto es esencialmente un poco de datos que nos dicen qué servicio se está ejecutando, y tal vez incluso qué versión está ejecutando.

Pero aclaremos una idea errónea: cuando hablemos de NMAP enviando datos al puerto de escaneo, en realidad no estamos enviando datos reales. Además del apretón de manos TCP real, la carga útil de los paquetes que envía NMAP es cero. No estamos enviando datos adicionales; Solo estamos tratando de iniciar una conexión.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detecting Nmap Port Scanning

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/cobaltstrike_beacon`
- **Índice Splunk relacionado**: `cobaltstrike_beacon`
- **Splunk SourceType relacionado**: `bro:conn:json`

---

# **Detección de escaneo de puertos NMAP con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar el escaneo de puertos NMAP, utilizando registros de Splunk y Zeek.

Detección de escaneo de puertos NMAP

```
index="cobaltstrike_beacon" sourcetype="bro:conn:json" orig_bytes=0 dest_ip IN (192.168.0.0/16, 172.16.0.0/12, 10.0.0.0/8)
| bin span=5m _time
| stats dc(dest_port) as num_dest_port by _time, src_ip, dest_ip
| where num_dest_port >= 3

```

![](https://academy.hackthebox.com/storage/modules/233/104.png)

**Desglose de búsqueda**:

- `index="cobaltstrike_beacon`": Esto restringe la búsqueda de registros almacenados en el`cobaltstrike_beacon`índice.
- `orig_bytes=0`: Esta parte del filtro de búsqueda se centra en eventos de red donde los bytes originales enviados son`zero`.
- `dest_ip IN (192.168.0.0/16, 172.16.0.0/12, 10.0.0.0/8)`: Esto restringe la búsqueda a eventos de red donde la dirección IP de destino está dentro de los rangos de dirección IP privada, que se usan comúnmente en redes internas.
- `| bin span=5m _time`: Este comando combina los eventos en`5`Anvalos de minuto basados en el`_time`campo, que es la marca de tiempo de cada evento.
- `| stats dc(dest_port) as num_dest_port by _time, src_ip, dest_ip`: El`stats`El comando se utiliza para agregar datos. El`dc(dest_port)`la función cuenta el distinto número de puertos de destino accedidos para cada combinación de`_time`, `src_ip`, y`dest_ip`. El resultado se almacena en un nuevo campo llamado`num_dest_port`.
- `| where num_dest_port >= 3`: Esta parte de la búsqueda filtra los resultados para mostrar solo esos registros donde el recuento distinto de los puertos de destino (`num_dest_port`) es`three`o mayor. Esto se basa en la suposición de que escanear tres o más puertos en un período de tiempo corto es un indicador potencial de un escaneo de puertos.