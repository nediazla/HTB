# Databases

`Databases`en`msfconsole`se utilizan para realizar un seguimiento de sus resultados. No es misterio que durante las evaluaciones de máquinas aún más complejas, mucho menos redes enteras, las cosas puedan volverse un poco confusas y complicadas debido a la gran cantidad de resultados de búsqueda, puntos de entrada, problemas detectados, credenciales descubiertas, etc.

Aquí es donde las bases de datos entran en juego.`Msfconsole`Tiene soporte incorporado para el sistema de base de datos PostgreSQL. Con él, tenemos acceso directo, rápido y fácil para escanear los resultados con la capacidad adicional de importar y exportar resultados junto con herramientas de terceros. Las entradas de la base de datos también se pueden usar para configurar los parámetros del módulo de exploit con los hallazgos ya existentes directamente.

---

# **Configuración de la base de datos**

Primero, debemos asegurarnos de que el servidor PostgreSQL esté en funcionamiento en nuestra máquina host. Para hacerlo, ingrese el siguiente comando:

### **Estado de PostgreSQL**

Bases de datos

```
xnoxos@htb[/htb]$ sudo service postgresql status● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; vendor preset: disabled)
     Active: active (exited) since Fri 2022-05-06 14:51:30 BST; 3min 51s ago
    Process: 2147 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 2147 (code=exited, status=0/SUCCESS)
        CPU: 1ms

May 06 14:51:30 pwnbox-base systemd[1]: Starting PostgreSQL RDBMS...
May 06 14:51:30 pwnbox-base systemd[1]: Finished PostgreSQL RDBMS.

```

### **Iniciar PostgreSQL**

Bases de datos

```
xnoxos@htb[/htb]$ sudo systemctl start postgresql
```

Después de comenzar PostgreSQL, necesitamos crear e inicializar la base de datos MSF con`msfdb init`.

### **MSF: inicie una base de datos**

Bases de datos

```
xnoxos@htb[/htb]$ sudo msfdb init[i] Database already started
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
rake aborted!
NoMethodError: undefined method `without' for #<Bundler::Settings:0x000055dddcf8cba8>
Did you mean? with_options

<SNIP>

```

A veces, puede ocurrir un error si el metasploit no está actualizado. Esta diferencia que causa el error puede ocurrir por varias razones. Primero, a menudo ayuda a actualizar MetaSploit nuevamente (`apt update`) para resolver este problema. Entonces podemos intentar reinicializar la base de datos MSF.

Bases de datos

```
xnoxos@htb[/htb]$ sudo msfdb init[i] Database already started
[i] The database appears to be already configured, skipping initialization

```

Si se omite la inicialización y MetaSploit nos dice que la base de datos ya está configurada, podemos volver a verificar el estado de la base de datos.

Bases de datos

```
xnoxos@htb[/htb]$ sudo msfdb status● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; vendor preset: disabled)
     Active: active (exited) since Mon 2022-05-09 15:19:57 BST; 35min ago
    Process: 2476 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 2476 (code=exited, status=0/SUCCESS)
        CPU: 1ms

May 09 15:19:57 pwnbox-base systemd[1]: Starting PostgreSQL RDBMS...
May 09 15:19:57 pwnbox-base systemd[1]: Finished PostgreSQL RDBMS.

COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
postgres 2458 postgres    5u  IPv6  34336      0t0  TCP localhost:5432 (LISTEN)
postgres 2458 postgres    6u  IPv4  34337      0t0  TCP localhost:5432 (LISTEN)

UID          PID    PPID  C STIME TTY      STAT   TIME CMD
postgres    2458       1  0 15:19 ?        Ss     0:00 /usr/lib/postgresql/13/bin/postgres -D /var/lib/postgresql/13/main -c con

[+] Detected configuration file (/usr/share/metasploit-framework/config/database.yml)

```

Si este error no aparece, lo que a menudo ocurre después de una nueva instalación de MetaSploit, veremos lo siguiente al inicializar la base de datos:

Bases de datos

```
xnoxos@htb[/htb]$ sudo msfdb init[+] Starting database
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema

