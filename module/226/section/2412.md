# Fundamentos de Suricata

Considerado como un instrumento potente para los sistemas de detección de intrusos de red (IDS), sistemas de prevención de intrusiones (IPS) y monitoreo de seguridad de red (NSM), Suricata representa una piedra angular de la seguridad de la red. Esta potencia de código abierto, administrada y desarrollada por Open Information Security Foundation (OISF), es un testimonio de la fortaleza de una iniciativa sin fines de lucro dirigida por la comunidad.

El objetivo de Suricata es diseccionar cada iota del tráfico de redes, buscando posibles signos de actividades maliciosas. Su fuerza radica en la capacidad no solo de realizar una evaluación radical de la condición de nuestra red, sino que también profundiza en los detalles de las transacciones individuales de la capa de aplicación. La clave de la operación exitosa de Suricata se encuentra en un conjunto de reglas intrincadamente diseñado. Estas pautas dirigen el proceso de análisis de Suricata, identificando posibles amenazas y áreas de interés. Equipado para funcionar a altas velocidades tanto en hardware estándar y diseñado específicamente, la eficiencia de Suricata es insuperable.

# **Modos de operación de Suricata**

Suricata opera en cuatro (4) modos distintos:

1. El`Intrusion Detection System (IDS) mode`posiciona a Suricata como un observador silencioso. En esta capacidad, Suricata examina meticulosamente el tráfico, marcando los posibles ataques pero absteniendo de cualquier forma de intervención. Al proporcionar una visión en profundidad de las actividades de la red y acelerar los tiempos de respuesta, este modo aumenta la visibilidad de la red, aunque sin ofrecer protección directa.
2. En el`Intrusion Prevention System (IPS) mode`, Suricata adopta una postura proactiva. Todo el tráfico de la red debe pasar a través de las estrictas controles de Suricata y solo se le otorga acceso a la red interna al aprobar la aprobación de Suricata. Este modo refuerza la seguridad al frustrar de forma proactiva los ataques antes de penetrar en nuestra red interna. La implementación de Suricata en modo IPS exige una comprensión íntima del panorama de la red para evitar el bloqueo inadvertido del tráfico legítimo. Además, cada activación de reglas requiere pruebas y validación rigurosas. Si bien este modo mejora la seguridad, el proceso de inspección puede introducir la latencia.
3. El`Intrusion Detection Prevention System (IDPS) mode`Reúne lo mejor de ID y IP. Si bien Suricata continúa monitoreando pasivamente el tráfico, posee la capacidad de transmitir activamente paquetes RST en respuesta a actividades anormales. Este modo entabla un equilibrio entre la protección activa y el mantenimiento de baja latencia, crucial para operaciones de red sin interrupciones.
4. En su`Network Security Monitoring (NSM) mode`, Suricata hace transiciones a un mecanismo de registro dedicado, evitando el análisis de tráfico activo o pasivo o las capacidades de prevención. Registra meticulosamente cada información de red que encuentra, proporcionando una valiosa riqueza de datos para investigaciones de incidentes de seguridad retrospectivos, a pesar del alto volumen de datos generados.

# **Entradas de Suricata**

Con respecto a las entradas de Suricata, hay dos categorías principales:

1. `Offline Input:`Esto implica leer archivos PCAP para procesar paquetes previamente capturados en el`LibPCAP`formato de archivo. No solo es ventajoso realizar un examen de datos post mortem, sino también instrumental cuando se experimenta con varios conjuntos y configuraciones de reglas.
2. `Live Input`: La entrada en vivo se puede facilitar a través de`LibPCAP`, donde los paquetes se leen directamente desde las interfaces de red. Sin embargo,`LibPCAP`está algo obstaculizado por sus limitaciones de rendimiento y la falta de capacidades de equilibrio de carga. Para operaciones en línea,`NFQ`y`AF_PACKET`Las opciones están disponibles.`NFQ`, un modo IPS en línea específico de Linux, colabora con iptables para desviar los paquetes del espacio del núcleo a Suricata para un escrutinio detallado. Comúnmente usado en línea,`NFQ`requiere reglas de caída para que Suricata obstruya efectivamente los paquetes. En cambio,`AF_PACKET`proporciona una mejora del rendimiento sobre`LibPCAP`y admite múltiples subprocesos. Sin embargo, no es compatible con las distribuciones de Linux más antiguas y no se puede emplear en línea si la máquina también tiene la tarea de enrutar paquetes.

Tenga en cuenta que hay otras entradas disponibles, menos comúnmente utilizadas o más avanzadas disponibles.

# **Salidas de Suricata**

Suricata crea múltiples salidas, incluidos registros, alertas y datos adicionales relacionados con la red, como solicitudes DNS y flujos de red. Una de las salidas más críticas es`EVE`, un registro formateado JSON que registra una amplia gama de tipos de eventos que incluyen alertas, HTTP, DNS, metadatos TLS, Drop, metadatos SMTP, flujo, flujo de red y más. Herramientas como Logstash pueden consumir fácilmente esta salida, facilitando el análisis de datos.

Podríamos encontrarnos`Unified2`La salida de Suricata, que es esencialmente un formato de alerta binaria de Snort, que permite la integración con otro software que aprovecha Unified2. Cualquier salida de unificada se puede leer utilizando Snort's`u2spewfoo`Herramienta, que es un método directo y efectivo para obtener información sobre los datos de alerta.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, ssh en la IP objetivo utilizando las credenciales proporcionadas. La gran mayoría de los comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Configuración de Suricata y reglas personalizadas**

