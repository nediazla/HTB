# Técnicas y herramientas de adquisición de evidencia

`Evidence acquisition`es una fase crítica en forense digital, que involucra la recopilación de artefactos digitales y datos de diversas fuentes para preservar la evidencia potencial de análisis. Este proceso requiere herramientas y técnicas especializadas para garantizar la integridad, la autenticidad y la admisibilidad de la evidencia recopilada. Aquí hay una descripción general de las técnicas de adquisición de evidencia comúnmente utilizadas en forense digital:

- Imagen forense
- Extracción de evidencia basada en huéspedes y triaje rápido
- Extracción de evidencia de red

# **Imagen forense**

Forensic Imaging es un proceso fundamental en forense digital que implica crear una copia exacta y bit a bit de medios de almacenamiento digital, como discos duros, unidades de estado sólido, unidades USB y tarjetas de memoria. Este proceso es crucial para preservar el estado original de los datos, garantizar la integridad de los datos y mantener la admisibilidad de la evidencia en los procedimientos legales. Las imágenes forenses juegan un papel fundamental en las investigaciones al permitir a los analistas examinar la evidencia sin alterar o comprometer los datos originales.

A continuación se presentan algunas herramientas y soluciones de imágenes forenses:

- [FTK Imager](https://www.exterro.com/ftk-imager): Desarrollado por AccessData (ahora adquirido por Exterro), FTK Imager es una de las herramientas de imagen de disco más utilizadas en el campo de ciberseguridad. Nos permite crear copias (o imágenes) perfectas de discos informáticos para el análisis, preservando la integridad de la evidencia. También nos permite ver y analizar el contenido de los dispositivos de almacenamiento de datos sin alterar los datos.
- [Aff4 Imager](https://github.com/Velocidex/c-aff4): Una herramienta gratuita de código abierto elaborada para crear y duplicar imágenes de disco forense. Es fácil de usar y compatible con numerosos sistemas de archivos. Un beneficio de la imagen AFF4 es su capacidad de extraer archivos en función de su tiempo de creación, volúmenes de segmento y reducir el tiempo tomado para la imagen a través de la compresión.
- `DD and DCFLDD`: Ambos son utilidades de línea de comandos disponibles en sistemas basados en UNIX (incluidos Linux y MacOS). DD es una herramienta versátil incluida en la mayoría de los sistemas basados en UNIX de forma predeterminada, mientras que DCFLDD es una versión mejorada de DD con características específicamente útiles para forenses, como el hash.
- `Virtualization Tools`: Dado el uso prevalente de la virtualización en los sistemas modernos, los respondedores de incidentes a menudo necesitarán recopilar evidencia de entornos virtuales. Dependiendo de la solución de virtualización específica, se puede recopilar evidencia deteniendo temporalmente el sistema y transfiriendo el directorio que lo alberga. Otro método es utilizar la capacidad de instantánea presente en numerosas herramientas de software de virtualización.

---

**Ejemplo 1: Imágenes forenses con FTK Imager**

Veamos ahora una demostración de utilizar`FTK Imager`para elaborar una imagen de disco. Tenga en cuenta que requerirá un medio de almacenamiento auxiliar, como un disco duro externo o una unidad flash USB, para guardar la imagen de disco resultante.

- Seleccionar`File` -> `Create Disk Image`.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image11.png)
    
- A continuación, seleccione la fuente de medios. Típicamente, es`Physical Drive`o`Logical Drive`.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image1.png)
    
- Elija la unidad desde la que desea crear una imagen.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image3.png)
    
- Especifique el destino para la imagen.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image7.png)
    
- Seleccione el tipo de imagen deseado.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image4.png)
    
- Detalles de la evidencia de entrada.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image10.png)
    
- Elija la carpeta de destino y el nombre de archivo para la imagen. En este paso, también puede ajustar la configuración para la fragmentación de imágenes y la compresión.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image8.png)
    
- Una vez que se confirman todas las configuraciones, haga clic`Start`.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image2.png)
    
