La información del dominio es un componente central de cualquier prueba de penetración, y no se trata solo de los subdominios sino de toda la presencia en Internet. Por lo tanto, recopilamos información e intentamos comprender la funcionalidad de la empresa y qué tecnologías y estructuras son necesarias para que los servicios se ofrezcan de manera exitosa y eficiente.

Este tipo de información se recopila pasivamente sin exploraciones directas y activas. En otras palabras, permanecemos ocultos y navegamos como "clientes" o "visitantes" para evitar conexiones directas con la compañía que podría exponernos. Las secciones relevantes de OSINT son solo una pequeña parte de cómo va Osint en profundidad y describe solo algunas de las muchas formas de obtener información de esta manera. Se pueden encontrar más enfoques y estrategias para esto en el módulo.[OSINT: Corporate Recon](https://academy.hackthebox.com/course/preview/osint-corporate-recon).

Sin embargo, cuando`passively`Recopilando información, podemos usar servicios de terceros para comprender mejor a la empresa. Sin embargo, lo primero que debemos hacer es analizar el`main website`. Luego, debemos leer los textos, teniendo en cuenta qué tecnologías y estructuras son necesarias para estos servicios.

Por ejemplo, muchas compañías de TI ofrecen desarrollo de aplicaciones, IoT, alojamiento, ciencia de datos y servicios de seguridad de TI, dependiendo de su industria. Si encontramos un servicio con el que hemos tenido poco que ver antes, tiene sentido y es necesario conocerlo y descubrir en qué actividades consiste y qué oportunidades están disponibles. Esos servicios también nos dan una buena visión general de cómo se puede estructurar la empresa.

Por ejemplo, esta parte es la combinación entre el`first principle`y el`second principle`de enumeración. Prestamos atención a lo que`we see`y`we do not see`. Vemos los servicios pero no su funcionalidad. Sin embargo, los servicios están obligados a ciertos aspectos técnicos necesarios para proporcionar un servicio. Por lo tanto, tomamos la opinión del desarrollador y observamos todo desde su punto de vista. Este punto de vista nos permite obtener muchas ideas técnicas sobre la funcionalidad.

---

# **Presencia en línea**

Una vez que tenemos una comprensión básica de la empresa y sus servicios, podemos obtener una primera impresión de su presencia en Internet. Supongamos que una empresa de tamaño mediano nos ha contratado para probar toda su infraestructura desde una perspectiva de caja negra. Esto significa que solo hemos recibido un alcance de objetivos y debemos obtener toda la información nosotros mismos.

Nota: Recuerde que los ejemplos a continuación diferirán de los ejercicios prácticos y no darán los mismos resultados. Sin embargo, los ejemplos se basan en pruebas de penetración real e ilustran cómo y qué información se puede obtener.

El primer punto de presencia en Internet puede ser el`SSL certificate`del sitio web principal de la compañía que podemos examinar. A menudo, dicho certificado incluye más que un solo subdominio, y esto significa que el certificado se usa para varios dominios, y probablemente todavía estén activos.

![](https://academy.hackthebox.com/storage/modules/112/DomInfo-1.png)

Otra fuente para encontrar más subdominios es[CRT.SH](https://crt.sh/). Esta fuente es[Transparencia de certificado](https://en.wikipedia.org/wiki/Certificate_Transparency)registros. La transparencia del certificado es un proceso destinado a habilitar la verificación de certificados digitales emitidos para conexiones a Internet cifradas. El estándar ([RFC 6962](https://tools.ietf.org/html/rfc6962)) Proporciona el registro de todos los certificados digitales emitidos por una autoridad de certificado en registros a prueba de auditoría. Esto tiene la intención de habilitar la detección de certificados falsos o emitidos maliciosamente para un dominio. Proveedores de certificados SSL como[Vamos a cifrar](https://letsencrypt.org/)Comparta esto con la interfaz web[CRT.SH](https://crt.sh/), que almacena las nuevas entradas en la base de datos a acceder más tarde.

![](https://academy.hackthebox.com/storage/modules/112/DomInfo-2.png)

También podemos generar los resultados en formato JSON.

### **Transparencia de certificado**

Información de dominio

```
xnoxos@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .[
  {
    "issuer_ca_id": 23451835427,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 50815783237226155,
    "entry_timestamp": "2021-08-21T06:00:17.173",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe9017d6de5eda90"
  },
  {
    "issuer_ca_id": 6864563267,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 5081529377,
    "entry_timestamp": "2021-08-21T06:00:16.932",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe90104e271c98a90"
  },
  {
    "issuer_ca_id": 113123452,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "smartfactory.inlanefreight.com",
    "name_value": "smartfactory.inlanefreight.com",
    "id": 4941235512141012357,
    "entry_timestamp": "2021-07-27T00:32:48.071",
    "not_before": "2021-07-26T23:32:47",
    "not_after": "2021-10-24T23:32:45",
    "serial_number": "044bac5fcc4d59329ecbbe9043dd9d5d0878"
  },
  { ... SNIP ...

```

Si es necesario, también podemos tenerlos filtrados por los subdominios únicos.

Información de dominio

```
xnoxos@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -uaccount.ttn.inlanefreight.com
blog.inlanefreight.com
bots.inlanefreight.com
console.ttn.inlanefreight.com
ct.inlanefreight.com
data.ttn.inlanefreight.com
*.inlanefreight.com
inlanefreight.com
integrations.ttn.inlanefreight.com
iot.inlanefreight.com
mails.inlanefreight.com
marina.inlanefreight.com
marina-live.inlanefreight.com
matomo.inlanefreight.com
next.inlanefreight.com
noc.ttn.inlanefreight.com
preview.inlanefreight.com
shop.inlanefreight.com
smartfactory.inlanefreight.com
ttn.inlanefreight.com
vx.inlanefreight.com
www.inlanefreight.com

```

A continuación, podemos identificar a los hosts directamente accesibles desde Internet y no alojados por proveedores externos. Esto se debe a que no se nos permite probar a los anfitriones sin el permiso de proveedores externos.

### **Servidores alojados de la empresa**

Información de dominio

```
xnoxos@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;doneblog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250

```

Una vez que veamos qué hosts se pueden investigar más a fondo, podemos generar una lista de direcciones IP con un ajuste menor al`cut`comandar y ejecutarlos a través de`Shodan`.

[Shodán](https://www.shodan.io/)se puede utilizar para encontrar dispositivos y sistemas conectados permanentemente a Internet como`Internet of Things` (`IoT`). Busca en Internet en Internet los puertos TCP/IP y filtran los sistemas de acuerdo con términos y criterios específicos. Por ejemplo, abra los puertos HTTP o HTTPS y otros puertos del servidor para`FTP`, `SSH`, `SNMP`, `Telnet`, `RTSP`, o`SIP`se buscan. Como resultado, podemos encontrar dispositivos y sistemas, como`surveillance cameras`, `servers`, `smart home systems`, `industrial controllers`, `traffic lights`y`traffic controllers`y varios componentes de red.

### **SHODAN - Lista de IP**

Información de dominio

```
xnoxos@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;donexnoxos@htb[/htb]$ for i in $(cat ip-addresses.txt);do shodan host $i;done10.129.24.93
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T09:02:11.370085
Number of open ports:    2

Ports:
     80/tcp nginx
    443/tcp nginx

10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx
    443/tcp nginx
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2

10.129.27.22
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T15:39:55.446281
Number of open ports:    8

Ports:
     25/tcp
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
     53/tcp
     53/udp
     80/tcp Apache httpd
     81/tcp Apache httpd
    110/tcp
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2
    111/tcp
    443/tcp Apache httpd
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
                Fingerprint:   RFC3526/Oakley Group 14
    444/tcp

10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx
    443/tcp nginx
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2

```

Recordamos la IP`10.129.127.22` (`matomo.inlanefreight.com`) Para investigaciones activas posteriores que queremos realizar. Ahora, podemos mostrar todos los registros DNS disponibles donde podamos encontrar más hosts.

### **Registros de DNS**

Información de dominio

```
xnoxos@htb[/htb]$ dig any inlanefreight.com; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52058
;; flags: qr rd ra; QUERY: 1, ANSWER: 17, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;inlanefreight.com.             IN      ANY

;; ANSWER SECTION:
inlanefreight.com.      300     IN      A       10.129.27.33
inlanefreight.com.      300     IN      A       10.129.95.250
inlanefreight.com.      3600    IN      MX      1 aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      10 aspmx2.googlemail.com.
inlanefreight.com.      3600    IN      MX      10 aspmx3.googlemail.com.
inlanefreight.com.      3600    IN      MX      5 alt1.aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      5 alt2.aspmx.l.google.com.
inlanefreight.com.      21600   IN      NS      ns.inwx.net.
inlanefreight.com.      21600   IN      NS      ns2.inwx.net.
inlanefreight.com.      21600   IN      NS      ns3.inwx.eu.
inlanefreight.com.      3600    IN      TXT     "MS=ms92346782372"
inlanefreight.com.      21600   IN      TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
inlanefreight.com.      300     IN      TXT     "google-site-verification=bow47-er9LdgoUeah"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=gZsCG-BINLopf4hr2"
inlanefreight.com.      3600    IN      TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
inlanefreight.com.      300     IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
inlanefreight.com.      21600   IN      SOA     ns.inwx.net. hostmaster.inwx.net. 2021072600 10800 3600 604800 3600

;; Query time: 332 msec
;; SERVER: 127.0.0.53#53(127.0.0.53);; WHEN: Mi Sep 01 18:27:22 CEST 2021
;; MSG SIZE  rcvd: 940

```

Veamos lo que hemos aprendido aquí y volvamos a nuestros principios. Vemos un registro IP, algunos servidores de correo, algunos servidores DNS, registros TXT y un registro SOA.

- `A`Registros: Reconocemos las direcciones IP que apuntan a un dominio específico (sub) a través del registro A. Aquí solo vemos uno que ya conocemos.
- `MX`Registros: Los registros del servidor de correo nos muestran qué servidor de correo es responsable de administrar los correos electrónicos para la empresa. Dado que Google maneja esto en nuestro caso, debemos tener en cuenta esto y omitirlo por ahora.
- `NS`Registros: Este tipo de registros muestran qué servidores de nombres se utilizan para resolver el FQDN en las direcciones IP. La mayoría de los proveedores de alojamiento usan sus propios servidores de nombres, lo que facilita la identificación del proveedor de alojamiento.
- `TXT`Registros: Este tipo de registro a menudo contiene claves de verificación para diferentes proveedores de terceros y otros aspectos de seguridad de DNS, como[SPF](https://datatracker.ietf.org/doc/html/rfc7208), [Dmarc](https://datatracker.ietf.org/doc/html/rfc7489), y[Dkim](https://datatracker.ietf.org/doc/html/rfc6376), que son responsables de verificar y confirmar el origen de los correos electrónicos enviados. Aquí ya podemos ver información valiosa si observamos más de cerca los resultados.

Información de dominio

```
...SNIP... TXT     "MS=ms92346782372"
...SNIP... TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
...SNIP... TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
...SNIP... TXT     "google-site-verification=bow47-er9LdgoUeah"
...SNIP... TXT     "google-site-verification=gZsCG-BINLopf4hr2"
...SNIP... TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
...SNIP... TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"

```

Lo que pudimos ver hasta ahora fueron entradas en el servidor DNS, que a primera vista no parecía muy interesante (excepto las direcciones IP adicionales). Sin embargo, no pudimos ver a los proveedores externos detrás de las entradas que se muestran a primera vista. La información central que podemos ver ahora es:

|  |  |  |
| --- | --- | --- |
| [Atlassiano](https://www.atlassian.com/) | [Google Gmail](https://www.google.com/gmail/) | [Logmein](https://www.logmein.com/) |
| [Arma de correo](https://www.mailgun.com/) | [Perspectiva](https://outlook.live.com/owa/) | [Inwx](https://www.inwx.com/en)ID/Nombre de usuario |
| 10.129.24.8 | 10.129.27.2 | 10.72.82.106 |

Por ejemplo,[Atlassiano](https://www.atlassian.com/)establece que la compañía utiliza esta solución para el desarrollo y colaboración de software. Si no estamos familiarizados con esta plataforma, podemos probarlo de forma gratuita para familiarizarse con ella.

[Google Gmail](https://www.google.com/gmail/)Indica que Google se usa para la administración de correo electrónico. Por lo tanto, también puede sugerir que podríamos acceder a las carpetas o archivos de Open GDRive con un enlace.

[Logmein](https://www.logmein.com/)es un lugar central que regula y administra el acceso remoto en muchos niveles diferentes. Sin embargo, la centralización de tales operaciones es una espada de doble filo. Si se obtiene el acceso como administrador de esta plataforma (por ejemplo, a través de la reutilización de contraseña), uno también tiene acceso completo a todos los sistemas e información.

[Arma de correo](https://www.mailgun.com/)Ofrece varias API de correo electrónico, relés SMTP y webhooks con los que se pueden administrar los correos electrónicos. Esto nos dice que mantengamos nuestros ojos abiertos para las interfaces API que luego podemos probar varias vulnerabilidades como IDOR, SSRF, POST, Solicitudes y muchos otros ataques.

[Perspectiva](https://outlook.live.com/owa/)es otro indicador para la gestión de documentos. Las empresas a menudo usan Office 365 con recursos OneDrive y en la nube, como Azure Blob y el almacenamiento de archivos. El almacenamiento de archivos de Azure puede ser muy interesante porque funciona con el protocolo SMB.

Lo último que vemos es[Inwx](https://www.inwx.com/en). Esta compañía parece ser un proveedor de alojamiento donde los dominios se pueden comprar y registrar. El registro TXT con el valor "MS" a menudo se usa para confirmar el dominio. En la mayoría de los casos, es similar al nombre de usuario o ID utilizado para iniciar sesión en la plataforma de administración.

[Anterior](https://academy.hackthebox.com/module/112/section/1185) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/112/section/1062)

Hoja de trucosRecursos