# Herramientas rápidas de examen y análisis

Cuando se trata de un análisis rápido de triaje, las herramientas externas correctas son esenciales para un examen y análisis exhaustivos.

`Eric Zimmerman`ha seleccionado un conjunto de herramientas indispensables adaptadas para este mismo propósito. Estas herramientas están meticulosamente diseñadas para ayudar a los analistas forenses en su búsqueda para extraer información vital de dispositivos y artefactos digitales.

Para obtener una lista completa de estas herramientas, consulte:[https://ericzimmerman.github.io/#!index.md](https://ericzimmerman.github.io/#!index.md)

Vamos a navegar ahora hasta la parte inferior de esta sección y haga clic en "¡Haga clic aquí para generar el sistema de destino!". Luego, hagamos RDP en la IP de destino utilizando las credenciales proporcionadas. La gran mayoría de las acciones/comandos cubiertos desde este punto hasta el final de esta sección se pueden replicar dentro del objetivo, ofreciendo una comprensión más completa de los temas presentados.

Para optimizar el proceso de descarga, podemos visitar el sitio web oficial y seleccionar el enlace .NET 4 o .NET 6. Esta acción iniciará la descarga de todas las herramientas en un formato comprimido.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_eztools.png)

También podemos aprovechar el script PowerShell proporcionado, como se describe en el paso 2 de la captura de pantalla anterior, para descargar todas las herramientas.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools> .\Get-ZimmermanTools.ps1

This script will discover and download all available programs
from https://ericzimmerman.github.io and download them to C:\htb\dfir_module\tools

A file will also be created in C:\Users\johndoe\Desktop\Get-ZimmermanTools that tracks the SHA-1 of each file,
so rerunning the script will only download new versions.

To redownload, remove lines from or delete the CSV file created under C:\htb\dfir_module\tools and rerun. Enjoy!

Use -NetVersion to control which version of the software you get (4 or 6). Default is 6. Use 0 to get both

* Getting available programs...
* Files to download: 27
* Downloaded Get-ZimmermanTools.zip (Size: 10,396)
* C:\htb\dfir_module\tools\net6 does not exist. Creating...
* Downloaded AmcacheParser.zip (Size: 23,60,293) (net 6)
* Downloaded AppCompatCacheParser.zip (Size: 22,62,497) (net 6)
* Downloaded bstrings.zip (Size: 14,73,298) (net 6)
* Downloaded EvtxECmd.zip (Size: 40,36,022) (net 6)
* Downloaded EZViewer.zip (Size: 8,25,80,608) (net 6)
* Downloaded JLECmd.zip (Size: 27,79,229) (net 6)
* Downloaded JumpListExplorer.zip (Size: 8,66,96,361) (net 6)
* Downloaded LECmd.zip (Size: 32,38,911) (net 6)
* Downloaded MFTECmd.zip (Size: 22,26,605) (net 6)
* Downloaded MFTExplorer.zip (Size: 8,27,54,162) (net 6)
* Downloaded PECmd.zip (Size: 20,13,672) (net 6)
* Downloaded RBCmd.zip (Size: 18,19,172) (net 6)
* Downloaded RecentFileCacheParser.zip (Size: 17,22,133) (net 6)
* Downloaded RECmd.zip (Size: 36,89,345) (net 6)
* Downloaded RegistryExplorer.zip (Size: 9,66,96,169) (net 6)
* Downloaded rla.zip (Size: 21,55,515) (net 6)
* Downloaded SDBExplorer.zip (Size: 8,24,54,727) (net 6)
* Downloaded SBECmd.zip (Size: 21,90,158) (net 6)
* Downloaded ShellBagsExplorer.zip (Size: 8,80,06,168) (net 6)
* Downloaded SQLECmd.zip (Size: 52,83,482) (net 6)
* Downloaded SrumECmd.zip (Size: 24,00,622) (net 6)
* Downloaded SumECmd.zip (Size: 20,23,009) (net 6)
* Downloaded TimelineExplorer.zip (Size: 8,77,50,507) (net 6)
* Downloaded VSCMount.zip (Size: 15,46,539) (net 6)
* Downloaded WxTCmd.zip (Size: 36,98,112) (net 6)
* Downloaded iisGeolocate.zip (Size: 3,66,76,319) (net 6)

* Saving downloaded version information to C:\Users\johndoe\Desktop\Get-ZimmermanTools\!!!RemoteFileDetails.csv

```

Si bien utilizaremos un subconjunto de estas herramientas para analizar los datos de salida de KAPE, es prudente que nos familiaricemos con todo el kit de herramientas. Comprender las capacidades completas de cada herramienta puede mejorar significativamente nuestra destreza de investigación.

---

En esta sección trabajaremos con ciertas herramientas y evidencia que residen en los siguientes directorios del objetivo de esta sección.

- **Ubicación de evidencia**: `C:\Users\johndoe\Desktop\forensic_data`
    - **Ubicación de salida de Kape**: `C:\Users\johndoe\Desktop\forensic_data\kape_output`
- **Ubicación de herramientas de Eric Zimmerman**: `C:\Users\johndoe\Desktop\Get-ZimmermanTools`
- **Ubicación del editor de disco activo@**: `C:\Program Files\LSoft Technologies\Active@ Disk Editor`
- **Ubicación de EQL**: `C:\Users\johndoe\Desktop\eqllib-master`
- **Ubicación de Rishipper**: `C:\Users\johndoe\Desktop\RegRipper3.0-master`

---

### **Mac (b) Times en NTFS**

El término`MAC(b) times`denota una serie de marcas de tiempo vinculadas a archivos u objetos. Estas marcas de tiempo son fundamentales, ya que arrojan luz sobre la cronología de eventos o acciones en un sistema de archivos. El acrónimo`MAC(b)`es una abreviatura de`Modified, Accessed, Changed, and (b) Birth`veces. La inclusión de`b`significa el`Birth timestamp`, que no está universalmente presente en todos los sistemas de archivos o de fácil acceso a través de funciones de API de Windows estándar. Profundicemos más en los matices de`MACB`marcas de tiempo.

- `Modified Time (M)`: Esta marca de tiempo captura la última instancia cuando el contenido dentro del archivo sufrió modificaciones. Cualquier alteración a los datos del archivo, como las ediciones de contenido, desencadena una actualización de esta marca de tiempo.
- `Accessed Time (A)`: Esta marca de tiempo refleja la última ocasión en que se accedió o lee el archivo, actualizando cada vez que el archivo se abre o se involucra de otra manera.
- `Changed [Change in MFT Record ] (C)`: Esta marca de tiempo significa cambios en el registro MFT. Captura el momento en que el archivo se creó inicialmente. Sin embargo, vale la pena señalar que ciertos sistemas de archivos, como NTFS, podrían actualizar esta marca de tiempo si el archivo sufre movimiento o copia.
- `Birth Time (b)`: A menudo se le conoce la marca de tiempo de nacimiento o nacido, esto representa el momento preciso en que el archivo u objeto se instancia en el sistema de archivos. Su importancia en las investigaciones forenses no puede ser exagerada, especialmente al determinar el tiempo de creación original de un archivo.

### **Reglas generales para marcas de tiempo en el sistema de archivos de Windows NTFS**

La siguiente tabla delinea las reglas generales que rigen cómo varias operaciones de archivo influyen en las marcas de tiempo dentro del Windows NTFS (nuevo sistema de archivos de tecnología).

| **Operación** | **Modificado** | **Accedido** | **Nacimiento (creado)** |
| --- | --- | --- | --- |
| Archivo Crear | Sí | Sí | Sí |
| Archivo modificar | Sí | No | No |
| Copia de archivo | No (heredado) | Sí | Sí |
| Acceso de archivo | No | No* | No |
1. **Archivo Crear**:
    - `Modified Timestamp (M)`: La marca de tiempo modificada se actualiza para reflejar el tiempo de creación de archivos.
    - `Accessed Timestamp (A)`: La marca de tiempo al que se accede se actualiza para reflejar que se accedió al archivo en el momento de la creación.
    - `Birth (Created) Timestamp (b)`: La marca de tiempo de nacimiento se establece en la hora de la creación de archivos.
2. **Archivo modificar**:
    - `Modified Timestamp (M)`: La marca de tiempo modificada se actualiza para reflejar el momento en que el contenido o los atributos del archivo se modificaron por última vez.
    - `Accessed Timestamp (A)`: La marca de tiempo accedida no se actualiza cuando se modifica el archivo.
    - `Birth (Created) Timestamp (b)`: La marca de tiempo de nacimiento no se actualiza cuando se modifica el archivo.
3. **Copia de archivo**:
    - `Modified Timestamp (M)`: La marca de tiempo modificada generalmente no se actualiza cuando se copia un archivo. Por lo general, hereda la marca de tiempo del archivo fuente.
    - `Accessed Timestamp (A)`: La marca de tiempo al que se accede se actualiza para reflejar que se accedió al archivo al momento de la copia.
    - `Birth (Created) Timestamp (b)`: La marca de tiempo de nacimiento se actualiza al momento de la copia, lo que indica cuándo se creó la copia.
4. **Acceso de archivo**:
    - `Modified Timestamp (M)`: La marca de tiempo modificada no se actualiza cuando se accede al archivo.
    - `Accessed Timestamp (A)`: La marca de tiempo al que se accede se actualiza para reflejar el tiempo de acceso.
    - `Birth (Created) Timestamp (b)`: La marca de tiempo de nacimiento no se actualiza cuando se accede al archivo.

Todas estas marcas de tiempo residen en el`$MFT`Archivo, ubicado en la raíz de la unidad del sistema. Mientras el`$MFT`El archivo se cubrirá con mayor profundidad más adelante, nuestro enfoque actual sigue en comprender estas marcas de tiempo.

Estas marcas de tiempo se alojan dentro del`$MFT`En dos atributos distintos:

- `$STANDARD_INFORMATION`
- `$FILE_NAME`

Las marcas de tiempo visibles en el explorador de archivos de Windows se derivan del`$STANDARD_INFORMATION`atributo.

### **Investigación de tiempo**

Identificación de instancias de manipulación de la marca de tiempo, comúnmente denominada tiempo ([T1070.006](https://attack.mitre.org/techniques/T1070/006/)), presenta un desafío formidable en forense digital. El tiempo de tiempo implica la alteración de las marcas de tiempo de archivo para ofuscar la secuencia de actividades de archivo. Esta táctica es empleada con frecuencia por varias herramientas, como se ilustra en la técnica Timestomp de Mitre ATT & CK.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_time3.png)

Cuando los adversarios manipulan los tiempos de creación de archivos o implementan herramientas para tales fines, la marca de tiempo que se muestra en el explorador de archivos sufre modificación.

Por ejemplo, si cargamos`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`en`MFT Explorer`(disponible en`C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\MFTExplorer`) Notaremos que el tiempo de creación del archivo`ChangedFileTime.txt`ha sido manipulado, mostrando`03-01-2022`En el Explorador de archivos, que se desvía del tiempo de creación real.

**Nota**: `MFT Explorer`Tomará 15-25 minutos para cargar el archivo.

![](https://academy.hackthebox.com/storage/modules/237/img1.png)

Sin embargo, dado nuestro conocimiento de que las marcas de tiempo en el explorador de archivos se originan en el`$STANDARD_INFORMATION`atributo, podemos verificar cruzados estos datos con las marcas de tiempo de la`$FILE_NAME`atribuir`MFTEcmd`(disponible en`C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6`) como sigue.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT' --de 0x16169
MFTECmd version 1.2.2.1

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/MFTECmd

Command line: -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT --de 0x16169Warning: Administrator privileges not found!

File type: Mft

Processed C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT in 3.4924 secondsC:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT: FILE records found: 93,615 (Free records: 287) File size: 91.8MBDumping details for file record with key 00016169-00000004

Entry-seq #: 0x16169-0x4, Offset: 0x585A400, Flags: InUse, Log seq #: 0xCC5FB25, Base Record entry-seq: 0x0-0x0Reference count: 0x2, FixUp Data Expected: 04-00, FixUp Data Actual: 00-00 | 00-00 (FixUp OK: True)

**** STANDARD INFO ****
  Attribute #: 0x0, Size: 0x60, Content size: 0x48, Name size: 0x0, ContentOffset 0x18. Resident: True  Flags: Archive, Max Version: 0x0, Flags 2: None, Class Id: 0x0, Owner Id: 0x0, Security Id: 0x557, Quota charged: 0x0, Update sequence #: 0x8B71F8  Created On:         2022-01-03 16:54:25.2726453
  Modified On:        2023-09-07 08:30:12.4258743
  Record Modified On: 2023-09-07 08:30:12.4565632
  Last Accessed On:   2023-09-07 08:30:12.4258743

**** FILE NAME ****
  Attribute #: 0x3, Size: 0x78, Content size: 0x5A, Name size: 0x0, ContentOffset 0x18. Resident: True  File name: CHANGE~1.TXT
  Flags: Archive, Name Type: Dos, Reparse Value: 0x0, Physical Size: 0x0, Logical Size: 0x0
  Parent Entry-seq #: 0x16947-0x2  Created On:         2023-09-07 08:30:12.4258743
  Modified On:        2023-09-07 08:30:12.4258743
  Record Modified On: 2023-09-07 08:30:12.4258743
  Last Accessed On:   2023-09-07 08:30:12.4258743

**** FILE NAME ****
  Attribute #: 0x2, Size: 0x80, Content size: 0x68, Name size: 0x0, ContentOffset 0x18. Resident: True  File name: ChangedFileTime.txt
  Flags: Archive, Name Type: Windows, Reparse Value: 0x0, Physical Size: 0x0, Logical Size: 0x0
  Parent Entry-seq #: 0x16947-0x2  Created On:         2023-09-07 08:30:12.4258743
  Modified On:        2023-09-07 08:30:12.4258743
  Record Modified On: 2023-09-07 08:30:12.4258743
  Last Accessed On:   2023-09-07 08:30:12.4258743

**** DATA ****
  Attribute #: 0x1, Size: 0x18, Content size: 0x0, Name size: 0x0, ContentOffset 0x18. Resident: True  Resident Data

  Data:

    ASCII:
    UNICODE:

```

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_time5.png)

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_time6.png)

