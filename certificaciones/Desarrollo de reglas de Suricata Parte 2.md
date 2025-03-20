# Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

En el panorama en constante evolución de la seguridad de la red, a menudo nos enfrentamos a un desafío importante: el tráfico encriptado. El tráfico encriptado puede plantear obstáculos significativos cuando se trata de analizar de manera efectiva el tráfico y desarrollar reglas confiables del sistema de detección de intrusos (IDS) y del sistema de prevención de intrusiones (IPS).

Todavía hay varios aspectos que podemos aprovechar para detectar posibles amenazas de seguridad. Específicamente, podemos centrar nuestra atención en los elementos dentro de los certificados SSL/TLS y la huella digital JA3.

Los certificados SSL/TLS, intercambiados durante el apretón de manos inicial de una conexión SSL/TLS, contienen una gran cantidad de detalles que permanecen sin cifrar. Estos detalles pueden incluir el emisor, la fecha de emisión, la fecha de vencimiento y el sujeto (que contiene información sobre para quién es el certificado y el nombre de dominio). Los dominios sospechosos o maliciosos pueden utilizar certificados SSL/TLS con características anómalas o únicas. Reconocer estas anomalías en los certificados SSL/TLS puede ser un trampolín para crear reglas efectivas de Suricata.

Además, también podemos utilizar el[JA3](https://github.com/salesforce/ja3)Hash: un método de huellas digitales que proporciona una representación única para cada cliente SSL/TLS. El hash JA3 combina detalles del paquete Hello Hello del cliente durante el apretón de manos SSL/TLS, creando un resumen que podría ser único para familias de malware específicas o software sospechoso. Nuevamente, estos hashes pueden ser una herramienta poderosa para formular reglas de detección para el tráfico cifrado.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, ssh en la IP objetivo utilizando las credenciales proporcionadas. La gran mayoría de los comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Suricata Desarrollo de reglas Ejemplo 5: Detección de DRIDEX (TLS encriptado)**

Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

```
alert tls$EXTERNAL_NET any -> $HOME_NET any (msg:"ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex)"; flow:established,from_server; content:"|16|"; content:"|0b|"; within:8; byte_test:3,<,1200,0,relative; content:"|03 02 01 02 02 09 00|"; fast_pattern; content:"|30 09 06 03 55 04 06 13 02|"; distance:0; pcre:"/^[A-Z]{2}/R"; content:"|55 04 07|"; distance:0; content:"|55 04 0a|"; distance:0; pcre:"/^.{2}[A-Z][a-z]{3,}\s(?:[A-Z][a-z]{3,}\s)?(?:[A-Z](?:[A-Za-z]{0,4}?[A-Z]|(?:\.[A-Za-z]){1,3})|[A-Z]?[a-z]+|[a-z](?:\.[A-Za-z]){1,3})\.?[01]/Rs"; content:"|55 04 03|"; distance:0; byte_test:1,>,13,1,relative; content:!"www."; distance:2; within:4; pcre:"/^.{2}(?P<CN>(?:(?:\d?[A-Z]?|[A-Z]?\d?)(?:[a-z]{3,20}|[a-z]{3,6}[0-9_][a-z]{3,6})\.){0,2}?(?:\d?[A-Z]?|[A-Z]?\d?)[a-z]{3,}(?:[0-9_-][a-z]{3,})?\.(?!com|org|net|tv)[a-z]{2,9})[01].*?(?P=CN)[01]/Rs"; content:!"|2a 86 48 86 f7 0d 01 09 01|"; content:!"GoDaddy"; sid:2023476; rev:5;)

```

La regla anterior desencadena una alerta al detectar una sesión TLS desde la red externa a la red doméstica, donde la carga útil de la sesión contiene patrones de bytes específicos y cumple con varias condiciones. Estos patrones y condiciones corresponden a certificados SSL que se han vinculado a ciertas variaciones del troyano Dridex, como se hace referencia a la lista negra SSL en`abuse.ch`.

No es necesario comprender la regla en su totalidad, pero desglosemos las partes importantes de ella.

- `content:"|16|"; content:"|0b|"; within:8;`: La regla busca los valores hexadecimales 16 y 0b dentro de los primeros 8 bytes de la carga útil. Estos representan el mensaje de apretón de manos (0x16) y el tipo de certificado (0x0b) en el registro TLS.
- `content:"|03 02 01 02 02 09 00|"; fast_pattern;`: La regla busca este patrón específico de bytes en el paquete, que puede ser característico de los certificados utilizados por DRIDEX.
- `content:"|30 09 06 03 55 04 06 13 02|"; distance:0; pcre:"/^[A-Z]{2}/R"`;: Esto verifica el campo 'CountryName' en el tema del certificado. La coincidencia de contenido aquí corresponde a una secuencia ASN.1 que especifica un tipo de atributo y valor para 'CountryName' (OID 2.5.4.6). El siguiente PCRE verifica que el valor para 'CountryName' comienza con dos letras mayúsculas, que es un formato estándar para los códigos de país.
- `content:"|55 04 07|"; distance:0;`: Esto verifica el campo 'LocalityName' en el sujeto del certificado (OID 2.5.4.7).
- `content:"|55 04 0a|"; distance:0;`: Esto verifica el`organizationName`campo en el sujeto del certificado (OID 2.5.4.10).
- `content:"|55 04 03|"; distance:0; byte_test:1,>,13,1,relative;`: Esto verifica el`commonName`campo en el sujeto del certificado (OID 2.5.4.3). El siguiente byte_test verifica que la longitud del`commonName`El campo es más de 13.
- Por favor también dale esto muy interesante[Recurso en certificados DRIDEX SSL](https://unit42.paloaltonetworks.com/wireshark-tutorial-dridex-infection-traffic/)una mirada.

Los OID mencionados (identificadores de objetos) son parte del estándar X.509 para PKI y se utilizan para identificar de manera única los tipos de campos contenidos dentro de los certificados.

La regla anterior ya está incorporada en el`local.rules`Archivo encontrado en el`/home/htb-student`directorio del objetivo de esta sección. Para probarlo, primero, debe desenchufar la regla. Luego, ejecute Suricata en el`dridex.pcap`archivo, que se encuentra en el`/home/htb-student/pcaps`directorio.

Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

```
xnoxos@htb[/htb]$ sudo suricata -r /home/htb-student/pcaps/dridex.pcap -l . -k none15/7/2023 -- 20:34:11 - <Notice> - This is Suricata version 6.0.13 RELEASE running in USER mode
15/7/2023 -- 20:34:11 - <Notice> - all 3 packet processing threads, 4 management threads initialized, engine started.
15/7/2023 -- 20:34:11 - <Notice> - Signal Received.  Stopping engine.
15/7/2023 -- 20:34:11 - <Notice> - Pcap-file module read 1 files, 3683 packets, 3276706 bytes

```

Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

```
xnoxos@htb[/htb]$ cat fast.log07/09/2019-18:26:31.480302  [**] [1:2023476:5] ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex) [**] [Classification: (null)] [P             riority: 3] {TCP} 188.166.156.241:443 -> 10.7.9.101:49206
07/09/2019-18:26:33.937036  [**] [1:2023476:5] ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex) [**] [Classification: (null)] [P             riority: 3] {TCP} 188.166.156.241:443 -> 10.7.9.101:49207
07/09/2019-18:26:39.373287  [**] [1:2023476:5] ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex) [**] [Classification: (null)] [P             riority: 3] {TCP} 188.166.156.241:443 -> 10.7.9.101:49208
07/09/2019-18:26:29.628847  [**] [1:2023476:5] ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex) [**] [Classification: (null)] [P             riority: 3] {TCP} 188.166.156.241:443 -> 10.7.9.101:49205
07/09/2019-18:30:08.787378  [**] [1:2023476:5] ET MALWARE ABUSE.CH SSL Blacklist Malicious SSL certificate detected (Dridex) [**] [Classification: (null)] [P             riority: 3] {TCP} 72.205.170.179:443 -> 10.7.9.101:49212
---SNIP---

```

# **Desarrollo de reglas de Suricata Ejemplo 6: Detección de Sliver (TLS encriptado)**

Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

```
alert tls any any -> any any (msg:"Sliver C2 SSL"; ja3.hash; content:"473cd7cb9faa642487833865d516e578"; sid:1002; rev:1;)

```

La regla de Suricata anterior está diseñada para detectar ciertas variaciones de[Astilla](https://github.com/BishopFox/sliver)cada vez que identifica una conexión TLS con un hash JA3 específico.

Un archivo PCAP nombrado`sliverenc.pcap`que contiene tráfico de astillas cifrado se encuentra en el`/home/htb-student/pcaps`directorio del objetivo de esta sección.

El hash JA3 se puede calcular de la siguiente manera.

Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

```
xnoxos@htb[/htb]$ ja3 -a --json /home/htb-student/pcaps/sliverenc.pcap[
    {
        "destination_ip": "23.152.0.91",
        "destination_port": 443,
        "ja3": "771,49195-49199-49196-49200-52393-52392-49161-49171-49162-49172-156-157-47-53-49170-10-4865-4866-4867,0-5-10-11-13-65281-18-43-51,29-23-24-25,0",
        "ja3_digest": "473cd7cb9faa642487833865d516e578",
        "source_ip": "10.10.20.101",
        "source_port": 53222,
        "timestamp": 1634749464.600896
    },
    {
        "destination_ip": "23.152.0.91",
        "destination_port": 443,
        "ja3": "771,49195-49199-49196-49200-52393-52392-49161-49171-49162-49172-156-157-47-53-49170-10-4865-4866-4867,0-5-10-11-13-65281-18-43-51,29-23-24-25,0",
        "ja3_digest": "473cd7cb9faa642487833865d516e578",
        "source_ip": "10.10.20.101",
        "source_port": 53225,
        "timestamp": 1634749465.069819
    },
    {
        "destination_ip": "23.152.0.91",
        "destination_port": 443,
        "ja3": "771,49195-49199-49196-49200-52393-52392-49161-49171-49162-49172-156-157-47-53-49170-10-4865-4866-4867,0-5-10-11-13-65281-18-43-51,29-23-24-25,0",
        "ja3_digest": "473cd7cb9faa642487833865d516e578",
        "source_ip": "10.10.20.101",
        "source_port": 53229,
        "timestamp": 1634749585.240773
    },
---SNIP---

```

La regla anterior ya está incorporada en el`local.rules`Archivo encontrado en el`/home/htb-student`directorio del objetivo de esta sección. Para probarlo, primero, debe desenchufar la regla. Luego, ejecute Suricata en el`sliverenc.pcap`archivo, que se encuentra en el`/home/htb-student/pcaps`directorio.

Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

```
xnoxos@htb[/htb]$ sudo suricata -r /home/htb-student/pcaps/sliverenc.pcap -l . -k none15/7/2023 -- 22:30:37 - <Notice> - This is Suricata version 6.0.13 RELEASE running in USER mode
15/7/2023 -- 22:30:37 - <Notice> - all 3 packet processing threads, 4 management threads initialized, engine started.
15/7/2023 -- 22:30:37 - <Notice> - Signal Received.  Stopping engine.
15/7/2023 -- 22:30:37 - <Notice> - Pcap-file module read 1 files, 15547 packets, 11904606 bytes

```

Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)

```
xnoxos@htb[/htb]$ cat fast.log10/20/2021-17:04:25.166658  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53225 -> 23.152.0.91:443
10/20/2021-17:07:25.315183  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53231 -> 23.152.0.91:443
10/20/2021-17:04:24.700690  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53222 -> 23.152.0.91:443
10/20/2021-17:06:25.328173  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53229 -> 23.152.0.91:443
10/20/2021-17:10:25.311929  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53234 -> 23.152.0.91:443
10/20/2021-17:08:25.312485  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53232 -> 23.152.0.91:443
10/20/2021-17:09:25.210256  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53233 -> 23.152.0.91:443
10/20/2021-17:14:25.235711  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53243 -> 23.152.0.91:443
10/20/2021-17:11:25.213759  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53240 -> 23.152.0.91:443
10/20/2021-17:12:25.302237  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53241 -> 23.152.0.91:443
10/20/2021-17:13:25.236776  [**] [1:1002:1] Sliver C2 SSL [**] [Classification: (null)] [Priority: 3] {TCP} 10.10.20.101:53242 -> 23.152.0.91:443
---SNIP---
```