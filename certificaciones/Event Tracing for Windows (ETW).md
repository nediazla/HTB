# Event Tracing for Windows (ETW)

En el ámbito de la detección eficaz de amenazas y la respuesta a incidentes, a menudo nos encontramos dependiendo de los datos de registro limitados que tenemos a nuestra disposición. Sin embargo, este enfoque no logra aprovechar plenamente la inmensa riqueza de información que puede derivarse del poderoso recurso conocido como `Event Tracing for Windows (ETW)`. Desafortunadamente, este descuido puede atribuirse a una falta de conciencia y aprecio por los conocimientos completos e intrincados que ETW puede ofrecer.

---

# **¿Qué es ETW?**

Según Microsoft, `Event Tracing For Windows (ETW)` es una función de rastreo de alta velocidad y de uso general proporcionada por el sistema operativo. Utilizando un mecanismo de almacenamiento en búfer y registro implementado en el kernel, ETW proporciona un mecanismo de seguimiento para eventos generados tanto por aplicaciones en modo de usuario como por controladores de dispositivos en modo kernel.

ETW, que funciona como un mecanismo de seguimiento de eventos de alto rendimiento profundamente integrado en el sistema operativo Windows, presenta una oportunidad incomparable para reforzar nuestras capacidades de defensa. Su arquitectura facilita la generación, recopilación y análisis dinámicos de diversos eventos que ocurren dentro del sistema, lo que da como resultado la creación de registros complejos en tiempo real que abarcan un amplio espectro de actividades.

Al aprovechar ETW de manera efectiva, podemos aprovechar una amplia gama de fuentes de telemetría que superan las limitaciones impuestas por los datos de registro tradicionales. ETW captura un conjunto diverso de eventos, que abarcan llamadas al sistema, creación y terminación de procesos, actividad de red, modificaciones de archivos y registros, y muchas otras dimensiones. Estos eventos en conjunto tejen un tapiz detallado de la actividad del sistema, proporcionando un contexto invaluable para la identificación de comportamientos anómalos, el descubrimiento de posibles incidentes de seguridad y la facilitación de investigaciones forenses.

La versatilidad y extensibilidad de ETW se ven aún más acentuadas por su perfecta integración con los proveedores de eventos. Estos componentes especializados generan tipos específicos de eventos y pueden incorporarse sin problemas a aplicaciones, componentes del sistema operativo o software de terceros. En consecuencia, esta integración garantiza una amplia cobertura de posibles fuentes de eventos. Además, la extensibilidad de ETW permite la creación de proveedores personalizados diseñados para abordar requisitos organizacionales específicos, fomentando así un enfoque específico y enfocado para el registro y el monitoreo.

En particular, la naturaleza liviana de ETW y su mínimo impacto en el rendimiento lo convierten en una solución de telemetría óptima para el monitoreo en tiempo real y la evaluación continua de la seguridad. Al habilitar y configurar selectivamente proveedores de eventos relevantes, podemos ajustar con precisión el alcance de la recopilación de datos para alinearlos con nuestros objetivos de seguridad específicos, logrando un equilibrio armonioso entre la riqueza de la información y las consideraciones de rendimiento del sistema.

Además, la existencia de herramientas y utilidades sólidas, incluidos Message Analyzer de Microsoft y PowerShell`Get-WinEvent`cmdlet, simplifica enormemente la recuperación, el análisis y el análisis de los registros de ETW. Estas herramientas ofrecen capacidades de filtrado avanzadas, mecanismos de correlación de eventos y funciones de monitoreo en tiempo real, lo que permite a los miembros del equipo azul extraer información útil del vasto conjunto de información capturada por ETW.

---

# **Arquitectura y componentes de ETW**

