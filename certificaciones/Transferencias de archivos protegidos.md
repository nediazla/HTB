# Transferencias de archivos protegidos

Como probadores de penetración, a menudo obtenemos acceso a datos altamente confidenciales, como listas de usuarios, credenciales (es decir, descargar el archivo NTDS.DIT para el descifrado de contraseña fuera de línea) y los datos de enumeración que pueden contener información crítica sobre las infraestructuras de red de la organización y el entorno de Active Directory (AD), etc., por lo tanto, es esencial encriptar estos datos o utilizar las conexiones de datos encriptados como SSH, shtp y Https. Sin embargo, a veces estas opciones no están disponibles para nosotros, y se requiere un enfoque diferente.

Nota: A menos que un cliente solicite específicamente, no recomendamos exfiltrar datos como información de identificación personal (PII), datos financieros (es decir, números de tarjetas de crédito), secretos comerciales, etc., de un entorno del cliente. En cambio, si intenta probar los controles de la prevención de la pérdida de datos (DLP)/protecciones de filtrado de salida, cree un archivo con datos ficticios que imiten los datos que el cliente está tratando de proteger.

Por lo tanto, cifrar los datos o archivos antes de una transferencia a menudo es necesario para evitar que los datos se lean si se interceptan en tránsito.

La fuga de datos durante una prueba de penetración podría tener graves consecuencias para el probador de penetración, su empresa y el cliente. Como profesionales de la seguridad de la información, debemos actuar de forma profesional y responsable y tomar todas las medidas para proteger cualquier datos que encontremos durante una evaluación.

---

# **Cifrado de archivos en Windows**

Se pueden usar muchos métodos diferentes para cifrar archivos e información en los sistemas de Windows. Uno de los métodos más simples es el[Invoke-aesencryption.ps1](https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1)PowerShell Script. Este script es pequeño y proporciona cifrado de archivos y cadenas.

### **Invoke-aesencryption.ps1**

Transferencias de archivos protegidos

```powershell
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text"

Description
-----------
Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.

.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="

Description
-----------
Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.

.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin

Description
-----------
Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"

.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes

Description
-----------
Decrypts the file "file.bin.aes" and outputs an encrypted file "file.bin"
#>function Invoke-AESEncryption {
    [CmdletBinding()]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory = $true)]        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,        [Parameter(Mandatory = $true)]        [String]$Key,        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]        [String]$Text,        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]        [String]$Path    )

    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed        $aesManaged = New-Object System.Security.Cryptography.AesManaged        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros        $aesManaged.BlockSize = 128        $aesManaged.KeySize = 256    }

    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))        switch ($Mode) {            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                if ($Path) {                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue                    if (!$File.FullName) {                        Write-Error -Message "File not found!"
                        break
                    }
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)                    $outPath = $File.FullName + ".aes"                }

                $encryptor = $aesManaged.CreateEncryptor()                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)                $encryptedBytes = $aesManaged.IV + $encryptedBytes                $aesManaged.Dispose()                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                if ($Path) {                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime                    return "File encrypted to $outPath"
                }
            }

            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}

                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }

                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                $aesManaged.Dispose()

                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}

                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File decrypted to $outPath"
                }
            }
        }
    }

    End {
        $shaManaged.Dispose()        $aesManaged.Dispose()    }
}

```

Podemos usar cualquier método de transferencia de archivos mostrados previamente para llevar este archivo a un host de destino. Después de que se ha transferido el script, solo debe importarse como un módulo, como se muestra a continuación.

### **Módulo de importación Invoke-AesenCryption.Ps1**

Transferencias de archivos protegidos

```powershell
PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1

```

Después de importar el script, puede cifrar cadenas o archivos, como se muestra en los siguientes ejemplos. Este comando crea un archivo encriptado con el mismo nombre que el archivo encriptado pero con la extensión "`.aes`."

### **Ejemplo de cifrado de archivo**

Transferencias de archivos protegidos

```powershell
PS C:\htb> Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

File encrypted to C:\htb\scan-results.txt.aes
PS C:\htb> ls

    Directory: C:\htb

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/18/2020  12:17 AM           9734 Invoke-AESEncryption.ps1
-a----        11/18/2020  12:19 PM           1724 scan-results.txt
-a----        11/18/2020  12:20 PM           3448 scan-results.txt.aes

```

Usando muy`strong`y`unique`Es esencial las contraseñas de cifrado para cada empresa donde se realiza una prueba de penetración. Esto es para evitar que los archivos confidenciales y la información se descifren utilizando una sola contraseña que un tercero puede haber filtrado y agrietado.

---

# **Cifrado de archivo en Linux**

[Openssl](https://www.openssl.org/)Se incluye con frecuencia en las distribuciones de Linux, con sysadmins usándolo para generar certificados de seguridad, entre otras tareas. OpenSSL se puede usar para enviar archivos "estilo NC" a los archivos en cifrar.

Para cifrar un archivo usando`openssl`Podemos seleccionar diferentes cifrados, ver[Página del hombre de OpenSSL](https://www.openssl.org/docs/man1.1.1/man1/openssl-enc.html). Usemos`-aes256`Como ejemplo. También podemos anular las iteraciones predeterminadas cuenta con la opción`-iter 100000`y agregar la opción`-pbkdf2`Para usar el algoritmo de la función de derivación 2 basada en contraseña 2. Cuando presionamos Enter, necesitaremos proporcionar una contraseña.

### **Encriptación /etc /passwd con OpenSSL**

Transferencias de archivos protegidos

```
xnoxos@htb[/htb]$ openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.encenter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:

```

Recuerde usar una contraseña fuerte y única para evitar ataques de agrietamiento de fuerza bruta en caso de que una parte no autorizada obtenga el archivo. Para descifrar el archivo, podemos usar el siguiente comando:

### **Decrypt passwd.enc con OpenSSL**

Transferencias de archivos protegidos

```
xnoxos@htb[/htb]$ openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd                    enter aes-256-cbc decryption password:

```

Podemos usar cualquiera de los métodos anteriores para transferir este archivo, pero se recomienda utilizar un método de transporte seguro como HTTPS, SFTP o SSH. Como siempre, practique los ejemplos en esta sección contra hosts objetivo en este u otros módulos y reproduzca lo que puede (como el`openssl`Ejemplos usando el Pwnbox. La siguiente sección cubrirá diferentes formas de transferir archivos a través de HTTP y HTTPS.