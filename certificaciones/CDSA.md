
- **Incident Handling Process**
    - Introduction
	    - [**Incident Handling**](module/148/section/1362.md)
        - [**Cyber Kill Chain**](module/148/section/1363.md)
    - **The Incident Handling Process**
	    - [**Incident Handling Process Overview**](module/148/section/1364.md)
	    - [**Preparation Stage (Part 1)**](module/148/section/1365.md)
	    - [**Preparation Stage (Part 2)**](module/148/section/1366.md)
	    - [**Detection & Analysis Stage (Part 1)**](module/148/section/1367.md)
	    - [**Detection & Analysis Stage (Part 2)**](module/148/section/1368.md)
	    - [**Containment, Eradication, & Recovery Stage**](module/148/section/1369.md)
	    - [**Post-Incident Activity Stage**](module/148/section/1370.md)

El manejo de incidentes de seguridad se ha convertido en una parte vital de la estrategia defensiva de cada organización, ya que los ataques evolucionan constantemente y los compromisos exitosos se convierten en algo cotidiano. En este módulo, revisaremos el proceso de manejo de un incidente desde la etapa inicial de detección de un evento sospechoso hasta la confirmación de un compromiso y la respuesta al mismo.

- **Security Monitoring & SIEM Fundamentals**
    - **SIEM & SOC Fundamentals**
	    - [**SIEM Definition & Fundamentals**](2250.md)
	    - [**Introduction To The Elastic Stack**](2273.md)
	    - [**SOC Definition & Fundamentals**](2251.md)
	    - [**MITRE ATT&CK & Security Operations**](2252.md)
	    - [**SIEM Use Case Development**](2253.md)
    - **SIEM Visualization Development**
	    - [**SIEM Visualization Example 1: Failed Logon Attempts (All Users)**](2255.md)
	    - [**SIEM Visualization Example 2: Failed Logon Attempts (Disabled Users)**](2274.md)
	    - [**SIEM Visualization Example 3: Successful RDP Logon Related To Service Accounts**](2275.md)
	    - [**SIEM Visualization Example 4: Users Added Or Removed From A Local Group (Within A Specific Timeframe)**](2276.md)
    - **Clasificación de alertas**
	    - [**The Triaging Process**](2286.md)

Este módulo proporciona una descripción general concisa pero completa de la gestión de eventos e información de seguridad (SIEM) y el Elastic Stack. Desmitifica el funcionamiento esencial de un Centro de operaciones de seguridad (SOC), explora la aplicación del marco MITRE ATT&CK dentro de los SOC e introduce el desarrollo de consultas SIEM (KQL). Con un enfoque en habilidades prácticas, los estudiantes aprenderán cómo desarrollar visualizaciones y casos de uso de SIEM utilizando Elastic Stack.

- **Windows Event Logs & Finding Evil**
    - Introduccion
	    - [**Windows Event Logs**](2300.md)
	    - [**Analyzing Evil With Sysmon & Event Logs**](2301.md)
    - **Additional Telemetry Sources**
	    - [**Event Tracing for Windows (ETW)**](2302.md)
	    - [**Tapping Into ETW**](2325.md)
    - **Analyzing Windows Event Logs En Masse**
	    - [**Get-WinEvent**](2322.md)

Este módulo cubre la exploración de los registros de eventos de Windows y su importancia para descubrir actividades sospechosas. A lo largo del curso, profundizamos en la anatomía de los registros de eventos de Windows y destacamos los registros que contienen la información más valiosa para las investigaciones. El módulo también se centra en la utilización de Sysmon y registros de eventos para detectar y analizar comportamientos maliciosos. Además, profundizamos en el seguimiento de eventos para Windows (ETW), explicamos su arquitectura y componentes, y proporcionamos ejemplos de detección basados en ETW. Para agilizar el proceso de análisis, presentamos el potente cmdlet Get-WinEvent.

