# Dynamic Analysis

Cuando se trata del dominio del análisis de malware, el análisis dinámico o conductual representa un enfoque indispensable en nuestro arsenal de investigación. En el análisis dinámico, observamos e interpretamos el comportamiento del malware mientras se ejecuta, o en acción. Este es un contraste crítico con el análisis estático, donde diseccionamos las propiedades y el contenido del malware sin ejecutarlo. El objetivo principal del análisis dinámico es documentar y comprender el impacto del mundo real del malware en su entorno de host, por lo que es una parte integral del análisis integral de malware.

Al ejecutar el análisis dinámico, encapsulamos el malware dentro de un entorno estrechamente controlado, monitoreado y generalmente aislado para evitar cualquier propagación o daño involuntario. Este entorno es típicamente una máquina virtual (VM) a la que el malware es ajeno. Cree que está interactuando con un sistema genuino, mientras que nosotros, como investigadores, tenemos un control total sobre sus interacciones y podemos documentar su comportamiento a fondo.

Nuestro procedimiento de análisis dinámico se puede dividir en los siguientes pasos:

- `Environment Setup`: Primero establecemos un entorno seguro y controlado, típicamente un`VM`, aislado del resto de la red para evitar la contaminación o propagación inadvertida del malware. La configuración de VM debe imitar un sistema del mundo real, completo con software, aplicaciones y configuraciones de red que un usuario real podría tener.
- `Baseline Capture`: Una vez configurado el entorno, capturamos una instantánea del estado limpio del sistema. Esto incluye`system files`, `registry states`, `running processes`, `network configuration`, y más. Esta línea de base sirve como punto de referencia para identificar los cambios realizados por el malware después de la ejecución.
- `Tool Deployment (Pre-Execution)`: Para capturar las actividades del malware de manera efectiva, implementamos varias herramientas de monitoreo y registro. Herramientas como`Process Monitor (Procmon)`Desde Sysinternals Suite se utilizan para registrar llamadas al sistema, actividad del sistema de archivos, operaciones de registro, etc. También podemos emplear servicios públicos como`Wireshark`, `tcpdump`, y`Fiddler`para capturar el tráfico de red y`Regshot`tomar instantáneas antes y después del registro del sistema. Finalmente, herramientas como`INetSim`, `FakeDNS`, y`FakeNet-NG`se utilizan para simular los servicios de Internet.
- `Malware Execution`: Con nuestras herramientas en ejecución y lista, procedemos a ejecutar la muestra de malware en el entorno aislado. Durante la ejecución, las herramientas de monitoreo capturan y registran todas las actividades, incluida la creación de procesos, las modificaciones de archivos y registros, tráfico de red, etc.
- `Observation and Logging`: La muestra de malware puede ejecutarse durante una duración suficiente. Todo el tiempo, nuestras herramientas de monitoreo registran diligentemente cada uno de sus movimientos, lo que nos proporcionará una visión integral de su comportamiento y modus operandi.
- `Analysis of Collected Data`: Después de que el malware ha ejecutado su curso, detenemos su ejecución y detenemos las herramientas de monitoreo. Ahora examinamos los registros y datos recopilados, comparando el estado del sistema con nuestra línea de base inicial para identificar los cambios introducidos por el malware.

En algunos casos, cuando el malware es particularmente evasivo o complejo, podríamos emplear entornos de arena para el análisis dinámico. Cajas de arena, como`Cuckoo Sandbox`, `Joe Sandbox`, o`FireEye's Dynamic Threat Intelligence cloud`, proporcione un entorno automatizado, seguro y altamente controlado para la ejecución de malware. Vienen equipados con numerosas características para el análisis de comportamiento en profundidad y generan informes detallados sobre el comportamiento de la red del malware, la interacción del sistema de archivos, la huella de la memoria y más.

Sin embargo, es importante recordar que si bien los entornos de sandbox son herramientas valiosas, no son infalibles. Algunos malware avanzado pueden detectar entornos de sandbox y alterar su comportamiento en consecuencia, lo que dificulta a los investigadores determinar su verdadera naturaleza.

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Análisis dinámico

```
xnoxos@htb[/htb]$ xfreerdp /u:htb-student /p:'HTB_@cademy_stdnt!' /v:[Target IP] /dynamic-resolution
```

# **Análisis dinámico con Noriben**

