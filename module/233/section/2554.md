# Detección de ataques de fuerza bruta RDP

A menudo nos encontramos`Remote Desktop Protocol (RDP) brute force attacks`Como un vector favorito para que los atacantes obtengan un punto de apoyo inicial en una red. El concepto de un ataque de fuerza bruta RDP es relativamente sencillo: los atacantes intentan iniciar sesión en una sesión de escritorio remota adivinando e intentando sistemáticamente diferentes contraseñas hasta que encuentren la correcta. Este método explota el hecho de que muchos usuarios a menudo tienen contraseñas débiles o predeterminadas que son fáciles de adivinar.

### **Cómo se ve el tráfico RDP**

![](https://academy.hackthebox.com/storage/modules/233/100.png)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de ataques de fuerza bruta RDP

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/rdp_bruteforce`
- **Índice Splunk relacionado**: `rdp_bruteforce`
- **Splunk SourceType relacionado**: `bro:rdp:json`

---

# **Detección de ataques de fuerza bruta RDP con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar los ataques de fuerza bruta RDP, usando registros de Splunk y Zeek.

Detección de ataques de fuerza bruta RDP

```
index="rdp_bruteforce" sourcetype="bro:rdp:json"
| bin _time span=5m
| stats count values(cookie) by _time, id.orig_h, id.resp_h
| where count>30

```

![](https://academy.hackthebox.com/storage/modules/233/101.png)