```

Después de que se haya inicializado la base de datos, podemos comenzar`msfconsole`y conectarse a la base de datos creada simultáneamente.

### **MSF: conectarse a la base de datos iniciada**

Bases de datos

```
xnoxos@htb[/htb]$ sudo msfdb run[i] Database already started

         .                                         .
 .

      dBBBBBBb  dBBBP dBBBBBBP dBBBBBb  .                       o
       '   dB'                     BBP
    dB'dB'dB' dBBP     dBP     dBP BB
   dB'dB'dB' dBP      dBP     dBP  BB
  dB'dB'dB' dBBBBP   dBP     dBBBBBBB

                                   dBBBBBP  dBBBBBb  dBP    dBBBBP dBP dBBBBBBP
          .                  .                  dB' dBP    dB'.BP
                             |       dBP    dBBBB' dBP    dB'.BP dBP    dBP
                           --o--    dBP    dBP    dBP    dB'.BP dBP    dBP
                             |     dBBBBP dBP    dBBBBP dBBBBP dBP    dBP

                                                                    .
                .
        o                  To boldly go where no
                            shell has gone before

       =[ metasploit v6.1.39-dev                          ]
+ -- --=[ 2214 exploits - 1171 auxiliary - 396 post       ]
+ -- --=[ 616 payloads - 45 encoders - 11 nops            ]
+ -- --=[ 9 evasion                                       ]

msf6>

```

Sin embargo, si ya tenemos la base de datos configurada y no podemos cambiar la contraseña al nombre de usuario MSF, continúe con estos comandos:

### **MSF: reinicie la base de datos**

Bases de datos

```
xnoxos@htb[/htb]$ msfdb reinitxnoxos@htb[/htb]$ cp /usr/share/metasploit-framework/config/database.yml ~/.msf4/xnoxos@htb[/htb]$ sudo service postgresql restartxnoxos@htb[/htb]$ msfconsole -qmsf6 > db_status

[*] Connected to msf. Connection type: PostgreSQL.

```

Ahora estamos listos para irnos. El`msfconsole`También ofrece ayuda integrada para la base de datos. Esto nos da una buena visión general de interactuar y usar la base de datos.

### **MSF - Opciones de base de datos**

Bases de datos

```
msf6 > help database

Database Backend Commands
=========================

    Command           Description
    -------           -----------
    db_connect        Connect to an existing database
    db_disconnect     Disconnect from the current database instance
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache
    db_status         Show the current database status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces

msf6 > db_status

[*] Connected to msf. Connection type: postgresql.

```

---

# **Usando la base de datos**

Con la ayuda de la base de datos, podemos administrar muchas categorías y hosts diferentes que hemos analizado. Alternativamente, la información sobre ellos que hemos interactuado con el uso de MetaSploit. Estas bases de datos se pueden exportar e importar. Esto es especialmente útil cuando tenemos listas extensas de hosts, botín, notas y vulnerabilidades almacenadas para estos hosts. Después de confirmar que la base de datos está conectada con éxito, podemos organizar nuestro`Workspaces`.

### **Espacios de trabajo**

Podemos pensar en`Workspaces`De la misma manera que pensamos en las carpetas en un proyecto. Podemos segregar los diferentes resultados de escaneo, hosts e información extraída por IP, subred, red o dominio.

Para ver la lista actual del espacio de trabajo, use el`workspace`dominio. Agregando un`-a`o`-d`cambiar después del comando, seguido del nombre del espacio de trabajo, lo hará`add`o`delete`Ese espacio de trabajo para la base de datos.

Bases de datos

```
msf6 > workspace

* default

```

Observe que se nombra el espacio de trabajo predeterminado`default`y actualmente está en uso según el`*`símbolo. Escriba el`workspace [name]`Comando para cambiar el espacio de trabajo actualmente utilizado. Mirando hacia atrás en nuestro ejemplo, creemos un espacio de trabajo para esta evaluación y seleccione.

Bases de datos

```
msf6 > workspace -a Target_1

[*] Added workspace: Target_1
[*] Workspace: Target_1

msf6 > workspace Target_1

[*] Workspace: Target_1

msf6 > workspace

  default
* Target_1

```

Para ver qué más podemos hacer con los espacios de trabajo, podemos usar el`workspace -h`Comando para el menú de ayuda relacionado con los espacios de trabajo.

Bases de datos

```
msf6 > workspace -h

