# Virtual Hosts

Una vez que el DNS dirige el tráfico al servidor correcto, la configuración del servidor web se vuelve crucial para determinar cómo se manejan las solicitudes entrantes. Los servidores web como Apache, Nginx o IIS están diseñados para alojar múltiples sitios web o aplicaciones en un solo servidor. Lo logran a través del alojamiento virtual, que les permite diferenciar entre dominios, subdominios o incluso sitios web separados con contenido distinto.

# **Cómo funcionan los hosts virtuales: comprensión de los hosts virtuales y los subdominios**

En el núcleo de `virtual hosting` es la capacidad de los servidores web para distinguir entre múltiples sitios web o aplicaciones que comparten la misma dirección IP. Esto se logra aprovechando la `HTTP Host` encabezado, un dato incluido en cada `HTTP` Solicitud enviada por un navegador web.

La diferencia clave entre `VHosts` y `subdomains` es su relación con el `Domain Name System (DNS)` y la configuración del servidor web.

- `Subdomains`: Estas son extensiones de un nombre de dominio principal (por ejemplo, `blog.example.com` es un subdominio de `example.com`). `Subdomains` normalmente tienen su propio `DNS records`, apuntando a la misma dirección IP que el dominio principal o a una diferente. Se pueden utilizar para organizar diferentes secciones o servicios de un sitio web.
- `Virtual Hosts` (`VHosts`): Los hosts virtuales son configuraciones dentro de un servidor web que permiten alojar múltiples sitios web o aplicaciones en un solo servidor. Se pueden asociar con dominios de nivel superior (por ejemplo, `example.com`) o subdominios (por ejemplo, `dev.example.com`). Cada host virtual puede tener su propia configuración independiente, lo que permite un control preciso sobre cómo se manejan las solicitudes.

Si un host virtual no tiene un registro DNS, aún puede acceder a él modificando el `hosts` archivo en su máquina local. El `hosts`El archivo le permite asignar un nombre de dominio a una dirección IP manualmente, sin pasar por la resolución DNS.

Los sitios web suelen tener subdominios que no son públicos y no aparecen en los registros DNS. Estos `subdomains` Solo se puede acceder a ellos internamente o mediante configuraciones específicas. `VHost fuzzing`Es una técnica para descubrir públicos y no públicos. `subdomains` y `VHosts` probando varios nombres de host con una dirección IP conocida.

Los hosts virtuales también se pueden configurar para utilizar diferentes dominios, no sólo subdominios. Por ejemplo:

Código: apacheconf