- Observarás el progreso de las imágenes.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image12.png)
    
- Si optó por verificar la imagen, también verá el progreso de la verificación.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image6.png)
    
- Después de que se haya verificado la imagen, recibirá un resumen de imágenes. Ahora, estás preparado para analizar este volcado.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image9.png)
    

---

**Ejemplo 2: Montaje de una imagen de disco con la imagen de la imagen del arsenal**

Veamos ahora otra demostración de utilizar[Arsenal Imagen Mounter](https://arsenalrecon.com/downloads)Para montar una imagen de disco, hemos creado anteriormente (no la mencionada anteriormente) a partir de una máquina virtual comprometida (VM) que se ejecuta en VMware. El disco duro virtual de la VM se ha almacenado como`HTBVM01-000003.vmdk`.

Después de instalar Arsenal Image Mounter, asegurémonos de lanzarlo con`administrative rights`. En la ventana principal de Arsenal Image Mounter, hagamos clic en el`Mount disk image`botón. A partir de ahí, navegaremos a la ubicación de nuestro`.VMDK`archivo y seleccionarlo.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mount.png)

Arsenal Image Mounter comenzará su análisis del archivo VMDK. También tendremos la opción de decidir si queremos montar el disco como`read-only`o`read-write`basado en nuestros requisitos específicos.

*Elegir una imagen de disco como de solo lectura es un paso fundamental en los forenses digitales y la respuesta de incidentes. Este enfoque es vital para preservar el estado original de la evidencia, asegurando que su autenticidad e integridad permanezcan intactas.*

Una vez montada, la imagen aparecerá como una unidad, asignada la letra`D:\`.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_drive.png)

---

# **Extracción de evidencia basada en huéspedes y triaje rápido**

### **Evidencia basada en el huésped**

Los sistemas operativos modernos, con Microsoft Windows como un excelente ejemplo, generan una gran cantidad de artefactos de evidencia. Estos pueden surgir de la ejecución de la aplicación, las modificaciones de archivos o incluso la creación de cuentas de usuario. Cada una de estas acciones deja un sendero, proporcionando ideas invaluables para los analistas de respuesta a incidentes.

La evidencia en un sistema host varía en su naturaleza. El término`volatility`se refiere a la persistencia de los datos en un sistema de host, con datos volátiles de información que desaparece después de eventos como lognos o apagados de energía. Un tipo crucial de evidencia volátil es la memoria activa del sistema. Durante las investigaciones, especialmente aquellas relacionadas con las infecciones de malware, la memoria del sistema en vivo se vuelve indispensable. El malware a menudo deja rastros dentro de la memoria del sistema, y perder esta evidencia puede obstaculizar la investigación de un analista. Para capturar la memoria, herramientas como[FTK Imager](https://www.exterro.com/ftk-imager)se emplean comúnmente.

Algunas otras soluciones de adquisición de memoria son:

- [Winpmem](https://github.com/Velocidex/WinPmem): WinPMem ha sido el controlador de adquisición de memoria de código abierto predeterminado para Windows durante mucho tiempo. Solía vivir en el proyecto Rekall, pero recientemente se ha separado en su propio repositorio.
- [Dumpit](https://www.magnetforensics.com/resources/magnet-dumpit-for-windows/): Una utilidad simplista que genera un volcado de memoria física de Windows y Linux Machines. En Windows, concatena la memoria física del sistema de 32 y 64 bits en un solo archivo de salida, lo que lo hace extremadamente fácil de usar.
- [Memdump](http://www.nirsoft.net/utils/nircmd.html): Memdump es una utilidad de línea de comandos gratuita y directa que nos permite capturar el contenido de la RAM de un sistema. Es bastante beneficioso en las investigaciones forenses o al analizar un sistema para actividades maliciosas. Su simplicidad y facilidad de uso lo convierten en una opción popular para la adquisición de memoria.
- [Belkasoft Ram Capturer](https://belkasoft.com/ram-capturer): Esta es otra herramienta poderosa que podemos usar para la adquisición de memoria, proporcionada de forma gratuita por Belkasoft. Puede capturar la RAM de una computadora con Windows en ejecución, incluso si hay una protección activa anti-debuges o antidumping. Esto lo convierte en una herramienta altamente efectiva para extraer tantos datos como sea posible durante una investigación forense en vivo.
- [Captura de Ram de Magnet](https://www.magnetforensics.com/resources/magnet-ram-capture/): Desarrollado por Magnet Forensics, esta herramienta proporciona una forma gratuita y simple de capturar la memoria volátil de un sistema.
- [Lime (extractor de memoria de Linux)](https://github.com/504ensicsLabs/LiME): La cal es un módulo de núcleo cargable (LKM) que permite la adquisición de la memoria volátil. La cal es única en el sentido de que está diseñado para ser transparente al sistema objetivo, evadiendo muchas medidas anti-forenses comunes.

---

**Ejemplo 1: adquirir memoria con WinPMem**

Veamos ahora una demostración de utilizar`WinPmem`para la adquisición de memoria.

Para generar un volcado de memoria, simplemente ejecute el comando a continuación con los privilegios de administrador.

Técnicas y herramientas de adquisición de evidencia

```
C:\Users\X\Downloads> winpmem_mini_x64_rc2.exe memdump.raw