En los sistemas estándar de archivos de Windows como NTFS, los usuarios regulares generalmente carecen de los permisos para modificar directamente las marcas de tiempo de los nombres de archivo en`$FILE_NAME`. Dichas modificaciones están exclusivamente dentro del alcance del núcleo del sistema.

Para iniciar nuestra exploración, primero nos familiaricemos con los artefactos basados en el sistema de archivos. Comenzaremos con el`$MFT`Archivo, ubicado en el directorio raíz de la salida de KAPE.

### **Archivo mft**

El`$MFT`archivo, comúnmente conocido como el[Tabla de archivos maestros](https://learn.microsoft.com/en-us/windows/win32/fileio/master-file-table), es una parte integral del NTFS (nuevo sistema de archivos de tecnología) utilizada por los sistemas operativos de Windows contemporáneos. Este archivo es fundamental para organizar y catalogar archivos y directorios en un volumen NTFS. Cada archivo y directorio en dicho volumen tiene una entrada correspondiente en la tabla de archivos maestros. Piense en el MFT como una base de datos integral, documentando meticulosamente metadatos y detalles estructurales sobre cada archivo y directorio.

Para aquellos en el reino de los forenses digitales, el`$MFT`es un tesoro de información. Ofrece un registro granular de actividades de archivo y directorio en el sistema, que abarca acciones como creación de archivos, modificación, eliminación y acceso. Aprovechando el`$MFT`, los analistas forenses pueden reconstruir una línea de tiempo detallada de los eventos del sistema y las interacciones del usuario.

**Nota**: Una característica destacada del MFT es su capacidad para retener metadatos sobre archivos y directorios, incluso publicar su eliminación del sistema de archivos. Este rasgo eleva la importancia de la MFT en el análisis forense y la recuperación de datos.

El MFT se coloca estratégicamente en la raíz de la unidad del sistema.

Ya hemos extraído el MFT mientras mostramos las capacidades de Kape y lo guardamos en`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`.

Vamos a navegar al`$MFT`Archivo dentro del directorio de salida de KAPE anterior y cargándolo en`MFT Explorer`. Esta herramienta, una de las obras maestras de Eric Zimmerman, nos permite inspeccionar y analizar los metadatos ubicados en el MFT. Esto abarca una gran cantidad de información sobre archivos y directorios, desde nombres de archivo y marcas de tiempo (creadas, modificadas, accedidas) hasta tamaños de archivos, permisos y atributos. Una característica destacada del MFT Explorer es su interfaz intuitiva, que presenta registros de archivos en una jerarquía gráfica que recuerda al familiar Explorer de Windows.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_exp1_.png)

**Nota**: Vale la pena señalar que los registros de MFT, una vez creados, no se descartan. En cambio, a medida que surgen nuevos archivos y directorios, se agregan nuevos registros al MFT. Los registros correspondientes a los archivos eliminados se marcan como "gratis" y están listos para la reutilización.

### **Estructura del registro de archivos MFT**

Cada archivo o directorio en un volumen NTFS se simboliza por un registro en el MFT. Estos registros se adhieren a un formato estructurado, lleno de atributos y detalles sobre el archivo o directorio asociado. Comprar la estructura de la MFT es fundamental para tareas como el análisis forense, la gestión del sistema y la recuperación de datos en los ecosistemas de Windows. Equipa a los expertos forenses para determinar qué atributos están llenos de ideas intrigantes.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_str1.png)

Aquí hay una instantánea de los componentes:

- `File Record Header`: Contiene metadatos sobre el registro del archivo en sí. Incluye campos como firma, número de secuencia y otros datos administrativos.
- `Standard Information Attribute Header`: Almacena metadatos de archivo estándar, como marcas de tiempo, atributos de archivo e identificadores de seguridad.
- `File Name Attribute Header`: Contiene información sobre el nombre de archivo, incluida su longitud, espacio de nombres y caracteres Unicode.
- `Data Attribute Header`: Describe el atributo de datos de archivo, que puede ser`resident`(almacenado dentro del registro MFT) o`non-resident`(almacenado en grupos externos).
    - `File Data (File content)`: Esta sección contiene los datos reales del archivo, que pueden ser el contenido del archivo o las referencias a los grupos de datos no residentes. Para archivos pequeños (menos de 512 bytes), los datos pueden almacenarse dentro del registro MFT (`resident`). Para archivos más grandes, se hace referencia`non-resident`Registros de datos en el disco. Veremos un ejemplo de esto más adelante.
- `Additional Attributes (optional)`: NTFS admite varios atributos adicionales, como descriptores de seguridad (SD), IDS de objeto (OID), nombre de volumen (Volname), información de índice y más.

Estos atributos pueden variar según las características del archivo. Podemos ver el tipo de información común que se almacena dentro de estos encabezados y atributos en la imagen a continuación.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_str2.png)

### **Encabezado de registro de archivo**

Contiene metadatos sobre el registro del archivo en sí. Incluye campos como firma, número de secuencia y otros datos administrativos.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_str3.png)

El registro del archivo comienza con un encabezado que contiene metadatos sobre el registro del archivo en sí. Este encabezado generalmente incluye la siguiente información:

- `Signature`: Una firma de cuatro bytes, generalmente "archivo" o "baad", que indica si el registro está en uso o ha sido desasignado.
- `Offset to Update Sequence Array`: Una compensación de la matriz de secuencia de actualización (EE. UU.) Que ayuda a mantener la integridad del registro durante las actualizaciones.
- `Size of Update Sequence Array`: El tamaño de la matriz de secuencia de actualización en palabras.
- `Log File Sequence Number`: Un número que identifica la última actualización del registro del archivo.
- `Sequence Number`: Un número que identifica el registro del archivo. Los registros MFT están numerados secuencialmente, a partir de 0.
- `Hard Link Count`: El número de enlaces duros al archivo. Esto indica cuántas entradas de directorio apuntan a este registro de archivo.
- `Offset to First Attribute`: Un desplazamiento al primer atributo en el registro del archivo.

Cuando examinamos el archivo MFT usando`MFTECmd`y extraer detalles sobre un registro, la información del registro del archivo se presenta como se muestra en la captura de pantalla posterior.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT' --de 27142
MFTECmd version 1.2.2.1

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/MFTECmd

Command line: -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT --de 27142Warning: Administrator privileges not found!

File type: Mft

Processed C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT in 3.2444 secondsC:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT: FILE records found: 93,615 (Free records: 287) File size: 91.8MBDumping details for file record with key 00006A06-00000005

Entry-seq #: 0x6A06-0x5, Offset: 0x1A81800, Flags: InUse, Log seq #: 0xCC64595, Base Record entry-seq: 0x0-0x0Reference count: 0x1, FixUp Data Expected: 03-00, FixUp Data Actual: 6F-65 | 00-00 (FixUp OK: True)

**** STANDARD INFO ****
  Attribute #: 0x0, Size: 0x60, Content size: 0x48, Name size: 0x0, ContentOffset 0x18. Resident: True  Flags: Archive, Max Version: 0x0, Flags 2: None, Class Id: 0x0, Owner Id: 0x0, Security Id: 0x557, Quota charged: 0x0, Update sequence #: 0x8B8778  Created On:         2023-09-07 08:30:26.8316176
  Modified On:        2023-09-07 08:30:26.9097759
  Record Modified On: 2023-09-07 08:30:26.9097759
  Last Accessed On:   2023-09-07 08:30:26.9097759

**** FILE NAME ****
  Attribute #: 0x2, Size: 0x70, Content size: 0x54, Name size: 0x0, ContentOffset 0x18. Resident: True  File name: users.txt
  Flags: Archive, Name Type: DosWindows, Reparse Value: 0x0, Physical Size: 0x0, Logical Size: 0x0
  Parent Entry-seq #: 0x16947-0x2  Created On:         2023-09-07 08:30:26.8316176
  Modified On:        2023-09-07 08:30:26.8316176
  Record Modified On: 2023-09-07 08:30:26.8316176
  Last Accessed On:   2023-09-07 08:30:26.8316176

**** DATA ****
  Attribute #: 0x1, Size: 0x150, Content size: 0x133, Name size: 0x0, ContentOffset 0x18. Resident: True  Resident Data

  Data: 0D-0A-55-73-65-72-20-61-63-63-6F-75-6E-74-73-20-66-6F-72-20-5C-5C-48-54-42-56-4D-30-31-0D-0A-0D-0A-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-2D-0D-0A-41-64-6D-69-6E-69-73-74-72-61-74-6F-72-20-20-20-20-20-20-20-20-20-20-20-20-62-61-63-6B-67-72-6F-75-6E-64-54-61-73-6B-20-20-20-20-20-20-20-20-20-20-20-44-65-66-61-75-6C-74-41-63-63-6F-75-6E-74-20-20-20-20-20-20-20-20-20-20-20-0D-0A-47-75-65-73-74-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-4A-6F-68-6E-20-44-6F-65-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-20-57-44-41-47-55-74-69-6C-69-74-79-41-63-63-6F-75-6E-74-20-20-20-20-20-20-20-0D-0A-54-68-65-20-63-6F-6D-6D-61-6E-64-20-63-6F-6D-70-6C-65-74-65-64-20-73-75-63-63-65-73-73-66-75-6C-6C-79-2E-0D-0A-0D-0A

    ASCII:
User accounts for \\HTBVM01

-------------------------------------------------------------------------------
Administrator            backgroundTask           DefaultAccount
Guest                    John Doe                 WDAGUtilityAccount
The command completed successfully.

    UNICODE: ????????????????????????????????????????????????????????????????+++++????????+++++???????+++++????++++++++++????++++++++?????????4+++?????????????????????

