# Detección de querberoasting/aspersación

# **Querberoasting**

`Kerberoasting`es una técnica dirigida a cuentas de servicio en entornos de Active Directory para extraer y descifrar sus hash de contraseña. El ataque explota la forma en que los boletos de servicio Kerberos están encriptados y el uso de contraseñas débiles o fácilmente agrietables para cuentas de servicio. Una vez que un atacante descifra con éxito los hash de contraseña, puede obtener acceso no autorizado a las cuentas de servicio específicas y potencialmente moverse lateralmente dentro de la red.

Un ejemplo de un ataque de querberoasting es usar el[Rubeo](https://github.com/GhostPack/Rubeus) `kerberoast`módulo.

![](https://academy.hackthebox.com/storage/modules/233/image76.png)

### **Pasos de ataque:**

- `Identify Target Service Accounts`: El atacante enumera Active Directory para identificar cuentas de servicio con`Service Principal Names (SPNs)`colocar. Las cuentas de servicio a menudo se asocian con servicios que se ejecutan en la red, como SQL Server, Exchange u otras aplicaciones. El siguiente es un fragmento de código de`Rubeus`Eso está relacionado con este paso.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image2.png)
    
- `Request TGS Tickets`: El atacante utiliza las cuentas de servicio identificadas para solicitar`Ticket Granting Service (TGS)`boletos del`Key Distribution Center (KDC)`. Estos boletos TGS contienen hash de contraseña de la cuenta de servicio cifrado. El siguiente es un fragmento de código de`Rubeus`Eso está relacionado con este paso.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image87.png)
    
- `Offline Brute-Force Attack`: El atacante emplea técnicas de fuerza bruta fuera de línea, utilizando herramientas de agrietamiento de contraseña como`Hashcat`o`John the Ripper`, para intentar descifrar los hash de contraseña encriptados.

### **Proceso de acceso de servicio benigno y eventos relacionados**

Cuando un usuario se conecta a un`MSSQL (Microsoft SQL Server)`base de datos utilizando una cuenta de servicio con un`SPN`, Los siguientes pasos ocurren en el proceso de autenticación de Kerberos:

- `TGT Request`: El usuario (Cliente) inicia el proceso de autenticación solicitando un boleto de concesión de ticket (TGT) desde el Centro de distribución de clave (KDC), típicamente parte del controlador de dominio de Active Directory.
- `TGT Issue`: El KDC verifica la identidad del usuario (generalmente a través de un hash de contraseña) y emite un TGT encriptado con la clave secreta del usuario. El TGT es válido para un período específico y permite al usuario solicitar boletos de servicio sin necesidad de volver a autorenticar.
- `Service Ticket Request`: El cliente envía una solicitud de ticket de servicio (TGS-REQ) al KDC para el SPN del servidor MSSQL utilizando el TGT obtenido en el paso anterior.
- `Service Ticket Issue`: El KDC valida el TGT del cliente y, si tiene éxito, emite un ticket de servicio (TGS) encriptado con la clave secreta de la cuenta de servicio, que contiene la identidad del cliente y una clave de sesión. El cliente luego recibe el TGS.
- `Client Connection`: El cliente se conecta al servidor MSSQL y envía el TGS al servidor como parte del proceso de autenticación.
- `MSSQL Server Validates the TGS`: El servidor MSSQL descifra el TGS utilizando su propia clave secreta para obtener la clave de sesión y la identidad del cliente. Si el TGS es válido y la clave de sesión es correcta, el servidor MSSQL acepta la conexión del cliente y otorga acceso a los recursos solicitados.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image25.png)
    

Tenga en cuenta que los pasos mencionados anteriormente también se pueden observar durante el análisis de tráfico de red:

![](https://academy.hackthebox.com/storage/modules/233/image8.png)

Durante el proceso de autenticación de Kerberos, se generan varios eventos relacionados con la seguridad en el registro de eventos de Windows cuando un usuario se conecta a un servidor MSSQL:

- `Event ID 4768 (Kerberos TGT Request)`: Ocurre cuando la estación de trabajo del cliente solicita un TGT del KDC, generando este evento en el registro de seguridad en el controlador de dominio.
- `Event ID 4769 (Kerberos Service Ticket Request)`: Generado después de que el cliente recibe el TGT y solicita un TGS para el SPN del servidor MSSQL.
- `Event ID 4624 (Logon)`: Iniciar sesión en el registro de seguridad en el servidor MSSQL, lo que indica un inicio de sesión exitoso una vez que el cliente inicia una conexión al servidor MSSQL e inicia sesión utilizando la cuenta de servicio con el SPN para establecer la conexión.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image66.png)
    

