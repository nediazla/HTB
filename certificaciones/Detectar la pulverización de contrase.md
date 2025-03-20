# Detectar la pulverización de contraseña

# **Pulverización de contraseña**

A diferencia de los ataques tradicionales de fuerza bruta, donde un atacante intenta numerosas contraseñas para una sola cuenta de usuario,`password spraying`Distribuye el ataque en múltiples cuentas utilizando un conjunto limitado de contraseñas de uso común o fácilmente adivinable. El objetivo principal es evadir las políticas de bloqueo de cuentas típicamente instituidas por las organizaciones. Estas políticas generalmente bloquean una cuenta después de un número específico de intentos de inicio de sesión fallidos para frustrar los ataques de fuerza bruta en cuentas individuales. Sin embargo, la pulverización de contraseña reduce la posibilidad de activar los bloqueos de la cuenta, ya que cada cuenta de usuario recibe solo unos pocos intentos de contraseña, lo que hace que el ataque sea menos notable.

Un ejemplo de pulverización de contraseña utilizando el[Pulverización](https://github.com/Greenwolf/Spray)La herramienta se puede ver a continuación.

![](https://academy.hackthebox.com/storage/modules/233/image47.png)

### **Oportunidades de detección de pulverización de contraseña**

La detección de la pulverización de contraseña a través de los registros de Windows implica el análisis y el monitoreo de registros de eventos específicos para identificar patrones y anomalías que indican dicho ataque. Un patrón común es múltiples intentos de inicio de sesión fallidos con`Event ID 4625 - Failed Logon`de diferentes cuentas de usuario, pero se originan en la misma dirección IP de origen en un corto período de tiempo.

Otros registros de eventos que pueden ayudar en la detección de pulverización de contraseña incluyen:

- `4768 and ErrorCode 0x6 - Kerberos Invalid Users`
- `4768 and ErrorCode 0x12 - Kerberos Disabled Users`
- `4776 and ErrorCode 0xC000006A - NTLM Invalid Users`
- `4776 and ErrorCode 0xC0000064 - NTLM Wrong Password`
- `4648 - Authenticate Using Explicit Credentials`
- `4771 - Kerberos Pre-Authentication Failed`

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detectar la pulverización de contraseña con Splunk**

Ahora exploremos cómo podemos identificar los intentos de pulverización de contraseña, usando Splunk.

**Periodo de tiempo**: `earliest=1690280680 latest=1690289489`

Detectar la pulverización de contraseña

```
index=main earliest=1690280680 latest=1690289489 source="WinEventLog:Security" EventCode=4625
| bin span=15m _time
| stats values(user) as Users, dc(user) as dc_user by src, Source_Network_Address, dest, EventCode, Failure_Reason

```

![](https://academy.hackthebox.com/storage/modules/233/3.png)

**Desglose de búsqueda**:

- `Filtering by Index, Source, and EventCode`: La búsqueda comienza seleccionando eventos del índice principal donde está la fuente`WinEventLog:Security`y el`EventCode`es`4625`. Este código de evento representa intentos de inicio de sesión fallidos en el registro de eventos de seguridad de Windows.
- `Time Range Filter`: La búsqueda restringe el rango de tiempo de los eventos a los que ocurren entre las marcas de tiempo UNIX 1690280680 y 1690289489. Estas marcas de tiempo representan los momentos más tempranos y últimos en los que ocurrieron los eventos.
- `Time Binning`: El`bin`El comando se usa para crear`time buckets of 15 minutes`duración para cada evento basado en el`_time`campo. Este paso agrupa los eventos en intervalos de 15 minutos, lo que puede ser útil para analizar patrones o tendencias con el tiempo.
- `Statistics`: El`stats`El comando se utiliza para agregar eventos basados en los campos`src`, `Source_Network_Address`, `dest`, `EventCode`, y`Failure_Reason`. Para cada combinación única de estos campos, la búsqueda calcula las siguientes estadísticas:
    - `values(user) as Users`: Todos los valores únicos del`user`campo dentro de cada grupo.
    - `dc(user) as dc_user`: El recuento distintivo de valores únicos del`user`campo dentro de cada grupo. Esto representa el número de diferentes usuarios asociados con los intentos de inicio de sesión fallidos en cada grupo.