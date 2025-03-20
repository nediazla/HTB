# Detección de paso elevado

# **Pasajero elevado**

Los adversarios pueden utilizar el`Overpass-the-Hash`Técnica para obtener Kerberos TGT aprovechando los hash de contraseña robada para moverse lateralmente dentro de un entorno o para evitar los controles de acceso típicos del sistema. Paso elevado (también conocido como`Pass-the-Key`) permite que la autenticación ocurra a través de Kerberos en lugar de NTLM. Tanto los hashes NTLM como las claves AES pueden servir como base para solicitar un Kerberos TGT.

### **Pasos de ataque:**

- El atacante emplea herramientas como Mimikatz para extraer el hash NTLM de un usuario que actualmente inició sesión en el sistema comprometido. El atacante debe tener al menos privilegios de administrador local en el sistema para poder extraer el hash del usuario.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image65.png)
    
- El atacante utiliza una herramienta como Rubeus para crear una solicitud RAW AS-REQ para que un usuario especificado solicite un boleto TGT. Este paso no requiere privilegios elevados en el anfitrión para solicitar el TGT, lo que lo convierte en un enfoque más sigiloso que el ataque de pases de pases de Mimikatz.
    
    ![](https://academy.hackthebox.com/storage/modules/233/image3.png)
    
- Análogo a la técnica de pase-the-ticket, el atacante envía el boleto solicitado para la sesión de inicio de sesión actual.

### **Oportunidades de detección de paso elevado**

`Mimikatz`El ataque de paso de paso de paso por alto deja los mismos artefactos que el ataque de pases-the -hash, y se puede detectar utilizando las mismas estrategias.

`Rubeus`, sin embargo, presenta un escenario algo diferente. A menos que el TGT solicitado se use en otro host del host, los mecanismos de detección de boletos de paso pueden no ser efectivos, ya que Rubeus envía una solicitud AS-REQ directamente al controlador de dominio (DC), generando, generando`Event ID 4768 (Kerberos TGT Request)`. Sin embargo, la comunicación con el DC (`TCP/UDP port 88`) De un proceso inusual puede servir como un indicador de un posible ataque de paso elevado.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en http: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

# **Detección de paso elevado con Splunk (apuntando a Rubeo)**

Ahora exploremos cómo podemos identificar el paso elevado, usando Splunk.

**Periodo de tiempo**: `earliest=1690443407 latest=1690443544`

Detección de paso elevado

```
index=main earliest=1690443407 latest=1690443544 source="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" (EventCode=3 dest_port=88 Image!=*lsass.exe) OR EventCode=1
| eventstats values(process) as process by process_id
| where EventCode=3
| stats count by _time, Computer, dest_ip, dest_port, Image, process
| fields - count

```

![](https://academy.hackthebox.com/storage/modules/233/16.png)