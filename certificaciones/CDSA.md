# CDSA

- **Incident Handling Process**
    - Introduction
        [**Incident Handling**](Incident%20Handling.md)
        [**Cyber Kill Chain**](Cyber%20Kill%20Chain.md)
        
    - **The Incident Handling Process**
        [**Incident Handling Process Overview**](Incident%20Handling%20Process%20Overview.md)
        [**Preparation Stage (Part 1)**](Preparation%20Stage%20(Part%201).md)
        [**Preparation Stage (Part 2)**](Preparation%20Stage%20(Part%202).md)
        [**Detection & Analysis Stage (Part 1)**](Detection%20&%20Analysis%20Stage%20(Part%201).md)
        [**Detection & Analysis Stage (Part 2)**](Detection%20&%20Analysis%20Stage%20(Part%202).md)
        [**Containment, Eradication, & Recovery Stage**](Containment,%20Eradication,%20&%20Recovery%20Stage.md)
        [**Post-Incident Activity Stage**](Post-Incident%20Activity%20Stage.md)

El manejo de incidentes de seguridad se ha convertido en una parte vital de la estrategia defensiva de cada organización, ya que los ataques evolucionan constantemente y los compromisos exitosos se convierten en algo cotidiano. En este módulo, revisaremos el proceso de manejo de un incidente desde la etapa inicial de detección de un evento sospechoso hasta la confirmación de un compromiso y la respuesta al mismo.

- **Security Monitoring & SIEM Fundamentals**
    - **SIEM & SOC Fundamentals**
        [**SIEM Definition & Fundamentals**](SIEM%20Definition%20&%20Fundamentals.md)
        [**Introduction To The Elastic Stack**](Introduction%20To%20The%20Elastic%20Stack.md)
        [**SOC Definition & Fundamentals**](SOC%20Definition%20&%20Fundamentals.md)
        [**MITRE ATT&CK & Security Operations**](MITRE%20ATT&CK%20&%20Security%20Operations.md)
        [**SIEM Use Case Development**](SIEM%20Use%20Case%20Development.md)
        
    - **SIEM Visualization Development**
        [**SIEM Visualization Example 1: Failed Logon Attempts (All Users)**](SIEM%20Visualization%20Example%201%20Failed%20Logon%20Attempts.md)
        [**SIEM Visualization Example 2: Failed Logon Attempts (Disabled Users)**](SIEM%20Visualization%20Example%202%20Failed%20Logon%20Attempts.md)
        [**SIEM Visualization Example 3: Successful RDP Logon Related To Service Accounts**](SIEM%20Visualization%20Example%203%20Successful%20RDP%20Logon.md)
        [**SIEM Visualization Example 4: Users Added Or Removed From A Local Group (Within A Specific Timeframe)**](SIEM%20Visualization%20Example%204%20Users%20Added%20Or%20Remove.md)
        
    - **Clasificación de alertas**
        [**The Triaging Process**](The%20Triaging%20Process.md)

Este módulo proporciona una descripción general concisa pero completa de la gestión de eventos e información de seguridad (SIEM) y el Elastic Stack. Desmitifica el funcionamiento esencial de un Centro de operaciones de seguridad (SOC), explora la aplicación del marco MITRE ATT&CK dentro de los SOC e introduce el desarrollo de consultas SIEM (KQL). Con un enfoque en habilidades prácticas, los estudiantes aprenderán cómo desarrollar visualizaciones y casos de uso de SIEM utilizando Elastic Stack.

- **Windows Event Logs & Finding Evil**
    - Introduccion
        [**Windows Event Logs**](Windows%20Event%20Logs.md)
        [**Analyzing Evil With Sysmon & Event Logs**](Analyzing%20Evil%20With%20Sysmon%20&%20Event%20Logs.md)
        
    - **Additional Telemetry Sources**
        [**Event Tracing for Windows (ETW)**](Event%20Tracing%20for%20Windows%20(ETW).md)
        [**Tapping Into ETW**](Tapping%20Into%20ETW.md)
        
    - **Analyzing Windows Event Logs En Masse**
        [**Get-WinEvent**](HTB/certificaciones/Get-WinEvent.md)

Este módulo cubre la exploración de los registros de eventos de Windows y su importancia para descubrir actividades sospechosas. A lo largo del curso, profundizamos en la anatomía de los registros de eventos de Windows y destacamos los registros que contienen la información más valiosa para las investigaciones. El módulo también se centra en la utilización de Sysmon y registros de eventos para detectar y analizar comportamientos maliciosos. Además, profundizamos en el seguimiento de eventos para Windows (ETW), explicamos su arquitectura y componentes, y proporcionamos ejemplos de detección basados en ETW. Para agilizar el proceso de análisis, presentamos el potente cmdlet Get-WinEvent.

