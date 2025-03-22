# Detección de exfiltración (DNS)

Los atacantes emplean`DNS-based exfiltration`Debido a su confiabilidad, sigilo y al hecho de que el tráfico DNS a menudo se permite por defecto en las reglas de firewall de red. Al integrar los datos dentro de las consultas y respuestas DNS, los atacantes pueden pasar por alto los controles de seguridad y exfiltrar datos de manera encubierta. A continuación se muestra una explicación detallada de esta técnica y métodos de detección:

### **Cómo funciona la exfiltración DNS:**

- `Initial Compromise`: El atacante obtiene acceso a la red de la víctima, generalmente a través de malware, phishing o explotando vulnerabilidades.
- `Data Identification and Preparation`: El atacante localiza los datos que desean exfiltrarse y prepararse para la transmisión. Esto generalmente implica codificar o encriptar los datos y dividirlos en pequeños trozos.
- `Exfiltration via DNS`: El atacante envía los datos en los subdominios de las consultas DNS, utilizando técnicas como el túnel DNS o el flujo rápido. Por lo general, usan un dominio bajo su control o un dominio comprometido para este propósito. El servidor DNS del atacante recibe las consultas, extrae los datos y lo vuelve a montar.
- `Data Retrieval and Analysis`: Después de la exfiltración, el atacante decodifica o descifra los datos y los analiza.

### **Cómo se ve el tráfico de exfiltración de DNS**

![](https://academy.hackthebox.com/storage/modules/233/119.png)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de exfiltración (DNS)

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/dns_exf`
- **Índice Splunk relacionado**: `dns_exf`
- **Splunk SourceType relacionado**: `bro:dns:json`

---

# **Detección de exfiltración DNS con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar la exfiltración de DNS, usando registros de Splunk y Zeek.

Detección de exfiltración (DNS)

```
index=dns_exf sourcetype="bro:dns:json"
| eval len_query=len(query)
| search len_query>=40 AND query!="*.ip6.arpa*" AND query!="*amazonaws.com*" AND query!="*._googlecast.*" AND query!="_ldap.*"
| bin _time span=24h
| stats count(query) as req_by_day by _time, id.orig_h, id.resp_h
| where req_by_day>60
| table _time, id.orig_h, id.resp_h, req_by_day

```

![](https://academy.hackthebox.com/storage/modules/233/120.png)