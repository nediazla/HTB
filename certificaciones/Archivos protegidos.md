# Archivos protegidos

El uso del cifrado de archivos a menudo aún falta`private`y`business`asuntos. Incluso hoy en día, los correos electrónicos que contienen solicitudes de empleo, estados de cuenta o contratos a menudo se envían sin cifrar. Esto es muy negligente y, en muchos casos, incluso se castiga con la ley. Por ejemplo, GDPR exige el requisito de almacenamiento cifrado y transmisión de datos personales en la Unión Europea. Especialmente en casos de negocios, esto es bastante diferente para los correos electrónicos. Hoy en día, es bastante común comunicarse`confidential`temas o envío`sensitive`datos`by email`. Sin embargo, los correos electrónicos no son mucho más seguros que las postales, lo que puede interceptarse si el atacante se coloca correctamente.

Cada vez más empresas están aumentando sus precauciones e infraestructura de seguridad de TI a través de cursos de capacitación y seminarios de conciencia de seguridad. Como resultado, se está volviendo cada vez más común que los empleados de la compañía cifre/codifiquen archivos confidenciales. Sin embargo, incluso estos se pueden agrupar y leer con la elección correcta de listas y herramientas. En muchos casos,`symmetric encryption`como`AES-256`se usa para almacenar de forma segura archivos o carpetas individuales. Aquí, el`same key`se usa para cifrar y descifrar un archivo.

Por lo tanto, para enviar archivos,`asymmetric encryption`se usa, en el que`two separate keys`son necesarios. El remitente cifra el archivo con el`public key`del destinatario. El destinatario, a su vez, puede descifrar el archivo utilizando un`private key`.

---

# **Caza de archivos codificados**