```

Cada atributo significa alguna información de entrada, identificada por tipo.

| **Tipo** | **Atributo** | **Descripción** |
| --- | --- | --- |
| 0x10 (16) | $ Standard_information | Información general: banderas, Mac Times, propietario e identificación de seguridad. |
| 0x20 (32) | $ Attribute_list | Puntos a otros atributos y una lista de atributos no residentes. |
| 0x30 (48) | $ File_name | Nombre del archivo - (unicode) y tiempos de Mac obsoletos |
| 0x40 (64) | $ Volumen_version | Información de volumen - NTFS V1.2 Solo y Windows NT, ya no se usa |
| 0x40 (64) | $ Object_id | Identificador único de 16B: para archivos o directorio (NTFS 3.0+; Windows 2000+) |
| 0x50 (80) | $ Security_descriptor | Lista de control de acceso del archivo y propiedades de seguridad |
| 0x60 (96) | $ Volumen_name | Nombre de volumen |
| 0x70 (112) | $ Volumen_información | Versión del sistema de archivos y otra información |
| 0x80 (128) | $ Datos | Contenido del archivo |
| 0x90 (144) | $ Index_root | Nodo raíz de un árbol índice |
| 0xa0 (160) | $ Index_allocation | Nodos de un árbol índice: con una raíz en $ index_root |
| 0xb0 (176) | $ Mapa de bits | Bitmap: para el archivo $ MFT y para los índices (directorios) |
| 0xc0 (192) | $ Symbolic_link | Información de enlace suave - (solo NTFS V1.2 y Windows NT) |
| 0xc0 (192) | $ REPARSE_POINT | Datos sobre un punto de reparación: utilizado para un enlace suave (NTFS 3.0+; Windows 2000+) |
| 0xd0 (208) | $ Ea_información | Se utiliza para la compatibilidad hacia atrás con aplicaciones OS/2 (HPFS) |
| 0xe0 (224) | $ Ea | Se utiliza para la compatibilidad hacia atrás con aplicaciones OS/2 (HPFS) |
| 0x100 (256) | $ Logged_utilidad_stream | Claves y otra información sobre atributos cifrados (NTFS 3.0+; Windows 2000+) |

Para desmitificar la estructura de un registro de archivo NTFS MFT, estamos aprovechando las capacidades de[Editor de disco activo@](https://www.disk-editor.org/index.html). Esta potente herramienta de edición de disco de Freeware está disponible en`C:\Program Files\LSoft Technologies\Active@ Disk Editor`y facilita la visualización y modificación de los datos de disco sin procesar, incluida la tabla de archivos maestros de un sistema NTFS. Las mismas ideas se pueden obtener de otras herramientas de análisis de MFT, como`MFT Explorer`.

Podemos echar un vistazo más de cerca al abrir`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`en`Active@ Disk Editor`y luego presionando`Inspect File Record`.

![](https://academy.hackthebox.com/storage/modules/237/img2.png)

![](https://academy.hackthebox.com/storage/modules/237/img3.png)

En Disk Editor, estamos al tanto de los datos sin procesar de las entradas MFT. Esto incluye una representación hexadecimal del registro MFT, completa con su encabezado y atributos.

**Bandera no residente**

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_str4.png)

Al analizar la entrada en`MFTECmd`, así es como aparece el encabezado de datos no residente.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_ecmd2_.png)

**Bandera residente**

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_str5.png)

Al analizar la entrada en`MFTECmd`, así es como aparece el encabezado de datos del residente.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_ecmd3.png)

### **Zona. Datos del identificador en el registro de archivos MFT**

El`Zone.Identifier`es un atributo de metadatos de archivo especializado en el sistema operativo Windows, lo que significa la zona de seguridad desde la cual se obtuvo un archivo. Es una parte integral del Servicio de Ejecución de Adjuntos de Windows (AES) y es fundamental para determinar cómo Windows procesa archivos adquiridos desde Internet u otros orígenes potencialmente no confiables.

Cuando se obtiene un archivo de Internet, Windows le asigna un identificador de zona (`ZoneId`). Este ZoneId, incrustado en los metadatos del archivo, significa la fuente o zona de seguridad del origen del archivo. Por ejemplo, los archivos de origen internet generalmente tienen un`ZoneId`de`3`, denota la zona de Internet.

Por ejemplo, descargamos varias herramientas dentro del`C:\Users\johndoe\Downloads`directorio del objetivo de esta sección. Después de la carga, un`ZoneID`repleto de la zona. Identifier (es decir, la URL de origen) se les ha asignado.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Downloads> Get-Item * -Stream Zone.Identifier -ErrorAction SilentlyContinue

PSPath        : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads\Autoruns.zip:Zone.Identifier
PSParentPath  : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads
PSChildName   : Autoruns.zip:Zone.Identifier
PSDrive       : C
PSProvider    : Microsoft.PowerShell.Core\FileSystem
PSIsContainer : False
FileName      : C:\Users\johndoe\Downloads\Autoruns.zip
Stream        : Zone.Identifier
Length        : 130

PSPath        : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads\chainsaw_all_platforms+rules+examples.
                zip:Zone.Identifier
PSParentPath  : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads
PSChildName   : chainsaw_all_platforms+rules+examples.zip:Zone.Identifier
PSDrive       : C
PSProvider    : Microsoft.PowerShell.Core\FileSystem
PSIsContainer : False
FileName      : C:\Users\johndoe\Downloads\chainsaw_all_platforms+rules+examples.zip
Stream        : Zone.Identifier
Length        : 679

PSPath        : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads\disable-defender.ps1:Zone.Identifier
PSParentPath  : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads
PSChildName   : disable-defender.ps1:Zone.Identifier
PSDrive       : C
PSProvider    : Microsoft.PowerShell.Core\FileSystem
PSIsContainer : False
FileName      : C:\Users\johndoe\Downloads\disable-defender.ps1
Stream        : Zone.Identifier
Length        : 55

PSPath        : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads\USN-Journal-Parser-master.zip:Zone.Ide
                ntifier
PSParentPath  : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads
PSChildName   : USN-Journal-Parser-master.zip:Zone.Identifier
PSDrive       : C
PSProvider    : Microsoft.PowerShell.Core\FileSystem
PSIsContainer : False
FileName      : C:\Users\johndoe\Downloads\USN-Journal-Parser-master.zip
Stream        : Zone.Identifier
Length        : 187

PSPath        : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads\volatility3-develop.zip:Zone.Identifie
                r
PSParentPath  : Microsoft.PowerShell.Core\FileSystem::C:\Users\johndoe\Downloads
PSChildName   : volatility3-develop.zip:Zone.Identifier
PSDrive       : C
PSProvider    : Microsoft.PowerShell.Core\FileSystem
PSIsContainer : False
FileName      : C:\Users\johndoe\Downloads\volatility3-develop.zip
Stream        : Zone.Identifier
Length        : 184

```

Para revelar el contenido de un`Zone.Identifier`Para un archivo, el siguiente comando se puede ejecutar en PowerShell.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Downloads> Get-Content * -Stream Zone.Identifier -ErrorAction SilentlyContinue
[ZoneTransfer]
ZoneId=3
ReferrerUrl=https://learn.microsoft.com/
HostUrl=https://download.sysinternals.com/files/Autoruns.zip
[ZoneTransfer]
ZoneId=3
ReferrerUrl=https://github.com/WithSecureLabs/chainsaw/releases
HostUrl=https://objects.githubusercontent.com/github-production-release-asset-2e65be/395658506/222c726c-0fe8-4a13-82c4-a4c9a45875c6?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20230813%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20230813T181953Z&X-Amz-Expires=300&X-Amz-Signature=0968cc87b63f171b60eb525362c11cb6463ac5681db50dbb7807cc5384fcb771&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=395658506&response-content-disposition=attachment%3B%20filename%3Dchainsaw_all_platforms%2Brules%2Bexamples.zip&response-content-type=application%2Foctet-stream
[ZoneTransfer]
ZoneId=3
HostUrl=https://github.com/
[ZoneTransfer]
ZoneId=3
ReferrerUrl=https://github.com/PoorBillionaire/USN-Journal-Parser
HostUrl=https://codeload.github.com/PoorBillionaire/USN-Journal-Parser/zip/refs/heads/master
[ZoneTransfer]
ZoneId=3
ReferrerUrl=https://github.com/volatilityfoundation/volatility3
HostUrl=https://codeload.github.com/volatilityfoundation/volatility3/zip/refs/heads/develop

```

Uno de los mecanismos de seguridad, conocidos como el`Mark of the Web` (`MotW`), depende del identificador de zona. Aquí, el marcador MOTW diferencia los archivos procedentes de Internet u otras fuentes potencialmente dudosas de las originarias de contextos confiables o locales. Con frecuencia se emplea para reforzar la seguridad de aplicaciones como Microsoft Word. Cuando una aplicación, por ejemplo, Microsoft Word, abre un archivo con un MOTW, puede instituir medidas de seguridad específicas basadas en la presencia de MOTW. Por ejemplo, se podría lanzar un documento de Word con un MOTW en`Protected View`, un modo restringido que aísla el documento del sistema más amplio, mitigando posibles amenazas de seguridad.

Si bien su función principal es reforzar la seguridad para los archivos descargados desde la web, los analistas forenses pueden aprovecharla para actividades de investigación. Al examinar este atributo, pueden determinar el método de descarga del archivo. Vea un ejemplo a continuación.

![](https://academy.hackthebox.com/storage/modules/237/img4.png)

### **Análisis con Timeline Explorer**

`Timeline Explorer`es otra herramienta forense digital desarrollada por Eric Zimmerman que se utiliza para ayudar a analistas forenses e investigadores a crear y analizar artefactos de línea de tiempo de varias fuentes. Los artefactos de la línea de tiempo proporcionan una visión cronológica de los eventos y actividades del sistema, lo que facilita la reconstrucción de una secuencia de eventos durante una investigación. Podemos filtrar datos de línea de tiempo basados en criterios específicos, como rangos de fecha y hora, tipos de eventos, palabras clave y más. Esta característica ayuda a enfocar la investigación en la información relevante.

Este arreglo de diferentes eventos siguientes uno tras otro a tiempo es realmente útil para crear una historia o línea de tiempo sobre lo que sucedió antes y después de eventos específicos. Esta secuencia de eventos ayuda a establecer una línea de tiempo de actividades en un sistema.

Cargar un archivo CSV convertido en Timeline Explorer es un proceso sencillo. Timeline Explorer está diseñado para funcionar con los datos de la línea de tiempo, incluidos los archivos CSV que contienen eventos o actividades de tiempo de tiempo. Para cargar el archivo CSV de datos de eventos en el Explorador de línea de tiempo, podemos iniciar Timeline Explorer y simplemente arrastrar y soltar desde su ubicación (por ejemplo, nuestro directorio de análisis KAPE) en la ventana Explorer de línea de tiempo.

Una vez ingerido, Timeline Explorer procesará y mostrará los datos. La duración de este proceso depende del tamaño del archivo.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_winevt8.png)

Veremos la línea de tiempo poblada con los eventos del archivo CSV en orden cronológico. Con los datos de la línea de tiempo ahora cargados, podemos explorar y analizar los eventos utilizando las diversas características proporcionadas por Timeline Explorer. Podemos ampliar los rangos de tiempo específicos, filtrar eventos, buscar palabras clave y correlacionar actividades relacionadas.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_winevt9.png)

Proporcionaremos múltiples ejemplos del uso de Timeline Explorer en esta sección.

### **Revista USN**

`USN`, o`Update Sequence Number`, es un componente vital del sistema de archivos NTFS en Windows. La revista USN es esencialmente una característica de revista de cambio que registra meticulosamente las alteraciones a archivos y directorios en un volumen NTFS.

Para aquellos en forense digital, el USN Journal es una mina de oro. Nos permite monitorear las operaciones como la creación de archivos, el cambio de nombre, la eliminación y la sobrescritura de datos.

En el entorno de Windows, el archivo de revista USN se designa como`$J`. El directorio de salida de KAPE alberga la revista USN recolectada en el siguiente directorio:`<KAPE_output_folder>\<Drive>\$Extend`

Así es como se ve en la producción de nuestra Kape (`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend`)

![](https://academy.hackthebox.com/storage/modules/237/img5.png)

### **Análisis de la revista USN utilizando mftecmd**

Anteriormente utilizamos`MFTECmd`, una de las herramientas de Eric Zimmerman, para analizar el archivo MFT. Si bien su enfoque principal es el MFT, MFTECMD también puede ser fundamental para analizar la revista USN. Esto se debe a que las entradas en la revista USN a menudo aluden a modificaciones a archivos y directorios que se documentan en el MFT. Por lo tanto, emplearemos esta herramienta para diseccionar la revista USN.

Para facilitar el análisis de la revista USN utilizando`MFTECmd`, ejecute un comando similar al siguiente:

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J' --csv C:\Users\johndoe\Desktop\forensic_data\mft_analysis\ --csvf MFT-J.csv
MFTECmd version 1.2.2.1

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/MFTECmd

Command line: -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J --csv C:\Users\johndoe\Desktop\forensic_data\mft_analysis\ --csvf MFT-J.csvWarning: Administrator privileges not found!

File type: UsnJournal

Processed C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J in 0.1675 secondsUsn entries found in C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J: 89,704        CSV output will be saved to C:\Users\johndoe\Desktop\forensic_data\mft_analysis\MFT-J.csv

```

El archivo de salida resultante se guarda como`MFT-J.csv`dentro del`C:\Users\johndoe\Desktop\forensic_data\mft_analysis`directorio. Importémoslo en`Timeline Explorer`(disponible en`C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\TimelineExplorer`).

**Nota**: Elimine el filtro en el número de entrada para ver la imagen completa.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_usn2.png)

