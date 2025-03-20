# Snort Fundaments

Snort es una herramienta de código abierto, que sirve como un sistema de detección de intrusos (IDS) y el sistema de prevención de intrusiones (IPS), pero también puede funcionar como un registrador de paquetes o rastrear, similar a Suricata. Al inspeccionar a fondo todo el tráfico de red, Snort tiene la capacidad de identificar e registrar toda la actividad dentro de ese tráfico, proporcionando una visión integral de la situación y los registros detallados de todas las transacciones de la capa de aplicación. Requerimos conjuntos de reglas específicos para instruir a Snort sobre cómo realizar su inspección y qué necesita identificar exactamente. Snort fue creado para operar eficientemente tanto en hardware de uso general como personalizado.

# **Modos de operación de Snort**

Snort generalmente funciona en los siguientes modos:

- IDS en línea/IPS
- IDS pasivos
- IDS basados en la red
- IDS basados en host (sin embargo, Snort no es idealmente un IDS basado en host. Recomendamos optar por herramientas más especializadas para esto).

**De acuerdo a[La documentación de Snort](https://docs.snort.org/start/inspection)**:

"Con ciertos módulos DAQ, Snort puede utilizar dos modos de operación diferentes:`passive`y`inline`. `Passive mode`Da a Snort la capacidad de observar y detectar el tráfico en una interfaz de red, pero evita el bloqueo directo del tráfico.`Inline mode`Por otro lado, da a Snort la capacidad de bloquear el tráfico si un paquete en particular garantiza tal evento.

Snort inferirá el modo de operación particular en función de las opciones utilizadas en la línea de comando. Por ejemplo, leer de un archivo PCAP con el`-r`opción o escucha en una interfaz con`-i`Hará que Snort se ejecute en modo pasivo por defecto. Sin embargo, si el DAQ admite en línea, los usuarios pueden especificar el`-Q`bandera para ejecutar Snort en línea.

Un módulo DAQ que admite el modo en línea es`afpacket`, que es un módulo que le da acceso a los paquetes recibidos en los dispositivos de red Linux ".

# **Arquitectura de brea**

Para que resoplen la transición de un sniffer de paquetes simple a un IDS robusto, se agregaron varios componentes clave:`Preprocessor`, `Detection Engine`, `Logging and Alerting System`, y varios`Output modules`.

- El paquete Sniffer (que incluye el decodificador de paquetes) extrae el tráfico de red, reconociendo la estructura de cada paquete. Los paquetes crudos que se recogen se envían posteriormente al`Preprocessors`.
- `Preprocessors`dentro de Snort identifica el tipo o comportamiento de los paquetes reenviados. Snort tiene una variedad de`Preprocessor`complementos, como el complemento HTTP que distingue los paquetes relacionados con HTTP o el`port_scan Preprocessor`que identifica posibles intentos de escaneo de puertos basados en protocolos predefinidos, tipos de escaneos y umbrales. Después de la`Preprocessors`han completado su tarea, la información se pasa a la`Detection Engine`. La configuración de estos`Preprocessors`se puede encontrar dentro del archivo de configuración de Snort,`snort.lua`.
- El`Detection Engine`Compara cada paquete con un conjunto predefinido de reglas de inicio. Si se encuentra una coincidencia, la información se reenvía al`Logging and Alerting System`.
- El`Logging and Alerting System`y`Output modules`están a cargo de registrar o activar alertas según lo determinado por cada acción de la regla. Los registros generalmente se almacenan en`syslog`o`unified2`formatos o directamente en una base de datos. El`Output modules`están configurados dentro del archivo de configuración de Snort,`snort.lua`.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, ssh en la IP objetivo utilizando las credenciales proporcionadas. La gran mayoría de los comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Configuración de Snort y validación de la configuración de Snort**

Snort ofrece una amplia gama de opciones de configuración, y afortunadamente, el Snort 3 de código abierto proporciona a los usuarios archivos preconfigurados para facilitar un inicio rápido. Estos archivos de configuración predeterminados, a saber`snort.lua`y`snort_defaults.lua`, sirva como base para establecer Snort y hacerlo operativo en poco tiempo. Proporcionan un marco de configuración estándar para los usuarios de Snort.

El`snort.lua`El archivo sirve como el archivo de configuración principal para Snort. Este archivo contiene las siguientes secciones:

- Variables de red
- Configuración de decodificador
- Configuración del motor de detección base
- Configuración de la biblioteca dinámica
- Configuración del preprocesador
- Configuración del complemento de salida
- Personalización del conjunto de reglas
- Personalización del conjunto de reglas preprocesador y decodificador
- Personalización del conjunto de reglas de objetos compartidos

Navegemos el`snort.lua`Archivo que reside en el objetivo de esta sección de la siguiente manera.

Snort Fundaments

```
xnoxos@htb[/htb]$ sudo more /root/snorty/etc/snort/snort.lua---------------------------------------------------------------------------
-- Snort++ configuration
---------------------------------------------------------------------------

-- there are over 200 modules available to tune your policy.
-- many can be used with defaults w/o any explicit configuration.
-- use this conf as a template for your specific configuration.

-- 1. configure defaults
-- 2. configure inspection
-- 3. configure bindings
-- 4. configure performance
-- 5. configure detection
-- 6. configure filters
-- 7. configure outputs
-- 8. configure tweaks

---------------------------------------------------------------------------
-- 1. configure defaults
---------------------------------------------------------------------------

-- HOME_NET and EXTERNAL_NET must be set now
-- setup the network addresses you are protecting
HOME_NET = 'any'

-- set up the external network addresses.
-- (leave as "any" in most situations)
EXTERNAL_NET = 'any'

include 'snort_defaults.lua'

---------------------------------------------------------------------------
-- 2. configure inspection
---------------------------------------------------------------------------

-- mod = { } uses internal defaults
-- you can see them with snort --help-module mod

-- mod = default_mod uses external defaults
-- you can see them in snort_defaults.lua

-- the following are quite capable with defaults:

stream = { }
stream_ip = { }
stream_icmp = { }
stream_tcp = { }
stream_udp = { }
stream_user = { }
stream_file = { }
---SNIP---

```

Habilitación y ajuste de fino`modules`es un aspecto significativo del proceso de configuración. Para explorar la lista completa y obtener una breve descripción de todos los módulos Snort 3, puede usar el siguiente comando.

Snort Fundaments

```
xnoxos@htb[/htb]$ snort --help-modulesack (ips_option): rule option to match on TCP ack numbers
active (basic): configure responses
address_space_selector (policy_selector): configure traffic processing based on address space
alert_csv (logger): output event in csv format
alert_fast (logger): output event with brief text format
alert_full (logger): output event with full packet dump
alert_json (logger): output event in json format
alert_syslog (logger): output event to syslog
alert_talos (logger): output event in Talos alert format
alert_unixsock (logger): output event over unix socket
alerts (basic): configure alerts
appid (inspector): application and service identification
appids (ips_option): detection option for application ids
arp (codec): support for address resolution protocol
arp_spoof (inspector): detect ARP attacks and anomalies
---SNIP---

```

Estos módulos están habilitados y configurados dentro del`snort.lua`Archivo de configuración como literales de tabla Lua. Si un módulo se inicializa como una tabla vacía, implica que está utilizando su configuración "predeterminada" predefinida. Para ver esta configuración predeterminada, puede utilizar el siguiente comando.

Snort Fundaments

```
xnoxos@htb[/htb]$ snort --help-config arp_spoofip4 arp_spoof.hosts[].ip: host ip address
mac arp_spoof.hosts[].mac: host mac address

```

Pasar (y validar) los archivos de configuración para inhalar se pueden hacer de la siguiente manera.

Snort Fundaments

```
xnoxos@htb[/htb]$ snort -c /root/snorty/etc/snort/snort.lua --daq-dir /usr/local/lib/daq--------------------------------------------------
o")~   Snort++ 3.1.64.0
--------------------------------------------------
Loading /root/snorty/etc/snort/snort.lua:
Loading snort_defaults.lua:
Finished snort_defaults.lua:
        output
        ips
        classifications
        references
        binder
        file_id
        ftp_server
        smtp
        port_scan
        gtp_inspect
        dce_smb
        s7commplus
        modbus
        ssh
        active
        alerts
        daq
        decode
        host_cache
        host_tracker
        hosts
        network
        packets
        process
        search_engine
        so_proxy
        stream_icmp
        normalizer
        stream
        stream_ip
        stream_tcp
        stream_udp
        stream_user
        stream_file
        arp_spoof
        back_orifice
        dns
        imap
        netflow
        pop
        rpc_decode
        sip
        ssl
        telnet
        cip
        dnp3
        iec104
        mms
        dce_tcp
        dce_udp
        dce_http_proxy
        dce_http_server
        ftp_client
        ftp_data
        http_inspect
        http2_inspect
        file_policy
        js_norm
        appid
        wizard
        trace
Finished /root/snorty/etc/snort/snort.lua:
Loading file_id.rules_file:
Loading file_magic.rules:
Finished file_magic.rules:
Finished file_id.rules_file:
--------------------------------------------------
ips policies rule stats
              id  loaded  shared enabled    file
               0     208       0     208    /root/snorty/etc/snort/snort.lua
--------------------------------------------------
rule counts
       total rules loaded: 208
               text rules: 208
            option chains: 208
            chain headers: 1
--------------------------------------------------
service rule counts          to-srv  to-cli
                  file_id:      208     208
                    total:      208     208
--------------------------------------------------
fast pattern groups
                to_server: 1
                to_client: 1
--------------------------------------------------
search engine (ac_bnfa)
                instances: 2
                 patterns: 416
            pattern chars: 2508
               num states: 1778
         num match states: 370
             memory scale: KB
             total memory: 68.5879
           pattern memory: 18.6973
        match list memory: 27.3281
        transition memory: 22.3125
appid: MaxRss diff: 3084
appid: patterns loaded: 300
--------------------------------------------------
pcap DAQ configured to passive.

Snort successfully validated the configuration (with 0 warnings).
o")~   Snort exiting

```

**Nota**: `--daq-dir /usr/local/lib/daq`no está obligado a pasar y validar un archivo de configuración. Se agrega para que podamos replicar el comando en el objetivo de esta sección.

Desde que mencionamos`DAQ`, Snort 3 debe saber dónde encontrar el apropiado`LibDAQ`. `LibDAQ`es la "biblioteca de adquisición de datos", y en un alto nivel, es una capa de abstracción utilizada por`modules`comunicarse con fuentes de datos de red de hardware y software.

Recomendamos encarecidamente tomarse el tiempo para leer los comentarios dentro del`snort.lua`Archivo mientras proporcionan ideas valiosas.

# **Entradas**

Para observar Snort en acción, el método más fácil es ejecutarlo contra un archivo de captura de paquetes. Proporcionando el nombre del archivo PCAP como argumento al`-r`Opción En la línea de comando, Snort procesará el archivo en consecuencia.

Snort Fundaments

```
xnoxos@htb[/htb]$ sudo snort -c /root/snorty/etc/snort/snort.lua --daq-dir /usr/local/lib/daq -r /home/htb-student/pcaps/icmp.pcap[sudo] password for htb-student:
--------------------------------------------------
o")~   Snort++ 3.1.64.0
--------------------------------------------------
Loading /root/snorty/etc/snort/snort.lua:
Loading snort_defaults.lua:
Finished snort_defaults.lua:
---SNIP---
Finished /root/snorty/etc/snort/snort.lua:
Loading file_id.rules_file:
Loading file_magic.rules:
Finished file_magic.rules:
Finished file_id.rules_file:
--------------------------------------------------
ips policies rule stats
              id  loaded  shared enabled    file
               0     208       0     208    /root/snorty/etc/snort/snort.lua
--------------------------------------------------
rule counts
       total rules loaded: 208
               text rules: 208
            option chains: 208
            chain headers: 1
--------------------------------------------------
service rule counts          to-srv  to-cli
                  file_id:      208     208
                    total:      208     208
--------------------------------------------------
fast pattern groups
                to_server: 1
                to_client: 1
--------------------------------------------------
search engine (ac_bnfa)
                instances: 2
                 patterns: 416
            pattern chars: 2508
               num states: 1778
         num match states: 370
             memory scale: KB
             total memory: 68.5879
           pattern memory: 18.6973
        match list memory: 27.3281
        transition memory: 22.3125
appid: MaxRss diff: 3024
appid: patterns loaded: 300
--------------------------------------------------
pcap DAQ configured to read-file.
Commencing packet processing
++ [0] /home/htb-student/pcaps/icmp.pcap
-- [0] /home/htb-student/pcaps/icmp.pcap
--------------------------------------------------
Packet Statistics
--------------------------------------------------
daq
                    pcaps: 1
                 received: 8
                 analyzed: 8
                    allow: 8
                 rx_bytes: 592
--------------------------------------------------
codec
                    total: 8            (100.000%)
                      eth: 8            (100.000%)
                    icmp4: 8            (100.000%)
                     ipv4: 8            (100.000%)
--------------------------------------------------
Module Statistics
--------------------------------------------------
appid
                  packets: 8
        processed_packets: 8
           total_sessions: 1
--------------------------------------------------
binder
                new_flows: 1
                 inspects: 1
--------------------------------------------------
detection
                 analyzed: 8
--------------------------------------------------
port_scan
                  packets: 8
                 trackers: 2
--------------------------------------------------
stream
                    flows: 1
--------------------------------------------------
stream_icmp
                 sessions: 1
                      max: 1
                  created: 1
                 released: 1
--------------------------------------------------
Summary Statistics
--------------------------------------------------
timing
                  runtime: 00:00:00
                  seconds: 0.033229
                 pkts/sec: 241
o")~   Snort exiting

```

Snort also has the capability to listen on active network interfaces. To specify this behavior, you can utilize the `-i` option followed by the names of the interfaces on which Snort should run.

Snort Fundaments

```
xnoxos@htb[/htb]$ sudo snort -c /root/snorty/etc/snort/snort.lua --daq-dir /usr/local/lib/daq -i ens160--------------------------------------------------
o")~   Snort++ 3.1.64.0
--------------------------------------------------
Loading /root/snorty/etc/snort/snort.lua:
Loading snort_defaults.lua:
Finished snort_defaults.lua:
---SNIP---
Finished /root/snorty/etc/snort/snort.lua:
Loading file_id.rules_file:
Loading file_magic.rules:
Finished file_magic.rules:
Finished file_id.rules_file:
--------------------------------------------------
ips policies rule stats
              id  loaded  shared enabled    file
               0     208       0     208    /root/snorty/etc/snort/snort.lua
--------------------------------------------------
rule counts
       total rules loaded: 208
               text rules: 208
            option chains: 208
            chain headers: 1
--------------------------------------------------
service rule counts          to-srv  to-cli
                  file_id:      208     208
                    total:      208     208
--------------------------------------------------
fast pattern groups
                to_server: 1
                to_client: 1
--------------------------------------------------
search engine (ac_bnfa)
                instances: 2
                 patterns: 416
            pattern chars: 2508
               num states: 1778
         num match states: 370
             memory scale: KB
             total memory: 68.5879
           pattern memory: 18.6973
        match list memory: 27.3281
        transition memory: 22.3125
appid: MaxRss diff: 2820
appid: patterns loaded: 300
--------------------------------------------------
pcap DAQ configured to passive.
Commencing packet processing
++ [0] ens160
^C** caught int signal
== stopping
-- [0] ens160
--------------------------------------------------
Packet Statistics
--------------------------------------------------
daq
                 received: 33
                 analyzed: 33
                    allow: 33
                     idle: 9
                 rx_bytes: 2756
--------------------------------------------------
codec
                    total: 33           (100.000%)
                 discards: 2            (  6.061%)
                      arp: 13           ( 39.394%)
                      eth: 33           (100.000%)
                    icmp4: 12           ( 36.364%)
                    icmp6: 3            (  9.091%)
                     ipv4: 17           ( 51.515%)
                     ipv6: 3            (  9.091%)
                      tcp: 5            ( 15.152%)
--------------------------------------------------
Module Statistics
--------------------------------------------------
appid
                  packets: 18
        processed_packets: 16
          ignored_packets: 2
           total_sessions: 3
--------------------------------------------------
arp_spoof
                  packets: 13
--------------------------------------------------
binder
              raw_packets: 15
                new_flows: 3
                 inspects: 18
--------------------------------------------------
detection
                 analyzed: 33
--------------------------------------------------
port_scan
                  packets: 20
                 trackers: 8
--------------------------------------------------
stream
                    flows: 3
--------------------------------------------------
stream_icmp
                 sessions: 2
                      max: 2
                  created: 2
                 released: 2
--------------------------------------------------
stream_tcp
                 sessions: 1
                      max: 1
                  created: 1
                 released: 1
             instantiated: 1
                   setups: 1
            data_trackers: 1
              segs_queued: 1
            segs_released: 1
                 max_segs: 1
                max_bytes: 64
--------------------------------------------------
tcp
        bad_tcp4_checksum: 2
--------------------------------------------------
Appid Statistics
--------------------------------------------------
detected apps and services
              Application: Services   Clients    Users      Payloads   Misc       Referred
                  unknown: 1          0          0          0          0          0
--------------------------------------------------
Summary Statistics
--------------------------------------------------
process
                  signals: 1
--------------------------------------------------
timing
                  runtime: 00:00:25
                  seconds: 25.182182
                 pkts/sec: 1
o")~   Snort exiting

```

# **Snort Rules**

Las reglas de inicio, que se asemejan a las reglas de Suricata, están compuestas por un`rule header`y`rule options`. A pesar de que las reglas de Snort comparten similitudes con las reglas de Suricata, sugerimos firmemente estudiar la redacción de reglas de Snort a partir de los siguientes recursos:[https://docs.snort.org/](https://docs.snort.org/), [https://docs.suricata.io/en/latest/rules/differences-from-snort.html](https://docs.suricata.io/en/latest/rules/differences-from-snort.html). Las reglas de Snort más recientes se pueden obtener del sitio web de Snort o el sitio web de amenazas emergentes.

En las implementaciones de Snort, tenemos flexibilidad en la gestión de reglas. Es posible colocar reglas (por ejemplo,`local.rules`residente en`/home/htb-student`) directamente dentro del`snort.lua`archivo de configuración utilizando el`ips`módulo de la siguiente manera.

Snort Fundaments

```
xnoxos@htb[/htb]$ sudo vim /root/snorty/etc/snort/snort.lua
```

Snort Fundaments

```
---SNIP---
ips =
{
    -- use this to enable decoder and inspector alerts
    --enable_builtin_rules = true,

    -- use include for rules files; be sure to set your path
    -- note that rules files can include other rules files
    -- (see also related path vars at the top of snort_defaults.lua)

    { variables = default_variables, include = '/home/htb-student/local.rules' }
}
---SNIP---

```

Luego, las reglas "incluidas" se cargarán automáticamente.

En nuestra implementación de Snort, tenemos un enfoque alternativo para incorporar reglas directamente desde la línea de comando. Podemos aprobar un archivo de reglas único o una ruta a un directorio que contiene archivos de reglas directamente a Snort. Esto se puede lograr utilizando dos opciones:

- Para un solo archivo de reglas, podemos usar el`R`opción seguida de la ruta al archivo de reglas. Esto nos permite especificar un archivo de reglas específico para ser utilizado por Snort.
- Para incluir un directorio completo de archivos de reglas, podemos usar el`-rule-path`opción seguida de la ruta al directorio de reglas. Esto nos permite proporcionar a Snort un directorio que contiene múltiples archivos de reglas.

# **Salidas**

En nuestra implementación de Snort, podemos encontrar una cantidad significativa de datos. Para proporcionar un resumen de los tipos de salida del núcleo, exploremos los aspectos clave:

- `Basic Statistics`: Tras el apagado, Snort genera varios recuentos según la configuración y el tráfico procesado. Esto incluye:
    - `Packet Statistics`: Incluye información del DAQ y los decodificadores, como el número de paquetes recibidos y paquetes UDP.
    - `Module Statistics`: Cada módulo realiza un seguimiento de la actividad a través de los recuentos de PEG, lo que indica la frecuencia de las acciones observadas o realizadas. Los ejemplos incluyen el recuento de solicitudes de obtención de HTTP procesadas y paquetes de reinicio TCP recortados.
    - `File Statistics`: Esta sección proporciona un desglose de los tipos de archivos, bytes y firmas.
    - `Summary Statistics`: Abarca el tiempo de ejecución total para el procesamiento de paquetes, los paquetes por segundo y, si se configuran, los datos de perfiles.
- `Alerts`: Cuando se configuran las reglas, es necesario habilitar la alerta (usando el`A`opción) para ver los detalles de los eventos de detección. Hay múltiples tipos de salidas de alerta disponibles, que incluyen:
    - `A cmg`: Esta opción se combina`A fast -d -e`y muestra información de alerta junto con encabezados de paquetes y carga útil.
    - `A u2`: Esta opción es equivalente a`A unified2`y registra eventos y activación de paquetes en un archivo binario, que se puede utilizar para el procesamiento posterior con otras herramientas.
    - `A csv`: Esta opción genera campos en formato de valor separado por comas, proporcionando opciones de personalización y facilitando el análisis de PCAP.
    
    Para descubrir los tipos de alerta disponibles, podemos ejecutar el siguiente comando.
    

Snort Fundaments

```
xnoxos@htb[/htb]$ snort --list-plugins | grep loggerlogger::alert_csv v0 static
logger::alert_fast v0 static
logger::alert_full v0 static
logger::alert_json v0 static
logger::alert_syslog v0 static
logger::alert_talos v0 static
logger::alert_unixsock v0 static
logger::log_codecs v0 static
logger::log_hext v0 static
logger::log_pcap v0 static
logger::unified2 v0 static

```

- `Performance Statistics`: Más allá de las salidas antes mencionadas, se pueden obtener datos adicionales. Configurando el`perf_monitor`módulo, podemos capturar un conjunto personalizable de`peg`cuenta durante el tiempo de ejecución. Estos datos pueden alimentarse en un programa externo para monitorear la actividad de Snort sin interrumpir su funcionamiento. El`profiler`El módulo permite el seguimiento del tiempo y el uso del espacio por módulos y reglas. Esta información es valiosa para optimizar el rendimiento del sistema. La salida del perfilador aparece en`Summary Statistics`durante el cierre.

Veamos un ejemplo del`cmg`producción.

Snort Fundaments

```
xnoxos@htb[/htb]$ sudo snort -c /root/snorty/etc/snort/snort.lua --daq-dir /usr/local/lib/daq -r /home/htb-student/pcaps/icmp.pcap -A cmg--------------------------------------------------
o")~   Snort++ 3.1.64.0
--------------------------------------------------
Loading /root/snorty/etc/snort/snort.lua:
Loading snort_defaults.lua:
Finished snort_defaults.lua:
--SNIP---
Finished /root/snorty/etc/snort/snort.lua:
Loading file_id.rules_file:
Loading file_magic.rules:
Finished file_magic.rules:
Finished file_id.rules_file:
Loading /home/htb-student/local.rules:
Finished /home/htb-student/local.rules:
--------------------------------------------------
ips policies rule stats
              id  loaded  shared enabled    file
               0     209       0     209    /root/snorty/etc/snort/snort.lua
--------------------------------------------------
rule counts
       total rules loaded: 209
               text rules: 209
            option chains: 209
            chain headers: 2
--------------------------------------------------
port rule counts
             tcp     udp    icmp      ip
     any       0       0       1       0
   total       0       0       1       0
--------------------------------------------------
service rule counts          to-srv  to-cli
                  file_id:      208     208
                    total:      208     208
--------------------------------------------------
fast pattern groups
                to_server: 1
                to_client: 1
--------------------------------------------------
search engine (ac_bnfa)
                instances: 2
                 patterns: 416
            pattern chars: 2508
               num states: 1778
         num match states: 370
             memory scale: KB
             total memory: 68.5879
           pattern memory: 18.6973
        match list memory: 27.3281
        transition memory: 22.3125
appid: MaxRss diff: 3024
appid: patterns loaded: 300
--------------------------------------------------
pcap DAQ configured to read-file.
Commencing packet processing
++ [0] /home/htb-student/pcaps/icmp.pcap
06/19-08:45:56.838904 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55107 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:8448  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:57.055699 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30433 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:8448  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:57.840049 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55110 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:8704  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:58.044196 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30436 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:8704  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:58.841168 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55113 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:8960  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:59.085428 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30448 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:8960  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:59.841775 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55118 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:9216  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:46:00.042354 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30453 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:9216  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

-- [0] /home/htb-student/pcaps/icmp.pcap
--------------------------------------------------
Packet Statistics
--------------------------------------------------
daq
                    pcaps: 1
                 received: 8
                 analyzed: 8
                    allow: 8
                 rx_bytes: 592
--------------------------------------------------
codec
                    total: 8            (100.000%)
                      eth: 8            (100.000%)
                    icmp4: 8            (100.000%)
                     ipv4: 8            (100.000%)
--------------------------------------------------
Module Statistics
--------------------------------------------------
appid
                  packets: 8
        processed_packets: 8
           total_sessions: 1
--------------------------------------------------
binder
                new_flows: 1
                 inspects: 1
--------------------------------------------------
detection
                 analyzed: 8
               hard_evals: 8
                   alerts: 8
             total_alerts: 8
                   logged: 8
--------------------------------------------------
port_scan
                  packets: 8
                 trackers: 2
--------------------------------------------------
search_engine
         qualified_events: 8
--------------------------------------------------
stream
                    flows: 1
--------------------------------------------------
stream_icmp
                 sessions: 1
                      max: 1
                  created: 1
                 released: 1
--------------------------------------------------
Summary Statistics
--------------------------------------------------
timing
                  runtime: 00:00:00
                  seconds: 0.042473
                 pkts/sec: 188
o")~   Snort exiting

```

El mismo comando pero usando un`.rules`archivos que pueden no estar "incluidos" en`snort.lua`es el siguiente.

Snort Fundaments

```
xnoxos@htb[/htb]$ sudo snort -c /root/snorty/etc/snort/snort.lua --daq-dir /usr/local/lib/daq -r /home/htb-student/pcaps/icmp.pcap -R /home/htb-student/local.rules -A cmg--------------------------------------------------
o")~   Snort++ 3.1.64.0
--------------------------------------------------
Loading /root/snorty/etc/snort/snort.lua:
Loading snort_defaults.lua:
Finished snort_defaults.lua:
--SNIP---
Finished /root/snorty/etc/snort/snort.lua:
Loading file_id.rules_file:
Loading file_magic.rules:
Finished file_magic.rules:
Finished file_id.rules_file:
Loading /home/htb-student/local.rules:
Finished /home/htb-student/local.rules:
--------------------------------------------------
ips policies rule stats
             id  loaded  shared enabled    file
              0     209       0     209    /root/snorty/etc/snort/snort.lua
--------------------------------------------------
rule counts
      total rules loaded: 209
              text rules: 209
           option chains: 209
           chain headers: 2
--------------------------------------------------
port rule counts
            tcp     udp    icmp      ip
    any       0       0       1       0
  total       0       0       1       0
--------------------------------------------------
service rule counts          to-srv  to-cli
                 file_id:      208     208
                   total:      208     208
--------------------------------------------------
fast pattern groups
               to_server: 1
               to_client: 1
--------------------------------------------------
search engine (ac_bnfa)
               instances: 2
                patterns: 416
           pattern chars: 2508
              num states: 1778
        num match states: 370
            memory scale: KB
            total memory: 68.5879
          pattern memory: 18.6973
       match list memory: 27.3281
       transition memory: 22.3125
appid: MaxRss diff: 3024
appid: patterns loaded: 300
--------------------------------------------------
pcap DAQ configured to read-file.
Commencing packet processing
++ [0] /home/htb-student/pcaps/icmp.pcap
06/19-08:45:56.838904 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55107 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:8448  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:57.055699 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30433 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:8448  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:57.840049 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55110 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:8704  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:58.044196 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30436 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:8704  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:58.841168 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55113 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:8960  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:59.085428 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30448 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:8960  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:45:59.841775 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 192.168.158.139 -> 174.137.42.77
00:0C:29:34:0B:DE -> 00:50:56:E0:14:49 type:0x800 len:0x4A
192.168.158.139 -> 174.137.42.77 ICMP TTL:128 TOS:0x0 ID:55118 IpLen:20 DgmLen:60
Type:8  Code:0  ID:512   Seq:9216  ECHO

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

06/19-08:46:00.042354 [**] [1:1000001:1] "ICMP test" [**] [Classification: Generic ICMP event] [Priority: 3] {ICMP} 174.137.42.77 -> 192.168.158.139
00:50:56:E0:14:49 -> 00:0C:29:34:0B:DE type:0x800 len:0x4A
174.137.42.77 -> 192.168.158.139 ICMP TTL:128 TOS:0x0 ID:30453 IpLen:20 DgmLen:60
Type:0  Code:0  ID:512  Seq:9216  ECHO REPLY

snort.raw[32]:
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -
61 62 63 64 65 66 67 68  69 6A 6B 6C 6D 6E 6F 70  abcdefgh ijklmnop
71 72 73 74 75 76 77 61  62 63 64 65 66 67 68 69  qrstuvwa bcdefghi
- - - - - - - - - - - -  - - - - - - - - - - - -  - - - - - - - - -

-- [0] /home/htb-student/pcaps/icmp.pcap
--------------------------------------------------
Packet Statistics
--------------------------------------------------
daq
                   pcaps: 1
                received: 8
                analyzed: 8
                   allow: 8
                rx_bytes: 592
--------------------------------------------------
codec
                   total: 8            (100.000%)
                     eth: 8            (100.000%)
                   icmp4: 8            (100.000%)
                    ipv4: 8            (100.000%)
--------------------------------------------------
Module Statistics
--------------------------------------------------
appid
                 packets: 8
       processed_packets: 8
          total_sessions: 1
--------------------------------------------------
binder
               new_flows: 1
                inspects: 1
--------------------------------------------------
detection
                analyzed: 8
              hard_evals: 8
                  alerts: 8
            total_alerts: 8
                  logged: 8
--------------------------------------------------
port_scan
                 packets: 8
                trackers: 2
--------------------------------------------------
search_engine
        qualified_events: 8
--------------------------------------------------
stream
                   flows: 1
--------------------------------------------------
stream_icmp
                sessions: 1
                     max: 1
                 created: 1
                released: 1
--------------------------------------------------
Summary Statistics
--------------------------------------------------
timing
                 runtime: 00:00:00
                 seconds: 0.042473
                pkts/sec: 188
o")~   Snort exiting

```

# **Características de la tecla Snort**

Las características clave que refuerzan la efectividad de Snort incluyen:

- Inspección profunda de paquetes, captura de paquetes y registro
- Detección de intrusos
- Monitoreo de seguridad de red
- Detección de anomalías
- Apoyo para múltiples inquilinos
- Se admiten tanto IPv6 como IPv4