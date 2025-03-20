# SSL Renegotiation Attacks

**Archivo PCAP relacionado**:

- `SSL_renegotiation_edited.pcapng`

---

Aunque el tráfico HTTP no está cifurado, a veces nos encontraremos con el tráfico HTTPS cifrado. Como tal, conocer los indicadores y signos del tráfico HTTPS malicioso es crucial para nuestros esfuerzos de análisis de tráfico.

# **Desglose HTTPS**

A diferencia de HTTP, que es un protocolo sin estado, HTTPS incorpora cifrado para proporcionar seguridad para servidores web y clientes. Lo hace con lo siguiente.

1. `Transport Layer Security (Transport Layer Security)`
2. `Secure Sockets Layer (SSL)`

En términos generales, cuando un cliente establece una conexión HTTPS con un servidor, realiza lo siguiente.

1. `Handshake:`El servidor y el cliente se someten a un apretón de manos al establecer una conexión HTTPS. Durante este apretón de manos, el cliente y el servidor acuerdan qué algoritmos de cifrado usar e intercambiar sus certificados.
2. `Encryption`: Al finalizar el apretón de manos, el cliente y el servidor usan el algoritmo de cifrado acordado previo para cifrar más datos comunicados entre ellos.
3. `Further Data Exchange:`Una vez que se establece la conexión cifrada, el cliente y el servidor continuarán intercambiando datos entre sí. Estos datos pueden ser páginas web, imágenes u otros recursos web.
4. `Decryption:` When the client transmits to the server, or the server transmits to the client, they must decrypt this data with the private and public keys.

Como tal, uno de los ataques basados ​​en HTTPS más comunes es la renegociación SSL, en la que un atacante negociará la sesión con el estándar de cifrado más bajo posible.

Sin embargo, hay otros ataques de cifrado que debemos tener en cuenta como el`heartbleed vulnerability`

