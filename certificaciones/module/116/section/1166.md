# Latest FTP Vulnerabilities

Al discutir las últimas vulnerabilidades, enfocaremos esta sección y las siguientes en uno de los ataques mostrados anteriormente y lo presentarán lo más simple posible sin entrar en demasiados detalles técnicos. Esto debería ayudarnos a facilitar el concepto del ataque a través de un ejemplo relacionado con un servicio específico para obtener una mejor comprensión.

En este caso, discutiremos el`CoreFTP before build 727`vulnerabilidad asignada[CVE-2022-22836](https://nvd.nist.gov/vuln/detail/CVE-2022-22836). Esta vulnerabilidad es para un servicio FTP que no procesa correctamente el`HTTP PUT`solicitar y conducir a un`authenticated directory`/`path traversal,`y`arbitrary file write`vulnerabilidad. Esta vulnerabilidad nos permite escribir archivos fuera del directorio al que el servicio tiene acceso.

---

# **El concepto del ataque**

Este servicio FTP utiliza un HTTP`POST`Solicitud para cargar archivos. Sin embargo, el servicio CoreFTP permite un HTTP`PUT`Solicitud, que podemos usar para escribir contenido en archivos. Echemos un vistazo al ataque basado en nuestro concepto. El[explotar](https://www.exploit-db.com/exploits/50652)porque este ataque es relativamente sencillo, basado en un solo`cURL`dominio.

### **Explotación de CoreftP**

Últimas vulnerabilidades de FTP

```
xnoxos@htb[/htb]$ curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "PoC." --path-as-is https://<IP>/../../../../../../whoops

```

Creamos un HTTP en bruto`PUT`pedido (`-X PUT`) con autenticación básica (`--basic -u <username>:<password>`), la ruta para el archivo (`--path-as-is https://<IP>/../../../../../whoops`), y su contenido (`--data-binary "PoC."`) con este comando. Además, especificamos el encabezado del host (`-H "Host: <IP>"`) con la dirección IP de nuestro sistema de destino.

### **El concepto de ataques**

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

En resumen, el proceso real intensifica la entrada del usuario de la ruta. Esto lleva al acceso a la carpeta restringida que se está omitiendo. Como resultado, los permisos de escritura en el HTTP`PUT`La solicitud no se controla adecuadamente, lo que nos lleva a poder crear los archivos que queremos fuera de las carpetas autorizadas. Sin embargo, omitiremos la explicación del`Basic Auth`Procese y salte directamente a la primera parte de la exploit.

### **Recorrido por el directorio**

| **Paso** | **Recorrido por el directorio** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `1.` | El usuario especifica el tipo de solicitud HTTP con el contenido del archivo, incluido el escape de los caracteres para salir del área restringida. | `Source` |
| `2.` | El proceso cambió y procesa el tipo de solicitud HTTP, contenido de archivo y ruta ingresada por el usuario. | `Process` |
| `3.` | La aplicación verifica si el usuario está autorizado para estar en la ruta especificada. Dado que las restricciones solo se aplican a una carpeta específica, todos los permisos otorgados se omiten a medida que se extiende de esa carpeta utilizando el Traversal del Directorio. | `Privileges` |
| `4.` | El destino es otro proceso que tiene la tarea de escribir el contenido especificado del usuario en el sistema local. | `Destination` |

Hasta este punto, hemos pasado por alto las restricciones impuestas por la aplicación utilizando los caracteres de escape (`../../../../`) y llegar a la segunda parte, donde el proceso escribe el contenido que especificamos a un archivo de nuestra elección. Esto es cuando el ciclo comienza nuevamente, pero esta vez para escribir contenido en el sistema de destino.

### **Archivo arbitrario Escribir**

| **Paso** | **Archivo arbitrario Escribir** | **Concepto de ataques - categoría** |
| --- | --- | --- |
| `5.` | La misma información que ingresó el usuario se utiliza como fuente. En este caso, el nombre de archivo (`whoops`) y el contenido (`--data-binary "PoC."`). | `Source` |
| `6.` | El proceso toma la información especificada y procede a escribir el contenido deseado en el archivo especificado. | `Process` |
| `7.` | Dado que todas las restricciones se omitieron durante la vulnerabilidad transversal del directorio, el servicio aprueba escribir el contenido del archivo especificado. | `Privileges` |
| `8.` | El nombre de archivo especificado por el usuario (`whoops`) con el contenido deseado (`"PoC."`) ahora sirve como destino en el sistema local. | `Destination` |

Una vez que se haya completado la tarea, podremos encontrar este archivo con el contenido correspondiente en el sistema de destino.

### **Sistema objetivo**

Últimas vulnerabilidades de FTP

```
C:\> type C:\whoops

PoC.

```

[Anterior](https://academy.hackthebox.com/module/116/section/1165)