La arquitectura subyacente y los componentes clave de Event Tracing para Windows (ETW) se ilustran en el siguiente diagrama de[microsoft](https://web.archive.org/web/20200725154736/https://docs.microsoft.com/en-us/archive/blogs/ntdebugging/part-1-etw-introduction-and-overview).

![](https://academy.hackthebox.com/storage/modules/216/etw.png)

- `Controllers`: El componente Controladores, como su nombre lo indica, asume el control sobre todos los aspectos relacionados con las operaciones de ETW. Abarca funcionalidades como iniciar y finalizar sesiones de seguimiento, así como habilitar o deshabilitar proveedores dentro de un seguimiento en particular. Las sesiones de seguimiento pueden establecer suscripciones a uno o varios proveedores, otorgando así a los proveedores la capacidad de iniciar operaciones de registro. Un ejemplo de controlador ampliamente utilizado es la utilidad incorporada "logman.exe", que facilita la gestión de las actividades de ETW.

El núcleo de la arquitectura de ETW es el modelo de publicación-suscripción. Este modelo involucra dos componentes principales:

- `Providers`: Los proveedores desempeñan un papel fundamental a la hora de generar eventos y escribirlos en las sesiones de ETW designadas. Las aplicaciones tienen la capacidad de registrar proveedores de ETW, permitiéndoles generar y transmitir numerosos eventos. Hay cuatro tipos distintos de proveedores utilizados dentro de ETW.
    - `MOF Providers`: Estos proveedores se basan en el formato de objetos administrados (MOF) y son capaces de generar eventos de acuerdo con esquemas MOF predefinidos. Ofrecen un enfoque flexible para la generación de eventos y se utilizan ampliamente en diversos escenarios.
    - `WPP Providers`: Los proveedores de WPP, que significan "Preprocesador de seguimiento de software de Windows", aprovechan macros y anotaciones especializadas dentro del código fuente de la aplicación para generar eventos. Este tipo de proveedor se utiliza a menudo para fines de depuración y seguimiento en modo kernel de bajo nivel.
    - `Manifest-based Providers`: Los proveedores basados ​​en manifiestos representan una forma más contemporánea de proveedores dentro de ETW. Se basan en archivos de manifiesto XML que definen la estructura y las características de los eventos. Este enfoque ofrece mayor flexibilidad y facilidad de gestión, lo que permite la generación y personalización dinámicas de eventos.
    - `TraceLogging Providers`: Los proveedores de TraceLogging ofrecen un enfoque simplificado y eficiente para la generación de eventos. Aprovechan la API TraceLogging, introducida en versiones recientes de Windows, que agiliza el proceso de generación de eventos con una mínima sobrecarga de código.
- `Consumers`: Los consumidores se suscriben a eventos específicos de interés y los reciben para su posterior procesamiento o análisis. De forma predeterminada, los eventos generalmente se dirigen a un archivo .ETL (Registro de seguimiento de eventos) para su manejo. Sin embargo, un escenario de consumidor alternativo implica aprovechar las capacidades de la API de Windows para procesar y consumir los eventos.
- `Channels`: Para facilitar la recopilación y el consumo eficiente de eventos, ETW confía en los canales de eventos. Los canales de eventos actúan como contenedores lógicos para organizar y filtrar eventos en función de sus características e importancia. ETW admite múltiples canales, cada uno con su propio propósito y audiencia definidos. Los consumidores de eventos pueden suscribirse selectivamente a canales específicos para recibir eventos relevantes para sus respectivos casos de uso.
- `ETL files`: ETW proporciona soporte especializado para escribir eventos en el disco mediante el uso de archivos de registro de seguimiento de eventos, comúnmente denominados "archivos ETL". Estos archivos sirven como almacenamiento duradero para eventos, lo que permite análisis fuera de línea, archivado a largo plazo e investigaciones forenses. ETW permite una rotación y gestión fluidas de archivos ETL para garantizar una utilización eficiente del almacenamiento.

**Notas**:

- ETW admite proveedores de eventos tanto en modo kernel como en modo usuario.
- Algunos proveedores de eventos generan un volumen significativo de eventos, que potencialmente pueden saturar los recursos del sistema si están constantemente activos. Como resultado, para evitar el consumo innecesario de recursos, estos proveedores suelen estar deshabilitados de forma predeterminada y solo se habilitan cuando una sesión de seguimiento solicita específicamente su activación.
- Además de sus capacidades inherentes, ETW se puede ampliar a través de proveedores de eventos personalizados.
- [El registro de eventos solo puede consumir los eventos del proveedor ETW a los que se les ha aplicado una propiedad de canal.](https://medium.com/threat-hunters-forge/threat-hunting-with-etw-events-and-helk-part-1-installing-silketw-6eb74815e4a0)

---

# **Interactuando con ETW**

`Logman`es una utilidad preinstalada para administrar el seguimiento de eventos para Windows (ETW) y las sesiones de seguimiento de eventos. Esta herramienta es invaluable para crear, iniciar, detener e investigar sesiones de seguimiento. Esto es particularmente útil al determinar qué sesiones están configuradas para la recopilación de datos o al iniciar su propia recopilación de datos.

Empleando el `-ets` El parámetro permitirá una investigación directa de las sesiones de seguimiento de eventos, proporcionando información sobre las sesiones de seguimiento de todo el sistema. Como ejemplo, las sesiones de seguimiento de eventos de Sysmon se pueden encontrar hacia el final de la información mostrada.

Seguimiento de eventos para Windows (ETW)

```
C:\Tools> logman.exe query -ets

Data Collector Set                      Type                          Status
-------------------------------------------------------------------------------
Circular Kernel Context Logger          Trace                         Running
Eventlog-Security                       Trace                         Running
DiagLog                                 Trace                         Running
Diagtrack-Listener                      Trace                         Running
EventLog-Application                    Trace                         Running
EventLog-Microsoft-Windows-Sysmon-Operational Trace                         Running
EventLog-System                         Trace                         Running
LwtNetLog                               Trace                         Running
Microsoft-Windows-Rdp-Graphics-RdpIdd-Trace Trace                         Running
NetCore                                 Trace                         Running
NtfsLog                                 Trace                         Running
RadioMgr                                Trace                         Running
UBPM                                    Trace                         Running
WdiContextLog                           Trace                         Running
WiFiSession                             Trace                         Running
SHS-06012023-115154-7-7f                Trace                         Running
UserNotPresentTraceSession              Trace                         Running
8696EAC4-1288-4288-A4EE-49EE431B0AD9    Trace                         Running
ScreenOnPowerStudyTraceSession          Trace                         Running
SYSMON TRACE                            Trace                         Running
MSDTC_TRACE_SESSION                     Trace                         Running
SysmonDnsEtwSession                     Trace                         Running
MpWppTracing-20230601-115025-00000003-ffffffff Trace                         Running
WindowsUpdate_trace_log                 Trace                         Running
Admin_PS_Provider                       Trace                         Running
Terminal-Services-LSM-ApplicationLag-3764 Trace                         Running
Microsoft.Windows.Remediation           Trace                         Running
SgrmEtwSession                          Trace                         Running

The command completed successfully.

```

Cuando examinamos una sesión de seguimiento de eventos directamente, descubrimos detalles específicos de la sesión, incluido el nombre, el tamaño máximo del registro, la ubicación del registro y los proveedores suscritos. Esta información es invaluable para los respondedores de incidentes. Descubrir una sesión que registre proveedores relevantes para sus intereses puede proporcionar registros cruciales para una investigación.

Tenga en cuenta que el parámetro "-ets" es vital para el comando. Sin él, Logman no identificará la sesión de seguimiento de eventos.

Por cada proveedor suscrito a la sesión, podemos adquirir datos críticos:

- `Name / Provider GUID`: Este es el identificador exclusivo del proveedor.
- `Level`: Esto describe el nivel del evento, indicando si está filtrando por eventos de advertencia, informativos, críticos o todos.
- `Keywords Any`: Las palabras clave crean un filtro basado en el tipo de evento generado por el proveedor.

Seguimiento de eventos para Windows (ETW)

```
C:\Tools> logman.exe query "EventLog-System" -ets

Name:                 EventLog-System
Status:               Running
Root Path:            %systemdrive%\PerfLogs\Admin
Segment:              Off
Schedules:            On
Segment Max Size:     100 MB

Name:                 EventLog-System\EventLog-System
Type:                 Trace
Append:               Off
Circular:             Off
Overwrite:            Off
Buffer Size:          64
Buffers Lost:         0
Buffers Written:      47
Buffer Flush Timer:   1
Clock Type:           System
File Mode:            Real-time

Provider:
Name:                 Microsoft-Windows-FunctionDiscoveryHost
Provider Guid:        {538CBBAD-4877-4EB2-B26E-7CAEE8F0F8CB}
Level:                255
KeywordsAll:          0x0
KeywordsAny:          0x8000000000000000 (System)
Properties:           65
Filter Type:          0

Provider:
Name:                 Microsoft-Windows-Subsys-SMSS
Provider Guid:        {43E63DA5-41D1-4FBF-ADED-1BBED98FDD1D}
Level:                255
KeywordsAll:          0x0
KeywordsAny:          0x4000000000000000 (System)
Properties:           65
Filter Type:          0

Provider:
Name:                 Microsoft-Windows-Kernel-General
Provider Guid:        {A68CA8B7-004F-D7B6-A698-07E2DE0F1F5D}
Level:                255
KeywordsAll:          0x0
KeywordsAny:          0x8000000000000000 (System)
Properties:           65
Filter Type:          0

Provider:
Name:                 Microsoft-Windows-FilterManager
Provider Guid:        {F3C5E28E-63F6-49C7-A204-E48A1BC4B09D}
Level:                255
KeywordsAll:          0x0
KeywordsAny:          0x8000000000000000 (System)
Properties:           65
Filter Type:          0

--- SNIP ---

The command completed successfully.

```

Al utilizar el `logman query providers` comando, podemos generar una lista de todos los proveedores disponibles en el sistema, incluidos sus respectivos GUID.

Seguimiento de eventos para Windows (ETW)

```
C:\Tools> logman.exe query providers

Provider                                 GUID
-------------------------------------------------------------------------------
ACPI Driver Trace Provider               {DAB01D4D-2D48-477D-B1C3-DAAD0CE6F06B}
Active Directory Domain Services: SAM    {8E598056-8993-11D2-819E-0000F875A064}
Active Directory: Kerberos Client        {BBA3ADD2-C229-4CDB-AE2B-57EB6966B0C4}
Active Directory: NetLogon               {F33959B4-DBEC-11D2-895B-00C04F79AB69}
ADODB.1                                  {04C8A86F-3369-12F8-4769-24E484A9E725}
ADOMD.1                                  {7EA56435-3F2F-3F63-A829-F0B35B5CAD41}
Application Popup                        {47BFA2B7-BD54-4FAC-B70B-29021084CA8F}
Application-Addon-Event-Provider         {A83FA99F-C356-4DED-9FD6-5A5EB8546D68}
ATA Port Driver Tracing Provider         {D08BD885-501E-489A-BAC6-B7D24BFE6BBF}
AuthFw NetShell Plugin                   {935F4AE6-845D-41C6-97FA-380DAD429B72}
BCP.1                                    {24722B88-DF97-4FF6-E395-DB533AC42A1E}
BFE Trace Provider                       {106B464A-8043-46B1-8CB8-E92A0CD7A560}
BITS Service Trace                       {4A8AAA94-CFC4-46A7-8E4E-17BC45608F0A}
Certificate Services Client CredentialRoaming Trace {EF4109DC-68FC-45AF-B329-CA2825437209}
Certificate Services Client Trace        {F01B7774-7ED7-401E-8088-B576793D7841}
Circular Kernel Session Provider         {54DEA73A-ED1F-42A4-AF71-3E63D056F174}
Classpnp Driver Tracing Provider         {FA8DE7C4-ACDE-4443-9994-C4E2359A9EDB}
Critical Section Trace Provider          {3AC66736-CC59-4CFF-8115-8DF50E39816B}
DBNETLIB.1                               {BD568F20-FCCD-B948-054E-DB3421115D61}
Deduplication Tracing Provider           {5EBB59D1-4739-4E45-872D-B8703956D84B}
Disk Class Driver Tracing Provider       {945186BF-3DD6-4F3F-9C8E-9EDD3FC9D558}
Downlevel IPsec API                      {94335EB3-79EA-44D5-8EA9-306F49B3A041}
Downlevel IPsec NetShell Plugin          {E4FF10D8-8A88-4FC6-82C8-8C23E9462FE5}
Downlevel IPsec Policy Store             {94335EB3-79EA-44D5-8EA9-306F49B3A070}
Downlevel IPsec Service                  {94335EB3-79EA-44D5-8EA9-306F49B3A040}
EA IME API                               {E2A24A32-00DC-4025-9689-C108C01991C5}
Error Instrument                         {CD7CF0D0-02CC-4872-9B65-0DBA0A90EFE8}
FD Core Trace                            {480217A9-F824-4BD4-BBE8-F371CAAF9A0D}
FD Publication Trace                     {649E3596-2620-4D58-A01F-17AEFE8185DB}
FD SSDP Trace                            {DB1D0418-105A-4C77-9A25-8F96A19716A4}
FD WNet Trace                            {8B20D3E4-581F-4A27-8109-DF01643A7A93}
FD WSDAPI Trace                          {7E2DBFC7-41E8-4987-BCA7-76CADFAD765F}
FDPHost Service Trace                    {F1C521CA-DA82-4D79-9EE4-D7A375723B68}
File Kernel Trace; Operation Set 1       {D75D8303-6C21-4BDE-9C98-ECC6320F9291}
File Kernel Trace; Operation Set 2       {058DD951-7604-414D-A5D6-A56D35367A46}
File Kernel Trace; Optional Data         {7DA1385C-F8F5-414D-B9D0-02FCA090F1EC}
File Kernel Trace; Volume To Log         {127D46AF-4AD3-489F-9165-F00BA64D5467}
FWPKCLNT Trace Provider                  {AD33FA19-F2D2-46D1-8F4C-E3C3087E45AD}
FWPUCLNT Trace Provider                  {5A1600D2-68E5-4DE7-BCF4-1C2D215FE0FE}
Heap Trace Provider                      {222962AB-6180-4B88-A825-346B75F2A24A}
IKEEXT Trace Provider                    {106B464D-8043-46B1-8CB8-E92A0CD7A560}
IMAPI1 Shim                              {1FF10429-99AE-45BB-8A67-C9E945B9FB6C}
IMAPI2 Concatenate Stream                {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E9D}
IMAPI2 Disc Master                       {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E91}
IMAPI2 Disc Recorder                     {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E93}
IMAPI2 Disc Recorder Enumerator          {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E92}
IMAPI2 dll                               {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E90}
IMAPI2 Interleave Stream                 {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E9E}
IMAPI2 Media Eraser                      {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E97}
IMAPI2 MSF                               {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E9F}
IMAPI2 Multisession Sequential           {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7EA0}
IMAPI2 Pseudo-Random Stream              {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E9C}
IMAPI2 Raw CD Writer                     {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E9A}
IMAPI2 Raw Image Writer                  {07E397EC-C240-4ED7-8A2A-B9FF0FE5D581}
IMAPI2 Standard Data Writer              {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E98}
IMAPI2 Track-at-Once CD Writer           {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E99}
IMAPI2 Utilities                         {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E94}
IMAPI2 Write Engine                      {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E96}
IMAPI2 Zero Stream                       {0E85A5A5-4D5C-44B7-8BDA-5B7AB54F7E9B}
IMAPI2FS Tracing                         {F8036571-42D9-480A-BABB-DE7833CB059C}
Intel-iaLPSS-GPIO                        {D386CC7A-620A-41C1-ABF5-55018C6C699A}
Intel-iaLPSS-I2C                         {D4AEAC44-AD44-456E-9C90-33F8CDCED6AF}
Intel-iaLPSS2-GPIO2                      {63848CFF-3EC7-4DDF-8072-5F95E8C8EB98}
Intel-iaLPSS2-I2C                        {C2F86198-03CA-4771-8D4C-CE6E15CBCA56}
IPMI Driver Trace                        {D5C6A3E9-FA9C-434E-9653-165B4FC869E4}
IPMI Provider Trace                      {651D672B-E11F-41B7-ADD3-C2F6A4023672}
KMDFv1 Trace Provider                    {544D4C9D-942C-46D5-BF50-DF5CD9524A50}
Layer2 Security HC Diagnostics Trace     {2E8D9EC5-A712-48C4-8CE0-631EB0C1CD65}
Local Security Authority (LSA)           {CC85922F-DB41-11D2-9244-006008269001}
LsaSrv                                   {199FE037-2B82-40A9-82AC-E1D46C792B99}
Microsoft-Antimalware-AMFilter           {CFEB0608-330E-4410-B00D-56D8DA9986E6}
Microsoft-Antimalware-Engine             {0A002690-3839-4E3A-B3B6-96D8DF868D99}
Microsoft-Antimalware-Engine-Instrumentation {68621C25-DF8D-4A6B-AABC-19A22E296A7C}
Microsoft-Antimalware-NIS                {102AAB0A-9D9C-4887-A860-55DE33B96595}
Microsoft-Antimalware-Protection         {E4B70372-261F-4C54-8FA6-A5A7914D73DA}
Microsoft-Antimalware-RTP                {8E92DEEF-5E17-413B-B927-59B2F06A3CFC}
Microsoft-Antimalware-Scan-Interface     {2A576B87-09A7-520E-C21A-4942F0271D67}
Microsoft-Antimalware-Service            {751EF305-6C6E-4FED-B847-02EF79D26AEF}
Microsoft-Antimalware-ShieldProvider     {928F7D29-0577-5BE5-3BD3-B6BDAB9AB307}
Microsoft-Antimalware-UacScan            {D37E7910-79C8-57C4-DA77-52BB646364CD}
Microsoft-AppV-Client                    {E4F68870-5AE8-4E5B-9CE7-CA9ED75B0245}
Microsoft-AppV-Client-StreamingUX        {28CB46C7-4003-4E50-8BD9-442086762D12}
Microsoft-AppV-ServiceLog                {9CC69D1C-7917-4ACD-8066-6BF8B63E551B}
Microsoft-AppV-SharedPerformance         {FB4A19EE-EB5A-47A4-BC52-E71AAC6D0859}
Microsoft-Client-Licensing-Platform      {B6CC0D55-9ECC-49A8-B929-2B9022426F2A}
Microsoft-Gaming-Services                {BC1BDB57-71A2-581A-147B-E0B49474A2D4}
Microsoft-IE                             {9E3B3947-CA5D-4614-91A2-7B624E0E7244}
Microsoft-IE-JSDumpHeap                  {7F8E35CA-68E8-41B9-86FE-D6ADC5B327E7}
Microsoft-IEFRAME                        {5C8BB950-959E-4309-8908-67961A1205D5}
Microsoft-JScript                        {57277741-3638-4A4B-BDBA-0AC6E45DA56C}
Microsoft-OneCore-OnlineSetup            {41862974-DA3B-4F0B-97D5-BB29FBB9B71E}
Microsoft-PerfTrack-IEFRAME              {B2A40F1F-A05A-4DFD-886A-4C4F18C4334C}
Microsoft-PerfTrack-MSHTML               {FFDB9886-80F3-4540-AA8B-B85192217DDF}
Microsoft-User Experience Virtualization-Admin {61BC445E-7A8D-420E-AB36-9C7143881B98}
Microsoft-User Experience Virtualization-Agent Driver {DE29CF61-5EE6-43FF-9AAC-959C4E13CC6C}
Microsoft-User Experience Virtualization-App Agent {1ED6976A-4171-4764-B415-7EA08BC46C51}
Microsoft-User Experience Virtualization-IPC {21D79DB0-8E03-41CD-9589-F3EF7001A92A}
Microsoft-User Experience Virtualization-SQM Uploader {57003E21-269B-4BDC-8434-B3BF8D57D2D5}
Microsoft-Windows Networking VPN Plugin Platform {E5FC4A0F-7198-492F-9B0F-88FDCBFDED48}
Microsoft-Windows-AAD                    {4DE9BC9C-B27A-43C9-8994-0915F1A5E24F}
Microsoft-Windows-ACL-UI                 {EA4CC8B8-A150-47A3-AFB9-C8D194B19452}

The command completed successfully.

```

Windows 10 incluye más de 1000 proveedores integrados. Además, el software de terceros suele incorporar sus propios proveedores ETW, especialmente aquellos que operan en modo Kernel.

Debido al gran número de proveedores, suele ser ventajoso filtrarlos utilizando `findstr`. Por ejemplo, verá varios resultados para "Winlogon" en el ejemplo dado.

Seguimiento de eventos para Windows (ETW)

```
C:\Tools> logman.exe query providers | findstr "Winlogon"
Microsoft-Windows-Winlogon               {DBE9B383-7CF3-4331-91CC-A3CB16A3B538}
Windows Winlogon Trace                   {D451642C-63A6-11D7-9720-00B0D03E0347}

```

Al especificar un proveedor con Logman, obtenemos una comprensión más profunda de la función del proveedor. Esto nos informará sobre las palabras clave que podemos filtrar, los niveles de eventos disponibles y qué procesos están utilizando actualmente el proveedor.

Seguimiento de eventos para Windows (ETW)

```
C:\Tools> logman.exe query providers Microsoft-Windows-Winlogon

Provider                                 GUID
-------------------------------------------------------------------------------
Microsoft-Windows-Winlogon               {DBE9B383-7CF3-4331-91CC-A3CB16A3B538}

Value               Keyword              Description
-------------------------------------------------------------------------------
0x0000000000010000  PerfInstrumentation
0x0000000000020000  PerfDiagnostics
0x0000000000040000  NotificationEvents
0x0000000000080000  PerfTrackContext
0x0000100000000000  ms:ReservedKeyword44
0x0000200000000000  ms:Telemetry
0x0000400000000000  ms:Measures
0x0000800000000000  ms:CriticalData
0x0001000000000000  win:ResponseTime     Response Time
0x0080000000000000  win:EventlogClassic  Classic
0x8000000000000000  Microsoft-Windows-Winlogon/Diagnostic
0x4000000000000000  Microsoft-Windows-Winlogon/Operational
0x2000000000000000  System               System

Value               Level                Description
-------------------------------------------------------------------------------
0x02                win:Error            Error
0x03                win:Warning          Warning
0x04                win:Informational    Information

PID                 Image
-------------------------------------------------------------------------------
0x00001710
0x0000025c

The command completed successfully.

```

El `Microsoft-Windows-Winlogon/Diagnostic` y `Microsoft-Windows-Winlogon/Operational` Las palabras clave hacen referencia a los registros de eventos generados por este proveedor.

También existen alternativas basadas en GUI. Estos son:

1. Usando la interfaz gráfica del `Performance Monitor` herramienta, podemos visualizar varias sesiones de seguimiento en ejecución. Se puede acceder a una descripción detallada de un seguimiento específico simplemente haciendo doble clic en él. Esto revela todos los datos pertinentes relacionados con el seguimiento, desde los proveedores contratados y sus funciones activadas hasta la naturaleza del seguimiento en sí. Además, estas sesiones se pueden modificar para adaptarlas a nuestras necesidades incorporando o eliminando proveedores. Por último, podemos idear nuevas sesiones optando por la categoría "Definido por el usuario".
    
    ![](https://academy.hackthebox.com/storage/modules/216/image46.png)
    
    ![](https://academy.hackthebox.com/storage/modules/216/image55.png)
    
2. Los metadatos del proveedor ETW también se pueden ver a través de [Proyecto EtwExplorer](https://github.com/zodiacon/EtwExplorer).
    
    ![](https://academy.hackthebox.com/storage/modules/216/image56.png)
    

---

# **Proveedores útiles**

- `Microsoft-Windows-Kernel-Process`: este proveedor ETW es fundamental para monitorear la actividad relacionada con los procesos dentro del kernel de Windows. Puede ayudar a detectar comportamientos inusuales de procesos, como inyección de procesos, vaciado de procesos y otras tácticas comúnmente utilizadas por malware y amenazas persistentes avanzadas (APT).
- `Microsoft-Windows-Kernel-File`: Como sugiere el nombre, este proveedor se centra en operaciones relacionadas con archivos. Puede emplearse para escenarios de detección que impliquen acceso no autorizado a archivos, cambios en archivos críticos del sistema u operaciones de archivos sospechosos que indiquen exfiltración o actividad de ransomware.
- `Microsoft-Windows-Kernel-Network`: este proveedor de ETW ofrece visibilidad de la actividad relacionada con la red a nivel del kernel. Es especialmente útil para detectar ataques basados ​​en la red, como exfiltración de datos, conexiones de red no autorizadas y posibles señales de comunicación de comando y control (C2).
- `Microsoft-Windows-SMBClient/SMBServer`: Estos proveedores monitorean la actividad del servidor y del cliente del Bloque de mensajes del servidor (SMB), brindando información sobre el intercambio de archivos y la comunicación en red. Se pueden utilizar para detectar patrones de tráfico inusuales para PYMES, lo que podría indicar movimiento lateral o filtración de datos.
- `Microsoft-Windows-DotNETRuntime`: este proveedor se centra en eventos de tiempo de ejecución de .NET, lo que lo hace ideal para identificar anomalías en la ejecución de aplicaciones .NET, posible explotación de vulnerabilidades de .NET o carga de ensamblados .NET maliciosos.
- `OpenSSH`: La supervisión del proveedor OpenSSH ETW puede proporcionar información importante sobre los intentos de conexión de Secure Shell (SSH), las autenticaciones exitosas y fallidas y los posibles ataques de fuerza bruta.
- `Microsoft-Windows-VPN-Client`: este proveedor permite el seguimiento de eventos de clientes de red privada virtual (VPN). Puede resultar útil para identificar conexiones VPN no autorizadas o sospechosas.
- `Microsoft-Windows-PowerShell`: este proveedor ETW rastrea la ejecución de PowerShell y la actividad de los comandos, lo que lo hace invaluable para detectar usos sospechosos de PowerShell, registros de bloques de scripts y posibles usos indebidos o explotación.
- `Microsoft-Windows-Kernel-Registry`: este proveedor monitorea las operaciones del registro, lo que lo hace útil para escenarios de detección relacionados con cambios en las claves del registro, a menudo asociados con mecanismos de persistencia, instalación de malware o cambios en la configuración del sistema.
- `Microsoft-Windows-CodeIntegrity`: este proveedor supervisa las comprobaciones de integridad del código y del controlador, que pueden ser clave para identificar intentos de cargar códigos o controladores maliciosos o no firmados.
- `Microsoft-Antimalware-Service`: Este proveedor ETW se puede utilizar para detectar posibles problemas con el servicio antimalware, incluidos servicios deshabilitados, cambios de configuración o posibles técnicas de evasión empleadas por malware.
- `WinRM`: La supervisión del proveedor de administración remota de Windows (WinRM) puede revelar actividad de administración remota sospechosa o no autorizada, que a menudo indica movimiento lateral o ejecución remota de comandos.
- `Microsoft-Windows-TerminalServices-LocalSessionManager`: este proveedor rastrea las sesiones locales de Terminal Services, lo que lo hace útil para detectar actividades sospechosas o no autorizadas en el escritorio remoto.
- `Microsoft-Windows-Security-Mitigations`: este proveedor controla la efectividad y las operaciones de las mitigaciones de seguridad implementadas. Es esencial para identificar posibles intentos de eludir estos controles de seguridad.
- `Microsoft-Windows-DNS-Client`: este proveedor ETW brinda visibilidad de la actividad del cliente DNS, lo cual es crucial para detectar ataques basados ​​en DNS, incluidos túneles DNS o solicitudes DNS inusuales que pueden indicar comunicación C2.
- `Microsoft-Antimalware-Protection`: este proveedor monitorea las operaciones de los mecanismos de protección antimalware. Se puede utilizar para detectar cualquier problema con estos mecanismos, como funciones de protección deshabilitadas, cambios de configuración o signos de técnicas de evasión empleadas por actores maliciosos.

---

# **Proveedores restringidos**

En el ámbito de la seguridad del sistema operativo Windows, ciertos proveedores de ETW se consideran "restringidos". Estos proveedores ofrecen telemetría valiosa, pero solo son accesibles para procesos que cuentan con los permisos necesarios. Esta exclusividad está diseñada para garantizar que los datos confidenciales del sistema permanezcan protegidos de posibles amenazas.

Uno de estos proveedores restringidos y de alto valor es `Microsoft-Windows-Threat-Intelligence`. Este proveedor ofrece información crucial sobre posibles amenazas a la seguridad y, a menudo, se aprovecha en operaciones de análisis forense digital y respuesta a incidentes (DFIR). Sin embargo, para acceder a este proveedor, los procesos deben tener privilegios con un derecho específico, conocido como Protected Process Light (PPL).

Según Elástico:*Para poder ejecutarse como PPL, un proveedor de antimalware debe presentar una solicitud a Microsoft, demostrar su identidad, firmar documentos legales vinculantes, implementar un controlador Anti-Malware de inicio temprano (ELAM), ejecutarlo a través de un conjunto de pruebas y enviarlo. a Microsoft para obtener una firma Authenticode especial. No es un proceso trivial. Una vez que se completa este proceso, el proveedor puede usar este controlador ELAM para que Windows proteja su servicio antimalware ejecutándolo como PPL.*Dicho esto, [soluciones para acceder a`Microsoft-Windows-Threat-Intelligence`proveedor existe](https://posts.specterops.io/uncovering-windows-events-b4b9db7eac54).

En el contexto de `Microsoft-Windows-Threat-Intelligence`, los beneficios de este acceso privilegiado son múltiples. Este proveedor puede registrar datos muy granulares sobre amenazas potenciales, lo que permite a los profesionales de la seguridad detectar y analizar ataques sofisticados que pueden haber eludido otras defensas. Su telemetría puede servir como evidencia vital en investigaciones forenses, revelando detalles sobre el origen de una amenaza, los sistemas y datos con los que interactuó y las modificaciones que realizó. Además, al monitorear a este proveedor en tiempo real, los equipos de seguridad pueden identificar amenazas en curso e intervenir para mitigar los daños.

En la siguiente sección, utilizaremos ETW para investigar ataques que pueden evadir la detección si confiamos únicamente en Sysmon para el monitoreo y análisis, debido a sus limitaciones inherentes para capturar ciertos eventos.

---

# **Referencias**

- [https://nasbench.medium.com/a-primer-on-event-tracing-for-windows-etw-997725c082bf](https://nasbench.medium.com/a-primer-on-event-tracing-for-windows-etw-997725c082bf)
- [https://bmcder.com/blog/a-begginers-all-inclusive-guide-to-etw](https://web.archive.org/web/20230222121234/https://bmcder.com/blog/a-begginers-all-inclusive-guide-to-etw)