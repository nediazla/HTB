# Archivos protegidos

Además de archivos independientes, también hay otro formato de archivos que pueden contener no solo datos, como un documento de oficina o un PDF, sino también otros archivos dentro de ellos. Este formato se llama`archive`o`compressed file`Eso puede protegerse con una contraseña si es necesario.

Supongamos el papel de un empleado en una empresa administrativa e imaginemos que nuestro cliente quiere resumir el análisis en diferentes formatos, como Excel, PDF, Word y una presentación correspondiente. Una solución sería enviar estos archivos individualmente, pero si ampliamos este ejemplo a una gran empresa que se ocupa de varios proyectos que se ejecutan simultáneamente, este tipo de transferencia de archivos puede volverse engorroso y conducir a que se pierdan archivos individuales. En estos casos, los empleados a menudo confían en los archivos, que les permiten dividir todos los archivos necesarios de manera estructurada de acuerdo con los proyectos (a menudo en subcarpetas), resumirlos y empacarlos en un solo archivo.

Hay muchos tipos de archivos de archivo. Algunas extensiones de archivos comunes incluyen, pero no se limitan a:

|  |  |  |  |
| --- | --- | --- | --- |
| `tar` | `gz` | `rar` | `zip` |
| `vmdb/vmx` | `cpt` | `truecrypt` | `bitlocker` |
| `kdbx` | `luks` | `deb` | `7z` |
| `pkg` | `rpm` | `war` | `gzip` |

