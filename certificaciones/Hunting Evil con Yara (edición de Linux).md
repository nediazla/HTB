# Hunting Evil con Yara (edición de Linux)

Enfrentemos la realidad de las operaciones de ciberseguridad: a menudo, como analistas de seguridad, no obtenemos el lujo del acceso directo a un sistema potencialmente comprometido. Imagínese, tenemos banderas sospechosas que saludan desde una máquina remota, pero debido a los límites de la organización, permisos o problemas logísticos, simplemente no podemos poner en nuestras manos. Se siente como saber que hay un incendio potencial pero no poder verlo o tocarlo directamente. Esta situación puede ser estresante, pero es un desafío que hemos aprendido a abordar.

Aquí es donde las cosas se ponen interesantes. Incluso si no podemos acceder a la máquina, en muchos casos, una captura de memoria (o volcado de memoria) del sistema sospechoso se nos puede entregar en el Centro de Operaciones de Seguridad (SOC). Es similar a recibir una instantánea de todo lo que sucede en el sistema en un momento particular. Y solo porque tengamos esta instantánea, no significa que nuestras manos estén atadas.

Afortunadamente, nuestra herramienta de confianza Yara llega al rescate. Podemos ejecutar escaneos basados en Yara directamente en estas imágenes de memoria. Es como tener una visión de rayos X: podemos mirar al estado del sistema, buscando signos de actividad maliciosa o indicadores comprometidos, todo sin tener acceso directo a la máquina misma. Esta capacidad no solo mejora nuestra destreza de investigación, sino que también garantiza que incluso los sistemas remotos e inaccesibles no nos sigan siendo cajas negras. Entonces, si bien la ruta directa puede bloquearse, con herramientas como Yara y nuestra experiencia, siempre encontramos una manera de arrojar luz a las sombras.

# **Cazar al mal dentro de las imágenes de la memoria con Yara**

La incorporación de Yara extiende las capacidades de la memoria forense de memoria, una técnica fundamental en el análisis de malware y la respuesta a incidentes. Nos equipa para atravesar el contenido de la memoria, buscando signos reveladores o indicadores de compromiso.

El escaneo de imagen de memoria de Yara refleja su contraparte basada en disco. Mapeemos el proceso:

- **`Create YARA Rules`**: Desarrolle las reglas de Yara a medida o se apoye en las existentes que se dirigen a rasgos de malware basados en la memoria o comportamientos dudosos.
- **`Compile YARA Rules`**: Compilar las reglas de Yara en un formato binario utilizando el`yarac`Herramienta (compilador Yara). Este paso crea un archivo que contiene las reglas compiladas de Yara con un`.yrc`extensión. Este paso es opcional, ya que podemos usar las reglas normales en formato de texto también. Si bien es posible usar Yara en su formato legible por humanos, compilar las reglas es una mejor práctica al implementar sistemas de detección basados en Yara o trabajar con una gran cantidad de reglas para garantizar un rendimiento y efectividad óptimos. Además, las reglas de compilación proporcionan cierto nivel de protección al convertirlas en formato binario, lo que dificulta que otros vean el contenido de la regla real.
- **`Obtain Memory Image`**: Capture una imagen de memoria utilizando herramientas como[Dumpit](https://www.magnetforensics.com/resources/magnet-dumpit-for-windows/), [Memdump](http://www.nirsoft.net/utils/nircmd.html), [Belkasoft Ram Capturer](https://belkasoft.com/ram-capturer), [Captura de Ram de Magnet](https://www.magnetforensics.com/resources/magnet-ram-capture/), [FTK Imager](https://www.exterro.com/ftk-imager), y[Lime (extractor de memoria de Linux)](https://github.com/504ensicsLabs/LiME).
- **`Memory Image Scanning with YARA`**: Usa el`yara`herramienta y las reglas compiladas de Yara para escanear la imagen de memoria para posibles coincidencias.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, ssh en la IP objetivo utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Por ejemplo, tenemos una instantánea de memoria nombrada`compromised_system.raw`(residiendo en el`/home/htb-student/MemoryDumps`directorio del objetivo de esta sección) que se origina en un sistema bajo el asedio de`WannaCry`Ransomware. Enfrentemos esta imagen con el`wannacry_artifacts_memory.yar`Regla de Yara (que reside en el`/home/htb-student/Rules/yara`directorio del objetivo de esta sección).

Aquí hay un comando de ejemplo para el escaneo de memoria basado en Yara:

Hunting Evil con Yara (edición de Linux)

```
xnoxos@htb[/htb]$ yara /home/htb-student/Rules/yara/wannacry_artifacts_memory.yar /home/htb-student/MemoryDumps/compromised_system.raw --print-stringsRansomware_WannaCry /home/htb-student/MemoryDumps/compromised_system.raw
0x4e140:$wannacry_payload_str1: tasksche.exe0x1cb9b24:$wannacry_payload_str1: tasksche.exe0xdb564d8:$wannacry_payload_str1: tasksche.exe0x13bac36c:$wannacry_payload_str1: tasksche.exe0x16a2ae44:$wannacry_payload_str1: tasksche.exe0x16ce55d8:$wannacry_payload_str1: tasksche.exe0x17bf1fe6:$wannacry_payload_str1: tasksche.exe0x17cb8002:$wannacry_payload_str1: tasksche.exe0x17cb80d0:$wannacry_payload_str1: tasksche.exe0x17cb80f8:$wannacry_payload_str1: tasksche.exe0x18a68f50:$wannacry_payload_str1: tasksche.exe0x18a9b4b8:$wannacry_payload_str1: tasksche.exe0x18dc15a8:$wannacry_payload_str1: tasksche.exe0x18df37d0:$wannacry_payload_str1: tasksche.exe0x19a4b522:$wannacry_payload_str1: tasksche.exe0x1aac0600:$wannacry_payload_str1: tasksche.exe0x1c07ed9a:$wannacry_payload_str1: tasksche.exe0x1c59cd32:$wannacry_payload_str1: tasksche.exe0x1d1593f0:$wannacry_payload_str1: tasksche.exe0x1d1c6fe2:$wannacry_payload_str1: tasksche.exe0x1d92632a:$wannacry_payload_str1: tasksche.exe0x1dd65c34:$wannacry_payload_str1: tasksche.exe0x1e607a1e:$wannacry_payload_str1: tasksche.exe0x1e607dca:$wannacry_payload_str1: tasksche.exe0x13bac3d7:$wannacry_payload_str2: www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com0x197ba5e0:$wannacry_payload_str2: www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com0x1a07cedf:$wannacry_payload_str2: www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com0x1a2cb300:$wannacry_payload_str2: www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com0x1b644cd8:$wannacry_payload_str2: www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com0x1d15945b:$wannacry_payload_str2: www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com0x1dd65c9f:$wannacry_payload_str2: www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com0x450b048:$wannacry_payload_str3: mssecsvc.exe0x5a7f3d4:$wannacry_payload_str3: mssecsvc.exe0xda1c350:$wannacry_payload_str3: mssecsvc.exe0x12481048:$wannacry_payload_str3: mssecsvc.exe0x17027910:$wannacry_payload_str3: mssecsvc.exe0x17f0dc18:$wannacry_payload_str3: mssecsvc.exe0x18c360cc:$wannacry_payload_str3: mssecsvc.exe0x1a2a02f0:$wannacry_payload_str3: mssecsvc.exe0x13945408:$wannacry_payload_str4: diskpart.exe0x19a28480:$wannacry_payload_str4: diskpart.exe
```

Más allá de las herramientas independientes, sumergirse más profundamente en la memoria forense de memoria ofrece una gran cantidad de avenidas. La integración de Yara dentro de los marcos forenses de memoria amplifica su potencial. Con el marco de volatilidad y Yara operando en tándem, se pueden detectar IOC específicos de WannaCry sin problemas.

El[Marco de volatilidad](https://www.volatilityfoundation.org/releases)es una poderosa herramienta forense de memoria de código abierto utilizada para analizar imágenes de memoria de varios sistemas operativos. Yara se puede integrar en el marco de volatilidad como un complemento llamado`yarascan`permitiendo la aplicación de las reglas de Yara para el análisis de memoria.

El marco de volatilidad se cubre en detalle dentro de HTB Academy's`Introduction to Digital Forensics`módulo.

Por ahora, solo discutamos cómo Yara puede usarse como un complemento en el marco de volatilidad.

### **Patrón único que escanean Yara contra una imagen de memoria**

En este caso, especificaremos un patrón de regla Yara directamente en la línea de comandos que se busca dentro de la imagen de memoria por el`yarascan`complemento de volatilidad. La cadena debe estar encerrada en cotizaciones (`"`) después del`-U`opción. Esto es útil cuando tenemos una regla o patrón de Yara específico que queremos aplicar sin crear un archivo de reglas Yara separado.

Del análisis anterior sabemos que el malware WannaCry intenta conectarse al siguiente URI codificado`www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com`

Introducir este patrón dentro de la línea de comando usando`-U "www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com"`provoca una búsqueda dentro del`compromised_system.raw`Imagen de memoria.

Hunting Evil con Yara (edición de Linux)

```
xnoxos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/compromised_system.raw yarascan -U "www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com"Volatility Foundation Volatility Framework 2.6.1
/usr/local/lib/python2.7/dist-packages/volatility/plugins/community/YingLi/ssh_agent_key.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
  from cryptography.hazmat.backends.openssl import backend
Rule: r1
Owner: Process svchost.exe Pid 1576
0x004313d7  77 77 77 2e 69 75 71 65 72 66 73 6f 64 70 39 69   www.iuqerfsodp9i
0x004313e7  66 6a 61 70 6f 73 64 66 6a 68 67 6f 73 75 72 69   fjaposdfjhgosuri
0x004313f7  6a 66 61 65 77 72 77 65 72 67 77 65 61 2e 63 6f   jfaewrwergwea.co
0x00431407  6d 00 00 00 00 00 00 00 00 01 00 00 00 00 00 00   m...............
0x00431417  00 f0 5d 17 00 ff ff ff ff 00 00 00 00 00 00 00   ..].............
0x00431427  00 00 00 00 00 00 00 00 00 20 00 00 00 04 00 00   ................
0x00431437  00 01 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431447  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431457  00 00 00 00 00 50 51 17 00 00 00 00 00 00 00 00   .....PQ.........
0x00431467  00 13 00 00 00 b8 43 03 00 00 00 00 00 00 00 00   ......C.........
0x00431477  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431487  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431497  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x004314a7  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x004314b7  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x004314c7  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
Rule: r1
Owner: Process svchost.exe Pid 1576
0x0013dcd8  77 77 77 2e 69 75 71 65 72 66 73 6f 64 70 39 69   www.iuqerfsodp9i
0x0013dce8  66 6a 61 70 6f 73 64 66 6a 68 67 6f 73 75 72 69   fjaposdfjhgosuri
0x0013dcf8  6a 66 61 65 77 72 77 65 72 67 77 65 61 2e 63 6f   jfaewrwergwea.co
0x0013dd08  6d 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   m...............
---SNIP---

```

Esta opción nos permite especificar directamente una cadena de reglas Yara dentro de la línea de comandos en sí. Veamos cómo podemos buscar el contenido de un archivo de regla de Yara completo (es decir,`.yar`Archivo de regla) en archivos de imagen de memoria.

### **Múltiples escaneo de reglas de Yara contra una imagen de memoria**

Cuando tenemos múltiples reglas de Yara o un conjunto de reglas complejas que queremos aplicar a una imagen de memoria, podemos usar el`-y`Opción seguida de la ruta del archivo de regla en el marco de volatilidad, que nos permite especificar la ruta a un archivo de reglas Yara. El archivo de reglas de yara (`wannacry_artifacts_memory.yar`En nuestro caso) debe contener una o más reglas de Yara en un archivo separado.

El archivo de reglas Yara que utilizaremos para fines de demostración es el siguiente.

Hunting Evil con Yara (edición de Linux)

```
xnoxos@htb[/htb]$ cat /home/htb-student/Rules/yara/wannacry_artifacts_memory.yarrule Ransomware_WannaCry {

    meta:
        author = "Madhukar Raina"
        version = "1.1"
        description = "Simple rule to detect strings from WannaCry ransomware"
        reference = "https://www.virustotal.com/gui/file/ed01ebfbc9eb5bbea545af4d01bf5f1071661840480439c6e5babe8e080e41aa/behavior"

    strings:
$wannacry_payload_str1 = "tasksche.exe" fullword ascii$wannacry_payload_str2 = "www.iuqerfsodp9ifjaposdfjhgosurijfaewrwergwea.com" ascii$wannacry_payload_str3 = "mssecsvc.exe" fullword ascii$wannacry_payload_str4 = "diskpart.exe" fullword ascii$wannacry_payload_str5 = "lhdfrgui.exe" fullword ascii    condition:
        3 of them

```

Ejecutemos la volatilidad con la regla`wannacry_artifacts_memory.yar`(residiendo en el`/home/htb-student/Rules/yara`directorio) para escanear la imagen de memoria`compromised_system.raw`(residiendo en el`/home/htb-student/MemoryDumps`directorio)

Hunting Evil con Yara (edición de Linux)

```
xnoxos@htb[/htb]$ vol.py -f /home/htb-student/MemoryDumps/compromised_system.raw yarascan -y /home/htb-student/Rules/yara/wannacry_artifacts_memory.yarVolatility Foundation Volatility Framework 2.6.1
/usr/local/lib/python2.7/dist-packages/volatility/plugins/community/YingLi/ssh_agent_key.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
  from cryptography.hazmat.backends.openssl import backend
Rule: Ransomware_WannaCry
Owner: Process svchost.exe Pid 1576
0x0043136c  74 61 73 6b 73 63 68 65 2e 65 78 65 00 00 00 00   tasksche.exe....
0x0043137c  52 00 00 00 43 6c 6f 73 65 48 61 6e 64 6c 65 00   R...CloseHandle.
0x0043138c  57 72 69 74 65 46 69 6c 65 00 00 00 43 72 65 61   WriteFile...Crea
0x0043139c  74 65 46 69 6c 65 41 00 43 72 65 61 74 65 50 72   teFileA.CreatePr
0x004313ac  6f 63 65 73 73 41 00 00 6b 00 65 00 72 00 6e 00   ocessA..k.e.r.n.
0x004313bc  65 00 6c 00 33 00 32 00 2e 00 64 00 6c 00 6c 00   e.l.3.2...d.l.l.
0x004313cc  00 00 00 00 68 74 74 70 3a 2f 2f 77 77 77 2e 69   ....http://www.i
0x004313dc  75 71 65 72 66 73 6f 64 70 39 69 66 6a 61 70 6f   uqerfsodp9ifjapo
0x004313ec  73 64 66 6a 68 67 6f 73 75 72 69 6a 66 61 65 77   sdfjhgosurijfaew
0x004313fc  72 77 65 72 67 77 65 61 2e 63 6f 6d 00 00 00 00   rwergwea.com....
0x0043140c  00 00 00 00 01 00 00 00 00 00 00 00 f0 5d 17 00   .............]..
0x0043141c  ff ff ff ff 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0043142c  00 00 00 00 20 00 00 00 04 00 00 00 01 00 00 00   ................
0x0043143c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0043144c  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0043145c  50 51 17 00 00 00 00 00 00 00 00 00 13 00 00 00   PQ..............
Rule: Ransomware_WannaCry
Owner: Process svchost.exe Pid 1576
0x004313d7  77 77 77 2e 69 75 71 65 72 66 73 6f 64 70 39 69   www.iuqerfsodp9i
0x004313e7  66 6a 61 70 6f 73 64 66 6a 68 67 6f 73 75 72 69   fjaposdfjhgosuri
0x004313f7  6a 66 61 65 77 72 77 65 72 67 77 65 61 2e 63 6f   jfaewrwergwea.co
0x00431407  6d 00 00 00 00 00 00 00 00 01 00 00 00 00 00 00   m...............
0x00431417  00 f0 5d 17 00 ff ff ff ff 00 00 00 00 00 00 00   ..].............
0x00431427  00 00 00 00 00 00 00 00 00 20 00 00 00 04 00 00   ................
0x00431437  00 01 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431447  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431457  00 00 00 00 00 50 51 17 00 00 00 00 00 00 00 00   .....PQ.........
0x00431467  00 13 00 00 00 b8 43 03 00 00 00 00 00 00 00 00   ......C.........
0x00431477  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431487  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x00431497  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x004314a7  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x004314b7  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x004314c7  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
Rule: Ransomware_WannaCry
Owner: Process svchost.exe Pid 1576
0x0040e048  6d 73 73 65 63 73 76 63 2e 65 78 65 00 00 00 00   mssecsvc.exe....
0x0040e058  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e068  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e078  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e088  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e098  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e0a8  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e0b8  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e0c8  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e0d8  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e0e8  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e0f8  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e108  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e118  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e128  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
0x0040e138  00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00   ................
Rule: Ransomware_WannaCry
Owner: Process svchost.exe Pid 1576
---SNIP---

```

Podemos ver en los resultados que el`yarascan`El complemento en volatilidad puede encontrar el proceso`svchost.exe`con pid`1576`En la imagen de memoria del sistema comprometido.

En resumen, el`-U`La opción nos permite especificar directamente una cadena de reglas Yara dentro de la línea de comandos, mientras que el`-y`La opción se usa para especificar la ruta a un archivo que contiene una o más reglas de Yara. La elección entre las dos opciones depende de nuestros requisitos específicos y si tenemos una sola regla o un conjunto de reglas para aplicar durante el análisis.