### **Oportunidades de detección de kerberoasting**

Dado que la fase inicial de Kerberoasting implica identificar cuentas de servicio objetivo, el monitoreo de la actividad LDAP, como se explica en la sección de reconocimiento de dominio, puede ayudar a identificar consultas LDAP sospechosas.

Un enfoque alternativo se centra en la diferencia entre el acceso al servicio benigno y un ataque de querberoasting. En ambos escenarios, se solicitarán boletos TGS para el servicio, pero solo en el caso de acceso benigno del servicio, el usuario se conectará al servidor y presentará el boleto TGS.

![](https://academy.hackthebox.com/storage/modules/233/image18.png)

La lógica de detección implica encontrar todos los eventos para las solicitudes de TGS y los eventos de inicio de sesión del mismo usuario, luego identificar instancias en las que una solicitud TGS está presente sin un evento de inicio de sesión posterior. En el caso de IIS Service Access utilizando una cuenta de servicio con un SPN, un adicional`4648 (A logon was attempted using explicit credentials)`El evento se generará como un evento de inicio de sesión.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detección de querberoasting con Splunk**

Ahora exploremos cómo podemos identificar la kerberoasting, usando Splunk.

### **Solicitudes de TGS benignas**

Primero, veamos algunas solicitudes benignas de TGS en Splunk.

**Periodo de tiempo**: `earliest=1690388417 latest=1690388630`

Detección de querberoasting/aspersación

```
index=main earliest=1690388417 latest=1690388630 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)
| dedup RecordNumber
| rex field=user "(?<username>[^@]+)"
| table _time, ComputerName, EventCode, name, username, Account_Name, Account_Domain, src_ip, service_name, Ticket_Options, Ticket_Encryption_Type, Target_Server_Name, Additional_Information

```

![](https://academy.hackthebox.com/storage/modules/233/7.png)

**Desglose de búsqueda**:

- `index=main earliest=1690388417 latest=1690388630`: Esto filtra la búsqueda para incluir solo eventos del índice principal que ocurrió entre las marcas de tiempo de época más tempranas y más recientes especificadas.
- `EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)`: Esto filtra aún más la búsqueda para incluir solo eventos con un`EventCode`de`4648` `or`un`EventCode`de`4769`con un`service_name`de`iis_svc`.
- `| dedup RecordNumber`: Esto elimina los eventos duplicados basados en el`RecordNumber`campo.
- `| rex field=user "(?<username>[^@]+)"`: Esto extrae el`username`parte del`user`campo usando una expresión regular y la almacena en un nuevo campo llamado`username`.
- `| table _time, ComputerName, EventCode, name, username, Account_Name, Account_Domain, src_ip, service_name, Ticket_Options, Ticket_Encryption_Type, Target_Server_Name, Additional_Information`: Esto muestra los campos especificados en formato tabular.

### **Detección de kerberoasting - consultas SPN**

**Periodo de tiempo**: `earliest=1690448444 latest=1690454437`

Detección de querberoasting/aspersación

```
index=main earliest=1690448444 latest=1690454437 source="WinEventLog:SilkService-Log"
| spath input=Message
| rename XmlEventData.* as *
| table _time, ComputerName, ProcessName, DistinguishedName, SearchFilter
| search SearchFilter="*(&(samAccountType=805306368)(servicePrincipalName=*)*"

```

![](https://academy.hackthebox.com/storage/modules/233/8.png)

### **Detección de kerberoasting - Solicitudes TGS**

**Periodo de tiempo**: `earliest=1690450374 latest=1690450483`

Detección de querberoasting/aspersación

```
index=main earliest=1690450374 latest=1690450483 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)
| dedup RecordNumber
| rex field=user "(?<username>[^@]+)"
| bin span=2m _time
| search username!=*$ | stats values(EventCode) as Events, values(service_name) as service_name, values(Additional_Information) as Additional_Information, values(Target_Server_Name) as Target_Server_Name by _time, username
| where !match(Events,"4648")

```

![](https://academy.hackthebox.com/storage/modules/233/9.png)

**Desglose de búsqueda**:

- `index=main earliest=1690450374 latest=1690450483 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)`: Filtra la búsqueda para incluir solo eventos del`main`índice que ocurrió entre las marcas de tiempo de época más tempranas y más recientes especificadas. Filtra aún más la búsqueda para incluir solo eventos con un`EventCode`de`4648`o un`EventCode`de`4769`con un`service_name`de`iis_svc`.
- `| dedup RecordNumber`: Elimina eventos duplicados basados en el`RecordNumber`campo.
- `| rex field=user "(?<username>[^@]+)"`: Extrae el`username`parte del`user`campo usando una expresión regular y la almacena en un nuevo campo llamado`username`.
- `| bin span=2m _time`: Contiene los eventos en intervalos de 2 minutos basados en el`_time`campo.
- `| search username!=*$`: Filtra eventos donde el`username`El campo termina con un`$`.
- `| stats values(EventCode) as Events, values(service_name) as service_name, values(Additional_Information) as Additional_Information, values(Target_Server_Name) as Target_Server_Name by _time, username`: Agrupa los eventos por el`_time`y`username`campos y crea nuevos campos que contienen el`unique`valores de la`EventCode`, `service_name`, `Additional_Information`, y`Target_Server_Name`campos dentro de cada grupo.
- `| where !match(Events,"4648")`: Filtra eventos que tienen el valor`4648`en el campo de los eventos.

### **Detección de kerberoasting utilizando transacciones: solicitudes TGS**

**Periodo de tiempo**: `earliest=1690450374 latest=1690450483`

Detección de querberoasting/aspersación

```
index=main earliest=1690450374 latest=1690450483 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)
| dedup RecordNumber
| rex field=user "(?<username>[^@]+)"
| search username!=*$ | transaction username keepevicted=true maxspan=5s endswith=(EventCode=4648) startswith=(EventCode=4769)
| where closed_txn=0 AND EventCode = 4769
| table _time, EventCode, service_name, username

```

![](https://academy.hackthebox.com/storage/modules/233/10.png)

**Desglose de búsqueda**:

Esta consulta de búsqueda de Splunk es diferente de la consulta anterior principalmente debido al uso de la`transaction`Comando, que agrupa eventos en transacciones basadas en campos y criterios especificados.

- `index=main earliest=1690450374 latest=1690450483 EventCode=4648 OR (EventCode=4769 AND service_name=iis_svc)`: Filtra la búsqueda para incluir solo eventos del`main`índice que ocurrió entre las marcas de tiempo de época más tempranas y más recientes especificadas. Filtra aún más la búsqueda para incluir solo eventos con un`EventCode`de`4648`o un`EventCode`de`4769`con un`service_name`de`iis_svc`.
- `| dedup RecordNumber`: Elimina eventos duplicados basados en el`RecordNumber`campo.
- `| rex field=user "(?<username>[^@]+)"`: Extrae el`username`parte del`user`campo usando una expresión regular y la almacena en un nuevo campo llamado`username`.
- `| search username!=*$`: Filtra eventos donde el`username`El campo termina con un`$`.
- `| transaction username keepevicted=true maxspan=5s endswith=(EventCode=4648) startswith=(EventCode=4769)`: Grupe eventos en`transactions`basado en el`username`campo. El`keepevicted=true`La opción incluye eventos que no cumplen con los criterios de transacción. El`maxspan=5s`La opción establece la duración máxima de tiempo de una transacción a 5 segundos. El`endswith=(EventCode=4648)`y`startswith=(EventCode=4769)`Las opciones especifican que las transacciones deben comenzar con un evento con`EventCode 4769`y termina con un evento con`EventCode 4648`.
- `| where closed_txn=0 AND EventCode = 4769`: Filtra los resultados para solo incluir transacciones que no están cerradas (`closed_txn=0`) y tener un`EventCode`de`4769`.
- `| table _time, EventCode, service_name, username`: Muestra los eventos restantes en formato tabular con los campos especificados.

Esta consulta se centra en identificar eventos con un`EventCode`de`4769`que son parte de una transacción incompleta (es decir, no terminaron con un evento con`EventCode 4648`dentro del`5`-Segundo de la ventana).

# **Asombrado**

`ASREPRoasting`es una técnica utilizada en entornos de Active Directory para dirigir las cuentas de los usuarios sin habilitado la preautenticación. En Kerberos, la preautenticación es una característica de seguridad que requiere que los usuarios prueben su identidad antes de emitir el TGT. Sin embargo, ciertas cuentas de los usuarios, como aquellas con delegación no restringida, no tienen habilitados la preautenticación, lo que las hace susceptibles a los ataques de aspersión.

![](https://academy.hackthebox.com/storage/modules/233/image40.png)

### **Pasos de ataque:**

- `Identify Target User Accounts`: El atacante identifica las cuentas de los usuarios sin autorización previa habilitada. El siguiente es un fragmento de código de`Rubeus`Eso está relacionado con este paso.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image13.png)
    
- `Request AS-REQ Service Tickets`: El atacante inicia una solicitud de boleto de servicio AS-Req para cada cuenta de usuario de destino identificada. El siguiente es un fragmento de código de`Rubeus`Eso está relacionado con este paso.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image24.png)
    
- `Offline Brute-Force Attack`: El atacante captura el TGTS encriptado y emplea técnicas de fuerza bruta fuera de línea para intentar descifrar los hash de contraseña.

### **Pre-autorenticación de Kerberos**

`Kerberos pre-authentication`es un mecanismo de seguridad adicional en el protocolo de autenticación Kerberos que mejora la protección de las credenciales del usuario durante el proceso de autenticación. Cuando un usuario intenta acceder a un recurso o servicio de red, el cliente envía una solicitud de autenticación como REQ al KDC.

Si se habilita la pre-autoridad, esta solicitud también contiene una marca de tiempo cifrada (`pA-ENC-TIMESTAMP`). El KDC intenta descifrar esta marca de tiempo utilizando el hash de contraseña del usuario y, si tiene éxito, emite un TGT al usuario.

![](https://academy.hackthebox.com/storage/modules/233/image79.png)

Cuando el KDC no está desactivada previa a la autenticación, el KDC no existe la validación de la marca de tiempo, lo que permite a los usuarios solicitar un boleto TGT sin conocer la contraseña del usuario.

![](https://academy.hackthebox.com/storage/modules/233/image78.png)

### **Oportunidades de detección de repertirización**

Similar a la kerberoasting, la fase inicial de aspersación de asignación implica la identificación de cuentas de los usuarios con delegación sin restricciones habilitadas o cuentas sin preautenticación, que puede detectarse mediante el monitoreo de LDAP.

Autenticación de Kerberos`Event ID 4768 (TGT Request)`contiene un`PreAuthType`Atributo en la parte de información adicional del evento que indica si la preautenticación está habilitada para una cuenta.

# **Detectar como se reproadía con Splunk**

Ahora exploremos cómo podemos identificar como reprovivación, usando Splunk.

### **Detección de cuentas de consulta como desactivación con discapacitado previo a la autor**

**Periodo de tiempo**: `earliest=1690392745 latest=1690393283`

Detección de querberoasting/aspersación

```
index=main earliest=1690392745 latest=1690393283 source="WinEventLog:SilkService-Log"
| spath input=Message
| rename XmlEventData.* as *
| table _time, ComputerName, ProcessName, DistinguishedName, SearchFilter
| search SearchFilter="*(samAccountType=805306368)(userAccountControl:1.2.840.113556.1.4.803:=4194304)*"

```

![](https://academy.hackthebox.com/storage/modules/233/11.png)

### **Detección de solicitudes de reproading-TGT de cuentas con discapacitado previo**

**Periodo de tiempo**: `earliest=1690392745 latest=1690393283`

Detección de querberoasting/aspersación

```
index=main earliest=1690392745 latest=1690393283 source="WinEventLog:Security" EventCode=4768 Pre_Authentication_Type=0
| rex field=src_ip "(\:\:ffff\:)?(?<src_ip>[0-9\.]+)"
| table _time, src_ip, user, Pre_Authentication_Type, Ticket_Options, Ticket_Encryption_Type

```

![](https://academy.hackthebox.com/storage/modules/233/12.png)

**Desglose de búsqueda**:

- `index=main earliest=1690392745 latest=1690393283 source="WinEventLog:Security" EventCode=4768 Pre_Authentication_Type=0`: Filtra la búsqueda para incluir solo eventos del`main`índice que ocurrió entre las marcas de tiempo de época más tempranas y más recientes especificadas. Filtra aún más la búsqueda para incluir solo eventos con una fuente de`WinEventLog:Security`, un`EventCode`de`4768`, y un`Pre_Authentication_Type`de`0`.
- `| rex field=src_ip "(\:\:ffff\:)?(?<src_ip>[0-9\.]+)"`: Usa una expresión regular para extraer el`src_ip`(Dirección IP de origen) Campo. La expresión coincide con una opcional`"::ffff:"`Prefijo seguido de una dirección IP en notación decimal punteada. Este paso maneja las direcciones IPv6 mapeadas de IPv4 extrayendo la porción IPv4.
- `| table _time, src_ip, user, Pre_Authentication_Type, Ticket_Options, Ticket_Encryption_Type`: Muestra los eventos restantes en formato tabular con los campos especificados.