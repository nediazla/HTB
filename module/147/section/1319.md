# Passwd, Shadow & Opasswd

Las distribuciones basadas en Linux pueden usar muchos mecanismos de autenticación diferentes. Uno de los mecanismos estándar más utilizados y[Módulos de autenticación conectables](https://web.archive.org/web/20220622215926/http://www.linux-pam.org/Linux-PAM-html/Linux-PAM_SAG.html) (`PAM`). Los módulos utilizados para esto se llaman`pam_unix.so`o`pam_unix2.so`y están ubicados en`/usr/lib/x86_x64-linux-gnu/security/`En Distribuciones basadas en Debian. Estos módulos administran información del usuario, autenticación, sesiones, contraseñas actuales y contraseñas antiguas. Por ejemplo, si queremos cambiar la contraseña de nuestra cuenta en el sistema Linux con`passwd`Se llama a Pam, que toma las precauciones y tiendas apropiadas y maneja la información en consecuencia.

El`pam_unix.so`El módulo estándar para la administración utiliza llamadas API estandarizadas de las bibliotecas y archivos del sistema para actualizar la información de la cuenta. Los archivos estándar que se leen, administran y actualizan son`/etc/passwd`y`/etc/shadow`. PAM también tiene muchos otros módulos de servicio, como LDAP, Monte o Kerberos.

---

# **PASSWD FILE**

El`/etc/passwd`El archivo contiene información sobre cada usuario existente en el sistema y puede ser leído por todos los usuarios y servicios. Cada entrada en el`/etc/passwd`El archivo identifica a un usuario en el sistema. Cada entrada tiene siete campos que contienen una forma de una base de datos con información sobre el usuario en particular, donde un colon (`:`) separa la información. En consecuencia, dicha entrada puede verse algo así:

### **Formato de aprobación**

| **`cry0l1t3`** | **`:`** | **`x`** | **`:`** | **`1000`** | **`:`** | **`1000`** | **`:`** | **`cry0l1t3,,,`** | **`:`** | **`/home/cry0l1t3`** | **`:`** | **`/bin/bash`** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Nombre de inicio de sesión |  | Información de contraseña |  | Uid |  | Guía |  | Nombre completo/comentarios |  | Directorio de inicio |  | Caparazón |

El campo más interesante para nosotros es el campo de información de contraseña en esta sección porque puede haber diferentes entradas aquí. Uno de los casos más raros que podemos encontrar solo en sistemas muy antiguos es el hash de la contraseña encriptada en este campo. Los sistemas modernos tienen los valores hash almacenados en el`/etc/shadow`Archivo, al que volveremos más tarde. Sin embargo,`/etc/passwd`es legible en todo el sistema, dando a los atacantes la posibilidad de descifrar las contraseñas si los hash se almacenan aquí.

Por lo general, encontramos el valor`x`en este campo, lo que significa que las contraseñas se almacenan en forma encriptada en el`/etc/shadow`archivo. Sin embargo, también puede ser que el`/etc/passwd`El archivo se puede escribir por error. Esto nos permitiría borrar este campo para el usuario`root`para que el campo de información de contraseña esté vacío. Esto hará que el sistema no envíe una solicitud de contraseña cuando un usuario intente iniciar sesión como`root`.

### **Edición /etc /passwd - antes**

Passwd, shadow & upasswd

```
root:x:0:0:root:/root:/bin/bash

```

### **Edición /etc /passwd - después**

Passwd, shadow & upasswd

```
root::0:0:root:/root:/bin/bash

```

### **Root sin contraseña**

Passwd, shadow & upasswd

```
[cry0l1t3@parrot]─[~]$ head -n 1 /etc/passwdroot::0:0:root:/root:/bin/bash

[cry0l1t3@parrot]─[~]$ su[root@parrot]─[/home/cry0l1t3]#

```

Aunque los casos mostrados rara vez ocurrirán, aún debemos prestar atención y observar las brechas de seguridad porque hay aplicaciones que requieren que establezcamos permisos específicos para carpetas completas. Si el administrador tiene poca experiencia con Linux o las aplicaciones y sus dependencias, el administrador puede dar permisos de escritura al`/etc`directorio y olvida corregirlos.

---

# **Archivo sombra**

Dado que leer los valores hash de contraseña puede poner todo el sistema en peligro, el archivo`/etc/shadow`fue desarrollado, que tiene un formato similar a`/etc/passwd`pero solo es responsable de las contraseñas y su administración. Contiene toda la información de contraseña para los usuarios creados. Por ejemplo, si no hay entrada en el`/etc/shadow`Presentar a un usuario en`/etc/passwd`, el usuario se considera inválido. El`/etc/shadow`El archivo también solo es legible por usuarios que tienen derechos de administrador. El formato de este archivo se divide en`nine fields`:

### **Formato de sombra**

| **`cry0l1t3`** | **`:`** | **`$6$wBRzy$...SNIP...x9cDWUxW1`** | **`:`** | **`18937`** | **`:`** | **`0`** | **`:`** | **`99999`** | **`:`** | **`7`** | **`:`** | **`:`** | **`:`** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Nombre de usuario |  | Contraseña encriptada |  | Último cambio de PW |  | Mínimo Edad de PW |  | Max. Edad de PW |  | Período de advertencia | Período de inactividad | Fecha de expiración | No usado |

### **Archivo sombra**

Passwd, shadow & upasswd

```
[cry0l1t3@parrot]─[~]$ sudo cat /etc/shadowroot:*:18747:0:99999:7:::
sys:!:18747:0:99999:7:::
...SNIP...
cry0l1t3:$6$wBRzy$...SNIP...x9cDWUxW1:18937:0:99999:7:::
```

Si el campo de contraseña contiene un personaje, como`!`o`*`, el usuario no puede iniciar sesión con una contraseña Unix. Sin embargo, aún se pueden usar otros métodos de autenticación para iniciar sesión, como Kerberos o autenticación basada en clave. El mismo caso se aplica si el`encrypted password`el campo está vacío. Esto significa que no se requiere contraseña para el inicio de sesión. Sin embargo, puede llevar a programas específicos que niegan el acceso a las funciones. El`encrypted password`También tiene un formato particular por el cual también podemos encontrar información:

- `$<type>$<salt>$<hashed>`

Como podemos ver aquí, las contraseñas encriptadas se dividen en tres partes. Los tipos de cifrado nos permiten distinguir entre lo siguiente:

### **Tipos de algoritmo**

- `$1$`MD5
- `$2a$`Blowfish
- `$2y$`eksblowfish
- `$5$`SHA-256
- `$6$`SHA-512

Por defecto, el SHA-512 (`$6$`) El método de cifrado se utiliza en las últimas distribuciones de Linux. También encontraremos los otros métodos de cifrado que luego podemos tratar de descifrar en sistemas más antiguos. Discutiremos cómo funciona un poco el agrietamiento.

---

# **Opasswd**

La biblioteca PAM (`pam_unix.so`) puede evitar la reutilización de contraseñas antiguas. El archivo donde se almacenan las contraseñas antiguas es el`/etc/security/opasswd`. Los permisos de administrador/raíz también deben leer el archivo si los permisos para este archivo no se han cambiado manualmente.

### **Lectura/etc/seguridad/opasswd**

Passwd, shadow & upasswd

```
xnoxos@htb[/htb]$ sudo cat /etc/security/opasswdcry0l1t3:1000:2:$1$HjFAfYTG$qNDkF0zJ3v8ylCOrKB0kt0,$1$kcUjWZJX$E9uMSmiQeRh4pAAgzuvkq1
```

Mirando el contenido de este archivo, podemos ver que contiene varias entradas para el usuario`cry0l1t3`, separado por una coma (`,`). Otro punto crítico al que prestar atención es el tipo de hashing que se ha utilizado. Esto es porque el`MD5` (`$1$`) El algoritmo es mucho más fácil de descifrar que SHA-512. Esto es especialmente importante para identificar contraseñas antiguas y tal vez incluso su patrón porque a menudo se usan en varios servicios o aplicaciones. Aumentamos la probabilidad de adivinar la contraseña correcta muchas veces en función de su patrón.

---

# **Crujir las credenciales de Linux**

Una vez que hemos recopilado algunos hashes, podemos intentar descifrarlos de diferentes maneras para obtener las contraseñas en ClearText.

### **Desanimar**

Passwd, shadow & upasswd

```
xnoxos@htb[/htb]$ sudo cp /etc/passwd /tmp/passwd.bak xnoxos@htb[/htb]$ sudo cp /etc/shadow /tmp/shadow.bak xnoxos@htb[/htb]$ unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
```

### **Hashcat: grietas de hashes sin capacitación**

Passwd, shadow & upasswd

```
xnoxos@htb[/htb]$ hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```

### **Hashcat - Rompiendo hashes MD5**

Passwd, shadow & upasswd

```
xnoxos@htb[/htb]$ cat md5-hashes.listqNDkF0zJ3v8ylCOrKB0kt0
E9uMSmiQeRh4pAAgzuvkq1

```

Passwd, shadow & upasswd

```
xnoxos@htb[/htb]$ hashcat -m 500 -a 0 md5-hashes.list rockyou.txt
```