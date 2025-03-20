# GPP Passwords

# **Descripción**

`SYSVOL`es una parte de la red en todos los controladores de dominio, que contiene scripts de inicio de sesión, datos de políticas grupales y otros datos de dominio requeridos. Adedantes todas las políticas grupales en`\\<DOMAIN>\SYSVOL\<DOMAIN>\Policies\`. Cuando Microsoft lo lanzó con Windows Server 2008,`Group Policy Preferences` (`GPP`) introdujo la capacidad de almacenar y usar credenciales en varios escenarios, todos los cuales se almacena en el directorio de políticas en`SYSVOL`.

Durante los compromisos, podríamos encontrar tareas y scripts programados ejecutados bajo un usuario en particular y contener el nombre de usuario y una versión cifrada de la contraseña en archivos de política XML. La clave de cifrado que AD usa para cifrar los archivos de política XML (el`same`Para todos los entornos de Active Directory) se lanzó en Microsoft Docs, lo que permite a cualquier persona descifrar las credenciales almacenadas en los archivos de políticas. Cualquiera puede descifrar las credenciales porque el`SYSVOL`La carpeta es accesible para todos los 'usuarios autenticados' en el dominio, que incluye usuarios y computadoras. Microsoft publicó el[Clave privada AES en MSDN](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-gppref/2c15cbf0-f086-4c74-8b70-1f2fa45dd4be?redirectedfrom=MSDN):

![](https://academy.hackthebox.com/storage/modules/176/A3/GPPkey1.png)

Además, como referencia, este es cómo se ve un archivo XML de ejemplo que contiene una contraseña cifrada (tenga en cuenta que se llama a la propiedad`cpassword`):

![](https://academy.hackthebox.com/storage/modules/176/A3/GPPcPass.png)

---

# **Ataque**

Abusar`GPP Passwords`, usaremos el[Get-GPPPassword](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1)función de`PowerSploit`, que analiza automáticamente todos los archivos XML en la carpeta de políticas en`SYSVOL`, recogiendo aquellos con el`cpassword`propiedad y descifrarlos una vez detectados:

Contraseñas de GPP

```powershell
PS C:\Users\bob\Downloads> Import-Module .\Get-GPPPassword.ps1
PS C:\Users\bob\Downloads> Get-GPPPassword

UserName  : svc-iis
NewName   : [BLANK]
Password  : abcd@123
Changed   : [BLANK]
File      : \\EAGLE.LOCAL\SYSVOL\eagle.local\Policies\{73C66DBB-81DA-44D8-BDEF-20BA2C27056D}\
            Machine\Preferences\Groups\Groups.xml
NodeName  : Groups
Cpassword : qRI/NPQtItGsMjwMkhF7ZDvK6n9KlOhBZ/XShO2IZ80

