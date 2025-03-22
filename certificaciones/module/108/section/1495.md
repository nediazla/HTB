# Exportar los resultados

OpenVas proporciona los resultados de escaneo en un informe al que se puede acceder cuando está en el`Scans`página, como se muestra a continuación.

![](https://academy.hackthebox.com/storage/modules/108/openvas/viewingreport.png)

Una vez que haga clic en el informe, puede ver los resultados de escaneo y la información del sistema operativo, abrir puertos, servicios, etc., en otras pestañas en el informe de escaneo.

![](https://academy.hackthebox.com/storage/modules/108/openvas/openvas_reports.png)

---

# **Formatos de exportación**

Existen varios formatos de exportación para fines de informes, incluidos XML, CSV, PDF, ITG y TXT. Si elige exportar su informe como un XML, puede aprovechar varios analizadores XML para ver los datos en un formato más fácil de leer.

![](https://academy.hackthebox.com/storage/modules/108/openvas/reportformat.png)

Exportaremos nuestros resultados en XML y usaremos el[OpenVasReporting](https://github.com/TheGroundZero/openvasreporting)Herramienta por el Sroundzero. El`openvasreporting`La herramienta ofrece varias opciones al generar salida. Estamos utilizando la opción estándar para un archivo de Excel para este informe.

Exportar los resultados

```
xnoxos@htb[/htb]$ python3 -m openvasreporting -i report-2bf466b5-627d-4659-bea6-1758b43235b1.xml -f xlsx
```

Este comando generará un documento de Excel similar al siguiente:

![](https://academy.hackthebox.com/storage/modules/108/openvas/openvas_report.png)

![](https://academy.hackthebox.com/storage/modules/108/openvas/report_toc.png)

[Anterior](https://academy.hackthebox.com/module/108/section/1463) Mark Complete & Next[Próximo](https://academy.hackthebox.com/module/108/section/1516)