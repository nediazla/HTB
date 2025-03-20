# Utilising WHOIS

# **Utilizando WHOIS**

---

Consideremos tres escenarios para ayudar a ilustrar el valor de los datos de WHOIS.

# **Escenario 1: Investigación de phishing**

Una puerta de enlace de seguridad de correo electrónico señala un correo electrónico sospechoso enviado a varios empleados dentro de una empresa. El correo electrónico afirma ser del banco de la empresa e insta a los destinatarios a hacer clic en un enlace para actualizar la información de su cuenta. Un analista de seguridad investiga el correo electrónico y comienza realizando una búsqueda de WHOIS en el dominio vinculado en el correo electrónico.

El registro de WHOIS revela lo siguiente:

- `Registration Date`: El dominio fue registrado hace apenas unos días.
- `Registrant`: La información del registrante está oculta detrás de un servicio de privacidad.
- `Name Servers`: Los servidores de nombres están asociados con un conocido proveedor de alojamiento a prueba de balas que a menudo se utiliza para actividades maliciosas.

Esta combinación de factores genera importantes señales de alerta para el analista. La fecha de registro reciente, la información oculta del registrante y el alojamiento sospechoso sugieren fuertemente una campaña de phishing. El analista alerta rápidamente al departamento de TI de la empresa para que bloquee el dominio y advierte a los empleados sobre la estafa.

Una investigación más profunda sobre el proveedor de alojamiento y las direcciones IP asociadas puede descubrir dominios o infraestructura de phishing adicionales que utiliza el actor de amenazas.

# **Escenario 2: Análisis de malware**

Un investigador de seguridad está analizando una nueva cepa de malware que ha infectado varios sistemas dentro de una red. El malware se comunica con un servidor remoto para recibir comandos y extraer datos robados. Para obtener información sobre la infraestructura del actor de amenazas, el investigador realiza una búsqueda de WHOIS en el dominio asociado con el servidor de comando y control (C2).

El registro de WHOIS revela:

- `Registrant`: El dominio está registrado a nombre de una persona mediante un servicio de correo electrónico gratuito conocido por su anonimato.
- `Location`: La dirección del registrante se encuentra en un país con una alta prevalencia de delitos cibernéticos.
- `Registrar`: El dominio fue registrado a través de un registrador con un historial de políticas de abuso laxas.

Con base en esta información, el investigador concluye que el servidor C2 probablemente esté alojado en un servidor comprometido o "a prueba de balas". Luego, el investigador utiliza los datos de WHOIS para identificar al proveedor de alojamiento y notificarle sobre la actividad maliciosa.

# **Escenario 3: Informe de inteligencia sobre amenazas**

Una empresa de ciberseguridad rastrea las actividades de un sofisticado grupo de actores de amenazas conocido por atacar a instituciones financieras. Los analistas recopilan datos de WHOIS sobre múltiples dominios asociados con las campañas pasadas del grupo para compilar un informe completo de inteligencia sobre amenazas.

Al analizar los registros de WHOIS, los analistas descubren los siguientes patrones:

- `Registration Dates`: Los dominios se registraron en grupos, a menudo poco antes de ataques importantes.
- `Registrants`: Los solicitantes de registro utilizan varios alias e identidades falsas.
- `Name Servers`: Los dominios suelen compartir los mismos servidores de nombres, lo que sugiere una infraestructura común.
- `Takedown History`: Muchos dominios han sido eliminados después de los ataques, lo que indica intervenciones policiales o de seguridad previas.

Estos conocimientos permiten a los analistas crear un perfil detallado de las tácticas, técnicas y procedimientos (TTP) del actor de amenazas. El informe incluye indicadores de compromiso (IOC) basados ​​en los datos de WHOIS, que otras organizaciones pueden utilizar para detectar y bloquear futuros ataques.

# **Usando WHOIS**

Antes de usar el `whois` comando, deberá asegurarse de que esté instalado en su sistema Linux. Es una utilidad disponible a través de los administradores de paquetes de Linux y, si no está instalada, se puede instalar simplemente con

Utilizando WHOIS