```

![](https://academy.hackthebox.com/storage/modules/237/image13.png)

---

**Ejemplo 2: Adquisición de la memoria VM**

Estos son los pasos para adquirir memoria de una máquina virtual (VM).

1. Abra las opciones de VM en ejecución
2. Suspender la VM en ejecución
3. Localizar el`.vmem`Archivo dentro del directorio de la VM.

![](https://academy.hackthebox.com/storage/modules/237/suspend-vm.png)

![](https://academy.hackthebox.com/storage/modules/237/suspend-vmem.png)

---

Por otro lado, los datos no volátiles permanecen en el disco duro, que generalmente persisten a través de las paradas. Esta categoría incluye artefactos como:

- Registro
- Registro de eventos de Windows
- Artifactos relacionados con el sistema (por ejemplo, pre-Fetch, Amcache)
- Artifactos específicos de la aplicación (por ejemplo, registros de IIS, historial del navegador)

### **Triaje rápido**

Este enfoque enfatiza la recopilación de datos de sistemas potencialmente comprometidos. El objetivo es centralizar los datos de alto valor, simplificando su indexación y análisis. Al centralizar estos datos, los analistas pueden implementar más efectivamente herramientas y técnicas, perfeccionando los sistemas con el valor más probatorio. Este enfoque dirigido permite una inmersión más profunda en los forenses digitales, ofreciendo una imagen más clara de las acciones del adversario.

Una de las mejores, si no las mejores y rápidas soluciones de análisis de artefactos y extracción es[Kape (Kroll Artifact Parser y Extractor)](https://www.kroll.com/en/services/cyber-risk/incident-response-litigation-support/kroll-artifact-parser-extractor-kape). Veamos cómo podemos emplear`KAPE`Para recuperar datos forenses valiosos de la imagen, montamos previamente con la ayuda de la imagen de la imagen del arsenal (`D:\`).

`KAPE`es una herramienta poderosa en el ámbito de los forenses digitales y la respuesta de incidentes. Diseñado para ayudar a expertos e investigadores forenses, Kape facilita la recolección y el análisis de la evidencia digital de los sistemas basados en Windows. Desarrollado y mantenido por`Kroll`(anteriormente conocido como`Magnet Forensics`), Kape se celebra por sus características integrales de colección, adaptabilidad e interfaz intuitiva. El siguiente diagrama ilustra el flujo operativo de Kape.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape.png)

Referencia de imagen:[https://ericzimmerman.github.io/kapedocs/# )index.md](https://ericzimmerman.github.io/KapeDocs/#!index.md)

Kape opera según los principios de`Targets`y`Modules`. Estos elementos guían la herramienta para procesar datos y extraer artefactos forenses. Cuando alimentamos una fuente a KAPE, duplica archivos específicos relacionados con el forense a un directorio de salida designado, todo mientras se mantiene los metadatos de cada archivo.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_working.png)

Después de descargar, descomprima el archivo y inicie KAPE. Dentro del directorio KAPE, notaremos dos archivos ejecutables:`gkape.exe`y`kape.exe`. Kape proporciona a los usuarios dos modos: CLI (`kape.exe`) y GUI (`gkape.exe`).

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_modes.png)

Optemos por la versión GUI para explorar las opciones disponibles más visualmente.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_window.png)

El quid del proceso radica en seleccionar las configuraciones de destino apropiadas.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_target.png)

En la terminología de Kape,`Targets`Consulte los artefactos específicos que nuestro objetivo es extraer de una imagen o sistema. Estos se duplican al directorio de salida.

Los archivos de destino de Kape tienen un`.tkape`extensión y residir en el`<path to kape>\KAPE\Targets`directorio. Por ejemplo, el objetivo`RegistryHivesSystem.tkape`En la captura de pantalla a continuación especifica las ubicaciones y las máscaras de archivos asociadas con las colmenas de registro relacionadas con el sistema. En esta configuración de destino,`RegistryHivesSystem.tkape`Contiene información para recopilar los archivos con la máscara de archivos`SAM.LOG*`desde`C:\Windows\System32\config`directorio.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_tkape.png)

Kape también ofrece`Compound Targets`, que son esencialmente amalgamaciones de múltiples objetivos. Esta característica acelera el proceso de recopilación recopilando múltiples archivos definidos en varios objetivos en una sola ejecución. El`Compound`directorio`KapeTriage`El archivo proporciona una descripción general del contenido de este objetivo compuesto.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_compound.png)

Especifiquemos nuestra fuente (en nuestro escenario, es`D:\`) y designe una ubicación para almacenar los datos cosechados. También podemos determinar una carpeta de salida para albergar los datos procesados de KAPE.

Después de configurar nuestras opciones, presionemos el`Execute`botón para iniciar la recopilación de datos.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_gui.png)

Tras la ejecución, Kape comenzará la colección, almacenando los resultados en el destino predeterminado.

Técnicas y herramientas de adquisición de evidencia

```
KAPE version 1.3.0.2, Author: Eric Zimmerman, Contact: https://www.kroll.com/kape (kape@kroll.com)

