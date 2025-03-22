# Trabajar con la salida de escaneo de Nessus

Nessus nos brinda la opción de exportar los resultados de la exploración en una variedad de formatos de informe, así como la opción de exportar los resultados de escaneo de Nessus en bruto que se importan a otras herramientas, archivadas o pasadas a herramientas, como[Testigo ocular](https://github.com/FortyNorthSecurity/EyeWitness), que se puede utilizar para tomar capturas de pantalla de todas las aplicaciones web identificadas por Nessus y ayudarnos enormemente a trabajar a través de los resultados y encontrar más valor en ellas.

---

# **Nessus informa**

Una vez que se completa un escaneo, podemos elegir exportar un informe en`.pdf`, `.html`, o`.csv`formatos. Los informes .pdf y .html dan la opción de un resumen ejecutivo o un informe personalizado. El informe de resumen ejecutivo proporciona una lista de hosts, un número total de vulnerabilidades descubiertas por host y un`Show Details`Opción para ver la gravedad, la puntuación CVSS, el número de complemento y el nombre de cada problema descubierto. El número de complemento contiene un enlace a la redacción completa del complemento desde la base de datos de complementos Tenable. La opción PDF proporciona los resultados de escaneo en un formato que es más fácil de compartir. La opción de informe CSV nos permite seleccionar qué columnas nos gustaría exportar. Esto es particularmente útil si importa los resultados de la exploración en otra herramienta, como Splunk, si un documento debe compartirse con muchas partes interesadas internas responsables de la remediación de los diversos activos escaneados o realizar análisis en los datos de escaneo.

![](https://academy.hackthebox.com/storage/modules/108/nessus/exportreport.png)

**Nota:**Estos informes de escaneo solo deben compartirse como un apéndice o datos complementarios a una prueba de penetración personalizada/informe de evaluación de vulnerabilidad. No se deben entregar a un cliente como el entrega final para cualquier tipo de evaluación.

A continuación se muestra un ejemplo del informe HTML:

![](https://academy.hackthebox.com/storage/modules/108/nessus/htmlreport.png)

Es mejor asegurarse siempre de que las vulnerabilidades se agrupen para una comprensión clara de cada problema y los activos afectados.

---

# **Exportando escaneos de Nessus**

Nessus también da la opción de exportar escaneos en dos formatos`Nessus (scan.nessus)`o`Nessus DB (scan.db)`. El`.nessus`El archivo es un`.xml`Archivo e incluye una copia de la configuración de escaneo y las salidas de complementos. El`.db`el archivo contiene el`.nessus`Archivo y la ruta de auditoría de complementos de archivo y el escaneo, y cualquier accesorio de exploración. Más información sobre el`KB`y`Audit Trail`se puede encontrar[aquí](https://community.tenable.com/s/article/What-is-included-in-a-nessus-db-file).

Guiones como el[Nessus-Report-Downloader](https://raw.githubusercontent.com/eelsivart/nessus-report-downloader/master/nessus6-report-downloader.rb)se puede usar para descargar rápidamente los resultados de escaneo en todos los formatos disponibles de la CLI utilizando la API Nessus REST:

Trabajar con la salida de escaneo de Nessus

```
xnoxos@htb[/htb]$ ./nessus_downloader.rb Nessus 6 Report Downloader 1.0

Enter the Nessus Server IP: 127.0.0.1
Enter the Nessus Server Port [8834]: 8834
Enter your Nessus Username: admin
Enter your Nessus Password (will not echo):

Getting report list...
Scan ID Name                                               Last Modified                  Status
------- ----                                               -------------                  ------
1     Windows_basic                                Aug 22, 2020 22:07 +00:00      completed

Enter the report(s) your want to download (comma separate list) or 'all': 1

Choose File Type(s) to Download:
[0] Nessus (No chapter selection)
[1] HTML
[2] PDF
[3] CSV (No chapter selection)
[4] DB (No chapter selection)
Enter the file type(s) you want to download (comma separate list) or 'all': 3

Path to save reports to (without trailing slash): /assessment_data/inlanefreight/scans/nessus

Downloading report(s). Please wait...

[+] Exporting scan report, scan id: 1, type: csv
[+] Checking export status...
[+] Report ready for download...
[+] Downloading report to: /assessment_data/inlanefreight/scans/nessus/inlanefreight_basic_5y3hxp.csv

Report Download Completed!

```

También podemos escribir nuestros propios scripts para automatizar muchas características de Nessus.