# Contraseñas de reutilización / contraseñas predeterminadas

Es común que tanto los usuarios como los administradores dejen los valores predeterminados en su lugar. Los administradores deben realizar un seguimiento de toda la tecnología, la infraestructura y las aplicaciones junto con los datos que se accede. En este caso,`the same password`a menudo se usa para fines de configuración, y luego la contraseña se olvida de cambiarse para una interfaz u otra. Además, muchas aplicaciones que funcionan con mecanismos de autenticación, básicamente casi todos, a menudo vienen con`default credentials`Después de la instalación. Se pueden olvidar que estas credenciales predeterminadas se cambiarán después de la configuración, especialmente cuando se trata de aplicaciones internas donde los administradores asumen que nadie más los encontrará y ni siquiera intenta usarlas.

Además, las contraseñas fáciles de recordar que se pueden escribir rápidamente en lugar de escribir contraseñas de 15 caracteres a menudo se usan repetidamente porque[Firme](https://en.wikipedia.org/wiki/Single_sign-on) (`SSO`) no siempre está disponible de inmediato durante la instalación inicial, y la configuración en redes internas requiere cambios significativos. Al configurar redes, a veces trabajamos con grandes infraestructuras (dependiendo del tamaño de la compañía) que pueden tener muchos cientos de interfaces. A menudo se pasa por alto un dispositivo de red, como un enrutador, impresora o un firewall, y el`default credentials`se usan o lo mismo`password is reused`.

---

# **Relleno de credenciales**

Hay varias bases de datos que mantienen una lista de ejecución de credenciales predeterminadas conocidas. Uno de ellos es el[Default cre-cheat-sheet](https://github.com/ihebski/DefaultCreds-cheat-sheet). Aquí hay un pequeño extracto de toda la mesa de esta hoja de trucos:

| **Producto/proveedor** | **Nombre de usuario** | **Contraseña** |
| --- | --- | --- |
| Zyxel (SSH) | zyfwp | Prow! An_fxp |
| APC UPS (web) | APC | APC |
| WebLogic (web) | sistema | gerente |
| WebLogic (web) | sistema | gerente |
| WebLogic (web) | weblogico | weblogic1 |
| WebLogic (web) | Weblogico | Weblogico |
| WebLogic (web) | PÚBLICO | PÚBLICO |
| WebLogic (web) | Ejemplos | Ejemplos |
| WebLogic (web) | weblogico | weblogico |
| WebLogic (web) | sistema | contraseña |
| WebLogic (web) | weblogico | Bienvenido (1) |
| WebLogic (web) | sistema | Bienvenido (1) |
| WebLogic (web) | operador | weblogico |
| WebLogic (web) | operador | contraseña |
| WebLogic (web) | sistema | PASSW0RD |
| WebLogic (web) | monitor | contraseña |
| Kanboard (web) | administración | administración |
| Vectr (web) | administración | 11_THISISTTHEFIRSTPASSWORD_11 |
| Caldera (web) | administración | administración |
| Dlink (web) | administración | administración |
| Dlink (web) | 1234 | 1234 |
| Dlink (web) | raíz | 12345 |
| Dlink (web) | raíz | raíz |
| Jiofibra | administración | jiocentrón |
| Gigafiber | administración | jiocentrón |
| Kali Linux (OS) | kali | kali |
| F5 | administración | administración |
| F5 | raíz | por defecto |
| F5 | apoyo |  |
| ... | ... | ... |

Las credenciales predeterminadas también se pueden encontrar en la documentación del producto, ya que contienen los pasos necesarios para configurar el servicio correctamente. Algunos dispositivos/aplicaciones requieren que el usuario configure una contraseña en la instalación, pero otros usan una contraseña predeterminada y débil. Se llama a atacar esos servicios con las credenciales predeterminadas u obtenidas[Relleno de credenciales](https://owasp.org/www-community/attacks/Credential_stuffing). Esta es una variante simplificada de forzamiento bruto porque solo se utilizan los nombres de usuario compuestos y las contraseñas asociadas.

Podemos imaginar que hemos encontrado algunas aplicaciones utilizadas en la red por nuestros clientes. Después de buscar en Internet las credenciales predeterminadas, podemos crear una nueva lista que separe estas credenciales compuestas con un colon (`username:password`). Además, podemos seleccionar las contraseñas y mutarlas por nuestro`rules`para aumentar la probabilidad de golpes.

### **Relleno de credenciales - Sintaxis de Hydra**

Contraseñas de reutilización / contraseñas predeterminadas

```
xnoxos@htb[/htb]$ hydra -C <user_pass.list> <protocol>://<IP>

```

### **Relleno de credenciales - Hydra**

Contraseñas de reutilización / contraseñas predeterminadas

```
xnoxos@htb[/htb]$ hydra -C user_pass.list ssh://10.129.42.197...

```

Aquí, Osint juega otro papel importante. Debido a que OSINT nos da una "sensación" de cómo se estructuran la empresa y su infraestructura, comprenderemos qué contraseñas y nombres de usuario podemos combinar. Luego podemos almacenarlos en nuestras listas y usarlas después. Además, podemos usar Google para ver si las aplicaciones que encontramos tienen credenciales codificadas que se pueden usar.

### **Búsqueda de Google: credenciales predeterminadas**

![](https://academy.hackthebox.com/storage/modules/147/Google-default-creds.png)

Además de las credenciales predeterminadas para las aplicaciones, algunas listas las ofrecen para enrutadores. Una de estas listas se puede encontrar[aquí](https://www.softwaretestinghelp.com/default-router-username-and-password-list/). Es mucho menos probable que las credenciales predeterminadas para los enrutadores se dejen sin cambios. Dado que estas son las interfaces centrales para las redes, los administradores generalmente prestan mucha más atención al endurecerlas. Sin embargo, todavía es posible que un enrutador se pase por alto o que actualmente solo se use en la red interna para fines de prueba, que luego podemos explotar para más ataques.

| **Marca de enrutador** | **Dirección IP predeterminada** | **Nombre de usuario predeterminado** | **Contraseña predeterminada** |
| --- | --- | --- | --- |
| 3com | http://192.168.1.1 | administración | Administración |
| Belkin | http://192.168.2.1 | administración | administración |
| Benq | http://192.168.1.1 | administración | Administración |
| D-Link | http://192.168.0.1 | administración | Administración |
| Digicom | http://192.168.1.254 | administración | Miguel Ángel |
| Linksys | http://192.168.1.1 | administración | Administración |
| Netgear | http://192.168.0.1 | administración | contraseña |
| ... | ... | ... | ... |