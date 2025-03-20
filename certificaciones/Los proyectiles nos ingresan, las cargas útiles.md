# Los proyectiles nos ingresan, las cargas útiles nos entregan conchas

[](https://academy.hackthebox.com/storage/modules/115/nonono.txt)

A`shell`es un programa que proporciona a un usuario de una computadora una interfaz para ingresar instrucciones en el sistema y ver la salida de texto (BASH, ZSH, CMD y PowerShell, por ejemplo). Como probadores de penetración y profesionales de seguridad de la información, un shell es a menudo el resultado de explotar una vulnerabilidad o pasar por alto las medidas de seguridad para obtener acceso interactivo a un host. Es posible que hayamos escuchado o leído las siguientes frases utilizadas por personas que discuten un compromiso o una sesión de práctica reciente:

- `"I caught a shell."`
- `"I popped a shell!"`
- `"I dropped into a shell!"`
- `"I'm in!"`

Por lo general, estas frases se traducen en la comprensión de que esta persona ha explotado con éxito una vulnerabilidad en un sistema y ha podido obtener el control remoto del shell en el sistema operativo de la computadora de destino. Este es un objetivo común que tendrá un probador de penetración al intentar acceder a una máquina vulnerable. Notaremos que la mayor parte de este módulo se centrará en lo que viene después de la enumeración e identificación de hazañas prometedoras.

---

# **¿Por qué conseguir un caparazón?**

Recuerde que el shell nos da acceso directo al`OS`, `system commands`, y`file system`. Entonces, si obtenemos acceso, podemos comenzar a enumerar el sistema para vectores que pueden permitirnos aumentar los privilegios, pivotar, transferir archivos y más. Si no establecemos una sesión de shell, estamos bastante limitados sobre cuán lejos podemos llegar en una máquina de destino.

Establecer un caparazón también nos permite mantener la persistencia en el sistema, dándonos más tiempo para trabajar. Puede hacer que sea más fácil usar nuestro`attack tools`, `exfiltrate data`, `gather`, `store`y`document`Todos los detalles de nuestro ataque, como pronto veremos en las manifestaciones procedentes. Es importante tener en cuenta que establecer una concha casi siempre significa que estamos accediendo a la CLI del sistema operativo, y esto puede hacernos más difíciles de notar que si estuviéramos accediendo remotamente a un shell gráfico sobre[VNC](https://en.wikipedia.org/wiki/Virtual_Network_Computing)o[RDP](https://www.cloudflare.com/learning/access-management/what-is-the-remote-desktop-protocol/). Otro beneficio significativo de ser experto en interfaces de línea de comandos es que pueden ser`harder to detect than graphical shells`, `faster to navigate the OS`, y`easier to automate our actions`. Vemos conchas a través de la lente de las siguientes perspectivas a lo largo de este módulo:

| **Perspectiva** | **Descripción** |
| --- | --- |
| `Computing` | El entorno de usuario basado en texto que se utiliza para administrar tareas y enviar instrucciones en una PC. Piense en Bash, ZSH, CMD y PowerShell. |
| `Exploitation` `&` `Security` | Un shell es a menudo el resultado de explotar una vulnerabilidad o evitar medidas de seguridad para obtener acceso interactivo a un host. Un ejemplo sería activador[Eterna](https://www.cisecurity.org/wp-content/uploads/2019/01/Security-Primer-EternalBlue.pdf)En un host de Windows para obtener acceso al CMD-Prompt en un host de forma remota. |
| `Web` | Esto es un poco diferente. Un shell web se parece mucho a un shell estándar, excepto que explota una vulnerabilidad (a menudo la capacidad de cargar un archivo o script) que proporciona al atacante una forma de emitir instrucciones, leer y acceder a archivos, y potencialmente realizar acciones destructivas para el host subyacente. El control del shell web a menudo se realiza llamando al script dentro de una ventana del navegador. |

---

# **Las cargas útiles nos entregan conchas**

Dentro de la industria de TI en su conjunto, un`payload`se puede definir de diferentes maneras:

- `Networking`: La parte de datos encapsulados de un paquete que atraviesa redes de computadoras modernas.
- `Basic Computing`: Una carga útil es la parte de un conjunto de instrucciones que define la acción a tomar. Encabezados y información de protocolo eliminada.
- `Programming`: La parte de datos referenciada o llevada por la instrucción del lenguaje de programación.
- `Exploitation & Security`: Una carga útil es`code`Hecho a mano con la intención de explotar una vulnerabilidad en un sistema informático. El término carga útil puede describir varios tipos de malware, incluidos, entre otros, ransomware.

En este módulo, trabajaremos con muchos tipos diferentes de`payloads`y métodos de entrega dentro del contexto de otorgándonos acceso a un host y establecer`remote shell`Sesiones con sistemas vulnerables.