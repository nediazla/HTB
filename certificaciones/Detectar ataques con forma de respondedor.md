# Detectar ataques con forma de respondedor

# **LLMNR/NBT-NS/MDNS ENVENDEINACIÓN**

`LLMNR (Link-Local Multicast Name Resolution) and NBT-NS (NetBIOS Name Service) poisoning`, también conocido como suplantación de NBNS, son ataques a nivel de red que explotan las ineficiencias en estos protocolos de resolución de nombres. Ambos`LLMNR`y`NBT-NS`se utilizan para resolver los nombres de host en las direcciones IP en las redes locales cuando falla la resolución de nombre de dominio totalmente calificado (FQDN). Sin embargo, su falta de mecanismos de seguridad incorporados los hace susceptibles a la falsificación y los ataques de envenenamiento.

Típicamente, los atacantes emplean el[Respondedor](https://github.com/lgandx/Responder)Herramienta para ejecutar LLMNR, NBT-NS o MDNS Preisis.

### **Pasos de ataque:**

- Un dispositivo de víctima envía una consulta de resolución de nombre para un nombre de host con mistre (por ejemplo,`fileshrae`).
- DNS no resuelve el nombre de host de Mistyped.
- El dispositivo víctima envía una consulta de resolución de nombre para el nombre de host Mistyped usando llmnr/nbt-ns.
- El anfitrión del atacante responde al tráfico LLMNR (UDP 5355)/NBT-NS (UDP 137), fingiendo conocer la identidad del host solicitado. Esto envenena efectivamente el servicio, ordenando a la víctima que se comunique con el sistema controlado por el adversario.

![](https://academy.hackthebox.com/storage/modules/233/image68.png)

El resultado de un ataque exitoso es la adquisición del hash NetNTLM de la víctima, que puede ser agrietado o transmitido en un intento de obtener acceso a los sistemas donde estas credenciales son válidas.

### **Oportunidades de detección de respuesta**

La detección de envenenamiento por LLMNR, NBT-NS y MDNS puede ser un desafío. Sin embargo, las organizaciones pueden mitigar el riesgo implementando las siguientes medidas:

- Implementar soluciones de monitoreo de red para detectar patrones de tráfico LLMNR y NBT-NS inusuales, como un volumen elevado de solicitudes de resolución de nombres de una sola fuente.
- Emplear un enfoque de honeypot: la resolución de nombre para hosts inexistentes debería fallar. Si un atacante está presente y falsifica las respuestas LLMNR/NBT-NS/MDNS, la resolución de nombre tendrá éxito.[https://www.praetorian.com/blog/a-simple-and-eficective-way-to-detect-broadcast-name-resolution-poisoning-bnrp/](https://www.praetorian.com/blog/a-simple-and-effective-way-to-detect-broadcast-name-resolution-poisoning-bnrp/)

![](https://academy.hackthebox.com/storage/modules/233/image22.png)

Un script de PowerShell similar al anterior se puede automatizar para ejecutarse como una tarea programada para ayudar en la detección. Registrar esta actividad podría representar un desafío, pero el`New-EventLog`Se puede usar el cmdlet de PowerShell.

Detectar ataques con forma de respondedor

```powershell
PS C:\Users\Administrator> New-EventLog -LogName Application -Source LLMNRDetection

```

Para crear un evento, el`Write-EventLog`se debe usar cmdlet:

Detectar ataques con forma de respondedor

```powershell
PS C:\Users\Administrator> Write-EventLog -LogName Application -Source LLMNRDetection -EventId 19001 -Message $msg -EntryType Warning
```

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detectar ataques con forma de respondedor con Splunk**

Ahora exploremos cómo podemos identificar los ataques con forma de respondedor discutidos anteriormente, usando Splunk y registros de un script PowerShell similar al anterior.

**Periodo de tiempo**: `earliest=1690290078 latest=1690291207`

Detectar ataques con forma de respondedor

```
index=main earliest=1690290078 latest=1690291207 SourceName=LLMNRDetection
| table _time, ComputerName, SourceName, Message

```

![](https://academy.hackthebox.com/storage/modules/233/4.png)

---

[Sysmon Event ID 22](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=90022)También se puede utilizar para rastrear las consultas DNS asociadas con acciones de archivos inexistentes/mistentes.

**Periodo de tiempo**: `earliest=1690290078 latest=1690291207`

Detectar ataques con forma de respondedor

```
index=main earliest=1690290078 latest=1690291207 EventCode=22
| table _time, Computer, user, Image, QueryName, QueryResults

```

![](https://academy.hackthebox.com/storage/modules/233/89.png)

---

Además, recuerda que[Evento 4648](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4648)Se puede utilizar para detectar inicios de sesión explícitos en acciones de archivo deshonesto que los atacantes podrían usar para reunir credenciales de usuario legítimas.

**Periodo de tiempo**: `earliest=1690290814 latest=1690291207`

Detectar ataques con forma de respondedor

```
index=main earliest=1690290814 latest=1690291207 EventCode IN (4648)
| table _time, EventCode, source, name, user, Target_Server_Name, Message
| sort 0 _time

```

![](https://academy.hackthebox.com/storage/modules/233/6.png)