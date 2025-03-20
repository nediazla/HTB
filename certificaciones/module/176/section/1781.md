# GPO Permissions/GPO Files

# **Descripción**

A[Objeto de política grupal (GPO)](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/policy/group-policy-objects)es una colección virtual de configuraciones de políticas que tiene un nombre único.`GPOs`son la herramienta de administración de configuración más utilizada en Active Directory. Cada GPO contiene una colección de cero o más configuraciones de políticas. Están vinculados a un`Organizational Unit`En la estructura de anuncios, para su configuración se aplicará a los objetos que residen en el OU o cualquier niño de la que está vinculado el GPO. Los GPOS se pueden restringir a los objetos que aplican especificando, por ejemplo, un grupo de anuncios (por defecto, se aplica a usuarios autenticados) o un filtro WMI (por ejemplo, aplique solo a las máquinas Windows 10).

Cuando creamos un nuevo GPO, solo los administradores de dominio (y roles privilegiados similares) pueden modificarlo. Sin embargo, dentro de los entornos, encontraremos diferentes delegaciones que permiten cuentas menos privilegiadas para realizar ediciones en los GPO; Aquí es donde se encuentra el problema. Muchas organizaciones tienen GPO que pueden modificar 'usuarios autenticados' o 'usuarios de dominio', lo que implica que cualquier usuario comprometido permitirá al atacante alterar estos GPO. Las modificaciones pueden incluir adiciones de scripts de inicio o una tarea programada para ejecutar un archivo, por ejemplo. Este acceso permitirá que un adversario comprometa todos los objetos de la computadora en el OUS a los que están vinculados los GPO vulnerables.

Del mismo modo, los administradores realizan instalación de software a través de GPOS o configuran scripts de inicio ubicados en acciones de red. Si el recurso compartido de la red está mal configurado, un adversario puede reemplazar el archivo que el sistema ejecuta con uno malicioso. El GPO puede no tener configuraciones erróneas en estos escenarios, solo los permisos de NTFS mal configurados en los archivos implementados.

---

# **Ataque**

No hay un tutorial de ataque disponible aquí: es una simple edición GPO o reemplazo de archivos.

---

# **Prevención**

Una forma de evitar este ataque es bloquear los permisos de GPO para ser modificados por un grupo particular de usuarios solo o por una cuenta específica, ya que esto limitará significativamente la capacidad de quién puede editar el GPO o cambiar sus permisos (en lugar de Todos en administradores de dominio, que en algunas organizaciones pueden ser fácilmente más de 50). Del mismo modo, nunca implementa archivos almacenados en ubicaciones de red para que muchos usuarios puedan modificar los permisos de compartir.

También debemos revisar los permisos de los GPO de manera activa y regular, con la opción de automatizar una tarea que se ejecuta por hora y alertas si se detectan desviaciones de los permisos esperados.

---

# **Detección**

Afortunadamente, es sencillo detectar cuándo se modifica un GPO. Si el servicio de directorio cambia de auditoría está habilitado, entonces la identificación del evento`5136`se generará:

