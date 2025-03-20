# Comenzando con Nessus

Veamos cómo podemos descargar y configurar Nessus para su primer uso para que podamos comenzar a aprender sus diversas características. Siéntase libre de seguir y configurar una instancia de Nessus en su propia VM. Para las partes interactivas de este módulo, proporcionamos una instancia de laboratorio de Nessus y otra con OpenVas instalado.

---

# **Descargando nessus**

Para descargar Nessus, podemos navegar a su

[Página de descarga](https://www.tenable.com/downloads/nessus?loginAttempted=true)

Para descargar el binario Nessus correcto para nuestro sistema. Descargaremos el paquete Debian para

```
Ubuntu
```

para este tutorial.

![](https://academy.hackthebox.com/storage/modules/108/openvas/deb.png)

---

# **Solicitud de licencia gratuita**

A continuación, podemos visitar el

[Página del código de activación](https://www.tenable.com/products/nessus/activation-code)

Para solicitar un código de activación de Nessus, que es necesario para obtener la versión gratuita de Nessus:

![](https://academy.hackthebox.com/storage/modules/108/nessus/register.png)

![](https://academy.hackthebox.com/storage/modules/108/nessus/registrationcode.png)

---

# **Paquete de instalación**

Con el código binario y de activación en la mano, ahora podemos instalar el paquete Nessus:

Comenzando con Nessus

```
xnoxos@htb[/htb]$ dpkg -i Nessus-8.15.1-ubuntu910_amd64.debSelecting previously unselected package nessus.
(Reading database ... 132030 files and directories currently installed.)
Preparing to unpack Nessus-8.15.1-ubuntu910_amd64.deb ...
Unpacking nessus (8.15.1) ...
Setting up nessus (8.15.1) ...
Unpacking Nessus Scanner Core Components...
Created symlink /etc/systemd/system/nessusd.service → /lib/systemd/system/nessusd.service.
Created symlink /etc/systemd/system/multi-user.target.wants/nessusd.service → /lib/systemd/system/nessusd.service.

```

---

# **Comenzando Nessus**

Una vez que tengamos instalado Nessus, podemos iniciar el servicio Nessus:

Comenzando con Nessus

```
xnoxos@htb[/htb]$ sudo systemctl start nessusd.service
```

---

# **Acceder a Nessus**

Para acceder a Nessus, podemos navegar a

```
https://localhost:8834
```

. Una vez que llegamos a la página de configuración, debemos seleccionar

```
Nessus Essentials
```

Para la versión gratuita, y luego podemos ingresar nuestro código de activación:

![](https://academy.hackthebox.com/storage/modules/108/nessus/essentials.png)

Una vez que ingresamos nuestro código de activación, podemos configurar un usuario con un

```
secure
```

Contraseña para nuestra cuenta Nessus. Luego, los complementos comenzarán a compilar una vez que se complete este paso:

![](https://academy.hackthebox.com/storage/modules/108/nessus/init.png)

**Nota:**La VM proporcionada en el`Nessus Skills Assessment`La sección ha preinstalado Nessus y los objetivos en funcionamiento. Puede ir a esa sección y comenzar la VM y usar Nessus en todo el módulo, a la que se puede acceder en`https:// < IP >:8834`. Las credenciales de Nessus son:`htb-student`:`HTB_@cademy_student!`. También puede usar estas credenciales para SSH en la VM de destino para configurar Nessus.

Finalmente, una vez que se completa la configuración, podemos comenzar a crear escaneos, políticas de escaneo, reglas de complemento y configuración de personalización. El`Settings`Page tiene una gran cantidad de opciones, como configurar un servidor proxy o servidor SMTP, opciones de administración de cuentas estándar y configuraciones avanzadas para personalizar la interfaz de usuario, escaneo, registro, rendimiento y opciones de seguridad.

![](https://academy.hackthebox.com/storage/modules/108/nessus/nessus_settings.png)

[Anterior](https://academy.hackthebox.com/module/108/section/1230) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/108/section/1029)