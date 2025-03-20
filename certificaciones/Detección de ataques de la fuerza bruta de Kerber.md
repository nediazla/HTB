# Detección de ataques de la fuerza bruta de Kerberos

Cuando los adversarios realizan`Kerberos-based user enumeration`, envían un mensaje AS-REQ (solicitud de servicio de autenticación) al Centro de distribución de clave (KDC), que es responsable de manejar la autenticación de Kerberos. Este mensaje incluye el nombre de usuario que intentan validar. Prestan mucha atención a la respuesta que reciben, ya que revela información valiosa sobre la existencia de la cuenta de usuario especificada.

Un nombre de usuario válido le pedirá al servidor que`return a TGT`o plantear un error como`KRB5KDC_ERR_PREAUTH_REQUIRED`, indicando que se requiere la preautenticación. Por otro lado, se cumplirá un nombre de usuario no válido con un código de error de Kerberos`KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN`En el mensaje AS-Rep (respuesta al servicio de autenticación). Al examinar las respuestas a sus mensajes AS-REQ, los adversarios pueden determinar rápidamente qué nombres de usuario son válidos en el sistema de destino.

### **Cómo se ven los ataques de la fuerza bruta de Kerberos en el cable**

![](https://academy.hackthebox.com/storage/modules/233/107.png)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de ataques de la fuerza bruta de Kerberos

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/kerberos_bruteforce`
- **Índice Splunk relacionado**: `kerberos_bruteforce`
- **Splunk SourceType relacionado**: `bro:kerberos:json`

---

# **Detección de ataques de fuerza bruta de Kerberos con troncos Splunk & Zeek**

Ahora exploremos cómo podemos identificar los ataques de la Fuerza Bruta Kerberos, usando registros de Splunk y Zeek.

Detección de ataques de la fuerza bruta de Kerberos

```
index="kerberos_bruteforce" sourcetype="bro:kerberos:json"
error_msg!=KDC_ERR_PREAUTH_REQUIRED
success="false" request_type=AS
| bin _time span=5m
| stats count dc(client) as "Unique users" values(error_msg) as "Error messages" by _time, id.orig_h, id.resp_h
| where count>30

```

![](https://academy.hackthebox.com/storage/modules/233/108.png)