# Credentials in Object Properties

# **Descripción**

Los objetos en Active Directory tienen una gran cantidad de diferentes propiedades; Por ejemplo, un`user`El objeto puede contener propiedades que contienen información como:

- Es la cuenta activa
- ¿Cuándo expira la cuenta?
- ¿Cuándo fue el último cambio de contraseña?
- ¿Cuál es el nombre de la cuenta?
- Ubicación de la oficina para el empleado y número de teléfono

Cuando los administradores crean cuentas, completan esas propiedades. Una práctica común en el pasado era agregar la contraseña del usuario (o la cuenta de servicio) en el`Description`o`Info`Propiedades, pensando que se necesitan derechos administrativos en AD para ver estas propiedades. Sin embargo,`every`El usuario del dominio puede leer la mayoría de las propiedades de un objeto (incluido`Description`y`Info`).

---

# **Ataque**

Un simple script de PowerShell puede consultar todo el dominio buscando términos/cadenas de búsqueda específicos en el`Description`o`Info`campos:

Código:powershell

```powershell
Function SearchUserClearTextInformation
{
    Param (
        [Parameter(Mandatory=$true)]
        [Array] $Terms,

        [Parameter(Mandatory=$false)]
        [String] $Domain
    )

    if ([string]::IsNullOrEmpty($Domain)) {
        $dc = (Get-ADDomain).RIDMaster
    } else {
        $dc = (Get-ADDomain $Domain).RIDMaster
    }

    $list = @()

    foreach ($t in $Terms)
    {
        $list += "(`$_.Description -like `"*$t*`")"
        $list += "(`$_.Info -like `"*$t*`")"
    }

    Get-ADUser -Filter * -Server $dc -Properties Enabled,Description,Info,PasswordNeverExpires,PasswordLastSet |
        Where { Invoke-Expression ($list -join ' -OR ') } |
        Select SamAccountName,Enabled,Description,Info,PasswordNeverExpires,PasswordLastSet |
        fl
}

```

Ejecutaremos el guión para buscar la cadena`pass`, para encontrar la contraseña`Slavi123`en el`Description`Propiedad del usuario`bonni`:

Credenciales en propiedades de objetos

```powershell
PS C:\Users\bob\Downloads> SearchUserClearTextInformation -Terms "pass"

SamAccountName       : bonni
Enabled              : True
Description          : pass: Slavi123
Info                 :
PasswordNeverExpires : True
PasswordLastSet      : 05/12/2022 15.18.05

```

