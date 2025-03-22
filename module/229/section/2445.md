# Intermediate Network Traffic Analysis Overview

No se puede exagerar la importancia de dominar el análisis de tráfico de red en nuestros entornos de red de ritmo rápido, en constante evolución e intrincados. Confrontado con un volumen abrumador de tráfico que atraviesa nuestra infraestructura de red, puede sentirse desalentador. Nuestro potencial para sentirse mal equipado o incluso abrumado es un desafío inherente que debemos superar.

En este módulo, nuestro enfoque estará en un extenso conjunto de ataques que abarcan componentes cruciales de nuestra infraestructura de red. Profundizaremos en ataques que tienen lugar en la capa de enlace, la capa IP y las capas de transporte y red. Nuestra exploración incluso abarcará ataques que se dirigen a la capa de aplicación. El objetivo es discernir patrones y tendencias dentro de estos ataques. Reconocer estos patrones nos equipa con las habilidades esenciales para detectar y responder a estas amenazas de manera eficaz.

Además, discutiremos habilidades adicionales para aumentar nuestras habilidades. Tocemos las técnicas de detección de anomalías, profundizaremos en facetas del análisis de registro e investigaremos algunos indicadores de compromiso (COI). Este enfoque integral no solo refuerza nuestra capacidad de identificación de amenazas proactivas, sino que también mejora nuestras medidas reactivas. En última instancia, esto nos permitirá identificar, informar y responder a las amenazas de manera más efectiva y dentro de un período de tiempo más corto.

---

**Nota**: Para participar en este módulo y completar los ejercicios prácticos, descargue`pcap_files.zip`desde`Resources`Sección (esquina superior derecha).

Puedes descargar y no compresión`pcaps.zip`a un directorio nombrado`pcaps`Dentro de Pwnbox de la siguiente manera.

Descripción general del análisis de tráfico de red intermedia

```
xnoxos@htb[/htb]$ wget -O file.zip 'https://academy.hackthebox.com/storage/resources/pcap_files.zip' && mkdir tempdir && unzip file.zip -d tempdir && mkdir -p pcaps && mv tempdir/Intermediate_Network_Traffic_Analysis/* pcaps/ && rm -r tempdir file.zip--2023-08-08 14:09:14--  https://academy.hackthebox.com/storage/resources/pcap_files.zip
Resolving academy.hackthebox.com (academy.hackthebox.com)... 104.18.20.126, 104.18.21.126, 2606:4700::6812:147e, ...
Connecting to academy.hackthebox.com (academy.hackthebox.com)|104.18.20.126|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 19078200 (18M) [application/zip]
Saving to: ‘file.zip’

file.zip           100%[===============>]  18.19M  71.4MB/s    in 0.3s

2023-08-08 14:09:14 (71.4 MB/s) - ‘file.zip’ saved [19078200/19078200]

Archive:  file.zip
   creating: tempdir/Intermediate_Network_Traffic_Analysis/
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/ARP_Poison.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/ARP_Scan.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/ARP_Spoof.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/basic_fuzzing.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/CRLF_and_host_header_manipulation.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/deauthandbadauth.cap
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/decoy_scanning_nmap.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/dns_enum_detection.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/dns_tunneling.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/funky_dns.pcap
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/funky_icmp.pcap
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/icmp_frag.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/ICMP_rand_source.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/ICMP_rand_source_larg_data.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/ICMP_smurf.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/icmp_tunneling.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/ip_ttl.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/LAND-DoS.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/nmap_ack_scan.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/nmap_fin_scan.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/nmap_frag_fw_bypass.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/nmap_null_scan.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/nmap_syn_scan.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/nmap_xmas_scan.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/number_fuzzing.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/rogueap.cap
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/RST_Attack.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/SSL_renegotiation_edited.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/SSL_renegotiation_original.pcap
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/TCP-hijacking.pcap
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/TCP_rand_source_attacks.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/telnet_tunneling_23.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/telnet_tunneling_9999.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/telnet_tunneling_ipv6.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/udp_tunneling.pcapng
  inflating: tempdir/Intermediate_Network_Traffic_Analysis/XSS_Simple.pcapng
```