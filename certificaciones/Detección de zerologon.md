# Detección de zerologon

El`Zerologon`La vulnerabilidad, también conocida como CVE-2020-1472, es una falla crítica en la implementación del protocolo remoto de Netlogon, específicamente en el algoritmo criptográfico utilizado por el protocolo. El atacante puede explotar la vulnerabilidad para hacerse pasar por cualquier computadora, incluido el controlador de dominio, y ejecutar llamadas de procedimientos remotos en su nombre. Vamos a sumergirnos en los detalles técnicos de este defecto.

En el corazón de Zerologon está el problema criptográfico en la forma en que el protocolo remoto de Netlogon de Microsoft autentica a los usuarios y máquinas en un dominio de Windows. Cuando un cliente quiere autenticarse contra el controlador de dominio, utiliza un protocolo llamado MS-NRPC, una parte de NetLogon, para establecer un canal seguro.

Durante este proceso, el cliente y el servidor generan una clave de sesión, que se calcula a partir de la contraseña de la cuenta de la máquina. Esta clave se usa para derivar un vector de inicialización (IV) para el modo de cifrado AES-CFB8. En una configuración segura, el IV debe ser único y aleatorio para cada operación de cifrado. Sin embargo, debido a la implementación defectuosa en el protocolo NetLogon, el IV se establece en un valor fijo de todos los ceros.

El atacante puede explotar esta debilidad criptográfica intentando autenticarse contra el controlador de dominio utilizando una clave de sesión que consiste en todos los ceros, evitando efectivamente el proceso de autenticación. Esto permite al atacante establecer un canal seguro con el controlador de dominio sin conocer la contraseña de la cuenta de la máquina.

Una vez que se establece este canal, el atacante puede utilizar la función NetRServerPasswordSet2 para cambiar la contraseña de la cuenta de la computadora a cualquier valor, incluida una contraseña en blanco. Esto efectivamente le da al atacante control total sobre el controlador de dominio y, por extensión, todo el dominio de Active Directory.

La vulnerabilidad de Zerologon es particularmente peligrosa debido a su simplicidad y al nivel de acceso que brinda a los atacantes. La explotación de este defecto requiere solo unos pocos mensajes de Netlogon, y se puede ejecutar en segundos.

### **Cómo se ve Zerologon desde una perspectiva de red**

![](https://academy.hackthebox.com/storage/modules/233/116.png)

**Fuente de la imagen**: [https://www.trendmicro.com/en_us/what-is/zerologon.html](https://www.trendmicro.com/en_us/what-is/zerologon.html)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de zerologon

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/zerologon`
- **Índice Splunk relacionado**: `zerologon`
- **Splunk SourceType relacionado**: `bro:dce_rpc:json`

---

# **Detección de Zerologon con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar Zerologon, usando registros de Splunk y Zeek.

Detección de zerologon

```
index="zerologon" endpoint="netlogon" sourcetype="bro:dce_rpc:json"
| bin _time span=1m
| where operation == "NetrServerReqChallenge" OR operation == "NetrServerAuthenticate3" OR operation == "NetrServerPasswordSet2"
| stats count values(operation) as operation_values dc(operation) as unique_operations by _time, id.orig_h, id.resp_h
| where unique_operations >= 2 AND count>100

```

![](https://academy.hackthebox.com/storage/modules/233/117.png)