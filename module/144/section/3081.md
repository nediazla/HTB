# Automating Recon

Si bien el reconocimiento manual puede ser efectivo, también puede ser lento y propenso al error humano. La automatización de las tareas de reconocimiento web puede mejorar significativamente la eficiencia y la precisión, lo que le permite recopilar información a escala e identificar vulnerabilidades potenciales más rápidamente.

# **¿Por qué automatizar el reconocimiento?**

La automatización ofrece varias ventajas clave para el reconocimiento web:

- `Efficiency`: Las herramientas automatizadas pueden realizar tareas repetitivas mucho más rápido que los humanos, liberando un tiempo valioso para el análisis y la toma de decisiones.
- `Scalability`: La automatización le permite escalar sus esfuerzos de reconocimiento en una gran cantidad de objetivos o dominios, descubriendo un alcance de información más amplio.
- `Consistency`: Las herramientas automatizadas siguen reglas y procedimientos predefinidos, asegurando resultados consistentes y reproducibles y minimizando el riesgo de error humano.
- `Comprehensive Coverage`: La automatización se puede programar para realizar una amplia gama de tareas de reconocimiento, incluida la enumeración de DNS, el descubrimiento de subdominios, el rastreo web, el escaneo de puertos y más, lo que garantiza una cobertura exhaustiva de los posibles vectores de ataque.
- `Integration`: Muchos marcos de automatización permiten una fácil integración con otras herramientas y plataformas, creando un flujo de trabajo perfecto desde el reconocimiento hasta la evaluación y explotación de vulnerabilidades.

# **Marcos de reconocimiento**

Estos marcos tienen como objetivo proporcionar un conjunto completo de herramientas para el reconocimiento web:

