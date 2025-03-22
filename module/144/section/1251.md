# Digging DNS

Habiendo establecido una comprensión sólida de los fundamentos del DNS y sus diversos tipos de registros, pasemos ahora a la práctica. Esta sección explorará las herramientas y técnicas para aprovechar DNS para el reconocimiento web.

# **Herramientas DNS**

El reconocimiento de DNS implica el uso de herramientas especializadas diseñadas para consultar servidores DNS y extraer información valiosa. Estas son algunas de las herramientas más populares y versátiles del arsenal de los profesionales del reconocimiento web:

| **Herramienta** | **Características clave** | **Casos de uso** |
| --- | --- | --- |
| `dig` | Herramienta de búsqueda de DNS versátil que admite varios tipos de consultas (A, MX, NS, TXT, etc.) y resultados detallados. | Consultas manuales de DNS, transferencias de zona (si están permitidas), resolución de problemas de DNS y análisis en profundidad de registros DNS. |
| `nslookup` | Herramienta de búsqueda de DNS más sencilla, principalmente para registros A, AAAA y MX. | Consultas DNS básicas, comprobaciones rápidas de resolución de dominio y registros del servidor de correo. |
| `host` | Herramienta de búsqueda de DNS optimizada con resultados concisos. | Comprobaciones rápidas de registros A, AAAA y MX. |
| `dnsenum` | Herramienta automatizada de enumeración de DNS, ataques de diccionario, fuerza bruta, transferencias de zona (si se permiten). | Descubrir subdominios y recopilar información DNS de manera eficiente. |
| `fierce` | Herramienta de reconocimiento de DNS y enumeración de subdominios con búsqueda recursiva y detección de comodines. | Interfaz fácil de usar para reconocimiento de DNS, identificación de subdominios y objetivos potenciales. |
| `dnsrecon` | Combina múltiples técnicas de reconocimiento de DNS y admite varios formatos de salida. | Enumeración completa de DNS, identificación de subdominios y recopilación de registros DNS para su posterior análisis. |
| `theHarvester` | Herramienta OSINT que recopila información de diversas fuentes, incluidos registros DNS (direcciones de correo electrónico). | Recopilar direcciones de correo electrónico, información de empleados y otros datos asociados con un dominio de múltiples fuentes. |
| Servicios de búsqueda de DNS en línea | Interfaces fáciles de usar para realizar búsquedas de DNS. | Búsquedas de DNS rápidas y sencillas, prácticas cuando las herramientas de línea de comandos no están disponibles, para comprobar la disponibilidad del dominio o información básica |

# **El manipulador de información de dominio**

El `dig` dominio (`Domain Information Groper`) es una utilidad versátil y poderosa para consultar servidores DNS y recuperar varios tipos de registros DNS. Su flexibilidad y resultados detallados y personalizables lo convierten en la opción ideal.

### **Comandos de excavación comunes**

