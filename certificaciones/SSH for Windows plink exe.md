# SSH for Windows: plink.exe

[Emplomar](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), abreviatura de Pastty Link, es una herramienta SSH de línea de comandos de Windows que viene como parte del paquete de masilla cuando se instala. Similar a SSH, Plink también se puede usar para crear proxies de costados y calcetines dinámicos. Antes de la caída de[2018](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview)Windows no tenía un cliente SSH nativo incluido, por lo que los usuarios tendrían que instalar su propio. La herramienta de elección para muchos sysadmin que necesitaban conectarse a otros hosts fue[Masilla](https://www.putty.org/).

Imagine que estamos en un Pentest y accedemos a una máquina de Windows. Enumeramos rápidamente el host y su postura de seguridad y determinamos que está moderadamente bloqueado. Necesitamos usar este host como un punto de pivote, pero es poco probable que podamos sacar nuestras propias herramientas al host sin estar expuestos. En cambio, podemos vivir de la tierra y usar lo que ya está allí. Si el host es más antiguo y la masilla está presente (o podemos encontrar una copia en un archivo compartido), Plink puede ser nuestro camino hacia la victoria. Podemos usarlo para crear nuestro pivote y potencialmente evitar la detección un poco más.

Ese es solo un escenario potencial en el que Plink podría ser beneficioso. También podríamos usar Plink si usamos un sistema de Windows como nuestro host de ataque principal en lugar de un sistema basado en Linux.

---

# **Conociendo a Plink**

En la imagen a continuación, tenemos un host de ataque basado en Windows.

![](https://academy.hackthebox.com/storage/modules/158/66.png)

El host del ataque de Windows inicia un proceso plink.exe con los argumentos de línea de comandos a continuación para iniciar un puerto dinámico hacia adelante sobre el servidor Ubuntu. Esto inicia una sesión SSH entre el host de ataque de Windows y el servidor Ubuntu, y luego Plink comienza a escuchar en el puerto 9050.

### **Usando plink.exe**

SSH para Windows: plink.exe

```
plink -ssh -D 9050 ubuntu@10.129.15.50

```

Otra herramienta basada en Windows llamada[Proxificador](https://www.proxifier.com/)Se puede usar para iniciar un túnel de calcetines a través de la sesión SSH que creamos. Proxifier es una herramienta de Windows que crea una red tunelada para aplicaciones de clientes de escritorio y le permite operar a través de un proxy de calcetines o HTTPS y permite encadenar proxy. Es posible crear un perfil donde podamos proporcionar la configuración para nuestro servidor de calcetines iniciado por PLINK en el puerto 9050.

![](https://academy.hackthebox.com/storage/modules/158/reverse_shell_9.png)

Después de configurar el servidor de calcetines para`127.0.0.1`y puerto 9050, podemos comenzar directamente`mstsc.exe`Para iniciar una sesión RDP con un objetivo de Windows que permite las conexiones RDP.

Nota: Podemos intentar esta técnica en cualquier sección interactiva de este módulo desde un host de ataque personal basado en Windows. Una vez que haya completado este módulo de un host de ataque basado en Linux, no dude en intentar volver a través de él desde un host de ataque personal basado en Windows. Además, al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurada para que la conexión a su objetivo funcione sin problemas.