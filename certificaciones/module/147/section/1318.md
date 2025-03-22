# Creditencial de caza en Windows

Una vez que tenemos acceso a una máquina de Windows de destino a través de la GUI o CLI, podemos beneficiarnos significativamente de incorporar la caza de credenciales en nuestro enfoque.`Credential Hunting`es el proceso de realizar búsquedas detalladas en todo el sistema de archivos y a través de varias aplicaciones para descubrir credenciales. Para comprender este concepto, colocemos en un escenario. Hemos obtenido acceso a la estación de trabajo de Windows 10 de un administrador de TI a través de RDP.

---

# **Búsqueda centrada**

Muchas de las herramientas disponibles para nosotros en Windows tienen funcionalidad de búsqueda. En la actualidad, hay características centradas en la búsqueda integradas en la mayoría de las aplicaciones y sistemas operativos, por lo que podemos usar esto para nuestra ventaja en un compromiso. Un usuario puede haber documentado sus contraseñas en algún lugar del sistema. Incluso puede haber credenciales predeterminadas que se puedan encontrar en varios archivos. Sería prudente basar nuestra búsqueda de credenciales sobre lo que sabemos sobre cómo se está utilizando el sistema objetivo. En este caso, sabemos que tenemos acceso a la estación de trabajo de un administrador de TI.

`What might an IT admin be doing on a day-to-day basis & which of those tasks may require credentials?`

Podemos usar esta pregunta y consideración para refinar nuestra búsqueda para reducir la necesidad de adivinar aleatorias tanto como sea posible.

### **Términos clave para buscar**

Ya sea que terminemos con el acceso a la GUI o la CLI, sabemos que tendremos algunas herramientas para buscar para buscar, pero de igual importancia es exactamente lo que estamos buscando. Aquí hay algunos términos clave útiles que podemos usar que pueden ayudarnos a descubrir algunas credenciales:

|  |  |  |
| --- | --- | --- |
| Contraseñas | Frases de contracción | Llaves |
| Nombre de usuario | Cuenta de usuario | Creditaciones |
| Usuarios | Sencillo | Frases de contracción |
| configuración | dbcredential | DBPassword |
| pwd | Acceso | Cartas credenciales |

Usemos algunos de estos términos clave para buscar en la estación de trabajo del administrador de TI.

---

# **Herramientas de búsqueda**

Con acceso a la GUI, vale la pena intentar usar`Windows Search`Para encontrar archivos en el objetivo utilizando algunas de las palabras clave mencionadas anteriormente.

![](https://academy.hackthebox.com/storage/modules/147/WindowsSearch.png)

De manera predeterminada, buscará varias configuraciones del sistema operativo y el sistema de archivos para archivos y aplicaciones que contienen el término clave ingresado en la barra de búsqueda.

También podemos aprovechar las herramientas de terceros como[Lazagne](https://github.com/AlessandroZ/LaZagne)Para descubrir rápidamente las credenciales que los navegadores web u otras aplicaciones instaladas pueden almacenar inseguamente. Sería beneficioso mantener un[copia independiente](https://github.com/AlessandroZ/LaZagne/releases/)de Lazagne en nuestro host de ataque para que podamos transferirlo rápidamente al objetivo.`Lazagne.exe`lo hará bien por nosotros en este escenario. Podemos usar nuestro cliente RDP para copiar el archivo al objetivo de nuestro host de ataque. Si estamos usando`xfreerdp`Todo lo que debemos hacer es copiar y pegar en la sesión RDP que hemos establecido.

Una vez que lazagne.exe está en el objetivo, podemos abrir el símbolo del sistema o PowerShell, navegar al directorio al que se cargó el archivo y ejecutar el siguiente comando:

### **Corriendo lazagne todo**

Creditencial de caza en Windows

```
C:\Users\bob\Desktop> start lazagne.exe all

```

Esto ejecutará lazagne y correrá`all`Módulos incluidos. Podemos incluir la opción`-vv`Estudiar lo que está haciendo en el fondo. Una vez que presionamos Enter, abrirá otro aviso y mostrará los resultados.

### **Salida de lazagne**

Creditencial de caza en Windows

```
|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|

########## User: bob ##########

------------------- Winscp passwords -----------------

[+] Password found !!!
URL: 10.129.202.51
Login: admin
Password: SteveisReallyCool123
Port: 22

```

Si usamos el`-vv`Opción, veríamos intentos de recopilar contraseñas de todo el software compatible con Lazagne. También podemos mirar en la página GitHub en la sección Software compatible para ver que todo el software Lazagne intentará recopilar credenciales. Puede ser un poco impactante ver lo fácil que puede ser obtener credenciales en texto claro. Gran parte de esto se puede atribuir a la forma insegura que muchas aplicaciones almacenan credenciales.

### **Usando Findstr**

También podemos usar[Findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr)Para buscar patrones en muchos tipos de archivos. Teniendo en cuenta los términos clave comunes, podemos usar variaciones de este comando para descubrir credenciales en un objetivo de Windows:

Creditencial de caza en Windows

```
C:\> findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml

```

---

# **Consideraciones adicionales**

Hay miles de herramientas y términos clave que podríamos usar para buscar credenciales en los sistemas operativos de Windows. Sepa cuáles de los que elegimos usar se basará principalmente en la función de la computadora. Si aterrizamos en un sistema operativo de Windows Server, podemos usar un enfoque diferente a si aterrizamos en un sistema operativo de escritorio de Windows. Siempre tenga en cuenta cómo se usa el sistema, y esto nos ayudará a saber dónde buscar. A veces incluso podemos encontrar credenciales navegando y enumerando directorios en el sistema de archivos a medida que nuestras herramientas se ejecutan.

Aquí hay otros lugares que debemos tener en cuenta cuando la caza de credenciales:

- Contraseñas en la política grupal en el sysvol Share
- Contraseñas en scripts en el sysvol compartir
- Contraseña en los scripts en él comparte
- Contraseñas en archivos web.config en máquinas Dev y comparte TI
- desatend.xml
- Contraseñas en los campos de descripción del usuario o de la computadora de la computadora
- Bases de datos KeepAss -> Tire del hash, cride y obtenga un montón de acceso.
- Encontrado en sistemas de usuario y acciones
- Archivos como pass.txt, contraseña.docx, contraseñas.xlsx encontrado en sistemas de usuario, acciones,[Sharepoint](https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration)

---

Ha obtenido acceso a la estación de trabajo de Windows 10 de un administrador de TI y comienza su proceso de caza de credenciales buscando credenciales en ubicaciones de almacenamiento comunes.

`Connect to the target and use what you've learned to discover the answers to the challenge questions`.