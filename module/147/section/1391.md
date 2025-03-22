# Mutaciones de contraseña

Muchas personas crean sus contraseñas según`simplicity instead of security`. Para eliminar esta debilidad humana que a menudo compromete las medidas de seguridad, las políticas de contraseña se pueden crear en todos los sistemas que determinan cómo debe verse una contraseña. Esto significa que el sistema reconoce si la contraseña contiene letras mayúsculas, caracteres especiales y números. Además, la mayoría de las políticas de contraseña requieren una longitud mínima de ocho caracteres en una contraseña, incluida al menos una de las especificaciones anteriores.

En las secciones anteriores, adivinamos contraseñas muy simples, pero se vuelve mucho más difícil adaptar esto a los sistemas que aplican políticas de contraseña que obligan a la creación de contraseñas más complejas.

Desafortunadamente, la tendencia a los usuarios a crear contraseñas débiles también se produce a pesar de la existencia de políticas de contraseña. La mayoría de las personas/empleados siguen las mismas reglas al crear contraseñas más complejas. Las contraseñas a menudo se crean estrechamente relacionadas con el servicio utilizado. Esto significa que muchos empleados a menudo seleccionan contraseñas que pueden tener el nombre de la empresa en las contraseñas. Las preferencias e intereses de una persona también juegan un papel importante. Estos pueden ser mascotas, amigos, deportes, pasatiempos y muchos otros elementos de la vida.`OSINT`La recopilación de información puede ser muy útil para obtener más información sobre las preferencias de un usuario y puede ayudar con la adivinación de contraseña. Se puede encontrar más información sobre OSINT en el[OSINT: módulo de reconocimiento corporativo](https://academy.hackthebox.com/course/preview/osint-corporate-recon). Comúnmente, los usuarios utilizan las siguientes adiciones para que su contraseña se ajuste a las políticas de contraseña más comunes:

| **Descripción** | **Sintaxis de contraseña** |
| --- | --- |
| La primera letra es superior. | `Password` |
| Agregando números. | `Password123` |
| Agregar año. | `Password2022` |
| Agregando mes. | `Password02` |
| El último personaje es un signo de exclamación. | `Password2022!` |
| Agregar caracteres especiales. | `P@ssw0rd2022!` |

Teniendo en cuenta que muchas personas quieren mantener sus contraseñas lo más simples posible a pesar de las políticas de contraseña, podemos crear reglas para generar contraseñas débiles. Basado en estadísticas proporcionadas por[WPEGINE](https://wpengine.com/resources/passwords-unmasked-infographic/), la mayoría de las longitudes de contraseña son`not longer`que`ten`personajes. Entonces, lo que podemos hacer es elegir términos específicos que sean al menos`five`Los personajes largos y parecen ser los más familiarizados para los usuarios, como los nombres de sus mascotas, pasatiempos, preferencias y otros intereses. Si el usuario elige una sola palabra (como el mes actual), agrega el`current year`, seguido de un personaje especial, al final de su contraseña, llegaríamos a la`ten-character`Requisito de contraseña. Teniendo en cuenta que la mayoría de las empresas requieren cambios de contraseña regulares, un usuario puede modificar su contraseña simplemente cambiando el nombre de un mes o un solo número, etc. Usemos un ejemplo simple para crear una lista de contraseñas con solo una entrada.

### **Lista de contraseñas**

Mutaciones de contraseña

```
xnoxos@htb[/htb]$ cat password.listpassword

```

Podemos usar una herramienta muy poderosa llamada[Hashcat](https://hashcat.net/hashcat/)Para combinar listas de nombres y etiquetas potenciales con reglas de mutación específicas para crear listas de palabras personalizadas. Para familiarizarse más con Hashcat y descubrir todo el potencial de esta herramienta, recomendamos el módulo[Romper las contraseñas con hashcat](https://academy.hackthebox.com/course/preview/cracking-passwords-with-hashcat). Hashcat utiliza una sintaxis específica para definir caracteres y palabras y cómo se pueden modificar. La lista completa de esta sintaxis se puede encontrar en el oficial[documentación](https://hashcat.net/wiki/doku.php?id=rule_based_attack)de hashcat. Sin embargo, los que se enumeran a continuación son suficientes para que comprendamos cómo el hashcat muta las palabras.

| **Función** | **Descripción** |
| --- | --- |
| `:` | No hacer nada. |
| `l` | En minúsculas todas las letras. |
| `u` | Alcebre de todas las letras. |
| `c` | Capitalice la primera letra y en minúsculas. |
| `sXY` | Reemplace todas las instancias de X con Y. |
| `$!` | Agregue el carácter de exclamación al final. |

Cada regla está escrita en una nueva línea que determina cómo se debe mutar la palabra. Si escribimos las funciones que se muestran arriba en un archivo y consideramos los aspectos mencionados, este archivo puede verse así:

### **Archivo de regla hashcat**

Mutaciones de contraseña

```
xnoxos@htb[/htb]$ cat custom.rule:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!$! c$! so0$! sa@$! c so0$! c sa@$! so0 sa@$! c so0 sa@
```

Hashcat aplicará las reglas de`custom.rule`Para cada palabra en`password.list`y almacene la versión mutada en nuestro`mut_password.list`respectivamente. Por lo tanto, una palabra dará como resultado quince palabras mutadas en este caso.

### **Generación de la lista de palabras basada en reglas**

Mutaciones de contraseña

```
xnoxos@htb[/htb]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.listxnoxos@htb[/htb]$ cat mut_password.listpassword
Password
passw0rd
Passw0rd
p@ssword
P@ssword
P@ssw0rd
password!
Password!
passw0rd!
p@ssword!
Passw0rd!
P@ssword!
p@ssw0rd!
P@ssw0rd!

```

`Hashcat`y`John`Venga con listas de reglas preconstruidas que podemos usar para nuestros propósitos de generación y agrietamiento. Una de las reglas más utilizadas es`best64.rule`, lo que a menudo puede conducir a buenos resultados. Es importante tener en cuenta que el agrietamiento de la contraseña y la creación de listas de palabras personalizadas es un juego de adivinanzas en la mayoría de los casos. Podemos reducir esto y realizar adivinanzas más específicas si tenemos información sobre la política de contraseña y tener en cuenta el nombre de la empresa, la región geográfica, la industria y otros temas/palabras que los usuarios pueden seleccionar para crear sus contraseñas. Las excepciones son, por supuesto, casos en los que se filtran y se encuentran contraseñas.

### **Reglas existentes hashcat**

Mutaciones de contraseña

```
xnoxos@htb[/htb]$ ls /usr/share/hashcat/rules/best64.rule                  specific.rule
combinator.rule              T0XlC-insert_00-99_1950-2050_toprules_0_F.rule
d3ad0ne.rule                 T0XlC-insert_space_and_special_0_F.rule
dive.rule                    T0XlC-insert_top_100_passwords_1_G.rule
generated2.rule              T0XlC.rule
generated.rule               T0XlCv1.rule
hybrid                       toggles1.rule
Incisive-leetspeak.rule      toggles2.rule
InsidePro-HashManager.rule   toggles3.rule
InsidePro-PasswordsPro.rule  toggles4.rule
leetspeak.rule               toggles5.rule
oscommerce.rule              unix-ninja-leetspeak.rule
rockyou-30000.rule

```

Ahora podemos usar otra herramienta llamada[Cewl](https://github.com/digininja/CeWL)para escanear posibles palabras del sitio web de la compañía y guárdelas en una lista separada. Luego podemos combinar esta lista con las reglas deseadas y crear una lista de contraseñas personalizada que tenga una mayor probabilidad de adivinar una contraseña correcta. Especificamos algunos parámetros, como la profundidad de Spider (`-d`), la longitud mínima de la palabra (`-m`), el almacenamiento de las palabras encontradas en minúsculas (`--lowercase`), as well as the file where we want to store the results (`-w`).

### **Generar listas de palabras usando cewl**

Mutaciones de contraseña

```
xnoxos@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlistxnoxos@htb[/htb]$ wc -l inlane.wordlist326

```