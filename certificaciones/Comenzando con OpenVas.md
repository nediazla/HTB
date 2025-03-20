# Comenzando con OpenVas

[Abierto](https://openvas.org/), por Greenbone Networks, es un escáner de vulnerabilidad disponible públicamente. Greenbone Networks tiene un administrador de vulnerabilidad completo, parte de la cual es el escáner OpenVas. El Gerente de Vulnerabilidad de Greenbone también está abierto al público y de forma gratuita. OpenVas tiene las capacidades de realizar escaneos de red, incluidas pruebas autenticadas y no autenticadas.

![](https://academy.hackthebox.com/storage/modules/108/openvas/Greenbone_Security_Assistant.png)

Comenzaremos con el uso de OpenVas siguiendo las instrucciones de instalación a continuación para la seguridad del loro. La herramienta está preinstalada en el host proporcionado en una sección posterior.

---

# **Paquete de instalación**

Primero, podemos comenzar instalando la herramienta:

Comenzando con OpenVas

```
xnoxos@htb[/htb]$ sudo apt-get update && apt-get -y full-upgradexnoxos@htb[/htb]$ sudo apt-get install gvm && openvas
```

A continuación, para comenzar el proceso de instalación, podemos ejecutar el siguiente comando a continuación:

Comenzando con OpenVas

```
xnoxos@htb[/htb]$ gvm-setup
```

Esto comenzará el proceso de configuración y tomará hasta 30 minutos.

![](https://academy.hackthebox.com/storage/modules/108/openvas/gvmsetup.png)

---

# **Comenzando OpenVas**

Finalmente, podemos comenzar OpenVas:

Comenzando con OpenVas

```
xnoxos@htb[/htb]$ gvm-start
```

![](https://academy.hackthebox.com/storage/modules/108/openvas/gvmstart.png)

**Nota:**La VM proporcionada en el`OpenVAS Skills Assessment`La sección ha sido preinstalada de OpenVas y los objetivos en ejecución. Puede ir a esa sección y comenzar la VM y usar OpenVas en todo el módulo, a la que se puede acceder en`https://< IP >:8080`. Las credenciales de OpenVas son:`htb-student`:`HTB_@cademy_student!`. También puede usar estas credenciales para SSH en la VM de destino para configurar OpenVas.