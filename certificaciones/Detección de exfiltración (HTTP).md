# Detección de exfiltración (HTTP)

`Data exfiltration`Dentro del cuerpo posterior es una técnica que los atacantes emplean para extraer información confidencial de un sistema comprometido al disfrazarlo como tráfico web legítimo. Implica transmitir los datos robados del sistema comprometido a un servidor externo controlado por el atacante utilizando solicitudes de publicación HTTP. Dado que las solicitudes de publicación se usan comúnmente para fines legítimos, como los envíos de formularios y las cargas de archivos, este método de exfiltración de datos puede ser difícil de detectar.

Para exfiltrar los datos, los atacantes los envían como el cuerpo de una solicitud post HTTP a su comando y control (C2) servidor. A menudo usan URL y encabezados aparentemente inocuos para disfrazar aún más el tráfico malicioso. El servidor C2 recibe la solicitud de publicación, extrae los datos del cuerpo y decodifica o lo descifra para su posterior análisis y explotación.

Para detectar la exfiltración de datos a través de Post Body, podemos emplear herramientas de monitoreo y análisis de red para agregar todos los datos enviados a direcciones IP específicas y puertos. Al analizar los datos agregados, podemos identificar patrones y anomalías que pueden indicar intentos de exfiltración de datos.

En esta sección, monitorearemos el volumen de tráfico saliente de nuestra red a direcciones y puertos IP específicos. Si observamos transferencias de datos inusualmente grandes o frecuentes a un destino específico, puede indicar la exfiltración de datos.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de exfiltración (HTTP)

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/cobaltstrike_exfiltration_http`
- **Índice Splunk relacionado**: `cobaltstrike_exfiltration_http`
- **Splunk SourceType relacionado**: `bro:http:json`

---

# **Detección de exfiltración HTTP con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar la exfiltración HTTP, usando registros de Splunk y Zeek.

Detección de exfiltración (HTTP)

```
index="cobaltstrike_exfiltration_http" sourcetype="bro:http:json" method=POST
| stats sum(request_body_len) as TotalBytes by src, dest, dest_port
| eval TotalBytes = TotalBytes/1024/1024

```

![](https://academy.hackthebox.com/storage/modules/233/118.png)