KAPE directory: C:\htb\dfir_module\data\kape\KAPE
Command line:   --tsource D: --tdest C:\htb\dfir_module\data\investigation\image --target !SANS_Triage --gui

System info: Machine name: REDACTED, 64-bit: True, User: REDACTED OS: Windows10 (10.0.22621)

Using Target operations
Found 18 targets. Expanding targets to file list...
Target ApplicationEvents with Id 2da16dbf-ea47-448e-a00f-fc442c3109ba already processed. Skipping!
Target ApplicationEvents with Id 2da16dbf-ea47-448e-a00f-fc442c3109ba already processed. Skipping!
Target ApplicationEvents with Id 2da16dbf-ea47-448e-a00f-fc442c3109ba already processed. Skipping!
Target ApplicationEvents with Id 2da16dbf-ea47-448e-a00f-fc442c3109ba already processed. Skipping!
Target ApplicationEvents with Id 2da16dbf-ea47-448e-a00f-fc442c3109ba already processed. Skipping!
Found 639 files in 4.032 seconds. Beginning copy...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDefenderApiLogger.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDefenderAuditLogger.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDiagLog.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDiagtrack-Listener.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTEventLog-Application.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTEventlog-Security.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTEventLog-System.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTSgrmEtwSession.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTUBPM.etl due to UnauthorizedAccessException...
  Deferring D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTWFP-IPsec Diagnostics.etl due to UnauthorizedAccessException...
  Deferring D:\$MFT due to UnauthorizedAccessException...  Deferring D:\$LogFile due to UnauthorizedAccessException...  Deferring D:\$Extend\$UsnJrnl:$J due to NotSupportedException...  Deferring D:\$Extend\$UsnJrnl:$Max due to NotSupportedException...  Deferring D:\$Secure:$SDS due to NotSupportedException...  Deferring D:\$Boot due to UnauthorizedAccessException...  Deferring D:\$Extend\$RmMetadata\$TxfLog\$Tops:$T due to NotSupportedException...Deferred file count: 17. Copying locked files...
  Copied deferred file D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDefenderApiLogger.etl to C:\htb\dfir_module\data\investigation\image\D\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDefenderApiLogger.etl. Hashing source file...
  Copied deferred file D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDiagLog.etl to C:\htb\dfir_module\data\investigation\image\D\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDiagLog.etl. Hashing source file...
  Copied deferred file D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDiagtrack-Listener.etl to C:\htb\dfir_module\data\investigation\image\D\Windows\System32\LogFiles\WMI\RtBackup\EtwRTDiagtrack-Listener.etl. Hashing source file...
  Copied deferred file D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTEventLog-Application.etl to C:\htb\dfir_module\data\investigation\image\D\Windows\System32\LogFiles\WMI\RtBackup\EtwRTEventLog-Application.etl. Hashing source file...
  Copied deferred file D:\Windows\System32\LogFiles\WMI\RtBackup\EtwRTEventLog-System.etl to C:\htb\dfir_module\data\investigation\image\D\Windows\System32\LogFiles\WMI\RtBackup\EtwRTEventLog-System.etl. Hashing source file...
  Copied deferred file D:\$MFT to C:\htb\dfir_module\data\investigation\image\D\$MFT. Hashing source file...  Copied deferred file D:\$LogFile to C:\htb\dfir_module\data\investigation\image\D\$LogFile. Hashing source file...  Copied deferred file D:\$Extend\$UsnJrnl:$J to C:\htb\dfir_module\data\investigation\image\D\$Extend\$J. Hashing source file...  Copied deferred file D:\$Extend\$UsnJrnl:$Max to C:\htb\dfir_module\data\investigation\image\D\$Extend\$Max. Hashing source file...  Copied deferred file D:\$Secure:$SDS to C:\htb\dfir_module\data\investigation\image\D\$Secure_$SDS. Hashing source file...  Copied deferred file D:\$Boot to C:\htb\dfir_module\data\investigation\image\D\$Boot. Hashing source file...
