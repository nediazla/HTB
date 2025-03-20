# Desovando conchas interactivas

Al final de la última sección, establecimos una sesión de shell con el objetivo. Inicialmente, nuestro caparazón era limitado (a veces conocido como un caparazón de la cárcel), por lo que usamos Python para generar un shell de Bourne tty para darnos acceso a más comandos y un aviso para trabajar. Es probable que esta sea una situación en la que nos encontramos cada vez más a medida que practicamos en Hack the Box y en el mundo real en compromisos.

Puede haber momentos en que aterricemos en un sistema con un caparazón limitado, y Python no está instalada. En estos casos, es bueno saber que podríamos usar varios métodos diferentes para generar un caparazón interactivo. Examinemos algunos de ellos.

Sepa que cada vez que veamos`/bin/sh`o`/bin/bash`, esto también podría reemplazarse con el binario asociado con el lenguaje de intérprete de shell presente en ese sistema. Con la mayoría de los sistemas de Linux, probablemente nos encontraremos`bourne shell` (`/bin/sh`) y`bourne again shell` (`/bin/bash`) presente en el sistema de forma nativa.

---

# **/bin/sh -i**

Este comando ejecutará el intérprete de shell especificado en la ruta en modo interactivo (`-i`).

### **Interactivo**

Desovando conchas interactivas

```
/bin/sh -i
sh: no job control in this shell
sh-4.2$

```

---

# **Perl**

Si el lenguaje de programación[Perl](https://www.perl.org/)está presente en el sistema, estos comandos ejecutarán el intérprete de shell especificado.

### **Perl a la cáscara**

Desovando conchas interactivas

```
perl —e 'exec "/bin/sh";'

```

Desovando conchas interactivas

```
perl: exec "/bin/sh";

```

El comando directamente anterior debe ejecutarse desde un script.

---

# **Rubí**

Si el lenguaje de programación[Rubí](https://www.ruby-lang.org/en/)está presente en el sistema, este comando ejecutará el intérprete de shell especificado:

### **Ruby a la concha**

Desovando conchas interactivas

```
ruby: exec "/bin/sh"

```

El comando directamente anterior debe ejecutarse desde un script.

---

# **Lua**

Si el lenguaje de programación[Lua](https://www.lua.org/)está presente en el sistema, podemos usar el`os.execute`Método para ejecutar el intérprete de shell especificado utilizando el comando completo a continuación:

### **Lua a shell**

Desovando conchas interactivas

```
lua: os.execute('/bin/sh')

```

El comando directamente anterior debe ejecutarse desde un script.

---

# **Asombrar**

[Asombrar](https://man7.org/linux/man-pages/man1/awk.1p.html)Es un lenguaje de escaneo y procesamiento de patrones tipo C presente en la mayoría de los sistemas basados en UNIX/Linux, ampliamente utilizado por desarrolladores y sistemas para generar informes. También se puede usar para generar un caparazón interactivo. Esto se muestra en el breve script AWK a continuación:

### **Awk to shell**

Desovando conchas interactivas

```
awk 'BEGIN {system("/bin/sh")}'

```

---

# **Encontrar**

[Encontrar](https://man7.org/linux/man-pages/man1/find.1.html)es un comando presente en la mayoría de los sistemas UNIX/Linux ampliamente utilizados para buscar y a través de archivos y directorios utilizando diversos criterios. También se puede utilizar para ejecutar aplicaciones e invocar un intérprete de shell.

### **Usando Find para un caparazón**

Desovando conchas interactivas

```
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;

```

Este uso del comando Find está buscando cualquier archivo enumerado después del`-name`opción, luego se ejecuta`awk` (`/bin/awk`) y ejecuta el mismo script que discutimos en la sección AWK para ejecutar un intérprete de shell.

---

# **Uso de EXEC para iniciar un shell**

Desovando conchas interactivas

```
find . -exec /bin/sh \; -quit

```

Este uso del comando Find usa la opción Ejecutar (`-exec`) para iniciar el intérprete de shell directamente. Si`find`No puedo encontrar el archivo especificado, entonces no se alcanzará ningún shell.

---

# **EMPUJE**

Sí, podemos establecer el lenguaje del intérprete de shell dentro del popular editor de texto basado en línea de comandos`VIM`. Esta es una situación muy nicho en la que nos encontraríamos para necesitar usar este método, pero es bueno saberlo por si acaso.

### **Vim a caparazón**

Desovando conchas interactivas

```
vim -c ':!/bin/sh'

```

### **Vim Escape**

Desovando conchas interactivas

```
vim
:set shell=/bin/sh
:shell

```

---

# **Consideraciones de permisos de ejecución**

Además de conocer todas las opciones enumeradas anteriormente, debemos tener en cuenta los permisos que tenemos con la cuenta de la sesión de shell. Siempre podemos intentar ejecutar este comando para enumerar las propiedades y permisos del archivo que nuestra cuenta tiene sobre cualquier archivo o binario determinado:

### **Permisos**

Desovando conchas interactivas

```
ls -la <path/to/fileorbinary>

```

También podemos intentar ejecutar este comando para verificar qué`sudo`Permisos La cuenta en la que aterrizamos tiene:

### **Sudo -l**

Desovando conchas interactivas

```
sudo -l
Matching Defaults entries for apache on ILF-WebSrv:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User apache may run the following commands on ILF-WebSrv:
    (ALL : ALL) NOPASSWD: ALL

```

El comando sudo -l anterior necesitará un shell interactivo estable para ejecutar. Si no está en un caparazón completo o está sentado en un caparazón inestable, es posible que no obtenga ningún retorno de él. No solo considerar los permisos nos permitirá ver qué comandos podemos ejecutar, sino que también puede comenzar a darnos una idea de vectores potenciales que nos permitirán aumentar los privilegios.