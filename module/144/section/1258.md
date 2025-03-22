# Registros de transparencia de certificado

En la expansión de Internet, la confianza es un producto frágil. Una de las piedras angulares de esta confianza es el`Secure Sockets Layer/Transport Layer Security` (`SSL/TLS`) Protocolo, que encripta la comunicación entre su navegador y un sitio web. En el corazón de SSL/TLS se encuentra el`digital certificate`, un pequeño archivo que verifica la identidad de un sitio web y permite una comunicación segura y cifrada.

Sin embargo, el proceso de emisión y administración de estos certificados no es infalible. Los atacantes pueden explotar certificados deshonestos o mal emitidos para hacerse pasar por sitios web legítimos, interceptar datos confidenciales o difundir malware. Aquí es donde entran en juego los registros de transparencia del certificado (CT).

# **¿Qué son los registros de transparencia de certificados?**

`Certificate Transparency` (`CT`) Los registros son públicos, solo contabilidades, que registran la emisión de certificados SSL/TLS. Cada vez que una autoridad de certificado (CA) emite un nuevo certificado, debe enviarlo a múltiples registros de CT. Las organizaciones independientes mantienen estos registros y están abiertas para que cualquiera inspeccione.

Piense en los registros de CT como un`global registry of certificates`. Proporcionan un registro transparente y verificable de cada certificado SSL/TLS emitido para un sitio web. Esta transparencia tiene varios propósitos cruciales:

- `Early Detection of Rogue Certificates`: Al monitorear los registros de CT, los investigadores de seguridad y los propietarios de sitios web pueden identificar rápidamente certificados sospechosos o mal emitidos. Un certificado Rogue es un certificado digital no autorizado o fraudulento emitido por una autoridad de certificado de confianza. Detectar estos temprano permite que Swift Action revoque los certificados antes de que puedan usarse con fines maliciosos.
- `Accountability for Certificate Authorities`: Los registros de CT responsabilizan a CAS por sus prácticas de emisión. Si una CA emite un certificado que viola las reglas o estándares, será visible públicamente en los registros, lo que llevará a posibles sanciones o pérdida de confianza.
- `Strengthening the Web PKI (Public Key Infrastructure)`: El PKI web es el sistema de confianza que sustenta la comunicación segura en línea. Los registros de CT ayudan a mejorar la seguridad y la integridad del PKI web al proporcionar un mecanismo para la supervisión pública y la verificación de los certificados.
- Haga clic para expandir un desglose técnico de cómo funcionan los registros de CT
    
    # 
    
    1. 
    2. 
    3. 
    4. 
    5. 
    
    ### 
    
    ![](https://academy.hackthebox.com/storage/modules/144/diagram-001.png)
    
    - 
    - 
    - 
    1. 
    2. 
    3. 

# **Registros de CT y reconocimiento web**

Los registros de transparencia del certificado ofrecen una ventaja única en la enumeración del subdominio en comparación con otros métodos. A diferencia de los enfoques de forcedura bruta o de la lista de palabras, que se basan en adivinar o predecir nombres de subdominios, los registros de CT proporcionan un registro definitivo de certificados emitidos para un dominio y sus subdominios. Esto significa que no está limitado por el alcance de su lista de palabras o la efectividad de su algoritmo de forzamiento bruto. En cambio, obtiene acceso a una visión histórica e integral de los subdominios de un dominio, incluidos los que podrían no ser utilizados activamente o fácilmente adivinables.

Además, los registros de CT pueden revelar subdominios asociados con certificados antiguos o caducados. Estos subdominios pueden alojar software o configuraciones obsoletas, haciéndolos potencialmente vulnerables a la explotación.

En esencia, los registros de CT proporcionan una forma confiable y eficiente de descubrir subdominios sin la necesidad de un exhaustivo forzo bruto o depender de la integridad de las listas de palabras. Ofrecen una ventana única a la historia de un dominio y pueden revelar subdominios que de otro modo podrían permanecer ocultos, mejorando significativamente sus capacidades de reconocimiento.

# **Buscando registros de CT**

Hay dos opciones populares para buscar registros de CT:

| **Herramienta** | **Características clave** | **Casos de uso** | **Pros** | **Contras** |
| --- | --- | --- | --- | --- |
| [CRT.SH](https://crt.sh/) | Interfaz web fácil de usar, búsqueda simple por dominio, muestra detalles de certificados, entradas SAN. | Búsquedas rápidas y fáciles, identificación de subdominios, verificación del historial de emisión de certificados. | GRATIS, fácil de usar, no se requiere registro. | Opciones limitadas de filtrado y análisis. |
| [Censas](https://search.censys.io/) | Potente motor de búsqueda para dispositivos conectados a Internet, filtrado avanzado por dominio, IP, atributos de certificado. | Análisis en profundidad de certificados, identificación de configuraciones erróneas, encontrar certificados y hosts relacionados. | Extensas opciones de datos y filtrado, acceso de API. | Requiere registro (nivel gratuito disponible). |

### **Búsqueda CRT.SH**

Mientras`crt.sh`Ofrece una interfaz web conveniente, también puede aprovechar su API para búsquedas automatizadas directamente desde su terminal. Veamos cómo encontrar todos los subdominios 'Dev' en`facebook.com`usando`curl`y`jq`:

Registros de transparencia de certificado

```
xnoxos@htb[/htb]$ curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
*.dev.facebook.com
*.newdev.facebook.com
*.secure.dev.facebook.com
dev.facebook.com
devvm1958.ftw3.facebook.com
facebook-amex-dev.facebook.com
facebook-amex-sign-enc-dev.facebook.com
newdev.facebook.com
secure.dev.facebook.com

```

- `curl -s "https://crt.sh/?q=facebook.com&output=json"`: Este comando obtiene la salida JSON de CRT.SH para certificados que coincidan con el dominio`facebook.com`.
- `jq -r '.[] | select(.name_value | contains("dev")) | .name_value'`: Esta parte filtra los resultados de JSON, seleccionando solo entradas donde el`name_value`campo (que contiene el dominio o subdominio) incluye la cadena "`dev`". El`r`la bandera dice`jq`para emitir cadenas sin procesar.
- `sort -u`: Esto clasifica los resultados alfabéticamente y elimina los duplicados.