```bash
# Example of name-based virtual host configuration in Apache
<VirtualHost *:80>ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost><VirtualHost *:80>ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost><VirtualHost *:80>ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

Aquí, `example1.com`, `example2.org`, y `another-example.net` Son dominios distintos alojados en el mismo servidor. El servidor web utiliza el`Host`encabezado para ofrecer el contenido apropiado según el nombre de dominio solicitado.

### **Búsqueda de servidor VHost**

A continuación se ilustra el proceso de cómo un servidor web determina el contenido correcto a servir basándose en el `Host` encabezamiento:

[](https://mermaid.ink/svg/pako:eNqNUsFuwjAM_ZUop00CPqAHDhubuCBNBW2XXrzUtNFap3McOoT496WUVUA3aTkltp_f84sP2rgcdaI9fgYkgwsLBUOdkYqnARZrbAMk6oFd65HHiTd8XyPvfku9WpYA1dJ5eXS0tcW4ZOFMqJEkdU4y6vNnqul8PvRO1HKzeVFpp9KLumvbdmapAsItoy1KmRlX3_fwAXTd4OkLakuoOjVqiZAj_7_PaJJEPVvK1QrElJYK1UcDg1h3HmOEmV4LSlEC0-CA6i24Zb406IRhizuM7BV6BVFCit4FNuh77GX9DeGfmEu-s_mD4b5x5PH2Y4aqhfVNBftufomsGemJrpFrsHncqkOHy7SUWGOmk3jNgT8yndEx1kEQt96T0YlwwIlmF4pSJ1uofHyFJgf52cchirkVx6t-aU-7e_wG--_4bQ)

1. `Browser Requests a Website`: Cuando ingresa un nombre de dominio (por ejemplo, `www.inlanefreight.com`) en su navegador, inicia una solicitud HTTP al servidor web asociado con la dirección IP de ese dominio.
2. `Host Header Reveals the Domain`: El navegador incluye el nombre de dominio en la solicitud`Host`encabezado, que actúa como una etiqueta para informar al servidor web qué sitio web se está solicitando.
3. `Web Server Determines the Virtual Host`: El servidor web recibe la solicitud, examina el`Host`encabezado y consulta su configuración de host virtual para encontrar una entrada coincidente para el nombre de dominio solicitado.
4. `Serving the Right Content`: Al identificar la configuración de host virtual correcta, el servidor web recupera los archivos y recursos correspondientes asociados con ese sitio web desde su raíz de documentos y los envía de regreso al navegador como respuesta HTTP.

En esencia, el `Host` El encabezado funciona como un interruptor, lo que permite al servidor web determinar dinámicamente qué sitio web servir en función del nombre de dominio solicitado por el navegador.

### **Tipos de alojamiento virtual**

Existen tres tipos principales de alojamiento virtual, cada uno con sus ventajas e inconvenientes:

1. `Name-Based Virtual Hosting`: Este método se basa únicamente en la `HTTP Host header` para distinguir entre sitios web. Es el método más común y flexible, ya que no requiere múltiples direcciones IP. Es rentable, fácil de configurar y es compatible con la mayoría de los servidores web modernos. Sin embargo, requiere que el servidor web admita archivos basados ​​en nombres.`virtual hosting`y puede tener limitaciones con ciertos protocolos como`SSL/TLS`.
2. `IP-Based Virtual Hosting`: Este tipo de hosting asigna una dirección IP única a cada sitio web alojado en el servidor. El servidor determina qué sitio web ofrecer en función de la dirección IP a la que se envió la solicitud. No depende del`Host header`, se puede utilizar con cualquier protocolo y ofrece un mejor aislamiento entre sitios web. Aún así, requiere múltiples direcciones IP, lo que puede resultar costoso y menos escalable.
3. `Port-Based Virtual Hosting`: Diferentes sitios web están asociados con diferentes puertos en la misma dirección IP. Por ejemplo, se puede acceder a un sitio web en el puerto 80, mientras que otro está en el puerto 8080.`Port-based virtual hosting`Se puede utilizar cuando las direcciones IP son limitadas, pero no es tan común ni tan fácil de usar como`name-based virtual hosting`y puede requerir que los usuarios especifiquen el número de puerto en la URL.

# **Herramientas de descubrimiento de hosts virtuales**

Mientras que el análisis manual de `HTTP headers` y revertir `DNS lookups` puede ser eficaz, especializado`virtual host discovery tools`automatizar y agilizar el proceso, haciéndolo más eficiente e integral. Estas herramientas emplean varias técnicas para sondear el servidor de destino y descubrir posibles`virtual hosts`.

Hay varias herramientas disponibles para ayudar en el descubrimiento de hosts virtuales:

| **Herramienta** | **Descripción** | **Características** |
| --- | --- | --- |
| [gobuster](https://github.com/OJ/gobuster) | Una herramienta multipropósito que se utiliza a menudo para la fuerza bruta de directorios/archivos, pero que también es eficaz para el descubrimiento de hosts virtuales. | Rápido, admite múltiples métodos HTTP y puede usar listas de palabras personalizadas. |
| [Feroxbuster](https://github.com/epi052/feroxbuster) | Similar a Gobuster, pero con una implementación basada en Rust, conocida por su velocidad y flexibilidad. | Admite recursividad, descubrimiento de comodines y varios filtros. |
| [ufff](https://github.com/ffuf/ffuf) | Otro fuzzer web rápido que se puede utilizar para el descubrimiento de hosts virtuales al difuminar el `Host`encabezamiento. | Opciones de filtrado y entrada de lista de palabras personalizables. |

### **gobuster**

Gobuster es una herramienta versátil comúnmente utilizada para la fuerza bruta de directorios y archivos, pero también destaca en el descubrimiento de hosts virtuales. Envía sistemáticamente solicitudes HTTP con diferentes`Host`encabezados a una dirección IP de destino y luego analiza las respuestas para identificar hosts virtuales válidos.

Hay un par de cosas que debes preparar para la fuerza bruta. `Host` encabezados:

1. `Target Identification`: Primero, identifique la dirección IP del servidor web de destino. Esto se puede hacer mediante búsquedas de DNS u otras técnicas de reconocimiento.
2. `Wordlist Preparation`: Prepare una lista de palabras que contenga posibles nombres de host virtuales. Puede utilizar una lista de palabras precompilada, como SecLists, o crear una personalizada basada en la industria de su objetivo, las convenciones de nomenclatura u otra información relevante.

El `gobuster` El comando para aplicar fuerza bruta a vhosts generalmente se ve así:

Anfitriones virtuales

```bash
xnoxos@htb[/htb]$ gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain

```

- El `u` bandera especifica la URL de destino (reemplazar `<target_IP_address>` con la IP real).
- El `w` bandera especifica el archivo de lista de palabras (reemplazar `<wordlist_file>` con la ruta a tu lista de palabras).
- El `-append-domain` flag agrega el dominio base a cada palabra en la lista de palabras.

En las versiones más recientes de Gobuster, se requiere el indicador --append-domain para agregar el dominio base a cada palabra en la lista de palabras al realizar el descubrimiento de host virtual. Este indicador garantiza que Gobuster construya correctamente los nombres de host virtuales completos, lo cual es esencial para la enumeración precisa de subdominios potenciales. En versiones anteriores de Gobuster, esta funcionalidad se manejaba de manera diferente y el indicador --append-domain no era necesario. Es posible que los usuarios de versiones anteriores no encuentren este indicador disponible o necesario, ya que la herramienta agregó el dominio base de forma predeterminada o empleó un mecanismo diferente para la generación de host virtual.

`Gobuster`generará hosts virtuales potenciales a medida que los descubra. Analice los resultados cuidadosamente y observe cualquier hallazgo inusual o interesante. Es posible que sea necesario realizar más investigaciones para confirmar la existencia y funcionalidad de los hosts virtuales descubiertos.

Hay un par de argumentos más que vale la pena conocer:

- Considere usar el `t` bandera para aumentar el número de subprocesos para un escaneo más rápido.
- El `k` flag puede ignorar los errores del certificado SSL/TLS.
- Puedes usar el `o` bandera para guardar el resultado en un archivo para su posterior análisis.

Anfitriones virtuales

```bash
xnoxos@htb[/htb]$ gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://inlanefreight.htb:81
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Found: forum.inlanefreight.htb:81 Status: 200 [Size: 100]
[...]
Progress: 114441 / 114442 (100.00%)
===============================================================
Finished
===============================================================

```

El descubrimiento de hosts virtuales puede generar un tráfico significativo y puede ser detectado por sistemas de detección de intrusiones (IDS) o firewalls de aplicaciones web (WAF). Tenga cuidado y obtenga la autorización adecuada antes de escanear cualquier objetivo.