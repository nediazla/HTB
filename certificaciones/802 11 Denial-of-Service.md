# 802.11 Denial-of-Service

**Archivo PCAP relacionado**:

- `deauthandbadauth.cap`

---

En el dominio del análisis de tráfico, es invariablemente crítico analizar todos los aspectos de los protocolos y comunicaciones de la capa de enlace. Un tipo prominente de ataque de capa de enlace es el que se dirige a`802.11 (Wi-Fi)`. Tal vector de ataque a menudo es fácil para nosotros ignorar, pero dado que los errores humanos pueden conducir al fracaso de nuestra seguridad perimetral, es esencial que auditemos continuamente nuestras redes inalámbricas.

# **Capturando el tráfico 802.11**

Para examinar nuestro tráfico crudo 802.11, requeriríamos un`WIDS`/`WIPS`sistema o una interfaz inalámbrica equipada con el modo monitor. Similar al modo promiscuo en Wireshark, el modo de monitor nos permite ver tramas RAW 802.11 y otros tipos de paquetes que de otro modo podrían permanecer invisibles.

Supongamos que poseemos una interfaz Wi-Fi capaz de monitor. Podríamos enumerar nuestras interfaces inalámbricas en Linux usando el siguiente comando:

### **Interfaces inalámbricas**

```
[!bash!]$ iwconfigwlan0     IEEE 802.11  ESSID:off/any
          Mode:Managed  Access Point: Not-Associated   Tx-Power=20 dBm
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off

```

Tenemos un par de opciones para configurar nuestra interfaz en modo monitor. En primer lugar, empleando`airodump-ng`, podemos usar el comando resultante:

### **Airmón-ng**

```
[!bash!]$ sudo airmon-ng start wlan0Found 2 processes that could cause trouble.
Kill them using 'airmon-ng check kill' before putting
the card in monitor mode, they will interfere by changing channels
and sometimes putting the interface back in managed mode

    PID Name
    820 NetworkManager
   1389 wpa_supplicant

PHY     Interface       Driver          Chipset

phy0    wlan0           rt2800usb       Ralink Technology, Corp. RT2870/RT3070
                (mac80211 monitor mode vif enabled for [phy0]wlan0 on [phy0]wlan0mon)
                (mac80211 station mode vif disabled for [phy0]wlan0)

```

En segundo lugar, utilizando utilidades del sistema, necesitaríamos desactivar nuestra interfaz, modificar su modo y luego reactivarla.

### **Modo de monitor**

```
[!bash!]$ sudo ifconfig wlan0 down[!bash!]$ sudo iwconfig wlan0 mode monitor[!bash!]$ sudo ifconfig wlan0 up
```

Podríamos verificar si nuestra interfaz está en`monitor mode`usando el`iwconfig`utilidad.

```
[!bash!]$ iwconfigwlan0mon  IEEE 802.11  Mode:Monitor  Frequency:2.457 GHz  Tx-Power=20 dBm
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off

```

Es posible que nuestra interfaz no se ajuste al`wlan0mon`convención. En cambio, podría llevar un nombre como el siguiente.

```
[!bash!]$ iwconfigwlan0     IEEE 802.11  Mode:Monitor  Frequency:2.457 GHz  Tx-Power=20 dBm
          Retry short  long limit:2   RTS thr:off   Fragment thr:off
          Power Management:off

```

El factor crucial aquí es que el modo debe ser "monitor". El nombre de la interfaz no es particularmente importante, y en muchos casos, nuestra distribución de Linux podría asignarle un nombre completamente diferente.

Para comenzar a capturar el tráfico de nuestros clientes y redes, podemos emplear`airodump-ng`. Necesitamos especificar el canal de nuestro AP con`-c`, es bssid con`--bssid`, y el nombre del archivo de salida con`-w`.

```
[!bash!]$ sudo airodump-ng -c 4 --bssid F8:14:FE:4D:E6:F1 wlan0 -w rawBSSID              PWR RXQ  Beacons#Data, #/s  CH   MB   ENC CIPHER  AUTH ESSIDF8:14:FE:4D:E6:F1  -23  64      115        6    0   4  130   WPA2 CCMP   PSK  HTB-Wireless

```

Podemos usar`tcpdump`Para lograr resultados similares, pero Airodump-NG resulta igualmente efectivo.

---

# **Cómo funcionan los ataques de desautenticación**

Entre los ataques más frecuentes que podríamos presenciar o detectar se encuentra un ataque de desautenticación/disociación. Este es un ataque precursor de la capa de enlace común que los adversarios podrían emplear por varias razones:

1. `To capture the WPA handshake to perform an offline dictionary attack`
2. `To cause general denial of service conditions`
3. `To enforce users to disconnect from our network, and potentially join their network to retrieve information`

En esencia, el atacante fabricará un marco de desautenticación 802.11 que finge que se origina en nuestro punto de acceso legítimo. Al hacerlo, el atacante podría desconectar a uno de nuestros clientes de la red. A menudo, el cliente se volverá a conectar y pasará por el proceso de apretón de manos mientras el atacante está olfateando.

