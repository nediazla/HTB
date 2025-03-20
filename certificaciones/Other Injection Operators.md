# Other Injection Operators

Antes de seguir adelante, intentemos algunos otros operadores de inyección y veamos cuán diferente la aplicación web los manejaría.

---

# **Y operador**

Podemos comenzar con el`AND` (`&&`) operador, de modo que nuestra carga útil final sería (`127.0.0.1 && whoami`), y el comando ejecutado final sería lo siguiente:

Código:intento

```bash
ping -c 1 127.0.0.1 && whoami

```

Como siempre deberíamos, intentemos ejecutar el comando en nuestra VM Linux primero para asegurarnos de que sea un comando de funcionamiento:

Otros operadores de inyección

```
21y4d@htb[/htb]$ ping -c 1 127.0.0.1 && whoamiPING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=1.03 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 1.034/1.034/1.034/0.000 ms
21y4d

```

Como podemos ver, el comando se ejecuta, y obtenemos el mismo resultado que obtuvimos anteriormente. Intente consultar la tabla de operadores de inyección desde la sección anterior y vea cómo el`&&`El operador es diferente (si no escribimos una IP y comenzamos directamente con`&&`, ¿seguiría funcionando el comando?).

Ahora, podemos hacer lo mismo que hicimos antes copiando nuestra carga útil, pegándola en nuestra solicitud HTTP en

```
Burp Suite
```

, URL lo codifica y finalmente lo envía:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_AND.jpg)

Como podemos ver, inyectamos con éxito nuestro comando y recibimos el resultado esperado de ambos comandos.

---

# **U operador**

Finalmente, probemos el`OR` (`||`) Operador de inyección. El`OR`El operador solo ejecuta el segundo comando si el primer comando no se ejecuta. Esto puede ser útil para nosotros en los casos en que nuestra inyección rompería el comando original sin tener una forma sólida de que ambos comandos funcionen. Entonces, usando el`OR`El operador haría que nuestro nuevo comando se ejecute si el primero falla.

Si intentamos usar nuestra carga útil habitual con el`||`operador (`127.0.0.1 || whoami`), veremos que solo se ejecutaría el primer comando:

Otros operadores de inyección

```
21y4d@htb[/htb]$ ping -c 1 127.0.0.1 || whoamiPING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.635 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.635/0.635/0.635/0.000 ms

```

Esto se debe a cómo`bash`Los comandos funcionan. Como el primer comando devuelve el código de salida`0`indicando una ejecución exitosa, el`bash`El comando se detiene y no prueba el otro comando. Solo intentaría ejecutar el otro comando si el primer comando falló y devolviera un código de salida`1`.

`Try using the above payload in the HTTP request, and see how the web application handles it.`

Intentemos romper intencionalmente el primer comando al no suministrar una IP y usar directamente el`||`operador (`|| whoami`), de modo que el`ping`El comando fallaría y nuestro comando inyectado se ejecuta:

Otros operadores de inyección

```
21y4d@htb[/htb]$ ping -c 1 || whoamiping: usage error: Destination address required
21y4d

```

Como podemos ver, esta vez, el

```
whoami
```

el comando se ejecutaron después del

```
ping
```

El comando falló y nos dio un mensaje de error. Entonces, intentemos ahora el (

```
|| whoami
```

) carga útil en nuestra solicitud HTTP:

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_OR.jpg)

Vemos que esta vez solo obtuvimos la salida del segundo comando como se esperaba. Con esto, estamos utilizando una carga útil mucho más simple y obteniendo un resultado mucho más limpio.

Dichos operadores se pueden usar para varios tipos de inyección, como inyecciones SQL, inyecciones LDAP, XSS, SSRF, XML, etc. Hemos creado una lista de los operadores más comunes que se pueden usar para inyecciones:

| **Tipo de inyección** | **Operadores** |
| --- | --- |
| Inyección SQL | `'` `,` `;` `--` `/* */` |
| Inyección de comando | `;` `&&` |
| Inyección de LDAP | `*` `(` `)` `&` `|` |
| Inyección de xpath | `'` `or` `and` `not` `substring` `concat` `count` |
| Inyección de comando de OS | `;` `&` `|` |
| Inyección de código | `'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^` |
| Directorio Traversal/File Rath Traversal | `../` `..\\` `%00` |
| Inyección de objetos | `;` `&` `|` |
| Inyección de xquería | `'` `;` `--` `/* */` |
| Inyección de código de carcasa | `\x` `\u` `%u` `%n` |
| Inyección de encabezado | `\n` `\r\n` `\t` `%0d` `%0a` `%09` |

Tenga en cuenta que esta tabla está incompleta, y muchas otras opciones y operadores son posibles. También depende en gran medida del entorno con el que estamos trabajando y probando.

En este módulo, estamos tratando principalmente de inyecciones de comando directas, en las que nuestra entrada va directamente al comando del sistema, y estamos recibiendo la salida del comando. Para obtener más información sobre inyecciones de comando avanzadas, como inyecciones indirectas o inyección ciega, puede consultar el[Whitebox pentesting 101: inyección de comando](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection)Módulo, que cubre métodos de inyección avanzada y muchos otros temas.