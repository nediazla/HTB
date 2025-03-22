# RDP and SOCKS Tunneling with SocksOverRDP

A menudo hay veces durante una evaluación en la que podemos limitar a una red de Windows y no podemos usar SSH para pivotar. Tendríamos que usar herramientas disponibles para los sistemas operativos de Windows en estos casos.[Calcetines](https://github.com/nccgroup/SocksOverRDP)es un ejemplo de una herramienta que usa`Dynamic Virtual Channels` (`DVC`) desde la función de servicio de escritorio remoto de Windows. DVC es responsable de los paquetes de túneles a través de la conexión RDP. Algunos ejemplos de uso de esta característica serían la transferencia de datos del portapapeles y el intercambio de audio. Sin embargo, esta característica también se puede utilizar para túnel paquetes arbitrarios a través de la red. Podemos usar`SocksOverRDP`para túnel nuestros paquetes personalizados y luego proxy a través de él. Usaremos la herramienta[Proxificador](https://www.proxifier.com/)Como nuestro servidor proxy.

Podemos comenzar descargando los binarios apropiados a nuestro host de ataque para realizar este ataque. Tener los binarios en nuestro host de ataque nos permitirá transferirlos a cada objetivo donde sea necesario. Necesitaremos:

1. [SockSoverrdp X64 binarios](https://github.com/nccgroup/SocksOverRDP/releases)
2. [Proxificador binario portátil](https://www.proxifier.com/download/#win-tab)
- Podemos buscar`ProxifierPE.zip`

Luego podemos conectarnos al objetivo usando xfreerdp y copiar el`SocksOverRDPx64.zip`Archivo al objetivo. Desde el objetivo de Windows, necesitaremos cargar el socksoverrdp.dll usando regsvr32.exe.

### **Carga de socksoverrdp.dll usando regsvr32.exe**

RDP y túneles de calcetines con SockSoverrdp

```
C:\Users\htb-student\Desktop\SocksOverRDP-x64> regsvr32.exe SocksOverRDP-Plugin.dll

```

![](https://academy.hackthebox.com/storage/modules/158/socksoverrdpdll.png)

Ahora podemos conectarnos a 172.16.5.19 a través de RDP usando`mstsc.exe`, y debemos recibir un mensaje de que el complemento SockSoverrDP está habilitado, y escuchará el 127.0.0.1:1080. Podemos usar las credenciales`victor:pass@123`para conectarse a 172.16.5.19.

![](https://academy.hackthebox.com/storage/modules/158/pivotingtoDC.png)

Tendremos que transferir SockSoverrdPX64.zip o simplemente el SockSoverrdp-Server.exe a 172.16.5.19. Luego podemos iniciar SockSoverrdp-Server.exe con privilegios de administración.

![](https://academy.hackthebox.com/storage/modules/158/executingsocksoverrdpserver.png)

Cuando volvamos a nuestro objetivo de punto de apoyo y veremos con NetStat, deberíamos ver que nuestro oyente de calcetines comenzó el 127.0.0.1:1080.

### **Confirmando que se inicia el oyente de los calcetines**

RDP y túneles de calcetines con SockSoverrdp

```
C:\Users\htb-student\Desktop\SocksOverRDP-x64> netstat -antb | findstr 1080

  TCP    127.0.0.1:1080         0.0.0.0:0              LISTENING

```

Después de comenzar nuestro oyente, podemos transferir el proxificador portátil al objetivo de Windows 10 (en la red 10.129.xx), y configurarlo para reenviar todos nuestros paquetes a 127.0.0.1:1080. El proxificador enrutará el tráfico a través del host y el puerto dados. Consulte el siguiente clip para un tutorial rápido de configuración de proxificador.

### **Configuración del proxificador**

![](https://academy.hackthebox.com/storage/modules/158/configuringproxifier.gif)

Con el proxificador configurado y ejecutado, podemos iniciar MSTSC.exe, y usará el proxificador para pivotar todo nuestro tráfico a través de 127.0.0.1:1080, que lo tunnará a RDP a 172.16.5.19, que luego lo enrutará a 172.16.6.155 utilizando SockSoverRDP-Server.exe.

![](https://academy.hackthebox.com/storage/modules/158/rdpsockspivot.png)

### **Consideraciones de rendimiento de RDP**

Al interactuar con nuestras sesiones de RDP en un compromiso, podemos encontrarnos contendiendo con un rendimiento lento en una sesión determinada, especialmente si estamos administrando múltiples sesiones de RDP simultáneamente. Si este es el caso, podemos acceder al`Experience`pestaña en mstsc.exe y set`Performance`a`Modem`.

![](https://academy.hackthebox.com/storage/modules/158/rdpexpen.png)

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurada para que la conexión a su objetivo funcione sin problemas.