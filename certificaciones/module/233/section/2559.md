# Detección de querberoasting

En 2016, surgieron una serie de publicaciones y artículos de blog discutiendo la táctica de consultar el nombre del servicio de consulta (SPN) cuentas y sus boletos correspondientes, un ataque que se conoció como`Kerberoasting`. Al poseer solo una cuenta de usuario legítima y su contraseña, un atacante podría recuperar los boletos SPN e intentar romperlos fuera de línea.

Después de examinar numerosos recursos en kerberoasting, es evidente que`RC4`se utiliza para el cifrado de boletos detrás de escena. Explotaremos este respaldo como punto de detección en esta sección.

**Fuente de evidencia**: [https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/t1208-kerberoasting](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/t1208-kerberoasting)

### **Cómo se ve el tráfico de querberoasting**

![](https://academy.hackthebox.com/storage/modules/233/109.png)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, acceda a la interfaz Splunk en https: // [Target IP]: 8000 y inicie la aplicación Splunk de búsqueda e informes. La gran mayoría de las búsquedas cubiertas desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Además, podemos acceder al objetivo generado a través de RDP como se describe a continuación. Todos los archivos, registros y archivos PCAP relacionados con los ataques cubiertos se pueden encontrar en los directorios/Home/Htb-Student y/Home/Htb-Student/Module_Files.

Detección de querberoasting

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

### **Evidencia relacionada**

- **Directorio**: `/home/htb-student/module_files/sharphound`
- **Índice Splunk relacionado**: `sharphound`
- **Splunk SourceType relacionado**: `bro:kerberos:json`

---

# **Detección de kerberoasting con registros de Splunk & Zeek**

Ahora exploremos cómo podemos identificar la kerberoasting, usando registros de Splunk y Zeek.

Detección de querberoasting

```
index="sharphound" sourcetype="bro:kerberos:json"
request_type=TGS cipher="rc4-hmac"
forwardable="true" renewable="true"
| table _time, id.orig_h, id.resp_h, request_type, cipher, forwardable, renewable, client, service

```

![](https://academy.hackthebox.com/storage/modules/233/110_new.png)