# Primer de redes - Capas 1-4

Esta sección sirve como un repaso rápido en las redes y cómo algunos protocolos estándar podemos ver al realizar el tráfico capturan el trabajo. Estos conceptos están en el centro de capturar y diseccionar el tráfico. Sin una comprensión fundamental del flujo de red típico y qué puertos y protocolos se utilizan, no podemos analizar con precisión ningún tráfico que captemos. Si esta es la primera vez que encuentra algunos de estos términos o conceptos, sugerimos completar el [Introducción a las redes](https://academy.hackthebox.com/course/preview/introduction-to-networking) Módulo primero.

---

# **Modelos OSI / TCP-IP**

### **Modelos de redes**

![](https://academy.hackthebox.com/storage/modules/81/net_models4.png)

La imagen de arriba ofrece una excelente vista de la interconexión de sistemas abiertos (`OSI`modelo) y el protocolo de control de transmisión - Protocolo de Internet (`TCP-IP`) Modelo uno al lado del otro. Los modelos son una representación gráfica de cómo se maneja la comunicación entre las computadoras en red. Tomemos un segundo para comparar los dos:

### **Rasgos modelo comparación.**

| **Rasgo** | **OSI** | **TCP-IP** |
| --- | --- | --- |
| Capas | Siete | Cuatro |
| Flexibilidad | Estricto | Perder |
| Dependencia | Protocolo independiente y genérico | Basado en protocolos de comunicación comunes |

Al examinar estos dos modelos, podemos notar que el modelo OSI está segmentado más que el modelo TCP-IP. Esto se debe a que se descompone en pequeños trozos funcionales. Las capas uno a cuatro del modelo OSI se centran en controlar el transporte de datos entre hosts. Este control incluye todo, desde el medio físico utilizado para la transmisión hasta el protocolo utilizado para administrar la conversación o la falta de ella al transportar datos. Las capas cinco a siete manejan la interpretación, la gestión y la presentación de los datos encapsulados presentados al usuario final. Piense en el modelo OSI como la teoría de cómo funciona todo, mientras que el modelo TCP-IP está más estrechamente alineado con la funcionalidad real de las redes. El modelo TCP-IP está un poco más combinado y las reglas son flexibles. El modelo TCP-IP comprende cuatro capas donde las capas cinco, seis y siete del modelo OSI se alinean con la capa cuatro del modelo TCP-IP. La capa tres trata con el transporte, la capa dos es la capa de Internet que se alinea con la capa de red en OSI, y la capa uno es la capa de enlace que cubre las capas dos y una del modelo OSI.

A lo largo de este módulo, examinaremos muchas unidades de datos de protocolo diferentes (`PDU`), por lo que se requiere una comprensión funcional de cómo aparece en teoría y en el cable. Una PDU es un paquete de datos compuesto por información de control y datos encapsulados de cada capa del modelo OSI. La ruptura a continuación mostrará cómo las capas en los dos modelos coinciden con una PDU.

### **Ejemplo de PDU**

![](https://academy.hackthebox.com/storage/modules/81/net_models_pdu2.png)

Al inspeccionar una PDU, debemos tener en cuenta la idea de la encapsulación. A medida que nuestros datos se mueven por la pila de protocolo, cada capa envolverá los datos de las capas anteriores en una nueva burbuja que llamamos encapsulación. Esta burbuja agrega la información necesaria de esa capa en el encabezado de la PDU. Esta información puede variar por nivel, pero incluye lo que mantiene la capa anterior, las banderas operativas, cualquier opción requerida para negociar comunicaciones, las direcciones IP de origen y destino, puertos, transporte y protocolos de capa de aplicación.

### **Desglose de paquetes de PDU**

![](https://academy.hackthebox.com/storage/modules/81/pdu-wireshark.png)

La imagen de arriba nos muestra la composición de una PDU lado a lado con una ruptura de paquetes del panel Detalles de paquetes de Wireshark. Tenga en cuenta que cuando vemos la ruptura en Wireshark, está en orden inverso. Wireshark nos muestra la PDU en reversa porque está en el orden en que no estaba encapsulado.

---

# **Direccionamiento de mecanismos**

Ahora que hemos repasado los conceptos básicos que conducen el comportamiento de redes de redes, nos tomemos un tiempo para discutir los mecanismos de direccionamiento que permiten la entrega de nuestros paquetes a los hosts correctos. Comenzaremos primero con las direcciones de control de acceso a los medios.

### **Dirección mac**

Cada interfaz lógica o física conectada a un host tiene un control de acceso a medios (`MAC`) DIRECCIÓN. Esta dirección es un 48 bits `six octet` dirección representada en formato hexadecimal. Si miramos la imagen a continuación, podemos ver un ejemplo de uno por el `red` flecha.

### **Directorio**

![](https://academy.hackthebox.com/storage/modules/81/Addressing.png)

La dirección mac se utiliza en la capa dos ( `the data-link or link-layer depending on which model you look at` ) Comunicaciones entre anfitriones. Esto funciona a través de la comunicación anfitriona hacia el anfitrion dentro de un dominio de transmisión. Si el tráfico de la capa dos necesita cruzar una interfaz de la capa tres, esa PDU se envía a la interfaz de salida de la capa tres, y se enruta a la red correcta. En la capa dos, esto parece que la PDU está dirigida a la interfaz del enrutador, y el enrutador tendrá en cuenta la dirección de la capa tres al determinar dónde enviarla a continuación. Una vez que toma una decisión, elimina la encapsulación en la capa dos y la reemplaza con nueva información que indica la siguiente dirección física en la ruta.

---

# **Direccionamiento de IP**

El protocolo de Internet (`IP`) se desarrolló para entregar datos de un host a otro a través de los límites de la red. IP es responsable de enrutar paquetes, la encapsulación de datos y la fragmentación y el reensamblaje de los datagramas cuando llegan al host de destino. Por naturaleza, IP es un protocolo sin conexión que no proporciona garantías de que los datos alcanzarán a su receptor previsto. Para la fiabilidad y validación de la entrega de datos, IP se basa en protocolos de capa superior como TCP. Actualmente, existen dos versiones principales de IP. IPv4, que es el estándar dominante actual, e IPv6, que pretende ser el sucesor de IPv4.

### **IPv4**

El mecanismo de direccionamiento más común con el que la mayoría está familiarizado es la dirección del protocolo de Internet versión 4 (`IPv4`). La direccionamiento de IPv4 es el método central de enrutar paquetes a través de las redes a hosts ubicados fuera de nuestras inmediaciones. La siguiente imagen nos muestra un ejemplo de una dirección IPv4 por el `green` flecha.

### **Dirección IP**

![](https://academy.hackthebox.com/storage/modules/81/Addressing.png)

Una dirección IPv4 está compuesta por un 32 bits `four octet` número representado en formato decimal. En nuestro ejemplo, podemos ver la dirección `192.168.86.243`. Cada octeto de una dirección IP puede representarse mediante un número que van desde `0` a `255`. Al examinar una PDU, encontraremos direcciones IP en la capa tres (`Network`) del modelo OSI y la capa dos (`internet`) del modelo TCP-IP. No profundizaremos en IPv4 aquí, pero en aras de este módulo, comprenderemos cuáles son estas direcciones, qué hacen por nosotros y en qué capa se usan.

### **IPv6**

Después de un poco más de una década de utilizar IPv4, se determinó que habíamos agotado rápidamente el grupo de direcciones IP utilizables. Con tan grandes trozos seccionados para uso especial o direccionamiento privado, el mundo había usado rápidamente el espacio disponible. Para ayudar a resolver este problema, se hicieron dos cosas. El primero fue implementar máscaras de subred de longitud variable (`VLSM`) y enrutamiento entre dominios sin clase (`CIDR`). Esto nos permitió redefinir las direcciones IP utilizables en el formato V4 que cambia cómo se asignaron direcciones a los usuarios. El segundo fue la creación y el desarrollo continuo de`IPv6`como sucesor de IPv4.

IPv6 nos proporciona un espacio de direcciones mucho más grande que se puede utilizar para cualquier propósito en red. IPv6 es una dirección de 128 bits `16 octets` representado en formato hexadecimal. Podemos ver un ejemplo de una dirección IPv6 acortada en la imagen a continuación por la flecha azul.

### **Dirección IPv6**

![](https://academy.hackthebox.com/storage/modules/81/Addressing.png)

Junto con un espacio de direcciones mucho más grande, IPv6 proporciona: mejor soporte para la multidifusión (enviar tráfico de uno a muchos) direccionamiento global por dispositivo seguridad dentro del protocolo en forma de paquete de IPsec simplificado Los encabezados permiten un procesamiento más fácil y pasar de la conexión a la conexión sin ser reasignados en una dirección.

IPv6 utiliza cuatro tipos principales de direcciones dentro de su esquema:

### **Tipos de direccionamiento de IPv6**

| **Tipo** | **Descripción** |
| --- | --- |
| `Unicast` | Direcciones para una sola interfaz. |
| `Anycast` | Direcciones para múltiples interfaces, donde solo una de ellas recibe el paquete. |
| `Multicast` | Direcciones para múltiples interfaces, donde todas reciben el mismo paquete. |
| `Broadcast` | No existe y se realiza con direcciones de multidifusión. |

Al pensar en cada tipo de dirección, es útil recordar que el tráfico de unidifusión es host al host, mientras que la multidifusión es una para muchos, y cualquier CASCA es uno para muchos en un grupo donde solo uno responderá al paquete. (Piense en el equilibrio de carga).

Incluso con su estado actual que proporciona muchas ventajas sobre IPv4, la adopción de IPv6 ha tardado en atrapar.

### **Adopción de IPv6**

![](https://academy.hackthebox.com/storage/modules/81/ipv6-adoption.png)

Al momento de escribir, según estadísticas publicadas por Google, la tasa de adopción es solo alrededor del 40 por ciento a nivel mundial.

---

# **TCP / UDP, mecanismos de transporte**

La capa de transporte tiene varios mecanismos para ayudar a garantizar la entrega perfecta de datos de origen a destino. Piense en la capa de transporte como un centro de control. Los datos de aplicación de las capas superiores tienen que atravesar la pila hasta la capa de transporte. Esta capa dirige cómo el tráfico se encapsulará y arrojará a los protocolos de capa inferior (IP y Mac). Una vez que los datos llegan a su destinatario previsto, la capa de transporte, que trabaja con los protocolos de capa de red / Internet, es responsable de volver a armar los datos encapsulados en el orden correcto. Los dos mecanismos utilizados para lograr esta tarea son el control de transmisión (`TCP`) y el protocolo de datagrama del usuario (`UDP`).

### **TCP vs. UDP**

Tomemos un segundo para examinar estos dos protocolos uno al lado del otro.

### **TCP vs. UDP**

| **Característica** | **TCP** | **UDP** |
| --- | --- | --- |
| `Transmission` | Orientado a la conexión | Sin conexión. Dispara y olvida. |
| `Connection Establishment` | TCP utiliza un apretón de manos de tres vías para garantizar que se establezca una conexión. | UDP no asegura que el destino esté escuchando. |
| `Data Delivery` | Conversaciones basadas en la corriente | paquete por paquete, a la fuente no le importa si el destino está activo |
| `Receipt of data` | Los números de secuencia y reconocimiento se utilizan para tener en cuenta los datos. | UDP no le importa. |
| `Speed` | TCP tiene más gastos generales y es más lento debido a sus funciones incorporadas. | UDP es rápido pero poco confiable. |

Al mirar la tabla anterior, podemos ver que TCP y UDP proporcionan dos métodos de transmisión de datos muy diferentes. TCP se considera un protocolo más confiable, ya que permite la verificación de errores y el reconocimiento de datos como una función normal. Por el contrario, UDP es un protocolo rápido, de fuego y olvida el protocolo mejor utilizado cuando nos importa la velocidad sobre la calidad y la validación.

Para poner esto en perspectiva, TCP se utiliza al mover datos que requieren integridad sobre la velocidad. Por ejemplo, cuando usamos shell seguro (`SSH`) Para conectarse de un host a otro, se abre una conexión que permanece activa mientras emite comandos y realiza acciones. Esta es una función de TCP, asegurando que nuestra conversación con el anfitrión distante no sea interrumpida. Si se interrumpe por alguna razón, TCP no volverá a montar un fragmento parcial de un paquete y lo enviará a la aplicación. Podemos evitar errores de esta manera. ¿Qué pasaría si emitiéramos un comando como `sudo passwd user` Para cambiar la contraseña del usuario en un host remoto, y durante el cambio, la parte del mensaje cae. Si esto fuera sobre UDP, no tendríamos forma de saber qué sucedió con el resto de ese mensaje y potencialmente estropear la contraseña del usuario o peor. TCP ayuda a evitar esto al reconocer cada paquete recibido para garantizar que el host de destino haya adquirido cada paquete antes de ensamblar el comando y enviarlo a la solicitud de acción.

Por otro lado, cuando requerimos respuestas rápidas o utilizamos aplicaciones que requieren velocidad por completo, UDP es nuestra respuesta. Tome la transmisión de un video, por ejemplo. El usuario no notará un píxel o dos caídos de un video de transmisión. Nos importa más ver el video sin que se detenga constantemente para amortiguar la siguiente pieza. Otro ejemplo de esto sería DNS. Cuando un host solicita una entrada de registro para inlanefreight.com, el host está buscando una respuesta rápida para continuar el proceso que estaba realizando. Lo peor que sucede si se elimina una solicitud de DNS es que se vuelve a emitir. Sin daño, sin falta. El usuario no recibirá datos corruptos debido a esta caída.

El tráfico de UDP aparece como el tráfico regular; Es un solo paquete, sin respuesta o reconocimiento que fue enviado o recibido, por lo que no hay mucho que mostrar aquí. Sin embargo, podemos echar un vistazo a TCP y cómo establece conexiones.

---

# **TCP Tres apretones de manos**

Una de las formas en que TCP garantiza la entrega de datos de servidor a cliente es la utilización de sesiones. Estas sesiones se establecen a través de lo que se llama un apretón de manos de tres vías. Para que esto suceda, TCP utiliza una opción en el encabezado TCP llamado banderas. No nos sumergiremos profundamente en las banderas TCP ahora; Sepa que las banderas comunes que veremos en un apretón de manos de tres vías son la sincronización (`SYN`) y reconocimiento (`ACK`). Cuando un host solicita tener una conversación con un servidor a través de TCP;

1. El `client` Envía un paquete con el indicador SYN establecido junto con otras opciones negociables en el encabezado TCP.
    1. Este es un paquete de sincronización. Solo se establecerá en el primer paquete desde el host y el servidor y permite establecer una sesión permitiendo que ambos extremos acuerden un número de secuencia para comenzar a comunicarse.
    2. Esto es crucial para el seguimiento de los paquetes. Junto con la sincronización del número de secuencia, muchas otras opciones se negocian en esta fase para incluir el tamaño de la ventana, el tamaño máximo del segmento y los reconocimientos selectivos.
2. El `server` Responderá con un paquete TCP que incluye un conjunto de indicadores SYN para la negociación del número de secuencia y una bandera ACK establecida para reconocer el paquete SYN anterior enviado por el host.
    1. El servidor también incluirá cualquier cambio en las opciones TCP que requiere establecer en los campos de opciones del encabezado TCP.
3. El `client` Responderá con un paquete TCP con un conjunto de banderas ACK de acuerdo en la negociación.
    1. Este paquete es el final del apretón de manos de tres vías y estableció la conexión entre el cliente y el servidor.

Echemos un vistazo rápido a esto en acción para familiarizarse con él cuando aparezca en nuestra salida de paquete más adelante en el módulo.

### **TCP Tres apretones de manos**

![](https://academy.hackthebox.com/storage/modules/81/three-way-handshake.png)

Al examinar esta salida, podemos ver el inicio de nuestro apretón de manos en la línea uno. Mirando la información resaltada en el `red box`, podemos ver que se establece nuestro indicador SYN inicial. Si miramos los números de puerto subrayados en`green`, podemos ver dos números, `57678` y `80`. El primer número es el número de puerto alto aleatorio en uso por el cliente, y el segundo es el puerto bien conocido para HTTP utilizado por el servidor para escuchar las conexiones de solicitud web entrantes. En la línea 2, podemos ver la respuesta del servidor al cliente con un `SYN / ACK` Paquete enviado a los mismos puertos. En la línea 3, podemos ver al cliente reconocer el paquete de sincronización del servidor para establecer la conexión.

El paquete 4 nos muestra que se envió la solicitud HTTP y se establece una sesión para transmitir los datos de la imagen solicitada. Podemos ver a medida que la transmisión continúa que TCP envía reconocimientos para cada parte de los datos enviados. Este es un ejemplo de la comunicación TCP típica.

Hemos visto cómo se establece una sesión con TCP; Ahora, examinemos cómo se concluye una sesión.

### **TCP Session Tavown**

![](https://academy.hackthebox.com/storage/modules/81/session-teardown.png)

En la imagen de arriba, un conjunto de paquetes similares a nuestro apretón de manos de tres vías visible al final de la salida. Así es como TCP cierra con gracia las conexiones. Otra bandera que veremos con TCP es la `FIN` bandera. Se utiliza para señalar que la transferencia de datos está finalizada y el remitente solicita la terminación de la conexión. El cliente reconoce la recepción de los datos y luego envía un `FIN` y `ACK` Para comenzar la terminación de la sesión. El servidor responde con un reconocimiento de la aleta y envía su propia aleta. Finalmente, el cliente reconoce que la sesión está completa y cierra la conexión. Antes de la terminación de la sesión, deberíamos ver un patrón de paquetes de:

1. `FIN, ACK`
2. `FIN, ACK`,
3. `ACK`

Si miramos la imagen de arriba que detalla una sesión, veremos que este es el caso. Una salida similar a esto se considera una conexión con terminación adecuada.