![](https://academy.hackthebox.com/storage/modules/176/A4/GPO-modified.png)

Desde un punto de vista defensivo, si un usuario que es`not`Se espera tener derecho a modificar un GPO de repente aparece aquí, luego se debe levantar una bandera roja.

---

# **Mielero**

Un pensamiento común es que, debido a los fáciles métodos de detección de estos ataques, vale la pena tener un GPO mal configurado en el entorno para que los agentes de amenazas abusen; Esto también es cierto para un archivo implementado, ya que se puede monitorear continuamente para cualquier cambio en el archivo (por ejemplo, verificar constantemente si el valor hash del archivo no ha cambiado). Sin embargo, la implementación de estas técnicas solo se recomienda si una organización es madura y proactiva en responder a vulnerabilidades altas/críticas; Esto se debe a que, en el futuro, se descubre una ruta de escalada a través de alguna modificación GPO, a menos que sea posible mitigarlo en tiempo real, el`trap`Firos para convertirse en el punto más débil.

Sin embargo, al implementar un honeypot usando un GPO mal configurado, considere lo siguiente:

- GPO está vinculado solo a servidores no críticos.
- La automatización continua está en su lugar para monitorear las modificaciones de GPO. - - Si se modifica el archivo GPO, deshabilitaremos al usuario que realiza la modificación de inmediato.
- El GPO debe no unirse automáticamente de todas las ubicaciones si se detecta una modificación.

Considere el siguiente script para demostrar cómo PowerShell puede automatizar esto. En nuestro caso, el Honeypot GPO se identifica por un valor de GUID, y la acción deseada es deshabilitar las cuentas asociadas con este cambio. La razón de las cuentas potencialmente múltiples es que ejecutaremos el script cada 15 minutos como una tarea programada. Entonces, si se usaron numerosos usuarios comprometidos para modificar el GPO en este período de tiempo, los deshabilitaremos al instante. El script tiene una sección de comentarios que se puede usar para enviar un correo electrónico como alerta, pero para un POC, mostraremos la salida en la línea de comando:

Código:powershell

```powershell
# Define filter for the last 15 minutes
$TimeSpan = (Get-Date) - (New-TimeSpan -Minutes 15)

# Search for event ID 5136 (GPO modified) in the past 15 minutes
$Logs = Get-WinEvent -FilterHashtable @{LogName='Security';id=5136;StartTime=$TimeSpan} -ErrorAction SilentlyContinue |`
Where-Object {$_.Properties[8].Value -match "CN={73C66DBB-81DA-44D8-BDEF-20BA2C27056D},CN=POLICIES,CN=SYSTEM,DC=EAGLE,DC=LOCAL"}

if($Logs){
    $emailBody = "Honeypot GPO '73C66DBB-81DA-44D8-BDEF-20BA2C27056D' was modified`r`n"
    $disabledUsers = @()
    ForEach($log in $logs){
        If(((Get-ADUser -identity $log.Properties[3].Value).Enabled -eq $true) -and ($log.Properties[3].Value -notin $disabledUsers)){
            Disable-ADAccount -Identity $log.Properties[3].Value
            $emailBody = $emailBody + "Disabled user " + $log.Properties[3].Value + "`r`n"
            $disabledUsers += $log.Properties[3].Value
        }
    }
    # Send an alert via email - complete the command below
    # Send-MailMessage
    $emailBody
}

```

Veremos la siguiente salida (o cuerpo de correo electrónico si está configurado) si el script detecta que el Honeypot GPO fue modificado:

GPO Permisos/archivos GPO

```powershell
PS C:\scripts> # Define filter for the last 15 minutes$TimeSpan = (Get-Date) - (New-TimeSpan -Minutes 15)# Search for event ID 5136 (GPO modified) in the past 15 minutes$Logs = Get-WinEvent -FilterHashtable @{LogName='Security';id=5136;StartTime=$TimeSpan} -ErrorAction SilentlyContinue |`Where-Object {$_.Properties[8].Value -match "CN={73C66DBB-81DA-44D8-BDEF-20BA2C27056D},CN=POLICIES,CN=SYSTEM,DC=EAGLE,DC=LOCAL"}if($Logs){    $emailBody = "Honeypot GPO '73C66DBB-81DA-44D8-BDEF-20BA2C27056D' was modified`r`n"    $disabledUsers = @()    ForEach($log in $logs){        # Write-Host "User performing the modification is " $log.Properties[3].Value        If(((Get-ADUser -identity $log.Properties[3].Value).Enabled -eq $true) -and ($log.Properties[3].Value -notin $disabledUsers)){            Disable-ADAccount -Identity $log.Properties[3].Value            $emailBody = $emailBody + "Disabled user " + $log.Properties[3].Value + "`r`n"            $disabledUsers += $log.Properties[3].Value        }
    }
    # Send an alert via email    # Send-MailMessage    $emailBody}

Honeypot GPO '73C66DBB-81DA-44D8-BDEF-20BA2C27056D' was modified
Disabled user bob

PS C:\scripts>

```

Como podemos ver arriba, el usuario`bob`fue detectado modificando nuestro Honeypot GPO y, por lo tanto, está deshabilitado. Deshabilitar al usuario creará un evento con ID`4725`:

![](https://academy.hackthebox.com/storage/modules/176/A4/userDisabled.png)