Tras la inspección, podemos discernir una línea de tiempo de eventos ordenada cronológicamente. En particular, la entrada para`uninstall.exe`es evidente.

Aplicando un filtro en el número de entrada`93866`, que corresponde al`Entry ID`para`uninstall.exe`, podemos obtener la naturaleza de las modificaciones ejecutadas en este archivo específico.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_usn3.png)

La extensión del archivo,`.crdownload`, es indicativo de un archivo parcialmente descargado. Este tipo de archivo generalmente se genera al descargar contenido a través de navegadores como Microsoft Edge, Google Chrome o Chromium. Esta revelación es intrigante. Si el archivo se descargó a través de un navegador, es plausible que el`Zone.Identifier`podría revelar la fuente IP/dominio de su origen.

Para investigar esta suposición, deberíamos:

1. Crear un archivo CSV para`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT`usando`MFTECmd`Como lo hicimos por`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$Extend\$J`.
2. Importar el CSV relacionado con $ MFT en`Timeline Explorer`.
3. Aplicar un filtro en el número de entrada`93866`.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\MFTECmd.exe -f 'C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT' --csv C:\Users\johndoe\Desktop\forensic_data\mft_analysis\ --csvf MFT.csv
MFTECmd version 1.2.2.1

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/MFTECmd

Command line: -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT --csv C:\Users\johndoe\Desktop\forensic_data\mft_analysis\ --csvf MFT.csvWarning: Administrator privileges not found!

File type: Mft

Processed C:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT in 3.5882 secondsC:\Users\johndoe\Desktop\forensic_data\kape_output\D\$MFT: FILE records found: 93,615 (Free records: 287) File size: 91.8MB        CSV output will be saved to C:\Users\johndoe\Desktop\forensic_data\mft_analysis\MFT.csv

```

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_mft_ecmd5_.png)

### **Investigación de registros de eventos de Windows**

Probar en los registros de eventos de Windows es primordial en forense digital y respuesta a incidentes. Estos registros son repositorios de datos invaluables, actividades del sistema de crónica, comportamientos de los usuarios e incidentes de seguridad en una máquina de Windows. Cuando se ejecuta a Kape, duplica los registros de eventos originales, asegurando que su estado virgen se preserve como evidencia. El directorio de salida de KAPE alberga estos registros de eventos en el siguiente directorio:`<KAPE_output_folder>\Windows\System32\winevt\logs`

![](https://academy.hackthebox.com/storage/modules/237/img6.png)

Este directorio está poblado con`.evtx`Archivos, encapsulando una miríada de registros de eventos de Windows, incluidos, entre otros, seguridad, aplicación, sistema y sysmon (si se activa).

Nuestra misión es examinar estos registros de eventos, en la búsqueda de anomalías, patrones o indicadores de compromiso (COI). Como detectives forenses, debemos estar atentos, prestando atención a las identificaciones de eventos, marcas de tiempo, IPS de origen, nombres de usuario y otros detalles de registro pertinentes. Una gran cantidad de utilidades y scripts forenses, como herramientas de análisis de registros y sistemas SIEM, puede reforzar nuestro análisis. Es imperativo identificar las tácticas, las técnicas y los procedimientos (TTP) evidentes en cualquier actividad dudosa. Esto podría implicar profundizar en patrones de ataque conocidos y firmas de malware. Otro paso crucial es correlacionar los eventos de diversas fuentes de registro, creando una línea de tiempo integral de los eventos. Esta visión holística ayuda a reconstruir la secuencia de eventos.

El análisis de los registros de eventos de Windows se ha abordado en los módulos titulados`Windows Event Logs & Finding Evil`y`YARA & Sigma for SOC Analysts`.

### **Los registros de eventos de Windows analizan con EVTXECMD (EZ-Tool)**

`EvtxECmd`(disponible en`C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd`) es otra creación de Eric Zimmerman, adaptada para los archivos de registro de eventos de Windows (archivos EVTX). Con esta herramienta a nuestra disposición, podemos extraer registros de eventos específicos o una gama de eventos de un archivo EVTX, convirtiéndolos en formatos más digeribles como JSON, XML o CSV.

Iniciemos el menú de ayuda de EVTXECMD para familiarizarnos con las diversas opciones. El comando para acceder a la sección de ayuda es la siguiente.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd> .\EvtxECmd.exe -h
Description:
  EvtxECmd version 1.5.0.0

  Author: Eric Zimmerman (saericzimmerman@gmail.com)
  https://github.com/EricZimmerman/evtx

  Examples: EvtxECmd.exe -f "C:\Temp\Application.evtx" --csv "c:\temp\out" --csvf MyOutputFile.csv
            EvtxECmd.exe -f "C:\Temp\Application.evtx" --csv "c:\temp\out"
            EvtxECmd.exe -f "C:\Temp\Application.evtx" --json "c:\temp\jsonout"

            Short options (single letter) are prefixed with a single dash. Long commands are prefixed with two dashes

Usage:
  EvtxECmd [options]

Options:
  -f <f>           File to process. This or -d is required
  -d <d>           Directory to process that contains evtx files. This or -f is required
  --csv <csv>      Directory to save CSV formatted results to
  --csvf <csvf>    File name to save CSV formatted results to. When present, overrides default name
  --json <json>    Directory to save JSON formatted results to
  --jsonf <jsonf>  File name to save JSON formatted results to. When present, overrides default name
  --xml <xml>      Directory to save XML formatted results to
  --xmlf <xmlf>    File name to save XML formatted results to. When present, overrides default name
  --dt <dt>        The custom date/time format to use when displaying time stamps [default: yyyy-MM-dd HH:mm:ss.fffffff]
  --inc <inc>      List of Event IDs to process. All others are ignored. Overrides --exc Format is 4624,4625,5410
  --exc <exc>      List of Event IDs to IGNORE. All others are included. Format is 4624,4625,5410
  --sd <sd>        Start date for including events (UTC). Anything OLDER than this is dropped. Format should match --dt
  --ed <ed>        End date for including events (UTC). Anything NEWER than this is dropped. Format should match --dt
  --fj             When true, export all available data when using --json [default: False]
  --tdt <tdt>      The number of seconds to use for time discrepancy detection [default: 1]
  --met            When true, show metrics about processed event log [default: True]
  --maps <maps>    The path where event maps are located. Defaults to 'Maps' folder where program was executed
                   [default: C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd\Maps]
  --vss            Process all Volume Shadow Copies that exist on drive specified by -f or -d [default: False]
  --dedupe         Deduplicate -f or -d & VSCs based on SHA-1. First file found wins [default: True]
  --sync           If true, the latest maps from https://github.com/EricZimmerman/evtx/tree/master/evtx/Maps are
                   downloaded and local maps updated [default: False]
  --debug          Show debug information during processing [default: False]
  --trace          Show trace information during processing [default: False]
  --version        Show version information
  -?, -h, --help   Show help and usage information

```

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_winevt4_.png)

### **Mapas en evtxecmd**

Mapas en`EvtxECmd`son fundamentales. Metamorfose los datos personalizados en campos estandarizados en los datos de CSV (y JSON). Esta granularidad y precisión son indispensables en investigaciones forenses, lo que permite a los analistas interpretar y extraer información sobresaliente de los registros de eventos de Windows con delicadeza.

Campos estandarizados en mapas:

- `UserName`: Contiene información sobre el usuario y/o dominio que se encuentra en varios registros de eventos
- `ExecutableInfo`: Contiene información sobre la línea de comandos de proceso, tareas programadas, etc.
- `PayloadData1,2,3,4,5,6`: Campos adicionales para extraer y poner datos contextuales de registros de eventos
- `RemoteHost`: Contiene información sobre la dirección IP

`EvtxECmd`juega un papel importante en:

- Convertir la parte única de un evento, conocida como EventData, en un formato más estandarizado y legible por humanos.
- Asegurar que los archivos de mapa se adapten a registros de eventos específicos, como seguridad, aplicación o registros personalizados, para manejar las diferencias en las estructuras y datos de eventos.
- Utilizando un identificador único, el elemento del canal, para especificar para qué registro de eventos está diseñado un archivo de mapa en particular, evitando la confusión cuando las ID de eventos se reutilizan en diferentes registros.

Para garantizar que los mapas más recientes estén en su lugar antes de convertir los archivos EVTX en CSV/JSON, emplee el comando a continuación.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd> .\EvtxECmd.exe --sync
EvtxECmd version 1.5.0.0

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/evtx

Checking for updated maps at https://github.com/EricZimmerman/evtx/tree/master/evtx/Maps...

Updates found!

New maps
Application_ESENT_216
CiscoSecureEndpoint-Events_CiscoSecureEndpoint_100
CiscoSecureEndpoint-Events_CiscoSecureEndpoint_1300
CiscoSecureEndpoint-Events_CiscoSecureEndpoint_1310
Kaspersky-Security_OnDemandScan_3023
Kaspersky-Security_Real-Time_File_Protection_3023
Microsoft-Windows-Hyper-V-VMMS-Admin_Microsoft-Windows-Hyper-V-VMMS_13002
Microsoft-Windows-Hyper-V-VMMS-Admin_Microsoft-Windows-Hyper-V-VMMS_18304
Microsoft-Windows-Hyper-V-VMMS-Admin_Microsoft-Windows-Hyper-V-Worker_13003
Microsoft-Windows-Hyper-V-Worker-Admin_Microsoft-Windows-Hyper-V-Worker_18303
Microsoft-Windows-Hyper-V-Worker-Admin_Microsoft-Windows-Hyper-V-Worker_18504
Microsoft-Windows-Hyper-V-Worker-Admin_Microsoft-Windows-Hyper-V-Worker_18512
Microsoft-Windows-Windows-Defender-Operational_Microsoft-Windows-Windows-Defender_2050
PowerShellCore-Operational_PowerShellCore_4104
Security_Microsoft-Windows-Security-Auditing_6272
Security_Microsoft-Windows-Security-Auditing_6273

Updated maps
Microsoft-Windows-Hyper-V-Worker-Admin_Microsoft-Windows-Hyper-V-Worker_18500
Microsoft-Windows-Hyper-V-Worker-Admin_Microsoft-Windows-Hyper-V-Worker_18502
Microsoft-Windows-Hyper-V-Worker-Admin_Microsoft-Windows-Hyper-V-Worker_18508
Microsoft-Windows-Hyper-V-Worker-Admin_Microsoft-Windows-Hyper-V-Worker_18514
Microsoft-Windows-SMBServer-Security_Microsoft-Windows-SMBServer_551
Security_Microsoft-Windows-Security-Auditing_4616

```

Con los últimos mapas integrados, estamos equipados para infundir información contextual en campos distintos, simplificando el proceso de análisis de registro. Ahora, es hora de transmutar los registros en un formato que sea más sabroso.

Para hacer que los archivos EVTX sean más accesibles, podemos emplear`EvtxECmd`Para convertir perfectamente los archivos de registro de eventos en formatos fáciles de usar como JSON o CSV.

Por ejemplo, el siguiente comando facilita la conversión del`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx`Archivo a un archivo CSV:

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd> .\EvtxECmd.exe -f "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx" --csv "C:\Users\johndoe\Desktop\forensic_data\event_logs\csv_timeline" --csvf kape_event_log.csv
EvtxECmd version 1.5.0.0

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/evtx

Command line: -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx --csv C:\Users\johndoe\Desktop\forensic_data\event_logs\csv_timeline --csvf kape_event_log.csv

Warning: Administrator privileges not found!

CSV output will be saved to C:\Users\johndoe\Desktop\forensic_data\event_logs\csv_timeline\kape_event_log.csv

Processing C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx...

Event log details
Flags: None
Chunk count: 28
Stored/Calculated CRC: 3EF9F1C/3EF9F1C
Earliest timestamp: 2023-09-07 08:23:18.4430130
Latest timestamp:   2023-09-07 08:33:00.0069805
Total event log records found: 1,920

Records included: 1,920 Errors: 0 Events dropped: 0

Metrics (including dropped events)
Event ID        Count
1               95
2               76
3               346
4               1
8               44
10              6
11              321
12              674
13              356
16              1

Processed 1 file in 8.7664 seconds

```

Después de importar el CSV resultante en`Timeline Explorer`, deberíamos ver lo siguiente.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_winevt6.png)

**Información ejecutable**:

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_winevt7.png)

### **Investigar los registros de eventos de Windows con EQL**

