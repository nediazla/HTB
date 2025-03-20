# Vhost Fuzzing

Como vimos en la sección anterior, pudimos borrar los subdominios públicos utilizando registros DNS públicos. Sin embargo, cuando se trataba de subdominios confusos que no tienen un registro público de DNS o subdominios en sitios web que no son públicos, no podríamos usar el mismo método. En esta sección, aprenderemos cómo hacer eso con`Vhost Fuzzing`.

---

# **Vhosts vs. subdominios**

La diferencia clave entre VHOSTS y subdominios es que un VHOST es básicamente un 'subdominio' servido en el mismo servidor y tiene la misma IP, de modo que una sola IP podría servir dos o más sitios web diferentes.

`VHosts may or may not have public DNS records.`

En muchos casos, muchos sitios web realmente tendrían subdominios que no son públicos y que no los publicarán en los registros públicos de DNS, y por lo tanto, si los visitamos en un navegador, no nos conectaríamos, ya que el DNS público no conocería su IP. Una vez más, si usamos el`sub-domain fuzzing`, solo podríamos identificar subdominios públicos, pero no identificaremos ningún subdominio que no sea público.

Aquí es donde utilizamos`VHosts Fuzzing`En una IP ya tenemos. Ejecutaremos un escaneo y probaremos los escaneos en la misma IP, y luego podremos identificar subdominios y VHosts públicos y no públicos.

---

# **Vhosts borrosos**

Para escanear en busca de Vhosts, sin agregar manualmente la lista de palabras completa a nuestra`/etc/hosts`, seremos encabezados http confusos, específicamente el`Host:` encabezamiento. Para hacer eso, podemos usar el`-H`marcar para especificar un encabezado y usará el`FUZZ`Palabra clave dentro de ella, como sigue:

Vhost Fuzzing

```
xnoxos@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://academy.htb:PORT/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

mail2                   [Status: 200, Size: 900, Words: 423, Lines: 56]
dns2                    [Status: 200, Size: 900, Words: 423, Lines: 56]
ns3                     [Status: 200, Size: 900, Words: 423, Lines: 56]
dns1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
lists                   [Status: 200, Size: 900, Words: 423, Lines: 56]
webmail                 [Status: 200, Size: 900, Words: 423, Lines: 56]
static                  [Status: 200, Size: 900, Words: 423, Lines: 56]
web                     [Status: 200, Size: 900, Words: 423, Lines: 56]
www1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
<...SNIP...>

```

We see that all words in the wordlist are returning `200 OK`! This is expected, as we are simply changing the header while visiting `http://academy.htb:PORT/`. Entonces, sabemos que siempre obtendremos`200 OK`. Sin embargo, si el VHOST existe y enviamos uno correcto en el encabezado, deberíamos obtener un tamaño de respuesta diferente, como en ese caso, obtendríamos la página de esos Vhosts, que es probable que muestre una página diferente.

[Anterior](https://academy.hackthebox.com/module/54/section/488) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/54/section/502)

Hoja de trucos