[La vulnerabilidad del corazón CVE-2014-0160](https://heartbleed.com/)

---

# **Tls y apretones de manos SSL**

Para establecer una conexión encriptada, el cliente y el servidor deben someterse al proceso de apretón de manos. Afortunadamente para nosotros, los apretones de manos TLS y SSL son en su mayoría similares en sus pasos.

![](https://academy.hackthebox.com/storage/modules/229/tls-ssl-handshake.png)

Para desglosarlo aún más, podríamos observar lo siguiente durante nuestros esfuerzos de análisis de tráfico.

1. `Client Hello`El paso inicial es que el cliente envíe su mensaje de saludo al servidor. Este mensaje contiene información como qué versiones TLS/SSL son compatibles con el cliente, una lista de suites de cifrado (algá alka algoritmos de cifrado) y datos aleatorios (Nonces) que se utilizarán en los siguientes pasos.
2. `Server Hello`Respondiendo al cliente Hola, el servidor enviará un mensaje de saludo del servidor. Este mensaje incluye la versión TLS/SSL elegida por el servidor, su conjunto de cifrado seleccionado de las elecciones del cliente y un Nonce adicional.
3. `Certificate Exchange`El servidor luego envía su certificado digital al cliente, demostrando su identidad. Este certificado incluye la clave pública del servidor, que el cliente utilizará para llevar a cabo el proceso de intercambio de claves.
4. `Key Exchange`El cliente genera lo que se conoce como el secreto PremaSaster. Luego cifra este secreto utilizando la clave pública del servidor desde el certificado y la envía al servidor.
5. `Session Key Derivation`Luego, tanto el cliente como el servidor usan el Nonces intercambiados en los dos primeros pasos, junto con el secreto PremaSter para calcular las claves de la sesión. Estas claves de sesión se utilizan para el cifrado simétrico y el descifrado de datos durante la conexión segura.
6. `Finished Messages`Para verificar que el apretón de manos se complete y sea exitoso, y también que ambas partes han obtenido las mismas claves de sesión, los mensajes terminados con el intercambio del cliente y el servidor. Este mensaje contiene el hash de todos los mensajes de apretón de manos anteriores y está encriptado utilizando las claves de sesión.
7. `Secure Data Exchange`Ahora que el apretón de manos está completo, el cliente y el servidor ahora pueden intercambiar datos a través del canal cifrado.

También podemos ver esto desde una perspectiva algorítmica general.

| **Paso de apretón de manos** | **Cálculos relevantes** |
| --- | --- |
| `Client Hello` | `ClientHello = { ClientVersion, ClientRandom, Ciphersuites, CompressionMethods }` |
| `Server Hello` | `ServerHello = { ServerVersion, ServerRandom, Ciphersuite, CompressionMethod` } |
| `Certificate Exchange` | `ServerCertificate = { ServerPublicCertificate }` |
| `Key Exchange` | • `ClientDHPrivateKey`
• `ClientDHPublicKey = DH_KeyGeneration(ClientDHPrivateKey)`
• `ClientKeyExchange = { ClientDHPublicKey }`
• `ServerDHPrivateKey`
• `ServerDHPublicKey = DH_KeyGeneration(ServerDHPrivateKey)`
• `ServerKeyExchange = { ServerDHPublicKey }` |
| `Premaster Secret` | • `PremasterSecret = DH_KeyAgreement(ServerDHPublicKey, ClientDHPrivateKey)`
• `PremasterSecret = DH_KeyAgreement(ClientDHPublicKey, ServerDHPrivateKey)` |
| `Session Key Derivation` | `MasterSecret = PRF(PremasterSecret, "master secret", ClientNonce + ServerNonce`) |
|  | `KeyBlock = PRF(MasterSecret, "key expansion", ServerNonce + ClientNonce)` |
| `Extraction of Session Keys` | • `ClientWriteMACKey = First N bytes of KeyBlock`
• `ServerWriteMACKey = Next N bytes of KeyBlock`
• `ClientWriteKey = Next N bytes of KeyBlock`
• `ServerWriteKey = Next N bytes of KeyBlock`
• `ClientWriteIV = Next N bytes of KeyBlock`
• `ServerWriteIV = Next N bytes of KeyBlock` |
| `Finished Messages` | `FinishedMessage = PRF(MasterSecret, "finished", Hash(ClientHello + ServerHello))` |

# **Bucear en ataques de renegociación SSL**

Para encontrar irregularidades en los apretones de manos, podemos utilizar el volcado TCP y Wireshark como lo hemos hecho antes. Para filtrar solo mensajes de apretón de manos, podemos usar este filtro en Wireshark.

- `ssl.record.content_type == 22`

El tipo de contenido 22 especifica solo mensajes de apretón de manos. Especificando este filtro debemos obtener una vista como la siguiente.

![](https://academy.hackthebox.com/storage/modules/229/1-HTTPs.png)

Cuando buscamos ataques de renegociación SSL, podemos buscar lo siguiente.

1. `Multiple Client Hellos`Este es el signo más obvio de un ataque de renegociación SSL. Notaremos múltiples clientes de un cliente de un cliente en un período corto como arriba. El atacante repite este mensaje para activar la renegociación y, con suerte, obtener una suite de cifrado más baja.
2. `Out of Order Handshake Messages`En pocas palabras, a veces veremos algo de tráfico fuera de servicio debido a la pérdida de paquetes y otros, pero en el caso de la renegociación SSL, algunos signos obvios serían el servidor que recibe un saludo de cliente después de completar el apretón de manos.

Un atacante podría llevar a cabo este ataque contra nosotros por las siguientes razones.

1. `Denial of Service`Los ataques de renegociación SSL consumen una tonelada de recursos en el lado del servidor y, como tal, podría abrumar el servidor y hacer que no responda.
2. `SSL/TLS Weakness Exploitation`El atacante podría intentar la renegociación para explotar potencialmente las vulnerabilidades con nuestra implementación actual de suites cifrados.
3. `Cryptanalysis`El atacante podría usar la renegociación como parte de una estrategia general para analizar nuestros patrones SSL/TLS para otros sistemas.