```
xnoxos@htb[/htb]$ sudo apt updatexnoxos@htb[/htb]$ sudo apt install whois -y
```

La forma más sencilla de acceder a los datos de WHOIS es a través del `whois` herramienta de línea de comandos. Realicemos una búsqueda de WHOIS en`facebook.com`:

Utilizando WHOIS

```
xnoxos@htb[/htb]$ whois facebook.com   Domain Name: FACEBOOK.COM
   Registry Domain ID: 2320948_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.registrarsafe.com
   Registrar URL: http://www.registrarsafe.com
   Updated Date: 2024-04-24T19:06:12Z
   Creation Date: 1997-03-29T05:00:00Z
   Registry Expiry Date: 2033-03-30T04:00:00Z
   Registrar: RegistrarSafe, LLC
   Registrar IANA ID: 3237
   Registrar Abuse Contact Email: abusecomplaints@registrarsafe.com
   Registrar Abuse Contact Phone: +1-650-308-7004
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited   Domain Status: serverDeleteProhibited https://icann.org/epp#serverDeleteProhibited   Domain Status: serverTransferProhibited https://icann.org/epp#serverTransferProhibited   Domain Status: serverUpdateProhibited https://icann.org/epp#serverUpdateProhibited   Name Server: A.NS.FACEBOOK.COM
   Name Server: B.NS.FACEBOOK.COM
   Name Server: C.NS.FACEBOOK.COM
   Name Server: D.NS.FACEBOOK.COM
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2024-06-01T11:24:10Z <<<

[...]
Registry Registrant ID:
Registrant Name: Domain Admin
Registrant Organization: Meta Platforms, Inc.
[...]

```

La salida de WHOIS para `facebook.com` revela varios detalles clave:

1. `Domain Registration`:
    - `Registrar`: RegistrarSafe, LLC
    - `Creation Date`: 1997-03-29
    - `Expiry Date`: 2033-03-30
    
    Estos detalles indican que el dominio está registrado en RegistrarSafe, LLC y ha estado activo durante un período considerable, lo que sugiere su legitimidad y su presencia en línea establecida. La lejana fecha de caducidad refuerza aún más su longevidad.
    
2. `Domain Owner`:
    - `Registrant/Admin/Tech Organization`: Metaplataformas, Inc.
    - `Registrant/Admin/Tech Contact`: Administrador de dominio
    
    Esta información identifica a Meta Platforms, Inc. como la organización detrás `facebook.com`y "Administrador de dominio" como punto de contacto para asuntos relacionados con el dominio. Esto es consistente con la expectativa de que Facebook, una destacada plataforma de redes sociales, sea propiedad de Meta Platforms, Inc.
    
3. `Domain Status`:
    - `clientDeleteProhibited`, `clientTransferProhibited`, `clientUpdateProhibited`, `serverDeleteProhibited`, `serverTransferProhibited`, y`serverUpdateProhibited`
    
    Estos estados indican que el dominio está protegido contra cambios, transferencias o eliminaciones no autorizadas tanto en el lado del cliente como en el del servidor. Esto pone de relieve un fuerte énfasis en la seguridad y el control sobre el dominio.
    
4. `Name Servers`:
    - `A.NS.FACEBOOK.COM`, `B.NS.FACEBOOK.COM`, `C.NS.FACEBOOK.COM`, `D.NS.FACEBOOK.COM`
    
    Estos servidores de nombres están todos dentro del `facebook.com` dominio, lo que sugiere que Meta Platforms, Inc. administra su infraestructura DNS. Es una práctica común que las grandes organizaciones mantengan el control y la confiabilidad sobre su resolución DNS.
    

En general, el resultado de WHOIS para `facebook.com` se alinea con las expectativas de un dominio seguro y bien establecido propiedad de una gran organización como Meta Platforms, Inc.

Si bien el registro WHOIS proporciona información de contacto para problemas relacionados con el dominio, es posible que no sea de ayuda directa para identificar empleados individuales o vulnerabilidades específicas. Esto resalta la necesidad de combinar datos de WHOIS con otras técnicas de reconocimiento para comprender la huella digital del objetivo de manera integral.