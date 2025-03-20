# Performance

El rendimiento de escaneo juega un papel importante cuando necesitamos escanear una red extensa o estamos tratando con un bajo ancho de banda de red. Podemos usar varias opciones para decir`Nmap`que tan rápido (`-T <0-5>`), con qué frecuencia (`--min-parallelism <number>`), qué tiempo de espera (`--max-rtt-timeout <time>`) Los paquetes de prueba deben tener, cuántos paquetes deben enviarse simultáneamente (`--min-rate <number>`), y con el número de reintentos (`--max-retries <number>`) Para los puertos escaneados, los objetivos deben escanear.

---

# **Tiempos de espera**

Cuando NMAP envía un paquete, lleva algo de tiempo (`Round-Trip-Time` - `RTT`) para recibir una respuesta del puerto escaneado. Generalmente,`Nmap`comienza con un alto tiempo de espera (`--min-RTT-timeout`) de 100 ms. Veamos un ejemplo escaneando toda la red con 256 hosts, incluidos los 100 mejores puertos.

### **Escaneo predeterminado**

Actuación

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 39.44 seconds

```

### **RTT optimizado**

Performance

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F --initial-rtt-timeout 50ms --max-rtt-timeout 100ms<SNIP>
Nmap done: 256 IP addresses (8 hosts up) scanned in 12.29 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.0/24` | Escanea la red objetivo especificada. |
| `-F` | Escanea los 100 puertos principales. |
| `--initial-rtt-timeout 50ms` | Establece el valor de tiempo especificado como tiempo de espera inicial de RTT. |
| `--max-rtt-timeout 100ms` | Establece el valor de tiempo especificado como tiempo de espera máximo de RTT. |

Al comparar los dos escaneos, podemos ver que encontramos dos hosts menos con el escaneo optimizado, pero el escaneo tomó solo una cuarta parte del tiempo. A partir de esto, podemos concluir que establecer el tiempo de espera inicial de RTT (`--initial-rtt-timeout`) a un período de tiempo demasiado corto puede hacernos pasar por alto hosts.

---

# **Reintentos máximos**

Otra forma de aumentar la velocidad de escaneo es especificar la tasa de reintento de los paquetes enviados (`--max-retries`). El valor predeterminado es`10`, pero podemos reducirlo a`0`. Esto significa que si NMAP no recibe una respuesta para un puerto, no enviará más paquetes a ese puerto y lo omitirá.

### **Escaneo predeterminado**

Actuación

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F | grep "/tcp" | wc -l23

```

### **Reintentos reducidos**

Actuación

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F --max-retries 0 | grep "/tcp" | wc -l21

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.0/24` | Escanea la red objetivo especificada. |
| `-F` | Escanea los 100 puertos principales. |
| `--max-retries 0` | Establece el número de reintentos que se realizarán durante el escaneo. |

Nuevamente, reconocemos que acelerar también puede tener un efecto negativo en nuestros resultados, lo que significa que podemos pasar por alto información importante.

---

# **Tarifa**

Durante una prueba de penetración de caja blanca, podemos obtener la lista blanca para que los sistemas de seguridad verifiquen los sistemas en la red en busca de vulnerabilidades y no solo prueben las medidas de protección. Si conocemos el ancho de banda de la red, podemos trabajar con la tasa de paquetes enviados, lo que acelera significativamente nuestros escaneos con`Nmap`. Al establecer la tasa mínima (`--min-rate <number>`) para enviar paquetes, le decimos`Nmap`Para enviar simultáneamente el número especificado de paquetes. Intentará mantener la tasa en consecuencia.

### **Escaneo predeterminado**

Actuación

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.default<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 29.83 seconds

```

### **Escaneo optimizado**

Actuación

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.minrate300 --min-rate 300<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 8.67 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.0/24` | Escanea la red objetivo especificada. |
| `-F` | Escanea los 100 puertos principales. |
| `-oN tnet.minrate300` | Guarda los resultados en formatos normales, iniciando el nombre de archivo especificado. |
| `--min-rate 300` | Establece el número mínimo de paquetes que se enviarán por segundo. |

---

### **Escaneo predeterminado: puertos abiertos encontrados**

Actuación

```
xnoxos@htb[/htb]$ cat tnet.default | grep "/tcp" | wc -l23

```

### **Escaneo optimizado: puertos abiertos encontrados**

Actuación

```
xnoxos@htb[/htb]$ cat tnet.minrate300 | grep "/tcp" | wc -l23

```

---

# **Momento**

Porque dicha configuración no siempre se puede optimizar manualmente, como en una prueba de penetración de caja negra,`Nmap`ofrece seis plantillas de tiempo diferentes (`-T <0-5>`) para que lo usemos. Estos valores (`0-5`) Determine la agresividad de nuestros escaneos. Esto también puede tener efectos negativos si el escaneo es demasiado agresivo, y los sistemas de seguridad pueden bloquearnos debido al tráfico de red producido. La plantilla de tiempo predeterminada utilizada cuando no hemos definido nada más es lo normal (`-T 3`).

- `T 0` / `T paranoid`
- `T 1` / `T sneaky`
- `T 2` / `T polite`
- `T 3` / `T normal`
- `T 4` / `T aggressive`
- `T 5` / `T insane`

Estas plantillas contienen opciones que también podemos establecer manualmente, y ya hemos visto algunas de ellas. Los desarrolladores determinaron los valores establecidos para estas plantillas de acuerdo con sus mejores resultados, lo que nos facilita adaptar nuestros escaneos al entorno de red correspondiente. Las opciones de uso exacta con sus valores que podemos encontrar aquí:[https://nmap.org/book/performance-timing-templates.html](https://nmap.org/book/performance-timing-templates.html)

### **Escaneo predeterminado**

Actuación

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.default <SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 32.44 seconds

```

### **Escaneo**

Actuación

```
xnoxos@htb[/htb]$ sudo nmap 10.129.2.0/24 -F -oN tnet.T5 -T 5<SNIP>
Nmap done: 256 IP addresses (10 hosts up) scanned in 18.07 seconds

```

| **Opciones de escaneo** | **Descripción** |
| --- | --- |
| `10.129.2.0/24` | Escanea la red objetivo especificada. |
| `-F` | Escanea los 100 puertos principales. |
| `-oN tnet.T5` | Guarda los resultados en formatos normales, iniciando el nombre de archivo especificado. |
| `-T 5` | Especifica la plantilla de sincronización loca. |

---

### **Escaneo predeterminado: puertos abiertos encontrados**

Actuación

```
xnoxos@htb[/htb]$ cat tnet.default | grep "/tcp" | wc -l23

```

### **Escaneo loco: puertos abiertos encontrados**

Actuación

```
xnoxos@htb[/htb]$ cat tnet.T5 | grep "/tcp" | wc -l23

```

---

Más información sobre el rendimiento del escaneo que podemos encontrar en[https://nmap.org/book/man-performance.html](https://nmap.org/book/man-performance.html)