[Lenguaje de consultas de eventos de Endgame (EQL)](https://github.com/endgameinc/eqllib)es una herramienta indispensable para examinar los registros de eventos, identificar posibles amenazas de seguridad y descubrir actividades sospechosas en los sistemas de Windows. EQL ofrece un lenguaje estructurado que facilita la consulta y la correlación de eventos en múltiples fuentes de registro, incluidos los registros de eventos de Windows.

Actualmente, el módulo EQL es compatible con las versiones de Python 2.7 y 3.5+. Si tiene instalada una versión de Python compatible, ejecute el siguiente comando.

Herramientas rápidas de examen y análisis

```
C:\Users\johndoe>pip install eql

```

Si Python se configura y se incluye correctamente en su ruta, se debe acceder a EQL. Para verificar esto, ejecute el comando a continuación.

Herramientas rápidas de examen y análisis

```
C:\Users\johndoe>eql --version
eql 0.9.18

```

Dentro del repositorio de EQL (disponible en`C:\Users\johndoe\Desktop\eqllib-master`), hay un módulo de PowerShell que se remonta con funciones esenciales adaptadas para analizar eventos Sysmon de los registros de eventos de Windows. Este módulo reside en el`utils`directorio de`eqllib`, y se nombra`scrape-events.ps1`.

Desde el directorio EQL, inicie el módulo shrape-events.ps1 con el siguiente comando:

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\eqllib-master\utils> import-module .\scrape-events.ps1

```

Al hacerlo, activamos el`Get-EventProps`función, que es fundamental para analizar las propiedades del evento de los registros de Sysmon. Para transformar, por ejemplo,`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx`En un formato JSON adecuado para consultas EQL, ejecute el comando a continuación.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\eqllib-master\utils> Get-WinEvent -Path C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\winevt\logs\Microsoft-Windows-Sysmon%4Operational.evtx -Oldest | Get-EventProps | ConvertTo-Json | Out-File -Encoding ASCII -FilePath C:\Users\johndoe\Desktop\forensic_data\event_logs\eql_format_json\eql-sysmon-data-kape.json

```

Esta acción producirá un archivo JSON, preparado para consultas EQL.

Veamos ahora cómo podríamos haber identificado la enumeración del usuario/grupo a través de una consulta EQL contra el archivo JSON que creamos.

Herramientas rápidas de examen y análisis

```
C:\Users\johndoe>eql query -f C:\Users\johndoe\Desktop\forensic_data\event_logs\eql_format_json\eql-sysmon-data-kape.json "EventId=1 and (Image='*net.exe' and (wildcard(CommandLine, '* user*', '*localgroup *', '*group *')))"
{"CommandLine": "net  localgroup \"Remote Desktop Users\" backgroundTask /add", "Company": "Microsoft Corporation", "CurrentDirectory": "C:\\Temp\\", "Description": "Net Command", "EventId": 1, "FileVersion": "10.0.19041.1 (WinBuild.160101.0800)", "Hashes": "MD5=0BD94A338EEA5A4E1F2830AE326E6D19,SHA256=9F376759BCBCD705F726460FC4A7E2B07F310F52BAA73CAAAAA124FDDBDF993E,IMPHASH=57F0C47AE2A1A2C06C8B987372AB0B07", "Image": "C:\\Windows\\System32\\net.exe", "IntegrityLevel": "High", "LogonGuid": "{b5ae2bdd-9f94-64ec-0000-002087490200}", "LogonId": "0x24987", "ParentCommandLine": "C:\\Windows\\system32\\cmd.exe /c install.bat", "ParentImage": "C:\\Windows\\System32\\cmd.exe", "ParentProcessGuid": "{b5ae2bdd-8a0f-64f9-0000-00104cfc4700}", "ParentProcessId": "6540", "ProcessGuid": "{b5ae2bdd-8a14-64f9-0000-0010e8804800}", "ProcessId": "3808", "Product": "Microsoft? Windows? Operating System", "RuleName": null, "TerminalSessionId": "1", "User": "HTBVM01\\John Doe", "UtcTime": "2023-09-07 08:30:12.178"}
{"CommandLine": "net  users  ", "Company": "Microsoft Corporation", "CurrentDirectory": "C:\\Temp\\", "Description": "Net Command", "EventId": 1, "FileVersion": "10.0.19041.1 (WinBuild.160101.0800)", "Hashes": "MD5=0BD94A338EEA5A4E1F2830AE326E6D19,SHA256=9F376759BCBCD705F726460FC4A7E2B07F310F52BAA73CAAAAA124FDDBDF993E,IMPHASH=57F0C47AE2A1A2C06C8B987372AB0B07", "Image": "C:\\Windows\\System32\\net.exe", "IntegrityLevel": "High", "LogonGuid": "{b5ae2bdd-9f94-64ec-0000-002087490200}", "LogonId": "0x24987", "ParentCommandLine": "cmd.exe /c ping -n 10 127.0.0.1 > nul && net users > users.txt && net localgroup > groups.txt && ipconfig >ipinfo.txt && netstat -an >networkinfo.txt && del /F /Q C:\\Temp\\discord.exe", "ParentImage": "C:\\Windows\\System32\\cmd.exe", "ParentProcessGuid": "{b5ae2bdd-8a19-64f9-0000-0010c5914800}", "ParentProcessId": "4040", "ProcessGuid": "{b5ae2bdd-8a22-64f9-0000-0010c59f4800}", "ProcessId": "5364", "Product": "Microsoft? Windows? Operating System", "RuleName": null, "TerminalSessionId": "1", "User": "HTBVM01\\John Doe", "UtcTime": "2023-09-07 08:30:26.851"}
{"CommandLine": "net  localgroup  ", "Company": "Microsoft Corporation", "CurrentDirectory": "C:\\Temp\\", "Description": "Net Command", "EventId": 1, "FileVersion": "10.0.19041.1 (WinBuild.160101.0800)", "Hashes": "MD5=0BD94A338EEA5A4E1F2830AE326E6D19,SHA256=9F376759BCBCD705F726460FC4A7E2B07F310F52BAA73CAAAAA124FDDBDF993E,IMPHASH=57F0C47AE2A1A2C06C8B987372AB0B07", "Image": "C:\\Windows\\System32\\net.exe", "IntegrityLevel": "High", "LogonGuid": "{b5ae2bdd-9f94-64ec-0000-002087490200}", "LogonId": "0x24987", "ParentCommandLine": "cmd.exe /c ping -n 10 127.0.0.1 > nul && net users > users.txt && net localgroup > groups.txt && ipconfig >ipinfo.txt && netstat -an >networkinfo.txt && del /F /Q C:\\Temp\\discord.exe", "ParentImage": "C:\\Windows\\System32\\cmd.exe", "ParentProcessGuid": "{b5ae2bdd-8a19-64f9-0000-0010c5914800}", "ParentProcessId": "4040", "ProcessGuid": "{b5ae2bdd-8a22-64f9-0000-001057a24800}", "ProcessId": "4832", "Product": "Microsoft? Windows? Operating System", "RuleName": null, "TerminalSessionId": "1", "User": "HTBVM01\\John Doe", "UtcTime": "2023-09-07 08:30:26.925"}

```

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_winevt11.png)

### **Registro de Windows**

Una inmersión profunda en el registro colmenas puede proporcionarnos ideas invaluables, como el nombre de la computadora, la versión de Windows, el nombre del propietario y la configuración de red.

Los archivos relacionados con el registro cosechado de Kape generalmente se encuentran en`<KAPE_output_folder>\Windows\System32\config`

![](https://academy.hackthebox.com/storage/modules/237/img10.png)

Además, hay colmenas de registro específicas del usuario ubicadas dentro de directorios de usuarios individuales, como se ejemplifica en la siguiente captura de pantalla.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_registry1_.png)

Para un análisis exhaustivo, podemos emplear`Registry Explorer`(disponible en`C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\RegistryExplorer`) Una herramienta basada en la GUI ideada por Eric Zimmerman. Esta herramienta ofrece una interfaz simplificada para navegar y diseccionar el contenido de las colmenas del registro de Windows. Simplemente arrastrando y dejando caer estos archivos en Registry Explorer, la herramienta procesa los datos, presentándolo dentro de su GUI. El panel izquierdo muestra las colmenas del registro, mientras que el panel derecho revela sus valores correspondientes.

En la captura de pantalla a continuación hemos cargado el`SYSTEM`colmena, que se puede encontrar dentro del`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config`directorio del objetivo de esta sección.

