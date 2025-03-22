# DNS Zone Transfers

Si bien la fuerza bruta puede ser un enfoque fructífero, existe un método menos invasivo y potencialmente más eficiente para descubrir subdominios: las transferencias de zonas DNS. Este mecanismo, diseñado para replicar registros DNS entre servidores de nombres, puede convertirse sin darse cuenta en una mina de oro de información para miradas indiscretas si se configura mal.

# **¿Qué es una transferencia de zona?**

Una transferencia de zona DNS es esencialmente una copia completa de todos los registros DNS dentro de una zona (un dominio y sus subdominios) de un servidor de nombres a otro. Este proceso es esencial para mantener la coherencia y la redundancia entre los servidores DNS. Sin embargo, si no se protege adecuadamente, partes no autorizadas pueden descargar el archivo de zona completo, revelando una lista completa de subdominios, sus direcciones IP asociadas y otros datos DNS confidenciales.

[](https://mermaid.ink/svg/pako:eNqNkc9qwzAMxl9F-JSx7gV8KISWXcY2aHYYwxdjK39obGWKvBFK333ukg5aGNQnW9b3Q_q-g3LkUWk14mfC6HDb2YZtMBHyGdFR9JanCvkL-WG9vh-4C38FDeX74w52J-0oUHxQRHhjG8ca-W5mXAgy4YqpoXotM8EReygqsSxANZRJWuJOpoXSEw0gC3ku3QTfvlQLfBZh9DeOdbELbCgMPQr-58u1LZsnKEq3j_Tdo28wYJS8iVqpgBxs57PjhxPLKGnzr1E6XzNxb5SJx9xnk1A1Rae0cMKVYkpNq3Rt-zG_0uCtnLM6t6DvhPh5zvM31uMPG8qm-A)

1. `Zone Transfer Request (AXFR)`: El servidor DNS secundario inicia el proceso enviando una solicitud de transferencia de zona al servidor primario. Esta solicitud normalmente utiliza el tipo AXFR (Transferencia de zona completa).
2. `SOA Record Transfer`: Al recibir la solicitud (y potencialmente autenticar el servidor secundario), el servidor primario responde enviando su registro de Inicio de autoridad (SOA). El registro SOA contiene información vital sobre la zona, incluido su número de serie, lo que ayuda al servidor secundario a determinar si los datos de su zona están actualizados.
3. `DNS Records Transmission`: Luego, el servidor primario transfiere todos los registros DNS de la zona al servidor secundario, uno por uno. Esto incluye registros como A, AAAA, MX, CNAME, NS y otros que definen los subdominios, servidores de correo, servidores de nombres y otras configuraciones del dominio.
4. `Zone Transfer Complete`: Una vez que se han transmitido todos los registros, el servidor primario señala el final de la transferencia de zona. Esta notificación informa al servidor secundario que ha recibido una copia completa de los datos de la zona.
5. `Acknowledgement (ACK)`: El servidor secundario envía un mensaje de confirmación al servidor primario, confirmando la recepción y el procesamiento exitosos de los datos de la zona. Esto completa el proceso de transferencia de zona.

# **La vulnerabilidad de transferencia de zona**

Si bien las transferencias de zona son esenciales para una gestión DNS legítima, un servidor DNS mal configurado puede transformar este proceso en una vulnerabilidad de seguridad importante. La cuestión central radica en los controles de acceso que rigen quién puede iniciar una transferencia de zona.

En los primeros días de Internet, permitir que cualquier cliente solicitara una transferencia de zona desde un servidor DNS era una práctica común. Este enfoque abierto simplificó la administración pero abrió un enorme agujero de seguridad. Significaba que cualquiera, incluidos los actores malintencionados, podía solicitar a un servidor DNS una copia completa de su archivo de zona, que contiene una gran cantidad de información confidencial.

La información obtenida de una transferencia de zona no autorizada puede ser invaluable para un atacante. Revela un mapa completo de la infraestructura DNS del objetivo, que incluye:

- `Subdomains`: una lista completa de subdominios, muchos de los cuales pueden no estar vinculados desde el sitio web principal o no ser fácilmente detectables por otros medios. Estos subdominios ocultos podrían albergar servidores de desarrollo, entornos de prueba, paneles administrativos u otros recursos confidenciales.
- `IP Addresses`: Las direcciones IP asociadas con cada subdominio, lo que proporciona objetivos potenciales para futuros reconocimientos o ataques.
- `Name Server Records`: Detalles sobre los servidores de nombres autorizados para el dominio, que revelan el proveedor de alojamiento y posibles errores de configuración.

### **Remediación**

Afortunadamente, ha aumentado la conciencia sobre esta vulnerabilidad y la mayoría de los administradores de servidores DNS han mitigado el riesgo. Los servidores DNS modernos generalmente están configurados para permitir transferencias de zona solo a servidores secundarios confiables, lo que garantiza que los datos confidenciales de la zona permanezcan confidenciales.

Sin embargo, aún pueden producirse errores de configuración debido a errores humanos o prácticas obsoletas. Por este motivo, intentar una transferencia de zona (con la autorización adecuada) sigue siendo una técnica de reconocimiento valiosa. Incluso si no tiene éxito, el intento puede revelar información sobre la configuración y la situación de seguridad del servidor DNS.

### **Transferencias de zona de explotación**

Puedes usar el `dig` comando para solicitar una transferencia de zona:

Transferencias de zona DNS

```bash
xnoxos@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me
```

Este comando instruye `dig` para solicitar una transferencia de zona completa (`axfr`) del servidor DNS responsable de `zonetransfer.me`. Si el servidor está mal configurado y permite la transferencia, recibirá una lista completa de los registros DNS para el dominio, incluidos todos los subdominios.

Transferencias de zona DNS

```bash
xnoxos@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> axfr @nsztm1.digi.ninja zonetransfer.me
; (1 server found)
;; global options: +cmd
zonetransfer.me.	7200	IN	SOA	nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
zonetransfer.me.	300	IN	HINFO	"Casio fx-700G" "Windows XP"
zonetransfer.me.	301	IN	TXT	"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"
zonetransfer.me.	7200	IN	MX	0 ASPMX.L.GOOGLE.COM.
...
zonetransfer.me.	7200	IN	A	5.196.105.14
zonetransfer.me.	7200	IN	NS	nsztm1.digi.ninja.
zonetransfer.me.	7200	IN	NS	nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me. 301 IN	TXT	"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"
_sip._tcp.zonetransfer.me. 14000 IN	SRV	0 0 5060 www.zonetransfer.me.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200	IN PTR www.zonetransfer.me.
asfdbauthdns.zonetransfer.me. 7900 IN	AFSDB	1 asfdbbox.zonetransfer.me.
asfdbbox.zonetransfer.me. 7200	IN	A	127.0.0.1
asfdbvolume.zonetransfer.me. 7800 IN	AFSDB	1 asfdbbox.zonetransfer.me.
canberra-office.zonetransfer.me. 7200 IN A	202.14.81.230
...
;; Query time: 10 msec
;; SERVER: 81.4.108.41#53(nsztm1.digi.ninja) (TCP);; WHEN: Mon May 27 18:31:35 BST 2024
;; XFR size: 50 records (messages 1, bytes 2085)

```

`zonetransfer.me`es un servicio específicamente creado para demostrar los riesgos de las transferencias de zona para que el `dig` El comando devolverá el registro de zona completo.