```

![](https://academy.hackthebox.com/storage/modules/176/A3/GPPPass.png)

---

# **Prevención**

Una vez que la clave de cifrado se hizo pública y comenzó a ser abusado, Microsoft lanzó un parche (`KB2962486)`en 2014 para prevenir`caching credentials`en GPP. Por lo tanto, GPP ya no debería almacenar contraseñas en nuevos entornos parcheados. Sin embargo, desafortunadamente, hay una multitud de entornos de Active Directory construidos después de 2015, que por alguna razón contienen credenciales en`SYSVOL`. Por lo tanto, se recomienda altamente evaluar y revisar continuamente el entorno para garantizar que no se expusen credenciales aquí.

Es crucial saber que si una organización construyó su entorno publicitario antes de 2014, es probable que sus credenciales aún estén almacenadas en caché porque el parche no borra las credenciales almacenadas existentes (solo evita el almacenamiento en caché de otras nuevas).

---

# **Detección**

Hay dos técnicas de detección para este ataque:

- Acceder al archivo XML que contiene las credenciales debe ser una bandera roja si estamos auditando el acceso al archivo; Esto es más realista (debido al volumen de lo contrario) con respecto a la detección si se trata de un archivo XML ficticio, no asociado con ningún GPO. En este caso, no habrá razón para que nadie toque este archivo, y es probable que cualquier intento sea sospechoso. Como lo demuestra`Get-GPPPasswords`, analiza todos los archivos XML en la carpeta de políticas. Para la auditoría, podemos generar un evento cada vez que un usuario lea el archivo:

![](https://academy.hackthebox.com/storage/modules/176/A3/audit1.png)

Una vez que se habilita la auditoría, cualquier acceso al archivo generará un evento con la ID`4663`:

![](https://academy.hackthebox.com/storage/modules/176/A3/audit2.png)

- Los intentos de inicio de sesión (fallidos o exitosos, dependiendo de si la contraseña está actualizada) del usuario cuyas credenciales están expuestas es otra forma de detectar el abuso de este ataque; Esto debería generar uno de los eventos`4624` (`successful logon`), `4625` (`failed logon`), o`4768` (`TGT requested`). Un inicio de sesión exitoso con la cuenta de nuestro escenario de ataque generaría el siguiente evento en el controlador de dominio:

![](https://academy.hackthebox.com/storage/modules/176/A3/audit3.png)

En el caso de una cuenta de servicio, podemos correlacionar los intentos de inicio de sesión con el dispositivo desde el cual se origina el intento de autenticación, ya que esto debería ser fácil de detectar, suponiendo que sabemos dónde se usan ciertas cuentas (principalmente si el inicio de sesión se originó en una estación de trabajo, que es un comportamiento anormal para una cuenta de servicio).

---

# **Mielero**

Este ataque ofrece una excelente oportunidad para configurar una trampa: podemos usar un usuario semipivilegiado con un`wrong password`. Las cuentas de servicio brindan una oportunidad más realista porque:

- Por lo general, se espera que la contraseña sea vieja, sin modificaciones recientes o regulares.
- Es fácil asegurarse de que el último cambio de contraseña sea más antiguo que cuando el archivo GPP XML se modificó por última vez. Si la contraseña del usuario se cambia después de modificar el archivo, entonces ningún adversario intentará iniciar sesión con esta cuenta (es probable que la contraseña ya no sea válida).
- Programe que el usuario realice cualquier tarea ficticia para asegurarse de que haya intentos de inicio de sesión recientes.

Cuando hacemos lo anterior, podemos configurar una alerta de que si se producen intentos de inicio de sesión exitosos o fallidos con esta cuenta de servicio, debe ser maliciosa (suponiendo que blancamos el inicio de sesión de tarea ficticia que simula la actividad de inicio de sesión en la alerta).

Debido a que la contraseña proporcionada es incorrecta, principalmente esperaríamos intentos de inicio de sesión fallidos. Tres ID de evento (`4625`, `4771`, y`4776`) puede indicar esto; Así es como buscan nuestro entorno de juegos si un atacante intenta autenticarse con una contraseña incorrecta:

- `4625`

![](https://academy.hackthebox.com/storage/modules/176/A3/honeypot4dot3.png)

- `4771`

![](https://academy.hackthebox.com/storage/modules/176/A3/honeypot4.png)

- `4776`

![](https://academy.hackthebox.com/storage/modules/176/A3/honeypot4dot2.png)

### **Preguntas y respuestas**

1) Conéctese al objetivo y ejecute la función PowersPloPT GPPPPassword. ¿Cuál es la contraseña del usuario SVC-IIS?

Importemos el[Get-GPPPassword](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1)función de`PowerSploit`, que analiza automáticamente todos los archivos XML en la carpeta de políticas en`SYSVOL` .

Este error ocurre porque la política de ejecución en el sistema está configurada para restringir la ejecución del script, evitando el`Get-GPPPassword.ps1`script de ser cargado. Esta es una característica de seguridad en PowerShell para evitar la ejecución de scripts potencialmente maliciosos.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FboOMq4Q4IQzsWcioNhG9%252FScreenshot%2817%29.png%3Falt%3Dmedia%26token%3Daed96547-c202-4925-aeae-a652a0831bf8&width=768&dpr=4&quality=100&sign=1289d5c3&sv=2)

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FFyNJbWymDTem8bfpfNme%252FScreenshot%2818%29.png%3Falt%3Dmedia%26token%3D6949ad37-7c9e-4254-bd81-d065643837c4&width=768&dpr=4&quality=100&sign=90aeb203&sv=2)

Respuesta: ABCD@123

2) Después de ejecutar el ataque anterior, conéctese a DC1 (172.16.18.3) como 'htb-student: htb_@cademy_stdnt!' y mire el espectador de registros en eventos. ¿Cuál es la máscara de acceso de los eventos generados?

Conectemos a DC1 (172.16.18.3) desde nuestra máquina de Windows con escritorio remoto.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FSCxcRwBrQxXY19I0IYUS%252FScreenshot%2815%29.png%3Falt%3Dmedia%26token%3D20c7bc60-cd6c-4a01-bc2f-4a42f554ba81&width=768&dpr=4&quality=100&sign=30dc88a8&sv=2)

A continuación, abra el visor de eventos y filtre por ID de evento 4663.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252Fpwctv6jLAA5hAogoUHFE%252FScreenshot%2819%29.png%3Falt%3Dmedia%26token%3De9a73c47-76ab-480d-8406-f52a9321b5e9&width=768&dpr=4&quality=100&sign=b56c4e6a&sv=2)

Respuesta: 0x80