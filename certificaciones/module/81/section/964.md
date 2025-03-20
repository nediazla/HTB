# Descifrar conexiones RDP

El propósito de este laboratorio es dar una muestra de Power Wireshark. En este laboratorio, trabajaremos con el tráfico RDP. Si uno tiene la clave requerida utilizada entre los dos hosts para encriptar el tráfico, Wireshark puede desobfuscar el tráfico por nosotros.

Al realizar el IR y el análisis en la máquina de Bob, el equipo IR capturó un PCAP del tráfico RDP que notaron desde el host de Bob a otro host en la red. El líder de nuestro equipo nos ha pedido investigar la ocurrencia. Mientras peinaba a su anfitrión para más evidencia, encontraste una tecla RDP oculta en una colmena de carpetas en el anfitrión de Bob. Después de algunas investigaciones, nos damos cuenta de que podemos utilizar esa clave para descifrar el tráfico RDP para inspeccionarlo.

Intente utilizar los conceptos de las secciones del proceso de análisis para completar un análisis del análisis RDP.zip proporcionado.

---

# **Tareas:**

### **Tarea #1**

`Open the rdp.pcapng file in Wireshark.`

Descomprima el archivo zip incluido en los recursos opcionales y ábralo en Wireshark.

### **Tarea #2**

`Analyze the traffic included.`

Tómese un minuto para mirar el tráfico. Observe que hay mucha información aquí. Sabemos que nuestro enfoque está en RDP, así que tomemos un segundo para filtrar `rdp`y ver lo que regresa.

### **Filtro RDP**

![](https://academy.hackthebox.com/storage/modules/81/enc-rdp.png)

Tal como está, no se puede ver mucho, ¿verdad? Esto se debe a que RDP, por defecto, está utilizando TLS para cifrar los datos, por lo que no podremos ver nada que haya sucedido con el tráfico RDP. ¿Cómo podemos verificar su existencia en este archivo? Una forma es filtrar en los conocidos usos de puerto RDP típicamente.

`Filter on port 3389 to determine if any RDP traffic encrypted or otherwise exists.`

- **Haga clic para mostrar respuesta**
    
    Utilice el filtro de visualización `tcp.port == 3389`.
    
    ### **Filtrar para el puerto TCP 3389**
    
    ![](https://academy.hackthebox.com/storage/modules/81/3389.png)
    

Al menos podemos verificar que se estableció una sesión entre los dos hosts sobre el puerto TCP 3389.

### **Tarea #3**

`Provide the RDP-key to Wireshark so it can decrypt the traffic.`

Ahora, vayamos a esto un paso más allá y usemos la clave que encontramos para intentar descifrar el tráfico.

Para aplicar la clave en Wireshark:

1. Vaya a Editar → Preferencias → Protocolos → TLS
2. En la página TLS, seleccione Editar por lista de teclas RSA → Se abrirá una nueva ventana.
    
    ![](https://academy.hackthebox.com/storage/modules/81/import-ws.png)
    
3. Siga los pasos a continuación para importar la clave del servidor RSA.

### **Importar una clave RDP**

**Pasos**

---

Haga clic en el + para agregar una nueva clave

---

Escriba la dirección IP del servidor RDP

```
10.129.43.29
```

---

Escriba el puerto utilizado

```
3389
```

---

Protocolo archivado es igual

```
tpkt
```

o

```
blank
```

.

---

Navegar al

```
server.key
```

Archivo y agréguelo en la sección de archivo de clave.

---

Guarde y actualice su archivo PCAP.

---

### **Pasos de importación**

![](https://academy.hackthebox.com/storage/modules/81/import-steps.png)

Al filtrar una vez más en RDP, deberíamos ver algo de tráfico en la pantalla.

### **RDP en el claro**

![](https://academy.hackthebox.com/storage/modules/81/rdp-clear.png)

Desde aquí, podemos realizar un análisis del tráfico RDP. Ahora podemos seguir las transmisiones de TCP, exportar cualquier objeto potencial encontrado y cualquier otra cosa que se sienta necesario para nuestra investigación. Esto funciona porque adquirimos la clave RSA utilizada para cifrar la sesión RDP. Los pasos para adquirir la clave fueron un poco largos, pero el final es que si el certificado RDP se adquiere del servidor,,`OpenSSL`puede sacar la llave privada.

---

# **Realizar análisis del tráfico no encriptado**

Ahora que hemos roto a RDP del túnel TLS, ¿qué podemos encontrar? Realice los pasos de análisis e intente responder las preguntas a continuación.

### **Preguntas:**

¿Qué host inició la sesión RDP con nuestro servidor?

- **Haga clic para mostrar respuesta**
    
    Si prestamos atención al primer paquete, `packet # 8` Del apretón de manos de tres vías, podemos ver que el anfitrión que inició la conexión es 10.129.43.27.
    

¿Qué cuenta de usuario se utilizó para iniciar la conexión RDP?

- **Haga clic para mostrar respuesta**
    
    Cuando se filtre en `tcp.port == 3389`, podemos ver un registro etiquetado con el registro ignorado desconocido. Si examinamos el ASCII, nos mostrará un nombre de usuario.
    
    ### **Usuario**
    
    ![](https://academy.hackthebox.com/storage/modules/81/rdp-user.png)
    

---

# **Resumen:**

Este laboratorio debía servir como un ejemplo de lo que Wireshark puede hacer con los datos capturados y sus complementos. La capacidad de Wireshark para ingerir información e iluminar lo oscuro es robusta. Tener la capacidad de descifrar datos después de la ingestión es una capacidad poderosa. Este concepto podría aplicarse a cualquier protocolo que utilice el cifrado siempre que tengamos la clave que se utilizará para establecer las conexiones.