- **Introduction to Threat Hunting & Hunting With Elastic**
    - **Threat Hunting & Threat Intelligence Fundamentals**
	    - [**Threat Hunting Definition**](2280.md)
	    - [**The Threat Hunting Process**](2295.md)
	    - [**Threat Hunting Glossary**](2282.md)
	    - [**Threat Intelligence Fundamentals**](2281.md)
    - **Threat Hunting With The Elastic Stack**
	    - [**Hunting For Stuxbot**](2285.md)

Inicialmente, este módulo sienta las bases para comprender la caza de amenazas, desde su definición básica hasta la estructura de un equipo de caza de amenazas. El módulo también profundiza en el proceso de búsqueda de amenazas, destacando las interrelaciones entre la búsqueda de amenazas, la evaluación de riesgos y el manejo de incidentes. Además, el módulo aclara los fundamentos de Cyber Threat Intelligence (CTI). Amplía los diferentes tipos de inteligencia sobre amenazas y ofrece orientación sobre cómo interpretar eficazmente un informe de inteligencia sobre amenazas. Finalmente, el módulo pone la teoría en práctica y muestra cómo realizar una búsqueda de amenazas utilizando la pila Elastic. Este segmento práctico utiliza registros del mundo real para brindar a los alumnos una experiencia práctica.

- **Understanding Log Sources & Investigating with Splunk**
    - **Splunk Fundamentals**
	    - [Introduction To Splunk & SPL](2356.md)
	    - [Using Splunk Applications](2389.md)
    - **Investigating With Splunk**
	    - [Intrusion Detection With Splunk](2357.md)
	    - [Detecting Attacker Behavior With Splunk Based On TTPs](2388.md)
	    - [Detecting Attacker Behavior With Splunk Based On Analytics](2390.md)

Este módulo proporciona una introducción completa a Splunk, centrándose en su arquitectura y la creación de búsquedas SPL (lenguaje de procesamiento de búsqueda) relacionadas con la detección efectivas. Aprenderemos a investigar con Splunk como herramienta SIEM y desarrollaremos búsquedas de SPL basadas en TTP y análisis para mejorar la detección y respuesta a amenazas. A través de ejercicios prácticos, aprenderemos a identificar y comprender los datos ingeridos y los campos disponibles dentro de Splunk. También obtendremos experiencia práctica en el aprovechamiento de las poderosas funciones de Splunk para el monitoreo de seguridad y la investigación de incidentes.

- **Windows Attacks & Defense**
    - **Setting the stage** 
	    - [Introduction and Terminology](1753.md)
	    - [Overview and Lab Enviroment](1754.md)
    - **Attacks & Defense**
	    - [Kerberoasting](1778.md)
	    - [AS-REProasting](1779.md)
	    - [GPP Passwords](1780.md)
	    - [GPO Permissions/GPO Files](1781.md)
	    - [Credentials in Shares](1782.md)
	    - [Credentials in Object Properties](1783.md)
	    - [DCSync](1784.md)
	    - [Golden Ticket](1785.md)
	    - [Kerberos Constrained Delegation](1786.md)
	    - [Print Spooler & NTLM Relaying](1787.md)
	    - [Coercing Attacks & Unconstrained Delegation](1788.md)
	    - [Object ACLs](1789.md)
	    - [PKI - ESC1](1790.md)

Microsoft Active Directory (AD) ha sido, durante los últimos 20 años, la suite de gestión de dominios empresariales líder, que proporciona gestión de identidades y acceso, administración centralizada de dominios, autenticación y mucho más. A lo largo de esos años, cuanto más integradas se han vuelto nuestras aplicaciones y datos con AD, más expuestos estamos a un compromiso a gran escala. En este módulo, analizaremos los ataques más comúnmente abusados y fructíferos contra entornos de Active Directory que permiten a los actores de amenazas realizar escaladas de privilegios horizontales y verticales, además del movimiento lateral. Uno de los objetivos principales del módulo es mostrar métodos de prevención y detección contra los ataques cubiertos de Active Directory.

