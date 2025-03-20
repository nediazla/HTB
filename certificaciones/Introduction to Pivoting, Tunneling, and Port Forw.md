# Introduction to Pivoting, Tunneling, and Port Forwarding

![](https://academy.hackthebox.com/storage/modules/158/PivotingandTunnelingVisualized.gif)

Durante un`red team engagement`, `penetration test`, o un`Active Directory assessment`, a menudo nos encontraremos en una situación en la que ya nos hayamos comprometido a los requeridos`credentials`, `ssh keys`, `hashes`, o`access tokens`Para pasar a otro anfitrión, pero puede que no haya otro anfitrión directamente accesible de nuestro anfitrión de ataque. En tales casos, es posible que necesitemos usar un`pivot host`Que ya nos hemos comprometido a encontrar una manera de nuestro próximo objetivo. Una de las cosas más importantes para hacer al aterrizar en un anfitrión por primera vez es verificar nuestro`privilege level`, `network connections`y potencial`VPN or other remote access software`. Si un host tiene más de un adaptador de red, es probable que podamos usarlo para movernos a un segmento de red diferente. Pivotar es esencialmente la idea de`moving to other networks through a compromised host to find more targets on different network segments`.

Hay muchos términos diferentes utilizados para describir un host comprometido que podemos usar para`pivot`a un segmento de red previamente inalcanzable. Algunos de los más comunes son:

- `Pivot Host`
- `Proxy`
- `Foothold`
- `Beach Head system`
- `Jump Host`

El uso principal de Pivoting es derrotar a la segmentación (tanto física como virtualmente) para acceder a una red aislada.`Tunneling`, por otro lado, es un subconjunto de pivote. El túnel encapsula el tráfico de la red en otro protocolo y enruta el tráfico a través de él. Piense en ello así:

Tenemos un`key`Necesitamos enviar a un compañero, pero no queremos que nadie que vea nuestro paquete sepa que es una clave. Así que obtenemos un juguete de animal de peluche y escondemos la llave adentro con instrucciones sobre lo que hace. Luego empaquetamos el juguete y se lo enviamos a nuestro compañero. Cualquiera que inspeccione la caja verá un juguete de peluche simple, sin darse cuenta de que contiene algo más. Solo nuestro socio sabrá que la clave está oculta por dentro y aprenderá cómo acceder y usarla una vez entregada.

Las aplicaciones típicas como VPN o navegadores especializados son solo otra forma de tráfico de red de túneles.

---

Inevitablemente encontraremos varios términos diferentes utilizados para describir lo mismo en la industria de It y Infosec. Con pivote, notaremos que esto a menudo se conoce como`Lateral Movement`.

`Isn't it the same thing as pivoting?`

La respuesta a eso no es exactamente. Tomemos un segundo para comparar y contrastar`Lateral Movement`con`Pivoting and Tunneling`, ya que puede haber cierta confusión de por qué algunos los consideran diferentes conceptos.

---

# **Movimiento lateral, pivote y túneles en comparación**

### **Movimiento lateral**

El movimiento lateral se puede describir como una técnica utilizada para promover nuestro acceso a`hosts`, `applications`, y`services`dentro de un entorno de red. El movimiento lateral también puede ayudarnos a obtener acceso a recursos de dominio específicos que podemos necesitar para elevar nuestros privilegios. El movimiento lateral a menudo permite la escalada de privilegios entre los anfitriones. Además de la explicación que hemos proporcionado para este concepto, también podemos estudiar cómo otras organizaciones respetadas explican el movimiento lateral. Consulte estas dos explicaciones cuando el tiempo lo permita:

[Explicación de Palo Alto Network](https://www.paloaltonetworks.com/cyberpedia/what-is-lateral-movement)

[Explicación de Mitre](https://attack.mitre.org/tactics/TA0008/)

Un ejemplo práctico de`Lateral Movement`sería:

Durante una evaluación, obtuvimos acceso inicial al entorno objetivo y pudimos obtener el control de la cuenta de administrador local. Realizamos un escaneo de red y encontramos tres hosts más de Windows en la red. Intentamos usar las mismas credenciales de administrador local, y uno de esos dispositivos compartió la misma cuenta de administrador. Utilizamos las credenciales para movernos lateralmente a ese otro dispositivo, lo que nos permite comprometer aún más el dominio.

### **Pivote**

Utilizando múltiples hosts para cruzar`network`Límites a los que generalmente no tendrías acceso. Este es más un objetivo objetivo. El objetivo aquí es permitirnos avanzar en una red comprometiendo hosts o infraestructura específicos.

Un ejemplo práctico de`Pivoting`sería:

Durante un compromiso difícil, el objetivo tenía su red se separó física y lógicamente. Esta separación nos dificultó movernos y completar nuestros objetivos. Tuvimos que buscar en la red y comprometer un host que resultó ser la estación de trabajo de ingeniería utilizada para mantener y monitorear el equipo en el entorno operativo, enviar informes y realizar otras tareas administrativas en el entorno empresarial. Ese anfitrión resultó ser de doble salto (con más de una NIC física conectada a diferentes redes). Sin que tuviera acceso a las redes empresariales y operativas, no habríamos podido pivotar, ya que necesitábamos completar nuestra evaluación.

### **Túnel**

A menudo nos encontramos utilizando varios protocolos para transportar el tráfico dentro/fuera de una red donde existe la posibilidad de que se detecte nuestro tráfico. Por ejemplo, el uso de HTTP para enmascarar nuestro comando y control del tráfico de un servidor que poseemos al host de víctima. La clave aquí es la ofuscación de nuestras acciones para evitar la detección durante el mayor tiempo posible. Utilizamos protocolos con medidas de seguridad mejoradas como HTTPS sobre TLS o SSH sobre otros protocolos de transporte. Estos tipos de acciones también permiten tácticas como la exfiltración de datos fuera de una red de destino o la entrega de más cargas útiles e instrucciones en la red.

Un ejemplo práctico de`Tunneling`sería:

Una forma en que usamos túneles era crear nuestro tráfico para esconderse en HTTP y HTTPS. Esta es una forma común en que mantuvimos el comando y el control (C2) de los hosts que nos habíamos comprometido dentro de una red. Enmascaramos nuestras instrucciones dentro de las solicitudes de Get y Post que aparecían como tráfico normal y, para el ojo no capacitado, parecería una solicitud web o respuesta a cualquier sitio web antiguo. Si el paquete se formara correctamente, se reenviaría a nuestro servidor de control. Si no fuera así, se redirigiría a otro sitio web, potencialmente arrojando al defensor revisándolo.

Para resumir, debemos ver estas tácticas como cosas separadas. El movimiento lateral nos ayuda a extendernos dentro de una red, elevando nuestros privilegios, mientras que el pivote nos permite profundizar en las redes que acceden a entornos previamente inalcanzables. Tenga en cuenta esta comparación mientras se mueve a través de este módulo.

---