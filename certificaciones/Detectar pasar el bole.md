# Detectar pasar el boleto

# **Pases**

`Pass-the-Ticket (PtT)`es una técnica de movimiento lateral utilizada por los atacantes para moverse lateralmente dentro de una red al abusar de los boletos Kerberos TGT (Ticket Octecting Bicket) y TGS (servicio de concesión de boletos). En lugar de usar hashes NTLM, PTT aprovecha los boletos de Kerberos para autenticarse a otros sistemas y acceder a recursos de red sin necesidad de conocer las contraseñas de los usuarios. Esta técnica permite a los atacantes moverse lateralmente y obtener acceso no autorizado en múltiples sistemas.

### **Pasos de ataque:**

- El atacante gana acceso administrativo a un sistema, ya sea a través de un compromiso inicial o una escalada de privilegios.
- El atacante usa herramientas como`Mimikatz`o`Rubeus`Para extraer tickets Válidos TGT o TGS de la memoria del sistema comprometido.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image9.png)
    
- El atacante envía el boleto extraído para la sesión de inicio de sesión actual. El atacante ahora puede autenticarse en otros sistemas y recursos de red sin necesidad de contraseñas de texto sin formato.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image41.png)
    
    ![](https://academy.hackthebox.com/storage/modules/233/image10.png)
    

### **Proceso de autenticación de Kerberos**

`Kerberos`es un protocolo de autenticación de red utilizado para autenticar de forma segura usuarios y servicios dentro de un entorno de Windows Active Directory (AD). Los siguientes pasos ocurren en el proceso de autenticación de Kerberos:

- El usuario (Cliente) inicia el proceso de autenticación solicitando un boleto de concesión de ticket (TGT) desde el Centro de distribución de clave (KDC), típicamente parte del controlador de dominio de Active Directory.
- El KDC verifica la identidad del usuario (generalmente a través de una contraseña) y emite un TGT encriptado con la clave secreta del usuario. El TGT es válido para un período específico y permite al usuario solicitar boletos de servicio sin necesidad de volver a autorenticar.
- El cliente envía una solicitud de boletos de servicio (TGS-REQ) al KDC para el servicio utilizando el TGT obtenido en el paso anterior.
- El KDC valida el TGT del cliente y, si tiene éxito, emite un ticket de servicio (TGS) encriptado con la clave secreta de la cuenta de servicio y que contiene la identidad del cliente y una clave de sesión. El cliente luego recibe el boleto de servicio (TGS) del KDC.
- El cliente se conecta al servidor y envía el TGS al servidor como parte del proceso de autenticación.

![](https://academy.hackthebox.com/storage/modules/233/image25.png)

### **Eventos de seguridad de Windows relacionados**

Durante el acceso al usuario a los recursos de red, se generan varios registros de eventos de Windows para registrar el proceso de inicio de sesión y las actividades relacionadas.

- `Event ID 4648 (Explicit Credential Logon Attempt)`: Este evento se registra cuando se proporcionan credenciales explícitas (por ejemplo, nombre de usuario y contraseña) durante el inicio de sesión.
- `Event ID 4624 (Logon)`: Este evento indica que un usuario ha iniciado sesión con éxito en el sistema.
- `Event ID 4672 (Special Logon)`: Este evento se registra cuando el inicio de sesión de un usuario incluye privilegios especiales, como ejecutar aplicaciones como administrador.
- `Event ID 4768 (Kerberos TGT Request)`: Este evento se registra cuando un cliente solicita un boleto de concesión de boletos (TGT) durante el proceso de autenticación de Kerberos.
- `Event ID 4769 (Kerberos Service Ticket Request)`: Cuando un cliente solicita un boleto de servicio (boleto TGS) para acceder a un servicio remoto durante el proceso de autenticación de Kerberos, se genera ID del evento 4769.

![](https://academy.hackthebox.com/storage/modules/233/image14.png)

### **Oportunidades de detección de pases de boletos**

La detección de ataques de pase de boleto puede ser un desafío, ya que los atacantes están aprovechando boletos válidos de Kerberos en lugar de hashes de credenciales tradicionales. La distinción clave es que cuando se ejecute el ataque de paso el boleto, el proceso de autenticación de Kerberos será parcial. Por ejemplo, un atacante importa un boleto TGT en una sesión de inicio de sesión y solicita un boleto TGS para un servicio remoto. Desde la perspectiva del controlador de dominio, el TGT importado nunca se solicitó antes del sistema del atacante, por lo que no habrá una ID de evento asociada 4768.

![](https://academy.hackthebox.com/storage/modules/233/image61.png)

Este enfoque se puede convertir en la siguiente detección de Splunk: busque`Event ID 4769 (Kerberos Service Ticket Request)` `or` `Event ID 4770 (Kerberos Service Ticket was renewed)`sin un`Event ID 4768 (Kerberos TGT Request)`desde el mismo sistema dentro de una ventana de tiempo específica.

Otro enfoque es buscar desajustes entre el servicio y las identificaciones de host (en`Event ID 4769`) y la fuente y el destino real IPS (en`Event ID 3`). Tenga en cuenta que habrá varios desajustes legítimos, pero los nombres o servicios de host inusuales deben investigarse más a fondo.

Además, en los casos en que un atacante importa un boleto TGS en la sesión de inicio de sesión, es importante revisarlo`Event ID 4771 (Kerberos Pre-Authentication Failed)`para desajuste entre el tipo de autorautenticación y el código de falla. Por ejemplo,`Pre-Authentication type 2 (Encrypted Timestamp)`con`Failure Code 0x18 (Pre-authentication information was invalid)`Indicaría que el cliente envió un Kerberos As-Req con una marca de tiempo cifrada de preautenticación, pero el KDC no pudo descifrarla.

Es esencial comprender que estas oportunidades de detección deben mejorarse con la detección basada en el comportamiento. En otras palabras, el contexto es vital. Buscando identificaciones de eventos`4769`, `4770`, o`4771`Solo probablemente generará muchos falsos positivos. Correlacione los registros de eventos con los patrones de comportamiento del usuario y del sistema, y considere si hay actividades sospechosas asociadas con el usuario o el sistema involucrado en los registros.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detectar el paso del boleto con Splunk**

Ahora exploremos cómo podemos identificar el paso del boleto, usando Splunk.

**Periodo de tiempo**: `earliest=1690451665 latest=1690451745`

Detectar pasar el boleto

```
index=main earliest=1690392405 latest=1690451745 source="WinEventLog:Security" user!=*$ EventCode IN (4768,4769,4770) | rex field=user "(?<username>[^@]+)"
| rex field=src_ip "(\:\:ffff\:)?(?<src_ip_4>[0-9\.]+)"
| transaction username, src_ip_4 maxspan=10h keepevicted=true startswith=(EventCode=4768)
| where closed_txn=0
| search NOT user="*$@*"
| table _time, ComputerName, username, src_ip_4, service_name, category

```

![](https://academy.hackthebox.com/storage/modules/233/15_.png)

**Desglose de búsqueda**:

- `index=main earliest=1690392405 latest=1690451745 source="WinEventLog:Security" user!=*$ EventCode IN (4768,4769,4770)`: Este comando filtra eventos de la`main`índice que cae dentro del rango de tiempo especificado. Selecciona eventos de la`WinEventLog:Security`fuente, donde el`user`el campo no termina con un dólar (`$`) y el`EventCode`es uno de`4768`, `4769`, o`4770`.
- `| rex field=user "(?<username>[^@]+)"`: Este comando extrae el`username`desde`user`campo usando una expresión regular. Asigna el valor extraído a un nuevo campo llamado`username`.
- `| rex field=src_ip "(\:\:ffff\:)?(?<src_ip_4>[0-9\.]+)"`: Este comando extrae la dirección IPv4 de la`src_ip`campo, incluso si se registra originalmente como una dirección IPv6. Asigna el valor extraído a un nuevo campo llamado`src_ip_4`.
- `| transaction username, src_ip_4 maxspan=10h keepevicted=true startswith=(EventCode=4768)`: Este comando agrupa eventos en transacciones basadas en el`username`y`src_ip_4`campos. Una transacción comienza con un evento que tiene un`EventCode`de`4768`. El`maxspan=10h`El parámetro establece una duración máxima de`10`horas para una transacción. El`keepevicted=true`El parámetro asegura que las transacciones abiertas sin un evento final se incluyan en los resultados.
- `| where closed_txn=0`: Este comando filtra los resultados para incluir solo transacciones abiertas, que no tienen un evento final.
- `| search NOT user="*$@*"`: Este comando filtra los resultados donde el`user`el campo termina con un asterisco () y contiene un letrero (`@`).
- `| table _time, ComputerName, username, src_ip_4, service_name, category`: Este comando muestra los campos especificados en un formato de tabla.