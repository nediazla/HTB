# Latest SMB Vulnerabilities

Se llamó a una vulnerabilidad significativa reciente que afectó el protocolo de SMB[Smbghost](https://arista.my.site.com/AristaCommunity/s/article/SMBGhost-Wormable-Vulnerability-Analysis-CVE-2020-0796)con el[CVE-2020-0796](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2020-0796). La vulnerabilidad consistió en un mecanismo de compresión de la versión SMB V3.1.1 que hizo que las versiones de Windows 10 1903 y 1909 sean vulnerables al ataque de un atacante no autenticado. La vulnerabilidad permitió al atacante obtener la ejecución del código remoto (`RCE`) y acceso completo al sistema de destino remoto.

No discutiremos la vulnerabilidad en detalle en esta sección, ya que una explicación muy profunda requiere una experiencia de ingeniería inversa y un conocimiento avanzado de CPU, kernel y desarrollo de explotación. En cambio, solo nos centraremos en el concepto de ataque porque incluso con hazañas y vulnerabilidades más complicadas, el concepto sigue siendo el mismo.

---

# **El concepto del ataque**

En términos simples, este es un[desbordamiento entero](https://en.wikipedia.org/wiki/Integer_overflow)Vulnerabilidad en una función de un controlador SMB que permite sobrescribir los comandos del sistema al acceder a la memoria. Un desbordamiento entero resulta de una CPU que intenta generar un número que sea mayor que el valor requerido para el espacio de memoria asignado. Las operaciones aritméticas siempre pueden devolver valores inesperados, lo que resulta en un error. Puede ocurrir un ejemplo de un desbordamiento entero cuando un programador no permite que ocurra un número negativo. En este caso, un desbordamiento entero ocurre cuando una variable realiza una operación que da como resultado un número negativo, y la variable se devuelve como un entero positivo. Esta vulnerabilidad ocurrió porque, en ese momento, la función carecía de verificaciones de límites para manejar el tamaño de los datos enviados en el proceso de negociación de la sesión de SMB.

Para obtener más información sobre las técnicas y vulnerabilidades de desbordamiento de búfer, consulte el[Se desborda el búfer a base de pila en Linux x86](https://academy.hackthebox.com/course/preview/stack-based-buffer-overflows-on-linux-x86), y[Se desborda el búfer basado en pila en Windows x86](https://academy.hackthebox.com/course/preview/stack-based-buffer-overflows-on-windows-x86)módulo. Estos entran en detalles sobre los conceptos básicos de cómo el atacante puede sobrescribir y manejar el búfer.

### **El concepto de ataques**

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

La vulnerabilidad ocurre mientras se procesa un mensaje comprimido malformado después del`Negotiate Protocol Responses`. Si el servidor SMB permite solicitudes (a través de TCP/445), la compresión generalmente se admite, donde el servidor y el cliente establecen los términos de comunicación antes de que el cliente envíe más datos. Suponga que los datos transmitidos exceden los límites de la variable entero debido a la cantidad excesiva de datos. En ese caso, estas partes se escriben en el búfer, lo que conduce a la sobrescribencia de las instrucciones posteriores de la CPU e interrumpe la ejecución normal o planificada del proceso. Estos conjuntos de datos se pueden estructurar para que las instrucciones sobrescribidas se reemplacen por las propias y, por lo tanto, forzamos la CPU (y por lo tanto también el proceso) a realizar otras tareas e instrucciones.

### **Iniciación del ataque**

| **Paso** | **Smbghost** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `1.` | El cliente envía una solicitud manipulada por el atacante al servidor SMB. | `Source` |
| `2.` | Los paquetes comprimidos enviados se procesan de acuerdo con las respuestas de protocolo negociadas. | `Process` |
| `3.` | Este proceso se realiza con los privilegios del sistema o al menos con los privilegios de un administrador. | `Privileges` |
| `4.` | El proceso local se utiliza como destino, que debe procesar estos paquetes comprimidos. | `Destination` |

Esto es cuando el ciclo comienza nuevamente, pero esta vez para obtener acceso remoto al sistema de destino.

### **Activar la ejecución del código remoto**

| **Paso** | **Smbghost** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `5.` | Las fuentes utilizadas en el segundo ciclo son del proceso anterior. | `Source` |
| `6.` | En este proceso, el desbordamiento entero ocurre reemplazando el búfer sobrescribido con las instrucciones del atacante y obligando a la CPU a ejecutar esas instrucciones. | `Process` |
| `7.` | Se utilizan los mismos privilegios del servidor SMB. | `Privileges` |
| `8.` | El sistema de atacantes remotos se utiliza como destino, en este caso, otorga acceso al sistema local. | `Destination` |

Sin embargo, a pesar de la complejidad de la vulnerabilidad debido a la manipulación del amortiguador, que podemos ver en el[POC](https://www.exploit-db.com/exploits/48537), sin embargo, el concepto del ataque se aplica aquí.