![](https://academy.hackthebox.com/storage/modules/237/img11.png)

Registry Explorer cuenta con un conjunto de características, que incluyen análisis de colmena, capacidades de búsqueda, opciones de filtrado, visualización de marca de tiempo y marcadores. La utilidad de marcador es particularmente útil, lo que permite a los usuarios destinar ubicaciones o claves fundamentales para referencia posterior.

En la captura de pantalla a continuación hemos cargado el`SOFTWARE`colmena, que se puede encontrar dentro del`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config`directorio del objetivo de esta sección. Observe los marcadores disponibles en Registry Explorer.

![](https://academy.hackthebox.com/storage/modules/237/img12.png)

### **Retripador**

Otra herramienta potente en nuestro arsenal es`RegRipper`(disponible en`C:\Users\johndoe\Desktop\RegRipper3.0-master`), una utilidad de línea de comandos experto en extraer rápidamente información del registro.

Para familiarizarnos con las funcionalidades de Rehisper, invocemos la sección de ayuda ejecutando`rip.exe`acompañado por el`-h`parámetro.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -h
Rip v.3.0 - CLI RegRipper tool
Rip [-r Reg hive file] [-f profile] [-p plugin] [options]
Parse Windows Registry files, using either a single module, or a profile.

NOTE: This tool does NOT automatically process Registry transaction logs! The tool
does check to see if the hive is dirty, but does not automatically process the
transaction logs.  If you need to incorporate transaction logs, please consider
using yarp + registryFlush.py, or rla.exe from Eric Zimmerman.

  -r [hive] .........Registry hive file to parse
  -d ................Check to see if the hive is dirty
  -g ................Guess the hive file type
  -a ................Automatically run hive-specific plugins
  -aT ...............Automatically run hive-specific TLN plugins
  -f [profile].......use the profile
  -p [plugin]........use the plugin
  -l ................list all plugins
  -c ................Output plugin list in CSV format (use with -l)
  -s systemname......system name (TLN support)
  -u username........User name (TLN support)
  -uP ...............Update default profiles
  -h.................Help (print this information)

Ex: C:\>rip -r c:\case\system -f system
    C:\>rip -r c:\case\ntuser.dat -p userassist
    C:\>rip -r c:\case\ntuser.dat -a
    C:\>rip -l -c

All output goes to STDOUT; use redirection (ie, > or >>) to output to a file.

copyright 2020 Quantum Analytics Research, LLC

```

Para una experiencia perfecta con Regripper, es esencial familiarizarnos con sus complementos. Para enumerar todos los complementos disponibles y catalogarlos en un archivo CSV (por ejemplo,`rip_plugins.csv`), use el comando a continuación.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -l -c > rip_plugins.csv

```

Esta acción compila una lista completa de complementos, que detalla las colmenas asociadas y la guarda como un archivo CSV.

La captura de pantalla a continuación aclara el contenido de este archivo, destacando el nombre del complemento, su colmena de registro correspondiente y una breve descripción.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_regripper_5.png)

Para comenzar las cosas, ejecutemos el`compname`comando en la colmena del sistema (ubicado en`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config`), que recupera el nombre de la computadora.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SYSTEM" -p compname
Launching compname v.20090727
compname v.20090727
(System) Gets ComputerName and Hostname values from System hive

ComputerName    = HTBVM01
TCP/IP Hostname = HTBVM01

```

Veamos algunos ejemplos más contra diferentes colmenas.

**Zona horaria**

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SYSTEM" -p timezone
Launching timezone v.20200518
timezone v.20200518
(System) Get TimeZoneInformation key contents

TimeZoneInformation key
ControlSet001\Control\TimeZoneInformation
LastWrite Time 2023-08-28 23:03:03Z
  DaylightName   -> @tzres.dll,-211
  StandardName   -> @tzres.dll,-212
  Bias           -> 480 (8 hours)
  ActiveTimeBias -> 420 (7 hours)
  TimeZoneKeyName-> Pacific Standard Time

```

**Información de red**

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SYSTEM" -p nic2
Launching nic2 v.20200525
nic2 v.20200525
(System) Gets NIC info from System hive

Adapter: {50c7b4ab-b059-43f4-8b0f-919502abc934}
LastWrite Time: 2023-09-07 08:01:06Z
  EnableDHCP                   0
  Domain
  NameServer                   10.10.10.100
  DhcpServer                   255.255.255.255
  Lease                        1800
  LeaseObtainedTime            2023-09-07 07:58:03Z
  T1                           2023-09-07 08:13:03Z
  T2                           2023-09-07 08:24:18Z
  LeaseTerminatesTime          2023-09-07 08:28:03Z
  AddressType                  0
  IsServerNapAware             0
  DhcpConnForceBroadcastFlag   0
  DhcpInterfaceOptions         ├╝               ├Ä☻  w               ├Ä☻  /               ├Ä☻  .               ├Ä☻  ,               ├Ä☻  +               ├Ä☻  !               ├Ä☻  ▼               ├Ä☻  ♥               ├Ä☻  ☼               ├Ä☻  ♠               ├Ä☻  ☺               ├Ä☻  3               ├Ä☻  6               ├Ä☻  5               ├Ä☻
  DhcpGatewayHardware          ├Ç┬¿┬╢☻♠    PV├Ñ┬ó┬¥
  DhcpGatewayHardwareCount     1
  RegistrationEnabled          1
  RegisterAdapterName          0
  IPAddress                    10.10.10.11
  SubnetMask                   255.0.0.0
  DefaultGateway               10.10.10.100
  DefaultGatewayMetric         0

ControlSet001\Services\Tcpip\Parameters\Interfaces has no subkeys.
Adapter: {6c733e3b-de84-487a-a0bd-48b9d9ec7616}
LastWrite Time: 2023-09-07 08:01:06Z
  EnableDHCP                   1
  Domain
  NameServer
  RegistrationEnabled          1
  RegisterAdapterName          0

```

La misma información se puede extraer utilizando el`ips`complemento.

**Ejecución del instalador**

Herramientas rápidas de examen y análisis

```powershell
Microsoft\Windows\CurrentVersion\Installer\UserData not found.
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config\SOFTWARE" -p installer
Launching installer v.20200517
Launching installer v.20200517
(Software) Determines product install information

Installer
Microsoft\Windows\CurrentVersion\Installer\UserData

User SID: S-1-5-18
Key      : 01DCD275E2FC1D341815B89DCA09680D
LastWrite: 2023-08-28 09:39:56Z
20230828 - Microsoft Visual C++ 2019 X86 Additional Runtime - 14.28.29913 14.28.29913 (Microsoft Corporation)

Key      : 3367A02690A78A24580870A644384C0B
LastWrite: 2023-08-28 09:39:59Z
20230828 - Microsoft Visual C++ 2019 X64 Additional Runtime - 14.28.29913 14.28.29913 (Microsoft Corporation)

Key      : 426D5FF15155343438A75EC40151376E
LastWrite: 2023-08-28 09:40:29Z
20230828 - VMware Tools 11.3.5.18557794 (VMware, Inc.)

Key      : 731DDCEEAD31DE64DA0ADB7F8FEB568B
LastWrite: 2023-08-28 09:39:58Z
20230828 - Microsoft Visual C++ 2019 X64 Minimum Runtime - 14.28.29913 14.28.29913 (Microsoft Corporation)

Key      : DBBE6326F05F3B048B91D80B6C8003C8
LastWrite: 2023-08-28 09:39:55Z
20230828 - Microsoft Visual C++ 2019 X86 Minimum Runtime - 14.28.29913 14.28.29913 (Microsoft Corporation)

```

**Carpetas/documentos de acceso recientemente**

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Users\John Doe\NTUSER.DAT" -p recentdocs
Launching recentdocs v.20200427
recentdocs v.20200427
(NTUSER.DAT) Gets contents of user's RecentDocs key

RecentDocs
**All values printed in MRUList\MRUListEx order.
Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs
LastWrite Time: 2023-09-07 08:28:20Z
  2 = The Internet
  7 = threat/
  0 = system32
  6 = This PC
  5 = C:\
  4 = Local Disk (C:)
  3 = Temp
  1 = redirect

Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs\Folder
LastWrite Time 2023-09-07 08:28:20Z
MRUListEx = 1,0,3,2
  1 = The Internet
  0 = system32
  3 = This PC
  2 = Local Disk (C:)

```

**AutoStart - Ejecutar entradas clave**

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\RegRipper3.0-master> .\rip.exe -r "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Users\John Doe\NTUSER.DAT" -p run
Launching run v.20200511
run v.20200511
(Software, NTUSER.DAT) [Autostart] Get autostart key contents from Software hive

Software\Microsoft\Windows\CurrentVersion\Run
LastWrite Time 2023-09-07 08:30:07Z
  MicrosoftEdgeAutoLaunch_0562217A6A32A7E92C68940F512715D9 - "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --no-startup-window --win-session-start /prefetch:5
  DiscordUpdate - C:\Windows\Tasks\update.exe

Software\Microsoft\Windows\CurrentVersion\Run has no subkeys.

Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Run not found.

Software\Microsoft\Windows\CurrentVersion\RunOnce not found.

Software\Microsoft\Windows\CurrentVersion\RunServices not found.

Software\Microsoft\Windows\CurrentVersion\RunServicesOnce not found.

Software\Microsoft\Windows NT\CurrentVersion\Terminal Server\Install\Software\Microsoft\Windows\CurrentVersion\Run not found.

Software\Microsoft\Windows NT\CurrentVersion\Terminal Server\Install\Software\Microsoft\Windows\CurrentVersion\RunOnce not found.

Software\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run not found.

Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run not found.

Software\Microsoft\Windows\CurrentVersion\StartupApproved\Run not found.

Software\Microsoft\Windows\CurrentVersion\StartupApproved\Run32 not found.

Software\Microsoft\Windows\CurrentVersion\StartupApproved\StartupFolder not found.

```

### **Artifactos de ejecución del programa**

Cuando hablamos de`execution artifacts`En forense digital, nos referimos a los rastros y la evidencia que quedan en un sistema informático o dispositivo cuando se ejecuta un programa. Estas pequeñas fragmentos de información pueden darnos una idea de las actividades y comportamientos de software, usuarios e incluso aquellos con intención maliciosa. Si queremos reconstruir lo que pasó en una computadora, es imprescindible sumergirse en estos artefactos de ejecución.

Puede tropezar con algunos artefactos de ejecución bien conocidos en estos componentes de Windows:

- `Prefetch`
- `ShimCache`
- `Amcache`
- `BAM (Background Activity Moderator)`

Vamos a profundizar en cada uno de estos para obtener una mejor comprensión del tipo de detalles de ejecución del programa que capturan.

### **Investigación de pre -Fetch**

`Prefetch`es una característica del sistema operativo Windows que ayuda a optimizar la carga de aplicaciones precargando ciertos componentes y datos. Se crean archivos previos a cada programa que se ejecuta en un sistema de Windows, y esto incluye tanto aplicaciones instaladas como ejecutables independientes. La convención de denominación de archivos prefetch se basa en el nombre original del archivo ejecutable, seguido de un valor hexadecimal de la ruta donde reside el archivo ejecutable, y termina con el`.pf`Extensión de archivo.

En Forensics digitales, la carpeta previa a la captura y los archivos asociados pueden proporcionar información valiosa sobre las aplicaciones que se han ejecutado en un sistema de Windows. Los analistas forenses pueden examinar los archivos previos para determinar qué aplicaciones se han ejecutado, con qué frecuencia se ejecutaron y cuándo se ejecutaron por última vez.

En general, los archivos de pre -Fetch se almacenan en el`C:\Windows\Prefetch\`directorio.

Los archivos relacionados con la satisfacción cosechados de Kape se encuentran típicamente en`<KAPE_output_folder>\Windows\prefetch`.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch_.png)

Eric Zimmerman proporciona una herramienta para los archivos previos a la captura:`PECmd`(disponible en`C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6`).

Aquí hay un ejemplo de cómo iniciar el menú de ayuda de PECMD desde el directorio de herramientas Ericzimmerman.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\PECmd.exe -h
Description:
  PECmd version 1.5.0.0

  Author: Eric Zimmerman (saericzimmerman@gmail.com)
  https://github.com/EricZimmerman/PECmd

  Examples: PECmd.exe -f "C:\Temp\CALC.EXE-3FBEF7FD.pf"
            PECmd.exe -f "C:\Temp\CALC.EXE-3FBEF7FD.pf" --json "D:\jsonOutput" --jsonpretty
            PECmd.exe -d "C:\Temp" -k "system32, fonts"
            PECmd.exe -d "C:\Temp" --csv "c:\temp" --csvf foo.csv --json c:\temp\json
            PECmd.exe -d "C:\Windows\Prefetch"

            Short options (single letter) are prefixed with a single dash. Long commands are prefixed with two dashes

Usage:
  PECmd [options]

Options:
  -f <f>           File to process. Either this or -d is required
  -d <d>           Directory to recursively process. Either this or -f is required
  -k <k>           Comma separated list of keywords to highlight in output. By default, 'temp' and 'tmp' are
                   highlighted. Any additional keywords will be added to these
  -o <o>           When specified, save prefetch file bytes to the given path. Useful to look at decompressed Win10
                   files
  -q               Do not dump full details about each file processed. Speeds up processing when using --json or --csv
                   [default: False]
  --json <json>    Directory to save JSON formatted results to. Be sure to include the full path in double quotes
  --jsonf <jsonf>  File name to save JSON formatted results to. When present, overrides default name
  --csv <csv>      Directory to save CSV formatted results to. Be sure to include the full path in double quotes
  --csvf <csvf>    File name to save CSV formatted results to. When present, overrides default name
  --html <html>    Directory to save xhtml formatted results to. Be sure to include the full path in double quotes
  --dt <dt>        The custom date/time format to use when displaying time stamps. See https://goo.gl/CNVq0k for
                   options [default: yyyy-MM-dd HH:mm:ss]
  --mp             When true, display higher precision for timestamps [default: False]
  --vss            Process all Volume Shadow Copies that exist on drive specified by -f or -d [default: False]
  --dedupe         Deduplicate -f or -d & VSCs based on SHA-1. First file found wins [default: False]
  --debug          Show debug information during processing [default: False]
  --trace          Show trace information during processing [default: False]
  --version        Show version information
  -?, -h, --help   Show help and usage information

```

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch1_.png)

PECMD analizará el archivo de Fetch (`.pf`) y muestre varias información sobre la ejecución de la aplicación. Esto generalmente incluye detalles como:

- Primeras y últimos marcas de tiempo de ejecución.
- Número de veces que se ha ejecutado la aplicación.
- Información de volumen y directorio.
- Nombre y ruta de la aplicación.
- Información del archivo, como el tamaño del archivo y los valores hash.

Veamos proporcionando una ruta a un solo archivo previo a la Fetch, por ejemplo, el archivo de pre -Fetch relacionado con`discord.exe`(es decir, discord.exe-7191fad6.pf ubicado en`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch`).

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\PECmd.exe -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch\DISCORD.EXE-7191FAD6.pf
PECmd version 1.5.0.0

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/PECmd

Command line: -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch\DISCORD.EXE-7191FAD6.pf

Warning: Administrator privileges not found!

Keywords: temp, tmp

Processing C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch\DISCORD.EXE-7191FAD6.pf

Created on: 2023-09-07 08:30:16
Modified on: 2023-09-07 08:30:16
Last accessed on: 2023-09-17 15:55:01

Executable name: DISCORD.EXE
Hash: 7191FAD6
File size (bytes): 51,104
Version: Windows 10 or Windows 11

Run count: 1
Last run: 2023-09-07 08:30:06

Volume information:

#0: Name: \VOLUME{01d9da035d4d8f00-285d5e74} Serial: 285D5E74 Created: 2023-08-28 22:59:56 Directories: 23 File references: 106Directories referenced: 23

00: \VOLUME{01d9da035d4d8f00-285d5e74}\$EXTEND01: \VOLUME{01d9da035d4d8f00-285d5e74}\TEMP (Keyword True)
02: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS
03: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE
04: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA
05: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL
06: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT
07: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS
08: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS\CACHES
09: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS\INETCACHE
10: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS\INETCACHE\IE
11: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS\INETCACHE\IE\8O7R2XTQ
12: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\TEMP (Keyword True)
13: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\DOWNLOADS
14: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS
15: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\APPPATCH
16: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\GLOBALIZATION
17: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\GLOBALIZATION\SORTING
18: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\REGISTRATION
19: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32
20: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DRIVERS
21: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\EN-US
22: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\TASKS

Files referenced: 76

