# Detección de malware de baliza

`Malware beaconing`es una técnica que encontramos con frecuencia en nuestras investigaciones de ciberseguridad. Se refiere a la comunicación periódica iniciada por los sistemas infectados por malware con sus respectivos servidores de comando y control (C2). Las balizas, típicamente paquetes de datos pequeños, se envían a intervalos regulares, al igual que un faro envía una señal regular.

En nuestro análisis del comportamiento de baliza, a menudo observamos varios patrones distintos. Los intervalos de baliza se pueden solucionar, nerviarse (variar ligeramente de un patrón fijo) o seguir un horario más complejo basado en los objetivos específicos del malware. Hemos encontrado malware que utiliza varios protocolos para la baliza, incluido HTTP/HTTPS, DNS e incluso ICMP (ping).

En esta sección, nos concentraremos en detectar el comportamiento de baliza asociado con un marco de comando y control (C2) ampliamente reconocido conocido como`Cobalt Strike`(en su configuración predeterminada).

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de malware de baliza

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/cobaltstrike_beacon`
- **Índice Splunk relacionado**: `cobaltstrike_beacon`
- **Splunk SourceType relacionado**: `bro:http:json`

---

# **Detección de malware de baliza con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar la baliza, usando registros de Splunk y Zeek.

Detección de malware de baliza

```
index="cobaltstrike_beacon" sourcetype="bro:http:json"
| sort 0 _time
| streamstats current=f last(_time) as prevtime by src, dest, dest_port
| eval timedelta = _time - prevtime
| eventstats avg(timedelta) as avg, count as total by src, dest, dest_port
| eval upper=avg*1.1
| eval lower=avg*0.9
| where timedelta > lower AND timedelta < upper
| stats count, values(avg) as TimeInterval by src, dest, dest_port, total
| eval prcnt = (count/total)*100
| where prcnt > 90 AND total > 10

```

![](https://academy.hackthebox.com/storage/modules/233/102.png)

**Desglose de búsqueda**:

- `index="cobaltstrike_beacon" sourcetype="bro:http:json"`: Selecciona los datos del`cobaltstrike_beacon`Eventos de tipo de índice y filtros de índice de índice`bro:http:json`, que representan registros de Zeek HTTP.
- `| sort 0 _time`: Clasifica los eventos en orden ascendente basado en su marca de tiempo (`_time`).
- `| streamstats current=f last(_time) as prevtime by src, dest, dest_port`: Para cada evento, calcula la marca de tiempo del evento anterior (`prevtime`) agrupado por fuente IP (`src`), Destino IP (`dest`) y puerto de destino (`dest_port`).
- `| eval timedelta = _time - prevtime`: Calcula la diferencia de tiempo (`timedelta`) entre las marcas de tiempo de los eventos actuales y anteriores.
- `| eventstats avg(timedelta) as avg, count as total by src, dest, dest_port`: Calcula la diferencia de tiempo promedio (`avg`) y el número total de eventos (`total`) para cada combinación de`src`, `dest`, y`dest_port`.
- `| eval upper=avg*1.1`: Establece un límite superior para la diferencia de tiempo agregando un`10%`margen al promedio.
- `| eval lower=avg*0.9`: Establece un límite inferior para la diferencia de tiempo restando un`10%`margen del promedio.
- `| where timedelta > lower AND timedelta < upper`: Filtra los eventos donde la diferencia horaria cae dentro de los límites superiores e inferiores definidos.
- `| stats count, values(avg) as TimeInterval by src, dest, dest_port, total`: Cuenta el número de eventos y extrae el intervalo de tiempo promedio para cada combinación de`src`, `dest`, `dest_port`, y`total`.
- `| eval prcnt = (count/total)*100`: Calcula el porcentaje (`prcnt`) de eventos dentro de los límites de intervalo de tiempo definidos.
- `| where prcnt > 90 AND total > 10`: Filtra los resultados para incluir solo aquellos donde más`90%`de los eventos caen dentro de los límites de intervalo de tiempo definidos, y hay más de`10`Total de eventos.