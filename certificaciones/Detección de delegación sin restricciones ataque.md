# Detección de delegación sin restricciones/ataques de delegación restringidos

# **Delegación sin restricciones**

`Unconstrained Delegation`es un privilegio que se puede otorgar a cuentas de usuario o cuentas informáticas en un entorno de activo de directorio, lo que permite que un servicio se autentique a otro recurso en nombre de`any`usuario. Esto podría ser necesario cuando, por ejemplo, un servidor web requiere acceso a un servidor de base de datos para realizar cambios en nombre de un usuario.

![](https://academy.hackthebox.com/storage/modules/233/image49.png)

### **Pasos de ataque:**

- El atacante identifica los sistemas en los que la delegación sin restricciones está habilitada para cuentas de servicio.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image19.png)
    
- El atacante obtiene acceso a un sistema con delegación sin restricciones habilitada.
- El atacante extrae boletos de ticket de concesión de ticket (TGT) desde la memoria del sistema comprometido utilizando herramientas como`Mimikatz`.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image3.png)
    

### **Autenticación de Kerberos con delegación sin restricciones**

Cuando la delegación sin restricciones está habilitada, la principal diferencia en la autenticación de Kerberos es que cuando un usuario solicita un boleto TGS para un servicio remoto, el controlador de dominio incrustará el TGT del usuario en el ticket de servicio. Al conectarse al servicio remoto, el usuario presentará no solo el boleto TGS sino también su propio TGT. Cuando el servicio necesita autenticarse a otro servicio en nombre del usuario, presentará el boleto TGT del usuario, que el servicio recibió con el boleto TGS.

![](https://academy.hackthebox.com/storage/modules/233/image51.png)

### **Oportunidades de detección de ataque de delegación sin restricciones**

Los comandos de PowerShell y los filtros de búsqueda LDAP utilizados para el descubrimiento de delegación sin restricciones se pueden detectar mediante el monitoreo de la registro del bloque de script de PowerShell (`Event ID 4104`) y registro de solicitud de LDAP.

El objetivo principal de un ataque de delegación no restringido es recuperar y reutilizar los boletos TGT, por lo que también se puede usar la detección de pases de boletos.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detectar ataques de delegación sin restricciones con Splunk**

Ahora exploremos cómo podemos identificar ataques de delegación sin restricciones, usando Splunk.

**Periodo de tiempo**: `earliest=1690544538 latest=1690544540`

Detección de delegación sin restricciones/ataques de delegación restringidos

```
index=main earliest=1690544538 latest=1690544540 source="WinEventLog:Microsoft-Windows-PowerShell/Operational" EventCode=4104 Message="*TrustedForDelegation*" OR Message="*userAccountControl:1.2.840.113556.1.4.803:=524288*"
| table _time, ComputerName, EventCode, Message

```

![](https://academy.hackthebox.com/storage/modules/233/20.png)

# **Delegación restringida**

`Constrained Delegation`es una característica en Active Directory que permite que los servicios deleguen las credenciales de los usuarios solo a los recursos especificados, reduciendo el riesgo asociado con la delegación sin restricciones. Cualquier cuenta de usuario o computadora que tenga nombres principales de servicio (SPN) establecidos en su`msDS-AllowedToDelegateTo`La propiedad puede hacerse pasar por cualquier usuario en el dominio a esos SPN especiales.

![](https://academy.hackthebox.com/storage/modules/233/image26.png)

### **Pasos de ataque:**

- El atacante identifica los sistemas donde la delegación restringida está habilitada y determina los recursos a los que se les permite delegar.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image35.png)
    
- El atacante obtiene acceso al TGT del principal (usuario o computadora). El TGT se puede extraer de la memoria (volcado de Rubeus) o solicitarse con el hash del principal.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image64.png)
    
- El atacante utiliza la técnica S4U para hacerse pasar por una cuenta de alto privilegio al servicio objetivo (solicitando un boleto TGS).
    
    ![](https://academy.hackthebox.com/storage/modules/233/image48.png)
    
- El atacante inyecta el boleto solicitado y accede a los servicios dirigidos como el usuario de las huelgas.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image60.png)
    

### **Extensiones de protocolo de Kerberos - Servicio para el usuario**

`Service for User to Self (S4U2self)`y`Service for User to Proxy (S4U2proxy)`Permita que un servicio solicite un boleto del Centro de distribución de clave (KDC) en nombre de un usuario. S4U2Self permite que un servicio obtenga un TGS para sí mismo en nombre de un usuario, mientras que S4U2Proxy permite que el servicio obtenga un TGS en nombre de un usuario para un segundo servicio.

S4U2Self fue diseñado para permitir que un usuario solicite un boleto TGS cuando se usó otro método de autenticación en lugar de Kerberos. Es importante destacar que este boleto TGS se puede solicitar en nombre de cualquier usuario, por ejemplo, un administrador.

![](https://academy.hackthebox.com/storage/modules/233/image29.png)

S4U2Proxy fue diseñado para tomar un boleto de referencia y usarlo para solicitar un boleto TGS a cualquier SPN especificado en el`msds-allowedtodelegateto`Opciones para el usuario especificado en la parte S4U2Self.

Con una combinación de S4U2Self y S4u2Proxy, un atacante puede hacerse pasar por cualquier usuario a atender los nombres principales (SPN) establecidos en`msDS-AllowedToDelegateTo`propiedades.

### **Oportunidades de detección de ataque de delegación restringida**

Similar a la delegación sin restricciones, es posible detectar comandos de PowerShell y solicitudes LDAP destinadas a descubrir usuarios y computadoras de delegación restringidas vulnerables.

Para solicitar un boleto TGT para un director, así como un boleto TGS utilizando la técnica S4U, Rubeo hace conexiones con el controlador de dominio. Esta actividad puede detectarse como una conexión de red de proceso inusual al puerto TCP/UDP`88`(Kerberos).

# **Detección de ataques de delegación restringidos con Splunk**

Ahora exploremos cómo podemos identificar ataques de delegación restringidos, usando Splunk.

### **Detección de ataques de delegación restringidos: aprovechando los registros de PowerShell**

**Periodo de tiempo**: `earliest=1690544553 latest=1690562556`

Detección de delegación sin restricciones/ataques de delegación restringidos

```
index=main earliest=1690544553 latest=1690562556 source="WinEventLog:Microsoft-Windows-PowerShell/Operational" EventCode=4104 Message="*msDS-AllowedToDelegateTo*"
| table _time, ComputerName, EventCode, Message

```

![](https://academy.hackthebox.com/storage/modules/233/21.png)

### **Detección de ataques de delegación restringidos: aprovechando los registros de Sysmon**

**Periodo de tiempo**: `earliest=1690562367 latest=1690562556`

Detección de delegación sin restricciones/ataques de delegación restringidos

```
index=main earliest=1690562367 latest=1690562556 source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational"
| eventstats values(process) as process by process_id
| where EventCode=3 AND dest_port=88
| table _time, Computer, dest_ip, dest_port, Image, process

```

![](https://academy.hackthebox.com/storage/modules/233/22.png)