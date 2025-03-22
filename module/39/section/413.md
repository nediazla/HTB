# Plugins

Los complementos son un software fácilmente disponible que ya ha sido lanzado por terceros y ha dado su aprobación a los creadores de MetaSploit para integrar su software dentro del marco. Estos pueden representar productos comerciales que tienen un`Community Edition`Para uso gratuito pero con funcionalidad limitada, o pueden ser proyectos individuales desarrollados por personas individuales.

El uso de complementos hace que la vida de un Pentester sea aún más fácil, lo que lleva la funcionalidad del software bien conocido al`msfconsole`o entornos MetaSploit Pro. Mientras que antes, necesitábamos andar en bicicleta entre diferentes software para importar y exportar resultados, configurar opciones y parámetros una y otra vez, ahora, con el uso de complementos, MSFConsole documenta automáticamente todo en la base de datos que estamos utilizando y los hosts, los servicios y las vulnerabilidades están disponibles en el usuario.[Complementos](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf/Plugin)Trabaje directamente con la API y se puede usar para manipular todo el marco. Pueden ser útiles para automatizar tareas repetitivas, agregando nuevos comandos al`msfconsole`, y extendiendo el marco ya poderoso.

---

# **Usando complementos**

Para comenzar a usar un complemento, deberemos asegurarnos de que esté instalado en el directorio correcto en nuestra máquina. Navegando a`/usr/share/metasploit-framework/plugins`, que es el directorio predeterminado para cada nueva instalación de`msfconsole`, debería mostrarnos qué complementos tenemos para nuestra disponibilidad:

Complementos

```
xnoxos@htb[/htb]$ ls /usr/share/metasploit-framework/pluginsaggregator.rb      beholder.rb        event_tester.rb  komand.rb     msfd.rb    nexpose.rb   request.rb  session_notifier.rb  sounds.rb  token_adduser.rb  wmap.rb
alias.rb           db_credcollect.rb  ffautoregen.rb   lab.rb        msgrpc.rb  openvas.rb   rssfeed.rb  session_tagger.rb    sqlmap.rb  token_hunter.rb
auto_add_route.rb  db_tracker.rb      ips_filter.rb    libnotify.rb  nessus.rb  pcap_log.rb  sample.rb   socket_logger.rb     thread.rb  wiki.rb

```

If the plugin is found here, we can fire it up inside `msfconsole` and will be met with the greeting output for that specific plugin, signaling that it was successfully loaded in and is now ready to use:

### **MSF - Cargar nessus**

Complementos

```
msf6 > load nessus

[*] Nessus Bridge for Metasploit
[*] Type nessus_help for a command listing
[*] Successfully loaded Plugin: Nessus

msf6 > nessus_help

Command                     Help Text
-------                     ---------
Generic Commands
-----------------           -----------------
nessus_connect              Connect to a Nessus server
nessus_logout               Logout from the Nessus server
nessus_login                Login into the connected Nessus server with a different username and

<SNIP>

nessus_user_del             Delete a Nessus User
nessus_user_passwd          Change Nessus Users Password

Policy Commands
-----------------           -----------------
nessus_policy_list          List all polciies
nessus_policy_del           Delete a policy

```

Si el complemento no está instalado correctamente, recibiremos el siguiente error al intentar cargarlo.

Complementos

```
msf6 > load Plugin_That_Does_Not_Exist

[-] Failed to load plugin from /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb: cannot load such file -- /usr/share/metasploit-framework/plugins/Plugin_That_Does_Not_Exist.rb

```

Para comenzar a usar el complemento, comience a emitir los comandos disponibles para nosotros en el menú Ayuda de ese complemento específico. Cada integración multiplataforma nos ofrece un conjunto único de interacciones que podemos usar durante nuestras evaluaciones, por lo que es útil leer en cada uno de estos antes de emplearlas para aprovechar al máximo las de las dedos.

---

# **Instalación de nuevos complementos**

Se instalan nuevos complementos más populares con cada actualización de la distribución del sistema operativo Parrot, ya que sus fabricantes los empujan hacia el público, recopilados en el repositorio de la actualización Parrot. Para instalar nuevos complementos personalizados no incluidos en las nuevas actualizaciones de la distribución, podemos tomar el archivo .rb proporcionado en la página del fabricante y colocarlo en la carpeta en`/usr/share/metasploit-framework/plugins`con los permisos adecuados.

