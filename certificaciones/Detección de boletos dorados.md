# Detección de boletos dorados

Anteriormente en esta sección, cubrimos`Golden Tickets`. Desafortunadamente, Zeek carece de la capacidad de identificar confiablemente los boletos de oro. Por lo tanto, concentraremos nuestra búsqueda de Splunk en descubrir anomalías en la creación de boletos de Kerberos.

En un boleto de oro o un ataque de pases-ticket, el atacante evita el proceso habitual de autenticación de Kerberos, que involucra los mensajes AS-REQ y AS-REP.

En un proceso típico de autenticación de Kerberos, un cliente comienza enviando un mensaje AS-REQ (solicitud de servicio de autenticación) al Centro de distribución de clave (KDC), específicamente el servicio de autenticación (AS), solicitando un boleto de concesión de ticket (TGT). El KDC responde con un mensaje AS-REP (respuesta al servicio de autenticación), que incluye el TGT si las credenciales del cliente son válidas. El cliente puede usar el TGT para solicitar boletos de servicio (tickets de servicio de concesión de boletos, o TGS) para servicios específicos en la red.

- En un ataque de boleto dorado, el atacante genera un TGT forjado, que les otorga acceso a cualquier servicio en la red sin tener que autenticarse con el KDC. Dado que el atacante tiene un TGT forjado, puede solicitar directamente los boletos TGS sin pasar por el proceso AS-REQ y AS-REP.
- En un ataque de pase del boleto, el atacante roba un boleto TGT o TGS válido de un usuario legítimo (por ejemplo, al comprometer su máquina) y luego utiliza ese boleto para acceder a los servicios en la red como si fueran el usuario legítimo. Una vez más, dado que el atacante ya tiene un boleto válido, puede evitar el proceso AS-REQ y AS-REP.

### **Cómo se ve el tráfico de boletos dorados**

![](https://academy.hackthebox.com/storage/modules/233/111.png)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de boletos dorados

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/golden_ticket_attack`
- **Índice Splunk relacionado**: `golden_ticket_attack`
- **Splunk SourceType relacionado**: `bro:kerberos:json`

---

# **Detección de boletos dorados con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar boletos dorados, usando registros de Splunk y Zeek.

Detección de boletos dorados

```
index="golden_ticket_attack" sourcetype="bro:kerberos:json"
| where client!="-"
| bin _time span=1m
| stats values(client), values(request_type) as request_types, dc(request_type) as unique_request_types by _time, id.orig_h, id.resp_h
| where request_types=="TGS" AND unique_request_types==1

```

![](https://academy.hackthebox.com/storage/modules/233/112.png)

**Desglose de búsqueda**:

- `index="golden_ticket_attack" sourcetype="bro:kerberos:json"`: Esta línea especifica la fuente de datos que la consulta está buscando. Está buscando eventos en el`golden_ticket_attack`índice donde el`sourcetype`(formato de datos) es`bro:kerberos:json`.
- `| where client!="-"`: Esta línea filtra eventos donde el`client`el campo es igual a. Esto es eliminar el ruido de los datos excluyendo los eventos donde la información del cliente no está disponible.
- `| bin _time span=1m`: Esta línea divide los datos en`one-minute`intervalos basados en el`_time`campo, que es la marca de tiempo de cada evento. Esto se utiliza para analizar patrones de actividad dentro de cada ventana de un minuto.
- `| stats values(client), values(request_type) as request_types, dc(request_type) as unique_request_types by _time, id.orig_h, id.resp_h`: Esta línea agrega los datos por minuto, dirección IP de origen (`id.orig_h`) y dirección IP de destino (`id.resp_h`). Calcula lo siguiente para cada combinación de estos campos de agrupación:
    - `values(client)`: Todos los valores únicos del cliente asociados con los eventos.
    - `values(request_type) as request_types`: Todos los tipos de solicitudes únicos asociados con los eventos.
    - `dc(request_type) as unique_request_types`: El recuento distinto de los tipos de solicitudes.
- `| where request_types=="TGS" AND unique_request_types==1`: Esta línea filtra los resultados para mostrar solo aquellos donde es el único tipo de solicitud`TGS`(Servicio de concesión de boletos), y solo hay un tipo de solicitud único.