![](https://academy.hackthebox.com/storage/modules/176/A6/A6creds.png)

---

# **Prevención**

Tenemos muchas opciones para evitar este ataque/configuración errónea:

- `Perform` `continuous assessments`para detectar el problema de almacenar credenciales en propiedades de los objetos.
- `Educate`Empleados con altos privilegios para evitar almacenar credenciales en propiedades de los objetos.
- `Automate`En la medida de lo posible del proceso de creación de usuarios para garantizar que los administradores no manejen las cuentas manualmente, reduciendo el riesgo de introducir credenciales codificados en objetos de usuario.

---

# **Detección**

El comportamiento de los usuarios de Baseline es la mejor técnica para detectar el abuso de credenciales expuestas en las propiedades de los objetos. Aunque esto puede ser complicado para las cuentas de los usuarios regulares, es más fácil activar una alerta para los administradores/cuentas de servicio cuyo comportamiento se puede entender y es más fácil. Las herramientas automatizadas que monitorean el comportamiento del usuario han mostrado un mayor éxito en la detección de inicios de sesión anormales. En el ejemplo anterior, suponiendo que las credenciales proporcionadas estén actualizadas, esperaríamos eventos con ID de eventos`4624`/`4625`(inicio de sesión fallido y exitoso) y`4768`(Kerberos TGT solicitado). A continuación se muestra un ejemplo de identificación de eventos`4768`:

![](https://academy.hackthebox.com/storage/modules/176/A6/Detect1.png)

Desafortunadamente, la identificación del evento`4738`Generado cuando se modifica un objeto de usuario no muestra la propiedad específica que se alteró, ni proporciona los nuevos valores de propiedades. Por lo tanto, no podemos usar este evento para detectar si los administradores agregan credenciales a las propiedades de los objetos.

---

# **Mielero**

Almacenar credenciales en propiedades de los objetos es una excelente técnica de honeypot para entornos no muy maduros. Si tiene dificultades con la higiene cibernética básica, es más probable que se espera que tengan tales problemas (almacenamiento de credenciales en las propiedades de los objetos) en un entorno AD. Para configurar un usuario de honeypot, debemos garantizar los siguientes:

- La contraseña/credencial está configurada en el`Description`campo, ya que es el más fácil de recoger por cualquier adversario.
- La contraseña proporcionada es falsa/incorrecta.
- La cuenta está habilitada y tiene intentos de inicio de sesión recientes.
- Si bien podemos usar un usuario regular o una cuenta de servicio, es más probable que las cuentas de servicio tengan esto expuesto ya que los administradores tienden a crearlos manualmente. Por el contrario, los sistemas automatizados de recursos humanos a menudo hacen cuentas de los empleados (y los empleados probablemente ya han cambiado la contraseña).
- La cuenta tiene la última contraseña configurada hace más de 2 años (hace que sea más creíble que la contraseña probablemente funcione).

Debido a que la contraseña proporcionada es incorrecta, principalmente esperaríamos intentos de inicio de sesión fallidos; tres ID de evento (`4625`, `4771`, y`4776`) puede indicar esto. Así es como se ven en nuestro entorno de juegos si un atacante intenta autenticarse con la cuenta`svc-iis`y una contraseña incorrecta:

- `4625`

![](https://academy.hackthebox.com/storage/modules/176/A6/honeypot4dot3.png)

- `4771`

![](https://academy.hackthebox.com/storage/modules/176/A6/honeypot4.png)

- `4776`

![](https://academy.hackthebox.com/storage/modules/176/A6/honeypot4dot2.png)

## **Preguntas y respuestas**

1) Conéctese al objetivo y use un script para enumerar los campos de propiedades de objetos. ¿Qué contraseña se puede encontrar en el campo Descripción del usuario de Bonni?

Comencemos creando el script que usaremos para buscar cadenas.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FTGdJdYv0xK5xfbPRPuiI%252FScreenshot%282%29.png%3Falt%3Dmedia%26token%3D847fbaf1-12ef-4510-9f4b-e0281ba304bf&width=768&dpr=4&quality=100&sign=dc184e4f&sv=2)

A continuación, usemos la función para buscar la contraseña del usuario Bonni.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FWoitOvkaGZ3WFWnVkilC%252FScreenshot%283%29.png%3Falt%3Dmedia%26token%3D8912c261-8e37-4518-b176-d0f9543bc5c2&width=768&dpr=4&quality=100&sign=dc282c83&sv=2)

Respuesta: Slavi1234

2) Usando la contraseña descubierta en la pregunta anterior, intente autenticarse en DC1 como el usuario de Bonni. ¿Es válida la contraseña?

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FGibBBUeNjxrelnkGB8ik%252FScreenshot%284%29.png%3Falt%3Dmedia%26token%3Dc3050d53-b601-4c4c-8dd3-b4b0b320287e&width=768&dpr=4&quality=100&sign=572ce3b7&sv=2)

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FNdgUxv9dZd1f3EEHLZMi%252FScreenshot%285%29.png%3Falt%3Dmedia%26token%3D2b620c7e-d274-4435-9a1c-82b571f72118&width=768&dpr=4&quality=100&sign=b1de97d6&sv=2)

Respuesta: No

3) Conéctese a DC1 como 'htb-student: htb_@cademy_stdnt!' y mire el espectador de registros en eventos. ¿Cuál es el TargetSid del usuario de Bonni?

Abra el visor de eventos en DC1 y filtemos para el ID del evento 4771.

No pude localizar al usuario objetivo con el visor de eventos, así que usé WMIC en su lugar.

Copiar

```
wmic useraccount where name='bonni' get name,sid
```

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FIPLYbCIZPKT828Du360B%252FScreenshot%286%29.png%3Falt%3Dmedia%26token%3D173da576-3c40-4049-9d39-932a3a279ccb&width=768&dpr=4&quality=100&sign=afdaf5ce&sv=2)

Respuesta: S-1-5-21-1518138621-4282902758-7524445584-3102