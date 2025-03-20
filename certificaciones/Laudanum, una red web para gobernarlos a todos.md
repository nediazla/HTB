# Laudanum, una red web para gobernarlos a todos

Laudanum es un repositorio de archivos listos que se pueden usar para inyectar en una víctima y recibir acceso a la espalda a través de un shell inverso, ejecutar comandos en el host víctima directamente desde el navegador y más. El repositorio incluye archivos inyectables para muchos lenguajes de aplicaciones web diferentes para incluir`asp, aspx, jsp, php,`y más. Este es un elemento básico para tener en cualquier pentest. Si está utilizando su propia VM, Laudanum está integrado en Parrot OS y Kali de forma predeterminada. Para cualquier otra distribución, es probable que necesite retirar una copia para usar. Puedes conseguirlo[aquí](https://github.com/jbarcia/Web-Shells/tree/master/laudanum). Examinemos a Laudanum y veamos cómo funciona.

---

# **Trabajando con Laudanum**

Los archivos laudanum se pueden encontrar en el`/usr/share/laudanum`directorio. Para la mayoría de los archivos dentro de Laudanum, puede copiarlos como es y colocarlos donde los necesita en la víctima para ejecutar. Para archivos específicos como los shells, debe editar el archivo primero para insertar su`attacking`Host Dirección IP para asegurarse de que puede acceder al shell web o recibir una devolución de llamada en la instancia de que usa un shell inverso. Antes de usar los diferentes archivos, asegúrese de leer los contenidos y los comentarios para asegurarse de tomar las acciones adecuadas.

---

# **Demostración de laudanum**

Ahora que entendemos qué es Laudanum y cómo funciona, veamos una aplicación web que hemos encontrado en nuestro entorno de laboratorio y veamos si podemos ejecutar un shell web. Si desea seguir con esta demostración, deberá agregar una entrada a su`/etc/hosts`Archivo en su VM de ataque o dentro de Pwnbox para el host que estamos atacando. Esa entrada debe leer:`<target ip> status.inlanefreight.local`. Una vez hecho esto, puede jugar y explorar esta demostración siempre que esté en la VPN o usando Pwnbox.

### **Mueva una copia para la modificación**

Laudanum, una red web para gobernarlos a todos

```
xnoxos@htb[/htb]$ cp /usr/share/laudanum/aspx/shell.aspx /home/tester/demo.aspx
```

Agregue su dirección IP al`allowedIps`variable en línea`59`. Haga cualquier otro cambio que desee. Puede ser prudente eliminar el arte y los comentarios ASCII del archivo. Estos elementos en una carga útil a menudo se firman y pueden alertar a los defensores/AV a lo que está haciendo.

### **Modificar el shell para su uso**

![](https://academy.hackthebox.com/storage/modules/115/modify-shell.png)

Estamos aprovechando la función de carga en la parte inferior de la página de estado (`Green Arrow`) para que esto funcione. Seleccione su archivo de shell y presione la carga. Si tiene éxito, debe imprimir la ruta hacia donde se guardó el archivo (flecha amarilla). Use la función de carga. El éxito imprime dónde fue el archivo, navegue a él.

### **Aproveche la función de carga**

![](https://academy.hackthebox.com/storage/modules/115/laud-upload.png)

Una vez que la carga sea exitosa, deberá navegar a su shell web para utilizar sus funciones. La imagen a continuación nos muestra cómo hacerlo. Como se ve desde la última imagen, nuestro shell se cargó al`\\files\`directorio, y el nombre se mantuvo igual. Este no siempre será el caso. Puede encontrarse con algunas implementaciones que aleatorizan los nombres de archivo en la carga que no tienen un directorio de archivos públicos o en cualquier otra cantidad de otras salvaguardas potenciales. Por ahora, tenemos suerte de que ese no es el caso. Con esta aplicación web en particular, nuestro archivo fue a`status.inlanefreight.local\\files\demo.aspx`y requerirá que busquemos la carga utilizando eso \ en la ruta en lugar de la / me gusta normal. Una vez que haga esto, su navegador lo limpiará en su ventana de URL para aparecer como`status.inlanefreight.local//files/demo.aspx`.

### **Navegue a nuestro caparazón**

![](https://academy.hackthebox.com/storage/modules/115/laud-nav.png)

Ahora podemos utilizar el shell Laudanum que subimos para emitir comandos para el host. Podemos ver en el ejemplo que el`systeminfo`El comando se ejecutaron.

### **Éxito de la cáscara**

![](https://academy.hackthebox.com/storage/modules/115/laud-success.png)