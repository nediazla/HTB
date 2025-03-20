# Anatomía de un caparazón

Cada sistema operativo tiene un shell e interactuar con él, debemos usar una aplicación conocida como un`terminal emulator`. Estos son algunos de los emuladores terminales más comunes:

| **Emulador terminal** | **Sistema operativo** |
| --- | --- |
| [Terminal de Windows](https://github.com/microsoft/terminal) | Windows |
| [cmder](https://cmder.app/) | Windows |
| [Masilla](https://www.putty.org/) | Windows |
| [bote](https://sw.kovidgoyal.net/kitty/) | Windows, Linux y MacOS |
| [Alacrito](https://github.com/alacritty/alacritty) | Windows, Linux y MacOS |
| [xterm](https://invisible-island.net/xterm/) | Linux |
| [Terminal de gnomo](https://en.wikipedia.org/wiki/GNOME_Terminal) | Linux |
| [Terminal de pareja](https://github.com/mate-desktop/mate-terminal) | Linux |
| [Konsole](https://konsole.kde.org/) | Linux |
| [Terminal](https://en.wikipedia.org/wiki/Terminal_(macOS)) | Macosa |
| [iterm2](https://iterm2.com/) | Macosa |

Esta lista de ninguna manera está disponible en cada emulador terminal, pero incluye algunos notables. Además, debido a que muchas de estas herramientas son de código abierto, podemos instalarlas en diferentes sistemas operativos de manera que pueda diferir de las intenciones originales de los desarrolladores. Sin embargo, ese es un proyecto más allá del alcance de este módulo. Seleccionar el emulador de terminal adecuado para el trabajo es principalmente una preferencia personal y estilística basada en nuestros flujos de trabajo que se desarrollan a medida que nos familiarizamos con nuestro sistema operativo de elección. Así que no dejes que nadie te haga sentir mal por seleccionar una opción sobre la otra. El emulador terminal con el que interactúa en los objetivos dependerá esencialmente de lo que existe en el sistema de forma nativa.

---

# **Interpretadores del lenguaje de comandos**

Al igual que un intérprete de lenguaje humano traducirá el lenguaje hablado o de señas en tiempo real, un`command language interpreter`es un programa que trabaja para interpretar las instrucciones proporcionadas por el usuario y emitir las tareas al sistema operativo para su procesamiento. Entonces, cuando discutimos las interfaces de línea de comandos, sabemos que es una combinación del sistema operativo, la aplicación del emulador terminal y el intérprete del lenguaje de comando. Se pueden usar muchos intérpretes de lenguaje de comandos diferentes, algunos de los cuales también se llaman`shell scripting languages`o`Command and Scripting interpreters`como se define en el[Técnicas de ejecución](https://attack.mitre.org/techniques/T1059/)del`MITRE ATT&CK Matrix`. No necesitamos ser desarrolladores de software para comprender estos conceptos, pero cuanto más sabemos, más éxito podemos tener al intentar explotar sistemas vulnerables para obtener una sesión de shell.

Comprender el intérprete del lenguaje de comando en uso en cualquier sistema dado también nos dará una idea de qué comandos y scripts debemos usar. Vamos a ser prácticos con algunos de estos conceptos.

---

# **Práctico con emuladores y conchas terminales**

Usemos nuestro`Parrot OS`Pwnbox para explorar más a fondo la anatomía de un caparazón. Haga clic en el`green`icono cuadrado en la parte superior de la pantalla para abrir el`MATE`emulador terminal y luego escriba algo aleatorio y presione Enter.

### **Ejemplo terminal**

![](https://academy.hackthebox.com/storage/modules/115/green-square.png)

Tan pronto como seleccionamos el icono, abrió la aplicación Mate Terminal Emulator, que se ha configurado previamente para usar un intérprete de lenguaje de comando. En este caso, estamos "al tanto de qué intérprete de lenguaje está en uso al ver el`$`firmar. Este signo $ se usa en Bash, KSH, Posix y muchos otros idiomas de Shell para marcar el inicio del`shell prompt`donde el usuario puede comenzar a escribir comandos y otras entradas. Cuando escribimos nuestro texto aleatorio y presionamos Enter, se identificó nuestro intérprete de lenguaje de comando. Eso es bash diciéndonos que no reconoció ese comando que escribimos. Entonces, aquí podemos ver que los intérpretes de lenguaje de comandos pueden tener su propio conjunto de comandos que reconocen. Otra forma en que podemos identificar el intérprete del lenguaje es ver los procesos que se ejecutan en la máquina. En Linux, podemos hacer esto usando el siguiente comando:

### **Validación de shell de 'PS'**

Anatomía de un caparazón

```
xnoxos@htb[/htb]$ ps    PID TTY          TIME CMD
   4232 pts/1    00:00:00 bash
  11435 pts/1    00:00:00 ps

```

También podemos averiguar qué lenguaje de shell está en uso al ver las variables de entorno utilizando el`env`dominio:

### **Validación de shell usando 'env'**

Anatomía de un caparazón

```
xnoxos@htb[/htb]$ envSHELL=/bin/bash

```

Ahora seleccionemos el icono de Blue Square en la parte superior de la pantalla en Pwnbox.

### **PowerShell vs. Bash**

![](https://academy.hackthebox.com/storage/modules/115/blue-box.png)

Seleccionar este icono también abre la aplicación de terminal Mate, pero usa un intérprete de lenguaje de comando diferente esta vez. Comógelos mientras se colocan uno al lado del otro.

- `What differences can we identify?`
- `Why would we use one over the other on the same system?`

Hay innumerables diferencias y personalizaciones que podríamos descubrir. Intente usar algunos comandos que conoce en ambos y tome una nota mental de las diferencias en la producción y qué comandos se reconocen. Uno de los puntos principales que podemos quitar de esto es que un emulador terminal no está vinculado a un idioma específico. En realidad, el lenguaje de shell se puede cambiar y personalizar para adaptarse al sistema de sysadmin, el desarrollador o las preferencias personales, el flujo de trabajo y las necesidades técnicas de Pentester.