- **Intro to Network Traffic Analysis**
    - **Introducción**
	    - [Network Traffic Analysis](773.md)
	    - [Networking Primer - Layers 1-4](954.md)
	    - [Networking Primer - Layers 5-7](963.md)
    - **Análisis**
	    - [The Analysis Process](953.md)
	    - [Analysis in Practice](957.md)
    - **Tcpdump**
	    - [Tcpdump Fundamentals](774.md)
	    - [Fundamentals Lab](786.md)
	    - [Tcpdump Packet Filtering](785.md)
	    - [Interrogating Network Traffic With Capture and Display Filters](787.md)
    - **Wireshark**
	    - [Analysis with Wireshark](775.md)
	    - [Familiarity With Wireshark](788.md)
	    - [Wireshark Advanced Usage](800.md)
	    - [Packet Inception, Dissecting Network Traffic With Wireshark](789.md)
	    - [Guided Lab: Traffic Analysis Workflow](962.md)
	    - [# Decrypting RDP connections](964.md)

Los equipos de seguridad utilizan el análisis del tráfico de red para monitorear la actividad de la red y buscar anomalías que podrían indicar problemas operativos y de seguridad. Los profesionales de seguridad ofensivos pueden utilizar el análisis del tráfico de red para buscar datos confidenciales como credenciales, aplicaciones ocultas, segmentos de red accesibles u otra información potencialmente confidencial "en el cable". El análisis del tráfico de red tiene muchos usos tanto para atacantes como para defensores.

- **Intermediate Network Traffic Analysis**
    - **Introduction**
	    - [Intermediate Network Traffic Analysis Overview](2445.md)
    - **Link Layer Attacks**
	    - [ARP Spoofing & Abnormality Detection](2446.md)
	    - [ARP Scanning & Denial-of-Service](2447.md)
	    - [802.11 Denial-of-Service](2448.md)
	    - [Rogue Access Point & Evil-Twin Attacks](2449.md)
    - **Detecting Network Abnormalities**
	    - [Fragmentation Attacks](2452.md)
	    - [IP Source & Destination Spoofing Attacks](2456.md)
	    - [IP Time-to-Live Attacks](2457.md)
	    - [TCP Handshake Abnormalities](2458.md)
	    - [TCP Connection Resets & Hijacking](2460.md)
	    - [ICMP Tunneling](2461.md)
    - **Application Layer Attacks**
	    - [HTTP/HTTPs Service Enumeration Detection](2459.md)
	    - [Strange HTTP Headers](2464.md)
	    - [Cross-Site Scripting (XSS) & Code Injection Detection](2465.md)
	    - [SSL Renegotiation Attacks](2466.md)
	    - [Peculiar DNS Traffic](2467.md)
	    - [Strange Telnet & UDP Connections](2468.md)
        
A través del análisis del tráfico de red, este módulo mejora las habilidades para detectar ataques a la capa de enlace, como anomalías de ARP y puntos de acceso no autorizados, identificar anomalías de la red como suplantación de IP e irregularidades en el protocolo de enlace TCP, y descubrir amenazas a la capa de aplicación, desde vulnerabilidades basadas en la web hasta actividades DNS peculiares.

- **Working with IDS/IPS**
	- [**Introduction To IDS/IPS**](2419.md)
    - **Suricata**
	    - [Suricata Fundamentals](2412.md)
	    - [Suricata Rule Development Part 1](2415.md)
	    - [Suricata Rule Development Part 2 (Encrypted Traffic)](2451.md)
    - Snort
	    - [Snort Fundaments](2413.md)
	    - [Snort Rule Development](2416.md)
    - Zeek
	    - [Zeek Fundamentals](2414.md)
	    - [Intrusion Detection With Zeek](2417.md)

Este módulo ofrece una exploración en profundidad de Suricata, Snort y Zeek, cubriendo tanto el desarrollo de reglas como la detección de intrusiones. Lo guiaremos a través del desarrollo de reglas basadas en firmas y análisis, y aprenderá a abordar el tráfico cifrado. El módulo presenta numerosos ejemplos prácticos, centrándose en la detección de malware frecuente como PowerShell Empire, Covenant, Sliver, Cerber, Dridex, Ursnif y Patchwork. También nos sumergimos en la detección de técnicas de ataque como exfiltración de DNS, exfiltración TLS/HTTP, movimiento lateral de PsExec y balizas a través de IDS/IPS.

- **Introduction to Malware Analysis**
	- [**Introducción al análisis de malware y malware**](2492.md)
    - **Prerequisites**
	    - [Windows Internals](2493.md)
    - **Static Analysis**
	    - [**Análisis estático en Linux**](2494.md)
	    - [**Análisis estático en Windows**](2495.md)
	- [**Dynamic Analysis**](2500.md)
    - **Code Analysis**
	    - [**Análisis de código**](2499.md)
	    - [**Debugging**](2496.md)
    - [**Creating Detection Rules**](2497.md)
    
Este módulo ofrece una exploración del análisis de malware, específicamente dirigido a amenazas basadas en Windows. El módulo cubre el análisis estático utilizando herramientas de Linux y Windows, desempaquetado de malware, análisis dinámico (incluido el análisis de tráfico de malware), ingeniería inversa para análisis de código y depuración utilizando x64dbg. Se analizan ejemplos de malware del mundo real como WannaCry, DoomJuice, Brbbot, Dharma y Meterpreter para proporcionar experiencia práctica.

- **JavaScript Deobfuscation**
    - **Introduction**
        - [**Introduction**](449.md)
        - [**Source Code**](441.md)
    - **Obfuscation**
        - [**Code Obfuscation**](440.md)
        - [**Basic Obfuscation**](447.md)
        - [**Advanced Obfuscation**](448.md)
        - [**Deobfuscation**](442.md)
    - **Deobfuscation Examples**
        - [**Code Analysis**](443.md)
        - [**HTTP Requests**](444.md)
        - [**Decoding**](445.md)

Este módulo lo llevará paso a paso a través de los fundamentos de la desofuscación de JavaScript hasta que pueda desofuscar el código JavaScript básico y comprender su propósito.

- **YARA & Sigma for SOC Analysts**
    - [**Introduction to YARA & Sigma**](2511.md)
    - **Aprovechando a Yara**
        - [**Reglas de Yara y Yara**](2513.md)
        - [**Desarrollo de las reglas de Yara**](2514.md)
        - [**Hunting Evil con Yara (edición de Windows)**](2515.md)
        - [**Hunting Evil con Yara (edición de Linux)**](2574.md)
        - [**Hunting Evil con Yara (edición web)**](2575.md)
    - Leveraging and Sigma Rules
    - **Leveraging Sigma**
        - [**Reglas de Sigma y Sigma**](2512.md)
        - [**Desarrollo de reglas de Sigma**](2571.md)
        - [**Caza del mal con Sigma (edición de motosierra)**](2572.md)
        - [**Hunting Evil con Sigma (edición Splunk)**](2516.md)

Este módulo de Hack The Box Academy cubre cómo crear reglas YARA tanto de forma manual como automática y aplicarlas para buscar amenazas en el disco, procesos en vivo, memoria y bases de datos en línea. Luego, el módulo cambia a las reglas de Sigma que cubren cómo crear reglas de Sigma, traducirlas en consultas SIEM usando "sigmac" y buscar amenazas tanto en registros de eventos como en soluciones SIEM. Todo es práctico y se utilizan técnicas y malware del mundo real.

- **Introduction to Digital Forensics**
    - [**Introducción a los forenses digitales**](2608.md)
    - [**Descripción general forense de Windows**](2629.md)
    - [**Técnicas y herramientas de adquisición de evidencia**](2609.md)
- **Evidence Examination & Analysis**
    - [**Forense de memoria**](2610.md)
    - [**Disco forense**](2611.md)
     - [**Herramientas rápidas de examen y análisis**](2612.md)
    - [**Escenario forense digital práctico**](2613.md)

Sumérgete en la ciencia forense digital de Windows con el módulo "Introducción a la ciencia forense digital" de Hack The Box Academy. Obtenga dominio sobre conceptos y herramientas forenses básicos como FTK Imager, KAPE, Velociraptor y Volatility. Profundice en análisis forense de memoria, análisis de imágenes de disco y procedimientos de clasificación rápida. Aprenda a construir líneas de tiempo a partir de MFT, USN Journals y registros de eventos de Windows mientras practica artefactos clave como MFT, USN Journal, Registry Hives, Prefetch Files, ShimCache, Amcache, BAM y datos SRUM.

- **Detecting Windows Attacks with Splunk**
    - **Leveraging Windows Event Logs**
        - [**Leveraging Windows Event Logs**](2523.md)
        - [**Detectar la pulverización de contraseña**](2524.md)
        - [**Detectar ataques con forma de respondedor**](2525.md)
        - [**Detección de querberoasting/aspersación**](2526.md)
        - [**Detectar el paso de pase**](2527.md)
        - [**Detectar pasar el boleto**](2528.md)
        - [**Detección de paso elevado**](2532.md)
        - [**Detección de entradas doradas/boletos de plata**](2529.md)
        - [**Detección de delegación sin restricciones/ataques de delegación restringidos**](2530.md)
        - [**Detección de DCSYNC/DCSHADOW**](2531.md)
        
    - **Leveraging Splunk's Application Capabilities**
        - [**Creación de aplicaciones Splunk personalizadas**](2533.md)
        
    - **Leveraging Zeek Logs**
        - [**Detección de ataques de fuerza bruta RDP**](2554.md)
        - [**Detección de malware de baliza**](2555.md)
        - [**Detección de escaneo de puertos NMAP**](2556.md)
        - [**Detección de ataques de la fuerza bruta de Kerberos**](2558.md)
        - [**Detección de querberoasting**](2559.md)
        - [**Detección de boletos dorados**](2560.md)
        - [**Detectando el psexec de Cobalt Strike**](2561.md)
        - [**Detección de zerologon**](2562.md)
        - [**Detección de exfiltración (HTTP)**](2564.md)
        - [**Detección de exfiltración (DNS)**](2565.md)
        - [**Detección de ransomware**](2566.md)

Este módulo de Hack The Box Academy se centra en identificar ataques en Windows y Active Directory. Utilizando Splunk como piedra angular para la investigación, esta capacitación brindará a los participantes la experiencia necesaria para identificar hábilmente las amenazas basadas en Windows aprovechando los registros de eventos de Windows y los registros de red de Zeek. Además, los participantes se beneficiarán de los archivos PCAP reales asociados con los ataques de Windows y Active Directory discutidos, mejorando su comprensión de los respectivos patrones y técnicas de ataque.

- **Security Incident Reporting**
	- [**Introducción al informe de incidentes de seguridad**](2580.md)
	- [**El proceso de informes de incidentes**](2582.md)
	- [**Elementos de un informe incidente adecuado**](2581.md)
	- [**Comunicación**](2583.md)
	- [**Informe de incidentes del mundo real**](2584.md)

Diseñado para brindar una comprensión integral, este módulo de Hack The Box Academy garantiza que los participantes sean expertos en identificar, categorizar y documentar incidentes de seguridad con la máxima precisión y profesionalismo. El módulo desglosa meticulosamente los elementos de un informe de incidente sólido y luego presenta a los participantes un informe de incidente del mundo real, ofreciendo información práctica sobre la aplicación de los conceptos discutidos.