# Rogue Access Point & Evil-Twin Attacks

**Archivo PCAP relacionado**:

- `rogueap.cap`

---

Abordar los puntos de acceso deshonesto y los ataques entre malas gemelos pueden parecer una tarea gigantesca debido a su naturaleza a menudo difícil de alcanzar. Sin embargo, con las estrategias apropiadas establecidas, estos puntos de acceso ilegítimos se pueden detectar y gestionar de manera efectiva. En el ámbito de los puntos de acceso malévolos, los ataques rebeldes y de gemelo malvado surgen invariablemente como preocupaciones significativas.

![](https://academy.hackthebox.com/storage/modules/229/rogueap.png)

Un punto de acceso deshonesto sirve principalmente como una herramienta para eludir los controles perimetrales en su lugar. Un adversario podría instalar dicho punto de acceso a los controles de red Sidestep y las barreras de segmentación, lo que podría, en muchos casos, tomar la forma de puntos de acceso o conexiones atadas. Incluso se sabe que estos puntos deshonrosos se infiltran en redes de aire obtenido. Su función principal es proporcionar acceso no autorizado a secciones restringidas de una red. El punto crítico para recordar aquí es que los puntos de acceso Rogue están directamente conectados a la red.

---

# **Girar mal**

Un atacante gira un maldad, por otro lado para un atacante para muchos otros propósitos diferentes. La clave aquí es que en la mayoría de los casos estos puntos de acceso no están conectados a nuestra red. En cambio, son puntos de acceso independientes, que pueden tener un servidor web o algo más para actuar como un hombre en el medio para clientes inalámbricos.

![](https://academy.hackthebox.com/storage/modules/229/evil-twin.png)

Los atacantes pueden configurarlos para cosechar contraseñas inalámbricas o de dominio, entre otras piezas de información. Comúnmente, estos ataques también pueden abarcar un ataque de portal hostil.

# **Detección Airodump-Ng**

De inmediato, podríamos utilizar el filtro ESSID para Airodump-Ng para detectar puntos de acceso de estilo malvado.

Punto de acceso pícaro y ataques de giro malvado

```bash
xnoxos@htb[/htb]$ sudo airodump-ng -c 4 --essid HTB-Wireless wlan0 -w raw CH  4 ][ Elapsed: 1 min ][ 2023-07-13 16:06
 BSSID              PWR RXQ  Beacons#Data, #/s  CH   MB   ENC CIPHER  AUTH ESSID F8:14:FE:4D:E6:F2   -7 100      470      155    0   4   54   OPN              HTB-Wireless
 F8:14:FE:4D:E6:F1   -5  96      682        0    0   4  324   WPA2 CCMP   PSK  HTB-Wireless

```

El ejemplo anterior mostraría que, de hecho, un atacante podría haber subido un punto de acceso abierto que tiene un ESSID idéntico como punto de acceso. Un atacante podría hacer esto para organizar lo que comúnmente se conoce como un ataque de portal hostil. Los atacantes utilizan un ataque de portal hostil para extraer credenciales de usuarios, entre otras acciones nefastas.

También podríamos estar atentos a los intentos de desautenticación, lo que podría sugerir medidas de aplicación del atacante que opera el punto de acceso que gira mal.

Para determinar de manera concluyente si se trata de una anomalía o un error Airodump-NG, podemos comenzar nuestros esfuerzos de análisis de tráfico (`rogueap.cap`). Para filtrar para marcos de baliza, podríamos usar lo siguiente.

- `(wlan.fc.type == 00) and (wlan.fc.type_subtype == 8)`

![](https://academy.hackthebox.com/storage/modules/229/1-evil-twin.png)

El análisis de baliza es crucial para diferenciar entre puntos de acceso genuinos y fraudulentos. Uno de los lugares iniciales para comenzar es el`Robust Security Network (RSN)`información. Estos datos comunican información valiosa a los clientes sobre los cifrados compatibles, entre otras cosas.

Supongamos que deseamos examinar la información RSN de nuestro punto de acceso legítimo.

![](https://academy.hackthebox.com/storage/modules/229/2-evil-twin.png)

Indicaría que WPA2 es compatible con AES y TKIP con PSK como su mecanismo de autenticación. Sin embargo, cuando cambiamos a la información RSN del punto de acceso ilegítimo, podemos encontrar que falta notablemente.

![](https://academy.hackthebox.com/storage/modules/229/3-evil-twin.png)

En la mayoría de los casos, un ataque de maldad estándar exhibirá esta característica. Sin embargo, siempre debemos investigar campos adicionales para las discrepancias, particularmente cuando se trata de ataques malvados más sofisticados. Por ejemplo, un atacante podría emplear el mismo cifrado que usa nuestro punto de acceso, lo que hace que la detección de este ataque sea más desafiante.

En tales circunstancias, podríamos explorar otros aspectos del marco de la baliza, como la información específica del proveedor, que probablemente esté ausente desde el punto de acceso del atacante.

# **Encontrar un usuario caído**

A pesar de la capacitación integral de conciencia de seguridad, algunos usuarios pueden ser víctimas de ataques como estos. Afortunadamente, en el caso de los ataques de maldad de ojo de estilo de red abierta, podemos ver la mayoría del tráfico de nivel superior en un formato sin cifrar. Para filtrar exclusivamente para el punto de acceso de giro malvado, emplearíamos el siguiente filtro.

- `(wlan.bssid == F8:14:FE:4D:E6:F2)`

![](https://academy.hackthebox.com/storage/modules/229/4-evil-twin.png)

Si detectamos solicitudes ARP que emanan desde un dispositivo cliente conectado a la red sospechosa, identificaríamos esto como un posible indicador de compromiso. En tales casos, debemos registrar detalles pertinentes sobre el dispositivo cliente para promover nuestros esfuerzos de respuesta a incidentes.

1. `Its MAC address`
2. `Its host name`

En consecuencia, podríamos instigar restos de contraseña y otras medidas reactivas para evitar una mayor infracción de nuestro entorno.

# **Encontrar puntos de acceso deshonesto**

Por otro lado, la detección de puntos de acceso deshonesto a menudo puede ser una tarea simple de verificar nuestras listas de dispositivos de red. En el caso de los puntos de acceso deshonesto basados ​​en puntos de acceso (como los puntos de acceso de Windows), podríamos analizar las redes inalámbricas en nuestras inmediaciones. Si encontramos una red inalámbrica irreconocible con una señal fuerte, particularmente si carece de cifrado, esto podría indicar que un usuario ha establecido un punto de acceso deshonesto para navegar alrededor de nuestros controles perimetrales.