00: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\NTDLL.DLL
01: \VOLUME{01d9da035d4d8f00-285d5e74}\TEMP\DISCORD.EXE (Executable: True)
02: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\KERNEL32.DLL
03: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\KERNELBASE.DLL
04: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\LOCALE.NLS
05: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\APPHELP.DLL
06: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\APPPATCH\SYSMAIN.SDB
07: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\ADVAPI32.DLL
08: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\MSVCRT.DLL
09: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SECHOST.DLL
10: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\RPCRT4.DLL
11: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SHELL32.DLL
12: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\MSVCP_WIN.DLL
13: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\UCRTBASE.DLL
14: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\USER32.DLL
15: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\NETAPI32.DLL
16: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WIN32U.DLL
17: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\GDI32.DLL
18: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\GDI32FULL.DLL
19: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WS2_32.DLL
20: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WININET.DLL
21: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\NETUTILS.DLL
22: \VOLUME{01d9da035d4d8f00-285d5e74}\$MFT23: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SAMCLI.DLL
24: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\IMM32.DLL
25: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DRIVERS\CONDRV.SYS
26: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\NTMARTA.DLL
27: \VOLUME{01d9da035d4d8f00-285d5e74}\TEMP\UNINSTALL.EXE (Keyword: True)
28: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\TASKS\MICROSOFT.WINDOWSKITS.FEEDBACK.EXE
29: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\DOWNLOADS\UNINSTALL.EXE:ZONE.IDENTIFIER
30: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\TASKS\MICROSOFT.WINDOWSKITS.FEEDBACK.EXE:ZONE.IDENTIFIER
31: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\IERTUTIL.DLL
32: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\COMBASE.DLL
33: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SHCORE.DLL
34: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\GLOBALIZATION\SORTING\SORTDEFAULT.NLS
35: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SSPICLI.DLL
36: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINDOWS.STORAGE.DLL
37: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WLDP.DLL
38: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SHLWAPI.DLL
39: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\PROFAPI.DLL
40: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\ONDEMANDCONNROUTEHELPER.DLL
41: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINHTTP.DLL
42: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\KERNEL.APPCORE.DLL
43: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\MSWSOCK.DLL
44: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\IPHLPAPI.DLL
45: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINNSI.DLL
46: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\NSI.DLL
47: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\URLMON.DLL
48: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SRVCLI.DLL
49: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\OLEAUT32.DLL
50: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\OLE32.DLL
51: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DNSAPI.DLL
52: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\RASADHLP.DLL
53: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\FWPUCLNT.DLL
54: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\BCRYPT.DLL
55: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\EN-US\MSWSOCK.DLL.MUI
56: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WSHQOS.DLL
57: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\EN-US\WSHQOS.DLL.MUI
58: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\C_20127.NLS
59: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS\INETCACHE\IE\8O7R2XTQ\DISCORDSETUP[1].EXE
60: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\TEMP\DISCORDSETUP.EXE (Keyword: True)
61: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\TASKS\UPDATE.EXE
62: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\BCRYPTPRIMITIVES.DLL
63: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\RPCSS.DLL
64: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\UXTHEME.DLL
65: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\PROPSYS.DLL
66: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\CFGMGR32.DLL
67: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\CLBCATQ.DLL
68: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\REGISTRATION\R000000000006.CLB
69: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS\CACHES\CVERSIONS.1.DB
70: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\JOHN DOE\APPDATA\LOCAL\MICROSOFT\WINDOWS\CACHES\{AFBF9F1A-8EE8-4C77-AF34-C647E37CA0D9}.1.VER0X0000000000000003.DB
71: \VOLUME{01d9da035d4d8f00-285d5e74}\USERS\DESKTOP.INI
72: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SAMLIB.DLL
73: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\CRYPTBASE.DLL
74: \VOLUME{01d9da035d4d8f00-285d5e74}\TEMP\INSTALL.BAT (Keyword: True)
75: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\CMD.EXE

---------- Processed C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch\DISCORD.EXE-7191FAD6.pf in 0.29670430 seconds ----------

```

Al desplazarse hacia abajo en la salida, podemos ver los directorios referenciados por este ejecutable.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch3.png)

El desplazamiento adicional hacia abajo, la salida revela los archivos a los que se hace referencia a este ejecutable.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch4.png)

### **Actividad sospechosa en archivos referenciados**

También debemos considerar el directorio donde se ejecutó la aplicación. Si se ejecutó desde una ubicación inusual o inesperada, puede ser sospechoso. Por ejemplo, la siguiente captura de pantalla muestra algunas ubicaciones y archivos sospechosos.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch5_.png)

### **Convertir archivos de prejuicio a CSV**

Para un análisis más fácil, podemos convertir los datos previos a la captura en CSV de la siguiente manera.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\PECmd.exe -d C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch --csv C:\Users\johndoe\Desktop\forensic_data\prefetch_analysis
PECmd version 1.5.0.0

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/PECmd

Command line: -d C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch --csv C:\Users\johndoe\Desktop\forensic_data\prefetch_analysis

Warning: Administrator privileges not found!

Keywords: temp, tmp

Looking for prefetch files in C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch

Found 161 Prefetch files

Processing C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch\APPLICATIONFRAMEHOST.EXE-8CE9A1EE.pf

Created on: 2023-08-28 09:39:12
Modified on: 2023-09-07 08:28:29
Last accessed on: 2023-09-17 16:02:51

Executable name: APPLICATIONFRAMEHOST.EXE
Hash: 8CE9A1EE
File size (bytes): 61,616
Version: Windows 10 or Windows 11

Run count: 2
Last run: 2023-09-07 08:28:18
Other run times: 2023-08-28 09:39:02

Volume information:

#0: Name: \VOLUME{01d9da035d4d8f00-285d5e74} Serial: 285D5E74 Created: 2023-08-28 22:59:56 Directories: 8 File references: 74Directories referenced: 8

00: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS
01: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\FONTS
02: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\GLOBALIZATION
03: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\GLOBALIZATION\SORTING
04: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32
05: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\EN-US
06: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEMAPPS
07: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEMAPPS\MICROSOFT.WINDOWS.SECHEALTHUI_CW5N1H2TXYEWY

Files referenced: 84

00: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\NTDLL.DLL
01: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\APPLICATIONFRAMEHOST.EXE (Executable: True)
02: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\KERNEL32.DLL
03: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\KERNELBASE.DLL
04: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\LOCALE.NLS
05: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\MSVCRT.DLL
06: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\COMBASE.DLL
07: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\UCRTBASE.DLL
08: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\RPCRT4.DLL
09: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DXGI.DLL
10: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WIN32U.DLL
11: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\GDI32.DLL
12: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\GDI32FULL.DLL
13: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\MSVCP_WIN.DLL
14: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\USER32.DLL
15: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\IMM32.DLL
16: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\RPCSS.DLL
17: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\KERNEL.APPCORE.DLL
18: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\BCRYPTPRIMITIVES.DLL
19: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\CLBCATQ.DLL
20: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\REGISTRATION\R000000000006.CLB
21: \VOLUME{01d9da035d4d8f00-285d5e74}\$MFT22: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\APPLICATIONFRAME.DLL
23: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SHCORE.DLL
24: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SHLWAPI.DLL
25: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\OLEAUT32.DLL
26: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\TWINAPI.APPCORE.DLL
27: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\UXTHEME.DLL
28: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\PROPSYS.DLL
29: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DEVOBJ.DLL
30: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\CFGMGR32.DLL
31: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\TWINAPI.DLL
32: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SECHOST.DLL
33: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\BCP47MRM.DLL
34: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\D3D11.DLL
35: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DWMAPI.DLL
36: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\D2D1.DLL
37: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\OLE32.DLL
38: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\ONECOREUAPCOMMONPROXYSTUB.DLL
39: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WIN32KBASE.SYS
40: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\MSCTF.DLL
41: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\D3D10WARP.DLL
42: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\ADVAPI32.DLL
43: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\RESOURCEPOLICYCLIENT.DLL
44: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DRIVERS\DXGMMS2.SYS
45: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DRIVERS\DXGKRNL.SYS
46: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DXCORE.DLL
47: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\DCOMP.DLL
48: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\COREMESSAGING.DLL
49: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WS2_32.DLL
50: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WIN32KFULL.SYS
51: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\EN-US\APPLICATIONFRAME.DLL.MUI
52: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\UIAUTOMATIONCORE.DLL
53: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\GLOBALIZATION\SORTING\SORTDEFAULT.NLS
54: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\SHELL32.DLL
55: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINDOWS.STORAGE.DLL
56: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WLDP.DLL
57: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\PROFAPI.DLL
58: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINDOWS.STATEREPOSITORYCORE.DLL
59: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINDOWS.STATEREPOSITORYPS.DLL
60: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINDOWSCODECS.DLL
61: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\BCRYPT.DLL
62: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEMAPPS\MICROSOFT.WINDOWS.SECHE
---SNIP---
60: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\UMPDC.DLL
61: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SERVICING\CBSAPI.DLL
62: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SOFTWAREDISTRIBUTION\DOWNLOAD\A766D9CA8E03365B463454014B3585CB\CBSHANDLER\STATE
63: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WINDOWS.STORAGE.DLL
64: \VOLUME{01d9da035d4d8f00-285d5e74}\WINDOWS\SYSTEM32\WLDP.DLL

---------- Processed C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\prefetch\WUAUCLT.EXE-5D573F0E.pf in 0.05522470 seconds ----------
Processed 161 out of 161 files in 14.3289 seconds

CSV output will be saved to C:\Users\johndoe\Desktop\forensic_data\prefetch_analysis\20230917160113_PECmd_Output.csv
CSV time line output will be saved to C:\Users\johndoe\Desktop\forensic_data\prefetch_analysis\20230917160113_PECmd_Output_Timeline.csv

```

El directorio de destino contiene la salida analizada en formato CSV.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch7_.png)

Ahora podemos analizar fácilmente la salida en Timeline Explorer. Cargamos ambos archivos.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch9.png)

El segundo archivo de salida es el archivo de línea de tiempo, que muestra los detalles ejecutables ordenados por el tiempo de ejecución.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_prefetch8.png)

### **Investigación de Shimcache (caché de compatibilidad de la aplicación)**

`ShimCache`(también conocido como AppCompatCache) es un mecanismo de Windows utilizado por los sistemas operativos de Windows para identificar problemas de compatibilidad de aplicaciones. Esta base de datos registra información sobre aplicaciones ejecutadas y se almacena en el registro de Windows. Los desarrolladores pueden utilizar esta información para rastrear problemas de compatibilidad con programas ejecutados.

En el`AppCompatCache`Entradas de caché, podemos ver la información como:

- Rutas de archivo completas
- Marcas de tiempo
    - Último tiempo modificado ($ Standard_information)
    - Último tiempo actualizado (shimcache)
- Bandera de ejecución de procesos
- Posición de entrada de caché

Los investigadores forenses pueden usar esta información para detectar la ejecución de archivos potencialmente maliciosos.

El`AppCompatCache`La clave se encuentra en el`HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\ControlSet001\Control\Session Manager\AppCompatCache`Ubicación de registro.

Vamos a cargar el`SYSTEM`Registro Hive (disponible en`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\System32\config`) en`Registry Explorer`y vea qué tipo de información contiene. Podemos hacerlo abriendo el explorador del registro y dejando caer los archivos de colmena del registro. Entonces tendremos que ir a marcadores y seleccionar`AppCompatCache`. En el lado inferior derecho, debemos ver la evidencia de ejecución de la aplicación como se muestra en la captura de pantalla.

