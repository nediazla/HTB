# Attacking DNS

El[Sistema de nombres de dominio](https://www.cloudflare.com/learning/dns/what-is-dns/) (`DNS`) traduce los nombres de dominio (por ejemplo, hackhebox.com) a las direcciones IP numéricas (por ejemplo, 104.17.42.72). DNS es principalmente`UDP/53`, pero DNS confiará en`TCP/53`más a medida que avanza el tiempo. DNS siempre ha sido diseñado para usar el puerto UDP y TCP 53 desde el principio, siendo UDP el valor predeterminado, y vuelve a usar TCP cuando no puede comunicarse en UDP, generalmente cuando el tamaño del paquete es demasiado grande para empujar en un solo paquete UDP. Dado que casi todas las aplicaciones de red usan DNS, los ataques contra los servidores DNS representan una de las amenazas más frecuentes y significativas en la actualidad.

---

# **Enumeración**

DNS posee información interesante para una organización. Como se discutió en la sección de información de dominio en el[Módulo de huella](https://academy.hackthebox.com/course/preview/footprinting), podemos entender cómo opera una empresa y los servicios que brinda, así como a proveedores de servicios de terceros como correos electrónicos.

El nmap`-sC`(scripts predeterminados) y`-sV`(Version Scan) Las opciones se pueden usar para realizar la enumeración inicial contra los servidores DNS de destino:

Atacando DNS

```
xnoxos@htb[/htb]# nmap -p53 -Pn -sV -sC 10.10.110.213Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-29 03:47 EDT
Nmap scan report for 10.10.110.213
Host is up (0.017s latency).

PORT    STATE  SERVICE     VERSION
53/tcp  open   domain      ISC BIND 9.11.3-1ubuntu1.2 (Ubuntu Linux)

```

---

# **Transferencia de zona DNS**

Una zona DNS es una parte del espacio de nombres DNS que administra una organización o administrador específico. Dado que DNS comprende múltiples zonas DNS, los servidores DNS utilizan transferencias de zona DNS para copiar una parte de su base de datos a otro servidor DNS. A menos que un servidor DNS esté configurado correctamente (limitando qué IPS puede realizar una transferencia de zona DNS), cualquiera puede pedirle a un servidor DNS una copia de su información de zona, ya que las transferencias de zona DNS no requieren ninguna autenticación. Además, el servicio DNS generalmente se ejecuta en un puerto UDP; Sin embargo, al realizar la transferencia de la zona DNS, utiliza un puerto TCP para la transmisión de datos confiable.

Un atacante podría aprovechar esta vulnerabilidad de transferencia de la zona DNS para aprender más sobre el espacio de nombres DNS de la organización objetivo, aumentando la superficie de ataque. Para la explotación, podemos usar el`dig`utilidad con tipo de consulta DNS`AXFR`Opción para descargar todos los espacios de nombres DNS de un servidor DNS vulnerable:

### **DIG - Transferencia de zona de AXFR**

Atacando DNS

```
xnoxos@htb[/htb]# dig AXFR @ns1.inlanefreight.htb inlanefreight.htb; <<>> DiG 9.11.5-P1-1-Debian <<>> axfr inlanefrieght.htb @10.129.110.213
;; global options: +cmd
inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
inlanefrieght.htb.         604800  IN      AAAA    ::1
inlanefrieght.htb.         604800  IN      NS      localhost.
inlanefrieght.htb.         604800  IN      A       10.129.110.22
admin.inlanefrieght.htb.   604800  IN      A       10.129.110.21
hr.inlanefrieght.htb.      604800  IN      A       10.129.110.25
support.inlanefrieght.htb. 604800  IN      A       10.129.110.28
inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
;; Query time: 28 msec
;; SERVER: 10.129.110.213#53(10.129.110.213);; WHEN: Mon Oct 11 17:20:13 EDT 2020
;; XFR size: 8 records (messages 1, bytes 289)

```

Herramientas como[Feroz](https://github.com/mschwager/fierce)También se puede utilizar para enumerar todos los servidores DNS del dominio de la raíz y escanear una transferencia de zona DNS:

Atacando DNS

```
xnoxos@htb[/htb]# fierce --domain zonetransfer.meNS: nsztm2.digi.ninja. nsztm1.digi.ninja.
SOA: nsztm1.digi.ninja. (81.4.108.41)
Zone: success
{<DNS name @>: '@ 7200 IN SOA nsztm1.digi.ninja. robin.digi.ninja. 2019100801 '
               '172800 900 1209600 3600\n'
               '@ 300 IN HINFO "Casio fx-700G" "Windows XP"\n'
               '@ 301 IN TXT '
               '"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"\n'
               '@ 7200 IN MX 0 ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT1.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT2.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 20 ASPMX2.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX3.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX4.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX5.GOOGLEMAIL.COM.\n'
               '@ 7200 IN A 5.196.105.14\n'
               '@ 7200 IN NS nsztm1.digi.ninja.\n'
               '@ 7200 IN NS nsztm2.digi.ninja.',
 <DNS name _acme-challenge>: '_acme-challenge 301 IN TXT '
                             '"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"',
 <DNS name _sip._tcp>: '_sip._tcp 14000 IN SRV 0 0 5060 www',
 <DNS name 14.105.196.5.IN-ADDR.ARPA>: '14.105.196.5.IN-ADDR.ARPA 7200 IN PTR '
                                       'www',
 <DNS name asfdbauthdns>: 'asfdbauthdns 7900 IN AFSDB 1 asfdbbox',
 <DNS name asfdbbox>: 'asfdbbox 7200 IN A 127.0.0.1',
 <DNS name asfdbvolume>: 'asfdbvolume 7800 IN AFSDB 1 asfdbbox',
 <DNS name canberra-office>: 'canberra-office 7200 IN A 202.14.81.230',
 <DNS name cmdexec>: 'cmdexec 300 IN TXT "; ls"',
 <DNS name contact>: 'contact 2592000 IN TXT "Remember to call or email Pippa '
                     'on +44 123 4567890 or pippa@zonetransfer.me when making '
                     'DNS changes"',
 <DNS name dc-office>: 'dc-office 7200 IN A 143.228.181.132',
 <DNS name deadbeef>: 'deadbeef 7201 IN AAAA dead:beaf::',
 <DNS name dr>: 'dr 300 IN LOC 53 20 56.558 N 1 38 33.526 W 0.00m',
 <DNS name DZC>: 'DZC 7200 IN TXT "AbCdEfG"',
 <DNS name email>: 'email 2222 IN NAPTR 1 1 "P" "E2U+email" "" '
                   'email.zonetransfer.me\n'
                   'email 7200 IN A 74.125.206.26',
 <DNS name Hello>: 'Hello 7200 IN TXT "Hi to Josh and all his class"',
 <DNS name home>: 'home 7200 IN A 127.0.0.1',
 <DNS name Info>: 'Info 7200 IN TXT "ZoneTransfer.me service provided by Robin '
                  'Wood - robin@digi.ninja. See '
                  'http://digi.ninja/projects/zonetransferme.php for more '
                  'information."',
 <DNS name internal>: 'internal 300 IN NS intns1\ninternal 300 IN NS intns2',
 <DNS name intns1>: 'intns1 300 IN A 81.4.108.41',
 <DNS name intns2>: 'intns2 300 IN A 167.88.42.94',
 <DNS name office>: 'office 7200 IN A 4.23.39.254',
 <DNS name ipv6actnow.org>: 'ipv6actnow.org 7200 IN AAAA '
                            '2001:67c:2e8:11::c100:1332',
...SNIP...

```

---

# **Adquisiciones de dominio y enumeración del subdominio**

`Domain takeover`está registrando un nombre de dominio inexistente para obtener control sobre otro dominio. Si los atacantes encuentran un dominio caducado, pueden reclamar ese dominio para realizar más ataques, como alojar contenido malicioso en un sitio web o enviar un correo electrónico de phishing que aproveche el dominio reclamado.

La adquisición del dominio también es posible con subdominios llamados`subdomain takeover`. Nombre canónico de un DNS (`CNAME`) El registro se usa para asignar diferentes dominios a un dominio principal. Muchas organizaciones usan servicios de terceros como AWS, Github, Akamai, Fastly y otras Redes de Entrega de Contenido (CDN) para alojar su contenido. En este caso, generalmente crean un subdominio y hacen que señale esos servicios. Por ejemplo,

Atacando DNS

```
sub.target.com.   60   IN   CNAME   anotherdomain.com

```

El nombre de dominio (por ejemplo,`sub.target.com`) usa un registro CNAME a otro dominio (por ejemplo,`anotherdomain.com`). Supongamos el`anotherdomain.com`expira y está disponible para que cualquiera reclame el dominio desde el`target.com`El servidor DNS tiene el`CNAME`registro. En ese caso, cualquiera que se registre`anotherdomain.com`tendrá un control completo sobre`sub.target.com`Hasta que se actualice el registro DNS.

### **Enumeración del subdominio**

Antes de realizar una adquisición del subdominio, debemos enumerar los subdominios para un dominio objetivo utilizando herramientas como[Subfintor](https://github.com/projectdiscovery/subfinder). Esta herramienta puede raspar subdominios de fuentes abiertas como[Dnsdumpster](https://dnsdumpster.com/). Otras herramientas como[Sublist3r](https://github.com/aboul3la/Sublist3r)También se puede utilizar para los subdominios de fuerza bruta mediante el suministro de una lista de palabras previamente generada:

Atacando DNS

```
xnoxos@htb[/htb]# ./subfinder -d inlanefreight.com -v
        _     __ _         _
____  _| |__ / _(_)_ _  __| |___ _ _
(_-< || | '_ \  _| | ' \/ _  / -_) '_|
/__/\_,_|_.__/_| |_|_||_\__,_\___|_| v2.4.5
                projectdiscovery.io

[WRN] Use with caution. You are responsible for your actions
[WRN] Developers assume no liability and are not responsible for any misuse or damage.
[WRN] By using subfinder, you also agree to the terms of the APIs used.

[INF] Enumerating subdomains for inlanefreight.com
[alienvault] www.inlanefreight.com
[dnsdumpster] ns1.inlanefreight.com
[dnsdumpster] ns2.inlanefreight.com
...snip...
[bufferover] Source took 2.193235338s for enumeration
ns2.inlanefreight.com
www.inlanefreight.com
ns1.inlanefreight.com
support.inlanefreight.com
[INF] Found 4 subdomains for inlanefreight.com in 20 seconds 11 milliseconds

```

Una excelente alternativa es una herramienta llamada[Subrute](https://github.com/TheRook/subbrute). Esta herramienta nos permite usar solucionadores autodefinidos y realizar ataques de forzamiento bruto DNS puro durante las pruebas de penetración interna en hosts que no tienen acceso a Internet.

### **Subrute**

Atacando DNS

```
xnoxos@htb[/htb]$ git clone https://github.com/TheRook/subbrute.git >> /dev/null2>&1xnoxos@htb[/htb]$ cd subbrutexnoxos@htb[/htb]$ echo "ns1.inlanefreight.com" > ./resolvers.txtxnoxos@htb[/htb]$ ./subbrute inlanefreight.com -s ./names.txt -r ./resolvers.txtWarning: Fewer than 16 resolvers per process, consider adding more nameservers to resolvers.txt.
inlanefreight.com
ns2.inlanefreight.com
www.inlanefreight.com
ms1.inlanefreight.com
support.inlanefreight.com

<SNIP>

```

A veces, las configuraciones físicas internas están mal aseguradas, lo que podemos explotar para cargar nuestras herramientas desde un palo USB. Otro escenario sería que hemos llegado a un anfitrión interno a través de pivotaciones y queremos trabajar desde allí. Por supuesto, hay otras alternativas, pero no está de más conocer formas y posibilidades alternativas.

La herramienta ha encontrado cuatro subdominios asociados con`inlanefreight.com`. Usando el`nslookup`o`host`comando, podemos enumerar el`CNAME`Registros para esos subdominios.

Atacando DNS

```
xnoxos@htb[/htb]# host support.inlanefreight.comsupport.inlanefreight.com is an alias for inlanefreight.s3.amazonaws.com

```

El`support`El subdominio tiene un registro de alias que apunta a un cubo AWS S3. Sin embargo, la URL`https://support.inlanefreight.com`muestra un`NoSuchBucket`Error que indica que el subdominio es potencialmente vulnerable a una adquisición del subdominio. Ahora, podemos hacerse cargo del subdominio creando un cubo AWS S3 con el mismo nombre del subdominio.

![](https://academy.hackthebox.com/storage/modules/116/s3.png)

El[can-i-as-over-xyz](https://github.com/EdOverflow/can-i-take-over-xyz)El repositorio también es una excelente referencia para una vulnerabilidad de adquisición del subdominio. Muestra si los servicios objetivo son vulnerables a una adquisición de subdominios y proporciona pautas para evaluar la vulnerabilidad.

---

# **Paro de DNS**

La suplantación de DNS también se conoce como intoxicación por caché DNS. Este ataque implica alterar registros DNS legítimos con información falsa para que puedan usarse para redirigir el tráfico en línea a un sitio web fraudulento. Caminos de ataque de ejemplo para la intoxicación por caché DNS son los siguientes:

- Un atacante podría interceptar la comunicación entre un usuario y un servidor DNS para enrutar al usuario a un destino fraudulento en lugar de una legítima realizando un hombre en el medio (`MITM`) ataque.
- Explotar una vulnerabilidad encontrada en un servidor DNS podría producir control sobre el servidor por parte de un atacante para modificar los registros DNS.

### **Envenenamiento de caché del DNS local**

Desde una perspectiva de red local, un atacante también puede realizar una intoxicación por caché DNS utilizando herramientas MITM como[Attercap](https://www.ettercap-project.org/)o[Bettercap](https://www.bettercap.org/).

Para explotar el envenenamiento de caché DNS a través de`Ettercap`, primero debemos editar el`/etc/ettercap/etter.dns`archivo para asignar el nombre de dominio de destino (por ejemplo,`inlanefreight.com`) que quieren falsificar y la dirección IP del atacante (por ejemplo,`192.168.225.110`) que quieren redirigir a un usuario para:

Atacando DNS

```
xnoxos@htb[/htb]# cat /etc/ettercap/etter.dnsinlanefreight.com      A   192.168.225.110
*.inlanefreight.com    A   192.168.225.110

```

A continuación, comience el`Ettercap`herramienta y escaneo de hosts en vivo dentro de la red navegando a`Hosts > Scan for Hosts`. Una vez completado, agregue la dirección IP de destino (por ejemplo,`192.168.152.129`) a Target1 y agregar una IP de puerta de enlace predeterminada (por ejemplo,`192.168.152.2`) a Target2.

![](https://academy.hackthebox.com/storage/modules/116/target.png)

Activar`dns_spoof`ataque navegando a`Plugins > Manage Plugins`. Esto envía la máquina de destino con respuestas DNS falsas que se resolverán`inlanefreight.com`a la dirección IP`192.168.225.110`:

![](https://academy.hackthebox.com/storage/modules/116/etter_plug.png)

Después de un exitoso ataque de parodia DNS, si un usuario víctima que proviene de la máquina de destino`192.168.152.129`visita el`inlanefreight.com`dominio en un navegador web, serán redirigidos a un`Fake page`que está alojado en la dirección IP`192.168.225.110`:

![](https://academy.hackthebox.com/storage/modules/116/etter_site.png)

Además, un ping proveniente de la dirección IP objetivo`192.168.152.129`a`inlanefreight.com`debe resolverse`192.168.225.110`también:

Atacando DNS

```
C:\>ping inlanefreight.com

Pinging inlanefreight.com [192.168.225.110] with 32 bytes of data:
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64

Ping statistics for 192.168.225.110:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

```

Estos son algunos ejemplos de ataques DNS comunes. Hay otros ataques más avanzados que se cubrirán en módulos posteriores.