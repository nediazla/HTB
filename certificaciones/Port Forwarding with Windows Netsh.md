# Port Forwarding with Windows: Netsh

[Netsh](https://docs.microsoft.com/en-us/windows-server/networking/technologies/netsh/netsh-contexts)es una herramienta de línea de comandos de Windows que puede ayudar con la configuración de red de un sistema de Windows en particular. Estas son solo algunas de las tareas relacionadas con las redes que podemos usar`Netsh`para:

- `Finding routes`
- `Viewing the firewall configuration`
- `Adding proxies`
- `Creating port forwarding rules`

Tomemos un ejemplo del siguiente escenario donde nuestro host comprometido es una estación de trabajo de TI admin de Windows 10 (`10.129.15.150`, `172.16.5.25`). Tenga en cuenta que es posible en un compromiso que podamos acceder a la estación de trabajo de un empleado a través de métodos como la ingeniería social y el phishing. Esto nos permitiría girar más lejos de la red en la que se encuentra la estación de trabajo.

![](https://academy.hackthebox.com/storage/modules/158/88.png)

Podemos usar`netsh.exe`Para reenviar todos los datos recibidos en un puerto específico (digamos 8080) a un host remoto en un puerto remoto. Esto se puede realizar utilizando el siguiente comando.

### **Uso de netsh.exe para avanzar hacia adelante**

Reenvío de puertos con Windows Netsh

```
C:\Windows\system32> netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.42.198 connectport=3389 connectaddress=172.16.5.25

```

### **Verificación del puerto hacia adelante**

Reenvío de puertos con Windows Netsh

```
C:\Windows\system32> netsh.exe interface portproxy show v4tov4

Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
10.129.42.198   8080        172.16.5.25     3389

```

Después de configurar el`portproxy`En nuestro host Pivot basado en Windows, intentaremos conectarnos al puerto 8080 de este host desde nuestro host de ataque usando XFreerDP. Una vez que se envía una solicitud desde nuestro host de ataque, el host de Windows enrutará nuestro tráfico de acuerdo con la configuración de proxy configurada por Netsh.exe.

### **Conectarse al host interno a través del puerto hacia adelante**

![](https://academy.hackthebox.com/storage/modules/158/netsh_pivot.png)

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurada para que la conexión a su objetivo funcione sin problemas.