- [Recién recogido](https://github.com/thewhiteh4t/FinalRecon): Una herramienta de reconocimiento basada en Python que ofrece una gama de módulos para diferentes tareas como verificación de certificados SSL, recopilación de información, análisis de encabezado y rastreo. Su estructura modular permite una fácil personalización para necesidades específicas.
- [Reconocer](https://github.com/lanmaster53/recon-ng): Un marco poderoso escrito en Python que ofrece una estructura modular con varios módulos para diferentes tareas de reconocimiento. Puede realizar la enumeración del DNS, el descubrimiento de subdominios, el escaneo de puertos, el rastreo web e incluso explotar las vulnerabilidades conocidas.
- [TheHarvester](https://github.com/laramies/theHarvester): Diseñado específicamente para recopilar direcciones de correo electrónico, subdominios, hosts, nombres de empleados, puertos abiertos y pancartas de diferentes fuentes públicas como motores de búsqueda, servidores de claves PGP y la base de datos de Shodan. Es una herramienta de línea de comandos escrita en Python.
- [Pie de araña](https://github.com/smicallef/spiderfoot): Una herramienta de automatización de inteligencia de código abierto que se integra con varias fuentes de datos para recopilar información sobre un objetivo, incluidas direcciones IP, nombres de dominio, direcciones de correo electrónico y perfiles de redes sociales. Puede realizar búsquedas DNS, rastreo web, escaneo de puertos y más.
- [Marco de OSINT](https://osintframework.com/): Una colección de varias herramientas y recursos para la recopilación de inteligencia de código abierto. Cubre una amplia gama de fuentes de información, incluidas las redes sociales, los motores de búsqueda, los registros públicos y más.

### **Recién recogido**

`FinalRecon`Ofrece una gran cantidad de información de reconocimiento:

- `Header Information`: Revela detalles del servidor, tecnologías utilizadas y posibles configuraciones erróneas de seguridad.
- `Whois Lookup`: Descubre detalles de registro de dominio, incluida la información del registrante y los datos de contacto.
- `SSL Certificate Information`: Examina el certificado SSL/TLS para la validez, el emisor y otros detalles relevantes.
- `Crawler`:
    - HTML, CSS, JavaScript: extrae enlaces, recursos y vulnerabilidades potenciales de estos archivos.
    - Enlaces internos/externos: mapea la estructura del sitio web e identifica las conexiones a otros dominios.
    - Imágenes, robots.txt, siteMap.xml: recoge información sobre rutas de rastreo permitidas/no permitidas y la estructura del sitio web.
    - Enlaces en JavaScript, Wayback Machine: descubre enlaces ocultos y datos históricos del sitio web.
- `DNS Enumeration`: Consultas sobre 40 tipos de registros DNS, incluidos los registros DMARC para la evaluación de seguridad por correo electrónico.
- `Subdomain Enumeration`: Aprovecha múltiples fuentes de datos (CRT.SH, ANUBISDB, amenazas, Certspotter, API de Facebook, API Virustotal, API de Shodan, API de Bevigil) para descubrir subdominios.
- `Directory Enumeration`: Admite listas de palabras personalizadas y extensiones de archivos para descubrir directorios y archivos ocultos.
- `Wayback Machine`: Recupera las URL de los últimos cinco años para analizar los cambios en el sitio web y las posibles vulnerabilidades.

La instalación es rápida y fácil:

Automatización de Recon

```
xnoxos@htb[/htb]$ git clone https://github.com/thewhiteh4t/FinalRecon.gitxnoxos@htb[/htb]$ cd FinalReconxnoxos@htb[/htb]$ pip3 install -r requirements.txtxnoxos@htb[/htb]$ chmod +x ./finalrecon.pyxnoxos@htb[/htb]$ ./finalrecon.py --helpusage: finalrecon.py [-h] [--url URL] [--headers] [--sslinfo] [--whois]
                     [--crawl] [--dns] [--sub] [--dir] [--wayback] [--ps]
                     [--full] [-nb] [-dt DT] [-pt PT] [-T T] [-w W] [-r] [-s]
                     [-sp SP] [-d D] [-e E] [-o O] [-cd CD] [-k K]

FinalRecon - All in One Web Recon | v1.1.6

optional arguments:
  -h, --help  show this help message and exit
  --url URL   Target URL
  --headers   Header Information
  --sslinfo   SSL Certificate Information
  --whois     Whois Lookup
  --crawl     Crawl Target
  --dns       DNS Enumeration
  --sub       Sub-Domain Enumeration
  --dir       Directory Search
  --wayback   Wayback URLs
  --ps        Fast Port Scan
  --full      Full Recon

Extra Options:
  -nb         Hide Banner
  -dt DT      Number of threads for directory enum [ Default : 30 ]
  -pt PT      Number of threads for port scan [ Default : 50 ]
  -T T        Request Timeout [ Default : 30.0 ]
  -w W        Path to Wordlist [ Default : wordlists/dirb_common.txt ]
  -r          Allow Redirect [ Default : False ]
  -s          Toggle SSL Verification [ Default : True ]
  -sp SP      Specify SSL Port [ Default : 443 ]
  -d D        Custom DNS Servers [ Default : 1.1.1.1 ]
  -e E        File Extensions [ Example : txt, xml, php ]
  -o O        Export Format [ Default : txt ]
  -cd CD      Change export directory [ Default : ~/.local/share/finalrecon ]
  -k K        Add API key [ Example : shodan@key ]

```

Para comenzar, primero clonarás el`FinalRecon`repositorio de Github usando`git clone https://github.com/thewhiteh4t/FinalRecon.git`. Esto creará un nuevo directorio llamado "FinalRecon" que contiene todos los archivos necesarios.

A continuación, navegue por el directorio recién creado con`cd FinalRecon`. Una vez dentro, instalará las dependencias de Python requeridas utilizando`pip3 install -r requirements.txt`. Esto asegura que`FinalRecon`tiene todas las bibliotecas y módulos que necesita funcionar correctamente.

Para asegurarse de que el script principal sea ejecutable, deberá cambiar los permisos de archivo utilizando`chmod +x ./finalrecon.py`. Esto le permite ejecutar el script directamente desde su terminal.

Finalmente, puedes verificar que`FinalRecon`se instala correctamente y obtiene una descripción general de sus opciones disponibles ejecutando`./finalrecon.py --help`. Esto mostrará un mensaje de ayuda con detalles sobre cómo usar la herramienta, incluidos los diversos módulos y sus respectivas opciones:

| **Opción** | **Argumento** | **Descripción** |
| --- | --- | --- |
| `-h`, `--help` |  | Muestre el mensaje de ayuda y la salida. |
| `--url` | Url | Especifique la URL objetivo. |
| `--headers` |  | Recupere la información del encabezado para la URL objetivo. |
| `--sslinfo` |  | Obtenga información del certificado SSL para la URL de destino. |
| `--whois` |  | Realice una búsqueda de Whois para el dominio objetivo. |
| `--crawl` |  | Ratea el sitio web de Target. |
| `--dns` |  | Realice la enumeración de DNS en el dominio objetivo. |
| `--sub` |  | Enumere los subdominios para el dominio objetivo. |
| `--dir` |  | Busque directorios en el sitio web de destino. |
| `--wayback` |  | Recupere las URL Wayback para el objetivo. |
| `--ps` |  | Realice un escaneo de puerto rápido en el objetivo. |
| `--full` |  | Realice un escaneo de reconocimiento completo en el objetivo. |

Por ejemplo, si queremos`FinalRecon`para recopilar información de encabezado y realizar una búsqueda de whois para`inlanefreight.com`, usaríamos las banderas correspondientes (`--headers`y`--whois`), para que el comando sería:

Automatización de Recon

```
xnoxos@htb[/htb]$ ./finalrecon.py --headers --whois --url http://inlanefreight.com ______  __   __   __   ______   __
/\  ___\/\ \ /\ "-.\ \ /\  __ \ /\ \
\ \  __\\ \ \\ \ \-.  \\ \  __ \\ \ \____
 \ \_\   \ \_\\ \_\\"\_\\ \_\ \_\\ \_____\
  \/_/    \/_/ \/_/ \/_/ \/_/\/_/ \/_____/
 ______   ______   ______   ______   __   __
/\  == \ /\  ___\ /\  ___\ /\  __ \ /\ "-.\ \
\ \  __< \ \  __\ \ \ \____\ \ \/\ \\ \ \-.  \
 \ \_\ \_\\ \_____\\ \_____\\ \_____\\ \_\\"\_\
  \/_/ /_/ \/_____/ \/_____/ \/_____/ \/_/ \/_/

[>] Created By   : thewhiteh4t
 |---> Twitter   : https://twitter.com/thewhiteh4t
 |---> Community : https://twc1rcle.com/
[>] Version      : 1.1.6

[+] Target : http://inlanefreight.com

[+] IP Address : 134.209.24.248

[!] Headers :

Date : Tue, 11 Jun 2024 10:08:00 GMT
Server : Apache/2.4.41 (Ubuntu)
Link : <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/", <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json", <https://www.inlanefreight.com/>; rel=shortlink
Vary : Accept-Encoding
Content-Encoding : gzip
Content-Length : 5483
Keep-Alive : timeout=5, max=100
Connection : Keep-Alive
Content-Type : text/html; charset=UTF-8

[!] Whois Lookup :

   Domain Name: INLANEFREIGHT.COM
   Registry Domain ID: 2420436757_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.registrar.amazon.com
   Registrar URL: http://registrar.amazon.com
   Updated Date: 2023-07-03T01:11:15Z
   Creation Date: 2019-08-05T22:43:09Z
   Registry Expiry Date: 2024-08-05T22:43:09Z
   Registrar: Amazon Registrar, Inc.
   Registrar IANA ID: 468
   Registrar Abuse Contact Email: abuse@amazonaws.com
   Registrar Abuse Contact Phone: +1.2024422253
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited   Name Server: NS-1303.AWSDNS-34.ORG
   Name Server: NS-1580.AWSDNS-05.CO.UK
   Name Server: NS-161.AWSDNS-20.COM
   Name Server: NS-671.AWSDNS-19.NET
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/

[+] Completed in 0:00:00.257780

[+] Exported : /home/htb-ac-643601/.local/share/finalrecon/dumps/fr_inlanefreight.com_11-06-2024_11:07:59
```