Una vez que hayamos accedido a la instancia de Suricata implementada sobre SSH, podemos obtener una descripción general de todos los archivos de reglas con un comando de ejecución simple.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ ls -lah /etc/suricata/rules/total 27M
drwxr-xr-x 2 root root 4.0K Jun 28 12:10 .
drwxr-xr-x 3 root root 4.0K Jul  4 14:44 ..
-rw-r--r-- 1 root root  31K Jun 27 20:55 3coresec.rules
-rw-r--r-- 1 root root 1.9K Jun 15 05:51 app-layer-events.rules
-rw-r--r-- 1 root root 2.1K Jun 27 20:55 botcc.portgrouped.rules
-rw-r--r-- 1 root root  27K Jun 27 20:55 botcc.rules
-rw-r--r-- 1 root root 109K Jun 27 20:55 ciarmy.rules
-rw-r--r-- 1 root root  12K Jun 27 20:55 compromised.rules
-rw-r--r-- 1 root root  21K Jun 15 05:51 decoder-events.rules
-rw-r--r-- 1 root root  468 Jun 15 05:51 dhcp-events.rules
-rw-r--r-- 1 root root 1.2K Jun 15 05:51 dnp3-events.rules
-rw-r--r-- 1 root root 1.2K Jun 15 05:51 dns-events.rules
-rw-r--r-- 1 root root  32K Jun 27 20:55 drop.rules
-rw-r--r-- 1 root root 2.7K Jun 27 20:55 dshield.rules
-rw-r--r-- 1 root root 365K Jun 27 20:55 emerging-activex.rules
-rw-r--r-- 1 root root 613K Jun 27 20:55 emerging-adware_pup.rules
-rw-r--r-- 1 root root 650K Jun 27 20:55 emerging-attack_response.rules
-rw-r--r-- 1 root root  33K Jun 27 20:55 emerging-chat.rules
-rw-r--r-- 1 root root  19K Jun 27 20:55 emerging-coinminer.rules
-rw-r--r-- 1 root root 119K Jun 27 20:55 emerging-current_events.rules
-rw-r--r-- 1 root root 1.7M Jun 27 20:55 emerging-deleted.rules
-rw-r--r-- 1 root root  20K Jun 27 20:55 emerging-dns.rules
-rw-r--r-- 1 root root  62K Jun 27 20:55 emerging-dos.rules
-rw-r--r-- 1 root root 606K Jun 27 20:55 emerging-exploit_kit.rules
-rw-r--r-- 1 root root 1.1M Jun 27 20:55 emerging-exploit.rules
-rw-r--r-- 1 root root  45K Jun 27 20:55 emerging-ftp.rules
-rw-r--r-- 1 root root  37K Jun 27 20:55 emerging-games.rules
-rw-r--r-- 1 root root 572K Jun 27 20:55 emerging-hunting.rules
-rw-r--r-- 1 root root  18K Jun 27 20:55 emerging-icmp_info.rules
-rw-r--r-- 1 root root  11K Jun 27 20:55 emerging-icmp.rules
-rw-r--r-- 1 root root  15K Jun 27 20:55 emerging-imap.rules
-rw-r--r-- 1 root root  11K Jun 27 20:55 emerging-inappropriate.rules
-rw-r--r-- 1 root root 2.9M Jun 27 20:55 emerging-info.rules
-rw-r--r-- 1 root root  48K Jun 27 20:55 emerging-ja3.rules
-rw-r--r-- 1 root root 8.2M Jun 27 20:55 emerging-malware.rules
---SNIP---

```

Las reglas se pueden ver en un formato de lista directo y ser inspeccionados para comprender su funcionalidad, de la siguiente manera.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ more /etc/suricata/rules/emerging-malware.rules# Emerging Threats#
# This distribution may contain rules under two different licenses.#
#  Rules with sids 1 through 3464, and 100000000 through 100000908 are under the GPLv2.#  A copy of that license is available at http://www.gnu.org/licenses/gpl-2.0.html#
#  Rules with sids 2000000 through 2799999 are from Emerging Threats and are covered under the BSD License#  as follows:#
#*************************************************************#  Copyright (c) 2003-2022, Emerging Threats#  All rights reserved.#
#  Redistribution and use in source and binary forms, with or without modification, are permitted provided that the#  following conditions are met:#
#  * Redistributions of source code must retain the above copyright notice, this list of conditions and the following#    disclaimer.#  * Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the#    following disclaimer in the documentation and/or other materials provided with the distribution.#  * Neither the name of the nor the names of its contributors may be used to endorse or promote products derived#    from this software without specific prior written permission.#
#  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS AS IS AND ANY EXPRESS OR IMPLIED WARRANTIES,#  INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE#  DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,#  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR#  SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,#  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE#  USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.#
#*************************************************************#
#
#
#

# This Ruleset is EmergingThreats Open optimized for suricata-5.0-enhanced.#alert tcp $HOME_NET any -> $EXTERNAL_NET any (msg:"ET MALWARE Psyb0t joining an IRC Channel"; flow:established,to_server; flowbits:isset,is_proto_irc; content:"
JOIN #mipsel"; reference:url,www.adam.com.au/bogaurd/PSYB0T.pdf; reference:url,doc.emergingthreats.net/2009172; classtype:trojan-activity; sid:2009172; rev:2; metadata:created_at 2010_07_30, updated_at 2010_07_30;)

alert tcp$HOME_NET any -> $EXTERNAL_NET 25 (msg:"ET MALWARE SC-KeyLog Keylogger Installed - Sending Initial Email Report"; flow:established,to_server; content:"
Installation of SC-KeyLog on host "; nocase; content:"<p>You will receive a log report every "; nocase; reference:url,www.soft-central.net/keylog.php; reference:url,doc.emergingthreats.net/2002979; classtype:trojan-activity; sid:2002979; rev:4; metadata:created_at 2010_07_30, updated_at 2010_07_30;)

alert tcp$HOME_NET any -> $EXTERNAL_NET 25 (msg:"ET MALWARE SC-KeyLog Keylogger Installed - Sending Log Email Report"; flow:established,to_server; content:"SC-K
eyLog log report"; nocase; content:"See attached file"; nocase; content:".log"; nocase; reference:url,www.soft-central.net/keylog.php; reference:url,doc.emergingthreats.net/2008348; classtype:trojan-activity; sid:2008348; rev:2; metadata:created_at 2010_07_30, updated_at 2010_07_30;)
---SNIP---

```

Las reglas pueden comentarse, lo que significa que no están cargados y no afectan el sistema. Esto generalmente sucede cuando entra en juego una nueva versión de la regla o si la amenaza asociada con la regla se vuelve anticuada o irrelevante.

Cada regla generalmente implica variables específicas, como`$HOME_NET`y`$EXTERNAL_NET`. La regla examina el tráfico de las direcciones IP especificadas en el`$HOME_NET`rumbo de variable hacia las direcciones IP en el`$EXTERNAL_NET`variable.

Estas variables se pueden definir en el`suricata.yaml`archivo de configuración.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ more /etc/suricata/suricata.yaml%YAML 1.1
---

# Suricata configuration file. In addition to the comments describing all# options in this file, full documentation can be found at:# https://suricata.readthedocs.io/en/latest/configuration/suricata-yaml.html# This configuration file generated by Suricata 6.0.13.suricata-version: "6.0"

#### Step 1: Inform Suricata about your network##vars:
# more specific is better for alert accuracy and performance  address-groups:
    HOME_NET: "[10.0.0.0/8]"
#HOME_NET: "[192.168.0.0/16]"#HOME_NET: "[10.0.0.0/8]"#HOME_NET: "[172.16.0.0/12]"#HOME_NET: "any"    EXTERNAL_NET: "!$HOME_NET"
    #EXTERNAL_NET: "any"

    HTTP_SERVERS: "$HOME_NET"
    SMTP_SERVERS: "$HOME_NET"
    SQL_SERVERS: "$HOME_NET"
    DNS_SERVERS: "$HOME_NET"
    TELNET_SERVERS: "$HOME_NET"
    AIM_SERVERS: "$EXTERNAL_NET"
    DC_SERVERS: "$HOME_NET"
    DNP3_SERVER: "$HOME_NET"
---SNIP---

```

Esto nos permite personalizar estas variables de acuerdo con nuestro entorno de red específico e incluso definir nuestras propias variables.

Finalmente, para configurar Suricata para cargar firmas de un archivo de reglas personalizadas, como`local.rules`en el`/home/htb-student`Directorio, ejecutaríamos el siguiente.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo vim /etc/suricata/suricata.yaml
```

1. Agregar`/home/htb-student/local.rules`a`rule-files:`
2. Presione el`Esc`llave
3. Ingresar`:wq`Y luego, presione el`Enter`llave

El archivo Local.Rules que reside en el directorio /Home /HTB-Student del objetivo de esta sección ya contiene una regla de Suricata. Esta regla es adecuada para los objetivos de aprendizaje de esta sección. Elaboraremos más sobre el desarrollo de reglas de Suricata en la siguiente sección.