Usage:
    workspace                  List workspaces
    workspace -v               List workspaces verbosely
    workspace [name]           Switch workspace
    workspace -a [name] ...    Add workspace(s)
    workspace -d [name] ...    Delete workspace(s)
    workspace -D               Delete all workspaces
    workspace -r     Rename workspace
    workspace -h               Show this help information

```

---

# **Importación de resultados de escaneo**

A continuación, supongamos que queremos importar un`Nmap scan`de un host en el espacio de trabajo de nuestra base de datos para comprender mejor el objetivo. Podemos usar el`db_import`comando para esto. Después de completar la importación, podemos verificar la presencia de la información del host en nuestra base de datos utilizando el`hosts`y`services`comandos. Tenga en cuenta que el`.xml`Se prefiere el tipo de archivo para`db_import`.

### **Escaneo nmap almacenado**

Bases de datos

```
xnoxos@htb[/htb]$ cat Target.nmapStarting Nmap 7.80 ( https://nmap.org ) at 2020-08-17 20:54 UTC
Nmap scan report for 10.10.10.40
Host is up (0.017s latency).
Not shown: 991 closed ports
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.81 seconds

```

### **Importación de resultados de escaneo**

Bases de datos

```
msf6 > db_import Target.xml

[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Nokogiri v1.10.9'
[*] Importing host 10.10.10.40
[*] Successfully imported ~/Target.xml

msf6 > hosts

Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.10.10.40             Unknown                    device

msf6 > services

Services
========

host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.10.10.40  135    tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  139    tcp    netbios-ssn   open   Microsoft Windows netbios-ssn
10.10.10.40  445    tcp    microsoft-ds  open   Microsoft Windows 7 - 10 microsoft-ds workgroup: WORKGROUP
10.10.10.40  49152  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49153  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49154  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49155  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49156  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49157  tcp    msrpc         open   Microsoft Windows RPC

```

---

# **Uso de NMAP dentro de MSFConsole**

¡Alternativamente, podemos usar NMAP directamente de MSFConsole! Para escanear directamente desde la consola sin tener que tener un fondo o salir del proceso, use el`db_nmap`dominio.

### **MSF - NMAP**

Bases de datos

```
msf6 > db_nmap -sV -sS 10.10.10.8

[*] Nmap: Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-17 21:04 UTC
[*] Nmap: Nmap scan report for 10.10.10.8
[*] Nmap: Host is up (0.016s latency).
[*] Nmap: Not shown: 999 filtered ports
[*] Nmap: PORT   STATE SERVICE VERSION
[*] Nmap: 80/TCP open  http    HttpFileServer httpd 2.3
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 11.12 seconds

msf6 > hosts

Hosts
=====

address      mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------      ---  ----  -------  ---------  -----  -------  ----  --------
10.10.10.8              Unknown                    device
10.10.10.40             Unknown                    device

msf6 > services

Services
========

host         port   proto  name          state  info
----         ----   -----  ----          -----  ----
10.10.10.8   80     tcp    http          open   HttpFileServer httpd 2.3
10.10.10.40  135    tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  139    tcp    netbios-ssn   open   Microsoft Windows netbios-ssn
10.10.10.40  445    tcp    microsoft-ds  open   Microsoft Windows 7 - 10 microsoft-ds workgroup: WORKGROUP
10.10.10.40  49152  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49153  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49154  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49155  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49156  tcp    msrpc         open   Microsoft Windows RPC
10.10.10.40  49157  tcp    msrpc         open   Microsoft Windows RPC

```

---

# **Copia de seguridad de datos**

Después de terminar la sesión, asegúrese de hacer una copia de seguridad de nuestros datos si sucede algo con el servicio PostgreSQL. Para hacerlo, use el`db_export`dominio.

### **MSF - Exportación de DB**

Bases de datos

```
msf6 > db_export -h

Usage:
    db_export -f <format> [filename]
    Format can be one of: xml, pwdump
[-] No output file was specified

msf6 > db_export -f xml backup.xml

[*] Starting export of workspace default to backup.xml [ xml ]...
[*] Finished export of workspace default to backup.xml [ xml ]...

```

Estos datos se pueden importar a MSFConsole más adelante cuando sea necesario. Otros comandos relacionados con la retención de datos son el uso extendido de`hosts`, `services`, y el`creds`y`loot`comandos.

---

# **Anfitriones**

El`hosts`El comando muestra una tabla de base de datos poblada automáticamente con las direcciones de host, los nombres de host y otra información que encontramos sobre estas durante nuestros escaneos e interacciones. Por ejemplo, supongamos`msfconsole`está vinculado con complementos del escáner que pueden realizar la detección de servicios y del sistema operativo. En ese caso, esta información debe aparecer automáticamente en la tabla una vez que los escaneos se completen a través de MSFConsole. Una vez más, herramientas como Nessus, Nexpose o NMAP nos ayudarán en estos casos.

Los hosts también se pueden agregar manualmente como entradas separadas en esta tabla. Después de agregar nuestros hosts personalizados, también podemos organizar el formato y la estructura de la tabla, agregar comentarios, cambiar la información existente y más.

### **MSF - Hosts almacenados**

Bases de datos

```
msf6 > hosts -h

Usage: hosts [ options ] [addr1 addr2 ...]

OPTIONS:
  -a,--add          Add the hosts instead of searching
  -d,--delete       Delete the hosts instead of searching
  -c <col1,col2>    Only show the given columns (see list below)
  -C <col1,col2>    Only show the given columns until the next restart (see list below)
  -h,--help         Show this help information
  -u,--up           Only show hosts which are up
  -o <file>         Send output to a file in CSV format
  -O <column>       Order rows by specified column number
  -R,--rhosts       Set RHOSTS from the results of the search
  -S,--search       Search string to filter by
  -i,--info         Change the info of a host
  -n,--name         Change the name of a host
  -m,--comment      Change the comment of a host
  -t,--tag          Add or specify a tag to a range of hosts

Available columns: address, arch, comm, comments, created_at, cred_count, detected_arch, exploit_attempt_count, host_detail_count, info, mac, name, note_count, os_family, os_flavor, os_lang, os_name, os_sp, purpose, scope, service_count, state, updated_at, virtual_host, vuln_count, tags

```

---

# **Servicios**

El`services`Las funciones de comando de la misma manera que la anterior. Contiene una tabla con descripciones e información sobre los servicios descubiertos durante escaneos o interacciones. De la misma manera que el comando anterior, las entradas aquí son altamente personalizables.

### **MSF - Servicios almacenados de hosts**

Bases de datos

```
msf6 > services -h

Usage: services [-h] [-u] [-a] [-r <proto>] [-p <port1,port2>] [-s <name1,name2>] [-o <filename>] [addr1 addr2 ...]

  -a,--add          Add the services instead of searching
  -d,--delete       Delete the services instead of searching
  -c <col1,col2>    Only show the given columns
  -h,--help         Show this help information
  -s <name>         Name of the service to add
  -p <port>         Search for a list of ports
  -r <protocol>     Protocol type of the service being added [tcp|udp]
  -u,--up           Only show services which are up
  -o <file>         Send output to a file in csv format
  -O <column>       Order rows by specified column number
  -R,--rhosts       Set RHOSTS from the results of the search
  -S,--search       Search string to filter by
  -U,--update       Update data for existing service

Available columns: created_at, info, name, port, proto, state, updated_at

```

---

# **Cartas credenciales**

El`creds`El comando le permite visualizar las credenciales reunidas durante sus interacciones con el host de destino. También podemos agregar credenciales manualmente, coincidir con las credenciales existentes con las especificaciones del puerto, agregar descripciones, etc.

### **MSF - Credenciales almacenadas**

Bases de datos

```
msf6 > creds -h

With no sub-command, list credentials. If an address range is
given, show only credentials with logins on hosts within that
range.

Usage - Listing credentials:
  creds [filter options] [address range]

Usage - Adding credentials:
  creds add uses the following named parameters.
    user      :  Public, usually a username
    password  :  Private, private_type Password.
    ntlm      :  Private, private_type NTLM Hash.
    Postgres  :  Private, private_type Postgres MD5
    ssh-key   :  Private, private_type SSH key, must be a file path.
    hash      :  Private, private_type Nonreplayable hash
    jtr       :  Private, private_type John the Ripper hash type.
    realm     :  Realm,
    realm-type:  Realm, realm_type (domain db2db sid pgdb rsync wildcard), defaults to domain.

Examples: Adding
# Add a user, password and realm   creds add user:admin password:notpassword realm:workgroup
# Add a user and password   creds add user:guest password:'guest password'
# Add a password   creds add password:'password without username'
# Add a user with an NTLMHash   creds add user:admin ntlm:E2FC15074BF7751DD408E6B105741864:A1074A69B1BDE45403AB680504BBDD1A
# Add a NTLMHash   creds add ntlm:E2FC15074BF7751DD408E6B105741864:A1074A69B1BDE45403AB680504BBDD1A
# Add a Postgres MD5   creds add user:postgres postgres:md5be86a79bf2043622d58d5453c47d4860
# Add a user with an SSH key   creds add user:sshadmin ssh-key:/path/to/id_rsa
# Add a user and a NonReplayableHash   creds add user:other hash:d19c32489b870735b5f587d76b934283 jtr:md5
# Add a NonReplayableHash   creds add hash:d19c32489b870735b5f587d76b934283

General options
  -h,--help             Show this help information
  -o <file>             Send output to a file in csv/jtr (john the ripper) format.
                        If the file name ends in '.jtr', that format will be used.
                        If file name ends in '.hcat', the hashcat format will be used.
                        CSV by default.
  -d,--delete           Delete one or more credentials

Filter options for listing
  -P,--password <text>  List passwords that match this text
  -p,--port <portspec>  List creds with logins on services matching this port spec
  -s <svc names>        List creds matching comma-separated service names
  -u,--user <text>      List users that match this text
  -t,--type <type>      List creds that match the following types: password,ntlm,hash
  -O,--origins <IP>     List creds that match these origins
  -R,--rhosts           Set RHOSTS from the results of the search
  -v,--verbose          Don't truncate long password hashes

Examples, John the Ripper hash types:
  Operating Systems (starts with)
    Blowfish ($2a$)   : bf    BSDi     (_)      : bsdi
    DES               : des,crypt
    MD5      ($1$)    : md5    SHA256   ($5$)    : sha256,crypt    SHA512   ($6$)    : sha512,crypt  Databases
    MSSQL             : mssql
    MSSQL 2005        : mssql05
    MSSQL 2012/2014   : mssql12
    MySQL < 4.1       : mysql
    MySQL >= 4.1      : mysql-sha1
    Oracle            : des,oracle
    Oracle 11         : raw-sha1,oracle11
    Oracle 11 (H type): dynamic_1506
    Oracle 12c        : oracle12c
    Postgres          : postgres,raw-md5

Examples, listing:
  creds# Default, returns all credentials  creds 1.2.3.4/24# Return credentials with logins in this range  creds -O 1.2.3.4/24# Return credentials with origins in this range  creds -p 22-25,445# nmap port specification  creds -s ssh,smb# All creds associated with a login on SSH or SMB services  creds -t NTLM# All NTLM creds  creds -j md5# All John the Ripper hash type MD5 credsExample, deleting:
# Delete all SMB credentials  creds -d -s smb

```

---

# **Botín**

El`loot`El comando funciona junto con el comando anterior para ofrecerle una lista de servicios y usuarios propios. El botín, en este caso, se refiere a vertederos de hash de diferentes tipos de sistemas, a saber, hashes, passwd, sombra y más.

### **MSF - botín almacenado**

Bases de datos

```
msf6 > loot -h

Usage: loot [options]
 Info: loot [-h] [addr1 addr2 ...] [-t <type1,type2>]
  Add: loot -f [fname] -i [info] -a [addr1 addr2 ...] -t [type]
  Del: loot -d [addr1 addr2 ...]

  -a,--add          Add loot to the list of addresses, instead of listing
  -d,--delete       Delete *all* loot matching host and type
  -f,--file         File with contents of the loot to add
  -i,--info         Info of the loot to add
  -t <type1,type2>  Search for a list of types
  -h,--help         Show this help information
  -S,--search       Search string to filter by
```