- **Introduction to Threat Hunting & Hunting With Elastic**
    - **Threat Hunting & Threat Intelligence Fundamentals**
        [**Threat Hunting Definition**](Threat%20Hunting%20Definition.md)
        [**The Threat Hunting Process**](The%20Threat%20Hunting%20Process.md)
        [**Threat Hunting Glossary**](Threat%20Hunting%20Glossary.md)
        [**Threat Intelligence Fundamentals**](Threat%20Intelligence%20Fundamentals.md)
        
    - **Threat Hunting With The Elastic Stack**
        [**Hunting For Stuxbot**](Hunting%20For%20Stuxbot.md)

Inicialmente, este módulo sienta las bases para comprender la caza de amenazas, desde su definición básica hasta la estructura de un equipo de caza de amenazas. El módulo también profundiza en el proceso de búsqueda de amenazas, destacando las interrelaciones entre la búsqueda de amenazas, la evaluación de riesgos y el manejo de incidentes. Además, el módulo aclara los fundamentos de Cyber Threat Intelligence (CTI). Amplía los diferentes tipos de inteligencia sobre amenazas y ofrece orientación sobre cómo interpretar eficazmente un informe de inteligencia sobre amenazas. Finalmente, el módulo pone la teoría en práctica y muestra cómo realizar una búsqueda de amenazas utilizando la pila Elastic. Este segmento práctico utiliza registros del mundo real para brindar a los alumnos una experiencia práctica.