```

El directorio de salida de Kape alberga los frutos de la colección y procesamiento de artefactos. El contenido exacto de este directorio puede diferir según los artefactos seleccionados y el conjunto de configuraciones. En nuestra manifestación, optamos por el`!SANS_Triage`Configuración del objetivo de recolección. Vamos a navegar al directorio de salida de Kape para inspeccionar los datos cosechados.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_output.png)

De los resultados mostrados, es evidente que el`$MFT`Se ha recopilado el archivo, junto con el`Users`y`Windows`directorios.

Vale la pena señalar que Kape también ha cosechado el`Windows event logs`, que están ubicados dentro de los subfolders del directorio de Windows.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_kape_winevt.png)

---

¿Qué pasaría si quisiéramos realizar una colección de artefactos de forma remota y en masa? Aquí es donde las soluciones EDR y[Velociraptor](https://github.com/Velocidex/velociraptor)entrar en juego.

Las plataformas de detección y respuesta de punto final (EDR) ofrecen una ventaja significativa para los analistas de respuesta a incidentes. Habilitan la adquisición y el análisis remotos de la evidencia digital. Por ejemplo, las plataformas EDR pueden mostrar binarios ejecutados recientemente o archivos recientemente agregados. En lugar de examinar los sistemas individuales, los analistas pueden buscar dichos indicadores en toda la red. Otro beneficio es la capacidad de recopilar evidencia, ya sea archivos específicos o paquetes forenses integrales. Esta funcionalidad acelera la recopilación de evidencia y facilita la búsqueda y la recolección a gran escala.

[Velociraptor](https://github.com/Velocidex/velociraptor)es una herramienta potente para recopilar información basada en el host utilizando consultas de lenguaje de consultas de Velociraptor (VQL). Más allá de esto, Velociraptor puede ejecutar`Hunts`para acumular varios artefactos. Un artefacto utilizado con frecuencia es el`Windows.KapeFiles.Targets`. Mientras que Kape (Kroll Artifact Parser y Extractor) en sí no es de código abierto, es accesible su lógica de recolección de archivos, codificada en YAML, a través del[Proyecto Kapefiles](https://github.com/EricZimmerman/KapeFiles). Este enfoque es un elemento básico en el triaje rápido.

Para utilizar Velociraptor para los artefactos de Kapefiles:

- Iniciar una nueva caza.
    
    ![](https://academy.hackthebox.com/storage/modules/237/vel0.png)
    
    ![](https://academy.hackthebox.com/storage/modules/237/image33.png)
    
- Elegir`Windows.KapeFiles.Targets`Como los artefactos para la colección.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image64.png)
    
- Especifique la colección para usar.
    
    ![](https://academy.hackthebox.com/storage/modules/237/vel1.png)
    
    ![](https://academy.hackthebox.com/storage/modules/237/image19.png)
    
- Hacer clic en`Launch`Para comenzar la caza.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image27.png)
    
- Una vez completado, descargue los resultados.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image90.png)
    

Extraer el archivo revelará archivos relacionados con los artefactos recopilados y todos los archivos reunidos.

![](https://academy.hackthebox.com/storage/modules/237/image12_.png)

![](https://academy.hackthebox.com/storage/modules/237/image37.png)

Para la colección de volcado de memoria remota usando Velociraptor:

- Comience una nueva caza, pero esta vez, seleccione el`Windows.Memory.Acquisition`artefacto.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image92.png)
    
- Después de que concluye la caza, descargue el archivo resultante. En dentro, encontrará un archivo llamado`PhysicalMemory.raw`, que contiene el volcado de memoria.
    
    ![](https://academy.hackthebox.com/storage/modules/237/image45.png)
    

# **Extracción de evidencia de red**

A lo largo de nuestra exploración de los módulos en el`SOC Analyst`Path, hemos profundizado ampliamente en el ámbito de la evidencia de la red, un aspecto fundamental para cualquier analista de SOC.

- Primero, nuestro`Intro to Network Traffic Analysis`y`Intermediate Network Traffic Analysis`Módulos cubiertos`traffic capture analysis`. Piense en la captura del tráfico como una instantánea de todas las conversaciones digitales que ocurren en nuestra red. Herramientas como`Wireshark`o`tcpdump`Permítanos capturar y diseccionar estos paquetes, dándonos una vista granular de los datos en tránsito.
- Entonces, nuestro`Working with IDS/IPS`y`Detecting Windows Attacks with Splunk`Los módulos cubrieron el uso de datos derivados de IDS/IPS.`Intrusion Detection Systems (IDS)`son nuestros centinelas vigilantes, monitoreando constantemente el tráfico de la red para obtener signos de actividad maliciosa. Cuando detectan algo mal, nos alertan. Por otro lado,`Intrusion Prevention Systems (IPS)`Da un paso más allá. No solo detectan, sino que también toman acciones predefinidas para bloquear o prevenir esas actividades maliciosas.
- `Traffic flow`datos, a menudo obtenidos de herramientas como`NetFlow`o`sFlow`, nos proporciona una visión más amplia del comportamiento de nuestra red. Si bien es posible que no nos dé los detalles de cada paquete, ofrece una descripción general de alto nivel de los patrones de tráfico.
- Por último, nuestro confiable`firewalls`. Estas no son solo barreras que bloquean o permiten el tráfico basado en reglas predefinidas. Los firewalls modernos son bestias inteligentes. Pueden identificar aplicaciones, usuarios e incluso detectar y bloquear las amenazas. Al analizar los registros de firewall, podemos descubrir intentos de explotar vulnerabilidades, intentos de acceso no autorizados y otras actividades maliciosas.