# Detectando el psexec de Cobalt Strike

Cobalt Strike's`psexec`El comando es una implementación de la popular herramienta PSEXEC, que forma parte de la suite Sysinternals de Microsoft. Es un reemplazo de Telnet liviano que le permite ejecutar procesos en otros sistemas. La versión de Cobalt Strike se utiliza para ejecutar cargas útiles en sistemas remotos, como parte del proceso posterior a la explotación.

Cuando el`psexec`El comando se invoca dentro de la huelga de cobalto, ocurren los siguientes pasos:

- `Service Creation`: La herramienta primero crea un nuevo servicio en el sistema de destino. Este servicio es responsable de ejecutar la carga útil deseada. El servicio generalmente se crea con un nombre aleatorio para evitar una detección fácil.
- `File Transfer`: Cobalt Strike luego transfiere la carga útil al sistema objetivo, a menudo al`ADMIN$`compartir. Esto generalmente se hace utilizando el protocolo SMB.
- `Service Execution`: Luego se inicia el servicio recién creado, que a su vez ejecuta la carga útil. Esta carga útil puede ser un shellcode, un ejecutable o cualquier otro tipo de archivo que se pueda ejecutar.
- `Service Removal`: Después de ejecutar la carga útil, el servicio se detiene y se elimina del sistema de destino para minimizar las trazas de la intrusión.
- `Communication`: Si la carga útil es un faro u otro tipo de puerta trasera, generalmente establecerá la comunicación al servidor del equipo de Cobalt Strike, lo que permite que se envíen y ejecuten más comandos y ejecuten en el sistema comprometido.

Cobalt Strike's`psexec`Funciona sobre el puerto 445 (SMB), y requiere privilegios de administrador local en el sistema de destino. Por lo tanto, a menudo se usa después de que se ha logrado el acceso inicial y se han intensificado los privilegios.

### **How Cobalt Strike PSExec Traffic Looks Like**

![](https://academy.hackthebox.com/storage/modules/233/113.png)

**Fuente de la imagen**: [https://thedfirreport.com/2021/08/29/cobalt-strike-a-defenders-guide/](https://thedfirreport.com/2021/08/29/cobalt-strike-a-defenders-guide/)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detectando el psexec de Cobalt Strike

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/cobalt_strike_psexec`
- **Índice Splunk relacionado**: `cobalt_strike_psexec`
- **Splunk SourceType relacionado**: `bro:smb_files:json`

---

# **Detección de Psexec de Cobalt Strike con Splunk & Zeek Logs**

Ahora exploremos cómo podemos identificar el PSEXEC de Cobalt Strike, usando registros de Splunk y Zeek.

Detectando el psexec de Cobalt Strike

```
index="cobalt_strike_psexec"
sourcetype="bro:smb_files:json"
action="SMB::FILE_OPEN"
name IN ("*.exe", "*.dll", "*.bat")
path IN ("*\\c$", "*\\ADMIN$")
size>0

```

![](https://academy.hackthebox.com/storage/modules/233/114.png)