![](https://academy.hackthebox.com/storage/modules/237/img13.png)

### **Investigación de Amcache**

`AmCache`se refiere a un archivo de registro de Windows que se utiliza para almacenar evidencia relacionada con la ejecución del programa. Sirve como un recurso valioso para forenses digitales e investigaciones de seguridad, lo que ayuda a los analistas a comprender el historial de ejecución de aplicaciones y detectar signos de cualquier ejecución sospechosa.

La información que contiene incluye la ruta de ejecución, el tiempo ejecutado por primera vez, el tiempo eliminado y la primera instalación. También proporciona el archivo hash para los ejecutables.

En Windows OS, la colmena Amcache se encuentra en`C:\Windows\AppCompat\Programs\AmCache.hve`

Los archivos relacionados con el amcache cosechados de Kape se encuentran típicamente en`<KAPE_output_folder>\Windows\AppCompat\Programs`.

Vamos a cargar`C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\AppCompat\Programs\AmCache.hve`en Registry Explorer para ver qué tipo de información contiene.

![](https://academy.hackthebox.com/storage/modules/237/img14.png)

Usando Eric Zimmerman's[Amcacheparser](https://github.com/EricZimmerman/AmcacheParser), podemos analizar y convertir este archivo en un CSV y analizarlo en detalle dentro del Explorador de Liney.

Herramientas rápidas de examen y análisis

```powershell
PS C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6> .\AmcacheParser.exe -f "C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\AppCompat\Programs\AmCache.hve" --csv C:\Users\johndoe\Desktop\forensic_data\amcache-analysis
AmcacheParser version 1.5.1.0

Author: Eric Zimmerman (saericzimmerman@gmail.com)
https://github.com/EricZimmerman/AmcacheParser

Command line: -f C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\AppCompat\Programs\AmCache.hve --csv C:\Users\johndoe\Desktop\forensic_data\amcache-analysis

Warning: Administrator privileges not found!

C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\AppCompat\Programs\AmCache.hve is in new format!

Total file entries found: 93
Total shortcuts found: 49
Total device containers found: 15
Total device PnPs found: 183
Total drive binaries found: 372
Total driver packages found: 4

Found 36 unassociated file entry

Results saved to: C:\Users\johndoe\Desktop\forensic_data\amcache-analysis

Total parsing time: 0.539 seconds

```

### **Investigación de Windows BAM (moderador de actividad de fondo)**

El`Background Activity Moderator`(BAM) es un componente en el sistema operativo de Windows que rastrea e registra la ejecución de ciertos tipos de antecedentes o tareas programadas. BAM es en realidad un controlador de dispositivo de kernel como se muestra en la siguiente captura de pantalla.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_bamdrv.png)

Es el principal responsable de controlar la actividad de las aplicaciones de antecedentes, pero puede ayudarnos a proporcionar la evidencia de la ejecución del programa que enumera bajo la colmena del registro BAM. La tecla BAM se encuentra en la siguiente ubicación del registro.`HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\bam\State\UserSettings\{USER-SID}`

Usando Registry Explorer, podemos explorar esto dentro de la colmena del sistema para ver los nombres ejecutables. Registry Explorer ya tiene un marcador para`bam`.

![](https://academy.hackthebox.com/storage/modules/237/img15.png)

También podemos usar`RegRipper`para obtener información similar a través de su`bam`complemento.

### **Análisis de datos de llamadas API capturados (`.apmx64`)**

`.apmx64`Los archivos se generan por[Monitor API](http://www.rohitab.com/apimonitor), que registra datos de llamadas API. Estos archivos se pueden abrir y analizar dentro de la herramienta misma. API Monitor es un software que captura y muestra llamadas API iniciadas por aplicaciones y servicios. Si bien su función principal es la depuración y el monitoreo, su capacidad para capturar datos de llamadas API lo hace útil para descubrir artefactos forenses. Procedamos cargando`C:\Users\johndoe\Desktop\forensic_data\APMX64\discord.apmx64`en API Monitor (disponible en`C:\Program Files\rohitab.com\API Monitor`) y examinando su contenido para obtener información valiosa.

El lanzamiento del monitor API iniciará ciertos archivos necesarios.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon1.png)

Al abrir la aplicación API Monitor, diremos a la`File`menú y elija`Open`A partir de ahí, navegemos a la ubicación del`.apmx64`archivo y seleccionarlo.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon2.png)

Después de abrir el archivo, se mostrará una lista de llamadas API grabadas realizadas por la aplicación monitoreada. Por lo general, esta lista contiene detalles como el nombre de la función API, los parámetros, los valores de retorno y las marcas de tiempo. La captura de pantalla a continuación ofrece una vista completa de la interfaz de usuario del monitor API y sus diversas secciones.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon3_.png)

Al hacer clic en los procesos monitoreados a la izquierda, mostrará los datos de llamadas API grabados para el proceso elegido en la vista resumida a la derecha. Para ilustración, considere seleccionar el`discord.exe`proceso. En la visión resumida, observaremos las llamadas de la API iniciadas por`discord.exe`.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon4.png)

Una observación notable de la captura de pantalla es la llamada al[Función GetenV](https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/getenv-wgetenv?view=msvc-170). Aquí está la sintaxis de esta función.

Herramientas rápidas de examen y análisis

```
char *getenv(
   const char *varname
);

```

Esta función recupera el valor de una variable de entorno especificada. Requiere un`varname`Parámetro, que representa un nombre de variable de entorno válido y devuelve un puntero que apunta a la entrada de la tabla que contiene el valor de la variable de entorno respectivo.

API Monitor cuenta con una gran cantidad de capacidades de filtrado y búsqueda. Esto nos permite perfeccionar llamadas de API específicas basadas en funciones o marcos de tiempo. Al navegar a través del resumen o utilizar el filtro y las funcionalidades de búsqueda, podemos descubrir detalles intrigantes, como las llamadas API relacionadas con la creación de archivos, la creación de procesos, las alteraciones del registro y más.

**Registro de persistencia a través de claves de ejecución**

Una estrategia de los adversarios a menudo empleados para mantener el acceso no autorizado a un sistema comprometido está insertando una entrada en el`run keys`Dentro del registro de Windows. Investigamos si hay alguna referencia al`RegOpenKeyExA`función, que accede a la clave de registro designada. Para realizar esta búsqueda, simplemente escriba`RegOpenKey`en el cuadro de búsqueda, generalmente situado sobre la ventana del monitor API, y presione`Enter`.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon6.png)

De los resultados mostrados, es evidente que la clave del registro`SOFTWARE\Microsoft\Windows\CurrentVersion\Run`Corresponde a la clave de registro Run, que desencadena el programa designado sobre cada inicio de sesión de usuario. Las entidades maliciosas a menudo explotan esta clave para insertar entradas que apuntan a su puerta trasera, una tarea que se puede lograr a través de la función API de registro`RegSetValueExA`.

Para explorar más a fondo, busquemos cualquier mención del`RegSetValueExA`función, que define los datos y el tipo de un valor especificado dentro de una clave de registro. Enganche el cuadro de búsqueda, escriba`RegSet`, y golpear`Enter`.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon7.png)

Una observación notable es la`RegSetValueExA`invocación. Antes de sumergirnos más profundamente, familiaricemos con la documentación de esta función.

Herramientas rápidas de examen y análisis

```
LSTATUS RegSetValueExA(
  [in]           HKEY       hKey,
  [in, optional] LPCSTR     lpValueName,
                 DWORD      Reserved,
  [in]           DWORD      dwType,
  [in]           const BYTE *lpData,
  [in]           DWORD      cbData
);

```

- `hKey1`es un mango de la clave de registro donde desea establecer un valor de registro.
- `lpValueName`es un puntero a una cadena terminada nula que especifica el nombre del valor de registro que desea establecer. En este caso, se llama`DiscordUpdate`.
- El`Reserved`El parámetro está reservado y debe ser cero.
- `dwType`Especifica el tipo de datos del valor de registro. Es probable que sea una constante entera que represente el tipo de datos (por ejemplo,`REG_SZ`para un valor de cadena).
- `(BYTE*)lpData`es un elenco de tipo que convierte el`_lpData_`variable a un puntero a un byte (`BYTE*`). Esto se hace para garantizar que los datos apuntan por`_lpData_`se trata como una matriz de bytes, que es el formato esperado para los datos binarios en el registro de Windows. En nuestro caso, esto se muestra en la vista del búfer como`C:\Windows\Tasks\update.exe`.
- `cbData`es un entero que especifica el tamaño, en los bytes, de los datos señalados por`_lpData_`.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon9.png)

Una conclusión crítica de esta llamada de API es la`lpData`parámetro, que revela la ubicación de la puerta trasera,`C:\Windows\Tasks\update.exe`.

**Inyección de procesos**

Para examinar la creación de procesos, busquemos el`CreateProcessA`función. Iniciemos`CreateProcess`En el cuadro de búsqueda y presione`Enter`.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_apimon5_.png)

A continuación se presenta la sintaxis de la función API de Windows,`CreateProcessA`.

Herramientas rápidas de examen y análisis

```
BOOL CreateProcessA(
  [in, optional]      LPCSTR                lpApplicationName,
  [in, out, optional] LPSTR                 lpCommandLine,
  [in, optional]      LPSECURITY_ATTRIBUTES lpProcessAttributes,
  [in, optional]      LPSECURITY_ATTRIBUTES lpThreadAttributes,
  [in]                BOOL                  bInheritHandles,
  [in]                DWORD                 dwCreationFlags,
  [in, optional]      LPVOID                lpEnvironment,
  [in, optional]      LPCSTR                lpCurrentDirectory,
  [in]                LPSTARTUPINFOA        lpStartupInfo,
  [out]               LPPROCESS_INFORMATION lpProcessInformation
);

```

Un elemento intrigante dentro de esta API es el`lpCommandLine`parámetro. Revela la línea de comando ejecutada, que, en este contexto, es`C:\Windows\System32\comp.exe`. Notablemente, el`lpCommandLine`se puede especificar sin delinear la ruta ejecutable completa en el`lpApplicationName`valor.

Otro parámetro fundamental que vale la pena señalar es`dwCreationFlags`, establecido en`CREATE_SUSPENDED`. Esto indica que el hilo principal del nuevo proceso comienza en un estado suspendido y permanece inactivo hasta`ResumeThread`la función se invoca.

El`lpCommandLine`El parámetro de esta API llama a la luz sobre el proceso infantil que se inició, a saber,`C:\Windows\System32\comp.exe`.

Más abajo también notamos que las funciones relacionadas con la inyección de procesos se utilizan por`discord.exe`.

![](https://academy.hackthebox.com/storage/modules/237/disc_inj.png)

Todo lo anterior son fuertes indicadores de inyección de proceso.

### **Actividad de PowerShell**

Las transcripciones de PowerShell registran meticulosamente tanto los comandos emitidos como sus respectivos resultados durante una sesión de PowerShell. Ocasionalmente, dentro del directorio de documentos de un usuario, podríamos tropezar con los archivos de transcripción de PowerShell. Estos archivos nos otorgan una ventana a las actividades de PowerShell grabadas en el sistema.

La captura de pantalla posterior muestra los archivos de transcripción de PowerShell ubicados en el directorio de documentos del usuario en una imagen forense montada.

![](https://academy.hackthebox.com/storage/modules/237/win_dfir_accessdata_ps1.png)

Revisar la actividad relacionada con PowerShell en detalle puede ser instrumental durante las investigaciones.

Aquí hay algunas pautas recomendadas al manejar los datos de PowerShell.

- `Unusual Commands`: Busque comandos de PowerShell que no sean típicos en su entorno o que se asocien comúnmente con actividades maliciosas. Por ejemplo, comandos para descargar archivos de Internet (Invoke-WebRequest o WGet), comandos que manipulan el registro, o aquellos que implican la creación de tareas programadas.
- `Script Execution`: Verifique la ejecución de los scripts de PowerShell, especialmente si no están firmados o provienen de fuentes no confiables. Los scripts se pueden usar para automatizar acciones maliciosas.
- Comandos codificados: los actores maliciosos a menudo usan comandos de PowerShell codificados u ofuscados para evadir la detección. Busque signos de comandos codificados en transcripciones.
- `Privilege Escalation`: Los comandos que intentan aumentar los privilegios, cambiar los permisos de los usuarios o realizar acciones generalmente restringidas a los administradores pueden ser sospechosos.
- `File Operations`: Verifique los comandos de PowerShell que implicen crear, mover o eliminar archivos, especialmente en ubicaciones de sistema confidencial.
- `Network Activity`: Busque comandos relacionados con la actividad de la red, como realizar solicitudes HTTP o iniciar conexiones de red. Estos pueden ser indicativos de comunicaciones de comando y control (C2).
- `Registry Manipulation`: Verifique los comandos que implican modificar el registro de Windows, ya que esta puede ser una táctica común para la persistencia de malware.
- `Use of Uncommon Modules`: Si un script o comando de PowerShell usa módulos poco comunes o no estándar, podría ser un signo de actividad sospechosa.
- `User Account Activity`: Busque cambios en las cuentas de los usuarios, incluida la creación, la modificación o la eliminación. Los actores maliciosos pueden intentar crear o manipular las cuentas de los usuarios para la persistencia.
- `Scheduled Tasks`: Investigue la creación o modificación de las tareas programadas a través de PowerShell. Este puede ser un método común para la persistencia.
- `Repeated or Unusual Patterns`: Analice los patrones de los comandos de PowerShell. Los comandos repetidos e idénticos o secuencias inusuales de comandos pueden indicar automatización o comportamiento malicioso.
- `Execution of Unsigned Scripts`: La ejecución de scripts sin firmar puede ser un signo de actividad sospechosa, especialmente si las políticas de ejecución de script están configuradas para restringir esto.