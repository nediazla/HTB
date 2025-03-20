# Latest SQL Vulnerabilities

Esta vez discutamos una vulnerabilidad que no tiene una CVE y no requiere una exploit directa. La sección anterior muestra que podemos obtener el`NTLMv2`Hashes interactuando con el servidor MSSQL. Sin embargo, debemos mencionar nuevamente que este ataque es posible a través de una conexión directa con el servidor MSSQL y las aplicaciones web vulnerables. Sin embargo, solo nos centraremos en la variante más simple por el momento, a saber, la interacción directa.

---

# **El concepto del ataque**

Nos centraremos en la función de servidor MSSQL indocumentada llamada`xp_dirtree`para esta vulnerabilidad. Esta función se utiliza para ver el contenido de una carpeta específica (local o remota). Además, esta función proporciona algunos parámetros adicionales que se pueden especificar. Estos incluyen la profundidad, hasta qué punto la función debe llegar en la carpeta y la carpeta de destino real.

### **El concepto de ataques**

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Lo interesante es que la función MSSQL`xp_dirtree`no es directamente una vulnerabilidad, pero aprovecha el mecanismo de autenticación de SMB. Cuando intentamos acceder a una carpeta compartida en la red con un host de Windows, este host de Windows envía automáticamente un`NTLMv2`hash para la autenticación.

Este hash se puede usar de varias maneras contra el servidor MSSQL y otros hosts en la red corporativa. Esto incluye un ataque de retransmisión de SMB donde "reproducimos" el hash para iniciar sesión en otros sistemas donde la cuenta tiene privilegios de administración locales o`cracking`Este hash en nuestro sistema local. El agrietamiento exitoso nos permitiría ver y usar la contraseña en ClearText. Un exitoso ataque de retransmisión de SMB nos otorgaría derechos de administrador de los Estados Unidos a otro anfitrión en la red, pero no necesariamente el host donde se originó el hash porque Microsoft parchó un defecto más antiguo que permitió un retransmisión de SMB al anfitrión de origen. Sin embargo, posiblemente podríamos obtener administrador local a otro host y luego robar credenciales que podrían reutilizarse para obtener acceso de administrador local al sistema original donde se originó el hash NTLMV2.

### **Iniciación del ataque**

| **Paso** | **Xp_dirtree** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `1.` | La fuente aquí es la entrada del usuario, que especifica la función y la carpeta compartida en la red. | `Source` |
| `2.` | El proceso debe garantizar que todos los contenidos de la carpeta especificada se muestren al usuario. | `Process` |
| `3.` | La ejecución de los comandos del sistema en el servidor MSSQL requiere privilegios elevados con los que el servicio ejecuta los comandos. | `Privileges` |
| `4.` | El servicio SMB se utiliza como el destino al que se reenvía la información especificada. | `Destination` |

Esto es cuando el ciclo comienza nuevamente, pero esta vez para obtener el hash NTLMV2 del usuario del servicio MSSQL.

### **Robar el hash**

| **Paso** | **Robando el hash** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `5.` | Aquí, el servicio SMB recibe la información sobre el orden especificado a través del proceso anterior del servicio MSSQL. | `Source` |
| `6.` | Luego se procesan los datos y se consulta la carpeta especificada para el contenido. | `Process` |
| `7.` | El hash de autenticación asociado se usa en consecuencia desde que el usuario de MSSQL ejecuta el servicio consulta el servicio. | `Privileges` |
| `8.` | En este caso, el destino para la autenticación y la consulta es el host que controlamos y la carpeta compartida en la red. | `Destination` |

Finalmente, el hash es interceptado por herramientas como`Responder`, `WireShark`, o`TCPDump`y nos mostramos, que podemos intentar usar para nuestros propósitos. Aparte de eso, hay muchas formas diferentes de ejecutar comandos en MSSQL. Por ejemplo, otro método interesante sería ejecutar el código Python en una consulta SQL. Podemos encontrar más sobre esto en el[documentación](https://docs.microsoft.com/en-us/sql/machine-learning/tutorials/quickstart-python-create-script?view=sql-server-ver15)de Microsoft. Sin embargo, esta y otras posibilidades de lo que podemos hacer con MSSQL se discutirá en otro módulo.

[Anterior](https://academy.hackthebox.com/module/116/section/1169)