Se puede encontrar una extensa lista de tipos de archivo en[Fileinfo.com](https://fileinfo.com/filetypes/compressed). Sin embargo, en lugar de escribirlos manualmente, también podemos consultarlos usando una línea de una sola, filtrarlos y guardarlos en un archivo si es necesario. Al momento de escribir, hay`337`Tipos de archivos de archivos listados en fileInfo.com.

### **Descargue todas las extensiones de archivos**

Archivos protegidos

```
xnoxos@htb[/htb]$ curl -s https://fileinfo.com/filetypes/compressed | html2text | awk '{print tolower($1)}' | grep "\." | tee -a compressed_ext.txt.mint
.htmi
.tpsr
.mpkg
.arduboy
.ice
.sifz
.fzpz
.rar
.comppkg.hauptwerk.rar
...SNIP...

```

Es importante tener en cuenta que no todos los archivos anteriores admiten protección de contraseña. A menudo se usan otras herramientas para proteger los archivos correspondientes con una contraseña. Por ejemplo, con`tar`, la herramienta`openssl`o`gpg`se usa para cifrar los archivos.

---

# **Archivos de agrietamiento**

Dado el número de diferentes archivos y la combinación de herramientas, mostraremos solo algunas de las posibles formas de descifrar archivos específicos en esta sección. Cuando se trata de archivos protegidos con contraseña, generalmente necesitamos ciertos scripts que nos permitan extraer los hashes de los archivos protegidos y usarlos para descifrar la contraseña de ellos.

El formato .zip a menudo se usa en gran medida en entornos de Windows para comprimir muchos archivos en un solo archivo. El procedimiento que ya hemos visto sigue siendo el mismo excepto para usar un script diferente para extraer los hashes.

---

# **Crujido zip**

### **Usando Zip2John**

Archivos protegidos

```
xnoxos@htb[/htb]$ zip2john ZIP.zip > zip.hashver 2.0 efh 5455 efh 7875 ZIP.zip/flag.txt PKZIP Encr: 2b chk, TS_chk, cmplen=42, decmplen=30, crc=490E7510

```

Al extraer los hash, también veremos qué archivos están en el archivo ZIP.

### **Ver el contenido de Zip.hash**

Archivos protegidos

```
xnoxos@htb[/htb]$ cat zip.hash ZIP.zip/customers.csv:$pkzip2$1*2*2*0*2a*1e*490e7510*0*42*0*2a*490e*409b*ef1e7feb7c1cf701a6ada7132e6a5c6c84c032401536faf7493df0294b0d5afc3464f14ec081cc0e18cb*$/pkzip2$:customers.csv:ZIP.zip::ZIP.zip
```

Una vez que hemos extraído el hash, ahora podemos usar`john`nuevamente para romperlo con la lista de contraseñas deseada. Porque si`john`Logrece con éxito, nos mostrará la contraseña correspondiente que podemos usar para abrir el archivo ZIP.

### **Agrietar el hash con John**

Archivos protegidos

```
xnoxos@htb[/htb]$ john --wordlist=rockyou.txt zip.hashUsing default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
1234             (ZIP.zip/customers.csv)
1g 0:00:00:00 DONE (2022-02-09 09:18) 100.0g/s 250600p/s 250600c/s 250600C/s 123456..1478963
Use the "--show" option to display all of the cracked passwords reliably
Session completed

```

### **Ver el hash agrietado**

Archivos protegidos

```
xnoxos@htb[/htb]$ john zip.hash --showZIP.zip/customers.csv:1234:customers.csv:ZIP.zip::ZIP.zip

1 password hash cracked, 0 left

```

---

# **Cracking OpenSsl Archivos encriptados**

Además, no siempre es aparente directamente si el archivo encontrado está protegido con contraseña, especialmente si se utiliza una extensión de archivo que no admite la protección de contraseñas. Como ya hemos discutido,`openssl`se puede usar para cifrar el`gzip`formato como ejemplo. Usando la herramienta`file`, podemos obtener información sobre el formato del archivo especificado. Esto podría verse así, por ejemplo:

### **Enumerando los archivos**

Archivos protegidos

```
xnoxos@htb[/htb]$ lsGZIP.gzip  rockyou.txt

```

### **Uso de archivo**

Archivos protegidos

```
xnoxos@htb[/htb]$ file GZIP.gzip GZIP.gzip: openssl enc'd data with salted password

```

Al agrietarse los archivos y archivos cifrados de OpenSSL, podemos encontrar muchas dificultades diferentes que traerán muchos falsos positivos o incluso no pueden adivinar la contraseña correcta. Por lo tanto, la opción más segura para el éxito es usar el`openssl`herramienta en un`for-loop`Eso intenta extraer los archivos del archivo directamente si la contraseña se adivina correctamente.

La siguiente línea mostrará muchos errores relacionados con el formato GZIP, que podemos ignorar. Si hemos usado la lista de contraseñas correcta, como en este ejemplo, veremos que hemos extraído con éxito otro archivo del archivo.

### **Uso de un for-bucle para mostrar contenidos extraídos**

Archivos protegidos

```
xnoxos@htb[/htb]$ for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i2>/dev/null| tar xz;donegzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now

<SNIP>

```

Una vez que el for-bucle ha terminado, podemos volver a buscar nuevamente en la carpeta actual para verificar si el agrietamiento del archivo fue exitoso.

### **Enumerando el contenido del archivo agrietado**

Archivos protegidos

```
xnoxos@htb[/htb]$ lscustomers.csv  GZIP.gzip  rockyou.txt

```

---

# **Cracking BitLocker Cumenced Drives**

[Bitlocker](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10)es un programa de cifrado para particiones completas y unidades externas. Microsoft lo desarrolló para el sistema operativo Windows. Ha estado disponible desde Windows Vista y usa el`AES`Algoritmo de cifrado con 128 bits o 256 bits de longitud. Si se olvida la contraseña o PIN para BitLocker, podemos usar la clave de recuperación para descifrar la partición o la unidad. La clave de recuperación es una cadena de números de 48 dígitos generadas durante la configuración de BitLocker que también puede ser forzada.

A menudo se crean unidades virtuales en las que la información personal, las notas y los documentos se almacenan en la computadora o la computadora portátil proporcionada por la Compañía para evitar el acceso a esta información por terceros. De nuevo, podemos usar un script llamado`bitlocker2john`Para extraer el hash necesitamos romper.[Cuatro hash diferentes](https://openwall.info/wiki/john/OpenCL-BitLocker)se extraerá, que se puede usar con diferentes modos hash hashcat. Para nuestro ejemplo, trabajaremos con el primero, que se refiere a la contraseña de BitLocker.

### **Usando bitLocker2John**

Archivos protegidos

```
xnoxos@htb[/htb]$ bitlocker2john -i Backup.vhd > backup.hashesxnoxos@htb[/htb]$ grep "bitlocker\$0" backup.hashes > backup.hashxnoxos@htb[/htb]$ cat backup.hash$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e...SNIP...70696f7eab6b
```

Ambos`John`y`Hashcat`se puede usar para este propósito. Este ejemplo analizará el procedimiento con`Hashcat`. El modo hashcat para agrietarse bitlocker hashes es`-m 22100`. Por lo tanto, proporcionamos a hashcat el archivo con el hash, especificamos nuestra lista de contraseñas y especificamos el modo hash. Dado que este es un cifrado robusto (`AES`), el agrietamiento puede llevar algún tiempo, dependiendo del hardware utilizado. Además, podemos especificar el nombre de archivo en el que se debe almacenar el resultado.

### **Usar hashcat para romper la copia de seguridad.**

Archivos protegidos

```
xnoxos@htb[/htb]$ hashcat -m 22100 backup.hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt -o backup.crackedhashcat (v6.1.1) starting...

<SNIP>

$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f:1234qwer
Session..........: hashcat
Status...........: Cracked
Hash.Name........: BitLocker
Hash.Target......:$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$10...8ec54fTime.Started.....: Wed Feb  9 11:46:40 2022 (1 min, 42 secs)
Time.Estimated...: Wed Feb  9 11:48:22 2022 (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       28 H/s (8.79ms) @ Accel:32 Loops:4096 Thr:1 Vec:8Recovered........: 1/1 (100.00%) Digests
Progress.........: 2880/6163 (46.73%)
Rejected.........: 0/2880 (0.00%)
Restore.Point....: 2816/6163 (45.69%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:1044480-1048576Candidates.#1....: chemical -> secretsStarted: Wed Feb  9 11:46:35 2022
Stopped: Wed Feb  9 11:48:23 2022

```

### **Ver el hash agrietado**

Archivos protegidos

```
xnoxos@htb[/htb]$ cat backup.cracked$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f:1234qwer
```

Una vez que hayamos descifrado la contraseña, podremos abrir las unidades encriptadas. La forma más fácil de montar una unidad virtual cifrada de bitlocker es transferirla a un sistema de Windows y montarlo. Para hacer esto, solo tenemos que hacer doble clic en la unidad virtual. Dado que está protegido con contraseña, Windows nos mostrará un error. Después de montar, nuevamente podemos hacer doble clic en BitLocker para solicitarnos la contraseña.

### **Windows - Montaje BitLocker VHD**

![](https://academy.hackthebox.com/storage/modules/147/bitlocker.png)