![](https://academy.hackthebox.com/storage/modules/229/deauth-attack.png)

Este ataque opera por el atacante falsificando o alterando la Mac del remitente del marco. El dispositivo cliente realmente no puede discernir la diferencia sin controles adicionales como IEEE 802.11w (protección del cuadro de administración). Cada solicitud de desautenticación está asociada con un código de razón que explica por qué el cliente está siendo desconectado.

En la mayoría de los escenarios, herramientas básicas como`aireplay-ng`y`mdk4`Emplear la razón`code 7`para la desautenticación.

# **Encontrar ataques de desautenticación**

Para detectar estos posibles ataques, podemos abrir el archivo de captura de tráfico relacionado (`deauthandbadauth.cap`) como se muestra a continuación.

### **Wireshark**

```
[!bash!]$ sudo wireshark deauthandbadauth.cap
```

Si quisiéramos limitar nuestro punto de vista al tráfico desde el BSSID de nuestro AP (`MAC`), podríamos usar el siguiente filtro Wireshark:

- `wlan.bssid == xx:xx:xx:xx:xx:xx`

![](https://academy.hackthebox.com/storage/modules/229/1-deauth.png)

Supongamos que queríamos echar un vistazo a los marcos de desautenticación de nuestro BSSID o un atacante que finge enviarlos desde nuestro BSSID, podríamos usar el siguiente filtro de Wireshark:

- `(wlan.bssid == xx:xx:xx:xx:xx:xx) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12)`

Con este filtro, especificamos el tipo de marco (`management`) con`00`y el subtipo (`deauthentication`) con`12`.

![](https://academy.hackthebox.com/storage/modules/229/2-deauth.png)

Podríamos notar de inmediato que se envió una cantidad excesiva de marcos de desautenticación a uno de nuestros dispositivos de clientes. Este sería un indicador inmediato de este ataque. Además, si abramos los parámetros fijos en gestión inalámbrica, podríamos notar esa razón`code 7`fue utilizado.

![](https://academy.hackthebox.com/storage/modules/229/3-deauth.png)

Como se mencionó anteriormente, si quisiéramos verificar que esto fue hecho por un atacante, deberíamos poder filtrar aún más solo para solicitudes de desautenticación con razón`code 7`. Como se mencionó,`aireplay-ng`y`mdk4`, que son herramientas de ataque comunes, utilice este código de razón por defecto. Podríamos hacer con el siguiente filtro Wireshark.

- `(wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12) and (wlan.fixed.reason_code == 7)`

![](https://academy.hackthebox.com/storage/modules/229/4-deauth.png)

---

# **Códigos de razón giratoria**

Alternativamente, un actor más sofisticado podría intentar evadir este signo innatamente obvio mediante los códigos de razón giratorios. El principio de esto es que un atacante podría tratar de evadir cualquier alarma que pueda activar con un sistema inalámbrico de detección de intrusos cambiando el código de razón de vez en cuando.

El truco para esta técnica de detección es incrementarse como lo haría un guión de atacantes. Primero comenzaríamos con el Código de Razón 1.

- `(wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12) and (wlan.fixed.reason_code == 1)`

![](https://academy.hackthebox.com/storage/modules/229/5-deauth.png)

Entonces nos trasladamos al Código 2 de la razón.

- `(wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12) and (wlan.fixed.reason_code == 2)`

![](https://academy.hackthebox.com/storage/modules/229/6-deauth.png)

Continuaríamos esta secuencia.

- `(wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 12) and (wlan.fixed.reason_code == 3)`

![](https://academy.hackthebox.com/storage/modules/229/7-deauth.png)

Como tal, la desautenticación puede ser un dolor, pero tenemos algunas medidas de compensación que podemos implementar para evitar que esto ocurra en la actualidad y la edad. Estos son:

1. `Enable IEEE 802.11w (Management Frame Protection) if possible`
2. `Utilize WPA3-SAE`
3. `Modify our WIDS/WIPS detection rules`

### **Encontrar intentos de autenticación fallidos**

Supongamos que un atacante debía intentar conectarse a nuestra red inalámbrica. Podríamos notar una cantidad excesiva de solicitudes de asociación provenientes de un dispositivo. Para filtrar para estos podríamos usar lo siguiente.

- `(wlan.bssid == F8:14:FE:4D:E6:F1) and (wlan.fc.type == 00) and (wlan.fc.type_subtype == 0) or (wlan.fc.type_subtype == 1) or (wlan.fc.type_subtype == 11)`

![](https://academy.hackthebox.com/storage/modules/229/1-fakeauth.png)

Como tal, es importante que podamos distinguir entre el tráfico legítimo 802.11 y el tráfico de atacantes. La seguridad de la capa de enlace en esta perspectiva puede significar la diferencia entre el compromiso de Perimiter y nuestra seguridad.