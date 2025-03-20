# Latest Email Service Vulnerabilities

Uno de los más recientes revelados públicamente y peligroso[Protocolo de transferencia de correo simple (SMTP)](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)vulnerabilidades fueron descubiertas en[Opensmtpd](https://www.opensmtpd.org/)hasta la versión 6.6.2 El servicio fue en 2020. Esta vulnerabilidad fue asignada[CVE-2020-7247](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-7247)y conduce a RCE. Ha sido explotable desde 2018. Este servicio se ha utilizado en muchas distribuciones de Linux diferentes, como Debian, Fedora, Freebsd y otros. Lo peligroso de esta vulnerabilidad es la posibilidad de ejecutar comandos del sistema de forma remota en el sistema y que explotar esta vulnerabilidad no requiere autenticación.

De acuerdo a[Shodan.io](https://www.shodan.io/), al momento de escribir este artículo (abril de 2022), hay más de 5,000 servidores OpenSMTPD de acceso público en todo el mundo, y la tendencia está creciendo. Sin embargo, esto no significa que esta vulnerabilidad afecte cada servicio. En cambio, queremos mostrarle cuán significativo sería el impacto de un RCE en caso de que se descubriera esta vulnerabilidad ahora. Sin embargo, por supuesto, esto también se aplica a todos los demás servicios.

### **Búsqueda de shodan**

![](https://academy.hackthebox.com/storage/modules/116/opensmtpd.png)

### **Tendencia de shodan**

![](https://academy.hackthebox.com/storage/modules/116/opensmtpd_trend.png)

---

# **El concepto del ataque**

Como ya sabemos, con el servicio SMTP, podemos componer correos electrónicos y enviarlos a las personas deseadas. La vulnerabilidad en este servicio se encuentra en el código del programa, a saber, en la función que registra la dirección de correo electrónico del remitente. Esto ofrece la posibilidad de escapar de la función utilizando un punto y coma (`;`) y hacer que el sistema ejecute comandos de shell arbitrarios. Sin embargo, hay un límite de 64 caracteres, que se pueden insertar como un comando. Se pueden encontrar los detalles técnicos de esta vulnerabilidad[aquí](https://www.openwall.com/lists/oss-security/2020/01/28/3).

### **El concepto de ataques**

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Aquí primero debemos inicializar una conexión con el servicio SMTP. Esto puede ser automatizado por un script o ingresarse manualmente. Después de establecer la conexión, se debe compuesto un correo electrónico en el que definimos el remitente, el destinatario y el mensaje real para el destinatario. El comando del sistema deseado se inserta en el campo del remitente conectado a la dirección del remitente con un semicolon (`;`). Tan pronto como terminemos de escribir, los datos ingresados son procesados por el proceso OpenSMTPD.

### **Iniciación del ataque**

| **Paso** | **Ejecución de código remoto** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `1.` | La fuente es la entrada del usuario que se puede ingresar manualmente o automatizarse durante la interacción directa con el servicio. | `Source` |
| `2.` | El servicio tomará el correo electrónico con la información requerida. | `Process` |
| `3.` | Escuchar los puertos estandarizados de un sistema requiere`root`privilegios en el sistema, y si se utilizan estos puertos, el servicio se ejecuta en consecuencia con privilegios elevados. | `Privileges` |
| `4.` | Como destino, la información ingresada se reenvía a otro proceso local. | `Destination` |

Esto es cuando el ciclo comienza nuevamente, pero esta vez para obtener acceso remoto al sistema de destino.

### **Activar la ejecución del código remoto**

| **Paso** | **Ejecución de código remoto** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `5.` | Esta vez, la fuente es toda la entrada, especialmente desde el área del remitente, que contiene nuestro comando del sistema. | `Source` |
| `6.` | El proceso lee toda la información y el punto y coma (`;`) interrumpe la lectura debido a reglas especiales en el código fuente que conduce a la ejecución del comando del sistema ingresado. | `Process` |
| `7.` | Dado que el servicio ya se está ejecutando con privilegios elevados, otros procesos de OpenSMTPD se ejecutarán con los mismos privilegios. Con estos, el comando del sistema que ingresamos también se ejecutará. | `Privileges` |
| `8.` | El destino para el comando del sistema puede ser, por ejemplo, la red a nuestro host a través del cual obtenemos acceso al sistema. | `Destination` |

Un[explotar](https://www.exploit-db.com/exploits/47984)ha sido publicado en el[Exploit-db](https://www.exploit-db.com/)Plataforma para esta vulnerabilidad que puede usarse para un análisis más detallado y la funcionalidad del desencadenante para la ejecución de los comandos del sistema.

---

# **Siguientes pasos**

Como hemos visto, los ataques por correo electrónico pueden conducir a una divulgación de datos confidencial a través del acceso directo a la bandeja de entrada de un usuario o combinando una configuración errónea con un correo electrónico de phishing convincente. Hay otras formas de atacar los servicios de correo electrónico que también pueden ser muy efectivos. Algunas cajas de piratería demuestran ataques por correo electrónico, como[Conejo](https://www.youtube.com/watch?v=5nnJq_IWJog), que trata con el acceso web de Outlook de forcedura de bruto (OWA) y luego enviando un documento con una macro maliciosa para Phish a un usuario,[Cola de cañón](https://0xdf.gitlab.io/2020/11/28/htb-sneakymailer.html)que tiene elementos de phishing y enumerando la bandeja de entrada de un usuario utilizando NetCat y un cliente IMAP, y[Carrete](https://0xdf.gitlab.io/2018/11/10/htb-reel.html)que se ocupó de los usuarios de SMTP que forzan bruto y phishing con un archivo RTF malicioso.

Vale la pena reproducir estas cajas, o al menos ver el video IPPSEC o leer un tutorial para ver ejemplos de estos ataques en acción. Esto se aplica a cualquier ataque demostrado en este módulo (u otros). El sitio[Ippsec.rocks](https://ippsec.rocks/?#)Se puede usar para buscar términos comunes y mostrará en qué cajas HTB aparecen, lo que revelará una gran cantidad de objetivos para practicar.