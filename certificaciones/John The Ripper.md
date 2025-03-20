# John The Ripper

[John the Ripper](https://github.com/openwall/john) (`JTR`o`john`) es una herramienta de pentestación esencial utilizada para verificar la fuerza de las contraseñas y las contraseñas de crack cifradas (o hash) utilizando la fuerza bruta o los ataques de diccionario. Es un software de código abierto desarrollado inicialmente para sistemas basados en UNIX y lanzado por primera vez en 1996. Se ha convertido en un elemento básico de los profesionales de la seguridad debido a sus diversas capacidades. La variante "Jumbo" se recomienda para aquellos en el campo de seguridad, ya que tiene optimizaciones de rendimiento y características adicionales, como listas de palabras multilingües y soporte para arquitecturas de 64 bits. Esta versión es más efectiva para descifrar contraseñas con mayor precisión y velocidad.

Con esto, podemos usar varias herramientas para convertir diferentes tipos de archivos y hashes en un formato que se puede utilizar por John. Además, el software se actualiza regularmente para mantenerse al día con las tendencias y tecnologías de seguridad actuales, asegurando la seguridad del usuario.

---

# **Tecnologías de cifrado**

| **Tecnología de cifrado** | **Descripción** |
| --- | --- |
| `UNIX crypt(3)` | Crypt (3) es un sistema de cifrado UNIX tradicional con una clave de 56 bits. |
| `Traditional DES-based` | El cifrado basado en DES utiliza el algoritmo estándar de cifrado de datos para cifrar datos. |
| `bigcrypt` | BigCrypt es una extensión del cifrado tradicional basado en DES. Utiliza una clave de 128 bits. |
| `BSDI extended DES-based` | El cifrado basado en BSDI extendido es una extensión del cifrado tradicional basado en DES y utiliza una clave de 168 bits. |
| `FreeBSD MD5-based`(Linux y Cisco) | El cifrado basado en FreeBSD MD5 utiliza el algoritmo MD5 para cifrar datos con una clave de 128 bits. |
| `OpenBSD Blowfish-based` | El cifrado basado en Blowfish OpenBSD utiliza el algoritmo de pez soplete para cifrar datos con una clave de 448 bits. |
| `Kerberos/AFS` | Kerberos y AFS son sistemas de autenticación que utilizan el cifrado para garantizar la comunicación segura de la entidad. |
| `Windows LM` | El cifrado de Windows LM utiliza el algoritmo estándar de cifrado de datos para cifrar datos con una clave de 56 bits. |
| `DES-based tripcodes` | Los códigos de tripes basados en DES se utilizan para autenticar a los usuarios en función del algoritmo estándar de cifrado de datos. |
| `SHA-crypt hashes` | Los hashes Sha-Crypt se utilizan para cifrar datos con una clave de 256 bits y están disponibles en versiones más nuevas de Fedora y Ubuntu. |
| `SHA-crypt`y`SUNMD5 hashes`(Solaris) | Los hashes Sha-Crypt y SUNMD5 utilizan los algoritmos Sha-Crypt y MD5 para cifrar datos con una clave de 256 bits y están disponibles en Solaris. |
| `...` | Y muchos más. |

---

# **Métodos de ataque**

### **Ataques de diccionario**

Los ataques de diccionario implican el uso de una lista de palabras y frases preceneradas (conocidas como diccionario) para intentar descifrar una contraseña. Esta lista de palabras y frases a menudo se adquiere de varias fuentes, como diccionarios disponibles públicamente, contraseñas filtradas o incluso compradas de compañías especializadas. El diccionario se usa para generar una serie de cadenas que luego se utilizan para compararse con las contraseñas hash. Si se encuentra una coincidencia, la contraseña se agrieta, proporcionando un acceso al atacante al sistema y a los datos almacenados dentro de él. Este tipo de ataque es altamente efectivo. Por lo tanto, es esencial tomar las medidas necesarias para garantizar que las contraseñas se mantengan seguras, como usar contraseñas complejas y únicas, cambiarlas regularmente y usar autenticación de dos factores.

### **Ataques de fuerza bruta**

Los ataques de fuerza bruta implican intentar cada combinación concebible de personajes que puedan formar una contraseña. Este es un proceso extremadamente lento, y el uso de este método suele ser aconsejable si no hay otras alternativas. También es importante tener en cuenta que cuanto más tiempo y más compleja sea la contraseña, más difícil será descifrar y más tiempo llevará agotar cada combinación. Por esta razón, se recomienda encarecidamente que las contraseñas tengan al menos 8 caracteres de longitud, con una combinación de letras, números y símbolos.

### **Ataques de mesa del arco iris**

Los ataques de tabla del arco iris implican el uso de una tabla de hashs precomputada y sus contraseñas correspondientes de texto sin formato, que es un método mucho más rápido que un ataque de fuerza bruta. Sin embargo, este método está limitado por el tamaño de la tabla del arco iris: cuanto más grande sea la tabla, más contraseñas y hashes que puede almacenar. Además, debido a la naturaleza del ataque, es imposible usar tablas de arco iris para determinar el texto de los hashes que no se incluyen en la tabla. Como resultado, los ataques de mesa del arco iris solo son efectivos contra los hash ya presentes en la tabla, lo que hace que cuanto más grande sea la tabla, más exitoso es el ataque.

---

# **Modos de agrietamiento**

`Single Crack Mode`es uno de los modos John más comunes utilizados cuando se intenta descifrar contraseñas utilizando una sola lista de contraseñas. Es un ataque de fuerza bruta, lo que significa que todas las contraseñas en la lista se prueban, una por una, hasta que se encuentre la correcta. Este método es la forma más básica y directa de descifrar contraseñas y, por lo tanto, es una opción popular para aquellos que deseen obtener acceso a un sistema seguro. Sin embargo, está lejos del método más eficiente, ya que puede tomar una cantidad de tiempo indefinida para romper una contraseña, dependiendo de la longitud y la complejidad de la contraseña en cuestión. La sintaxis básica para el comando es:

### **Modo de crack**

John the Ripper

```
xnoxos@htb[/htb]$ john --format=<hash_type> <hash or hash_file>

```

Por ejemplo, si tenemos un archivo nombrado`hashes_to_crack.txt`que contiene`SHA-256`Hashes, el comando para descifrarlos sería:

John the Ripper

```
xnoxos@htb[/htb]$ john --format=sha256 hashes_to_crack.txt
```

- `john`es el comando para ejecutar el programa John the Ripper
- `-format=sha256`Especifica que el formato hash es SHA-256
- `hashes.txt`¿El nombre del archivo que contiene los hashes se agrietará?

Cuando ejecutemos el comando, John leerá los hash del archivo especificado, y luego intentará descifrarlos comparándolos con las palabras en su lista de palabras incorporada y cualquier lista de palabras adicional especificada con el`--wordlist`opción. Además, utilizará cualquier regla establecida con el`--rules`Opción (si se dan reglas) para generar más contraseñas candidatas.

El proceso de descifrar las contraseñas puede ser`very time-consuming`, ya que la cantidad de tiempo requerida para descifrar una contraseña depende de múltiples factores, como la complejidad de la contraseña, la configuración de la máquina y el tamaño de la lista de palabras. Rompar contraseñas es casi cuestión de suerte. Debido a que la contraseña en sí puede ser elemental, pero si usamos una lista incorrecta donde la palabra no está presente o no puede ser generada por John, eventualmente no podremos descifrar la contraseña.

John emitirá las contraseñas agrietadas a la consola y el archivo "John.pot" (`~/.john/john.pot`) al directorio de inicio del usuario actual. Además, continuará agrietando los hash restantes en el fondo, y podemos verificar el progreso ejecutando el`john --show`dominio. Para maximizar las posibilidades de éxito, es importante garantizar que las listas de palabras y las reglas utilizadas sean integrales y actualizadas.

### **Agrietando con John**

| **Formato hash** | **Comando de ejemplo** | **Descripción** |
| --- | --- | --- |
| AFS | `john --format=afs hashes_to_crack.txt` | AFS (Sistema de archivos Andrew) Password Hashes |
| befgg | `john --format=bfegg hashes_to_crack.txt` | Hashes de BfEgg utilizados en Bot de IRC de EggRrop |
| BF | `john --format=bf hashes_to_crack.txt` | Cripta a base de soplar (3) hashes |
| bsdi | `john --format=bsdi hashes_to_crack.txt` | BSDI Crypt (3) Hashes |
| cripta (3) | `john --format=crypt hashes_to_crack.txt` | Hastas tradicionales de la cripta Unix (3) |
| desesperación | `john --format=des hashes_to_crack.txt` | Criptos tradicionales basados en des (3) hashes |
| DMD5 | `john --format=dmd5 hashes_to_crack.txt` | DMD5 (Dragonfly BSD MD5) Password Hashes |
| dominó | `john --format=dominosec hashes_to_crack.txt` | IBM Lotus Domino 6/7 Password Hashes |
| Episerver Sid Hashes | `john --format=episerver hashes_to_crack.txt` | Episerver Sid (identificador de seguridad) Password Hashes |
| HDAA | `john --format=hdaa hashes_to_crack.txt` | HDAA Password Hashes utilizados en OpenWall GNU/Linux |
| HMAC-MD5 | `john --format=hmac-md5 hashes_to_crack.txt` | HMAC-MD5 CONTRASEÑA |
| hmailserver | `john --format=hmailserver hashes_to_crack.txt` | hmailserver contraseña hash |
| IPB2 | `john --format=ipb2 hashes_to_crack.txt` | Invision Power Board 2 Password Hashes |
| KRB4 | `john --format=krb4 hashes_to_crack.txt` | Kerberos 4 Password Hashes |
| KRB5 | `john --format=krb5 hashes_to_crack.txt` | Kerberos 5 Password Hashes |
| Lm | `john --format=LM hashes_to_crack.txt` | LM (LAN Manager) hash de contraseña |
| Lotus5 | `john --format=lotus5 hashes_to_crack.txt` | Lotus Notes/Domino 5 Password Hashes |
| MSCASH | `john --format=mscash hashes_to_crack.txt` | MS Cache Password Hashes |
| mscash2 | `john --format=mscash2 hashes_to_crack.txt` | MS CACHE V2 PASSAWS PASSWORD HAHES |
| MSCHAP2 | `john --format=mschapv2 hashes_to_crack.txt` | MS CHAP V2 PASSAWS PASSAY HAHES |
| MSKRB5 | `john --format=mskrb5 hashes_to_crack.txt` | MS Kerberos 5 Password Hashes |
| MSSQL05 | `john --format=mssql05 hashes_to_crack.txt` | MS SQL 2005 Password Hashes |
| MSSQL | `john --format=mssql hashes_to_crack.txt` | MS SQL Password Hashes |
| mySQL-Fast | `john --format=mysql-fast hashes_to_crack.txt` | MySQL Fast Password Hashes |
| mysql | `john --format=mysql hashes_to_crack.txt` | MySQL Password Hashes |
| mysql-sha1 | `john --format=mysql-sha1 hashes_to_crack.txt` | Mysql sha1 contraseña hash |
| Netlm | `john --format=netlm hashes_to_crack.txt` | Netlm (NT LAN Manager) Password Hashes |
| Netlmv2 | `john --format=netlmv2 hashes_to_crack.txt` | NetLMV2 (NT LAN Manager Versión 2) Password Hashes |
| Netntlm | `john --format=netntlm hashes_to_crack.txt` | Netntlm (NT LAN Manager) Password Hashes |
| Netntlmv2 | `john --format=netntlmv2 hashes_to_crack.txt` | NetNTLMV2 (NT LAN Manager Versión 2) Hashes de contraseña |
| Nethalflm | `john --format=nethalflm hashes_to_crack.txt` | Nethalflm (NT LAN Manager) Password Hashes |
| MD5NS | `john --format=md5ns hashes_to_crack.txt` | MD5NS (espacio de nombres MD5) Hashes de contraseña |
| nsldap | `john --format=nsldap hashes_to_crack.txt` | NSLDAP (OpenLDAP SHA) PASSAWASS PASSWORD |
| ssha | `john --format=ssha hashes_to_crack.txt` | SSHA (saled SHA) Hashes de contraseña |
| Nuevo Testamento | `john --format=nt hashes_to_crack.txt` | NT (Windows NT) Password Hashes |
| abrezsha | `john --format=openssha hashes_to_crack.txt` | OpenSSH Key privado contraseña hash |
| Oracle11 | `john --format=oracle11 hashes_to_crack.txt` | Oracle 11 Password Hashes |
| oráculo | `john --format=oracle hashes_to_crack.txt` | Hashes de contraseña de Oracle |
| pdf | `john --format=pdf hashes_to_crack.txt` | PDF (formato de documento portátil) Hashes de contraseña |
| PhPass-MD5 | `john --format=phpass-md5 hashes_to_crack.txt` | PhPass-MD5 (Marco de hash de la contraseña de Php Portable) |
| PHPS | `john --format=phps hashes_to_crack.txt` | Hashes de contraseña de PHPS |
| Pix-MD5 | `john --format=pix-md5 hashes_to_crack.txt` | Cisco Pix MD5 Password Hashes |
| correos | `john --format=po hashes_to_crack.txt` | PO (Sybase SQL Anywhere) Password Hashes |
| rar | `john --format=rar hashes_to_crack.txt` | RAR (Winrar) Password Hashes |
| RAW-MD4 | `john --format=raw-md4 hashes_to_crack.txt` | Hashes de contraseña MD4 RAW |
| RAW-MD5 | `john --format=raw-md5 hashes_to_crack.txt` | Hashes de contraseña MD5 RAW |
| RAW-MD5-UNICODE | `john --format=raw-md5-unicode hashes_to_crack.txt` | Hashes de contraseña Unicode RAW MD5 |
| Raw-Sha1 | `john --format=raw-sha1 hashes_to_crack.txt` | Hashes de contraseña SHA1 sin formato |
| Raw-sha224 | `john --format=raw-sha224 hashes_to_crack.txt` | Hashes de contraseña SHA224 RAW |
| Raw-sha256 | `john --format=raw-sha256 hashes_to_crack.txt` | Hashes de contraseña SHA256 RAW |
| Raw-sha384 | `john --format=raw-sha384 hashes_to_crack.txt` | Hashes de contraseña de SHA384 sin procesar |
| Raw-sha512 | `john --format=raw-sha512 hashes_to_crack.txt` | Hashes de contraseña SHA512 RAW |
| SHA salado | `john --format=salted-sha hashes_to_crack.txt` | Hashes de contraseña de SHA salted |
| SAPB | `john --format=sapb hashes_to_crack.txt` | SAP CODVN B (Bcode) hash de contraseña |
| sapg | `john --format=sapg hashes_to_crack.txt` | SAP CODVN G (contraseña) hash de contraseña |
| Gen Sha1 | `john --format=sha1-gen hashes_to_crack.txt` | Hashes de contraseña genéricos SHA1 |
| skey | `john --format=skey hashes_to_crack.txt` | S/Key (contraseña única) hashes |
| ssh | `john --format=ssh hashes_to_crack.txt` | SSH (shell seguro) hash de contraseña |
| sybasease | `john --format=sybasease hashes_to_crack.txt` | Sybase ASE Password Hashes |
| XSHA | `john --format=xsha hashes_to_crack.txt` | XSHA (extendido SHA) Hashes de contraseña |
| cremallera | `john --format=zip hashes_to_crack.txt` | Zip (Winzip) Password Hashes |

### **Modo de lista de palabras**

`Wordlist Mode`se usa para descifrar contraseñas utilizando múltiples listas de palabras. Es un ataque de diccionario que significa que intentará todas las palabras en las listas una por una hasta que encuentre la correcta. Generalmente se usa para descifrar múltiples hash de contraseña utilizando una lista de palabras o una combinación de listas de palabras. Es más efectivo que el modo de crack único porque utiliza más palabras, pero sigue siendo relativamente básico. La sintaxis básica para el comando es:

John the Ripper

```
xnoxos@htb[/htb]$ john --wordlist=<wordlist_file> --rules <hash_file>

```

Primero, especificamos el archivo o archivos de la lista de palabras que se utilizarán para descifrar los hashs de contraseña. La lista de palabras puede estar en formato de texto plano, con una palabra por línea. Se pueden especificar varias listas de palabras separándolas con una coma. Luego podemos especificar un conjunto de reglas o aplicar las reglas de desglose incorporadas a las palabras en la lista de palabras. Estas reglas generan contraseñas candidatas utilizando transformaciones como agregar números, capitalizar letras y agregar caracteres especiales.

### **Modo incremental**

`Incremental Mode`es un modo de John avanzado utilizado para descifrar contraseñas usando un conjunto de caracteres. Es un ataque híbrido, lo que significa que intentará hacer coincidir la contraseña intentando todas las combinaciones posibles de personajes del conjunto de caracteres. Este modo es el más efectivo pero más lento de todos los modos John. Este modo funciona mejor cuando sabemos cuál podría ser la contraseña, ya que intentará todas las combinaciones posibles en secuencia, comenzando desde la más corta. Esto lo hace mucho más rápido que el ataque de la fuerza bruta, donde todas las combinaciones se prueban al azar. Además, el modo incremental también se puede usar para descifrar contraseñas débiles, lo que puede ser un desafío para descifrar el uso de los modos John estándar. La principal diferencia entre el modo incremental y el modo de lista de palabras es la fuente de las conjeturas de contraseña. El modo incremental genera las conjeturas en la mosca, mientras que el modo de lista de palabras usa una lista predefinida de palabras. Al mismo tiempo, el modo de crack único se usa para verificar una sola contraseña contra un hash.

La sintaxis para ejecutar John the Ripper en modo incremental es la siguiente:

### **Modo incremental en John**

John the Ripper

```
xnoxos@htb[/htb]$ john --incremental <hash_file>

```

Usando este comando, leeremos los hashes en el archivo hash especificado y luego generaremos todas las combinaciones posibles de caracteres, comenzando con un solo carácter e incrementando con cada iteración. Es importante tener en cuenta que este modo es`highly resource intensive`y puede tardar mucho tiempo en completarse, dependiendo de la complejidad de las contraseñas, la configuración de la máquina y el número de caracteres establecidos. Además, es importante tener en cuenta que el conjunto de caracteres predeterminado se limita a`a-zA-Z0-9`. Por lo tanto, si intentamos descifrar contraseñas complejas con caracteres especiales, necesitamos usar un conjunto de caracteres personalizado.

---

# **Archivos de roto**

También es posible descifrar incluso archivos protegidos con contraseña o encriptados con John. Utilizamos herramientas adicionales que procesan los archivos dados y producen hashes con los que John puede trabajar. Detecta automáticamente los formatos e intenta descifrarlos. La sintaxis para esto puede verse así:

### **Rompiendo archivos con John**

John the Ripper

```
cry0l1t3@htb:~$ <tool> <file_to_crack> > file.hash
cry0l1t3@htb:~$ pdf2john server_doc.pdf > server_doc.hashcry0l1t3@htb:~$ john server_doc.hash# ORcry0l1t3@htb:~$ john --wordlist=<wordlist.txt> server_doc.hash

```

Además, podemos usar diferentes modos para esto con nuestras listas y reglas personales. Hemos creado una lista que incluye muchas, pero no todas las herramientas que se pueden usar para John:

| **Herramienta** | **Descripción** |
| --- | --- |
| `pdf2john` | Convierte documentos PDF para John |
| `ssh2john` | Convierte las llaves privadas de SSH para John |
| `mscash2john` | Convierte los hash de MS Cash para John |
| `keychain2john` | Convierte archivos de llavero OS X para John |
| `rar2john` | Convierte los archivos de rar para John |
| `pfx2john` | Convierte archivos PKC#12 para John |
| `truecrypt_volume2john` | Convierte los volúmenes de TrueCrypt para John |
| `keepass2john` | Convierte las bases de datos Keepass para John |
| `vncpcap2john` | Convierte los archivos PCAP VNC para John |
| `putty2john` | Convierte las llaves privadas de masilla para John |
| `zip2john` | Convierte los archivos zip para John |
| `hccap2john` | Convierte capturas de apretón de manos WPA/WPA2 para John |
| `office2john` | Convierte los documentos de MS Office para John |
| `wpa2john` | Convierte los apretones de manos WPA/WPA2 para John |

Se pueden encontrar más de estas herramientas en`Pwnbox`De la siguiente manera:

John the Ripper

```
xnoxos@htb[/htb]$ locate *2john*/usr/bin/bitlocker2john
/usr/bin/dmg2john
/usr/bin/gpg2john
/usr/bin/hccap2john
/usr/bin/keepass2john
/usr/bin/putty2john
/usr/bin/racf2john
/usr/bin/rar2john
/usr/bin/uaf2john
/usr/bin/vncpcap2john
/usr/bin/wlanhcx2john
/usr/bin/wpapcap2john
/usr/bin/zip2john
/usr/share/john/1password2john.py
/usr/share/john/7z2john.pl
/usr/share/john/DPAPImk2john.py
/usr/share/john/adxcsouf2john.py
/usr/share/john/aem2john.py
/usr/share/john/aix2john.pl
/usr/share/john/aix2john.py
/usr/share/john/andotp2john.py
/usr/share/john/androidbackup2john.py
...SNIP...

```

En este módulo, trabajaremos mucho con John y, por lo tanto, debemos saber de qué es capaz esta herramienta.