# **Práctico con entradas de Suricata**

Con las entradas de Suricata, podemos experimentar con la entrada fuera de línea y en vivo:

1. Para entrada fuera de línea (leer archivos PCAP -[sospechoso.pcap](https://dingtoffee.medium.com/holiday-hack-challenge-2022-writeup-stage-2-recover-the-tolkien-ring-3f76fcd7dab9)En este caso), el siguiente comando debe ser ejecutado, y Suricata creará varios registros (principalmente`eve.json`, `fast.log`, y`stats.log`).
    
    ```
    xnoxos@htb[/htb]$ suricata -r /home/htb-student/pcaps/suspicious.pcap5/7/2023 -- 13:35:51 - <Notice> - This is Suricata version 6.0.13 RELEASE running in USER mode
    5/7/2023 -- 13:35:51 - <Notice> - all 3 packet processing threads, 4 management threads initialized, engine started.
    5/7/2023 -- 13:35:51 - <Notice> - Signal Received.  Stopping engine.
    5/7/2023 -- 13:35:51 - <Notice> - Pcap-file module read 1 files, 5172 packets, 3941260 bytes
    
    ```
    
    Se puede ejecutar un comando alternativo para omitir las suma de verificación (`-k`indicador) e iniciar sesión en un directorio diferente (`-l`bandera).
    
    ```
    xnoxos@htb[/htb]$ suricata -r /home/htb-student/pcaps/suspicious.pcap -k none -l .5/7/2023 -- 13:37:43 - <Notice> - This is Suricata version 6.0.13 RELEASE running in USER mode
    5/7/2023 -- 13:37:43 - <Notice> - all 3 packet processing threads, 4 management threads initialized, engine started.
    5/7/2023 -- 13:37:43 - <Notice> - Signal Received.  Stopping engine.
    5/7/2023 -- 13:37:43 - <Notice> - Pcap-file module read 1 files, 5172 packets, 3941260 bytes
    
    ```
    
2. Para obtener información en vivo, podemos probar Suricata (en vivo)`LibPCAP`modo de la siguiente manera.
    
    ```
    xnoxos@htb[/htb]$ ifconfigens160: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.129.205.193  netmask 255.255.0.0  broadcast 10.129.255.255
        inet6 dead:beef::250:56ff:feb9:68dc  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::250:56ff:feb9:68dc  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:68:dc  txqueuelen 1000  (Ethernet)
        RX packets 281625  bytes 84557478 (84.5 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 62276  bytes 23518127 (23.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 888  bytes 64466 (64.4 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 888  bytes 64466 (64.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    ```
    
    ```
    xnoxos@htb[/htb]$ sudo suricata --pcap=ens160 -vv[sudo] password for htb-student:
    5/7/2023 -- 13:44:01 - <Notice> - This is Suricata version 6.0.13 RELEASE running in SYSTEM mode
    5/7/2023 -- 13:44:01 - <Info> - CPUs/cores online: 2
    5/7/2023 -- 13:44:01 - <Info> - Setting engine mode to IDS mode by default
    5/7/2023 -- 13:44:01 - <Info> - Found an MTU of 1500 for 'ens160'
    5/7/2023 -- 13:44:01 - <Info> - Found an MTU of 1500 for 'ens160'
    5/7/2023 -- 13:44:01 - <Info> - fast output device (regular) initialized: fast.log
    5/7/2023 -- 13:44:01 - <Info> - eve-log output device (regular) initialized: eve.json
    5/7/2023 -- 13:44:01 - <Info> - stats output device (regular) initialized: stats.log
    5/7/2023 -- 13:44:01 - <Info> - Running in live mode, activating unix socket
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for http_uri
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for http_uri
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for http_raw_uri
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for http_raw_uri
    5/7/2023 -- 13:44:01 - <Info> - 1 rule files processed. 1 rules successfully loaded, 0 rules failed
    5/7/2023 -- 13:44:01 - <Info> - Threshold config parsed: 0 rule(s) found
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for tcp-packet
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for tcp-stream
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for udp-packet
    5/7/2023 -- 13:44:01 - <Perf> - using shared mpm ctx' for other-ip
    5/7/2023 -- 13:44:01 - <Info> - 1 signatures processed. 0 are IP-only rules, 0 are inspecting packet payload, 1 inspect application layer, 0 are decoder event only
    5/7/2023 -- 13:44:01 - <Perf> - TCP toserver: 1 port groups, 1 unique SGH's, 0 copies
    5/7/2023 -- 13:44:01 - <Perf> - TCP toclient: 0 port groups, 0 unique SGH's, 0 copies
    5/7/2023 -- 13:44:01 - <Perf> - UDP toserver: 1 port groups, 1 unique SGH's, 0 copies
    5/7/2023 -- 13:44:01 - <Perf> - UDP toclient: 0 port groups, 0 unique SGH's, 0 copies
    5/7/2023 -- 13:44:01 - <Perf> - OTHER toserver: 0 proto groups, 0 unique SGH's, 0 copies
    5/7/2023 -- 13:44:01 - <Perf> - OTHER toclient: 0 proto groups, 0 unique SGH's, 0 copies
    5/7/2023 -- 13:44:01 - <Perf> - Unique rule groups: 2
    5/7/2023 -- 13:44:01 - <Perf> - Builtin MPM "toserver TCP packet": 0
    5/7/2023 -- 13:44:01 - <Perf> - Builtin MPM "toclient TCP packet": 0
    5/7/2023 -- 13:44:01 - <Perf> - Builtin MPM "toserver TCP stream": 0
    5/7/2023 -- 13:44:01 - <Perf> - Builtin MPM "toclient TCP stream": 0
    5/7/2023 -- 13:44:01 - <Perf> - Builtin MPM "toserver UDP packet": 0
    5/7/2023 -- 13:44:01 - <Perf> - Builtin MPM "toclient UDP packet": 0
    5/7/2023 -- 13:44:01 - <Perf> - Builtin MPM "other IP packet": 0
    5/7/2023 -- 13:44:01 - <Perf> - AppLayer MPM "toserver dns_query (dns)": 1
    5/7/2023 -- 13:44:01 - <Info> - Using 1 live device(s).
    5/7/2023 -- 13:44:01 - <Info> - using interface ens160
    5/7/2023 -- 13:44:01 - <Perf> - ens160: disabling rxcsum offloading
    5/7/2023 -- 13:44:01 - <Perf> - ens160: disabling txcsum offloading
    5/7/2023 -- 13:44:01 - <Info> - running in 'auto' checksum mode. Detection of interface state will require 1000ULL packets
    5/7/2023 -- 13:44:01 - <Info> - Found an MTU of 1500 for 'ens160'
    5/7/2023 -- 13:44:01 - <Info> - Set snaplen to 1524 for 'ens160'
    5/7/2023 -- 13:44:01 - <Perf> - NIC offloading on ens160: RX unset TX unset
    5/7/2023 -- 13:44:01 - <Perf> - NIC offloading on ens160: SG: unset, GRO: unset, LRO: unset, TSO: unset, GSO: unset
    5/7/2023 -- 13:44:01 - <Info> - RunModeIdsPcapAutoFp initialised
    5/7/2023 -- 13:44:01 - <Info> - Running in live mode, activating unix socket
    5/7/2023 -- 13:44:01 - <Info> - Using unix socket file '/var/run/suricata/suricata-command.socket'
    5/7/2023 -- 13:44:01 - <Notice> - all 3 packet processing threads, 4 management threads initialized, engine started.
    
    ```
    
3. Para suricata en línea (`NFQ`) Modo, el siguiente comando debe ejecutarse primero.
    
    ```
    xnoxos@htb[/htb]$ sudo iptables -I FORWARD -j NFQUEUE
    ```
    
    Entonces, deberíamos poder ejecutar lo siguiente.
    
    ```
    xnoxos@htb[/htb]$ sudo suricata -q 05/7/2023 -- 13:52:38 - <Notice> - This is Suricata version 6.0.13 RELEASE running in SYSTEM mode
    5/7/2023 -- 13:52:39 - <Notice> - all 4 packet processing threads, 4 management threads initialized, engine started.
    
    ```
    
    Además, para probar Suricata en`IDS`modo`AF_PACKET`entrada, ejecute uno de los siguientes.
    
    ```
    xnoxos@htb[/htb]$ sudo suricata -i ens1605/7/2023 -- 13:53:35 - <Notice> - This is Suricata version 6.0.13 RELEASE running in SYSTEM mode
    5/7/2023 -- 13:53:35 - <Notice> - all 1 packet processing threads, 4 management threads initialized, engine started.
    
    ```
    
    ```
    xnoxos@htb[/htb]$ sudo suricata --af-packet=ens1605/7/2023 -- 13:54:34 - <Notice> - This is Suricata version 6.0.13 RELEASE running in SYSTEM mode
    5/7/2023 -- 13:54:34 - <Notice> - all 1 packet processing threads, 4 management threads initialized, engine started.
    
    ```
    

Para observar Suricata que trata con el tráfico "en vivo", establezcamos una conexión SSH adicional y utilicemos`tcpreplay`Para reproducir el tráfico de red desde un archivo PCAP (`suspicious.pcap`en este caso).

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo  tcpreplay -i ens160 /home/htb-student/pcaps/suspicious.pcap^C User interrupt...
sendpacket_abort
Actual: 730 packets (663801 bytes) sent in 22.84 seconds
Rated: 29060.3 Bps, 0.232 Mbps, 31.95 pps
Statistics for network device: ens160
        Successful packets:        729
        Failed packets:            0
        Truncated packets:         0
        Retried packets (ENOBUFS): 0
        Retried packets (EAGAIN):  0

```

Luego, siéntase libre de terminar tanto tcpreplay como suricata. Los registros del tráfico observado (reproducido) estarán disponibles en`/var/log/suricata`

La opción -i ayuda a Suricata a elegir la mejor opción de entrada. En el caso de Linux, la mejor opción de entrada es AF_Packet. Si se necesita el modo PCAP, se recomienda la opción - -PCAP. La configuración de (en vivo) libpcap se puede lograr a través del archivo suricata.yaml, incluida la configuración del tamaño del búfer, los filtros BPF o TCPDUMP, la validación de la suma de verificación, los hilos, el modo promiscuo, la longitud de la instantánea, etc.

# **Práctico con salidas de Suricata**

Suricata registra una variedad de datos en registros que residen en el`/var/log/suricata`directorio por defecto. Para que podamos acceder y manipular estos registros, requerimos acceso a nivel raíz. Entre estos registros, encontramos el`eve.json`, `fast.log`, y`stats.log`archivos, que proporcionan una visión invaluable de la actividad de la red. Vamos a profundizar en cada uno:

1. `eve.json`: Este archivo es la salida recomendada de Suricata y contiene objetos JSON, cada uno con información diversa, como marcas de tiempo, Flow_id, Event_Type y más. Intente inspeccionar el contenido de`old_eve.json`residente en`/var/log/suricata`como sigue.
    
    ```
    xnoxos@htb[/htb]$ less /var/log/suricata/old_eve.json{"timestamp":"2023-07-06T08:34:24.526482+0000","event_type":"stats","stats":{"uptime":8,"capture":{"kernel_packets":4,"kernel_drops":0,"errors":0},"decoder":{"pkts":3,"bytes":212,"invalid":0,"ipv4":0,"ipv6":1,"ethernet":3,"chdlc":0,"raw":0,"null":0,"sll":0,"tcp":0,"udp":0,"sctp":0,"icmpv4":0,"icmpv6":1,"ppp":0,"pppoe":0,"geneve":0,"gre":0,"vlan":0,"vlan_qinq":0,"vxlan":0,"vntag":0,"ieee8021ah":0,"teredo":0,"ipv4_in_ipv6":0,"ipv6_in_ipv6":0,"mpls":0,"avg_pkt_size":70,"max_pkt_size":110,"max_mac_addrs_src":0,"max_mac_addrs_dst":0,"erspan":0,"event":{"ipv4":{"pkt_too_small":0,"hlen_too_small":0,"iplen_smaller_than_hlen":0,"trunc_pkt":0,"opt_invalid":0,"opt_invalid_len":0,"opt_malformed":0,"opt_pad_required":0,"opt_eol_required":0,"opt_duplicate":0,"opt_unknown":0,"wrong_ip_version":0,"icmpv6":0,"frag_pkt_too_large":0,"frag_overlap":0,"frag_ignored":0},"icmpv4":{"pkt_too_small":0,"unknown_type":0,"unknown_code":0,"ipv4_trunc_pkt":0,"ipv4_unknown_ver":0},"icmpv6":{"unknown_type":0,"unknown_code":0,"pkt_too_small":0,"ipv6_unknown_version":0,"ipv6_trunc_pkt":0,"mld_message_with_invalid_hl":0,"unassigned_type":0,"experimentation_type":0},"ipv6":{"pkt_too_small":0,"trunc_pkt":0,"trunc_exthdr":0,"exthdr_dupl_fh":0,"exthdr_useless_fh":0,"exthdr_dupl_rh":0,"exthdr_dupl_hh":0,"exthdr_dupl_dh":0,"exthdr_dupl_ah":0,"exthdr_dupl_eh":0,"exthdr_invalid_optlen":0,"wrong_ip_version":0,"exthdr_ah_res_not_null":0,"hopopts_unknown_opt":0,"hopopts_only_padding":0,"dstopts_unknown_opt":0,"dstopts_only_padding":0,"rh_type_0":0,"zero_len_padn":0,"fh_non_zero_reserved_field":0,"data_after_none_header":0,"unknown_next_header":0,"icmpv4":0,"frag_pkt_too_large":0,"frag_overlap":0,"frag_invalid_length":0,"frag_ignored":0,"ipv4_in_ipv6_too_small":0,"ipv4_in_ipv6_wrong_version":0,"ipv6_in_ipv6_too_small":0,"ipv6_in_ipv6_wrong_version":0},"tcp":{"pkt_too_small":0,"hlen_too_small":0,"invalid_optlen":0,"opt_invalid_len":0,"opt_duplicate":0},"udp":{"pkt_too_small":0,"hlen_too_small":0,"hlen_invalid":0,"len_invalid":0},"sll":{"pkt_too_small":0},"ethernet":{"pkt_too_small":0},"ppp":{"pkt_too_small":0,"vju_pkt_too_small":0,"ip4_pkt_too_small":0,"ip6_pkt_too_small":0,"wrong_type":0,"unsup_proto":0},"pppoe":{"pkt_too_small":0,"wrong_code":0,"malformed_tags":0},"gre":{"pkt_too_small":0,"wrong_version":0,"version0_recur":0,"version0_flags":0,"version0_hdr_too_big":0,"version0_malformed_sre_hdr":0,"version1_chksum":0,"version1_route":0,"version1_ssr":0,"version1_recur":0,"version1_flags":0,"version1_no_key":0,"version1_wrong_protocol":0,"version1_malformed_sre_hdr":0,"version1_hdr_too_big":0},"vlan":{"header_too_small":0,"unknown_type":0,"too_many_layers":0},"ieee8021ah":{"header_too_small":0},"vntag":{"header_too_small":0,"unknown_type":0},"ipraw":{"invalid_ip_version":0},"ltnull":{"pkt_too_small":0,"unsupported_type":0},"sctp":{"pkt_too_small":0},"mpls":{"header_too_small":0,"pkt_too_small":0,"bad_label_router_alert":0,"bad_label_implicit_null":0,"bad_label_reserved":0,"unknown_payload_type":0},"vxlan":{"unknown_payload_type":0},"geneve":{"unknown_payload_type":0},"erspan":{"header_too_small":0,"unsupported_version":0,"too_many_vlan_layers":0},"dce":{"pkt_too_small":0},"chdlc":{"pkt_too_small":0}},"too_many_layers":0},"tcp":{"syn":0,"synack":0,"rst":0,"sessions":0,"ssn_memcap_drop":0,"pseudo":0,"pseudo_failed":0,"invalid_checksum":0,"midstream_pickups":0,"pkt_on_wrong_thread":0,"segment_memcap_drop":0,"stream_depth_reached":0,"reassembly_gap":0,"overlap":0,"overlap_diff_data":0,"insert_data_normal_fail":0,"insert_data_overlap_fail":0,"insert_list_fail":0,"memuse":606208,"reassembly_memuse":98304},"flow":{"memcap":0,"tcp":0,"udp":0,"icmpv4":0,"icmpv6":1,"tcp_reuse":0,"get_used":0,"get_used_eval":0,"get_used_eval_reject":0,"get_used_eval_busy":0,"get_used_failed":0,"wrk":{"spare_sync_avg":100,"spare_sync":1,"spare_sync_incomplete":0,"spare_sync_empty":0,"flows_evicted_needs_work":0,"flows_evicted_pkt_inject":0,"flows_evicted":0,"flows_injected":0},"mgr":{"full_hash_pass":1,"closed_pruned":0,"new_pruned":0,"est_pruned":0,"bypassed_pruned":0,"rows_maxlen":0,"flows_checked":0,"flows_notimeout":0,"flows_timeout":0,"flows_timeout_inuse":0,"flows_evicted":0,"flows_evicted_needs_work":0},"spare":9900,"emerg_mode_entered":0,"emerg_mode_over":0,"memuse":7394304},"defrag":{"ipv4":{"fragments":0,"reassembled":0,"timeouts":0},"ipv6":{"fragments":0,"reassembled":0,"timeouts":0},"max_frag_hits":0},"flow_bypassed":{"local_pkts":0,"local_bytes":0,"local_capture_pkts":0,"local_capture_bytes":0,"closed":0,"pkts":0,"bytes":0},"detect":{"engines":[{"id":0,"last_reload":"2023-07-06T08:34:16.502768+0000","rules_loaded":1,"rules_failed":0}],"alert":0,"alert_queue_overflow":0,"alerts_suppressed":0},"app_layer":{"flow":{"http":0,"ftp":0,"smtp":0,"tls":0,"ssh":0,"imap":0,"smb":0,"dcerpc_tcp":0,"dns_tcp":0,"nfs_tcp":0,"ntp":0,"ftp-data":0,"tftp":0,"ikev2":0,"krb5_tcp":0,"dhcp":0,"snmp":0,"sip":0,"rfb":0,"mqtt":0,"rdp":0,"failed_tcp":0,"dcerpc_udp":0,"dns_udp":0,"nfs_udp":0,"krb5_udp":0,"failed_udp":0},"tx":{"http":0,"ftp":0,"smtp":0,"tls":0,"ssh":0,"imap":0,"smb":0,"dcerpc_tcp":0,"dns_tcp":0,"nfs_tcp":0,"ntp":0,"ftp-data":0,"tftp":0,"ikev2":0,"krb5_tcp":0,"dhcp":0,"snmp":0,"sip":0,"rfb":0,"mqtt":0,"rdp":0,"dcerpc_udp":0,"dns_udp":0,"nfs_udp":0,"krb5_udp":0},"expectations":0},"http":{"memuse":0,"memcap":0},"ftp":{"memuse":0,"memcap":0},"file_store":{"open_files":0}}}
    ---SNIP---
    
    ```
    
    Si deseamos filtrar solo eventos de alerta, por ejemplo, podemos utilizar el`jq`Procesador JSON de línea de comandos de la siguiente manera.
    
    ```
    xnoxos@htb[/htb]$ cat /var/log/suricata/old_eve.json | jq -c 'select(.event_type == "alert")'{"timestamp":"2023-07-06T08:34:35.003163+0000","flow_id":1959965318909019,"in_iface":"ens160","event_type":"alert","src_ip":"10.9.24.101","src_port":51833,"d est_ip":"10.9.24.1","dest_port":53,"proto":"UDP","tx_id":0,"alert":{"action":"allowed","gid":1,"signature_id":1,"rev":0,"signature":"Known bad DNS lookup, possible Dridex infection","category":"","severity":3},"dns":{"query":[{"type":"query","id":6430,"rrname":"adv.epostoday.uk","rrtype":"A","tx_id":0,"opcode":0}    ]},"app_proto":"dns","flow":{"pkts_toserver":1,"pkts_toclient":0,"bytes_toserver":76,"bytes_toclient":0,"start":"2023-07-06T08:34:35.003163+0000"}}
    
    ```
    
    Si deseamos identificar el primer evento DNS, por ejemplo, podemos utilizar el`jq`Procesador JSON de línea de comandos de la siguiente manera.
    
    ```
    xnoxos@htb[/htb]$ cat /var/log/suricata/old_eve.json | jq -c 'select(.event_type == "dns")' | head -1 | jq .{
     "timestamp": "2023-07-06T08:34:35.003163+0000",
     "flow_id": 1959965318909019,
     "in_iface": "ens160",
      "event_type": "dns",
      "src_ip": "10.9.24.101",
     "src_port": 51833,
     "dest_ip": "10.9.24.1",
     "dest_port": 53,
     "proto": "UDP",
     "dns": {
      "type": "query",
      "id": 6430,
    	 "rrname": "adv.epostoday.uk",
    	 "rrtype": "A",
    	 "tx_id": 0,
    	"opcode": 0
     }
    }
    
    ```
    
    También podemos usar comandos similares para filtrar para otros tipos de eventos como TLS y SSH.
    
    `flow_id`: Este es un identificador único asignado por Suricata a cada flujo de red. Un flujo, en términos de Suricata, se define como un conjunto de paquetes IP que pasan a través de una interfaz de red en una dirección específica y entre un par dado de puntos finales de origen y destino. Cada uno de estos flujos obtiene un Flow_ID único. Este identificador nos ayuda a rastrear y correlacionar varios eventos relacionados con el mismo flujo de red en el registro EVE JSON. Usando`flow_id`, podemos asociar diferentes piezas de información relacionadas con el mismo flujo, como alertas, transacciones de red y paquetes, proporcionando una visión cohesiva de lo que está sucediendo en un canal de comunicación específico.
    
    `pcap_cnt`: Este es un contador que Suricata se incrementa para cada paquete que procesa desde el tráfico de red o desde un archivo PCAP (en modo fuera de línea).`pcap_cnt`nos permite rastrear un paquete a su orden original en el archivo PCAP o la transmisión de red. Esto es beneficioso para comprender la secuencia de eventos de red a medida que ocurrieron. Puede ayudar a identificar con precisión cuando se activó una alerta en relación con otros paquetes, lo que puede proporcionar un contexto valioso en una investigación.
    
2. `fast.log`: Este es un formato de registro basado en texto que registra solo alertas y está habilitado de forma predeterminada. Intente inspeccionar el contenido de`old_fast.log`residente en`/var/log/suricata`como sigue.
    
    ```
    xnoxos@htb[/htb]$ cat /var/log/suricata/old_fast.log07/06/2023-08:34:35.003163  [**] [1:1:0] Known bad DNS lookup, possible Dridex infection [**] [Classification: (null)] [Priority: 3] {UDP} 10.9.24.101:51833 -> 10.9.24.1:53
    
    ```
    
3. `stats.log`: Este es un registro de estadísticas legibles por humanos, que puede ser particularmente útil al depurar las implementaciones de Suricata. Intente inspeccionar el contenido de`old_stats.log`residente en`/var/log/suricata`como sigue.
    
    ```
    xnoxos@htb[/htb]$ cat /var/log/suricata/old_stats.log------------------------------------------------------------------------------------
    Date: 7/6/2023 -- 08:34:24 (uptime: 0d, 00h 00m 08s)
    ------------------------------------------------------------------------------------
    Counter                                       | TM Name                   | Value
    ------------------------------------------------------------------------------------
    capture.kernel_packets                        | Total                     | 4
    decoder.pkts                                  | Total                     | 3
    decoder.bytes                                 | Total                     | 212
    decoder.ipv6                                  | Total                     | 1
    decoder.ethernet                              | Total                     | 3
    decoder.icmpv6                                | Total                     | 1
    decoder.avg_pkt_size                          | Total                     | 70
    decoder.max_pkt_size                          | Total                     | 110
    flow.icmpv6                                   | Total                     | 1
    flow.wrk.spare_sync_avg                       | Total                     | 100
    flow.wrk.spare_sync                           | Total                     | 1
    flow.mgr.full_hash_pass                       | Total                     | 1
    flow.spare                                    | Total                     | 9900
    tcp.memuse                                    | Total                     | 606208
    tcp.reassembly_memuse                         | Total                     | 98304
    flow.memuse                                   | Total                     | 7394304
    ------------------------------------------------------------------------------------
    ---SNIP---
    
    ```
    

---

Para aquellos de nosotros que queremos una estrategia de salida más enfocada, existe una opción para desactivar lo integral`EVE`Salir y activar salidas particulares en su lugar. Llevar`http-log`, por ejemplo. Al activar esto, cada vez que Suricata se ejecuta y encuentra eventos HTTP, un nuevo`http.log`se generará el archivo.

# **Atitativo con salidas de Suricata - Extracción de archivos**

Suricata tiene una característica infrautilizada pero muy potente -[extracción de archivo](https://docs.suricata.io/en/suricata-6.0.13/file-extraction/file-extraction.html). Esta característica nos permite capturar y almacenar archivos transferidos en varios protocolos diferentes, proporcionando datos invaluables para la caza de amenazas, forense o simplemente análisis de datos.

Así es como hacemos habilitar la extracción de archivos en Suricata.

Comenzamos haciendo cambios en el archivo de configuración de Suricata (`suricata.yaml`). En este archivo, encontraremos una sección con nombre`file-store`. Aquí es donde le decimos a Suricata cómo manejar los archivos que extrae. Específicamente, necesitamos establecer`version`a`2`, habilitado para`yes`, y el`force-filestore`opción también para`yes`. La sección resultante debería verse algo así.

Fundamentos de Suricata

```
file-store:
  version: 2
  enabled: yes
  force-filestore: yes

```

En el mismo`file-store`Sección, definimos dónde almacena Suricata los archivos extraídos. Establecimos el`dir`opción para el directorio de nuestra elección.

Como ejercicio rápido, habilitemos la extracción de archivos y ejecutemos Suricata en el`/home/htb-student/pcaps/vm-2.pcap`presentar desde[www.netresec.com](https://www.netresec.com/?page=PcapFiles).

De acuerdo con las pautas presentadas en la documentación de Suricata, la extracción de archivos no es un proceso automático que ocurra sin nuestras instrucciones explícitas. Es fundamentalmente crucial para nosotros elaborar una regla específica que instruya a Suricata cuándo y qué tipo de archivos debería extraer.

La regla más simple que podemos agregar a nuestro`local.rules`El archivo para experimentar con la extracción de archivos es el siguiente.

Fundamentos de Suricata

```
alert http any any -> any any (msg:"FILE store all"; filestore; sid:2; rev:1;)

```

Si configuramos Suricata correctamente, se almacenarán varios archivos dentro del`filestore`directorio.

Ejecutemos Suricata en el`/home/htb-student/pcaps/vm-2.pcap`archivo.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ suricata -r /home/htb-student/pcaps/vm-2.pcap7/7/2023 -- 06:25:57 - <Notice> - This is Suricata version 6.0.13 RELEASE running in USER mode
7/7/2023 -- 06:25:57 - <Notice> - all 3 packet processing threads, 4 management threads initialized, engine started.
7/7/2023 -- 06:25:57 - <Notice> - Signal Received.  Stopping engine.
7/7/2023 -- 06:25:57 - <Notice> - Pcap-file module read 1 files, 803 packets, 683915 bytes

```

Notaremos que`eve.json`, `fast.log`, `stats.log`, y`suricata.log`fueron creados, junto con un nuevo directorio llamado`filestore`. `filestore`El contenido en términos de los archivos que contiene se puede inspeccionar de la siguiente manera.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ cd filestorexnoxos@htb[/htb]$ find . -type f./fb/fb20d18d00c806deafe14859052072aecfb9f46be6210acfce80289740f2e20e
./21/214306c98a3483048d6a69eec6bf3b50497363bc2c98ed3cd954203ec52455e5
./21/21742fc621f83041db2e47b0899f5aea6caa00a4b67dbff0aae823e6817c5433
./26/2694f69c4abf2471e09f6263f66eb675a0ca6ce58050647dcdcfebaf69f11ff4
./2c/2ca1a0cd9d8727279f0ba99fd051e1c0acd621448ad4362e1c9fc78700015228
./7d/7d4c00f96f38e0ffd89bc2d69005c4212ef577354cc97d632a09f51b2d37f877
./6b/6b7fee8a4b813b6405361db2e70a4f5a213b34875dd2793667519117d8ca0e4e
./2e/2e2cb2cac099f08bc51abba263d9e3f8ac7176b54039cc30bbd4a45cfa769018
./50/508c47dd306da3084475faae17b3acd5ff2700d2cd85d71428cdfaae28c9fd41
./c2/c210f737f55716a089a33daf42658afe771cfb43228ffa405d338555a9918815
./ea/ea0936257b8d96ee6ae443adee0f3dacc3eff72b559cd5ee3f9d6763cf5ee2ab
./1a/1aab7d9c153887dfa63853534f684e5d46ecd17ba60cd3d61050f7f231c4babb
./c4/c4775e980c97b162fd15e0010663694c4e09f049ff701d9671e1578958388b9f
./63/63de4512dfbd0087f929b0e070cc90d534d6baabf2cdfbeaf76bee24ff9b1638
./48/482d9972c2152ca96616dc23bbaace55804c9d52f5d8b253b617919bb773d3bb
./8e/8ea3146c676ba436c0392c3ec26ee744155af4e4eca65f4e99ec68574a747a14
./8e/8e23160cc504b4551a94943e677f6985fa331659a1ba58ef01afb76574d2ad7c
./a5/a52dac473b33c22112a6f53c6a625f39fe0d6642eb436e5d125342a24de44581

```

Nuevamente de acuerdo con las pautas presentadas en la documentación de Suricata, la`file-store`El módulo utiliza su propio directorio de registro (predeterminado:`filestore`en el directorio de registro predeterminado) y registra archivos utilizando el SHA256 del contenido como nombre de archivo. Luego, cada archivo se coloca en un directorio llamado 00 a FF, donde el directorio comparte los primeros 2 caracteres del nombre de archivo. Por ejemplo, si la cadena hexadecimal SHA256 de un archivo extraído comienza con`f9bc6d...`el archivo que nos colocamos en el directorio`filestore/f9`.

Si queríamos inspeccionar, por ejemplo, el`/21/21742fc621f83041db2e47b0899f5aea6caa00a4b67dbff0aae823e6817c5433`archivo dentro del`filestore`directorio, podríamos usar el`xxd`herramienta de la siguiente manera.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ cd filestorexnoxos@htb[/htb]$ xxd ./21/21742fc621f83041db2e47b0899f5aea6caa00a4b67dbff0aae823e6817c5433 | head00000000: 4d5a 9000 0300 0000 0400 0000 ffff 0000  MZ..............
00000010: b800 0000 0000 0000 4000 0000 e907 0000  ........@.......
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 8000 0000  ................
00000040: 0e1f ba0e 00b4 09cd 21b8 014c cd21 5468  ........!..L.!Th
00000050: 6973 2070 726f 6772 616d 2063 616e 6e6f  is program canno
00000060: 7420 6265 2072 756e 2069 6e20 444f 5320  t be run in DOS
00000070: 6d6f 6465 2e0d 0d0a 2400 0000 0000 0000  mode....$.......00000080: 5045 0000 4c01 0300 fc90 8448 0000 0000  PE..L......H....
00000090: 0000 0000 e000 0f01 0b01 0600 00d0 0000  ................

```

En este caso, el archivo era un ejecutable de Windows basado en el encabezado del archivo. Se puede encontrar más sobre el formato MS-DOS EXE en el siguiente recurso[MZ](https://wiki.osdev.org/MZ).

# **Característica de recarga de reglas en vivo y actualización de reglas de Suricata**

La recarga de reglas en vivo es una característica crucial en Suricata que nos permite actualizar nuestro conjunto de reglas sin interrumpir la inspección de tráfico en curso. Esta característica proporciona un monitoreo continuo y minimiza las posibilidades de faltar cualquier actividad maliciosa.

Para habilitar la recarga de reglas en vivo en Suricata, necesitamos configurar nuestro archivo de configuración de Suricata (`suricata.yaml`). En el`suricata.yaml`archivo, debemos localizar el`detect-engine`sección y establecer el valor del`reload`parámetro`true`. Se ve algo así:

Fundamentos de Suricata

```
detect-engine:
  - reload: true

```

Proceda a ejecutar lo siguiente`kill`comando, que indicará el proceso de Suricata (determinado por`$(pidof suricata)`) para actualizar su conjunto de reglas sin requerir un reinicio completo.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo kill -usr2 $(pidof suricata)
```

This modification tells Suricata to check for changes in the ruleset periodically and apply them without needing to restart the service.

La mayoría de los comandos a continuación no se pueden replicar dentro del objetivo de esta sección, ya que requieren conectividad a Internet.

La actualización del conjunto de reglas de Suricata se puede realizar utilizando el`suricata-update`herramienta. Podemos realizar una actualización simple para el conjunto de reglas de Suricata utilizando el siguiente comando.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo suricata-update6/7/2023 -- 06:46:44 - <Info> -- Using data-directory /var/lib/suricata.
6/7/2023 -- 06:46:44 - <Info> -- Using Suricata configuration /etc/suricata/suricata.yaml
6/7/2023 -- 06:46:44 - <Info> -- Using /etc/suricata/rules for Suricata provided rules.
6/7/2023 -- 06:46:44 - <Info> -- Found Suricata version 6.0.13 at /usr/bin/suricata.
6/7/2023 -- 06:46:44 - <Info> -- Loading /etc/suricata/suricata.yaml
6/7/2023 -- 06:46:44 - <Info> -- Disabling rules for protocol http2
6/7/2023 -- 06:46:44 - <Info> -- Disabling rules for protocol modbus
6/7/2023 -- 06:46:44 - <Info> -- Disabling rules for protocol dnp3
6/7/2023 -- 06:46:44 - <Info> -- Disabling rules for protocol enip
6/7/2023 -- 06:46:44 - <Info> -- No sources configured, will use Emerging Threats Open
6/7/2023 -- 06:46:44 - <Info> -- Fetching https://rules.emergingthreats.net/open/suricata-6.0.13/emerging.rules.tar.gz.
 100% - 3963342/3963342
6/7/2023 -- 06:46:45 - <Info> -- Done.
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/app-layer-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/decoder-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/dhcp-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/dnp3-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/dns-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/files.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/http-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/ipsec-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/kerberos-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/modbus-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/nfs-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/ntp-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/smb-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/smtp-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/stream-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Loading distribution rule file /etc/suricata/rules/tls-events.rules
6/7/2023 -- 06:46:45 - <Info> -- Ignoring file rules/emerging-deleted.rules
6/7/2023 -- 06:46:48 - <Info> -- Loaded 43453 rules.
6/7/2023 -- 06:46:48 - <Info> -- Disabled 14 rules.
6/7/2023 -- 06:46:48 - <Info> -- Enabled 0 rules.
6/7/2023 -- 06:46:48 - <Info> -- Modified 0 rules.
6/7/2023 -- 06:46:48 - <Info> -- Dropped 0 rules.
6/7/2023 -- 06:46:48 - <Info> -- Enabled 131 rules for flowbit dependencies.
6/7/2023 -- 06:46:48 - <Info> -- Creating directory /var/lib/suricata/rules.
6/7/2023 -- 06:46:48 - <Info> -- Backing up current rules.
6/7/2023 -- 06:46:49 - <Info> -- Writing rules to /var/lib/suricata/rules/suricata.rules: total: 43453; enabled: 34465; added: 43453; removed 0; modified: 0
6/7/2023 -- 06:46:49 - <Info> -- Writing /var/lib/suricata/rules/classification.config
6/7/2023 -- 06:46:49 - <Info> -- Testing with suricata -T.
6/7/2023 -- 06:47:11 - <Info> -- Done.

```

Como se muestra en el ejemplo anterior, la salida indica que el`suricata-update`El comando ha recuperado con éxito las reglas al establecer una conexión con`https://rules.emergingthreats.net/open/`. Posteriormente, el comando guarda las reglas recién obtenidas para el`/var/lib/suricata/rules/`directorio.

En el futuro, ejecutemos el comando proporcionado a continuación para generar una lista completa de todos los proveedores de conjuntos de reglas.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo suricata-update list-sources6/7/2023 -- 06:59:29 - <Info> -- Using data-directory /var/lib/suricata.
6/7/2023 -- 06:59:29 - <Info> -- Using Suricata configuration /etc/suricata/suricata.yaml
6/7/2023 -- 06:59:29 - <Info> -- Using /etc/suricata/rules for Suricata provided rules.
6/7/2023 -- 06:59:29 - <Info> -- Found Suricata version 6.0.13 at /usr/bin/suricata.
6/7/2023 -- 06:59:29 - <Info> -- No source index found, running update-sources
6/7/2023 -- 06:59:29 - <Info> -- Downloading https://www.openinfosecfoundation.org/rules/index.yaml
6/7/2023 -- 06:59:29 - <Info> -- Adding all sources
6/7/2023 -- 06:59:29 - <Info> -- Saved /var/lib/suricata/update/cache/index.yaml
Name: et/open
  Vendor: Proofpoint
  Summary: Emerging Threats Open Ruleset
  License: MIT
Name: et/pro
  Vendor: Proofpoint
  Summary: Emerging Threats Pro Ruleset
  License: Commercial
  Replaces: et/open
  Parameters: secret-code
  Subscription: https://www.proofpoint.com/us/threat-insight/et-pro-ruleset
Name: oisf/trafficid
  Vendor: OISF
  Summary: Suricata Traffic ID ruleset
  License: MIT
Name: scwx/enhanced
  Vendor: Secureworks
  Summary: Secureworks suricata-enhanced ruleset
  License: Commercial
  Parameters: secret-code
  Subscription: https://www.secureworks.com/contact/ (Please reference CTU Countermeasures)
Name: scwx/malware
  Vendor: Secureworks
  Summary: Secureworks suricata-malware ruleset
  License: Commercial
  Parameters: secret-code
  Subscription: https://www.secureworks.com/contact/ (Please reference CTU Countermeasures)
Name: scwx/security
  Vendor: Secureworks
  Summary: Secureworks suricata-security ruleset
  License: Commercial
  Parameters: secret-code
  Subscription: https://www.secureworks.com/contact/ (Please reference CTU Countermeasures)
Name: sslbl/ssl-fp-blacklist
  Vendor: Abuse.ch
  Summary: Abuse.ch SSL Blacklist
  License: Non-Commercial
Name: sslbl/ja3-fingerprints
  Vendor: Abuse.ch
  Summary: Abuse.ch Suricata JA3 Fingerprint Ruleset
  License: Non-Commercial
Name: etnetera/aggressive
  Vendor: Etnetera a.s.
  Summary: Etnetera aggressive IP blacklist
  License: MIT
Name: tgreen/hunting
  Vendor: tgreen
  Summary: Threat hunting rules
  License: GPLv3
Name: malsilo/win-malware
  Vendor: malsilo
  Summary: Commodity malware rules
  License: MIT
Name: stamus/lateral
  Vendor: Stamus Networks
  Summary: Lateral movement rules
  License: GPL-3.0-only

```

Hagamos una nota de la salida anterior de un nombre específico del conjunto de reglas del que queremos que Suricata obtenga conjuntos de reglas. El`et/open`Los conjuntos de reglas sirven como una excelente opción para fines de demostración.

A continuación, proceda a ejecutar el siguiente comando para recuperar y habilitar el`et/open`conjuntos de reglas dentro de nuestras reglas de Suricata.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo suricata-update enable-source et/open6/7/2023 -- 07:02:08 - <Info> -- Using data-directory /var/lib/suricata.
6/7/2023 -- 07:02:08 - <Info> -- Using Suricata configuration /etc/suricata/suricata.yaml
6/7/2023 -- 07:02:08 - <Info> -- Using /etc/suricata/rules for Suricata provided rules.
6/7/2023 -- 07:02:08 - <Info> -- Found Suricata version 6.0.13 at /usr/bin/suricata.
6/7/2023 -- 07:02:08 - <Info> -- Creating directory /var/lib/suricata/update/sources
6/7/2023 -- 07:02:08 - <Info> -- Source et/open enabled

```

Por último, volvamos a emitir el`suricata-update`Comando para cargar el conjunto de reglas recién adquirido.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo suricata-update
```

También se puede requerir un reinicio del servicio Suricata.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo systemctl restart suricata
```

# **Validando la configuración de Suricata**

La validación de la configuración de Suricata también es una parte esencial para mantener la robustez de nuestra configuración IDS/IPS. Para validar la configuración, podemos usar el`-T`Opción proporcionada por el comando SURICATA. Este comando ejecuta una prueba para verificar si el archivo de configuración es válido y todos los archivos referenciados en la configuración son accesibles.

Así es como podemos hacer esto.

Fundamentos de Suricata

```
xnoxos@htb[/htb]$ sudo suricata -T -c /etc/suricata/suricata.yaml6/7/2023 -- 07:13:29 - <Info> - Running suricata under test mode
6/7/2023 -- 07:13:29 - <Notice> - This is Suricata version 6.0.13 RELEASE running in SYSTEM mode
6/7/2023 -- 07:13:29 - <Notice> - Configuration provided was successfully loaded. Exiting.

```

# **Documentación de Suricata**

Suricata es una herramienta increíblemente versátil con extensa funcionalidad. Recomendamos encarecidamente explorar[Documentación de Suricata](https://docs.suricata.io/)para obtener una comprensión más profunda de sus capacidades.

# **Características clave de Suricata**

Las características clave que refuerzan la efectividad de Suricata incluyen:

- Inspección profunda de paquetes y registro de captura de paquetes
- Detección de anomalías y monitoreo de seguridad de red
- Detección y prevención de intrusos, con un modo híbrido disponible
- Lua Scripting
- Identificación de IP geográfica (GEOIP)
- Soporte completo de IPv4 e IPv6
- Reputación de IP
- Extracción de archivo
- Inspección avanzada del protocolo
- Multitenancia

**Nota**: Suricata también se puede usar para detectar el tráfico "no estándar/anómalo". Podemos aprovechar las estrategias descritas en Suricata[Página de detección de anomalías de protocolo](https://redmine.openinfosecfoundation.org/projects/suricata/wiki/Protocol_Anomalies_Detection). Este enfoque mejora nuestra visibilidad en un comportamiento inusual o no conforme dentro de nuestra red, aumentando así nuestra postura de seguridad.