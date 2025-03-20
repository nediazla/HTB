# Latest RDP Vulnerabilities

En 2019, se publicó una vulnerabilidad crítica en el RDP (`TCP/3389`) Servicio que también condujo a la ejecución del código remoto (`RCE`) con el identificador[CVE-2019-0708](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2019-0708). Esta vulnerabilidad se conoce como`BlueKeep`. No requiere acceso previo al sistema para explotar el servicio para nuestros propósitos. Sin embargo, la explotación de esta vulnerabilidad condujo y aún conduce a muchos ataques de malware o ransomware. Las grandes organizaciones como los hospitales, cuyo software solo está diseñado para versiones y bibliotecas específicas, son particularmente vulnerables a tales ataques, ya que el mantenimiento de la infraestructura es costoso. Aquí, también, no entraremos en detalles minuciosos sobre esta vulnerabilidad, sino que mantendremos el enfoque en el concepto.

---

# **El concepto del ataque**

La vulnerabilidad también se basa, como con SMB, en las solicitudes manipuladas enviadas al servicio objetivo. Sin embargo, lo peligroso aquí es que la vulnerabilidad no requiere que se active la autenticación del usuario. En cambio, la vulnerabilidad ocurre después de inicializar la conexión cuando la configuración básica se intercambia entre el cliente y el servidor. Esto se conoce como un[Uso-después de](https://cwe.mitre.org/data/definitions/416.html) (`UAF`) técnica que utiliza la memoria liberada para ejecutar código arbitrario.

### **El concepto de ataques**

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Este ataque implica muchos pasos diferentes en el núcleo del sistema operativo, que no son de gran importancia aquí por el momento de comprender el concepto detrás de él. Después de que la función se ha explotado y la memoria se ha liberado, los datos se escriben en el núcleo, lo que nos permite sobrescribir la memoria del núcleo. Esta memoria se usa para escribir nuestras instrucciones en la memoria liberada y dejar que la CPU las ejecute. Si queremos ver el análisis técnico de la vulnerabilidad de Bluekeep, esto[artículo](https://unit42.paloaltonetworks.com/exploitation-of-windows-cve-2019-0708-bluekeep-three-ways-to-write-data-into-the-kernel-with-rdp-pdu/)Proporciona una buena descripción general.

### **Iniciación del ataque**

| **Paso** | **Manta** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `1.` | Aquí, la fuente es la solicitud de inicialización del intercambio de configuración entre el servidor y el cliente que el atacante ha manipulado. | `Source` |
| `2.` | La solicitud conduce a una función utilizada para crear un canal virtual que contiene la vulnerabilidad. | `Process` |
| `3.` | Dado que este servicio es adecuado para[administración](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account)del sistema, se ejecuta automáticamente con el[Cuenta de localsystem](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account)privilegios del sistema. | `Privileges` |
| `4.` | La manipulación de la función nos redirige a un proceso de núcleo. | `Destination` |

Esto es cuando el ciclo comienza nuevamente, pero esta vez para obtener acceso remoto al sistema de destino.

### **Activar la ejecución del código remoto**

| **Paso** | **Manta** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `5.` | La fuente esta vez es la carga útil creada por el atacante que se inserta en el proceso para liberar la memoria en el núcleo y colocar nuestras instrucciones. | `Source` |
| `6.` | El proceso en el núcleo se activa para liberar la memoria del núcleo y dejar que la CPU apunte a nuestro código. | `Process` |
| `7.` | Dado que el núcleo también se ejecuta con los más altos privilegios posibles, las instrucciones que ponemos en la memoria liberada del núcleo aquí también se ejecutan con[Cuenta de localsystem](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account)privilegios. | `Privileges` |
| `8.` | Con la ejecución de nuestras instrucciones desde el núcleo, se envía un shell inverso a través de la red a nuestro host. | `Destination` |

No todas las variantes de Windows más nuevas son vulnerables a Bluekeep, según Microsoft. Las actualizaciones de seguridad para las versiones actuales de Windows están disponibles, y Microsoft también ha proporcionado actualizaciones para muchas versiones de Windows anteriores que ya no son compatibles. Sin embargo,`950,000`Los sistemas de Windows se identificaron como vulnerables a`Bluekeep`ataques en un escaneo inicial en mayo de 2019, e incluso hoy, sobre`a quarter`De esos anfitriones aún son vulnerables.

Nota: Este es un defecto con el que probablemente nos encontraremos durante nuestras pruebas de penetración, pero puede causar inestabilidad del sistema, incluida una "pantalla azul de muerte (BSOD)", y debemos tener cuidado antes de usar el exploit asociado. En caso de duda, es mejor hablar primero con nuestro cliente para que entiendan los riesgos y luego decidan si les gustaría que ejecutemos la exploit o no.