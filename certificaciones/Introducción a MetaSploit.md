# Introducción a MetaSploit

El`Metasploit Project`Es una plataforma de prueba de penetración modular basada en ruby que le permite escribir, probar y ejecutar el código de explotación. El usuario puede hacer personalizado por el usuario o tomar de una base de datos que contiene las últimas exploits ya descubiertas y modularizadas. El`Metasploit Framework`Incluye un conjunto de herramientas que puede usar para probar vulnerabilidades de seguridad, enumerar las redes, ejecutar ataques y evadir la detección. En su núcleo, el`Metasploit Project`es una colección de herramientas de uso común que proporciona un entorno completo para las pruebas de penetración y el desarrollo de explotación.

![](https://academy.hackthebox.com/storage/modules/39/S02_SS01.png)

El`modules`Se mencionan la prueba de conceptos de exploit real que ya se han desarrollado y probado en la naturaleza e integrado dentro del marco para proporcionar a los pentestres facilidad de acceso a diferentes vectores de ataque para diferentes plataformas y servicios. Metasploit no es un gato de todos los oficios, sino una navaja suiza con las herramientas suficientes para llevarnos a través de las vulnerabilidades sin parpadear más comunes.

Su traje fuerte es que proporciona una gran cantidad de objetivos y versiones disponibles, todos a algunos comandos de una posición exitosa. Estos, combinados con un exploit a medida a esas versiones vulnerables y con una carga útil que se envía después del exploit, que nos dará acceso real al sistema, nos proporcionará una forma fácil y automatizada de cambiar entre conexiones de destino durante nuestras empresas posteriores a la explotación.

---

# **MetaSploit Pro**

`Metasploit`Como un producto se divide en dos versiones. El`Metasploit Pro`la versión es diferente de la`Metasploit Framework`uno con algunas características adicionales:

- Cadenas de tareas
- Ingeniería social
- Validaciones de vulnerabilidad
- Guía
- Wizards de inicio rápido
- Integración de nexpose

Si es más un usuario de una línea de comandos y prefiere las características adicionales, la versión Pro también contiene su propia consola, al igual que`msfconsole`.

Para tener una idea general de lo que pueden lograr las nuevas funciones de MetaSploit Pro, consulte la lista a continuación:

| **Infiltrado** | **Recopilar datos** | **Remediar** |
| --- | --- | --- |
| Explotación manual | Importar y escanear datos | Fuerza bruta |
| Evasión antivirus | Escaneos de descubrimiento | Cadenas de tareas |
| Evasión de IPS/IDS | Meta módulos | Flujo de trabajo de explotación |
| Proxy pivote | Integración de escaneo nexpose | Repunión de la sesión |
| Después de la explotación |  | Reproducción de tareas |
| Limpieza de la sesión |  | Integración de Sonar Project |
| Reutilización de credenciales |  | Gestión de sesiones |
| Ingeniería social |  | Gestión de credenciales |
| Generador de carga útil |  | Colaboración en equipo |
| Prueba de pluma rápida |  | Interfaz web |
| VPN pivote |  | Copia de seguridad y restauración |
| Validación de vulnerabilidad |  | Exportación de datos |
| Mago de phishing |  | Recopilación de pruebas |
| Prueba de aplicaciones web |  | Informes |
| Sesiones persistentes |  | Etiquetado de datos |

---

# **Consola de marco de Metasploit**

El`msfconsole`es probablemente la interfaz más popular para el`Metasploit Framework` `(MSF)`. Proporciona una consola centralizada "todo en uno" y le permite un acceso eficiente a prácticamente todas las opciones disponibles en el`MSF`. `Msfconsole`Puede parecer intimidante al principio, pero una vez que aprenda la sintaxis de los comandos, aprenderá a apreciar el poder de utilizar esta interfaz.

Las características que`msfconsole`Generalmente traes son los siguientes:

- Es la única forma compatible de acceder a la mayoría de las características dentro de`Metasploit`
- Proporciona una interfaz basada en la consola al`Framework`
- Contiene la mayoría de las características y es la más estable`MSF`interfaz
- Soporte completo de la línea de lectura, pestañas y finalización del comando
- Ejecución de comandos externos en`msfconsole`

Ambos productos mencionados anteriormente vienen con una extensa base de datos de módulos disponibles para usar en nuestras evaluaciones. Estos, combinados con el uso de comandos externos, como escáneres, kits de herramientas de ingeniería social y generadores de carga útil, pueden convertir nuestra configuración en una máquina lista para ir a huelga que nos permitirá controlar y manipular sin problemas diferentes vulnerabilidades en la naturaleza con el uso de sesiones y trabajos de la misma manera que veremos tabulaciones en una navegación de Internet.

El término clave aquí es la usabilidad: experiencia del usuario. La facilidad con la que podemos controlar la consola puede mejorar nuestra experiencia de aprendizaje. Por lo tanto, profundicemos en los detalles.

---

# **Comprender la arquitectura**

Para operar completamente cualquier herramienta que estemos usando, primero debemos mirar debajo de su capó. Es una buena práctica, y puede ofrecernos una mejor visión de lo que ocurrirá durante nuestras evaluaciones de seguridad cuando esa herramienta entre en juego. Es esencial no tener[Cualquier comodín que pueda dejarlo a usted o a su cliente expuesto a violaciones de datos](https://www.cobaltstrike.com/blog/cobalt-strike-rce-active-exploitation-reported).

Por defecto, todos los archivos base relacionados con el marco de metasploit se pueden encontrar en`/usr/share/metasploit-framework`en nuestro`ParrotOS Security`distribución.

### **Datos, documentación, lib**

Estos son los archivos base para el marco. Los datos y Lib son las partes funcionales de la interfaz MSFConsole, mientras que la carpeta de documentación contiene todos los detalles técnicos sobre el proyecto.

### **Módulos**

Los módulos detallados anteriormente se dividen en categorías separadas en esta carpeta. Entraremos en detalles sobre estos en las siguientes secciones. Están contenidos en las siguientes carpetas:

Introducción a MetaSploit

```
xnoxos@htb[/htb]$ ls /usr/share/metasploit-framework/modulesauxiliary  encoders  evasion  exploits  nops  payloads  post

```

### **Complementos**

Los complementos ofrecen al Pentester más flexibilidad al usar el`msfconsole`Dado que se pueden cargar fácilmente o automáticamente de manera manual o automáticamente que sea necesario para proporcionar funcionalidad y automatización adicionales durante nuestra evaluación.

Introducción a MetaSploit

```
xnoxos@htb[/htb]$ ls /usr/share/metasploit-framework/plugins/aggregator.rb      ips_filter.rb  openvas.rb           sounds.rb
alias.rb           komand.rb      pcap_log.rb          sqlmap.rb
auto_add_route.rb  lab.rb         request.rb           thread.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_adduser.rb
db_credcollect.rb  msfd.rb        sample.rb            token_hunter.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb

```

### **Guiones**

Funcionalidad de meterpreter y otros scripts útiles.

Introducción a MetaSploit

```
xnoxos@htb[/htb]$ ls /usr/share/metasploit-framework/scripts/meterpreter  ps  resource  shell

```

### **Herramientas**

Utilidades de línea de comandos que se pueden llamar directamente desde el`msfconsole`menú.

Introducción a MetaSploit

```
xnoxos@htb[/htb]$ ls /usr/share/metasploit-framework/tools/context  docs     hardware  modules   payloads
dev      exploit  memdump   password  recon

```

Ahora que sabemos todas estas ubicaciones, será fácil para nosotros hacer referencia en el futuro cuando decidamos importar nuevos módulos o incluso crear otros nuevos desde cero.