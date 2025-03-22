# Whois

WHOIS es un protocolo de consulta y respuesta ampliamente utilizado diseñado para acceder a bases de datos que almacenan información sobre recursos de Internet registrados. Principalmente asociado con nombres de dominio, WHOIS también puede proporcionar detalles sobre bloques de direcciones IP y sistemas autónomos. Piense en ello como una guía telefónica gigante para Internet, que le permite buscar quién posee o es responsable de diversos activos en línea.

QUIÉN ES

```
xnoxos@htb[/htb]$ whois inlanefreight.com[...]
Domain Name: inlanefreight.com
Registry Domain ID: 2420436757_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.registrar.amazon
Registrar URL: https://registrar.amazon.com
Updated Date: 2023-07-03T01:11:15Z
Creation Date: 2019-08-05T22:43:09Z
[...]

```

Cada registro de WHOIS normalmente contiene la siguiente información:

- `Domain Name`: el nombre de dominio en sí (por ejemplo, ejemplo.com)
- `Registrar`: la empresa donde se registró el dominio (por ejemplo, GoDaddy, Namecheap)
- `Registrant Contact`: La persona u organización que registró el dominio.
- `Administrative Contact`: La persona responsable de gestionar el dominio.
- `Technical Contact`: La persona que maneja los problemas técnicos relacionados con el dominio.
- `Creation and Expiration Dates`: Cuándo se registró el dominio y cuándo expirará.
- `Name Servers`: Servidores que traducen el nombre de dominio en una dirección IP.

# **Historia de WHOIS**

La historia de WHOIS está intrínsecamente ligada a la visión y dedicación de [Elizabeth Feinler](https://en.wikipedia.org/wiki/Elizabeth_J._Feinler), un científico informático que desempeñó un papel fundamental en la configuración de los inicios de Internet.

En la década de 1970, Feinler y su equipo en el Centro de Información de Redes (NIC) del Instituto de Investigación de Stanford reconocieron la necesidad de un sistema para rastrear y gestionar el creciente número de recursos de red en ARPANET, el precursor de la Internet moderna. Su solución fue la creación del directorio WHOIS, una base de datos rudimentaria pero innovadora que almacenaba información sobre usuarios de la red, nombres de host y nombres de dominio.

- Haga clic para ampliar una parte interesante de la historia de Internet si está interesado.
    
    ### 
    
    ### 
    
    ### 
    
    ### 
    

# **Por qué WHOIS es importante para Web Recon**

Los datos de WHOIS sirven como un tesoro de información para los evaluadores de penetración durante la fase de reconocimiento de una evaluación. Ofrece información valiosa sobre la huella digital de la organización objetivo y sus posibles vulnerabilidades:

- `Identifying Key Personnel`: Los registros de WHOIS a menudo revelan los nombres, direcciones de correo electrónico y números de teléfono de las personas responsables de administrar el dominio. Esta información se puede aprovechar para ataques de ingeniería social o para identificar objetivos potenciales para campañas de phishing.
- `Discovering Network Infrastructure`: Los detalles técnicos como servidores de nombres y direcciones IP proporcionan pistas sobre la infraestructura de red del objetivo. Esto puede ayudar a los evaluadores de penetración a identificar posibles puntos de entrada o configuraciones erróneas.
- `Historical Data Analysis`: Acceder a registros históricos de WHOIS a través de servicios como [WhoisFreaks](https://whoisfreaks.com/) puede revelar cambios de propiedad, información de contacto o detalles técnicos a lo largo del tiempo. Esto puede resultar útil para seguir la evolución de la presencia digital del objetivo.