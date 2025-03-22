# Subdomain Bruteforcing

`Subdomain Brute-Force Enumeration` es una poderosa técnica de descubrimiento de subdominios activos que aprovecha listas predefinidas de posibles nombres de subdominios. Este enfoque prueba sistemáticamente estos nombres con el dominio de destino para identificar subdominios válidos. Al utilizar listas de palabras cuidadosamente elaboradas, puede aumentar significativamente la eficiencia y eficacia de sus esfuerzos de descubrimiento de subdominios.

El proceso se divide en cuatro pasos:

1. `Wordlist Selection`: El proceso comienza con la selección de una lista de palabras que contiene posibles nombres de subdominios. Estas listas de palabras pueden ser:
    - `General-Purpose`: Contiene una amplia gama de nombres de subdominios comunes (p. ej., `dev`, `staging`, `blog`, `mail`, `admin`, `test`). Este enfoque es útil cuando no se conocen las convenciones de nomenclatura del objetivo.
    - `Targeted`: Centrado en industrias, tecnologías o patrones de nombres específicos relevantes para el objetivo. Este enfoque es más eficiente y reduce las posibilidades de falsos positivos.
    - `Custom`: Puede crear su propia lista de palabras basada en palabras clave específicas, patrones o información recopilada de otras fuentes.
2. `Iteration and Querying`: un script o herramienta recorre en iteración la lista de palabras y agrega cada palabra o frase al dominio principal (p. ej., `example.com`) para crear posibles nombres de subdominio (por ejemplo, `dev.example.com`, `staging.example.com`).
3. `DNS Lookup`: Se realiza una consulta DNS para cada subdominio potencial para verificar si se resuelve en una dirección IP. Normalmente, esto se hace utilizando el tipo de registro A o AAAA.
4. `Filtering and Validation`: si un subdominio se resuelve correctamente, se agrega a una lista de subdominios válidos. Es posible que se tomen pasos de validación adicionales para confirmar la existencia y funcionalidad del subdominio (por ejemplo, intentando acceder a él a través de un navegador web).

Hay varias herramientas disponibles que destacan en la enumeración de fuerza bruta:

| **Herramienta** | **Descripción** |
| --- | --- |
| [dnsenum](https://github.com/fwaeytens/dnsenum) | Completa herramienta de enumeración de DNS que admite ataques de diccionario y de fuerza bruta para descubrir subdominios. |
| [feroz](https://github.com/mschwager/fierce) | Herramienta fácil de usar para el descubrimiento recursivo de subdominios, que incluye detección de comodines y una interfaz fácil de usar. |
| [dnsrecon](https://github.com/darkoperator/dnsrecon) | Herramienta versátil que combina múltiples técnicas de reconocimiento de DNS y ofrece formatos de salida personalizables. |
| [acumular](https://github.com/owasp-amass/amass) | Herramienta mantenida activamente centrada en el descubrimiento de subdominios, conocida por su integración con otras herramientas y amplias fuentes de datos. |
| [buscador de activos](https://github.com/tomnomnom/assetfinder) | Herramienta sencilla pero eficaz para encontrar subdominios utilizando diversas técnicas, ideal para escaneos rápidos y ligeros. |
| [purés](https://github.com/d3mondev/puredns) | Potente y flexible herramienta de fuerza bruta DNS, capaz de resolver y filtrar resultados de forma eficaz. |

### **DNSEnum**

`dnsenum`es una herramienta de línea de comandos versátil y ampliamente utilizada escrita en Perl. Es un conjunto de herramientas integral para el reconocimiento de DNS, que proporciona varias funcionalidades para recopilar información sobre la infraestructura DNS de un dominio de destino y sus posibles subdominios. La herramienta ofrece varias funciones clave:

- `DNS Record Enumeration`: `dnsenum`puede recuperar varios registros DNS, incluidos registros A, AAAA, NS, MX y TXT, lo que proporciona una descripción general completa de la configuración DNS del objetivo.
- `Zone Transfer Attempts`: La herramienta intenta automáticamente transferencias de zona desde servidores de nombres descubiertos. Si bien la mayoría de los servidores están configurados para evitar transferencias de zona no autorizadas, un intento exitoso puede revelar un tesoro escondido de información DNS.
- `Subdomain Brute-Forcing`: `dnsenum`admite la enumeración de subdominios por fuerza bruta utilizando una lista de palabras. Esto implica probar sistemáticamente los nombres de subdominios potenciales con el dominio de destino para identificar los válidos.
- `Google Scraping`: La herramienta puede extraer los resultados de búsqueda de Google para encontrar subdominios adicionales que podrían no aparecer directamente en los registros DNS.
- `Reverse Lookup`: `dnsenum`puede realizar búsquedas DNS inversas para identificar dominios asociados con una dirección IP determinada, lo que podría revelar otros sitios web alojados en el mismo servidor.
- `WHOIS Lookups`: La herramienta también puede realizar consultas de WHOIS para recopilar información sobre la propiedad del dominio y los detalles de registro.

Vamos a ver `dnsenum` en acción demostrando cómo enumerar subdominios para nuestro objetivo, `inlanefreight.com`. En esta demostración, usaremos el`subdomains-top1million-5000.txt`lista de palabras de [Listas secundarias](https://github.com/danielmiessler/SecLists), que contiene los 5000 subdominios más comunes.

Código: intento

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```

En este comando:

- `dnsenum --enum inlanefreight.com`: especificamos el dominio de destino que queremos enumerar, junto con un acceso directo para algunas opciones de ajuste`-enum`.
- `f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`: Indicamos la ruta a la lista de palabras SecLists que usaremos para la fuerza bruta. Ajuste la ruta si su instalación de SecLists es diferente.
- `r`: Esta opción habilita la fuerza bruta recursiva en subdominios, lo que significa que si `dnsenum` encuentra un subdominio, luego intentará enumerar los subdominios de ese subdominio.

Fuerza bruta de subdominio

```bash
xnoxos@htb[/htb]$ dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----

Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]

done.
```