Por ejemplo, intentemos instalar[Metasploit-Plugins de Darkoperator](https://github.com/darkoperator/Metasploit-Plugins.git). Luego, siguiendo el enlace de arriba, obtenemos un par de Ruby (`.rb`) archivos que podemos colocar directamente en la carpeta mencionada anteriormente.

### **Descargar complementos MSF**

Complementos

```
xnoxos@htb[/htb]$ git clone https://github.com/darkoperator/Metasploit-Pluginsxnoxos@htb[/htb]$ ls Metasploit-Pluginsaggregator.rb      ips_filter.rb  pcap_log.rb          sqlmap.rb
alias.rb           komand.rb      pentest.rb           thread.rb
auto_add_route.rb  lab.rb         request.rb           token_adduser.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_hunter.rb
db_credcollect.rb  msfd.rb        sample.rb            twitt.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
growl.rb           openvas.rb     sounds.rb

```

Aquí podemos tomar el complemento`pentest.rb`Como ejemplo y copiarlo para`/usr/share/metasploit-framework/plugins`.

### **MSF - Copiar complemento a MSF**

Complementos

```
xnoxos@htb[/htb]$ sudo cp ./Metasploit-Plugins/pentest.rb /usr/share/metasploit-framework/plugins/pentest.rb
```

Después, lanza`msfconsole`y verifique la instalación del complemento ejecutando el`load`dominio. Después de cargar el complemento, el`help menu`al`msfconsole`se extiende automáticamente por funciones adicionales.

### **MSF - complemento de carga**

Complementos

```
xnoxos@htb[/htb]$ msfconsole -qmsf6 > load pentest

       ___         _          _     ___ _           _
      | _ \___ _ _| |_ ___ __| |_  | _ \ |_  _ __ _(_)_ _
      |  _/ -_) ' \  _/ -_|_-<  _| |  _/ | || / _` | | ' \
      |_| \___|_||_\__\___/__/\__| |_| |_|\_,_\__, |_|_||_|
                                              |___/

Version 1.6
Pentest Plugin loaded.
by Carlos Perez (carlos_perez[at]darkoperator.com)
[*] Successfully loaded plugin: pentest

msf6 > help

Tradecraft Commands
===================

    Command          Description
    -------          -----------
    check_footprint  Checks the possible footprint of a post module on a target system.

auto_exploit Commands
=====================

    Command           Description
    -------           -----------
    show_client_side  Show matched client side exploits from data imported from vuln scanners.
    vuln_exploit      Runs exploits based on data imported from vuln scanners.

Discovery Commands
==================

    Command                 Description
    -------                 -----------
    discover_db             Run discovery modules against current hosts in the database.
    network_discover        Performs a port-scan and enumeration of services found for non pivot networks.
    pivot_network_discover  Performs enumeration of networks available to a specified Meterpreter session.
    show_session_networks   Enumerate the networks one could pivot thru Meterpreter in the active sessions.

Project Commands
================

    Command       Description
    -------       -----------
    project       Command for managing projects.

Postauto Commands
=================

    Command             Description
    -------             -----------
    app_creds           Run application password collection modules against specified sessions.
    get_lhost           List local IP addresses that can be used for LHOST.
    multi_cmd           Run shell command against several sessions
    multi_meter_cmd     Run a Meterpreter Console Command against specified sessions.
    multi_meter_cmd_rc  Run resource file with Meterpreter Console Commands against specified sessions.
    multi_post          Run a post module against specified sessions.
    multi_post_rc       Run resource file with post modules and options against specified sessions.
    sys_creds           Run system password collection modules against specified sessions.

<SNIP>

```

Muchas personas escriben muchos complementos diferentes para el marco de MetaSploit. Todos tienen un propósito específico y pueden ser una excelente ayuda para ahorrar tiempo después de familiarizarnos con ellos. Consulte la lista de complementos populares a continuación:

|  |  |  |
| --- | --- | --- |
| [NMAP (preinstalado)](https://nmap.org/) | [Nexpose (preinstalado)](https://sectools.org/tool/nexpose/) | [Nessus (preinstalado)](https://www.tenable.com/products/nessus) |
| [Mimikatz (preinstalado v.1)](http://blog.gentilkiwi.com/mimikatz) | [Stdapi (preinstalado)](https://www.rubydoc.info/github/rapid7/metasploit-framework/Rex/Post/Meterpreter/Extensions/Stdapi/Stdapi) | [Ferrocarril](https://github.com/rapid7/metasploit-framework/wiki/How-to-use-Railgun-for-Windows-post-exploitation) |
| [Privilegio](https://github.com/rapid7/metasploit-framework/blob/master/lib/rex/post/meterpreter/extensions/priv/priv.rb) | [Incógnito (preinstalado)](https://www.offensive-security.com/metasploit-unleashed/fun-incognito/) | [Darkoperator's](https://github.com/darkoperator/Metasploit-Plugins) |

---

# **Mezcla**

El marco de Metasploit está escrito en Ruby, un lenguaje de programación orientado a objetos. Esto juega un papel importante en lo que hace`msfconsole`Excelente para usar. Mixins es una de esas características que, cuando se implementan, ofrecen una gran cantidad de flexibilidad tanto al creador del script como al usuario.

Mixins son clases que actúan como métodos para usar por otras clases sin tener que ser la clase principal de esas otras clases. Por lo tanto, se consideraría inapropiado llamarlo herencia, pero más bien inclusión. Se usan principalmente cuando nosotros:

1. Desea proporcionar muchas funciones opcionales para una clase.
2. Desea usar una característica particular para una multitud de clases.

La mayor parte del lenguaje de programación de Ruby gira en torno a las mezclas como módulos. El concepto de mixins se implementa utilizando la palabra`include`, a lo que pasamos el nombre del módulo como un`parameter`. Podemos leer más sobre mixins[aquí](https://en.wikibooks.org/wiki/Metasploit/UsingMixins).

Si recién estamos comenzando con MetaSploit, no debemos preocuparnos por el uso de Mixins o su impacto en nuestra evaluación. Sin embargo, se mencionan aquí como una nota de cuán compleja puede ser la personalización de MetaSploit.