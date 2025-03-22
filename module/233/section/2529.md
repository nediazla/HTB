# Detección de entradas doradas/boletos de plata

# **Boleto dorado**

A`Golden Ticket`El ataque es un método potente en el que un atacante forja un boleto de concesión de boletos (TGT) para obtener acceso no autorizado a un dominio de Windows Active Directory como administrador de dominio. El atacante crea un TGT con credenciales de usuario arbitrarias y luego usa este boleto forjado para hacerse pasar por un administrador de dominio, obteniendo así el control total sobre el dominio. El ataque de boleto de oro es sigiloso y persistente, ya que el boleto forjado tiene un largo período de validez y sigue siendo válido hasta que expira o se revoca.

### **Pasos de ataque:**

- El atacante extrae el hash NTLM de la cuenta KRBTGT utilizando un`DCSync`ataque (alternativamente, pueden usar`NTDS.dit`y`LSASS process dumps`en el controlador de dominio).
    
    ![](https://academy.hackthebox.com/storage/modules/233/image74.png)
    
- Armado con el`KRBTGT`Hash, el atacante forja un TGT para una cuenta de usuario arbitraria, asignando privilegios de administrador de dominio de TI.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image17.png)
    
- El atacante inyecta el TGT falsificado de la misma manera que un ataque de pase el boleto.

### **Oportunidades de detección de boletos de oro**

La detección de ataques de boletos dorados puede ser un desafío, ya que un atacante puede forjar el TGT, sin dejar prácticamente rastros de`Mimikatz`ejecución. Una opción es monitorear los métodos comunes para extraer el`KRBTGT`picadillo:

- `DCSync attack`
- `NTDS.dit file access`
- `LSASS memory read on the domain controller (Sysmon Event ID 10)`

Desde otro punto de vista, un boleto de oro es solo otro boleto para la detección de pases.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detección de boletos dorados con Splunk (otro boleto que se pasará por el enfoque)**

Ahora exploremos cómo podemos identificar boletos dorados, usando Splunk.

**Periodo de tiempo**: `earliest=1690451977 latest=1690452262`

Detección de entradas doradas/boletos de plata

```
index=main earliest=1690451977 latest=1690452262 source="WinEventLog:Security" user!=*$ EventCode IN (4768,4769,4770) | rex field=user "(?<username>[^@]+)"
| rex field=src_ip "(\:\:ffff\:)?(?<src_ip_4>[0-9\.]+)"
| transaction username, src_ip_4 maxspan=10h keepevicted=true startswith=(EventCode=4768)
| where closed_txn=0
| search NOT user="*$@*"
| table _time, ComputerName, username, src_ip_4, service_name, category

```

![](https://academy.hackthebox.com/storage/modules/233/17.png)

# **Boleto de plata**

Adversarios que poseen el hash de contraseña de una cuenta de servicio de destino (por ejemplo,`SharePoint`, `MSSQL`) May Forge Kerberos Boleting Servicing Service (TGS) Entradas, también conocidas como`Silver Tickets`. Los boletos de plata se pueden usar para hacerse pasar por cualquier usuario, pero tienen un alcance más limitado que los boletos dorados, ya que solo permiten a los adversarios acceder a un recurso específico (por ejemplo,`MSSQL`) y el sistema que aloja el recurso.

### **Pasos de ataque:**

- El atacante extrae el hash NTLM de la cuenta de servicio objetivo (o la cuenta de la computadora para`CIFS`acceso) usando herramientas como`Mimikatz`u otras técnicas de descarga de credenciales.
- Genere un boleto de plata: utilizando el hash NTLM extraído, el atacante emplea herramientas como`Mimikatz`Para crear un boleto TGS forjado para el servicio especificado.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image37.png)
    
- El atacante inyecta el TGT falsificado de la misma manera que un ataque de pase el boleto.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image77.png)
    

### **Oportunidades de detección de boletos de plata**

La detección de boletos de servicio forjados (TGS) puede ser un desafío, ya que no hay indicadores simples de ataque. Tanto en los ataques de boletos dorados como de plata, se pueden usar usuarios arbitrarios,`including non-existent ones`. `Event ID 4720 (A user account was created)`puede ayudar a identificar usuarios recién creados. Posteriormente, podemos comparar esta lista de usuarios con usuarios iniciados.