[Noriben](https://github.com/Rurik/Noriben)es una herramienta poderosa en nuestro kit de herramientas de análisis dinámico, esencialmente actuando como un envoltorio de pitón para los sistemas internos.`ProcMon`, una utilidad integral de monitoreo del sistema. Orquesta el funcionamiento de`ProcMon`, refina la salida y agrega una capa de inteligencia específica de malware al proceso. Apalancamiento`Noriben`, podemos capturar los comportamientos de malware más convenientemente y entenderlos con más precisión.

Para entender como`Noriben`Funda nuestros esfuerzos de análisis dinámico, primero revisemos rápidamente`ProcMon`. Esta herramienta, de`Sysinternals Suite`, monitorea el sistema de archivos, el registro y la actividad de procesos/hilos en tiempo real. Combina las características de los servicios públicos como`Filemon`, `Regmon`y características avanzadas como filtrado, resaltado avanzado y propiedades extensas de eventos, lo que lo convierte en una poderosa herramienta de monitoreo de sistemas para el análisis de malware.

Sin embargo, el volumen y la amplitud de la información que`ProcMon`Las colecciones pueden ser abrumadoras. Sin el filtrado adecuado y el análisis contextual, el examen a través de estos datos sin procesar se convierte en un desafío considerable. Aquí es donde interviene Noriben. Utiliza`ProcMon`Para capturar eventos del sistema, pero luego filtra y analiza estos datos para extraer información significativa y identificar actividades maliciosas.

En nuestro proceso de análisis dinámico de malware, así es como empleamos Noriben:

- `Setting Up Noriben`: Iniciamos`Noriben`Al iniciarlo desde la línea de comando. La herramienta admite numerosos argumentos de línea de comandos para personalizar su operación. Por ejemplo, podemos definir la duración de la recopilación de datos, especificar una muestra de malware personalizada para la ejecución o seleccionar un personalizado`ProcMon`archivo de configuración.
- `Launching ProcMon`: Al iniciar,`Noriben`comienzo`ProcMon`con una configuración predefinida. Esta configuración contiene un conjunto de filtros diseñados para excluir la actividad normal del sistema y centrarse en posibles indicadores de acciones maliciosas.
- `Executing the Malware Sample`: Con`ProcMon`correr,`Noriben`Ejecuta la muestra de malware seleccionada. Durante esta fase,`ProcMon`Captura todas las actividades del sistema, incluidas las operaciones de proceso, los cambios del sistema de archivos y las modificaciones de registro.
- `Monitoring and Logging`: `Noriben`controla la duración del monitoreo, y una vez que concluye, comanda`ProcMon`Para guardar los datos recopilados en un archivo CSV y luego termina Procmon.
- `Data Analysis and Reporting`: Aquí es donde`Noriben`brilla. Procesa el archivo csv generado por`ProcMon`, aplicando filtros adicionales y realización de análisis contextual.`Noriben`Identifica actividades potencialmente sospechosas y las organiza en diferentes categorías, como la actividad del sistema de archivos, las operaciones de proceso y las conexiones de red. Este proceso da como resultado un informe claro y legible en formato HTML o TXT, destacando los rasgos de comportamiento del malware analizado.

La integración de Noriben con las reglas de Yara es otra característica notable. Podemos aprovechar las reglas de Yara para mejorar nuestras capacidades de filtrado de datos, lo que nos permite identificar patrones de interés de manera más eficiente.

---

Para fines de demostración, realizaremos un análisis dinámico en una muestra de malware nombrada`shell.exe`, encontrado en el`C:\Samples\MalwareAnalysis`directorio en la máquina de destino de esta sección. Sigue estos pasos:

- Inicie una nueva interfaz de línea de comandos y diríjase a la`C:\Tools\Noriben-master`directorio.
- Iniciar Noriben como se indica.

Análisis dinámico

```
C:\Tools\Noriben-master> python .\Noriben.py
[*] Using filter file: ProcmonConfiguration.PMC
[*] Using procmon EXE: C:\ProgramData\chocolatey\bin\procmon.exe
[*] Procmon session saved to: Noriben_27_Jul_23__23_40_319983.pml
[*] Launching Procmon ...
[*] Procmon is running. Run your executable now.
[*] When runtime is complete, press CTRL+C to stop logging.

```

- Al ver el`User Account Control`Aviso, seleccione`Yes`.
- Proceder a`C:\Samples\MalwareAnalysis`y activar`shell.exe`haciendo doble clic.
- `shell.exe`identificará que se ejecuta dentro de un sandbox. Cierre la ventana que creó.
- Terminar`ProcMon`.
- En el símbolo del sistema que ejecuta noriben, use el`Ctrl+C`comandar para cesar su funcionamiento.

Análisis dinámico

```
C:\Tools\Noriben-master> python .\Noriben.py
[*] Using filter file: ProcmonConfiguration.PMC
[*] Using procmon EXE: C:\ProgramData\chocolatey\bin\procmon.exe
[*] Procmon session saved to: Noriben_27_Jul_23__23_40_319983.pml
[*] Launching Procmon ...
[*] Procmon is running. Run your executable now.
[*] When runtime is complete, press CTRL+C to stop logging.

[*] Termination of Procmon commencing... please wait
[*] Procmon terminated
[*] Saving report to: Noriben_27_Jul_23__23_42_335666.txt
[*] Saving timeline to: Noriben_27_Jul_23__23_42_335666_timeline.csv
[*] Exiting with error code: 0: Normal exit

```

Observarás que Noriben genera un`.txt`Informe dentro de su directorio, compilando toda la información de comportamiento que logró recopilar.

![](https://academy.hackthebox.com/storage/modules/227/noriben2.png)

Como se discutió,`Noriben`usos`ProcMon`Para capturar eventos del sistema, pero luego filtra y analiza estos datos para extraer información significativa y identificar actividades maliciosas.

`Noriben`podría filtrar alguna información potencialmente valiosa. Por ejemplo, no recibimos datos perspicaces del informe de Noriben sobre cómo`shell.exe`reconoció que estaba funcionando dentro de una caja de arena o máquina virtual.

Tomemos un enfoque diferente y lanzemos manualmente`ProcMon`(disponible en`C:\Tools\sysinternals`) Uso de su configuración predeterminada, más inclusiva. Después de esto, volvamos a ejecutar`shell.exe`. Esto podría darnos ideas sobre cómo`shell.exe`Detecta la presencia de una caja de arena o una máquina virtual.

Luego, configuremos el filtro (`Ctrl+L`) como sigue y presiona`Apply`.

![](https://academy.hackthebox.com/storage/modules/227/filter.png)

Finalmente, navegemos hasta el final de los resultados. Puede observar que`shell.exe`realiza la detección de sandbox o máquinas virtuales consultando el registro de la presencia de`VMware Tools`.

![](https://academy.hackthebox.com/storage/modules/227/results1.png)