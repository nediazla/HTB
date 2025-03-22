# Escaneo de openvas

La aplicación OpenVas Greenbone Security Assistant tiene varias pestañas con las que puede interactuar. Para esta sección, cavaremos en los escaneos. Si navegas al`Scans`Pestaña que se muestra a continuación, verá los escaneos que se han ejecutado en el pasado. También podrá ver cómo crear una nueva tarea para ejecutar un escaneo. Las tareas funcionan fuera de las configuraciones de escaneo que el usuario configura.

**Nota:**Los escaneos que se muestran en esta sección ya han sido previamente recorridos para ahorrarle el tiempo de esperar que terminen. Si vuelve a ejecutar el escaneo, es mejor pasar por vulnerabilidades a medida que vengan, en lugar de esperar a que termine el escaneo, ya que pueden tardar 1-2 horas en terminar.

![](https://academy.hackthebox.com/storage/modules/108/openvas/creatingscan1.png)

**Nota:**Para este módulo, el objetivo de Windows será`172.16.16.100`y el objetivo de Linux será`172.16.16.160`.

![](https://academy.hackthebox.com/storage/modules/108/openvas/scanconfigs.png)

---

# **Configuración**

Antes de configurar los escaneos, es mejor configurar los objetivos para el escaneo. Si navegas al`Configurations`pestaña y seleccione`Targets`, verá objetivos que ya se han agregado a la aplicación.

![](https://academy.hackthebox.com/storage/modules/108/openvas/targets.png)

Para agregar el suyo, haga clic en el icono resaltado a continuación y agregue un objetivo individual o una lista de host. También puede configurar otras opciones, como los puertos, la autenticación y los métodos de identificación si el host es accesible. Para el`Alive Test`, el`Scan Config Default`Opción de OpenVas aprovecha el`NVT Ping Host`en el`NVT Family`. Puedes aprender sobre la familia NVT[aquí](https://docs.greenbone.net/GSM-Manual/gos-22.04/en/scanning.html#creating-a-target).

![](https://academy.hackthebox.com/storage/modules/108/openvas/addingtarget.png)

Típicamente, un`authenticated scan`aprovecha a un usuario de alto privilegiado como`root`o`Administrator`. Dependiendo del nivel de permiso para el usuario, si es el nivel de permiso más alto, recuperará la cantidad máxima de información del host con respecto a las vulnerabilidades presentes ya que tendría acceso completo.

**Nota:**Para ejecutar un escaneo credencial en el destino, use las siguientes credenciales:`htb-student_adm`:`HTB_@cademy_student!`para Linux y`administrator`:`Academy_VA_adm1!`para ventanas. Estos escaneos ya se han configurado en el objetivo de OpenVas para ahorrarle tiempo.

Una vez que haya agregado su objetivo, aparecerán en la lista a continuación:

![](https://academy.hackthebox.com/storage/modules/108/openvas/targetsview.png)

---

# **Configuración de un escaneo**

Configuraciones de exploración múltiple aprovechan las familias de la prueba de vulnerabilidad de la red OpenVas (NVT), que consisten en muchas categorías diferentes de vulnerabilidades, como las de Windows, Linux, aplicaciones web, etc. Puede ver algunos tipos diferentes de familias que se muestran a continuación:

![](https://academy.hackthebox.com/storage/modules/108/openvas/nvt2.png)

OpenVas tiene varias configuraciones de escaneo para elegir para escanear una red. Recomendamos solo aprovechar las siguientes, ya que otras opciones podrían causar interrupciones del sistema en una red:

- `Base`: Esta configuración de escaneo está destinada a enumerar información sobre el estado del host y la información del sistema operativo. Esta configuración de escaneo no verifica si hay vulnerabilidades.
- `Discovery`: Esta configuración de escaneo está destinada a enumerar información sobre el sistema. La configuración identifica los servicios, el hardware, los puertos accesibles y el software del host que se utilizan en el sistema. Esta configuración de escaneo tampoco verifica las vulnerabilidades.
- `Host Discovery`: Esta configuración de escaneo prueba únicamente si el host está vivo y determina qué dispositivos son`active`en la red. Esta configuración de escaneo tampoco verifica las vulnerabilidades.*OpenVas aprovecha a Ping para identificar si el host está vivo.*
- `System Discovery`: Este escaneo enumera el host de destino más allá del 'escaneo de descubrimiento' e intenta identificar el sistema operativo y el hardware asociados con el host.
- `Full and fast`: OpenVas recomienda esta configuración como la opción más segura y aprovecha la inteligencia para usar las mejores verificaciones NVT para los host (s) en función de los puertos accesibles.

Puede crear su propio escaneo navegando a la pestaña 'escaneos' y haciendo clic en el icono del asistente.

![](https://academy.hackthebox.com/storage/modules/108/openvas/creatingscan2.png)

Una vez que haga clic en el icono del asistente, el panel que se muestra a continuación aparecerá y le permitirá configurar su escaneo.

![](https://academy.hackthebox.com/storage/modules/108/openvas/Newscan.png)

Configuraremos el escaneo con las opciones a continuación, a las que se dirige`172.16.16.160`y luego ejecute nuestro escaneo, que puede tomar`30-60 minutes`para terminar.

![](https://academy.hackthebox.com/storage/modules/108/openvas/linux_basic.png)

![](https://academy.hackthebox.com/storage/modules/108/openvas/linux_unauthedtarget.png)

[Anterior](https://academy.hackthebox.com/module/108/section/1026) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/108/section/1495)