| **Dominio** | **Descripción** |
| --- | --- |
| `dig domain.com` | Realiza una búsqueda de registro A predeterminada para el dominio. |
| `dig domain.com A` | Recupera la dirección IPv4 (registro A) asociada con el dominio. |
| `dig domain.com AAAA` | Recupera la dirección IPv6 (registro AAAA) asociada al dominio. |
| `dig domain.com MX` | Encuentra los servidores de correo (registros MX) responsables del dominio. |
| `dig domain.com NS` | Identifica los servidores de nombres autorizados para el dominio. |
| `dig domain.com TXT` | Recupera cualquier registro TXT asociado con el dominio. |
| `dig domain.com CNAME` | Recupera el registro de nombre canónico (CNAME) del dominio. |
| `dig domain.com SOA` | Recupera el registro de inicio de autoridad (SOA) para el dominio. |
| `dig @1.1.1.1 domain.com` | Especifica un servidor de nombres específico para consultar; en este caso 1.1.1.1 |
| `dig +trace domain.com` | Muestra la ruta completa de la resolución DNS. |
| `dig -x 192.168.1.1` | Realiza una búsqueda inversa en la dirección IP 192.168.1.1 para encontrar el nombre de host asociado. Es posible que necesite especificar un servidor de nombres. |
| `dig +short domain.com` | Proporciona una respuesta breve y concisa a la consulta. |
| `dig +noall +answer domain.com` | Muestra solo la sección de respuestas del resultado de la consulta. |
| `dig domain.com ANY` | Recupera todos los registros DNS disponibles para el dominio (Nota: muchos servidores DNS ignoran `ANY` consultas para reducir la carga y evitar abusos, según[RFC 8482](https://datatracker.ietf.org/doc/html/rfc8482)). |

Precaución: algunos servidores pueden detectar y bloquear consultas DNS excesivas. Tenga cuidado y respete los límites de tarifas. Obtenga siempre permiso antes de realizar un reconocimiento DNS exhaustivo en un objetivo.

# **DNS a tientas**

Excavando DNS

```
xnoxos@htb[/htb]$ dig google.com; <<>> DiG 9.18.24-0ubuntu0.22.04.1-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             0       IN      A       142.251.47.142

;; Query time: 0 msec
;; SERVER: 172.23.176.1#53(172.23.176.1) (UDP);; WHEN: Thu Jun 13 10:45:58 SAST 2024
;; MSG SIZE  rcvd: 54

```

Esta salida es el resultado de una consulta DNS utilizando el `dig` comando para el dominio `google.com`. El comando se ejecutó en un sistema que ejecuta`DiG`versión `9.18.24-0ubuntu0.22.04.1-Ubuntu`. El resultado se puede dividir en cuatro secciones clave:

1. Encabezamiento
    - `;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449`: Esta línea indica el tipo de consulta (`QUERY`), el estado exitoso (`NOERROR`) y un identificador único (`16449`) para esta consulta específica.
        - `;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0`: Esto describe las banderas en el encabezado DNS:
            - `qr`: Indicador de respuesta de consulta: indica que se trata de una respuesta.
            - `rd`: Indicador de recursividad deseada: significa que se solicitó recursividad.
            - `ad`: Indicador de datos auténticos: significa que el solucionador considera los datos auténticos.
            - Los números restantes indican la cantidad de entradas en cada sección de la respuesta DNS: 1 pregunta, 1 respuesta, 0 registros de autoridad y 0 registros adicionales.
    - `;; WARNING: recursion requested but not available`: Esto indica que se solicitó la recursividad, pero el servidor no la admite.
2. Sección de preguntas
    - `;google.com. IN A`: Esta línea especifica la pregunta: "¿Cuál es la dirección IPv4 (registro A) para`google.com`?"
3. Sección de respuestas
    - `google.com. 0 IN A 142.251.47.142`: Esta es la respuesta a la consulta. Indica que la dirección IP asociada con`google.com`es `142.251.47.142`. El '`0`' representa el `TTL` (tiempo de vida), que indica cuánto tiempo se puede almacenar en caché el resultado antes de actualizarlo.
4. Pie de página
    - `;; Query time: 0 msec`: Muestra el tiempo que tardó en procesarse la consulta y recibirse la respuesta (0 milisegundos).
    - `;; SERVER: 172.23.176.1#53(172.23.176.1) (UDP)`: Identifica el servidor DNS que proporcionó la respuesta y el protocolo utilizado (UDP).
    - `;; WHEN: Thu Jun 13 10:45:58 SAST 2024`: Esta es la marca de tiempo de cuando se realizó la consulta.
    - `;; MSG SIZE rcvd: 54`: Indica el tamaño del mensaje DNS recibido (54 bytes).

Un `opt pseudosection` A veces puede existir en un `dig` consulta. Esto se debe a los mecanismos de extensión para DNS (`EDNS`), que permite funciones adicionales como mensajes de mayor tamaño y extensiones de seguridad DNS (`DNSSEC`) apoyo.

Si solo desea la respuesta a la pregunta, sin ninguna otra información, puede consultar `dig` usando`+short`:

Excavando DNS

```
xnoxos@htb[/htb]$ dig +short hackthebox.com104.18.20.126
104.18.21.126
```