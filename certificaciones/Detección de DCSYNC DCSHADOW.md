# Detección de DCSYNC/DCSHADOW

# **Dcsync**

`DCSync`es una técnica explotada por los atacantes para extraer hash de contraseña de los controladores de dominio de Active Directory (DCS). Este método capitaliza el`Replication Directory Changes`Permiso generalmente otorgado a los controladores de dominio, lo que les permite leer todos los atributos del objeto, incluidos los hash de contraseña. Los miembros de los administradores, administradores de dominio y grupos de administración empresarial, o cuentas informáticas en el controlador de dominio, tienen la capacidad de ejecutar DCSYNC para extraer datos de contraseña de Active Directory. Estos datos pueden abarcar las hashes actuales e históricas de cuentas potencialmente valiosas, como KRBTGT y administradores.

### **Pasos de ataque:**

- El atacante asegura el acceso administrativo a un sistema unido por dominio o intensifica los privilegios para adquirir los derechos requeridos para solicitar datos de replicación.
- Utilizando herramientas como Mimikatz, el atacante solicita datos de replicación de dominio utilizando la interfaz drsgetNcChanges, imitando efectivamente un controlador de dominio legítimo.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image73.png)
    
- El atacante puede crear boletos dorados, boletos de plata u optar por emplear ataques de pases de pasos/paso elevado.

### **Oportunidades de detección de DCSYNC**

`DS-Replication-Get-Changes`Las operaciones se pueden registrar con`Event ID 4662`. Sin embargo, un adicional`Audit Policy Configuration`es necesario ya que no está habilitado de forma predeterminada (configuración de computadora/configuración de Windows/configuración de seguridad/acceso avanzado de la política de auditoría/Configuración de DS).

![](https://academy.hackthebox.com/storage/modules/233/image72.png)

Buscar eventos que contengan la propiedad`{1131f6aa-9c07-11d1-f79f-00c04fc2dcd2}`, correspondiente a`DS-Replication-Get-Changes`, como evento`4662`Solo consiste en GUID.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detección de DCSYNC con Splunk**

Ahora exploremos cómo podemos identificar DCSYNC, usando Splunk.

**Periodo de tiempo**: `earliest=1690544278 latest=1690544280`

Detección de DCSYNC/DCSHADOW

```
index=main earliest=1690544278 latest=1690544280 EventCode=4662 Message="*Replicating Directory Changes*"
| rex field=Message "(?P<property>Replicating Directory Changes.*)"
| table _time, user, object_file_name, Object_Server, property

```

![](https://academy.hackthebox.com/storage/modules/233/23.png)

# **Dcshadow**

`DCShadow`es una táctica avanzada empleada por los atacantes para promulgar alteraciones no autorizadas a los objetos de Active Directory, que abarca la creación o modificación de objetos sin producir registros de seguridad estándar. El asalto aprovecha el`Directory Replicator (Replicating Directory Changes)`permiso, concedido habitualmente a los controladores de dominio para tareas de replicación. DCShadow es una técnica clandestina que permite a los atacantes manipular los datos de Active Directory y establecer la persistencia dentro de la red. El registro de un DC Rogue requiere la creación de un nuevo servidor y`nTDSDSA`objetos en la partición de configuración del esquema de anuncios, que exige privilegios del administrador (ya sea dominio o local para el DC) o el`KRBTGT`picadillo.

### **Pasos de ataque:**

- El atacante asegura el acceso administrativo a un sistema unido por dominio o intensifica los privilegios para adquirir los derechos necesarios para solicitar datos de replicación.
- El atacante registra un controlador de dominio deshonesto dentro del dominio, aprovechando el`Directory Replicator`permiso y ejecuta cambios a los objetos AD, como modificar grupos de usuarios a grupos de administradores de dominio.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image43.png)
    
- El controlador de dominio Rogue inicia la replicación con los controladores de dominio legítimos, difundiendo los cambios en todo el dominio.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image42.png)
    

### **Oportunidades de detección de dcshadow**

Para emular un controlador de dominio, DCShadow debe implementar modificaciones específicas en Active Directory:

- `Add a new nTDSDSA object`
- `Append a global catalog ServicePrincipalName to the computer object`

`Event ID 4742 (Computer account was changed)`Los cambios de registro relacionados con los objetos de la computadora, incluido`ServicePrincipalName`.

# **Detección de dcshadow con Splunk**

Ahora exploremos cómo podemos identificar DCShadow, usando Splunk.

**Periodo de tiempo**: `earliest=1690623888 latest=1690623890`

Detección de DCSYNC/DCSHADOW

```
index=main earliest=1690623888 latest=1690623890 EventCode=4742
| rex field=Message "(?P<gcspn>XX\/[a-zA-Z0-9\.\-\/]+)"
| table _time, ComputerName, Security_ID, Account_Name, user, gcspn
| search gcspn=*

```

![](https://academy.hackthebox.com/storage/modules/233/24_.png)