- **Understanding Log Sources & Investigating with Splunk**
    - **Splunk Fundamentals**
        [Introduction To Splunk & SPL](Introduction%20To%20Splunk%20&%20SPL.md)
        [[Using Splunk Applications](Using%20Splunk%20Applications.md)
        
    - **Investigating With Splunk**
        [Intrusion Detection With Splunk (Real-world Scenario)](Intrusion%20Detection%20With%20Splunk%20(Real-world%20Scenar%2017b03ba572bc80ca82f2c10f52a3c89d.md)
        [Detecting Attacker Behavior With Splunk Based On TTPs](Detecting%20Attacker%20Behavior%20With%20Splunk%20Based%20On%20T.md)
        [Detecting Attacker Behavior With Splunk Based On Analytics](Detecting%20Attacker%20Behavior%20With%20Splunk%20Based%20On%20A.md)

Este módulo proporciona una introducción completa a Splunk, centrándose en su arquitectura y la creación de búsquedas SPL (lenguaje de procesamiento de búsqueda) relacionadas con la detección efectivas. Aprenderemos a investigar con Splunk como herramienta SIEM y desarrollaremos búsquedas de SPL basadas en TTP y análisis para mejorar la detección y respuesta a amenazas. A través de ejercicios prácticos, aprenderemos a identificar y comprender los datos ingeridos y los campos disponibles dentro de Splunk. También obtendremos experiencia práctica en el aprovechamiento de las poderosas funciones de Splunk para el monitoreo de seguridad y la investigación de incidentes.

- **Windows Attacks & Defense**
    - **Setting the stage**
        [Introduction and Terminology](Introduction%20and%20Terminology.md)
        [Overview and Lab Environment](Overview%20and%20Lab%20Environment.md)
        
    - **Attacks & Defense**
        [Kerberoasting](HTB/certificaciones/Kerberoasting.md)
        [AS-REProasting](HTB/certificaciones/AS-REProasting.md)
        [GPP Passwords](GPP%20Passwords.md)
        [GPO Permissions/GPO Files](GPO%20Permissions%20GPO%20Files.md)
        [Credentials in Shares](Credentials%20in%20Shares.md)
        [Credentials in Object Properties](Credentials%20in%20Object%20Properties.md)
        [DCSync](HTB/certificaciones/DCSync.md)
        [Golden Ticket](HTB/certificaciones/Golden%20Ticket.md)
        [Kerberos Constrained Delegation](HTB/certificaciones/Kerberos%20Constrained%20Delegation.md)
        [Print Spooler & NTLM Relaying](Print%20Spooler%20&%20NTLM%20Relaying.md)
        [Coercing Attacks & Unconstrained Delegation](Coercing%20Attacks%20&%20Unconstrained%20Delegation.md)
        [Object ACLs](Object%20ACLs.md)
        [PKI - ESC1](PKI%20-%20ESC1.md)

Microsoft Active Directory (AD) ha sido, durante los últimos 20 años, la suite de gestión de dominios empresariales líder, que proporciona gestión de identidades y acceso, administración centralizada de dominios, autenticación y mucho más. A lo largo de esos años, cuanto más integradas se han vuelto nuestras aplicaciones y datos con AD, más expuestos estamos a un compromiso a gran escala. En este módulo, analizaremos los ataques más comúnmente abusados y fructíferos contra entornos de Active Directory que permiten a los actores de amenazas realizar escaladas de privilegios horizontales y verticales, además del movimiento lateral. Uno de los objetivos principales del módulo es mostrar métodos de prevención y detección contra los ataques cubiertos de Active Directory.

- **Intro to Network Traffic Analysis**
    - **Introducción**
        
        [Análisis de tráfico de red](Análisis%20de%20tráfico%20de%20red.md)
        
        [Primer de redes - Capas 1-4](Primer%20de%20redes%20-%20Capas%201-4.md)
        
        [Primer de redes - Capas 5-7](Primer%20de%20redes%20-%20Capas%205-7.md)
        
    - **Análisis**
        
        [El proceso de análisis](El%20proceso%20de%20análisis.md)
        
        [Análisis en la práctica](Análisis%20en%20la%20práctica.md)
        
    - **Tcpdump**
        
        [Fundamentos tcpdump](Fundamentos%20tcpdump.md)
        
        [Capturar con TCPDUMP (Fundamentals Labs)](Capturar%20con%20TCPDUMP%20(Fundamentals%20Labs).md)
        
        [Filtrado de paquetes tcpdump](Filtrado%20de%20paquetes%20tcpdump.md)
        
        [Interrogar el tráfico de red con filtros de captura y visualización](Interrogar%20el%20tráfico%20de%20red%20con%20filtros%20de%20captu.md)
        
    - **Wireshark**
        
        [Análisis con Wireshark](Análisis%20con%20Wireshark.md)
        
        [Familiaridad con Wireshark](Familiaridad%20con%20Wireshark.md)
        
        [Uso avanzado de Wireshark](Uso%20avanzado%20de%20Wireshark.md)
        
        [Incepción de paquetes, diseccionar el tráfico de red con Wireshark](Incepción%20de%20paquetes,%20diseccionar%20el%20tráfico.md)
        
        [Laboratorio guiado: flujo de trabajo de análisis de tráfico](Laboratorio%20guiado%20flujo%20de%20trabajo%20de%20análisis%20d.md)
        
        [Descifrar conexiones RDP](Descifrar%20conexiones%20RDP.md)
        
    

Los equipos de seguridad utilizan el análisis del tráfico de red para monitorear la actividad de la red y buscar anomalías que podrían indicar problemas operativos y de seguridad. Los profesionales de seguridad ofensivos pueden utilizar el análisis del tráfico de red para buscar datos confidenciales como credenciales, aplicaciones ocultas, segmentos de red accesibles u otra información potencialmente confidencial "en el cable". El análisis del tráfico de red tiene muchos usos tanto para atacantes como para defensores.

- **Intermediate Network Traffic Analysis**
    - **Introduction**
        
        [Intermediate Network Traffic Analysis Overview](Intermediate%20Network%20Traffic%20Analysis%20Overview.md)
        
    - **Link Layer Attacks**
        
        [ARP Spoofing & Abnormality Detection](HTB/certificaciones/ARP%20Spoofing%20&%20Abnormality%20Detection.md)
        
        [ARP Scanning & Denial-of-Service](HTB/certificaciones/ARP%20Scanning%20&%20Denial-of-Service.md)
        
        [802.11 Denial-of-Service](802%2011%20Denial-of-Service%201a603ba572bc80f2b003eb4df924e046.md)
        
        [Rogue Access Point & Evil-Twin Attacks](HTB/certificaciones/Rogue%20Access%20Point%20&%20Evil-Twin%20Attacks.md)
        
    - **Detecting Network Abnormalities**
        
        [Fragmentation Attacks](HTB/certificaciones/Fragmentation%20Attacks.md)
        
        [IP Source & Destination Spoofing Attacks](HTB/certificaciones/IP%20Source%20&%20Destination%20Spoofing%20Attacks.md)
        
        [IP Time-to-Live Attacks](HTB/certificaciones/IP%20Time-to-Live%20Attacks.md)
        
        [TCP Handshake Abnormalities](HTB/certificaciones/TCP%20Handshake%20Abnormalities.md)
        
        [TCP Connection Resets & Hijacking](HTB/certificaciones/TCP%20Connection%20Resets%20&%20Hijacking.md)
        
        [ICMP Tunneling](HTB/certificaciones/ICMP%20Tunneling.md)
        
    - **Application Layer Attacks**
        
        [HTTP/HTTPs Service Enumeration Detection](HTTP%20HTTPs%20Service%20Enumeration%20Detection.md)
        
        [Strange HTTP Headers](HTB/certificaciones/Strange%20HTTP%20Headers.md)
        
        [Cross-Site Scripting (XSS) & Code Injection Detection](Cross-Site%20Scripting%20(XSS)%20&%20Code%20Injection%20Detect.md)
        
        [SSL Renegotiation Attacks](HTB/certificaciones/SSL%20Renegotiation%20Attacks.md)
        
        [Peculiar DNS Traffic](HTB/certificaciones/Peculiar%20DNS%20Traffic.md)
        
        [Strange Telnet & UDP Connections](HTB/certificaciones/Strange%20Telnet%20&%20UDP%20Connections.md)
        

A través del análisis del tráfico de red, este módulo mejora las habilidades para detectar ataques a la capa de enlace, como anomalías de ARP y puntos de acceso no autorizados, identificar anomalías de la red como suplantación de IP e irregularidades en el protocolo de enlace TCP, y descubrir amenazas a la capa de aplicación, desde vulnerabilidades basadas en la web hasta actividades DNS peculiares.

- **Working with IDS/IPS**
    
    [**Introduction To IDS/IPS**](Introduction%20To%20IDS%20IPS.md)
    
    - **Suricata**
        
        [Fundamentos de Suricata](Fundamentos%20de%20Suricata.md)
        
        [Desarrollo de reglas de Suricata Parte 1](Desarrollo%20de%20reglas%20de%20Suricata%20Parte%201.md)
        
        [Desarrollo de reglas de Suricata Parte 2 (tráfico cifrado)](Desarrollo%20de%20reglas%20de%20Suricata%20Parte%202%20(tra%CC%81fico%201b003ba572bc8046ab3cf86408037061.md)
        
    - Snort
        
        [Snort Fundaments](Snort%20Fundaments.md)
        
        [Desarrollo de reglas de inicio](Desarrollo%20de%20reglas%20de%20inicio.md)
        
    - Zeek
        
        [Fundamentos de Zeek](Fundamentos%20de%20Zeek.md)
        
        [Detección de intrusión con Zeek](Detección%20de%20intrusión%20con%20Zeek.md)
        
    

Este módulo ofrece una exploración en profundidad de Suricata, Snort y Zeek, cubriendo tanto el desarrollo de reglas como la detección de intrusiones. Lo guiaremos a través del desarrollo de reglas basadas en firmas y análisis, y aprenderá a abordar el tráfico cifrado. El módulo presenta numerosos ejemplos prácticos, centrándose en la detección de malware frecuente como PowerShell Empire, Covenant, Sliver, Cerber, Dridex, Ursnif y Patchwork. También nos sumergimos en la detección de técnicas de ataque como exfiltración de DNS, exfiltración TLS/HTTP, movimiento lateral de PsExec y balizas a través de IDS/IPS.

- **Introduction to Malware Analysis**
    
    [**Introducción al análisis de malware y malware**](Introducción%20al%20análisis%20de%20malware%20y%20malware.md)
    
    - **Prerequisites**
        
        [**Windows -THERAL**](Windows%20-THERAL.md)
        
    - **Static Analysis**
        
        [**Análisis estático en Linux**](Análisis%20estático%20en%20Linux.md)
        
        [**Análisis estático en Windows**](Análisis%20estático%20en%20Windows.md)
        
    
    [**Dynamic Analysis**](HTB/certificaciones/Dynamic%20Analysis.md)
    
    - **Code Analysis**
        
        [**Análisis de código**](Análisis%20de%20código.md)
        
        [**Debugging**](HTB/certificaciones/Debugging.md)
        
    
    [**Creating Detection Rules**](HTB/certificaciones/Creating%20Detection%20Rules.md)
    

Este módulo ofrece una exploración del análisis de malware, específicamente dirigido a amenazas basadas en Windows. El módulo cubre el análisis estático utilizando herramientas de Linux y Windows, desempaquetado de malware, análisis dinámico (incluido el análisis de tráfico de malware), ingeniería inversa para análisis de código y depuración utilizando x64dbg. Se analizan ejemplos de malware del mundo real como WannaCry, DoomJuice, Brbbot, Dharma y Meterpreter para proporcionar experiencia práctica.

- **JavaScript Deobfuscation**
    - **Introduction**
        
        [**Introduction**](Introduction.md)
        
        [**Source Code**](Source%20Code.md)
        
    - **Obfuscation**
        
        [**Code Obfuscation**](Code%20Obfuscation.md)
        
        [**Basic Obfuscation**](Basic%20Obfuscation.md)
        
        [**Advanced Obfuscation**](Advanced%20Obfuscation.md)
        
        [**Deobfuscation**](Deobfuscation.md)
        
    - **Deobfuscation Examples**
        
        [**Code Analysis**](HTB/certificaciones/Code%20Analysis.md)
        
        [**HTTP Requests**](HTB/certificaciones/HTTP%20Requests.md)
        
        [**Decoding**](HTB/certificaciones/Decoding.md)
        

Este módulo lo llevará paso a paso a través de los fundamentos de la desofuscación de JavaScript hasta que pueda desofuscar el código JavaScript básico y comprender su propósito.

- **YARA & Sigma for SOC Analysts**
    
    [**Introduction to YARA & Sigma**](Introduction%20to%20YARA%20&%20Sigma.md)
    
    - **Aprovechando a Yara**
        
        [**Reglas de Yara y Yara**](Reglas%20de%20Yara%20y%20Yara.md)
        
        [**Desarrollo de las reglas de Yara**](Desarrollo%20de%20las%20reglas%20de%20Yara.md)
        
        [**Hunting Evil con Yara (edición de Windows)**](Hunting%20Evil%20con%20Yara%20(edición%20de%20Windows).md)
        
        [**Hunting Evil con Yara (edición de Linux)**](Hunting%20Evil%20con%20Yara%20(edición%20de%20Linux).md)
        
        [**Hunting Evil con Yara (edición web)**](Hunting%20Evil%20con%20Yara%20(edición%20web).md)
        
    - Leveraging and Sigma Rules
        
        
    - **Leveraging Sigma**
        
        [**Reglas de Sigma y Sigma**](Reglas%20de%20Sigma%20y%20Sigma.md)
        
        [**Desarrollo de reglas de Sigma**](Desarrollo%20de%20reglas%20de%20Sigma.md)
        
        [**Caza del mal con Sigma (edición de motosierra)**](Caza%20del%20mal%20con%20Sigma%20(edición%20de%20motosierra).md)
        
        [**Hunting Evil con Sigma (edición Splunk)**](Hunting%20Evil%20con%20Sigma%20(edición%20Splunk).md)
        

Este módulo de Hack The Box Academy cubre cómo crear reglas YARA tanto de forma manual como automática y aplicarlas para buscar amenazas en el disco, procesos en vivo, memoria y bases de datos en línea. Luego, el módulo cambia a las reglas de Sigma que cubren cómo crear reglas de Sigma, traducirlas en consultas SIEM usando "sigmac" y buscar amenazas tanto en registros de eventos como en soluciones SIEM. Todo es práctico y se utilizan técnicas y malware del mundo real.

- **Introduction to Digital Forensics**
    
    [**Introducción a los forenses digitales**](Introducción%20a%20los%20forenses%20digitales.md)
    
    [**Descripción general forense de Windows**](Descripción%20general%20forense%20de%20Windows.md)
    
    [**Técnicas y herramientas de adquisición de evidencia**](Técnicas%20y%20herramientas%20de%20adquisición%20de%20eviden.md)
    
    - **Evidence Examination & Analysis**
        
        [**Forense de memoria**](Forense%20de%20memoria.md)
        
        [**Disco forense**](Disco%20forens.md)
        
        [**Herramientas rápidas de examen y análisis**](Herramientas%20rápidas%20de%20examen%20y%20análisis.md)
        
        [**Escenario forense digital práctico**](Escenario%20forense%20digital%20práctico.md)
        

Sumérgete en la ciencia forense digital de Windows con el módulo "Introducción a la ciencia forense digital" de Hack The Box Academy. Obtenga dominio sobre conceptos y herramientas forenses básicos como FTK Imager, KAPE, Velociraptor y Volatility. Profundice en análisis forense de memoria, análisis de imágenes de disco y procedimientos de clasificación rápida. Aprenda a construir líneas de tiempo a partir de MFT, USN Journals y registros de eventos de Windows mientras practica artefactos clave como MFT, USN Journal, Registry Hives, Prefetch Files, ShimCache, Amcache, BAM y datos SRUM.

- **Detecting Windows Attacks with Splunk**
    - **Leveraging Windows Event Logs**
        
        [**Leveraging Windows Event Logs**](Leveraging%20Windows%20Event%20Logs.md)
        
        [**Detectar la pulverización de contraseña**](Detectar%20la%20pulverización%20de%20contrase.md)
        
        [**Detectar ataques con forma de respondedor**](Detectar%20ataques%20con%20forma%20de%20respondedor.md)
        
        [**Detección de querberoasting/aspersación**](Detección%20de%20querberoasting%20aspersación.md)
        
        [**Detectar el paso de pase**](Detectar%20el%20paso%20de%20pa.md)
        
        [**Detectar pasar el boleto**](Detectar%20pasar%20el%20bole.md)
        
        [**Detección de paso elevado**](Detección%20de%20paso%20elevado.md)
        
        [**Detección de entradas doradas/boletos de plata**](Detección%20de%20entradas%20doradas%20boletos%20de%20plata.md)
        
        [**Detección de delegación sin restricciones/ataques de delegación restringidos**](Detección%20de%20delegación%20sin%20restricciones%20ataque.md)
        
        [**Detección de DCSYNC/DCSHADOW**](Detección%20de%20DCSYNC%20DCSHADOW.md)
        
    - **Leveraging Splunk's Application Capabilities**
        
        [**Creación de aplicaciones Splunk personalizadas**](Creación%20de%20aplicaciones%20Splunk%20personalizadas.md)
        
    - **Leveraging Zeek Logs**
        
        [**Detección de ataques de fuerza bruta RDP**](Detección%20de%20ataques%20de%20fuerza%20bruta%20RDP.md)
        
        [**Detección de malware de baliza**](Detección%20de%20malware%20de%20baliza.md)
        
        [**Detección de escaneo de puertos NMAP**](Detección%20de%20escaneo%20de%20puertos%20NMAP.md)
        
        [**Detección de ataques de la fuerza bruta de Kerberos**](Detección%20de%20ataques%20de%20la%20fuerza%20bruta%20de%20Kerber.md)
        
        [**Detección de querberoasting**](Detección%20de%20querberoasting.md)
        
        [**Detección de boletos dorados**](Detección%20de%20boletos%20dorados.md)
        
        [**Detectando el psexec de Cobalt Strike**](Detectando%20el%20psexec%20de%20Cobalt%20Strike.md)
        
        [**Detección de zerologon**](Detección%20de%20zerologon.md)
        
        [**Detección de exfiltración (HTTP)**](Detección%20de%20exfiltración%20(HTTP).md)
        
        [**Detección de exfiltración (DNS)**](Detección%20de%20exfiltración%20(DNS).md)
        
        [**Detección de ransomware**](Detección%20de%20ransomware.md)
        

Este módulo de Hack The Box Academy se centra en identificar ataques en Windows y Active Directory. Utilizando Splunk como piedra angular para la investigación, esta capacitación brindará a los participantes la experiencia necesaria para identificar hábilmente las amenazas basadas en Windows aprovechando los registros de eventos de Windows y los registros de red de Zeek. Además, los participantes se beneficiarán de los archivos PCAP reales asociados con los ataques de Windows y Active Directory discutidos, mejorando su comprensión de los respectivos patrones y técnicas de ataque.

- **Security Incident Reporting**
    [**Introducción al informe de incidentes de seguridad**](Introducción%20al%20informe%20de%20incidentes%20de%20seguridad.md)
    [**El proceso de informes de incidentes**](El%20proceso%20de%20informes%20de%20incidentes.md)
    [**Elementos de un informe incidente adecuado**](Elementos%20de%20un%20informe%20incidente%20adecuado.md)
    [**Comunicación**](Comunicación.md)
    [**Informe de incidentes del mundo real**](Informe%20de%20incidentes%20del%20mundo%20real.md)

Diseñado para brindar una comprensión integral, este módulo de Hack The Box Academy garantiza que los participantes sean expertos en identificar, categorizar y documentar incidentes de seguridad con la máxima precisión y profesionalismo. El módulo desglosa meticulosamente los elementos de un informe de incidente sólido y luego presenta a los participantes un informe de incidente del mundo real, ofreciendo información práctica sobre la aplicación de los conceptos discutidos.