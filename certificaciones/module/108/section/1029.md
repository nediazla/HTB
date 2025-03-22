# Escaneo de nessus

Se puede configurar un nuevo escaneo de Nessus haciendo clic en`New Scan`y seleccionar un tipo de escaneo. Las plantillas de escaneo se dividen en tres categorías:`Discovery`, `Vulnerabilities`, y`Compliance`.

**Nota:**Los escaneos que se muestran en esta sección ya han sido previamente recorridos para ahorrarle el tiempo de esperar que terminen. Si vuelve a ejecutar el escaneo, es mejor pasar por vulnerabilidades a medida que vengan, en lugar de esperar a que termine el escaneo, ya que pueden tardar 1-2 horas en terminar.

---

# **Nuevo escaneo**

Aquí tenemos opciones para un básico`Host Discovery`escanear para identificar hosts en vivo/puertos abiertos o una variedad de tipos de escaneo como el`Basic Network Scan`, `Advanced Scan`, `Malware Scan`, `Web Application Tests`, así como escaneos dirigidos a los estándares específicos de CVE y auditoría y cumplimiento. Se puede encontrar una descripción de cada tipo de escaneo[aquí](https://docs.tenable.com/nessus/Content/ScanAndPolicyTemplates.htm).

![](https://academy.hackthebox.com/storage/modules/108/nessus/nessus_scan_types.png)

Para los fines de este ejercicio, elegiremos el

```
Basic Network Scan
```

opción, y podemos ingresar a nuestros objetivos:

![](https://academy.hackthebox.com/storage/modules/108/nessus/general.png)

**Nota:**Para este módulo, el objetivo de Windows será`172.16.16.100`y el objetivo de Linux será`172.16.16.160`.

---

# **Descubrimiento**

En el

```
Discovery
```

sección, debajo

```
Host Discovery
```

, se nos presenta la opción de habilitar escaneo para dispositivos frágiles. Los dispositivos de escaneo como las impresoras de red a menudo dan como resultado que impriman resmas de papel con texto de basura, dejando los dispositivos inutilizables. Podemos dejar esta configuración deshabilitada:

![](https://academy.hackthebox.com/storage/modules/108/nessus/options.png)

En

```
Port Scanning
```

, podemos elegir si escanear puertos comunes, todos los puertos o un rango autodefinido, dependiendo de nuestros requisitos:

![](https://academy.hackthebox.com/storage/modules/108/nessus/discovery.png)

Dentro del`Service Discovery`subsección, la`Probe all ports to find services`La opción se selecciona de forma predeterminada. Es posible que una aplicación o servicio mal diseñado se bloquee como resultado de este sondeo, pero la mayoría de las aplicaciones deben ser lo suficientemente robustas como para manejar esto. La búsqueda de servicios SSL/TLS también está habilitado de forma predeterminada en un escaneo personalizado, y Nessus también puede recibir instrucciones de identificar certificados expirados y revocados.

---

# **Evaluación**

Bajo el

```
Assessment
```

Categoría, el escaneo de aplicaciones web también se puede habilitar si es necesario, y se puede especificar un agente de usuario personalizado y varias otras opciones de escaneo de aplicaciones web (por ejemplo, una URL para pruebas de inclusión de archivos remotos (RFI)):

![](https://academy.hackthebox.com/storage/modules/108/nessus/webapp.png)

Si lo desea, Nessus puede intentar autenticarse contra aplicaciones y servicios descubiertos utilizando credenciales proporcionadas (si se ejecuta un escaneo credencial), o de lo contrario puede realizar un ataque de fuerza bruta con el nombre de usuario y las listas de contraseñas proporcionadas:

![](https://academy.hackthebox.com/storage/modules/108/nessus/hydra.png)

La enumeración del usuario también se puede realizar utilizando diversas técnicas, como el forzamiento de Rid Brute:

![](https://academy.hackthebox.com/storage/modules/108/nessus/userenum.png)

Si optamos por realizar el forzamiento Brute Brute, podemos establecer los UID de inicio y finalización para cuentas de usuarios de dominio y de dominio:

![](https://academy.hackthebox.com/storage/modules/108/nessus/ridbf.png)

---

# **Avanzado**

En el

```
Advanced
```

pestaña, los controles seguros están habilitados de forma predeterminada. Esto evita que Nessus ejecute cheques que pueden afectar negativamente el dispositivo o red de destino. También podemos optar por retrasar o acelerar el escaneo si Nessus detecta cualquier congestión de la red, dejar de intentar escanear cualquier hosts que no responda e incluso elija que Nessus escanee nuestra lista de IP de destino en orden aleatorio:

![](https://academy.hackthebox.com/storage/modules/108/nessus/advanced.png)

[Anterior](https://academy.hackthebox.com/module/108/section/1231) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/108/section/1232)