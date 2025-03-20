# Introducción a MSFConsole

Para comenzar a interactuar con el marco de MetaSploit, necesitamos escribir`msfconsole`en la terminal de nuestra elección. Muchas distribuciones orientadas a la seguridad, como Parrot Security y Kali Linux, vienen con`msfconsole`preinstalado. Podemos usar varias otras opciones al iniciar el script como con cualquier otra herramienta de línea de comandos. Estos varían desde los interruptores/opciones de visualización gráfica hasta los de procedimiento.

---

# **Preparación**

Al lanzar el`msfconsole`, nos reunimos con su arte de chapoteo acuñado y el indicador de la línea de comandos, esperando nuestro primer comando.

### **Lanzamiento de MSFConsole**

Introducción a MSFConsole

```
xnoxos@htb[/htb]$ msfconsole
                                              `:oDFo:`
                                           ./ymM0dayMmy/.
                                        -+dHJ5aGFyZGVyIQ==+-
                                    `:sm⏣~~Destroy.No.Data~~s:`
                                 -+h2~~Maintain.No.Persistence~~h+-
                             `:odNo2~~Above.All.Else.Do.No.Harm~~Ndo:`
                          ./etc/shadow.0days-Data'%20OR%201=1--.No.0MN8'/.
                       -++SecKCoin++e.AMd`       `.-://///+hbove.913.ElsMNh+-
                      -~/.ssh/id_rsa.Des-                  `htN01UserWroteMe!-
                      :dopeAW.No<nano>o                     :is:TЯiKC.sudo-.A:
                      :we're.all.alike'`                     The.PFYroy.No.D7:
                      :PLACEDRINKHERE!:                      yxp_cmdshell.Ab0:
                      :msf>exploit -j.                       :Ns.BOB&ALICEes7:
                      :---srwxrwx:-.`                        `MS146.52.No.Per:
                      :<script>.Ac816/                        sENbove3101.404:
                      :NT_AUTHORITY.Do                        `T:/shSYSTEM-.N:
                      :09.14.2011.raid                       /STFU|wall.No.Pr:
                      :hevnsntSurb025N.                      dNVRGOING2GIVUUP:
                      :#OUTHOUSE-  -s:                       /corykennedyData:                          :$nmap -oS                              SSo.6178306Ence:                          :Awsm.da:                            /shMTl#beats3o.No.:                          :Ring0:                             `dDestRoyREXKC3ta/M:
                      :23d:                               sSETEC.ASTRONOMYist:
                       /-                        /yo-    .ence.N:(){ :|: & };:
                                                 `:Shall.We.Play.A.Game?tron/
                                                 ```-ooy.if1ghtf0r+ehUser5`
                                               ..th3.H1V3.U2VjRFNN.jMh+.`
                                              `MjM~~WE.ARE.se~~MMjMs
                                               +~KANSAS.CITY's~-`
                                                J~HAKCERS~./.`
                                                .esc:wq!:`
                                                 +++ATH`
                                                  `

       =[ metasploit v6.1.9-dev                           ]
+ -- --=[ 2169 exploits - 1149 auxiliary - 398 post       ]
+ -- --=[ 592 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: Use sessions -1 to interact with the last opened session

msf6 >

```

Alternativamente, podemos usar el`-q`opción, que no muestra el banner.

Introducción a MSFConsole

```
xnoxos@htb[/htb]$ msfconsole -qmsf6 >

```

Para ver mejor todos los comandos disponibles, podemos escribir el`help`dominio. Lo primero es lo primero, nuestras herramientas deben ser agudas. Una de las primeras cosas que debemos hacer es asegurarnos de que los módulos que compongan el marco estén actualizados, y se puedan importar cualquiera nueva disponible para el público.

La antigua forma habría sido correr`msfupdate`en nuestro terminal del sistema operativo (afuera`msfconsole`). Sin embargo, el`apt`Package Manager actualmente puede manejar la actualización de módulos y características sin esfuerzo.

### **Instalación de MSF**

Introducción a MSFConsole

```
xnoxos@htb[/htb]$ sudo apt update && sudo apt install metasploit-framework<SNIP>

(Reading database ... 414458 files and directories currently installed.)
Preparing to unpack .../metasploit-framework_6.0.2-0parrot1_amd64.deb ...
Unpacking metasploit-framework (6.0.2-0parrot1) over (5.0.88-0kali1) ...
Setting up metasploit-framework (6.0.2-0parrot1) ...
Processing triggers for man-db (2.9.1-1) ...
Scanning application launchers
Removing duplicate launchers from Debian
Launchers are updated

```

Uno de los primeros pasos que cubriremos en este módulo es buscar un`exploit`para nuestro`target`. Sin embargo, necesitamos tener una perspectiva detallada sobre el`target`en sí mismo antes de intentar cualquier explotación. Esto involucra el`Enumeration`Proceso, que precede a cualquier tipo de intento de explotación.

Durante`Enumeration`, tenemos que mirar nuestro objetivo e identificar qué servicios de orientación pública se están ejecutando en él. Por ejemplo, ¿es un servidor HTTP? ¿Es un servidor FTP? ¿Es una base de datos SQL? Estos diferentes`target`Las tipologías varían sustancialmente en el mundo real. Tendremos que comenzar con un minucioso`scan`de la dirección IP del objetivo para determinar qué servicio se ejecuta y qué versión está instalada para cada servicio.

Notaremos a medida que avanzamos que las versiones son los componentes clave durante el`Enumeration`proceso que nos permitirá determinar si el objetivo es vulnerable o no. Las versiones sin parpadear de servicios previamente vulnerables o código obsoleto en una plataforma de acceso público a menudo serán nuestro punto de entrada en el`target`sistema.

---

# **Estructura de compromiso de MSF**

La estructura de compromiso de MSF se puede dividir en cinco categorías principales.

- Enumeración
- Preparación
- Explotación
- Escalada de privilegios
- Después de la explotación

Esta división nos hace más fácil encontrar y seleccionar las funciones de MSF apropiadas de una manera más estructurada y trabajar con ellas en consecuencia. Cada una de estas categorías tiene diferentes subcategorías que están destinadas a fines específicos. Estos incluyen, por ejemplo, la validación de servicios y la investigación de vulnerabilidad.

Por lo tanto, es crucial que nos familiaricemos con esta estructura. Por lo tanto, veremos los componentes de este marco para comprender mejor cómo están relacionados.

![](https://academy.hackthebox.com/storage/modules/39/S04_SS03.png)

Revisaremos cada una de estas categorías durante el módulo, pero recomendamos mirar los componentes individuales nosotros mismos y cavar más profundamente. Experimentar con las diferentes funciones es una parte integral del aprendizaje de una nueva herramienta o habilidad. Por lo tanto, debemos probar todo lo imaginable aquí en los siguientes laboratorios y analizar los resultados de forma independiente.