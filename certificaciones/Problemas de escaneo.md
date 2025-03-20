# Problemas de escaneo

Nessus es una plataforma de escaneo de vulnerabilidad bien conocida y ampliamente utilizada. Sin embargo, se deben tener en cuenta algunas mejores prácticas antes de comenzar un escaneo. Los escaneos pueden causar problemas en las redes confidenciales y proporcionar falsos positivos, sin resultados o tener un impacto desfavorable en la red. Siempre es mejor comunicarse con su cliente (o partes interesadas internas si se ejecuta un escaneo contra su propia red) sobre si algún host confidencial/heredado debe estar excluidos del escaneo o si algún host de alta prioridad/alta disponibilidad debe escanear por separado, fuera de las horas comerciales regulares o con diferentes configuraciones de escaneo para evitar posibles problemas.

También hay momentos en que un escaneo puede devolver resultados inesperados y necesita ser ajustado.

---

# **Problemas de mitigación**

Algunos firewalls nos harán recibir resultados de escaneo que muestren todos los puertos abiertos o no se abren los puertos. Si esto sucede, una solución rápida a menudo es configurar un escaneo avanzado y deshabilitar el`Ping the remote host`opción. Esto evitará que el escaneo use ICMP para verificar que el host esté "en vivo" y, en su lugar, continúe con el escaneo. Algunos firewalls pueden devolver un mensaje "ICMP inalcanzable" que Nessus interpretará como un anfitrión en vivo y proporcionará muchos hallazgos informativos falsos positivos.

En redes sensibles, podemos usar la limitación de la velocidad para minimizar el impacto. Por ejemplo, podemos ajustarnos`Performance Options`y modificar`Max Concurrent Checks Per Host`Si el host de destino a menudo está bajo una carga pesada, como una aplicación web ampliamente utilizada. Esto limitará el número de complementos utilizados simultáneamente contra el host.

Podemos evitar escanear sistemas heredados y elegir la opción de no escanear impresoras, como mostramos en una sección anterior. Si un anfitrión es de particular preocupación, debe dejarse fuera del alcance objetivo o podemos usar el`nessusd.rules`Archivo para configurar los escaneos Nessus. Más información al respecto que puede encontrar[aquí](https://community.tenable.com/s/article/What-is-the-Nessus-rules-file?language=en_US).

Finalmente, a menos que se solicite específicamente, nunca debemos realizar[Denificación de cheques de servicio](https://www.tenable.com/plugins/nessus/families/Denial%20of%20Service). Podemos asegurarnos de que este tipo de complementos no se utilicen siempre habilitando el["Checks seguros"](https://www.tenable.com/blog/understanding-the-nessus-safe-checks-option)Opción Al realizar escaneos para evitar cualquier complemento de red que pueda tener un impacto negativo en un objetivo, como bloquear un demonio de red. Habilitar la opción de "verificaciones seguras" no garantiza que un escaneo de vulnerabilidad de Nessus tenga un impacto adverso cero, pero minimizará significativamente el impacto potencial y la disminución del tiempo de escaneo.

Siempre es mejor comunicarse con nuestros clientes o partes interesadas internas y alertar al personal necesario antes de comenzar un escaneo. Cuando se complete el escaneo, debemos mantener registros detallados de la actividad de escaneo en caso de que ocurra un incidente que debe investigarse.

---

# **Impacto de la red**

También es esencial tener en cuenta el impacto potencial del escaneo de vulnerabilidad en una red, especialmente en un bajo ancho de banda o enlaces congestionados. Esto se puede medir usando[vnstat](https://humdi.net/vnstat/):

Problemas de escaneo

```
xnoxos@htb[/htb]$ sudo apt install vnstat
```

Vamos a monitorear el`eth0`Adaptador de red antes de ejecutar un escaneo Nessus:

Problemas de escaneo

```
xnoxos@htb[/htb]$ sudo vnstat -l -i eth0Monitoring eth0...    (press CTRL-C to stop)

   rx:       332 bit/s     0 p/s          tx:       332 bit/s     0 p/s

   rx:         0 bit/s     0 p/s          tx:         0 bit/s     0 p/s
   rx:         0 bit/s     0 p/s          tx:         0 bit/s     0 p/s^C

 eth0  /  traffic statistics

                           rx         |       tx
--------------------------------------+------------------
  bytes                        572 B  |           392 B
--------------------------------------+------------------
          max              480 bit/s  |       332 bit/s
      average              114 bit/s  |        78 bit/s
          min                0 bit/s  |         0 bit/s
--------------------------------------+------------------
  packets                          8  |               5
--------------------------------------+------------------
          max                  1 p/s  |           0 p/s
      average                  0 p/s  |           0 p/s
          min                  0 p/s  |           0 p/s
--------------------------------------+------------------
  time                    40 seconds

```

Podemos comparar este resultado con el resultado que obtenemos al monitorear la misma interfaz durante un escaneo de Nessus contra un solo host:

Problemas de escaneo

```
xnoxos@htb[/htb]$ sudo vnstat -l -i eth0Monitoring eth0...    (press CTRL-C to stop)

   rx:   307.92 kbit/s   641 p/s          tx:   380.41 kbit/s   767 p/s^C

 eth0  /  traffic statistics

                           rx         |       tx
--------------------------------------+------------------
  bytes                     1.04 MiB  |        1.34 MiB
--------------------------------------+------------------
          max          414.81 kbit/s  |   480.59 kbit/s
      average          230.57 kbit/s  |   296.72 kbit/s
          min                0 bit/s  |         0 bit/s
--------------------------------------+------------------
  packets                      18252  |           22733
--------------------------------------+------------------
          max                864 p/s  |         969 p/s
      average                480 p/s  |         598 p/s
          min                  0 p/s  |           0 p/s
--------------------------------------+------------------
  time                    38 seconds

real  0m38.588s
user  0m0.002s
sys 0m0.016s

```

When comparing the results, we can see that the number of bytes and packets transferred during a vulnerability scan is quite significant and can severely impact a network if not tuned properly or performed against fragile/sensitive devices.

[Anterior](https://academy.hackthebox.com/module/108/section/1024) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/108/section/1233)