Debido a que no hay validación para los permisos de los usuarios, los usuarios pueden recibir permisos administrativos.`Event ID 4672 (Special Logon)`Se puede emplear para detectar privilegios asignados anómicamente.

# **Detección de boletos de plata con Splunk**

Ahora exploremos cómo podemos identificar boletos de plata, usando Splunk.

### **Detección de boletos de plata con Splunk a través de la correlación del usuario**

Primero creemos una lista de usuarios (`users.csv`) Apalancamiento`Event ID 4720 (A user account was created)`como sigue.

Detección de entradas doradas/boletos de plata

```
index=main latest=1690448444 EventCode=4720
| stats min(_time) as _time, values(EventCode) as EventCode by user
| outputlookup users.csv

```

**Nota**: `users.csv`se puede descargar del`Resources`sección de este módulo (esquina superior derecha) y cargado a Splunk haciendo clic en`Settings` -> `Lookups` -> `Lookup table files` -> `New Lookup Table File`.

Comparemos ahora la lista de arriba con los usuarios registrados de la siguiente manera.

**Periodo de tiempo**: `latest=1690545656`

Detección de entradas doradas/boletos de plata

```
index=main latest=1690545656 EventCode=4624
| stats min(_time) as firstTime, values(ComputerName) as ComputerName, values(EventCode) as EventCode by user
| eval last24h = 1690451977
| where firstTime > last24h
```| eval last24h=relative_time(now(),"-24h@h")```
| convert ctime(firstTime)
| convert ctime(last24h)
| lookup users.csv user as user OUTPUT EventCode as Events
| where isnull(Events)

```

![](https://academy.hackthebox.com/storage/modules/233/18.png)

**Desglose de búsqueda**:

- `index=main latest=1690545656 EventCode=4624`: Este comando filtra eventos de la`main`índice que ocurre antes de una marca de tiempo específica y tener una`EventCode`de`4624`, indicando un inicio de sesión exitoso.
- `| stats min(_time) as firstTime, values(ComputerName) as ComputerName, values(EventCode) as EventCode by user`: Este comando calcula el tiempo de inicio de sesión más temprano para cada usuario, los agrupa por el`user`campo, y crea una tabla con columnas`firstTime`, `ComputerName`, y`EventCode`.
- `| eval last24h = 1690451977`: Este comando define una variable`last24h`y lo asigna un`timestamp`valor. Este valor representa un umbral de tiempo para filtrar los resultados.
- `| where firstTime > last24h`: Este comando filtra los resultados para incluir solo inicios de sesión que ocurrieron después del umbral de tiempo definido en`last24h`.
- `| eval last24h=relative_time(now(),"-24h@h")`: Este comando (comentado) redefiniría el`last24h`variable será exactamente 24 horas antes de la hora actual. Tenga en cuenta que esta línea se comenta con retroceso, por lo que no se ejecutará en esta búsqueda.
- `| convert ctime(firstTime)`: Este comando convierte el`firstTime`Campo desde el tiempo de la época hasta un formato legible por humanos.
- `| convert ctime(last24h)`: Este comando convierte el`last24h`Campo desde el tiempo de la época hasta un formato legible por humanos.
- `| lookup users.csv user as user OUTPUT EventCode as Events`: Este comando realiza un`lookup`usando el`users.csv`archivo, coincide con el`user`campo de los resultados de búsqueda con el`user`campo en el archivo CSV y genera el`EventCode`columna del archivo CSV como un nuevo campo llamado`Events`.
- `| where isnull(Events)`: Este comando filtra los resultados para incluir solo aquellos donde el`Events`El campo es nulo. Esto indica que el usuario no se encontró en el`users.csv`archivo.

### **Detección de boletos de plata con Splunk apuntando a privilegios especiales asignados a un nuevo inicio de sesión**

**Periodo de tiempo**: `latest=1690545656`

Detección de entradas doradas/boletos de plata

```
index=main latest=1690545656 EventCode=4672
| stats min(_time) as firstTime, values(ComputerName) as ComputerName by Account_Name
| eval last24h = 1690451977
```| eval last24h=relative_time(now(),"-24h@h") ```
| where firstTime > last24h
| table firstTime, ComputerName, Account_Name
| convert ctime(firstTime)

```

![](https://academy.hackthebox.com/storage/modules/233/19.png)