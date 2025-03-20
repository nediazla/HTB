# Capturar archivos a través de http/s

# **Http/s**

La transferencia web es la forma más común en que la mayoría de las personas transfieren archivos porque`HTTP`/`HTTPS`son los protocolos más comunes permitidos a través de los firewalls. Otro beneficio inmenso es que, en muchos casos, el archivo se encriptará en tránsito. No hay nada peor que estar en una prueba de penetración, y las ID de red de un cliente se aceleran en un archivo confidencial que se transfiere a través del texto sin formato y que les pregunte por qué enviamos una contraseña a nuestro servidor en la nube sin usar cifrado.

Ya hemos discutido usando el python3[módulo de servidor de carga](https://github.com/Densaugeo/uploadserver)Para configurar un servidor web con capacidades de carga, pero también podemos usar Apache o Nginx. Esta sección cubrirá la creación de un servidor web seguro para operaciones de carga de archivos.

---

# **Nginx - habilitando PUT**

Una buena alternativa para transferir archivos a`Apache`es[Nginx](https://www.nginx.com/resources/wiki/)porque la configuración es menos complicada, y el sistema de módulos no conduce a problemas de seguridad como`Apache`poder.

Al permitir`HTTP`Cargas, es fundamental ser 100% positivo que los usuarios no puedan cargar shells web y ejecutarlos.`Apache`hace que sea fácil dispararnos en el pie con esto, como el`PHP`A el módulo le encanta ejecutar cualquier cosa que termine en`PHP`. Configuración`Nginx`Usar PHP no es tan simple.

### **Cree un directorio para manejar archivos cargados**

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```

### **Cambiar al propietario a data www**

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```

### **Crear archivo de configuración de Nginx**

Cree el archivo de configuración de Nginx creando el archivo`/etc/nginx/sites-available/upload.conf`Con el contenido:

Capturar archivos a través de http/s

```
server {
    listen 9001;

    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}

```

### **Symlink nuestro sitio al directorio habilitado para sitios**

Catching Files over HTTP/S

```
xnoxos@htb[/htb]$ sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```

### **Iniciar nginx**

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ sudo systemctl restart nginx.service
```

Si recibimos algún mensaje de error, verifique`/var/log/nginx/error.log`. Si usa Pwnbox, veremos que el puerto 80 ya está en uso.

### **Verificación de errores**

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ tail -2 /var/log/nginx/error.log2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()
```

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ ss -lnpt | grep 80LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=`2811`,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))

```

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ ps -ef | grep 2811user65      2811    1856  0 16:05 ?        00:00:04 `python -m websockify 80 localhost:5901 -D`
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811

```

Vemos que ya hay un módulo que escucha en el puerto 80. Para evitar esto, podemos eliminar la configuración NGINX predeterminada, que se une en el puerto 80.

### **Eliminar la configuración de NginxDefault**

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```

Ahora podemos probar la carga usando`cURL`Para enviar un`PUT`pedido. En el siguiente ejemplo, subiremos el`/etc/passwd`archivar al servidor y llamarlo usuarios.txt

### **Cargar archivo usando curl**

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
```

Capturar archivos a través de http/s

```
xnoxos@htb[/htb]$ sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt user65:x:1000:1000:,,,:/home/user65:/bin/bash

```

Una vez que tenemos este trabajo, una buena prueba es asegurarse de que la lista de directorio no esté habilitada navegando a`http://localhost/SecretUploadDirectory`. Por defecto, con`Apache`, si presionamos un directorio sin un archivo de índice (index.html), enumerará todos los archivos. Esto es malo para nuestro caso de uso de archivos exfillantes porque la mayoría de los archivos son sensibles por naturaleza, y queremos hacer todo lo posible para ocultarlos. Gracias a`Nginx`Siendo mínimo, características como esa no están habilitadas de forma predeterminada.

---

# **Uso de herramientas incorporadas**

En la siguiente sección, presentaremos el tema de "vivir fuera de la tierra" o usar las utilidades incorporadas de Windows y Linux para realizar actividades de transferencia de archivos. Repitalmente volveremos a este concepto a lo largo de los módulos en la ruta del probador de penetración al cubrir tareas como Windows y la escalada de privilegios de Linux y la enumeración y explotación de Active Directory.

[Anterior](https://academy.hackthebox.com/module/24/section/684) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/24/section/1575)

Hoja de trucos