Muchas extensiones de archivos diferentes pueden identificar estos tipos de archivos encriptados/codificados. Por ejemplo, se puede encontrar una lista útil en[FileInfo](https://fileinfo.com/filetypes/encoded). Sin embargo, para nuestro ejemplo, solo veremos los archivos más comunes como los siguientes:

### **Caza de archivos**

Archivos protegidos

```
cry0l1t3@unixclient:~$ for ext in $(echo ".xls .xls* .xltx .csv .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext2>/dev/null | grep -v "lib\|fonts\|share\|core" ;doneFile extension:  .xls

File extension:  .xls*

File extension:  .xltx

File extension:  .csv
/home/cry0l1t3/Docs/client-emails.csv
/home/cry0l1t3/ruby-2.7.3/gems/test-unit-3.3.4/test/fixtures/header-label.csv
/home/cry0l1t3/ruby-2.7.3/gems/test-unit-3.3.4/test/fixtures/header.csv
/home/cry0l1t3/ruby-2.7.3/gems/test-unit-3.3.4/test/fixtures/no-header.csv
/home/cry0l1t3/ruby-2.7.3/gems/test-unit-3.3.4/test/fixtures/plus.csv
/home/cry0l1t3/ruby-2.7.3/test/win32ole/orig_data.csv

File extension:  .od*
/home/cry0l1t3/Docs/document-temp.odt
/home/cry0l1t3/Docs/product-improvements.odp
/home/cry0l1t3/Docs/mgmt-spreadsheet.ods
...SNIP...

```

Si encontramos extensiones de archivos en el sistema con el que no estamos familiarizados, podemos usar los motores de búsqueda con los que estamos familiarizados para descubrir la tecnología detrás de ellos. Después de todo, hay cientos de extensiones de archivos diferentes, y se espera que nadie las conozca a todos de memoria. Primero, sin embargo, debemos saber cómo encontrar la información relevante que nos ayude. Nuevamente, podemos usar los pasos que ya cubrimos en el`Credential Hunting`secciones o repetirlas para encontrar claves SSH en el sistema.

### **Caza de teclas SSH**

Archivos protegidos

```
cry0l1t3@unixclient:~$ grep -rnw "PRIVATE KEY" /*2>/dev/null | grep ":1"/home/cry0l1t3/.ssh/internal_db:1:-----BEGIN OPENSSH PRIVATE KEY-----
/home/cry0l1t3/.ssh/SSH.private:1:-----BEGIN OPENSSH PRIVATE KEY-----
/home/cry0l1t3/Mgmt/ceil.key:1:-----BEGIN OPENSSH PRIVATE KEY-----

```

La mayoría de las claves SSH que encontraremos hoy en día están encriptadas. Podemos reconocer esto por el encabezado de la tecla SSH porque esto muestra el método de cifrado en uso.

### **Teclas SSH cifradas**

Archivos protegidos

```
cry0l1t3@unixclient:~$ cat /home/cry0l1t3/.ssh/SSH.private-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,2109D25CC91F8DBFCEB0F7589066B2CC

8Uboy0afrTahejVGmB7kgvxkqJLOczb1I0/hEzPU1leCqhCKBlxYldM2s65jhflD
4/OH4ENhU7qpJ62KlrnZhFX8UwYBmebNDvG12oE7i21hB/9UqZmmHktjD3+OYTsD
...SNIP...

```

Si vemos dicho encabezado en una clave SSH, en la mayoría de los casos, no podremos usarlo inmediatamente sin más acciones. Esto se debe a que las teclas SSH cifradas están protegidas con una frase de pases que debe ingresarse antes de su uso. Sin embargo, muchos a menudo son descuidados en la selección de contraseña y su complejidad porque SSH se considera un protocolo seguro, y muchos no saben que incluso liviano[AES-128-CBC](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)puede ser agrietado.

---

# **Agrietando con John**

`John The Ripper`Tiene muchos scripts diferentes para generar hashes a partir de archivos que luego podemos usar para agrietarse. Podemos encontrar estos scripts en nuestro sistema utilizando el siguiente comando.

### **Guiones de John hashing**

Archivos protegidos

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

Podemos convertir muchos formatos diferentes en hashs individuales e intentar descifrar las contraseñas con esto. Luego, podemos abrir, leer y usar el archivo si tenemos éxito. Hay un guión de Python llamado`ssh2john.py`Para las teclas SSH, que genera los hashes correspondientes para las teclas SSH cifradas, que luego podemos almacenar en archivos.

Archivos protegidos

```
xnoxos@htb[/htb]$ ssh2john.py SSH.private > ssh.hashxnoxos@htb[/htb]$ cat ssh.hash ssh.private:$sshng$0$8$1C258238FD2D6EB0$2352$f7b...SNIP...
```

A continuación, necesitamos personalizar los comandos en consecuencia con la lista de contraseñas y especificar nuestro archivo con los hash como el objetivo que se agrietará. Después de eso, podemos mostrar los hashes agrietados especificando el archivo hash y utilizando el`--show`opción.

### **Rompiendo las teclas SSH**

Archivos protegidos

```
xnoxos@htb[/htb]$ john --wordlist=rockyou.txt ssh.hashUsing default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
1234         (SSH.private)
1g 0:00:00:00 DONE (2022-02-08 03:03) 16.66g/s 1747Kp/s 1747Kc/s 1747KC/s Knightsing..Babying
Session completed

```

Archivos protegidos

```
xnoxos@htb[/htb]$ john ssh.hash --showSSH.private:1234

1 password hash cracked, 0 left

```

---

# **Documentos de roto**

En el curso de nuestra carrera, nos encontraremos con muchos documentos diferentes, que también son protegidos con contraseña para evitar el acceso de personas no autorizadas. Hoy, la mayoría de las personas usan archivos de oficina y PDF para intercambiar información y datos comerciales.

Casi todos los informes, documentación y hojas de información se pueden encontrar en forma de documentos de oficina y pdfs. Esto se debe a que ofrecen la mejor representación visual de información. John proporciona un guión de Python llamado`office2john.py`extraer hash de todos los documentos de la oficina comunes que luego pueden alimentarse con John o hashcat para agrietarse fuera de línea. El procedimiento para descifrarlos sigue siendo el mismo.

### **Documentos de Microsoft Office de Microsoft**

Archivos protegidos

```
xnoxos@htb[/htb]$ office2john.py Protected.docx > protected-docx.hashxnoxos@htb[/htb]$ cat protected-docx.hashProtected.docx:$office$*2007*20*128*16*7240...SNIP...8a69cf1*98242f4da37d916305d8e2821360773b7edc481b
```

Archivos protegidos

```
xnoxos@htb[/htb]$ john --wordlist=rockyou.txt protected-docx.hashLoaded 1 password hash (Office, 2007/2010/2013 [SHA1 256/256 AVX2 8x / SHA512 256/256 AVX2 4x AES])
Cost 1 (MS Office version) is 2007 for all loaded hashes
Cost 2 (iteration count) is 50000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
1234             (Protected.docx)
1g 0:00:00:00 DONE (2022-02-08 01:25) 2.083g/s 2266p/s 2266c/s 2266C/s trisha..heart
Use the "--show" option to display all of the cracked passwords reliably
Session completed

```

Archivos protegidos

```
xnoxos@htb[/htb]$ john protected-docx.hash --showProtected.docx:1234

```

### **PDFS agrietos**

Archivos protegidos

```
xnoxos@htb[/htb]$ pdf2john.py PDF.pdf > pdf.hashxnoxos@htb[/htb]$ cat pdf.hash PDF.pdf:$pdf$2*3*128*-1028*1*16*7e88...SNIP...bd2*32*a72092...SNIP...0000*32*c48f001fdc79a030d718df5dbbdaad81d1f6fedec4a7b5cd980d64139edfcb7e
```

Archivos protegidos

```
xnoxos@htb[/htb]$ john --wordlist=rockyou.txt pdf.hashUsing default input encoding: UTF-8
Loaded 1 password hash (PDF [MD5 SHA2 RC4/AES 32/64])
Cost 1 (revision) is 3 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
1234             (PDF.pdf)
1g 0:00:00:00 DONE (2022-02-08 02:16) 25.00g/s 27200p/s 27200c/s 27200C/s bulldogs..heart
Use the "--show --format=PDF" options to display all of the cracked passwords reliably
Session completed

```

Archivos protegidos

```
xnoxos@htb[/htb]$ john pdf.hash --showPDF.pdf:1234

1 password hash cracked, 0 left

```

Una de las principales dificultades en este proceso es la generación y mutación de las listas de contraseñas. Este es el requisito previo para descifrar con éxito las contraseñas para todos los archivos y puntos de acceso protegidos con contraseña. Esto se debe a que a menudo ya no es suficiente usar una lista de contraseñas conocida en la mayoría de los casos, ya que estos son conocidos por los sistemas y a menudo son reconocidos y bloqueados por mecanismos de seguridad integrados. Estos tipos de archivos pueden ser más difíciles de descifrar (o no romperse en absoluto dentro de un tiempo razonable) porque los usuarios pueden verse obligados a seleccionar una contraseña más larga generada al azar o una frase de pases. Sin embargo, siempre vale la pena intentar descifrar documentos protegidos con contraseña, ya que pueden producir datos confidenciales que podrían ser útiles para promover nuestro acceso.