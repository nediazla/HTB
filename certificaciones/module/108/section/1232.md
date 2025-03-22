# Configuración avanzada

Podemos configurar una serie de configuraciones avanzadas para Nessus y sus escaneos, como políticas de escaneo, complementos y credenciales, todas las cuales cubriremos en esta sección.

---

# **Políticas de escaneo**

Nessus nos da la opción de crear políticas de escaneo. Esencialmente, estos son escaneos personalizados que nos permiten definir opciones de escaneo específicas, guardar la configuración de la política y tenerlos disponibles para nosotros bajo`Scan Templates`Al crear un nuevo escaneo. Esto nos brinda la capacidad de crear escaneos específicos para cualquier número de escenarios, como un escaneo más lento y evasivo, un escaneo centrado en la web o un escaneo para un cliente en particular que usa uno o varios conjuntos de credenciales. Las políticas de escaneo se pueden importar de otros escáneres de Nessus o exportarse para que luego se importen a otro escáner Nessus.

![](https://academy.hackthebox.com/storage/modules/108/nessus/nessus_policies.png)

---

# **Creación de una política de escaneo**

Para crear una política de escaneo, podemos hacer clic en el`New Policy`Botón en la parte superior derecha, y se nos presentará la lista de escaneos preconfigurados. Podemos elegir un escaneo, como el`Basic Network Scan`, luego personalícelo, o podemos crear el nuestro. Elegiremos`Advanced Scan`Para crear un escaneo totalmente personalizado sin recomendaciones preconfiguradas incorporadas.

Después de elegir el tipo de escaneo como nuestra base, podemos darle un nombre a la política de escaneo y una descripción si es necesario:

![](https://academy.hackthebox.com/storage/modules/108/nessus/policy.png)

Desde aquí, podemos configurar la configuración, agregar las credenciales necesarias y especificar cualquier estándar de cumplimiento para ejecutar el escaneo. También podemos elegir habilitar o deshabilitarlo todo[complemento](https://docs.tenable.com/nessus/Content/Plugins.htm)familias o complementos individuales.

Una vez que hayamos terminado de personalizar el escaneo, podemos hacer clic en

```
Save
```

y la política recién creada aparecerá en la lista de políticas. De aquí en adelante, cuando vamos a crear un nuevo escaneo, habrá una nueva pestaña nombrada

```
User Defined
```

bajo

```
Scan Templates
```

que mostrarán todas nuestras políticas de escaneo personalizadas:

![](https://academy.hackthebox.com/storage/modules/108/nessus/htb_policydefined.png)

---

# **Complementos de Nessus**

Nessus trabaja con complementos escritos en el[Nessus Attack Scripting Language (NASL)](https://en.wikipedia.org/wiki/Nessus_Attack_Scripting_Language)y puede apuntar a nuevas vulnerabilidades y CVE. Estos complementos contienen información como el nombre de la vulnerabilidad, el impacto, la remediación y una forma de probar la presencia de un problema en particular.

Los complementos se clasifican por nivel de gravedad:`Critical`, `High`, `Medium`, `Low`, `Info`. En el momento de este escrito, el tenable ha publicado`145,973`complementos que cubren`58,391`CVE IDS y`30,696` [Bugtraq](https://en.wikipedia.org/wiki/Bugtraq)Ids. Una base de datos de búsqueda de todos los complementos publicados está en el[Sitio web tenable](https://www.tenable.com/plugins).

El`Plugins`TAB proporciona más información sobre una detección particular, incluida la mitigación. Al realizar escaneos recurrentes, puede haber una vulnerabilidad/detección de que, tras el examen más detallado, no se considera un problema. Por ejemplo, Microsoft DirectAccess (una tecnología que proporciona conectividad de red interna a los clientes a través de Internet) permite suites de cifrado inseguras y nulas. El siguiente escaneo realizado con`sslscan`Muestra un ejemplo de suites de cifrado inseguras y nulas:

Configuración avanzada

```
xnoxos@htb[/htb]$ sslscan example.com<SNIP>

Preferred TLSv1.0  128 bits  ECDHE-RSA-AES128-SHA          Curve 25519 DHE 253
Accepted  TLSv1.0  256 bits  ECDHE-RSA-AES256-SHA          Curve 25519 DHE 253
Accepted  TLSv1.0  128 bits  DHE-RSA-AES128-SHA            DHE 2048 bits
Accepted  TLSv1.0  256 bits  DHE-RSA-AES256-SHA            DHE 2048 bits
Accepted  TLSv1.0  128 bits  AES128-SHA
Accepted  TLSv1.0  256 bits  AES256-SHA

<SNIP>

```

Sin embargo, esto es por diseño. SSL/TLS no es

[requerido](https://directaccess.richardhicks.com/2014/09/23/directaccess-ip-https-ssl-and-tls-insecure-cipher-suites/)

En este caso, e implementarlo resultaría en un impacto negativo en el rendimiento. Para excluir este falso positivo de los resultados del escaneo mientras mantiene la detección activa para otros hosts, podemos crear una regla de complemento:

![](https://academy.hackthebox.com/storage/modules/108/nessus/plugin_rules.png)

Bajo el

```
Resources
```

Sección, podemos seleccionar

```
Plugin Rules
```

. En la nueva regla de complemento, ingresamos al host para que se excluyan, junto con el ID de complemento para Microsoft DirectAccess, y especificamos la acción que se realizará como

```
Hide this result
```

:

![](https://academy.hackthebox.com/storage/modules/108/nessus/new-rule.png)

También es posible que deseemos excluir ciertos problemas de nuestros resultados de escaneo, como complementos para problemas que no son directamente explotables (por ejemplo,

[Certificado autofirmado por SSL](https://www.tenable.com/plugins/nessus/57582)

). Podemos hacer esto especificando el ID del complemento y el host (s) para ser excluidos:

![](https://academy.hackthebox.com/storage/modules/108/nessus/plugins2.png)

---

# **Escaneo con credenciales**

Nessus también admite escaneo credencial y proporciona mucha flexibilidad al admitir hashes LM/NTLM, autenticación de Kerberos y autenticación de contraseña.

Las credenciales se pueden configurar para la autenticación basada en host a través de SSH con una contraseña, clave pública, certificado o autenticación basada en Kerberos. También se puede configurar para la autenticación basada en Windows Host con una contraseña, Kerberos, LM Hash o NTLM Hash:

![](https://academy.hackthebox.com/storage/modules/108/nessus/creds.png)

Nessus también admite la autenticación para una variedad de tipos de bases de datos, incluidos Oracle, PostgreSQL, DB2, MySQL, SQL Server, MongoDB y Sybase:

![](https://academy.hackthebox.com/storage/modules/108/nessus/db_creds.png)

**Nota:**Para ejecutar un escaneo credencial en el destino, use las siguientes credenciales:`htb-student_adm`:`HTB_@cademy_student!`para Linux y`administrator`:`Academy_VA_adm1!`para ventanas. Estos escaneos ya se han establecido en el objetivo de Nessus para ahorrarle tiempo.

Además de eso, Nessus puede realizar la autenticación de texto sin formato a servicios como FTP, HTTP, IMAP, IPMI, Telnet y más:

![](https://academy.hackthebox.com/storage/modules/108/nessus/plaintext_auth.png)

Finalmente, podemos verificar la salida de Nessus para confirmar si la autenticación a la aplicación o servicio de destino con las credenciales suministradas fue exitosa:

![](https://academy.hackthebox.com/storage/modules/108/nessus/sqlserv.png)

[Anterior](https://academy.hackthebox.com/module/108/section/1029) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/108/section/1024)